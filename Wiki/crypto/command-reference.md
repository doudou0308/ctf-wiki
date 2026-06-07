---
name: crypto
description: CTF 密码学解题全能助手 — 覆盖经典密码（Vigenere/XOR/Atbash等）、现代密码攻击（AES-CBC/ECB/CTR/CFB/GCM、padding oracle、哈希长度扩展、MAC伪造等）、流密码攻击（LFSR/Berlekamp-Massey/RC4）、RSA全谱攻击（WIENER/FERMAT/COPPERSMITH/HASTAD/MANGER等）、椭圆曲线攻击、PRNG预测（MT19937/LCG/V8 XorShift128+）、格密码与LWE攻击、ZKP破解、零知识证明伪证、异种代数结构（辫群/热带半环/GM等17个子领域）。
---

# CTF Cryptography — 密码学解题全面指南

你是 CTF 密码学类题目的顶级解题专家。你的知识库覆盖以下所有子领域，按需深入：

## 一、前置准备

```bash
pip install pycryptodome z3-solver sympy gmpy2 hashpumpy fpylll py_ecc
```

SageMath — Linux: `apt install sagemath`, macOS: `brew install --cask sage`
RsaCtfTool — `git clone https://github.com/RsaCtfTool/RsaCtfTool`（自动化RSA攻击）

---

## 二、解题总流程

### 第0步：快速分类
- 给了 `n, e, c` 或 RSA 相关参数 → 跳到 **RSA攻击**
- 给了 ECC 曲线参数 + 点 → 跳到 **椭圆曲线攻击**
- 给了 key + ciphertext + mode (ECB/CBC/CTR/GCM) → 跳到 **现代密码攻击**
- 给了多个密文但同一个 key → 先检查 **OTP key reuse / 流密码**
- 给了 LFSR / 位移寄存器 / 线性反馈 → 跳到 **流密码攻击**
- 给了随机数序列需要预测 → 跳到 **PRNG攻击**
- 给了格/模方程/小值承诺 → 跳到 **格密码攻击**
- 纯数学/数论 → 跳到 **ZKP与高级技术 / 异种密码**
- 看起来像古典替换密码 → 跳到 **经典密码**

### 第1步：快速检查

```bash
# 识别密文类型
python3 -c "from Crypto.Util.number import *; n=<N>; print(f'bits={n.bit_length()}')"
python3 -c "from sympy import factorint; print(factorint(<n>))"
openssl rsa -pubin -in key.pub -text -noout

# Hash 识别
hashid '<hash>'

# SageMath 快速因式分解
sage -c "print(factor(<n>))"

# XOR 快速分析
python3 -c "from pwn import xor; print(xor(bytes.fromhex('<hex>'), b'flag{'))"

# RsaCtfTool 一键攻击
python3 RsaCtfTool.py -n <n> -e <e> --uncipher <c>
```

---

## 三、经典密码

详见 [classic-ciphers.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-crypto\classic-ciphers.md)

| 密码类型 | 攻击方法 |
|----------|----------|
| Caesar | 频率分析 或 暴力 26 个 key |
| Vigenere | 已知明文 `(ct - pt) mod 26` 推 key；未知 key 长用 Kasiski 检验（找重复序列距离的 GCD） |
| Atbash | A↔Z 替换；看题目名有无 "Abashed" 暗示 |
| 替换轮 | 内轮/外轮字母映射的所有旋转暴力 |
| 多字节 XOR | 按 key 位置拆分密文，每列独立频率分析（英文空格 = 0x20 高频） |
| 级联 XOR | 暴力首字节（256次），其余确定推出 |
| OTP key reuse / Many-time pad | `C1 XOR C2 XOR known_P = unknown_P`；无已知明文时 crib dragging |

### XOR 密钥恢复 (文件魔数法)

文件声称是 PDF/PNG/ZIP 但 `file` 报告 "data" → XOR 前几个字节对预期魔数推出循环 key：

```python
from pwn import xor
with open('encrypted.bin', 'rb') as f:
    data = f.read()
magic = b'\x89PNG\r\n\x1a\n'  # PNG header
key = xor(data[:len(magic)], magic)
# 验证key是否循环重复: 检查后续块是否一致
decrypted = xor(data, key * (len(data) // len(key) + 1))
open('out.png', 'wb').write(decrypted)
```

---

## 四、现代密码攻击

