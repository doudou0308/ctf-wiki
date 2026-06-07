# 已安装 Skill 完整目录

> 生成时间: 2026-06-02 | 总数: 12 个 CTF 专项 + 3 个辅助 + 103 个专项工具 = 118 个 Skill

---

## 一、CTF 专项 Skill（12 个）—— 必选

| Skill 名 | 用途 |
|----------|------|
| `ctf-reverse` | 逆向工程（Native/VM/固件/加壳/反调试/Python字节码/WASM/Unity） |
| `ctf-web` | Web 安全（SQLi/XSS/SSTI/SSRF/反序列化/JWT/OAuth/Web3/CSP绕过） |
| `ctf-pwn` | 二进制漏洞利用（栈/堆/ROP/格式化字符串/内核/沙箱逃逸） |
| `ctf-crypto` | 密码学（RSA/AES/ECC/格/LWE/PRNG/流密码/经典密码/ZKP） |
| `ctf-forensics` | 取证（磁盘/内存/网络流量/隐写/信号/3D打印/Windows/Linux） |
| `ctf-misc` | 杂项（编码/沙箱逃逸/pyjail/bashjail/游戏/Z3/RF/SDR/DNS） |
| `ctf-malware` | 恶意软件分析（C2协议/PE/.NET/脚本混淆/Shellcode） |
| `ctf-osint` | 开源情报（社交媒体/地理定位/DNS/域名/图片溯源） |
| `ctf-ai-ml` | AI安全（对抗样本/LLM注入/模型攻击/提示注入） |
| `ctf-writeup` | Writeup 生成 |
| `solve-challenge` | CTF 题目分派入口（自动识别类别→路由到对应 ctf-* skill） |
| `skill-creator` | 创建新的 Skill |

---

## 二、辅助 Skill（3 个）

| Skill 名 | 用途 |
|----------|------|
| `TRAE-code-review` | 代码审查（MR/差异/代码质量/最佳实践反馈） |
| `code-audit` | 专业代码安全审计（55+ 漏洞类型，9 语言，乌·云 88636 案例库） |

---

## 三、专项工具 Skill — 按领域分类（103 个）

### 3.1 逆向工程 (Reverse Engineering)

| Skill 名 | 用途 |
|----------|------|
| `analyzing-android-malware-with-apktool` | APK 静态分析（apktool/jadx/androguard） |
| `analyzing-golang-malware-with-ghidra` | Go 二进制逆向（Ghidra + Go 类型恢复） |
| `analyzing-packed-malware-with-upx-unpacker` | UPX 脱壳 |
| `intercepting-mobile-traffic-with-burpsuite` | 移动端 BurpSuite 抓包 |
| `performing-binary-exploitation-analysis` | pwntools 二进制漏洞利用分析 |
| `performing-dynamic-analysis-of-android-app` | Android Frida/Objection 动态分析 |
| `performing-firmware-extraction-with-binwalk` | 固件提取（binwalk） |
| `performing-fuzzing-with-aflplusplus` | AFL++ 覆盖率引导 Fuzz |
| `reverse-engineering-android-malware-with-jadx` | JADX 安卓逆向 |
| `reverse-engineering-dotnet-malware-with-dnspy` | .NET dnSpy 逆向 |
| `reverse-engineering-ios-app-with-frida` | iOS Frida 逆向 |
| `reverse-engineering-malware-with-ghidra` | Ghidra 恶意软件逆向 |
| `reverse-engineering-ransomware-encryption-routine` | 勒索软件加密逆向 |
| `reverse-engineering-rust-malware` | Rust 编译恶意软件逆向 |
| `deobfuscating-javascript-malware` | JS 恶意代码反混淆 |
| `deobfuscating-powershell-obfuscated-malware` | PowerShell 反混淆 |
| `extracting-config-from-agent-tesla-rat` | Agent Tesla RAT 配置提取 |
| `performing-automated-malware-analysis-with-cape` | CAPEv2 沙箱自动化分析 |

### 3.2 漏洞利用 (Exploitation)

