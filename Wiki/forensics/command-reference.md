---
name: forensics
description: CTF 取证解题全能助手 — 覆盖磁盘取证（Volatility/磁盘挂载/文件雕刻/VM取证/RAID/APFS/ZFS）、内存取证、Windows取证（注册表/事件日志/回收站/NTFS/USN/RDP/WMI）、Linux取证（日志/Docker/Browser/Git/KeePass）、网络取证（tcpdump/TLS解密/PCAP分析/SMB3/5G/隐蔽信道）、隐写术（图像/音频/视频/PDF/SVG/APNG/FFT/DTMF/SSTV/Arnold猫映射）、硬件信号解码（VGA/HDMI/DisplayPort/侧信道/UART/I2C）、外设捕获（USB HID鼠标/键盘绘图/MIDI/蓝牙）等15个子领域。
---

# CTF Forensics — 取证解题全面指南

你是 CTF 取证类题目的顶级解题专家。你的知识库覆盖以下所有子领域，按需深入：

## 一、前置准备

```bash
pip install volatility3 Pillow numpy matplotlib

# Linux
apt install binwalk foremost libimage-exiftool-perl tshark sleuthkit ffmpeg steghide testdisk john pcapfix

# macOS
brew install binwalk exiftool wireshark sleuthkit ffmpeg testdisk john-jumbo

# Ruby
gem install zsteg
```

---

## 二、解题总流程

### 第0步：快速分类
- 给了 `.dd` / `.img` / `.dmp` / `.vmdk` / `.ova` → **磁盘/内存取证**
- 给了 `.pcap` / `.pcapng` → **网络取证**
- 给了图片/音频/视频/PDF → 先 `exiftool` + `binwalk` + `strings` → **隐写分析**
- 给了 `.evtx` / 注册表 hive → **Windows 取证**
- 给了日志文件 / Docker tar / Git repo → **Linux 取证**
- 给了 `.bin` 硬件信号文件 / `.sal` → **硬件信号解码**
- 给了 USB 流量 / 键盘鼠标数据 → **外设捕获**
- 给了 `.gcode` / `.bgcode` → **3D 打印取证**

### 第1步：文件分析

```bash
file suspicious_file
exiftool suspicious_file
binwalk suspicious_file
strings -n 8 suspicious_file
hexdump -C suspicious_file | head
```

---

## 三、磁盘与内存取证

详见 [disk-and-memory.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-forensics\disk-and-memory.md)、[disk-advanced.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-forensics\disk-advanced.md)、[disk-recovery.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-forensics\disk-recovery.md)

### Volatility 3 内存取证

```bash
vol3 -f memory.dmp windows.info        # 系统信息
vol3 -f memory.dmp windows.pslist      # 进程列表
vol3 -f memory.dmp windows.pstree      # 进程树
vol3 -f memory.dmp windows.cmdline     # 命令行参数
vol3 -f memory.dmp windows.netscan     # 网络连接
vol3 -f memory.dmp windows.filescan    # 文件对象
vol3 -f memory.dmp windows.dumpfiles --physaddr <addr>  # 提取文件
vol3 -f memory.dmp windows.mftscan | grep flag  # MFT扫描
vol3 -f memory.dmp windows.clipboard   # 剪贴板内容
vol3 -f memory.dmp windows.registry.hivelist  # 注册表hive
```

### 磁盘镜像分析

```bash
sudo mount -o loop,ro image.dd /mnt/evidence  # 只读挂载
fls -r image.dd              # 递归列出文件
icat image.dd <inode>        # 按inode提取文件
photorec image.dd            # 雕刻已删除文件
foremost -i image.dd         # 文件头雕刻
```

### VM 取证 (OVA/VMDK)

```bash
tar -xvf machine.ova                   # OVA 是 tar 包
7z l disk.vmdk | head -100             # 列出VMDK内容
7z x disk.vmdk -oextracted "Windows/System32/config/SAM" -r  # 提取SAM
```

关键提取目标：`SAM`（密码哈希）、`SYSTEM`（启动密钥）、`SOFTWARE`（已安装软件）、`NTUSER.DAT`（用户注册表）

### VMware 快照取证

```bash
vmss2core -W snapshot.vmss snapshot.vmem  # 转为 memory.dmp
```

### 内存字符串快速搜索

```bash
strings -a -n 6 memdump.bin | grep -E "FLAG|SSH_CLIENT|SESSION_KEY"
```

