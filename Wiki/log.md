---
title: Activity Log
tags: [meta, log]
created: 2026-06-07
updated: 2026-06-08
---

# Activity Log

Chronological record of all operations on the wiki.

## [2026-06-07 19:18] init | LLM Wiki Methodology

- Initialized wiki structure based on [[../LLM Wiki]] methodology
- Created `raw/` directory for source documents
- Created `wiki/` directory for wiki pages
- Created `AGENTS.md` with schema and workflow definitions
- Created `wiki/index.md` as content catalog
- Created `wiki/log.md` as activity log
- Configured Obsidian attachment path to `raw/assets/`

---

## [2026-06-07 19:35] ingest | 第二届梨花杯wp

- Read source: `raw/第二届梨花杯wp.md`
- Created `summary--第二届梨花杯wp` — 7 道 CTF 题解概要 *(页面已移除)*
- Created `ctf-梨花杯-2026` — 竞赛实体页 *(页面已移除)*
- Updated [[index]] with new entries

---

## [2026-06-07 20:XX] migrate | ctf-kb Phase 1 — 67 named pages

Migrated 67 named technical pages from ctf-kb (`.trae/ctf-kb/`) into wiki with proper directory structure:

**Web** (17 pages): SSRF, HTTP 请求走私, SSTI, 原型链污染, PHP LFI/反序列化, gopher SSRF, SQL 注入, NPS 代理, 运维/文档攻击面, Layer Breach 系列

**Code Audit** (12 pages): Aria2, DNS 域传送, FastAdmin, Grafana SSRF, InfluxDB, 快排CMS, MySQL UDF, Scrapyd, ShowDoc, Ueditor, 微信客户端, XXL-JOB

**Crypto** (4 pages): RSA 低指数, ECDSA nonce 重用, XOR 已知明文, CPA 侧信道

**Forensics** (3 pages): ICMP/DNS 隐写, 数据泄露溯源, 日志攻击链

**Misc** (5 pages): Base64+XOR, 损坏ZIP, 嵌套压缩包, 办公室爱情, 供应链投毒

**PWN** (4 pages): ret2win, ret2text, heap UAF, Docker 容器渗透

**Reverse** (7 pages): PyInstaller, ChaCha20 APK, DES APK, 字节码, PE SBOX, SMC, 凯撒RC4

**AI/ML** (11 pages): Agent 架构模式/对比, Observer 系统, 模型调度, 上下文压缩, MCP 接入, 零界论坛, 编排平台攻击面

**Cloud Security** (4 pages): 容器逃逸, Docker 渗透, Nacos, 数据库后渗透

- Added `category` and `triggers` fields to all frontmatter for agent matching
- All pages use kebab-case English filenames with Chinese in `title`
- Updated [[AGENTS.md]] with subdirectory structure + tags/triggers spec

---

## [2026-06-07 20:XX] migrate | ctf-kb Phase 2 — GHSA/CVE synthesis (8 concept pages)

Synthesized 1124 GHSA entries into 8 curated concept pages organized by vulnerability class:

**Web** (4 pages):
- [[web/ghsa-rce-overview]] — RCE/命令注入（~300 篇 web GHSA）
- [[web/ghsa-xss-csrf]] — XSS + CSRF + CORS（~50 篇）
- [[web/ghsa-ssrf-auth-bypass]] — SSRF + 认证绕过 + JWT/OAuth（~60 篇）
- [[web/ghsa-sqli-path-traversal]] — SQLi + 路径遍历 + 文件读写（~60 篇）

**Code Audit** (3 pages):
- [[code-audit/ghsa-rce-patterns]] — 代码审计 RCE 模式（~300 篇）
- [[code-audit/ghsa-privilege-escalation]] — 权限提升 + 反序列化（~50 篇）
- [[code-audit/ghsa-cloud-security]] — 容器/云/CI-CD 安全（~100 篇）

**Crypto** (1 page):
- [[crypto/ghsa-crypto-weakness]] — 加密/随机数漏洞（~15 篇）

- Added wikilink cross-references from 8 existing concept pages to GHSA pages
- GHSA raw files stay in `ctf-kb/` as authoritative source; wiki pages are curated indexes
- Updated [[AGENTS.md]] with GHSA handling instructions

---