| Skill 名 | 用途 |
|----------|------|
| `exploiting-broken-link-hijacking` | 死链劫持 |
| `exploiting-deeplink-vulnerabilities` | Android/iOS DeepLink 漏洞利用 |
| `exploiting-http-request-smuggling` | HTTP 请求走私 |
| `exploiting-idor-vulnerabilities` | IDOR 越权漏洞 |
| `exploiting-insecure-deserialization` | 反序列化漏洞（Java/PHP/Python/.NET） |
| `exploiting-ipv6-vulnerabilities` | IPv6 攻击 |
| `exploiting-mass-assignment-in-rest-apis` | REST API Mass Assignment |
| `exploiting-nosql-injection-vulnerabilities` | NoSQL 注入（MongoDB/CouchDB） |
| `exploiting-oauth-misconfiguration` | OAuth 2.0/OIDC 配置错误利用 |
| `exploiting-prototype-pollution-in-javascript` | JS 原型污染 |
| `exploiting-race-condition-vulnerabilities` | 竞态条件（Turbo Intruder） |
| `exploiting-sql-injection-vulnerabilities` | SQL 注入（手注+sqlmap） |
| `exploiting-sql-injection-with-sqlmap` | sqlmap 专项 |
| `exploiting-template-injection-vulnerabilities` | SSTI 模板注入 |
| `exploiting-type-juggling-vulnerabilities` | PHP 类型戏法 |
| `exploiting-websocket-vulnerabilities` | WebSocket 漏洞 |
| `performing-blind-ssrf-exploitation` | 盲 SSRF（DNS/OOB） |
| `performing-container-escape-detection` | 容器逃逸检测 |
| `performing-content-security-policy-bypass` | CSP 绕过 |
| `performing-directory-traversal-testing` | 目录穿越 |
| `performing-http-parameter-pollution-attack` | HTTP 参数污染 |
| `performing-jwt-none-algorithm-attack` | JWT None 算法攻击 |
| `performing-second-order-sql-injection` | 二阶 SQL 注入 |
| `performing-web-application-firewall-bypass` | WAF 绕过 |
| `performing-web-cache-deception-attack` | Web 缓存欺骗 |
| `performing-web-cache-poisoning-attack` | Web 缓存投毒 |

### 3.3 渗透测试 & 安全审计

| Skill 名 | 用途 |
|----------|------|
| `performing-api-fuzzing-with-restler` | REST API Fuzzing（Microsoft RESTler） |
| `performing-api-rate-limiting-bypass` | API 速率限制绕过 |
| `performing-clickjacking-attack-test` | 点击劫持测试 |
| `performing-csrf-attack-simulation` | CSRF 攻击模拟 |
| `performing-graphql-depth-limit-attack` | GraphQL 深度限制攻击 |
| `performing-graphql-introspection-attack` | GraphQL 内省攻击 |
| `performing-graphql-security-assessment` | GraphQL 安全评估 |
| `performing-hash-cracking-with-hashcat` | Hashcat 密码破解 |
| `performing-ios-app-security-assessment` | iOS App 安全评估 |
| `performing-iot-security-assessment` | IoT 安全评估 |
| `performing-privilege-escalation-assessment` | 提权评估（Linux/Windows） |
| `performing-privilege-escalation-on-linux` | Linux 提权 |
| `performing-security-headers-audit` | HTTP 安全头审计 |
| `performing-service-account-audit` | 服务账户审计 |
| `performing-ssl-tls-security-assessment` | SSL/TLS 配置评估 |
| `performing-supply-chain-attack-simulation` | 供应链攻击模拟 |
| `testing-cors-misconfiguration` | CORS 配置错误测试 |
| `testing-for-broken-access-control` | 访问控制漏洞测试 |
| `testing-for-email-header-injection` | 邮件头注入测试 |
| `testing-for-host-header-injection` | Host 头注入测试 |
| `testing-for-sensitive-data-exposure` | 敏感数据泄露测试 |
| `testing-for-xss-vulnerabilities` | XSS 跨站脚本测试 |
| `testing-jwt-token-security` | JWT Token 安全测试 |
| `testing-mobile-api-authentication` | 移动端 API 认证测试 |
| `testing-oauth2-implementation-flaws` | OAuth2 实现缺陷测试 |
| `testing-websocket-api-security` | WebSocket API 安全测试 |
| `testing-android-intents-for-vulnerabilities` | Android Intent 漏洞测试 |
| `performing-bluetooth-security-assessment` | 蓝牙安全评估 |
| `scanning-network-with-nmap-advanced` | Nmap 高级扫描 |

### 3.4 内网渗透 & AD