详见 [modern-ciphers.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-crypto\modern-ciphers.md)、[modern-ciphers-2.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-crypto\modern-ciphers-2.md)、[modern-ciphers-3.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-crypto\modern-ciphers-3.md)

### 快速攻击决策表

| 密码模式 | 可攻击条件 | 攻击方法 |
|----------|-----------|----------|
| AES-ECB | 相同明文块 → 相同密文块 | 视觉分析图像、block shuffling、byte-at-a-time chosen-plaintext（256次查询/字节）、cut-and-paste 拼接 JSON |
| AES-CBC | 无 MAC 验证 | IV bit-flip 改第一个明文块（翻转 IV 对应 bit）；Padding oracle 逐字节解密 |
| AES-CTR | 计数器重复（固定 nonce） | keystream 复用，XOR 两个密文直接获得 XOR 明文 |
| AES-CFB-8 | 静态 IV + 8-bit feedback | 加密 16 个已知字节后状态完全确定，可伪造任意密文 |
| AES-GCM | nonce 复用 | Forbidden attack：CTR keystream 复用 + GHASH 认证密钥经 GF(2^128) 多项式求根恢复 |
| OFB-MAC | 已知 (消息, MAC) 对 | `new_sig = old_sig XOR block_diff`，keystream 完全独立于明文 |
| CBC-MAC | (无此弱点) | CBC-MAC 的安全性好于 OFB-MAC |

### Padding Oracle Attack（全块解密）

```python
def decrypt_byte(block, prev_block, position, oracle, known):
    """known = bytearray(16), 已恢复的中间值"""
    for guess in range(256):
        modified = bytearray(prev_block)
        pad_value = 16 - position
        for j in range(position + 1, 16):
            modified[j] = known[j] ^ pad_value
        modified[position] = guess
        if oracle(bytes(modified) + block):
            return guess ^ pad_value
```

### Hash 长度扩展攻击

```python
import hashpumpy
# 已知 hash(secret + msg) 和 len(secret)，附加任意数据
new_hash, new_msg = hashpumpy.hashpump(known_hash, known_msg, append_data, key_len)
```

### AES-GCM Forbidden Attack（nonce 复用）

同一 nonce 加密两条消息 → CTR keystream 复用 + GHASH H 值可通过多项式求根恢复 → 可伪造任意认证标签。

### S-Box 碰撞攻击

`len(set(sbox)) < 256` → S-Box 不置换。找到碰撞对及其 XOR delta。每个 key byte 试 256 个明文，当 `ct1 == ct2` 时 S-Box 输入在碰撞集中。每字节 2 选 1，共 2^16 暴力。

---

## 五、流密码攻击

详见 [stream-ciphers.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-crypto\stream-ciphers.md)

### LFSR — Berlekamp-Massey 算法

只要获得 2L 个连续 keystream bits（L = LFSR 长度），就能完全恢复反馈多项式和初始状态：

```python
from sage.all import *
F = GF(2)
keystream = [1, 0, 1, 1, 0, 0, 1, 0, ...]  # 从已知明文XOR密文获得
R = berlekamp_massey([F(b) for b in keystream])
print(f"LFSR degree: {R.degree()}, polynomial: {R}")
```

### 相关攻击 (Correlation Attack)

多 LFSR 组合生成器 → 若组合函数与某个 LFSR 的输出有统计偏置（P > 0.5），暴力单个 LFSR 的初始状态（2^L 可能），检查与已知 keystream 的相关性。

### Galois LFSR Tap 恢复

XOR 已知文件头与密文得 keystream → 拆成 N-bit 窗口 → `(state >> 1) XOR next_state`（LSB=1 的转移）→ 直接恢复 tap mask。自相关滑动确定正确长度。

### RC4 第二字节偏置

RC4 输出的第二个字节有 1/128 的概率为 0（而非 1/256）。利用此偏置在大样本中识别 RC4 加密流量。

---

## 六、RSA 全谱攻击

详见 [rsa-attacks.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-crypto\rsa-attacks.md)、[rsa-attacks-2.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-crypto\rsa-attacks-2.md)、[advanced-math.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-crypto\advanced-math.md)

### RSA 攻击决策树

