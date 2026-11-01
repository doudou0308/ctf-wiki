---
title: Des-CTF 语料索引 — Forensics（37 篇比赛 WP）
tags: [source-index, corpus, des-ctf, wp, forensics]
category: forensics
triggers: ["volatility", "手机取证", "流量分析", "内存取证", "hashdump", "mimikatz", "tshark", "HTTP", "取证与溯源", "MySQL", "pstree", "Volatility2", "rockyou", "vmem", "imageinfo", "malfind", "Win7SP1x64", "应急响应", "DataCon", "Wireshark"]
created: 2026-11-01
updated: 2026-11-01
sources: [https://github.com/Dest1ny-Sec/Des-CTF-Knowledge]
---

# Des-CTF 语料索引 — Forensics

## Summary

外部语料库 `Dest1ny-Sec/Des-CTF-Knowledge` 中 **37 篇 Forensics 比赛 WP**（覆盖 36 场比赛）的结构化索引。
每条记录保留「比赛 → 题目/攻击链 → 关键 payload → 要义」，原始全文未镜像，需细节时按 `contest` 回源 GitHub 的 `CTF大赛WP集合/articles/<文件名>.full.md`。

## Key Points

- 高频考点（按 tags 词频）：volatility、手机取证、流量分析、内存取证、hashdump、mimikatz、tshark、HTTP、取证与溯源、MySQL、pstree、Volatility2、rockyou、vmem、imageinfo
- 高频比赛：2023 全国网络安全行业职业技能大赛、2020 美亚杯 (Meiya Cup) 个人赛、2022 USTC Hackergame (中科大信安赛)、江苏省数据安全竞赛 2023、2023 工业信息安全技能大赛鹏城锦标赛、第四届全国网络空间取证竞赛 (FIC 2024)、智能家电行业赛道、2024 盘古石晋级赛
- tags 来自上游 frontmatter（允许跨赛道噪声，如 Web 页出现 ret2libc），仅作检索线索
- 用法：Grep 本页的比赛名或考点词 → 命中条目 → 需要细节时回源原文；不要在本页上做二次推理

## 按比赛分组

### 2020 美亚杯 (Meiya Cup) 个人赛（1 篇）
- 美亚杯经典诈骗案：笔记本+手机+USB 三件套 61 问取证 → `shy1211@mtzh.gow.tw + 信用卡号 + ZIP SHA-256`；中文 CTF 取证赛最大特色是 60+ 问答题，每问 1-3 分，工具链要全（FTK/X-Ways/Autopsy/火眼）

### Hack A Sat Qualifier 2020 (美国AFRL/NASA太空安全赛)（1 篇）
- 利用NASA开源COSMOS地面站+cFS(核心飞行系统)PEEK_MEM指令从12-212偏移读KitToFlagPkt符号名dump卫星内存。 → `PEEK_MEM + CCSDS_STREAMID 6280 + MEM_TYPE 1 + ADDR_SYMBOL_NAME 'KitToFlagPkt'`；太空信息安全赛的核心是利用COSMOS+CCSDS协议栈;cFS MM(Memory Manager)应用提供PEEK_MEM指令…

### 2022 USTC Hackergame (中科大信安赛)（1 篇）
- USTC Hackergame 多题混战：rclone密码还原+Go AES-CTR+heilang正则 → `flag{get_rclone_password_from_config!_2oi3dz1}`；通用工具 rclone/curl/wireshark/python 都能解

### DataCon2022 物联网安全（1 篇）
- DataCon2022 物联网安全冠军 S7-1200：固件基地址识别 + bindiff 符号恢复 + 整数溢出检测。；固件逆向三件套: 1) 加载基地址识别 (字符串统计+熵分析) 2) bindiff 对比已知版本恢复符号 3) 整数溢出检测 (…

### DataCon2022 网络流量分析（1 篇）
- DataCon2022 流量分析冠军 见世面：挖矿流量检测 (TLS ChangeCIPherSpec) + 智能蜜罐构建 Level1/2。；加密挖矿流量检测靠 TLS ChangeCIPherSpec 包特征 + CICFlowMeter 流量特征 + OneClass…

### 2022 idekCTF（1 篇）
- idekCTF 2022 r3kapig 战队 Forensics 套题，PGP+SSH+流量+内存取证综合。 → `PGP 爆破 + Netascii 修复 + SSH AES-GCM 解密`；真实企业环境中 PGP/SSH 凭据泄露是高频事件，forensic 套题模拟完整流程。

### 2022 内存取证通用指南（1 篇）
- 跳跳糖投稿2022:volatility2自定义内核profile三步走(make dwarfdump+zip+plugins/linux)。 → `module.dwarf + System.map + zip打包 = LinuxUbuntu1804-5_4_0-84x64 profile`；volatility2不支持新内核时必须自建profile:同版本kernel headers+dwarfdump生成module…

### 2022 取证（1 篇）
- Linux 5.15 新版内核的 Volatility2/3 profile 生成方法 (docker + dwarf2json) + bip39 钱包 mnemonic 解密 CTF 题。

### 2022 流量分析专题（1 篇）
- CTF流量分析常见协议技巧：tshark提取+HID字典映射+WIFI/工控/蓝牙协议 → `tshark -r test.pcapng -T fields -e usb.capdata > usbdata.txt;normalKeys+shiftKeys字典;n[2:4]…`；USB HID键盘流量用normalKeys/shiftKeys字典映射；n[0:2]==02判定Shift

### 2022 长安杯（1 篇）
- 第四届长安杯电子数据取证(虚拟币交易诈骗+USTD币+HT币+勒索+Java 5服务后台+nohup日志+Frida Hook java.lang.String.equals+扩展欧几里得逆… → `nohup java -jar admin-api.jar > admin-api.file 2>&1 &/SHOW VARIABLES LIKE ''gen%'';/Frida…`

### 2023 全国网络安全行业职业技能大赛（2 篇）
- 综合电子取证：手机备份+镜像+磁盘+VM+Docker+IM APP 6 检材 30+ 题。；取证题重点是答案格式精准（手机号/身份证号/MD5 大写），数据库时间字段定位是常见考点。
- 取证淘汰赛 C 卷 5 检材：ext4+E01+监控+宝塔面板 综合 20 题。；监控厂商识别靠字符串匹配；宝塔面板默认密码在 config.json；命令历史第 11 条要按序号精确定位。

### 2023 工业信息安全技能大赛鹏城锦标赛（1 篇）
- 工业信息安全综合：IEC104 + CAN + ICMP 隐写 + SMC TEA 变种 + AES + 手撕 PEM RSA。；PEM corrupt 时手撕结构体；SMC TEA 变种要看 delta 和移位；Wiener 攻击是大 e RSA 兜底。

### 2023 Volatility CTF（1 篇）
- Volatility 3 vs 2 内存取证总结 (日语) — Windows 关键命令 pstree+netscan+cmdline+dumpfiles+hashdump+mutantsc…；Volatility 3 完全替代 2;windows.info 识别 OS;pstree+netscan 是黄金组合;linux…

### 2023 数信杯数据安全大赛（1 篇）
- 6道数据安全题涵盖内存取证(Win7SP1x64 volatility全流程)+SQL盲注双写绕(like binary逐位爆)+Download.php SSRF(#截断)+binwalk… → `volatility...hashdump/hahaha123/flag{49059213-a7e2-4e39-b179-953ae9641063}/flag{61909dd6f4…`

### 2023 某部内存取证比赛（1 篇）
- 某部内存取证比赛WP,volatility2全套命令实战:WinXPSP3x86镜像+进程树+进程dump+注册表蜂巢+SAM hashdump+Winlogon最后登录用户+AES解密+m… → `key=Th1s_1s_K3y00000 iv=1234567890123456`；volatility2是Windows内存取证必备工具,常用命令组合:imageinfo→pstree→pslist→memdum…

### 江苏省数据安全竞赛 2023（1 篇）
- 哥斯拉 webshell 流量解密 + AES md5 爆破 5 位 hex key + 内存取证 + 框架版本还原。；哥斯拉 webshell 加密模式 = XOR(16 byte key) + base64 + gzip；AES-CBC key…

### 2023 盘古石杯（1 篇）
- 首届盘古石杯全国电子数据取证大赛总决赛-数据分析:copy *.csv Merged.csv+SQL SUM CASE WHEN+NULLIF+COUNT(DISTINCT)聚合查询银行卡交… → `copy *.csv Merged.csv/SELECT COUNT(DISTINCT 交易对手账卡号) FROM Merged WHERE 交易是否成功 = 1 AND 收付标志…`

### 2023 陇剑杯决赛（1 篇）
- 陇剑杯决赛win场景4问:VMX加密pyvmx-cracker+rockyou爆破somewhere+Xshell 7 NetSarang Computer\7\Xshell\Session… → `pyvmx-cracker.py -v VMX.vmx -d rockyou.txt/encryption.keySafe = vmware:key/list/(pair/(phr…`

### 2024 盘古石晋级赛（1 篇）
- 盘古石晋级赛：iOS 毛雪柳记账 iCost+ Mattermost + Android 伏季雅威尼斯诈骗 APP + 义言 Mattermost 多检材协同；雷电 APP 智能分析一键导出… → `iCost APP default.realm + realm studio 2 月 type=1 收入 11957`；iOS 取证从 iTunes 备份 + 雷电 APP 分析 + realm studio 看 realm 数据库是 2024 主流…

### 2024 第二届龙信杯电子数据取证竞赛（1 篇）
- 龙信杯计算机取证：恶意程序解密 t.wnry（WannaCry 变种勒索）后 dll 的 MD5 值；属于应急响应+样本分析综合取证。 → `解 t.wnry → MD5(dll) 全大写`；龙信杯是国内电子数据取证头部赛事，WannaCry 变种（t.wnry 加密扩展名）是常见考点；MD5 答案严格全大写。

### 2024 CyberDefenders（1 篇）
- CyberDefenders TeamSpy 内存取证 WP，Win7SP1x64 镜像 + svchost/explorer 进程注入 + mimikatz 凭据提取。 → `Win7SP1x64 profile DTB=0x187000 KDBG=0xf800029ed070`；volatility 2 imageinfo 优先 Win7SP1x64 候选；malfind 找 PAGE_EXECUTE_RE…

### DownUnderCTF 2024（1 篇）
- DownUnderCTF 2024 取证4题：流量+域控WMI+Bad Policies+emuc2 → `nmap_7.25 # 类似格式`；取证题常用流量分析+SMB事件+注册表+内存镜像组合

### UCTC CTF 2024 高校新生网安赛（1 篇）
- UCTC 高校新生赛 5 大题：MISC zip 反转 + PWN 栈溢出 + 格式化串 + py 反编译 + Java smali → `pyc magic 0x0A0D0D55 (Python 3.8) 修复后 md5 解密`；新生赛涵盖 reverse/pwn/web/misc；md5 4 段拼接 + base64 码表替换是入门 reverse

### 2024 vol2-share（1 篇）
- Volatility 2 取证入门，VMware 文件格式 + 全部插件功能列表。 → `volatility 2.x 插件列表`；内存取证 = 解析 .vmem + 跑 volatility 插件。

### 2024 全国职业技能大赛（1 篇）
- 全国职业技能大赛 Linux 应急响应技术，9 大模块：进程/端口/历史/恶意文件/恶意程序/账户/cron/系统日志/.ssh。 → `lsof -p /etc/passwd uid=0 /var/log/secure /etc/crontab /var/spool/cron`；应急响应 9 模块：lsof 进程 + 端口 + history + find + strings + /etc/passwd +…

### 2024 强网杯 (Qiangwang)（1 篇）
- NTLMSSP 离线爆破 + SMB 会话密钥解密 + RDP 键盘还原 → `tom::.:c1dec53240124487:ca32f9b5b48c04ccfa96f35213d63d75:0101...2900`；pcapng 含 NTLMv2 时可直接 hashcat 爆破密码；session key 解密后才有 SMB 流量；RDP fa…

### 2024 春秋杯（1 篇）
- 2024 春秋杯夏季赛初探勒索病毒，基于 Black Basta Buster 解密工具的签到题。 → `black-basta-buster decryptblocks.py SRL_IGNORE_MAGIC=1 已知 key.block`；Black Basta 用 ChaCha 密钥流加密 64 字节块 + XOR；解密需已知 64 字节明文 + 块位置；SRLab…

### 2024 智能家电行业赛道（1 篇）
- BLE+RSA gcdext+路由器注入+shiro 反序列化+Modbus 图拼接+libc XOR。 → `g, s, t = gmpy2.gcdext(3, 17) ; m = pow(c1, s, n) * pow(c2, t, n) % n ; from iroot(m, g)[0…`；RSA 同模攻击 (e1, e2 互质) 必须用 gcdext 找逆元；工控流量 modbus 35152 是 PNG 起始 ma…

### 2024 暗魂CTF（1 篇）
- 暗魂CTF工控协议分析三题：modbus+s7comm异常过滤+omron字节交换解码 → `wireshark filter modbus;s7comm.header.errcod !== 0x00;omron.command==0x0102;swap_bytes(每两字…`；工控CTF核心Wireshark过滤器：modbus/s7comm/omron；OMRON密文常需每两字节swap

### 第四届全国网络空间取证竞赛 (FIC 2024)（1 篇）
- 杀猪盘综合取证：手机+服务器+PC 三检材联动 → `ruoyi-admin.jar BOOT-INF/classes/application-druid.yml → MYSQL_ROOT_PASSWORD=my-secret-pw`；综合取证赛三大类：微信/QQ 聊天记录；ESXi/Docker 容器部署链路；加密卷密码与镜像；iStoreOS 软路由+订阅 t…

### 2025 某集团 ctf 决赛（1 篇）
- 2025 某集团决赛 RATTRACER：ST.p12 私钥解 TLS + AsyncRAT 通信解密 gzip+MsgPack + RDP JPEG 截图提取 flag。 → `1f 8b 08 gzip 头 + MsgPack 0xc4 0xc6 标记`；AsyncRAT 通信 = TLS 1.x 外层 + gzip+MsgPack 内层 + 0xc4/0xc6 binary 标记；…

### 2025 高校网络安全管理运维赛（1 篇）
- 2025 高校网络安全管理运维赛电子取证分析师决赛：签到 GIF + log awk + PACP pcap + Keepass + VeraCrypt + 勒索恢复 + RAID 对比。 → `Keepass ironbox.safe → PqR$34%sTuVwX`；高校运维赛取证赛道覆盖签到/log/pcap/账户/Keepass/VeraCrypt/勒索/RAID 全栈；Keepass 主密…

### 2025 MineCraftCTF + Solar 月赛（1 篇）
- MineCraftCTF + Solar 月赛内存取证：搜日志找攻击者 IP (10.10.0.1) + netscan 找外联 IP + 端口。 → `/`；/

### 2025 UniCTF (YunSee团队)（1 篇）
- YunSee 团队招新流量分析题，工厂应急响应 7 个 flag + BlueBreath XOR 加密 webshell 完整解密，HTTP+Modbus+OPC UA+SNMP 协议全接… → `modbus.func_code==5 && modbus.data==ff:00 / bkcrack -C hint.zip -c hint.png -p png.header…`

### 2025 西湖论剑（1 篇）
- 西湖论剑决赛 misc — R-Studio 恢复 + 文件名拼 zip + 冰蝎3/4 流量 AES 解密 + 拼图 + RC4 像素级解密 + 盲水印。；目录名拼 zip 需按 mtime 排序再 unhexlify；冰蝎4 → 冰蝎3 链路是先解冰蝎4 得 key 再解冰蝎3 得…

### 2026 软件系统安全赛（1 篇）
- 软件系统安全赛traffic_hunt流量分析,冰蝎马AES-ECB+MD5(p)密钥推导+分片文件上传(blockIndex乱序重组)+Python pyc C2植入物+AES-GCM后渗… → `dart{d9850b27-85cb-4777-85e0-df0b78fdb722}`；冰蝎马用MD5(p)前16字节作AES-ECB密钥是标准实现;分片文件上传blockIndex乱序是流量侧的检测规避;Python…

## Connections

- 上层索引：[[ctf/source-corpus--des-ctf-knowledge]]
- Source: [https://github.com/Dest1ny-Sec/Des-CTF-Knowledge](https://github.com/Dest1ny-Sec/Des-CTF-Knowledge)

## Open Questions

- 本页为索引层，题目细节请回源；若某场比赛多题值得展开，再单独建 `ctf/<比赛>.md` 实体页。
