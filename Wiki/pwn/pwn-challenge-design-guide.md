---
title: "CTF Pwn 题目设计指南 — 先知社区"
category: pwn
tags: [pwn, ctf, 出题, docker, deploy, compile, xinetd]
triggers: [pwn出题, docker pwn, xinetd, ctf_xinetd, 编译参数, 题目设计]
created: 2026-06-10
updated: 2026-06-10
sources: [raw/CTF Pwn 题目设计指南-先知社区.md]
---

# CTF Pwn 题目设计指南

> 来源：先知社区 | 完整 Docker 部署 + 编译参数 + 常见问题

## Docker 部署

### 项目结构
```
docker/
├── Dockerfile
├── bin/pwn
├── ctf.xinetd
├── docker-compose.yml
├── flag / flag.sh
├── start.sh
```

### 基础命令
```bash
docker build -t ctf-pwn .
docker run -d -p 9999:9999 --name pwn_test ctf-pwn
docker exec -it [CONTAINER_ID] /bin/bash
```

### Dockerfile 要点
- 32 位程序需启用 i386 架构并安装 `libc6:i386`, `libstdc++6:i386` 等
- 使用 `ctf_xinetd` 项目作为基础：[Eadom/ctf_xinetd](https://github.com/Eadom/ctf_xinetd)
- 避免静态链接（关键函数可能被优化掉）

## 编译参数

| 参数 | 效果 | 利用难度 |
|------|------|---------|
| `-z execstack` | 关闭 NX，栈可执行 | 低 (shellcode) |
| `-z noexecstack` | 开启 NX，需 ROP | 中 |
| `-fno-stack-protector` | 关闭 Canary | 低 |
| `-fstack-protector` | 部分 Canary | 中 |
| `-no-pie` | 固定基址 | 低 |
| `-pie` | 地址随机化 | 中 (需泄露) |
| `-z norelro` | GOT 全程可写 | 低 |
| `-z lazy` | 部分 RELRO (默认) | 中 |
| `-z now` | 完全 RELRO，GOT 只读 | 高 |
| `-m32` | 编译为 32 位 | 低 |
| `-s` | 去除符号表 | 中 |

## 常见问题

- **缺少库**: `ldd` 查看依赖 → Dockerfile 显式安装对应包
- **not found 但命令存在**: xinetd 删除系统目录导致依赖库缺失
- **避免静态链接**: `-static` 可能优化掉 `system/execve/open` 等函数

## Connections
- Source: [[raw/CTF Pwn 题目设计指南-先知社区]]
