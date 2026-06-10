---
title: "腾讯云安全挑战赛第一期wp-先知社区"
source: "https://xz.aliyun.com/news/18944"
author:
published:
created: 2026-06-10
description: "先知社区是一个安全技术社区，旨在为安全技术研究人员提供一个自由、开放、平等的交流平台。"
tags:
  - "clippings"
---
---
title: "腾讯云安全挑战赛第一期wp-先知社区"
source: "https://xz.aliyun.com/news/18944"
author: ""
published: ""
created: "2026-06-10T21:20:31+08:00"
description: "先知社区是一个安全技术社区，旨在为安全技术研究人员提供一个自由、开放、平等的交流平台。"
tags: [clippings]
---

# 腾讯云安全挑战赛第一期wp-先知社区

COS提权与利用

解密脚本

![](https://xzfile.aliyuncs.com/media/upload/picture/20250923102321-46a048a4-9824-1.png)

下面 xor 后的三个结果分别是 ak，sk，session token，配置下 secretid 和 secretkey（开了几次环境，所以下面ak和sk可能会有变化）

token 需要手动去配置下

┌──(root㉿lll)-\[~\]

└─# cat.tccli/default.credential

{

"secretId": "AKIDrGuURY3ThyLSMqDsEv1ecnGSsRP0m0krNNUWUgTvqr\_IitXJvmdelZq8dx5XEtt3",

"secretKey": "q9wsCzWhmEp3E/DvDhld/MMiuDo8yTn0shcpM7wBB1o=",

"token": "ukN1JfAFyFEJbyw3cdc5TTdUzPBhknna27c03053a861748506b924176014da27MuPprHAIl3oB2kxiKigKZ3oP1nsJz1BPLiljMlfGOSIlVWWb9uk\_wtk1LKFaWPcJUJ44PxuPnc\_f3dRc26dfmvJnOxgtmzdfjeZMsyq0iUbwcO1Be0\_UlwREY9GPw4hvvUqdUkv6SJyb-6yVNoDA0hXnUBUgEKFRCGAJ7bs5\_8tq\_gok265jvp7Nes1NJ\_ZEedEff4Tzd5iXb6Ix1ZSyGe4Yy4TJEcs2ixHvSXsCpqNLNSCR1JDSOG66GrfAqO0bC-uvsVrwva49kxchxWa\_NR9cRaSoSumJE-N2siJ2pUZhDuqvNBUujkbHKo6Tqtg7Z8-mLjmfxZOY3HhJZOoR73Drqhpt6ILX4IcEuoNyOEQ9lkjg10lW\_OoCKQmv\_\_Iey6\_9IqWYfIVgOwChCYGzhD-TrEHmjGickxNXzG3qKEez8YTWc973G7UhBplySXuq0oziGVjfWOxQ3MqocCTsyONWyZHsIo9RdnsZNuzvMFyR6JBgc-2l1r2p5hNunusTalPtZWsi0nBj3Co0ZRDe\_OQqv6Xkbpn4eVgiryKLKwH4iTid2nCTBY9kDj0tS76\_U\_QVlLIK3zagu5xF3RR37Xy3BE\_29q-XkbX8S0nNM1wwUAVO6L\_zWUMUwaWu2UkDab1OXi-RV98a8N6bs9WCtA"

}

┌──(root㉿lll)-\[~\]

└─# tccli sts GetCallerIdentity

{

"Arn": "qcs::sts:100026992078:federated-user/100043488407",

"AccountId": "100026992078",

"UserId": "100043488407:challenge\_01\_q81gl6k4osny",

"PrincipalId": "100043488407",

"Type": "CAMUser",

"RequestId": "ab10f09b-42a7-4dcf-a96a-a85a0feb54c7"

}

后续发现很多命令有问题，还是用 aws 吧

┌──(root㉿lll)-\[~\]

└─# cat.aws/config

\[default\]

region = ap-guangzhou

s3 =

endpoint\_url = https://cos.ap-guangzhou.myqcloud.com

┌──(root㉿lll)-\[~\]

└─# cat.aws/credentials

\[default\]

aws\_access\_key\_id = AKIDrGuURY3ThyLSMqDsEv1ecnGSsRP0m0krNNUWUgTvqr\_IitXJvmdelZq8dx5XEtt3

aws\_secret\_access\_key = q9wsCzWhmEp3E/DvDhld/MMiuDo8yTn0shcpM7wBB1o=

aws\_session\_token = ukN1JfAFyFEJbyw3cdc5TTdUzPBhknna27c03053a861748506b924176014da27MuPprHAIl3oB2kxiKigKZ3oP1nsJz1BPLiljMlfGOSIlVWWb9uk\_wtk1LKFaWPcJUJ44PxuPnc\_f3dRc26dfmvJnOxgtmzdfjeZMsyq0iUbwcO1Be0\_UlwREY9GPw4hvvUqdUkv6SJyb-6yVNoDA0hXnUBUgEKFRCGAJ7bs5\_8tq\_gok265jvp7Nes1NJ\_ZEedEff4Tzd5iXb6Ix1ZSyGe4Yy4TJEcs2ixHvSXsCpqNLNSCR1JDSOG66GrfAqO0bC-uvsVrwva49kxchxWa\_NR9cRaSoSumJE-N2siJ2pUZhDuqvNBUujkbHKo6Tqtg7Z8-mLjmfxZOY3HhJZOoR73Drqhpt6ILX4IcEuoNyOEQ9lkjg10lW\_OoCKQmv\_\_Iey6\_9IqWYfIVgOwChCYGzhD-TrEHmjGickxNXzG3qKEez8YTWc973G7UhBplySXuq0oziGVjfWOxQ3MqocCTsyONWyZHsIo9RdnsZNuzvMFyR6JBgc-2l1r2p5hNunusTalPtZWsi0nBj3Co0ZRDe\_OQqv6Xkbpn4eVgiryKLKwH4iTid2nCTBY9kDj0tS76\_U\_QVlLIK3zagu5xF3RR37Xy3BE\_29q-XkbX8S0nNM1wwUAVO6L\_zWUMUwaWu2UkDab1OXi-RV98a8N6bs9WCtA

先看下当前用户，这里报错是因为腾讯云 STS API 不是 S3 协议兼容的，而是走的 TC3-HMAC-SHA256 签名体系

┌──(root㉿lll)-\[~\]

└─# aws --endpoint-url https://sts.tencentcloudapi.com sts get-caller-identity

Unable to parse response (not well-formed (invalid token): line 1, column 0), invalid XML received. Further retries may succeed:

b'{"Response":{"Error":{"Code":"MissingParameter","Message":"The request header is missing a required common parameter \`X-TC-Action\`."},"RequestId":"6d5608bf-1a85-4a9c-8363-6395952d4191"}}'

可以用 tccli 看

┌──(root㉿lll)-\[~\]

└─# tccli sts GetCallerIdentity

{

"Arn": "qcs::sts:100026992078:federated-user/100043488407",

"AccountId": "100026992078",

"UserId": "100043488407:challenge\_01\_xkeb6gd9n2ee",

"PrincipalId": "100043488407",

"Type": "CAMUser",

"RequestId": "158c4139-4eeb-4c17-948a-ab394bd9b14f"

}

拿到 bucket 名字 （这个字段是根据 py 脚本回显来判断的）

看下该 bucket 下的所有 object，权限不够

┌──(root㉿lll)-\[~\]

└─# aws --endpoint-url https://xkeb6gd9n2ee-1313380398.cos.ap-guangzhou.myqcloud.com s3 ls

An error occurred (AccessDenied) when calling the ListBuckets operation: Access Denied.

看下该 bucket 的策略，COS 不支持 path-style 访问方式，必须用 virtual-hosted style，改下配置文件

┌──(root㉿lll)-\[~\]

└─# cat.aws/config

\[default\]

region = ap-guangzhou

s3 =

addressing\_style = virtual

┌──(root㉿lll)-\[~\]

└─# aws s3api get-bucket-policy --bucket 21cf8bc7bq56-1313380398 --endpoint-url http://cos.ap-guangzhou.myqcloud.com --output text | python3 -m json.tool

{

"Statement": \[

{

"Action": \[

"name/cos:PutBucketACL"

\],

"Condition": {

"ip\_not\_equal": {

"qcs:ip": \[

"43.138.212.54",

"172.16.0.22"

\]

}

},

"Effect": "Deny",

"Principal": {

"qcs": \[

"qcs::cam::uin/100026992078:uin/100043488407"

\]

},

"Resource": \[

"qcs::cos:ap-guangzhou:uid/1313380398:21cf8bc7bq56-1313380398/\*"

\],

"Sid": "costs-1757861762000000978297-46861-45"

}

\],

"version": "2.0"

}

有 PutBucketACL 权限，但限制了 ip，试了下 tccli cam 所有指令，有三个有权限，但都没用，aws s3api 也试了下，除了 get-bucket-policy 都不行

查看当前 STS 权限范围内的云主机实例列表

第一台机子的 ip 刚好是满足条件的，注意到其相关配置

说明这台机子是没有设密码且登录名是 root，这里注意目标实例在创建时没有设置密码且没有绑定 SSH 密钥，我们这里可以把密码重置

必须停止才能重置密码，但是这没权限停止，到这其实就不知道咋办了，后续看了狼组的 wp ，发现可以用腾讯云的 python sdk 来强制修改密码的。参考：

[

https://mp.weixin.qq.com/s?\_\_biz=MzIyMjkzMzY4Ng==&mid=2247511178&idx=1&sn=8d4d1ba961a2aee497a712ce2a82ff4c&chksm=e934b59ca1ccc328c9f64b2ece0b1fa719efb5f2e0f0561c06e7df3fc3922cca7e27ead9d73d&mpshare=1&scene=23&srcid=0919iN7MiVZCa0PkTOwoXQMP&sharer\_shareinfo=1b3be1b1bf770ffeae88c43a168d98b6&sharer\_shareinfo\_first=1b3be1b1bf770ffeae88c43a168d98b6#rd

](https://mp.weixin.qq.com/s?__biz=MzIyMjkzMzY4Ng==&mid=2247511178&idx=1&sn=8d4d1ba961a2aee497a712ce2a82ff4c&chksm=e934b59ca1ccc328c9f64b2ece0b1fa719efb5f2e0f0561c06e7df3fc3922cca7e27ead9d73d&mpshare=1&scene=23&srcid=0919iN7MiVZCa0PkTOwoXQMP&sharer_shareinfo=1b3be1b1bf770ffeae88c43a168d98b6&sharer_shareinfo_first=1b3be1b1bf770ffeae88c43a168d98b6#rd)

最后 putbucketacl 修改下 acl 策略即可（比赛结束没环境了，后续就 put 个 acl 上去就好了，应该没什么需要注意的）