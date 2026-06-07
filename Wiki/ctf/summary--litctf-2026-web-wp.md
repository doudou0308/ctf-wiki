---
title: LitCTF 2026 WEB方向全WP（源文档摘要）
category: source-summary
tags: [source-summary, litctf, web, writeup]
created: 2026-06-07
updated: 2026-06-07
sources: [raw/LitCTF 2026 WEB方向全WP.md]
---

# LitCTF 2026 WEB方向全WP

> 源文档：[raw/LitCTF 2026 WEB方向全WP](file:///raw/LitCTF%202026%20WEB%E6%96%B9%E5%90%91%E5%85%A8WP.md)
> 作者：青岑 (mp.weixin.qq.com)

## 摘要

LitCTF 2026 的 5 道 Web 题目完整 WP，涵盖：

| # | 题目 | 技术点 | Wiki 页面 |
|---|------|--------|----------|
| 1 | lit_ezsql | GBK 宽字节注入 + Union + hex 绕过 | [[web/wide-byte-gbk-injection]] |
| 2 | lit_ezssti | Mako `<% %>` + getattr() WAF 绕过 | [[web/mako-ssti-code-block-bypass]] |
| 3 | lit_reverse_my_web | Go 逆向 JWT 密钥 + HS256 伪造 | [[web/go-reverse-jwt-key-extraction]] |
| 4 | Northbridge Document Hub | kkFileView 前端泄露 + Base64 LFI | [[web/kkfileview-arbitrary-file-read]] |
| 5 | 华辰企业服务运营平台 | Actuator heapdump + Shiro GCM | [[web/ops-console-attack-surface]] |

## 与已有知识的关系

- **宽字节注入**：新专题，填补 SQLi 知识库空白
- **Mako SSTI**：与 Jinja2 SSTI 互补，扩展模板引擎覆盖
- **Go 逆向 JWT**：新方向 — JWT 密钥不仅来自 Flask/SSTI 泄露，也来自二进制逆向
- **kkFileView**：企业文档预览组件的新攻击面，补充文档报告攻击面专题
- **Shiro heapdump**：增强运维攻击面页 — JDumpSpider 提取 ShiroKey 的完整流程

## Connections
- Related: [[ctf/litctf-2026]]
