---
title: Wiki Index
tags: [meta, index]
created: 2026-06-07
updated: 2026-06-07
ingested: 217
---

# Wiki Index

This is the catalog of all pages in the wiki. Each entry includes a link, a one-line summary, and metadata.

## Meta

| Page | Summary | Updated |
|------|---------|---------|
| [[index]] | This index — catalog of all wiki pages | 2026-06-07 |
| [[log]] | Chronological activity log | 2026-06-07 |

## Entity Pages

| Page | Summary | Updated |
|------|---------|---------|
| [[ctf-梨花杯-2026]] | 第二届梨花杯 CTF 竞赛概况 | 2026-06-07 |

## Web

| Page | Category | Summary |
|------|----------|---------|
| [[web/ssrf-gopher-techniques]] | SSRF | gopher 协议发送任意 TCP 数据，多层内网穿透 |
| [[web/http-request-smuggling]] | HTTP 走私 | CL/TE 解析差异触发双重响应绕过 Nginx |
| [[web/flask-ssti-session-forgery]] | SSTI | Flask render_template_string SSTI + 黑名单绕过 + SECRET_KEY泄露 + itsdangerous 伪造 session 越权 |
| [[web/prototype-pollution-command-injection]] | Node.js | 原型链污染 + exec() 命令注入盲外带 |
| [[web/php-file-inclusion-filter]] | PHP LFI | php://filter/convert.base64-encode 任意文件读取 |
| [[web/php-deserialization-balance-tamper]] | PHP 反序列化 | __destruct 自动篡改余额 |
| [[web/sql-direct-postgresql]] | SQL | PostgreSQL 直连 + psql 查询 |
| [[web/client-validation-bypass]] | 前端安全 | 抓包修改分数参数绕过客户端校验 |
| [[web/ops-console-attack-surface]] | 运维平台 | Grafana/Nacos/JumpServer + Actuator heapdump → ShiroKey + JDumpSpider/ShiroAttack2 攻击链 |
| [[web/enterprise-document-report-attack-surface]] | 文档报表 | 文档/OA/报表中心的业务链路攻击面 |
| [[web/aictf-layer-breach]] | 多层渗透 | gopher SSRF 打通内网 → Flask SQLi → 多层穿透 |
| [[web/aictf-layer-breach-champion]] | 实战记录 | 冠军 425 轮完整解题链（6 flags） |
| [[web/aictf-sqli-ssrf-tunnel]] | SQL 注入 | 通过 SSRF 隧道间接对内部 Flask 做注入 |
| [[web/aictf-nps-proxy-tunnel]] | 代理隧道 | NPS 客户端建立 SOCKS 隧道 + 文件浏览器直读 flag |
| [[web/aictf-url-encoding-multi-proxy]] | 编码问题 | 多层代理 URL 编码踩坑记录（+ vs %20） |
| [[web/multi-proxy-encoding-pitfalls]] | 编码陷阱 | 多层代理场景的 HTTP 参数编码失真分析 |
| [[web/aictf-enterprise-helpdesk]] | 企业系统 | Helpdesk 权限提升与数据泄露 |
| [[web/ghsa-rce-overview]] | GHSA | RCE/命令注入合集（~300 篇） |
| [[web/ghsa-xss-csrf]] | GHSA | XSS + CSRF + CORS 合集（~50 篇） |
| [[web/ghsa-ssrf-auth-bypass]] | GHSA | SSRF + 认证绕过 + JWT/OAuth 合集（~60 篇） |
| [[web/ghsa-sqli-path-traversal]] | GHSA | SQLi + 路径遍历 + 文件读写合集（~60 篇） |
| [[web/jwt-hs256-prng-pipeline-race]] | JWT | JWT HS256 PRNG 暴力枚举 + HTTP Pipeline 竞态绕过限流 |
| [[web/csrf-dangling-markup-token-leak]] | CSRF | CSP script-src 'none' 下 Dangling Markup 外带 token |
| [[web/just-proto]] | Node.js | 原型链污染 + exec() 命令注入 + 盲 Oracle exit code 外带 |
| [[web/wide-byte-gbk-injection]] | SQLi | GBK 宽字节 %df' → Union + hex 绕过 MariaDB |
| [[web/mako-ssti-code-block-bypass]] | SSTI | Mako `<% %>` 代码块 + getattr() 反射绕过 WAF |
| [[web/go-reverse-jwt-key-extraction]] | JWT | Go 二进制逆向提取 JWT HS256 密钥 + BurpSuite JWT Editor 伪造 |
| [[web/kkfileview-arbitrary-file-read]] | LFI | 前端 JS 泄露凭证 + kkFileView Base64 urlPath 任意文件读 |

