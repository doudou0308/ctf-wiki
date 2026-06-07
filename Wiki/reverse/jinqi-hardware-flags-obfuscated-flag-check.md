---
title: 京麒 Hardware Flaws! — 混淆 Flag 验证与线性变换逆推
category: reverse
tags: [reverse-engineering, gdb, linear-transformation, gf2, matrix, anti-debug]
triggers: [京麒CTF, Hardware Flaws, 硬件敏感, GDB oracle, 线性变换逆推, RDTSC, GF(2) 矩阵, bcmp, basis vector, Capstone]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/reverse/Jinqi-Hardware-Flaws-Obfuscated-FlagCheck.md]
---

## Summary

二进制 flag 验证程序，96 字节 flag 通过 GF(2) 线性变换与期望值比较。包含 RDTSC 时序反调试 + 物理机/WSL 环境检测。通过 GDB oracle 和基向量探测构建变换矩阵。

## Key Points

### 环境检测
- **RDTSC 时序检测**：判断是否运行在虚拟机中（时序差异）
- **物理机/WSL**：仅物理机或 WSL 环境正常工作

### 线性变换逆推
1. 96 bytes flag 通过 GF(2) 矩阵运算与期望值比较
2. **基向量探测**：输入单 bit 为 1（其余为 0），观察输出 -> 构建变换矩阵
3. 求解逆矩阵还原原始输入

### GDB 自动化 Oracle
```python
# 在 bcmp 函数调用处设断点
# rdi = 用户输入变换后结果, rsi = 期望值
# 脚本化批量测试不同输入
gdb -batch -x /tmp/gdb_oracle.py 2>&1 | grep -E '^(OUTPUT|TARGET) '
```

### 工具链
- **GDB + Python 脚本**：自动化断点捕获与输入注入
- **Capstone 反汇编**：定位 keycheck 函数与常量区域
- **pyelftools**：ELF 段分析

### 反调试绕过
- NOP 跳转或 Patch 掉 RDTSC 检查
- Patch 关键分支确保进入分析路径

## Connections
- Related: [[skill-tools]] (GDB/Capstone)
- Related: [[skill-anti-analysis]] (反调试技术)
