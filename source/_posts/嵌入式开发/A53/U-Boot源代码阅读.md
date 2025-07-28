---
title: U-Boot源代码阅读
tags:
  - 嵌入式开发
  - ARM系统结构与编程
  - A53
categories:
  - 嵌入式开发
  - A53
cover: 'https://images4.alphacoders.com/130/thumbbig-1300843.webp'
abbrlink: fe4174d9
date: 2023-02-08 11:45:09
---

# U-Boot源代码阅读

初始化异常向量表

```assembly
	.globl	_stext
_stext:
	b 	reset
	/*异常向量表 8种异常*/
	ldr	pc, _undefined_instruction 	/*未定义指令异常*/
	ldr	pc, _software_interrupt		/*软中断异常*/
	ldr	pc, _prefetch_abort			/*内存操作异常*/
	ldr	pc, _data_abort				/*数据异常*/
	ldr	pc, _not_used				/*未使用*/
	ldr	pc, _irq					/*普通中断异常*/
	ldr	pc, _fiq					/*快速中断异常*/
```

链接脚本中异常向量的入口地址

```assembly
/*后面的符号正好是对应的中断异常服务程序的入口地址*/
/*每种异常入口处都填写一条跳转指令，直接跳转到相应的异常处理函数中*/
_undefined_instruction:	.word undefined_instruction
_software_interrupt:	.word software_interrupt
_prefetch_abort:	.word prefetch_abort
_data_abort:		.word data_abort
_not_used:		.word not_used
_irq:			.word irq
_fiq:			.word fiq

	.balignl 16,0xdeadbeef		  /*16bytes对齐，并且使用0xdeadbeef填充*/
```

计算一些段的标号

```assembly
.globl TEXT_BASE
TEXT_BASE:
	.word	CONFIG_SYS_TEXT_BASE  @定义的宏，U-Boot被拷贝到SDRAM中的起始地址

/*
 * These are defined in the board-specific linker script.
 * 下面这些是定义在开发板目录链接脚本中的
 * 计算一些段的标号
 */
.globl _bss_start_ofs
_bss_start_ofs:
	.word __bss_start - _stext

.globl _bss_end_ofs
_bss_end_ofs:
	.word __bss_end - _stext

.globl _end_ofs
_end_ofs:
	.word _end - _stext
```

初始化从这里开始，CPU一上电以后就是跳到这里执行的，更改处理器模式为`SVC模式`。

```assembly
	.globl reset
	/*这里便是reset函数的入口*/
    /*CPU一上电以后就是跳到这里执行的*/

reset:
	bl	save_boot_params /*跳转到save_boot_params符号处*/

	/*
	 * set the cpu to SVC32 mode
	 * 设置cpu为SVC32模式，管理模式
	 */
	mrs	r0, cpsr 		/*读出cpsr的值*/
	bic	r0, r0, #0x1f	/*清零低5位*/
	orr	r0, r0, #0xd3	/*0xd3，即1 1 0 1 0 0 1 1，即为SVC模式，禁止IRQ,FIQ中断(bit6,7)*/
	msr	cpsr,r0 		/*写回cpsr使其生效*/
```

禁用看门狗

```assembly
/* disable watchdog 禁用看门狗 */
	ldr	r0, =0xC0019000
	mov	r1, #0
	str	r1, [r0]
```

两句跳转指令`cpu_init_cp15`和`cpu_init_crit`单独分析

```assembly
	/* the mask ROM code should have PLL and others stable */
#ifndef CONFIG_SKIP_LOWLEVEL_INIT
	bl	cpu_init_cp15
	bl	cpu_init_crit
#endif
```

{% tabs test1 %}
<!-- tab cpu_init_cp15 -->

```assembly
/*************************************************************************
 *
 * cpu_init_cp15
 *
 * Setup CP15 registers (cache, MMU, TLBs). The I-cache is turned on unless
 * 配置CP15，关闭的cache、MMU、TLBs。icache视情况
 * CONFIG_SYS_ICACHE_OFF is defined.
 *
 *************************************************************************/
ENTRY(cpu_init_cp15)
	/*
	 * Invalidate L1 I/D
	 */

	mov	r0, #0			@ set up for MCR
	mcr	p15, 0, r0, c8, c7, 0	@ invalidate TLBs 使TLBs无效
	mcr	p15, 0, r0, c7, c5, 0	@ invalidate icache 使icache无效
	mcr	p15, 0, r0, c7, c5, 6	@ invalidate BP array
#ifndef CONFIG_MACH_S5P6818
	mcr p15, 0, r0, c7, c10, 4	@ DSB 数据同步屏障
	mcr p15, 0, r0, c7, c5, 4	@ ISB 指令同步屏障
#endif

	/*
	 * disable MMU stuff and caches
	 */
	mrc	p15, 0, r0, c1, c0, 0  @将SCTLR的值赋给r0
	bic	r0, r0, #0x00002000	@ clear bits 13 (--V-) 清零第13位，异常向量映射到0x00000000
	bic	r0, r0, #0x00000007	@ clear bits 2:0 (-CAM) 失效dcache，失效对齐检查，禁用MMU
	orr	r0, r0, #0x00000002	@ set bit 1 (--A-) Align 使能对齐检查模式
	orr	r0, r0, #0x00000800	@ set bit 11 (Z---) BTB 使能分支预测
#ifdef CONFIG_SYS_ICACHE_OFF
	bic	r0, r0, #0x00001000	@ clear bit 12 (I) I-cache
#else
	orr	r0, r0, #0x00001000	@ set bit 12 (I) I-cache 使能icache
#endif
	mcr	p15, 0, r0, c1, c0, 0  @将r0的值赋给SCTLR
	mov	pc, lr			@ back to my caller 返回调用处
ENDPROC(cpu_init_cp15)

#ifndef CONFIG_SKIP_LOWLEVEL_INIT
```

