---
title: ARM汇编-栈操作指令
abbrlink: d3f2a9c1
date: 2023-02-04 11:11:50
tags:
  - 嵌入式开发
  - ARM汇编
  - A53
categories:
  - 嵌入式开发
  - A53
cover: https://images8.alphacoders.com/130/thumbbig-1300753.webp
---

# ARM汇编-栈操作指令

## ARM中栈种类

在ARM中有4种栈，分别是：**减栈、增栈、满栈、空栈**

- 减栈：向内存地址减小的方向发生变化
- 增栈：向内存地址增加的方向发生变化
- 满栈：栈指针（sp）指向的栈顶有有效数据
- 空栈：栈指针（sp）指向的栈顶没有有效数据

{% tip info %}

在学习ARM的三级流水线时，执行取指、解码、执行三步操作时对PC进行偏移，每一步都偏移PC-4。

上述偏移之所以是PC-4，原因就是在ARM种默认的栈种类就是减栈。

{% endtip %}

上述4种栈也可以进行组合，再次区分为： 满减栈、满增栈、空减栈、空增栈。**ARM中默认使用的栈的形式是满减栈。**

## 栈指令

`LDMXX sp!, {r1, r2, r3, r5, r8}`

将`sp`指向的栈空间中的数据加载到 r1, r2, r3, r5, r8 寄存器中

`STMXX sp!, {r1, r2, r3, r5, r8}`

将 r1, r2, r3, r5, r8 寄存器中的值存放到`sp`指向的栈空间中

```
其中xx:
  	fd  满减  ldmfd sp!, {r1, r2, r3, r5, r8} 
  	fa  满增
  	ed  空减
  	ea  空增 
```

> 往栈空间中放数据时，如果需要先改变`sp`的值，则为满栈，如果可以直接放数据则为空栈。

```assembly
stmfd sp!, {r0-r5, r8}    === push {r0-r5, r8} 压栈操作 
ldmfd sp!, {r0-r5, r8}    === pop {r0-r5, r8}  弹栈操作
```

按满减栈进行压栈, 也按满减栈进行弹栈, 就能恢复寄存器的内容

{% tip info %}

## 局部变量在栈空间中的分布

<img src="http://img.dpool.love/202312071455898.png" alt="image-20230204181304805" style="zoom:33%;" />

<iframe src="//player.bilibili.com/player.html?aid=608759496&bvid=BV1U84y1V7te&cid=993032407&page=1" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true" width="100%" height="500px"> </iframe>

{% endtip %}

