---
title: Java程序流程控制与数组
categories: 编程开发学习
tags:
  - Java
cover: 'https://i.loli.net/2020/07/27/d6gvZ4rhtlNHcjU.jpg'
abbrlink: 2441
date: 2020-10-16 21:05:08
---

# 程序流程控制与数组


## 顺序结构<a id="测试">测试</a>


- 程序从上到下逐行执行，中间没有任何判断和跳转。
- 如果没有任何流程控制，程序总是从上到下执行每条语句。

## 分支结构


- 根据条件，选择性地执行某段代码。
- 有if-else和switch-case两种分支语句。


### if-else结构

- else是可选的
- 针对多个条件表达式之间是“互斥”关系（或者没有交集的关系）。
- 如果多个条件表达式之间有交集关系，需要根据实际情况，考虑应该将哪个结构声明在上面。
- 如果多个条件表达式之间有包含关系，**一定要先处理包含范围小的情况**，否则范围小的没机会运行。
- if-else结构是可以嵌套的。


**if语句三种格式**

``` Java
if(条件表达式){
	执行表达式；
}
```
![java_circl_01](https://i.loli.net/2020/10/06/hw8v29WiysljbzS.png)

``` java
if(条件表达式){
	执行表达式1;
}
else{
	执行表达式2;
}
```


![java_circl_02](https://i.loli.net/2020/10/06/TXJ6aOz5pLiwnNl.png)

``` java
if(条件表达式){
	执行表达式1;
}
else if{
	执行表达式2;
}
... ...
else{
	执行表达式n；
}
```


![java_circl_03](https://i.loli.net/2020/10/06/xbyAjGagsk1cSJe.png)


**具体举例**
``` java
class IfTest{
	public static void main(String[] args){
		//格式1
		int hearBeats = 78;
		if(hearBeats < 60 || hearBeats > 100){
			System.out.println("需要进一步检查！");
		}
		System.out.println("检查结束");
		
		//格式2
		int age = 23;
		if(age < 18){
			System.out.println("你不能结婚");
		}else{
			System.out.println("你可以结婚");
		}
		
		//格式3
		if(age < 0){
			System.out.println("您输入数据非法！");
		}else if(age < 18){
			System.out.println("青年时期");
		}else if(age < 35){
			System.out.println("青壮年时期");
		}else if(]age < 60){
			System.out.println("中年时期");
		}else{
			System.out.println("老年时期");
		}
	}
}
```



- if、else-if、else后面花括号内的多行代码被称为代码块，**一个代码块通常看作一个整体来执行**（除非运行过程中遇到**return、break、continue**等关键字，或是遇到了异常。）
- else的含义是“否则”，**else本身就是一个条件**，这也是把if、else后面代码块统称为条件执行体的原因，**else的隐含条件是对前面的条件取反**。




### switch-case结构


``` java
switch(表达式){
	case 常量1:
		语句1;
		//break;
	case 常量2:
		语句2;
		//break;
	... ...
	case 常量N:
		语句N ;
		//break;
	default;
		语句;
		//break;
}
```

![switch-case结构](https://i.loli.net/2020/10/08/CakJsuxehjVKy7g.png)


- 根据switch表达式中的值，依次匹配各个case中的常量。一旦匹配成功，则进入相应case结构中，调用其执行语句。当调用完执行语句后，仍然继续向下执行其他case结构中的执行语句，直到遇到break关键字或程序终止为止。
- 在switch-case结构中，一旦执行break关键字，就跳出switch-case结构。
- switch结构表达式，只能是**byte、short、char、int四种整数类型和枚举类型、String类型（Java7开始允许）**，6中数据类型之一，**不能是boolean类型**。
- case之后只能声明常量不能声明 范围。
- break关键字是可选的。
- default相当于if-else中的else。

***例子***

``` java
String season = "summer";
switch(season){
	case"spring":
		System.out.println("春天");
		break;
	case"summer":
		System.out.println("夏天");
		break;
	case"autumn":
		System.out.println("秋天");
		break;
	case"winter":
		System.out.println("冬天");
		break;
	default:
		System.out.println("季节输入有误！");
		break; 
}
```

**关于break例题**

``` java
//从键盘输入2019年的“month”和“day”，要求通过程序输入的日期为2019年的第几天

import java.util.Scanner;
class SwitchCaseTest2{
	public static void main(String[] args){
		Scanner scanner = new Scanner(System.in);
		System.out.println("请输入2019年month：");
		int month = scanner.nextInt();
		System.out.println("请输入2019年day：");
		int day = scanner.nextInt();
		
		switch(month){
			case 12:
				sumDays += 30;
			case 11:
				sumDays += 31;
			case 10:
				sumDays += 30;
			case 9:
				sumDays += 31;
			case 8:
				sumDays += 31;
			case 7:
				sumDays += 30;
			case 6:
				sumDays += 31;
			case 5:
				sumDays += 30;
			case 4:
				sumDays += 31;
			case 3:
				sumDays += 28;
			case 2:
				sumDays += 31;
			case 1:
				sumDays += day;
		}
		System.out.println(sumDays);
	}
}

```
**说明break在switch-case中是可选的**

- 凡是使用switch-case的结构，都可以转化为if-else。反之，不成立。
- 写分支结构时，当发现既可以使用switch-case，（同时，switch中表达式的取值情况不太多），又可以使用if-else时，我们优先选择使用switch-case。**原因：switch-case执行效率稍高。**






## 循环结构

- 在某些条件满足的情况下，反复执行特定代码的功能。
- 有for、while、do-while三种循环语句。

![循环结构](https://i.loli.net/2020/10/09/MCFAhlOHG57tmzT.png)
**注：JDK1.5提供了for-each循环，方便地遍历集合、数组元素。**


**循环结构的4个要素**
> ① 初始化条件
② 循环条件 （条件是boolean类型）
③ 循环体
④ 迭代条件


### for循环结构的使用

**for循环的结构**


```java
for(①;②;④){
	③
}
```

执行过程：① -> ② -> ③ -> ④ -> ② -> ③ -> ④ ... -> ② 



for循环圆括号内两个分号是必须的，初始化语句、循环条件、迭代语句都是可以省略的，如果省略了循环条件，则这个循环条件默认为true，将会产生一个死循环。

```java
public class Text09 {

	public static void main(String[] args) {
        //省略了for循环的三个部分，循环条件一直是true
		for (;;) {
			System.out.println(123);
		}
	}
```

> **输出**
>
> 123
>
> 123
>
> ...



使用for循环时，还可以把初始化条件定义在循环体外，把循环迭代语句放在循环体内，这种方式类似下面的while循环。

```java
public class Text10 {

	public static void main(String[] args) {
		int i = 0;
		for (;i<3;) {
			System.out.println(i);
			i++;
		}
		System.out.println("循环结束");
	}
```

> **输出：**
>
> 0
> 1
> 2
> 循环结束




**for循环例题**

```java
/*
题目 ：输入两个正整数m和n，求其最大公约数和最小公倍数
比如：12和20的最大公约数是4，最小公倍数是60
*/

import java.util.Scanner;
class ForTest{
	public static void main(String[] args){
		Scanner scan = new Scanner(System.in);
		
		System.out.println("请输入第一个整数：");
		int m = scan.nextInt();
		int m = scan.nextInt();
		
		System.out.println("请输入第一个整数：");
		int m = scan.nextInt();
		
		//获取两个数的最大公约数
		int min = (m <= n)? m : n;
		for(int i = min;i >= 1;i--){
			if(m % i == 0&& n %i == 0){
				System.out.prinyln("最大公约数为：" + i);
				break;//一旦在循环中执行到break，就跳出循环
			}
		}
		
		//获取两个数的最小公倍数
		int max = (m >=n)? m : n;
		for(int i = max;i <= n*m;i++){
			if(m % i == 0&& n %i == 0){
				System.out.println("最小公倍数为：");
				break;
		}
	}
}


/*
输出所有的水仙花数，所谓水仙花数是指一个3位数，其各个位上数字立方和等于其本身
	例如：153=1*1*1+3*3*3+5*5*5
*/

import java.util.Scanner;
class ForTest2{
	public static void main(String[] args){
		int a,b,c,s;
		for(i = 0;i <= 100;i++){
			c=i%10;
			b=(i/10)%10;
			a=i/100;
			if(Math.pow(a,3)+Math.pow(b,3)+Math.pow(c,3)==i)
		}
		System.out.println(""+i);
	}
}
```


### while循环

> ① 初始化条件
② 循环条件 （条件是boolean类型）
③ 循环体
④ 迭代条件  

**while循环结构**

```java
①
while(②){
	③;
	④;
}
```

执行过程：① -> ② -> ③ -> ④ -> ② -> ③ -> ④ ... -> ② 


**说明**
- 写while循环千万小心不要丢了迭代条件。一旦丢了，就可能导致死循环！
- 写程序时，要避免出现死循环，算法要具有有限性。
- for循环和while循环时可以相互转换的
	**区别：for循环和while循环的初始化条件部分的作用范围不同。**

```java
class WhileTest{
	public static void main(String[] args){
		
		//遍历100以内的所有偶数
		int i = 1;
		while(i <= 100){
			if(i % 2 ==0){
				System.out.println(i);
			}
			i++;
		}
		//出了while循环后，仍可以调用i（for循环和while循环的区别）
		System.out.println(i);// i=101
	}
}
```


### do-while循环

> ① 初始化条件
② 循环条件 （条件是boolean类型）
③ 循环体
④ 迭代条件  

**do-while循环结构**

```java
①
do{
	③;
	④;
}while(②);
```
执行过程：① -> ③ -> ④ -> ② -> ③ -> ④ ... -> ② 

**说明：**
- do-while循环至少会执行一次循环体
- 开发中，使用for和while循环更多些，较少使用do-while

```java
class DoWhileTest{
	public static void main(String[] args){
		//遍历100以内的偶数，并计算所有偶数的和以及偶数的个数
		int num = 1;
		int sum = 0;//记录总和
		int count = 0;//记录个数
		do{
			if(num % 2 == 0){
				System.out.println(num);
				sum += num;
				count++;
			}
			num++;
		}while(num <= 100);
	}
	System.out.println("总和为："+sum);
	System.out.println("个数为："+count);
}
```

### while(true)结构的使用

**题目：从键盘读入个数不确定的整数，并判断读入的正数和负数的个数，输入为0时结束程序**

```java
import java.util.Scanner;

class ForWhileTest{
	public static void main(String[] args){
		
		Scanner scan = new Scanner(System.in);
		
		int positiveNumber = 0;    //记录正数的个数
		int negativeNumber = 0;    //记录负数的个数
		
		while(true){                     //for(;;)
			int number = scan.nextInt();
			
			//判断number的正负情况
			if(number >0 ){
				positiveNumber++;
			}else if(number < 0){
				negativeNumber++;
			}else{
				break;
			}
		}
		System.out.println("输入正数有："+positiveNumber);
		System.out.println("输入负数有："+negativeNumber);

	}
}
```

**说明：**
- 不在循环条件部分限制次数的结构：for(;;)或while(true)
- 循环结束的方式：
	1、循环条件部分返回false 
	2、在循环体中，执行break
- 建议不要在循环体中改变循环变量的值，否则会增加出错的可能性。**（需要修改的情况下，重新定义一个临时变量）**



>**注意**
>
>for循环和while、do-while有区别：while、do-while的循环迭代语句紧跟着循环体，如果循环体不能完全执行（如用continue结束本次循环），循环迭代语句是不会被执行的。但for循环的迭代语句并没有与循环体放在一起，因此不管是否使用continue语句来结束本次循环，迭代语句一样会获得执行。


### 嵌套循环

**嵌套循环的使用**

- 将一个循环结构A声明在另一个循环结构B的循环体中，就构成了循环嵌套
- 外层循环就是循环结构B，内层循环就是循环结构A
- 内层循环结构遍历一遍，只相当于外层循环循环体执行了一次
- 外层循环执行m次，内层循环执行n次，此时内层循环的循环体一共执行了m*n次。


**嵌套循环联系**

> **九九乘法表**
1 × 1 = 1
2 × 1 = 2  2 × 2 = 4
...
9 × 1 = 9 ... 9 × 9 = 81

```java
class NineTable{
	public static void main(String[] args) {

		for(int i = 1;i <= 9;i++){
			for(int j = 1;j <= i;j++){

				System.out.print(i+"×"+j+"="+(i*j));

			}
			System.out.println();
		}
		
	}

}
```

> **100以内所有质数**
质数：素数，只能被1和他本身整除的自然数

```java
class PrimeNumberTest{   //质数
	p	public static void main(String[] args) {
		
		
		
		for(int i = 2;i <= 100;i++){ //遍历100以内的自然数
			boolean isFlag = true;
			for(int j = 2;j < i;j++) { //和i做除法
				
				if(i % j == 0) {
					isFlag = false;
				}
				
			}
			if(isFlag == true) {
				System.out.println(i);	
			}
		}
		
	}
}

```

**优化算法**
```java
public class PrimeNumberTest1 {

	public static void main(String[] args) {
		
		//获取当前时间距离1970-01-01 00:00:00的距离
		long start = System.currentTimeMillis();
		
		int count = 0;
		
		for(int i = 2;i <= 10000;i++){ //遍历100以内的自然数
			boolean isFlag = true;
			for(int j = 2;j <= Math.sqrt(i);j++) { //和i做除法
				
				if(i % j == 0) {
					isFlag = false;
					break;//优化1：只对本身非质数的自然数有效
				}
				
			}
			if(isFlag == true) {
				//System.out.println(i);
				count++;
			}
		}

		//获取当前时间距离1970-01-01 00:00:00的距离
		long end = System.currentTimeMillis();
		System.out.println(count);
		System.out.println(end-start);
	}

}
```

​	



## 如何从键盘获取变量

具体实现步骤:

1. 导包：import java.util.Scanner;
2. Scanner的实例化：Scanner scan = new Scanner(System.in);
3. 调用Scanner类的相关方法，来获取指定类型的变量。

**注意**
需要根据相应的方法，来输入指定类型的值。如果输入的数据类型与要求的类型不匹配时，会报异常，导致程序终止。

``` java
import java.util.Scanner;

class ScannerTest{
	public static void main(String[] args){
		Scanner scan = new Scanner(System.in);
		System.out.println("请输入任意整数：");
		int num = scan.nextInt();
		System.out.println(num);
	}
}
```

- 对于char型的获取，Scanner没有提供相关方法，只能获取一个字符串。

**如何获取一个随机数**

公式：[a,b]  (int)(Math.random() * (b - a + 1) + a);

``` java
//获取10-90的随机数
int value = (int)(Math.random() * 90 + 10);
```

**获取字符串对应位置的字符**

``` java
String gender = scan.next();//输入字符串
char genderChar = gender.charAt(0);//获取所以为0位置上的字符，实际为输入字符串第一个字符
System.out.println(genderChar);//输出获取的字符
```



## 数组类型

### 数组的概述

数组是多个相同类型数据按一定顺序排列的集合，并使用一个名字命名，通过编号的方式对这些数据进行统一管理。

数组是一种**数据结构**，用来储存多个数据，每个数组元素存放一个数据，通常可以通过数组元素的索引`arr[i]`来访问数组元素。所有的数组元素需具有**相同的数据类型**，一个数组只能存储**一种数据类型**的数据。



### 数组的相关概念

- 数组名
- 元素
- 角标、下标、索引
- 数组的长度：元素的个数

### 数组的特点

1. 数组是有序排列的
2. 数组属于引用数据类型的变量。数组的元素既可以是基本数据类型，也可以是引用数据类型
3. 创建数组对象会在内存中开辟一整块连续的空间
4. 数组的长度一旦确定，就不能修改

### 数组的分类

1. 按维数分：一维数组、二维数组.....
2. 按数组元素的类型：基本数据类型元素的数组、引用数据类型的数组



### 一维数组的使用

#### 一维数组的声明和初始化

**数组的初始化**

Java数组必须先初始化，然后才可以使用。初始化就是为数组的数组元素分配内存空间，并为每个数组元素分配初始值。

**静态初始化**

数组的初始化和数组元素的赋值同时进行，初始化时由程序员指定每个数组元素的初始值，由系统决定数组长度。

静态初始化的语法格式如下：

`arrayName = new type[]{element1,element2,element3 ... }`

其中`type`就是数组元素的数据类型，此处的`type`必须与定义数组变量时所使用的`type`相同，也可以定义数组时所指定的`type`的子类

**动态初始化**

数组的初始化和数组元素的赋值同时进行，初始化时程序员只指定数组长度，由系统为每个数组元素指定初始值。

动态初始化的语法格式如下：

`arrayName = new type[length]`

其中需要指定一个int的类型的length参数，这个参数指定了数组的长度，也就是可以容纳数组元素的个数。`type`与静态初始化类似。

```java
public static void main(String[] args) {
		int[] a = {1,2,3,4,5};//数组数据不能用投个变量名直接获取，需要用a[下标]
		//数组定义
		//第一种
//		int[] b = {1,2,3,4,5};
		//第二种
		int b[] = {1,2,3,4,5};//[]写在变量右侧是可以的，但一般不这么写
		//第三种
		int[] c = new int[5];//数组长度是固定的，需要声明
		c[1] = 3;//数组第二个元素存入3
		//第四种
		int[] e = new int[]{1,2,3,4,5};//可以先声明后初始化
		int[] e1;//声明
		e1 = new int[]{1,2,3,4,5};//初始化
		
		System.out.println(e1[3]);//访问数值元素
		e1[3] = 8;//修改数组中元素内容
		
		//访问数组如果超过数组中元素的内容，就会报数组下标越界的错误
//		System.out.println(e1[10]);//数组e1中只有5个元素，下标为0-4，e[10]会导致越界
		//数组e1的长度
		System.out.println("e1数组长度："+e1.length);
		
		//数组最大访问下标
		System.out.println(e1[e1.length-1]);
		
	}
```



> 数组一旦初始化完成，数组的长度就确定了

```java
public static void main (String[] args){
    
    int num;//声明
    num = 10;//初始化
    int id = 1001;//声明+初始化
    
    //一维数组的声明和初始化
    int[] ids;//数组声明
    //静态初始化
    ids = new int[]{1001,1002,1003,1004};//数组初始化
    
    //动态初始化
    String[] names = new String[5];
}
```

#### 如何调用数组指定位置的元素

通过角标（索引）的方式调用：`arr[i]`，数组的角标（索引）从0开始，到数组的长度-1结束

```java
public static void main (String[] args){
	 String[] names = new String[5];
    names[0] = "长城";
    names[1] = "故宫";
    names[2] = "天坛";
    names[3] = "鸟巢";
    names[4] = "圆明园";
//  names[5] = "颐和园";//索引越界异常
    char b = names[4].charAt(0);//提取字符串某一字符
	System.out.println(b);
   
}
```

如果访问数组元素时指定值小于0，或大于等于数组长度，运行时会出现异常报错： java.lang.ArrayIndexOutOfBoundsException：N（数组索引越界异常），其中N就是我们试图访问的数组索引。

#### 如何获取数组的长度和遍历数组 

```java
public static void main(String[] args) {
    	int[] a = {1,2,3,4,5};
    //获取数组长度，属性：length
    	System.out.println(a.length);//5
		
    //如何遍历数组
		for(int i = 0;i < a.length;i++){
            System.out.println(a[i]);
        }
	}
```

#### foreach 循环（增强for循环）

使用foreach 循环（增强for循环）遍历数组和集合元素时，无需获得数组和集合的长度，无需根据索引来访问数组元素和集合元素，foreach 循环（增强for循环）自动遍历数组和集合的每个元素。

```java
for(type variableName : array | collection){//把冒号右边的数组遍历，遍历出来的元素放到定义的变量中
    //自动迭代访问每个数组
}
```

foreach 循环（增强for循环）和普通循环的不同是 ，它无须循环条件，无需迭代语句，这些由系统完成，当每个数组元素都被迭代一次后，foreach 循环（增强for循环）自动结束。

使用foreach 循环（增强for循环）迭代数组元素时，并不能改变数组元素的值，因此不需要对foreach 循环（增强for循环）的循环变量进行赋值。

#### 数组元素的默认初始化值

为数组的数组元素分配内存空间时，一旦分配完空间后，每个内存空间里存储的内容就是该数组元素的值，即使内存空间内存储的内容是空，也是有默认的值（null）

**数组的元素是基本数据类型：**

- 数组类型是整型：0
- 数组类型是浮点型：0.0
- 数组类型是char型：0或'\u0000'，而非'0'
- 数组类型是boolean型：false

**数组的元素是引用类型（String）：**null



> **注意：**
>
> 不要同时使用静态初始化和动态初始化，也就是说，不要在进行数组初始化时，既指定数组长度也为每个数组元素分配初始值，错误代码如下
>
> ` int[] arr = new int[5]{1,2,3,4,5} `<label style="color:red">**错误！！！**</label>



```
public static void main(String[] args) {
	//数组元素是整型
        int[] arr = new int[4];
        for(int i = 0;i < arr.length;i++){
            System.out.print(arr[i]+" ");
        }
        
        System.out.println();
    //数组元素是短整型
        short[] arr1 = new short[4];//byte、long效果相同
        for(int i = 0;i < arr1.length;i++){
            System.out.print(arr1[i]+" ");
        }
        
        System.out.println();
	//数组元素是浮点型
        float[] arr2 = new float[4]; //double效果相同
        for(int i = 0;i < arr2.length;i++){
            System.out.print(arr2[i]+" ");
            
        System.out.println();
            
	//数组元素是char型    
        char[] arr3 = new char[4]; 
        for(int i = 0;i < arr3.length;i++){
            System.out.print(arr3[i]+" ");
        }
            
        System.out.println();
	//数组元素是布尔型
        boolean[] arr4 = new boolean[4]; 
        for(int i = 0;i < arr4.length;i++){
            System.out.print(arr4[i]+" ");
        }
        
        System.out.println();
	//数组元素是引用数据类型
        String[] arr5 = new String[4]; 
        for(int i = 0;i < arr5.length;i++){
            System.out.print(arr5[i]+" ");
        }
}
```



> **输出：**
>
> 0 0 0 0 
> 0 0 0 0 
> 0.0 0.0 0.0 0.0
>
> ​				
>
> false false false false 
>
> null null null null 





#### 一维数组的内存解析

**内存简化结构：** 

![内存简化结构](https://i.loli.net/2021/10/12/BJ4tjI3lvLFigyD.png)

**一维数组的内存解析：**	

##### ![一维数组的内存解析](https://i.loli.net/2021/10/12/BAoTGbafChUJ6Xj.png)



> **程序员进行程序开发时，不仅仅要停留在代码表面，而要深入底层的运行机制，才可以对程序的运行机制有更准确的把握。**



看待一个数组时，要把数组看作两个部分：一部分是数组引用，也就是在代码中定义的数组变量；还有一部分是实际的数组对象，这部分是在堆内存里运行的，通常无法直接访问，只能通过数组引用变量来访问。



### 二维数组的使用





