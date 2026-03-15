+++
title = "OS 课下实验Lab0"
author = "Lalalala-yeye"
date = 2026-03-08T00:00:00+08:00
categories = ["OS实验"]
tags = ["OS","Soft Where","学习分享"]
draft = false
+++

在这里开始写正文，支持 **Markdown**。
OS lab0知识点和解题过程分享
<!--more-->
#OS lab0知识点和解题过程分享
首先没有经验的话大家还是尽量读完guide book再做题([guide-book(lab0部分).pdf](/files/guide-book(lab0部分).pdf))工具部分可以等lab0做完再看，不过还是建议早点看，毕竟vim，git仓库之类的可以大幅降低CLI编程和文件处理一开始的不适感，而且很方便。在教学内容->实验教学资料里有，也可以在给的github仓库里看一下代码，不过代码库是实时更新的，这里就不贴了。

lab0作为带大家熟悉CLI(命令行)界面的编程主要还是考察一些指令，在课程网站给出的lab0串讲([2026 操作系统 Lab0 串讲.pdf](/files/2026操作系统Lab0串讲.pdf))里也有提示，考察了C语言和gcc编译指令，sed，cp，shell，grep,基本上借助AI和看完指令都可以轻松解决，就不多说了。

然后开始吧，做实验先去自动分支初始化，然后进入虚拟机等一样的步骤，这里不多说。主要过程放在labpre里(虽然写这篇的时候pre还没写(毕竟只是为了督促我自己写实验()))

把题目贴在这里(神秘北航学生未选课录不进系统，神秘的假期预习根本看不了，要是有来预习的(真的会有吗)可以看这里)

<details>
<summary>📎 题目（点击展开）</summary>
Exercise 0.1 Lab0 第一道练习题包括以下四题，如果你四道题全部完成且正确，即可获得50 分。

1、在Lab0 工作区的src 目录中，存在一个名为palindrome.c 的文件，使用刚刚学
过的工具打开palindrome.c，使用 c 语言实现判断输入整数 n(1≤n≤10000) 是否为回
文数的程序(输入输出部分已经完成)。通过stdin每次只输入一个整数n，若这个数字为
回文数则输出Y，否则输出N。[注意：正读倒读相同的整数叫回文数]

2、在src目录下，存在一个空白的Makefile文件，借助刚刚掌握的Makefile知识，
将其补全，以实现通过make命令触发src目录下的palindrome.c文件的编译链接的功
能，生成的可执行文件命名为palindrome。

3、在src/sh_test目录下，有一个file文件和hello_os.sh文件。hello_os.sh是
一个未完成的脚本文档，请同学们借助shell编程的知识，将其补完，以实现通过命令bash hello_os.shAAABBB，在hello_os.sh所处的目录新建一个名为BBB的文件，其内容为AAA文件的第8、32、128、512、1024行的内容提取(AAA文件行数一定超过1024行)。[注意：对于命令bashhello_os.sh AAABBB，AAA及BBB可为任何合法文件的名称，例如bashhello_os.sh filehello_os.c，若已有hello_os.c文件，则将其原有内容覆盖]

4、补全后的palindrome.c、Makefile、hello_os.sh依次复制到路径dstpalindrome.c,dst/Makefile,dst/sh_test/hello_os.sh[注意：文件名和路径必须与题目要求相同]
要求按照要求完成后，最终提交的文件树图示如下
```
1 |--dst
2 | |--Makefile
3 | |--palindrome.c
4 | `--sh_test
5 | `--hello_os.sh
6 `--src
7 |--Makefile
8 |--palindrome.c
9 `--sh_test
10 |--file
11 `--hello_os.sh
第一题最终提交的文件树
```

Exercise0.2 Lab0第二道练习题包括以下一题，如果你完成且正确，即可获得12分。

1、在Lab0工作区ray/sh_test1目录中，含有100个子目录file1~file100，还存在一个名为changefile.sh的文件，将其补完，以实现通过命令bashchangefile.sh，可以删除该目录内file71~file100共计30个子目录，将file41~file70共计30个子目录重命名为newfile41~newfile70。[注意：评测时仅检测changefile.sh的正确性]要求按照要求完成后，最终提交的文件树图示如下(file下标只显示1~12，newfile
下标只显示41~55)
```
1 `--sh_test1
2 |--file1
3 |--file10
4 |--file11
5 |--file12
6 |--file2
7 |--file3
8 |--file4
9 |--file5
10 |--file6
11 |--file7
12 |--file8
13 |--file9
14 |--newfile41
15 |--newfile42
16 |--newfile43
17 |--newfile44
18 |--newfile45
19 |--newfile46
20 |--newfile47
21 |--newfile48
22 |--newfile49
23 |--newfile50
24 |--newfile51
25 |--newfile52
26 |--newfile53
27 |--newfile54
28 |--newfile55
29 `--changefile.sh
第二题最终提交的文件树
```

Exercise 0.3 Lab0第三道练习题包括以下一题，如果你完成且正确，即可获得12分。
1、在Lab0工作区的ray/sh_test2目录下，存在一个未补全的search.sh文件，将其补完，以实现通过命令bash search.shfile intresult，可以在当前目录下生成result文件，内容为file文件含有int字符串所在的行数，即若有多行含有int字符串需要全部输出。[注意：对于命令bashsearch.shfileintresult，file及result可为任何合法文件名称，int可为任何合法字符串，若已有result文件，则将其原有内容覆盖，匹配时大小写不忽略]要求按照要求完成后，result内显示样式如下(一个答案占一行)：

```
1 39
2 123
3 134
4 147
5 344
6 395
7 446
8 471
9 735
10 908
11 1207
12 1422
13 1574
14 1801
15 1822
16 1924
17 1940
18 1984