## Crypto

| Page | Summary |
|------|---------|
| [[crypto/rsa-low-exponent-attack]] | RSA e=3 低指数攻击，直接开立方还原明文 |
| [[crypto/ecdsa-nonce-reuse]] | ECDSA nonce 重用导致私钥恢复 |
| [[crypto/xor-known-plaintext-docx]] | XOR 已知明文攻击恢复 DOCX，需 ROT13 二次解码 |
| [[crypto/cpa-side-channel-aes]] | CPA 功耗分析恢复 AES-128 密钥 |
| [[crypto/ghsa-crypto-weakness]] | GHSA 加密/随机数漏洞合集（~15 篇） |
| [[crypto/hastad-coppersmith-linear-padding]] | Hastad Broadcast + Coppersmith 线性填充 RSA |

## PWN

| Page | Summary |
|------|---------|
| [[pwn/ret2win]] | gets() 栈溢出 → backdoor → system("/bin/sh") |
| [[pwn/ret2text-stack-alignment]] | No PIE 栈溢出 + movaps 栈对齐 |
| [[pwn/heap-uaf-fastbin-overlap]] | UAF → Fastbin Overlap → 函数指针劫持 |
| [[pwn/docker-container-pentest]] | Docker 容器 flag 定位与提取 |
| [[pwn/stack-shellcode-injection]] | NX 关闭 → 栈溢出 + shellcode 注入 execve("/bin/sh") |
| [[pwn/jinqi-easy-wasm-v8-sandbox-bypass]] | V8 Wasm GC ref.test 类型混淆 → addrof/fakeobj → JIT RWX shellcode 执行 |

## Reverse

| Page | Summary |
|------|---------|
| [[reverse/pyinstaller-cython-reverse]] | PyInstaller 解包 + Cython .pyd 逆向 |
| [[reverse/chacha20-apk-native]] | APK JNI ChaCha20 Native 逆向 |
| [[reverse/des-apk-rodata]] | APK 多层 DEX 嵌套 + Native DES-ECB + .rodata hex 直接提取 flag |
| [[reverse/python-bytecode-tracing]] | Python 3.12 pyc 字节码追踪 |
| [[reverse/simple-smc]] | SMC 双层 XOR + GF(2) 线性变换矩阵求逆 |
| [[reverse/caesar-rc4-leet-speak]] | 魔改凯撒 + XOR 两段拼接 leet-speak flag |
| [[reverse/jinqi-labyrinth-maze-binary]] | 2048×2048 ELF 迷宫 + UD2 方向编码 + BFS 寻路 |
| [[reverse/rerere-windows-pe-sbox-xor]] | Windows PE .rdata 段 S-BOX + XOR 查表解码 |
| [[reverse/jinqi-hardware-flags-obfuscated-flag-check]] | GF(2) 线性变换逆推 + GDB oracle + RDTSC 反调试 |

## Forensics

| Page | Summary |
|------|---------|
| [[forensics/covert-channel-icmp-dns]] | ICMP TTL + DNS TXT 双隐写信道 |
| [[forensics/data-leak-trace-purge]] | 数据泄露溯源与处置清单生成 |
| [[forensics/log-forensics-attack-chain]] | Apache log + mod_dumpio 攻击链还原 |
| [[forensics/multi-layer-steganography]] | Arnold 置乱 + LSB + 零宽字符 + PYC ROT/XOR 多层隐写 |
| [[forensics/zip-encryption-png-ihdr-fix]] | WinZip AES 加密破解 + PNG IHDR 高度隐藏修复 |

