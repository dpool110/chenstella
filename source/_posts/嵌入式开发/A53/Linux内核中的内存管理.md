---
title: Linux内核中的内存管理
abbrlink: '90e43536'
date: 2023-02-20 14:26:57
tags:
  - 嵌入式开发
  - ARM系统结构与编程
  - A53
categories:
  - 嵌入式开发
  - A53
cover: https://images.alphacoders.com/103/thumbbig-103533.webp
---

# Linux内核中的内存管理

## 基本概念

Linux内核管理内存时以**物理页**（4k）为单位内核会为每一个物理内存页建立一个数据结构

```c
struct page{
    //内存被引用的次数，为0表示空闲页
    atomic_t _count;	//引用计数
};
```

Linux内核在管理内存时，并不一视同仁

**高端内存：**物理内存超过896M（大小可调）的空间

动态映射的策略，使用时进行映射，使用完毕之后ima接触映射

**低端内存：**介于0~896M的内存，映射关系是固定的，`虚拟地址 = 0xc0000000 + 物理偏移`

<img src="http://img.dpool.love/202312071503519.png" style="zoom: 50%;" />

## 内核中动态申请内存的方法

### 按页申请

方式一

`alloc_pages(gfd_mask, order)`

order，要申请2^order

`void *page_address(const struct page *page)`

将`page`指定的物理内存进行映射，返回映射后的虚拟地址

方式二

`unsigned long __get_free_pages(gfp_t gfp_mask, unsigned int order)`

要申请2^order个物理内存页

方式三

 `__get_free_page`申请一页的物理内存

`get_zeroed_page(gfp_t gfp_mask)`申请一个内存页，并清零

释放物理内存页：

`void free_pages(unsigned long addr, unsigned int order)`

### 按字节申请

方式一

`kmalloc / kfree`

`kmalloc(size, flags)`

size要申请的字节个数

flags常用的取值：`GFP_KERNEL`、`GFP_ATMIC`

`GFP_KERNEL`申请内存的过程中，可能发生阻塞，所以不能用于中断上下文

`GFP_ATMIC`申请内存的过程中，一旦阻塞立即返回，可以用于中断上下文

kmalloc申请的内存是连续的，执行效率高

方式二

`vmalloc / vfree`

`vmalloc(size)`

size要申请的字节个数

`vmalloc`得到的虚拟地址也是连续的，但是将来对应的物理地址可能不连续

`vfree(ptr);`一定不要在中断上下文中使用

### 映射之后的转换

前提是必须已经建立映射

虚拟地址到物理地址之间的转换`virt_to_phys`

物理地址到虚拟地址之间的转换`static inline void *phys_to_virt(phys_addr_t x)`

## `ioremap`

**在Linux内核态直接访问特殊功能寄存器**

统一编址：将内存和外设（特殊功能寄存器）使用一套编号，ARM、mips中使用的是统一编址

独立编制：使用不同编号

将外设称为IO端口



{% timeline 使用步骤, green %}

<!-- timeline -->

申请I/O内存（告诉linux内核我要使用）

`reques_mem_region(start,n,name)`

`start`要申请的起始物理地址

`n`要连续申请的字节数

`name`名称

<!-- endtimeline -->



<!-- timeline -->

`static inline void __iomem *ioremap(phys_addr_t offset, unsigned long size)`

`offset`要映射的起始物理地址

`size`要连续映射的字节数

返回值是映射后的虚拟地址

<img src="http://img.dpool.love/202312071504458.png" style="zoom: 50%;" />

<!-- endtimeline -->



<!-- timeline -->

通过映射后的虚拟地址访问I/O内存

```c
writeb		*((volatile unsigned char *) ptr) = 0x100;
writew
writel
```

<!-- endtimeline -->





<!-- timeline -->

解除映射

`static inline void iounmap(void __iomem *addr)`

<!-- endtimeline -->



<!-- timeline -->

释放IO内存（告诉Linux内核不用了）

`release_mem_region`

`release_region(start,n)`

<!-- endtimeline -->

{% endtimeline %}



# mmap

实际编程中有两种`mmap`的使用场景

1. 映射普通文件
2. 映射设备文件

映射LED设备，在LED设备中实现`mmap`的功能，该函数将控制LED的特殊功能寄存器对应的物理地址映射到0~3G的虚拟地址空间，达到在用户空间直接操作硬件的效果

> 硬件操作函数集合中`mmp`，可以将硬件的物理地址映射到用户空间，从而在用户空间直接空着硬件

可以使用mmap函数直接将LCD显存映射到用户空间，这样做的好处在于可以减小数据的拷贝的过程

<img src="http://img.dpool.love/202312071504091.png" alt="image-20230220162046556" style="zoom:67%;" />

# g-sensor驱动在Linux中的实现

IIC控制器的驱动在Linux中已经实现了

```c
struct bus_type i2c_bus_type = {
	.name		= "i2c",
	.match		= i2c_device_match,
	.probe		= i2c_device_probe,
	.remove		= i2c_device_remove,
	.shutdown	= i2c_device_shutdown,
	.pm		= &i2c_device_pm_ops,
};
```

设备

```c
struct i2c_client {
	unsigned short flags;		/* div., see below		*/
    //设备地址
	unsigned short addr;		/* chip address - NOTE: 7bit	*/
					/* addresses are stored in the	*/
					/* _LOWER_ 7 bits		*/
    //设备名称
	char name[I2C_NAME_SIZE];
    
	struct i2c_adapter *adapter;	/* the adapter we sit on	*/
	struct i2c_driver *driver;	/* and our access routines	*/
    //基类
	struct device dev;		/* the device structure		*/
	int irq;			/* irq issued by device		*/
	//struct list_head detected;
};
```

驱动

```c
struct i2c_driver {
	//探测控制器上挂载了哪些i2c设备
	int (*probe)(struct i2c_client *, const struct i2c_device_id *); 
    struct device_driver driver;
    //存储了该驱动中支持的i2c芯片的id裂变
	const struct i2c_device_id *id_table;
};
//安装
i2c_del_driver(struct i2c_driver * driver)
```

![image-20230221102914138](http://img.dpool.love/202312071504995.png)



{% timeline 实验步骤, green %}

<!-- timeline 1234-->

<!-- endtimeline -->



<!-- timeline -->

<!-- endtimeline -->

{% endtimeline %}
