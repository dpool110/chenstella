---
title: C语言奇怪的声明
categories: 嵌入式开发
tags:
  - 嵌入式开发
  - C语言
abbrlink: 36366
date: 2022-10-25 18:06:08
cover: https://picx.zhimg.com/80/v2-b575517441b44bb1b035f775e58da0f0_720w.webp
---

指针常量-常量指针、指针数组-数组指针、指针函数-函数指针、函数指针函数到底是些啥？

<!-- more -->

# C语言奇怪的声明

## 前言

<div align="center">
    <img src="http://img.dpool.love/202312071113341.png" alt="image-20230131203952546" style="zoom:50%;"><img src="http://img.dpool.love/202312071113469.png" alt="image-20230131204003006" style="zoom: 67%;">
</div>

**指针常量-常量指针、指针数组-数组指针、指针函数-函数指针、函数指针函数**

听完老师讲后知道了辨别的方法——后两个字就是它的本质，听了这句话，我的嘴角露出了轻蔑的笑容，就这？

<img src="http://tva3.sinaimg.cn/bmiddle/ceeb653ely1g10fyg0z7wj206o06o74j.jpg" style="zoom: 67%;" >

直到我看见了这些题目

```
a) 一个整型数（An integer）

b) 一个指向整型数的指针（A pointer to an integer）

c) 一个指向指针的的指针，它指向的指针是指向一个整型数（A pointer to a pointer to an integer）

d) 一个有10个整型数的数组（An array of 10 integers）

e) 一个有10个指针的数组，该指针是指向一个整型数的（An array of 10 pointers to integers）

f) 一个指向有10个整型数数组的指针（A pointer to an array of 10 integers）

g) 一个指向函数的指针，该函数有一个整型参数并返回一个整型数（A pointer to a function that takes an integer as an argument and returns an integer）

h) 一个有10个指针的数组，该指针指向一个函数，该函数有一个整型参数并返回一个整型数（ An array of ten pointers to functions that take an integer argument and return an integer ）
```

<img src="http://img.dpool.love/202312071453530.png" style="zoom:50%;" />

我不是会了吗？？？这是个啥？？？看了几题我就知道今天的课算是白听了，不得不记录一下。

## 指针常量-常量指针

首先回答下面的问题，如果下面的问题完全没问题可以直接跳到下一个知识点，如果回答的有些问题，不如再看看。

<details>
    <summary><b>常量指针与指针常量的区别？</b></summary>
    	1. 指针常量指针是常量，常量指针指针指向常量<br>
    	2. 指针常量不可以通过指针名去修改指针的指向，可以通过指针常量修改指向空间的数据；<br>
    	&nbsp&nbsp&nbsp&nbsp常量指针可以修改指针的指向，但是不能通过指针修改指向空间的数据；
</details>
<details>
    <summary><b>判断下面程序对错，并说明理由</b></summary>
	str是一个指针常量，"apple"是在常量区中
</details>

```c
int main()
{
    char * const str = "apple";
    str = "orange";
    
    printf("%s\n", str);
    
    return 0;
}
```

### 常量

首先，`const`的功能是用来修饰只读的，它是C语言关键字的一员，当变量使用`const`关键字修饰时,变量名变为只读的了。我们只能访问常量，而无法对常量进行其他的操作。

```c
int main(int argc, char *argv[])
{
    //下面都是const的用法
	const int a = 100;	//int const a = 100;

	a = 200;	//报错！ 不能通过变量名a进行修改,因为变量名a是只读的

	return 0;
}
/*
编译报错：
	error: assignment of read-only variable ‘a’
  	a = 200;//不能通过变量名a进行修改,因为变量名a是只读的
*/
```

上面的程序会报`error`，就是因为`a`变量此时是只读的状态，我们无法直接对`a`变量进行赋值的操作。

但是，我们有指针啊，很多事情都是太过直接，我们需要委婉一点，使用指针侧面入手。

```c
int main(int argc, char *argv[])
{
	//const int a = 100;
	int const a = 100;
	int *p = &a;

	printf("a = %d\n", a);
	
	//a = 200;不能通过变量名a进行修改,因为变量名a是只读的
	*p = 9527;

	printf("a = %d\n", a);

	return 0;
}
/*
输出
	a = 100
	a = 9527
*/
```

在编译阶段会报警告，我们可以忽略，输出结果后我们发现，使用我们使用指针确实修改了`a`变量的值。

既然可以用指针进行修改，<font style="color:red">说明`const`关键字是修饰的变量名，并不是存储空间</font>，此时`a`变量的存储空间依旧是在栈中，再用变量名`a`操作就只有只读的权限了。

### 指针常量和常量指针

指针常量和常量指针，只看后两个字能够清楚，指针常量是常量，而常量指针是指针。这样紫说可能还不是很清楚，要看它们俩的定义：

```c
//指针常量
int * const p;

//常量指针
const int *p;
char const *p;
```

观察关键字`const`所在的位置，指针常量`const`是在变量前，而常量指针`const`是在`*`前面的。

#### 指针常量的特性

指针常量不能通过指针名去修改指向，但是可以通过指针常量修改指向空间的数据