## Misc

| Page | Summary |
|------|---------|
| [[misc/base64-xor-double-encoding]] | BASE64 + XOR 双层编码解密 |
| [[misc/damaged-zip-base64]] | 损坏 ZIP 包手动提取 + Base64 解码 |
| [[misc/maze-nested-zip]] | 嵌套压缩包 + 非标准 Base64 干扰字符 |
| [[misc/office-love-multi-stego]] | Word/PDF/PPTX 多载体嵌套隐写 |
| [[misc/aictf-supply-chain-poisoning]] | npm/pip 供应链投毒场景分析 |

## AI / ML

| Page | Summary |
|------|---------|
| [[ai-ml/ai-agent-architecture-patterns]] | M/S/O 三层解耦 / 黑板蚁群 / Less Than Nothing |
| [[ai-ml/ai-agent-architecture-comparison]] | 开源 AI 渗透 Agent 架构优劣对比 |
| [[ai-ml/ai-solver-rules]] | AI 解题 10 条强制规则 + Observer 设计原则 |
| [[ai-ml/model-scheduling-strategy]] | 48K 轮模型分配数据与调度策略 |
| [[ai-ml/observer-system-prompt-analysis]] | Observer 旁路模式 / Ideas+Memory 看板 |
| [[ai-ml/multi-model-context-compression]] | RTK Rewrite 三层上下文压缩 |
| [[ai-ml/ctf-agent-open-source-overview]] | CTF Agent 开源方案速查（Stars 排行） |
| [[ai-ml/challenge-config-reference]] | AICTF 赛题配置参考 |
| [[ai-ml/ctf-api-mcp-paradigm]] | CTF MCP/API 接入范式 |
| [[ai-ml/zero-bound-ai-social-network]] | AI 社交网络 4 类挑战攻略 |
| [[ai-ml/llm-agent-orchestration-attack-surface]] | LLM Agent 编排平台攻击面（Dify/Langflow/ComfyUI/MCP） |
| [[ai-ml/ctf-agent-benchmark-reference]] | CTF Agent 评测 Benchmark 框架参考（BoxPwnr/HackSynth/XBOW） |
| [[ai-ml/prompt-injection-llm-jailbreak]] | Prompt 注入与 LLM Jailbreak 攻击技巧 |
| [[ai-ml/jinqi-modelhub-pickle-deserialization]] | PyTorch .pt Pickle 反序列化 RCE（__reduce__ → eval） |

## Cloud Security

| Page | Summary |
|------|---------|
| [[cloud-security/container-escape]] | 容器逃逸综合技术速查 |
| [[cloud-security/docker-container-pentest]] | Docker 容器 flag 定位与提取 |
| [[cloud-security/nacos-unauthorized-access]] | Nacos 未授权创建用户/查看配置 |
| [[cloud-security/database-post-exploitation]] | MySQL/PostgreSQL/Redis/MSSQL 提权与 RCE 速查 |
| [[cloud-security/cloud-cve-product-index]] | Docker/K8s/VMware/MinIO/Nacos 产品漏洞索引（28 个 CVE） |

## Code Audit

