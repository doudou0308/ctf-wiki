---
name: reverse
description: CTF 逆向工程解题全能助手 — 覆盖静态分析（GDB/Ghidra/radare2/IDA/Binary Ninja/dogbolt.org）、动态分析（Frida/angr/lldb/x64dbg/Qiling/Triton）、反分析对抗（ptrace/PEB/CPUID/VM检测/DBI检测/代码完整性/不透明谓词/MBA混淆/控制流平坦化）、二进制模式（自定义VM/自修改代码/XOR加密/SECCOMPBPF）、语言特定（Python字节码/PyArmor/Go/Rust/Swift/Kotlin/Haskell/C++）、平台特定（macOS/iOS/Mach-O/Android/JNI/DEX/Godot/Roblox/Electron/Unity IL2CPP）、硬件（RISC-V/ARM64/固件/游戏引擎/汽车CAN总线）、固件提取/加壳脱壳（UPX/VMProtect/Themida/PyInstaller）等19个子领域。
---

# CTF Reverse Engineering — 逆向工程解题指南

你是 CTF 逆向工程类题目的顶级解题专家。你的知识库覆盖以下所有子领域，按需深入：

## 一、前置准备

```bash
pip install frida-tools angr qiling uncompyle6 capstone lief z3-solver

# Linux
apt install gdb radare2 binutils strace ltrace apktool upx

# macOS
brew install gdb radare2 binutils apktool upx ghidra

# GDB插件 — pwndbg
# Linux: https://github.com/pwndbg/pwndbg

# 补充工具
git clone https://github.com/zrax/pycdc && cd pycdc && cmake . && make  # Python 3.9+ 字节码
gem install one_gadget

# Ghidra — https://ghidra-sre.org
# Binary Ninja — https://binary.ninja
# dogbolt.org — 在线多引擎对比反编译
```

---

## 二、解题总流程

### 第0步：快速获胜（先试最简单的！）

```bash
strings binary | grep -E "flag\{|CTF\{|pico"
strings binary | grep -iE "flag|secret|password"
rabin2 -z binary | grep -i "flag"

ltrace ./binary              # 库调用追踪 — 常直接捕获 flag
strace -f -s 500 ./binary    # 系统调用追踪
xxd binary | grep -i flag    # 十六进制搜索

./binary AAAA                # 快速试运行
echo "test" | ./binary
```

### 第1步：文件识别

```bash
file binary               # 类型、架构
checksec --file=binary    # 安全特征
readelf -h binary         # ELF头
```

### 第2步：根据目标类型深入子领域

- Native Linux/Windows 二进制 → 静态工具 + 动态分析
- 自定义 VM/字节码解释器 → 自定义 VM 逆向模式
- Python/PyArmor/PyInstaller → 语言特定逆向
- Go/Rust/Swift 编译 → 编译语言逆向
- APK/Android → 平台特定
- wasm/.NET/Unity → 对应工具链
- 固件/裸机 → 嵌入式逆向

---

## 三、静态分析工具

详见 [tools.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-reverse\tools.md)

### GDB 基础

```bash
gdb ./binary
start                    # 运行到 main
b *0x401234              # 地址断点
b *main+0xca            # 相对断点（PIE兼容）
c / si / ni              # 继续/步进/步过
x/s $rsi                 # 查看 RSI 指向的字符串
x/20x $rsp               # 查看栈
info registers           # 寄存器
set $eax=0               # 修改寄存器值
```

### PIE 二进制调试

```bash
gdb ./binary
start                    # 强制解析 PIE 基址
b *main+0xca             # 相对 main 的偏移断点
run
```

### 一行自动化

```bash
gdb -ex 'start' -ex 'b *main+0x198' -ex 'run' ./binary
```

### 内存转储策略（关键技巧）

**核心洞察：** 让程序计算出答案，再转储它。在最终比较 `strcmp` 处设断点，输入正确长度的任意值，然后 `x/s $rsi` 转储计算出的 flag。

**诱饵 flag 检测：** 注意按顺序的多个比较目标和不同的成功消息。断点应设在**最终**比较处，不要在前面就被骗了。

### 比较方向（关键！）

两种模式：
1. `transform(flag) == stored_target` → 逆向变换算法
2. `transform(stored_target) == flag` → flag 就是变换后的数据，直接对 stored_target 应用变换即可

### Radare2

