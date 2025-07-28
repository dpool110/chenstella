---
title: STM32-看门狗
tags:
  - 嵌入式开发
  - STM32
categories: 
  - 嵌入式开发
  - STM32
cover: https://pic1.zhimg.com/80/v2-938653ab34bc868ccb4c82c78f9cca04_720w.webp
abbrlink: 494954c0
date: 2022-12-26 19:30:53
---

# STM32-看门狗

## 用途

在工业现场环境非常的复杂，人为检测非常不便,可以使用看门狗进行检测超高温、超低温、强烈的电磁干扰，这些环境都会影响设备的正常工作，会造成死机或者程序跑飞。

**将来一旦死机可以利用看门狗对系统进行重启**

## 简介

**看门狗属于定时器资源**，学习系统定时器(systick)时我们知道，决定定时长短或超时时间，由**时钟频率**和**计数值**决定。看门狗的定时长短也是这两个因素，当看门狗的定时时间到达后会直接重启系统。<font style="color:red">**相对于系统定时器看门狗就多了一个复位重启的功能**</font>

如果开启看门狗，但是程序正常，也没有发生死机。在系统定时器(systick)到定时时间重装载值寄存器把重装载的值重装载到当前值寄存器中。在看门狗中我们需要做同样的事，我们称为**feed dog**(喂狗)

##  喂狗

feed dog(喂狗)本质上就是让看门狗的计数值永远减不到0。

使用看门狗资源检测系统，当程序正常且设备没有发生死机，是不需要重启的，就需要每隔一段时间喂狗。

## 看门狗外设资源

看门狗外设分为**独立看门狗(IWDG)**和**窗口看门狗(WWDG)**，在A53或者其他的高端soc芯片中只有一种看门狗，类似于独立看门狗。

### IWDG和WWDG的区别

**相同点：**

1. 都是STM32提供的外设资源
2. 都会因为程序的跑飞或者死机而重启系统
3. 都可以自行调节喂狗的时间

**区别：**

1. 时钟源不同

   <img src="http://img.dpool.love/202312071118410.png" alt="IWDG时钟源" style="zoom:67%;" />

   IWGD用的独立的**40KHz 的RC振荡器**，不需要通过总线使能时钟

   WWDG用的**APB1总线**提供的时钟，需要通过总线使能时钟

1. 产生的中断不同

   **IWDG没有中断**，只要触发IWG，直接重启系统
   
   **WWDG有中断**，当定时时间到达之后可以触发中断
   
3. 计数的范围不同

   IWDG 用了**12bit的计数器**

   WWDG 用了**7bit的计数器**

1. 喂狗的范围不同

   IWDG 只要计数值到达0之前的任意时间都可以
   
   WWDG 有上阙值和下阙值
   
   

## 独立看门狗的寄存器

<img src="http://img.dpool.love/202312071302572.png" alt="独立看门狗框架图" style="zoom:50%;" />

### KR 寄存器(键)

- 写`0xCCCC`启动看门狗
- 写`0XAAAA`喂狗
- 写`0x5555`关闭PR，RLR 寄存器的**写保护**，也就是开启写访问

### PR 寄存器

设置分频系数 ，分频系数越小，分出的频率越大

<img src="http://img.dpool.love/202312071303732.png" alt="image-20221229205841631" style="zoom:50%;" />

`PR[2:0]`低三位有效 

### RLR 寄存器

重装载值寄存器

<img src="http://img.dpool.love/202312071303485.png" alt="image-20221229210107713" style="zoom: 33%;" />

## 实现步骤

{% tabs iwdg %}
<!-- tab iwdg.h-->

```c
#ifndef __IWDG_H__
#define __IWDG_H__

#include "stm32f10x_conf.h"

/*
	用于驱动独立看门狗
	由时钟频率 计数值决定时长
*/
//独立看门狗的初始化
extern void iwdg_init(u32 ms);

/*
	IWDG_KR 0xCCCC 开启看门狗  0xAAAA 喂狗  0x5555 关闭写保护
	IWDG_PR		设置分频系数
	IWDG_RLR	设置计数值
*/
//喂狗操作
extern void iwdg_feed_dog(void);

#endif
```
<!-- endtab -->

<!-- tab iwdg.c-->
```c
#include "iwdg.h"

void iwdg_init(u32 ms)
{
    //关闭PR RLR寄存器的写保护
    IWDG_WriteAccessCmd(IWDG_WriteAccess_Enable);

    //设置时钟频率 时钟频率 = 40KHz/64 = 625Hz
    IWDG_SetPrescaler(IWDG_Prescaler_64);

    //设置重装载值
    IWDG_SetReload(ms);

    //喂狗
    iwdg_feed_dog();

    //开启看门狗
    IWDG_Enable();
}

void iwdg_feed_dog(void)
{
	//喂狗
	IWDG_ReloadCounter(); 
}
```
<!-- endtab -->
{% endtabs %}















