---
title: "2026 CISCN&长城杯半决赛 AWDP-PWN-先知社区"
source: "https://xz.aliyun.com/news/91900"
author:
published:
created: 2026-06-10
description: "先知社区是一个安全技术社区，旨在为安全技术研究人员提供一个自由、开放、平等的交流平台。"
tags:
  - "clippings"
---
---
title: "2026 CISCN&长城杯半决赛 AWDP-PWN-先知社区"
source: "https://xz.aliyun.com/news/91900"
author: ""
published: ""
created: "2026-06-10T21:11:48+08:00"
description: "先知社区是一个安全技术社区，旨在为安全技术研究人员提供一个自由、开放、平等的交流平台。"
tags: [clippings]
---

# 2026 CISCN&长城杯半决赛 AWDP-PWN-先知社区

2026长城杯半决赛 AWDP-PWN

附件：

[

https://pan.quark.cn/s/ed26974c74d5

](https://pan.quark.cn/s/ed26974c74d5)

catchme

break

这题很明显有个 uaf 漏洞，free 后 show 可以泄露出 libc， libc 版本是 2.27 ，由于 edit 只能从 8 字节偏移处开始写，没法用常规的 uaf 修改 tcachebin 和 fastbin ，因此我们需要用到 house of storm 的手法，只要堆地址是 0x56 开头就可以成功

exp

from pwn import \*

context(log\_level="debug",os="linux",arch="amd64")

pr = lambda x: success('\\x1b\[01;38;5;214m' + hex(x) + '\\x1b\[0m')

sl = lambda x: p.sendline(x)

s = lambda x: p.send(x)

rt = lambda x: p.recvuntil(x)

ru = lambda x: u64(p.recv(x).ljust(8,b'\\x00'))

ri = lambda x: int(p.recv(x),16)

it = lambda: p.interactive()

file\_name = './catchme'

p = process(file\_name)

elf = ELF(file\_name)

libc = elf.libc

def borrow(idx):

rt(">>\\n")

sl('1')

rt("(3)otter\\n")

sl(str(idx))

def delete(idx):

rt(">>\\n")

sl("2")

rt("index:\\n")

sl(str(idx))

def show(idx):

rt(">>\\n")

sl("3")

rt("index:\\n")

fix

free 后把指针清空就可以过 check

改为

broken\_message

break

逆向出的结构体

初始化我们可以看到这里注册了一个堆上的备用栈

![](https://xzfile.aliyuncs.com/media/upload/picture/20260402133459-b0bb3680-2e55-1.png)

漏洞点

![](https://xzfile.aliyuncs.com/media/upload/picture/20260402133500-b0fd96b3-2e55-1.png)

可以看到也是 uaf 漏洞

![](https://xzfile.aliyuncs.com/media/upload/picture/20260402133500-b13866df-2e55-1.png)

从具体的 free 函数看出程序对空闲堆块的管理是单链表，对指针有类似高版本 glibc 的指针保护，这意味着我们没法泄露出 heap 地址

但是我们看到程序注册了段错误处理的 handle

![](https://xzfile.aliyuncs.com/media/upload/picture/20260402133500-b175f9db-2e55-1.png)

这里可以看到会把报错的地址给我们，也就意味着，我们随便伪造一个非法地址，程序不会退出，而是在备用栈上重新启动一个 main，那么通过这个非法地址的信息，我们就可以计算出 heap 地址：double free 后 show，我们可以得到 heap^key 的值，再 add 写入 0x80808080，这个时候就把一个伪造的地址链入了 bin 中，再 add 两次就会触发段错误，会将错误地址打印出来，这个错误地址就是 0x80808080 ^ key，我们可以算出 key 进而算出 heap 地址

由于我们已经知道了 heap 地址，第二次我们 double free 后show 就可以算出 key，这个时候就相当于有了任意地址读写，而备用栈是在 heap 地址上的，所以我们也知道了栈地址，只要读出栈上的函数地址就可以泄露 libc，然后 ret2system 即可

exp

fix

把 size 的清空改成 heap 的清空

改成

easy\_rw\_revenge

break

和初赛一样，有个前端，这次前端比较简单

![](https://xzfile.aliyuncs.com/media/upload/picture/20260402133501-b1aedb75-2e55-1.png)

只要过了 rc4 加密就可以和后端通信

通过这个脚本可以拿到 cookie，接下来就看后端

后端服务需要爆破一下 md5 值

![](https://xzfile.aliyuncs.com/media/upload/picture/20260402133501-b1e55cb1-2e55-1.png)

这里由于 md5 值是 0x7C994FE632004064，第三位是 \\0，因此我们只需爆破三位即可

得到的结果是 aaabUYkl

漏洞点比较隐晦

![](https://xzfile.aliyuncs.com/media/upload/picture/20260402133502-b224044f-2e55-1.png)

这里看似 free 后会把原先值覆盖，但是由于 size 是 int 类型，导致可以负数绕过大小限制，malloc 失败后程序程序不会中止，原先的 heap 地址也没有覆盖，这样就会导致 uaf 漏洞；由于只有 0x500 大小以上的堆块可以 uaf，因此通过 largebin attack 覆盖 \_IO\_list\_all 打 house of cat 的 orw

exp

fix

把有符号跳转改成无符号跳转即可

改成

minidb

break

漏洞分析

程序正常逻辑是只有堆块引用为 0 才会 free

![](https://xzfile.aliyuncs.com/media/upload/picture/20260402133502-b25c4504-2e55-1.png)

但是有一个分支

![](https://xzfile.aliyuncs.com/media/upload/picture/20260402133502-b292eaee-2e55-1.png)

这里不管堆块引用有多少，都会 free 掉，这里就会存在 uaf 漏洞；

同时

![](https://xzfile.aliyuncs.com/media/upload/picture/20260402133503-b2c99ab5-2e55-1.png)

这里有没有对 heap 内容清空，会导致信息残留

由于该程序没有提供类似 edit 的功能，我们考虑通过预留指针造成堆叠来修改已经 free 的堆块，最后走 house of cat

exp