```bash
r2 -d ./binary     # 调试模式
aaa                # 分析
afl                # 列出函数
pdf @ main         # 反汇编 main
s <addr>           # 跳转到地址
```

### Ghidra (Headless)

```bash
analyzeHeadless project/ tmp -import binary -postScript script.py
```

### dogbolt.org — 多引擎对比反编译

上传二进制，同时比 Hex-Rays/Ghidra/angr/angr 的多种反编译结果，找最容易理解的版本。

### Binary Ninja Patching

```python
import binaryninja
bv = binaryninja.BinaryViewType["ELF"].open("binary")
bv.write(addr, bytes_to_patch)
bv.save("patched")
```

---

## 四、动态分析工具

详见 [tools-dynamic.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-reverse\tools-dynamic.md)

### Frida — 动态插桩

**安装：** `pip install frida-tools frida`

```javascript
// hook strcmp — 捕获所有比较
Interceptor.attach(Module.findExportByName(null, "strcmp"), {
    onEnter: function(args) {
        this.arg0 = Memory.readUtf8String(args[0]);
        this.arg1 = Memory.readUtf8String(args[1]);
        console.log(`strcmp("${this.arg0}", "${this.arg1}")`);
    },
    onLeave: function(retval) {
        console.log(`  → ${retval}`);
    }
});
```

```bash
frida -p $(pidof binary) -l hook.js     # 附加运行进程
frida -f ./binary -l hook.js --no-pause # 启动并插桩
```

**反调试绕过：**
```javascript
// ptrace(PTRACE_TRACEME) → 返回 0
Interceptor.attach(Module.findExportByName(null, "ptrace"), {
    onLeave(retval) { if (this.request === 0) retval.replace(ptr(0)); }
});
// 时间检查绕过 — clock_gettime 返回常数
// 内存扫描 — Process.enumerateRanges + Memory.scan("flag{")
```

### angr — 符号执行

基本流程：输入符号化 → 探索路径 → 添加约束 → 求解 flag。

```python
import angr
proj = angr.Project('./binary')
state = proj.factory.entry_state()
simgr = proj.factory.simulation_manager(state)
simgr.explore(find=0x401234, avoid=0x401000)  # find=成功, avoid=失败
if simgr.found:
    print(simgr.found[0].posix.dumps(0))       # 打印求解的输入
```

**处理路径爆炸：**
- Hook 复杂函数替代为简单实现
- 从特定地址开始执行（而非 entry）
- 使用 CFG 约束引导探索

### Qiling Framework — 跨平台模拟

模拟非本机架构的二进制，绕过重度反调试，无需硬件。

### Triton DSE — 动态符号执行

从具体执行轨迹中提取约束 → 符号求解。适用于 `movfuscator`、MBA 混淆等。

---

## 五、反分析对抗与绕过

详见 [anti-analysis.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-reverse\anti-analysis.md)

### Linux 反调试速查

| 检测方式 | 方法 | 绕过 |
|----------|------|------|
| `ptrace(PTRACE_TRACEME)` | 自跟踪被调试器占用 | GDB: `set $rax=0`；patch: `xor eax, eax; ret` |
| `/proc/self/status` TracerPid | 检查 TracerPid != 0 | LD_PRELOAD hook |
| 时间检查 `clock_gettime` | 单步执行变慢 | Hook 返回常数 |
| `SIGTRAP` 自设信号处理 | 无调试器时正常执行 | `handle SIGTRAP nostop noprint` |
| 直接 syscall (`syscall` 指令) | 绕过 glibc hook | 在 syscall 处设断点，`set $rax=0` |

### LD_PRELOAD Hook 模板

```c
// hook.c — 编译: gcc -shared -fPIC hook.c -o hook.so -ldl
#define _GNU_SOURCE
#include <dlfcn.h>
#include <sys/ptrace.h>
long ptrace(int request, ...) {
    if (request == 0) return 0;  // PTRACE_TRACEME 返回成功
    typeof(ptrace) *real = dlsym(RTLD_NEXT, "ptrace");
    return real(request);
}
```
```bash
LD_PRELOAD=./hook.so ./binary
```

### Windows 反调试速查

| 检测方式 | 绕过 |
|----------|------|
| `IsDebuggerPresent()` (PEB.BeingDebugged) | Frida: `retval.replace(0)` |
| `NtQueryInformationProcess(ProcessDebugPort)` | Patch 返回值 |
| `CheckRemoteDebuggerPresent()` | Frida hook |
| TLS Callbacks（入口前执行） | 在 TLS callback 内设断点 |
| 硬件断点检测（检查 DR0-DR3） | 避免硬件断点，用软件断点 |
| INT3 扫描（检查 0xCC 字节） | 不在代码中设软断点，用 `hbreak` |

