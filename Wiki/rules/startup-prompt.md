# CTF 启动提示词

每次发题时在最前面粘贴这段话。`[额外Skill]` 部分按需填写，不需要则留空。

---

```
【强制启动流程】

收到此题的第一次回复，必须并行执行以下操作（同一轮 tool call）：

--- Skill 加载 ---
必选: ctf-reverse（根据题型替换为 ctf-web / ctf-pwn / ctf-crypto / ctf-forensics / ctf-misc / ctf-malware）
额外: <如需要则填写，如 code-audit / analyzing-linux-elf-malware / performing-fuzzing-with-aflplusplus / deobfuscating-powershell-obfuscated-malware，不需要则留空>

--- 侦察 ---
5. strings + grep flag
6. file
7. binwalk -e / exiftool

然后立即阅读所有加载的 Skill 的 SKILL.md + 各 1 个最相关的 reference 文件。
读完 Skill 内容后再深入分析。

知识库（Obsidian Wiki）不在启动阶段查，仅在以下情况触发：
- 同一方向 2 轮卡壳无进展
- 完全陌生的题型/框架/文件格式
- 解题方向摇摆不定
触发时优先搜 triggers: Grep -i "triggers:.*关键词" "C:\Users\ZZH\Documents\Obsidian Vault\Wiki\Wiki\"
triggers 无命中全文搜: Grep -ri "关键词" "C:\Users\ZZH\Documents\Obsidian Vault\Wiki\Wiki\"

题目: <粘贴题目描述或附件路径>
```

---

## 使用方式

1. 复制上面的模板
2. 修改 `必选` 为对应题型的 ctf-* skill
3. 在 `额外` 行填写需要加载的非 ctf skill（可填多个，逗号分隔），不需要则删掉该行或写 `无`
4. 把 `<粘贴题目描述或附件路径>` 替换为实际题目

## 示例

```
【强制启动流程】
必选: ctf-reverse
额外: code-audit

题目: baby_re.exe，长城杯 2022 逆向题
```

```
【强制启动流程】
必选: ctf-web
额外: 无

题目: http://example.com:8080/login
```

```
【强制启动流程】
必选: ctf-pwn
额外: analyzing-linux-elf-malware, performing-fuzzing-with-aflplusplus

题目: chall.elf，远程 nc example.com 1337
```