## [2026-06-07 20:XX] fix | Repair + Connections (Phase 3)

**Fixes:**
- Created 2 missing pages: [[reverse/pyinstaller-cython-reverse]], [[ai-ml/llm-agent-orchestration-attack-surface]]
- Deleted duplicate `pwn/docker-container-pentest.md` (kept in `cloud-security/`)

**Connections:**
- Added `## Connections` section to all 50+ previously missing pages
- Established bidirectional wikilinks across all 9 subdirectories
- Added GHSA references from 8+ named pages → GHSA synthesis pages
- Updated [[AGENTS.md]] with GHSA/CVE handling workflow

**Index:** Updated to 78 total pages
**Log:** Appended this entry

---

## [2026-06-07 22:XX] migrate-fix | Phase 4 — 补充丢失页 + CVE 产品索引

**补充 8 个遗漏命名页**：

- `web/jwt-hs256-prng-pipeline-race` — JWT HS256 PRNG + Pipeline 竞态绕过
- `web/csrf-dangling-markup-token-leak` — CSP 下 Dangling Markup token 外带
- `crypto/hastad-coppersmith-linear-padding` — Hastad Broadcast + Coppersmith 线性填充 RSA
- `pwn/stack-shellcode-injection` — NX 关闭 → 栈 shellcode 注入
- `forensics/multi-layer-steganography` — Arnold + LSB + 零宽 + PYC 多层隐写
- `forensics/zip-encryption-png-ihdr-fix` — ZIP 加密破解 + PNG IHDR 修复
- `ai-ml/ctf-agent-benchmark-reference` — CTF Agent Benchmark 评测框架
- `ai-ml/prompt-injection-llm-jailbreak` — Prompt 注入与 LLM Jailbreak

**覆盖 ~130 个 CVE 产品漏洞页**：

- 新建 `code-audit/cve-product-index` — 按 RCE/SQLi/SSRF/反序列化 等分类的产品索引
- 更新 `ghsa-rce-patterns` triggers 加入 Apache OFBiz、Confluence、Drupal 等 30+ 产品名
- 更新 `ghsa-privilege-escalation` triggers 加入 Fastjson、Dubbo、OfBiz、Superset 等
- 更新 `ghsa-cloud-security` triggers 加入 Containerd、runC、K8s API 等具体 CVE 条目

**Index:** Updated to 86 total pages

---

## [2026-06-07 23:XX] enhance | PoC Quick Reference + AGENTS.md Solution Page Structure

**cve-product-index.md** — 新增 "关键 PoC 命令速查" 章节：
- 为 40+ 产品添加一行式核心验证命令（curl/Aria2/Fastjson/ThinkPHP/Redis RCE/Confluence/OFBiz 等）
- 按 Web 应用 / Apache 生态 / 中间件 / 云安全 / 企业版 分组
- 更新使用说明：agent 从产品名 → PoC 命令 → GHSA 合成页，3 步完成

**AGENTS.md** — 新增 "Solution Page Structure" 章节：
- **Solution Pages (CTF Writeups)** — 定义解题页格式（关键考点/解题误区/破题突破口/核心脚本）
- **Product CVE Pages** — 定义产品漏洞专题格式（指纹/验证顺序/最小PoC/常见漏扫原因）
- **CVE Product Index Pages** — 定义 PoC 速查表格式
- **Triggers 设计原则** — 展开版（中英双语/文件名变体/粒度原则/控制 5-20 词）
- 继承 `_CONVENTIONS.md` 全部规范，AGENTS.md 成为唯一规范来源

---

## [2026-06-07 20:50] migrate | .trae Phase 5 — Skills/Commands/Rules 全文入库

从 `.trae/` 将全部 CTF 相关技能、命令指南、规则文件全文搬迁至 Wiki：

