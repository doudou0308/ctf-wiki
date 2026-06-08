# CTF Skills 全局规则（速度优化版 v4）

## 架构

Skill 包基于 `ljagiello/ctf-skills`（v2.x），全部在 `c:\Users\ZZH\.trae\skills\`。
知识库在 Obsidian Wiki：`C:\Users\ZZH\Documents\Obsidian Vault\Wiki\Wiki\`（217 页，YAML frontmatter + wikilinks）。
Docker 工具容器：`ctf-box`（持久化，挂载 `/work` → 工作目录，`/wiki` → 知识库）。

## 核心原则：Flag 即终点

> **唯一目标 = 拿到 flag。拿到即停，不要多跑一步。**

---

### 规则〇：强制启动检查表（最高优先级）

收到任何题目后，**第一轮 tool call 必须并行执行以下全部步骤**，一步不省：

```
Batch 1（并行，全部同时发出）：
  ├── 0.1 Skill 加载：调用 Skill 工具加载对应类别的 ctf-* skill
  ├── 0.2 文件侦察（如有附件）：
  │     ├── file <file>                          # 识别真实格式
  │     ├── strings <file>（若无则用 [System.Text.Encoding]::UTF8.GetString([IO.File]::ReadAllBytes("file"))）
  │     │   然后 Grep 搜 flag 模式（见规则十二）
  │     └── 对压缩包(.zip/.tar.gz/.7z/.rar)：先解压再对内容跑上述命令
  │         密码保护 → 尝试常见密码：infected, 123456, password, flag, <文件名>, 无密码(回车)
  ├── 0.3 URL/服务侦察（如有 URL/端口）：
  │     ├── docker exec ctf-box python3 /work/web_recon.py http://host:port/ --timeout 15
  │     └── curl -s http://host:port/ -Method HEAD -UseBasicParsing
  └── 0.4 无附件/纯文本描述 → 读题 3 遍，识别关键词直接分类
```

做完 Batch 1 后，**立即阅读加载的 Skill 的 SKILL.md + 最相关的 1 个 reference 文件**。

> **知识库检索不在启动阶段执行。** 触发条件：
> - 同一条路 2 轮仍无进展
> - 遇到不在"规则二"分流表内的文件格式
> - 解题方向摇摆不定
>
> 触发时（优先搜 triggers 字段）：
> ```bash
> Grep -i "triggers:.*<关键词>" "C:\Users\ZZH\Documents\Obsidian Vault\Wiki\Wiki\"
> ```
> triggers 无命中再全文：`Grep -ri "<关键词>" "C:\Users\ZZH\Documents\Obsidian Vault\Wiki\Wiki\"`

---

### 强制 TodoWrite 清单模板

```
1. [进行中] Skill 加载 + 零轮侦察（Batch 1 并行）
2. [待办] 解题：逐层分析直至拿 flag
3. [待办] 归档：写入 Obsidian Wiki 对应赛道目录
```

---

### 规则一：一键分流（禁止询问用户类别）

**不问用户类别——直接判断，直接上手：**

| 迹象 | 直接调用 | 注意 |
|------|---------|------|
| `.elf` + 远程端口 | `ctf-pwn` | |
| `.elf`/`.exe`（无远程端口） | `ctf-reverse` | `.exe` 在 Windows 上可能是 malware，结合上下文判断 |
| `.apk` | `ctf-reverse` + JADX MCP | 或使用 `performing-android-app-static-analysis-with-mobsf` |
| `.ipa` | 使用 `performing-ios-app-security-assessment` | |
| URL/HTML/PHP/JS/Python Web 源码 | `ctf-web` | |
| `.py`/`.sage`/数字文本/`.pem` | `ctf-crypto` | |
| `.pcap`/`.pcapng`/`.raw`/`.E01`/`.dd`/图片/音频 | `ctf-forensics` | |
| `.zip`/`.tar.gz`/`.7z`/`.rar` | 先解压→再对内容分流 | 密码先试常见值 |
| `.pyc`/`.class`/`.wasm` | `ctf-reverse` | |
| 纯文本描述（无附件） | `ctf-misc` 兜底 | 再根据关键词二次分流 |
| Dockerfile / docker-compose | `ctf-misc` / `cloud-security` | |
| 用户已明确指定类别 | **跳过侦察，直入 exploit** | 但仍需做 Batch 0.2/0.3 |

