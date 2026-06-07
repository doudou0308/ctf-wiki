---
title: 第三届"长城杯"网数智安全大赛(防护赛)决赛 部分 WP
category: ctf
tags: [ctf, changchengbei, 长城杯, reverse, ai, web]
triggers: [长城杯, 长城杯RE, 长城杯AI, DokiLogic, notjavaweb, dumbcpp, LatticeCNN, wallkone, Ren'Py, Unity IL2CPP, 自定义VM, LWE CNN]
created: 2026-06-07
updated: 2026-06-07
sources: [raw/第三届"长城杯"网数智安全大赛(防护赛)决赛-部分WP.md]
---

# 第三届"长城杯"网数智安全大赛(防护赛)决赛 — 部分 WP

> 作者：wallkone | 排名：全国二等奖（16名）
> 来源：[mp.weixin.qq.com](https://mp.weixin.qq.com/s/QwUF9NvhA-lWXPlZDxCc1g)
> 日期：2026-04-29

## 题目一览

| 题目 | 赛道 | 核心技术 | Wiki 页面 |
|------|------|---------|----------|
| DokiLogic | Reverse | Ren'Py 游戏逆向 + 嵌入 PE + XOR 0x23 | [[reverse/renpy-pe-xor-23]] |
| notjavaweb | Reverse | Spring Boot 自定义栈式 VM + Rust AES-CBC + PCAP 解密 | [[reverse/springboot-custom-vm-pcap-aes]] |
| dumbcpp | Reverse | Unity IL2CPP + Themida 内存 Dump + AES-CBC 解密 | [[reverse/unity-il2cpp-themida-aes]] |
| LatticeCNN | AI | LWE 小维度噪声枚举 + CNN 特征提取 + AES 密钥推导 | [[ai-ml/lattice-cnn-lwe-small-dimension]] |
| ISW | Web | Web 后台 getshell + find 提权（基础套路） | — |

## Flags

| 题目 | Flag |
|------|------|
| DokiLogic | `flag{f17c53c3-dc26-46b1-b373-2ca00a6a6721}` |
| notjavaweb | `flag{F1n@1ly_Y0u_G0t_Th1s_f1ag_and_f1nd_7h3_TRUTH_D0_Y0u_L1k3_1t?}` |
| dumbcpp | `flag{cc697aa6-42b6-4341-9bed-6b5cc5c603fd}` |
| LatticeCNN | `flag{7ffd71471b67009e1c39cfaa259cf7b2}` |

## 填补的知识空白

1. **Ren'Py 游戏逆向**：`.rpyc` 反序列化 + 嵌入 PE + XOR 解码 — 全新游戏引擎逆向方向
2. **自定义栈式 VM**：Spring Boot JADX 定位 VM 上下文 → PCAP 提取 → payload 解密 → Rust AES-CBC 逆向 — 复杂多阶段逆向链
3. **Unity IL2CPP + Themida**：内存 dump 脱壳 → IL2CPP 元数据定位方法 → AES-CBC 解密 — Unity 游戏逆向标准流程
4. **LWE 小维度枚举**：卷积公开 + 隐藏向量 + 小噪声 → 暴力枚举 5^8 种噪声 → 推导 AES 密钥 — 新类型 CTF AI 题

## Connections
- Source: [[raw/第三届"长城杯"网数智安全大赛(防护赛)决赛-部分WP]]
