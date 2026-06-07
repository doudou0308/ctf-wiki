---
title: SQL Direct — PostgreSQL 直连接入
category: web
tags: [sqli, postgresql, psql, database]
triggers: [psql, PostgreSQL, \dt, 直连数据库, postgres, picoCTF]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/web/SQL-Direct.md]
---

## Summary

PostgreSQL 数据库直连场景，用 Docker 中的 `psql` 客户端一行命令查询数据。

## Key Points

- 优先用 Docker 运行 psql 客户端而非 Python psycopg2（网络/权限问题多）
- `\dt` 查看表，`\d` 查看表结构

## Details

### 核心命令

```bash
PGPASSWORD=postgres psql -h saturn.picoctf.net -p 60919 -U postgres -d pico -c "SELECT * FROM flags;"
```

一行 Docker 命令：`docker run --rm alpine sh -c "apk add postgresql-client && psql -h ... -c '\dt'"`

## Connections

- GHSA: [[web/ghsa-sqli-path-traversal]]
- Related: [[aictf-sqli-ssrf-tunnel]]