`pu_init_cp15`函数是通过配置`CP15`协处理器相关寄存器来进行一些设置。设置内容主要是失效`TLBs`（这与MMC有关，后面再讲）、失效`icache`、失效`BP array`。

<!-- endtab -->

<!-- tab cpu_init_crit -->

这部分的内容，跳转到`lowlevel_init`符号处。

```assembly
/*************************************************************************
 *
 * CPU_init_critical registers
 *
 * setup important registers
 * setup memory timing
 *
 *************************************************************************/
ENTRY(cpu_init_crit)
	/*
	 * Jump to board specific initialization...
	 * The Mask ROM will have already initialized
	 * basic memory. Go here to bump up clock rate and handle
	 * wake up conditions.
	 */
	b	lowlevel_init		@ go setup pll,mux,memory
ENDPROC(cpu_init_crit)
#endif
```

<!-- endtab -->


{% endtabs %}

重新定位内存中的u-boot代码到TEXT_BASE处

```assembly
#ifdef CONFIG_RELOC_TO_TEXT_BASE
relocate_to_text:

/*程序继续执行，此程序段便是要重新定位内存中的u-boot代码到TEXT_BASE处*/

	/*
	 * relocate u-boot code on memory to text base
	 * for nexell arm core (add by jhkim)
	 */
	adr	r0, _stext				/* r0 <- current position of code   */
	ldr	r1, TEXT_BASE			/* test if we run from flash or RAM */
	cmp r0, r1              	/* don't reloc during debug         */
	beq clear_bss

	ldr	r2, _bss_start_ofs		@_bss_start为数据段地址
	add	r2, r0, r2				/* r2 <- source end address         */

copy_loop_text:
	ldmia	r0!, {r3-r10}		/* copy from source address [r0]    */
	@从源地址[r0]读取8个字节到寄存器,每读一个就更新一次r0地址
	stmia	r1!, {r3-r10}		/* copy to   target address [r1]    */
	cmp	r0, r2					/* until source end addreee [r2]    */
	ble	copy_loop_text

	ldr	r1, TEXT_BASE			/* restart at text base */
	mov pc, r1 					/*程序从TEXT_BASE处开始执行*/	
```

清除未初始化数据段

```assembly
clear_bss:						/*清除未初始化数据段*/
#ifdef CONFIG_MMU_ENABLE
	bl	mmu_turn_on				/*ROM到RAM的copy完成后，继续调用mmu_turn_on函数，主要功能是打开MMU*/
#endif
	ldr	r0, _bss_start_ofs
	ldr	r1, _bss_end_ofs
	ldr	r4, TEXT_BASE			/* text addr */
	add	r0, r0, r4
	add	r1, r1, r4
	mov	r2, #0x00000000			/* clear */

clbss_l:str	r2, [r0]			/* clear loop... */
	add	r0, r0, #4
	cmp	r0, r1
	bne	clbss_l

	ldr	sp, =(CONFIG_SYS_INIT_SP_ADDR)
	bic	sp, sp, #7					/* 8-byte alignment for ABI compliance */
	sub	sp, #GD_SIZE				/* allocate one GD above SP */
	bic	sp, sp, #7					/* 8-byte alignment for ABI compliance */
	mov	r9, sp						/* GD is above SP */
	mov	r0, #0
	bl	board_init_f

	mov	sp, r9						/* SP is GD's base address */
	bic	sp, sp, #7					/* 8-byte alignment for ABI compliance */
	sub	sp, #GENERATED_BD_INFO_SIZE	/* allocate one BD above SP */
	bic	sp, sp, #7					/* 8-byte alignment for ABI compliance */

	mov	r0, r9						/* gd_t *gd */
	ldr r1, TEXT_BASE				/* ulong text */
	mov r2, sp						/* ulong sp */
	bl	gdt_reset

	/* call board_init_r(gd_t *id, ulong dest_addr) */
	mov	r0, r9							/* gd_t */
	ldr	r1,  =(CONFIG_SYS_MALLOC_END)	/* dest_addr for malloc heap end */
	/* call board_init_r */
	ldr	pc, =board_init_r				/* this is auto-relocated! */

#else	/* CONFIG_RELOC_TO_TEXT_BASE */

	bl	_main
#endif
```

