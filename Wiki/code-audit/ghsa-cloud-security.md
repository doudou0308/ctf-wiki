---
title: GHSA — 容器/云/CI-CD 安全（Code Audit）
category: code-audit
tags: [ghsa, container, cloud, docker, k8s, ci-cd, kafka, middleware, product-index]
triggers: [容器, 容器逃逸, Docker, K8s, kubernetes, CI/CD, Jenkins, Argo CD, Kafka, 云安全, 中间件, 消息队列, 配置中心, 注册中心, Containerd逃逸, runC逃逸, cgroup逃逸, Docker API未授权, K8s API未授权, K8s etcd, K8s Ingress-nginx RCE, K8s privileged容器, K8s Shadow API Server, K8s CronJob后门, K8s DaemonSet后门, VMware vCenter, MinIO SSRF, Nacos未授权, MySQL UDF提权, Docker socket挂载, procfs挂载, cgroup devices.allow]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/code-audit/GHSA-*]
---

## Summary

容器/云原生/CI-CD 基础设施安全漏洞合集。涵盖 Docker/K8s 暴露面、CI/CD 工具链、消息队列、配置中心等。

**总量**: code-audit/ 下约 100+ 篇 GHSA-* 文件涉及容器/云/CI-CD 安全

## Common Patterns

### 容器/云安全模式

| 模式 | 攻击入口 | 典型后果 |
|------|---------|---------|
| **特权容器挂载** | `--privileged` + 宿主磁盘挂载 | 容器逃逸到宿主 |
| **cgroup v1 release_agent** | 可写 cgroup 可配 release_agent | 容器逃逸 |
| **docker.sock 暴露** | `/var/run/docker.sock` 可写 | 完全控制 Docker 宿主 |
| **K8s ServiceAccount 滥用** | Pod ServiceAccount 绑定过高权限 | 集群提权 |
| **metrics 端口公开** | 9308/10250 等未鉴权 | 信息泄露 |

### CI/CD 工具链漏洞

| 工具 | 常见漏洞 | 严重程度 |
|------|---------|---------|
| **Argo CD** | SSO 弱随机/ XSS → RCE | critical |
| **Jenkins** | CLI 任意文件读 / Script Console | critical |
| **AdGuard Home** | h2c 认证绕过 | critical (9.8) |
| **Milvus** | 未授权 API 访问 | critical (9.8) |

### 中间件漏洞

| 组件 | 漏洞类型 | 端口 | CVSS |
|------|---------|------|------|
| **Kafka SASL** | 认证绕过 (用户名前缀匹配) | 9092 | 9.1 |
| **Juju** | zip slip + 端点上传来经鉴权 | 17070 | 8.8 |
| **Nacos** | 未授权用户创建 | 8848 | high |

## Representative GHSA Entries

| GHSA | CVE | Severity | Package | Brief |
|------|-----|----------|---------|-------|
| GHSA-4g76-w3xw-2x6w | — | critical (9.1) | Kafka SASL | 用户名检查缺陷→完全绕过 |
| GHSA-6f9g-cxwr-q5jr | — | critical (9.8) | Jenkins CLI | 任意文件读→凭证窃取 |
| GHSA-7ppg-37fh-vcr6 | — | critical (9.8) | Milvus | 监控端口未授权访问 |
| GHSA-24ch-w38v-xmh8 | CVE-2025-53513 | high (8.8) | Juju | zip slip → SSH 密钥覆盖 |
| GHSA-2hj5-g64g-fp6p | — | critical (9.1) | Argo CD | 仓库页面存储 XSS |
| GHSA-2h2p-mvfx-868w | — | critical (9.3) | SiYuan | /export 路径遍历 |
| GHSA-4hqq-7q79-932p | — | critical (9.8) | mcp-kubernetes-server | K8s MCP 命令注入 |

## Connections

- Related: [[container-escape]]
- Related: [[docker-container-pentest]]
- Related: [[nacos-unauthorized-access]]
- Related: [[database-post-exploitation]]
- Related: [[ops-console-attack-surface]]