第三题完成后结果
```

Exercise 0.4 Lab0第四道练习题包括以下两题，如果你均完成且正确，即可获得26分。

1、在Lab0工作区的csc/code目录下，存在fibo.c、main.c，其中fibo.c有点小问题，还有一个未补全的modify.sh文件，将其补完，以实现通过命令bashmodify.shfibo.ccharint，可以将fibo.c中所有的char字符串更改为int字符串。[注意：对于命令bashmodify.sh fibo.ccharint，fibo.c可为任何合法文件名，char及int可以是任何字符串，评测时评测modify.sh的正确性，而不是检查修改后fibo.c的正确性]

2、Lab0工作区的csc/code/fibo.c成功更换字段后(bashmodify.shfibo.ccharint)，现已有csc/Makefile和csc/code/Makefile，补全两个Makefile文件，要求在csc目录下通过命令make可在csc/code目录中生成fibo.o、main.o，在csc目录中生成可执行文件fibo，再输入命令makeclean后只删除两个.o文件。[注意：不能修改fibo.h和main.c文件中的内容，提交的文件中fibo.c必须是修改后正确的fibo.c，可执行文件fibo作用是输入一个整数n(从stdin输入n)，可以输出斐波那契数列前n项，每一项之间用空格分开。比如n=5，输出11235]要求成功使用脚本文件modify.sh修改fibo.c，实现使用make命令可以生成.o文件和可执行文件，再使用命令makeclean可以将.o文件删除，但保留fibo和.c文件。最终提交时文件中fibo和.o文件可有可无。

```
1 |--code
2 | |--Makefile
3 | |--fibo.c
4 | |--fibo.o
5 | |--main.c
6 | |--main.o
7 | `--modify.sh
8 |--fibo
9 |--include
10 | `--fibo.h
11 `--Makefile
第四题make后文件树

1|-- code
2| |-- Makefile
3| |-- fibo.c
4| |-- main.c
5| `-- modify.sh
6|-- fibo
7|-- include
8| `-- fibo.h
9`-- Makefile
第四题make clean 后文件树
```
</details>
##开始写
做过pre实验(并且和贴主一样愚蠢，毫无基础的)的可以在之前的地方应该可以直接cd xxxxxxxx你的学号，直接来到git仓库的根目录，反正就是要到这个git仓库的根目录里，用`git branch`先看现在处于哪个分支，再用`git fetch origin`把新加的分支拿来，然后用`git branch -a`绿色的是现在的git分支，红色的是远程仓库里的保存好的其他分支，话说这个CLI经常有红色提示，但其实红色的消息不一定是危险，只是为了区分，危险一般会把错误和警告给出来，比如`warning：`。不过也不是所有warning都要管，做这个blog的时候无视了一堆warning(),其实和写代码不管warning差不多()。
##exercise 0.1
然后就开始补充 palindrome.c,我是先`cd src`然后`vim palindrome.c`，就可以进去编辑了，vim的具体规则你自己去看吧，这里就说一下进入插入模式点“i”，退出点“esc”，退出插入模式(这里后面的都是英文输入，懒得切换了)后输入:w可以保存，:q可以退出，：wq保存并退出。
###palindrome.c
就是补全C程序很简单，就略过了，用vim可以写好直接粘贴过去，不过可能要处理一下缩进，但是改代码还是IDE里好改。(nano我就不知道了，本人用了一次连编辑都失败，就懒得尝试了，反正还是推荐vim，扩展性和便利性感觉还是比nano好(虽然我的cursor告诫我第一次用推荐用nano()))
仅供参考

```
int temp=n;
int rev=0;
while(n%10!=0){
    rev*=10;
    rev+=n%10;
    n=n/10;
}
if(temp==rev)
```

###Makefile
在guide book里写了应该怎么办，应该看一下都能看懂，或者直接问一下AI也很好懂，这里只要求一个目标和对应命令，我就说一下扩展的话就直接贴着写就行。

```
目标1: 依赖
	命令1
