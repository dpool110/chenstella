---
title: stm32-基础day01
tags:
  - 嵌入式开发
  - STM32
categories: 
  - 嵌入式开发
  - STM32
cover: https://dogefs.s3.ladydaily.com/~/source/wallhaven/small/r2/r25kqj.jpg?w=400&h=200&fmt=webp
abbrlink: adc49732
date: 2022-12-22 21:31:14
---

# stm32-基础day01

STM32是mcu单片机，是嵌入式的开发板。

## 嵌入式简介

{% tip info %}

**什么是嵌入式？**

嵌入式系统是以应用为中心，以计算机技术为基础，软硬件可剪裁，适应应用系统对功能、体积、成本、功耗等严格要求的专用计算机系统。

{% endtip %}

我们之前学的C\C++、uc、qt等，它们是用在应用开发的。uc解决的就是**计算机体系结构**和**操作系统原理**（内存管理、进程管理、进程间通信、网络......）

### 嵌入式系统的构成

硬件最小系统：

| 硬件     | 介绍                                                         |
| :------- | :----------------------------------------------------------- |
| 处理器   | ARM、mips、PowerPC------>**RSIC 精简指令集**<br>x86(lintel)------>**CSIC 复杂指令集** |
| 内存     | 掉电丢失的------>ram、SRAM、DRAM<br />掉电不丢失的------>rom 只读存储器<br><p style="text-indent:9em">prom 可编程的 一次写入<br />EPROM 可擦除的，可以多次写入<br />EEPROM 电信号可擦除</p> |
| Falsh    | 闪存------>程序存在闪存中<br />[NOR-flash和Nand-flash的区别](https://blog.csdn.net/qq_43857510/article/details/102673106)<br />Emmc（A53） |
| 电源     | \                                                            |
| 复位电路 | \                                                            |
| 时钟电路 | \                                                            |
| ...      | ...                                                          |

### 软件的构成

主要是应用程序，使用C语言、UC、C++、QT进行开发。

以及操作系统，主要是Linux操作系统，**Linux操作系统是基于时间片轮转的，基于优先级的，先来先服务的抢占式操纵系统。**

嵌入式的操作系统还有：UCOS-ll 操作系统（实时操作系统）和vxworks（实时操作系统）

单片机上一般不跑操作系统，我们把单片机这种驱动程序叫做裸板程序。

## 嵌入式软件的开发模式

我们需要在PC上完成代码的编辑和编译工作，之后烧录（Host）到目标的下位机（Taget）中，烧录的介质非常多，我们可以使用USB、rs232、network以及jtag。

## 基于物联网的智能终端

### 物联网的基础框架 IOT

嵌入式是物联网的基础和保障，物联网是基于犬儒是系统的数据存储、转存、分析和处理等

**物联网的层次**

- 应用层  物联网云平台也有对应的配套的手机APP也可以在web上 
- 平台层（）云

	华为  Ocean connect 
	小米  小米IOT
	阿里  AIOT
	百度  天工 
	移动  OneNet
	瑞通  EsLink

- 网络层（管）手机热点、路由器、基站
- 感知层（端）STM32F103RTB6 + esp8266（WiFi模块）

![image-20230130230008895](http://img.dpool.love/202312071116531.png)

## 了解开发板设备

**STM32F103RBT6**

<img src="http://img.dpool.love/202312071117297.png" alt="STM32F103RBT6" style="zoom:50%;" />

- 4位的共阴极的数码管 
- 74HC138  38译码器   
- 74HC595  将串行的数据转化为并行的数据进行输出 
- 高精度的可调电阻 连接了A/D 转换的管脚 
- 无线模块的接口 
- ESP8266 NB-IOT  GPRS 接口 
- 红外接受
- DHT11 SHT30 温湿度的传感器  --》 采集温湿度的信息
  eeprom  
- OLED 显示屏的接口 
- SD卡 --- 当采集的数据过多的时候，可以存放到SD卡
  flash 
- STM32F103RBT6 --- arm core   cpu 
- 重启按键  
- PWM 接口  扩展 IO  
- RTC 电池  ---》 给系统时钟进行供电 
- STM32F103CBT6  负责烧录程序到flash 
- 5V的直流供电接口    Debug 接口 （ST-Link）  
- 电源开关   
- LED灯 3个   蜂鸣器   用户按键  3个

{% tip info %}

**了解芯片：？**

无论是STM32（单片机） 还是SOC (A53 高端芯片)  system on chip 跑操作系统，都可以自己组成一个微型的计算机系统。

{% endtip %}

STM32F103 =  cpu + 内存 + 硬盘 + 外设

- cpu 是执行指令的   
- 硬盘是存储程序
- 内存存的是数据+指令 (支援程序的运行)
- 外设程序员通过代码指令操作外设

## 搭建开发环境 

​	搭建一个模板工程

{% folding 步骤 %}

1. 在桌面上新建一个目录 《stm32f103》  

2. 在1新建的目录中再创建一个目录 01test 

3. 在01test 新建以下文件夹

   cmsis   cortex-m存放核心文件  

   fwlib    固件库 存放ST公司的官方固件

   mylib   存放程序员自己写的驱动

   project  存放项目工程文件 

   usr   存放主文件 + ST 官方补充的文件 

4. 将STM32RBT6\ST官方固件\STM32F10x_StdPeriph_Lib_V3.5.0\Libraries\CMSIS\CM3\CoreSupport下的所有文件拷贝到cmsis

   将STM32RBT6\ST官方固件\STM32F10x_StdPeriph_Lib_V3.5.0\Libraries\CMSIS\CM3\DeviceSupport\ST\STM32F10x\startup\arm\startup_stm32f10x_md.s拷贝到cmsis //md 指的中等容量的产品

5. 将STM32RBT6\ST官方固件\STM32F10x_StdPeriph_Lib_V3.5.0\Libraries\CMSIS\CM3\DeviceSupport\ST\STM32F10x\下面的三个文件拷贝到usr中

6. 将STM32RBT6\ST官方固件\STM32F10x_StdPeriph_Lib_V3.5.0\Libraries\STM32F10x_StdPeriph_Driver\inc  和src 拷贝到 fwlib 

7. 将STM32RBT6\ST官方固件\STM32F10x_StdPeriph_Lib_V3.5.0\Project\STM32F10x_StdPeriph_Template\下的6个文件拷贝到 usr中

8. 创建工程文件

   打开keil， 点击project, 选择New uVersion project... ；选择刚刚创建的目录, 文件名test, 点击保存, 在弹出的对话框中搜索“stm32f103rb”, 点击ok

9. 弹出的对话框, Mangment RUN-TIME env .... 直接关闭即可

10. 点击第三行菜单栏 “options for taget” 熟悉集成开发环境，点击“品字”, 将 Taget 1 改为 Taget, 将 Source group 1 改为cmsis，再将mylib, fwlib, user 添加进来 

11. 点击cmsis, 点击 Add files, 给4个目录增加.c .s 的文件，fwlib 目前只放了stm32f10x_rcc.c  点击ok即可

12. 将main.c 中的内容全部删除，增加一个最简单的空入口函数即可

13. 头文件的处理, 点击魔法棒，选择C/C++ --> define --->STM32F10X_MD,USE_STDPERIPH_DRIVER
    Include paths , 选择... , 双击之后，又出来..., 添加路径。

点击全部保存，点击rebuild, 如果报0 errors 0 warnings , 表示环境OK！

{% endfolding %}

## 安装上位机开发环境 Keil  

### 安装KEIL开发环境

{% folding 步骤 %}

1. 打开<STM32RBT6\开发工具\MDK520>目录,双击<MDK520.exe>
2. 安装时基本上直接下一步下一步即可
3. 注意
   1. 有一步需要选择安装路径,安装路径一定不要有中文
   2. 有一步需要大家填写姓 名 公司名称 邮箱,都写数字1即可
   3. 有一步会弹出小的对话框提示是否安装ULINK驱动,选择安装
   4. 安装结束后会弹出网页,关闭网页
   5. 安装结束后会弹出自动更新补丁的对话框,关闭更新补丁的对话框
4. 安装完KEIL之后，在<3_MDK\STM32RBT6\开发工具\MDK520>目录中有5个以.pack结尾的文件,安装从上往下的顺序,依次双击安装即可

{% endfolding %}

### 安装仿真器驱动

打开目录<STM32RBT6\开发工具\STLink V2驱动\STLINK_V2_USBDriver>中

- 如果是64bit操作系统安装<dpinst_amd64.exe>
- 如果是32bit操作系统安装<dpinst_x86.exe>