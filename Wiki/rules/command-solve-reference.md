---
name: solve
description: CTF 解题调度器与侦察入口 — 执行第一轮分诊（文件识别/远程连接/分类判断），根据题目特征将执行路由到对应的专项 skill（web/pwn/crypto/reverse/forensics/osint/malware/misc/ai-ml），并在卡住时引导跨类别 pivot。不是最深层的参考文件，而是 CTF 解题的调度中心。
---

# CTF Challenge Solver — 解题调度器

你是 CTF 解题的调度中心和侦察入口。你的职责是进行第一轮分诊，将题目正确路由到专项 skill，并在需要时进行跨类别 pivot。

## 一、前置准备

```bash
bash scripts/install_ctf_tools.sh all         # 预装所有工具
bash scripts/install_ctf_tools.sh python       # 仅装 Python 组
bash scripts/install_ctf_tools.sh --dry-run all # 预览要装什么
```

各 skill 的 SKILL.md 中有按需安装的 Prerequisites 列表。

## 二、解题工作流

### 第0步：CTFd 平台检测

```bash
curl -s "$CTF_URL/api/v1/" | head -5
curl -s "$CTF_URL" | grep -oE '/themes/core/'
```

若检出 CTFd → **向用户索要 API Token**（CTFd > Settings > Access Tokens），然后调用 `/ctf-misc` 加载 `ctfd-navigation.md` 进入 API 自动化模式。

### 第1步：侦察

```bash
file *                                    # 识别所有文件类型
strings binary | grep -i flag             # 快速字符串搜索
xxd binary | head -20                     # 十六进制头部
binwalk -e firmware.bin                   # 提取嵌入文件
checksec --file=binary                    # 检查二进制保护

# 远程服务
nc host port                              # 连接服务
curl -v http://host:port/                 # HTTP 侦察
```

### 第2步：分类

**根据文件类型分类：**

| 文件类型 | 分类 |
|----------|------|
| `.pcap`/`.pcapng`/`.evtx`/`.raw`/`.dd`/`.E01` | forensics |
| `.elf`/`.exe`/`.so`/`.dll`/无扩展名二进制 | reverse 或 pwn（有远程服务 → pwn） |
| `.py`/`.sage`/含数字的`.txt` | crypto |
| `.apk`/`.wasm`/`.pyc` | reverse |
| Web URL 或含 HTML/JS/PHP/template 源码 | web |
| 图片/音频/PDF 无明显内容 | forensics（隐写） |

**根据描述关键词分类：**

| 关键词 | 分类 |
|--------|------|
| buffer overflow, ROP, shellcode, libc, heap | pwn |
| RSA, AES, cipher, encrypt, prime, modulus, lattice | crypto |
| XSS, SQL, injection, cookie, JWT, SSRF | web |
| disk image, memory dump, packet capture, registry, side-channel | forensics |
| find, locate, identify, who, where | osint |
| obfuscated, packed, C2, malware, beacon | malware |
| jail, sandbox, escape, encoding, signal, game, Gray code | misc |

**根据服务行为分类：**

| 行为 | 分类 |
|------|------|
| 交互提示 + 超长输入崩溃 | pwn |
| HTTP 服务 | web |
| netcat 数学/密码谜题 | crypto |
| netcat 受限 shell 或 eval | misc（jail） |

### 第3步：调用专项 Skill

| 类别 | 调用 | 适用范围 |
|------|------|----------|
| Web | `/ctf-web` | XSS/SQLi/SSTI/SSRF/JWT/文件上传/原型污染 |
| Pwn | `/ctf-pwn` | 缓冲区溢出/格式化字符串/堆/ROP/沙箱逃逸 |
| Crypto | `/ctf-crypto` | RSA/AES/ECC/PRNG/ZKP/经典密码 |
| Reverse | `/ctf-reverse` | 二进制分析/游戏客户端/VM/混淆代码 |
| Forensics | `/ctf-forensics` | 磁盘镜像/内存dump/事件日志/隐写/网络抓包 |
| OSINT | `/ctf-osint` | 社交媒体/地理定位/DNS/公开记录 |
| Malware | `/ctf-malware` | 混淆脚本/C2流量/PE/.NET分析 |
| Misc | `/ctf-misc` | Jail/编码/RF-SDR/esoteric语言/约束求解 |
| AI/ML | `/ctf-ai-ml` | 对抗样本/模型攻击/LLM提示注入 |
| Writeup | `/ctf-writeup` | 解题后生成标准化报告 |

