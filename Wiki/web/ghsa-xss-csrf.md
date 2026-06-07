---
title: GHSA — XSS + CSRF + CORS（Web 安全）
category: web
tags: [ghsa, xss, csrf, cors, client-side-security, samesite]
triggers: [XSS, Cross-Site Scripting, CSRF, CORS, same-origin, cookie theft, XSS via, stored XSS, reflected XSS, clickjacking]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/web/GHSA-*]
---

## Summary

跨站脚本（XSS）、跨站请求伪造（CSRF）、跨域资源共享（CORS）等客户端安全漏洞合集。

**总量**: web/ 下约 50+ 篇 GHSA-* 文件涉及 XSS/CSRF/CORS

## Common Patterns

### XSS 入口模式

| 模式 | 描述 | 典型场景 |
|------|------|---------|
| **Stored XSS** | 恶意脚本持久化存储 | 评论/文章/用户资料 |
| **Reflected XSS** | 即时反射回显 | 搜索/错误提示/重定向 |
| **DOM-based XSS** | 前端 JS 操作 DOM | innerHTML/document.write |
| **XSS via 文件上传** | SVG/HTML 文件中的脚本 | 头像/附件上传 |
| **XSS via OAuth** | OAuth 回调中的注入 | 第三方登录流程 |

### CSRF/CORS 模式

- CSRF: 缺少 `SameSite` 或 CSRF token
- CORS: `Access-Control-Allow-Origin` 反射请求头
- Host header 注入: 密码重置投毒

## Representative GHSA Entries

| GHSA | CVE | Severity | Package | Brief |
|------|-----|----------|---------|-------|
| GHSA-2hj5-g64g-fp6p | — | critical (9.1) | Argo CD | 仓库页面存储 XSS |
| GHSA-2j42-h78h-q4fg | — | critical (9.3) | Beego | RenderForm() 反射/存储 XSS |
| GHSA-3p8v-w8mr-m3x8 | — | critical (9.1) | Butterfly | URL/路径混淆 → XSS |
| GHSA-37m4-hqxv-w26g | — | critical (9.1) | XWiki | 调度器 CSRF → RCE |
| GHSA-4655-wh7v-3vmg | — | critical (9.1) | XWiki | Logging UI eval 注入 |
| GHSA-49gm-5685-8fxv | — | critical (9.7) | Liferay | OAuth XSS |
| GHSA-rwhh-6x83-84v6 | — | critical (9.6) | Apache Superset | 仪表盘 XSS |
| GHSA-22m3-c7vp-49fj | CVE-2026-28681 | high (8.1) | IRRD | Host header → 密码重置投毒 |

## Detection Tips

1. **用户输入回显处必有 XSS** — 搜索框 / 用户 profile / 博客评论
2. **文件上传 SVG/HTML** 可绕过 content-type 检测
3. **CSRF 检测** — 看关键操作是否带 CSRF token 或 `SameSite=Strict`
4. **CORS 检测** — 响应头是否反射 Origin 为 `*`
5. **Host header 检测** — 密码重置链中 Host 是否被用于生成链接

## Connections

- Related: [[ops-console-attack-surface]] (Grafana/Kibana 的 XSS 入口)
- Related: [[client-validation-bypass]]