| Page | Summary |
|------|---------|
| [[code-audit/aria2-arbitrary-file-write]] | Aria2 RPC 任意文件写入 + cron 反弹 |
| [[code-audit/dns-zone-transfer]] | DNS AXFR 域传送泄露子域名 |
| [[code-audit/fastadmin-rce]] | FastAdmin 分片上传 RCE |
| [[code-audit/grafana-ssrf]] | Grafana 数据源 SSRF + 云元数据 |
| [[code-audit/influxdb-unauthorized-access]] | InfluxDB JWT 空密钥伪造 token |
| [[code-audit/kuaipai-cms-file-upload]] | 快排 CMS 后台任意文件上传 |
| [[code-audit/mysql-udf-privilege-escalation]] | MySQL UDF 插件提权 RCE |
| [[code-audit/scrapyd-unauthorized-access]] | Scrapyd 未授权部署恶意 egg |
| [[code-audit/showdoc-file-upload]] | ShowDoc 前台文件上传绕过 |
| [[code-audit/ueditor-vulnerabilities]] | 百度 UEditor 多漏洞（文件读写/SSRF/XSS） |
| [[code-audit/wechat-client-rce]] | 微信 Windows 客户端 Chrome V8 引擎 RCE |
| [[code-audit/xxl-job-rce]] | XXL-JOB 弱口令 GLUE 模式 RCE |
| [[code-audit/ghsa-rce-patterns]] | GHSA 代码审计 RCE 模式（~300 篇） |
| [[code-audit/ghsa-privilege-escalation]] | GHSA 权限提升+反序列化（~50 篇） |
| [[code-audit/ghsa-cloud-security]] | GHSA 容器/云/CI-CD 安全（~100 篇） |
| [[code-audit/cve-product-index]] | CVE 产品漏洞索引 — 按产品线分类（~130 个产品） |

## Source Summaries

| Page | Summary | Updated |
|------|---------|---------|
| [[summary--第二届梨花杯wp]] | 7 道 CTF 题解（5 Web + 2 Crypto） | 2026-06-07 |

## Synthesis & Comparisons

*(None yet)*

---

## Skill References（技能速查参考）

各赛道完整 `skill-*.md` 文件列表。这些文件是 `.trae/skills/` 的全文镜像，包含每种技术的 PoC 代码和攻击模式。Agent 解题时 Grep 搜索自动命中。

### PWN

| File | Content |
|------|---------|
| [[pwn/skill-SKILL.md]] | PWN 入口速查 — 前置条件/快速命令/赛道分流 |
| [[pwn/skill-overflow-basics.md]] | 栈溢出 15+ 变体（ret2win/canary/CSV注入/OOB）|
| [[pwn/skill-rop-and-shellcode.md]] | 核心 ROP（ret2libc/syscall/ret2csu/bad char 绕过）|
| [[pwn/skill-rop-advanced.md]] | 高级 ROP（SROP/seccomp 绕过/架构切换）|
| [[pwn/skill-format-string.md]] | 格式化字符串（%n GOT 覆写/盲打/ROT13 编码）|
| [[pwn/skill-advanced.md]] | 高级综合（seccomp/JIT/ret2dlresolve）|
| [[pwn/skill-heap-techniques.md]] | House 系列（Apple2/Einherjar/Orange/Lore/Force）|
| [[pwn/skill-heap-techniques-2.md]] | CTF-Writeup 堆变体（UAF vtable/tcache 中毒/FSOP）|
| [[pwn/skill-heap-fsop.md]] | FILE 结构体（_IO_FILE）利用技术 |
| [[pwn/skill-advanced-exploits.md]]~[[pwn/skill-advanced-exploits-5.md]] | 高级利用专题 1-5（VM/类型混淆/io_uring/Windows SEH/神经网络 OOB）|
| [[pwn/skill-sandbox-escape.md]] | 沙箱逃逸（自定义 VM/FUSE/CPU 模拟器注入）|
| [[pwn/skill-kernel.md]] | Linux 内核利用基础（堆喷/栈溢出/modprobe_path）|
| [[pwn/skill-kernel-techniques.md]] | 内核利用技术（tty_struct/SLUB/userfaultfd）|
| [[pwn/skill-kernel-bypass.md]] | 内核防护绕过（KASLR/KPTI/SMEP/SMAP）|
| [[pwn/skill-field-notes.md]] | Pwn 详细笔记 |

### Crypto

