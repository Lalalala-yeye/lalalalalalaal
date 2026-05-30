+++
title = "OS_lab2_实验报告"
author = "Lalalala-yeye"
date = 2026-04-8T00:00:00+08:00
categories = ["OS实验"]
tags = ["OS","实验报告","Shell","Makefile","grep","sed","awk"]
draft = false
+++

本篇为 OS Lab2 的实验报告，重点记录 Thinking 题作答、关键难点分析与实验体会。

<!--more-->

## 一、实验目标与内容


- 了解MIPS4Kc 的访存流程与内存映射布局
- 掌握与实现物理内存的管理方法（链表法）
- 掌握与实现虚拟内存的管理方法（两级页表）
- 掌握TLB清除与重填的流程

---

## 二、Thinking


### Thinking 2.1：
虚拟地址，虚拟地址。因为cpu访存就是作为虚拟地址先通过TLB取得物理地址，不行再在虚拟内存里分配页面。

### Thinking 2.2：

1.宏实现时field可以取代各种数据结构，所以链表可以不用限定节点的数据结构，同时将操作统一进宏函数里，方便使用而且功能增加可以一起更改不用一个个修改。
2.单向链表要删除需要找到前一个结点修改next指针，要遍历节点，循环单链表也是，但双向链表可以直接删除，所以复杂度相较于O(n)变为O(1)。插入操作都可以在有限操作里完成为O(1)。

### Thinking 2.3：

C，首先prev是指向前节点next指针的指针，所以是**，其次page_list用指向第一节点的指针来表示每张list，所以是 *lh_first意味着这个结构体里包含这个指针。

### Thinking 2.4

1.因为多进程的需要，不同的地址空间是可以重复的，但是所代表的物理地址并不相同，ASID可以判别属于哪个地址空间，避免了进程切换同一虚拟地址反复修改TLB。
2.ASID占了[7:0]位，一共八位，最多2的8次个不同地址空间

### Thinking 2.5
1.tlb_invalidate调用tlb_out函数来无效化有效位。
2.根据va和asid无效化对应的TLB表项，这样下一次访存时就会更新该项
```Makefile
LEAF(tlb_out)
.set noreorder
        mfc0    t0, CP0_ENTRYHI #拔ENTRYHI存入t0
        mtc0    a0, CP0_ENTRYHI #把a0赋予ENTRYHI
        nop
        /* Step 1: Use 'tlbp' to probe TLB entry */
        /* Exercise 2.8: Your code here. (1/2) */
        tlbp #根据ENTRYHI中的值找到对应的ENTRYLo0和ENTRYLo1，index写入索引
        nop
        /* Step 2: Fetch the probe result from CP0.Index */
        mfc0    t1, CP0_INDEX #把index的值赋给t1
.set reorder
        bltz    t1, NO_SUCH_ENTRY #t1<0说明不存在此表项
.set noreorder
        mtc0    zero, CP0_ENTRYHI #对ENTRY三个寄存器赋0
        mtc0    zero, CP0_ENTRYLO0
        mtc0    zero, CP0_ENTRYLO1
        nop
        /* Step 3: Use 'tlbwi' to write CP0.EntryHi/Lo into TLB at CP0.Index  */
        /* Exercise 2.8: Your code here. (2/2) */
        tlbwi #根据得到的index，把修改过的三个ENTRY寄存器的值填回该索引对应的TLB表项
.set reorder
 
NO_SUCH_ENTRY:
        mtc0    t0, CP0_ENTRYHI #把t0的值赋给ENTRYHI 
        j       ra #返回ra
END(tlb_out)
```

### Thinking 2.6
这里用户第一次发起访存，系统先初始化好vm的初始规则和空闲链表页的初始化，然后TLBmiss之后，发起TLB重叠，再查看该页是否有映射，没有则分配内存，并且用page_insert,page_invalidate无效化TLB表项，下次访存会触发TLB重填，成功访问数据。
cpu访存是硬件在检查状态，发现问题，函数帮助硬件完成映射的建立和修改。

### Thinking 2.7
地址变换结构：
X86：历史上“分段+分页”，现代以分页为主。
MIPS：通常无复杂分段，主要靠 TLB/分页与地址段划分。

TLB 管理方式：
X86：TLB miss 后多由硬件按页表格式自动走表。
MIPS：TLB miss 常走异常，由软件（内核）填充，软件可控性更高。

页表耦合度：
X86：硬件规定页表格式较强。
MIPS：硬件约束相对少，OS 可灵活设计页表与策略。

复杂度与灵活性：
X86：硬件复杂、通用性强、对 OS 透明度高。
MIPS：硬件相对简洁，但 OS 处理路径更重、实现责任更大。

内核直映习惯：
MIPS：常见固定内核直映段（如 kseg0）非常典型。
X86：通常通过页表建立高地址内核映射，不靠固定“段式直映窗口”。

### Thinking A.1
1.取s=PTbase前九位,所以是s << 30 +s << 21 +s << 12。
2.取s=PTbase前九位,所以是s << 30 +s << 21 +s << 12 +s << 3。
---
## 三、难点分析

1.宏链表对于其他函数的调用不熟练会难写。
2.理解页表自映射机制和二级页表结构的具体实现，以及代码中何时使用虚拟地址和物理地址做出转换，并且要合理理解指针在函数调用时作为的是他自己还是它指向的部分。


---

## 四、实验体会

1.实验主要涉及三个部分，物理内存的分配和宏链表的宏函数编写，虚拟内存部分的函数编写和tlb相关的mips语言补充和C语言函数的实现，在这些过程中，要熟悉宏定义的格式和C语言实现中宏链表**指针的使用和函数的封装性，虚拟内存部分有涉及pgdir函数的多用途和pp以及pte指针的使用和虚拟地址的转换，初入手有些无从下手，要先熟悉文件原有的函数和接口，熟悉后方便很多。

2.在线考试主要涉及自映射和宏定义部分，宏定义部分比较简单，自映射涉及四个函数，主要难点在自映射的理解部分，代码实现部分主要是要写好基地址到页目录和物理页框的转换。

---