### 高级磁盘技术

- **APFS 快照恢复**：macOS 文件系统快照可以 mount 查看历史版本
- **RAID 5 XOR 恢复**：已知 N-1 个盘和 parity，XOR 恢复缺失盘
- **ZFS 取证**：检查 dataset snapshot、zpool history
- **LUKS 主密钥恢复**：从内存 dump 中提取 LUKS header
- **BTRFS subvolume/snapshot 恢复**：检查隐藏的子卷
- **FAT16 已删除文件恢复**：`fls -r` + `icat`
- **ext2 孤立 inode 恢复**：`fsck -n` 检查 + 手动提取
- **损坏 ZIP 头部修复**：重建 local file header

---

## 四、Windows 取证

详见 [windows.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-forensics\windows.md)

### 事件日志 (.evtx)

```python
import Evtx.Evtx as evtx
with evtx.Evtx("Security.evtx") as log:
    for record in log.records():
        print(record.xml())
```

**关键 Event ID：**

| Event ID | 含义 |
|----------|------|
| 1102 | 审计日志被清除 |
| 4720 | 用户账户创建 |
| 4724 | 尝试重置密码 |
| 4726 | 用户账户删除 |
| 4781 | 账户被重命名 |
| 4648 | 显式凭据登录 |
| 4624/4625 | 登录成功/失败 |

**RDP 会话 (TerminalServices-LocalSessionManager)：**
- 21 = 登录成功, 24 = 断开, 1149 = RDP认证成功（含源IP）

### 注册表分析

```bash
rip.pl -r NTUSER.DAT -p all     # RegRipper 自动分析
```

关键 Hive：`NTUSER.DAT`（用户设置）、`SAM`（账户）、`SYSTEM`（系统配置）、`SOFTWARE`（软件）

### SAM 密码哈希提取

```bash
python -c "from impacket.examples.secretsdump import *; SAMHashes('SAM', LocalOperations('SYSTEM').getBootKey()).dump()"
```

### 回收站取证

- `$R` + 随机字符串 = 文件内容
- `$I` + 随机字符串 = 元数据（原文件名、删除时间）

### NTFS 高级取证

- **Alternate Data Streams (ADS)**：`fls -r image.dd | grep ":"` 检测隐藏流，`icat` 提取
- **USN Journal ($J)**：文件操作时间线。日志被清除后仍可追踪文件级活动
- **MFT 分析**：`vol3 -f memory.dmp windows.mftscan`

### 日志被清除后的替代取证源

1. **USN Journal** — 文件操作时间线（MFT引用号、时间戳、原因码）
2. **SAM 注册表** — 键的 `last_modified` 时间戳反推账户创建
3. **PowerShell 历史** — `ConsoleHost_history.txt`（USN DATA_EXTEND = 命令时间）
4. **Defender MPLog** — 独立日志，含威胁检测和 ASR 事件
5. **Prefetch** — 程序执行证据
6. **用户 Profile 创建时间** — USN Journal 中首次登录时间
7. **WMI 持久化** — `OBJECTS.DATA` 中的 FilterToConsumerBindings

### 反取证检测清单

- `cipher.exe /w` 擦除 → 检查 `C:\EFSTMPWP` 文件夹存在
- 日志清除 → `EventID 1102` 或 USN Journal 对比
- 时间戳篡改 → 跨源交叉验证（MFT vs USN vs 事件日志）

---

## 五、Linux 与应用程序取证

详见 [linux-forensics.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-forensics\linux-forensics.md)

### 日志分析

```bash
grep -iE "(flag|part|piece|fragment)" server.log
grep "FLAGPART" server.log | sed 's/.*FLAGPART: //' | uniq | tr -d '\n'  # 重构分片
sort logfile.log | uniq -c | sort -rn | head  # 异常检测
```

### 攻击链分析

```bash
grep -A2 "session opened" /var/log/auth.log  # SSH 会话
cat /home/*/.bash_history                     # 命令历史
find /usr/bin -newer /var/log/auth.log        # 新下载文件
```

### Docker 镜像取证

Docker 镜像 config JSON (`blobs/sha256/<hash>`) 永久保留**所有** `RUN` 命令历史，即使后续层已清理：

```bash
tar xf app.tar
python3 -m json.tool blobs/sha256/<config_hash> | grep -A2 "created_by"
```

### 浏览器取证

