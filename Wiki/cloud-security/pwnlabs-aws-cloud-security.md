---
title: "从 pwnlabs 学习云安全 — AWS/S3/EBS/RDS 攻击实战"
category: cloud-security
tags: [aws, cloud, s3, ebs, rds, pwnlabs, cloud-security]
triggers: [pwnlabs, pwnedlabs, s3-account-search, 公共快照, public EBS snapshot, public RDS snapshot, LeakyBucket]
created: 2026-06-10
updated: 2026-06-10
sources: [raw/从pwnlabs学习云安全-先知社区.md]
---

# 从 pwnlabs 学习云安全

> 来源：先知社区 | 3 个 AWS 云安全靶场实战

## 靶场 1 — 从 Public S3 Bucket 获取 AWS Account ID

**目标：** 仅知网站 IP 地址，获取 AWS Account ID

**步骤：**
1. 访问网站 → 图片来自 S3 存储桶 `mega-big-tech.s3.amazonaws.com`
2. 匿名列出桶内容：`aws s3 ls s3://mega-big-tech --no-sign-request --recursive`
3. 使用 [s3-account-search](https://github.com/WeAreCloudar/s3-account-search) 枚举 Account ID
4. 需要一个对目标桶有 `s3:GetObject` 权限的 ARN（可用 pwnlabs 提供的 `arn:aws:iam::427648302155:role/LeakyBucket`）

**工具原理：** S3 的 `s3:ResourceAccount` 条件键可在策略模拟时反推 Account ID。

## 靶场 2 — 公共 EBS 快照泄露

**目标：** 从 intern 的 AWS 凭证开始，提权获取敏感数据

**步骤：**
1. `aws sts get-caller-identity` → 确定当前用户
2. `aws iam list-attached-user-policies` + `get-policy-version` → 发现策略含 `ec2:DescribeSnapshotAttribute` 和 `ec2:DescribeSnapshots`
3. `aws ec2 describe-snapshots --owner-ids self` → 找到快照
4. 查看 `createVolumePermission` → `{"Group":"all"}` 公共可访问
5. AWS 控制台 → 从快照创建卷 → 挂载到 EC2 → 读取文件系统
6. 在 `/home/intern/practice_files/` 中找到 `s3_download_file.php` → 泄露新 AK/SK

## 靶场 3 — 公共 RDS 快照泄露

**目标：** 仅知 AWS Account ID

**步骤：**
1. 搜索该 Account ID 下所有公共快照 → 发现公共 RDS 快照
2. 还原快照 → 创建新 VPC 并设为公开访问
3. 创建 EC2（与数据库同 VPC）→ 连接 RDS → `psql` 查询数据库

## 常用操作

```bash
# 匿名列出 S3 桶
aws s3 ls s3://bucket-name --no-sign-request --recursive

# 查看当前身份
aws sts get-caller-identity

# 枚举公共快照
aws ec2 describe-snapshots --owner-ids <account-id>

# 查看快照共享权限
aws ec2 describe-snapshot-attribute --snapshot-id <id> --attribute createVolumePermission
```

## Connections
- Related: [[cloud-security/minio-cve-2025-31489-arbitrary-write]], [[cloud-security/docker-container-pentest]]
- Source: [[raw/从pwnlabs学习云安全-先知社区]]
