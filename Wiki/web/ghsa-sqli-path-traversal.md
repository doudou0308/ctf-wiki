---
title: GHSA — SQLi + 路径遍历 + 文件读写（Web 安全）
category: web
tags: [ghsa, sqli, path-traversal, file-read, file-write, lfi, rfi]
triggers: [SQL注入, SQL injection, 路径遍历, path traversal, 任意文件读取, 任意文件写入, LFI, RFI, zip slip, directory traversal, file upload, 文件上传]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/web/GHSA-*]
---

## Summary

SQL 注入（SQLi）、路径遍历/目录穿越（Path Traversal）、文件读写漏洞合集。

**总量**: web/ 下约 60+ 篇 GHSA-* 文件涉及 SQLi/路径遍历/文件操作

## Common Patterns

### SQLi 模式

| 模式 | 描述 | 典型触发词 |
|------|------|-----------|
| **参数化注入** | 未使用 PreparedStatement | `WHERE id = '` + 用户输入 |
| **存储过程注入** | 动态 SQL 拼接 | `EXEC()`, `sp_executesql` |
| **ORM 注入** | ORM 的 raw query | `JPA @Query`, `findBy*` 拼接 |
| **NoSQL 注入** | MongoDB $where | `$gt`, `$ne`, `$regex` |
| **AI/ML 框架** | LLM 检索中的注入 | DuckDB, llama-index 检索器 |

### 文件操作漏洞模式

| 模式 | 描述 | 常见点 |
|------|------|--------|
| **zip slip** | ZIP 解压时路径穿越 | 文件上传解压 |
| **路径遍历读** | `../../../etc/passwd` | 文件下载/预览 |
| **任意文件写** | 通过上传/导出写文件 | 导出、模板、插件 |
| **SSRF → 文件读取** | 通过 SSRF 读内部文件 | cloud metadata |

## Representative GHSA Entries

| GHSA | CVE | Severity | Package | Brief |
|------|-----|----------|---------|-------|
| GHSA-2g22-wg49-fgv5 | — | critical (10.0) | XWiki Full Calendar | SQLi 通过宏参数 |
| GHSA-2jxw-4hm4-6w87 | — | critical (9.8) | llama-index | SQLi |
| GHSA-339r-cjv9-x78g | — | critical (9.8) | LlamaIndex DuckDB | SQLi 检索器 |
| GHSA-2h2p-mvfx-868w | — | critical (9.3) | SiYuan | /export 路径遍历 |
| GHSA-3xq5-x4fj-rff7 | — | critical (9.1) | DB-GPT | 任意文件上传+路径遍历 |
| GHSA-3p8v-w8mr-m3x8 | — | critical (9.1) | Butterfly | URL/路径混淆→文件操作 |
| GHSA-24ch-w38v-xmh8 | CVE-2025-53513 | high (8.8) | Juju | zip slip → SSH 密钥覆盖 |

## Connections

- Related: [[sql-direct-postgresql]]
- Related: [[aictf-sqli-ssrf-tunnel]]
- Related: [[php-file-inclusion-filter]]
- Related: [[php-deserialization-balance-tamper]]
- Related: [[showdoc-file-upload]]
