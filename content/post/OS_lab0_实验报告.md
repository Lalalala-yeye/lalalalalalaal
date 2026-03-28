+++
title = "OS_lab0_实验报告"
author = "Lalalala-yeye"
date = 2026-03-18T00:00:00+08:00
categories = ["OS实验"]
tags = ["OS","实验报告","Shell","Makefile","grep","sed","awk"]
draft = false
+++

本篇为 OS Lab0 的实验报告，重点记录 Thinking 题作答、关键难点分析与实验体会。

<!--more-->

## 一、实验目标与内容


- 基础 Linux 命令（文件/目录/权限/查找）
- Shell 脚本参数与批处理
- `grep` / `sed` / `awk` 文本处理
- `Makefile` 的编译组织方式
- 将代码与脚本按要求复制到指定目录并完成提交

对应题目主要包括：

1. `palindrome.c` 回文数判断
2. `src/Makefile` 编译 `palindrome`
3. `hello_os.sh` 提取指定行到新文件
4. `changefile.sh` 批量删除与重命名目录
5. `search.sh` 输出匹配字符串所在行号
6. `modify.sh` 字符串替换 + 两层 Makefile 组织构建

---

## 二、Thinking


### Thinking 1：

不一样，因为三个.txt文件和他们的名字一样，是未跟踪，已跟踪，已跟踪但修改为暂存，所以两次的结果不一样

### Thinking 2：

```bash
git add FILE #add the file 把文件添加到暂存区
git add FILE #stage the file 把修改添加到暂存区
git commit -m"message" #commit 把文件提交到本地仓库
```

### Thinking 3：

```bash
git restore print.c
git checkout -- print.c #从暂存区恢复print.c
git restore --staged print.c
git restore print.c #撤回暂存区的删除，然后从暂存区恢复print.c
git checkout HEAD -- print.c #从上一次提交里恢复print.c
git restore --staged hello.txt #撤回了hello.txt的上传暂存区
```

### Thinking 4：

```bash
git reset --hard head^ #回到修改前提交的上一次提交 
git reset --hard <hash> #可以回到指定哈希数的那次提交
```
不会显示跳转到的提交之后的提交，所以会一次显示三次提交，两次提交，一次提交，再变成三次提交

### Thinking 5：

先是在终端输出first，然后把second写入文件，然后是third替换second，然后forth追加到third后面，
```bash
first #终端 1
second #output.txt 2
third # 3
third
fourth # 4
```

### Thinking 6：

```bash
# command
echo 'echo Shell Start...' > test
echo 'echo set a = 1' >> test
echo 'a=1' >> test
echo 'echo set b = 2' >> test
echo 'b=2' >> test
echo 'echo set c = a+b' >> test
echo 'c=$[$a+$b]' >> test
echo 'echo c = $c' >> test
echo 'echo save c to ./file1' >> test
echo 'echo $c>file1' >> test
echo 'echo save b to ./file2' >> test
echo 'echo $b>file2' >> test
echo 'echo save a to ./file3' >> test
echo 'echo $a>file3' >> test
echo 'echo save file1 file2 file3 to file4' >> test
echo 'cat file1>file4' >> test
echo 'cat file2>>file4' >> test
echo 'cat file3>>file4' >> test
echo 'echo save file4 to ./result' >> test
echo 'cat file4>result' >> test
```
```bash
# result
3
2
1
```
a=1,b=2,c=a+b=3,然后abc分别放入3，2，1文件，三个文件的内容再按1，2，3的顺序加入4，再把4加到result文件里，所以就是3，2，1
echo echo Shell Start 与 echo `echo Shell Start` ，前者输出 echo Shell Start；后者先做命令替换，输出 Shell Start
echo echo $c>file1 与 echo `echo $c>file1` ，前者把echo $c重定向，后者把$c重定向

### Thinking A.1：

不会

---
## 三、难点分析


1. `grep/awk` 组合时的“格式对齐”
   - `grep -n "$2" "$1"` 会输出形如 `行号:行内容` 的结果
   - 需要再用 `awk -F:` 把分隔符从 `:` 处拆开，确保最终只输出行号
2. Shell 参数引用与引号
   - 题目允许 `file/result/str` 是任意合法名称（可能包含特殊字符/空格）
   - 因此涉及 `$1/$2/$3` 时基本都需要使用 `"$1"`、`"$2"`、`"$3"`，避免被 shell 提前展开或拆分
3. 管道与重定向的输出覆盖
   - `result` 若已存在要求覆盖，所以使用 `> "$3"`（而不是 `>>`）
   - 先用管道把 `grep` 的输出送给 `awk`，再由重定向落盘
4. `sed` 字段替换的正确写法
   - `modify.sh fibo.c char int` 的核心是把文件内所有 `char` 字段替换为 `int` 字段
   - 需要使用全局替换：`sed "s/$2/$3/g" "$1"`（或等价形式），并确保变量在命令中正确展开
5. 两层 Makefile 的依赖关系与构建入口
   - 内层 `csc/code/Makefile` 负责把 `fibo.c/main.c` 编译成 `fibo.o/main.o`
   - 外层 `csc/Makefile` 负责链接生成可执行文件，并在 `clean` 中只删除 `.o`
   - 外层调用内层常用 `make -C code`，避免手动 `cd` 且让相对路径更稳定


---

## 四、实验体会

1. 之前对命令行脚本“能跑就行”的理解，这次被迫进一步细化到“输出格式、引号、重定向语义”和“依赖/入口”上：比如 `grep -n` 与 `awk -F:` 的配合、`>` 覆盖结果文件，都很容易在一两个字符上出错。
2. `grep/sed/awk` 的价值很直观：相比写循环/解析字符串，用现成工具把“匹配、抽取、替换”拆成独立步骤，再用管道串起来，思路更清晰，也更接近真实工程中的流水线处理。
3. Makefile 让“编译流程”可复用：两层结构（内层生成 `.o`、外层链接与清理）把职责分开后，`make`/`make clean` 的行为就更稳定，也更符合评测对文件组织的要求。
4. 最终体会：做这种实验，关键不是一次把所有东西写对，而是先保证每一步命令在终端验证通过（先不进脚本/Makefile），再把它们组合到脚本和 Makefile 的目标流程里。

---


