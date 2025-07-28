---
title: DMA scatterlist
tags:
  - DMA
abbrlink: 81af76f3
date: 2023-08-03 14:00:32
cover: http://img.dpool.love/202312071510974.png
---

# DMA scatterlist

DMA直接以物理地址形式访问memory，软件进行一系列操作之后，只要把存储空间交给DMA控制器，最终由DMA控制器将数据搬移给某个外设是，由于DMA控制器只能访问物理地址，只能以不连续的物理内存块为单位递交。

所以需要用一个数据结构来描述一个个不连续的物理内存，这个数据结构就是`scatterlist`。`scatterlist`本身并不是一种链表的结构，只是用来描述一个单独的内存块。多个`scatterlist`组合在一起形成一个表，可以是`scatterlist`类型的数组，也可以是内核抽象出来的`sg_table`

`scatterlist`就是DMA物理地址映射虚拟地址的数据结构，多个`scatterlist`

<img src="http://img.dpool.love/2019102510253829.gif">



## API描述

`struct scatterlist`用于描述在一个物理地址上连续的内存块（以page为单位）

```c
/*
	page_link，指示该内存块所在的页面。bit0和bit1有特殊用途，因此要求page最低4字节对齐。
    offset，指示该内存块在页面中的偏移（起始位置）。
    length，该内存块的长度。
    dma_address，该内存块实际的起始地址
*/
struct scatterlist {
	unsigned long	page_link;
	unsigned int	offset;
	unsigned int	length;
	dma_addr_t	dma_address;
};
```

`struct sg_table`

在实际的应用场景中，单个的`scatterlist`是没有多少意义的，我们需要多个`scatterlist`组成一个数组，以表示在物理上不连续的虚拟地址空间。通常情况下，使用`scatterlist`功能的模块，会自行维护这个数组（指针和长度）。另外kernel抽象出来了一个简单的数据结构：`struct sg_table`，帮忙保存`scatterlist`的数组指针和长度：

```
struct sg_table {
	struct scatterlist *sgl;	/* the list */
	unsigned int nents;		/* number of mapped entries */
	unsigned int orig_nents;	/* original size of list */
};
```

### 创建`scatterlist`链表

创建一个scatterlist链有两种方式，一种是静态声明，并调用API进行初始化

```c
struct scatterlist sgt[8];
sg_init_table(sgt, 8);
```

sg_init_table()函数定义如下

 ```c
/**
 * sg_init_table - Initialize SG table
 * @sgl:	   The SG table
 * @nents:	   Number of entries in table
 *
 * Notes:
 *   If this is part of a chained sg table, sg_mark_end() should be
 *   used only on the last table part.
 *
 **/
void sg_init_table(struct scatterlist *sgl, unsigned int nents)
{
	memset(sgl, 0, sizeof(*sgl) * nents);
#ifdef CONFIG_DEBUG_SG
	{
		unsigned int i;
		for (i = 0; i < nents; i++)
        sgl[i].sg_magic = SG_MAGIC;
	}
#endif
	sg_mark_end(&sgl[nents - 1]);
}
EXPORT_SYMBOL(sg_init_table);
 ```

传入的参数sgl是scatterlist的数组地址，nents是数组长度，函数内部将数组内存初始化，然后调用了`sg_mark_end`将数组的最后一个元素的`page_link`标记为尾节点

<img src="http://img.dpool.love/88591fc234e44c73a5e5ac68b877d90d.png">

另一种方法是动态创建

```c
struct sg_table sgt;
sg_alloc_table(&sgt, 8, GFP_KERNEL);
```

 sg_alloc_table()函数定义如下

```c
/**
 * sg_alloc_table - Allocate and initialize an sg table
 * @table:	The sg table header to use
 * @nents:	Number of entries in sg list
 * @gfp_mask:	GFP allocation mask
 *
 *  Description:
 *    Allocate and initialize an sg table. If @nents@ is larger than
 *    SG_MAX_SINGLE_ALLOC a chained sg table will be setup.
 *
 **/
int sg_alloc_table(struct sg_table *table, unsigned int nents, gfp_t gfp_mask)
{
	int ret;
 
	ret = __sg_alloc_table(table, nents, SG_MAX_SINGLE_ALLOC,
			       NULL, gfp_mask, sg_kmalloc);
	if (unlikely(ret))
		__sg_free_table(table, SG_MAX_SINGLE_ALLOC, false, sg_kfree);
 
	return ret;
}
EXPORT_SYMBOL(sg_alloc_table);
```

其内部通过`__sg_alloc_table()`来动添申请内存创建。这里的`SG_MAX_SINGLE_ALLOC`是一个由于各种原因的限制，即动态申请的连续内存的`sg_table`必须在一个页面中，默认一个页面4096的话，`scatterlist`结构体大小20字节，那么一个页面最大能够创建大小为204的`scatterlist`链，而如果要创建更大数目的话，需要通过铰链的方式将多个分散在不同页面的`scatterlist`链串联起来，这在`__sg_alloc_table()`内部已经实现，用户不需要关心

<img src="http://img.dpool.love/c5ca03bd795e4cb793c1ea2f7e8f6b3d.png">

# 使用

通常的应用场景是将应用程序分配的buffer映射到sg table上

```c
void map_user_buf_to_sgl(struct sg_table *sgt, void __user *buf, int len)
{
    int page_nr = (((unsigned long)buf + len + PAGE_SIZE - 1) -
				 ((unsigned long)buf & PAGE_MASK))
				>> PAGE_SHIFT; 
    
    /* alloc sg table */
    sg_alloc_table(sgt, pages_nr, GFP_KERNEL);
 
    /* alloc page ptr */
    struct page **pages = kcalloc(pages_nr, sizeof(struct page *), GFP_KERNEL);
    
    /* buf to pages */
    get_user_pages_fast((unsigned long)buf, pages_nr, 1/* write */,
				pages);
    
    struct scatterlist *sg = sgt->sgl;
    for (i = 0; i < pages_nr; i++, sg = sg_next(sg)) {
        unsigned int offset = offset_in_page(buf);
        sg_set_page(sg, pages[i], len, offset);
    }
}
```

首先根据buf和len得到页面数量，然后根据页面数量创建对应长度的`sg_table`，然后获取buf对应的页面，最后遍历`sg_table`，通过`sg_set_page()`将页面设置到scatterlist上

其他的一些API

- `sg_alloc_table_from_pages`：从一组页面创建`sg_table`
- `sg_nents`：获取sg的entity数量
- `sg_copy_from_buffer`：从一个连续的buffer拷贝到`sg_list`
- `sg_chain`：将两个单独的`sg_list`串联























