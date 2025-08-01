---
title: Linux基础(2)
categories: 嵌入式开发
tags:
  - 嵌入式开发基础
  - Linux
abbrlink: 30195
date: 2022-10-09 17:46:39
cover: https://media.geeksforgeeks.org/wp-content/uploads/s2-1.jpg
---

# Linux基础(2)

## Linux命令

```shell
命令提示符	命令
命令提示符	命令 选项
命令提示符	命令 参数
命令提示符	命令 选项 参数 参数
```

### 常用命令

| 命令            | 作用         |
| --------------- | ------------ |
| `reboot/init 6` | 重起系统     |
| `init 0`        | 关闭系统     |
| `sleep`         | 睡眠         |
| `cal`           | 查看日历     |
| `uname`         | 查看系统信息 |

#### `cd`-改变目录/切换目录

效果和双击打开文件夹的效果一样，cd命令只作用于目录或者路径

|      命令       |      作用      |
| :-------------: | :------------: |
|      `cd`       | 直接回到家目录 |
| ``cd + 目录名`` | 进入到该目录中 |
|   `cd + 路径`   | 进入到该路径中 |

#### `ls`-查看文件名

**用来显示文件名**

- `ls`：显示当前目录下文件的文件名
- `ls -l`：以长格式显示文件信息

