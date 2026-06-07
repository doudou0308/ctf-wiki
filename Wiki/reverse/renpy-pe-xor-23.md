---
title: Ren'Py 游戏逆向 — script.rpyc 反序列化 + 嵌入 PE XOR 0x23
category: reverse
tags: [renpy, game-reverse, pe-embedded, xor, rpyc, pickle, zlib, script-bytecode]
triggers: [Ren'Py, script.rpyc, RENPY RPC2, renpy.input, .rpyc反序列化, pickle zlib, 嵌入PE, XOR 0x23, subprocess stdout, dokigame]
created: 2026-06-07
updated: 2026-06-07
sources: [raw/第三届"长城杯"网数智安全大赛(防护赛)决赛-部分WP.md]
---

## Summary

Ren'Py 打包的游戏 `game/script.rpyc` 文件中嵌入了一个 Windows PE 二进制。游戏逻辑：运行该 PE 得到 stdout 密文 → 用户输入逐字节 XOR `0x23` → 与密文比较。反序列化 `.rpyc` 的 AST 后提取 PE 并运行即可还原输入。

## Key Points

- `.rpyc` 文件头 `RENPY RPC2`，数据经过 zlib 压缩 + pickle 序列化
- 反序列化后得到 Ren'Py AST，在 init Python 块中找 `_f` 变量（bytes 字面量 = 嵌入 PE）
- PE 运行时通过 `subprocess.run` 的 stdout 输出密文
- 用户输入逐字节 `XOR 0x23` 后与密文比较 → `flag = cipher XOR 0x23`

## 提取脚本流程

```python
import zlib, pickle, struct, subprocess, ast, os

# Step 1: 读取 .rpyc，解压缩 slot=2
data = open("game/script.rpyc", "rb").read()
# 跳过 "RENPY RPC2" header + 解析 slot 表
# slot_id=2 的块用 zlib.decompress 解压

# Step 2: pickle.loads 得到 AST 元组
_, stmts = loads(read_rpyc("game/script.rpyc", 2))
init_code = stmts[1].block[0].code.source  # init Python 块源码

# Step 3: 解析 AST 提取 _f 字节数组
tree = ast.parse(init_code)
for node in ast.walk(tree):
    if isinstance(node, ast.Assign):
        if node.targets[0].id == "_f":
            blob = node.value.value  # bytes literal

# Step 4: 写入临时文件并执行
with open("payload.exe", "wb") as f:
    f.write(blob)
out = subprocess.run(["payload.exe"], stdout=subprocess.PIPE).stdout
os.remove("payload.exe")

# Step 5: XOR 0x23 还原 flag
flag = "".join(chr(b ^ 0x23) for b in out)
print(flag)
```

输出：`f17c53c3-dc26-46b1-b373-2ca00a6a6721`

连接形式：`flag{f17c53c3-dc26-46b1-b373-2ca00a6a6721}`

## Connections
- Related: [[pyinstaller-cython-reverse]] (Python 字节码逆向)
- Related: [[skill-languages]] (Python/游戏逆向)
- Source: [[raw/第三届"长城杯"网数智安全大赛(防护赛)决赛-部分WP]]
