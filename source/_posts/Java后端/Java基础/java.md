---
title: Java运算符
categories: 编程开发学习
tags:
  - Java
cover: 'https://i.loli.net/2020/07/27/d6gvZ4rhtlNHcjU.jpg'
abbrlink: 778179f
date: 2020-10-14 14:31:24
---

# Java定义的数据类型

## 变量按照数据类型分

### 变量

>变量就是指在内存中开辟的存储空间，用于存放运算过程中需要用到的数据，变量是可变的。



#### 变量声明

Java是个强类型语言，所有变量必须**先声明后使用**，声明会对变量类型进行判断，指定的类型变量只能接收类型与之匹配的值 ，声明的语法只需要指定变量的类型和变量名即可。



#### 变量初始化

```java
int x;//如果不给x进行赋值，是不能直接打印的
```

如果要使用变量必须**初始化**，有两种方式：

```java
//在声明同时，直接初始化
 int a = 10;

 //先声明，后初始化
 int a;
 a = 10;
```



### 基本数据类型

> 整型：byte、short、int、long
浮点型：float、double
字符型：char
布尔型：boolean

#### 整数类型

- Java各整数有固定的表数范围和字段长度，不受具体OS的影响，以保证Java程序的可移植性。
- Java的整型常量默认为int型，声明long型产量须后加'l'或'L'
- **Java程序中变量通常声明为int型，除非不足以表示较大的数，才使用long**


|类型|占用存储空间|表数范围|
|-|-|-|
|byte|1字节=8bit位|-128~127|
|short|2字节|-2^15~2^15-1|
|int|4字节|-2^31~2^31-1(约21亿)|
|long|8字节|-2^63~2^63-1|



**注意**

>可以把一个较小的整数值直接赋值给long类型变量，Java并不会将这个变量当作long类型处理，Java依旧会将这个整数值当作int类型来处理，因为int类型的值会自动类型转换到long类型。

```java
public class Text02 {

	public static void main(String[] args) {
		long l1 = 123456;	
		System.out.println(l1);
		System.out.println(getType(l1));
		
		long l2 = 1234578;
		int a = (int) (l2*1236789);
		System.out.println(a);
		System.out.println(getType(a));
		
	}

  private static String getType(Object a) {
        return a.getClass().toString();
    }
}
```



**Java表示进制数**

```Java
//Java表示进制数
int 八进制 = 010;
System.out.println("八进制的“10”="+八进制);
int 十六进制 = 0x10;
System.out.println("十六进制的“10”="+十六进制);
int 二进制 = 0b10;
System.out.println("二进制的“10”="+二进制);
```

>**输出：**
>
>八进制的“10”=8
>十六进制的“10”=16
>二进制的“10”=2



#### 浮点类型

- 与整数类型类似，Java浮点类型也有固定的表数范围和字段长度，不受具体操作系统的影响。
- float：单精度4字节，尾数可以精确到7为有效数字，很多情况下，精度很难满足。
  double：双精度8字节，精度是float的两倍，通常采用此类型。
- **Java的浮点型常量默认为double型，声明float型常量，须后加'f'或'F'**



Java表示浮点数的两种方法：

> 十进制数形式：这种形式就是简单的浮点数，如5.12、512.0、.512。**浮点数必须包含一个小数，否则会被当初int类型处理**。
>
> 科学计数法：如5.12e2（记5.12*10^2^）不区分大小写。



#### 字符类型

- char型数据用来表示通常意义上“字符”(2字节)
- Java中所有字符都使用Unicode编码，故一个字符可以储存一个字母，一个汉字，或其他书面语的一个字符。
- 字符型变量的三种表现形式：
  字符常量是用单引号 '' 括起来的单个字符。例如：char c1 = 'a';char c2 = '中';char c3 = '9'

```java
例如：
char c1 = 'a';
char c2 = '中';
char c3 = '9';
```

Java中还允许使用转义字符”\“来将其后的字符转变为特殊字符型常量。
直接使用Unicode值来表示字符型常量：'\XXXX'。其中，XXXX代表一个十六进制整数。

