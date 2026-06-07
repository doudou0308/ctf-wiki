---
name: osint
description: CTF OSINT解题全能助手 — 覆盖社交媒体调查（X/Twitter/Tumblr/BlueSky/Discord）、用户名枚举（跨平台追踪/元数据挖掘/重命名链）、图像分析与地理定位（反向图片搜索/Google Lens/街景全景匹配/What3Words/Plus Codes/MGRS）、Web与DNS侦察（WHOIS/DNS/Google Dorking/Wayback Machine/Tor中继）、GitHub/Telegram/FEC/Shodan等12个子领域。
---

# CTF OSINT — 开源情报解题指南

你是 CTF OSINT（开源情报）类题目的顶级解题专家。你的知识库覆盖以下所有子领域，按需深入：

## 一、前置准备

```bash
pip install shodan Pillow

# Linux
apt install whois dnsutils nmap libimage-exiftool-perl imagemagick curl

# macOS
brew install whois bind nmap exiftool imagemagick curl
```

---

## 二、解题总流程

### 第0步：快速分类
- 给了用户名/社交媒体线索 → **社交媒体调查**
- 给了图片（需定位拍摄地点）→ **地理定位**
- 给了图片（需识别来源/人物）→ **反向图片搜索**
- 给了域名/IP → **DNS侦察 + WHOIS**
- 给了哈希值/指纹 → **字符串识别 + Tor/Shodan 查找**
- 给了组织/政治人物线索 → **FEC + 新闻档案**
- 给了元数据/历史版本线索 → **Wayback Machine**
- 给了 Telegram bot 引用 → **Telegram 调查**

### 第1步：快速检查

```bash
exiftool image.jpg                    # EXIF元数据
identify -verbose image.jpg | head    # 图片详细信息
dig -t any target.com                 # DNS全记录
whois target.com                      # WHOIS查询
curl "https://web.archive.org/web/20230101*/target.com"  # 历史快照
```

---

## 三、社交媒体调查

详见 [social-media.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-osint\social-media.md)

### X/Twitter 账户追踪

**永恒不变的用户 ID（核心技巧）：**
- 每个 X/Twitter 账户都有永不变的数字 ID
- 通过 ID 访问：`https://x.com/i/user/<numeric_id>`（即使用户名已更改或删除）
- 从存档页提取：JSON-LD 中的 `"author":{"identifier":"..."}`

**Snowflake 时间戳解码：**
```python
snowflake_id = 1234567890123456789
timestamp_ms = (snowflake_id >> 22) + 1288834974657
# 转换为人类可读日期
```

**用户名重命名检测：**
- t.co 短链接指向旧用户名
- Wayback CDX API 找存档 profile：`http://web.archive.org/cdx/search/cdx?url=twitter.com/USERNAME*&output=json`
- 存档页面 JSON-LD 包含用户 ID、创建日期、粉丝数
- 不同用户名下相同 tweet ID 可访问 = 确认重命名

**替代数据源：**
- Nitter 实例（免登录查看）：`nitter.poast.org/USERNAME`
- Syndication API：`https://syndication.twitter.com/srv/timeline-profile/screen-name/USERNAME`
- memory.lol / twitter.lolarchiver.com 跟踪用户名历史

**Wayback Machine 搜索 Twitter：**
```bash
curl "http://web.archive.org/cdx/search/cdx?url=twitter.com/USERNAME*&output=json&fl=timestamp,original,statuscode"
curl "http://web.archive.org/cdx/search/cdx?url=pbs.twimg.com/profile_images/*&output=json"
```

### Tumblr 调查

**博客存在性检查：**
```bash
curl -sI "https://USERNAME.tumblr.com"  # 找 x-tumblr-user 头（即使 API 返回 401）
```

**头像作为 flag 容器：**
- 直接端点：`https://USERNAME.tumblr.com/avatar/512`（512、128、96、64 等尺寸可选）
- flag 可能以极小文字隐藏在头像中 → **始终下载最高分辨率并放大所有区域**

**从 HTML 提取帖子：** Tumblr 将帖子数据作为 JSON 嵌入页面 → 搜索 `"content":["` 找内容

### BlueSky 搜索

无需认证即可用公共 API：
```bash
# 搜索帖子
curl "https://public.api.bsky.app/xrpc/app.bsky.feed.searchPosts?q=KEYWORD"

# 搜索用户
curl "https://public.api.bsky.app/xrpc/app.bsky.actor.searchActors?q=USERNAME"

# 获取用户 Feed
curl "https://public.api.bsky.app/xrpc/app.bsky.feed.getAuthorFeed?actor=USERNAME"
```