**禁止行为**：问"这题是什么类型？""需要我先分析吗？"

---

### 规则二：Skill 加载——只加载最相关的 1-2 个文件

每次只加载 SKILL.md + 当前漏洞类型对应的 1 个 reference 文件。不要全量加载。

---

### 规则三：卡住即 Pivot（1 轮即转）

- **1 轮失败** → 反思：分类是否正确？跨类别（Web+Crypto/Pwn+Reverse）？
- **2 轮失败** → 强制换策略 + 查知识库。候选：已知 CVE / 默认凭据 / 逻辑 bug / 环境变量泄露 / 源码注释中的提示 / 更简单的路径
- 同一条路不挣扎超过 **2 轮**。

---

### 规则四：代码规范

1. Pwn: `pwntools`（`remote()`/`process()`/`ELF()`/`ROP()`/`p64()`/`u64()`）
2. Crypto: `gmpy2` + `pycryptodome`（`from Crypto.Util.number import *`），格用 SageMath
3. Web: `requests.Session()` 保持 cookie；需要浏览器操作用 Playwright MCP
4. 每个脚本开头完整 import，末尾 `print(flag)`
5. **不加注释**。代码自解释。
6. 单文件 `solve.py`，>200 行才拆。

---

### 规则五：Writeup 按需生成

- 仅在 flag 到手后、或用户明确要求时才生成
- 输出到 `ctf-solutions/<题目名>/writeup.md`

---

### 规则六：目录结构

```
ctf-solutions/<题目名>/
├── solve.py           # 单脚本
├── scripts/           # 脚本 ≥2 个时创建
├── attachments/       # 解压/解码/提取出的内容
├── writeup.md
└── output.txt
```

原始附件留在原位不复制。

---

### 规则七：自动执行策略

| 操作类型 | 策略 |
|----------|------|
| `ls`/`file`/`strings`/`checksec`/`exiftool`/`binwalk`/提取/解压 | ✅ 直接执行 |
| 本地 Python 脚本（解密/数据处理/本地 exploit） | ✅ 直接执行 |
| 本地二进制分析（`objdump`/`readelf`，WSL 或用 `pyelftools`） | ✅ 直接执行 |
| `pip install` 缺失包 | ✅ 直接执行 |
| 远程连接（`nc host port`/`curl external-IP`/远程 exploit） | ⚠️ 先展示代码，等确认 |
| 大规模端口扫描 (`nmap -p-`) | ⚠️ 先展示命令，等确认 |

**本地 = 不连外部网络 = 全部自动跑。**

---

### 规则八：MCP 全自动调用（含降级方案）

所有 MCP 工具一律自动调用，不等确认：

| MCP 服务 | 自动行为 | 触发条件 | 不可用时降级 |
|----------|---------|---------|------------|
| **IDA Pro** | 反编译/交叉引用/字符串搜索/打补丁/重命名 | RE/Pwn 题打开二进制 | `objdump -d` + `strings` + `pyelftools` |
| **JADX** | 反编译 APK/搜索类/方法/字段/查交叉引用 | Android/Mobile 题 | `unzip` + `d2j-dex2jar` + `jd-gui` 或用 `apktool` |
| **Playwright** | 打开网页/截图/点按钮/填表单/执行 JS | Web 题需浏览器 | `curl` + `requests` 直接发 HTTP 请求 |
| **WireMCP** | 抓包/分析 PCAP/检测威胁/提取凭据 | Forensics/Network 题 | `tshark`/`tcpdump` + Python `scapy` |

**禁止**：问"要我通过 MCP 查吗？"——直接做。

---

### 规则九：解题优先级（快→慢）

```
1. strings + grep -i flag                        ← 10秒
2. binwalk -e / exiftool / 解压看内容             ← 30秒
3. 默认凭据 (admin/admin, guest/guest, root/root) ← Web/SSH 必试
4. 版本号/框架名 → CVE 搜索                       ← 版本明确时
5. 源码注释/配置文件/环境变量中的明文信息          ← 代审第一步
6. 逻辑缺陷 (溢出/竞态/类型戏法/整数溢出)         ← 代码审计
7. 标准攻击 (SQLi/SSTI/ROP/GOT/格式化字符串)       ← Skill 速查
8. 深度逆向/堆利用/内核                           ← 最后手段
```

**每步成功→直接拿 flag→停。**

---

### 规则十：工具优先级 & Shell 兼容性