### 反 VM/沙箱检测

| 检测 | Bypass |
|------|--------|
| CPUID hypervisor bit (ECX bit 31) | GDB: `set $ecx = $ecx & ~(1<<31)` |
| MAC 前缀 (00:0C:29/00:50:56/08:00:27) | 修改 VM NIC MAC |
| 注册表键 / 文件检查 | 删除对应键/文件 |
| 磁盘 < 60GB / CPU < 2核 | 扩展资源或 patch 比较 |

### 反调试绕过通用策略

1. **Patch 法**：`JNZ→JZ`、`JLE→JGE`、改立即数
2. **LD_PRELOAD**：hook `ptrace`、`getuid` 等
3. **GDB 绕过**：`set $rax=0` 跳过检查；`catch syscall ptrace`
4. **Frida**：比 GDB 更隐蔽，适用于重度反调试
5. **Qiling 模拟**：完全不触发反检测代码

### 反汇编对抗技术

- **不透明谓词**：始终为 true/false 但反编译器无法推断的条件 → 识别死代码段
- **垃圾字节/重叠指令**：`jmp into middle` → 反编译器产生错误 `db`/`??` → 手动修正
- **控制流平坦化**：switch-case 分发器 → D-810(Ghidra)/Miasm 解混淆
- **MBA (Mixed Boolean-Arithmetic)**：复杂布尔代数混淆 → 用 MBA 简化器或符号执行

---

## 六、二进制模式与技巧

详见 [patterns.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-reverse\patterns.md)、[patterns-runtime.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-reverse\patterns-runtime.md)

### 常见加密模式

| 模式 | 识别特征 | 逆向方法 |
|------|----------|----------|
| 单字节 XOR | 简单的 `xor al, key` | 暴力 256 个值 |
| 已知明文 XOR | 已知 `flag{` 前缀 | `key = ct[0] ^ ord('f')` |
| 位置 XOR (`^ i` 或 `^ (i & 0xff)`) | XOR 操作数是循环变量 | 直接逆向循环 |
| RC4 | 顺序 S-Box 初始化 0..255 | 提取硬编码 key，还原 RC4 |
| 自定义排列 + XOR | 两次变换 | 先逆向 XOR，再逆排列 |
| XOR + 滚动 key | 多层 XOR，key 来自前一步 | 逐层逆向 |
| 已知明文反推 (XOR + 循环 key) | XOR 文件头魔数 (PNG/PDF) 与加密数据 | 推出循环 key |

### 自定义 VM 逆向

**识别：** 二进制打包了字节码 blob + 分发器循环

**分析步骤：**
1. 找到 opcode switch table
2. 写反汇编器解析字节码
3. 理解算法后再求解

**常见 VM 操作码模式：**
```c
case 1: *R[op1] *= op2; break;   // MUL
case 2: *R[op1] -= op2; break;   // SUB
case 3: *R[op1] = ~*R[op1]; break; // NOT
case 4: *R[op1] ^= mem[op2]; break; // XOR
case 7: if (R0) IP += op1; break; // JNZ
case 8: putc(R0); break;          // PRINT
case 10: R0 = getc(); break;      // INPUT
```

**状态机 VM (90K+ 状态):** BFS 遍历所有状态转移路径，找有效 flag 路径。

### 自修改代码

```python
# 在 GDB 中：执行完 XOR 解密循环后的代码 → dump 内存
dump memory decrypted.bin start_addr end_addr
# 然后用 Ghidra 分析解密后的代码
```

### LLVM 混淆 (OLLVM)

控制流平坦化：所有基本块被重排到 switch 分发器。使用 D-810 IDA plugin 或 Miasm 解混淆。

### SECCOMP/BPF 过滤器分析

```bash
seccomp-tools dump ./binary
```

### 时间侧信道攻击

验证时间随正确字符增加而变化 → 逐字符爆破：

```python
import time
flag = ""
for pos in range(flag_length):
    best_char, best_time = '', 0
    for c in string.printable:
        t = time.perf_counter()
        io.sendline((flag + c).ljust(flag_length, 'A'))
        io.recv()
        elapsed = time.perf_counter() - t
        if elapsed > best_time:
            best_time, best_char = elapsed, c
    flag += best_char
```