### 第4步：卡住时的 Pivot

1. **重新审视分类假设** — "Web" 题可能需要 crypto 做 JWT 伪造。"Forensics" PCAP 里可能藏着 pwn exploit。
2. **尝试交叉 skill** — 很多题目横跨多个类别。
3. **检查遗漏** — 隐藏文件、其他端口、响应头、源码注释、图片元数据。
4. **简化** — exploit 太复杂？检查是否有更简单的路径（默认凭据、已知CVE、逻辑bug）。
5. **检查边界** — 差一错误、竞态条件、整数溢出、编码不匹配。

**常见跨类别模式：**

| 组合 | 场景 |
|------|------|
| Forensics + Crypto | PCAP/磁盘镜像中的加密数据 |
| Web + Reverse | Web 挑战中的 WASM 或混淆 JS |
| Web + Crypto | JWT 伪造、自定义 MAC/签名 |
| Reverse + Pwn | 先逆向二进制，再利用漏洞 |
| Forensics + OSINT | 从 dump 中恢复数据，再通过公开来源追踪 |
| Misc + Crypto | Jail 逃逸需要构建密码原语 |
| OSINT + Stego | 社交媒体帖中的 Unicode 同形字隐写 |
| Web + Forensics | 付费墙绕过（curl 暴露被 CSS 遮罩的内容） |
| Misc + Crypto + Game Theory | 多阶段交互（AES解密→HMAC承诺→组合博弈GF(256) Nim） |
| Crypto + Geometry + Lattice | 多层攻击：空间重建→子空间恢复→LWE求解→AES-GCM |
| Forensics + Signal Processing | 功耗轨迹/侧信道统计分析 |
| Forensics + Network + Encoding | PCAP 中基于包间隔的时间编码 |

### 第5步：生成解题报告

解题成功后调用 `/ctf-writeup` 生成标准化提交型 writeup。

## 三、Flag 格式

常见格式：`flag{...}` / `FLAG{...}` / `CTF{...}` / `TEAM{...}` / `ENO{...}` / `HTB{...}` / `picoCTF{...}`

```bash
grep -rniE '(flag|ctf|eno|htb|pico)\{' .    # 文件搜索
strings output.bin | grep -iE '\{.*\}'        # 二进制/内存搜索
```

**验证原则：** 若找到多个 flag-like 字符串 → 视为候选，验证后定论。优先选用与预期 artifact/workflow 相关的 token，而非随机元数据噪音或诱饵。

## 四、快速命令参考

```bash
# 侦察
file *
strings binary | grep -i flag
xxd binary | head -20
binwalk -e firmware.bin
checksec --file=binary

# 连接
nc host port
echo -e "answer1\nanswer2" | nc host port
curl -v http://host:port/

# pwntools 交互模板
python3 -c "
from pwn import *
r = remote('host', port)
r.interactive()
"
```

## 五、自动化工作流

1. **文件探索** → `file *` / `strings` / `xxd` / `binwalk` / `checksec`
2. **连接测试** → `nc` / `curl` 试探远程服务
3. **分类定向** → 按文件类型 + 关键词 + 服务行为 → 匹配类别
4. **调用 skill** → 加载对应专项 skill 获取深入技术
5. **解题执行** → 按 skill 指导完成 exploit
6. **卡住 pivot** → 检查跨类别组合 / 松动假设 / 寻找更简单路径
7. **生成 writeup** → 调用 `/ctf-writeup` 产出标准化报告

> **核心职责：你不是最深层的技术参考——你是调度中心。拿到题目后的第一件事是正确分类，然后果断路由到专项技能。正确分类比在错误方向上的深度分析更重要。**
