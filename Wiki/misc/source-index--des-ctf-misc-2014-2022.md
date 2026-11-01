---
title: Des-CTF 语料索引 — Misc（2014-2022）（78 篇比赛 WP）
tags: [source-index, corpus, des-ctf, wp, misc]
category: misc
triggers: ["TPU", "CCF-BDCI", "VGG", "BMODEL", "算能芯片", "LightGBM", "EMA", "ChaMd5_Venom", "base64", "USB-HID", "ARCHPR", "malware", "angr", "jjencode", "ssti", "uaf", "NLP", "Nezha", "SWA", "人群密度估计"]
created: 2026-11-01
updated: 2026-11-01
sources: [https://github.com/Dest1ny-Sec/Des-CTF-Knowledge]
---

# Des-CTF 语料索引 — Misc（2014-2022）

## Summary

外部语料库 `Dest1ny-Sec/Des-CTF-Knowledge` 中 **78 篇 Misc（2014-2022） 比赛 WP**（覆盖 60 场比赛）的结构化索引。
每条记录保留「比赛 → 题目/攻击链 → 关键 payload → 要义」，原始全文未镜像，需细节时按 `contest` 回源 GitHub 的 `CTF大赛WP集合/articles/<文件名>.full.md`。

## Key Points

- 高频考点（按 tags 词频）：TPU、CCF-BDCI、VGG、BMODEL、算能芯片、LightGBM、EMA、ChaMd5_Venom、base64、USB-HID、ARCHPR、malware、angr、jjencode、ssti
- 高频比赛：CCF BDCI 2022 (第十届CCF大数据与计算智能大赛)、CCF-BDCI、祥云杯、DataCon 2020、DataCon 2021、FE-CTF 2022、数字中国、红明谷
- tags 来自上游 frontmatter（允许跨赛道噪声，如 Web 页出现 ret2libc），仅作检索线索
- 用法：Grep 本页的比赛名或考点词 → 命中条目 → 需要细节时回源原文；不要在本页上做二次推理

## 按比赛分组

### 2014 SSCTF（1 篇）
- Windows 7 SP1 x86 下 win32k!EngPaint 越界 BSoD 漏洞剖析 (CloverSec bee13oy 作品)。 → `NtGdiSetBitmapAttributes(hBitmap1, 0x8f9)`；早期 win32k 图形子系统有大量未公开 IOCTL/结构偏移；现代 Win10/Win11 已修复此类 ENG 路径。

### 2019 D3CTF（1 篇）
- D3CTF 2019 bet2loss 智能合约赌博 + croupier 私钥泄露 + AirdropCheck 薅羊毛 + keccak256 赌博熵预测。 → `croupier 0xACB7a6Dc0215cFE38e7e22e3F06121D2a1C42f6C balance > 300000`；bet2loss 类赌博合约 require balance > 阈值；AirdropCheck 给新用户 1000 是薅羊毛入口…

### DataCon 2020（2 篇）
- DataCon2020僵尸网络分析：蜜罐HTTP日志+LD_PRELOAD ELF+xgoahead → `sub_5C4(c) = chr(ord(c) - 98) # 字符减98`；蜜罐日志过滤用长URL+LD_PRELOAD+ELF头识别攻击payload
- DataCon2020恶意软件分析：LIEF+正则+CFG图可视化 → `m32_pat = re.compile(b''\x55\x8b\xec[^\xc3]*\xc3'') # 函数头`；PE section熵+操作码序列+CFG是恶意软件家族分类特征

### 2020 Hack-A-Sat（1 篇）
- VirtualAGC 复现 Apollo AGC 的 DSKY 命令，从 ROM rope-memory 中挖出被篡改的 PI/16 DP 值。；历史向 CTF 考察领域知识（AGC 八进制 + Bank 寻址 + DP 浮点 + V/N 命令），解题关键是搞清 V27N02…

### 2020 HackASat（1 篇）
- HackASat 2020 资格赛 beckley 题复现，KML + Skyfield 库 + Google Earth 三件套算卫星拍摄角度。 → `remote.kml Skyfield 库 LookAt 元素 longitude/latitude/altitude`；HackASat 是美国国防部太空 CTF；KML 是 Google Earth 数据格式；Skyfield 是 Python 卫…

### 2020 PlaidCTF（1 篇）
- PlaidCTF 2020 mojo：Chrome 沙箱逃逸，Mojo PlaidStore 接口 IsRenderFrameLive 悬空指针 + 越界读 OOB + 跨 frame UA…；Chrome Mojo 接口的 RenderFrameHost 悬空指针是经典沙箱逃逸向量；跨 frame 引用是 UAF 触发器…

### 2020 TryHackMe（1 篇）
- TryHackMe Looking Glass：5000 SSH 端口二分爆破 + Jabberwocky 诗 + sudo reboot 提权到 humptydumpty + alice… → `wall $(cat /home/tweedledum/poem.txt) # 注入点`；真实 Linux box 中 wall 命令注入是经典 sudo reboot 提权路径；sudo -h host NOPASSW…

### 2020 祥云杯（2 篇）
- MMS 工控流量 tshark 提取 exe → 反调试脱壳 → XOR 解出 Python 还原脚本 → AES/CBC padding 爆破 key 倒推 IV → `data = b''; [data += bytes.fromhex(line.strip()) for line in open('data.txt')]; open('test…`
- 祥云杯决赛多方向题集：SoapClient SSRF 登录 + XXE expect + mp3 LSB + 自定义 VM + UAF + 线程环形越界 + XOR 密钥 + ORW。；SOAP SSRF 头注入（user_agent CRLF）+ expect:// 协议是经典 PHP CTF 套路；多线程链路越…

### 2021 圣诞活动（1 篇）
- 16-puzzle 数字华容道 A* 自动求解 → `5, 1, 6, 8, 2, 11, 7, 13, 15, 14, 3, 4, 10, 16, 12, 0`；N-puzzle 是经典 AI 搜索问题；曼哈顿距离是 admissible heuristic

### DataCon 2021（2 篇）
- DataCon2021域名体系安全：27万域名分类+并查集团伙分析+HTML相似度优化 → `\b[CA证书Alternative Name]\b / \b[HTML相似度>90%]\b # 7种关联边`；大规模域名分析需用并查集+LCS优化+多进程并行
- DataCon2021物联网固件angr+def-use自动漏洞挖掘多架构 → `RDA.tracking=arg; if arg@ro: NOT_EXPLOITABLE`；angr RDA+CFG可跨mips/arm/x86分析，PyPy加速

### ZJCTF 2021（1 篇）
- ZJCTF 2021 Triple Language 逆向：x86 主程序 + Unicorn 模拟 MIPS+ARM 双架构 + 自定义 hook 改寄存器 + CRC32 表爆破前 4… → `MIPS_CODE @0x10000 + uc_reg_read(t1..t6) == {0x2F2E,0x282A,0x2C42,0x2A8A,0x13E0,0x36D4} /…`

### 2021 安洵杯（1 篇）
- ChaMd5 Venom安洵杯全方向:Web反序列化/Misc CRC32+Tupper/Pwn SROP+chunk合并+FSOP。 → `Phar(thin\process\pipes\Windows) + crc32 reverse + Tupper k=1251077... + SROP canary+elf_b…`；ThinkPHP5 Phar反序列化windows\process\pipes\Windows链触发Request::system…

### 2021 湖湘杯（1 篇）
- 第七届湖湘杯 leaker 数字水印题，RGBA Alpha LSB 提取 01 矩阵 + 2x4 块 ASCII 编码还原。 → `Alpha LSB 255/254 周期 76 行 2x4 矩阵 ASCII 编码`；数字水印 = 抗修改隐写术，重复填充实现任意位置可读；RGBA Alpha 通道 255/254 区分是 LSB 隐写信号；2x4…

### 看雪·众安 2021 KCTF秋季赛（1 篇）
- 看雪众安KCTF2021秋Q5《拔刀相向》数据流混淆：DARTS并行+Codelets打断控制流+多项式商环RSA+Feistel → `blockHasher:xorshift+0x2545F4914F6CDD1D;Poly:t^24+51*t^21+...+t+38 mod 61;phi=(61^11-1)*(6…`；数据流混淆（Dataflow flattening）+ DARTS并行codelet调度干扰控制流分析；多项式商环RSA用Sage…

### 2022 1024 羊群之谜 (M01N 队出题)（1 篇）
- 1024 羊群之谜 3 关：DNS TXT 邮件协议 + 改 map_id 简化 + ptrace 在 write() 系统调用时劫持 PC 跳 puts 输出未覆盖的 flag M01N{… → `berichyang.group TXT spf1 a mx ?all / map_id=80001 简化 / ptrace 注入 → write 时改 PC=0x40058E →…`

### 2022 智能网联汽车漏洞挖掘赛（1 篇）
- 2022 智能网联汽车：UART 帧格式 + USB 键盘流量 + Hill 密码 ngram → `tshark -r usb.pcap -T fields -e usb.capdata 'usb.data_len == 8' > usb.txt`；智能网联汽车安全 = UART + USB + CAN-bus + OTA；UsbKeyboardHacker 是经典工具

### 2022 BCTF（1 篇）
- BCTF 自动驾驶 — Stanley 横向控制 + PID 纵向控制 + Lane Detection targeted attack + Adversarial NPC GPS 缩放攻击…；自动驾驶对抗攻击: 横向 Stanley + 纵向 PID 控制;GPS 注入通过修改 npc JSON 即可;车道线检测可被 a…

### 2022 ByteCTF（1 篇）
- ByteCTF 2022 Silver Droid — 攻击者提供腾讯 COS POC URL + 模拟器启动 + am broadcast SET_FLAG 走 root 旁路 expor…；WebView shouldOverrideUrlLoading + shouldInterceptRequest 是 Andro…

### CCF BDCI 2022 (第十届CCF大数据与计算智能大赛)（9 篇）
- 台州学院innovation团队用VGG-INT8+图像增强在算能TPU上B榜第五。 → `VGG-INT8 + 576×768 + 锐度×1.5 + 亮度×1.5`；模型选择需兼顾精度和泛化 — A榜10张图选VGG，到A榜1201张MCNN精度严重下滑；量化迭代次数对精度影响不大(趋于平稳)；…
- 高级工程师单挑用VGG+精确尺寸组合在算能TPU上拿下三等奖。 → `VGG + 预处理最大780×780 + 最小144×144 + 模型输入144×144`；模型加速首先选对基础模型 — 直接转FP32bmodel对比4个模型(CANNet/CSRNet/MCNN/VGG)选VGG;ba…
- 冀科数字团队用Spark+LakeSoul+coalesce在数据湖流批一体赛道上拿三等奖。 → `spark.sql.shuffle.partitions=20 + spark.default.parallelism=12 + coalesce合并`；流批一体场景中字段合并用coalesce可同时处理null字符串和空值,比RDD算子更清晰高效;并行11个parquet任务+Da…
- 中物院+东南+海康+南大+上海工程技术跨单位团队用Stacking树+神经网络集成在返乡预测拿三等奖。 → `对抗验证+LightGBM+CatBoost+XGBoost+MLP+TabTransformer+Stacking`；匿名数据无法业务理解时用暴力特征交叉+重复次数计数构造特征;对抗验证能识别训练集中的干扰数据(本例50000-59872为噪声);…
- 华中科大博士用遗传算法+Network Activations在TPU人群密度估计上第3名,得分252.14。 → `VGG + fp32bmodel + 格雷码遗传算法 + Network Activations访存指标`；TPU平台低FLOPs≠低推理时间 — depth-wise类(EfficientNet)在TPU上因访存带宽限制实际更慢;Net…
- 哈工大(深圳)研究生用RAKE+SimCSE+MemoryBank+R-Drop在小样本专利分类上A榜0.6371/B榜0.6023。 → `RAKE关键词 + MLM+SimCSE预训练 + MemoryBank + R-Drop + Mixup + 5折融合`；长文本小样本场景先用RAKE降学习难度;MemoryBank+BCELoss+对比学习防过拟合;R-Drop利用Dropout机制…
- 重邮+哈工跨校团队用GRTE+GlobalPointer差异化融合在工业知识图谱关系抽取B榜0.6671第2。 → `RoBERTa-wwm-large + UER-large + NeZha-large + FGM对抗 + SWA权重平均 + 5折融合`；长文本按标点拆分+多折融合能缓解长度限制;差异化模型(GRTE表填充+GlobalPointer)增加融合多样性;FGM+EMA+…
- 中国电信3人小队用同义三元组替换+滑动窗口+弱监督在工业关系抽取B榜0.666。 → `同义三元组替换 + 滑动窗口切分 + 弱监督Loss + FGM+EMA+R-Drop + 5折融合`；关系分布极不均(部件故障6000/检测工具28)时,小类别同义替换阈值降低+三元组随机插入+句子随机重复可缓解;滑动窗口(以字/以…
- 汤振东+樊秋轩双人在LakeSoul源码加自定义版本号使乱序upsert并行可行,数据湖性能优化拿二等奖。 → `自定义Upsert版本号 + Future并行 + LakeSoul MOR表 + 读时合并`；LakeSoul等数据湖默认按系统时间戳决定合并顺序,无法乱序并行;改源码增userDefinedTimestamp使并行+乱序+…

### CCF BDCI 2022 数字安全公开赛（2 篇）
- 北工大硕士+中大博士用TF-IDF+集成学习做CVE漏洞三级分类,线上第3。 → `TF-IDF(降维) + LightGBM分类 + 三级标签分开训练`；漏洞分类三级标签分开训练比合并训练效果好;TF-IDF降维既降内存又加速训练;集成模型相对深度网络速度更快、性能更稳定;换榜后不易…
- 中科院计算所硕士用BERT+LightGBM集成在CVE漏洞分类上第5名,准确率0.4695。 → `BERT-6层-12头-768维 + focal loss + LightGBM×BERT ensemble`；5个字段分开训练 + 用未标注60000条做领域MLM预训练 + LightGBM二分类更强 + Bert+Bert多分类更强;f…

### 2022 CCF-BDCI（3 篇）
- 5 个中文+英文预训练模型集成 + 领域预训练 + AWP/SWA + 伪标签，小样本专利分类 CCF BDCI 2022 一等奖。；小样本学习的关键是数据增强（伪标签）+ 模型多样性（5 个不同架构）+ 鲁棒性优化（AWP+SWA+MSD）。
- 2022 CCF BDCI TPU 人群密度估计：4 模型网格搜索 + INT8 量化对比 + 选 VGG FP32 144×192 拿 B 榜第二。；网格搜索是模型选择标准方法；FP32 vs INT8 量化在精度敏感任务中 INT8 不一定更好。
- 金融交易图 3 阶频繁子图挖掘：C++ + mmap + OpenMP + 边属性标签化 + 排序分类 + 概率剪枝。；频繁子图挖掘的核心是减少比较次数，边属性标签化 + 排序 + 概率剪枝 + 并行是工业级 4 把刀。

### DataCon2022 域名体系安全（1 篇）
- DataCon2022 域名体系安全冠军 NDNS：8 类域名分类判定指标 + 5 关域名接管 (CNAME + TXT 归属权验证)。；CDN 域名为厂商 SLD 下的 CNAME 链 (如 admin.datacon.cdn.)；GFW 隐藏域名 IP ASO 集…

### DataCon2022 涉网犯罪分析（1 篇）
- DataCon2022 涉网犯罪分析冠军 N0nE429：WAF 日志+frame-tag 攻击者画像+同源关联 + Mirai IOC 自动化提取。；攻击者画像核心是建立指标体系 (is_dangerous/is_proxy/is_gov_targeted 等)；Mirai 反编…

### ECW CTF 2022（1 篇）
- ECW CTF 2022 三道逆向：Minifilter 驱动文件加密 + EFI Decompress Protocol UTF-16 + OpenGL hook.so LD_PRELOA… → `key = [data[0] ^ 1 ^ 0xff, data[1] ^ 1 ^ 0xfe, data[2] ^ 1 ^ 0x45, data[3] ^ 1 ^ 0] / ciph…`

### FE-CTF 2022（2 篇）
- FE-CTF Cyber Demon Blackbox：黑盒fuzz测试+EOF检测 → `b''\n'' # 触发返回`；黑盒题先试简单输入观察响应
- FE-CTF Possible Game：secret_key缺失+urandom初始化+汇编hlt → `echo much secret > secret_key # 绕过文件检查`；写secret_key文件绕过文件存在性检查

### HITCON CTF 2022（1 篇）
- HITCON CTF 2022 Fourchain Hypervisor：E1000 MMIO fake pDevIns劫持callback → `R3 pDevIns->pfnWriteCallback = system; "touch /tmp/456"`；VirtualBox E1000 pDevIns->pfnWriteCallback可劫持为system

### HITCTF 2022（1 篇）
- HITCTF 2022 re3 debug_maze 逆向：IsDebuggerPresent 抗反调试 + call $+5 花指令 + PEB 找 kernel32 + SM3 模拟 G… → `call $+5; mul eax; cmp eax, 51h / sub_401D10 XOR 自解密 / DEBUG_ONLY_THIS_PROCESS / int 3 tra…`

### HKCERT CTF 2022（1 篇）
- HKCERT 2022 Misc：z3求解Minecraft mycraft+getRotation种子 → `z3.Solver + xorshift seed + weighted-baked-model`；Java Random种子可由x,y,z坐标异或重建

### Hack The Boo 2022 (HTB)（1 篇）
- HTB Hack The Boo 2022：10题Halloween主题（Forensics+Rev+Pwn+Web） → `olevba.py invitation.docm → base64 -d → HTB{5up3r_345y_m4cr05}`；olevba是提取Office宏的标配工具

### 2022 MRCTF（1 篇）
- MRCTF 2022 Writeup: JPEG Huffman 手动解码 + 4-bit flag 提取 + AI 口罩对抗样本扰动 (checkSkin + checkMask 双重绕过… → `/`；/

### 2022 Misc CTF（1 篇）
- CTF Misc zip 压缩包分析 — 伪加密 (1400XX0008 改 9) + fcrackzip 暴力破解 + CRC32 5 字符爆破 + 明文攻击 (hex 对比) + lin…；zip 伪加密是改目录 14 00 XX 00 08 中 XX 第 1 位 (0/1 切换);CRC32 爆破只适合 ≤5 字符;…

### N1CTF 2022（1 篇）
- N1CTF 2022 cppmaster 逆向：LLVM Pass dump class 结构，deque<map<unordered_map>> 三层嵌套 STL 容器 + 动调 inpu… → `deque 三件套 (map/size + start/finish iterator) / vector 三指针 (start/finish/end_of_storage) /…`

### NCTF 2022（1 篇）
- NCTF 2022 ezshellcode：父子进程 ptrace 注入绕过 close(0/1/2) + seccomp 沙箱，父进程 PTRACE_ATTACH+SYSCALL+GETR… → `echo 0 > /proc/sys/kernel/yama/ptrace_scope / shellcode = shellcraft.ptrace(0x10,PTRACE_AT…`

### RCTF 2022（1 篇）
- RCTF 2022 F61d 全方向多题 WP：PWN (diary encrypt 错位 fake chunk / bank free_hook 8 次 add)、Web (Jinja2… → `encrypt(0,4,0x6) 错位 0x6 字节加密触发 fake chunk + p64(ogg=libcbase+0xe3b01) + a'*0x2e8`

### 2022 Realworld CTF（1 篇）
- Realworld CTF 之 hso-groupie：xpdf 4.03 JBIG2Stream 整数溢出 + 堆风水覆盖 GList 后备存储 + 图灵完备 (全加器 + 5 种 bit… → `gmallocn(numSyms, sizeof(JBIG2Bitmap*)) 整数溢出后 syms[k++] = symbolDict->getBitmap(k) 堆溢出`

### 2022 SUSTF（1 篇）
- SUSCTF 招新题库，ChaMd5 Venom IOT/工控组，涵盖 LFSR/Browser/Web 三类。 → `lfsr(seed2, mask2, n2) gauss mask1`；LFSR 已知明文 + 短长度即可用高斯消元解掩码；浏览器 OOB 读写比传统 UAF 更具威力。

### TsukuCTF 2022（1 篇）
- TsukuCTF 2022 14 题 OSINT 杂烩：PDP-11 1971 复古二进制 → 日本 OSINT 找车站/机场/酒店/猫生年月日/开业日 → SSH 公钥寻 GitHub 用… → `pdp11dasm version 0.0.3 / 算 1+1 = 2 (題1) / ssh -T git@github.com -i ./a (Hi Ann0nymusTsuku…`

### 2022 jakespringer/angr_ctf 训练营（1 篇）
- angr-CTF 15 关系统训练，从 find/avoid 路径搜索到 SimProcedure 替换库函数避爆。；SimProcedure 替代 check_equals + veritesting 是处理 2^16 路径爆炸的标准姿势；hoo…

### 2022 picoCTF（1 篇）
- picoCTF Beginner picoMini 2022 全 12 题 — runme + nc + convertme + code + fixme1/2 + 4 个 level (s…；picoCTF 入门是综合小练习合集:Python 脚本分析、密码学 (XOR/md5)、代码修复 (缩进/语法)、二进制 (pr…

### 2022 东软杯 / ChaMd5 Neusoft NSSCTF（1 篇）
- 东软杯 8 题混合栈：SSTI 模板上传 + SQL 盲注 + 压缩包爆破 + DH bsgs + Java 素数 + 3 道 PWN + signal 驱动 VM。；signal 驱动 VM 是 RE 高级形态，sigaction(34-47) 各绑 handler (push/pop/add/…

### 2022 中国工业互联网（1 篇）
- 江苏省工控选拔赛 4 题：RSA 二次方程求 p + e=2^I-1 开方 + SHA256 8 位爆破 + modbus 流量 + upload 竞争条件。；工控场景下 RSA 密码学 + pcap 流量 + 竞争条件上传 + Hash 爆破是常见组合。

### 2022 华为杯第一届中国研究生网络安全创新大赛实网对抗赛初赛（2 篇）
- 华为杯研网赛初赛官方WP(一):hashCode+QLExpress+CSP+50x50DP+迷宫花指令+LZSS六题综合。 → `hashCode绕过串"guanzhujiarandundunjiechbO" + QLExpress+nashorn+java.lang.Runtime.getInputStre…`；hashCode=s[0]*31+s[1]可逆推;QLExpress第一参数是表达式但有黑名单,绕Nashorn ScriptEn…
- 华为杯研网赛初赛官方WP(二):JWT密钥混淆RS256/HS256+Coppersmith Hastad广播攻击+多项式参数化small_roots。 → `JWT算法混淆 + home={pathname:app%2e%6a%73} + file://协议 + 4组CRT+coppersmith(X=2^464)`；JWT密钥混淆攻击:RS256签发HS256验证,后端secret传公钥当HS256密钥;home={pathname:'app%…

### 2022 压缩包隐写综合（1 篇）
- 8 种压缩包隐写技巧：识别头/爆破/伪加密/CRC32 碰撞/已知明文/docx/注释/文件修复。；zip 标志位奇偶控制伪加密; 10 字节内可 CRC32 碰撞; docx/jar/ipa 都是 zip; 已知明文攻击靠 bk…

### 天融信教育杯 2022（1 篇）
- 杂烩：steghide 隐写 + base64 + PHPCMS 路径穿越 + SQL 注入 + 数组硬编码。；PHPCMS 路径穿越要拼 timestamp 字典爆破 avatar 路径；steghide 隐写密码是入门考点。

### 2022 安徽大学生攻防赛 (EDI安全)（1 篇）
- EDI安全2022安大攻防赛Misc 3题+Re 2题+Cr 3题,RSA三种解法全覆盖。 → `RSA(CRT+dpdq) + gcd(n1,n2) + LCG(s3-s2=a(s2-s1) mod p) + DFS迷宫`；RSA三类解法:已知dp/dq用CRT求m;两n共享p用gcd;多seed题用LCG逆推(s3-s2=a(s2-s1) mod p…

### 2022 工具介绍（1 篇）
- 用 Tabby (soot + Neo4j) 静态分析 Java 代码属性图 + Cypher 查询，自动化找反序列化 gadget 链和 hessian JDK-only 链。 → `/`；/

### 强网拟态 2022（1 篇）
- 强网拟态2022 W&M全方向9+题:Node原型链命令注入/SQL时间盲注/Jinja2 Unicode转义/JDBC MySQL_Fastjson反序列化/JMX RMI反序列化/fmt… → `json.command数组+constructor.prototype.argv0 + benchmark时间盲注 + __class__八进制 + JDBC autoDeser…`

### 2022 强网拟态(强网杯)（1 篇）
- ChaMd5 Venom强网拟态全方向8+题:PHP反序列化+全角绕/Jinja2/Node原型链/区块链签名/二分搜索/自定义VM。 → `全角字符替换 + __proto__+command数组 + ethereumjs-tx _Cal+rawTx + 二分搜索15次+VM指令`；PHP反序列化用全角字符替换a-z绕SSTI黑名单;Node原型链__proto__.command数组注入;区块链用ethere…

### 2022 强网杯（1 篇）
- 强网杯2022 ChaMd5 Venom全方向12题:admin绕/Phar+SSRF/Pickle+userdata/.inc spl_autoload/栈溢出mprotect/House… → `Phar反序列化+SSRF AdminShow+Pickle RCE + .inc spl_autoload + mprotect+rdx=7 + RSA e=3 AMM`

### 2022 数字中国（2 篇）
- 2022 数字中国车联网安全赛 best_agent，CAN 总线流量分析 + websocket 实时流量抓取 + git 信息泄露。 → `open_left 接口 + 重复 CAN 流量 + websocket on_message`；车联网 CAN 流量分析 + websocket 实时流量是车联网 CTF 核心；多次触发同操作找重复帧是 CAN 流量特征提取标…
- 2022 数字中国车联网初赛 6 题 WP，CANopen 协议 SDO 读对象解析 + 仲裁 ID 表查询。 → `设备类型 41f7 设备名 CANopenDemoLinux-9f1b 硬件版本 f957da98 软件版本 b0459595fd93`；CANopen 协议常用对象：0x1000 设备类型/0x1008 设备名/0x1009 硬件版本/0x100A 软件版本；SDO…

### 2022 春秋杯（1 篇）
- 2022 春秋杯春季赛 Misc/Crypto 4 题 WP，CRC 隐写 + VeraCrypt 爆破 + ICMP 流量恢复 zip + IP 末位编码。 → `春秋伽玛公众号"上课铃声" VeraCrypt 密码 aaaAAA111 IP 末位 38/39 → 0/1`；PNG IDAT 10000+ 块且 CRC 不正常是 CRC 隐写标志；VeraCrypt 密码本爆破可让 passware k…

### 2022 春秋杯冬季赛（1 篇）
- 春秋杯冬两游戏题：楠之勇者传/proc/self/mem写PLT注入shellcode+驯鹿游戏PyInstaller补pyc头反编译 → `../../../../proc/self/mem;elf.plt['write']=4327552;shellcraft.sh() base64;b64encode(asm(sh…`；/proc/self/mem可绕过沙箱向python plt写shellcode（PIE关闭时基址固定）；PyInstaller补…

### 2022 河南工业互联网（1 篇）
- 2022 河南工业互联网安全大赛复赛 3 题 WP，MMS fileOpen 读图 + hex 移位爆破 + modbus 异常检测。 → `flag{ICS-mms104} flag{RP2U5myhBI5m} modbus function 17 109 65 1 1 3 3 67`；MMS (Manufacturing Message Specification) 是工业自动化协议；modbus functio…

### 2022 浙江省信安赛（1 篇）
- 浙江省信安赛 6 题综合：mt_srand 时间种子预测 + USB HID 字典 + 仿射密码 + Coppersmith small_roots + ORW ROP。；mt_srand(time) 是 PHP shuffle 预测经典套路；Coppersmith small_roots 是已知 p…

### 看雪2022 KCTF秋季赛（1 篇）
- KCTF2022秋Q9《乡绅压迫》全misc：伪加密+云影+base32+JJEncode+艾特巴什+Unicode+pydumpck+多层base → `504b0102第5字节09改08伪加密;云影密码;base32;JJEncode;艾特巴什码;pydumpck;base85→base91→base36→base16`；ZIP伪加密=504b0102第5字节改08；多层编码识别顺序是CTF misc基本功

### 2022 第七届楚慧杯预赛（1 篇）
- 第七届楚慧杯预赛：mobile AES+misc VeraCrypt+BitLocker+stegsolve+改CRC → `flag{380605c6-7123-4f71-b573-601e8c4457b4};flag{6b1df900-1284-11ed-9fa7-5405dbe5e745};flag…`；综合取证常用：FFD9后藏文件+改图片高度+BitLocker密码恢复+stegsolve提取

### 2022 红明谷（2 篇）
- 第二届红明谷杯题解（三），RTPS 协议 RSA/AES 流量分析 + USB 键盘流量盲打还原。 → `USB HID normalKeys/shiftKeys 0x04-0x1d 字典 RTPS onboard/offboard 切换`；RTPS 协议是 ROS/DDS 中间件的实时通信协议；USB 键盘流量通过 HID Usage ID 0x04-0x1d 映射到…
- 第二届红明谷杯题解（二），SM2 biased nonce 攻击 + DPAPI masterkey/blob 内存取证解密。 → `dpapi masterkey SID=S-1-5-21-206512979-2006505507-2644814589-1001 password=123456 flag{Hid…`；SM2 nonce 偏差攻击用 HNP (Hidden Number Problem) 构造 LLL 格；DPAPI Master…

### 2022 西湖论剑（1 篇）
- 西湖论剑 2022 官方 MISC 5 题 (Volatility VRAM + Rabin / npbk 模拟器 + alpha 隐写 / Kylin CNVD-2022-78421 /…

### 2022 跳跳糖 (技术文章)（1 篇）
- php://filter 终极技巧 The End of AFR：使用 CSUNICODE/UCS-4LE 字符集链 + dechunk 单字符探测 + base64 截断，绕过几乎所有 d… → `php://filter/convert.base64-encode/convert.iconv..CSISO2022KR/convert.base64-encode/dechun…`

### 2022 香山杯（1 篇）
- 香山杯初赛 4 题：PHP 反序列化 POP 链绕 preg_match + rot23 + 内存 dump ou.exe + Chaquopy XXTEA 9 round。 → `Welcome->H4ck3r->G00d sort /[!q]1[!q][!q] + Chaquopy hello.py + XXTEA-like 9 round`

## Connections

- 上层索引：[[ctf/source-corpus--des-ctf-knowledge]]
- Source: [https://github.com/Dest1ny-Sec/Des-CTF-Knowledge](https://github.com/Dest1ny-Sec/Des-CTF-Knowledge)

## Open Questions

- 本页为索引层，题目细节请回源；若某场比赛多题值得展开，再单独建 `ctf/<比赛>.md` 实体页。