### 多阶段 Shellcode

```bash
# GDB 调试: 在 call rax 断点 → stepi 进入 → 绕过 ptrace → 步过 XOR 解码循环
# 逐阶段重复直到最终 payload
# 最终阶段往往用 mov 指令加载 flag 4字节一组:
values = [0x6174654d, 0x7b465443, ...]  # 小端
flag = b''.join(v.to_bytes(4, 'little') for v in values)
```

---

## 七、语言特定逆向

详见 [languages.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-reverse\languages.md)、[languages-compiled.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-reverse\languages-compiled.md)

### Python 字节码

从 `dis.dis` 输出逆向：
- `LOAD_CONST` + `COMPARE_OP` → 目标值
- `BINARY_XOR` → 变换算法
- `BUILD_TUPLE`/`BUILD_LIST` → 预期输出数组

```python
# 经典模式: 偶数位 XOR key1 = p1, 奇数位 XOR key2 = p2
flag = [''] * length
for i in range(len(p1)):
    flag[2*i] = chr(p1[i] ^ key1)
    flag[2*i+1] = chr(p2[i] ^ key2)
```

### Python Opcode 重映射

PyInstaller 中的 `opcode.pyc` 被修改 → 与原始 Python opcode diff → 构建映射 → patch .pyc → 正常反编译。

**Python 版本选择：** `uncompyle6` 支持 2.x-3.8；3.9+ 用 `pycdc`。

### PyArmor 静态解包

```bash
python /path/to/oneshot/shot.py /path/to/scripts
```

先检查签名：payload 以 `PY` + 六位数字开头。

### Go 二进制

```bash
# 识别
file binary | grep -i "go"
strings binary | grep "go.buildid"

# 符号恢复
./GoReSym -d binary > symbols.json  # https://github.com/mandiant/GoReSym

# Ghidra + golang-loader plugin
```

**Go 特征：**
- 非常大的静态二进制（"hello world" ≈ 2MB）
- `runtime.*` 符号（即使 stripped）
- `main.main` 作为入口
- 检测 goroutine 和 channel 操作模式

### Rust 二进制

**识别：** `rustc`、`rust_panic`、`str::` 字符串，特有的错误信息格式

**Demangle：** Ghidra/IDA 的 Rust demangler plugin

**关键模式：** `Option<T>` = tag (0/1) + data；`Result<T,E>` = tag (0/1) + union；`Vec<T>` = ptr + len + cap

### C++ 二进制

- **vtable 重建：** `.rodata` 中的函数指针数组 → 类层次推断
- **RTTI：** `type_info` 结构揭示类名（stripped 时极其有用）
- **STL 模式：** `std::string` = ptr + size + capacity（SSO: 小字符串内联存储）

### WASM

```bash
wasm2wat binary.wasm -o out.wat       # 转为文本格式
wasm-decompile binary.wasm -o out.dcmp  # 反编译为类C
```

### Brainfuck / Esolangs

- **BF 逐字符静态分析：** 直接用 Python 写解释器，对每个输入符号模拟后检查内存状态
- **BF 边信道：** 观察 `read()` 调用次数泄漏输入位置
- **BF 比较惯用语：** 识别 `[-]`（清零）和循环结构来推断比较语义

---

## 八、平台与框架特定

详见 [languages-platforms.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-reverse\languages-platforms.md)、[platforms.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-reverse\platforms.md)

### Android APK

```bash
apktool d app.apk                     # 反编译
jadx app.apk                          # Java源码
# 关键位置: classes.dex, lib/armeabi-v7a/lib*.so, assets/
```

**JNI RegisterNatives 混淆：** native 方法在 `JNI_OnLoad` 中通过 `RegisterNatives` 动态注册 → 找 `gMethods` 数组映射 Java 方法名到 native 函数。

**DEX 运行时 patch：** 写 `/proc/self/maps` 修改内存中的 DEX 字节码绕过检查。

### Flutter APK

用 `blutter` 提取 Dart AOT snapshot → 分析生成的 pseudo-code。

### Unity IL2CPP

```bash
Il2CppDumper -- 从 global-metadata.dat 恢复方法名/类结构
# 然后分析 libil2cpp.so 中的 C++ 代码
```

### Roblox Place File

`.rbxlbin` 格式含 INST/PROP/PRNT 块 → parse 提取脚本 → 检查版本历史（旧版本可能含真 flag，最新版本是诱饵）。