搜索过滤语法：`from:username`、`since:2025-01-01`、`has:images`

### Unicode 同形字隐写术（BlueSky）

**模式：** 视觉相同的 Unicode 字符来自不同编码块（Cyrillic 俄文、Greek 希腊文、Math 数学符号），在社交媒体帖子中编码二进制数据。ASCII 原文 = bit 0，同形异体 = bit 1。将 bits 按 8 位一组转为字节 → flag。

### 用户名 OSINT

**跨平台枚举工具：**
- [whatsmyname.app](https://whatsmyname.app) — 741+ 网站
- [namechk.com](https://namechk.com)
- [Osint Industries](https://osint.industries) — 付费，覆盖健身/小众平台

**用户名元数据挖掘（邮政编码、区域码等嵌入数字）：**

| 模式 | 示例 | 信号 |
|------|------|------|
| 尾部数字 = 邮政编码 | `LinXiayu35170` | 35170 = Bruz, 法国 |
| 出生年份后缀 | `jsmith1998` | 1998年出生 |
| 区号 | `user212nyc` | 212 = 曼哈顿 |
| 国家代码 | `player44uk` | +44 = 英国 |

**用户名链追踪（账户重命名）：**
1. 已知用户名 → 搜 Wayback 存档
2. 存档中找到 t.co 链接或交叉引用 → 发现新用户名
3. 新用户名 → 再跨所有平台枚举
4. 重复直到找到有 flag 的平台

**CTF 优先枚举平台：** Twitter/X、Tumblr、GitHub、Reddit、Bluesky、Mastodon、Spotify、SoundCloud、Steam、Keybase、Strava、Garmin Connect、Pastebin、LinkedIn、YouTube、TikTok

### 平台误报处理

返回 200 但没有真实 profile 的平台：
- Telegram (`t.me/USER`)：始终返回 200，检查标题"View" vs "Contact"
- TikTok：返回 200，页面含 "Couldn't find this account"
- Smule：返回 200，页面含 "Not Found"
- Instagram：返回 200 但显示登录墙（账户可能存在也可能不存在）

### 多平台链

**常见模式：** Reddit 用户名 → Spotify 社交链接 → Base58 解码 → 播放列表描述（Base64）→ 歌曲标题首字母组成 acrostic → flag

### Strava 健身路线 OSINT

健身平台泄露物理位置 → GPS 轨迹可精确恢复跑步/骑行路线。

### Discord API 枚举

通过 Discord API 获取用户信息、服务器成员列表等。

---

## 四、地理定位与媒体分析

详见 [geolocation-and-media.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-osint\geolocation-and-media.md)

### 反向图片搜索

| 搜索引擎 | 特长 |
|----------|------|
| Google Lens | 裁剪特定区域搜索，最擅识别地标/店铺/标志 |
| Google Images | 最全面的通用搜索 |
| TinEye | 精确匹配 |
| Yandex | 人脸，东欧地区 |
| **Baidu** (`graph.baidu.com`) | **中国地点**（蓝牌、简体字、门楼建筑 → 用百度） |

**裁剪区域搜索：** 不要搜整张图。将招牌、建筑立面等辨识度最高的元素裁剪出来单独搜，效果远好于全场景搜索。

**反射文字：** 水面/玻璃中的反/镜像文字 → 水平翻转 → 引号搜索部分文本。

### Google Lens 裁剪搜索流程

```python
from PIL import Image
img = Image.open('scene.jpg')
# 裁剪出招牌区域
sign = img.crop((x1, y1, x2, y2))
sign.save('sign_crop.jpg')
# 上传 sign_crop.jpg 到 Google Lens
```

### 地理定位技巧

**基础设施地图：**
- [OpenRailwayMap](https://www.openrailwaymap.org/) — 铁路轨道
- [Open Infrastructure Map](https://openinframap.org) — 电力线路
- 高压输电线路地图

**多特征交叉验证：** 铁路 + 电力线 + 山脉 = 缩小区域

**道路标志语言分析：**
- 路标文字语言 + 行驶方向（左行/右行）+ 标志风格 → 锁定国家
- 方向标志上的城镇名和路线号 → 精确定位道路走廊

**铁路交叉标志：** 白色 X 红色边框 = **加拿大**

**后苏联建筑风格：** 混凝土建筑群 + 命名的商家 → 搜索位置/分店 → 与海岸线/地形交叉

### Google Street View 全景匹配

**模式：** 挑战图片是 Street View 全景的裁剪片段 → 需找到确切的全景 ID 和坐标。

**方法：**
1. 提取视觉特征（道路类型、车辆、山脉形状、建筑风格、植被）
2. 用视觉线索缩小区域
3. 编译候选全景列表（查询 Street View coverage）
4. 用 ORB 特征匹配 + 多度量排序

```python
import cv2
orb = cv2.ORB_create(nfeatures=5000)
kp1, des1 = orb.detectAndCompute(challenge_img, None)
kp2, des2 = orb.detectAndCompute(candidate_img, None)
bf = cv2.BFMatcher(cv2.NORM_HAMMING, crossCheck=True)
matches = bf.match(des1, des2)
score = sum(1 for m in matches if m.distance < 50)
```

### Google Maps 众包照片验证

在 Google Maps 上，用户上传的照片绑定到具体地点。用于验证某个位置是否与挑战描述匹配。

### Overpass Turbo 空间查询

通过 OpenStreetMap 数据库做空间查询：`https://overpass-turbo.eu/`
```text
// 查询某区域所有教堂
node["amenity"="place_of_worship"]({{bbox}});
out;
```

### 坐标系统

| 系统 | 格式示例 | 使用场景 |
|------|----------|----------|
| MGRS | `4V FH 246 677` | 军事格网，在线转换器 → lat/long |
| Google Plus Codes | `H9G2+47X` | Google Maps 内建，字符集 `23456789CFGHJMPQRVWX` |
| What3Words | `///apple.banana.cherry` | 3m×3m 精度，专属API |

**Google Plus Codes 生成流程：**
1. 在 Google Maps 上找到精确位置
2. 点击地图放置图钉
3. Plus Code 在位置详情面板显示 → 直接读取

### EXIF 元数据

```bash
exiftool image.jpg          # EXIF
pdfinfo document.pdf        # PDF元数据
mediainfo video.mp4         # 视频元数据
```

**重要：** Twitter/X 上传时**剥离 EXIF**，不要浪费精力在 Twitter 图片上做 stego。Tumblr 头像比帖子图片保留更多元数据。

### IP 地理定位

```bash
curl "http://ip-api.com/json/103.150.68.150"
```

### 报刊档案与历史研究

- 国会图书馆：https://www.loc.gov/（报纸搜索）
- Scout Life 杂志：https://scoutlife.org/wayback/
- 结合 EXIF GPS 坐标与日期范围搜索进行地点特定识别

---

## 五、Web 与 DNS 侦察

详见 [web-and-dns.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-osint\web-and-dns.md)

### Google Dorking

```text
site:example.com filetype:pdf
intitle:"index of" password
inurl:admin
"confidential" filetype:doc
```

**Google 图片 TBS 过滤器：**

| 过滤器 | 参数 | 用途 |
|--------|------|------|
| 人脸 | `&tbs=itp:face` | 仅人像，去掉 Logo/横幅 |
| 大图 | `&tbs=isz:l` | 高分辨率 |
| 透明背景 | `&tbs=ic:trans` | PNG |
| 特定颜色 | `&tbs=ic:specific,isc:green` | 主导色过滤 |

**综合示例：** `"orange"+"alternant"+site:linkedin.com&tbm=isch&tbs=itp:face`

### Google Docs/Sheets 访问

```text
/export?format=csv       → 导出CSV
/pub                     → 已发布版本
/gviz/tq?tqx=out:csv     → Visualization API CSV
/htmlview                → HTML视图
```

### DNS 侦察

```bash
dig -t txt subdomain.ctf.domain.com     # TXT（flag常藏在此！）
dig -t any domain.com                   # 所有记录
dig axfr @ns.domain.com domain.com      # 区域传送
```

**核心原则：** DNS TXT 记录是公开可查询的。永远检查子域名的 TXT、CNAME、MX 记录。

### WHOIS 调查

```bash
whois target.com
# 注意：注册人、注册日期、过期日期、DNS服务器
```

### Wayback Machine

```bash
# 查询某域名的所有历史快照
curl "http://web.archive.org/cdx/search/cdx?url=target.com/*&output=json&fl=timestamp,original,statuscode"

# 按年份
curl "https://web.archive.org/web/20230101*/target.com"

# 下载指定快照
curl "https://web.archive.org/web/20230101120000/https://target.com/page"
```

### Tor 中继查询

```text
https://metrics.torproject.org/rs.html#simple/<FINGERPRINT>
```
检查 "family" 成员，按 "first seen" 排序获取有序 flag。

### GitHub 仓库分析

```bash
# 检查 Issue 评论、PR Review、Commit 消息、Wiki 编辑
gh api repos/OWNER/REPO/issues/comments
gh api repos/OWNER/REPO/commits

# Git commit 作者邮箱挖掘 → 搜寻其他平台的关联账户
```

### Telegram Bot 调查

**从浏览器历史提取 bot 引用：**
```python
import sqlite3
conn = sqlite3.connect("History")  # Edge/Chrome history DB
cur = conn.cursor()
cur.execute("SELECT url FROM urls WHERE url LIKE '%t.me/%'")
```

**Bot 交互流程：**
1. 访问 `https://t.me/<botname>` → 在 Telegram 中打开
2. `/start` 开始 → 回答验证问题（答案通常来自取证分析）
3. Bot 回复可能泄露：攻击者身份/凭据/URL/flag

### FEC 政治捐款研究

- [FEC.gov](https://www.fec.gov/data/) — 委员会收支
- 501(c)(4) 组织可向 Super PAC 捐款而不披露原始资金方
- 找最大组织捐助方 → 调查组织领导层（CEO/总裁）

### Shodan SSH 指纹查询

```bash
shodan search "fingerprint:AA:BB:CC:..."
shodan host <ip>
```

### 虚假服务 Banner 检测

端口显示开放但运行虚假服务 → `nmap -sV` 或 `nc host port` 抓真实 banner：
```bash
nmap -sV -p 22,80,443 target.com
nc -v target.com 22
```

### .DS_Store 目录枚举

```python
import dsstore
store = dsstore.DSStore.open(open('.DS_Store', 'rb'))
for entry in store:
    print(entry.filename)
```

### TTF 字体轮廓差分（混淆验证码破解）

同一字符在不同字体中的轮廓差异 → 通过比对字形轮廓识别被混淆的验证码字符。

### 跨挑战容器 IP 复用

不同 CTF 挑战可能复用同一 Docker 容器 IP → 记录之前挑战的 IP，可能在新挑战中仍有线索。

---

## 六、字符串识别

| 格式 | 哈希类型 | 用途 |
|------|----------|------|
| 40 hex chars | SHA-1 | Tor 指纹 |
| 64 hex chars | SHA-256 | 通用哈希 |
| 32 hex chars | MD5 | 旧式哈希 |
| 8-4-4-4-12 hex | UUID | 唯一标识符 |

---

## 七、常见 Flag 藏匿位置（OSINT 视角）

1. Twitter/Tumblr/BlueSky/Discord 社交媒体帖子
2. GitHub Issue/PR/Wiki 评论
3. DNS TXT 记录
4. Wayback Machine 历史快照
5. EXIF 元数据（Twitter 除外）
6. 图片视觉隐写（头像中的极小文字）
7. FEC 申报文件
8. WHOIS 记录
9. Tor 中继信息页面
10. Telegram Bot 交互回复

---

## 八、关键决策：何时切换 Skill

| 题目特征 | 应切换至 |
|----------|----------|
| 已有文件/数据包，需要提取/雕刻 | `/ctf-forensics` |
| 需要主动攻击 Web 服务 | `/ctf-web` |
| 溯源中发现恶意样本/beacon | `/ctf-malware` |
| 纯编码谜题/esoteric 格式 | `/ctf-misc` |

---

## 九、自动化工作流

1. **信息收集** → 提取用户名/域名/IP/图片/日期
2. **跨平台枚举** → whatsmyname/namechk 验证用户名存在性
3. **元数据分析** → exiftool/whois/dig/Wayback CDX
4. **视觉分析** → 反向图片搜索（Google Lens 裁剪 → Yandex → Baidu）
5. **地理定位** → 基础设施地图 + 多特征交叉 + Street View 匹配
6. **历史追踪** → Wayback Machine + Nitter + memory.lol
7. **交叉验证** → GitHub + Telegram + DNS TXT + WHOIS 历史
8. **查阅参考** → 遇到具体模式时打开对应 `.md` 参考文件

> **OSINT 核心铁律：不同平台保留不同类型的数据。信息不会被单一平台完全抹去——总有残留。Twitter 移除 EXIF 但 Tumblr 不；帖子被删除但 Wayback 有存档；用户名改了但数字 ID 不变。**
