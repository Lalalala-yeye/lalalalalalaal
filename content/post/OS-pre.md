+++
title = "OS pre"
author = "Lalalala-yeye"
date = 2026-03-08T00:00:00+08:00
categories = ["OS实验"]
tags = ["OS","Soft Where","学习分享"]
draft = false
+++

在这里开始写正文，支持 **Markdown**。
OS pre 知识点和解题过程分享
<!--more-->

# OS-pre

## 实验经验

建议用 vim，vim 有很多其他的功能可以用文件加入啊，nano虽然感觉没vim好用，但是软件工程基础还是要求使用nano的，用了vim命令显示未知指令，不过软工是GUI，实在不熟练也可以直接改文件，但是会卡。lab0 一定要熟练 grep、shell 和 Makefile 文件的创建，真的要熟练才能及时写好，不然实验很麻烦。Git 因为有软工第一次实验，所以其实还好。

## C-exercise

认真阅读假期预习和 guide book，按照给的操作进入git仓库根目录也就是/24373433，再vim blib.c进入编辑就行，i进入插入模式。esc键退出，函数很简单，补充完整就好，然后make编译，再make run运行，出现 pass 就说明测试点过了，没过用 `make dbg` 调试一下就好了。

## make-exercise

<details>
<summary>📎 Makefile示例</summary>

```
CC := gcc
CFLAGS := -O2 -Wall
.PHONY: out clean case_add case_sub case_mul case_div case_all
# 用.PHONY避开文件存在的检查，伪目标，就不会检查同名文件是否存在。
casegen: casegen.c
	$(CC) $(CFLAGS) -o $@ $<
#gcc -O2 -Wall -o casegen casegen.c. $@=当前规则目标名，$<=第一个依赖文件
calc: calc.c
	$(CC) $(CFLAGS) -o $@ $<
# 同上
# 100 个加/减/乘/除样例，各自输出到对应文件
case_add: casegen
	./casegen add 100  > case_add

case_sub: casegen
	./casegen sub 100 > case_sub

case_mul: casegen
	./casegen mul 100  > case_mul

case_div: casegen
	./casegen div 100  > case_div
# 这部分在下面分析casegen.c里详细讲述
# 合并成 400 个样例
case_all: case_add case_sub case_mul case_div
	cat case_add case_sub case_mul case_div > case_all
# make 或 make out：用 calc 计算 case_all，输出 out
out: calc case_all
	./calc < case_all > out
# 默认目标
all: out
clean:
	rm -f case_* out casegen calc
```

</details>
<details>
<summary>📎 calc.c</summary>

```
#include <stdio.h>
#include <string.h>
  
int main() {
        char op[5];
        int a, b;
        while (scanf("%s%d%d", op, &a, &b) == 3) {
                if (!strcmp(op, "add")) {
                          printf("%d\n", a + b);
                } else if (!strcmp(op, "sub")) {
                         printf("%d\n", a - b);
                } else if (!strcmp(op, "mul")) {
                         printf("%d\n", a * b);
                } else if (!strcmp(op, "div")) {
                         printf("%d\n", a / b);
                }
        }
        return 0;
}
```

</details>
<details>
<summary>📎 casegen.c</summary>

```
const char *ops[] = {"add", "sub", "mul", "div"};
  7 
  8 uint32_t xrand() {
  9         /* Algorithm "xor" from p. 4 of Marsaglia, "Xorshift RNGs" */
 10         static uint32_t x = 2024;
 11         x ^= x << 13;
 12         x ^= x >> 17;
 13         x ^= x << 5;
 14         return x;
 15 }
 16 
 17 void help() {
 18         printf("Usage: ./casegen <op> <num>\n"
 19                "Available <op>s: add, sub, mul, div.\n"
 20                "<num>: The number of test cases generated (a positive integer).\n");
 21 }//这里写了命令的用法，usage：后面就是输出样例的指令，<op>表示操作名，<num>表示生成的个数，这就是为什么上面那么写
 22 
 23 int is_op_legal(char *op) {
 24         size_t sz = sizeof(ops) / sizeof(ops[0]);
 25         for (int i = 0; i < sz; i++) {
 26                 if (strcmp(op, ops[i]) == 0) {
 27                         return 1;
 28                 }
 29         }
 30         return 0;
 31 }
 32 
 33 int get_num(char *str_num, int *num) {
 34         *num = strtol(str_num, NULL, 10);
 35         return *num > 0;
 36 }
 37 
 38 int main(int argc, char **argv) {
 39         int num;
 40         if (argc != 3 || !is_op_legal(argv[1]) || !get_num(argv[2], &num)) {
 41                 help();
 42                 return 1;
 43         }
 44         while (num--) {
 45                 printf("%s %u %u\n", argv[1], xrand() % 10000 + 1, xrand() % 10000 + 1);
 46         }
 47         return 0;
 48 }
```

</details>

## mips-exercise

没有很多要说的，中文翻译过来一般都可以。
<details>
<summary>📎 casegen.c</summary>
```
#include <asm/asm.h>
  2 .data
  3 str:
  4 .asciiz "Hello World\n"  # Null-terminated string "Hello World" stored at label 'str'
  5 .align 2 # align to 4-byte boundary (2^2)
  6 var:
  7 .byte 3 # correctly aligned byte: 3
  8 /* '<x>' 是需要替换的内容. */
  9 /* use '.align <x>' to align the following words to 1-byte（希望是1字节一个对齐，所以就是.align 0） boundary (disabling word-alignment) */
 10 /* so that the byte 3 and word 7 is "connected" */
 11 /* Your code here. (1/6) */
 12 .align 0
 13 .word 7, 8, 9
 14 
 15 .text
 16 /* We define '_start_mips' here as the entry of our program. */
 17 EXPORT(_start_mips)
 18 .set at
 19 .set reorder
 20         mtc0    zero, CP0_STATUS
 21         li      sp, 0x84000000
 22         /* Load the address of the string 'str' into the first parameter register. */
 23         la      a0, str
 24         /* use 'addiu  sp, sp, <x>' to push a proper-sized frame onto the stack for Nonleaf function 'print_str'. */
 25         /* Your code here. (2/6) */
 26         addiu sp,sp,-16
 27         jal     print_str
 28         /* use 'addiu  sp, sp, <x>' to restore stack pointer. */
 29         /* Your code here. (3/6) */
 30         addiu sp,sp,16
 31         /* Set the first four parameters. */
 32         li      a0, 0
 33         li      a1, 1
 34         li      a2, 2
 35         li      a3, 3
 36         /* use 'addiu  sp, sp, <x>' to push a proper-sized frame onto the stack for Nonleaf function 'hello'. */
 37         /* Your code here. (4/6) */
 38         addiu sp,sp,-24
 39         lw      t1, var
 40         li      t2, 5
 41         /* use 'sw  t1, <x>(sp)' to store t1 at the proper place of the stack */
 42         /* so that t1 is 5th argument of function hello. */
 43         /* Your code here. (5/6) */
 44         sw t1,16(sp)
 45         /* use 'sw  t2, <x>(sp)' to store t2 at the proper place of the stack */
 46         /* so that t2 is 6th argument of function hello. */
 47         /* Your code here. (6/6) */
 48         sw t2,20(sp)
 49         /* use 'j' to call the function 'hello', we use 'j' instead of 'jal' because 'hello' is 'noreturn' */
 50         j       hello
 ```

 </details>

摘要以上的内容会显示在列表/首页；`<!--more-->` 以下是正文。