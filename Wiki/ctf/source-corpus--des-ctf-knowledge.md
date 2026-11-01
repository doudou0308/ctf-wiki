---
title: 外部语料索引 — Des-CTF-Knowledge（比赛 WP 语料库）
tags: [source-index, corpus, des-ctf, wp, 语料库, 来源标注]
category: ctf
triggers: [Des-CTF-Knowledge, Dest1ny, WP 语料, 比赛 WP 库, 外部知识库, 语料索引, 比赛来源查询, 2022 羊城杯, 强网杯, 春秋杯, 网鼎杯, 西湖论剑, 长城杯, 阿里云CTF, 腾讯游戏安全, 数据安全竞赛]
created: 2026-11-01
updated: 2026-11-01
sources: [https://github.com/Dest1ny-Sec/Des-CTF-Knowledge]
---

# 外部语料索引 — Des-CTF-Knowledge

## Summary

本页记录外部语料库 `Dest1ny-Sec/Des-CTF-Knowledge`（438 star）的**结构与可检索入口**。该库提供两大块本 wiki 原先缺失的内容：① 12 篇 Web 漏洞体系化长文（约 24,000 行）；② **1005 篇比赛 WP（708 场比赛）**，每篇已被预处理成带 `contest / category / tags / attack_chain / key_payload / lesson` 的结构化 frontmatter。

吸收策略：**不镜像全文**（体量会撑爆 wiki 且大量与现有页重合），只把「技术要点 + PoC + 来源比赛」提炼进 `wiki/web/`，原始细节按需回源。

## Key Points

- 该库的 WP 每篇 frontmatter 自带 `attack_chain`（解题链）、`key_payload`（关键 payload）、`lesson`（经验）——**这是最值钱的检索字段**
- **1027 篇已全部索引化**：按赛道 + 年份段拆成 14 个索引页（见下表），每条压缩为「比赛 → 攻击链 → 关键 payload → 要义」，单页 13–43 KB，便于 Grep 命中
- 本 wiki 新增 4 页基于它补缺：[[web/php-lfi-getshell-advanced]]、[[web/rce-bypass-techniques]]、[[web/file-upload-bypass]]、[[web/ssrf-protocol-weaponization]]
- 引用规则：本 wiki 提到某技术时，若来自该库，需标注**原始比赛**（如「2022 羊城杯」），而非仅标注仓库名
- 回源方式：GitHub 上按目录 `CTF大赛WP集合/articles/` 找 `<比赛>-*.full.md`

## 语料构成（实测）

| 目录 | 内容 | 规模 |
|------|------|------|
| `*.md`（根目录 12 篇） | Web 漏洞体系化长文 | 约 24,000 行 |
| `CTF大赛WP集合/articles/` | 比赛 WP（结构化 frontmatter） | **1027 篇 / 723 场** |
| `CTF常用脚本及工具/` | 场景化 exp 脚本 | 50+ 目录 |
| `WP汇总/` | 靶场类 WP | — |
| `工具使用/` | 工具手册 | — |
| `AI-SEARCH-INDEX.md` | 作者自建的检索索引 | 1 篇 |

WP 赛道分布（`category` 字段统计）：web 315 / misc 219 / pwn 184 / reverse 152 / crypto 120 / forensic 37。

### 已产出的赛道索引页（1027 篇全覆盖）

| 赛道 | 索引页 | 篇数 |
|------|--------|------|
| Web | [[web/source-index--des-ctf-web-2017-2022]] | 98 |
| Web | [[web/source-index--des-ctf-web-2023-2024]] | 150 |
| Web | [[web/source-index--des-ctf-web-2025+]] | 67 |
| PWN | [[pwn/source-index--des-ctf-pwn-2016-2022]] | 56 |
| PWN | [[pwn/source-index--des-ctf-pwn-2023-2024]] | 94 |
| PWN | [[pwn/source-index--des-ctf-pwn-2025+]] | 34 |
| Misc | [[misc/source-index--des-ctf-misc-2014-2022]] | 78 |
| Misc | [[misc/source-index--des-ctf-misc-2023-2024]] | 100 |
| Misc | [[misc/source-index--des-ctf-misc-2025+]] | 41 |
| Reverse | [[reverse/source-index--des-ctf-reverse-2015-2022]] | 36 |
| Reverse | [[reverse/source-index--des-ctf-reverse-2023-2024]] | 76 |
| Reverse | [[reverse/source-index--des-ctf-reverse-2025+]] | 40 |
| Crypto | [[crypto/source-index--des-ctf-crypto]] | 120 |
| Forensics | [[forensics/source-index--des-ctf-forensics]] | 37 |

### 12 篇长文的原始出处

| 文件 | 行数 | 原始出处 |
|------|------|----------|
| `PHP反序列化漏洞总结.md` | 6971 | 多篇社区文章汇编 |
| `命令执行.md` | 3475 | 先知社区 / 奇安信攻防社区（`shs3.b.qianxin.com` 图床） |
| `文件包含.md` | 2630 | 0x002.com《LFI》系列 + sakurahack-y《php 文件包含总结》（2022-11） |
| `SQL.md` | 2623 | 社区汇编 |
| `文件上传漏洞.md` | 2127 | 1997sty 博客 + upload-labs |
| `SSRF漏洞.md` | 1894 | hackerqwq《浅析 SSRF 漏洞》（2020-11）+ secpulse |
| `php代码审计.md` | 1657 | 社区汇编 |
| `图片隐写.md` | 910 | 社区汇编 |
| `压缩包总结.md` | 657 | 社区汇编 |
| `JWT.md` | 437 | 社区汇编 |
| `SSTI.md` | 394 | 社区汇编 |
| `音频隐写.md` | 328 | 社区汇编 |

### 脚本库亮点（`CTF常用脚本及工具/`）

- `RSA综合脚本利用/` — 按「已知量」分 20+ 子目录（已知 e,n,c / Wiener / 共模 / dp 泄露 / 中国剩余定理 / 公钥提取），**按题面条件直接取脚本**
- `CRC32校验爆破/` — 1~5 字节 CRC 爆破
- `Base/` — base64 隐写、连续解码、字符替换表、异或
- `图片爆破宽高/`、`频域盲水印/`、`TTL隐写/`、`usb流量/`、`字节转二维码/`
- `一些比赛的脚本/祥云杯-xixixi/`

## Details — 技术 → 比赛 反查表

已核验（本 wiki 页面引用的来源）：

| 技术 | 比赛 / 场次 | 关键 payload |
|------|-------------|--------------|
| pearcmd.php + config-create | **2022 羊城杯** rce_me | `?+config-create+/&file=/usr/local/lib/php/pearcmd.php&/<?=phpinfo()?>+/tmp/hello.php` |
| pearcmd.php 变体 | **2023 ACTF**、**第八届强网杯** | 同上思路，子命令按环境换 |
| SUID `date -f /flag` 提权 | **2022 羊城杯** | `find / -perm -u=s -type f` → `/usr/bin/date -f /flag` |
| `.htaccess` 上传 getshell | **BuildCTF 2024** Babyupload、**2024 第四届长城杯初赛** | `AddType application/x-httpd-php .xxx` + `tac /flag` |
| `getallheaders()` 无参数 RCE | **2023 NKCTF** Webpagetest、**2023 第八届上海市大学生网络安全大赛** easy_log | `username[<?php eval(end(getallheaders()));?>][]=1` |
| `LD_PRELOAD` 劫持 | **2022 USTC Hackergame** | `unsetenv("LD_PRELOAD")` + setuid 提权 |
| gopher + Redis `SERIALIZE_PHP` 反序列化 | **2022 春秋杯春季赛** easy-swoole | gopher 写序列化数据 → `UploadFile::__destruct` → Prophecy/PHPUnit Mock 链 RCE |
| gopher → XDebug / rpdb / JDWP 三语言 | **2021 春秋杯秋季赛** mimic-ssrf | XDebug `13000`、rpdb `4444`、JDWP `20211` |
| gopher → MySQL | **ISITDTU 2018** Friss | Gopherus 生成 |
| Swoole `system()` 无回显 → 抛异常回显 | **2022 春秋杯春季赛** | `throw new Exception(结果)` |
| flask-unsign + 弱 secret | **2024 长城杯初赛** | `flask-unsign --sign --cookie ... --secret a123456` |
| Java `ı`/`ſ` case-folding 绕过 | **BuildCTF 2024** | `Character.toUpperCase()` Unicode 折叠 |
| `ffifdyop` MD5 注入 | **BuildCTF 2024** | `'or'` 绕过 |

## Connections

- 派生页面：[[web/php-lfi-getshell-advanced]]、[[web/rce-bypass-techniques]]、[[web/file-upload-bypass]]、[[web/ssrf-protocol-weaponization]]
- 已有相关页：[[web/php-file-inclusion-filter]]、[[web/ssrf-gopher-techniques]]
- Source: [Dest1ny-Sec/Des-CTF-Knowledge](https://github.com/Dest1ny-Sec/Des-CTF-Knowledge)

## Open Questions

- 索引层已覆盖全部 1027 篇，但每条仅有「一句攻击链 + 一条 payload」。对**多题且高频**的比赛（强网杯 / 春秋杯 / 网鼎杯 / 西湖论剑 / 长城杯），仍值得单独建 `ctf/<比赛>.md` 实体页展开细节。
- 该库 WP 的 `contest` 字段有少量历史赛事与现名不一致（如「第八届强网杯」vs「强网杯 S8」），引用时需统一；索引页标题以原始 `contest` 为准。
- 上游 `tags` 存在跨赛道噪声（如 Web 页出现 `ret2libc`），高频考点统计仅作检索线索，不代表真实赛道分布。
