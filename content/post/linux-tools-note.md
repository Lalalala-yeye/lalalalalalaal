+++
title = "Linux 基础指令与 grep/awk/sed & Shell/Makefile 总结"
author = "Lalalala-yeye"
date = 2026-03-18T00:00:00+08:00
categories = ["学习分享"]
tags = ["Linux","Shell","Makefile","grep","awk","sed"]
draft = false
+++

Linux 常用基础指令 + `grep/awk/sed` + Shell 脚本 + Makefile 知识点汇总，适合 **OS Lab0 / 预习 / 复习** 直接食用。

<!--more-->

## 一、Linux 基本常用指令

### 1. 文件和目录操作

- **查看当前目录**

```bash
pwd              # 显示当前路径
ls               # 列出当前目录文件
ls -l            # 以长格式显示（权限/用户/大小/时间）
ls -a            # 显示隐藏文件（以 . 开头）
ls -lh           # 以人类可读方式显示文件大小
```

- **切换目录**

```bash
cd /path/to/dir  # 进入指定目录
cd ~             # 回到当前用户家目录
cd ..            # 返回上一级目录
cd -             # 在最近两个目录之间来回切换
```

- **创建/删除目录**

```bash
mkdir test       # 创建目录
mkdir -p a/b/c   # 递归创建多级目录
rmdir emptydir   # 删除空目录
rm -r dir        # 递归删除目录（危险）
rm -rf dir       # 强制递归删除（非常危险，慎用）
```

- **文件操作**

```bash
touch file.txt           # 创建空文件或更新修改时间
cp src dest              # 复制文件
cp -r dir1 dir2          # 递归复制目录
mv old new               # 重命名或移动
rm file.txt              # 删除文件
rm -f file.txt           # 强制删除不提示
```

- **查看文件内容**

```bash
cat file                 # 一次性输出全部
tac file                 # 反向输出
more file                # 分页查看（空格下一页，q退出）
less file                # 更强大的分页查看（支持上下翻页、搜索）
head file                # 默认显示前10行
head -n 20 file          # 显示前20行
tail file                # 显示后10行
tail -n 50 file          # 显示后50行
tail -f logfile          # 实时查看文件末尾（看日志）
```

### 2. 权限与用户

```bash
ls -l              # 查看权限如：-rwxr-xr--
chmod 755 file     # 改权限为 rwxr-xr-x
chmod u+x file     # 给当前用户增加执行权限
chown user:group file  # 修改文件拥有者
```

权限位说明（以 `-rwxr-xr--` 为例）：

- 第1位：文件类型（`-` 普通文件，`d` 目录，`l` 链接）
- 后9位每3一组：`r`读、`w`写、`x`执行
  - 第1组：所有者
  - 第2组：所属组
  - 第3组：其他人

### 3. 进程与系统相关

```bash
ps              # 查看当前 shell 相关进程
ps aux          # 查看所有进程
top             # 动态查看系统进程

kill PID        # 向进程发送 SIGTERM
kill -9 PID     # 强制杀死进程（SIGKILL）

whoami          # 当前用户名
who             # 当前登录用户
uname -a        # 内核和系统信息
df -h           # 查看磁盘使用情况
du -sh dir      # 查看目录大小
free -h         # 查看内存使用
```

### 4. 查找与定位

```bash
which cmd           # 查看命令路径
whereis cmd         # 查看命令相关文件位置
find . -name '*.c'  # 在当前目录递归查找 .c 文件
locate filename     # 从数据库快速查找（需定期 updatedb）
```

---

## 二、grep 指令（文本搜索）

### 1. 基本用法

```bash
grep "pattern" file          # 在文件中查找包含 pattern 的行
grep -n "main" *.c          # 显示行号，并在所有 .c 文件中查找 main
grep -i "error" logfile     # 忽略大小写
grep -v "DEBUG" logfile     # 反选：不包含 DEBUG 的行
```

常用选项：

- `-n`: 显示行号
- `-i`: 忽略大小写
- `-v`: 取反匹配
- `-r`: 递归子目录
- `-E`: 支持扩展正则（相当于 `egrep`）
- `-o`: 只输出匹配部分
- `-c`: 只统计匹配行数

### 2. 正则匹配示例

```bash
# 匹配以 error 开头的行
grep "^error" logfile

# 匹配以 .c 结尾的行
grep "\.c$" filelist

# 匹配数字行
grep "[0-9]" file

# 匹配空行
grep "^$" file

# 匹配多种关键字（用 -E 或 egrep）
grep -E "error|warning|fatal" logfile
```

### 3. 管道配合使用

```bash
dmesg | grep -i usb             # 从内核日志中过滤 usb
ps aux | grep nginx             # 查看 nginx 相关进程
ls -l | grep "^d"               # 仅显示目录（以 d 开头）
```

---

## 三、awk 指令（文本处理 / 小脚本语言）

### 1. 基本结构

```bash
awk 'pattern { action }' file
```

字段说明：默认按空白分隔

- `$1`：第1列
- `$2`：第2列
- `$0`：整行
- `NR`：当前行号
- `NF`：当前行字段数

### 2. 常见用法

```bash
# 打印整行
awk '{ print }' file

# 打印第1列和第3列
awk '{ print $1, $3 }' file

# 只打印匹配包含 "error" 的行第2列
awk '/error/ { print $2 }' logfile

# 打印行号和内容
awk '{ print NR, $0 }' file

# 统计指定列的和，比如第2列是数字
awk '{ sum += $2 } END { print sum }' data.txt
```