| 浏览器 | 关键文件 |
|--------|----------|
| Chrome/Chromium | `History` (SQLite)、`Cookies`、`Login Data`、`Local Storage/` |
| Firefox | `places.sqlite`（历史）、`cookies.sqlite`、`logins.json`、`sessionstore-backups/` |

### Git 取证

```bash
git reflog                    # 所有 HEAD 变更历史
git fsck --lost-found         # 恢复悬空对象
git log --all --oneline       # 所有分支提交
git show <squashed-commit-hash>  # 查看被 squash 掉的提交
```

### KeePass 破解

```bash
keepass2john database.kdbx > hash.txt
hashcat -m 13400 hash.txt wordlist.txt
```

### Python 进程内源码恢复

```python
import pyrasite
pyrasite.attach(pid)
# 或从 /proc/pid/fd/ 提取 .pyc 文件后用 uncompyle6 反编译
```

---

## 六、网络取证

详见 [network.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-forensics\network.md)、[network-advanced.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-forensics\network-advanced.md)

### tcpdump 快速参考

```bash
tcpdump -r capture.pcap -A                        # ASCII查看
tcpdump -r capture.pcap -X                        # Hex+ASCII
tcpdump -r capture.pcap -A | grep -i flag         # 快速flag搜索
tcpdump -r capture.pcap -w filtered.pcap 'host 192.168.1.100 and port 80'
```

### TLS/SSL 解密

**方法1 — SSLKEYLOGFILE：**
```bash
export SSLKEYLOGFILE=/tmp/sslkeys.log
# Wireshark: Edit → Preferences → Protocols → TLS → (Pre)-Master-Secret log filename
```

**方法2 — RSA 私钥：** 仅 RSA key exchange 有效。有前向保密（ECDHE）的会话不行。

### PCAP 文件提取

```bash
# 导出 HTTP 对象
tshark -r capture.pcap --export-objects "http,/tmp/out"

# 导出所有 TCP 流
tshark -r capture.pcap -T fields -e tcp.payload | xxd -r -p > raw.bin

# USB HID 数据提取
tshark -r capture.pcap -Y "usb.transfer_type==1" -T fields -e usb.capdata
```

### 高级网络技术

| 技术 | 说明 |
|------|------|
| **包间隔时间编码** | 相同包有两种间隔（如 10ms/100ms）编码二进制 |
| **TCP Flag 隐蔽信道** | URG/PSH/FIN 等 flag 组合编码数据 |
| **DNS 尾部字节编码** | DNS 查询最后一个字节编码 ASCII |
| **ICMP 载荷长度编码** | Echo 请求的载荷大小编码数据 |
| **Brotli 解压炸弹** | 极大的解压比 → 含隐藏数据，解压后分析 seam 处 |
| **SMB RID 回收 (LSARPC)** | 已删除账户的 SID 被回收，枚举 RID 范围 |
| **Timeroasting (MS-SNTP)** | NTP 响应中的 hash 可离线爆破 |
| **dnscat2 重组** | 多层 DNS 隧道，逐个流重建 |
| **RADIUS 密钥破解** | `radius2john` + hashcat |
| **RC4 流识别** | shellcode 流量的 RC4 keystream 特征 |

### 损坏 PCAP 修复

```bash
pcapfix -o repaired.pcap corrupted.pcap
```

### WPA/WEP WiFi 解密

```bash
aircrack-ng -w wordlist.txt -b <BSSID> capture.pcap  # WPA
airdecap-ng -w <key> capture.pcap                     # WEP
```

---

## 七、隐写术

详见 [steganography.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-forensics\steganography.md)、[stego-image.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-forensics\stego-image.md)、[stego-advanced.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-forensics\stego-advanced.md)、[stego-advanced-2.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-forensics\stego-advanced-2.md)

### 快速工具

```bash
steghide extract -sf image.jpg     # JPG 隐写
stegseek image.jpg rockyou.txt    # steghide 快速爆破
zsteg image.png                   # PNG/BMP 分析
stegsolve                          # 位平面可视化分析
binwalk -e image.png              # 检查嵌入文件
```

### 图片隐写决策树

```
PNG？→ zsteg 全扫 → 检查 chunk 结构 → 检查 IHDR CRC/高度 → 检查 palette → LSB
JPG？→ steghide → exiftool → DQT表 → F5检测 → slack space → 检查 FFD9 后数据
GIF？→ 帧差分 → palette → 帧间 Morse
BMP？→ 位平面 → LSB → 边缘像素
```

