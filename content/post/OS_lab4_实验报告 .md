+++
title = "OS_lab4_实验报告"
author = "Lalalala-yeye"
date = 2026-05-23T00:00:00+08:00
categories = ["OS实验"]
tags = ["OS","实验报告","Shell","Makefile","grep","sed","awk"]
draft = false
+++

本篇为 OS Lab1 的实验报告，重点记录 Thinking 题作答、关键难点分析与实验体会。

<!--more-->

## 一、实验目标与内容

- 掌握系统调用的概念及流程
- 实现进程间通信机制
- 实现 fork 函数
- 掌握页写入异常的处理流程

---

## 二、Thinking


### Thinking 4.1：

- 1.将环境参数保存在Tf中保存，避免长时间使用通用寄存器造成占用，导致破坏需要恢复的现场。
- 2.不一定是，所以从tf里读取更保险
- 3.通过tf恢复现场，再sys_*调用
- 4.$v0 从“系统调用号/入口约定”变成“返回值”；PC 前进到 syscall 之后

### Thinking 4.2：

- 因为下标和id并不一一对应，下表只能找到需要的ENV应该存放的位置，但这个位里不一定是需要的env，因此如果不判断可能会导致找到的env和需要的不是一个。

### Thinking 4.3：

- 因为envid=0表示未初始化，是不合法的，所以不会输出0。在envid2env和系统调用以及IPC实现中都会额外判断envid！=0保证得到的进程块是合法的。

### Thinking 4.4

- C，因为父进程调用了一次fork，然后在两个进程分别产生两个不同的返回值

### Thinking 4.5

- 1.UXSTACK：每进程私有异常栈，会并发写入，不能共享；最小版不做 COW，应为子进程新分配物理页并映射。
- 2.UVPT/VPT/VPD：依赖本进程 env_pgdir 的自映射，不是普通数据；duppage 会导致子进程页表窗口指向错误或共享父 pgdir，引发映射不一致、权限错误和父子互相破坏。应在 fork 中跳过并在子进程上按 env_init 方式重建自映射。

### Thinking 4.6

- vpt/vpd：在 UVPT 自映射窗口上访问本进程 PTE/PDE 的指针；vpt[VPN(va)]、vpd[PDX(va)] 查表。
- 为何能访问：env_init 把 env_pgdir 映射到 UVPT，MMU 仍按本进程页目录翻译，故用户态可读自己的页表结构。
- 自映射体现：页目录一项指向自身物理页，vpt/vpd 是该映射的两种索引方式。
- 能否修改：一般不能；UVPT 只读，改页表须由内核通过系统调用完成。

### Thinking 4.7

- 处理过程中出现新的异常时，为了处理新的异常要使用新的现场，而在UXSTACK上栈式保存现场可以一层层回退完成异常处理。
- 用户 handler 运行在用户态，无法使用内核栈上的 Trapframe；复制到 UXSTACK 才能在用户空间读寄存器现场、处理完后正确恢复并返回原 fault 点，同时支持嵌套 fault，且不泄露内核栈。

### Thinking 4.8

- 这样内核可以更加小，不具体做实现，只保存核心的功能和对参数权限等的修改，避免了内核的臃肿。

### Thinking 4.9

- 写时复制依赖 写只读页 → TLB Mod → 用户 handler 拆页。syscall_set_tlb_mod_entry 负责在 fork 把页标成 COW 之前，让父拥有合法的 Mod 处理入口。若 COW 先完成再设置，则在 handler 就绪前的任何写入都会导致 Mod 异常无法被正确交给用户库处理，COW 机制失效。因此必须先 syscall_set_tlb_mod_entry，再建立写时复制保护。

---
## 三、难点分析

## 三、难点分析

- 1. **系统调用分发与 Trapframe**：从 `msyscall` 陷入到 `sys_*` 之间要理清 `SAVE_ALL`、从 `tf` 读 `$a0`～`a3` 和 `$v0`、再把返回值写回 `tf->v0` 的流程。容易误用已被内核改写的寄存器，或忘记返回用户态时只应改 `$v0` 和 `EPC`。

- 2. **`envid2env` 与 IPC / 系统调用**：`envid` 含槽位与世代，`e->env_id != envid` 和 `envid == 0` 都要判断。IPC、`sys_env_destroy` 等若漏判，会误操作已退出进程槽位上的新进程；`mkenvid` 不返回 0 与 `envid2env(0)` 的约定要一起理解。

- 3. **`fork` 与地址空间复制**：`duppage` 遍历时要跳过 **UXSTACK、UVPT/VPT/VPD**；UXSTACK 须为子进程重新分配，不能共享或 COW；子进程自映射要在 `env_setup_vm` 里按自己的 `env_pgdir` 重建。误对 UVPT 做 `duppage` 会导致页表视图错乱、父子互相干扰。

- 4. **用 `vpt` / `vpd` 遍历页表**：依赖 `lib.h` 里 UVPT 自映射，用 `vpt[VPN(va)]`、`vpd[PDX(va)]` 查 PTE/PDE。VPN/PDX 算错或漏判 `PTE_V` 会导致漏复制页或重复处理；需与 `mmu.h` 中地址布局对照。

- 5. **COW 与 `syscall_set_tlb_mod_entry` 顺序**：必须先登记 TLB Mod 处理入口，再把共享页标为只读/COW；顺序反了会在 handler 未就绪时写共享页，Mod 异常无法正确进入用户库拆页。

- 6. **`do_tlb_mod` 与用户态页写入异常**：要把 `Trapframe` 拷到 **UXSTACK** 再进用户 handler；需理解“异常重入”时为何在 UXSTACK 上栈式保存多帧现场。内核只做机制，具体拆页策略在用户库，和 `page_insert`、去 COW 位、TLB 无效化要衔接好。

- 7. **IPC 实现**（若实验包含）：`ipc_send` / `ipc_recv` 与 `envid2env`、权限检查、共享页映射配合；发送方与接收方 envid 过期、阻塞等待等边界情况易出 bug，需结合 `envid` 世代机制排查。


---

## 四、实验体会

- 1.实验很简单，时间系统调用，熟悉page_lookup就可以做完

- 2.extra部分写了一个半小时没写完，主要是对于函数接口不是很熟悉，同时在tlb的重写时思考asid不知道在哪里，以及最后重写do_tlb时没时间写，导致没分，前面的函数实现可能可以放一放，先把分拿了。

---