### 3. 指定分隔符与条件

```bash
# 以冒号为分隔符（如 /etc/passwd）
awk -F: '{ print $1, $3 }' /etc/passwd

# 以逗号为分隔符
awk -F',' '{ print $2 }' data.csv

# 打印第2列大于80的行
awk '$2 > 80 { print $0 }' score.txt

# 打印第2列在60~80之间的行
awk '$2 >= 60 && $2 <= 80 { print $1, $2 }' score.txt

# 统计行数
awk 'END { print NR }' file
```

---

## 四、sed 指令（流编辑器，常用于替换）

### 1. 基本替换

```bash
sed 's/old/new/' file         # 每行只替换第1个 old
sed 's/old/new/g' file        # 每行替换所有 old
```

### 2. 就地修改（in-place）

```bash
sed -i 's/old/new/g' file     # 直接修改文件
sed -i.bak 's/old/new/g' file # 修改前备份到 file.bak
```

### 3. 常见操作

```bash
# 删除空行
sed '/^$/d' file

# 删除包含某个关键词的行
sed '/DEBUG/d' logfile

# 只打印 10~20 行
sed -n '10,20p' file

# 将每行开头的 # 去掉（取消注释）
sed 's/^#//' file

# 在匹配行前/后插入内容
sed '/pattern/i \插入在前一行' file
sed '/pattern/a \插入在后一行' file
```

### 4. 补充：替换范围小抄

- `s/old/new/`：每行只换第一个
- `s/old/new/g`：每行全部替换
- `s/old/new/2`：只换每行第 2 个
- `s/old/new/2g`：从第 2 个开始到行尾都换

按行号：

```bash
sed '5s/old/new/' file      # 只改第 5 行
sed '2,4s/old/new/' file    # 只改第 2～4 行
sed '/int/s/old/new/' file  # 只改含 int 的行
```

---

## 五、Shell 脚本编写基础（bash）

### 1. 基本结构

```bash
#!/usr/bin/env bash

echo "Hello, Shell"
```

运行：

```bash
chmod +x script.sh
./script.sh
```

### 2. 变量与命令替换

```bash
name="Alice"
echo "Hello, $name"

now=$(date)
echo "Now is $now"
```

> 注意：赋值号两边不能有空格。

### 3. 位置参数与特殊变量

```bash
echo "脚本名: $0"
echo "第1个参数: $1"
echo "第2个参数: $2"
echo "参数个数: $#"
echo "所有参数: $@"
```

常见：

- `$#`：参数个数
- `$@`：所有参数列表
- `$$`：当前脚本进程 ID
- `$?`：上一条命令的退出状态（0 表示成功）

### 4. 条件判断

```bash
if [ "$1" -gt 10 ]; then
  echo "大于10"
elif [ "$1" -eq 10 ]; then
  echo "等于10"
else
  echo "小于10"
fi
```

整数比较：`-eq -ne -gt -ge -lt -le`  
文件测试：`-f` 普通文件，`-d` 目录，`-e` 存在与否。

### 5. 循环与函数

```bash
# fo                                                                                                                                                                              r
for i in 1 2 3 4 5; do
  echo "$i"
done

for f in *.c; do
  echo "处理 $f"
done

# while
count=1
while [ "$count" -le 5 ]; do
  echo "$count"
  count=$((count + 1))
done

# 函数
myfunc() {
  echo "参数1: $1"
  return 0
}

myfunc "hello"
echo "函数返回值: $?"
```

---

## 六、Makefile 编写基础

### 1. 规则格式

```make
target: dependencies
	<TAB>command
```

- `target`：目标（可执行文件 / .o / 伪目标）
- `dependencies`：依赖文件
- `command`：生成目标的命令（必须 TAB 开头）

### 2. 简单示例

```make
app: main.o foo.o
	gcc -o app main.o foo.o

main.o: main.c foo.h
	gcc -c main.c

foo.o: foo.c foo.h
	gcc -c foo.c

clean:
	rm -f app *.o
```

使用：

- `make`：默认执行第一个目标（这里是 `app`）
- `make clean`：执行清理

### 3. 变量与自动变量

```make
CC = gcc
CFLAGS = -Wall -O2

app: main.o foo.o
	$(CC) -o $@ $^ $(CFLAGS)

%.o: %.c
	$(CC) -c $< $(CFLAGS)
```

- `$@`：当前规则的目标名
- `$<`：第一个依赖
- `$^`：所有依赖

### 4. 伪目标

```make
.PHONY: clean run

clean:
	rm -f app *.o

run: app
	./app
```

---

## 七、综合小例子

### 1. 统计当前目录所有 `.c` 文件中包含 TODO 的总行数

```bash
#!/usr/bin/env bash

count=$(grep -r "TODO" --include="*.c" . | wc -l)
echo "共有 $count 行包含 TODO"
```

### 2. 每个文件 TODO 数统计（grep + awk）

```bash
grep -r "TODO" --include="*.c" . \
| awk -F: '{ file=$1; count[file]++ } END { for (f in count) print f, count[f] }'
```

这篇基本可以当作 **OS Lab0 工具部分 + Linux 基础指令** 的小抄，如果你想我再帮你和 `OS lab0` 那篇文章之间互相加一点链接/跳转，也可以继续说。
