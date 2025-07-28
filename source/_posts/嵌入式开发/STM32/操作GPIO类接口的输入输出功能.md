---
title: 操作GPIO类接口的输入输出功能
tags:
  - 嵌入式开发
  - STM32
categories:
  - 嵌入式开发
  - STM32
cover: 'https://images8.alphacoders.com/111/thumbbig-1117303.webp'
abbrlink: cca9e6ce
date: 2022-12-24 20:22:54
---

# 操作GPIO类接口的输入输出功能

手上的板子上的外设，纯GPIO类的设备有led、beep和按键（key），在框架图上可以看到，这些外设和cpu连接，通过cpu要去控制这些外设的状态。就要从cpu输出一些命令来控制，所以输出功能相当于是控制，而输入功能就相当于检测。

## 实现LED灯的驱动程序

分析具体的外设的工作原理

<div align="center">
    <img src="http://img.dpool.love/202312071113341.png" alt="image-20230131203952546" style="zoom:50%;"><img src="http://img.dpool.love/202312071113469.png" alt="image-20230131204003006" style="zoom: 67%;">
</div>

{% timeline 实现步骤, blue %}

<!-- timeline  -->

通过分析开发板的电路原理图可以得知外设具体对应的管脚，`LED0~1`分别对应CPU`PC1~3`管脚，并且管脚上输出高电平可以控制灯亮，反之灯灭。

led0  ---> pc1    输出高  亮

led1  ---> pc2    输出低  灭

led2  ---> pc3

<!-- endtimeline -->

<!-- timeline -->

通过APB2 外设总线将GPIOC组的时钟使能

<!-- endtimeline -->

<!-- timeline -->

初始化GPIO 

1. 设置为推挽输出模式
2. 选择 1 2 3 号管脚 
3. 设置输出的频率

<!-- endtimeline -->

{% endtimeline %}

## 代码实现

 {% tabs  %}

<!-- tab led.h -->

```c
#ifndef __LED_H__
#define __LED_H__

#include "stm32f10x_conf.h"

//Led灯的初始化
extern void led_init(void);

extern void led_on(int num);

extern void led_off(int num);

#endif
```

<!-- endtab -->

<!-- tab led.c -->

```c
#include "led.h"

void led_init(void)
{
	//声明一个结构体类型变量，将来用于初始化gpio
	GPIO_InitTypeDef Led_Value;
	
	//通过APB2外设总线将GPIOC组的时钟进行使能
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOC, ENABLE);
	
	//配置gpio
	Led_Value.GPIO_Mode = GPIO_Mode_Out_PP;
	Led_Value.GPIO_Pin = GPIO_Pin_1 | GPIO_Pin_2 | GPIO_Pin_3;
	Led_Value.GPIO_Speed = GPIO_Speed_50MHz;
	
	GPIO_Init(GPIOC, &Led_Value);
}

void led_on(int num)
{
	switch(num)
	{
		case 0:
			GPIO_SetBits(GPIOC, GPIO_Pin_1);
			break;
		case 1:
			GPIO_SetBits(GPIOC, GPIO_Pin_2);
			break;
		case 2:
			GPIO_SetBits(GPIOC, GPIO_Pin_3);
			break;
	}
}

void led_off(int num)
{
	switch(num)
	{
		case 0:
			GPIO_ResetBits(GPIOC, GPIO_Pin_1);
			break;
		case 1:
			GPIO_ResetBits(GPIOC, GPIO_Pin_2);
			break;
		case 2:
			GPIO_ResetBits(GPIOC, GPIO_Pin_3);
			break;
	}
}


```

<!-- endtab -->

{% endtabs %}

