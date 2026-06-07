# CTF Wiki

面向 LLM Agent 的 CTF 知识图谱，基于 LLM Wiki 三层架构搭建。

> 本项目基于 [karpathy/LLM Wiki](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) 方法论的指导，结合个人日常 CTF 竞赛与安全研究的知识点搜集整理，不断迭代更新。

## 架构

```
/
├── AGENTS.md               # 工作规范（LLM Agent 行为指南）
├── LLM Wiki.md              # Karpathy 方法论原始文档
├── raw/                     # Source Layer — 不可变源文档（剪报、WP 原文）
├── wiki/                    # Wiki Layer — LLM 生成的 markdown 知识页面
│   ├── index.md             # 完整目录索引
│   ├── log.md               # 变更日志
│   ├── web/                 # Web 安全（SQLi, SSTI, SSRF, JWT, 反序列化...）
│   ├── crypto/              # 密码学（RSA, ECC, 格, PRNG...）
│   ├── pwn/                 # 二进制利用（ret2win, UAF, ROP, 堆...）
│   ├── reverse/             # 逆向工程（APK, SMC, 字节码...）
│   ├── forensics/           # 取证（隐写, 网络, 磁盘, 日志...）
│   ├── misc/                # 杂项（编码, jail, 游戏, RF...）
│   ├── ai-ml/               # AI/ML 安全（Agent 架构, Prompt 注入...）
│   ├── cloud-security/      # 云安全（容器逃逸, K8s, Nacos...）
│   ├── code-audit/          # 代码审计（CVE 模式, OWASP Top 10...）
│   ├── malware/             # 恶意软件分析（C2, PE/.NET, 反混淆）
│   ├── osint/               # 开源情报（社交媒体, DNS, Google Dorking）
│   ├── rules/               # 全局规则（解题规则, 启动提示词, Skill 目录）
│   └── ctf/                 # 竞赛实体页（赛事索引 + WP 摘要）
└── AGENTS.md                # Schema Layer — Agent 工作流规范
```

## 覆盖赛道

| 赛道 | 命名页 | Skill 参考 | Command 指南 |
|------|--------|-----------|-------------|
| Web | 30+ | ✕ | ✓ |
| Crypto | 5 | 17 | ✓ |
| Pwn | 6 | 21 | ✓ |
| Reverse | 13 | 23 | ✓ |
| Forensics | 6 | 16 | ✓ |
| Misc | 7 | 13 | ✓ |
| AI-ML | 14 | 3 | ✓ |
| Cloud Security | 5 | ✕ | ✕ |
| Code Audit | 15 | 4 | ✕ |
| Malware | ✕ | 5 | ✓ |
| OSINT | ✕ | 5 | ✓ |

## 内容统计

- **命名技术/赛题页**: 97 页
- **Skill 深度参考**: 99 个（含 PoC 代码的攻击模式库）
- **Command 解题指南**: 11 个（覆盖全部 9 赛道 + solve/writeup）
- **GHSA 合成页**: 8 张（~1124 GHSA 条目按技术主题聚合）
- **CVE 产品漏洞索引**: 2 张（覆盖 ~400 个 CVE/CNVD 产品漏洞）
- **规则/目录**: 5 个
- **总计**: ~231 个 markdown 文件

## 食用方式

### 对于个人
在 Obsidian 中打开此 vault，通过 `index.md` 浏览或直接搜索知识点。

### 对于 LLM Agent
Agent 解题时通过 `SearchCodebase` / `Grep` 检索 Wiki 目录，匹配 `triggers` 字段中的技术关键词，快速定位相关知识点和 PoC 代码。

### 新增知识
1. 未处理的文章放入 `raw/`
2. Agent 按 `AGENTS.md` 规则读取并摄入到 `wiki/`
3. 更新 `index.md` 和 `log.md`

## License

MIT
