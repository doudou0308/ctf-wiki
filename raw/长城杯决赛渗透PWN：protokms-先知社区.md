---
title: "长城杯决赛渗透PWN：protokms-先知社区"
source: "https://xz.aliyun.com/news/92219"
author:
published:
created: 2026-06-10
description: "先知社区是一个安全技术社区，旨在为安全技术研究人员提供一个自由、开放、平等的交流平台。"
tags:
  - "clippings"
---
---
title: "长城杯决赛渗透PWN：protokms-先知社区"
source: "https://xz.aliyun.com/news/92219"
author: ""
published: ""
created: "2026-06-10T21:10:24+08:00"
description: "先知社区是一个安全技术社区，旨在为安全技术研究人员提供一个自由、开放、平等的交流平台。"
tags: [clippings]
---

# 长城杯决赛渗透PWN：protokms-先知社区

protokms

总结

这题不是普通菜单堆题，而是一个用

protobuf-c

封装出来的 KMS 服务。真正的漏洞在

CREATE

的失败路径里：

1

先按

key\_size

申请堆块；

2

立刻把指针写进全局槽位；

3

再校验

secret\_key

的真实长度；

4

如果

len(secret\_key) > key\_size

，就

free

掉刚申请的堆块；

5

但不会把全局槽位清空。

于是我们可以稳定拿到一个 dangling pointer。后面的

READ

和

UPDATE

又都直接围绕这个全局槽位工作，于是整题就变成了一套完整的 UAF 利用链：

协议还原

\-> CREATE 失败路径留下 stale slot

\-> 利用堆切割做 alias / overlap

\-> READ 泄露 libc + heap

\-> free fake 0x430 chunk 进 unsorted

\-> largebin attack 改 \_IO\_list\_all

\-> fake wide FILE + setcontext+0x3d

\-> openat2 / read / write 拿 flag

本地实际验证使用题目自带运行环境：

./ld-linux-x86-64.so.2 --library-path../protokms

对应

libc.so.6

版本是：

GNU C Library (Ubuntu GLIBC 2.35-0ubuntu3.13) stable release version 2.35.

所以后文所有 largebin、

\_IO\_list\_all

、wide FILE、

setcontext

的分析都以 glibc 2.35 为准。

证据来源

1

kms.proto

直接给出了请求/响应协议和字段含义。

2

protokms

虽然 stripped，但配合字符串、反汇编和全局数组访问，足够把

CREATE / READ / UPDATE / DELETE

的核心逻辑还原出来。

协议层还原

kms.proto

很直接：

syntax = "proto3";

enum ActionType {

CREATE = 0;

READ = 1;

UPDATE = 2;

DELETE = 3;

}

message Credential {

uint32 id = 1;

string service\_name = 2;

uint32 key\_size = 3;

bytes secret\_key = 4;

}

实际收发格式不是菜单字符串，而是：

2-byte little-endian length + protobuf body

对应脚本里的 helper 也不是“3 参数菜单函数”，而是在封装一个完整的

Credential

：

def add(id, name, key\_size, content):

req.cred.id = id

req.cred.service\_name = name

req.cred.key\_size = key\_size

req.cred.secret\_key = content

所以

add

有 4 个参数是协议决定的，不是多写了一个无关参数。只是从堆利用视角看，真正决定堆行为的往往是：

●

id

●

key\_size

●

content

而

service\_name

通常固定成

'aaa'

，主要只是陪跑字段。

题目源码逻辑还原

结合

kms.proto

、字符串引用和

protokms

反汇编，可以把服务端内部消费的对象近似还原成：

struct proto {

char pad\[0x18\];

uint32\_t id;

char \*key;

uint32\_t key\_size;

uint64\_t value\_len;

void \*value;

};

程序全局上维护的是三组数组：

char key\[16\]\[32\];

void \*value\[16\];

uint32\_t value\_len\[16\];

从反汇编里也能直接看出来：

●

0x8080

一带按

id \* 0x20

访问，明显是

key\[16\]\[32\]

●

0x82c0

一带按

id \* 8

访问，保存真实堆指针

●

0x8280

一带按

id \* 4

访问，保存逻辑长度

这里我们有一个自动化的脚本去识别

主循环和 action 分发

main

先读 2 字节长度，再读 protobuf 正文，调用

protobuf\_c\_message\_unpack

解包，然后根据

action

分发：

●

CREATE

走

0x2690

●

READ

走

0x2860

●

UPDATE

走

0x2980

●

DELETE

走

0x2ad0

如果包长超过

0x3ff

，会打印：

