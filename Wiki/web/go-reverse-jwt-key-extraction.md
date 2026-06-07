---
title: Go 二进制逆向提取 JWT 密钥 + HS256 权限伪造
category: web
tags: [jwt, go, ida, hs256, jwt-editor, reverse-engineering]
triggers: [Go逆向, main.main, JWT密钥, IDA F5, HS256, JWT Editor, Symmetric Key, role伪造, admin越权, BurpSuite, 逆向提取密钥]
created: 2026-06-07
updated: 2026-06-07
sources: [raw/LitCTF 2026 WEB方向全WP.md]
---

## Summary

Go 编译的 Web 服务端硬编码 JWT HS256 密钥，通过 IDA F5 反编译 `main.main` 提取密钥字符串，用 BurpSuite JWT Editor 插件伪造 admin 角色 token。

## Key Points

- Go 逆向特征：`main.main` 是入口函数名，`shift + F4` 搜函数列表
- JWT 密钥直接硬编码在 Go 二进制中（字符串未被混淆）
- HS256 是对称密钥算法 — 知道密钥即可任意伪造
- JWT Editor 插件是 BurpSuite 的 JWT 操作利器：`New Symmetric Key` → `Specify secret` → `Sign`

## Step-by-Step

### 1. Go 二进制逆向提取密钥

```asm
# IDA 中：
shift + F4 → 查看函数列表
ctrl + F → 搜索 "main.main"
F5 → 查看伪代码

# 在 main.main 中搜索字符串 "secret" 或 "key"
# 典型形式：JWT_SECRET = "rMw_2026_litctf_jwt_secret_key!!"
```

### 2. 获取合法 JWT

注册 test/123456 → 登录 → 抓包获取 token：

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
{
  "role": "user",
  "iss": "reverseMyWeb",
  "sub": "test",
  "exp": 1779598948,
  "iat": 1779512548
}
```

### 3. BurpSuite JWT Editor 伪造

1. `JWT Editor` 标签 → `New Symmetric Key` → `Generate` → 替换 k 值为 `rMw_2026_litctf_jwt_secret_key!!` (Base64)
2. 或直接 `Specify secret` 粘贴明文密钥
3. Repeater 中修改 `"role": "user"` → `"role": "admin"`
4. 点击 `Sign` → 选择刚创建的 key → 发送请求

### 核心工具

| 工具 | 用途 |
|------|------|
| IDA Pro | Go 二进制反编译 → 提取硬编码密钥 |
| BurpSuite JWT Editor | 可视化 JWT 签名伪造 |
| jwt.io | JWT 在线调试/解码 |

## JWT 密钥来源模式

| 来源 | 特征 | 提取方法 |
|------|------|---------|
| Go 硬编码 | `main.main` 中字符串常量 | IDA F5 |
| Flask config | `SECRET_KEY = "..."` | SSTI `{‌{config}}` 泄露 |
| heapdump | ShiroKey 在内存对象中 | JDumpSpider 提取 |
| 前端泄露 | JS 中 `secret: "..."` | 查看源码 |

## Connections
- Related: [[jwt-hs256-prng-pipeline-race]] (JWT HS256 PRNG + HTTP Pipeline 竞态)
- Related: [[flask-ssti-session-forgery]] (Flask SSTI 泄露密钥 + session 伪造)
- Source: [[raw/LitCTF 2026 WEB方向全WP]]