### 常见隐写模式速查

| 模式 | 检测方法 |
|------|----------|
| **LSB（最低有效位）** | `zsteg -a image.png`，逐位平面提取 |
| **PNG 高度/CRC 修改** | 修改 IHDR height 字段 → 暴力 CRC 至匹配 → 显示隐藏行 |
| **文件叠加（在 IEND/FFD9/%%EOF 后）** | `binwalk` + 手动 hexdump 检查 |
| **PNG Chunk 乱序** | 确保 IDAT 按序排列 |
| **JPEG DQT LSB** | 未使用的量化表(ID 2,3)的每个值 bit 0 |
| **F5 JPEG DCT** | 检测 DCT 系数直方图异常 |
| **二进制边框** | 1px 边框黑/白像素顺时针读 |
| **像素坐标链** | R=数据、G/B=下个像素坐标的链表遍历 |
| **APNG 帧提取** | `apngdis` 提取 `fdAT`/`fcTL` 帧 |
| **GIF 帧差分 Morse** | 帧间差异 = 亮/暗 → 点/划 |
| **Arnold 猫映射** | 周期性 → 暴力迭代次数恢复原图 |
| **SSTV** | `qsstv` 解码；可能是红鲱鱼，同时检查 LSB |
| **多轨音频差分** | `sox -m a0.wav "|sox a1.wav -p vol -1" diff.wav` |
| **EXIF zlib + LSB** | EXIF 字段压缩后的 bit 编码在像素中 |
| **PDF xref 隐写** | 交叉引用表的生成编号编码隐藏数据 |

### PDF 隐写

```bash
exiftool document.pdf          # 元数据
pdftotext document.pdf -       # 提取文本
strings document.pdf | grep -i flag
binwalk document.pdf           # 嵌入文件
```

六层 PDF 隐写技术：
1. **不可见文本分隔符**：下划线渲染为不可见线段
2. **URI 注解**：Link 类型含有 `\{` 和 `\}` 转义的 flag
3. **Wiener 反卷积**：模糊图像恢复
4. **矢量矩形 QR 码**：矢量图形拼接成 QR
5. **压缩对象流**：`mutool clean -d` 解压
6. **文档元数据字段**

### 像素 ECB 去重攻击

用 ECB 模式"加密"的 flag 图片，每种颜色的像素产生相同的"密文"像素。收集所有唯一密文块 → 每种颜色对应一个块 → 逐块替换恢复原图。

---

## 八、音频与视频取证

### 音频分析

```bash
sox audio.wav -n spectrogram      # 频谱图
ffmpeg -i audio -lavfi showspectrumpic  # 高分辨率频谱图
```

**DTMF 解码：** `multimon-ng -a DTMF audio.wav`
**SSTV 解码：** `qsstv`（GUI）/ 自定义 FM 解调
**DeepSound：** `deepsound-cli` 提取 + `john` 爆力密码

### 视频取证

```bash
# 帧累积（时间平均）
ffmpeg -i video.mp4 -vf "tblend=average,fps=1" frames/frame_%04d.png

# 帧差分 XOR
python3 -c "
import cv2; import numpy as np
cap = cv2.VideoCapture('video.avi')
ret, prev = cap.read()
while True:
    ret, curr = cap.read()
    if not ret: break
    diff = cv2.bitwise_xor(prev, curr)
    cv2.imwrite(f'diff_{int(cap.get(cv2.CAP_PROP_POS_FRAMES))}.png', diff)
    prev = curr
"
```

---

## 九、硬件信号解码

详见 [signals-and-hardware.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-forensics\signals-and-hardware.md)

### 快速分类

| 信号类型 | 特征 |
|----------|------|
| VGA | 5字节/像素 (R,G,B,HSync,VSync)，6-bit 颜色 |
| HDMI TMDS | 3通道 × 10-bit TMDS 符号 |
| DisplayPort | 8b/10b 编码 + LFSR 扰码 |
| Saleae Logic 2 (.sal) | 逻辑分析仪抓取，UART/I2C/SPI 协议解码 |
| Flipper Zero (.sub) | 射频信号录制 |
| 侧信道功率分析 (DPA) | 功耗波形，对齐后用汉明重量模型猜密钥 |

### VGA 帧解析