然后直接走退出路径。这一点正好被最后的 FILE 利用拿来当触发器。

这里就是决定了我们不能输入大量的数据

CREATE：题目的真正漏洞点

把

0x2690

那段逻辑翻成伪代码，核心就是：

漏洞点非常干净：

●

先

calloc

●

先写

value\[id\] = p

●

最后才判断

len(secret\_key) <= key\_size

●

失败后

free(p)

，但不会把

value\[id\]

清零

所以只要发送：

程序就会真的申请一个

0x110

对应的 chunk，写进槽位，然后立刻 free 掉它，留下一个稳定的 stale pointer。

READ：把 dangling pointer 指向的内容原样打包回去

0x2860

这段逻辑等价于：

这意味着：

●

READ

完全不关心这个指针是不是已经 free

●

只要

value\[id\]!= NULL

●

它就会把那块内存按

value\_len\[id\]

长度原样塞进 protobuf 响应

所以这里的

READ

本质就是一个“带长度的 UAF read primitive”。

UPDATE：围绕 dangling pointer 的受限写原语

0x2980

的逻辑大致是：

也就是说：

●

UPDATE

可以继续对 dangling pointer 指向的区域

memcpy

●

只要本次请求的

key\_size

不超过当前槽位记录的逻辑长度

●

同时本次输入的真实长度不超过本次请求的

key\_size

这给了我们一个非常稳定的“UAF write + 逻辑长度可调”原语。

DELETE：正常释放 + 清空槽位

0x2ad0

比较正常：

因此这题不是“delete 忘记清空”的普通 UAF，而是更阴一点：

●

CREATE

失败路径制造 dangling pointer

●

READ

负责 leak

●

UPDATE

负责写 metadata

●

DELETE

负责在错误时机 free live chunk / free fake chunk

glibc 2.35 基础：这题到底在利用什么规则

下面这一节专门把这题用到的 glibc 基础规则捋一遍。后面所有“切割”“合并”“进 unsorted”“进 largebin”“刷

\_IO\_list\_all

”都建立在这些规则上。

先说明一下版本问题：

●

本地真实利用环境是题目自带的

libc.so.6

，版本是

Ubuntu GLIBC 2.35-0ubuntu3.13

。

●

下面插入的源码链接来自 codebrowser 上公开可读的 glibc 源码树，用来解释同名逻辑。

●

本文只在“本地 2.35 反汇编已验证关键行为一致”的前提下引用这些源码路径，尤其是：

○

chunk 布局

○

request2size

○

top chunk split

○

tcache / unsorted / largebin

○

\_IO\_flush\_all

○

\_IO\_wfile\_overflow

○

setcontext

1\. chunk 物理布局：为什么 free chunk 头上会出现

fd/bk

glibc 在

malloc.c

里用

struct malloc\_chunk

这套“视图”来描述 chunk。对这题最有用的字段只有 6 个：

这不是说“每个已分配 chunk 都真的长期长这样”，而是说：

●

已分配 chunk 至少有

prev\_size

/

size

这两个头字段；

●

一旦 chunk 被 free，用户区开头就会被 allocator 接管，拿来放：

○

fd

○

bk

○

对大 chunk 还会放

fd\_nextsize

/

bk\_nextsize

所以这题里：

●

show(3)

读到的不是原始

'a'

●

而是 freed chunk 上的新链表指针

这不是“巧合覆盖”，而是 glibc 的正常 free 后布局。

对应源码：

●

