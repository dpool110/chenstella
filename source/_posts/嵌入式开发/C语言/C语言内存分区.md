---
title: C语言内存分区
categories: 嵌入式开发
tags:
  - 嵌入式开发
  - C语言
abbrlink: 56970
date: 2022-10-21 19:02:01
cover: https://pica.zhimg.com/80/v2-0f4fa7b40ca783ef823c5636ef1da4b8_720w.webp
---

C语言会把内存空间分为5大区：栈区、堆区、全局区、常量区、代码区

<!-- more -->

# C语言内存分区

## C语言内存空间5大区

C语言会把内存空间分为5大区：正文段、初始化的数据段（.data）、未初始化的数据段（.bass）、栈和堆

<img src="http://img.dpool.love/202312071452215.png" alt="image-20230204135407743" style="zoom:67%;" />

### 栈区(stack)

栈区由编译器自动分配释放，由操作系统自动管理，无须手动管理。
栈区上的内容只在函数范围内存在，当函数运行结束，这些内容也会自动被销毁。

- 栈区按内存地址<font style="color:red">**由高到低**</font>方向生长，其最大大小由编译时确定，速度快，但自由性差，最大空间不大。
- 栈区是<font style="color:red">**先进后出**</font>原则，即先进去的被堵在屋里的最里面，后进去的在门口，释放的时候门口的先出去。

### 堆区(heap)

**堆区**由程序员分配内存和释放。

- 堆区按内存地址<font style="color:red">**由低到高**</font>方向生长，其大小由系统内存/虚拟内存上限决定，速度较慢，但自由性大，可用空间大。

**堆区动态申请与释放内存**
用`malloc()`,`free()`等函数实现动态分布内存。

```c
void *malloc(size_t);
```

- 参数`size_t`是分配的字节大小；
- 返回值是一个`void*`型的指针，该指针指向分配空间的首地址；
  （`void *`型指针可以任意转换为其他类型的指针）

用`free()`函数进行内存释放，否则会造成内存泄漏。

```c
void free(void * /*ptr*/);
```

- 参数`ptr`是开辟的内存的首地址。
- 无返回值；

### 初始化数据段`.data`

通常将此段称为数据段，它包含了程序中需要明确地赋初值地变量。

### 未初始化数据段`.bss`

在程序开始执行之前，内核将此段中地数据初始化为0或空指针。出现在任何函数外的声明变量存放在非初始化数据段中。

### 正文段

这是由CPU执行的机器指令部分。通常，正文段是可共享的，所以即使是频繁执行的程序在存储器中也只需要一个副本。正文段通常是只读的，以防止程序由于异化而修改其自身的指令。

## 不同变量在内存中的分布

在Linux中我们可以使用`/proc/进程id/maps`来查看程序中声明的不同变量在内存中的分布。

```c
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <unistd.h>

//未初始化的全局变量a1和已初始化的全局变量a2
int a1;
int a2 = 100;

int main(void)
{
    //未初始化和已初始化的局部变量c、var
	int var = 200;
	char c;
    //局部静态变量
	static int a3 = 100;
	static int a4;
    //动态分配的变量m
	int *m = malloc(sizeof(int));
    //const修饰的变量
    const int a5 = 100;
	const int a6;

	printf("var = %p\nc = %p\n", &var, &c);
	printf("a1 = %p\na2 = %p\n", &a1, &a2);
	printf("a3 = %p\na4 = %p\n", &a3, &a4);
	printf("m = %p\n", m);
	printf("pid = %d\n", getpid());
    printf("a5 = %p\na6 = %p\n", &a5, &a6);
	getchar();

	return 0;
}
```

> ```c
> var = 0x7fff3b051a04
> c = 0x7fff3b051a03
> a1 = 0x5620a87e1020
> a2 = 0x5620a87e1010
> a3 = 0x5620a87e1014
> a4 = 0x5620a87e101c
> m = 0x5620a975b260
> a5 = 0x7fff3b051a08
> a6 = 0x7fff3b051a0c
> pid = 3034
> 
> 上面就是在终端打印出的信息，重新打开一个终端，查看3034进程号，对比上面不同变量在内存中的地址我们就可以知道这些变量具体在内存中是如何分布的了。
> ```

 ![image-20230204144310791](http://img.dpool.love/202312071453085.png)

{% tip info %}

通过对比变量地址和打印的内存地址，可以得知：

- 局部变量在栈中
- 全局变量在数据段（初始化的和未初始化的分别在对应的数据段中）
- 局部静态变量在数据段（初始化的和未初始化的分别在对应的数据段中）
- 动态分配的变量在堆中
- const修饰的变量在栈中

{% endtip %}



---

**更多信息参考：**

{% link C语言内存分区,  https://blog.csdn.net/zw_whusgg/article/details/126288914 %}

{% link C语言之堆栈,  https://blog.csdn.net/m0_67761835/article/details/124416227 %}