**Phase A — Skills 深参考文件**（99 个文件）
- `skills/ctf-pwn/*.md` → `wiki/pwn/skill-*.md`（19 个 pwn 参考文件）
- `skills/ctf-crypto/*.md` → `wiki/crypto/skill-*.md`（17 个 crypto 参考文件）
- `skills/ctf-reverse/*.md` → `wiki/reverse/skill-*.md`（23 个 reverse 参考文件）
- `skills/ctf-forensics/*.md` → `wiki/forensics/skill-*.md`（16 个 forensics 参考文件）
- `skills/ctf-misc/*.md` → `wiki/misc/skill-*.md`（13 个 misc 参考文件）
- `skills/ctf-ai-ml/*.md` → `wiki/ai-ml/skill-*.md`（3 个 ai-ml 参考文件）
- `skills/ctf-malware/*.md` → `wiki/malware/skill-*.md`（3 个 malware 参考文件）
- `skills/ctf-osint/*.md` → `wiki/osint/skill-*.md`（2 个 osint 参考文件）
- `skills/code-audit/*.md` → `wiki/code-audit/skill-*.md`（3 个 code-audit 参考文件）

**Phase B — Command 解题指南**（11 个文件）
- `commands/{web,pwn,crypto,reverse,forensics,misc,ai-ml,malware,osint}.md` → 各赛道 `command-reference.md`
- `commands/{solve,writeup}.md` → `wiki/rules/command-*-reference.md`

**Phase C — 规则与目录**（5 个文件）
- `rules/project_rules.md` → `wiki/rules/project-rules.md`
- `ctf-startup-prompt.md` → `wiki/rules/startup-prompt.md`
- `ctf-skills-catalog.md` → `wiki/rules/skills-catalog.md`
- `builtin_skills/TRAE-debugger/SKILL.md` → `wiki/malware/skill-TRAE-debugger.md`

**Phase D — 历史 Writeup**
- `ctf-solutions/CrackMe_2_3/` → `wiki/Raw/ctf-solutions/CrackMe_2_3/`
- `ctf-solutions/WEB-TaxSystem_SSTI/` → `wiki/Raw/ctf-solutions/WEB-TaxSystem_SSTI/`

- 新增子目录：`malware/`, `osint/`, `rules/`
- 全部 skill 文件使用 `skill-` 前缀，command 文件使用 `command-` 前缀
- 所有文件以 `skill-` 前缀命名避免与已有命名页冲突
- Updated [[AGENTS.md]] with new subdirectory structure and skill file conventions
- Updated [[index.md]] with Skill References and Rules sections

---

## [2026-06-07 22:10] migrate | .trae Phase 6 — 遗漏赛题补充 + Cloud CVE 索引

**6 个遗漏赛题页：**
- [[web/just-proto]] — Node.js 原型污染 + exec() + 盲 Oracle
- [[pwn/jinqi-easy-wasm-v8-sandbox-bypass]] — V8 Wasm GC 沙箱绕过
- [[reverse/jinqi-labyrinth-maze-binary]] — 2048×2048 ELF 迷宫 BFS
- [[reverse/rerere-windows-pe-sbox-xor]] — Windows PE S-BOX + XOR
- [[reverse/jinqi-hardware-flags-obfuscated-flag-check]] — GF(2) 线性变换 + GDB oracle
- [[ai-ml/jinqi-modelhub-pickle-deserialization]] — PyTorch Pickle RCE

**2 个已合并进已有页：**
- `AICTF-SQL注入-列数探测.md` → 已合并进 [[web/aictf-sqli-ssrf-tunnel]]
- `baby_re.md` → 已合并进 [[reverse/pyinstaller-cython-reverse]]

**1 个 Cloud CVE 索引页：**
- [[cloud-security/cloud-cve-product-index]] — 覆盖 28 个 cloud-security 产品漏洞 CVE

**更新：** index.md（ingested: 217）、log.md

---

## [2026-06-07 22:20] organize | 整理 raw/ 目录

- 将 `.trae/ctf-solutions/{CrackMe_2_3, WEB-TaxSystem_SSTI}` (25 files) 复制到 `raw/ctf-solutions/`
- 修复之前 Phase 5 中 ctf-solutions 未实际落盘的问题
- 更新 `index.md` Raw Sources 路径：`Raw/` → `raw/`（vault 根）
- 更新 `AGENTS.md` raw/ 目录树：新增 `ctf-solutions/` 子目录
- 补充引用 `raw/第二届梨花杯wp.md`

---

## [2026-06-07 22:35] ingest | archiving raw/ctf-solutions → wiki pages

