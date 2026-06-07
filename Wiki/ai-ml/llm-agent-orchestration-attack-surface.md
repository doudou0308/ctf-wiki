---
title: LLM / Agent 编排平台攻击面
category: ai-ml
tags: [llm, ai-agent, orchestration, mcp, prompt-injection, tool-poisoning, dify, langflow, comfyui]
triggers: [langflow, dify, comfyui, ragflow, ollama, mcp server, 编排平台, 工具投毒, 间接注入, 提示注入, Agent平台, 工作流, tool calling, function calling, .mcp.json]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/ai-ml/LLM-Agent-编排平台攻击面.md]
---

## Summary

AI 编排平台（Dify/Langflow/ComfyUI/RAGFlow/Ollama/MCP Server）的攻击面分析。危险不在模型本身，而在工具调用、配置反序列化、代码执行层。

## Key Points

### 高危入口

| 入口 | 风险 | 典型平台 |
|------|------|---------|
| **代码校验接口** | `ast` 解析后执行 → 装饰器/默认参数在定义阶段执行 | Dify, Langflow |
| **配置导入** | pickle/对象快照反序列化 | 工作流导入 |
| **工具投毒** | 工具描述含恶意指令进入模型上下文 | MCP 工具 |
| **间接提示注入** | Agent 读取的网页/Issue/文档含恶意指令 | 浏览器 Agent |
| **仓库配置投毒** | `.mcp.json`、hooks 自动生效 | VS Code MCP |
| **远程 MCP 服务器** | 任意 `tools/call`、`resources/read` | MCP 协议 |

### 攻击流程

1. 确认产品版本 → nuclei 模板验证历史 CVE
2. 识别执行层（代码校验/自定义节点/配置反序列化）
3. 测试代码校验绕过：`@exec("...")` 装饰器
4. 测试配置导入反序列化
5. 测试远程 MCP：`tools/list` → `resources/read file:///etc/passwd`

### 常见误区

- 把问题归咎于模型——真正危险的是工具、编排、配置
- 只测聊天框不测 API 和配置
- 忽略仓库内配置（`.mcp.json` 是供应链入口）
- 忽略"读取型输入"（Issue/网页/知识库 = 间接提示注入源）

## Connections

- Related: [[web/ghsa-rce-overview]] (RCE 模式)
- Related: [[web/ghsa-ssrf-auth-bypass]]
- Related: [[ctf-api-mcp-paradigm]]
- Related: [[ai-solver-rules]] (提示注入防御)