### Godot Game

`gdsdecomp` 提取 `.pck` + `KeyDot` 提取加密 key → 导入 Godot 编辑器查看源码。

### Electron App

```bash
asar extract app.asar ./unpacked
# 检查 native .node 模块 → 标准二进制逆向
```

### macOS / iOS

**Mach-O 分析：**
```bash
otool -l binary            # Load commands
lipo -info fat_binary       # 查看架构
lipo -thin arm64 -output thin  # 提取单架构
codesign -dvvv binary       # 签名信息
codesign --remove-signature binary  # 去签名（for patching）
```

**Objective-C Runtime：** `__objc_methname` section → 所有方法名明文。（即使 stripped）

### Serde JSON Schema 恢复 (Rust)

反汇编 serde 的 `Visitor` 实现 → `visit_str`=string, `visit_u64`=number, `visit_bool`=boolean, `visit_seq`=array → 重建预期 JSON 结构。

---

## 九、加壳与高级保护绕过

详见 [tools-advanced.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-reverse\tools-advanced.md)

### UPX 脱壳

```bash
upx -d packed_binary -o unpacked
# 手工: 找 popad/popfd → jmp 到 OEP → dump 进程内存
```

### PyInstaller 解包

```bash
python pyinstxtractor.py malware.exe
# 找 main .pyc → 修复 pyc header → uncompyle6 反编译
```

### VMProtect

**识别：** `.vmp0`/`.vmp1` section、pushad 式序幕、大型 switch-case 分发器

**CTF 策略：** 通常不需要完整脱虚拟化 → 动态追踪特定操作（加密/比较），记录 opcode + 操作数

### Themida / WinLicense

**CTF 策略：** dump 内存中的解密后代码 → 静态分析 dump。

### 二进制 Diffing (BinDiff / Diaphora)

比较两个版本的二进制（如打了补丁前后），快速定位修改的代码。

### 去混淆框架

| 工具 | 平台 | 用途 |
|------|------|------|
| D-810 | IDA | 自动简化不透明谓词/垃圾代码 |
| GOOMBA | Ghidra | MBA/控制流平坦化解混淆 |
| Miasm | Python | 符号执行解混淆 |
| RetDec | 独立 | 跨架构反编译（支持 LLVM IR 输出） |

### 自定义 VM 字节码提升到 LLVM IR

VM handler 实现 → 用 LLVM IR 构建等价 IR → 优化 → 还原为高级逻辑结构。

---

## 十、模拟与高级工具链

详见 [tools-emulation.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-reverse\tools-emulation.md)

### Unicorn Engine 模拟

```python
from unicorn import *
mu = Uc(UC_ARCH_X86, UC_MODE_64)
mu.mem_map(BASE, 0x1000)
mu.mem_write(BASE, shellcode)
mu.emu_start(BASE, BASE + len(shellcode))
```

### LD_PRELOAD 时间冻结

```c
time_t time(time_t *t) {
    time_t fixed = 1234567890;  // 固定时间戳
    if (t) *t = fixed;
    return fixed;
}
```
用于确定性分析——消除随机种子差异。

### GDB 高级技巧

- **反向调试 (rr)：** 录制运行时→逆向执行→无限次回放
- **条件断点：** `b *addr if $rax==0xDEAD`
- **Python 脚本：** `gdb -x script.py` 自动化分析
- **零标志监控：** PEDA `current_inst` 逐字节 scrap flag

### r2frida — Radare2 + Frida 融合

```bash
r2 frida://binary   # 在 radare2 界面中用 Frida 功能
```

---

## 十一、嵌入式/固件/硬件逆向

详见 [platforms.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-reverse\platforms.md)、[platforms-hardware.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-reverse\platforms-hardware.md)

### 固件提取

```bash
binwalk -e firmware.bin               # 自动提取
binwalk -Me firmware.bin              # 递归提取
strings firmware.bin | grep -i flag   # 字符串搜索
```

### 常用架构

- **ARM32/ARM64：** 注意 Thumb 模式 (LSB=1)、AAPCS 调用约定 (X0-X7 传参)、qemu-aarch64-static 模拟
- **MIPS：** 延迟槽注意
- **RISC-V：** 自定义扩展常见 → 反汇编可能需要调整

### 内核模块 (.ko)

```bash
modinfo module.ko     # 元数据
objdump -d module.ko  # 反汇编
```

### eBPF 程序

