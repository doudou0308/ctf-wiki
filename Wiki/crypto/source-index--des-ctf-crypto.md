---
title: Des-CTF 语料索引 — Crypto（120 篇比赛 WP）
tags: [source-index, corpus, des-ctf, wp, crypto]
category: crypto
triggers: ["RSA", "Coppersmith", "LLL", "SageMath", "AES-CBC", "lcg", "gcd", "Lattice", "MT19937", "side-channel", "sage", "ECDSA", "LWE", "AES-ECB", "皮尔逊相关系数", "finite-field", "lattice-attack", "partial-key-exposure", "SPN", "Matrix"]
created: 2026-11-01
updated: 2026-11-01
sources: [https://github.com/Dest1ny-Sec/Des-CTF-Knowledge]
---

# Des-CTF 语料索引 — Crypto

## Summary

外部语料库 `Dest1ny-Sec/Des-CTF-Knowledge` 中 **120 篇 Crypto 比赛 WP**（覆盖 105 场比赛）的结构化索引。
每条记录保留「比赛 → 题目/攻击链 → 关键 payload → 要义」，原始全文未镜像，需细节时按 `contest` 回源 GitHub 的 `CTF大赛WP集合/articles/<文件名>.full.md`。

## Key Points

- 高频考点（按 tags 词频）：RSA、Coppersmith、LLL、SageMath、AES-CBC、lcg、gcd、Lattice、MT19937、side-channel、sage、ECDSA、LWE、AES-ECB、皮尔逊相关系数
- 高频比赛：网鼎杯、强网拟态、N1CTF、LitCTF 2025、数字中国积分争夺赛、红明谷、领航杯、公众号文章
- tags 来自上游 frontmatter（允许跨赛道噪声，如 Web 页出现 ret2libc），仅作检索线索
- 用法：Grep 本页的比赛名或考点词 → 命中条目 → 需要细节时回源原文；不要在本页上做二次推理

## 按比赛分组

### WHCTF 2017 (Wani Hack)（1 篇）
- WHCTF 2017 Wbaes 9.3 MB MOV 混淆白盒 AES，DFA 失败后改用"已知密钥 + 文件全字节扫描可解 16 字节块"绕过白盒保护，flag: Whc7f&Fl@pp… → `key = b'whctf&flappypig!' / ciphertext = 13cb006c2994de6da1b81ba399206290 / plaintext = Wh…`

### 2020 公众号文章（2 篇）
- 连分数攻击小d RSA，含Wiener扩展+Verheul-Tilborg+Pell方程 → `def transform(x,y): res=[]; while y: res.append(x//y); x,y=y,x%y`；小解密指数RSA用连分数逼近k/d，d<N^0.292时仍可破
- Coppersmith攻击小e+部分p泄露+SHA256前缀爆破综合 → `f.small_roots(X=2^kbits, beta=0.4)[0] # sagemath`；已知p高位时用Coppersmith在Zmod(n)上多项式小根求低位

### Paradigm CTF 2021（1 篇）
- Paradigm 2021 Lockbox 通过 assembly 链式调用 6 个 stage 合约，分别用 blockhash、ecrecover、uint16 溢出、数组排序、kecc… → `uint16(0xff1c) + bytes32(0x1f9c551056...) r + bytes32(0x6e95dc...) s + bytes32(keccak('cho…`

### 2021 WMCTF（1 篇）
- WMCTF 早期 ChaMd5 Venom 战队杂烩 WP，AES-CBC 密码爆破 + OCB 模式 oracle 标签伪造 + 32 元子集和 + Rabin 链解 e=4096 + L… → `pmac_admin = my_pmac(bytearray(b'from admin')) / M = matrix(ZZ, m); v = M.BKZ(blocksize=22…`

### 0CTF/TCTF 2022（1 篇）
- 0CTF 2022 五道官方密码题，覆盖 DLP/AEAD/RSA/RLWE/Feistel+SPN；顶级 CTF 密码学题都是最新研究 + 论文级攻击；Coppersmith / Gröbner / DFA 是常见方法

### 2022 CCF BDCI SO-FAST TPU 密度估计（1 篇）
- 2022 CCF BDCI TPU 密度估计冠军 SO-FAST：CSRNet + INT8 量化 + 4N Batch + 2×2 图片划分 + 推理加速。；TPU 部署 5 步流程: 模型选择→INT8 量化→量化图片筛选→推理加速 (尺寸+4N Batch)→部署实测; 4N Bat…

### 2022 CCF BDCI 龙盈战队 知识图谱关系抽取（1 篇）
- 2022 CCF BDCI 知识图谱冠军 龙盈战队：DeBERTa + grte/GPLinker 双模型 + 外部数据伪标签 + 投票融合 + EMA。；关系抽取联合模型: grte (填表) + GPLinker (GlobalPointer) 互补, 投票融合优于概率融合; 按样…

### 2022 USTC Hackergame (中科大)（1 篇）
- USTC Hackergame 0x02 量子计算 + 拼图 + RSA + 旅行商 + LaTeX 渲染 → `flag{it_works_like_magic_e2a53e77e7}`；USTC 信安赛题目趣味性强 + 知识面广；量子计算 / 旅行商 / LaTeX 都是冷门考点

### 2022 巅峰极客（1 篇）
- 巅峰极客 2022 6 大题：Padding Oracle + Web + ECC + 三道 pwn → `y² = x³ + a*x + b 联立解出 a,b`；Padding Oracle 永远可破；ECC 加法公式代数化是基础；PROTECT_PTR 用 xor 绕过

### 2022 柏鹭杯 (Polaris)（1 篇）
- 2022 柏鹭杯：e=3 RSA 立方根爆破 + XXE + pwn function pointer 覆盖 → `for k in range(200000000): if gmpy2.iroot(c+n*k, 3)[1] == 1: print(long_to_bytes(iroot(c+n…`；e=3 RSA + m^3 < n 直接开根；XXE OOB 是经典；function pointer 覆盖是 pwn 入门

### 2022 第三届网鼎杯 玄武组（1 篇）
- 网鼎杯 2022 玄武：base62 换表 + Goldwasser-Micali 二次剩余 + 内核 pwn UAF → `base62.decode('7dFRjPItGFkeXAALp6GMKE9Y4R4BuNtIUK1RECFlU4f3PomCzGnfemFvO')`；Goldwasser-Micali 加密是 QR 加密经典；内核 pwn 用 msg_msg/sk_buff/pipe_buffe…

### 2022 美团 MTCTF 初赛（1 篇）
- 美团 MTCTF 6 大类：math + RSA + web + pwn + reverse + misc → `p = iroot(n/gift, 2) 爆破 1000 个候选`；gift = 高精度小数 → RealField 还原精确 n/gift → iroot 开方爆破 p

### 2022 ASIS CTF（1 篇）
- ASIS CTF Quals 2022 — 8 道 Crypto 集锦：Binomial 展开 + Lattice 哈希碰撞 + Fixed p DLP + Joye-Libert CRT…；ASIS 风格是全覆盖所有方向的高质量 Crypto;Joye-Libert 是教科书方案;Pohlig-Hellman 小因子+…

### CryptoCTF 2022（1 篇）
- 2022 CryptoCTF复现第一部分,涵盖Congruence同余式+AES已知明文攻击+3DES meet-in-the-middle+RSA多素数等经典密码学考点。 → `CCTF{f1nDin9_In7Eg3R_50Lut1Ons_iZ_in73rEStIn9!}`；任何(a,b)满足(ar+bs)%q=0时(ax+by)%q=0都成立,说明(x,y)与(r,s)同余,可设x或y为getPrim…

### DownUnderCTF 2022（1 篇）
- DownUnderCTF 2022 14题writeup：OFB+RSA+NoSQL+Symlink+SQLi+Python → `ofb ct2[i] XOR ct1[i+1] = pt2[i] # 已知前缀还原`；OFB可利用前缀+两次IV构造完整明文；RSA区间预言机二分

### 2022 GDG Algiers CTF（1 篇）
- GDG Algiers CTF 2题：矩阵对角化DLP+多项式LWE → `K = discrete_log(G(vP[11]), G(vD[11]))`；矩阵对角化可破解特征值加密；多项式+线性方程组可恢复字符

### HKCERT CTF 2022（2 篇）
- HKCERT 2022 Easy Crypto：DLP+Cookie翻转+负指数RSA恢复N → `gcd(c2*(m2*c1+1-m1*c1)^2 - c1^2, c3*(m3*c1+1-m1*c1)^3 - c1^3) # 求N`；负指数e=-1/-2/-3加密可构造已知消息恢复模数N
- HKCERT 2022 Harder Crypto：Windows批处理AES+双密钥密文对比 → `已知明文+密文对+多密钥差异`；批处理文件实现AES是新颖密码学题形式

### HackerGame 2022 (出题记)（1 篇）
- HackerGame 2022 出题记：AES-CBC chosen ciphertext+仿射CRC → `crc(x) = M*x + C in GF(2)`；CRC可表示为GF(2)上仿射变换；AES-CBC chosen ciphertext可构造任意块

### 2022 KCTF春季赛（1 篇）
- ydmp 原点磨盘密码：平方+公倍数+浮点误差+暴力 96^4 组合，4 元变量 sage 因子分解 + GCD 求唯一解。；自定义数学密码的破解依赖 gcd/factor + 暴力枚举；浮点误差处理是工程关键。

### 2022 KITCTF（1 篇）
- KITCTF 2022 boomer prime-guesser 1/2 复盘，8/16-bit RSA 弱密钥枚举。 → `FINDING Q 模式识别 + 二分搜索 q`；当 q < 2^k 时，q = 2^k + 1 转换点能定位 q 真实值。

### 2022 LakeCTF（1 篇）
- LakeCTF 2022 社工 + Crypto：通过 ProtonMail API 公开查询接口拿到 PGP 公钥用于后续攻击。 → `requests.get("https://api.protonmail.ch/pks/lookup?op=get&search=epfl-ctf-admin2@protonmai…`；任何 PGP 公钥服务器都允许陌生人拉取目标公钥；私钥安全完全依赖持有者。

### 2022 N1CTF（3 篇）
- N1CTF 2023 guess 题用 Soufflé 静态分析引擎 + Datalog 推理规则恢复 flag 序号。 → `ord() 推 symbol table 序号 + __LINE__ 动态调整`；Soufflé 等静态分析引擎的 symbol table + 推理规则是新型 CTF 出题方向。
- N1CTF 2022 Crypto: RSA 变种 (pubkey=(s, t, n), enc(m)=(m*r^s, m*r^t)), 字节级爆破 m (0-255) + xgcd 求 r… → `/`；/
- N1CTF 2023 Crypto: HHD ECDSA 攻击 (lattice 3x3 + BKZ block_size=25 + recentering 2^127) + message… → `/`；/

### corctf 2022 (狼组复现)（1 篇）
- 狼组出品的 corctf 4 道密码学复现：fizzbuzz100/101 RSA + 改进 LSB oracle 盲注 + 多元二次 Secret Sharing 代数化简 + 维吉尼亚… → `flag = ct * inverse(w, n) % n (w=907 prime 10-bit) / c = (L[6]-(L[2]+L[4]+L[5]-2*(L[0]+L[1…`

### 2022 m1z0r3CTF（1 篇）
- m1z0r3CTF 2022 三道密码学题：LCG 加密 + 链式 RSA + 16bit prime 拼接。 → `LCG 已知明文恢复 + 链式 RSA + 16bit prime`；LCG 加密 + 已知明文攻击是经典题型；链式 prime RSA 的"prime 链"是近年新趋势。

### 强网杯青少赛 2022（1 篇）
- 入门：Base64 + 字节奇偶互换 + XOR 32 + 大小写 -31/-32 偏移。；PNG 字节奇偶互换 + zfill(2) 重组是常见 stego 套路；大小写偏移 -31/-32 区分大小写。

### 望岳杯 2022（1 篇）
- RSA 小素数 + MD5 前缀爆破，5 重循环暴力枚举 printable 字符。；p/q 接近 33 位可直接读出当已知量；MD5 前缀爆破要选 4-6 字符效率最佳。

### 看雪2022 KCTF春季赛（1 篇）
- KCTF2022春Q12《尊严之战》：MT19937+自定义MD5+LWE矩阵乘mod 127+增广LLL格密码 → `init_genrand(0x91941ABB) seed爆破;A*l mod 127;LLL增广格64*2+48+1维;ANS[i][-1] in {127, -127};fla…`；LWE问题可构造增广格分层（err严格0/1，lr宽0-126），权重L=M^2+结果向量留痕

### 看雪2022 KCTF秋季赛（1 篇）
- KCTF2022秋Q4《治水搁浅》Crypto：RSA-3840 + 矩阵C求a + check前1920比特+p*i穷举恢复 → `RSA-3840;矩阵C;check函数取前1920比特转int;p*i穷举恢复;KCTF{B97E6ACEAC32A162F421D64410BB0853CD5856158224…`；RSA大数加密时若x.bit_length()<p.bit_length()需加iq穷举恢复前N比特

### 2022 红明谷（2 篇）
- 83 次方根求 RCE 字符串 + eval 触发 + LSB Oracle 还原 flag → `rce = b"__import__('os').system('ls')" + b'\xff' * (512 - len(rce)); m = gmpy2.iroot(bytes…`；RSA 加密函数中 eval 漏洞用 iroot 求整数根构造可执行 Python 字节；LSB Oracle 配合多步签名链路还…
- 第二届红明谷杯数据安全大赛题解（一），RSA Coppersmith 小根攻击 + phar 反序列化 Laminas 链。 → `Coppersmith small_roots X=2^100, beta=1, epsilon=0.03 phar 反序列化 LaminasViewResolver.Templa…`；m = M + k*r + 32 填充，k < n^(1/e) 是 Coppersmith 小根攻击标准配置；Laminas 框架…

### 2022 蓝帽杯（1 篇）
- 2022 蓝帽杯 PEM 文件 RSA 密码学详解。 → `PEM 解析 + RSA 参数构造`；PEM 文件本质是 base64 编码的 ASN.1 DER 序列，含 6 个 RSA 参数。

### 2022 西湖论剑（1 篇）
- 西湖论剑 2022 CRYPTO 官方 WP — MyCurveErrorLearn ECHNP 椭圆 HNP + LockByLock Pollard kangaroo + MyError…；HNP (Hidden Number Problem) 是密码学隐藏数问题模板;ECHNP/MIHNP/LWE 都基于 LLL;P…

### 2023 N1CTF Junior (Nu1L)（1 篇）
- Nu1L 30-bit r乘4096-bit secret模888-bit p,20次机会用5组CRT+LLL恢复secret。 → `LLL(6×6) + Delta=2^(4096+30) + CRT合并 + GCD校验`；30-bit与4096-bit数量级悬殊 → 30-bit部分可视为"短向量"通过LLL格密码规约恢复;secret远大于p所以必…

### 2023 香山杯 + 2023全国高校密码数学挑战赛赛道一（1 篇）
- 香山杯strange_hash倒推Rescue_Prime的2轮运算(input+0让check通过),核心技巧:加→减、乘→逆、幂→对角矩阵^(-1)。 → `Rescue_Prime + diag_matrix(tmp)^2 (平方) + diag_matrix(tmp)^(Inv-1) (1/3次方逆)`；Rescue_Prime 2轮运算每轮都包含"加Con→乘M→x^3→加Con→乘M→x^(1/3)",逆向时:加→减,乘→×M^…

### CakeCTF 2023 (出题人感想)（1 篇）
- CakeCTF 2023出题人感想，5题涵盖签名+15拼图+矩阵DLP+RSA-CRT → `det(M^r) = det(M)^r 在Fp上做Pohlig-Hellman`；矩阵离散对数可用det()降到Fp上DLP；RSA-CRT Garner中临时变量u可被pwn

### Firebird Internal CTF 2023（1 篇）
- Firebird CTF 2023：RSA partial info recovery+padding oracle → `random.seed(1337) # 固定随机种子`；固定seed RSA可预测；partial info论文+padding oracle组合

### 2023 Hack-A-Sat / Leaky Crypto（1 篇）
- AES T-表 Cache 侧信道攻击：100000 (明文, 时间) 数据 + 链式密钥字节还原 + AES 解密。；AES 软件实现 T-表 (SubBytes+ShiftRows+MixColumns 合并) 会因输入命中同一表项触发 Cach…

### 2023 Hack-A-Sat 2020 attitude（1 篇）
- 卫星姿态 attitude: 2500 恒星向量 + Kabsch 算法 SVD 旋转矩阵 + 四元数提交 20 次。；卫星姿态用四元数 (w,x,y,z) 表示避免万向锁；Kabsch 算法通过 SVD 找两组点集最佳旋转；rmsd 库 + sci…

### 2023 Midnight Sun CTF（1 篇）
- Midnight Sun CTF 2023 Finals 几道 reverse + crypto 综合题，多组 RSA + 共模攻击 + 整数算术 (a+b) 构造字符串执行。 → `/`；/

### N1CTF 2023（1 篇）
- ECDSA nonce 拼接泄漏 + LLL/small_roots + ECPrng pqr 分解 + RSA Coppersmith。；ECDSA nonce = msg/d 拼接是经典坑，用 LLL + small_roots 联合爆破；N=pqr 复合模数用 2…

### Realworld CTF 2023（1 篇）
- Realworld CTF 2023 Ferris_proxy：Rust 协议逆向 (有符号) + pcapng 全流量 rc4 解密 + RSA 加密 AES 密钥交换 + AES-128… → `d_lst[streamid] = concat(0x0100+RSA(rand_self)+RSA(rand_other)) + SHA256(aes_key) + AES_CB…`

### SECCON CTF 2023 Quals（1 篇）
- SECCON CTF 2023 Quals Bad-JWT：自研 JWT alg 字段未白名单 + prototype chain attack + 算法名 'constructor' 触发… → `header = {"typ": "JWT", "alg": "constructor"} + body = {"isAdmin": true} + jwt.signature =…`

### 2023 TetCTF（1 篇）
- TetCTF 2023 Neobeo 综合：负数赌场 + biased PRNG + 1-byte nonce ECDSA LLL + AES-CBC 位翻 LLL + PostgreSQL… → `{"Command":"Bet","Amount":-10**100}`；整数溢出 (int 类型足够大) + 弱 PRNG 种子 (seed=2023) + 1 字节 ECDSA nonce (256^…

### 2023 idekCTF（1 篇）
- idekCTF 2023 高分密码学题，Galois 域上多项式共轭变换 + Freshman's Dream 还原。 → `Frobenius 幂 + Freshman's Dream + 32 共轭爆破`；有限域上 (a+b)^p = a^p + b^p 的 Freshman's Dream 是处理 Frobenius 幂的关键。

### 2023 ångstromCTF（1 篇）
- VP-Union ångstromCTF 2023 第二份 WP，crypto 比重更大，含 RSA 二方程解、homomorphic oracle、伪 set 交为空 trick。 → `caesar: c = "rtkw{cf0bj_czbv_nv''cc_y4mv_kf_kip_re0kyvi_uivjj1ex_5vw89s3r44901831}" shift=…`

### 2023 全国大学生信息安全竞赛(信安国赛) + 伽玛实验场（1 篇）
- 北工大胡俊老师设计信安国赛可信度量题,3队解出:东北大学NEX一血/F1sh二血/北工大三血。 → `SM3(PCR旧值//digestValue) + 2^4=16种组合 + calculate_context_sm3(buf,len,out)`；可信计算TCM PCR寄存器写入原理:newPcrValue = SM3(oldPcrValue//digestValue);4种…

### 春秋杯2023春季赛（1 篇）
- 春秋杯2023春季赛crypto三题：Pell方程+恶意ECDH+超椭圆曲线Jacobian → `continued_fraction(sqrt(1117));flag1=(enc1-1)//n^2*inverse(233,n)%n;z = M1 - w*t*G - a*x*M…`；Pell方程连分数+ECDHE参数已知时逆推k2+超椭圆曲线Jacobian是近年crypto难点

### 2023 江苏省首届数据安全竞赛（1 篇）
- RSA e=13 低指数广播 + hint 填充 + Coppersmith 解小根分解模数。；pad 函数只在尾部填 0 可让 hint2 = hint1 * pad 高位已知；Coppersmith 适合解模 p 下的小根…

### 熵密杯 2023（1 篇）
- Gitea 检测 openssl 硬编码随机数 → TLS 解密 → SM2 临时密钥派生 → 恢复私钥。；openssl 改 drbg_lib.c 是经典后门；derive_from_time 用 SHA256(seed) 派生临时密钥…

### 2023 第十六届全国大学生信息安全竞赛(信安国赛)初赛 + 伽玛实验场（1 篇）
- 安徽问天量子设计信安国赛BB84量子密钥分发赛题:偏振对基+误码筛+线性同余取密钥+流密码异或。 → `BB84偏振协议 + 100%纠错 + 一次一密 + 流密码异或 + 线性同余(A=1709,B=2003)`；BB84协议:4种偏振态(0°/45°/90°/135°=H/V/±/+),Alice随机选基+随机比特,选错基时误码;有效探测=…

### 羊城杯 2023（1 篇）
- 4 题：RSA p^4 + AMM 开方根 + DES3 弱密钥 + MT19937 状态还原 + LLL LWE。；p=X^a+s 时 ab=iroot(n,a) + 因子爆破可解；MT19937 状态重组是经典 Python random 攻击…

### 2024 数信杯北部赛区（1 篇）
- 数信杯北部综合：XTEA 32 轮字节码逆向 + 凯撒+base64 + Log4j LDAP + SSTV 解码 + volatility Win7 取证 + frp socks5 + f… → `delta=555885348; sum1 -= delta; v1 -= (((v0<<4)^(v0>>5))+v0) ^ (sum1+k[(sum1>>11)&3])`

### 2024 春节活动 (52pojie)（1 篇）
- 2024 春节 web：ASCII art 读字 + CSS slider + LCG 模逆 + 大数因子 → `flag{5P3rF1ag3} / flag12{...}`；netcat 抓 banner 读字；CSS 变量 + slider 是 web 入门；LCG 模逆是密码学入门

### 2024 第四届网鼎杯（1 篇）
- 白虎组四题：GTP TEID 统计 + AES-ECB + php filter 侧信道 + zip 伪加密 + SPN 仿射 LLL 还原 keys。 → `S = chips 线性变换；S = LLL(chips) 还原 keys`；GTP 协议 TEID 是 5G 核心网隧道标识，tshark -e gtp.teid 提取；SPN 密码里 S 看作 chips…

### 2024 羊城杯粤港澳大湾区网络安全大赛（1 篇）
- 把 5 道 DASCTF 套题串成一篇——Hessian 曲线、自定义曲线离散对数、ECC log、AES 派生、bcactf 原题搬运、RSA sqrt+CRT，每题一段解题脚本。 → `PR.<d>=PolynomialRing(Zmod(p)); f=a*gx^3+gy^3+1-d*gx*gy; f.roots()`；Hessian curve 化简到 Weierstrass 是 SageMath 一行 EllipticCurve_from_cu…

### 2024 DIVER（2 篇）
- DIVER OSINT CTF 2024 复盘，日文 + 中文双语 WP 5 题。 → `Gmail/Google Maps + 推特 + OpenStreetMap + FlightRadar24`；OSINT 题的关键工具是 Gmail/Google Maps/推特/OpenStreetMap/FlightRadar24 五件…
- DIVER OSINT CTF 2025 复盘，作者 CLACKER 前田。 → `メタ読み（metadata reading）+ Sentinel-2 卫星图`；当 solve 数少的 hard 题，通过"问题描述的 solve 数"反推真实难度（メタ読み）。

### 2024 KCTF大赛（1 篇）
- BF-IBE + HMAC 密钥等价伪碰撞 + BLS12-381 光滑子群攻击 + LSB 多次 reset 拼方程。 → `admin@pubkey.com\x00 ; uname as HMAC key blocksize 64 pad equivalence ; E.order() = 3 * 11…`；IBE 类题目三大套路：HMAC 密钥块长等价、BLS 子群未校验、Pohlig-Hellman 光滑子群爆破。

### 2024 L3HCTF（1 篇）
- LLL DSA LCG 还原 + SPN 密钥爆破 + XTEA 变种 + Cyclotomic LLL + bytecode LCS。 → `M = Matrix(RR,15,15) ; t = abs(LLL[0][:5]) ; 32-bit TEA delta=0x114514 ; Cyclotomic 1024 阶…`；L3HCTF 风格偏论文题：DSA PRNG LLL / Cyclotomic 格基 / 自定义 SPN 都要懂。

### 2024 SandboxAQ（1 篇）
- SandboxAQ 后量子密码 CTF：Module-LWE 攻击 + Kyber-1024 PRNG 爆破 + X25519+Kyber768 混合 TLS 解密。 → `OQS_randombytes_nist_kat_init_256bit(entropy_input, NULL)`；后量子密码实现若 PRNG 种子空间过小，仍可通过暴力枚举恢复 shared secret。

### 2024 VxWorks 密码哈希 CTF（1 篇）
- VxWorks loginDefaultEncrypt 加权求和+魔数+32 位掩码导致大量碰撞，FLAG+4 字符 0.07 秒爆破。；VxWorks CVE-2010-2965 弱密码哈希无盐值 + 32 位掩码 (丢失 5 位) = 大量碰撞；防御要用 bcry…

### 2024 XCTF 第九届（1 篇）
- 第九届XCTF Tch3s时序攻击+弱PRNG爆破,识别srand(time(NULL))弱种子+遍历时间戳匹配测试明文+LD_PRELOAD劫持+GDB调用解密函数。 → `XCTF{Tch3s_PRNG_...}`；srand(time(NULL))是经典弱PRNG,精度仅秒级,几分钟内可遍历几年;LD_PRELOAD劫持srand+GDB调用…

### 2024 b01lersCTF（1 篇）
- b01lers CTF 多题 WP 合集，Polaris 战队对证书类 RSA 与格密码题给出系统化爆破思路。 → `t0 证书爆破 + Coppersmith small_roots`；RSA 弱密钥(e 极小、p 邻近、证书 t0 已知)攻击面比单纯 e*d 完整攻击更多；CTF 出题常借 X.509 包装弱密钥…

### 2024 全国职工职业技能大赛（1 篇）
- 2道密码题:crypto1不完整PEM私钥搜8181/8180分割提完整e+dp+dq爆破k求p,q得flag+PKCSv1.5填充;crypto2标准RSA但n=670位+38轮LCG h… → `e=0x10001 dp=0x00e85194...dq=0x6e2edb1b.../for k in range(1,e): if (e*dp-1)%k==0: if isPri…`

### 2024 工业信息安全技能大赛复赛(海南)（1 篇）
- 工业信息安全复赛车联网3题:功耗分析ByPass/手动CPA攻击AES-128/DH+SHA256+AES-CBC解密。 → `corrcoef<0.5 + AES-128 CPA + DH pow(g,5272,p) + sha256(key)+AES-CBC(iv=0)`；侧信道功耗分析找异常字符用corrcoef阈值0.5;AES-128 CPA看第一轮SBox输出与功耗相关性;RSA功耗分析倍增法…

### 2024 强网拟态（3 篇）
- AES-CFB 8-bit 单字节爆破 + reset 重置 iv/key 凑运气。 → `token = encrypt(f"{username}\x00{password}\x01\x02\x03", key) ; split('\x00') 切出多段时多余段归 _…`；AES CFB 8-bit 模式每个密文字节是独立流密码块，改 1 字节只影响后续；revenge 用不同 error 文案做 o…
- LCG 后门泄露参数 + 128 字节低 8 位泄漏用 LLL 格基规约恢复 128 位种子，复现 admin 密码。；LCG 的低 8 位 + 参数 + 足够样本可被 LLL 还原完整状态；Get Public Key 是典型后门设计。
- 强网拟态 2025 Unsafe Parameters：5 组 RSA 共享 d 触发 LLL 攻击，构造 6×6 矩阵恢复 d 分解模数解 AES。；多组 RSA 共享私钥是致命漏洞；e×d ≈ k×n 近似关系 + LLL 短向量是经典攻击。

### 2024 强网杯行业领域专项车联网赛道（1 篇）
- 强网杯车联网赛道:AES-CPA侧信道攻击+变种RC4 VIN加密+CAN总线数据最频值提取。 → `AES-128 CPA HW模型 + variantRC4 S流+K^(k%256) + CAN帧最频值`；AES-128 CPA侧信道:TRS文件ntraces/nsamples/dlen=32(16B PT+16B CT) → tra…

### 2024 数字中国积分争夺赛（2 篇）
- DSA LCG LLL + Boneh-Durfee small prime comb + Wiener 攻击 + AES-128-ECB。 → `sign: r = pow(g, nonce, p) % q ; s = inverse(nonce, q) * (sk * r + H(message)) % q ; L = M…`；论文级密码攻击：DSS LCG PRNG 攻击 ("Pseudo-Random Number Generation within…
- SM3 哈希扩展 + RSA p^4+tp 隐变量多项式 resultant small_roots。 → `sm3_hash_ext(64, "", known_hash, "") ; pad = 800000000000000000000000000000000000000000000…`；RSA 模数 p^4+tp 时可以把 a 当成未知数构造多项式环 mod n,Resultant GCD 是还原 m 的关键。

### 2024 春秋杯（1 篇）
- 2024 春秋杯夏季赛解题思路详解合集，ECC 离散对数 + RSA+AES HSSP + 格攻击。 → `ECC base point + pub + 模 M RSA 格攻击 case 排列爆破 SHA256 前 6 字符`；ECC 离散对数用 'pow(x2-x1, M-2, M)' 模逆 + c1-k*c2 还原明文；HSSP (Hidden Sum…

### 春秋杯2024冬季赛（1 篇）
- 春秋杯2024冬季赛D2十题混合：Python jail+字符高尔夫+线性方程组+PyVM逆向 → `def b(): def a(): yield g.gi_frame.f_back.f_back.f_back.f_back; g=a();g=[x for x in g][0];…`；Python沙箱逃逸核心：生成器gi_frame+f_back回溯+f_globals取全局；字符高尔夫用Unpacking+s+…

### 2024 第九届全国高校密码数学挑战赛（1 篇）
- 第九届全国高校密码数学挑战赛密码数学赛题三,AES侧信道攻击恢复密钥第一字节,涵盖CPA相关功耗分析+DPA差分功耗+互信息+KS检测+深度学习CNN方法,以及基于明文类别的均值滤波降噪。 → `AES 密钥第一字节恢复`；侧信道攻击的核心是挖掘信息特征(CPA/DPA/互信息/CNN),汉明重量模型失效时需要更复杂特征;降噪常用基于明文类别的均值滤波…

### 第十七届全国大学生信息安全竞赛-创新实践能力赛初赛(2024 CISCN)（1 篇）
- 大鲲智联+CISCN 2024侧信道功耗分析3种思路破解_ciscn_2024_12位密码:人工作图/argmin最低峰/皮尔逊相关系数自动。 → `40字符输入 × 13轮 × trace长度520 + corrcoef阈值0.90`；侧信道功耗分析经典三法:plot肉眼比较、argmin最低峰位置(正确密码因CPU执行不同而波谷X偏移)、corrcoef皮尔逊相…

### 2024 网鼎杯（4 篇）
- p 高位 Coppersmith + LFSR+NFSR 序列密码 + 有限域同构 AES key 还原。 → `f = (p>>248)<<248 + x ; f.small_roots(X=2^248, beta=0.49, epsilon=0.014) ; noise bit = 1/3…`；Coppersmith 攻击当 p 已知 256/512 bit 时爆破 7 bit 是 38 分钟；有限域同构问题本质是 GCD…
- Coppersmith 已知 p 高位 + 多层 RSA 套娃 + Shamir kernel 攻击。 → `f = (p>>248)<<248 + x ; f.small_roots(X=2^248, beta=0.49, epsilon=0.014) ; n1 // 2024 开根 =…`；Shamir 秘密共享若 threshold > 自由度，攻击者可取 C2 矩阵求 kernel 还原 C1 拼出完整方程组。
- 自定义 S 函数 + 等效 key 转换 + 线性方程组还原 → `def S(x): x1 = x; x2 = x << IV & MASK; x3 = x << IV & ((1 << block_size) - 1) / MASK; retu…`；自定义分组密码的 S 函数可通过等价 key 替换和线性方程组分析还原；MASK 异或泄低位 + unshiftLeft 恢复高位…
- 4 道 crypto 题合集：高位 p/q 还原 / p-1 smooth RSA / 双重签名 + Vigenère / SPN 分组密码 → `hint1, hint2 = p>>442, q>>442 # Coppersmith bd_attack 还原`；已知 p/q 高位 70bit 必用 Coppersmith bd_attack；2g+1 形式素数 p-1 平滑用 Pollar…

### 2024 长城杯（1 篇）
- 16 进制密码 zip 套娃逐字符 WinRAR 爆破。 → `WinRAR 命令 -ibck -y -p{password} ; replace '?' with a-f0-9 ; subprocess.run WinRAR.exe`；大量小 zip 套娃 + 单字符 16 进制密码时，单字符逐位爆破比全密码字典快 16 倍。

### 2024 领航杯（2 篇）
- RSA p^4+tp AMM 开方 + 身份证校验和提取 + web sum 哈希碰撞。 → `p, tp = genPrime() # p^4 + tp 素数 ; AMM(p^4 mod n) 拿 p ; this_is_pwd_j71nas2 ; 0x202f5 = 13…`；p^4+tp 模数可对 p^4 用 AMM 开方求 p 候选；web 弱哈希常是 sum(ord) 单值校验。
- 公众号风格的两题展示 — Crypto-randError 现场无人解，MT19937 逆推 + AES-ECB 明文前缀爆破；Misc-VC&EZ 提示恶意 docx + 符号链接 + P…；完整 EXP 被作者隐藏成引流钩子；MT19937 状态可逆可爆破前提是 update_state 是仿射的且 y*X 段可枚举。

### 2025 0xGame（1 篇）
- 7 个新人向 crypto 子题合集（DH s=1 / factordb / Vigenère 两种 / GB2312 三进制 iroot / POW + 2025 位 RSA）；DH 协议 s 值退化时（公钥=1）直接 s=1；factordb 是小指数 RSA 标准武器；Vigenère 乘法变种需逐位解…

### 2025 CISCN / 第三届长城杯网数智（1 篇）
- 2025 CISCN 第十九届+长城杯第三届初赛：ECDSA 已知私钥 + 双层 RSA smooth prime + wasm GDSC zstd + AES-128-GCM + 错误注入… → `d = (s*k - e) * pow(r, -1, n) % n`；CISCN 是国内最大 CTF 双赛（信安+长城杯网数智），初赛涉及密码学/逆向/Web/Pwn/AI 全栈；ECDSA 已知 n…

### 2025 KCTF（1 篇）
- 2025 KCTF 第九题 智斗邪首：斐波那契变种+Decimal 高精度倒数+压缩编码+多层输入校验，考验数学建模和工程实现。 → `getcontext().prec = int(prec); Decimal(1) / Decimal(n)`；Python 处理超长整数时 'sys.set_int_max_str_digits(0)' 必加，否则 str(int) 报 V…

### 2025 RCTF（1 篇）
- 2025 RCTF 高级 crypto：GMP MT19937 自实现 + WARM_UP 2000 + 二次幂模映射 + LSB 1 比特输出，624 LSB 完整观测后用 F2 上 LL… → `mti = 2000 % 624 = 128; gmp_urandomb_ui 取低 31 位`；GMP 内置 MT19937 是 gmpy2 / pycryptodome 测试向量的常见来源；自定义 WARM_UP + tem…

### 2025 古剑山（1 篇）
- 2025 古剑山 common_rsa 物不知数：同 N 同 m 不同 e1=35422, e2=1033 共模攻击，扩展欧几里得求 s, t，c1^(-1)^334 * c2^11453… → `m = (c1_inv)^/s/ * c2^t mod N`；RSA 共模攻击 = 同 N 同 m 不同 e，扩展欧几里得求 s*e1 + t*e2 = 1，m = c1^s * c2^t m…

### 2025 强网杯车联网赛道（1 篇）
- 2025 强网杯车联网：SCAAES CPA 侧信道攻击 AES-128 + IMG_256 + RC4 变种小程序 + CAN 信号频次统计。 → `CPA: correlations = np.dot(hw_normalized, traces_normalized) / num_traces`；车联网安全常考 CPA（相关功耗分析）攻击 AES-128 密钥，SubBytes 字节攻击 + S-box 汉明重量 + 皮尔逊…

### 2025 江苏省密码行业职业技能竞赛（1 篇）
- 2025 江苏密码行业赛：签到 base64 + 朴素 RSA gcd 拆 p + 熵密杯原题 mac 伪造 + Special DH 爆破 + SM3 国密。 → `gcd(pq, n) = p（朴素 RSA）`；江苏密码行业赛是省内权威赛事，一等奖通关；熵密杯是江苏本土赛事，2025 江苏密码决赛直接抄熵密杯原题；朴素 RSA 给 p(q-…

### 2025 湾区杯（1 篇）
- 2025 湾区杯：SM4 国密 32 轮解密 + Python 沙箱逃逸 globals+builtins+import + JWT 2 字母爆破 + Quaternion DLP BSGS… → `jwtKey = '@o70xO$0%#qR9#' + 2 字母 (charset 62)`；SM4 32 轮国密 SPN：FK[4] + CK[32] 固定 + sk[32] 子密钥；JWT HS256 弱密钥爆破 62…

### 2025 第三届熵密杯（1 篇）
- 2025 熵密杯 SM2+SM4+SM3 国密 EnvelopedData PKCS7 封装：sm2 加密 sm4 key + sm4-cbc 加密明文 + sm3 摘要 + pyasn1… → `EnvelopedData = SM2Cipher(encryptedKey) + EncryptedData(cipherText) + SM3 digest`

### 2025 能源 CTF（1 篇）
- 2025 能源 CTF：Q1 RSA hint p 线性同余 + Q2 HNP 隐数问题格攻击（30 组 512-bit a + 400-bit b + 隐 m）。 → `Lattice HNP + LLL 还原 m`；hint + 233*k == 233*k*p 是 RSA 隐 p 套路 → p = gcd(hint, ...) 试 233*k…

### 2025 能源网络安全大赛（1 篇）
- 2025 能源预赛 eazy_crypto：背包密码 + 超递增序列 LLL 还原 + mod b 恢复 private_key + 贪心解子集和。 → `private_key[i] = (public_key[i] * inverse(a, b)) % b`；背包密码（Merkle-Hellman）= 超递增序列 + 模 b 乘 a 混淆 + 模 b 求逆还原 + 子集和贪心；a*b 都…

### 2025 领航杯网安赛项（1 篇）
- 2025 领航杯教师组决赛上：Crypto 修栅栏+RSA + Misc DNS/FTP + Web locallogin XFF+cookie 替换+sqlmap。 → `X-Forwarded-For: 127.0.0.1 绕 IP 检测`；修栅栏密码 = 三组字符按行拼接 → base64 编码；X-Forwarded-For 绕 IP 检测是经典 Web 套路；ba…

### 2025 BabyStream（1 篇）
- BabyStream LFSR 流密码 — 128位状态 + feedforward 4/16/63次非线性 + 线性化攻击 (矩阵 A·y 加 6.25% 噪声) + 多次随机子集 200…；LFSR + 弱 feedforward = LPN 风格;线性化是 LPN 标准攻击;6.25% 噪声下需多次子集投票;O(n)…

### 2025 DASCTF（1 篇）
- DASCTF 2025 上半年赛 Mini-Venom 战队 WP，OBEX/RSA 复私钥/ECDSA 复用/SM4 文件系统解密。 → `OBEX 流量 + RSA 私钥恢复 + ECDSA nonce 复用`；蓝牙 OBEX 协议 tshark '-Y obex.header.value.byte_sequence' 抓图片；RSA 私钥…

### 2025 ISCTF（1 篇）
- ISCTF 2025 4 道密码 (变种凯撒 / 幂塔欧拉 / RSA p+q leak / LLL 三角函数) 速查。

### 2025 LitCTF（1 篇）
- LitCTF 2025 ez_math：2×2 矩阵 A^e mod p 的 Cayley-Hamilton 攻击，把高次幂降维到商环多项式求逆。；Cayley-Hamilton 定理 + 二次扩域是矩阵密码的通用解法；商环运算避开了复杂特征值计算。

### LitCTF 2025（3 篇）
- LitCTF 2025 math方向RSA带噪声hint泄露攻击链,标准hint = n + noise*(p+q+noise)形式,40位小素数Pollard-Rho分解+韦达定理还原p,… → `LitCTF{db6f52b9265971910b306754b9df8b76}`；任何hint都要先转成数学表达式: hint = n + noise*(p+q+noise) 是关键;小素数因子必用Pollard…
- LitCTF 2025 leak方向RSA dp高位泄露攻击深度解析,180位dp低位+LLL/Coppersmith寻找小根恢复完整dp,侧信道+实现漏洞双重视角。 → `LitCTF{...}`；RSA-CRT实现中泄露dp高位是致命的;Coppersmith小根攻击对(x≪n)的多项式F(x)有效;实际部署中应避免计算和存…
- LitCTF 2025 Basic方向RSA致命错误:n=getPrime(1024)用单个素数作模数,phi(n)=n-1,直接inverse(e,n-1)得d解密。 → `LitCTF{...}`；任何RSA题目都应先验证n是合数(质数检测)再决定攻击策略;φ(p)=p-1对质数成立;实现失误让1024位"安全"在毫秒级崩塌。

### 2025 Quantum CTF / Hack The Box QLotto（1 篇）
- 量子安全Qlotto CTF(Hack the box)量子电路+模拟器+6位bit映射+42取模,需理解Lotto/Test bit按位取反关系,用量子电路让test和lotto不同但gu… → `Lotto = ((21 - (Test - 1)) % 42) + 1`；量子电路题先理解验证逻辑(entropy validation + bit extraction + 42取模),再设计电路让te…

### 2025 SQCTF（1 篇）
- 第四届 SQCTF Crypto 方向 8 大题全解，覆盖 RSA/ECC/侧信道/经典密码学全栈。 → `Fermat attack + CRT + ECC + dp leak + side channel + common modulus + 转子密码机`；Fermat 攻击适用 p q 接近；CRT 适用多组 (n, c)；ECC 加法公式；dp 泄漏 + e 爆破；侧信道模板用欧氏…

### 2025 TJCTF（1 篇）
- TJCTF 2025 部分 Crypto：4 题 Baconian + 自定义 RC4 + 多模数 RSA + LCG 伪随机爆破。 → `AES-ECB(key, ciphertext) where key = LCG.randbytes(32)`；多模数 RSA 当 e 较小时 e 次方根可解；LCG 种子若用 time.asctime()，时间窗口可爆破。

### 2025 n1ctf（1 篇）
- n1ctf 2025 Crypto 高难度：把 35 维立方加密视为对称张量 f(x) = Σ w_k (v_k^T x)³，用 Jennrich 算法做 CP 分解恢复明文。；张量密码学的安全性依赖维度与域大小，Jennrich 算法的 O(n³) 复杂度在 GF(257) 上对 n=35 完全可行；GF…

### 2025 强网杯（1 篇）
- 2025 强网杯 ezran：MT19937 624 状态 GF(2) 线性方程组恢复 + Fisher-Yates 洗牌逆向。；MT19937 内部状态 19968 bit 可通过足够样本 (3108) 恢复；GF(2) 线性代数 + 高斯消元是标准攻击。

### 强网杯2025决赛（1 篇）
- 强网杯2025决赛 somebox:LCG逆推k/b密钥流+shellcode%5爆破jmp$+2花指令。 → `solve_for_k(dx,dy) + setKey(0xC0首包) + getKeyStream(mix+LCG) + shellcode%5爆破`；LCG求解k,b:用dx,dy和模逆pow(dxp,-1,Mp);逆向流密码:mix(rol64)→逆xor_shift→恢复KE…

### 2025 恒安嘉新杯 / EasyAES + 失窃的工艺（1 篇）
- 嘉新杯 2 题：EasyAES 4 层 CBC 双重爆破 + 失窃的工艺 OTA 流量分析 session_key 解密。；多层 AES-CBC 加密需要 valid_padding 检查 + unpad 逐层验证; SHA-256 短 key 派生可爆…

### 2025 春节活动（1 篇）
- 2025 春节解题领红包全题 writeup，覆盖 XXTEA + 自定义 SPN + AES OFB + 魔改 XTEA + 自定义 VM 5 大密码学/逆向方向。 → `AES key=7E13141528AED2A6ABF7158809CF4F3C 魔改 XTEA delta=0xB979379E 12 轮 XXTEA key="my-xxtea…`

### 2025 第三届陇剑杯初赛（1 篇）
- 第三届陇剑杯初赛夺旗闯关：F_{p^2}扩域Weil配对+RSA双线性对密码学难题 → `Weil配对 vs Tate配对;F_{p^2} y^2=x^3+1;j=0 E0;r-子群基co_r=(p+1)/r;Miller loop;deg φ=2^a*x;x_byte…`；Weil配对比Tale配对值会抵消规范化常数；r-子群基co_r=(p+1)/r；预计算j映射表

### 2025 职工数字化应用技术技能大赛（1 篇）
- 3道数据安全题,11名成绩,涵盖cookie伪造(username+date+URL编码)+符号替代密码表(!→1..)→0)+自定义Base32(32字母表)解码+首字节^0x33+8字节… → `cookie_value=quote_plus(''username=administrator&date=2025-11-XX+0000&'')/FNT5BMYAJD4IHLKU…`

### 腾讯云安全挑战赛 2025（1 篇）
- 腾讯云安全挑战赛COS提权与利用WP,Java CrackMe反编译AES-ECB+XOR deobfuscate解出AK/SK/Token,COS Bucket Policy分析(IPCA… → `AES+deobfuscate AKID3eUqV0RTdHaRQ6PeyFzouVVSsd1c5oU4dgxxMSdepyySy-3TzBV2As5bsmVZe6LW`

### 2025 解题领红包（1 篇）
- 解题领红包 Windows 高级 — UID+时间戳异或得 Str2 + 108 字符 Key 转 18 数组 + ECC 加解密 d-1 轮 + 复杂字符算式比较。；比较 Str1 != Str2 即失败, 需让两边相等; UID^2 决定 Str2, 但 UID 也被 time(0) 量化异或…

### 2026 DesCTF（1 篇）
- 2026 DesCTF Misc 全：bkcrack 已知明文 + PIL zigzag + torch 模型反演 + 红外遥控 6x6 网格 + Modbus func 8 + Shami… → `bkcrack -C challenge.zip -c challenge.png -x 0 89504E470d0a1a0a0000000d49484452`

### 2026 ACTF（1 篇）
- ACTF 2026 — 5 道 Crypto (inverse_pow + jail help('secret') + 低指 RSA 广播 + QuadraticField+decimal…；pydoc DATA 区会打印模块源 (help('secret') 非预期读 flag);TenSEAL CKKS 即使无 se…

### Hack.lu CTF 2017 / 后量子 crypto（1 篇）
- Sidelnikov-Shestakov 攻击 McEliece GRS 码 255 ratio 枚举 → `G[i,j] = v_j * α_j^i (i=0..k-1, j=0..n-1) — 范德蒙德结构`；McEliece 安全只对二元 Goppa 码；GRS / Reed-Solomon 实现的 McEliece 是 1992 年就…

## Connections

- 上层索引：[[ctf/source-corpus--des-ctf-knowledge]]
- Source: [https://github.com/Dest1ny-Sec/Des-CTF-Knowledge](https://github.com/Dest1ny-Sec/Des-CTF-Knowledge)

## Open Questions

- 本页为索引层，题目细节请回源；若某场比赛多题值得展开，再单独建 `ctf/<比赛>.md` 实体页。
