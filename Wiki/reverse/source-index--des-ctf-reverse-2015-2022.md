---
title: Des-CTF 语料索引 — Reverse（2015-2022）（36 篇比赛 WP）
tags: [source-index, corpus, des-ctf, wp, reverse]
category: reverse
triggers: ["rc4", "anti-debug", "rev", "nim", "flareon", "Android", "JNI", "ArmVMP", "Frida hook", "LLVM", "obfuscation", "OLLVM", "AES", "SBox", "MixColumns", "CRC32爆破", "key-recovery", "GF 2 -linear", "sage", "AES_PRECOMP_TE"]
created: 2026-11-01
updated: 2026-11-01
sources: [https://github.com/Dest1ny-Sec/Des-CTF-Knowledge]
---

# Des-CTF 语料索引 — Reverse（2015-2022）

## Summary

外部语料库 `Dest1ny-Sec/Des-CTF-Knowledge` 中 **36 篇 Reverse（2015-2022） 比赛 WP**（覆盖 27 场比赛）的结构化索引。
每条记录保留「比赛 → 题目/攻击链 → 关键 payload → 要义」，原始全文未镜像，需细节时按 `contest` 回源 GitHub 的 `CTF大赛WP集合/articles/<文件名>.full.md`。

## Key Points

- 高频考点（按 tags 词频）：rc4、anti-debug、rev、nim、flareon、Android、JNI、ArmVMP、Frida hook、LLVM、obfuscation、OLLVM、AES、SBox、MixColumns
- 高频比赛：看雪2022 KCTF秋季赛、看雪2022 KCTF春季赛、看雪·众安 2021 KCTF秋季赛、0CTF 2017、2016 腾讯游戏安全技术竞赛、2021 工业信息安全技能大赛、Bugku、移动安全挑战赛
- tags 来自上游 frontmatter（允许跨赛道噪声，如 Web 页出现 ret2libc），仅作检索线索
- 用法：Grep 本页的比赛名或考点词 → 命中条目 → 需要细节时回源原文；不要在本页上做二次推理

## 按比赛分组

### 2015 移动安全挑战赛（1 篇）
- AliCrackme_2 — Android so 静态分析 while 循环匹配 aWojiushidaan + Frida hook fgets 改 TracerPid:t0 + hoo…；Android 经典逆向三件套:静态分析硬编码字符串 + Frida hook 改 TracerPid + mprop 改 ro.…

### 2016 腾讯游戏安全技术竞赛（1 篇）
- 腾讯 GS Lab MFC 注册机：用户名 + 二次加密 + 自定义 base64 码表 → `序列号 = base64_encode(custom_table, encrypt2(encrypt1(username)))`；MFC GetDlgItem 是经典消息循环；base64 码表替换是入门加密；多 jne 串联是判断结构

### 0CTF 2017（1 篇）
- OLLVM flatten + AES-like scramble + CRC32 爆破前 3 字节 + GF(2) 线性方程恢复 PRNG seed；LLVM 混淆插件常用 CryptoUtils 做 PRNG；GF(2) 线性化是把非线性 S 盒转成线性系统求密钥的经典方法

### 2018 FLARE-ON（1 篇）
- FLARE-ON anode NodeJS NEXE 打包逆向，从十六进制编辑提取 JS 源码 + 状态机逆推。 → `NEXE 提 JS 源码 + 长 switch 状态机逆推`；NEXE 把 JS 源码直接打包进 EXE（无加密无压缩），十六进制编辑就能还原。

### 2021 工业信息安全技能大赛（1 篇）
- 工控 CTF 经典：变种 TEA + SSRF→OpenPLC RCE + Modbus 流量 + 7z 修复 + WinCC → `srand(1626940252) + tea_key={0x0D,0x0E,0x0A,0x0D,...0xEF} → flag.png`；工控/ICS 安全赛要熟悉 OpenPLC、WinCC、Modbus；TEA 系列密码 + srand 爆破是 reverse 入…

### 2021 KCTF秋季赛（1 篇）
- 把 flag 验证逻辑用 Lambda 演算写出来序列化到 Android JNI，逆向时要还原 Lambda 语法树并独立爆破每个 4bit 输入。；Lambda 演算 + 序列化是隐蔽的"代码即数据"反模式，逆向者要么反序列化还原 AST（方式 1），要么爆破每个独立分支（方式…

### 2021 SWPUCTF（1 篇）
- SWPUCTF 2021 新生赛 RE 题：pyinstxtractor 拆 EXE → pyc 补头 → 反编译 → DFS 迷宫 → md5 路径。 → `md5("sssssddssddssaa...ddsssssss")`；PyInstaller 打包 EXE 提取 pyc 只需补 16 字节头，常见反编译入口点。

### 看雪·众安 2021 KCTF秋季赛（3 篇）
- 看雪众安KCTF2021秋Q2《迷失丛林》：codeLSalt 256字节置换+前8字节排列爆破40320种+codeH爆破 → `codeLSalt[0x100] 256字节置换;csl=[30,40,75,109,140,163,210,251]前8字节40320排列;codeL={0x4B,0x6D,0x…`；256字节S盒前8字节可从固定集合排列爆破；gbsA统计+有效性筛选
- 看雪众安KCTF2021秋Q8《群狼环伺》Android CrackMe：双重DES+Frida hook 48轮密钥倒序+双输入爆破 → `Frida hook sub_788 DES;swapkey(48轮密钥倒序);encdata1=[0x45,0x68,0x97,0xa3,0x29,0x2a,0x7f,0xd4]…`；DES轮密钥swap函数可解密；hook NewStringUTF+debugger断点爆破
- 看雪众安KCTF2021秋Q10《生命的馈赠》10种jmp混淆+平方剩余+情感短视频+CrackMe bug修复 → `CodeLineToExpand结构;10种jmp混淆长度:6/0x1C/0xC/0x2D/0x34/0x86/0x59/0x42/0x99/0x46;6 call后lea esp…`；6 call+lea esp堆栈平衡识别真实指令；平方剩余算法；KCTF首部配短视频的CTF题

### 2022 Bugku（1 篇）
- Bugku 安卓逆向 LoopAndLoop — JNI chec 反射调 check1+check2+check3 三函数累加 + check(input,99)==1835996258…；JNI 反弹调 Java 函数是 Android 逆向难点;2*s%3 索引巧妙选函数;stringFromJNI2(in_int…

### CCF BDCI 2022 数字安全公开赛（1 篇）
- 上海交大NETSEC实验室用Bi-LSTM+GatedGCN+MS_loss在二进制函数相似性检测拿二等奖第4。 → `指令标准化(12532词表) + Bi-LSTM 2层128维 + GatedGCN 3层128维 + MS ε=0.1 α=2 β=50`；二进制函数相似性检测 = 度量学习;MS miner比Norm weighted miner更关注信息量大的样本对(0.883 v…

### 2022 CTF练习（1 篇）
- Reverse 32 位题：花指令 0x40146A 跳 0x40146F + Chacha20 + RC4 + srand(time^pid) 爆破。；花指令去花三步走 (D 转数据 + C 转代码 + NOP 填充)；Chacha20 特征 "expand 32-byte k"；…

### CakeCTF 2022（1 篇）
- CakeCTF 2022 5题writeup：welcome+nimrev+luau+zundamon+survey → `gdb-peda$ x/24c 0x7ffff7d0f0e0 → CakeCTF{s0m3t1m3s_n0t_C}`；NimMainModule中找eqStrings直接拿flag；Lua字节码用luadec反编译

### 2022 DASCTF+GFCTF（1 篇）
- cuteRE 入门逆向，动态调出运行时修改的 base64 表 + RC4 key。 → `动态调试拿真实 key (szv~) + 自定义 base64 表`；当 reverse 题 IDA 静态看不出真实 key，优先动态调试 print 实际写入的地址值。

### 2022 FLARE ON 9th Challenge（1 篇）
- FlareOn 9th (2022)全11题WP,涵盖Flaredle硬编码/Pixel Poker坐标/Magic 8 Ball方向键/darn_mice单字节ret反推/T8满月+sra… → `see three, C3 C3 C3 C3 C3 C3 C3! XD + MyV0ic3!`；FlareOn是Windows逆向顶级比赛,Mandiant出题质量高;mixed-mode PE(.NET+native)是复杂…

### Flare-ON 9 (2022)（1 篇）
- Flare-ON 9 第8题BackDoor：C#反混淆+DNS隧道+RC4解密flag → `ps = "powershell -exec bypass -enc " + base64 + "..."`；flareon_wrap_decrypt基于Nop+Call+flare_71模式识别包装方法

### Hackvent 2022（1 篇）
- 五题 Hard 覆盖频谱/图像/密码/AES/pwn → `HV22{v-wish-v-g0t-b33r} / HV22{1'm_y0ur_need13.} / HV22{HAVING_FUN_WITH_CHOSEN_PREFIX_PBKD…`；PBKDF2-HMAC 已知末段可前缀爆破；AES-ECB 配合可控前缀可逐字节 oracle；UAF 后用未清空指针覆盖堆块写任…

### 2022 Square CTF（1 篇）
- Square CTF 2022 YARA：自编译 yara --with-debug-verbose=8 跑规则，提取 OP_PUSH_8 + BITWISE_XOR trace 还原 fl… → `yara-4.2.3/yara -C flag.yarc test.txt 2>&1 / grep -B2 OP_BITWISE_XOR`

### 2022 X-MAS CTF（1 篇）
- X-MAS CTF Naughty List: C++ unordered_map insert 11 个 Naughty 触发 rehash, 让 PinkiePie 移出前 16 元素触… → `/`；/

### 2022 XCTF（1 篇）
- XCTF 2022 Lambda-revenge: 把 flag 分 11 组 3 字节，编码成 Church 数字 + 矩阵 + Church 布尔值，逆向需要还原 Lambda 演算 +… → `/`；/

### 2022 个人赛（1 篇）
- babyre Windows 逆向，IAT hook IsDebuggerPresent 抗调试 + 自定义 S-box 加密 + 16 轮 XOR 计算。 → `flag = Sangfor{855908672599db85b370dcb} v5 = 0x68546749 v6 = 0x79685365`；自定义 S-box 加密需要逆推 sub_401280；IAT hook 抗调试是 anti-debug 经典；VirtualPr…

### 2022 春秋杯（1 篇）
- 2022 春秋杯春季赛 Reverse/Pwn 2 题 WP，QMK 键盘固件逆向 + Layer keycode 继承 + LCG 还原。 → `加密 enc="1CTED8IL-BIMM-SMFP-HOKP-HOIDRZL4W6KR" SEED=0 rand 公式 214013*x + 2531011`；QMK 是开源键盘固件，组合键 (Combo) 触发 process_combo_event；KC_TRANSPARENT (1)…

### 2022 比赛逆向合集（1 篇）
- 6关CrackMe ELF逆向：长度+前缀+末位+十六进制位运算+异或+第三段 → `check1:strlen=34;check2:strncmp(a1,'flag{',5);check3:a1[strlen-1]==125;check4:olympics位运算;…`；多关CrackMe常以_分隔多段子串拼接；位运算(v1<<4)/v2需爆破

### 2022 看雪/Tide 安全 (kanxue/pediy1)（1 篇）
- Frida hook + 爆破绕过 Android JNI 校验 + kill 拦截绕反调试 → `Java.use("com.kanxue.pediy1.VVVVV").VVVV(CONTEXT2, String(x))`；Android CTF 高频工具是 Frida；自定义 classloader 需 enumerateClassLoaders 切…

### 看雪2022 KCTF春季赛（4 篇）
- KCTF2022春Q3《石像病毒》：MD5作AES key+S盒交换+shiftRow方向魔改+异常处理藏逻辑 → `k[42]=0xD4AF3085;swap_aes_sbox:inv_S[S[113]]与inv_S[S[163]]交换;invShiftRows ROF32(block, 8*i…`；魔改AES常见点：S盒交换/swaps、ShiftRows方向、keyExpansion、对比数组
- KCTF2022春Q2《末日邀请》签到题：CRC32折叠signA+Collatz序列+KCTF固定字符+前3位异或signB → `序列号=421KCTF381654729;arrayA折叠signA=0xF52E0765;3n+1 Collatz;SN前3位异或=signB;Arglist[3]=0x14 A…`；签到题常用Collatz(3n+1) + 字节异或；魔改CRC32折叠算法
- KCTF2022春季赛Q5《危机四伏》：32/64天堂之门+SEH+三段混淆算法+base64非预期 → `天堂之门: 切换到64位CS=0x33 retf;#pragma pack(1);LEFTMOVE/RIGHTMOVE;XOR_NUMBER=0x8160C68FF6C4875E^…`；天堂之门对抗大部分调试器；非预期思路：硬件断点+base64字符串+比对历史KCTF题目
- KCTF2022春Q8《迷雾散去》ArmVMP保护：ARM指令虚拟化+重定位+ptrace反调试+Frida hook DES → `ArmVMP vmdata; 44 f0 0d e5指令搜索;frida Interceptor.attach(baseAddr.add(0x4d15));ghidra强制识别ar…`；ArmVMP原理：反汇编+vmachine+解释器；指令模拟需重定位处理PC相对寻址；Frida可绕ptrace

### 看雪2022 KCTF秋季赛（5 篇）
- KCTF2022秋Q2《盗贼作乱》：62进制key+大数+31*x=1mod n数论+BigNumMul/Div干扰计算 → `sbase=0-9A-Za-z;num=10^19 cnt=32;31*x=1 mod n;31*y=n-1 mod n;x=gmpy2.invert(31,n);key=ZSxZ…`；大数模逆用gmpy2.invert；自定义大数库的隐藏干扰点（第31次触发）
- KCTF2022秋Q8《商贸往来》CrackMe：七段shellcode注入+单字节操作加密+进程注入反调试 → `BOOL FUNC(kernel32, LoadLibraryA, GetProcAddress, input);4字节长度+SHELLCODE*7;单字节操作;Decompres…`；Windows CrackMe经典进程注入模板：CreateProcessW(CREATE_SUSPENDED)→VirtualA…
- KCTF2022秋Q6《病疫先兆》CrackMe：srand种子爆破+rand对比+KCTF固定14位序列号 → `14位序列号=14725KCTF83690;srand爆破0-99999;arrSeed_14725[20]={15356,8563,...};arrSeed_83690[20]=…`；srand种子爆破范围小时（0-99999）只需1分钟穷举；20个rand值固定可反推种子
- KCTF2022秋Q11《衣锦昼行》：魔改DES+RSHash+正向迭代22次+断点取serial的非预期解 → `修正函数签名按push数确定参数个数;v70变量贯穿;0x5B6364断点;v391=correct serial;name=KCTF;serial=6FB1CE80914403C…`；IDA混淆函数按汇编push个数修正签名；正向迭代变量贯穿时中途断点取结果
- KCTF2022秋Q10《两袖清风》：AES前4枚举+RSA后缀解+shellcode验证+迷宫路径算法 → `AES-CBC IV=ABCDEF0123456789;后缀=AllIsNothing;前4枚举(0-9)横向纵向相加相等;AES key=1010AllIsNothing;迷宫1…`；python+vc混合枚举速度；AES-CBC shellcode按执行结果筛选key

### 蓝帽杯 2022 Reverse（1 篇）
- 手写 PE Loader 从 .data 段加载无文件 PE，IAT 用 CRC32 查表 + Base Relocation；nim 关键逻辑是 Pell 方程 x²-11y²=1 求解。；无文件 PE 加载 = VirtualProtect RWX + 手写 IAT (CRC 查函数名) + Base Relocat…

### 2022 西湖论剑 Upnp（1 篇）
- 西湖论剑UPnP WriteUp,NETGEAR R7000系列路由器固件,UPnP SOAPAction协议11个service+SOAPLogin+Cookie sess_id会话+Ge… → `SOAPAction: urn:NETGEAR-ROUTER:service:ParentalControl:1#Authenticate`

## Connections

- 上层索引：[[ctf/source-corpus--des-ctf-knowledge]]
- Source: [https://github.com/Dest1ny-Sec/Des-CTF-Knowledge](https://github.com/Dest1ny-Sec/Des-CTF-Knowledge)

## Open Questions

- 本页为索引层，题目细节请回源；若某场比赛多题值得展开，再单独建 `ctf/<比赛>.md` 实体页。
