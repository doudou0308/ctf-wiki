---
title: "2026 数字中国 PWN WP"
category: ctf
tags: [ctf, 数字中国, pwn, writeup, kernel, 红明谷]
triggers: [数字中国, 数字中国创新大赛, 红明谷, 数字安全赛道]
created: 2026-06-10
updated: 2026-06-10
sources: [raw/2026数字中国pwn-先知社区.md, raw/2026数字中国创新大赛数字安全赛道暨 三明市第六届"红明谷"杯 pwn 22-先知社区.md]
---

# 2026 数字中国创新大赛 PWN WP

> 来源：先知社区 | 2 场比赛共 4 题

## keep_stack — 栈溢出 + 混淆结构体

- 结构体 `ctx` 在栈上，`ctx->text` 成员越界可覆盖返回地址
- 步长选择 2 使覆写更完整；初始固定 4 字节跨度，第一次修改返回地址重新返回，第二次完成修改
- `max_units` 改到 0x24，在 0x23 时修改 `write_index` 为 0x29 即可溢出到返回地址

## hkobj — AArch64 内核题 (环境启动 + 8 字节任意地址写)

- `ioctl` + `kmalloc-64` 限制分配 3 个对象
- 漏洞点：`alloc` 后每次向后平移 0x10 字节；`if` 判断中额外向下越界写 8 字节 → 覆写 `user_payload.buf` 全局变量 → 任意 8 字节写
- 未找到泄露点，开启 KASLR 下无法获取内核基址
- 使用 Docker 启动题目环境，`docker cp` 传入 exp

## odd-chat (红明谷) — 整数溢出 → 堆溢出 → Tcache 投毒 → GOT 劫持

- `abs(INT_MIN) % 24` → `-8` → 强转为 `size_t` 巨大正数 → 堆溢出
- 溢出覆盖相邻空闲 Tcache chunk 的 `fd` 为 `.bss` 地址 → 分配到全局指针区域
- 写入 `free@GOT` 地址到 `name` 指针 → 泄露 libc
- `rename()` 的 `fgets` 向 `free@GOT` 写 one_gadget → `clear_chat()` 触发

## neural (红明谷) — AI Chatbot + system RCE

- `app.py` 前端暴露 `/api/raw` 透传接口 + `/api/knowledge/upload` 任意文件上传
- 后端 `load_ncml_model` 函数解析 `.ncm` 模型时发现 `Type=5` 数据块 → 拷贝到 `p_command` → `system(&p_command)`
- `validate_hook` 要求路径以 `/opt/neuralchat/plugins/` 开头 → 目录穿越 `/../../../` 绕过
- `handle_admin(CMD_ADMIN=0xFF)` 支持重新加载模型文件
- `verify_admin_token` 含时间校验(60s过期)，本地可获取，远程需爆破微调

## Connections
- Related: [[pwn/skill-advanced-exploits]], [[pwn/skill-advanced-exploits-2]]
- Source: [[raw/2026数字中国pwn-先知社区]]
