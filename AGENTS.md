# Wiki Schema & Workflow

This file defines how the wiki is structured and how you (the LLM) should maintain it. It is the source of truth for conventions, workflows, and standards.

## Directory Structure

```
Wiki/
├── AGENTS.md           # This file — schema and workflow definitions
├── raw/                # Immutable source documents (articles, papers, notes, images)
│   ├── assets/         # Downloaded images and attachments
│   ├── ctf-solutions/   # Historical CTF writeups with full exploit scripts
│   ├── ghsa/           # GHSA/CVE raw advisories (source documents, not indexed)
│   └── ...
├── wiki/               # LLM-generated markdown pages — the wiki itself
│   ├── index.md        # Content catalog: every page listed with link + summary
│   ├── log.md          # Chronological activity log
│   ├── web/            # Web security — SQLi, XSS, SSTI, SSRF, JWT, deserialization, etc.
│   ├── crypto/         # Cryptography — RSA, AES, ECC, lattices, PRNG, ZKP, etc.
│   ├── pwn/            # Binary exploitation — ret2win, UAF, ROP, heap, etc.
│   ├── reverse/        # Reverse engineering — PyInstaller, APK, SMC, etc.
│   ├── forensics/      # Forensics — stego, network, disk, memory, log, etc.
│   ├── misc/           # Miscellaneous — encoding, sandbox escape, game, etc.
│   ├── ai-ml/          # AI/ML security — CTF Agent, LLM attack surface, etc.
│   ├── cloud-security/ # Cloud security — container escape, Nacos, K8s, etc.
│   ├── code-audit/     # Code audit — OWASP Top 10, CVE patterns, etc.
│   ├── malware/        # Malware analysis — PE/.NET, C2, deobfuscation, shellcode (NEW)
│   ├── osint/          # OSINT — social media, DNS, geolocation, Google dorking (NEW)
│   ├── rules/          # Global rules — project rules, startup prompt, skills catalog (NEW)
│   └── ctf/            # CTF competition entity pages — one page per competition (NEW)
└── LLM Wiki.md         # The original methodology article
```

## Page Conventions

### Frontmatter (YAML)

Every wiki page MUST have YAML frontmatter:

```yaml
---
title: Page Title
tags: [tag1, tag2]                # 中英双语标签，如 [ssti, flask, template-injection]
category: web | crypto | pwn | reverse | forensics | misc | ai-ml | cloud-security | code-audit
triggers: [keyword1, keyword2]     # Agent 自动匹配关键词 — 覆盖中英双语、HTTP Header、路径名、题面常见词
created: YYYY-MM-DD
updated: YYYY-MM-DD
sources: [source1, source2]        # links to source documents in raw/
---
```

### Naming

- Use lowercase with hyphens: `quantum-computing-basics.md`
- Entity pages: noun-based, singular: `einstein.md`, `cryptography.md`
- Source summaries: prefix with source type: `paper--attention-is-all-you-need.md`
- Comparison/synthesis pages: `comparison--rnn-vs-transformer.md`

### Cross-references

- Use `[[wikilinks]]` (Obsidian native format) to link between wiki pages
- Use `[source](file:///raw/...)` to link to raw source documents
- Every page should link to related pages; no isolated pages without inbound links

### Section Structure (recommended)

```
# Title

## Summary
Brief 1-3 sentence overview.

## Key Points
Bullet-list of core takeaways.

## Details
Expanded explanation, structured by subtopics.

## Connections
- Related pages: [[page1]], [[page2]]
- Sources: [article title](file:///raw/article.md)

## Open Questions
- What remains unclear or contradictory?
```

## Operations

### Ingest

When the user provides a new source document:

1. **Read** the source document in `raw/`.
2. **Discuss** key takeaways with the user (optional — ask if they want a summary first).
3. **Create or update**: write a summary page in `wiki/`, then update all affected entity/concept pages across the wiki.
4. **Update `index.md`**: add the new page(s) to the catalog.
5. **Update `log.md`**: append a log entry with prefix `## [YYYY-MM-DD] ingest | <Title>`.

A single source may touch 10-15 wiki pages. Update all of them.

### Query

When the user asks a question:

1. **Read `index.md`** to find relevant pages.
2. **Read the relevant pages** in full.
3. **Synthesize an answer** with citations to wiki pages and source documents.
4. **If the answer is valuable** (a comparison, analysis, or discovery), file it as a new wiki page and update `index.md` + `log.md`.