输出示例：
![输出示例](http://img.dpool.love/202312071443583.png)

**显示的文件信息**

执行完`ls -l`命令后在终端显示的文件信息，具体可以分为8个部分，如图：
![文件信息](http://img.dpool.love/202312071444157.png)

**1.文件的类型**

在Linux环境中有7种文件类型：

| 文件信息 |            文件类型            |
| :------: | :----------------------------: |
|   `-`    |            普通文件            |
|   `d`    |            目录文件            |
|   `c`    |          字符设备文件          |
|   `b`    |           块设备文件           |
|   `p`    |            管道文件            |
|   `s`    |         网络套接字文件         |
|   `l`    | 链接文件（符号链接文件）硬链接 |


**2.文件的权限**
分为基本权限和特殊权限
文件权限又分为三个部分：文件所属者的权限、文件所属组的权限和其他用户的权限。

| 符号 | 数字 |    含义    |
| :--: | :--: | :--------: |
| `r`  |  4   |    可读    |
| `w`  |  2   |    可写    |
| `x`  |  1   |   可执行   |
| `-`  |  0   | 该处无权限 |

**3.文件链接数**

在Linux环境中有两种链接方式(**硬链接**和**符号链接**)

创建链接文件有两种方式：

```shell
ln 源文件文件名 目标文件文件名		#创建硬链接文件
ln -s 源文件文件名 目标文件文件名	  #创建符号链接文件
```

**硬链接和符号链接的特点**

-------硬链接---------

- 源文件和目标文件共用同一个inode号
- 当修改其中一个文件,另外的一个文件也会跟着修改
- 移动或者删除其中一个文件,不会对另外的一个文件影响

-------符号链接---------

- 符号链接文件相当于是windows操作系统中的快捷方式
- 源文件和目标文件使用的inode号不同
- 当修改其中一个文件,另外的一个文件也会跟着修改
- 移动或者删除源文件,会对符号链接文件有影响

> **总结：**
>
> <font style="font-size:18px">硬链接</font>
>
> 创建方式 : `ln 源文件 目标文件`
>
> 其实硬链接就是一个文件拥有多个文件名
>
> 源文件和硬链接文件可以在相同目录下,也可以在不同目录下
>
> <font style="background-color:red">注意:</font>
>
> 1. <font style="font-size:12px">修改源文件或者硬链接文件,对于另外一个文件也会发生修改</font>
>
> 2. <font style="font-size:12px">移动或者删除其中一个文件,不会对另外的一个文件影响</font>
>
> 3. <font style="font-size:12px">硬链接文件不占用存储空间</font>
>
> 4. <font style="font-size:12px">不能对于目录文件创建硬链接</font>
>
>    <font style="font-size:12px">即使是超级用户也不能对于目录文件创建硬链接，但是系统默认会对目录创建硬链接
>    </font>
>    <font style="font-size:12px">`.`就是当前目录的硬链接，`..`就是上一级目录的硬链接</font>
>
> 5. <font style="font-size:12px">硬链接文件不能跨系统分区</font>
>
> <font style="font-size:18px">符号链接</font>
>
> 创建方式 : `ln -s 源文件 目标文件`
>
> 符号链接文件类似于windows操作系统中的快捷方式
>
> 符号链接文件内存保存的是源文件的文件名或者路径
>
> <font style="background-color:red">注意:</font>
>
> 1. <font style="font-size:12px">符号链接文件会占用存储空间,保存的是源文件的文件名或者路径</font>
> 2. <font style="font-size:12px">符号链接文件是一个独立的文件,文件inode号和源文件不同</font>
> 3. <font style="font-size:12px">如果移动、重命名、删除源文件，对于符号链接文件会有影响</font>

**4.文件所属者**
**5.文件所属组**
**6.文件的字节数**
可以发现目录文件的字节数都是4096
目录为了可以快速访问其中的子文件和子目录，其中有一个表（索引），这个表（索引）大小为4096

**7.文件最后修改时间**
**8.文件名**



**pwd**				

查看当前所在目录的绝对路径

#### `touch`-创建普通文件

| 命令                      | 作用                                                    |
| :------------------------ | :------------------------------------------------------ |
| `touch 文件名`            | 创建以文件名命名的普通文件                              |
| `touch 已有文件名/目录名` | 会修改文件的最后修改时间                                |
| `touch 文件名 文件名`     | 可以创建多个文件                                        |
| `touch {1..20}.c`         | 可以创建`1.c ... 20.c`                                  |
| `touch 路径/文件名`       | 可以是绝对路径或者相对路径<br/>但是必须是一个正确的路径 |

#### `mkdir`-创建目录文件

| 命令                  | 作用                       |
| :-------------------- | :------------------------- |
| `mkdir 目录名`        | 创建以目录名命名的目录文件 |
| `mkdir 目录名 目录名` | 创建多个目录               |
| `mkdir dir{a..z}`     | 创建了`dira ... dirz`目录  |

#### `rm`-删除文件

| 命令           | 作用                          |
| :------------- | :---------------------------- |
| `rm 文件名`    | 用来删除普通文件              |
| `rm -r 文件名` | 用来删除目录文件              |
| `rm *.c`       | 删除所有以`.c`结尾的文件      |
| `rm -r dir*`   | 删除所有以`dir`开头的目录文件 |

**注意 : 谨慎使用	rm -rf /*     -r是递归的操作**

| 命令             |                                                              |
| ---------------- | ------------------------------------------------------------ |
| `cat 普通文件名` | 可以把普通文件的文件内容打印到终端中                         |
| `less`           | 从文件的起始位置开始显示,可以通过鼠标滚轮或键盘的上下按键进行翻阅,按`q`退出 |
| `more`           | 从文件的起始位置开始显示,可以显示百分比<br/>`s`向下翻阅`b`向上翻阅`q`退出 |
| `head`           | 默认显示文件的前10行内容                                     |
| `tail`           | 默认显示文件的末尾10行内容                                   |

#### `cp`-拷贝文件内容

| 命令                                | 作用                                                         |
| :---------------------------------- | :----------------------------------------------------------- |
| `cp 源文件文件名 目标文件文件名`    | 把源文件拷贝成目标文件<br />如果目标文件不存在,会新创建一个新的普通文件,并且把源文件的文件内容拷贝到目标文件中<br/>如果目标文件存在,会把源文件的文件内容覆盖到目标文件中 |
| `cp -r 源目录目录名 目标目录目录名` | 把源目录拷贝成目标目录<br/>如果目标目录不存在,会新创建一个新的目录文件,并且把源目录中的子文件内容拷贝到目标目录中<br/>如果目标目录存在,会把源目录拷贝到目标目录的内部 |

#### `mv`-移动/剪切/文件改名

`mv 源文件名 目标文件名`			在剪切目录文件时不需要加 -r 的操作

#### `tar`-打压缩包/解压缩包

| 命令                                       | 作用               |
| :----------------------------------------- | :----------------- |
| `tar cvf 最终生成tar包的名字 打压缩的文件` | 打压缩包           |
| `tar xvf tar包的名字`                      | 默认解压到当前目录 |
| `tar xvf tar包的名字 -C 目录`              | 指定解压的目录     |