```python
import numpy as np
from PIL import Image

data = open('vga.bin', 'rb').read()
TOTAL_W, TOTAL_H, BYTES_PER_SAMPLE = 800, 525, 5
samples = np.frombuffer(data, dtype=np.uint8).reshape(TOTAL_H, TOTAL_W, BYTES_PER_SAMPLE)
active = samples[:480, :640, :3]  # 裁剪到活动区域
img_arr = (active.astype(np.uint16) * 4).clip(0, 255).astype(np.uint8)  # 6-bit→8-bit
Image.fromarray(img_arr).save('vga_output.png')
```

### Side-Channel Power Analysis (DPA)

```python
import numpy as np
# 对齐所有 trace，按猜测密钥位分组
# 计算两组均值的差分 → 最大差异位 = 正确密钥位
traces = np.load('traces.npy')  # shape: (N_traces, N_samples)
for key_guess in range(256):
    group0 = traces[hamming_weight(pt ^ key_guess) < 4]
    group1 = traces[hamming_weight(pt ^ key_guess) >= 4]
    diff = np.abs(group0.mean(axis=0) - group1.mean(axis=0))
    score = diff.max()
```

### Saleae Logic 2 UART 解码

在 Saleae Logic 2 软件中：Analyzer → Async Serial → 设置波特率 → 导出为 CSV

### USB MIDI 恢复

Launchpad 灯光重建：Note On 消息的频率 = 按键位置，速度 = 颜色/亮度。

---

## 十、外设捕获分析 (USB HID / 蓝牙)

详见 [peripheral-capture.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-forensics\peripheral-capture.md)

### USB HID 鼠标/笔绘图恢复

```bash
tshark -r capture.pcap -Y "usb.transfer_type==1" -T fields -e usb.capdata > hid_data.txt
```

7-byte HID 报告格式：`[Button, Mode, dx_L, dx_H, dy_L, dy_H, Wheel]`

**恢复方法：** 累加 `dx/dy` 得到绝对坐标 → 不同 mode 用不同颜色渲染 → 过滤大跳跃（抬笔）

### USB HID 键盘捕获解码

USB HID 键盘码映射表 → 提取每次按键的 Usage ID → 转换为 ASCII

### 蓝牙 RFCOMM 重组

从 HCI 抓包中提取 RFCOMM 帧 → 按连接重组 → 重建完整数据流

---

## 十一、3D 打印取证

详见 [3d-printing.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-forensics\3d-printing.md)

- **PrusaSlicer 二进制 G-code (.bgcode)**：QOIF 缩略图 + heatshrink 压缩 → 解压看缩略图
- 魔数：`GCDE` 表示二进制 G-code

---

## 十二、常见 flag 藏匿位置

1. PDF 元数据（Author/Title/Keywords 字段）
2. 图片 EXIF 数据
3. 回收站 `$R` 文件
4. 注册表值
5. 浏览器历史
6. 日志文件碎片
7. 内存字符串
8. 已删除文件（photorec/foremost 雕刻）
9. NTFS ADS（Alternate Data Streams）
10. Docker build history

---

## 十三、关键决策：何时切换 Skill

| 题目特征 | 应切换至 |
|----------|----------|
| 提取出加密 blob 后核心是 RSA/AES/格 | `/ctf-crypto` |
| 涉及恶意软件 C2/beacon/打包样本 | `/ctf-malware` |
| 恢复的 artifact 是编译二进制/固件需反汇编 | `/ctf-reverse` |
| 纯编码谜题/esoteric 格式而非真实取证 | `/ctf-misc` |
| 需要从取证发现追溯到基础设施/属性/公开记录 | `/ctf-osint` |
| Web 应用备份或 API dump，剩余问题是应用逻辑 | `/ctf-web` |

---

## 十四、自动化工作流

1. **文件检测** → `file` + `exiftool` + `binwalk` + `strings`
2. **分类定向** → 磁盘/内存/网络/隐写/Windows/Linux/硬件/外设
3. **快速扫描** → 隐写 `zsteg`/`steghide`，内存 `vol3 windows.pslist`，PCAP `tcpdump -A | grep flag`
4. **深入分析** → 根据检测结果选择对应子领域技术深入
5. **查阅参考** → 遇到具体模式时打开对应的 `.md` 参考文件获取完整 payload
6. **数据恢复** → 提取 flag 并验证格式

> **最重要原则：不要遗漏任何数据源。取证的本质是"数据可以删除但往往不会消失"——检查每个角落：EOF 之后、NTFS ADS、USN Journal、Docker build history、Git reflog。**
