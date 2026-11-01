---
title: Des-CTF 语料索引 — PWN（2016-2022）（56 篇比赛 WP）
tags: [source-index, corpus, des-ctf, wp, pwn]
category: pwn
triggers: ["tcache", "UAF", "rop", "IoT", "qemu", "mips", "libc-2.23", "one_gadget", "ORW", "stack-overflow", "custom-vm", "ret2libc", "libc-leak", "fsop", "ChaMd5-Venom", "setcontext", "IO_FILE", "FIT-Image", "mqtt", "CVE-2022-23613"]
created: 2026-11-01
updated: 2026-11-01
sources: [https://github.com/Dest1ny-Sec/Des-CTF-Knowledge]
---

# Des-CTF 语料索引 — PWN（2016-2022）

## Summary

外部语料库 `Dest1ny-Sec/Des-CTF-Knowledge` 中 **56 篇 PWN（2016-2022） 比赛 WP**（覆盖 53 场比赛）的结构化索引。
每条记录保留「比赛 → 题目/攻击链 → 关键 payload → 要义」，原始全文未镜像，需细节时按 `contest` 回源 GitHub 的 `CTF大赛WP集合/articles/<文件名>.full.md`。

## Key Points

- 高频考点（按 tags 词频）：tcache、UAF、rop、IoT、qemu、mips、libc-2.23、one_gadget、ORW、stack-overflow、custom-vm、ret2libc、libc-leak、fsop、ChaMd5-Venom
- 高频比赛：ZJCTF、RWCTF 5th、ctf-share、2021 西湖论剑、2021 西湖论剑 IOT (海特实验室)、2022 强网杯 final、2022 USTC Hackergame、2022 浙江省赛 / 看雪
- tags 来自上游 frontmatter（允许跨赛道噪声，如 Web 页出现 ret2libc），仅作检索线索
- 用法：Grep 本页的比赛名或考点词 → 命中条目 → 需要细节时回源原文；不要在本页上做二次推理

## 按比赛分组

### 2016 ASISCTF（1 篇）
- ASIS CTF 2016 b00ks：Edit 写 fake book 结构 + Change name 触发 Print 泄 mmap 指针指向 libc，改 free_hook 拿 s…；Print 触发条件依赖 name 字段是 b00ks 经典套路；mmap 大块堆布局可绕过 tcache 限制。

### 2016 HITCONCTF（1 篇）
- HITCON 2016 Secret Holder：unsafe unlink + 三个 chunk 构造 + 改 free@GOT = puts 泄 libc + one_gadget 拿…；unsafe unlink 是经典堆利用；改 free@GOT → puts 泄 libc → one_gadget 是无 lib…

### 2018 HITCON Training（1 篇）
- HITCON 训练场经典 single-null 指针堆菜单，三种姿势教 unlink 怎么用。 → `/`；heaparray/magic 都是全局变量，fastbin attack 0x21 fake chunk 头、unlink 双向…

### 2018 ctf-share（2 篇）
- V-lab 实验室 CTF 内核漏洞挖掘教程，覆盖 core.ko 栈溢出 + zerofs 内核文件系统越界读写两套思路。 → `内核栈溢出 ROP + kallsyms 提权函数`；内核栈溢出要点：先泄 canary/ret_addr，再 ROP 走 prepare_kernel_cred(0) → commi…
- glibc 2.32+ 堆利用：safe-linking 机制 + tcache fd 异或攻击。 → `safe-linking 异或密钥 + tcache fd 攻击`；glibc 2.32+ 的 safe-linking 机制本质是 tcache/fastbin fd 的 12-bit ASLR…

### 2018 hitcontraining（1 篇）
- hitcontraining uaf — HackNote 堆菜单 free 双 chunk 不置 NULL + 0x18 fastbin 复用 + add(0x8, p32(magic))…；UAF 经典题型:0x8 note struct (含 func ptr) + 0x18 content, free 不置 NUL…

### 2019 ZJCTF（2 篇）
- ZJCTF 2019 EasyHeap — 堆菜单 free 没清指针 + 0x71 fake chunk + 0x6020ad fd 偏 0x23 字节对齐 + heaparray 落 f…；堆菜单 free 不清指针 + 利用 unsorted bin fd 偏 0x23 字节对齐 0x6020XX 是经典 trick…
- ZJCTF 2019 login PWN 复盘，C++ 虚函数表 + 二级指针调用 + 栈子函数复用覆盖。 → `C++ 二级虚函数指针 + 栈平移覆盖`；''父子函数栈平移时，子函数栈空间的指针变量可能被父函数新调用破坏。''

### 2021 西湖论剑（1 篇）
- libc-2.33 IO_FILE 利用 _IO_wfile_jumps + setcontext+61 + ORW → `mov rdx, [rdi+8]; mov [rsp], rax; call [rdx+0x20] → setcontext+61 触发 gadget`；libc-2.33+ 没了 __free_hook 还能用，要走 IO_FILE 走 exit 时 _IO_wfile_overf…

### 2021 西湖论剑 IOT (海特实验室)（1 篇）
- 2021 西湖论剑 IoT RW：FIT Image dts 注入 + dfu 刷回 + MQTT 协议 + USB 设备模拟 → `bootargs = "console=ttyS0,115200 rdinit=/bin/sh"`；FIT Image 是嵌入式 Linux 启动标准；dfu-util 是 IoT 调试必备；MQTT 协议是 IoT 通讯主流

### 2021 Realworld CTF（1 篇）
- Realworld CTF 之 Black Box：CVE-2020-14364 QEMU 5.15 USB UHCI 驱动 do_token_setup setup_len 用户可控 →… → `do_token_setup(size=0x5000) + do_token_out(offset=-8) 改 setup_index + do_token_in(OOB read…`

### 2021 湖湘杯（1 篇）
- 第七届湖湘杯 House of Emma 高级 IO 攻击，利用 _IO_cookie_jumps vtable + large bin attack 绕 vtable 检查。 → `_IO_cookie_jumps vtable 不在检查表 + large bin attack`；_IO_cookie_jumps 是 _IO_FILE_plus vtable 检查白名单外的合法 vtable；_IO_cook…

### 2021 红帽杯（1 篇）
- 红帽杯WriteUp多方向,涵盖ChaMd5 Venom战队招新文+PHP写文件短代码+Yii2 RCE2反序列化+SQL盲注+超椭圆曲线Jacobian DLP+XTEA魔改+fmt-wr… → `flag{1b82f60a-43ab-4f18-8ccc-97d120aae6fc}`；复杂混合赛题考查多个方向:PHP写入短代码绕字符限制+phpggc工具生成反序列化payload+HEC Jacobian DLP…

### 2021 绿城杯（1 篇）
- 绿城杯WriteUp多方向,Web(warmup ezphp easy_seri)+Pwn(null off-by-one+ezuaf double-free+GreentownNote u… → `eval(system("tac pages/flag.php")) + one_gadget 0x45226`；绿城杯是glibc-2.23时代典型off-by-one+tcache攻击;git信息泄露在Web题中是常见入口;时间盲注+1字节…

### 2021 美团CTF（1 篇）
- 美团CTF WriteUp,涵盖ChaMd5 Venom招新文+PHP OOP反序列化(Users/User/Welcome/File/Logs+call_user_func+phar触发)… → `PHP反序列化 + 时间盲注 + RSA低位爆破`；PHP OOP反序列化是Web经典题型,phar元数据是稳定反序列化触发点;时间盲注+1字节差是密码攻击实用技术;RSA拼接+低位…

### 2021 虎符 CTF（1 篇）
- 虎符CTF WriteUp,ChaMd5 Venom招新+俄文密码翻译+Lua沙箱逃逸(package.loadlib+io.popen)+Bulls-Cows猜数字算法+srand预测+f… → `HFCTF{c0d3735f-4062-4f39-9ad6-9e7df7994dad}`；虎符CTF质量较高,涵盖多方向:语言学翻译(俄文)+Lua沙箱逃逸(loadlib+io.popen)+Bulls-Cows信息论…

### 2021 西湖论剑线下（1 篇）
- 西湖论剑线下WriteUp,单点渗透web(qinggan-OA+phpggc+cache类反序列化+php://filter写somnus.php)+DPR纵深渗透靶场(CGI auth+… → `php://filter/write=convert.base64-decode/resource=somnus + cache反序列化`

### 2021 长城杯（1 篇）
- 第一届长城杯网络安全大赛 easy_vm 高级 PWN，自定义 VM + 64 位页表检查漏洞 + LW/SW 任意地址读写。 → `page_table 64 位低 16 位检查 LW/SW 任意地址读写 one_gadget`；VM 申请内存 > 0x200000 触发 mmap 与 libc/ld 固定偏移是泄地址经典方法；64 位页表项只检查低 16…

### 2022 D3CTF d3kheap（1 篇）
- D3CTF d3kheap 内核提权：msg_msg+sk_buff+pipe_buffer 三结构堆喷交叉，pipe_buf_operations->release 劫持 ROP 提权。；内核 UAF 三结构交叉堆喷是现代 Linux 内核 PWN 标准姿势：msg_msg 控制 0x400 大小块 + sk_buf…

### 2022 USTC Hackergame（1 篇）
- USTC Hackergame 真机 Linux VM pwn：iPXE 启动 → busybox shell → SUID 提权链 → `/challenge setuid 0 + uid 1337 owner of /flag2`；iPXE + SeaBIOS 模拟远程固件启动是 CTF 高端考法；多层提权是 Linux pwn 实战

### 2022 强网杯 final（1 篇）
- CVE-2022-23613 整型溢出堆溢出 + rdi 控命令 + g_execlp3 提权 → `p32(0x80000000)[::-1] + 'a'*0x4160 + '/tmp/do\x00'`；老 CVE 重现是 0day 防御学习的捷径；整数溢出导致"超大 to_read"是堆溢出经典

### 2022 浙江省赛 / 看雪（1 篇）
- 堆题两套解：libc2.27 __free_hook；libc2.35 setcontext+ORW+fake link_map → `pd = p64(gold_key)+p64(free_hook) + p64(setcontext+61) + './flag\x00'`；堆题要分清 libc 版本；2.27 直接 tcache dup + free_hook；2.35 走 setcontext+61…

### 2022 西湖论剑 (F61d 战队)（1 篇）
- 西湖论剑 2022 F61d 战队：ARM pwn + Node.js 反序列化 + LSB mp3 + zip 嵌套 → `leave_ret = 0x4013A2; libcbase = recv(12) - 0x24083; system('/bin/sh')`；qemu-arm-static 是 ARM pwn 本地调试；Node.js 原型链污染是 web 入门；MP3 隐写是 misc…

### 2022 西湖论剑 IoT-AWD（1 篇）
- 西湖论剑 IoT-AWD 1-2 号固件：蓝牙 HZCSSC + boa 8080 + MIPS 路由命令注入 → `;${PATH:10:8}/${PATH:8:1}? 192.168.132.2 2333 /${PATH:14:4}${PATH:4:2}?/ ${PATH:10:8}/${PA…`；IoT AWD 模式 = 真机攻击; ble-serial 是蓝牙调试工具; ${PATH:offset:length} 绕过大小…

### 2022 西湖论剑 IoT-AWD (下篇)（1 篇）
- 西湖论剑 IoT-AWD 3 号固件：dts uartlite 启停 + ubus 协议 + OpenWrt → `uartlite status='okay' 后 mkimage 重新打包 itb 刷入`；OpenWrt ubus 是 IoT 通用协议；dts 是嵌入式硬件配置

### 2022 ACTF（1 篇）
- ACTF 2022 Nameless_a 经验分享，从 Fuzz demo 入门到 XCTF treepwn 实战的二维坐标 UAF。 → `9x9 批量 delet + add(66,66,'nameless') 触发 nameless 链表 → UAF → __free_hook=system`；Fuzz 是发现 heap 漏洞模式的有效手段；randint 随机操作 + 检查残留指针前缀可快速发现 UAF；2D 坐标二叉树…

### 2022 BFS Lab（1 篇）
- BFS Ekoparty 2022 Linux Kernel — blunder.ko IPC 模块 mmap 漏检 VM_MAYWRITE + O_RDWR 加 VM_MAYWRITE 绕…；mmap 检查 VM_WRITE 漏 VM_MAYWRITE 是经典漏洞;Hardened usercopy 限制 0xfd0 跨…

### Faraday CTF 2022 (EKOParty)（1 篇）
- Faraday CTF IoT：TotoLink N100RE路由器ping bug+MIPS逆向 → `ipAddr = (char *)inet_ntoa((dest->sin_addr).s_addr)`；Ghidra反编译MIPS二进制+FreeBSD ping bug 类比

### Hayyim CTF 2022 (AuViel)（1 篇）
- AuViel - Hayyim CTF 2022 反反病毒 0day 挖掘：fuzzuf AFL 模糊测试 clamav-0.104.2 libclamav 触发 PE 解析 adjbuf… → `cmake -D CMAKE_C_COMPILER=afl-gcc / fuzzuf afl -i input -o output -- ./clamscan --database…`

### 2022 KCTF春季赛（1 篇）
- Rust VM pwn：size_hint CVE 触发越界读写，Ubuntu 16 one_gadget 改 __libc_start_main_ret 拿 shell。；Rust 安全性 ≠ 绝对安全，第三方库 size_hint 错误实现会导致越界；带 debug 符号的 Rust 程序逆向更容易…

### 2022 PWNHUB（1 篇）
- PWNHUB 2022 冬季赛 Polaris 战队 WP: PHP eval webshell + gopher MySQL UDF sys_eval + XXTEA btea + 字符串… → `/`；/

### 2022 RWCTF 5th（2 篇）
- RWCTF 5th ShellFind 长亭科技官方 WP：IoT 固件模拟 + UDP ipfind base64 解码栈溢出 + mips 无防护 ROP 改 GOT + dup2 bu… → `rop_chain = 0x3C1C0042 (lui $gp) + 0x279CB030 (addiu $gp) + lw $v0 server_sockfd + lw $a0…`
- RWCTF 5th Shellfind 复现 WP：qemu malta mips 模拟器 + tap0 网络 + ipfind UDP 62720 + base64 栈溢出 + dup2… → `base64(b'a'*padding + rop + shellcode)`；qemu-system-mips + tap0 桥接 + chroot 启动是 IoT 复现基础；ipfind 启动时指定 eth…

### 2022 Real World CTF 5（1 篇）
- Real World CTF 5 Teewars (Teeworlds) 自定义 map 文件 CDataFile 格式 Envelope_Points 整数除法溢出 + 写 ROP 链到… → `0x45f135 mov qword ptr [rdi], rax; ret + rop.rax + rop.rdi + rop.rsi + rop.rdx + rop.sysca…`

### 2022 RealWorld CTF 4th（1 篇）
- RealWorld CTF 4th r3kapig 多 PWN 大全：svme 自定义 VM 字节码逃逸 + ql_qiling /proc/self/mem 写 libc + NBD 网络… → `code=p32(GSTORE)+p32(0)+p32(GLOAD)+p32(0x86)+p32(PUSH)+p32(0x7ffff7e18410-0x7ffff7dea0b3)+…`

### 2022 SECCON（1 篇）
- SECCON 2022 quals simplemod 复盘，30 次单字节写 + naked syscall。 → `单字节写 gbuf 任意位置 + naked syscall`；有限次数的 1 字节写可以拼凑出任意 syscall gadget。

### SECCON 2022（1 篇）
- SECCON 2022 koncha (scanf 栈溢出 + one_gadget) + babyfile (FILE 结构 _IO_read_base/vtable 偏移 trick 任… → `trick(free_speace+i, system_byte) + trick(free_speace+8+i, binsh_byte) + trick(_IO_save_ba…`

### 2022 STACK the Flags（1 篇）
- STACK the Flags 2022 cursed_grimoires：菜单 1/2/3 二进制 + 1MB 大块跨 mmap 边界 + 单字节 edit 改 stdout。 → `edit(stdout_offset+0x28, 0x50) # _IO_write_ptr LSB`；Full RELRO + Canary + NX + PIE 全开下，FSOP via _IO_wfile_jumps 仍可作为终…

### 2022 SunshineCTF（1 篇）
- SunshineCTF 2022 Pwn (3 题)：simulator libc srand 预测 + chess ANSI color PIE 泄漏 + Elden Ring 主题题。 → `libc.rand() % i + 1 # i = 10 * 10^n`；libc.srand 接受 4 字节 seed，team name 缓冲区紧接其后即可泄 seed；Elden 风格题的关键是找到…

### 2022 TCTF/0CTF（1 篇）
- TCTF/0CTF 2022 Polaris：Crypto 光滑数 DLP + Pwn babyheap 2.35 largebin + ezvm 自定义 VM。 → `n+1 全小因子 (光滑) → Pohlig-Hellman`；p+1 光滑时 Pohlig-Hellman 直接降级 DLP；libc-2.35 后 vtable 范围检查使 House of…

### 2022 Unknown CTF（1 篇）
- 高级 C++ shared_ptr 堆利用 OOB write + UAF 重叠字符串指针 + 双重 free_hook 覆写 + 字符串内嵌 /bin/sh，flag: Sh4r3d_pt… → `edit_monster(33, 0, 0, 20, 20) # overwrites counter of shared pointer instances to 0 / fre…`

### 2022 bi0sCTF（1 篇）
- bi0sCTF 2022 r3kapig 战队多题 WP，菜单类 PWN 注入与跨题 ROP 链构造。 → `静态 AES key + encrypt-decrypt 注入链`；CTF 题目间可能存在 attack 复用：encrypt 题目拿到的 oracle 可注入其他题；RCE 链要从前置题"借"密钥…

### bi0sCTF 2022（1 篇）
- WebKit B3 rangeForMask 偏差 + 整数下溢消除边界检查 → `let c = b & 2; let idx = c - 1; if (idx < 1) { idx += -0x80000000; }`；JIT range analysis 若起点假定偏移，实际 0 也可触发下溢消除上界检查；patch Reflect.strid…

### 2022 idekCTF（1 篇）
- idekCTF 2022 r3kapig 战队 Pwn + Reverse 套题，survey 泄 canary + fmt-string 任意写。 → `格式化字符串 %9$hhn + canary 泄 + ret2dlresolve`；多步 fmt-string 利用要先泄 canary 再泄 textaddr 才能构造 ret 链。

### 2022 jarvisoj（1 篇）
- jarvisoj_fm 格式化字符串经典题，AAAA-%p 找偏移 11 + %4c%13$n 写 4 字节到任意地址。 → `addr=0x0804A02C payload=b"%4c%13$n" 偏移 11 = 0xB`；格式化字符串 %n 系列可任意地址写；%hhn = 单字节写；AAAA-%p-%p... 找 AAAA 在栈上偏移；%Nc 控制写…

### n1ctf 2022 UEFI（1 篇）
- UEFI PWN 入门：OVMF 解包 + UiApp 反编译 + Double GetVariable 栈溢出 + qemu gdb 动态调试 + 劫持 Boot Manager 拿 ro…；UEFI 漏洞核心在 gRT (Runtime Services) SetVariable/GetVariable; Double…

### 2022 个人赛（1 篇）
- SROP 入门 Pwn 题，通过 sigreturn 系统调用号 15 触发 SigreturnFrame 恢复寄存器 + execve("/bin/sh") 收 shell。 → `rax=15 sigreturn syscall syscall 0x400501 binsh_addr u64 - 280`；SROP 2014 年 Vrije Universiteit Amsterdam Erik Bosman 提出；rax=15 si…

### 2022 中国工业互联网安全大赛（1 篇）
- 工业互联网预选赛：z3 解 RSA 接近素数 + bss 段 fmt 改 printf.got = system + 自定义 HTTP 协议 srand 预测 + tcache 改 free… → `z3 Solver() + p*q=n, q-p=2^420+b, b<2000; '%9$p' fmt 泄 libc; 'POST / HTTP/1.1\r\n' + p8(cm…`

### 2022 安洵杯（1 篇）
- F61d战队第二名WP,2道PWN+4道RE+Web:Babybf(BF指令越界读libc+越界写system)+Babyarm(ARM QEMU shellcode)+Reee(花指令+R… → `cmd(p8(0x3e)*0x58+(p8(0x2e)+p8(0x3e))*8+p8(0x2d)*8)/sla('msg>','s1mpl3Dec0d4r') payload='a…`

### 2022 新生赛 PWN 入门（1 篇）
- bad_c0de 新生赛 11 题 PWN 入门：canary/ret2csu/fmtstr/C++虚函数/ret2libc/orw。；canary 截断 7 字节 + '\x10' 末尾恢复；ret2csu 用 0x4012aa 0x401290 中间跳板；fin…

### 2022 春秋杯（1 篇）
- 春秋杯秋季赛 PWN 梦空间 6 层梦境关卡设计稿，盗梦空间 + 神秘博士剧情融合，覆盖 ret2text/格式化字符串/UAF/house of force 多种 PWN 技巧。 → `hotel_backdoor = 0x4006B6 snow %1515c%43$n world house of force got 区域`；6 层关卡设计覆盖栈溢出 / fmt 写 .text 段 / UAF / House of Force 全 PWN 攻击面；.te…

### 2022 某新生赛 PWN 入门（1 篇）
- bad_c0de 某新生赛 9 题 PWN：encrypto 还原 + 32/64 位 ROP + fmtstr + 字符 shellcode + seccomp+orw。；encrypto 自实现加密直接 GDB 断点读密文 (srand(1) 固定种子)；字符限制 shellcode 用 xor […

### 看雪2022 KCTF春季赛（1 篇）
- KCTF2022春Q6《废土末世》PWN：纯汇编+SROP+vsyscall syscall;retn 0xffffffffff600000 → `栈缓冲区0x10溢出;read返回字节数触发SROP;vsyscall=0xffffffffff600000 syscall;retn;syscall号15(64)/77(32)`；无libc栈溢出用SROP（sigreturn 15/77）+vsyscall=0xffffffffff600000 gadget

### 看雪2022 KCTF秋季赛（1 篇）
- KCTF2022秋Q7《广厦万间》：CVE-2022-23613 xrdp-sesman整数溢出堆溢出+trans_recv函数指针劫持 → `CVE-2022-23613 xrdp-sesman;header_size=0x80000000整数溢出;trans结构体trans_recv函数指针;g_execvp=0x41…`；header_size负数校验缺失导致整数溢出堆溢出；堆结构体函数指针劫持是经典pwn利用

### 祥云杯2022（1 篇）
- 祥云杯2022 leak：glibc 2.27堆题+IO结构体泄露+fastbin reverse into tcache → `stdout._flags=0xfbad1800;_IO_write_base=heap_addr;fastbin->bk=&tcache[size];tcache双链表fd/bk…`；无show的2.27堆题：IO结构体伪造 + fastbin reverse into tcache利用堆地址

### 2022 第四届 Real World CTF 体验赛（1 篇）
- 第四届Real World CTF体验赛赛题全解,涵盖Java Processor注解处理+SNMP aarch64 PWN+kernel UAF+Docker逃逸+lvm VM+CAP_D… → `Log4j2 ${j${::-n}di:ldap://} + H2 CREATE ALIAS SHELLEXEC`；Real World CTF体验赛质量顶级,涵盖IOT/内核/容器/VM/CAP/JNDI/Redis/H2等真实场景;Log4j…

## Connections

- 上层索引：[[ctf/source-corpus--des-ctf-knowledge]]
- Source: [https://github.com/Dest1ny-Sec/Des-CTF-Knowledge](https://github.com/Dest1ny-Sec/Des-CTF-Knowledge)

## Open Questions

- 本页为索引层，题目细节请回源；若某场比赛多题值得展开，再单独建 `ctf/<比赛>.md` 实体页。