**从 raw writeup 增强已有 wiki 页面：**
- [[web/flask-ssti-session-forgery]] — 补全 Step-by-Step 审计流程（源码审计 → SSTI 验证 → SECRET_KEY 泄露 → session 伪造）+ 完整 exploit 脚本
- [[reverse/des-apk-rodata]] — 补全多层 DEX 架构图（classes3.dex → assets/classes3.dex → Native）、smali 代码分析（wide.verify / wide.callNativeMethod）、Native 函数地址表、DES-ECB 验证逻辑伪代码、hex 逐字节解码示例

**raw 中已有档案不变：**
- `raw/第二届梨花杯wp.md` — 已在 Phase 0 摄入到 `ctf-梨花杯-2026` + `summary--第二届梨花杯wp` *(页面已移除)*
- `raw/ctf-solutions/CrackMe_2_3/` — 完整 Writeup + solve.py + output.txt
- `raw/ctf-solutions/WEB-TaxSystem_SSTI/` — 完整 Writeup + 17 个 exploit 脚本 + tax.db

---

## [2026-06-07 23:00] ingest | LitCTF 2026 WEB方向全WP → wiki

**来源：** `raw/LitCTF 2026 WEB方向全WP.md`（mp.weixin.qq.com 青岑）

**4 个新建技术页：**
- [[web/wide-byte-gbk-injection]] — GBK 宽字节注入 + Union + hex 绕过 MariaDB
- [[web/mako-ssti-code-block-bypass]] — Mako `<% %>` 代码块 + getattr() 反射绕过 WAF
- [[web/go-reverse-jwt-key-extraction]] — Go 逆向提取 JWT HS256 密钥 + JWT Editor 伪造
- [[web/kkfileview-arbitrary-file-read]] — kkFileView Base64 路径遍历任意文件读

**1 个增强已有页：**
- [[web/ops-console-attack-surface]] — 新增 §5 Actuator heapdump → ShiroKey (JDumpSpider) → Shiro GCM 越权完整攻击链

**竞赛实体页 + 摘要：**
- [[ctf/litctf-2026]] — LitCTF 2026 5 题 Web WP 竞赛索引（新 `wiki/ctf/` 子目录）
- [[ctf/summary--litctf-2026-web-wp]] — 源文档摘要

---

## [2026-06-07 23:40] ingest | 第三届"长城杯"防护赛决赛 WP → wiki

**来源：** `raw/第三届"长城杯"网数智安全大赛(防护赛)决赛-部分WP.md`（wallkone）

**4 个新建技术页：**
- [[reverse/renpy-pe-xor-23]] — Ren'Py .rpyc 反序列化 + 嵌入 PE + XOR 0x23
- [[reverse/springboot-custom-vm-pcap-aes]] — Spring Boot 自定义栈式 VM + Rust AES-CBC + PCAP 解密
- [[reverse/unity-il2cpp-themida-aes]] — Unity IL2CPP + Themida 内存 Dump + AES-CBC
- [[ai-ml/lattice-cnn-lwe-small-dimension]] — 小维度 LWE 噪声枚举 5^8 + CNN 特征 + AES 密钥推导

**竞赛实体页 + 摘要：**
- [[ctf/changchengbei-2026]] — 第三届长城杯 4 题 WP 竞赛索引
- [[ctf/summary--changchengbei-wp]] — 源文档摘要

**填补知识空白：**
- Ren'Py 游戏逆向（全新游戏引擎逆向方向）
- 自定义栈式 VM（Spring Boot JAR 隐藏 VM + Rust ELF + PCAP 多阶段逆向链）
- Unity IL2CPP + Themida（内存 dump 脱壳 + IL2CPP 元数据定位 + 假 flag 干扰）
- LWE-like 小维度枚举（公开 CNN + 隐藏向量 + 小噪声暴力枚举 5^8）

**更新：** index.md（101 命名页 + 3 条记录）、log.md

**更新：** AGENTS.md（含 ctf/ 子目录）、index.md（新增 Web 4 页 + CTF Competitions + Source Summaries 分区）、log.md

---

## [2026-06-08 19:00] ingest | 2026 软件系统安全赛 初赛 WP → wiki

**来源：** `raw/2026 软件系统安全赛 初赛 wp.md`（mp.weixin.qq.com Spirit Team）

