---
title: "云安全 - k8s ingress漏洞进一步探索引发的源码层面的文件特性分析（golang、java、php）-先知社区"
source: "https://xz.aliyun.com/news/18157"
author:
published:
created: 2026-06-10
description: "先知社区是一个安全技术社区，旨在为安全技术研究人员提供一个自由、开放、平等的交流平台。"
tags:
  - "clippings"
---
---
title: "云安全 - k8s ingress漏洞进一步探索引发的源码层面的文件特性分析（golang、java、php）-先知社区"
source: "https://xz.aliyun.com/news/18157"
author: ""
published: ""
created: "2026-06-10T21:20:57+08:00"
description: "先知社区是一个安全技术社区，旨在为安全技术研究人员提供一个自由、开放、平等的交流平台。"
tags: [clippings]
---

# 云安全 - k8s ingress漏洞进一步探索引发的源码层面的文件特性分析（golang、java、php）-先知社区

一、前言

之前讨论过

IngressNightmare，但是需要利用起来并不是那么成功，需要猜对应的nginx进程的fd，能否有一个更好用的PoC呢？

IngressNightmare可以查看之前的云安全的系列文章：

21年挖的对象存储漏洞到现在结束了吗？- 云安全：

[

https://mp.weixin.qq.com/s/4cnBa6ysXvEG4ZOM0XkBxA

](https://mp.weixin.qq.com/s/4cnBa6ysXvEG4ZOM0XkBxA)

k8s被黑真能溯源到攻击者吗？：

[

https://mp.weixin.qq.com/s/-VLvp53vqhkVEbSkH2jCqg

](https://mp.weixin.qq.com/s?__biz=MzU1NzkwMzUzNg==&mid=2247484194&idx=1&sn=85c96519682c0bf127c3fd23fc6cd572&scene=21#wechat_redirect)

你的k8s集群又被拿下了？IngressNightmare - 云安全：

[

https://mp.weixin.qq.com/s/O19dvxyxWb2jwcKtHSUhPA

](https://mp.weixin.qq.com/s/O19dvxyxWb2jwcKtHSUhPA)

二、探索

CVE-2025-24513

CVE-2025-24513

在

IngressNightmare系列的漏洞，发现wiz还报告了一个漏洞，此漏洞配合其他漏洞获取到集群里面的密钥，于是想着是否可以获取到集群密钥进而接管整个集群，于是开始对CVE-2025-24513进行探索。

![](https://xzfile.aliyuncs.com/media/upload/picture/20250606150708-dc67d530-42a4-1.png!thumbnail)

于是翻到它的issue[https://github.com/kubernetes/ingress-nginx/pull/13068/commits/cbc159094f6d1b1bf8cf1761eb119138d1f95df1](https://github.com/kubernetes/ingress-nginx/pull/13068/commits/cbc159094f6d1b1bf8cf1761eb119138d1f95df1)

路由分析 & 数据流分析

与之前修复的文件

rootfs/etc/nginx/template/nginx.tmpl不在同一处，得重新找入口点在哪里。

根据之前写audit webhook（一个简单的webserver服务）找到了代码的逻辑。

进行静态分析，找到如下的调用链，可以用test函数以及注解可以快速动态以及静态分析。

internal/admission/controller/server.go:59 ServeHTTP

internal/admission/controller/main.go:54 HandleAdmission

internal/ingress/controller/controller.go:315 CheckIngress

internal/ingress/annotations/annotations.go:179 Extract

internal/ingress/annotations/auth/main.go:149 Parse

具体的代码逻辑如下。

internal/admission/controller/server.go:59

ServeHTTP

func (acs \*AdmissionControllerServer) ServeHTTP(w http.ResponseWriter, req \*http.Request) {

defer req.Body.Close()

data, err:= io.ReadAll(req.Body)

obj, \_, err:= codec.Decode(data, nil, nil)

....

result, err:= acs.AdmissionController.HandleAdmission(obj)

}

internal/admission/controller/main.go:54

HandleAdmission

func (ia \*IngressAdmission) HandleAdmission(obj runtime.Object) (runtime.Object, error) {

review, isV1:= obj.(\*admissionv1.AdmissionReview)

status:= &admissionv1.AdmissionResponse{}

status.UID = review.Request.UID

ingress:= networking.Ingress{}

....

if err:= ia.Checker.CheckIngress(&ingress); err!= nil {

klog.ErrorS(err, "invalid ingress configuration", "ingress", fmt.Sprintf("%v/%v", review.Request.Namespace, review.Request.Name))

status.Allowed = false

status.Result = &metav1.Status{

Status: metav1.StatusFailure, Code: http.StatusBadRequest, Reason: metav1.StatusReasonBadRequest,

Message: err.Error(),

}

review.Response = status

return review, nil

}

return review, nil

}

internal/ingress/controller/controller.go:315

CheckIngress

parsed, err:= annotations.NewAnnotationExtractor(n.store).Extract(ing)

internal/ingress/annotations/annotations.go:179

Extract

val, err:= annotationParser.Parse(ing)

internal/ingress/annotations/auth/main.go:149

Parse

passFilename:= fmt.Sprintf("%v/%v%v-%v.passwd", a.authDirectory, ing.GetNamespace(), ing.UID, secret.UID)

经过静态分析，发现参数均为可控，我们重点要分析的是Parse函数。

sink函数分析-

Parse

internal/ingress/annotations/auth/main.go:149 Parse

可以看到会对路径进行拼接

fmt.Sprintf("%v/%v-%v-%v.passwd", a.authDirectory, ing.GetNamespace(), ing.UID, secret.UID)，最终dumpSecretAuthFile文件到了拼接后的路径。

并且拼接的

ing \*networking.Ingress是参数，根据上面的路由以及数据流分析，ing参数是可控的。

所以我们可以污染文件路径，并且可写入到对应路径。

Parse漏洞代码片段

目前的核心问题是会带

.passwd后缀，导致文件名不能完整控制，那有什么去掉后缀吗？于是开启了考古式的探索

fmt.Sprintf("%v/%v%v-%v.passwd", a.authDirectory, ing.GetNamespace(), ing.UID, secret.UID)

语言特性探索方案

在上面的代码上下问中，想到如下的测试方案。

1、超长文件名截断

2、%00截断

3、协议解析特性如#

4、

Unicode编码问题

超长路径截断？

PHP 路径超长截断探索

在尝试截断的过程中，跟@yiqi一起聊到了php的超长路径截断，于是想深入分析下PHP什么场景下会对文件路径进行截断？在golang场景是否也有类似的问题？

但是遇到第一个问题，我在PHP 5.3的版本没有复现成功（自己很久之前也尝试复现，没复现成功，也没有去寻找原因），这次也问了一些php的大佬也没复习成功，于是想了解php的超长文件名是否有真实case，如果是真实case到底是cms代码逻辑有问题还是php代码有问题？以及这种手法是否对golang有效？于是进行了考古分析。

c语言 超长路径探索

根据之前自己分析php源码如何实现

exec功能，后面最终还是直接调用c的api，所以我们直接在c语言上测试超长文件，看看是否会截断？

测试结果很显然，会直接报告文件无法打开（原因就是文件名太长了，但是我没打印报错的信息出来）

![](https://xzfile.aliyuncs.com/media/upload/picture/20250606150710-dd323cc6-42a4-1.png!thumbnail)

php源码分析&确认问题

既然不是c的api导致的，那php的超长文件名截断是怎么造成的呢？于是开始搜索了很多资料，但是均是没有原理分析，甚至有文章进行误导。（

在跟群友讨论的时候，发出一篇文章，说明了php版本需要小于5.2.8，但是在实际的分析中，发现5.2.8并不存在这个问题

）

![](https://xzfile.aliyuncs.com/media/upload/picture/20250606150711-ddb756fe-42a4-1.png!thumbnail)

于是想从源码层面进行分析，通过搜索大量信息搜索到

MAXPATHLEN关键词，再根据file关键词，找到了php\_fopen\_with\_path函数。

PHPAPI FILE \*php\_fopen\_with\_path(const char \*filename, const char \*mode, const char \*path, zend\_string \*\*opened\_path)

发现关键函数，这里有长度的判断逻辑。[https://github.com/php/php-src/blob/16ca097ef2825cbf668a8ea6610e46db5e8df6a7/main/fopen\_wrappers.c#L653C14-L653C33](https://github.com/php/php-src/blob/16ca097ef2825cbf668a8ea6610e46db5e8df6a7/main/fopen_wrappers.c#L653C14-L653C33)

于是翻到5.2.7版本，并且与5.3.8版本对比，发现5.2.7版本直接使用snprintf函数复制路径并且指定

MAXPATHLEN长度进行截断，最终导致文件路径截断问题。

![](https://xzfile.aliyuncs.com/media/upload/picture/20250606150712-de91fd5e-42a4-1.png!thumbnail)

golang 文件路径超长截断？

那golang是否存在超长路径截断的问题呢？在调用

os.WriteFile函数的时候，

没有发现长度截断的代码，直接使用open的syscall调用c api接口，会直接导致长度过长报错。

![](https://xzfile.aliyuncs.com/media/upload/picture/20250606150713-df268668-42a4-1.png!thumbnail)

%00截断探索

java和php的%00截断探索

通过咨询deepseek发现php %00的漏洞的CVE编号是

CVE-2006-7243，并且根据之前给php提交bug的经验，搜索到对应的php bug地址：

[

https://bugs.php.net/bug.php?id=39863

](https://bugs.php.net/bug.php?id=39863)

，发现php bug id 39863，最终在github上搜到对应的commit。[https://github.com/php/php-src/commit/ce96fd6b0761d98353761bf78d5bfb55291179fd#diff-28ed31fa6b0d63b5c77f4c164e93fc6b0057d286d607c0d8d73897f5bd66bb6c](https://github.com/php/php-src/commit/ce96fd6b0761d98353761bf78d5bfb55291179fd#diff-28ed31fa6b0d63b5c77f4c164e93fc6b0057d286d607c0d8d73897f5bd66bb6c)

![](https://xzfile.aliyuncs.com/media/upload/picture/20250606150714-dff8a558-42a4-1.png!thumbnail)

这里的修复方案也比较简单，通过

strlen(filename)!= filename\_len)判断是否包含

空字符（\\0）。[https://github.com/php/php-src/blob/704bbb3263d0ec9a6b4a767bbc516e55388f4b0e/ext/standard/file.c#L909](https://github.com/php/php-src/blob/704bbb3263d0ec9a6b4a767bbc516e55388f4b0e/ext/standard/file.c#L909)

在 C 语言中，strlen 函数的行为是 遇到第一个空字符（\\0）就停止计算长度。

也就是会通过strlen获取的长度与实际的给的路径长度进行比较，如果不一致，则说明有

空字符（\\0）存在，直接返回False不打开文件。

![](https://xzfile.aliyuncs.com/media/upload/picture/20250606150716-e0bfa0e8-42a4-1.png!thumbnail)

java之前也是存在一样的问题，目前都会提前检查一下路径是否存在\\x00空字节。

golang

%00截断探索

我们再看看golang的处理，通过

ByteSliceFromString函数检查文件路径是否包含

\\x00空字节，如果包含直接返回nil以及报错信息。

![](https://xzfile.aliyuncs.com/media/upload/picture/20250606150717-e14dac6e-42a4-1.png!thumbnail)

调用栈

再探索

CVE-2025-24513

通过上面的分析，我们得到结论，我们无法摆脱.passw后缀，好像有点鸡肋。

那我们再看看是否可以污染文件内容或者读取敏感内容，在

dumpSecretAuthFile函数中，需要指定api.Secret类型，并且只能读取auth字段，同样鸡肋。

dumpSecretAuthFile函数

CVE-2025-24513基本可以放弃了。

二、提高成功率

？

2.2、更为通用的

mirror id注入

经过'$$$$$'大佬提醒，mirror id这个注入点更为通用，在我测试的版本都能成功。

k8s集群又被拿下了？IngressNightmare - 云安全：

[

https://mp.weixin.qq.com/s/O19dvxyxWb2jwcKtHSUhPA

](https://mp.weixin.qq.com/s/O19dvxyxWb2jwcKtHSUhPA)

文章中测试的auth-url参数并非更通用。

![](https://xzfile.aliyuncs.com/media/upload/picture/20250606150717-e1d6dd40-42a4-1.png!thumbnail)

![](https://xzfile.aliyuncs.com/media/upload/picture/20250606150718-e243563a-42a4-1.png!thumbnail)

确定注入的位置

如果是正常的url会注入两个地方，导致闭合难以完成

"nginx.ingress.kubernetes.io/mirror-target": "https://www.baidu.com/"

![](https://xzfile.aliyuncs.com/media/upload/picture/20250606150719-e2c59db8-42a4-1.png!thumbnail)

改成如下即可

并且整理成新的脚本：IngressNightmareV2.py[https://github.com/lufeirider/IngressNightmare-PoC/blob/main/IngressNightmareV2.py](https://github.com/lufeirider/IngressNightmare-PoC/blob/main/IngressNightmareV2.py)

2.1、还是回到fuzz？

CVE-2025-24513实在很鸡肋，回头看fuzz其实也不是不行，那如何进行优化呢？

这里涉及到常规的文件类型漏洞判断，我们如何分析一个文件是否写入成功？

1、通过各种报错信息返回（无权限、路径不存在），判断文件是否存在

2、通过延迟判断

k8s的ingress webhook接口是有返回报错信息的，那我们就可以利用第1点进行利用，优化我们的PoC。

首先判断一下那些PID是存活的，然后通过niginx缓存临时的so文件，再进行加载so即可完成目标。

我们判断/proc/xx/cmdline存在的时候，会报错

Exec format error

![](https://xzfile.aliyuncs.com/media/upload/picture/20250606150720-e396014c-42a4-1.png!thumbnail)

如果文件不存在报告

No such file or directory

![](https://xzfile.aliyuncs.com/media/upload/picture/20250606150722-e47ded4a-42a4-1.png!thumbnail)

我们可以先通过这样的回现去判断一下哪些PID存在的。

最后我们多线程判断这些pid的fd文件即可。

三、结论

为了探索

K8s Ingress

的更佳的利用姿势，深入分析K8s Ingress

CVE-2025-24513漏洞，并且举一反三从源码审计

了PHP、C、Golang的文件接口源码，总结了这些语言的文件接口在文件穿越场景的利用（进行深入探索，尤其分析PHP文件目录穿越，发现很多人只是看到过没复现成功过就算了）。

最终从优化PoC角度，将PoC改造更为通用、爆破效率更高，进一步提高PoC的成功率。