| File | Content |
|------|---------|
| [[crypto/skill-SKILL.md]] | Crypto 入口速查 — 前置条件/快速分类/Quick Start |
| [[crypto/skill-classic-ciphers.md]] | 经典密码（Vigenere/XOR/Atbash/OTP key reuse）|
| [[crypto/skill-modern-ciphers.md]] | 现代密码 1（AES-CBC/CFB/ECB/padding oracle）|
| [[crypto/skill-modern-ciphers-2.md]] | 现代密码 2（Blum-Goldwasser/哈希长度扩展/CRIME）|
| [[crypto/skill-modern-ciphers-3.md]] | 现代密码 3（CRC32 暴力/SPN/SSH 侧信道）|
| [[crypto/skill-stream-ciphers.md]] | 流密码（LFSR Berlekamp-Massey/RC4 第二字节偏差）|
| [[crypto/skill-rsa-attacks.md]] | RSA 全谱（Wiener/Coppersmith/Hastad/Franklin-Reiter）|
| [[crypto/skill-rsa-attacks-2.md]] | RSA 专项（CRT gcd>1/Bleichenbacher 低指数伪签）|
| [[crypto/skill-ecc-attacks.md]] | ECC 攻击（Smart/Pohlig-Hellman/nonce 重用）|
| [[crypto/skill-zkp-and-advanced.md]] | ZKP/零知识（Shamir SSS/Groth16/KZG）|
| [[crypto/skill-prng.md]] | PRNG 基础（MT19937/LCG/V8 XorShift128+）|
| [[crypto/skill-prng-attacks.md]] | PRNG 攻击（MT 种子恢复/Java LCG meet-in-the-middle）|
| [[crypto/skill-historical.md]] | 历史密码（Lorenz SZ40/42）|
| [[crypto/skill-advanced-math.md]] | 高级数学（BSGS/LWE/LLL/S-box/GF(2) CRT）|
| [[crypto/skill-lattice-and-lwe.md]] | 格攻击（LLL/BKZ/HNP/CVP/子集和）|
| [[crypto/skill-exotic-crypto.md]] | 异种密码 1（辫群/热带半环/Paillier/GM）|
| [[crypto/skill-exotic-crypto-2.md]] | 异种密码 2（BB-84/ElGamal 矩阵/OSS 伪签）|

### Reverse

| File | Content |
|------|---------|
| [[reverse/skill-SKILL.md]] | Reverse 入口速查 — 工具/Quick Wins/分流 |
| [[reverse/skill-tools.md]] | 静态分析工具（GDB/Ghidra/radare2/IDA）|
| [[reverse/skill-tools-dynamic.md]] | 动态分析（Frida/angr/lldb/x64dbg）|
| [[reverse/skill-tools-emulation.md]] | 仿真框架（Qiling/Triton/Intel Pin/侧信道）|
| [[reverse/skill-tools-advanced.md]] | 高级工具（VMProtect/BinDiff/D-810/Miasm/LLVM）|
| [[reverse/skill-tools-advanced-2.md]] | 高级工具 2（GDB 脚本/Ghidra 脚本/pwndbg/GEF）|
| [[reverse/skill-anti-analysis.md]] | 反分析分类（ptrace/PEB/CPUID/DBI/自修改）|
| [[reverse/skill-anti-analysis-ctf.md]] | CTF 反分析 Writeup 技巧 |
| [[reverse/skill-patterns.md]]~[[reverse/skill-patterns-ctf-3.md]] | 二进制模式 1-5（自定义 VM/SMC/XOR/Go/Rust）|
| [[reverse/skill-languages.md]]~[[reverse/skill-languages-compiled.md]] | 语言特定逆向 1-3（Python/Go/Rust/Swift/Kotlin）|
| [[reverse/skill-platforms.md]]~[[reverse/skill-platforms-hardware.md]] | 平台逆向（macOS/IoT/内核驱动/ARM64/RISC-V）|
| [[reverse/skill-field-notes.md]] | Reverse 速查笔记 |

### Forensics

