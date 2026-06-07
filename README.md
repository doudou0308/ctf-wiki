<p align="center">
  <img src="https://img.shields.io/badge/Obsidian-7C3AED?style=flat&logo=obsidian&logoColor=white"/>
  <img src="https://img.shields.io/badge/CTF%20Wiki-v1.0-22c55e?style=flat"/>
  <img src="https://img.shields.io/badge/LLM%20Ready-Yes-3b82f6?style=flat"/>
  <img src="https://img.shields.io/badge/pages-231%2B-f59e0b?style=flat"/>
</p>

# CTF Wiki 🛡️

**一个面向 LLM Agent 的 CTF 知识图谱** — 基于 [karpathy/LLM Wiki](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) 三层架构搭建，覆盖 **11 个赛道、231+ 个 markdown 文件**，在日常 CTF 竞赛与安全研究中持续迭代更新。

## 快速一览

| 统计项 | 数值 |
|--------|------|
| 命名技术/赛题页 | 97 页 |
| Skill 深度参考（含 PoC 代码） | 99 个 |
| Command 解题指南 | 11 个 |
| GHSA 概念合成页 | 8 张（覆盖 ~1124 条目） |
| CVE 产品漏洞索引 | 2 张（覆盖 ~400 个 CVE） |
| **总计** | **~231+ 个文件** |

## 覆盖赛道

```
├── Web             │  30+ 页  — SQLi, SSTI, SSRF, JWT, 反序列化, XSS, 原型链...
├── Crypto          │  5 页   — RSA, ECC, 格, PRNG, ZKP, 流密码, 经典密码...
├── Pwn             │  6 页   — ret2win, UAF, ROP, 堆利用, 内核, 沙箱逃逸...
├── Reverse         │  13 页  — APK, SMC, 字节码, PyInstaller, 固件...
├── Forensics       │  6 页   — 隐写, 网络取证, 磁盘, 日志, 内存, 信号...
├── Misc            │  7 页   — 编码, PyJail, BashJail, 游戏, RF, DNS...
├── AI-ML           │  14 页  — Agent 架构, Prompt 注入, 模型攻击...
├── Cloud Security  │  5 页   — 容器逃逸, K8s, Nacos, vCenter, MinIO...
├── Code Audit      │  15 页  — CVE 模式, OWASP Top 10, 产品漏洞索引...
├── Malware         │  Skill  — C2 协议, PE/.NET, 反混淆, shellcode...
└── OSINT           │  Skill  — 社交媒体, DNS, 地理定位, Google Dorking...
```

## 三层架构

```
/
├── raw/                # Source Layer — 不可变源文档（剪报、WP 原文、ctf-solutions）
├── wiki/               # Wiki Layer — 按赛道子目录分类的 markdown 知识页面
│   ├── index.md        # 完整目录索引（Agent 检索入口）
│   ├── log.md          # 变更日志
│   ├── {web,crypto,...}# 11 个赛道子目录
│   └── ctf/            # 竞赛实体页（赛事索引 + WP 摘要）
├── AGENTS.md           # Schema Layer — LLM Agent 工作规范（Ingest/Triggers/格式）
└── LLM Wiki.md         # Karpathy 方法论原始文档
```

每个知识页包含完整的 `frontmatter`（`triggers` 字段让 Agent 快速匹配）+ `Key Points`（核心要点）+ `Exploit` 代码 + `Connections`（交叉引用）。

## 食用方式

### 👤 个人使用
在 **Obsidian** 中打开此 vault，通过 `wiki/index.md` 浏览或 `Ctrl+O` 搜索知识点。

### 🤖 LLM Agent 使用
Agent 通过 `SearchCodebase`/`Grep` 检索 Wiki 目录，匹配 `triggers` 字段中的技术关键词，秒级定位相关知识点和 PoC 代码。

### 📥 新增知识
1. 剪报/文章放入 `raw/`
2. Agent 按 `AGENTS.md` 规则读取 → 摄入到 `wiki/` 对应赛道
3. 更新 `index.md` + `log.md`

## License

MIT