```c
int main(int argc, char *argv[])
{
	int a = 100, b = 200;
	int * const p = &a;

	printf("a = %d &a = %p\n", a, &a);
	printf("b = %d &b = %p\n", b, &b);
	printf("*p = %d p = %p\n", *p, p);
	printf("=========变化中=========\n");
	
    //修改指向空间的数据
    *p = 9527;
    
	printf("a = %d &a = %p\n", a, &a);
	printf("b = %d &b = %p\n", b, &b);
	printf("*p = %d p = %p\n", *p, p);

	return 0;
}
/*
输出：
	a = 100 &a = 0x7ffd6c26cea8
	b = 200 &b = 0x7ffd6c26ceac
	*p = 100 p = 0x7ffd6c26cea8
	=========变化中=========
	a = 9527 &a = 0x7ffd6c26cea8
	b = 200 &b = 0x7ffd6c26ceac
	*p = 9527 p = 0x7ffd6c26cea8
*/
```

但是我们把第12行代码修改为`p = &b;`后，在编译阶段会报错：

```shell
error: assignment of read-only variable ‘p’
  p = &b;
    ^
```

这里我们使用`指针p`去指向`变量b`的地址，这样的操作对于指针常量是不允许的。

>**注意:**
>	**如果使用指针常量,一定要给指针常量做初始化**

#### 常量指针的特性

常量指针可以修改指针的指向，但是不能通过指针修改指向空间的数据

常量指针和指针常量的特性就是翻过来了，代码我这里就不贴了（毕竟我懒），只需要修改上面第四行代码。



## 指针数组-数组指针

还是老方法，我们先分辨这两个的真面目：指针数组是一个数组，而数组指针是指针。

品一下感觉少了点东西，要知道两个的真正含义我们需要把修饰它们的前两个字的含义也加上，所以：

**指针数组：**是数组，数组中存储的是指针	`int *p1[5];`

**数组指针：**是指针，指针指向的是一个数组	`int *p2[5];`

这样我们知道了指针数组和数组指针各自代表的是什么，我们来具体看看他们是什么成分

### 指针数组

指针数组就是数组，数组中的成员都是相同类型的指针

定义：`int *p1[5];`

首先，对于语句`int*p1[5]`，因为`[]`的优先级要比`“*”`要高，所以 `p1` 先与`[]`结合，构成一个数组的定义，数组名为 `p1`，而`int*`修饰的是数组的内容，即数组的每个元素。也就是说，该数组包含 5 个指向 int 类型数据的指针，因此，它是一个指针数组。[^2]

<img src="https://img-blog.csdnimg.cn/20190917164233526.jpg">

我们每次写的`main`函数其中的第二个参数`char *argv[]`就是指针数组，我们也叫它命令行参数，前面的`int argc`代表指针数组中元素的个数。

```c
int main(int argc, char *argv[])//命令行参数
{
	int i = 0;//循环变量
    
	printf("argc = %d\n", argc);
	
    for(i = 0; i < argc; i++)
		printf("argv[%d] = %s\n", i, argv[i]);
	
    return 0;
}
/*
执行：./a.out
输出：
	1
	./a.out

执行：./a.out abc 123
输出：
	3
	./a.out
	abc
	123
*/
```

我们可以看到每次我们使用`./a.out`允许我们的程序时，`./a.out`就存储再`*argv[]`数组中，如果在`./a.out`后面用空格隔开添加其他参数也是可以的。

### 数组指针

数组指针是一个指针，该指针指向数组，而数组指针就是用来和多维数组产生关联的。

定义：`int (*p2)[5];`

对于语句`int(*p2)[5]`，`()`的优先级比`[]`高，`*`号和`p2`构成一个指针的定义，指针变量名为`p2`，而`int`修饰的是数组的内容，即数组的每个元素。也就是说，`p2`是一个指针，它指向一个包含 5 个 `int` 类型数据的数组。很显然，它是一个数组指针，数组在这里并没有名字，是个匿名数组。[^2]

<img src="https://img-blog.csdnimg.cn/20190917164330539.jpg">



由此可见，对指针数组来说，首先它是一个数组，数组的元素都是指针，也就是说该数组存储的是指针，数组占多少个字节由数组本身决定；而对数组指针来说，首先它是一个指针，它指向一个数组，也就是说它是指向数组的指针，在64位系统下永远占8字节，至于它指向的数组占多少字节，这个不能够确定，要看具体情况。

```c
int main(int argc, char *argv[])
{
	int a[2][3] = {11,22,33,44,55,66};
	int b[3][3] = {99,88,77,66,55,44,33,22,11};
	int i = 0, j = 0;
	int (*p)[3];//声明数组指针

	p = a;

	for(i = 0; i < 2; i++)
	{
		for(j = 0; j < 3; j++)
		{
			//printf("a[%d][%d] = %d\n", i, j, a[i][j]);
			//printf("*(*(a+%d)+%d) = %d\n", i, j, *(*(a+i)+j));
			printf("*(*(p+%d)+%d) = %d\n", i, j, *(*(p+i)+j));
			//printf("p[%d][%d] = %d\n", i, j, p[i][j]);
		}
	}
	
	p = b;

	for(i = 0; i < 3; i++)
	{
		for(j = 0; j < 3; j++)
		{
			printf("p[%d][%d] = %d\n", i, j, p[i][j]);
		}
	}

	return 0;
}
```

