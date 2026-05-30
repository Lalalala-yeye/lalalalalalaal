+++
title = "OS_lab1_实验报告"
author = "Lalalala-yeye"
date = 2026-03-25T00:00:00+08:00
categories = ["OS实验"]
tags = ["OS","实验报告","Shell","MMU","ELF32/64"]
draft = false
+++

本篇为 OS Lab1 的实验报告，重点记录 Thinking 题作答、关键难点分析与实验体会。

<!--more-->

## 一、实验目标与内容


- 从操作系统角度理解 MIPS 体系结构
- 掌握操作系统启动的基本流程
- 掌握 ELF 文件的结构和功能
- 完成 printk 函数的编写

---

## 二、Thinking


### Thinking 1：
#### 反汇编代码
objdump -d a.out # 反汇编 .text 节区
objdump -D a.out # 反汇编所有节区
objdump -d -M intel a.out # 使用 Intel 汇编语法
objdump -S a.out # 混合显示源代码与汇编（需 -g 编译）
#### 查看文件结构
objdump -f a.out # 文件头信息（架构、入口地址等）
objdump -h a.out # 节区头信息
objdump -x a.out # 所有头信息（等价于 -a -f -h -r -t）
#### 符号与重定位信息
objdump -t a.out # 符号表
objdump -T lib.so # 动态符号表
objdump -r a.out # 重定位信息
objdump -R lib.so # 动态重定位信息
#### 控制输出范围
objdump -d --start-address=0x400500 --stop-address=0x400530 a.out
objdump -j .text -s a.out # 查看指定节区内容

### Thinking 2：

因为是ELF64，自己的ELF32无法解析
```bash
ELF 头：
  Magic：   7f 45 4c 46 02 01 01 00 00 00 00 00 00 00 00 00 
  类别:                              ELF64
  数据:                              2 补码，小端序 (little endian)
  Version:                           1 (current)
  OS/ABI:                            UNIX - System V
  ABI 版本:                          0
  类型:                              DYN (Position-Independent Executable file)
  系统架构:                          Advanced Micro Devices X86-64
  版本:                              0x1
  入口点地址：               0x1180
  程序头起点：          64 (bytes into file)
  Start of section headers:          14488 (bytes into file)
  标志：             0x0
  Size of this header:               64 (bytes)
  Size of program headers:           56 (bytes)
  Number of program headers:         13
  Size of section headers:           64 (bytes)
  Number of section headers:         31
  Section header string table index: 30
```

### Thinking 3：

因为上电后BIOS会先启动，找到操作系统内核所在处，然后载入内存，内核本身不必位于上电复位地址，Bios本身可以充当一段跳转指令。

---
## 三、难点分析

1. readelf.c 里最容易错的是偏移计算。  
要先用 `e_shoff` 找到节头表，再按 `e_shentsize` 一个个往后取，不然地址会读错。

2. 自己写的 readelf 不能解析 readelf 本身。  
因为我们按 ELF32 写的，系统 readelf 一般是 ELF64，结构不一样，所以读不出来。

3. kernel.lds 里 `.text/.data/.bss` 的位置要放对。  
这个不是随便填，位置不对可能编译过了但运行有问题。

4. start.S 要先把栈指针设好，再跳到 `mips_init`。  
栈没设好，后面的 C 函数调用就可能不正常。

5. vprintfmt() 分支比较多。  
要先识别 `%`，再处理不同格式；有一个 case 漏掉就会输出不对。


---

## 四、实验体会

课下实验先仔细看 guidebook，跟着流程做会比较顺。  
但课上实验基础部分写得更多，不只是会写代码，还要能说明代码行为。

这次在查找表名字符串表时，我更清楚了一个点：这是文件内偏移，基址应该选 `binary`，不是表名字符串所在表。

在 printk 实验里，过程被简化了不少。参数表调用和功能函数不用自己全写，重点在标识符识别和正确调用功能函数。  
但课上实验更偏向把关键功能自己补完整，所以不能只复习课下流程，也要巩固那些课下没有手写的部分。

---


