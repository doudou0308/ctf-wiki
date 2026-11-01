---
title: Des-CTF 语料索引 — Reverse（2023-2024）（76 篇比赛 WP）
tags: [source-index, corpus, des-ctf, wp, reverse]
category: reverse
triggers: ["rc4", "Android", "JNI", "frida hook", "rev", "custom-vm", "base64", "inline-hook", "dnSpy", "Cython", "WebAssembly", "unidbg", "idea", "pyd", "frida", "Android APK", "stack-vm", "ArkTS", "pycdc", "base58"]
created: 2026-11-01
updated: 2026-11-01
sources: [https://github.com/Dest1ny-Sec/Des-CTF-Knowledge]
---

# Des-CTF 语料索引 — Reverse（2023-2024）

## Summary

外部语料库 `Dest1ny-Sec/Des-CTF-Knowledge` 中 **76 篇 Reverse（2023-2024） 比赛 WP**（覆盖 60 场比赛）的结构化索引。
每条记录保留「比赛 → 题目/攻击链 → 关键 payload → 要义」，原始全文未镜像，需细节时按 `contest` 回源 GitHub 的 `CTF大赛WP集合/articles/<文件名>.full.md`。

## Key Points

- 高频考点（按 tags 词频）：rc4、Android、JNI、frida hook、rev、custom-vm、base64、inline-hook、dnSpy、Cython、WebAssembly、unidbg、idea、pyd、frida
- 高频比赛：看雪2023 KCTF年度赛、KCTF大赛、羊城杯、CTF Reverse、公众号文章、D3CTF、DASCTF、强网杯2024
- tags 来自上游 frontmatter（允许跨赛道噪声，如 Web 页出现 ret2libc），仅作检索线索
- 用法：Grep 本页的比赛名或考点词 → 命中条目 → 需要细节时回源原文；不要在本页上做二次推理

## 按比赛分组

### 2023 37C3 (Chaos Communication Congress)（1 篇）
- 37C3 2023 Pot of Gold: 用 Binary Ninja IL 逆向 unicorn_blob 格式 + 自定义 4 字节指令 ISA 虚拟机。 → `/`；/

### 2023 ACTF（1 篇）
- ACTF Flutter 逆向学习 — blutter 解析 DartVM 快照 + IDA 符号还原 + Frida hook 按钮回调 + 真机 root 动态调试。；Flutter 逆向核心是 DartVM 快照 (libapp.so);blutter 是开源首选工具;高 SDK 版本需降 ta…

### 2023 D3CTF（2 篇）
- D3CTF 2023 四道逆向 WP 合集，NAND-VM / fork-pipe 套娃 / pyd Bindiff / syscall 劫持 VM。 → `RC4 加密 opcode + NAND VM + fork/pipe 子进程 + syscall hook`；当 IDA/Ghidra 看到 "mov byte 一坨"、自定义 syscall、fork/pipe 套娃时，要立刻反应过来这是…
- D^3CTF 2024 逆向 2 道：XTEA 变体解密 + IDA Python 递归函数图。 → `XTEA 变体 + IDA 递归脚本`；XTEA 变体只需调整 delta 和轮数；IDA Python recur() 适合还原复杂调用图。

### 2023 DASCTF（2 篇）
- DASCTF 2023 7 月逆向 3 题：控制流平坦化/矩阵求逆解密/128 位 RSA+TEA 自定义 VM。 → `控制流平坦化还原 + 矩阵求逆 + RSA+TEA`；控制流平坦化的逆向要点是把"被打散的 state 序列"用 Python 数组 ops 重新串起来执行。
- DASCTF 2024 BabyAndroid：Android JNI + RC4 + InMemoryDexClassLoader + AES/ECB + DCT 浮点加密。 → `RC4 解 dex + 自定义 hash 派生 AES + IDCT 浮点变换`；Android 逆向链：assets RC4 → InMemoryDexClassLoader → 反射调用 → 自定义 hash…

### 2023 DASCTF六月赛·二进制专项（1 篇）
- DASCTF六月赛二进制专项5题官方WP:Go RSA/pycdc XXTEA/线程注入RC4/gethostbyname内联HOOK/截屏加密。 → `Finger插件 + e=0x10001 + Yafu分解69位整数 + pycdc + XXTEA + RC4 + DNS内联HOOK`；Go逆向用Finger插件恢复关键函数;e=0x10001直接推测RSA;Yafu分解N得p1*p2=79619273727856…

### 2023 Nep CTF（1 篇）
- Nep CTF password: Android JNI verify + 自定义 base64 字符表 (小写字母+数字+大写字母+符号) + RC4 加密, v30 数组反推算法。 → `/`；/

### 2023 NepnepxCATCTF（1 篇）
- NepnepxCATCTF CatPaw: Android 小米备份 + Frida hook 绕 login + /dev/input/event1 触摸事件还原画图。

### SCTF 2023（1 篇）
- Syclang 自定义 IR 逆向：4 轮差分/加减/累加/索引置乱混合加密。；自定义 IR 逆向关键是识别循环结构 + 操作类型，差分加密逆向就是反向累加。

### TPCTF 2023（1 篇）
- TPCTF 2023 nanoPyEnc AES-ECB + maze Cython 编译产物 IDA 符号重建。；Python 字节码逆向先识别编译方式 (CPython .pyc, Cython, PyInstaller, Nuitka)，C…

### 2023 UIUCTF（1 篇）
- UIUCTF 2023 vmwhere 2 题：自定义字节码 VM + REVERSE TOP N 反转 + 24/47 字节 key 链式 XOR。 → `(a>>4) ^ a # flag 字符链式 XOR`；自研 VM 逆向时先用 Python 写 interpreter 复现 opcode，再分析 flag 校验逻辑。

### 2023 ad2001 Frida 系列（1 篇）
- Frida Hook Android：get_random+check函数hook+Caesar-21解flag → `AMDYV{WVWT_CJJF_0s1} # Caesar-21后flag`；Frida可用Java.use+"implementation"覆盖Java方法

### 2023 ナゾトキ（1 篇）
- ナゾトキ x CTF 虹色の研究（日语 CTF 入门题），MineNumber=777 触发 flag 字符串输出。 → `local_64 == 0x309 name ASCII sum = 777 flag.txt`；日语 CTF 入场题；ASCII 字符求和触发 flag 是入门级；strings + grep 找 flag 字符串；反编译看比…

### 南极动物厂 2023（1 篇）
- 内核检测 ReadProcessMemory Length：置 MmTrackPtes+MiTrackPtesAborted 遍历 PTE_TRACKER 链表查 0x2023。；tvm 保护下抓导入表+黑盒分析；WRK 反推内核结构；PTE_TRACKER.Length 是 ReadProcessMemor…

### 安询杯 2023（1 篇）
- 32 字节 ROT13 + 查表 XOR/ADD + 半字节反转 三层加密逆向。；半字节反转 dec3(v) = ((v>>0)&1)<<7 + ... 是经典 8-bit reverse；查表操作要反向回推。

### 2023 攻防世界 (xctf)（1 篇）
- 自定义 base64 字母表 + JNI 字符串置换还原 → `str1.translate(str.maketrans(string1, string2)) → base64.b64decode`；native 层 base64 经常换字母表防识别；JNI_OnLoad 里 StringUTFChars 读入后做字符表 swa…

### 看雪 KCTF 2023（1 篇）
- 韦达定理逆推 + 多种解法的 reverse 入门题 → `56101-61637`；两个未知数 + 两组和积约束 → 一元二次方程根；z3 / factordb / 韦达三选一即可

### 看雪2023 KCTF年度赛（6 篇）
- KCTF2023年度赛Q8《AI核心地带》AI出题+JS注册码生成+CrackMe signed char BUG修复 → `odd+k&1判断奇偶;d(i)=(0xFFFEC610>>i%0x1f)%0xA;e=[1,0,1,0,1,0,0,0,1]位图;JS算法生成582606981190746395…`；出题人故意留signed char BUG：S8左移保号使if多次成立，应用S8显式转换
- KCTF2023年度赛Q7《智能联盟计划》x86汇编混淆+keystone+capstone去混淆脚本 → `keystone.Ks(KS_ARCH_X86, KS_MODE_32);capstone.Cs(CS_ARCH_X86, CS_MODE_32);ob_start=0x403AC…`；大量x86混淆用keystone/capstone做汇编/反汇编引擎+Python遍历提取
- KCTF2023年度赛Q3《秘密计划》SOUI界面+4个定时器反调试+ARM代码明文SHA256验证 → `simulation::vftable偏移2a8;CloseHandle((HANDLE)0x99999999);4个CreateTimerQueueTimer;jz→jmp绕过反…`；SOUI界面逆向先提取dlg_main.xml定位vftable；定时器反调试patch jz→jmp绕过
- KCTF2023年度赛Q5《争分夺秒》Windows CrackMe：模式识别nop混淆+逆元模逆+双幻方对称加密 → `inside_dbg 0x402220调试器检测;nop混淆;a*x%n=1;0x7D45*x%0x346F8717=1逆元;0xD711*x%0x729969FF=1;key格式…`；计算混淆代码用模式识别特征+静态nop；逆元小整数方程a*x mod n=1直接枚举
- KCTF2023年度赛Q6《至暗时刻》数独+3层反调试+syscall系统调用断点查参数 → `TLS回调反调试;IsDebuggerPresent跳转patch;NtQueryInformationProcess 0x7;CheckRemoteDebuggerPresent…`；TLS回调+CheckRemoteDebuggerPresent多层反调试；syscall下断点查寄存器参数
- KCTF2023年度赛Q2《CN星际基地》39球称量问题+测量矩阵+IsDebuggerPresent反调试 → `39球编号1-39;测量矩阵(0不选/1右侧/-1左侧);称量4次得向量;srand(time(0))确定随机;IsDebuggerPresent取反patch`；称重问题用测量矩阵(0/-1/1)表示；每次测量结果-1/0/1；最少4次测量得解

### 2023 研究生国赛华为杯（1 篇）
- call $+5 混淆去 NOP + ChaCha 复用动调 + RC4+异或链 + Golang memequal + Unity Assembly-CSharp 日文假名密码。 → `xor_array = [0xFF, 0x24, 0x3F, 0xDA, 0xBE, 0xA9, 0xB6, 0xF7, 0x12, 0x8F, 0x29, 0xD0, ...]…`

### 2023 腾讯游戏安全（1 篇）
- 腾讯游戏安全初赛：CE 找 base64 表 + 找明文 + DLL 注入 PATCH 内存让 Base64 密文改明文，NOP 关键指令 + 修复 inline hook。；Windows inline hook 修复是高级游戏安全技术；DLL 注入 + WriteProcessMemory 是游戏外挂…

### 2023 腾讯游戏安全 PC 决赛（1 篇）
- Detours inline hook + TLS 计数 + CreateRemoteThread 远程注入的游戏反外挂守护。 → `jmpBytes = { 0xE9, 0xE0, 0x26, 0x16, 0x00 } ; originBytes = { 0x4C, 0x8B, 0xD1, 0xB8, 0x50…`；Windows Hook 反外挂关键在 ntdll 自身也被 hook 时需要 unhook 后再注入，特征码恢复靠写死 24 字…

### 腾讯游戏安全技术竞赛 2023 安卓客户端初赛（1 篇）
- 腾讯游戏安全技术竞赛2023安卓客户端初赛题解,frida源码bypass+IL2CPPDumper恢复符号+patch金币自增+四层加密(XTEA+VM+混淆去除+BlackObfusca… → `XTEA key + BlackObfuscator dex + VM decompile`；安卓游戏安全三大挑战:frida bypass(改re.frida_server字符串)+il2cpp符号恢复(IL2CPPDum…

### 2023 贵阳大数据（1 篇）
- EZRE_0 Reverse：错位异或 + TLS 回调 RC4(JT8U9ptt) + 自定义变换（参数 0x65/0x64 切换加解密）+ 5 阶段。；TLS 回调函数是 RE 隐蔽执行点；自定义算法通过第 5 参数切换加解密是好出题思路。

### 2024 春秋杯网络安全联赛冬季赛（1 篇）
- 春秋杯冬季赛 RE 套题：custom_md5_init 拷自身 1024 字节做 MD5 + srand seed + rand()%127 异或，42 字节异或直出 flag；sub_4… → `print(chr(xor[i] ^ cmp[i]), end='') for i in range(42)`；逆向题看到 'qmemcpy(v3, custom_md5_init, 0x400)' + MD5_Update 基本就是自实现…

### 2024 极客少年（1 篇）
- 极客少年 Reverse 两题：little re sbox 320 字节逆查 + BeautifulFlower XOR i + 花指令动态调试 — 都是逆向入门级查表/异或。 → `l.append(chr(sbox.index(v5[i]) ^ i))`；逆向入门套路 = sbox 逆查 + 异或 i + 动态调试绕花指令；CTF 入门题常 30 分钟内出，2024 极客少年属于"招…

### 2024 第四届网鼎杯（1 篇）
- 第四届网鼎杯朱雀组 Reverse 三题：SMC XOR 0x42 + base24 自定义码表解码 + VMP 壳 svchost.exe 字符串大法。 → `PatchByte(start+i, (Byte(start+i)+0x42)&0xff)`；SMC 自解密常见手法是 idapython PatchByte 跑一遍；base24/36 等自定义码表要把密文每个字符先查表得…

### 2024 西湖论剑（1 篇）
- Qrohttre 主进程用 Win32 DEBUG_PROCESS 调试子进程，TF 单步 + 双段 SMC 自解密 + 0xCC 断点同步用户输入；patch WriteProcessMe… → `CreateProcessA(..., DEBUG_PROCESS, ...); f_OnSingleStep_407530`；Win32 调试 API（DEBUG_PROCESS + TF 单步 + 0xCC 断点）实现父子进程同步通信是 Windows…

### 2024 长城杯初赛（1 篇）
- 长城杯 easyre：43 字节 input 自异或链 + SSE _mm_cmovemask_epi8 == 0xFFFF 校验 32 字节，爆破 1 字符逆推。 → `input[i] ^= input[i+1-42*(i+1/42)]`；SSE 比较 128-bit 一次比较 16 字节，全 0xFFFF = 全部相等；input 自异或链 = 已知密文 + 知道异…

### 2024 Android Crackme（1 篇）
- 自毁机制 Android crackme，3 种解法 (AndroidNativeEmu 模拟 / Frida hook / unidbg) 都不需要碰真实设备。

### 2024 Blue Water CTF（1 篇）
- Blue Water CTF 2024 re OORM — 800 个 funcs 链 20-bit 输入 + 模拟执行 Unicorn + DFS 剪枝爆破 (32766 阈值) + 末位…；模拟执行 + 剪枝是 OORM (Obscure Obfuscated Reversing Machine) 标准解法;Unico…

### 2024 ByteCTF（1 篇）
- ByteCTF babyapk 逆向 — Flutter APK blutter 解析 + IDA 找 m3N4B5V6 + Frida hook app.so + z3 解 4 轮 8 变…；Flutter APK 逆向核心是 blutter 解析 libapp.so;Rust FFI 加密函数 IDA 找 cross-…

### 2024 CISCN x 长城杯（1 篇）
- 2 字节密钥爆破 + CRC32 反推 + KeyList XOR。 → `uint8_t KeyList[] = {82,225,68,226,57,225,94,155,81,220,25,152,80,146,57,193,80,158,82,130…`；短密钥 + 标准 CRC32 函数 + 已知 48 字节密文是爆破黄金组合。

### 2024 CTF Reverse（2 篇）
- CTF VM 基础 — VM 字节码 0x01/0x04/0x05 (add/sub/xor) + IDA get_wide_byte 提取 + z3 BitVec 8 位约束 + deop…；VM 逆向 3 大解法:① IDA 提取 opcode + 解释执行 / ② 倒序遍历反推 (因为加减异或操作可逆) / ③ z3…
- 花指令阅读与分析 — JunkCode 算数拆分 + push/pop/retn 等价解释 + 抵消型 (add-sub/xor-xor) + jmp db 型 (EB 03 跳 55 E8…；花指令分 4 类:① 垃圾代码 ② 等价替换 ③ 抵消型 ④ jmp db 型;IDA LinearSweep 逐行 vs Rec…

### 2024 Dancing Circle（1 篇）
- Dancing Circle把"加密题"藏回数独与DLX,大整数生成数独初盘+DLX精确覆盖求解+5步×60次×4旋转=1200次cover+6次字节查表合成净变换T_net,IDA+Pyt… → `32415867057146038208672345171508423626351804840632517408215763137846025652307148`

### 2024 H&NCTF（1 篇）
- H&NCTF RWhackA：Enigma打包+反VM+反杀软+shellcode注入 → `sub_140001320 解密shellcode → 注入 exp10rer.exe`；Enigma Virtual Box打包需用动态调试+反VM检测绕过

### Hack.lu 2024（1 篇）
- Hack.lu 2024 Getting into Shape：TTF嵌WASM+Rust cipher+Salsa20 → `expand 32-byte k (Salsa20 constant)`；Rust WASM二进制可藏在字体文件TTF中

### 2024 KCTF大赛（5 篇）
- 27 位序列号 + 三段校验 + 四皇后 + 反调试改乘数 + md5 爆破。 → `hellocatx202112050a3b1c4d29 ; md5 = 40d511825ecbc207eb6ef9a7b1c6e34b ; g_serial3_verify =…`；序列号长度必须是 3 倍数 + 第 9 位覆盖隔壁变量的内存布局是 KCTF 老套路。
- .NET 数独 + 迷宫路径拼接。 → `xx xx 02 xx xx 07 xx xx x / xx 06 xx 09 xx xx 04 xx / xx 09 xx 02 05 xx xx xx 03 / ... / 路…`；.NET CrackMe 优先用 dnSpy 反编 main，关键算法用 GPT 提炼效率高。
- shellcode 拼图游戏 + 0 元素轨迹做注册码 + 6 字节花指令去混淆。 → `011110202122 ; 3 字节无效指令 FD EB 1F 3E 1C EB EB / FF EB 15 3E 1D EB FB / FE EB 18 3E 1C EB EB…`；无效指令 NOP 替换 + 0x2A 字节还原是 shellcode 混淆常见两步。
- PyInstaller CrackMe 替换 base64 + XOR 0x55 + swap pair + hook input 偷数据。 → `convertinput(s) = bytes((ord(c) ^ 0x77) + 21 for c in s) ; CrackMe.main(KCTF) = "QQMlP7!!"…`；PyInstaller CrackMe 在 hook 后模块名会被换，看 import sys.modules["CrackMe"…
- 机器码特征 NOP + Capstone 线性扫描 + IDAPython ctree 化简 + push eax 还原。 → `findall "9C FF 35 ?? ?? ?? ?? 8F 05 ?? ?? ?? ?? 9D" ; memset 90 ; Scylla dump PE ; capston…`；KCTF 反混淆标准流程：特征匹配 (第一遍) → CFG 还原 (第二遍) → 全局变量化简 (第三遍) → ctree AST…

### 2024 KGB Messenger CTF App（1 篇）
- Frida闯关KGB Messenger：4关hook俄罗斯判断+base64密码+登录 → `FLAG{57ERL1NG_4RCH3R}`；Frida实战CTF App：System.getProperty/Environment反俄判断

### 2024 N1CTF（1 篇）
- N1CTF 2024 ezapk: Android APK + native1/native2 JNI, Frida hook MainActivity.enc 观察明文/密文对比还原 fl…

### 2024 NKCTF（1 篇）
- NKCTF 2024 re VM?VM! — 2324x2324 Wireworld 元胞自动机 (0x914 像素) + XOR bit 流解码还原 flag。 → `/`；/

### 2024 SHCTF（1 篇）
- SHCTF 2024 鸿蒙 ArkTS 逆向，secretKey + encode 字符编码 (114514*x + 1919810) % 95 + 32 还原 + 迷宫生成。 → `secretKey = "[f#fLw)??Pz?#9w)Du[ks[q[#w4D?4P4UJf,kU[f.rDkfwrDtq...)?J.#rP4[qrPDJkkJ/.9J/qf…`

### SUCTF 2024（1 篇）
- SUCTF 2024 SU_Harmony：鸿蒙HAP包逆向+abc-decompiler+大整数运算链+z3解 → `abc-decompiler;modules.abc;libentry.so;sub_62F0/6D20/8270/9890/A8F0/C160大整数加/减/乘/除2;z3 ToI…`；鸿蒙HAP = Zip结构，逆向modules.abc (ArkTS) + libentry.so (NAPI)；大整数反转+加减…

### 2024 TetCTF（1 篇）
- TetCTF 2024 WebAssembly + check_key：WASM 字节码 Z3 求解 + FNV1a + xorshift128 矩阵求逆。 → `np.linalg.solve(X, B)`；WASM 反编译为 WAT 后用 z3 BitVec 求解简单运算链；含 xorshift128 的 21 元一次方程可用 num…

### 2024 Unidbg Android CTF（1 篇）
- Unidbg 模拟 Android JNI 调用 libj.so 绕过动态分析，无需真机直接拿到 flag。；Unidbg 是 Android RE 的金钥匙：AndroidEmulatorBuilder + setProcessName…

### 2024 corCTF（1 篇）
- corCTF 2024 逆向题，把位运算 VM 反编译为 32 位整型运算后用 GPU 跑 hash 爆破。 → `位运算 VM 模拟 32 位整型 + GPU 加速 hash 爆破`；当反编译工具 IDA/Ghidra 因一坨 mov byte 失败时，先识别"位运算 VM 模拟整型"模式，再反编译为 C/C++…

### 2024 ctfshow 月饼杯2 + 看雪论坛（1 篇）
- 看雪Sh4d0w详解WoW64天堂之门(32位程序调64位函数)技术+ctfshow月饼杯2 EasyTea实战魔改XXTEA。 → `CS=0x33 + 0xEA jmp far ptr + 魔改XXTEA delta=0x77B7EEBB`；WoW64下32位进程用CS=0x33/0x23切换32/64位模式,retf是关键指令;32位IDA无法反编译64位指令,需手动…

### 2024 公众号文章（2 篇）
- 逻辑门电路模拟flag验证，Z3建模为SAT求解 → `r2 -q -c 'aaa; s main; pdf' ./engineTest; Solver().add(...)`；复杂flag验证可降为SAT/Z3约束求解
- PowerPC64 CrackMe，IDEA+RC6双重加密逆向 → `r2 -q -c ''aaa; iz'' ./crackme3 # 找Bingo!和加密数据`；非x86架构二进制逆向需用file/readelf先确认，r2跨平台支持好

### 2024 国赛 babytree (Swift)（1 篇）
- Swift babytree 逆向：key="345y" 4 字节循环移位 + r0/r1/r2/r3 异或 + 42 字节目标反推。；Swift 编译产物可用 swiftc -dump-ast 看 AST 还原源码; 4 字节 key 循环移位加密是经典 patt…

### 2024 大狗Unlock活动（1 篇）
- 大狗Unlock 1-5题全解：APK静态jadx+ROT21/异或+动态Frida hook+云真机 → `ROT21偏移:chr(ord(char)-21)+26补偿;Dagou{Unlok_HOOK_0x1};Checker.code.value=512;frida Java.use…`；大狗云真机平台+Frida hook组合；APK定位用AndroidManifest.xml的LAUNCHER；ROT偏移负数补偿

### 2024 实战逆向 CTF（1 篇）
- 自实现 left/xor 双函数 + 32 轮调度 + dword 数组还原 flag。；IDA 反编译时遇到 '((a2 ^ a1) << 8) - a2' 这种结构要还原原始算术语义；32 轮调度类 VM 用 C 复…

### 2024 山石CTF（1 篇）
- C++ 保留符号无脱 → 看 main 还原 循环右移 + 位交换 加密 → `def swap_bits(byte, n): bit1 = byte & 1; bitN = (byte >> (n-1)) & 1; if bit1 != bitN: byte…`；招新赛题往往不脱符号；C++ 字符变换题常见循环移位+位操作复合；UPX l_info corrupted 时手脱 F8 步过 O…

### 2024 强网杯（1 篇）
- 强网杯 S8 决赛 S1mpleVM：自定义 16 opcode 栈式 VM 编译器，逆向需要还原指令语义。；自定义 VM 逆向关键在数据结构（LinkEntry 链表栈）+ opcode 表 + 操作数格式（1 字节）。

### 强网杯2024（2 篇）
- 强网杯2024 ez_vm:手撕Windows自定义VM字节码(0x08+寄存器+操作)+白盒AES DFA差分故障分析。 → `switch+tzcnt VM调度 + 字节码0x08 0x20格式 + DFA故障分析 + 白盒AES`；自定义VM逆向:switch+tzcnt指令调度;白盒AES密钥提取可用DFA差分故障分析(注入fault观察密文差分推key);…
- 强网杯2024 solve2 apk:TEA+字节序+Twofish RS_MDS_Encode(Reed-Solomon)+异或拼接三段flag。 → `TEA+switchEndian + Twofish+RS_MDS_Encode + 异或拼接`；APK逆向需结合Java层RS_MDS_Encode(GF(256) Reed-Solomon)+C++层TEA+switchEn…

### 春秋杯2024（1 篇）
- 春秋杯Re 2024三题：pygame RC4+UPX脱壳+IDC patch+TLS TEA+Nim时间反调试+二叉树TEA → `PatchByte(x, Fbin^0x66);TEA decipher(num_rounds=100, k={0x0CAA5BDD,0xD6846924,0x51041EB8,0…`；异或patch绕过反调试、TLS回调函数生成密钥、TEA delta=0x9E3779B9/0x61CBB648变种

### 春秋杯2024冬季赛（1 篇）
- 春秋杯2024冬季赛D1六题混合：ez_vm魔改XTEA+ezre custom_md5+RSA1 Franklin-Reiter+Coppersmith → `decipher(32, m, key={2,0,2,5});v[10]={0x83845981,...};delta=0x20252025;xtea var:((v1<<4)^(…`

### 2024 看雪（1 篇）
- animals CTF 逆向题，OLLVM 控制流平坦化 + NOP patch + 变异 MD5 加密 + 动物商店菜单选项。 → `patch 0x08750a74/0x13751574 为 NOP 变异 MD5 state 0xEFCDAB89 F 改 G`；OLLVM 控制流平坦化用 patch 0x08750a74 (mov/jz 反汇编码) 改 NOP 还原；MD5 变异 = 改…

### 2024 矩阵杯（2 篇）
- 矩阵杯 2024 Reverse：UPX 修复脱壳 + pycdc 反编译 wtf.pyc + IDEA 算法 pyd 逆向复用开源实现。；Python 字节码保护（base58+zlib+marshal 编码 + 缺文件头）是常见混淆；IDEA 是经典 mod 655…
- 矩阵杯攻防赛Mini-Venom WP：Python字节码RC4+矩阵杯reverse+pwn shellcode AWD → `run.py字节码;generate_key: random.seed+shuffle(range(256))+bytes(key);encrypt: encrypted.appe…`；Python run.py可通过dis反编译得字节码再恢复；矩阵杯攻防赛综合

### 2024 移动安全（1 篇）
- CTF 丛林的秘密 — 安卓 + WebAssembly 算法分析 + libgogogo.so mm0 导出 wasm 字节 + wasm2c 转 c + 32x32 矩阵 numpy.l…；WebAssembly 是 Android 算法保护新趋势;Frida hook 导出函数 + readByteArray 拿 w…

### 2024 羊城杯（2 篇）
- DistilBERT 情感分类对抗样本：反向情绪前缀 + very 提相似度。 → `data_dict_2[id] = {"text": "very " + ("happy/nothing/unhappy") + original_text} ; predict_…`；NLP 模型对抗样本常用策略：反向情绪关键词前缀 + 强度副词（very）保相似度。
- Cython 逆向 + Python 函数 hook + random.seed 已知还原魔改 IDEA/TEA/XTEA → `setattr(chal.chal, ''_p1'', hook_p1); chal.chal(''a''*48) # 拦截函数调用`；Cython 编译的 Python 库仍可从 .so 中逆向出 PyObject 结构和函数指针；用 setattr hook P…

### 腾讯游戏安全大赛 2024 决赛（1 篇）
- 腾讯游戏安全大赛2024决赛题解,Windows内核驱动Hook NtCreateFile/NtWriteFile,CR0关闭WP+inline hook+DbgBreakPoint反调试+… → `WPOFFx64 + CR0 + inline hook + NtCreateFile`；Windows内核Hook必学WPOFFx64(改CR0清WP位)+inline hook(5字节jmp)+DbgBreakPoi…

### 腾讯游戏安全大赛 2024 初赛（1 篇）
- 腾讯游戏安全大赛2024初赛题解,Windows DLL hack.dll hook WriteProcessMemory(12字节inline hook+jmp rax+VirtualPr… → `VirtualProtect+inline hook 12字节`；Windows hook链:DLL_PROCESS_ATTACH+AllocConsole+VirtualProtect+12字节…

## Connections

- 上层索引：[[ctf/source-corpus--des-ctf-knowledge]]
- Source: [https://github.com/Dest1ny-Sec/Des-CTF-Knowledge](https://github.com/Dest1ny-Sec/Des-CTF-Knowledge)

## Open Questions

- 本页为索引层，题目细节请回源；若某场比赛多题值得展开，再单独建 `ctf/<比赛>.md` 实体页。