## 指针函数-函数指针

从后两个字判断，指针函数是函数，而函数指针是指针，我们再说清楚一点

**指针函数：**是一个函数，这个函数的返回值是一个指针    `int *fun(int x,int y);`

**函数指针：**是一个指针，这个指针指向的是一个函数    `int (*fun)(int x,int y);`

### 指针函数

本质上是一个指针，但指向的是一个函数首地址（指针不能偏移），可以用函数指针来传递函数，常用于回调函数中

**定义：** `int *fun(int x,int y);`

其中，`func`是一个函数，`int *`作为一个整体，是 `func`函数的返回值，是一个指针的形式。

#### 指针函数返回字符串[^3]

以典型的字符串复制函数`strcpy`为例子，传入两个字符串`a b`指针做参数，拷贝`b`的内容到`a`之后返回a的指针

```c
char* strcpy(char* a,char* b)
{
    char* c=a;		//拷贝a的地址
    assert((a != NULL) && (b != NULL)); //判断输入正常
    while((*c++ = *b++)!='\0'); //使用拷贝的地址进行自加，将b的程序赋值到a中
    return a;	//返回a的地址
}
```

> **assert（条件语句）：断言函数，相当于一个if判断句，判断内容为真时程序正常运行，为假则报错，终止程序执行**



### 函数指针

本质上是一个函数，函数的返回值是一个指针，常用于返回数组、字符串等数据结构指针

**定义：** `int (*fun)(int x,int y);`

函数指针是需要把一个函数的地址赋值给它，有两种写法：[^4]

`fun = &Function；`
`fun = Function;`

取地址运算符&不是必需的，因为一个函数标识符就表示了它的地址，如果是函数调用，还必须包含一个圆括号括起来的参数表。

调用函数指针的方式也有两种：

`x = (*fun)();`
`x = fun();`

两种方式均可，其中第二种看上去和普通的函数调用没啥区别，如果可以的话，建议使用第一种，因为可以清楚的指明这是通过指针的方式来调用函数。当然，也要看个人习惯，如果理解其定义，随便怎么用都行啦。

#### 函数指针做回调函数

**回调函数就是一个通过函数指针调用的函数**，我们把函数的**指针（地址）**作为参数传递给另一个函数，然后在该函数中使用地址来回调被传递过来的函数，这就是我们说的回调函数

下面为一个演示函数，主函数中将函数指针传入中间函数，然后中间函数调用指向的函数

```c
#include<stdio.h>

int callback_1(void) //回调函数1主体
{
    printf("call_1\n");
    return 0;
};

int callback_2(void) //回调函数2主体
{
    printf("call_2\n");
    return 0;
};

//定义一个处理函数，传入的是函数指针
int Handle(int (*callback)(void))  
{
    //回调函数
    callback();
}

int main()
{
    //定义两个函数指针来指向函数地址
    //不定义也可以，因为函数名称本身就是函数入口地址
    int (*call1)(void)=&callback_1;
    int (*call2)(void)=&callback_2;

    Handle(call1);
    Handle(call2);

    //改变函数指针指向
    call1=&callback_2;
    Handle(call1);
    return 0;
}
/*
结果：
	call_1
	call_2
	call_2
*/
```

## 函数指针数组

函数指针数组是一个数组，数组中的每个成员都是函数指针，可以和函数指针和指针数组一起理解

定义：`int (*arr[5])(int, int);`

这里我们直接用命令行参数版本的计算器来理解

```c
int add(int a, int b)
{
	return a + b;
}

int sub(int a, int b)
{
	return a - b;
}

int mul(int a, int b)
{
	return a * b;
}

int dev(int a, int b)
{
	return a / b;
}

int main(int argc, char *argv[])
{
	int a = 13, b = 7;
	//int (*p)(int , int);//函数指针
	int (*arr[4])(int, int);//函数指针数组
	int i = 0;

	arr[0] = add;
	arr[1] = sub;
	arr[2] = mul;
	arr[3] = dev;

	for(i = 0; i < 4; i++)
	{
		printf("arr[%d](%d, %d) = %d\n", i, a, b, arr[i](a, b));
	}

	return 0;
}
```

## 写在最后

这里也就照着网上的文章稍微总结一下，留着以后复习时候可以翻一翻，不过我心里还是想着我的博客，新找到一个好看的主题，希望可以成功整好，就写到这里吧，然后，开摆！

<img src="http://tva3.sinaimg.cn/bmiddle/006APoFYly8h58qyznx20g3046061jrq.gif">

## 参考文章

[^1]:https://blog.csdn.net/qq_36132127/article/details/81940015
[^2]:https://blog.csdn.net/mick_hu/article/details/100931034
[^3]:https://blog.csdn.net/qq_45396672/article/details/119396143
[^4]:https://blog.csdn.net/luoyayun361/article/details/80428882