**5 个新建技术页：**
- [[web/file-protocol-redis-crlf-pickle-rce]] — file:// SSRF → Redis CRLF 协议注入 → RestrictedUnpickler 绕过 Pickle RCE
- [[web/thymeleaf-ssti-fragment-expression]] — Thymeleaf `__|$${...}|__::.x` fragment expression 绕过 + 7z SUID 提权
- [[crypto/prng-state-recovery]] — Java Random 48-bit LCG 状态恢复 + 密码预测
- [[misc/behinder-traffic-decryption]] — 冰蝎 AES-ECB Filter 注入 + AES-GCM C2 流量解密
- [[misc/png-idat-dp-repair]] — PNG IDAT 损坏 DP 对齐修复 + LSB + ZIP CRC32 爆破 + 零宽字符

**竞赛实体页：**
- [[ctf/2026-software-security-competition-qualifier]] — 2026 软件系统安全赛初赛 7 题 WP（Web/Pwn/Misc/Re/Crypto 全赛道 AK）

**关键新技术点：**
- `RestrictedUnpickler` 白名单绕过 — 只放行 `getattr` 仍可通过 `OnlineUser.__init__.__globals__` 拿到 `os.system`
- `file://` SSRF → Redis CRLF 注入 — 将 avatar_url HTTP 请求重定向到 127.0.0.1:6379 写恶意键
- XMLRPC 隐蔽 RCE — `/proc/<pid>/cmdline` 遍历子进程发现硬编码 token 的 XMLRPC 服务
- Thymeleaf 高版本 SSTI 绕过 — `${...}` 被拦截后用预处理字面量 `__|$${...}|__::.x` 拼接
- 7z SUID 读文件 — `7z a -ttar -an -so /flag` 输出到 stdout
- PNG IDAT DP 修复 — 动态规划枚举 filter_byte 偏移量最大化合法行数

**更新：** index.md（+6 页，ingested: 223）、log.md

---

## [2026-06-08 19:10] lint | index↔disk 核对修复

- **Broken link 修复**: `[[raw/LitCTF 2026 WEB方向全WP]]` → 原始文件不存在，改为 `*(raw/LitCTF 2026 WEB方向全WP)* | *(已摄入，原始文件已移除)*`
- **10 个 orphan skill 文件补入 index.md**:
  - PWN: `skill-advanced-exploits-2/3/4`（拆开 `~` range）
  - Reverse: `skill-patterns-ctf`, `skill-patterns-ctf-2`, `skill-patterns-runtime`, `skill-languages-platforms`
  - Misc: `skill-games-and-vms-2/3`
  - OSINT: `skill-geolocation-and-media`
- **所有 `~` range 展开为逐行**: PWN advanced-exploits, Reverse patterns/languages/platforms, Misc games-and-vms
- **ingested 计数修正**: 223 → 221（排除 index + log）

---

## [2026-06-09 22:00] ingest | 6 篇 raw 文档 → wiki

**来源：**
- `raw/2025 ACTF Web 复现.md`（妙尽璇机, 安全KER）
- `raw/2026长城杯AWDP半决赛WP(西南地区WEB) - Rycarls little blog.md`
- `raw/NewStar-web week1-2 - Rycarls little blog.md`
- `raw/NewStar_Week3_Web - Rycarls little blog.md`
- `raw/Newstar web week4 - Rycarls little blog.md`
- `raw/安洵杯部分WP-安全KER - 安全资讯平台.md`

**5 个新建竞赛实体页：**
- [[ctf/actf-2025]] — ACTF 2025 Web 3 题（Flask 命令注入 + 邮件 SSTI + Pandoc 注入）
- [[ctf/changchengbei-awdp-2026-semi]] — 长城杯 AWDP 半决赛西南 Web（iconv bypass + ZipSlip + SSRF）
- [[ctf/newstar-2025]] — NewStar CTF 2025 Web 全4周 17 题（从 robots.txt 到 POP 链）
- [[ctf/anxunbei-wp]] — 安洵杯部分 WP（Bash 限制绕过 / 堆利用 / Coppersmith / SMC / 取证）

**2 个新建技术页：**
- [[web/flask-unsign-session-forgery]] — `/proc/self/environ` 泄露 secret_key → `flask-unsign` 伪造 admin session
- [[web/iconv-encoding-bypass-path-traversal]] — iconv `//IGNORE` 编码转换丢弃非法字节绕过路径黑名单

**更新：** index.md（+6 页，ingested: 227）、log.md
