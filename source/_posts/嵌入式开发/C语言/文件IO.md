---
title: 文件IO
categories: 嵌入式开发
tags:
  - 嵌入式开发
  - C语言
cover: https://picx.zhimg.com/80/v2-7920c06393196b3066564791c6ec5f24_720w.webp
abbrlink: be1c1682
date: 2022-11-05 13:54:19
---

# 文件IO

## 前言

IO操作是一切实现的基础，我们之前写的程序里面的数据都是在内存中进行操作，数据随着程序的结束在内存中就被销毁了。

如果程序中产出的数据有保留的价值或者我们希望之后还能够使用，我们可以把这些数据保存到文件中。这些数据我们可以从保存的文件中读取出来进行使用或者二次加工 。

所以文件IO输入输出的操作是一切实现的基础，没有IO操作我们程序的结果是没有的。

### 标准IO和系统调用IO

IO分为**`stdio`标准IO**和**`sysio`系统调用IO（文件IO）**

两种IO都可以使用的情况下，我们优先选择标准IO

> 标准IO 是依赖于系统调用IO实现的，因为我们在不同编译环境下，机器的内核可能是不一样的，所以不同的`stdio`依赖的系统调用IO是不一样的。以`fopen`函数为例，在Linux环境下依赖的函数时`open`，而在Windows环境下依赖`openfile`，所以`open`和`openfile`就是内核提供的系统调用IO。
>
> 这种情况下，我们程序的移植性就很成问题，为了避免这样问题的产生，提高我们程序的移植性，我们会优先选择标准IO

## 标准IO

我们先用`man`打开`fopen();`函数

 ```c
 NAME
        fopen, fdopen, freopen - stream open functions//前三个函数的作用是打开一个stream
 
 SYNOPSIS
        #include <stdio.h>//要包含的头文件
 
        FILE *fopen(const char *pathname, const char *mode);
 ```

其中`FILE *fopen(const char *pathname, const char *mode);`

有两个参数`pathname`是指定要打开的文件，`mode`打开文件的权限，返回值`FILE *`  是一个结构体的起始位置。

```c
RETURN VALUE
       Upon successful completion fopen(), fdopen() and freopen() return a FILE pointer.  Otherwise, NULL is returned and errno is set to
       indicate the error.
```

`fopen()`函数调用成功会返回一个`FILE *`指针，失败会返回`NULL`空指针，并且会设置一个`errno`



 Start-BitsTransfer -Source https://down.qq.com/lol/pingbao/WeGameMiniLoader.LOL.2.6.8.1407.exe -Destination F:ser