| File | Content |
|------|---------|
| [[forensics/skill-SKILL.md]] | Forensics 入口速查 |
| [[forensics/skill-windows.md]] | Windows 取证（注册表/SAM/事件日志/USN）|
| [[forensics/skill-network.md]] | 网络取证基础（tcpdump/TLS/PCAP/SMB3）|
| [[forensics/skill-network-advanced.md]] | 高级网络取证（NTLMv2/DNS 隐写/RADIUS/ICMP）|
| [[forensics/skill-peripheral-capture.md]] | 外设捕获（USB HID/鼠标/键盘/蓝牙）|
| [[forensics/skill-disk-and-memory.md]] | 磁盘/内存取证（Volatility/VMware/Docker）|
| [[forensics/skill-disk-advanced.md]] | 高级磁盘（ZFS/RAID5/APFS/Partition 恢复）|
| [[forensics/skill-disk-recovery.md]] | 磁盘恢复（LUKS/PRNG/BTRFS/ext2/FAT16）|
| [[forensics/skill-steganography.md]] | 隐写基础（PDF/PNG/SVG/GIF/GZSteg）|
| [[forensics/skill-stego-image.md]] | 图像隐写（JPEG LSB/BMP QR/F5/PNG 调色板）|
| [[forensics/skill-stego-advanced.md]] | 高级隐写 1（FFT/DTMF/SSTV/BarCode 音频）|
| [[forensics/skill-stego-advanced-2.md]] | 高级隐写 2（Arnold 猫映射/视频帧/JPEG XL）|
| [[forensics/skill-linux-forensics.md]] | Linux 取证（日志/Docker/Git/KeePass/浏览器）|
| [[forensics/skill-signals-and-hardware.md]] | 硬件信号（VGA/HDMI/DisplayPort/UART/DPA）|
| [[forensics/skill-3d-printing.md]] | 3D 打印取证（G-code/QOIF/heatshrink）|

### Misc

| File | Content |
|------|---------|
| [[misc/skill-SKILL.md]] | Misc 入口速查 — 前置条件/Quick Start/编码速查 |
| [[misc/skill-pyjails.md]] | Python Jail 沙箱逃逸（func_globals/斥集/编码绕过）|
| [[misc/skill-bashjails.md]] | Bash Jail 逃逸（HISTFILE/ctypes.sh/verbose）|
| [[misc/skill-encodings.md]] | 编码/QR/Esolang/UTF-16/BCD |
| [[misc/skill-encodings-advanced.md]] | 高级编码（Verilog/Gray/RTF/SMS PDU）|
| [[misc/skill-rf-sdr.md]] | RF/SDR/IQ 信号（QAM-16/载波恢复）|
| [[misc/skill-dns.md]] | DNS 攻击（ECS/NSEC/IXFR/Rebinding/Tunneling）|
| [[misc/skill-games-and-vms.md]]~[[misc/skill-games-and-vms-4.md]] | 游戏/VM 专题 1-4（WASM/Roblox/Z3/Emulator/多阶段）|
| [[misc/skill-linux-privesc.md]] | Linux 提权（Sudo/PostgreSQL/NFS/SSH 隧道）|
| [[misc/skill-ctfd-navigation.md]] | CTFd 平台 API 导航 |

### AI-ML

| File | Content |
|------|---------|
| [[ai-ml/skill-SKILL.md]] | AI-ML 入口速查 |
| [[ai-ml/skill-model-attacks.md]] | 模型攻击（权重扰动求逆/LoRA/成员推理）|
| [[ai-ml/skill-adversarial-ml.md]] | 对抗样本（FGSM/PGD/C&W/投毒/后门检测）|
| [[ai-ml/skill-llm-attacks.md]] | LLM 攻击（Prompt 注入/Jailbreak/上下文操纵）|

### Malware（新赛道）

| File | Content |
|------|---------|
| [[malware/skill-SKILL.md]] | Malware 入口速查 |
| [[malware/skill-scripts-and-obfuscation.md]] | 脚本混淆（JS/PowerShell/Shellcode/YARA）|
| [[malware/skill-c2-and-protocols.md]] | C2 协议（RC4 WebSocket/DNS-C2/PCAP 分析）|
| [[malware/skill-pe-and-dotnet.md]] | PE/.NET 分析（peframe/dnSpy/LimeRAT）|
| [[malware/skill-TRAE-debugger.md]] | Trae Debugger 技能 |