#### Windows（PowerShell）环境
- **可用**：Python 3 + pip（pwntools, pycryptodome, gmpy2, scapy, pyelftools）
- **PowerShell 等价命令**：
  - `strings <file>` → 若无该命令，用 Grep 工具搜索 ASCII 字符串；或用 Python: `python -c "import sys; data=open(sys.argv[1],'rb').read(); print(''.join(c if 32<=ord(c)<127 else '\n' for c in data.decode('latin-1')))" file`
  - `grep` → 始终用 Grep 工具（不要用 `Select-String`）
  - 标准错误重定向 `2>/dev/null` → `2>$null`（PowerShell）
  - `file <file>` → 始终用 RunCommand（Windows 可能需 Git Bash 或 WSL）
- **缺失工具**: 先 `pip install` 再跑，不跳过

#### WSL/Linux 环境额外可用
`gdb`, `strace`, `ltrace`, `tshark`, `readelf`, `objdump`

---

### 规则十-A：Docker Kali 渗透工具箱

**!! Docker CLI 路径 !!** 
当前进程 PATH 含死路径 `C:\Program Files\Docker\Docker\resources\bin`（docker.exe 不存在）。
**任何涉及 docker 的命令，必须以下格式执行：**
```powershell
$env:Path = "F:\DockerDesktop\resources\bin;F:\DockerDesktop\resources\cli-plugins;" + $env:Path; docker <子命令>
```
不能省略 PATH 前缀，不能单写 `docker`。
**注册表已永久修复，重启 Trae IDE 后即可直接使用 `docker`。重启前必须带 PATH 前缀。**

调用前缀：`docker exec ctf-box <tool> <args>`。
文件交互：工作目录 `g:\比赛\训练集\测试用` 已挂载至容器 `/work`，Docker 内直接访问 `/work/` 即可。
知识库挂载：`C:\Users\ZZH\Documents\Obsidian Vault\Wiki\Wiki` 挂载至容器 `/wiki`。

GhidraMCP：headless REST 在 `localhost:8089`，Bridge SSE 在 `localhost:8766`。可使用 IDA MCP 工具自动连接。

| 类别 | 工具 | 示例 |
|------|------|------|
| Web | sqlmap, ffuf, wpscan, sslscan, nuclei, whatweb, arjun, commix, **web_recon**(自动侦察), **web_form_parser**(表单提取) | `docker exec ctf-box python3 /root/agent-work/web_recon.py URL` |
| 网络 | nmap, tcpdump, socat, hydra, netcat | `docker exec ctf-box nmap -sV TARGET` |
| 密码 | john, hashcat, hashid, cewl | `docker exec ctf-box john hash.txt` |
| 取证 | binwalk, foremost, steghide, exiftool, pngcheck, tshark, zsteg, sleuthkit, bulk-extractor | `docker exec ctf-box binwalk -e file` |
| Pwn | gdb, gdb-multiarch, radare2, pwndbg, pwntools(python), ROPgadget, one_gadget | `docker exec ctf-box python3 -c "import pwn; ..."` |
| 后渗透 | crackmapexec, evil-winrm, impacket-scripts, chisel, metasploit-framework, smbclient, smbmap | `docker exec ctf-box crackmapexec smb TARGET` |
| 逆向 | GhidraMCP(:8089/:8766), jadx, ysoserial, jwt_tool, phpggc, Gopherus, tplmap, XSStrike | MCP 自动连接 `localhost:8766` |
| 专项 | seclists(/usr/share/seclists/), Web-Fuzzing-Box(/usr/share/wordlists/Web-Fuzzing-Box/) | `docker exec ctf-box ffuf -w /usr/share/seclists/...` |

---

### 规则十-B：工具委派策略（容器优先，禁止造轮子）

> **铁律：能用 Docker 容器现有工具的，绝不写 Python 脚本。**

