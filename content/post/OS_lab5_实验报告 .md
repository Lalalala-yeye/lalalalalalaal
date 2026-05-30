+++
title = "OS_lab5_实验报告"
author = "Lalalala-yeye"
date = 2026-05-29T00:00:00+08:00
categories = ["OS实验"]
tags = ["OS","实验报告","Shell","Makefile","grep","sed","awk"]
draft = false
+++

本篇为 OS Lab1 的实验报告，重点记录 Thinking 题作答、关键难点分析与实验体会。

<!--more-->

## 一、实验目标与内容

- 了解文件系统的基本概念和作用。
- 了解普通磁盘的基本结构和读写方式。
- 了解实现设备驱动的方法。
- 掌握并实现文件系统服务的基本操作。
- 了解微内核的基本设计思想和结构。

---

## 二、Thinking


### Thinking 5.1：

- ksg0访问设备，如果对设备的状态写入被缓存了，由于设备状态的改变会随时变化，读操作会读到错误的设备状态，导致后续交互可能一段时间都是和cache在交互。不同设备都不能走ksg0，二者机制相同，但串口因字节级、实时性更强，问题更易立即暴露；IDE 因块传输和命令序列更长，错误常表现为磁盘读写失败或数据静默损坏。

### Thinking 5.2：

- 一个文件有10个直接指针，1个间接指针，磁盘块大小为4kb，直接指针有40kb，32位盘块地址4字节，4kb可以指向1kb-10个磁盘块，最大大小就是（1014+10）*4kb=4MB大小。

### Thinking 5.3：

```c
#define DISKMAP 0x10000000
#define DISKMAX 0x40000000
```
- 0x40000000，4GB.

### Thinking 5.4

```c
#define PTE_DIRTY 0x0004 //页表项是否被修改，决定写回等，用来维护空闲磁盘块链表等环节
#define SECT_SIZE 512  //扇区大小，在外设管理读数据和写数据时使用                  
#define NINDIRECT (BLOCK_SIZE / 4)//表示块大小除4，因为32位表示4字节，在read和write文件中遍历文件数据时使用
#define FS_MAGIC 0x68286097 // 魔数，标识文件系统
```

### Thinking 5.5

```c
#include <lib.h>

void umain(int argc, char **argv)
{
    int fd, r;
    char buf[16];
    int child;

    // 创建测试文件
    if ((fd = open("forktest.txt", O_CREAT | O_RDWR)) < 0)
        user_panic("open");
    if ((r = write(fd, "ABCDEFGH", 8)) != 8)
        user_panic("write init");

    close(fd);

    // 重新打开
    if ((fd = open("forktest.txt", O_RDONLY)) < 0)
        user_panic("open2");

    child = fork();
    if (child < 0)
        user_panic("fork");

    if (child == 0) {
        // 子进程读 3 字节，偏移变为 3
        if ((r = read(fd, buf, 3)) != 3)
            user_panic("child read");
        buf[3] = 0;
        cprintf("child read: %s, offset should be 3\n", buf);
        exit();
    }

    wait(child);

    // 父进程再读：若共享 offset，应读到 "DEFGH" 而不是 "ABCDEFGH"
    if ((r = read(fd, buf, 5)) != 5)
        user_panic("parent read");
    buf[5] = 0;
    cprintf("parent read: %s\n", buf);

    if (buf[0] == 'D')
        cprintf("PASS: parent and child share fd_offset\n");
    else
        cprintf("FAIL: fd_offset not shared\n");

    close(fd);
}
```

### Thinking 5.6

#### File
- f_name ：文件名。
- f_size ：文件大小，单位为字节。
- f_type ：文件类型，分为普通文件 
- FTYPE_REG 和目录FTYPE_DIR 
#### Fd
- fd_dev_id; //外设id，确定去哪找⽂件。
- fd_offset; //读写的偏移量，指出现在读到何处了，在seek()中会被修改
- fd_omode;  //权限，有0-R,1-W,2-RW三种
#### Filefd
- Fd f_fd; //⽂件描述符
- f_fileid; //描述已经打开的⽂件
- File f_file; //⽂件控制块，描述⽂件内容

