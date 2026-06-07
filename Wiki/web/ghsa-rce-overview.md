---
title: GHSA — RCE/命令注入综合（Web 安全）
category: web
tags: [ghsa, rce, command-injection, code-execution, vulnerability-collection]
triggers: [RCE, 远程命令执行, 代码执行, Code Injection, 命令注入, deserialization RCE, SpEL, eval注入, template RCE, SSTI RCE]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/web/GHSA-*]
---

## Summary

Web 领域的远程代码执行（RCE）与命令注入漏洞合集。涵盖 Web 框架、API 网关、CMS、CI/CD 工具等常见攻击面。

**总量**: web/ 下约 300+ 篇 GHSA-* 文件涉及 RCE/命令注入

## Common Patterns

### RCE 入口模式

| 模式 | 描述 | 典型触发词 |
|------|------|-----------|
| **反序列化 RCE** | 不安全的 deserialize/unpickle/yaml.load | `unserialize`, `pickle.load`, `yaml.load` |
| **SpEL/OGNL 注入** | 表达式引擎处理用户输入 | `#{...}`, `${...}`, SpEL, OGNL |
| **eval/exec 注入** | 动态代码执行 | `eval()`, `exec()`, runtime.exec |
| **模板注入 RCE** | 模板引擎的远程命令执行 | `{{...}}`, `${...}` 模板语法 |
| **上传 → Webshell** | 通过文件上传写 webshell | upload, import, plugin |
| **命令行参数注入** | 未过滤的 shell 参数 | `curl`, `wget`, `git clone` |

### 高命中率组件

- **XWiki**: 多次 eval injection / rendering RCE（critical）
- **Argo CD**: SSO / 仓库页面 XSS → RCE
- **Apache 项目**: Seata / ShenYu / Shiro
- **OpenMetadata**: SpEL 注入 RCE
- **BentoML / MLflow**: ML 框架的反序列化 RCE

## Representative GHSA Entries

| GHSA | CVE | Severity | Package | Brief |
|------|-----|----------|---------|-------|
| GHSA-2h2p-mvfx-868w | — | critical (9.3) | SiYuan | /export 路径遍历 → RCE |
| GHSA-32mf-57h2-64x9 | — | critical (10.0) | XWiki Rendering | 渲染处理中的 RCE |
| GHSA-33xw-247w-6hmc | — | critical (9.8) | BentoML | 不安全反序列化 RCE |
| GHSA-3xq2-w6j4-c99r | — | critical (8.1) | Apache Seata | 反序列化 RCE |
| GHSA-4957-7vhp-7v59 | — | critical (9.8) | synthcity | 反序列化 RCE |
| GHSA-4hqq-7q79-932p | — | critical (9.8) | mcp-kubernetes-server | OS 命令注入 |
| GHSA-7vf4-x5m2-r6gr | — | critical (9.4) | OpenMetadata | SpEL 注入 RCE |
| GHSA-cpv4-ggrr-7j9v | — | critical (9.1) | Rasa | 远程模型加载 RCE |
| GHSA-f5vg-q7c6-c6mf | — | critical (9.1) | Apache ShenYu | 网关 RCE |
| GHSA-j43h-3w5c-rp8v | — | critical (9.1) | Apache Shiro | 反序列化 RCE |

## Detection & Exploitation Tips

1. **先看受影响组件** — 如果目标跑的是 XWiki / Argo CD / Apache 系列，RCE 概率极高
2. **审计 deserialize 入口** — `ObjectInputStream.readObject()`, `pickle.loads()`, `yaml.load()`
3. **审计 eval 入口** — `eval()`, `exec()`, `Runtime.exec`, `ProcessBuilder`
4. **注意 SpEL 前缀** — `#{`, `${`, `<#` 等模板语法
5. **上传路径 + 执行路径** — 文件上传到可执行目录是最隐蔽的 RCE

## Connections

- Related: [[code-audit/ghsa-rce-patterns]]
- Related: [[php-deserialization-balance-tamper]]
- Related: [[web/flask-ssti-session-forgery]]
- Related: [[web/prototype-pollution-command-injection]]