| 你要做什么 | 用这个（Docker 里已有） | 不要做 |
|-----------|------------------------|--------|
| 端口扫描 | `nmap -sV -sC` | 别写 socket 扫描脚本 |
| SQL 注入 | `sqlmap -u URL --batch` | 别写 requests 手工注入 |
| 目录爆破 | `ffuf -w /usr/share/seclists/Discovery/Web-Content/common.txt` | 别写 for 循环 curl |
| 密码破解 | `john` / `hashcat` | 别写 Python 暴力循环 |
| 文件格式识别 | `file` / `binwalk` / `strings` | 别写 magic bytes 判断 |
| 隐写提取 | `zsteg` / `steghide` / `binwalk -e` | 别写 PIL 像素分析 |
| 二进制分析 | GhidraMCP / `gdb` / `objdump` | 别写 `struct.unpack` 解析 |
| Web 侦察 | `python3 /root/agent-work/web_recon.py URL` | 别写 requests.get+正则 |
| 表单解析 | `python3 /root/agent-work/web_form_parser.py < page.html` | 别写 BeautifulSoup 从头写 |
| 反序列化 | `ysoserial` / `phpggc` / `Gopherus` | 别手写 payload 构造链 |
| JWT | `jwt_tool` | 别写 PyJWT 脚本 |

**写 Python 脚本的唯一理由：** 容器里没有任何工具能完成当前任务（如自定义 exploit、格基约简 SageMath、特殊协议逆向）。

**判断标准：** 动手前先问自己——"Docker 容器里有工具做这件事吗？" 有就不用写脚本。

### 规则十-C：知识库搜索触发

以下任一信号出现 → 立刻 `Grep` 查 Obsidian Wiki，不延迟：

- 版本号（Flask/2.1, uvicorn/0.20, PyDash...）
- 错误信息含产品名（"Werkzeug debugger", "Django CSRF", "Apache Struts"）
- 题目描述/hint 中有 CVE 编号或版本号
- misc/cloud/blockchain + 非常规服务
- 已试 3 种标准 web 攻击向量（sqli/xss/ssrf）均无果
- executor 返回 `highest_anomaly` 含版本号

---

### 规则十一：Flag 抓取（增强版）

#### Flag 正则模式（优先级从高到低）
```
grep -iE '(flag|ctf|eno|htb|pico|dctf|tfcctf|crew|justctf|idek|csaw|wolv|glacier|kalmar|amateurs|bcactf|angstrom|patriot|nitectf|seccon|defcon|hackaday|inctf|balsn)\{[^}]+\}'
```

#### 编码/加密 Flag 检测
```
strings output.bin | grep -iE '^[A-Za-z0-9+/=]{20,}$'      # Base64
strings output.bin | grep -iE '^[0-9A-Fa-f]{20,}$'          # Hex
```

#### 全盘扫
```bash
# 用 Grep 工具递归搜索，模式同上
```

- 找到 flag → 输出 → **停止**
- 多候选 → 选与题目最相关的
- **不做中间产物分析报告**

---

### 规则十二：错误处理

```
exploit 失败 → 打印实际输出（非预期输出），分析报错信息
远程 exploit → 先本地测试，再打远程（Web 题可直连）
2 轮失败 → 强制 Pivot，不停留
脚本报错 → 读 traceback 最后一行的行号，直接修
```

---

### 规则十三：临时文件管理

- PowerShell 长脚本（>10 行）→ `Write` 到 `<cwd>/temp.ps1` → 执行 → `DeleteFile`
- Python 脚本 → `Write` 到 `<cwd>/solve.py` → 执行 → 保留不删（供 writeup 引用）
- 中间产物 → 放 `<cwd>/attachments/` 或 `<cwd>/temp/`
- **解题完成后清理所有 `temp.*` 文件**

---

### 规则十四：跨类别/多阶段题目处理

- 题目明显跨类别（如 Web 获取加密数据 + Crypto 解密）→ 按阶段拆子 Todo
- 交互式题目（SSH/nc Q&A/多轮对话）→ 用 `pwntools` 的 `interactive()` 或 `sendlineafter()`
- Docker 容器题 → 先 `docker-compose up -d`（如提供），再按服务分类

---

### 规则十五：知识库迭代归档（解题完成强制执行）

#### 15.1 收尾必归档

flag + writeup 完成后，**强制复盘**，提炼：
- 踩坑点 / 走偏方向
- 非常规突破口
- 冷门知识点

#### 15.2 分级标记

- **【新增冷门题型】**：不在 Skill 覆盖范围内 → 完整收录
- **【经典题型补充】**：常规题型但有本题独有细节 → 精简入库

#### 15.3 入库格式（Obsidian Wiki 格式，含 YAML frontmatter）

