---
title: LitCTF 2026 Web WP 索引
category: ctf
tags: [ctf, litctf, writeup, web]
triggers: [LitCTF, LitCTF2026, 2026 litctf, lit ctf, 青岑, lit_ezsql, lit_ezssti, lit_reverse_my_web, Northbridge Document Hub, 华辰企业服务运营平台]
created: 2026-06-07
updated: 2026-06-07
sources: [raw/LitCTF 2026 WEB方向全WP.md]
---

# LitCTF 2026 Web 方向 Writeup

> 作者：[青岑] | 来源：[mp.weixin.qq.com](https://mp.weixin.qq.com/s/4d5IvbO_Emj474gOW4njjA)
> 日期：2026-05-28 | 题数：5 题 (全 Web)

## 题目一览

| 题目 | 核心技术 | Wiki 页面 |
|------|---------|----------|
| lit_ezsql | 宽字节注入 + Union + 十六进制绕过单引号 | [[web/wide-byte-gbk-injection]] |
| lit_ezssti | Mako 模板注入 + getattr() 绕过 WAF | [[web/mako-ssti-code-block-bypass]] |
| lit_reverse_my_web | Go 逆向 JWT 密钥 + HS256 伪造 | [[web/go-reverse-jwt-key-extraction]] |
| Northbridge Document Hub | 前端泄露 + kkFileView 任意文件读 | [[web/kkfileview-arbitrary-file-read]] |
| 华辰企业服务运营平台 | Actuator heapdump + Shiro GCM 越权 | [[web/ops-console-attack-surface]] |

## 考点分布

| 考点类别 | 题目 | 技巧 |
|----------|------|------|
| SQL 注入 | lit_ezsql | GBK 宽字节 `%df'`、Union 联合查询、hex 编码绕过 |
| SSTI | lit_ezssti | Mako `<% %>` 代码块、`getattr()` 反射链、通配符绕过 |
| 身份认证 | lit_reverse_my_web | Go 二进制逆向提取 JWT HS256 密钥、JWT Editor 签名伪造 |
| 文件读取 | Northbridge Document Hub | 前端 JS 泄露凭证、kkFileView Base64 路径遍历 |
| 运维攻击面 | 华辰企业服务运营平台 | Actuator heapdump → ShiroKey → Shiro GCM 越权 |

## 解法亮点

1. **宽字节注入**：无报错回显时优先测宽字节（无 `Unknown column` 异常却正常回显）
2. **Mako SSTI**：`${7*7}` 返回 WAF → 切 `<% %>` 代码块 → `getattr()` 代替 `.`
3. **Go 逆向 JWT**：不会 Go 逆向也能用 IDA F5 直接看 `main.main` 伪代码
4. **kkFileView**：`atob()` 在 JS 源码中的出现是路径需 Base64 编码的暗示
5. **Shiro 多阶段 flag**：`actuator/heapdump` 和 `/api/admin/audit/list` 各藏一半

## Connections
- Source: [[raw/LitCTF 2026 WEB方向全WP]]