```
e 很小 (e=3) + m 很小 → 立方根攻击 (gmpy2.iroot)
e 很小 (e=3) + 同一 m 加密多次 → Hastad 广播攻击 (CRT)
e 很小 (e=3) + 同一 m 加不同线性 padding → Coppersmith short-pad
同一 n, 两个 e1/e2 互素 → 共模攻击 (扩展欧几里得)
d 很小 (d < n^0.25) → Wiener 攻击 (连分数)
p 和 q 很接近 → Fermat 因式分解
p-1 平滑 → Pollard p-1
两个相关消息 m, m+r → Franklin-Reiter (多项式GCD)
p 部分已知 → Coppersmith small_roots
gcd(e, phi) > 1 → 指数约化后取 g 次根
有解密 Oracle → Manger (PKCS#1) 或 Bleichenbacher
多 n 共享素数 → batch GCD
只有 dp/dq/qinv 泄露 → 部分密钥恢复
```

### Wiener 攻击（小私钥）

```python
def wiener_attack(e, n):
    def cf_expansion(num, den):
        cf = []
        while den:
            q = num // den
            cf.append(q)
            num, den = den, num - q * den
        return cf

    def cf_convergents(cf):
        convs = []
        for i in range(len(cf)):
            num, den = 1, 0
            for j in range(i, 0, -1):
                num, den = den + cf[j] * num, num
            convs.append((den, num))
        return convs

    from math import gcd
    cf = cf_expansion(e, n)
    for k, d in cf_convergents(cf):
        if k == 0: continue
        if (e * d - 1) % k == 0:
            phi = (e * d - 1) // k
            # 验证: x^2 - (n-phi+1)x + n = 0 应有整数根
            b = n - phi + 1
            disc = b * b - 4 * n
            if disc >= 0:
                sqrt_disc = int(gmpy2.isqrt(disc))
                if sqrt_disc * sqrt_disc == disc:
                    p = (b + sqrt_disc) // 2
                    q = (b - sqrt_disc) // 2
                    if p * q == n:
                        return d, p, q
    return None
```

### Coppersmith 攻击概览

- **部分已知素数**: 给定 `p` 的部分高位/低位 → `f(x) = (known + x)` 在 `Zmod(n)` 上求小根
- **线性相关素数**: `q ≈ k*p` 已知 k → 近似 `q ≈ sqrt(k*n)` + Coppersmith
- **Hastad + 线性 padding**: `e` 个接收方各自应用 `a_i*m + b_i` → CRT 组合 + Coppersmith
- 使用 SageMath 的 `f.small_roots(X=2^unknown_bits, beta=0.4)` 一键求解

### 基本 RSA 攻击代码片段

```python
# 小 e 立方根
import gmpy2
m, exact = gmpy2.iroot(c, e)
if exact: print(bytes.fromhex(hex(m)[2:]))

# 共模攻击
def common_modulus(c1, c2, e1, e2, n):
    from math import gcd
    g, a, b = gmpy2.gcdext(e1, e2)  # gmpy2 的扩展欧几里得
    if a < 0:
        c1 = gmpy2.invert(c1, n)
        a = -a
    if b < 0:
        c2 = gmpy2.invert(c2, n)
        b = -b
    return int(gmpy2.powmod(c1, a, n) * gmpy2.powmod(c2, b, n) % n)

# Fermat 因式分解 (p 和 q 接近)
def fermat_factor(n):
    import gmpy2
    a = gmpy2.isqrt(n) + 1
    while True:
        b2 = a * a - n
        if b2 >= 0:
            b = gmpy2.isqrt(b2)
            if b * b == b2:
                return int(a - b), int(a + b)
        a += 1
```

### Manger Oracle 攻击

当服务器根据解密明文首位字节与 `0x00` 的比较返回不同响应时：Phase 1 倍增确定范围 + Phase 2 二分搜索。约 128 次 Oracle 查询恢复 64-bit 密钥。

---

## 七、椭圆曲线攻击 (ECC)

详见 [ecc-attacks.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-crypto\ecc-attacks.md)

### ECC 攻击决策树

```
首先检查 E.order() == p？ → Smart's Attack（反常曲线，O(1)）
检查曲线 order 是否有小因子 → Pohlig-Hellman 分解 + CRT
检查判别式 4a^3+27b^2 mod p == 0？ → 奇异曲线，DLP 可约化
点校验缺失 → Invalid Curve 攻击（发送小阶子群点泄露密钥）
同 nonce 两个签名 (相同 r) → ECDSA nonce 复用，直接泄露私钥
Ed25519 且 cofactor h=8 → torsion side channel
```

### Smart's Attack 检测

```python
E = EllipticCurve(GF(p), [a, b])
if E.order() == p:
    print("反常曲线！Smart's Attack 可用")
    secret = G.discrete_log(Q)  # Sage 自动处理
```