```yaml
---
title: <题目名>
tags: [<题型标签>, <新增冷门|经典补充>]
category: web | crypto | pwn | reverse | forensics | misc | ai-ml | cloud-security | code-audit | malware | osint
triggers: [<中英双语关键词>, <CVE编号>, <http-header>, <路径名>, <题面常见词>]
created: YYYY-MM-DD
updated: YYYY-MM-DD
sources: [raw/ctf-solutions/<题目名>]
---
# <题目名>
- 标签: [<题型>] [<标记>]
- 赛题来源: <CTF名称/平台>
- 关键考点: <1-2 个核心技术点>
- 解题误区: <最容易走错的方向>
- 破题突破口: <关键一步>
- 核心脚本/命令: <最关键的 solve 代码片段>
```

写入 `C:\Users\ZZH\Documents\Obsidian Vault\Wiki\Wiki\<赛道>\<题目名>.md`

#### 15.4 知识库按需触发

仅当 2 轮卡壳、陌生格式、方向摇摆时触发，**优先搜 triggers 字段**：
```
Grep -i "triggers:.*<关键词>" "C:\Users\ZZH\Documents\Obsidian Vault\Wiki\Wiki\"
```
triggers 无命中再全文搜索：
```
Grep -ri "<关键词>" "C:\Users\ZZH\Documents\Obsidian Vault\Wiki\Wiki\"
```

#### 15.5 赛道分类（Obsidian Wiki 映射）

```
Wiki/
├── web/               # Web 安全（607 条含 GHSA）
├── crypto/            # 密码学
├── reverse/           # 逆向工程
├── pwn/               # 二进制漏洞利用
├── misc/              # 杂项
├── forensics/         # 取证
├── osint/             # 开源情报
├── malware/           # 恶意软件分析
├── ai-ml/             # AI 安全
├── cloud-security/    # 云安全
├── code-audit/        # 代码审计（876 条含 GHSA + CVE）
├── ctf/               # CTF 比赛实体页
└── rules/             # 规则文件
```

#### 15.6 检索指令

用户输入 "查看错题集 <赛道>" → 列出对应目录全部 `.md` 并总结核心考点。

---

### 规则〇-A：策略 Skill 加载（追加到 Batch 1）

Batch 1 中额外加载 `ctf-strategy` Skill：

```
Batch 1（并行）：
  ├── 0.1 Skill 加载：ctf-<category> + ctf-strategy
  ├── 0.2 文件侦察（如有附件）
  ├── 0.3 URL/服务侦察（如有 URL/端口）
  └── 0.4 无附件 → 读题分类
```

---

### 规则〇-B：PromptCompiler "审题"（零轮侦察后立即执行）

零轮侦察完成后，**必须先查知识库再动手**，按以下三步执行：

#### 步骤 1：知识库关键词匹配（如侦察中发现产品名/版本号/CVE）

```bash
Grep -i "triggers:.*(关键词|版本号|CVE编号)" "C:\Users\ZZH\Documents\Obsidian Vault\Wiki\Wiki\" -l
```

Wiki 当前 217 页，覆盖：
- `web/`：GHSA 注入/XSS/SSRF/认证绕过 + 历史做题经验 + AICTF 冠军实录
- `code-audit/`：200+ 产品级漏洞复现（Confluence/GitLab/Nexus/F5/禅道/XXL-JOB...）
- `cloud-security/`：K8s/Docker 逃逸/未授权
- `crypto/`：GHSA 加密 + 历史做题经验
- `pwn/`：ret2win/UAF/堆利用/WASM/V8 逃逸
- `rules/`：全局规则 + Skill 目录 + 启动提示词

**命中匹配** → 先读匹配到的 md 文件，提取利用链再动手。不读就动手 = 浪费时间。

**立即触发搜索的信号清单**（任一出现→立即查，不延迟）：
- 版本号（`/2.1`, `v0.20`, `PyDash`...）
- 错误页含产品名（`Werkzeug debugger`, `Django CSRF`, `Apache Struts`, `phpMyAdmin`）
- 题目描述/hint 中有 `CVE-20XX-XXXXX` 或版本号
- `misc/cloud/blockchain` 题目 + 非常规服务
- 已试 `sqli/xss/ssrf` 三种标准向量均无果
- executor 输出 `highest_anomaly` 或 `404/500` 含版本号

#### 步骤 2：攻击面分层映射（必做，5 层）