### Thinking 5.7

- 一开始箭头表示初始化用户进程和文件系统
- 实线实心箭头表示同步消息：发送方调用 ipc_send 后阻塞，接收方在 ipc_recv 中等待并处理。
- 虚线空心箭头表示返回消息：服务器处理完毕后 ipc_send 回传结果/dst_va，用户 ipc_recv 收到后才继续。
- 进程间的通信通过ipc_end和ipc_recv发送请求和返回应答。

---
## 三、难点分析

- 1. **IDE 磁盘驱动与 MMIO 访问**：`ide_read` / `ide_write` 要通过 kseg1 映射地址向设备寄存器发命令，并轮询状态位等待 ready/DRQ。容易混淆“读写映射缓冲区”和“真正触发磁盘传输”的步骤——只改缓存区地址不会写盘，必须向控制口写入才能启动 I/O。扇区（512B）与块（4KB）的换算（`SECT2BLK`、`BY2SECT`）也容易在偏移计算上出错。

- 2. **磁盘块缓存与 `diskaddr`**：块 `n` 映射到 `DISKMAP + n × BY2BLK`，且受 `DISKMAX` 限制。`read_block` / `write_block` 要在“页未映射则分配物理页并插入页表”和“已映射则直接返回”之间切换，并配合 `PTE_DIRTY` 标记脏页。难点在于分清虚址映射窗口、实际占用的物理页、以及磁盘上真实块三者关系，不能误以为缓存区大小等于整盘常驻内存。

- 3. **文件索引结构与块分配**：`File` 中 10 个直接指针 + 1 个一级间接指针，`file_get_block` / `file_map_block` 需按文件内块号逐层查找或分配。间接块前 10 个指针不用、新块要更新 bitmap 和 super 块空闲计数，任一步漏写都会导致读写越界、重复分配或泄漏磁盘块。目录遍历时 `(struct File *)disk[bno].data` 的指针转换也要和 `FILE2BLK` 对齐理解。

- 4. **文件系统服务器与用户库的分工**：`serve()` 死循环 `ipc_recv` 分发 `FSREQ_OPEN/READ/WRITE/CLOSE` 等请求，用户侧 `open` → `fd_alloc` → `fsipc_open` → `fsipc` 链路较长。要同时跟踪请求页（`fsreq`）、返回页（`dst_va`）和 `PTE_SHARE` 映射权限；服务端与用户端对 `Filefd`、`fd_offset` 的修改必须落在共享页上，否则 fork 后偏移不一致。

---

## 四、实验体会

- 本次实验考察了外部访问和文件系统的搭建，主要是实现read和write磁盘的系统调用函数和文件系统的映射和分配，以及请求的ipc函数的调用和请求的实现。
- exam考察了文件系统指令函数的实现，使用dir_lookup，walk_path函数等输出文件夹页目录递归得到文件夹内的文件。
- 实验的过程中对条件函数的条件编写有误，错把不为0定义为等于1，写作业时会用到很多已有的函数，要理解其他函数和额外的宏函数定义，同时一个函数往往有很多用处，需要去理解，精确调用，对于不需要输出的，如walk_path函数只需要前往对应路径，不需输出*dir和lastelem则可以直接使用0替换，之前在tlb相关函数中也出现类似的使用。
- 实验帮助理解了文件系统和磁盘寻道策略以及系统调用读写操作操作系统怎么影响外部设备，文件系统中对文件的读取和块的缓存，体验了从磁盘中寻找文件的具体过程。

## 五、原创说明

- 报告是自己写的，具体思考题有些不会的借用了辅助工具和其他人讨论。

---


