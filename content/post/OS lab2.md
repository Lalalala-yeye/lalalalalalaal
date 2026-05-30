+++
title = "OS 课下实验Lab2"
author = "Lalalala-yeye"
date = 2026-04-08T00:00:00+08:00
categories = ["OS实验"]
tags = ["OS","Soft Where","学习分享"]
draft = false
+++

OS lab2知识点和解题过程分享
<!--more-->
# OS lab2知识点和解题过程分享

## 开始写

### exercise 2.1

题目：请参考代码注释，补全 mips_detect_memory 函数。
在实验中，指定了硬件可用内存大小 memsize，请你用内存大小 memsize完成总物理
页数npage 的初始化。
```c
/* These variables are set by mips_detect_memory(ram_low_size); */
static u_long memsize; /* Maximum physical address */
u_long npage;          /* Amount of memory(in pages) */

Pde *cur_pgdir;
 
struct Page *pages;
static u_long freemem;

struct Page_list page_free_list; /* Free list of physical pages */
 
/* Overview:
*   Use '_memsize' from bootloader to initialize 'memsize' and
*   calculate the corresponding 'npage' value.
*/
void mips_detect_memory(u_int _memsize) {
        /* Step 1: Initialize memsize. */
        memsize = _memsize;
 
        /* Step 2: Calculate the corresponding 'npage' value. */
        /* Exercise 2.1: Your code here. */

        printk("Memory size: %lu KiB, number of pages: %lu\n", memsize / 1024, npage);
}
```
这里的memsize指的是硬件可用内存大小，而要补全的npage指的是总物理页数，所以我们只需要用可用内存大小除一页的大小了，guide文档里并未给出PGSHIFT或者PAGE_SIZE的大小啊，这些全局定义一般都放在了include文件夹下的mmu.h文件里啊，直接查看有没有定义过就行，就是查的时候会漏看啊，这种时候你记住这些量一般的表示，再用指令在mmu.h里找关键词就行，也可以用lab0学的awk，sed，grep之类的。针对具体文件就直接在后面加路径。
```bash
#eg
rg "关键词1|关键词2|关键词3" -n include/mmu.h

# A. 先宽搜关键词族
rg "关键词1|关键词2|关键词3" -n

# B. 专找宏定义
rg "#define\s+(关键词1|关键词2)\b" -n

# C. 看使用位置
rg "\b(关键词1|关键词2)\b" -n include kern lib

```
### exercise2.2

补充完整LIST_INSERT_AFTER,只需要按照正常的链表插入操作再将所有->next改成LIST_NEXT就行，就是要记得prev指向前驱的next指针是指针的指针，*prev=前一个的next， *next=**prev也就是现节点的地址。用&LIST_NEXT获得指针地址。

### exercise0.3


```

### exercise0.4


```

