---
title: GHSA — 代码审计 RCE 模式（Code Audit）
category: code-audit
tags: [ghsa, rce, code-audit, vulnerability-patterns, java, python, go, php, product-index]
triggers: [代码审计 RCE, SpEL, OGNL, 反序列化, eval注入, 模板注入, 命令执行审计, 审计 pattern, code review RCE, Apache OFBiz, Confluence, Drupal, Discuz, Fastjson, Druid, Airflow, Cacti, DedeCMS, Django, F5 BIG-IP, Craft CMS, Flink, Spark, CouchDB, Dubbo, Unomi, 74cms, CmsEasy, ECShop, Fuel CMS, CloudPanel, Dolibarr, VMware vCenter, ElasticSearch, FFmpeg, FastAdmin, GitLab, Jenkins, Apache Tomcat, Nginx, WebLogic, JBoss, WeChat]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/code-audit/GHSA-*]
---

## Summary

代码审计视角下的 RCE 模式合集。涵盖 Java/Python/Go 生态的典型漏洞模式。

**总量**: code-audit/ 下约 300+ 篇 GHSA-* 文件涉及代码审计 RCE

## Common Patterns

### Java RCE 模式

| 模式 | 危险函数/类 | 检测方法 |
|------|------------|---------|
| **SpEL 注入** | `SpelExpressionParser.parseExpression()`, `#{...}` | grep `parseExpression\|SpelExpressionParser` |
| **OGNL 注入** | `ognl.Ognl.getValue()` | grep `Ognl.getValue\|Ognl.parseExpression` |
| **反序列化 RCE** | `ObjectInputStream.readObject()`, `readUnshared()` | grep `readObject\|readUnshared` |
| **JNDI 注入** | `InitialContext.lookup()`, `Context.lookup()` | 参数可控的 lookup |
| **模板注入** | `Velocity`, `FreeMarker`, `Thymeleaf` 模板拼接 | 模板内容来自用户输入 |
| **XXE → RCE** | `DocumentBuilder.parse()`, `SAXParser` | DT entity 展开 |

### Python RCE 模式

| 模式 | 危险函数 | 检测方法 |
|------|---------|---------|
| **pickle 反序列化** | `pickle.loads()`, `pickle.load()` | `__reduce__` gadget |
| **yaml.load** | `yaml.load()` (非 safe_load) | `yaml.load` 不加 Loader |
| **eval/exec** | `eval()`, `exec()`, `compile()` | 字符串拼接后执行 |
| **jinja2 SSTI** | `render_template_string()` | `{{...}}` 模板语法 |

### Go RCE 模式

| 模式 | 危险函数 | 检测方法 |
|------|---------|---------|
| **exec.Command** | `exec.Command()` 参数含 shell 字符 | 参数拼接 |
| **template 注入** | `text/template`, `html/template` | `{{.}}` 模板路径 |
| **SSRF + 文件写** | `http.Get(url)` + `os.WriteFile()` | URL 用户可控 |

## Representative GHSA Entries

| GHSA | CVE | Severity | Package | Brief |
|------|-----|----------|---------|-------|
| GHSA-33xw-247w-6hmc | — | critical (9.8) | BentoML | pickle 反序列化 RCE |
| GHSA-3xq2-w6j4-c99r | — | critical (8.1) | Apache Seata | 反序列化 RCE |
| GHSA-4957-7vhp-7v59 | — | critical (9.8) | synthcity | 反序列化 RCE |
| GHSA-7vf4-x5m2-r6gr | — | critical (9.4) | OpenMetadata | SpEL 注入 RCE |
| GHSA-cpv4-ggrr-7j9v | — | critical (9.1) | Rasa | 远程模型加载 RCE |
| GHSA-j43h-3w5c-rp8v | — | critical (9.1) | Apache Shiro | 反序列化 RCE |
| GHSA-4hqq-7q79-932p | — | critical (9.8) | mcp-kubernetes-server | OS 命令注入 |

## Audit Workflow

```
1. 确定编程语言 → 查对应危险函数表
2. 追踪用户输入 → 是否进入危险函数
3. 检查过滤/编码 → 有无白名单/escaping
4. 测试 payload → 确认可达性
```

## Connections

- Related: [[web/ghsa-rce-overview]]
- Related: [[fastadmin-rce]]
- Related: [[xxl-job-rce]]
- Related: [[aria2-arbitrary-file-write]]
