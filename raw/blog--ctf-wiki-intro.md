# 花 3 天，我给 AI 搭了一个 CTF 知识图谱

> 240 个 markdown 文件、12 个赛道、100 个深度参考——花了 3 天给 AI 搭的 CTF 知识引擎。

## 痛点：AI 解题的幻觉死循环

打 CTF 的时候，最烦的不是题目难，而是换个环境就忘了以前踩过的坑。

SQL 注入的基本手法我背了十来遍了，但每次遇到新的过滤规则还是要翻笔记。House of Cat、Largebin Attack 这些堆利用技术，两个月不碰就跟没学过一样。更别提赛场上时间紧迫，脑子里一片空白的时候特别多。

我也想过让 AI 帮忙解题。但现实很骨感：

**痛点一：用不起高端模型**

GPT-5、Claude 确实是解题利器，但一个月 20 刀起步，API 调用下来赛场上猛跑几轮就几十块钱。对于学生党偶尔打打比赛的人来说，这个成本并不低。而免费模型在 CTF 这种需要多步推理的复杂场景下，效果差的不是一星半点。

**痛点二：国产模型遇长上下文就出现幻觉死循环**

国产大模型日常用还不错，但一旦碰到 CTF 这种需要连续推理的问题，上下文一长就开始出幻觉——自己编一个不存在的函数签名、发明一个不可能的绕过方式，然后基于错误的假设继续推导，越走越偏。最典型的就是在同一个思路上来回绕圈："再试一次"、"换个参数"、"再试一次"……直到耗尽 token 也没走出来。

**痛点三：AI 没有"记忆"**

就算同一个题型上周刚打过，这周遇到换个壳的问 AI，它依然从零开始推理，不会记得上次踩过的坑、绕过的弯。CTF 最宝贵的就是经验积累，但 AI 每次对话都是"失忆"的。

**痛点四：赛场上复现环境浪费时间**

遇到 Pwn 题或者需要特定工具链的题目，本地装环境、配依赖就要花掉半小时。用 Docker 做工具镜像可以解决，但每次手动拉镜像、跑容器、拷文件，操作一多就手忙脚乱。

---

## 破局：让 AI 读自己的知识库

后来我换了个思路：**如果笔记不是给我看，而是给 AI 看的呢？**

核心想法很直接——与其让 AI 凭空推理，不如把踩过的坑、总结好的攻击链、写好的 PoC 代码全部结构化地喂给它。当 AI 遇到一道题时，它不是在脑子里凭空搜索（那正是它最弱的地方），而是去检索一个**精心整理过的知识库**，拿到最相关的几页参考，再结合题目做适应性调整。

这样有几个好处：
- **不依赖模型本身的知识储备**——知识库里的内容是自己写的、验证过的，比模型训练数据里的泛泛信息可靠得多
- **对抗幻觉**——AI 不是"猜答案"，而是从已有文档中提取相关模式，再组合成解题方案，每一步都有据可查
- **低成本**——不需要花哨的 RAG 架构，一个文件夹 + 结构化 Markdown 就够了，免费模型也能用得很好