### Lint

Periodically (or when asked), health-check the wiki:

- Contradictions between pages
- Stale claims that newer sources have superseded
- Orphan pages with no inbound links
- Important concepts mentioned but lacking their own page
- Missing cross-references
- Data gaps that could be filled with a web search
- Suggest new questions and new sources to investigate

### Update Rules

- **No destructive edits without confirmation**: when updating an existing page, present the diff or summarize changes.
- **Flag contradictions**: when new information contradicts an existing claim, don't silently overwrite — add both perspectives and note the conflict.
- **Propagate changes**: updating one page may require updating its cross-references. Do this automatically.
- **Keep `updated` field current**: every page edit should update the `updated` frontmatter field.

### GHSA / CVE Handling

When encountering GHSA (GitHub Security Advisory) entries in `ctf-kb/`:

1. **Do not ingest individual GHSA files** as separate wiki pages (~1124 files would bloat the wiki).
2. **Synthesis pages** (in `wiki/web/ghsa-*`, `wiki/code-audit/ghsa-*`, `wiki/crypto/ghsa-*`) serve as curated indexes organized by vulnerability class (RCE, XSS, SSRF, SQLi, etc.). Each synthesis page includes:
   - A Summary section describing the vulnerability class
   - A Common Patterns table listing sub-types with detection tips
   - A Representative GHSA Entries table with GHSA ID, CVE, severity, package, and brief
   - A Connections section linking to relevant named pages
   - A Detection & Exploitation Tips section
3. **Cross-referencing**: Named pages (e.g. [[ssrf-gopher-techniques]]) link to relevant GHSA pages via `GHSA: [[web/ghsa-*]]` in their Connections section.
4. **Raw files** remain in `.trae/ctf-kb/{web,code-audit,crypto}/GHSA-*.md` as authoritative source; wiki pages are indexes only.

## Page Types

### Entity Pages
For people, places, technologies, concepts. Core of the wiki.

### Source Summary Pages
Summary of a single source document. Named: `summary--<short-title>.md`

### Synthesis Pages
Cross-cutting analyses that synthesize multiple sources. Named: `synthesis--<topic>.md`

### Comparison Pages
Side-by-side comparisons of two or more entities/concepts. Named: `comparison--<topic>.md`

### Solution Pages (CTF Writeups)
For individual CTF challenge writeups or technical attack walkthroughs. Named with kebab-case: `<technique-slug>.md`.

Frontmatter requirements — same as Entity pages (`category`, `tags`, `triggers`, etc.).

Recommended body structure:

```markdown
# Title

## Summary
Brief 1-3 sentence overview of the challenge/technique.

## Key Points
- 关键考点: 1-2 个核心技术点
- 解题误区: 最容易走错的方向
- 破题突破口: 关键一步
- 核心脚本/命令: 最关键的 solve 代码片段

## Details
Expanded explanation, structured by subtopics.

## Connections
- Related: [[page1]], [[page2]]
- GHSA: [[ghsa-*]]
- Source: [title](file:///ctf-kb/...)
```

### Product CVE Pages
For entries documenting vulnerabilities in a specific product (e.g., Nacos, ShowDoc, FastAdmin). These pages should include operational verification steps.

Recommended body structure:

```markdown
# Product Name Vulnerability Summary

## Summary

## 指纹与版本线索
How to identify the product version in target.

## 快速验证顺序 (nuclei 优先 → curl 复核 → PoC)
1. Try nuclei template first
2. Verify with minimal curl request
3. Full exploit if curl confirms

## 最小化复核请求
The shortest curl command that proves the vulnerability exists.

## 常见漏扫原因
Why automated scanners might miss this.

## Connections
```

### CVE Product Index Pages
For the master index (`code-audit/cve-product-index`), include a PoC Quick Reference table so that agent can directly copy-paste the most common verification command:

```markdown
## 关键 PoC 命令速查

| 产品 | 核心验证命令 |
|------|-------------|
| **Product Name** | `curl/short exploit` |
```

## Triggers 设计原则

`triggers` 用于 agent 自动关键词匹配。当题目描述中出现这些词时，相关知识页优先被匹配。

### 覆盖范围