[

malloc.c:

](https://codebrowser.dev/glibc/glibc/malloc/malloc.c.html#L1136)

struct malloc\_chunk

2.

PREV\_INUSE

到底是什么意思

很多调试器里会显示：

这个

PREV\_INUSE

不是在说“这个 chunk 自己正在使用”，而是在说：

glibc 在

malloc.c

里直接把这个 bit 塞在

size

的最低位。它的意义是：

●

如果

PREV\_INUSE=1

glibc 不能往前合并，也不能安全地去读前一个 chunk 的

prev\_size

●

如果

PREV\_INUSE=0

glibc 才会认为当前 chunk 前面是一块 free chunk，并尝试 backward consolidate

所以这题里修 fake chunk 周围的头，不只是为了“好看”，而是为了让

\_int\_free

在检查：

●

prev\_inuse

●

nextsize

●

邻块边界

这些条件时不直接崩掉。

对应源码：

●

[

malloc.c:

](https://codebrowser.dev/glibc/glibc/malloc/malloc.c.html#L1410)

PREV\_INUSE

[

相关宏

](https://codebrowser.dev/glibc/glibc/malloc/malloc.c.html#L1410)

●[malloc.c: chunk 说明注释](https://codebrowser.dev/glibc/glibc/malloc/malloc.c.html#L1161)

3\. 为什么

key\_size = 0x100

会得到

0x110

chunk

这是这题最值得单独说明的一个点。

glibc 的源码里有两个看起来矛盾、但实际上一起成立的事实：

1

chunk2mem

/

mem2chunk

使用的

CHUNK\_HDR\_SZ

是

2 \* SIZE\_SZ

，也就是 64 位下的

0x10

；

2

request2size(req)

只会在请求上额外加

SIZE\_SZ

，也就是 64 位下的

0x8

，再做 0x10 对齐。

源码里对应的是：

●

CHUNK\_HDR\_SZ

●

request2size(req)

●

以及前面那段 “allocated chunk looks like this” 的解释

这意味着：

但是“从请求大小到 chunk size”的换算并不是简单的：

而是更接近：

原因就在 glibc 自己的注释里写得很明白：当前 chunk 在 in-use 状态下，可以把“下一个 chunk 的

prev\_size

那 8 个字节”借给应用当可用空间的一部分，所以对

request2size

来说，真正计入本 chunk 开销的是

SIZE\_SZ

而不是完整

CHUNK\_HDR\_SZ

。

于是这题里常见尺寸的映射是：

所以你在这题里说：

●

key\_size = 0x100

对应

0x110

●

key\_size = 0x210

对应

0x220

是直接能从 glibc 的

request2size

逻辑推出来的，不是经验背表。

对应源码：

●

[

malloc.c:

](https://codebrowser.dev/glibc/glibc/malloc/malloc.c.html#L1281)

CHUNK\_HDR\_SZ

[

/

](https://codebrowser.dev/glibc/glibc/malloc/malloc.c.html#L1281)

chunk2mem

[

/

](https://codebrowser.dev/glibc/glibc/malloc/malloc.c.html#L1281)

mem2chunk

●

[

malloc.c:

](https://codebrowser.dev/glibc/glibc/malloc/malloc.c.html#L1314)

request2size

●[malloc.c: allocated chunk 注释](https://codebrowser.dev/glibc/glibc/malloc/malloc.c.html#L1164)

4.

malloc

和

calloc

的入口差别：为什么这题的

CREATE

更容易切到 top

protokms

的

CREATE

调的不是

malloc

，而是：

这在 glibc 2.35 里有一个对本题非常关键、但很容易被忽略的区别：

●

\_\_libc\_malloc

在入口就会先做一次 tcache fast path：

○

checked\_request2size(bytes)

○

计算

tc\_idx

○

如果对应 tcache bin 非空，就直接

tcache\_get(tc\_idx)

返回

●

\_\_libc\_calloc

虽然也会

MAYBE\_INIT\_TCACHE()

，但它后面不是“先从 tcache 直接弹一个回来”，而是拿着

sz

继续走

\_int\_malloc(av, sz)

，最后再做清零

所以阶段 1 那几轮连续

add/free

的作用，不只是口语化地说“把 tcache 填满”，而是更准确地：

●

把几类常见尺寸的 free chunk 暂时停在 per-thread tcache 里；

●

让 arena 视角下的 unsorted / regular bins 状态更干净；

●

而后面的

CREATE(calloc)

又不会像普通

malloc

那样，在入口先把这些 tcache 项直接

tcache\_get

出来

这就解释了一个很容易让人困惑的现象：

●

明明前面已经预热过

0x210

这类尺寸；

●

但后面调试时，仍然能看到贴着 top 的新切块

从源码视角看，这并不矛盾。被“寄存”在 tcache 里的 chunk，和

\_int\_malloc

正在处理的 arena bins / top chunk，本来就是两套路径。本题正是利用了

CREATE=calloc

这个入口差异，让 heap grooming 的结果更稳定。

对应源码：

●

[

malloc.c:

](https://codebrowser.dev/glibc/glibc/malloc/malloc.c.html#L3293)

\_\_libc\_malloc

[

的 tcache fast path

](https://codebrowser.dev/glibc/glibc/malloc/malloc.c.html#L3293)

●

[

malloc.c:

](https://codebrowser.dev/glibc/glibc/malloc/malloc.c.html#L3698)

\_\_libc\_calloc

[

直接进入

](https://codebrowser.dev/glibc/glibc/malloc/malloc.c.html#L3698)

\_int\_malloc

5\. 这题几个关键 size 在 glibc 里分别会落到哪条路径

如果把 glibc 2.35 的几个宏直接代进来，可以先得到两个边界：

●

MIN\_LARGE\_SIZE = 0x400

也就是只有

chunk size < 0x400

才算

in\_smallbin\_range

●

TCACHE\_MAX\_BINS = 64

配合

tidx2usize(idx)

可知默认最大 tcache request 是

0x408

，对应最大 tcache chunk 是

0x410

于是这题里常见尺寸的“归宿”可以直接列出来：

这里最关键的不是背表，而是看出这几件事：

1

题里绝大多数正常申请尺寸，其实都还在默认 tcache 覆盖范围内。

2

0x400

是一个很值得注意的边界：

○

对

in\_smallbin\_range

来说，

0x400

已经不再算 smallbin；

○

但对 tcache 来说，它仍然还能被缓存。

1

最后专门伪造

0x430

不是随手写的大数字，而是为了同时满足：

○

这块 fake chunk 不会先被 tcache 吃掉；

○

它也不会走 smallbin；

○

free 之后会先进入 unsorted，后续再由

\_int\_malloc

整理进 largebin

这也就是为什么后面的

free(6)

，目标必须是一块

0x430

级别的 fake chunk，而不是

0x220

、

0x310

、

0x400

这种还会被前面路径截走的尺寸。

对应源码：

●

[

malloc.c:

](https://codebrowser.dev/glibc/glibc/malloc/malloc.c.html#L292)

TCACHE\_MAX\_BINS

[

/

](https://codebrowser.dev/glibc/glibc/malloc/malloc.c.html#L292)

tidx2usize

●

[

malloc.c:

](https://codebrowser.dev/glibc/glibc/malloc/malloc.c.html#L1558)

MIN\_LARGE\_SIZE

[

/

](https://codebrowser.dev/glibc/glibc/malloc/malloc.c.html#L1558)

in\_smallbin\_range

●

[

malloc.c:

](https://codebrowser.dev/glibc/glibc/malloc/malloc.c.html#L1603)

bin\_index

6\. top chunk：为什么前两次

add/free

看起来像“从顶上切下来”

glibc 的

Top

注释直接说明了：

●

top chunk 永远不在任何 bin 里；

●

它是“堆末尾那块还能继续往后长”的特殊 chunk；

●

当没有更合适的 bin chunk 时，

malloc

会优先从 top 上切一块给当前请求；

●

剩余部分继续作为新的 top chunk。

而

\_int\_malloc

的

use\_top:

路径里，逻辑就是：

1

victim = av->top

2

计算

remainder\_size = size - nb

3

remainder = chunk\_at\_offset(victim, nb)

4

av->top = remainder

5

当前请求拿

victim

6

剩下的

remainder

继续充当新的 top

这正好解释了你前面那两次：

为什么在图上看起来就是：

这不是题目自己实现了什么“切片器”，而是 glibc 在

use\_top:

这条普通分配路径上天然就会这么做。

对应源码：

●[malloc.c: Top 注释](https://codebrowser.dev/glibc/glibc/malloc/malloc.c.html#L1665)

●

[

malloc.c:

](https://codebrowser.dev/glibc/glibc/malloc/malloc.c.html#L4427)

\_int\_malloc

[

的

](https://codebrowser.dev/glibc/glibc/malloc/malloc.c.html#L4427)

use\_top:

[

路径

](https://codebrowser.dev/glibc/glibc/malloc/malloc.c.html#L4427)

7\. tcache：为什么脚本一上来都是 7 次

开头这些循环：

不是拍脑袋写的。glibc 2.35 的

malloc.c

里默认就是：

而

mp\_.tcache\_count

默认就初始化成这个值。

也就是说，在默认配置下：

●

每个 tcache bin 最多缓存 7 个 chunk；

●

你先做 7 次同 size

malloc/free

●

就能把对应 size class 的 tcache 状态固定下来

这也解释了为什么这题脚本里大量出现 “连续 7 次相同 size 的 add/free”。

另外，tcache 单链表的

next

不是明文指针，而是

PROTECT\_PTR

处理过的值。所以你调试里看到像：

这种“不像正常地址”的数字，不是 GDB 坏了，而是 safe-linking 的结果。

对应源码：

●

[

malloc.c:

](https://codebrowser.dev/glibc/glibc/malloc/malloc.c.html#L311)

TCACHE\_FILL\_COUNT = 7

●

[

malloc.c:

](https://codebrowser.dev/glibc/glibc/malloc/malloc.c.html#L3156)

tcache\_put

●

[

malloc.c: safe-linking

](https://codebrowser.dev/glibc/glibc/malloc/malloc.c.html#L320)

PROTECT\_PTR

8\. free 的正常路径：为什么 fake chunk 周围必须修成“像真的”

glibc 对 normal chunk 的

free

路径并不是“塞进 bin 就完了”，而是要先过一轮基本一致性检查，再决定要不要前后合并。

在

\_int\_free\_merge\_chunk

/

\_int\_free\_create\_chunk

这两段里，glibc 会做：

1

检查

nextchunk

是否越界；

2

检查

prev\_inuse(nextchunk)

；

3

如果前后有 free chunk，就尝试 forward/backward consolidate；

4

最后把整理出来的新 free chunk 挂到 unsorted；

5

如果它不是 smallbin 范围，还会把

fd\_nextsize / bk\_nextsize

先清空。

这正是为什么你在伪造

0x421/0x431

之后，还必须用：

去把重叠区域里的几个小 chunk 头补回来。

不修的话，glibc 在

free(6)

这一步读到：

●

邻块 size

●

PREV\_INUSE

●

next chunk 边界

时就会直接在

\_int\_free

的一致性检查里崩掉，根本走不到 unsorted / largebin。

对应源码：

●

[

malloc.c:

](https://codebrowser.dev/glibc/glibc/malloc/malloc.c.html#L4688)

\_int\_free\_merge\_chunk

[

\->

](https://codebrowser.dev/glibc/glibc/malloc/malloc.c.html#L4688)

\_int\_free\_create\_chunk

9\. unsorted bin：为什么 glibc 会先给“一个机会”

glibc 对 unsorted bin 的官方注释非常适合拿来理解这题：

●

所有“分裂出来的 remainder”

●

以及所有“刚 free 回来的普通 chunk”

●

都会先放到 unsorted

●

然后在后续

malloc

里给它们一次“直接复用”的机会

●

只有不合适时，才再分流到 regular bins

所以这题里 fake

0x430

chunk 的正确理解不是：

而是：

这个“中间先挂 unsorted”的阶段，正是我们补写

bk\_nextsize

的窗口。

对应源码：

●[malloc.c: Unsorted chunks 注释](https://codebrowser.dev/glibc/glibc/malloc/malloc.c.html#L1648)

●

[

malloc.c:

](https://codebrowser.dev/glibc/glibc/malloc/malloc.c.html#L4726)

\_int\_free\_create\_chunk

[

挂 unsorted

](https://codebrowser.dev/glibc/glibc/malloc/malloc.c.html#L4726)

10\. largebin：为什么是

target - 0x20

当 unsorted 里的大 chunk 在后续

malloc

过程中被整理进 largebin 时，glibc 会维护两套链：

●

普通双向链：

fd / bk

●

同尺寸有序链：

fd\_nextsize / bk\_nextsize

你这题真正利用的是 largebin 插入时对

fd\_nextsize / bk\_nextsize

的维护写入。

源码里最关键的几行就是：

●

victim->fd\_nextsize = fwd

●

victim->bk\_nextsize = fwd->bk\_nextsize

●

fwd->bk\_nextsize = victim

●

victim->bk\_nextsize->fd\_nextsize = victim

最后这一句就是 largebin attack 的核心写原语来源。

而

fd\_nextsize

这个字段位于 free chunk 视图中的：

所以你只要伪造：

当 glibc 执行：

时，实际落点就变成：

这就是这题里：

为什么正好能把

\_IO\_list\_all

改成堆地址。

对应源码：

●[malloc.c: largebin 插入 nextsize 链](https://codebrowser.dev/glibc/glibc/malloc/malloc.c.html#L4179)

利用链

阶段 1：预热 tcache，固定后续分配行为

开头四个循环：

目的不是立刻利用，而是把常见 size class 的 tcache 状态先固定下来。这样后面做切割、复用、UAF alias 的时候，chunk 的来源会更可预期。

这里还有一个和题目实现强相关的效果：这些预热出来的 free chunk 会先停在 tcache 里，而

CREATE

走的是

calloc

，不是

malloc

的入口快路径。也就是说，后面 allocator 在 arena 视角下看到的状态，会比“普通菜单题直接用 malloc”更干净，这正是后面能稳定做 top split / alias 的基础之一。

阶段 2：先从 top 切两刀，准备后续的“堆切割”

接下来的两步：

本质是在当前 top chunk 上预先切出两个目标 size 的空闲块。这样后面失败

CREATE

从这些大块里继续切的时候，布局会更稳定。

你调试里看到“紧挨着 top 的 free chunk 先变成

0x130

，再多出一个

0x230

/

0x220

级别的大块”，就是这两刀的直接结果。

尤其第二刀最容易让人误判成“应该先去复用 tcache 里的旧块”。但这题这里真正发生的是

calloc -> \_int\_malloc

这套路径，而前面预热出来的同类 chunk 又被暂存在 per-thread tcache 里，不在 arena bins 上，所以你在堆图里看到贴着 top 的新切块，并不和前面的 7 次预热矛盾。

阶段 3：失败 CREATE + 复用，制造 stale alias

核心序列是：

这段的关键不是“单点 UAF”，而是“先切、再复用、再 alias”：

1

free(0)

把一个可控小块放回 bin。

2

失败

add(0, 0x100, 0x120)

会从这个 free chunk 里切出一块

0x110

，然后马上 free，但

slot 0

仍然指着它。

3

add(3, 0x100,...)

立刻复用同一块，于是

slot 0

和

slot 3

实际指向同一块内存。

4

此时

slot 0

是 stale alias，

slot 3

是逻辑上的真正拥有者。

5

再

free(0)

，等价于从背后把

slot 3

的 live chunk free 掉。

于是我们得到：

阶段 4：为什么

show(3)

能同时泄露 libc 和 heap

泄露点在：

原因是：

●

slot 3

逻辑上还存在

●

但它指向的 chunk 已经被 free 给 allocator

●

READ

又会把

value\[3\]

开头的

value\_len\[3\]

字节整个打回给我们

这样读出来的就不再是原始

'a'

，而是当前 freed chunk 上的 bin 元数据。

这里能恢复两个基址：

1

main\_arena

附近地址

用来算

libc base

2

tcache / 堆上指针

用来算

heap base

到这里，后面的 fake FILE、

\_IO\_list\_all

、堆上 ROP 全部都有了定位基础。

阶段 5：伪造 largebin victim

先把

slot 4

准备成 largebin victim 的落点：

这几步的作用是：

●

slot 4

附近先整理出一块将来能被重新占用的大区域

●

slot 6

则通过失败

CREATE

拿到一个 stale pointer

●

后面重新把

slot 4

申请回来并覆写后，

free(6)

就不再是“释放原来的小块”，而是在用 stale pointer 去 free 我们伪造出来的 fake chunk

然后在

slot 4

里布置 fake chunk 头：

这里的布局含义是：

●

前

0x100

字节保留给正常数据区

●

从

+0x100

开始伪造一个

size = 0x421

的 chunk 头

●

紧跟着再伪造下一个 chunk 的头，让 glibc 的相邻检查看起来成立

而这一句：

是在修补被重叠区域覆盖到的几个小 chunk 头，避免后续

free

/ 分配时直接触发一致性检查崩掉。

阶段 6：

free(6)

的真正作用

这一步很关键：

它不是在“正常释放 6 号块”，而是在拿

slot 6

这个 stale pointer 去释放我们刚刚在

slot 4

里伪造出来的 fake

0x430

chunk。

也就是说：

这一步之后，fake

0x430

chunk 就进入了 unsorted bin。

这里它之所以不会先被 tcache 吃掉，恰恰就是因为我们释放的已经不是原来的小块，而是一块伪造出来的

size = 0x430

的大 chunk。这个尺寸超过了默认 tcache 最大 chunk

0x410

，同时也已经落在

!in\_smallbin\_range

的区域里，所以 arena 路线只会先把它挂到 unsorted，后面再进入 largebin。

阶段 7：为什么 largebin attack 能改

\_IO\_list\_all

这里要结合 glibc 2.35 的

malloc/malloc.c

看。

1\. fake chunk 先进入 unsorted

free(6)

后，这块 fake

0x430

先被挂进 unsorted bin。这个阶段 glibc 主要改的是：

●

fd

●

bk

也就是 chunk 用户区前面的那组链表指针。

而

fd\_nextsize / bk\_nextsize

这组和 largebin 相关的字段，在它还停留在 unsorted 的时候通常还没被真正接管。这一点非常重要，因为它给了我们一个“先 free 进 unsorted，再补写 nextsize 字段”的窗口。

2\. 在 fake chunk 里补写

bk\_nextsize

脚本后面重新写

slot 4

：

这里最关键的一项是：

之所以是

\-0x20

，是因为在 glibc 的 largebin 插入逻辑里，会维护同 size 链表，存在这样一类写入：

而

fd\_nextsize

字段正好位于 chunk 基址

+0x20

左右的位置。所以如果伪造：

那么这次写入的落点就会变成：

也就是把

\_IO\_list\_all

改成我们堆上的 fake FILE 地址。

3\. 触发 unsorted -> largebin 排序

后面的：

本质上是在继续推动 allocator 的状态变化，让之前那块挂在 unsorted 上的 fake

0x430

chunk 被正式整理进 largebin。

一旦这次 largebin 插入发生，上面的

bk\_nextsize -> fd\_nextsize

写入就会被触发，于是

\_IO\_list\_all

被改到堆上。

从

malloc.c

的视角看这一步到底发生了什么

如果把这段利用完全翻成 glibc 术语，其实就是：

这里最重要的不是背某一个 if，而是抓住

malloc.c

的两个阶段：

1

\_int\_free

阶段

负责把这块 fake

0x430

chunk 挂到 unsorted。

2

\_int\_malloc

阶段

后续分配时会扫描 unsorted，把不直接满足当前请求的 chunk 重新分类到 smallbin / largebin。

我们这题正是利用了这两个阶段之间的空档：

●

先让 fake chunk 作为“看起来合法的大 free chunk”进入 unsorted

●

再在它还没被整理进 largebin 前，补写

bk\_nextsize

●

最后通过后续分配触发 glibc 真正去做 largebin 插入

所以从源码视角看，

target - 0x20

不是一个孤立技巧，而是卡在了 “unsorted 挂入” 和 “largebin 整理” 这两个 malloc 路径之间。

glibc 源码可以直接看：

●

malloc.c

阶段 8：为什么

\_IO\_list\_all

一旦被改，退出时就会命中 fake FILE

这一段要看 glibc 2.35 的

libio/genops.c

。

进程退出时，glibc 会沿着

\_IO\_list\_all

遍历 FILE 链表并尝试 flush。对我们来说，最关键的结论只有两个：

1

\_IO\_list\_all

是 exit 路径上必经的 FILE 链表头。

2

如果 fake FILE 满足 flush 条件，glibc 就会调用它的 vtable。

所以 largebin attack 成功之后，后面的事情其实就很直接：

为什么这里不能乱写 vtable

glibc 2.35 不会无条件相信你给的 vtable。

libioP.h

里有

IO\_validate\_vtable

，它会检查传入的 vtable 指针是不是落在 glibc 自己维护的

\_\_io\_vtables

区间里。

这意味着：

●

如果 vtable 直接指到堆上，通常会当场被拦下；

●

如果 vtable 指到 glibc 自己的 jump table 区域里，就能通过这层校验。

所以脚本里这一项：

并不是“随便找了个函数表地址”，而是同时满足两个条件：

1

仍然落在 glibc 合法的 vtable 区域里，能过校验；

2

通过

+0x30

这个偏移，把后续 JUMP 取到的槽位整体平移到 exploit 想要的 wide FILE helper 路径上。

这也是为什么这题最后走的是

\_IO\_wfile\_\*

路线，而不是直接伪造一张完全自定义的 FILE vtable。

对应源码可以看：

●

genops.c

●

libioP.h

阶段 9：fake wide FILE +

setcontext+0x3d

脚本里伪造的 FILE 在

slot 4

，堆上的上下文区在

slot 13

。

先在

slot 13

放好 ORW 和上下文数据：

然后在

slot 4

构造 fake FILE：

这里最重要的不是背偏移，而是理解几个约束：

1

\_mode = 1

逼 glibc 走 wide FILE 分支。

2

\_wide\_data

指向我们伪造的宽字符缓冲区。

3

wide\_data

里的

write\_ptr > write\_base

满足退出时的 flush 条件。

4

vtable = \_IO\_wfile\_jumps + 0x30

把调用引到 wide FILE 路径。

5

最终通过这条路径落到我们布置好的

setcontext+0x3d

。

为什么这些字段刚好满足 flush 条件

从

genops.c

的

\_IO\_flush\_all\_lockp

视角看，glibc 在遍历 FILE 链时，真正关心的是：

而我们这份 fake FILE 的对应关系正好是：

●

\[fp + 0xc0\] = 1

也就是

\_mode = 1

，强制走 wide 分支；

●

\[fp + 0xa0\] = fake\_addr - 0x10

也就是

\_wide\_data = fake\_addr - 0x10

；

●

因为

\_wide\_data = fake\_addr - 0x10

所以

wide\_data + 0x18 == fake\_addr + 0x8

所以

wide\_data + 0x20 == fake\_addr + 0x10

而脚本里：

刚好等价于：

于是：

flush 条件自然成立，glibc 就会继续往 wide FILE 的 overflow 路径走，而不是提前把这个 fake FILE 忽略掉。

为什么 wide FILE 路径会和

setcontext

接上

从本地

libc.so.6

的

\_IO\_wfile\_overflow

反汇编可以直接看到，这条路径一开始就会：

也就是先取：

后续大量逻辑也都是围绕这块

\_wide\_data

结构取字段、更新指针、再通过 vtable helper 继续走。所以这条链的本质不是“直接 call setcontext”，而是：

1

先把 glibc 骗进一条合法的 wide FILE 路径；

2

让它在这条路径里稳定解引用我们伪造的

\_wide\_data

；

3

再把最终 helper 的控制流带到

setcontext+0x3d

。

这也是为什么 fake FILE 里那些

\_wide\_data

、

\_mode

、

\_lock

字段一个都不能省，它们不是装饰字段，而是 glibc 真会读、真会检查的路径条件。

为什么这里必须强调 glibc 2.35 的

setcontext

本地

libc.so.6

反汇编可以直接看到：

也就是说，在这份 glibc 2.35 里，

setcontext

恢复上下文时真正依赖的是

rdx

指向的那块内存，而不是某些旧版本利用里常见的“默认靠

rdi

传上下文”。

所以这里的 fake FILE 不是简单 call 一个 gadget，而是要：

●

先把 glibc 的 wide FILE 路径调起来

●

再把它带到

setcontext+0x3d

●

然后让

rdx

指向我们在堆上准备好的上下文区

●

最终 pivot 到堆上的 ORW 链

从 glibc 源码视角把后半条链压成一句话，就是：

对应源码 / 反汇编位置：

●

wfileops.c

●

setcontext.S

阶段 10：seccomp 下的 ORW

主程序有限制，最后不能直接靠

system("/bin/sh")

收尾，所以实际 payload 走的是：

●

openat2

●

read

●

write

脚本里对应的 syscall 链是：

这里一个很容易写错的点是：

之所以专门找一个把

r10

设成

0x18

的 gadget，是因为

openat2

的第 4 个参数

size

走的是 syscall ABI 里的

r10

。

阶段 11：最后如何触发

最后并不是显式调用 fake FILE，而是给主循环发一个非法长度：

程序看到

0x1000 > 0x3ff

以后，会打印：

然后结束主循环并进入退出阶段。此时 glibc 刷新

\_IO\_list\_all

，于是命中我们堆上的 fake FILE。

本地验证结果

tracefull.3885

里最关键的几行是：

这已经足够说明：

1

非法长度成功触发退出路径。

2

\_IO\_list\_all

劫持后的 fake FILE 真的执行了。

3

openat2

确实成功打开了

/flag

。

4

read

和

write

也都跑通了。

最后的崩溃不影响拿 flag，因为 ORW 已经执行完了。

一句话概括这题

这题最关键的不是某个单独技巧，而是三个点接在一起：

1

protobuf

协议让它看起来不像普通菜单题。

2

CREATE

的失败路径把“申请、记录、校验、释放”的顺序写反了，制造出稳定 stale slot。

3

glibc 2.35 上再把这个 stale slot 扩展成：

○

堆切割

○

leak

○

free fake chunk

○

largebin attack

○

\_IO\_list\_all

○

wide FILE +

setcontext

把这几个点串起来以后，这题就从“一个奇怪的 protobuf 服务”变成了一道很完整的现代 glibc 2.35 FILE 利用题。

调试截图

下面这些图就是你调试时留下来的关键布局，和上文阶段一一对应。正文里已经把每一步在做什么说清楚了，这里保留作辅助对照。

一开始简单申请，确认基础状态：

![image.png](https://xz.aliyun.com/api/v2/files/f0dbd34e-1a3c-3021-aaa2-3d0f0e21de04)

之后进行基础布局：

释放对应 chunk：

重复操作，拿到第一批 UAF 相关布局：

合并出后面用于切割的大 chunk：

继续释放，形成后续 leak 所需的链表状态：

把 UAF chunk 再次拿回来，为稳定切

0x110

做准备：

再次释放：

继续往后切割：

恢复切割 chunk 的整体形状，同时保留残留指针：

残留指针生效后的状态：

再次申请回来，并埋 fake chunk 相关标志位：

继续补后面的 chunk 头：

通过

free(fake chunk)

成功把 largebin victim 放进 bin：

后续继续推进 allocator 状态：

再一次利用切割和整理，把目标推进到 largebin attack 可用的状态：

最后

\_IO\_list\_all

被成功改到堆上：

到这里，exit 路径就会命中 fake FILE，最终把 flag 打出来。

最终exp如下