```
1. Filesystem 层：文件权限、SUID/SGID、writable 目录、symlink、mounts
2. OS Primitives 层：cron jobs、PATH、环境变量、capabilities、/proc 暴露
3. Process/Service 层：运行中的守护进程、IPC、socket、scheduler 任务
4. Application 层：具体技术栈/框架/语言版本、配置文件、插件、路由
5. Network 层：暴露端口、内部服务、metadata 端点、代理/网关
```

每层只陈述可观察事实 + 证据来源，不写攻击猜测。

#### 步骤 3：结构化输出（三要素）

```
<software_fingerprint> Server: Apache/2.4.41, Framework: Flask/2.1.0, Runtime: Python/3.10 </software_fingerprint>
分析优先级：[最高价值未解疑点] > [次高] > [低]
关键未解问题：1)... 2)... 3)...
```

Web 题软件指纹必写。输出格式简洁，不写长篇分析。

---

### 规则三-A：Reflection 停滞检测增强（替换原规则三）

| 级别 | 阈值 | 动作 |
|------|------|------|
| L1 SOFT_WARN | 同类操作 ≥3 次 | 问自己：方向在绕圈吗？列出替代方向 |
| L2 HARD_REFLECT | 同类操作 ≥5 次 | **强制停。** 必须切换到不同类别的操作 |
| CHECKPOINT | 每 15 次工具调用 | 回顾：离 flag 近了吗？ |

操作类型分类与硬阻断策略：
- `path_scan`：curl/ffuf/gobuster/dirsearch → L2 硬阻断
- `config_query`：探测 versioning/logging/acl/policy 参数 → L2 硬阻断
- `credential_guess`：hydra/john/brute/login → 无硬阻断
- `api_variant`：POST/PUT + -d 参数变体 → 无硬阻断

**L2 硬阻断后禁止**：换参数重试同一接口、继续同类工具调用、"再试一次"。

#### HTTP 状态码快速响应（Web 题专用，无重试立即动作）

| 状态码 | 动作 |
|--------|------|
| **405** | 停用当前方法。按 GET→POST→PUT→PATCH→DELETE→OPTIONS 逐个枚举允许方法，每个试一次即报结果 |
| **401/403**（已试默认凭据后）| 停。转枚举无认证端点、JS 源码审计、参数污染绕过 |
| **404 ×5 次连续** | 停路径扫描。转爬取已存在链接、robots.txt/sitemap.xml、Google cache |
| **429** | 立即加 ≥2s 延迟，不继续锤 |
| **exit 0 但输出含 "Permission denied"/"No such file"** | 视为无效操作，3 次触发降级报告 |

---

### 规则三-B：顾问模式

陷入 L2 HARD_REFLECT 或连续 Pivot 时，自问三个问题：

1. 当前假设是什么？（我以为这是什么漏洞？为什么？）
2. 有什么证据？（实际观察到的，不是推测的）
3. 如果我完全推翻假设，还有什么其他可能？

---

### 规则十五-A：Post-Compact Recovery（上下文压缩恢复）

上下文被压缩后，按此顺序恢复：

```
1. progress.md  → Compiled Task Context / Attack Tree / Dead Ends / Current Phase
2. findings.log → next_action / paths_not_tried / 验证状态
3. hint.md      → 比赛提示（如有）
4. attack_timeline.md → 仅在 1-3 无法解释事实时
```

**禁止恢复后立即操作**。必须先理解当前阶段。

---

### 规则十五-B：知识库检索优先 triggers 匹配

触发知识库检索时，优先搜 `triggers` 字段：

```
Grep -i "triggers:.*<关键词>" "C:\Users\ZZH\Documents\Obsidian Vault\Wiki\Wiki\"
```

triggers 无命中再全文搜索：
```
Grep -ri "<关键词>" "C:\Users\ZZH\Documents\Obsidian Vault\Wiki\Wiki\"
```

命中则该页的 `tags` 和 wikilinks 指示进一步探索方向。

---

### 规则十五-C：Flag 自动提交（Playwright MCP）

当在 CTF 比赛平台上操作且获得 flag 后，用 Playwright MCP 自动提交：

1. `browser_snapshot` 获取当前页面 → 找到目标题目
2. 点击题目进入详情/提交页
3. 找到 flag 输入框 → `browser_fill` 填入
4. 点击提交按钮
5. `browser_snapshot` 验证提交结果（成功/失败/已提交）
6. 如提交框不可见 → 尝试滚动或切换标签

独立做题（非平台模式）→ 直接输出 flag 即可，不需要此规则。