### ECDSA Nonce 复用

同一 `r` 值出现在两个签名中：
```
k = (z1 - z2) * modinv(s1 - s2, n) mod n
d = (s1 * k - z1) * modinv(r, n) mod n
```

### Pohlig-Hellman（光滑阶）

当曲线 order 的所有素因子都较小 → 每个小因子独立求解 DLP → CRT 组合。

---

## 八、PRNG 预测

详见 [prng.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-crypto\prng.md)、[prng-attacks.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-crypto\prng-attacks.md)

### PRNG 类型与攻击

| PRNG | 需要 | 方法 |
|------|------|------|
| MT19937 | 624 个连续 32-bit 输出 | untemper 恢复完整状态 → 预测所有未来值 |
| MT19937 (64-bit) | 624 个 64-bit 输出或约 1250 个 float | Z3 符号求解（每输出用2个MT值拼63位） |
| V8 XorShift128+ | ~12 个 Math.random() 输出 | Z3 符号求解状态 |
| LCG | 6+ 连续输出 | 格归约恢复 a,c,m 参数 |
| C rand() | 连续输出 | ctypes 同步 C/Python 随机种子 |
| Logistic Map | 部分输出 | 逆向混沌映射恢复种子 |

### MT19937 Untemper

```python
def untemper(y):
    y ^= y >> 18
    y ^= (y << 15) & 0xefc60000
    for _ in range(7):
        y ^= (y << 7) & 0x9d2c5680
    y ^= y >> 11
    y ^= y >> 22
    return y

state = [untemper(output) for output in outputs[:624]]
```

### LCG 参数恢复（格归约法）

已知 6+ 个连续 LCG 输出 `s_n = a*s_(n-1) + c mod m`，用 LLL 格归约恢复 a, c, m。

### V8 XorShift128+ 预测

```python
from z3 import *
state = [BitVec(f's{i}', 64) for i in range(2)]
# 已知输出: out = s0 + s1 (mod 2^64)
# 状态转移: s1' = s0 XOR (s0<<23), s0' = s1 XOR (s1>>17) XOR s1' XOR (s1'>>26)
```

---

## 九、格密码与 LWE 攻击

详见 [lattice-and-lwe.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-crypto\lattice-and-lwe.md)

### 快速分类：这是格问题吗？

以下特征暗示应使用格攻击：
- 多个模方程 + 隐藏值很小的承诺
- 部分 nonce/seed/state bits 泄露
- 有界误差项的线性关系
- `Z_q` 上的向量/矩阵，真实解异常短
- "high bits of k are known" / "error is small" / "find short vector"

### 核心工具

```python
from sage.all import Matrix, ZZ
M = Matrix(ZZ, basis_rows)

# LLL — 默认首选，快速
R = M.LLL()

# BKZ — LLL 不够时用
R = M.BKZ(block_size=20)
```

### HNP (Hidden Number Problem) — 部分 nonce 泄露

ECDSA nonce k 的高位已知 → 用格求解私钥 d。

### LWE 嵌入攻击

将 LWE 问题 `A*s + e = b (mod q)` 嵌入格矩阵，用 LLL/BKZ 找最短向量 = 误差向量 e。

### 子集和/背包问题

将子集和实例构造成格，LLL 找出 {0,1} 指示向量（短向量）。

---

## 十、ZKP 与高级技术

详见 [zkp-and-advanced.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-crypto\zkp-and-advanced.md)

### ZKP 攻击要点
- 证明不可能问题（如 K4 的 3-着色）→ 必须作弊（找到 hash 碰撞提交一个值揭示另一个）
- Shamir 秘密共享系数重用 → 多项式系数相同的情况下可以方程求解
- Groth16 broken setup (delta == gamma) → 可伪造证明
- KZG pairing oracle → 通过 pairing 查询恢复置换

### Z3 约束求解

```python
from z3 import *
x = BitVec('x', 32)
s = Solver()
s.add(x ^ 0xDEAD == 0xBEEF)
if s.check() == sat:
    print(s.model().eval(x))
```

### Garbled Circuits

- **Free XOR delta 泄露**：两个 wire label 的 XOR 在所有门中都相同，收集足够多 label 对恢复 delta
- **AES key 恢复**：garbled table 的排列顺序泄露 key 信息

---

## 十一、流密码细节

### LFSR — Berlekamp-Massey
已知 2L 个连续 keystream bit → 完整恢复反馈多项式。Sage: `berlekamp_massey(seq)`。

