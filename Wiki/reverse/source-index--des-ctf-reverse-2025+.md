---
title: Des-CTF 语料索引 — Reverse（2025+）（40 篇比赛 WP）
tags: [source-index, corpus, des-ctf, wp, reverse]
category: reverse
triggers: ["Unreal Engine", "Frida", "UE4", "ELF", "unicorn-emulation", "statically-linked", "stripped", "signal-handler", "IDA", "custom-vm", "control-flow-obfuscation", "KCTF 2025 第二题 初窥门径", "32 战队提交 47 分最快", "三维迷宫 3x9x10", "maze_data 整数 不可分解为墙", "质因数分解还原迷宫", "keydict 36 字符", "ret 跳转函数拆分", "y 轴 12 漏洞多解", "野鹿子战队 yegu mesetop"]
created: 2026-11-01
updated: 2026-11-01
sources: [https://github.com/Dest1ny-Sec/Des-CTF-Knowledge]
---

# Des-CTF 语料索引 — Reverse（2025+）

## Summary

外部语料库 `Dest1ny-Sec/Des-CTF-Knowledge` 中 **40 篇 Reverse（2025+） 比赛 WP**（覆盖 31 场比赛）的结构化索引。
每条记录保留「比赛 → 题目/攻击链 → 关键 payload → 要义」，原始全文未镜像，需细节时按 `contest` 回源 GitHub 的 `CTF大赛WP集合/articles/<文件名>.full.md`。

## Key Points

- 高频考点（按 tags 词频）：Unreal Engine、Frida、UE4、ELF、unicorn-emulation、statically-linked、stripped、signal-handler、IDA、custom-vm、control-flow-obfuscation、KCTF 2025 第二题 初窥门径、32 战队提交 47 分最快、三维迷宫 3x9x10、maze_data 整数 不可分解为墙
- 高频比赛：腾讯游戏安全、2025 KCTF、春节活动、强网杯2025、2025 天网杯、2026 第三届长城杯网数智、2026 腾讯游戏安全、2026 解题领红包
- tags 来自上游 frontmatter（允许跨赛道噪声，如 Web 页出现 ret2libc），仅作检索线索
- 用法：Grep 本页的比赛名或考点词 → 命中条目 → 需要细节时回源原文；不要在本页上做二次推理

## 按比赛分组

### 2025 KCTF（3 篇）
- 2025 KCTF 第二题初窥门径：三维迷宫 3x9x10 + 质因数分解还原 + 路径映射字符 + ret 跳转轻度逆向干扰。47 分最快解。 → `maze_data[3][9][10] 质因数分解判墙/路`；迷宫类 CTF 题常用质因数分解判墙/路（"不可分解"=墙，"可分解"=路）；y 轴 %12 漏洞导致多解是 KCTF 评委点名的…
- 2025 KCTF 第六题 秘辛揭露：易语言内核特征检测 + 重载编译混淆 + RC4 KSA 流密码；自定义调用约定+ IDA 崩溃点+ 动态调试。 → `xor edx, 0BADF00D; xor ecx, 0CAFEBABE; or ecx, edx; setnz al`；易语言逆向需要 tools/link.ini 重新编译生成 obj → IDA 看易语言符号；水泊梁山战队（zZhouQing/0…
- 2025 KCTF 第十题 涅磐新生：VHDL 硬件设计 + GHDL 仿真器 + AES-256-CTR 加密 + 波形图分析 + Frida Hook；跨领域 VHDL+密码学综合。 → `Run_Through_Longjump(sub_140071B50) < 0 → 错误路径`；VHDL/GHDL 编译后的二进制是少见的逆向题类型，ghdl 源码+波形图+字符串抹除是常见反分析手段；Frida Hook +…

### 2025 天网杯（1 篇）
- 2025 天网杯 DamnEnv lambda co_consts 反编译 + SystemBot 提示词泄露 + Federate 联邦学习模型反演（LeNet+权重变化+inversio… → `scale_factor=-20 → (new - old) * -20 → gradient`；Python lambda 函数 __code__.co_consts 存所有常量包括 flag 字符串；联邦学习模型反演经典套路…

### 2025 AliyunCTF（1 篇）
- Aliyun 2025 easy-cuda-rev — CUDA PTX 汇编 sm_52 + 5 段 10485760 轮 T-table 查表 + 4-bit swap + XOR 索引…；CUDA reverse 用 cuobjdump -ptx 提汇编;__local_depot0 是 per-thread loc…

### CubeCTF 2025（1 篇）
- CubeCTF 2025 Operator取证题，pcap提取ELF后门逆向解密 → `tcp.flags == 0x0018; nc -lvp 2025 > /tmp/xcat`；多段TCP重组需按顺序拼接；自写加密工具逆向需识别sub_15D0/sub_16A0

### 2025 DEF CON CTF（1 篇）
- DEF CON CTF Finals 2025 — ico 二进制 byte 协议 (0x10/0x22/0x32) + 服务端 fork + /flag → tEXt Author chu…；服务端常用 byte 协议 (tag+length+payload) 替代 HTTP;flag 通常藏在 metadata chu…

### 2025 DEFCON（1 篇）
- DEFCON 33 Quals nfuncs 逆向题，angr + unicorn 联合自动化求解 8 字节表查找链。 → `angr + unicorn 混合符号执行 + SMC 自修改`；现代 DEFCON 逆向题常用 SMC 自修改代码 + 多层函数调用，需要 angr 符号执行 + unicorn 模拟混合。

### Google CTF 2025（1 篇）
- Dart AOT ARM64 逆向 + 自定义 b62 编解码 + 时间戳爆破 token + 多线程预计算 pin 优化。；Dart AOT 没有标准运行时元数据，靠 DecompressPointer+HEAP 寻址对象；优化 pin 循环时预计算 t…

### HKCERT CTF 2025 資格賽（1 篇）
- HKCERT 2025资格赛逆向方向5题,涵盖魔改SM4+自定义SBOX+魔改AES+魔改XXTEA+随机种子爆破,密码学逆向全方向深度。 → `hkcert25{...}`；魔改对称加密的破题关键是先识别原算法(SM4/AES/XXTEA),再识别SBOX/常量/DELTA/key的修改,逐项还原。随机…

### 2025 Super Flagio CTF（1 篇）
- Super Flagio CTF：cocos2d-x Android 游戏逆向，XXTEA 解 LuaJIT + 自定义 opcode VM 还原。 → `frida -l hook.js -f com.flagio.app`；加密字节码 + 自定义 opcode 是游戏反外挂常见组合，Frida hook luaL_loadbuffer 是标准破法。

### 2025 aliyunctf（1 篇）
- 阿里云 CTF 2025 Rust bevy 引擎逆向题，从 .scn 场景文件还原 ECS 实体组件系统状态机。 → `flag 通过 ECS Query<&mut Transform> 状态推进触发`；现代 Rust 游戏引擎(bevy)逆向关键是理解 ECS 模型 + App schedule + 系统依赖图，不必纠结 Rust…

### 2025 m0leCon（1 篇）
- m0leCon 2025 Teaser 嵌入式逆向，RISC-V 自定义分组密码 + 行列和反推 output。 → `1 位种子 + IP/P 盒 + 行列和反推`；当 output 矩阵每行/列只有 1 位为 1 时，行列和反推是经典线性代数问题。

### 强网拟态 2025 EZMiniAPP（1 篇）
- 强网拟态2025 EZMiniAPP微信小程序逆向,wxapkg解包(二进制格式+大端索引+UTF-8文件名)+enigmaticTransformation加密函数+XOR/rotl逆运算… → `enigmaticTransformation + newKey2025!`；微信小程序逆向从__APP__.wxapkg开始,用unwxapkg/wxappUnpacker解包;JS代码混淆名(enigma…

### 强网拟态 2025 HyperJump（1 篇）
- 强网拟态2025 HyperJump自定义VM口令验证,24字符逐位+查找表+XOR/ROL/ROR+SSE指令+patch返回码为r15索引(3字节patch替代5字节mov eax, 0… → `mov eax, r15d (3字节patch)`；自定义VM口令验证的爆破关键:让程序返回"已验证字符数"作为侧信道;r15作为索引寄存器的patch技术(短指令+NOP)是密码学…

### 强网拟态 2025 Icall（1 篇）
- 强网拟态2025 Icall多层加密+反调试对抗,pthread_create+prctl监控+TracerPid检测+kill自杀,绕反调试后三轮加密(多轮RC4+仿射+Enc1yp7)逐… → `r0uNd_Rc4 + Aff1ne + Enc1yp7`；多线程反调试(子线程prctl+TracerPid检测)比单线程更难绕过;GDB的return命令是动态绕反调试最灵活的方法;多轮…

### 强网拟态 2025 决赛 WeakJump（1 篇）
- 强网拟态2025决赛WeakJump逆向,静态链接+stripped+ptrace反调试(用GDB catch syscall ptrace改$rax=0绕过)+控制流平坦化(将函数视为黑盒… → `ptrace=$rax=0 + TEA Feistel`；ptrace反调试用GDB catch syscall+set $rax=0绕过;TEA特征常量0x9E3779B9+0xC6EF…

### 强网拟态 2025 决赛 easyre（1 篇）
- 强网拟态2025决赛easyre逆向,PE32+Windows+signal(SIGSEGV)+setjmp/longjmp控制流混淆+异常处理触发跳转+TEA解密(delta补码0x61C… → `0x9E3779B9 + 0x61C88647 + signal(SIGSEGV, handler)`；signal(SIGSEGV)+setjmp/longjmp是控制流混淆的经典手法;TEA特征常量0x9E3779B9+0xC6E…

### 强网拟态2025初赛 Mobile方向（1 篇）
- 强网拟态2025初赛Mobile just:Unity il2cpp Frida hook_clone NOP CRC+global-metadata.dat XOR解密。 → `Frida hook_clone + nop_64(0x119F8) CRC + global-metadata.dat XOR解密 + Il2CppDumper`

### 强网杯 2025 tradre（1 篇）
- 强网杯2025 tradre逆向,Full RELRO+Canary+NX+No PIE+控制流平坦化(182基本块+176状态转移)+fork()反调试+alarm(60)修补(mod $… → `minimal_patch_tradre (alarm(60)→alarm(0))`；tradre是2025强网杯控制流混淆+多反调试+魔改AES的高级题;alarm(60)单点patch是绕过60秒超时的标准方法;…

### 强网杯2025（2 篇）
- 强网杯2025 PolyEncryption:AI辅助+dotnet反编译+saw.py Hack.java patch+22轮AES风格PolyEnc纯Python实现。 → `22轮 PolyEnc(奇数SubBytes+MixColumns,偶数ShiftRows+AddRoundKey) + 密钥级联(A'=A^sbox(rol(D,8))^roun…`
- 强网杯2025 Secured Personal Vault:WinDbg JS转储BSOD内存+提取两进程AES key+iv解密flag。 → `WinDbg JS safeDumpMemory + 进程AES key+iv提取 + CommuStruct序列化`；WinDbg JS脚本:host.memory.readMemoryValues分页读取+失败用nullView填充;进程间Mai…

### 2025 强网杯S9初赛（1 篇）
- 强网杯S9初赛 tradere逆向:ptrace父子VM+int3替换+data结构func+Graphviz控制流图+VM模拟器还原算法。 → `ptrace PTRACE_GETREGS/POKEDATA/CONT + data结构体func映射JL/JLE/JNE/JE/JNS/JG + Graphviz printdo…`；父子进程ptrace+int3替换实现自定义VM调试;data结构体{lchild, rchild, func, reg2}0x2…

### 2025 第三届古剑山网络安全功防大赛初赛（1 篇）
- 古剑山初赛Reverse全解5题：easyre RC4+异或+HelloWorld字节码+VM+RC4 key is_good}+VxWorks固件 → `easyre:异或0x90504956+0x22222222+RC4+base64;HelloWorld字节码(v3[idx]=v28[src_idx] op=3);VM opco…`

### 2025 腾讯游戏安全（5 篇）
- 虚幻引擎 4 安卓手游 — Frida 写 GWorld/GName/GObjects 全局+解析 UObject+改 FirstPersonCharacter GunOffset + Pl…；UE4 安卓游戏逆向需先定位三大全局 (GWorld/GName/GObjects)；UObject 通过 FNameIndex…
- Windows 内核 VT 抗分析 — kdcom.dll 检测 + KdDisableDebugger + VT hook TEA + MSR(0xE8) 异或 + Unicorn 模拟还…；抗分析是分层叠加：模块名检测 + 调试 API 检测 + VT 虚拟化 + 硬件断点检测；XSafe 双驱动配合可定位+dump…
- 腾讯游戏安全初赛 — R3 windbg 阻断 + R0 驱动加载阻断 + 自定义 base58 字符集 + XOR "sxx" + ACEDriverSDK::FilterSendMess…；R3+R0 双层反调试常见：模块+0x10D0 必为 ret；驱动加载前 sub_140001370 是阻断点；base58 自定…
- 腾讯游戏安全安卓初賽 — UE4 + Disable Depth 透视 + LineTrace 渲染控制 + pthread_create 钩子 anti-bypass + RecoilPi…；UE4 安卓反作弊常驻检测 libGame.so+7068 偏移线程；MyProjectCharacter 0x52c Recoi…
- 腾讯游戏安全安卓决赛 — UE4 cheat 设备检测 (mtime 三相等 + 0x10 阈值) + mprotect/inotify/mincore 三重内存检测 + 函数 CRC32…；安卓反作弊三重检测：cheat 设备 (mtime+阈值) + 函数完整性 (CRC32) + 内存页存在 (mincore)；f…

### 2025 自制 CTF 教学题（1 篇）
- ELF+signal handler+OLLVM 三合一逆向入门 → `0x47c093 'wrong!' / 0x47c09a 'right!' / 0x47c0a1 'password:'`；signal handler 是反调试常用手段；OLLVM 是工业级混淆；静态链接二进制要手动定位

### 2025 阿里云CTF（1 篇）
- 阿里云CTF2025复现风清:runes(MIPS+chroot+SCM_RIGHTS+AF_UNIX+send_fd/recv_fd+8次chdir逃逸+open flag)+stack…；1) MIPS手搓syscall:rax=syscall号,rdi/rsi/rdx=参数;2) chroot逃逸:mkdir fo…

### 2025 鹏城杯（1 篇）
- 鹏城杯2025 WFP callout驱动逆向,ClassifyFn识别WFP过滤ICMP,MSR_LSTAR读KiSystemCall64前4字节作Key1,KUSER_SHARED_DA… → `msr[c0000082] = fffff801'0c211900/0f 01 f8 65(key1)/seed_str="C:\WINDOWS" seed_bytes=seed_…`

### 2026 第三届长城杯网数智（1 篇）
- 2026 长城杯网数智 Reverse AK：DokiLogic Renpy 游戏逆向 - .rpyc 打印 + 文件监控拷贝 .1.exe + UPX 脱壳 + XOR 35。 → `chr(ord(ll1l111ll11l) ^ llll1l111ll1=35)`；Renpy 引擎逆向 = 修改 renpy/script.py load_file 把 .rpyc 反编译输出到日志；游戏运行时临…

### 2026 腾讯游戏安全（1 篇）
- 2026 腾讯游戏安全 PC：SHAD0WNT_HYPERVMX 迷宫驱动逆向 + 命名事件反馈 + TEB+0x68 状态位。 → `WriteFile 0x68 TEB->LastErrorValue handle_ok`；腾讯游戏安全 PC 方向 = Windows 内核驱动逆向 + 命名事件/信号量 + 用户态注入 + IOCTL 接口逆向；TEB…

### 2026 解题领红包（1 篇）
- 2026 解题领红包 CrackMe v2.5：假码 16 + 真码 31 校验和 44709 + AES 点阵图 + JNI/Frida/模拟器多层检测 + Android 拼图 6 块… → `校验和 sum(Str[i] * (i+1)) = 44709`；CrackMe 真码 = 假码外+真码（长度+校验和+子函数动态生成）三层防御；JNI/Frida/模拟器检测是真机脱壳关键；An…

### 2026 软件系统安全赛（1 篇）
- 2026 软件系统安全赛 re1：64 位 .elf Loader dropper + 内嵌 base64 .pyc stager + 视频黑白色块解码。 → `stager_pyc_base64 → base64_decode → .pyc exec`；Loader 加载 video.mp4 + 内嵌 stager_pyc 是 2026 新型 ELF 逆向套路；.pyc 字节码用…

### 2026 春节活动（3 篇）
- 2026 春节 Tokeii 的全自动 AI Agent 做题系统分享，逆向题 Android JNI 实战。 → `Aclasses.dex (Java/Kotlin) + libhajimi.so (多架构 native)`；AI Agent 多模型协作可降低单模型偏差；常见工具 calling func 大幅减少 token；OpenViking 上下…
- 2026 春节初十 Windows 高级题，~95M 指令白盒密码 + MBA 混淆 + Unicorn 全指令模拟逆向。 → `RVA 0x154E50 已知白盒密码上下文 (~2.7MB) + RVA 0xAD7660 自定义白盒密码上下文 (~28.8MB) + CHIMERA1 头验证`；MBA(mixed boolean arithmetic) 表达式 n*(n+1) 或 n*(n-1) 是不透明谓词；白盒密码 2…
- 2026 春节 hhxxhg 纯手工 WP，覆盖 Python 3.12 pyc 逆向、WASM 加载、MCP 工具链审计流。 → `flag 第一位 5 长度 30 python312__Py_FalseStruct`；Python 3.12 pyc 用 IDA 跟入 real_main 排冗后通过逐字符获取是有效路径；wasm 加载失败时需用工具…

### 2026 阿里CTF（1 篇）
- 阿里CTF2026-license协议逆向:4字节长度+12字节nonce数据流→AES-GCM解密→zstd解压缩→base64→protobuf→魔改AES-160(20字节块7轮,SB… → `AES_SBOX=bytes.fromhex("637C699066329A0E6441CBA99FFAD5AA6524F777371D83EB981A2A7DBD2502EEE5…`

## Connections

- 上层索引：[[ctf/source-corpus--des-ctf-knowledge]]
- Source: [https://github.com/Dest1ny-Sec/Des-CTF-Knowledge](https://github.com/Dest1ny-Sec/Des-CTF-Knowledge)

## Open Questions

- 本页为索引层，题目细节请回源；若某场比赛多题值得展开，再单独建 `ctf/<比赛>.md` 实体页。
