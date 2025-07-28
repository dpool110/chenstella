---
title: ARM汇编
tags:
  - 嵌入式开发
  - ARM汇编
  - A53
categories:
  - 嵌入式开发
  - A53
cover: 'https://images4.alphacoders.com/129/thumbbig-1299555.webp'
abbrlink: 7dbaa5b1
date: 2023-02-01 20:28:19
---

# ARM汇编

## 基本概念

汇编语言，又叫助记符语言。由编译器产生最佳代码，尤其是Thumb

## ARM汇编指令

### ARM汇编指令的特点

- 大多数指令都是单周期
- 大多数指令都可以条件执行（`if else` / `switch case`）

### 条件码

{% folding blue, 查看 %}

| **操作码** | **条件助记符** | **标 志** |         **含 义**         |
| :--------: | :------------: | :-------: | :-----------------------: |
|    0000    |       EQ       |    Z=1    |           相等            |
|    0001    |       NE       |    Z=0    |          不相等           |
|    0010    |     CS/HS      |    C=1    |    无符号数大于或等于     |
|    0011    |     CC/LO      |    C=0    |       无符号数小于        |
|    0100    |       MI       |    N=1    |           负数            |
|    0101    |       PL       |    N=0    |         正数或零          |
|    0110    |       VS       |    V=1    |           溢出            |
|    0111    |       VC       |    V=0    |         没有溢出          |
|    1000    |       HI       |  C=1,Z=0  |       无符号数大于        |
|    1001    |       LS       |  C=0,Z=1  |    无符号数小于或等于     |
|    1010    |       GE       |    N=V    |    有符号数大于或等于     |
|    1011    |       LT       |   N!=V    |       有符号数小于        |
|    1100    |       GT       |  Z=0,N=V  |       有符号数大于        |
|    1101    |       LE       | Z=1,N!=V  |    有符号数小于或等于     |
|    1110    |       AL       |   任何    | 无条件执行 (指令默认条件) |
|    1111    |       NV       |   任何    |    从不执行(不要使用)     |


{% endfolding %}

## 分支跳转指令

### ARM跳转方式

- 发生异常
- 使用分支指令
- 直接向PC赋值

**支持的3条分支指令**

- **B**		分支指令
- **BL**	  带链接的分支指令
- **BX**      带状态切换的分支指令

{% tip info %}

**B**类似C语言中的goto，指令格式：B{cond}<*target_label*>

**BL**一般用于函数调用，指令格式：BL{cond}  <*target_address*>

```
bl main //在向main函数跳转之前, 记录当前的下条指令的地址存放到lr中
```

{% tip warning %}

**注意：**目标地址的范围 ±32MB

{cond} 为指令的条件码，如EQ、NE、CC、CS... ...

<*target_address*> 为跳转的目标地址 

将指令中的24位带符号的补码的立即数扩展为32bit，将32bit的数左移两位，将得到的值加上PC寄存器，得到的就是目标地址。

{% endtip %}

<img src="http://img.dpool.love/202312071456140.png" alt="image-20230201161502800" style="zoom:67%;" />

```
原理：根据4字节对齐
0000 0000
0004 0100
0008 1000
000c 1100
最后两位都是0
24bit的立即数就有了26位的寻址能力 ±32M = 2^26 = 16=64M
```

**BX**指令格式：BX{cond} <*Rm*>

<img src="http://img.dpool.love/202312071456617.png" alt="image-20230201163300551" style="zoom:67%;" />

跳转范围限制：绝对地址、4G范围

{% endtip %}

## 数据处理指令

### 移位指令

<img src="http://img.dpool.love/202312071456499.png" alt="image-20230201170420261" style="zoom:67%;" />

- **LSL 逻辑左移**（logical shift left）进行移位操作之后，空出来的位添0
  - `<RN>  LSL #<shift imm>` **立即数**
  - `<RN>  LSL <RS>`移位的个数由RS**寄存器**的bit[7:0]决定

- **LSR 逻辑右移**（logical shift right）进行移位操作之后，空出来的位添0
  - `<RN>  LRL #<shift imm>` **立即数**
  - ``<RN>  LRL <RS>`移位的个数由Rs**寄存器**的bit[7:0]决定

- **ASR 算数右移**（Arithmetic shift right）进行移位操作之后，最高位补**符号位**

- **ROR 循环右移**，最低位变成了最高位
- **RRX 带扩展位的循环右移**，新的最高位由cpsr的C位决定，最低位更新cpsr的C位

## 数据传输指令MOV

MOV的指令格式：`MOV{cond}{S} <Rd>, <shifter_operand>`

`cond`表示条件执行，EQ、NE、CC、CS... ...

`S`会影响cpsr的NZCV位

```assembly
mov r0,  #10  @ 表示r0 = 10 
movs r0, #10  @ r0 = 10  N = 0 Z = 0 
```

`Rd`  目标寄存器，一定是通用寄存器 

`shifter_operand`  有三种表现形式，即可以是**立即数**, 也可是**通用寄存器**, 或**通用寄存器移位之后的值**

```assembly
mov r0, #10 @注意立即数的合法性，存在一个bit的立即数能够循环右移偶数位得到，这个立即数就是合法的
mov r0, r2 @ r0 = r2
mov r0, r2 ,lsl #2 @ 把r2里面的值逻辑左移两位之后的值移动到r0寄存器中 r2 <<= 2 ，r0 = r2