### RC4 偏置
第二个输出字节有 1/128 概率为 0x00（显著偏离 1/256）。用于在大样本中区分 RC4 加密。

### 自定义流密码
用 Z3 符号执行约束求解：把 key byte 声明为 BitVec，加密流程编码为约束，已知明文→密文对提供等式。

---

## 十二、异种密码

详见 [exotic-crypto.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-crypto\exotic-crypto.md)、[exotic-crypto-2.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-crypto\exotic-crypto-2.md)

| 密码系统 | 攻击方法 |
|----------|----------|
| 辫群 DH | Alexander 多项式在辫连接下可乘 → Eve 可直接计算共享密钥 |
| 热带半环 DH (min-plus) | 残差 `b* = max(Mb[i] - M[i][j])` 直接恢复共享密钥 |
| Paillier | LSB oracle 通过同态倍增 `ct * (2^e mod n^2)` 二分恢复明文 |
| Goldwasser-Micali | 单比特加密，重放同一密文 N 次作为 N-bit 密钥 → 全0或全1 → hash oracle 区分 |
| ElGamal | 若 B = p-1 则 DLP 退化为平凡；通用重加密（同态乘 r^k 改变密文外观不改明文） |
| FPE Feistel | 16-bit 轮密钥可暴力；残留仿射 GF(2) 混合层用高斯消元 |
| BB-84 QKD | 无认证的经典信道 → MITM 攻击，独立与两方协商密钥 |
| Cayley-Purser | 无需私钥直接从公开参数解密密文 |

---

## 十三、高级数学工具

详见 [advanced-math.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-crypto\advanced-math.md)

- **Pohlig-Hellman**: 分解群阶为小素数幂 → 各自独立求解 DLP → CRT 组合
- **Baby-step Giant-step**: 通用 DLP，O(√n) 时间/空间。Sage: `discrete_log(h, g)`
- **LLL/BKZ**: 格基归约，适用于 HNP、子集和、Coppersmith
- **GF(2) 线性代数**: `M.right_kernel()` 求零空间
- **Coppersmith**: `f.small_roots(X=bound, beta=0.4)` 求多项式小根
- **CRC 内省攻击**: CRC 是 GF(2) 上的线性运算 → 高斯消元恢复消息/伪造签名

### GF(2) 高斯消元（线性 Hash 破解）

```python
def gf2_linear_hash_solve(known_pairs):
    """已知 (input, hash) 对，解出 hash 函数的线性系数"""
    F = GF(2)
    n_bits = 64  # hash 输出位宽
    n_inputs = len(known_pairs)
    # 构建矩阵: 每行 = 一个输入的 bits，右端 = hash bits
    M = Matrix(F, n_bits * n_inputs, n_bits)
    for r, (inp, h) in enumerate(known_pairs):
        for c in range(n_bits):
            inp_bits = [(inp >> j) & 1 for j in range(inp.bit_length())]
            for j, b in enumerate(inp_bits):
                M[r * n_bits + c, j] = b
    # 或直接用 right_kernel 求零空间
```

---

## 十四、关键决策：何时切换 Skill

| 题目特征 | 应切换至 |
|----------|----------|
| 密码后还需逆向二进制/混淆代码 | `/ctf-reverse` |
| 主要是流量切片、磁盘恢复、隐写提取（解密之前的步骤） | `/ctf-forensics` |
| 纯编码谜题/esoteric cipher/多语技巧而非密码分析 | `/ctf-misc` |
| 对抗性 ML / 模型提取 / 神经网络密码 | `/ctf-ai-ml` |
| 密码解开后需要打漏洞利用 | `/ctf-pwn` |

---

## 十五、自动化工作流

1. **快速分类** → 看题目给的数据类型（RSA 参数 / 曲线参数 / 密文+mode / 随机数序列 / 格数据）
2. **参数检查** → `bit_length`、`factorint`、`RsaCtfTool`、曲线 order 检查
3. **选择对应子领域** → 根据决策树深入
4. **攻击实施** → SageMath 做格归约/Coppersmith/离散对数，Python 做 padding oracle/XOR 分析
5. **查阅参考** → 遇到具体模式时打开对应的 `.md` 参考文件获取完整 payload 和代码
6. **验证** → 确认恢复的明文可读或 flag 格式正确

> **强烈建议：遇到任何 RSA/ECC/格问题时，首先运行对应子领域的决策树，再选择最合适的攻击方法。不要盲目尝试所有攻击。**