目标2: 依赖
	命令2
目标3:
	命令3
```

仅供参考

```
CC = gcc

palindrome: palindrome.c
	$(CC) -o palindrome palindrome.c
```

未设置默认目标时，make指令不加参数直接运行第一个目标，用all：xxx可以自己指定没有参数时默认执行哪个目标。
然后就可以用make指令实现了。
###exercise0.2

第二个联系很简单啊，就是补全对应目录下的.sh文件，这个.sh就是shell文件，支持各种分支，以及指令参数就可以用$1，$2这种来表示($0 代表的是执行文件的名称，包括路径，就是你在写的这个.sh文件)

| 参数 | 说明 |
|------|------|
| `$#` | 传递到脚本的参数个数 |
| `$*` | 以一个单字符串显示所有向脚本传递的参数。如 `"$*"` 用「"」括起来的情况、以 `"$1 $2 … $n"` 的形式输出所有参数。 |
| `$$` | 脚本运行的当前进程 ID 号 |
| `$!` | 后台运行的最后一个进程的 ID 号 |
| `$@` | 与 `$*` 相同，但是使用时加引号，并在引号中返回每个参数。如 `"$@"` 用「"」括起来的情况、以 `"$1" "$2" … "$n"` 的形式输出所有参数。 |
| `$-` | 显示 Shell 使用的当前选项，与 set 命令功能相同。 |
| `$?` | 显示最后命令的退出状态。0 表示没有错误，其他任何值表明有错误。 |

（本题暂时没用到）
至于本题循环已经帮你写好了，其他部分其实很简单，就是在大于70的里写删除a这个文件夹，大于40里对文件夹名进行修改。因为文件夹非空而且不是文件啊，rmdir不行，rm需要选择-r递归删除来删除这个文件夹啊，并且名称就是file"a",对于处于双引号下的内容，会先进行展开再带入啊，所以这里要表现删除的是file“a”这个数所以可以用"$a"会先将$a变为数字再识别成文件名，当然输入后你看到$a变蓝色了，就说明这里它是被当成参数看待的，不是“$a”看情况不加括号也行。
那就很显然是 rm -r file$a,然后是改名字，用mv就行，mv oldfilename newfilename,所以也很简单，就是mv file$a newfile$a。
###exercise0.3
我们先写好，$1是查询的文件，$2是要查询的字符串，$3是查询到的行放入的文件名
然后去看要做的事，改写search.sh文件使得$1中的所有包含$2的行数输入到$3中
串讲PPT里说的是用grep指令和awk指令来完成
首先是 grep指令，grep -n str file就是从file中找到包含str的行数和该行的内容并输出到标准输出，所以我们就可以得到grep -n "$2" "$1"来获得若干行一串带行号的字符串，这里一定要加引号，不然$2和$1都会被识别成名字，只有带双引号才会先展开，然后才解析指令，这里我们得到的有多余的内容，就可以用awk指令来分割，awk -F指令可以按照F后紧跟着的符号来分割期望的每行输出，然后分割完的内容从前到后一个一个就是$1 $2这样来代指。'{}'中的内容就是在这样做之后要执行的命令比如这里要输出行号而且末尾自动带换行，用print $1就可以实现，print $1, $3那么这两个输出的中间会是空格，每行末尾依旧是换行。
最后是输入到$3这个文件里，所以就是grep先进行后，输出传到awk命令，awk处理完再输入到$3里，那么很简单了，我们用管道定好方向，然后用>重定向到$3就行，具体就是grep -n "$2" "$1" | awk -F: '{print $1}' > "$3"
其次sed也可以写这道题啊，sed -n可以把包含指定字符串的行挑选出来，然后选择输出什么信息，sed -n '/str/=' file 里的=就是在说输出行号,-n其实也是在说关闭自动打印，不过也可以直接理解成标识符对应，而且要注意双引号来展开$2的内容，所以就是 sed -n "/$2/=" "$1" > "$3"
###exercise0.4
第一题就是替换文件中的字段啊，也是很简单的命令 sed 's/old/new/' file,题目里输入的参数是bash modify.sh fibo.c char int，所以$1是文件名，$2是old,$3是new，就是sed "s/$2/$3/" "$1",推荐sed "s/$2/$3/g" "$1"这样一行的所有都会被替换，第一种只会替换第一个
对于这个后缀，g=global，所以是全部替换，如果是数字n就是替换第n个，而ng就是从第n个开始替换到行尾。
还有前缀s，ns就是指只替换第n行，不换别的行，要是一个范围可以写成n，ms就是指替换从n到m行的old，如果是要求的是包含某些字符的行，就可以用/str/s，就只会选择出现str的行来进行替换，如果是两种条件同时满足可以把包含str和一整句写在花括号里，行号写外面，或者用，分隔，如sed '10{/x/s/old/new/}' file（或 sed '10,/x/s/old/new/' 等，视需求而定）。同时加-i标识符可以让输出不是标准输出而是改在文件里并保存，因为开文件看很麻烦，我建议大家可以先输出到标准输出，如果输出是对的，再加上-i就可以，-i。bak就是提前生成备份并保存。

