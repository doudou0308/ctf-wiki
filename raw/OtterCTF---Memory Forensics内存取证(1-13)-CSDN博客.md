---
title: "OtterCTF---Memory Forensics内存取证(1-13)-CSDN博客"
source: "https://blog.csdn.net/m0_65712192/article/details/130547657"
author:
  - "[[成就一亿技术人!]]"
  - "[[hope_wisdom 发出的红包]]"
published:
created: 2026-06-10
description: "文章浏览阅读3.2k次，点赞5次，收藏21次。OtterCTF 内存取证(1-13)_memory forensics"
tags:
  - "clippings"
---
---
title: "OtterCTF---Memory Forensics内存取证(1-13)-CSDN博客"
source: "https://blog.csdn.net/m0_65712192/article/details/130547657"
author: "[[成就一亿技术人!]],[[hope_wisdom 发出的红包]]"
published: ""
created: "2026-06-10T21:24:01+08:00"
description: "文章浏览阅读3.2k次，点赞5次，收藏21次。OtterCTF 内存取证(1-13)_memory forensics"
tags: [clippings]
---

# OtterCTF---Memory Forensics内存取证(1-13)-CSDN博客

## 一.OtterCTF 内存取证

**CTF地址：**

[OtterCTF](https://otterctf.com/ "OtterCTF")

![](https://i-blog.csdnimg.cn/blog_migrate/066b0f45f9dd08d4263ac48f2fba1a61.png)

国产化一下：

![](https://i-blog.csdnimg.cn/blog_migrate/aee0a8319875b7cccfc36c12015e11f5.png)

注册一下 登录就可以 （注：因为邮箱不验证，随意搞个就可以）：

![](https://i-blog.csdnimg.cn/blog_migrate/8aa9401527263b14f67113aaab71300b.png)

### 1 - What the password?

**第一题：**

![](https://i-blog.csdnimg.cn/blog_migrate/b595a91a42e0b6952d771231ac7dcdbc.png)

**国产化：**

![](https://i-blog.csdnimg.cn/blog_migrate/d5a34d2b7f74f378c276851f0f884203.png)

下载OtterCTF.7z的压缩包：

![](https://i-blog.csdnimg.cn/blog_migrate/5a32e206bfea6e860270f0df0f1fd8ac.png)

是OtterCTF.vmem镜像文件

**volatility介绍**  
Volatility是一款非常强大的内存取证工具,它是由来自全世界的数百位知名安全专家合作开发的一套工具, 可以用于windows,linux,mac osx,android等系统内存取证。Volatility是一款开源内存取证框架，能够对导出的内存镜像进行分析，通过获取内核数据结构，使用插件获取内存的详细情况以及系统的运行状态。

```cs
volatility工具的基本使用

命令格式

 

volatility -f [image] --profile=[profile] [plugin]

 

在分析之前，需要先判断当前的镜像信息，分析出是哪个操作系统

 

volatility -f xxx.vmem imageinfo

 

如果操作系统错误，是无法正确读取内存信息的，知道镜像后，就可以在--profile=中带上对应的操作系统

 

常用插件

 

下列命令以windows内存文件举例

 

查看用户名密码信息

 

volatility -f 1.vmem --profile=Win7SP1x64 hashdump

 

查看进程

 

volatility -f 1.vmem --profile=Win7SP1x64 pslist

 

查看服务

 

volatility -f 1.vmem --profile=Win7SP1x64 svcscan

 

查看浏览器历史记录

 

volatility -f 1.vmem --profile=Win7SP1x64 iehistory

 

查看网络连接

 

volatility -f 1.vmem --profile=Win7SP1x64 netscan

 

查看命令行操作

 

volatility -f 1.vmem --profile=Win7SP1x64 cmdscan

 

查看文件

 

volatility -f 1.vmem --profile=Win7SP1x64 filescan

 

查看文件内容

 

volatility -f 1.vmem --profile=Win7SP1x64 dumpfiles -Q 0xxxxxxxx -D ./

 

查看当前展示的notepad内容

 

volatility -f 1.vmem --profile=Win7SP1x64 notepad

 

提取进程

 

volatility -f 1.vmem --profile=Win7SP1x64 memdump -p xxx --dump-dir=./

 

屏幕截图

 

volatility -f 1.vmem --profile=Win7SP1x64 screenshot --dump-dir=./

 

查看注册表配置单元

 

volatility -f 1.vmem --profile=Win7SP1x64 hivelist

 

查看注册表键名

 

volatility -f 1.vmem --profile=Win7SP1x64 hivedump -o 0xfffff8a001032410

 

查看注册表键值

 

volatility -f 1.vmem --profile=Win7SP1x64 printkey -K "xxxxxxx"

 

查看运行程序相关的记录，比如最后一次更新时间，运行过的次数等。

 

volatility -f 1.vmem --profile=Win7SP1x64 userassist

 

最大程序提取信息

 

volatility -f 1.vmem --profile=Win7SP1x64 timeliner
cs运行
```

**windows：**

**1.查看操作系统**

```cs
volatility_2.6.exe -f OtterCTF.vmem imageinfocs运行
```

![](https://i-blog.csdnimg.cn/blog_migrate/371973ff6fd059abdadaa2cecf2ed5d6.png)

**2.查看密码**

**首先hash**

```cs
volatility_2.6.exe -f OtterCTF.vmem --profile=Win7SP1x64 hashdumpcs运行
```

![](https://i-blog.csdnimg.cn/blog_migrate/a394f014d31d286a1a9bec43d978e3c3.png)

518172d012f97d3a8fcc089615283940 这串哈希估计是解不出来，太复杂了。。。

![](https://i-blog.csdnimg.cn/blog_migrate/39dfb7db0152891f4425f3919185c984.png)

换个方法： lsadump模块提取密码：

```cs
volatility_2.6.exe -f OtterCTF.vmem --profile=Win7SP1x64 lsadumpcs运行
```

![](https://i-blog.csdnimg.cn/blog_migrate/ea9ead3c9853718739105f2fc8fab361.png)

**flag： `CTF{MortyIsReallyAnOtter}`**

**kali：同理：**

**系统信息**

```cs
vol.py -f OtterCTF.vmem imageinfo cs运行
```

![](https://i-blog.csdnimg.cn/blog_migrate/fd6dea2f2ee34f0b17958b15bd2e457f.png)

hash哈希

```cs
vol.py -f OtterCTF.vmem --profile Win7SP1x64 hashdump
cs运行
```

![](https://i-blog.csdnimg.cn/blog_migrate/2d98688658b53147672ce36a08257a5c.png)

lasdump密码

```cs
vol.py -f OtterCTF.vmem --profile Win7SP1x64 lsadump cs运行
```

![](https://i-blog.csdnimg.cn/blog_migrate/df6abc7f4666b7d4ec119b178e9fb696.png)

mimikatz这个也可以查看明文，不过我没成功 ，不知道啥原因 可能是python2的问题。。。

![](https://i-blog.csdnimg.cn/blog_migrate/748e9144f6be8e9a8baef547f612c450.png)

提交flag：

![](https://i-blog.csdnimg.cn/blog_migrate/f7cf4103705a4a9af4665d68555d7ef8.png)

### 2.General Info

![](https://i-blog.csdnimg.cn/blog_migrate/a093b2935dfa48895a1375dac136d3f8.png)

国产化：

![](https://i-blog.csdnimg.cn/blog_migrate/361263005789215d2f4df3b189a6746c.png)

**PC的ip和名称：**

**查看网络连接：**

```cs
volatility_2.6.exe -f OtterCTF.vmem --profile=Win7SP1x64 netscancs运行
```

![](https://i-blog.csdnimg.cn/blog_migrate/e390e6938177a59c7a91f7baaaad6a00.png)

**kali：**

```cs
vol.py -f OtterCTF.vmem --profile Win7SP1x64 netscan
cs运行
```

![](https://i-blog.csdnimg.cn/blog_migrate/34d198c5e83b89a8c1d87e0751338275.png)

虽然IP挺多的，但ip应该是192.168.202.131（因为就这一个像）

**CTF{192.168.202.131}**

查看主机名称：

查看注册表：

```cs
vol.py -f OtterCTF.vmem --profile Win7SP1x64 hivelist
cs运行
```

看到system

![](https://i-blog.csdnimg.cn/blog_migrate/306bf35e9bfce3bf86936ad3905fc038.png)

主机名 信息 在system的那一条记录中

**查看注册表键名**

用 `-o + 地址 printkey` 来查看指定的记录

```cs
vol.py -f OtterCTF.vmem --profile Win7SP1x64 -o 0xfffff8a000024010 printkey
cs运行
```

![](https://i-blog.csdnimg.cn/blog_migrate/79d02a20d09baf373285f828183a3622.png)

然后就是步步跟进了:

```cs
vol.py -f OtterCTF.vmem --profile Win7SP1x64 -o 0xfffff8a000024010 printkey -K "ControlSet001"cs运行
```

![](https://i-blog.csdnimg.cn/blog_migrate/5806dacd851164ee29f014408be43eda.png)

```cs
vol.py -f OtterCTF.vmem --profile Win7SP1x64 -o 0xfffff8a000024010 printkey -K "ControlSet001\Control"cs运行
```

![](https://i-blog.csdnimg.cn/blog_migrate/ac416b9539979dd6502c3b8f8102cddf.png)

```cs
vol.py -f OtterCTF.vmem --profile Win7SP1x64 -o 0xfffff8a000024010 printkey -K "ControlSet001\Control\ComputerName"cs运行
```

![](https://i-blog.csdnimg.cn/blog_migrate/c7bdfb7418d3a6796b1d0441e948a2ee.png)

```cs
vol.py -f OtterCTF.vmem --profile Win7SP1x64 -o 0xfffff8a000024010 printkey -K "ControlSet001\Control\ComputerName\ComputerName"cs运行
```

![](https://i-blog.csdnimg.cn/blog_migrate/d58cc8d8d954e95493d4e777c540d9a8.png)

运气较好 第一个直接试出来：

**CTF{WIN-LO6FAF3DTFE}**

![](https://i-blog.csdnimg.cn/blog_migrate/a13b6cc04d4cecee11dd60612beae764.png)

### 3.Play Time

![](https://i-blog.csdnimg.cn/blog_migrate/dce1ff4d15923636e52ba3084fc2a5c7.png)

国产化：

![](https://i-blog.csdnimg.cn/blog_migrate/08cdf75b46460cb09d2c08fc07633bfd.png)

游戏名称和服务器ip：

查看进程：

```cs
vol.py -f OtterCTF.vmem --profile Win7SP1x64 pslist cs运行
```

好多，盲猜吧。。。

![](https://i-blog.csdnimg.cn/blog_migrate/eebca3435e29062dca9b38e5ce5ef0c1.png)

LunarMS.exe，可以搜索一下，发现是个游戏。。。

![](https://i-blog.csdnimg.cn/blog_migrate/d4453aca119e600c948d60aad0493a7d.png)

![](https://i-blog.csdnimg.cn/blog_migrate/3627511046b3a0293ea9104dfde0727e.png)

用netscan查出ip地址

```cs
vol.py -f OtterCTF.vmem --profile Win7SP1x64 netscan
cs运行
```

![](https://i-blog.csdnimg.cn/blog_migrate/5018c6a1c000f3aa89ee2ffc0cc6fded.png) 在这：

![](https://i-blog.csdnimg.cn/blog_migrate/6e610fb6aa9ba72137724a6ab9a7c9e9.png)

**CTF{LunarMS}**

**CTF{77.102.199.102}**

![](https://i-blog.csdnimg.cn/blog_migrate/9719000bd6da1a88cd524ed80992092d.png)

### 4\. Name Game

![](https://i-blog.csdnimg.cn/blog_migrate/8beaedae4c6032a8c93c8d8ac8ecfe77.png)

国产化：

![](https://i-blog.csdnimg.cn/blog_migrate/acb1dacfbcc171a5a2037a0cf67ba680.png)

已知这个账户登录到称为Lunar-3的频道，找出账户名。

把OtterCTF.vmem搞到WinHex做分析：

先搜索Lunar-3

![](https://i-blog.csdnimg.cn/blog_migrate/74479a9033c28c0422f30b73bb9c7638.png)

后面有一段 字符串 ：

![](https://i-blog.csdnimg.cn/blog_migrate/534bd374c528db8f429eb18d95b5e523.png)

使用strings命令加上grep搜索， `-C 5` 表示查找前后5条记录，同样可以找到可疑字符串

```cs
strings OtterCTF.vmem|grep Lunar-3 -C 5  cs运行
```

![](https://i-blog.csdnimg.cn/blog_migrate/0dfb893e576df9f3db2db3d37af1ac0d.png)

**CTF{0tt3r8r33z3}**

![](https://i-blog.csdnimg.cn/blog_migrate/42c55eea7e71ca0d89adb1c60dabd174.png)

### 5.Name Game 2

![](https://i-blog.csdnimg.cn/blog_migrate/316453d356fa9252c14352d43ab34a07.png)

![](https://i-blog.csdnimg.cn/blog_migrate/f9a4c42c5a38578b0c28a7bb64734c1a.png)

看着还是围绕LunarMS这进程来的：

先提取进程：D保存当前目录就可以

```cs
vol.py -f OtterCTF.vmem --profile Win7SP1x64 memdump -p 708 -D ./
cs运行
```

![](https://i-blog.csdnimg.cn/blog_migrate/a86ee7f9da9d3611cee11583e3ff84c6.png)

进程号PID可以看前面的pslist：

![](https://i-blog.csdnimg.cn/blog_migrate/910ed18ab7025965a15acd45aa6c50dd.png)

**先分析题目十六进制数值：**

```cobol
0x64 0x？？{6-8} 0x40 0x06 0x？？{18} 0x5a 0x0c 0x00{2}

分析：0x 十六进制的标志    {6-8} {18} {2}间隔位数

64 ?? {6-8}未知 40 06 ?? {18} 5a 0c 00 {2} 

只能搜索 5a 0c 00 好找一些
```

**Winhex工具：**

**打开708.dmp 先搜索5a 0c 00**

![](https://i-blog.csdnimg.cn/blog_migrate/9a03a994930591356263e0de19ee03ac.png)

慢慢找。。。。。出来了M0rtyL0L

![](https://i-blog.csdnimg.cn/blog_migrate/0d66730155117ae2b82bc4ea1c4507a9.png) 这里也能查出来M0rtyL0L 不过旁边十六进制与题目所给的对不起来。。。

![](https://i-blog.csdnimg.cn/blog_migrate/51427b9fbd691a548dd65f8de700d427.png)

**kali同样也可以**

**hexdump命令：-A -B是指定列数**

```cobol
hexdump -C 708.dmp | grep "5a 0c 00" -A 3 -B 3
```

![](https://i-blog.csdnimg.cn/blog_migrate/a3040730a19eea163cf807a81f1a88b2.png)

找到：

![](https://i-blog.csdnimg.cn/blog_migrate/0a09d99703099abacfb763c17fd0a8bf.png)

还有这：

![](https://i-blog.csdnimg.cn/blog_migrate/ffe94199e2c811d066d323bb61346168.png)

**CTF{M0rtyL0L}**

![](https://i-blog.csdnimg.cn/blog_migrate/a8e88c96f6c3263522e3180104328b1f.png)

### 6.Silly Rick

![](https://i-blog.csdnimg.cn/blog_migrate/6705f2c8f98e06aefefc2123394012c5.png)

![](https://i-blog.csdnimg.cn/blog_migrate/bf109d9e58523ab81151e393310cb36a.png)

**找rick的电子邮件密码 ：**

题目说他总是复制并粘贴密码

那我们就查看粘贴板：

```cs
vol.py -f OtterCTF.vmem --profile Win7SP1x64 clipboard                                                          cs运行
```

![](https://i-blog.csdnimg.cn/blog_migrate/9dc3c024de04c0a8c18fc5395e9778e2.png)

**CTF{M@il\_Pr0vid0rs}**

![](https://i-blog.csdnimg.cn/blog_migrate/87688f1bd91f8908084f3147264b0d33.png)

### 7.Hide And Seek

![](https://i-blog.csdnimg.cn/blog_migrate/95e280f0e58264cbc8ed75c005a1835a.png)

![](https://i-blog.csdnimg.cn/blog_migrate/89c575028178754791b0c033e7890d17.png)

找到恶意软件进程名称（包括扩展名）

**PID和PPID：**

进程PID是当操作系统运行进程时系统自动为其分配的标识符，具有唯一性，且为非零整数。一个PID只会标识一个进程。

PPID代表的是父进程的PID，即父进程相应的进程号。当一个进程被创建时，创建它的那个进程会被称作为父进程，而子进程将以PPID指出它的父进程。

**查看进程：**

```cs
vol.py -f OtterCTF.vmem --profile Win7SP1x64 pslist
cs运行
```

![](https://i-blog.csdnimg.cn/blog_migrate/97aa751e12fd820813ffb288b1002e0f.png)

查看进程发现一个名为Rick And Morty的进程与题目对应

而且 vmware -tray.ex进程，PPID比PID还大 估计是。

![](https://i-blog.csdnimg.cn/blog_migrate/faa4d849f1a093c04815b9868823d4d2.png)

查看cmd历史命令：

```cs
vol.py -f OtterCTF.vmem --profile Win7SP1x64 cmdline  cs运行
```

![](https://i-blog.csdnimg.cn/blog_migrate/2078b0056014c61571b3b00338d6c692.png)

查看该进程的进程命令参数，发现Rick And Morty下载了vmware-tray.exe，默认下载路径在RarSFX目录下，并执行了它：

![](https://i-blog.csdnimg.cn/blog_migrate/618d8762b26bd6fb821115554b80953b.png)

dlllist查看一下进程相关的dll文件列表

```cs
vol.py -f OtterCTF.vmem --profile Win7SP1x64 dlllist -p 3720
cs运行
```

![](https://i-blog.csdnimg.cn/blog_migrate/1414fedeaf7a93d7d44e41f6d08e84c3.png)

**CTF{vmware-tray.exe}**

![](https://i-blog.csdnimg.cn/blog_migrate/2b47ff31310205a7b1e965be77692df8.png)

### 8.Path To Glory

![](https://i-blog.csdnimg.cn/blog_migrate/4a96f6ffb3a9b230480bb7d33ff62187.png)

![](https://i-blog.csdnimg.cn/blog_migrate/535bf0200560ced8f78918f2bc8cb54b.png)

这题意有点不明确。。。

恶意软件是如何进入 rick 的 PC 的？应该是下载的某个文件。

filescan找一下这个文件

```cs
vol.py -f OtterCTF.vmem --profile Win7SP1x64 filescan|grep 'Rick And Morty'
cs运行
```

![](https://i-blog.csdnimg.cn/blog_migrate/63a55015f57d394fde0b23ab15e76983.png)

一共三个exe和三个种子文件，我们要分析来源就要关注种子文件，里面可能放着地址信息：

先保存 在用strings命令 字符串查看文件：

可惜第一个和第二个都没有flag信息：

![](https://i-blog.csdnimg.cn/blog_migrate/222c171649b81887ec55b714b601dde9.png)

0x000000007dae9350 第二个 这个有信息：

```cs
//保存

vol.py -f OtterCTF.vmem --profile Win7SP1x64 dumpfiles -Q 0x000000007dae9350 -D ./

//查看

strings file.None.0xfffffa801b42c9e0.dat 
cs运行
```

![](https://i-blog.csdnimg.cn/blog_migrate/8d741f7f88af1d99f8b9d3d9f14e546e.png)

**CTF{M3an\_T0rren7\_4\_R!ck}**

![](https://i-blog.csdnimg.cn/blog_migrate/cd89e5b1ea511a9ea7c1b6777a3b6237.png)

### 9\. Path To Glory 2

![](https://i-blog.csdnimg.cn/blog_migrate/4bd16d57f1ea7403f3fdea9685e9868d.png)

![](https://i-blog.csdnimg.cn/blog_migrate/32cbbb5756f41e3d1acea53fe6d115fe.png)

让我们继续。。。

没头绪了，看大佬的文章吧。

torrent文件是通过web浏览器下载的 先将所有的chrome进程转储下来：

```cs
vol.py -f OtterCTF.vmem --profile Win7SP1x64 memdump -n chrome.exe  -D ./chrome
cs运行
```

![](https://i-blog.csdnimg.cn/blog_migrate/8d01ea006235bb15f4b39e61e315741c.png)

```cs
strings ./chrome/* | grep 'Rick And Morty season 1 download.exe' -C 10  cs运行
```

![](https://i-blog.csdnimg.cn/blog_migrate/ee9441e445289cbc85329b076ab5fc75.png) 在这：

![](https://i-blog.csdnimg.cn/blog_migrate/a0e2d9d6867cefc14c3602b45d0cf63e.png)

过程中发现了flag.txt 可能后面的关卡会用到

![](https://i-blog.csdnimg.cn/blog_migrate/3b97589de690858e9a5ed704ee4415dd.png)

**CTF{Hum@n\_I5\_Th3\_Weak3s7\_Link\_In\_Th3\_Ch@in}**

![](https://i-blog.csdnimg.cn/blog_migrate/a243ff7b277374417cee460aa733e669.png)

### 10.Bit 4 Bit

![](https://i-blog.csdnimg.cn/blog_migrate/4562a55f4ba631f3f0db4c8fb29dcd1f.png) ![](https://i-blog.csdnimg.cn/blog_migrate/5115c821d21d81fcf366c6f04859b5fa.png)

找攻击者的地址

两个方法：

vmware-tray.exe pid 3720

将恶意软件转储出来：

知识点: 要把内存中某个进程给dump出来，一般有两种方式

> memdump：以dmp格式保存  
> procdump：直接提取

```cs
vol.py -f OtterCTF.vmem --profile Win7SP1x64 procdump -p 3720  -D ./kiss 
cs运行
```

![](https://i-blog.csdnimg.cn/blog_migrate/fb12e1787c035e4d7ec1a76521fe66ac.png)

使用IDA Pro进行分析 打开executable.3720.exe

![](https://i-blog.csdnimg.cn/blog_migrate/e13209867797abe703dc0cae560d6e91.png)

方法二：

通过匹配

```cs
strings -e l OtterCTF.vmem | grep -i -A 5 "ransomware"
cs运行
```

![](https://i-blog.csdnimg.cn/blog_migrate/d6be9291cf35068e3aea8ea2734ef4e4.png)

**CTF{1MmpEmebJkqXG8nQv4cjJSmxZQFVmFo63M}**

![](https://i-blog.csdnimg.cn/blog_migrate/de9725c6bf87b1190576d5ef9e6b58e7.png)

### 11.Graphic's For The Weak

![](https://i-blog.csdnimg.cn/blog_migrate/29d961afe2097c75257dfeeb8ab06827.png)

![](https://i-blog.csdnimg.cn/blog_migrate/167832808e600b8c11aab5e3aa5228b3.png)

分离文件：

```cs
foremost kiss/executable.3720.exe -v
cs运行
```

![](https://i-blog.csdnimg.cn/blog_migrate/344418adc0513a41da2f55af0f68f213.png)

```cs
foremost kiss/executable.3720.exe -o odic

-o 分离到odic目录
cs运行
```

![](https://i-blog.csdnimg.cn/blog_migrate/0b61df67629d72c7b15e2c946a64c9cc.png)

![](https://i-blog.csdnimg.cn/blog_migrate/500e779519c3ffc1ff82b188cecd8174.png) 查看

![](https://i-blog.csdnimg.cn/blog_migrate/445f57e22e90a5fee21ccdf2a3f22807.png)

**CTF{S0\_Just\_M0v3\_Socy}**

![](https://i-blog.csdnimg.cn/blog_migrate/58dd77f4c495c1146041f1d89511fe4c.png)

### 12.Recovery

![](https://i-blog.csdnimg.cn/blog_migrate/6bd1dc2c3e58134308fefab306427b49.png)

![](https://i-blog.csdnimg.cn/blog_migrate/7703fee590b8db446a3e1d625412f4c2.png)

加密文件的随机密码：

IDA查看带有password的函数：

发现有 `computerName+"-"+userName+" "`,也就是 `WIN-LO6FAF3DTFE-Rick`

![](https://i-blog.csdnimg.cn/blog_migrate/e5f8b988a8525ce7848c0251a8a29d48.png)

strings命令查看：

```cs
-a --all：扫描整个文件而不是只扫描目标文件初始化和装载段

-f –print-file-name：在显示字符串前先显示文件名

-n –bytes=[number]：找到并且输出所有NUL终止符序列

- ：设置显示的最少的字符数，默认是4个字符

-t --radix={o,d,x} ：输出字符的位置，基于八进制，十进制或者十六进制

-o ：类似--radix=o

-T --target= ：指定二进制文件格式

-e --encoding={s,S,b,l,B,L} ：选择字符大小和排列顺序:s = 7-bit, S = 8-bit, {b,l} = 16-bit, {B,L} = 32-bit

@ ：读取中选项
cs运行
```

还要带上参数 `-e` ，要以 `16-bit` 寻找，即 `-el` 或 `-eb` ，不然找不到。

```cs
strings -eb OtterCTF.vmem | grep WIN-LO6FAF3DTFE-Rick cs运行
```

![](https://i-blog.csdnimg.cn/blog_migrate/db66d205d914234bf45df647fe5e52a5.png)

**CTF{aDOBofVYUNVnmp7}**

![](https://i-blog.csdnimg.cn/blog_migrate/d1eb3b33d35f2beb87636ed07d730df0.png)

### 13.Closure

![](https://i-blog.csdnimg.cn/blog_migrate/d0f8b68cf67a00cb92e970146cdac04f.png)

![](https://i-blog.csdnimg.cn/blog_migrate/2a95d7ceacb669c0d94d8abd58d2ba38.png)

最后一题了 解密rick的文件：

前面好像找到了一个flag.txt

在查找一下吧：

```cs
vol.py -f OtterCTF.vmem --profile Win7SP1x64 filescan|grep -i 'flag'  cs运行
```

![](https://i-blog.csdnimg.cn/blog_migrate/c42fe500fd8b48d343542db45d03d216.png)

应该是第二个 提取到kiss目录下：

```cs
vol.py -f OtterCTF.vmem --profile Win7SP1x64 dumpfiles -Q 0x000000007e410890 -D ./kisscs运行
```

![](https://i-blog.csdnimg.cn/blog_migrate/691697a987a691c9802374a07070e2d0.png)

cat查看 被加密了

![](https://i-blog.csdnimg.cn/blog_migrate/b4ada69dde18d42b21f338b1f9dbc329.png)

命令行把带有0字节的删除 然后保存到flag2.png.lockad

hexdump查看一下 还是没有发现flag

```cs
tr < file.None.0xfffffa801b0532e0.dat -d '\000' > flag2.png.locked

 

hexdump -C flag2.png.locked   
cs运行
```

![](https://i-blog.csdnimg.cn/blog_migrate/d112786a23985694e4186923daeeb3fd.png)

这时需要工具：

已知这个勒索软件为HiddenTear，直接在网上找到解密程序HiddenTearDecrypter

**winhex操作：**

**shift+delete删除：**

![](https://i-blog.csdnimg.cn/blog_migrate/3785f237846115445273a3f793590c43.png)

删除成功

![](https://i-blog.csdnimg.cn/blog_migrate/90344b750cc832680c23294320901b4f.png)

![](https://i-blog.csdnimg.cn/blog_migrate/9de4327e2f2c5f830144e0bef05c1b12.png)

**改名flag.png.locked**

![](https://i-blog.csdnimg.cn/blog_migrate/ad75adb08fa5b3689347488aff298417.png)

**HiddenTearDecrypter：**

![](https://i-blog.csdnimg.cn/blog_migrate/30ca0cba2216fdf0c9903172d6e50165.png)

密钥 就是12题的flag **aDOBofVYUNVnmp7**

当然，也可以破解：

![](https://i-blog.csdnimg.cn/blog_migrate/6d2465b52c08a4a131068176f76050d7.png)

就是有亿点点慢。。。。。

![](https://i-blog.csdnimg.cn/blog_migrate/5c870d1031308edd6877fbbe9f52990a.png)

还是直接填密钥吧：

![](https://i-blog.csdnimg.cn/blog_migrate/cc1eacae25dd9f0c47f03cad22798070.png)

代表成功：

![](https://i-blog.csdnimg.cn/blog_migrate/890aee94693eeef4b547771ee8b63831.png)

**flag.png.locked变成了falg.png**

![](https://i-blog.csdnimg.cn/blog_migrate/d430365ac91c4b103a1206d9fa0ce4df.png)

打开是损坏的：

![](https://i-blog.csdnimg.cn/blog_migrate/8eb144c6a79da81d075e9158305e8cb6.png)

改成flag.txt 查看：

![](https://i-blog.csdnimg.cn/blog_migrate/f398138216bcafcd96612c0014675710.png) Winhex查看：

![](https://i-blog.csdnimg.cn/blog_migrate/c3bdf8de08b7e3f1709bfec9ba4e959c.png)

**CTF{Im\_Th@\_B3S7\_RicK\_0f\_Th3m\_4ll}**

![](https://i-blog.csdnimg.cn/blog_migrate/3f79cd3b5996211d8b52983ef4cb18f8.png)

## 总结：

到此 13关全部完成，主要用到的就是volatility工具和一些查看16进制的工具，Winhex，还有逆向IDA反编译查询，最后的HiddenTear勒索病毒，大家要了解，现在已经可以破解密钥了，还有就是一些基本的kali的查询和工具命令要掌握。

**推荐博客：**

[内存取证-volatility工具的使用 （史上更全教程，更全命令）\_路baby的博客-CSDN博客](https://blog.csdn.net/m0_68012373/article/details/127419463 "内存取证-volatility工具的使用 （史上更全教程，更全命令）_路baby的博客-CSDN博客")