moveqs r0, r1   @ if (CPSR.Z == 1) 
					movs r0, r1 @ r0 = r1, if(r0 == 0)则Z == 1 else Z = 0 
								@ N = r0[31]
```

## 算数运算指令

`ADD{cond}{S} <Rd>, <Rn>, <shifter_operand>`



## 加载存储指令

在ARM中所有的运算都是在通用寄存器完成的，在C语言中的数据都是保存在内存中，我们编译好的代码要在内存中分成很多段[C语言内存分区](http://www.dpool.love/posts/56970.html)。加载存储指令分为两个操作，一个是存储**store**：将寄存器中运算的结果存储到内存中；另一个是加载**load**：将内存中的数据加载到通用寄存器中。所以操作对象就是两种，内存和通用寄存器。

<img src="http://img.dpool.love/202312071456069.png" alt="image-20230203212520852" style="zoom:50%;" />

{% tabs test1 %}
<!-- tab 加载指令ldr -->

```assembly
mov r1, #0x48000000
ldr r0, [r1]  @ 将内存0x48000000开始的4字节的数据加载到r0中, 等价于：r0 = *((int *)0x48000000)
ldrb r0, [r1] @ 将内存0x48000000开始的1字节的数据加载到r0中 b:byte
				@ *((unsigned char *)0x48000000) 取出来的8bit 存放到r0寄存器的低8个bit高24bit补0

ldrsb r0, [r1]  @ 将内存0x48000000开始的1字节的数据加载到r0中, r0的高24bit补符号位
ldrh  r0, [r1]  @ 将内存0x48000000开始的2字节的数据加载到r0中, r0的高16bit补0
ldrsh r0, [r1]  @ 将内存0x48000000开始的2字节的数据加载到r0中, r0的高16bit补符号位

ldr r0, [r1]  @ [r1] ---> r0

@------------------------------------------------------@

@例子
ldr r0, [r1, #0x08]   		@ [r1 + 0x08] ---> r0 
ldr r0, [r1, r2]      		@ [r1 + r2] ---> r0 
ldr r0, [r1, r2, lsl #2]  @ [r1 + r2 << 2] ---> r0

@------------------------------------------------------@

@注意：加!表示更新基址

```

<!-- endtab -->

<!-- tab 存储指令str -->

```assembly
mov r1, #0x48000000
str r0, [r1]  @将r0中的4字节数据写入到0x48000000指向的内存中
strb r0, [r1] @将r0中的1字节（低8bit）数据写入到0x48000000指向的内存中 
strh r0, [r1] @将r0中的2字节（低16bit）数据写入到0x48000000指向的内存中 
str r0, [r1]  			 			@ r0 ---> [r1]  
str r0, [r1, #0x08]  			@ r0 ---> [r1+0x08]
str r0, [r1, r2]     			@ r0 ---> [r1+r2] 
str r0, [r1, r2, lsl #2]  @ r0 ---> [r1 + r2 * 4]
@--------------------------@
str r0, [r1, #0x08]!       @ r0 ---> [r1 + 0x08]  r1 = r1 + 0x08
str r0, [r1, r2]!     		 @ r0 ---> [r1 + r2]  r1 = r1 + r2
str r0, [r1, r2, lsl #3]!  @ r0 ---> [r1 + r2 * 8]  r1 = r1 + r2 * 8
@--------------------------@
str r0, [r1], #0x08 	    @ r0 ---> [r1]   r1 = r1 + 0x08
str r0, [r1], r2					@ r0 ---> [r1]   r1 = r1 + r2
str r0, [r1], r2, lsl #3  @ r0 ---> [r1]   r1 = r1 + r2 * 8 
```

<!-- endtab -->
{% endtabs %}



{% folding 笔试题： 实现memcpy(3) 将内存0x8e00开始的64个字节到0x8f00开始的内存空间%}

<b>{% span red, 思路： 先初始化内存 8e00 ，load到寄存器中 再store 0x8f00 内存的位置 %}</b>

```assembly
.text
.code 32
.global _start

_start:
	mov r0, #0x8e00
	mov r1, #1
	mov r2, #16

loop:
	str r1, [r0], #4
	subs r2, r2, #1
	bne loop

mem_cpy:
    mov r0, #0x8e00  
    mov r1, #0x8f00  
    mov r2, #16  

next_word:
    ldr r3, [r0], #4  
    str r3, [r1], #4  
    subs r2, r2, #1  
    bne next_word  
    
mem_ok:
	b .

.data
	.space 1024

.end
```



**注意：链接的时候, 指定数据段的地址** 

```
arm-cortex_a9-linux-gnueabi-as memcpy.s -o memcpy.o -g
arm-cortex_a9-linux-gnueabi-ld -Tdata=0x8e00 memcpy.o -o memcpy 
qemu-arm -g 1234 memcpy  //开一个qemu的服务端
arm-cortex_a9-linux-gnueabi-gdb memcpy
target remote localhost:1234
查看内存的格式：
x /nfu addr 
x examine 
n 表示要连续查看的内存单元个数 
f 显示内存中数据时使用的哪种格式 
	x 按16进制进行显示 
	d 按10进制进行显示
u unit 每个内存单元显示的字节数 
	b  字节
	h  半字
	w  字
b 10 
c
x /16xw 0x8e00 
b 20 
c
x /16xw 0x8f00 
```

{% endfolding %}