| Skill 名 | 用途 |
|----------|------|
| `conducting-pass-the-ticket-attack` | Kerberos Pass-the-Ticket 攻击 |
| `performing-kerberoasting-attack` | Kerberoasting 攻击 |
| `analyzing-active-directory-acl-abuse` | AD ACL 滥用检测 |
| `performing-red-team-with-covenant` | Covenant C2 红队 |

### 3.5 取证 (Forensics)

| Skill 名 | 用途 |
|----------|------|
| `acquiring-disk-image-with-dd-and-dcfldd` | dd/dcfldd 磁盘镜像 |
| `analyzing-browser-forensics-with-hindsight` | Chromium 浏览器取证 |
| `analyzing-disk-image-with-autopsy` | Autopsy 磁盘镜像分析 |
| `analyzing-docker-container-forensics` | Docker 容器取证 |
| `analyzing-linux-audit-logs-for-intrusion` | auditd 日志入侵检测 |
| `analyzing-linux-system-artifacts` | Linux 系统痕迹取证 |
| `analyzing-lnk-file-and-jump-list-artifacts` | Windows LNK/JumpList 取证 |
| `analyzing-mft-for-deleted-file-recovery` | NTFS MFT 删除文件恢复 |
| `analyzing-memory-dumps-with-volatility` | Volatility 内存取证 |
| `analyzing-memory-forensics-with-lime-and-volatility` | LiME+Volatility Linux 内存取证 |
| `analyzing-outlook-pst-for-email-forensics` | Outlook PST/OST 邮件取证 |
| `analyzing-prefetch-files-for-execution-history` | Windows Prefetch 取证 |
| `analyzing-slack-space-and-file-system-artifacts` | 文件系统 Slack Space 取证 |
| `analyzing-usb-device-connection-history` | USB 连接历史取证 |
| `analyzing-windows-amcache-artifacts` | Windows Amcache 取证 |
| `analyzing-windows-lnk-files-for-artifacts` | Windows LNK 文件取证 |
| `analyzing-windows-prefetch-with-python` | Windows Prefetch Python 解析 |
| `analyzing-windows-registry-for-artifacts` | Windows 注册表取证 |
| `analyzing-windows-shellbag-artifacts` | Windows ShellBag 取证 |
| `extracting-browser-history-artifacts` | 浏览器历史提取 |
| `extracting-credentials-from-memory-dump` | 内存凭据提取 |
| `extracting-memory-artifacts-with-rekall` | Rekall 内存取证 |
| `extracting-windows-event-logs-artifacts` | Windows EVTX 日志提取 |
| `performing-disk-forensics-investigation` | 磁盘取证 |
| `performing-endpoint-forensics-investigation` | 端点取证 |
| `performing-file-carving-with-foremost` | Foremost 文件雕刻 |
| `performing-linux-log-forensics-investigation` | Linux 日志取证 |
| `performing-log-analysis-for-forensic-investigation` | 日志分析取证 |
| `performing-memory-forensics-with-volatility3` | Volatility3 内存取证 |
| `performing-memory-forensics-with-volatility3-plugins` | Volatility3 插件 |
| `performing-mobile-device-forensics-with-cellebrite` | Cellebrite 手机取证 |
| `performing-network-forensics-with-wireshark` | Wireshark 网络取证 |
| `performing-network-packet-capture-analysis` | PCAP 包分析 |
| `performing-network-traffic-analysis-with-zeek` | Zeek 流量分析 |
| `performing-sqlite-database-forensics` | SQLite 数据库取证 |
| `performing-steganography-detection` | 隐写检测 |
| `performing-timeline-reconstruction-with-plaso` | Plaso 时间线重建 |
| `performing-windows-artifact-analysis-with-eric-zimmerman-tools` | EZ Tools Windows 取证 |

### 3.6 恶意软件分析 (Malware)