![](https://i.loli.net/2021/09/14/R1Wrspj8danuJUY.png)



- char类型的值可以直接作为整数值来使用，相当于16位的无符号整数，表数范围为0～65535。
- char类型是可以进行运算的。因为它都对应Unicode码，实际是用该字符对应编码参与运算。

```java
public class Demo09 {

	public static void main(String[] args) {
		char aChar = '你';
		System.out.println(aChar);
		
		//直接将一个char变量当作int类型变量使用
		int aValue = aChar;
		System.out.println(aValue);
		
		//直接将一个0～65535范围内整数赋值给char类型变量
		char a = 99;
		System.out.println(a);
	}
```

>**输出**：
>
>你
>20320
>c



**字符串**

Java没有提供表示字符串的基本数据类型，而是通过String类型表示字符串，字符串由多个字符组成，字符串要用双引号括起来。



**字符串拼接**

```java
public class Demo01 {

	public static void main(String[] args) {
		//字符串拼接——从左向右拼接	
		System.out.println(1+2+"你好");
		System.out.println("你好"+1+2);
		System.out.println("你好"+(1+2));
	}
```

> **输出**
>
> 3你好
> 你好12
> 你好3

####  布尔类型

***只能取两个值之一：true、false***

```java
boolean isMarried = true;
if(isMarried){
	System.out.println("你就不能参加单身party了！很遗憾");
}else{
	System.out.println("你可以多谈恋爱！");
}
```


### 基本数据类型之间的运算规则
* 自动类型提升
	当容量小的数据类型的变量与容量打的数据类型的变量做运算时，结果自动提升为容量大的数据类型。
	说明：此时容量大小指的是，表示数的范围大和小。比如：float容量大于long的容量。
	**byte、char、short --> int --> long --> float --> double**

	***当 byte、char、short三种类型的变量做运算时，结果为int类型***


* 强制类型转换（自动类型提升运算的逆运算）
- 需要使用强转符：()
- 强制类型转换，可能导师精度损失。


```java
class Test{
	public static void main(String[] args){
		//精度损失
		double d1 = 12.9;
		int i1 = (int)d1;//阶段操作，强转为int型
		System.out.println(i1);
			
		//没有精度损失
		long l1 = 123;
		short s2 = (short)l1;
		System.out.println(s2);
		
		//精度损失
		int i2 = 128;
		byte b = (byte)i2;
		System.out.println(b);
	}
}
```

>输出
12
123
-128



### 引用数据类型

**String类型变量的使用**
- String属于引用的数据类型（字符串）
- 声明String类型变量时，使用 ""
- String可以和八种基本数据类型变量做运算，且运算只能是连接运算。
- 运算的结果仍然是String类型

> 
类(class)
接口(interface)
数组(array)

----

# Java的运算符

## 算数运算符

|运算符|运算|范例|结果|
| :-: | :-: | :-: | :-: |
|+|正号|+3|3|
|-|负号|b=4;b|-4|
|+|加|5+5|10|
|-|减|6-4|2|
|*|乘|3*4|12|
|/|除|5/5|1|
|%|取模（取余）|7%5|2|
|++<br>++|自增（前）：先运算后取值<br>自增（后）：先取值后运算|a=2;++a<br>a=2;a++|a=3;b=3<br>a=3;b=2|
|- -<br>- -|自减（前）：先运算后取值<br>自减（后）：先取值后运算|a=2;- -a<br>a=2;a- -|a=1;b=1<br>a=1;b=2|
|+|字符串连接|"He"+"llo"|"Hello"|

- 取余运算的符号与被模数的符号相同。
- 开发中，经常使用%来判断能否被除尽的情况。

- （前）++：先自增1，后运算；（后）++：先运算，后自增1
- （前）- -：先自减1，后运算；（后）- -：先运算，后自减1


## 赋值运算符

**符号：=**
- 当 "=" 两侧数据类型不一致时，可以使用自动类型转化或使用强制类型转换原则进行处理。
- **支持连续赋值。**
- 扩展赋值运算符：+=、-=、*=、/=、%=

``` java
int num = 10;//变量实现+1的操作的方法
//方法一
num = num + 1;
//方法二
num += 1;//不会改变数据类型
//方法三
num++；
```


## 比较运算符

|运算符|运算|范例|结果|
| :-: | :-: | :-: | :-: |
|==|相等于|4==3|false|
|!=|不等于|4!=3|true|
|<|小于|4<3|false|
|>|大于|4>3|false|
|<=|小于等于|4<=3|true|
|>=|大于等于|4>=3|true|
|instanceof|检查是否是类的对象|"Hello"instanceof String|true|

- 比较运算符的结果都是boolean型，要么是true，要么是false。
- **比较运算符"=="不能误写为"="** 

``` java
class CompareTest{
	public static void main(String[] args){
		int i = 10;
		int j = 20;
		System.out.println(i == j);
		System.out.println(i = j);
		
		boolean b1 = true;
		boolean b2 = false;
		System.out.println(b2 == b1);
		System.out.println(b2 = b1);
	}
}
```

> 输出：
	false
	20
	false
	true


## 逻辑运算符

**& --> 逻辑与、| --> 逻辑或、! --> 逻辑非、&& --> 短路与、|| --> 短路或、^ --> 逻辑异或**

|a|b|a&b|a&&b|a &#124; b|a &#124;&#124; b|!a|a^b|
| :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: |
|true|true|true|true|true|true|false|false|
|true|false|false|false|true|true|false|false|
|false|true|false|false|true|true|true|true|
|false|false|false|false|false|false|true|false|

- 逻辑运算符操作的都是bollean类型的变量

**区分 & 与 && **

- 相同点1： & 与 &&的运算结果相同。
- 相同点2：当符号左边是true时，二者都会执行符号右边的运算。
- 不同点：当符号左边是false时，& 继续执行符号右边的运算，而 && 不再执行右边的运算。

``` java
class LogicTest{
	public static void main(String[] args){
		boolean b1 = false;
		int num1 = 10;
		if(b1 & (num1++ > 0)){
			System.out.println("我现在在北京");
		}else{
			System.out.println("我现在在南京");
		}	
		System.out.println("num1 = "+num1);
	}
	
	boolean b2 = false;
		int num2 = 10;
		//短路与 && 判断为false，后面不执行，前后都要是true
		if(b1 && (num2++ > 0)){
			System.out.println("我现在在北京");
		}else{
			System.out.println("我现在在南京");
		}	
		System.out.println("num2 = "+num2);
	}
}

```

> 输出：
	我现在在南京
	num1 = 11
	我现在在南京
	num1 = 10


**区分 | 与 ||**

- 相同点1： | 与 || 的运算结果相同。
- 相同点2：当符号左边是false时，二者都会执行符号右边的运算。
- 不同点：当符号左边是true时，| 继续执行符号右边的运算，而 || 不再执行右边的运算。

```java
class LogicTest{
	public static void main(String[] args){
		boolean b3 = true;
		int num3 = 10;
		if(b3 | (num3++ > 0)){
			System.out.println("我现在在北京");
		}else{
			System.out.println("我现在在南京");
		}	
		System.out.println("num3 = "+num3);
	}
	
	boolean b4 = true;
		int num4 = 10;
		//短路或 || 判断为true，后面不执行，前后都要是false
		if(b4 || (num4++ > 0)){
			System.out.println("我现在在北京");
		}else{
			System.out.println("我现在在南京");
		}	
		System.out.println("num4 = "+num4);
	}
}

```

> 输出：
	我现在在南京
	num1 = 11
	我现在在南京
	num1 = 10

***开发中推荐使用短路与和短路或***


## 位运算符

- 位运算符操作都是整型的数据
- "<<"：在一定范围内，每向左移一位，相当于*2
- ">>"：在一定范围内，每向右移一位，相当于/2


|运算符|运算|范例|
|:-:|:-:|:-:|
|<<|左移|3 << 2 = 12-->3*2*2=12|
|>>|右移|3 >> 1 = 1-->3/2=1|
|>>>|无符号右移|3 >>> 1 = 1-->3/2=1|
|&|与运算|6&3 = 2|
|&#124;|或运算|6 &#124; 3 = 7|
|^|异或运算|6^3 = 5|
|~|取反运算|~6 = -7|

**交换两个变量的值**
``` java
int num1 = 10;
int num2 = 20;

//方法一：定义临时变量的方式
int temp = num1;
num1 = num2;
num2 = temp;
	
//方法二：好处：不用定义临时变量
//弊端：1、相加操作可能超出存储范围。2、有局限性，只适用于数值类型。
num1 = num1 + num2;
num2 = num1 + num2;
num1 = num1 - num2;
System.out.println("num1 = " + num1 +",num2 = "+ num2);

//方法三：使用位运算符
num1 = num1 ^ num2;
num2 = num1 ^ num2;
num1 = num1 ^ num2;
```


## 三元运算符 

- 结构：(条件表达式)?表达式1 : 表达式2
- 条件表达式的结果为boolean类型
- 根据条件表达式的结果为boolean类型
- 根据条件表达式真或假，决定执行表达式1，还是表达式2。如果表达式为true，则执行表达式1，如果表达式为false，则执行表达式2。
- 表达式1和表达式2要求是一致的类型
- 三元运算符是可以嵌套的。
- 凡是可以用三元运算符的地方，都可以改写为if-else
- 如果程序既可以使用三元运算符，又可以使用if-else结构，那么优先选择三元运算符。（简洁、执行效率高）

``` java
class SanYuanTest{
	public static void main(String[] args){
		//获取两个整数较大值
		int m = 12;
		int n = 5;
		
		int max = (m > n)? m : n;
		System.out.println(max);
	}
}
```





## 运算符优先级

- 运算符有不同的优先级，所谓优先级就是表达式运算中的运算顺序。上一行运算符总优先于下一行。
- 只有单目运算符、三元运算符、赋值运算符是从右向左运算的。











