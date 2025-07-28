---
title: STM32-IIC接口类设备OLED
tags:
  - 嵌入式开发
  - STM32
categories: 
  - 嵌入式开发
  - STM32
cover: https://pic2.zhimg.com/80/v2-9d36185b88cf07dceab5108c0cfd93c5_720w.webp
abbrlink: 85bcfab
date: 2022-12-30 11:23:22
subtitle: 持续更新ing...

---

<i class="iconfont icon-xxx"></i>


# STM32-IIC接口类设备OLED

## 简介

显示屏的驱动程序其实就是驱动OLED显示屏的控制器。一般来说，显示屏的驱动程序厂商出厂时就已经提供好了作为驱动开发工程师，就是要移植驱动程序。可以大大提高开发的效率，缩短开发周期。

嵌入式领域，关于显示类的设备有很多比如：LCD、OLED、数码管、段码屏、光立方 ...

显示类设备的接口也有很多：`gpio`、`usart`、`iic`、`spi` ...

设备上显示的信息有字符、字符串、数字、汉字和一些不在ASCII表种的特殊字符(℃)，以及图形、图像 ...

**显示的原理**

OLED或者LCD屏都是通过一个一个像素点显示的

## 实现OLED显示屏的驱动程序

> OLED显示屏的驱动程序，不需要自己实现，驱动工程师只需要移植到自己的开发平台即可。

















