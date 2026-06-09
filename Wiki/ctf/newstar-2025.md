---
title: "NewStar CTF 2025 Web 全4周 WP"
category: ctf
tags: [ctf, newstar, web, writeup, 初学者]
triggers: [newstar, NewStar, 新手赛]
created: 2026-06-09
updated: 2026-06-09
sources: [raw/NewStar-web week1-2 - Rycarls little blog.md, raw/NewStar_Week3_Web - Rycarls little blog.md, raw/Newstar web week4 - Rycarls little blog.md]
---

# NewStar CTF 2025 Web 全4周 WP

> 作者：Rycarl | 来源：[Rycarl's Blog](https://blog.rycarl.cn)

NewStar CTF 是一个面向 Web 安全初学者的系列赛，共 4 周。以下为考点汇总。

## 考点全景

| 周次 | 题目 | 核心考点 | 难度 |
|------|------|---------|------|
| W1 | multi-headach3 | robots.txt + HTTP HEAD 方法 + 响应头 flag | ☆ |
| W1 | strange_login | SQL 注入基础 (单引号闭合 + OR bypass) | ☆ |
| W1 | 宇宙的中心是 php | PHP `intval()` 八进制绕过 `intval($answer,0)==47` | ☆ |
| W1 | 我真得控制你了 | 禁用按钮绕过 + POST 手动发包 + 弱口令 + PHP 表达式注入 `eval("\$test = $input;")` | ☆ |
| W1 | 别笑，你也过不了第二关 | JS 前端游戏分数伪造 → POST score 绕过 | ☆ |
| W1 | 黑客小 W 的故事(1) | HTTP 方法篡改 (GET/POST/DELETE) + JWT + User-Agent 伪造 | ☆ |
| W2 | DD 加速器 | 命令注入 → `env` 读环境变量 flag | ☆ |
| W2 | 搞点哦润吉吃吃橘 | 备份文件泄露账号密码 + 表达式计算 | ☆ |
| W3 | who'ssti | Flask SSTI → `lipsum.__globals__['__builtins__']` 调用指定函数 | ★☆ |
| W3 | mygo!!! | SSRF → `file:///flag` 读根目录 flag | ☆ |
| W3 | mirror_gate | .htaccess 解析 .webp + GIF89a 头绕过 + `assert` 替代 `eval` 绕过关键词 | ★☆ |
| W3 | ez-chain | PHP 反序列化 POP 链 (wakeup→invoke→toString→get→call) | ★★ |
| W4 | 武功秘籍 | dcrcms CVE 文件上传 + 弱口令 admin/admin | ☆ |
| W4 | ssti在哪里 | PHP SSRF + Gopher 协议 → Flask SSTI (gopher://localhost:5001/_POST) | ★★ |
| W4 | sqlupload | SQL 注入 `ORDER BY` + `INTO OUTFILE` 写 webshell | ★☆ |
| W4 | 小 E 的留言板 | XSS 双写绕过 + HTML 实体编码绕过 | ☆ |
| W4 | 小羊走迷宫 | PHP 反序列化 POP 链 + `php://filter` 伪协议读源码 | ★★ |

## 关键解法

### W3 who'ssti — 指定函数调用 SSTI

```python
{{lipsum.__globals__['__builtins__']['__import__']('random').choice(['a','b','c'])}}
{{lipsum.__globals__['__builtins__']['__import__']('statistics').fmean([1.0, 2.0])}}
```

题目需要依次调用 funcList 中所有函数。

### W3 ez-chain — PHP 反序列化 POP 链

调用链：`startPoint::__wakeup → SaySomething::__invoke → Treasure::__toString → Treasure::__get → endPoint::__call → flag`

`$_GET['ma_ze.path']` 参数名中 `.` 被 PHP 替换为 `_`，使用 `ma[ze.path` 绕过。

### W4 ssti在哪里 — Gopher + Flask SSTI

```php
# PHP SSRF → Gopher 发送 POST 到 Flask 5001 端口
gopher://localhost:5001/_POST%20/%20HTTP/1.1%0D%0A...
```

## Connections
- Related: [[web/flask-ssti-session-forgery]], [[web/ssrf-gopher-techniques]], [[web/php-deserialization-balance-tamper]]
- Source: [[raw/NewStar-web week1-2 - Rycarls little blog]], [[raw/NewStar_Week3_Web - Rycarls little blog]], [[raw/Newstar web week4 - Rycarls little blog]]
