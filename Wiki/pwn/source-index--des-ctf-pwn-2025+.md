---
title: Des-CTF 语料索引 — PWN（2025+）（34 篇比赛 WP）
tags: [source-index, corpus, des-ctf, wp, pwn]
category: pwn
triggers: ["ret2libc", "pwntools", "shellcode", "off-by-null", "No-PIE", "no-canary", "N0wayBack 战队招新 cainyzb qq.com", "Java Runtime.exec bash base64", "timestamp year PHP 反序列化+文件包含", "XSLT TemplatesImpl 内存马", "OGNL systemProperties user.home", "MMXEncode packed_decrypt sub-key+rotate", "butterfly_mmx_encode", "custom VM dumpmem cap+keystone 重定位", "LACTF 2password i p 格式化串泄栈", "hunter2_cfc0xz68 flag", "state-change 0xF1EEEE2D 校验常量", "read 0x4012C1 通用 read", "door 0x4011D6 后门", "0x404540+0x20-1 栈迁移"]
created: 2026-11-01
updated: 2026-11-01
sources: [https://github.com/Dest1ny-Sec/Des-CTF-Knowledge]
---

# Des-CTF 语料索引 — PWN（2025+）

## Summary

外部语料库 `Dest1ny-Sec/Des-CTF-Knowledge` 中 **34 篇 PWN（2025+） 比赛 WP**（覆盖 32 场比赛）的结构化索引。
每条记录保留「比赛 → 题目/攻击链 → 关键 payload → 要义」，原始全文未镜像，需细节时按 `contest` 回源 GitHub 的 `CTF大赛WP集合/articles/<文件名>.full.md`。

## Key Points

- 高频考点（按 tags 词频）：ret2libc、pwntools、shellcode、off-by-null、No-PIE、no-canary、N0wayBack 战队招新 cainyzb qq.com、Java Runtime.exec bash base64、timestamp year PHP 反序列化+文件包含、XSLT TemplatesImpl 内存马、OGNL systemProperties user.home、MMXEncode packed_decrypt sub-key+rotate、butterfly_mmx_encode、custom VM dumpmem cap+keystone 重定位、LACTF 2password i p 格式化串泄栈
- 高频比赛：CISCN、磐石CTF2025、2025 强网杯、2025 LACTF、2025 XYCTF、2025 KCTF、强网杯、2025 磐石行动 (线下 AWD)
- tags 来自上游 frontmatter（允许跨赛道噪声，如 Web 页出现 ret2libc），仅作检索线索
- 用法：Grep 本页的比赛名或考点词 → 命中条目 → 需要细节时回源原文；不要在本页上做二次推理

## 按比赛分组

### 2025 KCTF（1 篇）
- 2025 KCTF 第八题 暗云涌动：自定义 VM + 指针标记 + 整数溢出 bypass adjust_pointer → 任意地址读写 + IO 链劫持。星盟出题。 → `0x4000000000200000 + 0x4000000000000000 = 0x8000000000200000 (整数溢出→指针)`；自定义 VM PWN 必看 ALU 整数溢出是否破坏指针标记；堆地址爆破 4-bit（16 试 1 中）时考虑 async 并发。

### 2025 LACTF（1 篇）
- 2025 LACTF 三 pwn：2password 格式化串泄栈 + state-change read 栈迁移到 0xF1EEEE2D 后门 + minceraft 看我世界游戏菜单。 → `p.sendline(f'%{i}$p') for i in range(6, 9)`；格式化串 '%N$p' 泄栈 + 小端序 hex 转 flag 是入门 pwn 第一招；栈迁移要算准 'target_addr -…

### 2025 XYCTF（1 篇）
- XYCTF 菜单 pwn：1 4 1 触发栈溢出 + bss 写 /bin/sh + ret;ret 对齐 + leave_ret 栈迁移 + system("/bin/sh")。 → `pay=b'/bin/sh\x00'+p64(rdi+1)+p64(rdi)+p64(sh)+p64(elf.plt['system'])`；ret;ret gadget 用来 16 字节对齐栈（system 之类要求 movaps 16 字节对齐）；rdi+1 是 re…

### 2025 强网杯（1 篇）
- 2025 强网杯 N0wayBack 招新+综合：Java XSLT 内存马 + PHP timestamp 反序列化 + OGNL + 自定义 VM 反编译 + MMXEncode pac… → `packed_decrypt: x[i]=(x[i]-key[i])&0xff; x=rotate_right_1; swap_2; x[i]^=key[i]`

### 2025 磐石行动 (线下 AWD)（1 篇）
- AWD 多目标攻击：4 题 30 机器脚本化攻击 + flag 自动提交 → `payload = shellcraft.cat('/flag/flag') + push 0 (rsp 16 字节对齐)`；AWD 模式要脚本化批量打；seccomp 时 system 不可用要 ORW；整数下溢注意负数边界

### 2025 黄鹤杯（1 篇）
- 2025 黄鹤杯 PWN Virtual Vehicle：自定义 VM 字节码 + vtable 8 函数指针 + seccomp 禁 execve + hashtable RWX 段喷 s… → `vtable 8 个函数指针 + hashtable RWX mmap 0x1000`；VM PWN 套路：先反汇编字节码格式（choide+arg），再算 op 总数与 vtable 索引对应关系，seccomp-t…

### 2025 CCF智能汽车大赛（1 篇）
- 首届CCF IVC 2025 Mini-Venom第一名:车联网pwn-3(0x88栈溢出+ret2libc)+easylogin(hash长度扩展)+serp(git+PHP反序列化Spl… → `b''a''*0x88 + p64(pop_rdi) + p64(1) + p64(pop_rsi) + p64(write_got)*2 + p64(write_plt) + p…`

### 2025 CISCN（2 篇）
- CISCN 2026 Easy_rw 系列 — 自定义 packet 协议 (header+len+payload) + Easy_rw_revenge proxy RC4 转发 + rts…；自定义二进制协议 reverse: header+length+payload 模板;RC4 + libc.srand(time(…
- ciscn 2025 final MQTT Pwn 题：物联网车机诊断协议 + 命令注入。 → `MQTT set_vin 命令注入 + sum2hex 鉴权`；物联网协议 (MQTT/CoAP/Modbus) 是现代 CTF 新增长点。

### HGAME 2025 杭电（1 篇）
- HGAME 2025 PWN：format字符串+diary keeper off-by-null合并 → `%*d+%s leak libc; AE64(system ROP)`；负索引vuln可绕过；PROTECT_PTR指针保护可用低12位异或绕过

### HKCERT CTF 2025 資格賽（1 篇）
- HKCERT 2025资格赛PWN方向5题集合,涵盖fmtstr基础题+PIE leak+GOT覆盖+ret2libc+ORW+musl FSOP fake IO_FILE。 → `fmtstr_payload(6, {printf_got: backdoor})`；fmtstr_payload偏移要从栈上找;musl的IO攻击面比glibc小很多,要自己分析__stdout_FILE结构体并构…

### 2025 ImaginaryCTF（1 篇）
- ImaginaryCTF 2025 Pwn 多题 (ret2libc 模板 / GOT patch / 多次栈迁移 / fmt-string) 速查。

### L3CTF 2025（1 篇）
- L3CTF 2025 heack/heack_revenge：RPG 风格 PWN 菜单 + SHSTK/IBT 双重 CET 加固 + off-by-null 改 chunk size +… → `over(b'a'*0x23+p8(0x37)+p8(0x6a)) + b'a'*0x20+rop (rdi+1+rdi+/bin/sh+system)`

### 2025 L3HCTF（1 篇）
- L3HCTF 2025 XMCVE-Polaris 战队的 vul2 / vul2_revenge 两道 libc 2.35 堆题，House of Apple 2 收尾。

### 2025 Nepctf（1 篇）
- Nepctf 2025 PWN: 多线程 race condition 绕过 flag 字符串过滤 + printf(name) format string 泄栈地址。 → `/`；/

### 2025 SECCON 13 CTF Final（1 篇）
- SECCON 13 CTF Final 作問記：FastCGI HTTP 头注入 HTTP_GIVEMEFLAG 触发 flag + URL 短链 + nginx ngx_http_vali… → `Host: app:%0d%0ahogehoge + FastCGI FCGI_PARAMS \x01\x04\x00\x01\x00\x15\x00\x00\x0f\x04HTT…`

### 2025 SQCTF（1 篇）
- 第四届 SQCTF PWN 方向 9 题合集，覆盖 ret2backdoor + shellcode + SROP + fmt 泄 canary + stack pivot + syscal… → `9 个 PWN 题涵盖 ret2backdoor/shellcode/SROP/fmt-leak/stack-pivot/syscall/asm 全套 PWN 基础`

### SUCTF 2025（1 篇）
- SUCTF 2025 imLZH1 两道 Pwn 题解：SU_baby 自定义命令菜单触发 syscall(open+read+write flag) + SU_text 自定义指令集 (0… → `gadget=0x04028A6 (mov edi,eax; push rsp; pop rsi; syscall) / sc=asm(shellcraft.open('flag'…`

### 2025 TAMUCTF（1 篇）
- TAMUCTF 2025 狼组 writeup：7 题 Pwn + 4 题 Reverse，混合 Go/C++/Python + Go SSRF + 矩阵 Z3。 → `io.send(b"x54x5Ex31xFFx0Fx05xC3") # 7-byte shellcode`；当 PIE 1/16 爆破可行时，覆盖 ret_addr 低 2 字节配合 read 重写栈是稳定技巧。

### 2025 miniL（1 篇）
- miniL 2025 mmapheap 题解，自定义 mmap 分配器 + 双向链表 chunk 攻击。 → `mmap 段切分 + 双向链表 + chunk overlap`；自定义分配器逆向关键是看 chunk_header 结构和链表操作。

### 2025 pwnable.kr（1 篇）
- pwnable.kr 经典 bof 题：52 字节 (13 * 4) padding + p32(0xcafebabe) 覆盖 key → 触发 system("/bin/sh") 反弹 s… → `bof = b'A'*13*4 + p32(0xcafebabe) / ssh('bof', 'pwnable.kr', password='guest', port=2222)…`

### 2025 公众号文章（1 篇）
- Fuzz在CTF基础利用：树形结构+随机操作+EOFError检测漏洞 → `TooManyElementsError → retry; EOFError → success`；堆漏洞Fuzz可降低堆风水调试难度

### 强网拟态 2025 BabyStack（1 篇）
- 强网拟态2025 BabyStack简单栈溢出,No-PIE+no-canary+NX,两次read(24/256),第二次248字节垃圾+8字节0x1337abc覆盖栈上v3变量,绕过if… → `b'a'*0xf8 + p64(0x1337abc)`；变量覆盖是栈溢出基础题,关键是计算read起点到目标变量的距离;初始化值与检查值不同(0xabc1337 vs 0x1337abc…

### 强网拟态 2025 Stack（1 篇）
- 强网拟态2025 Stack SROP+栈迁移+shellcode,No-PIE+No-canary+NX+Full RELRO,泄露栈+栈迁移到BSS+SROP调用mprotect(7=P… → `SROP + mprotect(bss, 0x100, 7) + shellcode(openat+sendfile)`；SROP是绕过NX+Full RELRO+无libc环境的终极武器,关键是构造SigreturnFrame+read返回15触发r…

### 2025 强网杯（1 篇）
- 整数溢出 + UAF 装备描述指针 + tcache 泄露 + stderr ROP 绕过 seccomp → `heap_addr = (edit_ring(b'a') << 12) - 0x19000; edit_ring(rop_chain_via_stderr)`；游戏题整数溢出金币可触发经济崩溃；C++ UAF 装备描述指针是经典 pwn 套路；glibc 2.32+ tcache 加密指针…

### 2025 津京冀网络攻防大赛决赛（1 篇）
- 津京冀决赛pwn1+pwn2全场唯一解：fopen(NULL) FILE结构泄漏+有符号比较堆溢出+fastbin attack stdout → `edit new_length=0x7fffffff00000000+0x80;libc=main_arena+88-0x3EC680;libc+0x4F440=system;pw…`；堆题中edit长度校验有符号比较用极大数绕过；fopen(NULL)FILE结构含libc指针可泄漏

### 磐石CTF2025（2 篇）
- 磐石CTF2025 account：32位v1数组越界+循环10次+v2覆盖+两次ROP（泄libc+ret2libc） → `v1[v2++]=v0;v1[10]=v2 idx;v1[14]=ret_addr;main=0x8049264;pop_ebx_ret=0x08049022;puts@got泄l…`；循环写入数组时v2 idx也可被覆盖，构造精确索引可写任意栈位置
- 磐石CTF2025 user：glibc 2.31菜单题无show+IO_FILE结构体伪造+stdout泄露libc → `heap[-8]=_IO_2_1_stdout_;_flags=0xfbad1880;_IO_write_base低地址;stdout距离heap 0x40字节;heap数组5个c…`；堆bss数组距stdout固定0x40字节可用edit(-8)修改stdout；_flags=0xfbad1880触发写缓冲

### 2025 自学系列 (来源 ctfiot)（1 篇）
- 经典 pwn 入坑文 — x86_64 16 字节栈对齐 movaps 崩溃 → `p64(0x00401186+1) 即跳到 push rbp 之后`；x86_64 SystemV ABI 要求调用时 rsp % 16 == 0；劫持 ret 跳到 push rbp 之后可省一次…

### 2025 长城杯（1 篇）
- 长城杯2025 simi-final php-master详解:PHP文件上传+Docker gdbserver调试+zend内存管理(heap+bin+free_slot)逆向+vuln.… → `version: ''3'' services: php-master ports: ["80:80","28888:9191"] volumes: [./data:/var/ww…`

### 2026 天府杯（1 篇）
- 2026 天府杯 pwn 0 解题目：libc-2.27 菜单题 add+show+0x400b7f/bea/ccd 子函数+result 全局标志 0/1，二次入口切换。 → `result=1 后 1337 失效，要重置触发两次入口`；天府杯是国内顶级线下 CTF，2026 pwn 出现 0 解题目说明难度极高；libc-2.27 经典堆漏洞是 tcache 时代…

### HGAME 2026（1 篇）
- HGAME 2026复现：short整数边界绕过+canary泄露+shellcode → `-32768 = 0xFFFB = +5 绕过case v6`；short int -32768 (0x8000) 取反+1=0xFFFB 实际为+5

### 2026 N1CTF Junior（1 篇）
- fgets 栈溢出 + add_rbx_rdx 累加写入 magic + SigreturnFrame 调 syscall → `payload = b'a'*0x28 + p64(add_rbx_rdx_ret)*6 + p64(pop_rbp) + p64(0x404018+0x3d) + p64(mag…`；"magic" gadget 写入需要 add_rbx_rdx_ret 累加；SROP 适合 NX 开启且 libc 未知场景

### XCTF（1 篇）
- qemu-system-arm LiteOS 模拟器 migrate exec 注入命令 + ARM shellcode readreg 系统调用 → `io.sendlineafter(''finish'', shellcode.hex()) # ARM shellcode 注入 qemu`；qemu-monitor 的 migrate "exec: cmd" 可作为嵌入式题的非预期入口；LiteOS 系统调用 0x20…

## Connections

- 上层索引：[[ctf/source-corpus--des-ctf-knowledge]]
- Source: [https://github.com/Dest1ny-Sec/Des-CTF-Knowledge](https://github.com/Dest1ny-Sec/Des-CTF-Knowledge)

## Open Questions

- 本页为索引层，题目细节请回源；若某场比赛多题值得展开，再单独建 `ctf/<比赛>.md` 实体页。