| Skill 名 | 用途 |
|----------|------|
| `analyzing-bootkit-and-rootkit-samples` | Bootkit/Rootkit 分析（MBR/VBR/UEFI） |
| `analyzing-cobalt-strike-beacon-configuration` | CobaltStrike Beacon 配置提取 |
| `analyzing-macro-malware-in-office-documents` | Office 宏恶意软件分析 |
| `analyzing-malware-behavior-with-cuckoo-sandbox` | Cuckoo 沙箱行为分析 |
| `analyzing-malware-persistence-with-autoruns` | Autoruns 持久化检测 |
| `analyzing-malware-sandbox-evasion-techniques` | 沙箱逃逸技术检测 |
| `analyzing-network-covert-channels-in-malware` | 隐蔽信道检测 |
| `analyzing-network-traffic-of-malware` | 恶意软件流量分析 |
| `analyzing-pdf-malware-with-pdfid` | PDF 恶意软件分析 |
| `analyzing-powershell-empire-artifacts` | Empire 痕迹检测 |
| `analyzing-powershell-script-block-logging` | PS ScriptBlock 日志分析 |
| `analyzing-ransomware-encryption-mechanisms` | 勒索软件加密分析 |
| `analyzing-ransomware-network-indicators` | 勒索软件网络指标 |
| `analyzing-ransomware-payment-wallets` | 勒索软件钱包追踪 |
| `analyzing-supply-chain-malware-artifacts` | 供应链恶意软件分析 |
| `analyzing-uefi-bootkit-persistence` | UEFI Bootkit 持久化 |
| `extracting-iocs-from-malware-samples` | 恶意软件 IOC 提取 |
| `performing-dynamic-analysis-with-any-run` | ANY.RUN 动态分析 |
| `performing-firmware-malware-analysis` | 固件恶意软件分析 |
| `performing-malware-ioc-extraction` | IOC 提取 |
| `performing-malware-triage-with-yara` | YARA 规则分类 |
| `performing-static-malware-analysis-with-pe-studio` | PEStudio 静态分析 |

### 3.7 威胁情报 (Threat Intel)

| Skill 名 | 用途 |
|----------|------|
| `analyzing-apt-group-with-mitre-navigator` | APT 组织 MITRE Navigator 分析 |
| `analyzing-campaign-attribution-evidence` | 攻击归因分析 |
| `analyzing-certificate-transparency-for-phishing` | 证书透明度钓鱼检测 |
| `analyzing-cyber-kill-chain` | 杀伤链分析 |
| `analyzing-indicators-of-compromise` | IOC 分析（VirusTotal/AbuseIPDB） |
| `analyzing-malware-family-relationships-with-malpedia` | Malpedia 恶意软件家族 |
| `analyzing-ransomware-leak-site-intelligence` | 勒索泄露站情报 |
| `analyzing-sbom-for-supply-chain-vulnerabilities` | SBOM 供应链漏洞（CycloneDX/SPDX） |
| `analyzing-threat-actor-ttps-with-mitre-attack` | ATT&CK TTPs 映射 |
| `analyzing-threat-actor-ttps-with-mitre-navigator` | ATT&CK Navigator 威胁画像 |
| `analyzing-threat-landscape-with-misp` | MISP 威胁情报平台 |
| `analyzing-tls-certificate-transparency-logs` | TLS CT 日志分析 |
| `analyzing-typosquatting-domains-with-dnstwist` | dnstwist 域名仿冒检测 |
| `collecting-open-source-intelligence` | OSINT 情报收集（Maltego/Shodan/SpiderFoot） |
| `hunting-for-cobalt-strike-beacons` | CS Beacon 检测（JA3/JARM/TLS） |
| `hunting-for-command-and-control-beaconing` | C2 Beacon 检测 |
| `hunting-for-domain-fronting-c2-traffic` | 域前置 C2 检测 |
| `performing-ip-reputation-analysis-with-shodan` | Shodan IP 信誉分析 |
| `performing-osint-with-spiderfoot` | SpiderFoot OSINT |
| `detecting-aws-cloudtrail-anomalies` | AWS CloudTrail 异常检测 |
| `detecting-supply-chain-attacks-in-ci-cd` | CI/CD 供应链攻击检测 |
| `performing-user-behavior-analytics` | UEBA 用户行为分析 |
| `performing-dns-enumeration-and-zone-transfer` | DNS 枚举/域传送 |
| `performing-subdomain-enumeration-with-subfinder` | Subfinder 子域名枚举 |

### 3.8 网络攻防 (Network)

