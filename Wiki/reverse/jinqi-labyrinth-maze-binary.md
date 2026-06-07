---
title: 京麒 L4byryη7h — 巨量 ELF 迷宫寻路
category: reverse
tags: [reverse-engineering, elf, maze, bfs, binary-patching, path-finding]
triggers: [京麒CTF, L4byryη7h, ELF迷宫, 2048x2048, UD2指令, loffs, ldata, lmsk, BFS寻路, 二进制Patch]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/reverse/Jinqi-L4byryη7h-Maze-Binary.md]
---

## Summary

ELF 二进制通过三个巨型 LOAD 段嵌入 2048×2048 迷宫（4,194,304 个 cell），每个 cell 的 UD2 指令编码方向可达性。需二进制 Patch + BFS 寻路。

## Key Points

### 迷宫数据结构
| 段 | 大小 | 用途 |
|----|------|------|
| `lmsk` | 524,288 bytes | 位图（每 cell 1 bit），指示 cell 的"极性" |
| `loffs` | 4,194,304 × 4 bytes | 偏移表，指向每个 cell 在 ldata 中的位置 |
| `ldata` | 4,194,304 × 96 bytes | 机器码，内含 UD2 指令序列编码方向 |

### 方向编码
- 每个 cell 的 96 字节 ldata 中 UD2 指令（`0F 0B`）出现的位置（4 个位置对应 W/A/S/D）
- UD2 后紧跟的字节最低位与 lmsk 位图配合决定方向是否可达

### 二进制 Patch
- 将 LOSE 处理函数首字节 patch 为 `ret (0xC3)`
- SIGTRAP 检查分支从 JE 改为 JMP（跳过校验走向 WIN）

### BFS 寻路
- 起点 index=3363229，终点 index=3562150
- 4M cell 中找到最短路径
- 路径还原为 WASD 全小写字符串
- Flag = `flag{md5(最短路径WASD字符串)}`

### 核心算法
```python
from collections import deque
q = deque([START])
while q:
    cur = q.popleft()
    # 解析 loffs[cur] → 定位 ldata
    # 扫描 96 bytes 中 UD2(0F 0B) 位置
    # 每个 UD2 字节后 &1 与 lmsk[cur] 比较确定方向有效性
```

## Connections
- Related: [[skill-tools]] (GDB)
- Related: [[skill-anti-analysis]] (反调试)