这个仓库叫 [ctf-wiki](https://github.com/doudou0308/ctf-wiki)，基于 [Karpathy 的 LLM Wiki](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) 三层架构搭建。

于是每篇知识页都有这样一个 frontmatter：

```yaml
---
title: "House of Cat — Largebin Attack + IO_FILE 利用"
category: pwn
tags: [pwn, heap, largebin-attack, fsop, io-file, house-of-cat]
triggers: [largebin attack, _IO_list_all, wide FILE, setcontext, 2.35, house of cat, FSOP]
---
```

`triggers` 字段就是给 AI 准备的。当你在 IDE 中问 AI 一个问题时，它会通过 `SearchCodebase` 或 `Grep` 匹配这些关键词，**精确命中**对应的技术页面，而不是泛泛地回答。这比任何 RAG 方案都简单直接——不需要向量数据库，不需要 embedding，一个 grep 就够了。

### 内容从哪来：每一页都是实战的沉淀

这个仓库的内容不是从什么地方批量扒来的。每一页知识都**来自于我参加过的比赛**——长城杯、ACTF、NewStar、数字中国、软件安全赛、安洵杯、LitCTF……打完一场比赛，我会把自己解题的完整过程写成 Writeup 发布在先知社区、安全客等论坛，再把这些经过验证的攻击链提炼成结构化的 wiki 页面。

这就是为什么每一页都带着 `sources` 字段——你可以追溯到原始的 Writeup 文章，看到完整的解题推导过程，而不是只有干巴巴的结论。

### 你也可以搭建自己的知识库

这个工作流的好处是：**它不依赖我这个仓库本身。**

你可以完全用自己的题目积累、自己的 Writeup，建立专属的知识库。只需要：
1. 建立一个类似的目录结构（raw + wiki + AGENTS.md）
2. 把打完比赛写的 Writeup 放进 raw/
3. 按照统一的 Markdown 格式提炼考点页
4. 配置 AI 的工作区指向这个文件夹

你的 AI 就能像用我这个仓库一样，精准检索你踩过的坑、你总结的套路。在这个线上 AI 解题逐渐普及的时代，**一个好的知识库就是平民模型和旗舰模型之间的差距弥合剂**——不需要 GPT、不需要 Claude Opus，一个普通的国产模型配合精心整理的知识库，也能在赛场上稳定输出有效的攻击思路。

## 三层架构

```
/
├── raw/          # 源文档层 — 各类 Writeup、文章、剪报的原始备份
├── wiki/         # 知识层 — 按赛道分类的 Markdown 知识页面
│   ├── {12个赛道}  # web/crypto/pwn/reverse/forensics/cloud-security...
│   ├── ctf/      # 竞赛实体页（每场比赛索引）
│   ├── index.md  # 完整目录（AI 检索入口）
│   └── log.md    # 变更日志
├── AGENTS.md     # 规范层 — AI 的工作流程和格式要求
└── LLM Wiki.md   # Karpathy 方法论原文
```

每页结构统一：**Frontmatter（含 triggers）→ Key Points → Exploit 代码 → Connections（交叉引用）**。

## 现在长什么样

240 个文件，覆盖 12 个安全赛道：

| 赛道 | 容量 | 亮点 |
|------|------|------|
| **Web** | 33 页 | SSTI/SSRF/SQLi/反序列化...一路到 Gopher + Flask RCE 链 |
| **Cloud Security** | 9 页 | AWS S3 靶场/K8s IngressNightmare/MinIO AI 投毒/腾讯云实战 |
| **Pwn** | 7 页 + 19 skill | House 系列从 Storm 到 Cat 到 Apple 2，IO_FILE/FSOP 通解模板 |
| **Crypto** | 7 页 + 17 skill | RSA Wiener/Coppersmith/格 LLL/PRNG 状态恢复 |
| **Code Audit** | 16 页 | OWASP Top 10 + FastAdmin/ShowDoc/Nacos 等产品 CVE 索引 |
| **Reverse** | 12 页 + 19 skill | APK/SMC/PyInstaller/Unity IL2CPP/自定义 VM |
| **AI-ML** | 15 页 | Prompt 注入/对抗样本/模型攻击/LWE |
| **Forensics** | 5 页 + 15 skill | OtterCTF 全题解/R3CTF WSL 磁盘/冰蝎流量解密 |
| **Misc** | 7 页 + 13 skill | PyJail/BashJail/PNG IDAT DP 修复/零宽字符 |
| **CTF 竞赛索引** | 15 页 | 长城杯/ACTF/NewStar/数字中国/安洵杯/软件安全赛 |
| **Malware** | Skill | C2 协议/PE .NET/反混淆/YARA |
| **OSINT** | Skill | 社交媒体/DNS/地理定位/Google Dorking |

**不过说清楚：这些东西不是面向初学者的教程。** 每个页面重心在"关键考点"和"攻击链"，而不是基础知识。适合有基础的人快速找回记忆，适合 AI 快速提取要点。

## 🐳 Agent Docker Tools：把解题环境变成"即拉即用"

知识库解决了"知道怎么做"的问题，但 CTF 的现场操作才是真正的战场——编译 exp、跑远程、调试崩溃，每一步都可能因为缺少一个工具或依赖而卡住。

所以我同时维护了一个配套仓库 **[agent-docker-tools](https://github.com/doudou0308/agent-docker-tools)**，一个基于 Kali Linux 的 AI 驱动 Docker 工具镜像，预装 30+ 安全工具，开箱即用。

### 内置工具链

| 类别 | 工具 |
|------|------|
| **Web 安全** | sqlmap, ffuf, whatweb, nuclei, wpscan, commix, subfinder, arjun, katana, httpx |
| **二进制/Pwn** | pwntools, gdb, pwndbg, ROPGadget, one_gadget, radare2 |
| **逆向** | Ghidra 12.0.3 + GhidraMCP (MCP Server), JADX 1.5.5 |
| **密码学** | RsaCtfTool, gmpy2, pycryptodome, z3-solver, hashcat, john |
| **取证** | binwalk, foremost, sleuthkit, tshark, volatility3, steghide, exiftool |
| **反序列化** | ysoserial, phpggc |
| **其他** | jwt_tool, tplmap, XSStrike, Gopherus, Metasploit, nuclei-templates, vulhub, SecLists |

### 一键启动

```bash
docker compose up -d
```

### 典型工作流

```
1. AI 分析题目 → 写出 solve.py
2. docker cp solve.py 到容器
3. docker exec 执行 → 结果写回 /tmp/out.txt
4. docker exec cat 读取结果
```

不用装环境、不用配依赖，拉到容器里就跑。这个工作流在"2026 软件安全赛"等比赛中已经验证过——从拿到附件到出 flag，全程 AI + Docker 配合，不需要手动折腾环境。

### Tri-Link：知识库 + 工具 + AI 的三联动

这两个仓库是配套设计的，各自解决不同层面的问题：

| 角色 | 组件 | 职责 |
|------|------|------|
| **AI (Brain)** | Trae IDE + Agent | 规划、推理、编排攻击链 |
| **Docker (Hands)** | agent-docker-tools | 执行安全工具、运行 exploit 脚本 |
| **Wiki (Memory)** | ctf-wiki（本仓库） | 卡壳时定向检索知识点与 PoC |

标准流程：**Trae 规划 → Docker 执行 → 卡壳查 Wiki → 继续推进**。

当你在赛场上遇到一个陌生的题型，AI 先查 Wiki 找到最接近的攻击模式，通过 Docker 容器里的工具链快速验证，如果卡住了再回 Wiki 查阅进阶技巧。**知识库提供思路，Docker 提供执行能力，AI 负责串联——三者缺一不可。**

这两个仓库的地址：
- 知识图谱：**[github.com/doudou0308/ctf-wiki](https://github.com/doudou0308/ctf-wiki)**
- Docker 工具：**[github.com/doudou0308/agent-docker-tools](https://github.com/doudou0308/agent-docker-tools)**

## 最让我意外的一点

原本建这个仓库只是为了自己用。但真正落地后，最爽的场景不是自己翻笔记，而是：**把一篇文章扔进 `raw/`，AI 自动读完、提炼考点、创建关联页面、更新目录索引。**

我只需要说一句"raw 新增了几篇文章"，剩下的所有事情——分类、创建 wiki 页、更新 index、提交 commit——AI 全包了。**我从一个笔记整理者变成了知识库的策展人**。

这个工作流大概是：

```
1. 看到好的 Writeup/文章 → 复制到 raw/
2. 告诉 AI："raw 新增了几篇文章"
3. AI 读完全文 → 提炼考点 → 创建赛道页面 → 更新索引 → 准备 commit
4. 我审核 → git push
```

整个过程从几小时缩短到几分钟。

## 最后的话：拥抱工具，但别忘了根本

不得不承认，这个时代变了。几年前打 CTF，高手们靠手搓 exploit、手撕加密，谁能第一个写出 payload 谁就拿一血。而现在，AI 参与解题已经不可避免——你还在手动调试的时候，别人已经让 AI 读完了题目、查了知识库、写好了 exploit 脚本。

这个仓库和配套的 Docker 工具，本质上是想在这个大环境下，**拉平平民模型和旗舰模型之间的差距**，让更多人能借助 AI 的力量更好地参与 CTF。

但我也想说清楚：**AI 是辅助，不是替代。**

这个仓库解决的是"AI 解题的痛点"——幻觉、死循环、没有记忆——但它不解决"你会不会打 CTF"的问题。如果你连 SQL 注入的原理都不清楚、连栈溢出的基本结构都没弄明白，AI 帮你写出 exploit 也只是知其然不知其所以然。

**基础不牢，地动山摇。** 不管 AI 再怎么进化，CTF 的核心从来不是写代码，而是理解漏洞的本质、培养安全的思维。这个仓库里的每一页知识，初衷都是帮你和 AI 更快地定位到正确的攻击方向，而不是跳过学习的过程。

希望你在使用这些工具的同时，也能回归根本——打好基础，理解原理，自己动手调试、思考、复盘。只有这样，无论这个时代怎么变，你都能步步跟上。

这两个仓库配合使用，效果更佳：
- 🧠 **知识图谱**：[github.com/doudou0308/ctf-wiki](https://github.com/doudou0308/ctf-wiki) — 思路与考点
- 🐳 **Docker 工具**：[github.com/doudou0308/agent-docker-tools](https://github.com/doudou0308/agent-docker-tools) — 工具与环境

---

*最后更新：2026-06-10 | 240+ 个文件 · MIT License*