```bash
llvm-objdump -d ebpf_prog.o  # 反汇编 eBPF 字节码
```

### 游戏引擎

- **Unreal Engine：** `.pak` 文件 → `unrealpak` 提取
- **Unity：** `Il2CppDumper` + `Assembly-CSharp.dll` 分析
- **Lua 脚本游戏：** 找 `luaL_loadbuffer` 调用 → dump 字节码 → `luadec` 反编译

### Verilog/HDL 逆向

将 Verilog 逻辑转换为 Python，逐信号追踪。

### CAN Bus 汽车逆向

分析 `.dbc` 文件或 CAN 日志中的消息 ID+数据 → 反向工程信号编码。

---

## 十二、高级 CTF 特定模式

详见 [patterns-ctf.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-reverse\patterns-ctf.md)、[patterns-ctf-2.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-reverse\patterns-ctf-2.md)、[patterns-ctf-3.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-reverse\patterns-ctf-3.md)、[anti-analysis-ctf.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-reverse\anti-analysis-ctf.md)

### 速查表

| 模式 | 方法 |
|------|------|
| 隐藏模拟器操作码 | 找未记录的 switch case → 分析其行为 |
| LD_PRELOAD 密钥提取 | Hook `read`/`write` → 记录密钥流 |
| Z3 单行 Python 电路 | 将布尔/算术约束转为 Z3 求解 |
| Sliding window popcount | 窗口宽度为 N 时的比特计数 → 识别滑动特征 |
| C++ 析构函数隐藏验证 | 对象析构时触发检查 → 在 `~ClassName()` 设断点 |
| 键盘 LED Morse 码 | ioctl 控制 LED → CAPS LOCK/NUM LOCK 闪烁编码 |
| 固定点数学收敛位图 | 收敛标志位映射为可视化输出 |
| Burrows-Wheeler 逆变换 | 识别排序+重建循环，标准逆 BWT 算法 |
| GLSL Shader VM | GPU shader 中的自修改代码 → 提取 SPIR-V 分析 |
| 指令计数器加密 | 指令数量 = 加密状态 → 执行计数泄露信息 |
| ROPfuscation 分析 | ROP gadget 链实现代码逻辑 → 反汇编 gadget 链重建控制流 |
| TensorFlow DNN 反演 | sigmoid 层数学反演 → `log(y/(1-y))` 恢复输入 |
| 批量 crackme 自动化 | `objdump` 提取模式 → 批处理多个简单 crackme |
| fork + pipe + dead branch | 子进程死分支反分析 → 利用 fork 的 COW 隔离 |
| GF(2^8) 高斯消元 | 识别 XOR + shift 操作 = GF(2^n) 算术 → 消元求解 |

---

## 十三、关键决策：何时切换 Skill

| 题目特征 | 应切换至 |
|----------|----------|
| 已理解二进制，需 heap/ROP/内核利用 | `/ctf-pwn` |
| 主要是恢复删除文件/PCAP/磁盘 artifact | `/ctf-forensics` |
| Web应用客户端脚本辅助分析 | `/ctf-web` |
| 实现 ML 模型，重点是模型攻击 | `/ctf-ai-ml` |
| 核心逻辑是密码算法/数学问题 | `/ctf-crypto` |
| 真实恶意样本含 C2/加壳/逃逸行为 | `/ctf-malware` |
| 玩具 VM/编码谜题/pyjail 而非真实二进制 | `/ctf-misc` |

---

## 十四、自动化工作流

1. **轻量级尝试** → `strings` / `ltrace` / `strace` / Frida hook strcmp/memcmp（先搞最快赢的）
2. **文件识别** → `file` / `checksec` / `readelf` → 确定目标和架构
3. **静态分析** → Ghidra/dogbolt.org 反编译 → 理解算法/控制流
4. **动态分析** → GDB 设关键断点；Frida hook 关键函数；angr 符号执行
5. **语言/平台识别** → Go/Rust/Swift → demangle；Python → dis；APK → jadx+JNI
6. **反分析绕过** → 识别障碍 → patch / LD_PRELOAD / Frida / Qiling
7. **查阅参考** → 遇到具体模式时打开对应 `.md` 参考文件

> **逆向工程核心原则：永远先来最简单的。90%的 CTF flag 不在复杂的混淆器后面——它们在 `strings` 输出里、在 `ltrace` 调用里、在 `strcmp` 的参数里。先做了快速获胜，再投入深度分析。**