| Skill 名 | 用途 |
|----------|------|
| `analyzing-dns-logs-for-exfiltration` | DNS 日志数据外泄检测 |
| `analyzing-heap-spray-exploitation` | Volatility3 堆喷射检测 |
| `analyzing-malicious-url-with-urlscan` | URLScan.io 恶意 URL 分析 |
| `analyzing-network-covert-channels-in-malware` | 隐蔽信道分析 |
| `analyzing-network-flow-data-with-netflow` | NetFlow/IPFIX 流量分析 |
| `analyzing-network-packets-with-scapy` | Scapy 网络包分析 |
| `analyzing-network-traffic-for-incidents` | 事件响应流量分析 |
| `analyzing-web-server-logs-for-intrusion` | Web 日志入侵检测 |
| `detecting-api-enumeration-attacks` | API 枚举攻击检测 |
| `detecting-shadow-api-endpoints` | 影子 API 端点发现 |
| `performing-arp-spoofing-attack-simulation` | ARP 欺骗模拟 |
| `performing-bandwidth-throttling-attack-simulation` | 带宽限制攻击模拟 |
| `performing-dns-tunneling-detection` | DNS 隧道检测 |
| `performing-network-traffic-analysis-with-tshark` | tshark 自动化分析 |
| `performing-packet-injection-attack` | 包注入攻击 |
| `performing-ssl-stripping-attack` | SSL 剥离攻击 |
| `performing-vlan-hopping-attack` | VLAN 跳跃攻击 |
| `performing-wireless-security-assessment-with-kismet` | Kismet 无线安全评估 |
| `performing-wifi-password-cracking-with-aircrack` | aircrack-ng WiFi 破解 |

### 3.9 密码学 & 身份认证 (Crypto & Auth)

| Skill 名 | 用途 |
|----------|------|
| `configuring-certificate-authority-with-openssl` | OpenSSL CA 搭建 |
| `configuring-hsm-for-key-storage` | HSM 密钥存储 |
| `configuring-tls-1-3-for-secure-communications` | TLS 1.3 配置 |
| `implementing-aes-encryption-for-data-at-rest` | AES-256-GCM 实现 |
| `implementing-digital-signatures-with-ed25519` | Ed25519 签名 |
| `implementing-end-to-end-encryption` | 端到端加密 |
| `implementing-jwt-signing-and-verification` | JWT 签名验证 |
| `implementing-rsa-key-pair-management` | RSA 密钥管理 |
| `implementing-zero-knowledge-proof-for-authentication` | Schnorr ZKP 认证 |
| `performing-cryptographic-audit-of-application` | 应用密码审计 |
| `performing-post-quantum-cryptography-migration` | 后量子密码迁移（FIPS 203/204/205） |

### 3.10 移动安全 (Mobile)

| Skill 名 | 用途 |
|----------|------|
| `analyzing-ios-app-security-with-objection` | Objection iOS 运行时分析 |
| `detecting-mobile-malware-behavior` | 移动恶意软件检测 |
| `performing-android-app-static-analysis-with-mobsf` | MobSF Android 静态分析 |
| `performing-mobile-app-certificate-pinning-bypass` | 移动端证书绑定绕过 |
| `testing-android-intents-for-vulnerabilities` | Android Intent 漏洞测试 |

### 3.11 其他 (Others)

| Skill 名 | 用途 |
|----------|------|
| `performing-firmware-extraction-with-binwalk` | Binwalk 固件提取 |
| `performing-steganography-detection` | 隐写检测 |

---

## 四、MCP 工具（自动化集成，无需确认直接调用）

| MCP 服务 | 核心工具 | 适用场景 | 触发条件 |
|----------|---------|---------|---------|
| **IDA Pro** | 反编译、交叉引用、字符串搜索、函数列表、实体查询、重命名类/函数/变量 | RE/Pwn 题打开二进制即可 | 有 `.exe`/`.elf`/`.so`/`.dll` 等二进制文件 |
| **JADX** | 反编译 APK、搜索类/方法/字段、交叉引用、读 AndroidManifest、读 Smali、重命名、调试（断点/栈帧/线程/变量） | Android/Mobile 题 | 有 `.apk`/`.aab`/`.dex` 文件 |
| **Playwright** | 打开网页、截图、点击、填表单、执行 JS、Console 日志、文件上传 | Web 题需要浏览器交互 | 有 URL/HTML 页面需要操作 |
| **WireMCP** | 抓包、分析 PCAP、检测威胁、提取凭据 | Forensics/Network 题 | 有 `.pcap`/`.pcapng`/网络流量文件 |

### IDA Pro 主要工具详情

