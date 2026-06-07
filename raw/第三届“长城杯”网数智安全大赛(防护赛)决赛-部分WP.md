---
title: "第三届“长城杯”网数智安全大赛(防护赛)决赛-部分WP"
source: "https://mp.weixin.qq.com/s/QwUF9NvhA-lWXPlZDxCc1g"
author:
  - "[[wallkone]]"
published:
created: 2026-06-07
description: "此文章涉及的wp均为赛后复现这次比赛也拿到了全国二等奖，排名全国16，题解数量，Re全解，一道AI，ISW一个"
tags:
  - "clippings"
---
---
title: "第三届“长城杯”网数智安全大赛(防护赛)决赛-部分WP"
source: "https://mp.weixin.qq.com/s/QwUF9NvhA-lWXPlZDxCc1g"
author: "[[wallkone]]"
published: ""
created: "2026-06-07T22:12:57+08:00"
description: "此文章涉及的wp均为赛后复现这次比赛也拿到了全国二等奖，排名全国16，题解数量，Re全解，一道AI，ISW一个"
tags: [clippings]
---

# 第三届“长城杯”网数智安全大赛(防护赛)决赛-部分WP

wallkone *2026年4月29日 19:22*

此文章涉及的wp均为赛后复现

![图片](https://mmbiz.qpic.cn/sz_mmbiz_jpg/2AAMh9HmvsRlWxp8Z8YHcuniczfods2s7zicN9wTOq9fITYDwfQC7SJjuUbSC3LwF3GJ4E1LJHNCOEZW5n3fIFCUTASzj6iaXpR7Yjao2soPT8/640?wx_fmt=jpeg&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=0)

这次比赛也拿到了全国二等奖，排名全国16，题解数量，Re全解，一道AI，ISW一个，整体来说还不错。

## Re

## DokiLogic

题目是一个 Ren'Py 游戏逆向。核心逻辑藏在 `game/script.rpyc` 中：脚本内嵌了一个 Windows PE，运行该 PE 得到密文，再将玩家输入逐字符 XOR `0x23` 后与密文比较。

## Solution

### Step 1: 定位 Ren'Py 脚本逻辑

目录中存在 `dokigame.py` 、 `renpy/` 、 `game/script.rpyc` ，可以判断这是 Ren'Py 打包游戏。直接对 `game/` 做明文搜索没有找到 flag，重点转向反序列化 `script.rpyc` 。

`script.rpyc` 文件头为 `RENPY RPC2` ，其中脚本数据经过 zlib 压缩和 pickle 序列化。反序列化 AST 后，主流程只有输入、加密和比较：

```
user_input=renpy.input("just input your answer: ", length=60)
user_input=user_input.strip()
encry_input=l11111l1ll1l(user_input)

ifencry_input==ll111l11l111:
    # success
```

init Python 块中的关键逻辑如下：

```
_f=b"..."

withopen(".1.exe", "wb") asf:
    f.write(_f)

l11l1ll111l1=subprocess.run("./.1.exe", stdout=subprocess.PIPE).stdout
os.remove(".1.exe")

defl11111l1ll1l(s):
    key=35
    return"".join(chr(ord(c) ^key) forcins)

 ll111l11l111=l11l1ll111l1.decode("latin-1")
```

因此校验关系是：

```
user_input XOR 0x23 == embedded_pe_stdout
```

### Step 2: 自动提取并还原答案

下面脚本会从 `game/script.rpyc` 反序列化出 Ren'Py AST，提取 init 块里的 `_f` 字节数组，运行嵌入 PE 获取 stdout，再 XOR `0x23` 还原输入。

```
importast
importos
importstruct
importsubprocess
importsys
importtypes
importzlib

sys.path.insert(0, os.getcwd())

importrenpy
importrenpy.error

renpy.import_all()
renpy.game.script=types.SimpleNamespace(
    record_pycode=True,
    all_pyexpr=[],
    all_pycode=[],
)
renpy.game.log=types.SimpleNamespace(mutated={})

fromrenpy.compat.pickleimportloads

defread_rpyc(path, slot=2):
    data=open(path, "rb").read()
    header=b"RENPY RPC2"

    ifnotdata.startswith(header):
        returnzlib.decompress(data)

    pos=len(header)

    whileTrue:
        slot_id, start, length=struct.unpack("III", data[pos:pos+12])

        ifslot_id==slot:
            returnzlib.decompress(data[start:start+length])

        ifslot_id==0:
            returnNone

        pos+=12

_, stmts=loads(read_rpyc("game/script.rpyc", 2))
init_source=stmts[1].block[0].code.source
tree=ast.parse(init_source)

blob=None
fornodeinast.walk(tree):
    ifnotisinstance(node, ast.Assign):
        continue

    fortargetinnode.targets:
        ifisinstance(target, ast.Name) andtarget.id=="_f":
            blob=node.value.value

ifblobisNone:
    raiseRuntimeError("embedded PE not found")

payload="payload.exe"
withopen(payload, "wb") asf:
    f.write(blob)

try:
    out=subprocess.run(
        [payload],
        stdout=subprocess.PIPE,
        stderr=subprocess.PIPE,
        timeout=5,
    ).stdout
finally:
    ifos.path.exists(payload):
        os.remove(payload)

answer="".join(chr(b^0x23) forbinout)
 print(answer)
```

运行结果：

```
f17c53c3-dc26-46b1-b373-2ca00a6a6721
```

嵌入 PE 的原始 stdout 十六进制为：

```
45121440161040100e474011150e171541120e411014100e114042131342154215141112
```

将这些字节逐个 XOR `0x23` 即可得到最终输入。

## notjavaweb

题目给了一个 Spring Boot JAR 和一份流量包。JAR 里隐藏了一个由用户行为驱动的栈式 VM，PCAP 中的 HTTP 请求会把 VM 指令和参数写入缓冲区；触发登出后 VM 解密并执行内置的 `payload.enc` 。解出的 payload 是 Rust ELF，它读取 flag 后用一套自定义 AES-CBC 变体加密，并通过 `/api/telemetry` 发出，最终从流量包中解密得到 flag。

### Step 1: 通过 JADX-MCP 定位隐藏 VM

重点查看的类如下：

- `com.example.moviereview.controller.ReviewController`
- `com.example.moviereview.controller.UserController`
- `com.example.moviereview.analytics.UserBehaviorAnalyticsAspect`
- `com.example.moviereview.analytics.AnalyticsReportGenerator`
- `com.example.moviereview.analytics.VmContext`

`/api/reviews/add` 会提取评论里的 `data[...]` 内容， `ReviewController` 和 `UserBehaviorAnalyticsAspect` 都会追加一次，所以每条 `data[...]` 参数会进入 VM 两次。 `/api/user/avatar` 会把 `emojiAvatarId` 作为整数指令追加。 `/api/logout` 调用 `analyticsReportGenerator.generateReport(vmContext.getBuffer())` ，真正执行 VM。

VM 关键 opcode：

- `10`: 读取 resource
- `11`: byte array get
- `12`: byte array set
- `13`: xor
- `14`: 乘法并取低 8 bit
- `15`: 写文件
- `16`: 执行命令
- `17`: byte array length
- `18`: parse int
- `19`: dup
- `20`: swap
- `21`: jump
- `22`: 条件跳转，条件为 0 时跳转
- `23`: sub
- `24`: add
- `25`: 从栈上按偏移取值
- `26`: pop

PCAP 的 stream 0 正好是一组驱动 VM 的请求：读取 `/payload.enc` ，逐字节变换，写入 `/tmp/payload_run` ，然后 `chmod +x` 并执行。

### Step 2: 解出 payload 并分析 telemetry 加密

将 VM 指令化简后， `payload.enc` 的解密逻辑为：

```
key=102
fori, binenumerate(data):
    signed_b=bifb<128elseb-256
    val= (signed_b^key) -i
    data[i] =val&0xff
    key=val^55
```

解出的文件是 ELF64 Rust PIE，SHA256：

```
5fafafc9718a42cbaa6cb7572c127c843f10b3ee1510d10e2d0dd987ef7f5f26
```

ELF 中可以看到明文路径和目标地址：

- `/home/ccb/flag.txt`
- `192.168.117.1:9961`
- `POST /api/telemetry`

同时在 rodata 中定位到自定义 AES-CBC 参数：

- IV: `8e1af65530c974bb2d974e1160daa73c`
- key: `4a7f2c91b35ed816fa4309cc7be5283d`
- row shifts: `[0, 3, 1, 2]`
- S-box: `payload_run` 文件偏移 `0x6970`

PCAP 的 stream 1 中 `/api/telemetry` body 是密文，使用上面的 AES 变体 CBC 解密即可得到 flag。

### Step 3: 完整 solve 脚本

脚本从当前目录下的 `movie-review-system-1.0.0.jar` 和 `traffic.pcap` 开始，最终打印 flag：

```
importhashlib
importjson
importre
importshutil
importsubprocess
importzipfile
frompathlibimportPath

JAR=Path("movie-review-system-1.0.0.jar")
PCAP=Path("traffic.pcap")

TSHARK=r"E:\tools\tools\Wireshark\tshark.exe"
ifnotPath(TSHARK).exists():
    TSHARK=shutil.which("tshark") or"tshark"

deftshark_posts():
    cmd= [
        TSHARK,
        "-r",
        str(PCAP),
        "-Y",
        'http.request.method == "POST"',
        "-T",
        "fields",
        "-e",
        "tcp.stream",
        "-e",
        "http.request.uri",
        "-e",
        "http.file_data",
    ]
    out=subprocess.run(cmd, check=True, capture_output=True, text=True).stdout
    rows= []
    forlineinout.splitlines():
        parts=line.split("\t")
        iflen(parts) <3:
            parts+= [""] * (3-len(parts))
        stream, uri, body_hex=parts[:3]
        rows.append((int(stream), uri, body_hex))
    returnrows

defrecover_vm_events(rows):
    events= []
    telemetry_hex=None

    forstream, uri, body_hexinrows:
        body=bytes.fromhex(body_hex) ifbody_hexelseb""

        ifstream==0anduri=="/api/reviews/add":
            obj=json.loads(body.decode())
            m=re.fullmatch(r"data\[(.*)\]", obj["content"])
            ifm:
                # ReviewController 和 analytics aspect 都会 append 一次。
                events.extend([m.group(1), m.group(1)])

        elifstream==0anduri=="/api/user/avatar":
            obj=json.loads(body.decode())
            events.append(obj["emojiAvatarId"])

        elifstream==1anduri=="/api/telemetry":
            telemetry_hex=body_hex

    iftelemetry_hexisNone:
        raiseRuntimeError("telemetry ciphertext not found")
    returnevents, bytes.fromhex(telemetry_hex)

defdecode_payload():
    withzipfile.ZipFile(JAR) aszf:
        data=bytearray(zf.read("BOOT-INF/classes/payload.enc"))

    key=102
    fori, binenumerate(data):
        signed_b=bifb<128elseb-256
        val= (signed_b^key) -i
        data[i] =val&0xff
        key=val^55

    returnbytes(data)

defxtime(a):
    ifa&0x80:
        return ((a<<1) ^0x1B) &0xFF
    return (a<<1) &0xFF

defgf_mul(a, b):
    out=0
    whileb:
        ifb&1:
            out^=a
        a=xtime(a)
        b>>=1
    returnout

defexpand_key(key, sbox):
    rcon= [0x00, 0x01, 0x02, 0x04, 0x08, 0x10, 0x20, 0x40, 0x80, 0x1B, 0x36]
    words= [list(key[i : i+4]) foriinrange(0, 16, 4)]

    foriinrange(4, 44):
        temp=words[i-1][:]
        ifi%4==0:
            temp=temp[1:] +temp[:1]
            temp= [sbox[x] forxintemp]
            temp[0] ^=rcon[i//4]
        words.append([a^bfora, binzip(words[i-4], temp)])

    return [sum(words[4*r : 4*r+4], []) forrinrange(11)]

defadd_round_key(state, rk):
    return [a^bfora, binzip(state, rk)]

definv_shift_rows(state, shifts):
    out= [0] *16
    forrinrange(4):
        row= [state[4*c+r] forcinrange(4)]
        k=shifts[r] %4
        ifk:
            row=row[-k:] +row[:-k]
        forc, vinenumerate(row):
            out[4*c+r] =v
    returnout

definv_mix_columns(state):
    out= [0] *16
    forcinrange(4):
        a= [state[4*c+r] forrinrange(4)]
        col= [
            gf_mul(a[0], 14) ^gf_mul(a[1], 11) ^gf_mul(a[2], 13) ^gf_mul(a[3], 9),
            gf_mul(a[0], 9) ^gf_mul(a[1], 14) ^gf_mul(a[2], 11) ^gf_mul(a[3], 13),
            gf_mul(a[0], 13) ^gf_mul(a[1], 9) ^gf_mul(a[2], 14) ^gf_mul(a[3], 11),
            gf_mul(a[0], 11) ^gf_mul(a[1], 13) ^gf_mul(a[2], 9) ^gf_mul(a[3], 14),
        ]
        forr, vinenumerate(col):
            out[4*c+r] =v
    returnout

defdecrypt_block(block, round_keys, inv_sbox, shifts):
    state=add_round_key(list(block), round_keys[10])
    state=inv_shift_rows(state, shifts)
    state= [inv_sbox[x] forxinstate]

    forrndinrange(9, 0, -1):
        state=add_round_key(state, round_keys[rnd])
        state=inv_mix_columns(state)
        state=inv_shift_rows(state, shifts)
        state= [inv_sbox[x] forxinstate]

    state=add_round_key(state, round_keys[0])
    returnbytes(state)

defdecrypt_telemetry(elf, ciphertext):
    iv=elf[0x5270:0x5280]
    key=elf[0x5280:0x5290]
    shifts= [
        int.from_bytes(elf[0x6930+i*8 : 0x6938+i*8], "little")
        foriinrange(4)
    ]
    sbox=list(elf[0x6970:0x6A70])

    inv_sbox= [0] *256
    fori, xinenumerate(sbox):
        inv_sbox[x] =i

    round_keys=expand_key(key, sbox)
    prev=iv
    plain=b""

    foroffinrange(0, len(ciphertext), 16):
        block=ciphertext[off : off+16]
        dec=decrypt_block(block, round_keys, inv_sbox, shifts)
        plain+=bytes(a^bfora, binzip(dec, prev))
        prev=block

    returnplain

rows=tshark_posts()
events, telemetry=recover_vm_events(rows)
elf=decode_payload()

assertelf.startswith(b"\x7fELF")

print("vm events:", len(events))
print("payload sha256:", hashlib.sha256(elf).hexdigest())

plain=decrypt_telemetry(elf, telemetry)
flag=re.search(rb"flag\{[^}]+\}", plain).group(0).decode()
 print(flag)
```

运行结果：

```
vm events: 86
payload sha256: 5fafafc9718a42cbaa6cb7572c127c843f10b3ee1510d10e2d0dd987ef7f5f26
flag{F1n@1ly_Y0u_G0t_Th1s_f1ag_and_f1nd_7h3_TRUTH_D0_Y0u_L1k3_1t?}
```

## Flag

```
flag{F1n@1ly_Y0u_G0t_Th1s_f1ag_and_f1nd_7h3_TRUTH_D0_Y0u_L1k3_1t?}
```

## dumbcpp

## 分析过程

程序入口非常简单：

```
int__stdcallWinMain(HINSTANCEhInstance, HINSTANCEhPrevInstance, LPSTRlpCmdLine, intnShowCmd)
{
  returnUnityMain2(hInstance, 0, lpCmdLine, nShowCmd);
}
```

说明 `dumbcpp.exe` 只是 Unity 启动器，核心逻辑在：

```
GameAssembly.dll
dumbcpp_Data/il2cpp_data/Metadata/global-metadata.dat
```

通过 IL2CPP 元数据定位到关键类和方法：

```
checker.CheckFlag
checker.VerifyInput
xYz987.check_input
xYz987.AESEncrypt
xYz987.GetRealKey
```

`GameAssembly.dll` 被 Themida 壳保护，静态分析不到有效逻辑，因此运行程序后 dump 内存中的 `GameAssembly.dll` 。

## 核心逻辑

在解包后的内存 dump 中定位到：

```
xYz987.check_input  -> 0x18028c110
xYz987.AESEncrypt   -> 0x18028b420
xYz987.GetRealKey   -> 0x18028bbc0
xYz987..ctor        -> 0x18028be70
```

`check_input` 逻辑如下：

```
1. 输入不能为空
2. 输入长度必须为 0x2a，也就是 42
3. 调用 GetRealKey() 生成 AES key
4. 调用 AESEncrypt(input, key)
5. 将加密结果与 target_cipher 比较
6. 相等则成功
```

运行时读取对象字段得到：

```
target_cipher =
c6788f4ebc791b7572eca04b2a9ccb23f9a1cee4a0dd3ec4e8d64d119fe3ac1b9cc08893bfba51ee7a3687207b7b3c68

key seed = asdfg
key_part1 = 9876543210
aes_iv = 1234567890ABCDEF
```

`GetRealKey` 生成 key：

```
"asdfg" + "9876543210".Substring(3, 5)
= "asdfg65432"
```

`AESEncrypt` 会将 key 右填充到 32 字节：

```
asdfg654320000000000000000000000
```

加密算法为：

```
AES-CBC + PKCS7
```

## 解密脚本

```
from cryptography.hazmat.primitives.ciphers import Cipher, algorithms, modes
from cryptography.hazmat.primitives import padding

target = bytes.fromhex(
    "c6788f4ebc791b7572eca04b2a9ccb23"
    "f9a1cee4a0dd3ec4e8d64d119fe3ac1"
    "b9cc08893bfba51ee7a3687207b7b3c68"
)

key = ("asdfg" + "9876543210"[3:8]).ljust(32, "0").encode()
iv = b"1234567890ABCDEF"

decryptor = Cipher(algorithms.AES(key), modes.CBC(iv)).decryptor()
padded = decryptor.update(target) + decryptor.finalize()

unpadder = padding.PKCS7(128).unpadder()
flag = unpadder.update(padded) + unpadder.finalize()

print(flag.decode())
```

运行结果：

```
flag{cc697aa6-42b6-4341-9bed-6b5cc5c603fd}
```

## 注意

`global-metadata.dat` 中还能搜到一个假 flag：

```
flag{f64827be-459a-416c-83bb-b172173f48a9}
```

但它加密后与程序中的 `target_cipher` 不匹配，因此不是正确答案。

## Flag

```
flag{cc697aa6-42b6-4341-9bed-6b5cc5c603fd}
```

## AI

## LatticeCNN

## 题目信息

附件包含 5 个文件：

- `task.py` ：题目生成脚本，给出 CNN-like 特征提取逻辑和带噪线性输出逻辑。
- `public.json` ：公开参数，包括模数 `q` 、噪声范围、卷积核和混合矩阵。
- `inputs.npy` ：24 个输入样本，每个样本是 `6 x 6` 的整数矩阵。
- `outputs.npy` ：24 个模型输出。
- `cipher.bin` ：加密后的 flag。

最终 flag：

```
flag{7ffd71471b67009e1c39cfaa259cf7b2}
```

## 核心思路

题目表面是一个很小的 CNN-like 模型，但真正需要恢复的是最后一层隐藏整数向量 `secret_s` 。每个样本会先经过固定的特征提取函数得到 8 维向量 `a` ，再计算：

```
b = <a, secret_s> + e mod q
```

其中：

- `q = 65537`
- `secret_s` 是 8 维整数向量
- `e` 是很小的噪声，范围为 `[-2, 2]`
- 给出了 24 组 `(input, output)`

因此题目可以转化为小维度 LWE 问题。因为维度只有 8，噪声范围只有 5 种取值，所以可以选取 8 条线性方程，枚举这 8 条方程的噪声，共 `5^8 = 390625` 种情况。每次消去噪声后在模 `q` 下解线性方程组得到候选 `secret_s` ，再用全部 24 条样本校验误差是否都落在 `[-2, 2]` 。

恢复出的隐藏向量为：

```
secret_mod    = [17, 65525, 9, 65522, 6, 14, 65529, 11]
secret_signed = [17, -12, 9, -15, 6, 14, -8, 11]
```

接着将有符号形式用逗号拼接：

```
17,-12,9,-15,6,14,-8,11
```

取 `SHA256` 后前 16 字节作为 AES-128-ECB 密钥，解密 `cipher.bin` 并去除 PKCS#7 padding，即可得到 flag。

## 文件分析

`task.py` 中关键代码如下：

```
q=65537
NOISE_BOUND=2
NUM_SAMPLES=24

deffeature(x):
    c1=avgpool2x2(relu(conv_valid(x, K1)))
    c2=avgpool2x2(relu(conv_valid(x, K2)))
    base=np.concatenate([c1.reshape(-1), c2.reshape(-1)]).astype(np.int64)
    mixed= (MIX@base) %q
    returnmixed.astype(np.int64)

defadd_noise_to_output(a, secret_s, noise_bound, q):
    e=random.randint(-noise_bound, noise_bound)
    b_i=int(np.dot(a, secret_s) +e) %q
     returnb_i
```

这说明每个输出 `b_i` 都是 8 维特征 `a_i` 和隐藏向量 `secret_s` 的模线性关系，只是额外加了很小的整数噪声。

特征提取部分虽然有卷积、ReLU、池化和矩阵乘法，但所有参数都在 `public.json` 中公开，因此可以完全复现每个输入样本对应的 `a_i` 。

## 数学建模

对第 `i` 个样本，有：

```
a_i * s + e_i = b_i mod q
```

其中 `a_i` 是 `1 x 8` 行向量， `s` 是 `8 x 1` 隐藏向量。

选择 8 条方程组成矩阵：

```
A * s + e = b mod q
```

因为 `e` 每个分量只可能是 `-2, -1, 0, 1, 2` ，所以枚举 `e` 后：

```
A * s = b - e mod q
s = A^-1 * (b - e) mod q
```

如果某个候选 `s` 是正确的，则代回全部 24 条数据时，所有误差都会处于 `[-2, 2]` 。

## EXP

仓库中已生成完整脚本： `exp.py` 。

该脚本不依赖 `numpy` ，会自行解析本题的 little-endian int64 `.npy` 文件，适合在缺少 `numpy` 的环境直接运行。

运行命令：

```
python3 exp.py
```

预期输出：

```
equation_indexes = (0, 1, 2, 3, 4, 5, 6, 7)
secret_mod       = [17, 65525, 9, 65522, 6, 14, 65529, 11]
secret_signed    = [17, -12, 9, -15, 6, 14, -8, 11]
noise_errors     = [-1, 1, 2, -1, 2, 2, 0, -2, 1, -1, -1, 2, 0, 2, 1, 1, -1, 2, -1, 1, -2, -2, 1, -2]
aes_key          = c0f8252947db9b448482008d3e8911fa
flag{7ffd71471b67009e1c39cfaa259cf7b2}
```

核心恢复逻辑如下：

```
for indexes in itertools.combinations(range(len(a_rows)), 8):
    inv = inverse_matrix_mod([a_rows[i] for i in indexes], q)
    if inv is None:
        continue

    selected_b = [b_rows[i] for i in indexes]
    for errors in itertools.product(range(-noise_bound, noise_bound + 1), repeat=8):
        rhs = [(b - e) % q for b, e in zip(selected_b, errors)]
        secret = matrix_vector_mod(inv, rhs, q)

        all_errors = [
            centered_mod(b - sum(ai * si for ai, si in zip(row, secret)), q)
            for row, b in zip(a_rows, b_rows)
        ]
        if all(-noise_bound <= e <= noise_bound for e in all_errors):
            return secret, all_errors, indexes
```

解密逻辑如下：

```
secret_signed = [17, -12, 9, -15, 6, 14, -8, 11]
key_material = ",".join(map(str, secret_signed)).encode()
key = hashlib.sha256(key_material).digest()[:16]
plaintext = unpad(AES.new(key, AES.MODE_ECB).decrypt(Path("cipher.bin").read_bytes()), 16)
```

## 关键点总结

- CNN-like 部分不是黑盒模型攻击，因为卷积核和 `MIX` 矩阵全部公开，可以直接复现特征向量。
- 输出层本质是带小噪声的模线性方程组。
- 维度只有 8，噪声只有 `[-2, 2]` ，直接枚举选中 8 条方程的误差即可恢复隐藏向量。
- 正确隐藏向量要用模数中心化转成有符号整数，否则 AES key derivation 不匹配。
- AES 使用 `SHA256(b"17,-12,9,-15,6,14,-8,11")[:16]` 作为密钥，模式为 ECB。

## Flag

```
flag{7ffd71471b67009e1c39cfaa259cf7b2}
```

## ISW

## 攻击思路

通过目录扫描，扫到sql数据库配置文件，在里面有管理员账号密码，通过账号密码登录web后台，简单前端校验绕过，上传文件getshell，通过find提取，获取flag

## 内网横向

由于队内的pwn师傅这次比赛缺席导致后续内网横向不理想，一个未授权目录遍历，获取二进制文件，猜测应该是要通过二进制文件获取远程服务器权限，打通二层服务，另一个ftp匿名登录，下载app.jar包，分析后需要伪造token，但是当时伪造的token一直卡在第一步，后续利用不成功，后面就转战ctf了，isw就告一段落

继续滑动看下一个

星络安全实验室

向上滑动看下一个