### OSINT（新赛道）

| File | Content |
|------|---------|
| [[osint/skill-SKILL.md]] | OSINT 入口速查 |
| [[osint/skill-social-media.md]] | 社交媒体 OSINT（Twitter/Tumblr/BlueSky/Discord）|
| [[osint/skill-web-and-dns.md]] | Web/DNS OSINT（Google Dorking/DNS/Wayback/MHOIS）|

### Code Audit

| File | Content |
|------|---------|
| [[code-audit/skill-SKILL.md]] | Code Audit 入口速查 |
| [[code-audit/skill-agent.md]] | Code Audit Agent 定义 |
| [[code-audit/skill-README.md]] | Code Audit README（英文）|
| [[code-audit/skill-README_CN.md]] | Code Audit README（中文）|

## Command References（解题指南全文）

各赛道完整 `command-` 文件。这些是 `.trae/commands/` 的全文镜像，包含完整的解题流程、攻击面分类、子领域覆盖表。

| File | Content |
|------|---------|
| [[web/command-reference.md]] | Web 安全解题指南（SQLi/SSTI/JWT/反序列化/XSS/Web3 等 20 子领域）|
| [[pwn/command-reference.md]] | PWN 解题指南（栈/堆/ROP/内核/沙箱逃逸等 19 子领域）|
| [[crypto/command-reference.md]] | Crypto 解题指南（RSA/ECC/格/PRNG/流密码等 17 子领域）|
| [[reverse/command-reference.md]] | Reverse 解题指南（静态/动态/反分析/固件等 19 子领域）|
| [[forensics/command-reference.md]] | Forensics 解题指南（磁盘/网络/隐写/信号等 15 子领域）|
| [[misc/command-reference.md]] | Misc 解题指南（编码/jail/游戏/Z3/RF-SDR/DNS）|
| [[ai-ml/command-reference.md]] | AI-ML 解题指南 |
| [[malware/command-reference.md]] | Malware 解题指南 |
| [[osint/command-reference.md]] | OSINT 解题指南 |
| [[rules/command-solve-reference.md]] | Solve 命令参考 |
| [[rules/command-writeup-reference.md]] | Writeup 命令参考 |

## Rules & Catalog（规则与目录）

| File | Content |
|------|---------|
| [[rules/project-rules.md]] | CTF Skills 全局解题规则（强制启动流程/一键分流/自动化策略/MCP 调度/工具优先级）|
| [[rules/startup-prompt.md]] | CTF 启动提示词模板（强制流程模板 + Web/Pwn/Reverse 用例）|
| [[rules/skills-catalog.md]] | 118 个 Skill 完整目录（12 CTF 专项 + 3 辅助 + 103 专项工具）|

## Raw Sources

| Page | Summary |
|------|---------|
| [[raw/ctf-solutions/CrackMe_2_3/writeup]] | DES 加密验证 APK 逆向 Writeup |
| [[raw/ctf-solutions/WEB-TaxSystem_SSTI/writeup]] | SSTI 税务系统 Writeup |
| [[raw/第二届梨花杯wp]] | 第二届梨花杯竞赛 WP（已摄入） |
| [[raw/LitCTF 2026 WEB方向全WP]] | LitCTF 2026 Web 5题 WP（已摄入） |

## CTF Competitions（竞赛实体页）

| Page | Summary |
|------|---------|
| [[ctf/litctf-2026]] | LitCTF 2026 — 宽字节注入/Mako SSTI/Go逆向JWT/kkFileView/Shiro GCM 5题 WP |

## Source Summaries

| Page | Summary |
|------|---------|
| [[ctf/summary--litctf-2026-web-wp]] | LitCTF 2026 WEB 方向全WP 源文档摘要 |

---

*Last updated: 2026-06-07 | Total pages: ~231+（97 命名页 + 99 skill 参考 + 11 command 参考 + 7 rules + 2 raw + 3 code-audit extras + 10 synthesis/index + 2 summary）*
