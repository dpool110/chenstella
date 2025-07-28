---
title: ARM伪指令
tags:
  - 嵌入式开发
  - ARM汇编
  - A53
categories:
  - 嵌入式开发
  - A53
cover: 'https://images5.alphacoders.com/121/thumbbig-1217672.webp'
abbrlink: 2239095a
date: 2023-02-06 19:43:00
---

# ARM伪指令

伪指令本身不能被CPU所识别，但是能被汇编器编译成一条或多条CPU所识别的指令。

## ldr伪指令

`ldr r0, [r1] @汇编指令`

要判断是否是伪指令，只需要看指令中是否存在`[]`，有`[]`的是加载指令，没有的则是伪指令

{% tip %}

`ldr r0, #0x1ff`是不合法的指令

其中地址是不合法的，{% span yellow,  存在一个8bit的立即数能够循环右移偶数位得到，这个立即数就是合法的 %}

要想执行这条本来不合法的指令，我们就可以通过伪指令实现。

```assembly
.code 32
.global _start
.text 
_start:
	ldr r0, #0x1ff
	ldr r1, =text   @将立即数本身放到r1寄存器 
	ldr r2, text	@将立即数指向的内存中的数据加载到r2寄存器中
	
text:
	.word 0x12345678
.end
```

{% endtip %}

## NOP伪指令

`mov r0, r0`

为了优化流水线而设计的，相当于空操作

## ARM伪操作

完成一些辅助功能的操作，在对汇编源程序进行汇编时由汇编程序处理，而不是在程序运行期间由机器执行。

**汇编文件中的以`.`开头的都是位操作**

```assembly
.text   @往下的代码放到代码段 
.data	@往下的代码放到数据段
.bss	@往下的代码放到未初始化的数据段 
.equ	.equ NUM, #20  @类似于C语言中的宏
.global @将一个标号声明为全局的
.extern
.byte 
.word 0x12345678
.space 1024  @分配1024字节的空间
.string 
	.string "abcdef"  @分配7个字节的空间 
.ascii 
.arm   == .code 32
.thumb == .code 16
.section <section_name>  
```

## 字符串的比较strcmp 

```

```

