---
title: CTF 竞赛 API 与 MCP 接入范式
category: ai-ml
tags: [mcp, api, ctf-platform, ai-integration]
triggers: [MCP, CTF API, list_challenges, submit_flag, Bearer Token, Claude MCP, LangChain MCP]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/ai-ml/AICTF-CTF竞赛API-MCP接入范式.md]
---

## Summary

AI Agent 通过 MCP/API 自动接入 CTF 平台的标准化方式。

## Key Points

- MCP 接入：5 核心工具（list_challenges/start/stop/submit_flag/view_hint）
- 限速：3 次/秒，最多 3 实例并发
- view_hint 扣 10% 总分
- 通用 MCP JSON：
```json
{"mcpServers": {"platform": {"url": "...", "headers": {"Authorization": "Bearer ..."}}}}
```

### Claude 接入

```bash
claude mcp add
```

### LangChain 接入

```python
MultiServerMCPClient + create_react_agent
```

### 常见误区

- 限速 3 次/秒需 `sleep(0.5)`
- 忘 stop 实例占并发
- 多 flag 题需逐个提交

## Connections

- Related: [[llm-agent-orchestration-attack-surface]]
- Related: [[ai-agent-architecture-patterns]]