具体的类型表格放在下面。
**替换范围：每行换几个**

| 写法 | 含义 | 示例（行内容 a b a b） |
|------|------|------------------------|
| `s/old/new/` | 每行只换第一个 | OK b a b |
| `s/old/new/g` | 每行全部替换 | OK b OK b |
| `s/old/new/2` | 只换每行第 2 个 | a b OK b |
| `s/old/new/2g` | 从第 2 个开始到行尾都换 | a b OK OK |
g = global，整行都换；数字 = 从第几个开始（或「第几个」视实现而定，常见是「从第 n 个起」）。

**输出方式：改不改原文件**

| 用法 | 效果 |
|------|------|
| `sed 's/old/new/' file` | 只打印到终端/管道，不改 file |
| `sed -i 's/old/new/' file` | 直接改 file（原地修改） |
| `sed -i.bak 's/old/new/' file` | 先备份成 file.bak，再改 file |
不加 `-i`：原文件不变，想看结果就重定向，例如 `sed 's/old/new/' file > newfile`。
加 `-i`：原文件被改掉，没有“打印一份”的效果。

按行号：只改第 n 行
sed '5s/old/new/' file → 只替换第 5 行的第一个 old。
按行号范围：只改第 2～4 行
sed '2,4s/old/new/' file
按模式：只改包含某字符串的行
sed '/int/s/old/new/' file → 仅对含 int 的行做替换。
组合：第 10 行且含 x 才换
sed '10{/x/s/old/new/}' file（或 sed '10,/x/s/old/new/' 等，视需求而定）。

**常见对照小结**

| 需求 | 写法示例 |
|------|----------|
| 每行只换第一个，只输出不改文件 | `sed 's/old/new/' file` |
| 每行全部替换，只输出 | `sed 's/old/new/g' file` |
| 直接改原文件 | `sed -i 's/old/new/g' file` |
| 改原文件并备份 | `sed -i.bak 's/old/new/g' file` |
| 只改第 3 行 | `sed '3s/old/new/' file` |
| 只改含 int 的行 | `sed '/int/s/old/new/' file` |
| 原/新里有 `/` | `sed 's|old|new|' file`（换分隔符） |
| 用变量 | `sed "s/$old/$new/g" file` |

然后是第二问
也是挺简单的，里层Makefile实现两个文件的.o文件的生成，在外层Makefile里链接生成可执行文件，并且有clean来实现清除code文件夹里的fibo.o和main.o。里层Makefile用gcc -c XXX.c -o XXX.o,外层默认是生成可执行文件，所以我们把这个写在第一个，而且他没要求名字，不过建议就叫fibo因为这样会预先检查有没有可执行文件fibo，后面跟需要依赖的文件fibo.o和main.o这里可以直接把这个写成指令，就是fibo.o main.o:这样就会把依赖文件识别为这个操作有没有进行过，所以你也可以随便取名，比如fibo：CSC那么就是CSC：，这只是为了保证我们已经进行过.o文件的创建，而对应fibo.o main.o的依赖就是fibo.c main.c。这个编译的过程我们已经写好了，所以可以直接用make -C指令先cd 到code文件夹再make，就是 fibo.o main.o: fibo.c main.c\n\tmake -C code。然后回到链接部分，gcc -o fibo code/fibo.o code/main.o就是这样按格式写就行，clean也很简单，你可以单纯指名道姓去删除，也可以直接*.o删除啊。记得把clean加进.PHONY里。下面展示一下代码
```
fibo: code/fibo.o code/main.o
        gcc -o fibo code/fibo.o code/main.o
code/fibo.o code/main.o:
        $(MAKE) -C code
.PHONY:clean
clean:
    	rm -f code/fibo.o code/main.o
#外层的                                         
```
```
all: fibo.o main.o
fibo.o: fibo.c
        gcc -I../include -c fibo.c -o fibo.o
main.o: main.c
        gcc -I../include -c main.c -o main.o
```
摘要以上的内容会显示在列表/首页；`<!--more-->` 以下是正文。
