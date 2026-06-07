---
title: CTF AI Agent Benchmark / 评测框架参考
category: ai-ml
tags: [benchmark, agent-evaluation, nyu-ctf-bench, boxpwnr, hacksynth, cybench, xbow]
triggers: [Benchmark, Agent评测, NYU CTF Bench, BoxPwnr, HackSynth, Cybench, XBOW, CVE-Bench, EnIGMA, PACEbench, SEC-bench, InterCode-CTF]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/ai-ml/AICTF-CTF-Agent-Benchmark评测参考.md]
---

## Summary

用什么基准来评估和校准 AI CTF 解题能力。汇总当前主流 CTF Agent 评测框架和 Benchmark。

## 推荐 Benchmark

| # | 框架 | 特点 |
|---|------|------|
| 1 | NYU CTF Bench | NYU-LLM-CTF 维护，配套 D-CIPHER Agent |
| 2 | **BoxPwnr** (401★) | HTB/THM/picoCTF/Cybench/XBOW 等 15 平台统一框架 |
| 3 | **HackSynth** (310★) | Planner+Summarizer，PicoCTF/OverTheWire 200 题 |
| 4 | Cybench | 真实 CVE 漏洞利用评测 |
| 5 | **XBOW** | 首个登顶 HackerOne 的 AI，偏真实场景 |
| 6 | CVE-Bench | LLM 在真实 CVE 上的漏洞利用基准 |
| 7 | EnIGMA (ICML 2025) | 交互工具辅助 LM Agent 发现漏洞 |
| 8 | PACEbench | 渗透 Agent 能力评估 |
| 9 | SEC-bench | LLM 辅助安全测试 |
| 10 | InterCode-CTF | 最早的系统化 CTF Agent 评测之一 |

## 评测方法论

- **黑盒 vs 白盒**: 白盒（有源码）显著优于黑盒
- **单 Agent vs 多 Agent**: 多 Agent 在复杂题目有优势
- **工具质量 > 模型**: EnIGMA 论文结论
- **成本效率**: 高价模型不一定性价比最优

## 自校准建议

1. 用 HackSynth 本地跑 200 题 PicoCTF 作为 baseline
2. 用 BoxPwnr 在不同平台验证
3. 关注 XBOW 风格的真实 exploit 验证而非只是 flag 提交

## 参考论文排名（CTF 相关 Top 5）

1. **EnIGMA** (ICML 2025) — 工具 > 模型，交互环境关键
2. **HackSynth** (2024.12) — 200 题实证
3. **D-CIPHER** (2025.02) — 多 Agent 协作
4. **Hacking CTFs with Plain Agents** (2024.12) — 简单有效
5. **xOffense** (2025.09) — 微调 Qwen3-32B vs 通用 LLM

## Connections

- **Related:** [[ai-agent-architecture-patterns]] — AI 解题架构
- **Related:** [[ai-agent-architecture-comparison]] — Agent 架构对比
- **Related:** [[multi-model-context-compression]] — 多模型竞争与上下文压缩
- **Source:** [ctf-kb/ai-ml/AICTF-CTF-Agent-Benchmark评测参考.md](file:///c:/Users/ZZH/.trae/ctf-kb/ai-ml/AICTF-CTF-Agent-Benchmark评测参考.md)
