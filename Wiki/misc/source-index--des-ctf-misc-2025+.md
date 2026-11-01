---
title: Des-CTF 语料索引 — Misc（2025+）（41 篇比赛 WP）
tags: [source-index, corpus, des-ctf, wp, misc]
category: misc
triggers: ["XXTEA", "数字中国 2026 初赛", "xor", "custom-base64", "aes-ecb", "stego", "rc4", "AI-assisted", "陇剑杯预选赛", "pickle反序列化", "PTH哈希传递", "PSEXESVC", "Wireshark", "7-bit-decoder", "SpamMimic", "libc-2.35", "gets", "openat", "PHP-act动态函数", "Spring-aop"]
created: 2026-11-01
updated: 2026-11-01
sources: [https://github.com/Dest1ny-Sec/Des-CTF-Knowledge]
---

# Des-CTF 语料索引 — Misc（2025+）

## Summary

外部语料库 `Dest1ny-Sec/Des-CTF-Knowledge` 中 **41 篇 Misc（2025+） 比赛 WP**（覆盖 37 场比赛）的结构化索引。
每条记录保留「比赛 → 题目/攻击链 → 关键 payload → 要义」，原始全文未镜像，需细节时按 `contest` 回源 GitHub 的 `CTF大赛WP集合/articles/<文件名>.full.md`。

## Key Points

- 高频考点（按 tags 词频）：XXTEA、数字中国 2026 初赛、xor、custom-base64、aes-ecb、stego、rc4、AI-assisted、陇剑杯预选赛、pickle反序列化、PTH哈希传递、PSEXESVC、Wireshark、7-bit-decoder、SpamMimic
- 高频比赛：2026 数字中国创新大赛、第三届陇剑杯预选赛、西湖论剑、阿里云CTF、2025 广西网络与信息安全职业技能竞赛、2025 御网杯、供应链安全、Android 逆向
- tags 来自上游 frontmatter（允许跨赛道噪声，如 Web 页出现 ret2libc），仅作检索线索
- 用法：Grep 本页的比赛名或考点词 → 命中条目 → 需要细节时回源原文；不要在本页上做二次推理

## 按比赛分组

### 2025 广西网络与信息安全职业技能竞赛（1 篇）
- 广西职业技能赛 8 大题：流量+密码+re+pwn+web+java → `Spring AOP JDK17 反序列化: TemplatesImpl + POJONode + EventListenerList + UndoManager`；Java 17+ 反序列化要靠 Spring AOP 跨 Module 反射；PHP act() 链很新颖

### 2025 御网杯（1 篇）
- 2025 御网杯本科组线下半决赛 Misc+Crypto 多题：USB 键盘流量+压缩包爆破+exe 改后缀+工业流量+rot13+Base 混合解码。 → `USB 键盘流量 <DEL> 8 次 = 8 个删除字符`；USB 键盘流量 '<DEL>' 是 Backspace 退格键，要计算删除字符数还原真实输入；exe 改后缀解压是流量包杂项最常…

### 2025 Android 逆向（1 篇）
- Android 多组件协同漏洞链 — APK 反编译 + MiscService (已导出) 透传 Intent CLASS_NAME 启动未导出 WebActivity + CoreSer…；Android normal 权限形同虚设,任何 app 可申请;MiscService 透传 Intent 是常见未导出组件攻击…

### 2025 Binarly UEFI 漏洞研究（1 篇）
- Binarly UEFI 固件安全研究：AMI UsbRt 7 年漏洞未修复 + CRC32 欺骗 + SMM ROP 链 + FwHunt 大规模检测。；AMI UsbRt 是 7 年老漏洞 (gUsbData 30+ 字段), CRC32 验证可被欺骗绕过, SMM_Code_Ch…

### 2025 BlockHarbor 比赛（1 篇）
- BlockHarbor汽车安全比赛AI辅助WP,涵盖caringcaribou UDS RDBI dump+Security Access算法逆向+CAN日志固件重建+seed→key计算,… → `32位hex大写answer`；汽车安全比赛常考UDS(统一诊断服务)+CAN总线+caringcaribou工具;SecurityAccess算法(Seed→K…

### DefCamp CTF 2025（1 篇）
- DefCamp 2025 onigirl：stb_image魔改+PIC格式+像素距离变换 → `*(double*)&modification_parameters[3*random_index] = rand()/2147483647.0*0.25+0.15`；stb_image读取+随机扰动+像素距离因子是复杂图像题常见模式

### SUCTF 2025（1 篇）
- SUCTF 2025 SU_APP Android 逆向：Frida hook android_dlopen_ext+fread 改写返回 ret 0x50 + SoFixer 修复 + 自… → `Interceptor.attach(dlopen, onEnter) + Interceptor.attach(0xA158, onLeave retval) / Memory.…`

### 2025 Text2SQL 比赛心得 (LLM/NL2SQL)（1 篇）
- Text2SQL 比赛实战心得，多级召回（DB→表→字段）+ 向量检索模拟问题 + 上下文重写 + Agent ReAct 循环 + 复读机熔断，覆盖自然语言到 SQL 的全流程难点。 → `多级召回 = LLM DB 选库 → LLM 选表 → LLM 选字段 / 向量检索 = LLM 模拟问题 → 向量化 → 用户问题 top-k / Think-Write SQL…`

### UKY 2025 UCSC 高校赛（1 篇）
- UKY 战队 UCSC 赛 8 小时 4 题全解+Misc 全收，覆盖 3 路 stego、3 道 pwn、4 道 re、4 道 crypto 完整链。 → `flag{8f02d3e7-ce89-4d6b-830e-5d0cb5695077} / b'%10$p'+b'%{shell}c%9$hn' / flat({0:b' sh',…`

### aliyunCTF 2025（1 篇）
- aliyunCTF 2025 beebee：Linux Kernel patch 添加自定义 BPF helper bpf_aliyunctf_xor (212) + verifier 越界… → `bpf_aliyunctf_xor(buf, buf_len=sizeof(s64)=8, res) / _res=2025; _res ^= *(s64*)buf; *res=_…`

### 2025 furryCTF（1 篇）
- furryCTF 2025 高校联合新神赛 WP，PPC 爬虫 + Pwn 基础 + Forensics 内存取证三大方向。 → `/api/flag/char/{i} 480 页 base64 + hex 拼接`；PPC 题目多用爬虫脚本自动化；fmt-string + 栈溢出是 PWN 入门；vol 是 Windows 内存取证标准。

### 天山固网-2025网络安全技能竞赛（1 篇）
- 天山固网2025赛涵盖Dvorak脑洞/random种子RSA/pollard分解/p^q异或剪枝/AndroidDemo AES-ECB/50字符IDA反推5题。 → `Dvorak键盘 + random.seed(0) + Pollard_p-1 + 30E0C3字节模式 + 0x5F下划线分隔`；Dvorak键盘脑洞题需要图片描述提示;random.seed(0)固定+myPrime伪随机可预测2000个64-bit素因子;…

### 2025 封神台CTF（1 篇）
- EVM 冷读/热读 gas 差异作为侧信道让 view 函数在不同调用返回不同值，绕过 wish_making 校验。；Solidity view 函数虽不能修改状态，但 gasleft() + cold/warm access 是隐蔽的"状态"维度…

### 强网拟态@2025（1 篇）
- 强网拟态2025全方向10+题:smolagents提示词注入+ROP+heap+AArch64+Spring Gateway Actuator SpEL+Flask SSRF链+Jinja… → `smolagents注入 + Actuator SpEL + Jinja2 lipsum os.popen + LCG LLL + 7步SSRF Refresh`

### 强网杯 2025 谍影重重6.0（1 篇）
- 强网杯2025谍影重重6.0 MISC综合,746MB pcap全UDP+RTP协议+1305个G.711 mu-law音频流+tshark/rtpbreak批量提取+AI识别+八进制数字判… → `1305 G.711音频流 + 八进制数字`；海量MISC数据处理必须用批量自动化脚本;RTP+G.711是VoIP标准协议;tshark/rtpbreak是RTP提取工具;1…

### 2025 技能兴鲁（1 篇）
- 山东省铸网 2025 工控 4 题：PCZ 解压找 flag + TCP 流拼字符 + CAN 速度积分算距离 + OTA AES-CBC UUID 爆破。；工控 OT/IT 融合题考察 OT 协议 (modbus/TCP/CAN) + 流量分析 + 加密协议 (AES-CBC) 综合能…

### 2025 浙江省信安赛（1 篇）
- 浙江省信安赛 6 题：XOR + JNI strcmp + RSA p+q-pq + 数据清洗 + 滑动窗口 + 鸿蒙 RC4 矩阵密码。；多技能融合赛，XOR/RSA/数据清洗/滑动窗口/矩阵密码各占一题；身份证校验码权重 [7,9,10,5,8,4,2,1,6,3,…

### 2025 第三届数信杯数据安全大赛（1 篇）
- 第三届数信杯AI模型：图片R通道mod10提取20维特征+3层MLP推理+27字符flag → `MultimodalMLP:fc1=Linear(20,64) fc2=Linear(64,32) fc3=Linear(32,27) ReLU;R mod 10得20维特征;ro…`；AI隐写赛=特征提取(R通道mod10)+轻量MLP推理(3层Linear+ReLU)+输出转ASCII

### 2025 第三届陇剑杯预选赛（2 篇）
- 第三届陇剑杯预选赛：RC5-32/12/b+Z3多约束+XXTEA+pickle反序列化+Wazuh应急响应 → `RC5-32/12/b rol32/ror32/0xB7E15163;flag{7ac1d3e59f0b2468};flag{cbee3251-9cff-4542-bf15-337…`；RC5算法key_schedule+decrypt块解密；Z3约束+popcount处理32位校验
- 第三届陇剑杯预选赛（另一版writeup）：RC5+Z3+XXTEA+pickle+Wazuh（与同篇WP同主题重复） → `flag{7ac1d3e59f0b2468};flag{cbee3251-9cff-4542-bf15-337bb8df7f3f};RC5-32/12/b;Z3多约束+popcou…`；同一比赛不同作者写的writeup会有不同侧重点，可互为补充

### 2025 西湖论剑（2 篇）
- 第八届西湖论剑初赛官方上半:420所高校758队3960人,涵盖已悟(sha256+shuffle预言机)+matrixRSA(p0+Coppersmall_roots+矩阵RSA+non-… → `S1.index(id)==S1[id] and S1[id]+id==15/p0<<100+x, f.monic(), f.small_roots(X=2^100, beta=0…`
- 第八届西湖论剑下半:MISC糟糕的磁盘(5张img合并)→RSA+AES-CBC+HMAC+COM_UnMunge2解码UDP stream→secret.pcapng/PWN-babytr… → `img1..5 合并/sha256(AES_key + HMAC_key) = hashlib.sha256(raw_aes_keys).digest()/int3+pop rax…`

### 铸网-2025 山东省工业互联网网络安全职业技能竞赛（1 篇）
- 铸网-2025山东省工业互联网职业技能竞赛WriteUp,涵盖流量分析PK文件提取+CRC32爆破+PNG高度修改隐写+RE魔改XXTEA+自定义Base64方向。 → `everflag{cd00b4953fe9a109148f350427ceddbd}`；工业互联网安全赛常考"流量+隐写+魔改密码学"组合拳,XXTEA魔改重点关注sum递增/递减方向+key长度+delta常量+pa…

### 2025 长城杯（1 篇）
- 第五届长城杯京津冀 Mini-Venom 10 题：caps 路径穿越 + tar 软连接 + Incomplete_Class + CVE-2024-2961 + safetensors…；长城杯京津冀特色：Web caps 控制大小写 + tar 软连接 + Incomplete_Class + AI 题 tf.io…

### 2025 长沙市数据安全管理员预选赛（1 篇）
- 长沙市2025预选赛数据安全管理员赛项:HexCode(7z解+二进制转图)+失控的服务器(应急分析)+Wiener攻击RSA(连分数逼近e/φ(N))+SSTI %c*11 char编码_… → `{%print(((lipsum/attr((''%c''*11)%(95,95,103,108,111,98,97,108,115,95,95))/attr((''%c''*11…`

### 2025 阿里云CTF（2 篇）
- Alibaba CTF 2025 多方向题：eBPF 只读 map verifier 缺陷实现只读内存写入 RCE + mba 整数溢出绕自检 + CUDA PTX AES 逆向。；eBPF verifier 对只读 map 的预载入是经典绕过点（Map pre-load attack），用户空间的 mmap…
- 阿里云 2025：Python _posixsubprocess 沙箱逃逸 + 时间盲注 + adminer 数据库写马 + CUDA S-box + XTEA-like 加密。；_posixsubprocess.fork_exec 是 Python 沙箱逃逸的隐藏口子；MySQL root:root 默认密…

### 2025 鹏云杯（1 篇）
- 鹏云杯 4 题：迷宫 game 字节 XOR + 3 轮 ror+swap 加密 + 4 阶 RSA 连分数攻击。；迷宫 + boss 影响 enc 是 CTF 出题常用套路；4 阶 RSA 分解需要连分数 + phi 多项式。

### 2025 鹏程杯（1 篇）
- 鹏程杯全栈：Cookie 改 admin 竞争上传 + Django cache pickle RCE + SM4 改 MASK key schedule + MQTT 注入。；Django 文件型缓存 pickle 反序列化是经典 RCE；MQTT 协议窗口期命令注入需时序控制。

### 2026 数字中国创新大赛（2 篇）
- 2026 数字中国图片隐写一：zsteg + LSB 1 比特 + G 通道 + start/step 爆破 + msb 拼字节 + isprintable 过滤。 → `G = rgb[1::3]; bits = [G[start + i*step] & 1 for i in range(L*8)]`；LSB 隐写爆破套路：start=起始位 0-7，step=间隔 1..len/(L*8)，msb/lsb 两种拼接，isprin…
- 2026 数字中国大批量文件处理：docx 提取纯文本 + 18 位身份证号正则批量提取 + 15 位老版兼容。 → `ET.parse iter t 标签 + re.finditer d{17}[dXx]`；docx 实质是 zip + XML；批量提取 = zipfile + ET.parse + iter + 正则；身份证 18 位…

### 2026 CTF Agent（1 篇）
- CTF Agent 校赛 TOP1 经验 — 三省六部制 (3核心+6子 Agent) + 异步记忆同步 + GPT-5 + sandbox + MCP 工具集, 1 小时领先 7000 分…；AI Agent 解 CTF 是新方向;三省六部制 异步记忆同步 解决 Agent 同步/效率;MCP 工具注册调用是接口标准;s…

### 2026 NewstarCTF 2025 Week1（1 篇）
- NewstarCTF 2025 Week1 Reverse 全解 (5 题)：自定义 base64 + AES-ECB Android + 双层 XOR + jigsaw 拼接 + 基础 X… → `alpha="HElLo!A=CrQzy-B4S3/is'waITt1ng&Y0u^{/(>v<)*}GO~256789pPqWXVKJNMF" + v5=[0x13,0x13,0…`

### 2026 OSINTUK（1 篇）
- OSINTUK 星座行动: 5 道 UK 都市传说主题 OSINT (Albert Square + Big John''s + toyota island + 报纸 + 虚无密码)。 → `/`；/

### 2026 事件分析（1 篇）
- 2026-05 Mini Shai-Hulud 蠕虫式供应链投毒事件分析: TanStack + Mistral + Squawk + OpenSearch + Guardrails AI… → `/`；/

### 2026 传智杯（1 篇）
- 传智杯 6 道算法题程序设计挑战赛 WP，整除判断 + 位运算 OR + 平方数 + DP + 二分 + LCA。 → `mask = (1<<30) - 1 math.isqrt(x) % 2 == 1 up[k][v] 二分 LCA`；程序设计挑战赛考察算法实现能力；(l+2)//3 <= r//3 整除判断；mask = (1<<30)-1 位运算 OR；mat…

### 2026 供应链安全（1 篇）
- TanStack 供应链劫持复盘 — Pwn Request + Actions 缓存中毒 + OIDC 内存提取 + P2P 信使 + 6分钟植入 84 个恶意版本，覆盖 npm+PyPI…；pull_request_target 是 CI 致命弱点（PR 触发可信工作流可读 secrets）；GitHub Action…

### 吾爱破解 2026 春节解题（1 篇）
- 吾爱破解 2026 春节: Native verifyAndDecrypt 反馈链 + WASM 0x2026 次 SHA-256 + Frida hook 64 字节比较点。；Native 验签成功判定要找 h1.a.S((byte[]) obj) == null 这种"成功条件"；WASM crypto…

### 2026 安全研究（1 篇）
- 银狐木马样本分析，PEB 遍历模块 + API Hash 还原 + ShellCode Loader 动态调试 + C2 提取。 → `PEB InMemoryOrderModuleList syumineyt.top syumingeyt.top API Hash 还原`；银狐木马典型用 PEB 隐藏 API 调用；API Hash = 常见 ROR13 算法；动态调试需自实现 Loader；C2 通…

### 2026 春节活动（1 篇）
- 2026 春节解题红包 8 道题 WP 复盘，Android/IDA/Lua/wasm/UPX 综合实战 + AI 辅助分析。 → `题目7 base64 码表 a-zA-Z0-9?! 内存地址 1295903`；逍遥模拟器+JEB 是 Android 动态调试标配；AI 翻译字节码/WASM 提速但要给足上下文；UPX 自动脱壳工具链成熟；…

### 2026 楚慧杯（1 篇）
- 楚慧杯流量分析：识别冰蝎3 behind.php + 蚁剑 .shE11.php + AES 解密 + base64 二层解码找 key 和 flag。；冰蝎3 与蚁剑的特征 URL 识别是流量取证基础；AES 加密 + base64 双层编码是常见 WebShell 通信模式。

## Connections

- 上层索引：[[ctf/source-corpus--des-ctf-knowledge]]
- Source: [https://github.com/Dest1ny-Sec/Des-CTF-Knowledge](https://github.com/Dest1ny-Sec/Des-CTF-Knowledge)

## Open Questions

- 本页为索引层，题目细节请回源；若某场比赛多题值得展开，再单独建 `ctf/<比赛>.md` 实体页。