| 工具 | 说明 |
|------|------|
| `list_instances` / `select_instance` | 查看/切换 IDA 实例 |
| `get_metadata` | 获取当前 IDB 元数据 |
| `get_function_by_name` / `get_function_by_address` | 按名称/地址获取函数 |
| `decompile` / `disassemble` | 反编译/反汇编 |
| `get_xrefs_to` / `get_xrefs_from` | 交叉引用分析 |
| `find_regex` | 正则搜索字符串 |
| `get_global_variable_value_by_name` | 读取全局变量 |
| `rename_function` / `rename_local_variable` / `rename_global_variable` | 重命名 |
| `patch` | 二进制补丁 |
| `analyze_batch` | 批量分析（反编译+反汇编+调用者+被调用者+字符串+常量） |
| `lookup_funcs` | 批量查询函数 |
| `get_callgraph` | 调用图分析 |

### JADX 主要工具详情

| 工具 | 说明 |
|------|------|
| `get_android_manifest` | 读取 AndroidManifest.xml |
| `get_main_activity_class` | 获取主 Activity |
| `get_all_classes` / `get_class_source` | 遍历/读取类源码 |
| `get_method_by_name` / `get_methods_of_class` | 方法查询 |
| `get_fields_of_class` | 字段查询 |
| `get_smali_of_class` | Smali 代码 |
| `get_xrefs_to_class` / `get_xrefs_to_method` / `get_xrefs_to_field` | 交叉引用 |
| `search_method_by_name` / `search_classes_by_keyword` | 全局搜索 |
| `get_strings` / `get_all_resource_file_names` / `get_resource_file` | 资源文件 |
| `get_package_tree` | 包结构树 |
| `rename_class` / `rename_method` / `rename_field` / `rename_package` / `rename_variable` | 重命名 |
| `debug_get_stack_frames` / `debug_get_threads` / `debug_get_variables` | 调试 |
| `get_manifest_component` | 按组件类型筛选（activity/provider/service/receiver） |
| `get_main_application_classes_names` / `get_main_application_classes_code` | 主应用类 |

### Playwright 主要工具详情

| 工具 | 说明 |
|------|------|
| `browser_close` | 关闭页面 |
| `browser_resize` | 调整窗口大小 |
| `browser_console_messages` | 获取控制台日志 |
| `browser_handle_dialog` | 处理弹窗（alert/confirm/prompt） |
| `browser_evaluate` | 执行 JS 代码 |
| `browser_file_upload` | 文件上传 |

---

## 五、快速索引 — 按场景速查

| 场景 | 推荐 Skill（逗号分隔） |
|------|---------------------|
| 常规逆向 | `ctf-reverse` |
| Web 渗透 | `ctf-web`, `testing-for-xss-vulnerabilities`, `exploiting-sql-injection-with-sqlmap` |
| Pwn | `ctf-pwn`, `performing-binary-exploitation-analysis` |
| 密码学 | `ctf-crypto` |
| 取证 | `ctf-forensics`, `performing-memory-forensics-with-volatility3` |
| 恶意软件 | `ctf-malware`, `reverse-engineering-malware-with-ghidra`, `extracting-iocs-from-malware-samples` |
| 代码审计 | `code-audit`（支持 9 语言 55+ 漏洞类型） |
| APK 逆向 | `ctf-reverse`, `analyzing-android-malware-with-apktool`, `reverse-engineering-android-malware-with-jadx` |
| IoT/固件 | `ctf-reverse`, `performing-firmware-extraction-with-binwalk`, `performing-iot-security-assessment` |
| 内网/AD | `ctf-pwn`, `conducting-pass-the-ticket-attack`, `performing-kerberoasting-attack` |
| GraphQL | `ctf-web`, `performing-graphql-security-assessment` |
| 移动安全 | `ctf-reverse`, `performing-ios-app-security-assessment`, `performing-android-app-static-analysis-with-mobsf` |
| .NET 逆向 | `ctf-reverse`, `reverse-engineering-dotnet-malware-with-dnspy` |
| Go/Rust 逆向 | `ctf-reverse`, `analyzing-golang-malware-with-ghidra`, `reverse-engineering-rust-malware` |
| Python 字节码 | `ctf-reverse` |
| WAF 绕过 | `ctf-web`, `performing-web-application-firewall-bypass` |
| 威胁情报 | `collecting-open-source-intelligence`, `analyzing-indicators-of-compromise` |
| C2 检测 | `hunting-for-cobalt-strike-beacons`, `hunting-for-command-and-control-beaconing` |
