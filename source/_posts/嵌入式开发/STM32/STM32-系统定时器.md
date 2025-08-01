---
title: STM32-系统定时器
tags:
  - 嵌入式开发
  - STM32
categories:
  - 嵌入式开发
  - STM32
cover: 'https://images7.alphacoders.com/617/thumbbig-617537.webp'
abbrlink: 35cd3ec5
date: 2022-12-26 16:20:01
---

# STM32-系统定时器

## 前言

{% link 白板导图delay,  https://boardmix.cn/app/editor/CSKY0IRwkkdE7C2t1DbJ0A , https://raw.githubusercontent.com/dpool110/picgo/master/202210310921391.png %}

在STM32中我们可以使用循环来实现延时（delay）功能，这种延时被称为粗略的延时。而要想实现精确的延时就要使用**系统定时器**（timer）systick、sysclock 

常用的定时器资源有：系统定时器（timer）、RTC（给系统时钟供电）、WDG（[看门狗](http://www.dpool.love/posts/494954c0.html)）。普通的定时器只提供了最基础的计时功能，而**看门狗**除了提供计时功能，还可以复位系统（到达定时时间，重启系统）。

**无论对于普通定时器，还是系统定时器，定时时长都由时钟频率和计数值决定。**

`定时时长 = 时钟周期（1/时钟频率） * 计数值`

STM32中的系统定时器不是由st公司提供的，而是由ARM核心提供的。了解系统定时器要从**时钟频率**和**计数值**进行分析。

## 时钟频率

### 系统时钟源

<img src="http://img.dpool.love/202312071304225.png" alt="image-20230205164906997" style="zoom:50%;" />

**一共有5种系统时钟源**

- HSE  系统外部的高速时钟
- HSI  系统内部的高速时钟
- PLL  倍频器 (乘法器) 但是本身不产生基础时钟
- LSI  系统内部的低速时钟  
- LSE  系统外部的低速时钟

其中**选择HSE时钟源用来给系统时钟提供时钟频率**，HSI和HSE都可以通过倍频器达到CPU的频率，但是由于**系统外部的晶振要比内部的晶振稳定**，所以选择HSE作为系统时钟源。

## 计数值

### SysTick 定时器

SysTick 是一个 **24 位的倒计数定时器**，当计到 0 时，将从 RELOAD 寄存器中自动重装载定时初值。只要不把它在 SysTick 控制及状态寄存器中的使能位清除，就永不停息。

### SysTick中的寄存器

<img src="http://img.dpool.love/202312071304077.png" alt="image-20230205173715591" style="zoom:67%;" />

其中灰色部分代表透明，表示不可见，为了将来向后兼容。

<img src="http://img.dpool.love/202312071304152.png" alt="image-20230205173240728" style="zoom:67%;" />

其中只有校验值寄存器是只读的。

#### 控制及状态寄存器

<img src="http://img.dpool.love/202312071304523.png" alt="控制及状态寄存器" style="zoom: 67%;" />

控制及状态寄存器使能系统时钟，如果没有设置系统时钟则地址被设为`0x00000004`

- bit[0] ：使能/禁止 counter
- bit[1] ：设置为0/1，当计数值减小到0的时候，触发中断请求；1不触发中断请求
- bit[2] :  设置为0/1代表外部/内部的时钟源
- bit[16] ：定时时间到达以后返回1，如果不使用中断，当读出来的是1，则定时时间到了；当使用中断的时候, 不需要读bit16 

#### 重装载值寄存器

<img src="http://img.dpool.love/202312071306626.png" alt="重装载值寄存器" style="zoom:67%;" />

重装载值寄存器的计数值的范围是bit0~bit23（16M）

#### 当前值寄存器

当前值寄存器保存当前的计数值，bit0 ~ bit23 保存的是计数值

{% tip warning %}

### 官方固件存在问题

了解上面四个寄存器，并且知道时钟频率和计数值，我们可以得到一个精确的延时，但是官方的固件中存在问题。

1. 我们可以通过设置控制及状态寄存器bit[1]来发出中断请求，但是系统定时器是默认开启中断的，所以控制及状态寄存器中bit[16]就不需再读。
2. 只有开启没有关闭

{% endtip %}