- **中英双语表达** — 如 `[ssti, template-injection, 模板注入]`
- **文件名/路径/HTTP Header 变体** — 如 `[actuator, /metrics, /health]`
- **题面描述常见关键词** — 如 `[grafana, 监控大屏, 运维平台]`
- **产品名 + 版本号** — 如 `[Drupal, drupal, CVE-2018-7600, drupalgeddon]`

### 粒度原则

| 级别 | 举例 | 匹配效果 |
|------|------|---------|
| 过宽（一个词匹配几百页） | `sql` | × 噪音太多 |
| 适中 | `sqli, blind-sqli, sql注入, time-based` | ✅ 精准 |
| 过窄 | `mysql-select-1-injection-2024-specific` | × 也需泛化名 |

- 每个 page 的 triggers 控制在 5-20 个词之间
- 优先包含：英文技术名 / 中文名 / 常见文件名 / 常见 CVE 编号 / 相关工具名

### Skill / Command Reference File Convention

Files migrated from `.trae/skills/` and `.trae/commands/` use special prefixes:

**`skill-*.md`** — Full text copies of `.trae/skills/<category>/*.md`
- Each skill file is a complete technical reference with PoC code, attack patterns, and decision trees.
- Named with `skill-` prefix to avoid conflicts with existing named entity pages.
- Indexed in `index.md` under the "Skill References" section by category.
- Agent can Grep-search these files during CTF solving; they contain detailed technique descriptions.

**`command-*.md`** — Full text copies of `.trae/commands/<category>.md`
- These are comprehensive "解题指南" (solving guides) with full attack surface trees and sub-field coverage tables.
- Each command file is a complete prompt-level guide for an entire CTF category.
- Serves as the ultimate fallback reference when no specific wiki page matches.
- Indexed in `index.md` under the "Command References" section.

**Directory Map:**
| Source (`.trae/`) | Destination (`wiki/`) |
|---|---|
| `skills/ctf-pwn/*.md` | `pwn/skill-*.md` |
| `skills/ctf-crypto/*.md` | `crypto/skill-*.md` |
| `skills/ctf-reverse/*.md` | `reverse/skill-*.md` |
| `skills/ctf-forensics/*.md` | `forensics/skill-*.md` |
| `skills/ctf-misc/*.md` | `misc/skill-*.md` |
| `skills/ctf-ai-ml/*.md` | `ai-ml/skill-*.md` |
| `skills/ctf-malware/*.md` | `malware/skill-*.md` |
| `skills/ctf-osint/*.md` | `osint/skill-*.md` |
| `skills/code-audit/*.md` | `code-audit/skill-*.md` |
| `commands/{web,pwn,crypto,...}.md` | `{web,pwn,crypto,...}/command-reference.md` |
| `ctf-startup-prompt.md` | `rules/startup-prompt.md` |
| `rules/project_rules.md` | `rules/project-rules.md` |
| `ctf-skills-catalog.md` | `rules/skills-catalog.md` |

### New Subdirectory Conventions

**`wiki/malware/`** — Malware analysis track
- Covers: PE/.NET analysis, C2 protocol decoding, script deobfuscation, shellcode analysis, YARA rules.
- CTF category for malicious binary analysis (C2 traffic, obfuscated loaders, ransomware).
- Corresponding skill files: `skill-SKILL.md`, `skill-scripts-and-obfuscation.md`, `skill-c2-and-protocols.md`, `skill-pe-and-dotnet.md`, `skill-TRAE-debugger.md`.

**`wiki/osint/`** — Open Source Intelligence track
- Covers: social media OSINT, DNS recon, geolocation, Google dorking, Wayback Machine, username enumeration.
- CTF category for information gathering from public sources.
- Corresponding skill files: `skill-SKILL.md`, `skill-social-media.md`, `skill-web-and-dns.md`.

**`wiki/rules/`** — Global rules and conventions
- Contains: project-rules (解题全局规则), startup-prompt (启动提示词模板), skills-catalog (118 Skill 完整目录).
- These files define how CTF challenges are approached and which tools/techniques are available.
- Updated when new skills are added to the Trae environment.

## Log Format

Each log entry starts with:

```
## [YYYY-MM-DD HH:MM] <action> | <title>
```

Actions: `ingest`, `query`, `lint`, `create`, `update`, `merge`

This format allows simple unix-style grep to parse the log:
```
grep "^## \[" wiki/log.md | tail -5
```
