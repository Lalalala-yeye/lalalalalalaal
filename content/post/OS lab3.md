+++
title = "OS 课下实验Lab2"
author = "Lalalala-yeye"
date = 2026-04-08T00:00:00+08:00
categories = ["OS实验"]
tags = ["OS","Soft Where","学习分享"]
draft = false
+++

OS lab3知识点和解题过程分享
<!--more-->
# OS lab3知识点和解题过程分享

## 开始写

### exercise 3.1

需要使用的函数已写出，链表在文件开头已给出，按照LIST_INIT和TAILQ_INIT的接口初始化就行。第二步直接循环倒序使用插入函数，在定义出可以看到envs是一个数列。

```
### exercise3.2

根据page_insert的接口填就行

```
### exercise3.3

只需要把ppref设为1然后按照提示赋值

```

### exercise3.4

在这里参数里的Env **new是传入的用来记录新分配内存的ENV控制块，所以开头先用LIST_FIRST取出空闲控制块的首项，然后e用env_setup_vm建立虚拟映射，分配内存，然后初始化参数，注意asid是传入asid的指针的指针，直接修改asid，带入参数，不用等于。最后也一样，new是传入的需要修改得到信息的指针，所以这里是将e移出空闲链表。

```

### exercise3.5

分配一个新页面，page_alloc（struct page **p）直接带入接口。
memcpy函数，把str2中的n个字符复制到str1中。

```

