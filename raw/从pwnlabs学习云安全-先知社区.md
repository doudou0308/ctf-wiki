---
title: "从pwnlabs学习云安全-先知社区"
source: "https://xz.aliyun.com/news/18050"
author:
published:
created: 2026-06-10
description: "先知社区是一个安全技术社区，旨在为安全技术研究人员提供一个自由、开放、平等的交流平台。"
tags:
  - "clippings"
---
---
title: "从pwnlabs学习云安全-先知社区"
source: "https://xz.aliyun.com/news/18050"
author: ""
published: ""
created: "2026-06-10T21:20:47+08:00"
description: "先知社区是一个安全技术社区，旨在为安全技术研究人员提供一个自由、开放、平等的交流平台。"
tags: [clippings]
---

# 从pwnlabs学习云安全-先知社区

[pwnedlabs](https://pwnedlabs.io/)

Identify the AWS Account ID from a Public S3 Bucket

Scenario

The ability to expose and leverage even the smallest oversights is a coveted skill. A global Logistics Company has reached out to our cybersecurity company for assistance and have provided the IP address of their website. Your objective? Start the engagement and use this IP address to identify their AWS account ID via a public S3 bucket so we can commence the process of enumeration.

靶机开启之后给了一个ip地址和AK/SK

![image.png](https://xz.aliyun.com/api/v2/files/22fbc7bc-f7dd-34b5-be9d-ae1a8473bc15)

这个AK/SK我没有找到有什么用处

最开始我刚开启靶机，试着ping这个ip地址来测试一下网络的连通性

结果就是死活ping不通

一次无意的curl 发现curl可以访问

这才意识到它可能ban掉了ping协议

nmap探测一手端口

80端口只是一个静态网站

不过网站的图片是从存储桶上面加载的

aws s3存储桶的命名规则是

<桶的名称>.s3.amazonaws.com

所以这个存储桶的名称就是

mega-big-tech

在浏览器上面访问这个存储桶，你会发现除了几张图片并没有什么东西

![](https://xzfile.aliyuncs.com/media/upload/picture/20260326201021-c2ebd8a7-290c-1.png)

同时你也可以使用aws cli 来访问这个s3存储桶

使用

aws s3 ls s3://mega-big-tech --no-sign-request --recursive

来匿名访问存储桶 并且递归显示出桶中的内容

![image.png](https://xz.aliyun.com/api/v2/files/499b0a38-35e5-32df-a241-888349c28606)

不过这个靶场只是让我们获取与这个s3存储桶关联的AWS账户的id

这里可以用\*\*

[

s3-account-search

](https://github.com/WeAreCloudar/s3-account-search)

\*\*这个工具来进行ID枚举

你可以在这里了解到这个工具的原理

[

Finding the Account ID of any public S3 bucket

](https://cloudar.be/awsblog/finding-the-account-id-of-any-public-s3-bucket/)

使用这个工具进行爆破的时候需要一个对目标桶具有

s3:GetObject

或者

s3:ListBucket

权限的角色的arn

如果你没有aws账户，你可以使用

pwnedlabs

提供的一个arn进行枚举

arn:aws:iam::427648302155:role/LeakyBucket

如果您有一个aws账户，你也可以自己配置一个角色来进行枚举

首先你需要创建一个用户

然后在策略里面新建两条策略

承担该角色的 IAM 用户将附加以下策略

{

"Version": "2012-10-17",

"Statement": {

"Effect": "Allow",

"Action": "sts:AssumeRole",

"Resource": "arn:aws:iam::<your aws account id>:role/<your role name>"

}

}

您的用户被允许承担的角色将附加以下策略，该策略允许

s3:GetObject

和

s3:ListBucket

访问存储桶的权限

mega-big-tech

{

"Version": "2012-10-17",

"Statement": \[

{

"Sid": "Enum",

"Effect": "Allow",

"Action": "s3:GetObject",

"Resource": "arn:aws:s3:::mega-big-tech/\*"

},

{

"Sid": "Enum1",

"Effect": "Allow",

"Action": "s3:ListBucket",

"Resource": "arn:aws:s3:::mega-big-tech"

}

\]

}

然后创建一个角色，将这两条测试附加上去，我这里分别是

rolepolicy

与

get-meth-big

随后你需要添加信任关系

{

"Version": "2012-10-17",

"Statement": \[

{

"Effect": "Allow",

"Principal": {

"AWS": "arn:aws:iam::<your aws account id>:user/s3enum"

},

"Action": "sts:AssumeRole"

}

\]

}

然后就可以使用这个角色的arn进行ID枚举了

等他完全爆破出来就行了

但是AWS Account ID并不是敏感信息

那么拿到这个信息有什么用呢？

接下来，来看下面这个靶场

Loot Public EBS Snapshots

Scenario

Huge Logistics, a titan in their industry, has invited you to simulate an "assume breach" scenario. They're handing you the keys to their kingdom - albeit, the basic AWS credentials of a fresh intern. Your mission, should you choose to accept it, is to navigate their intricate cloud maze, starting from this humble entry. Gain situational awareness, identify weak spots, and test the waters to see how far you can elevate your access. Can you navigate this digital labyrinth and prove that even the smallest breach can pose significant threats? The challenge is set. The game is on.

靶机只给了一个AK/SK

配置到aws cli中

执行

aws sts get-caller-identity

来查看当前用户信息。类似于linux中的

whoami

执行

aws iam list-attached-user-policies --user-name intern

来列出与intern关联的iam策略

查看该策略

aws iam get-policy --policy-arn arn:aws:iam::104506445608:policy/PublicSnapper

当前生效的策略版本是v9

●

PolicyName

: PublicSnapper - 策略的名称。

●

PolicyId

: ANPARQVIRZ4UD6B2PNSLD - 策略的唯一标识符。

●

Arn

: arn:aws:iam::104506445608:policy/PublicSnapper - 策略的 ARN。

●

Path

: / - 策略的路径（默认路径）。

●

DefaultVersionId

: v9 - 当前生效的策略版本是 v9，说明这个策略可能被更新过多次（从 v1 到 v9）。

●

AttachmentCount

: 1 - 该策略当前附加到了 1 个实体（这里是用户 intern）。

●

PermissionsBoundaryUsageCount

: 0 - 没有使用权限边界。

●

IsAttachable

: true - 表示这是一个托管策略，可以附加到用户、组或角色。

●

CreateDate

: 2023-06-10T22:33:41+00:00 - 策略创建时间（2023年6月10日）。

●

UpdateDate

: 2024-01-15T23:47:11+00:00 - 最后一次更新时间（2024年1月15日）。

●

Tags

: \[\] - 没有附加标签。

查看策略的具体内容

aws iam get-policy-version --policy-arn arn:aws:iam::104506445608:policy/PublicSnapper --version-id v9

拥有

ec2:DescribeSnapshotAttribute

与

ec2:DescribeSnapshots

这两个权限。这个两个权限都可以查看快照信息，不过第一个是针对于特定快照的，并且可以查看具体信息。第二个是针对所有快照的，只能查看基本信息。

列出当前用户拥有的快照

aws ec2 describe-snapshots --owner-ids self --region us-east-1

查看快照的

createVolumePermission

属性

createVolumePermission

这个属性决定了谁可以用这个快照来创建EBS卷

EBS（Elastic Block Store）

是

AWS 提供的持久性块存储服务

，用于给 EC2 实例提供存储。简单来说，EBS 卷就像是一块

云端的硬盘

，可以挂载到 EC2 服务器上，并在服务器关机后仍然保留数据。

{"Group":"all"}

表明所有用户都可以用这个夸张进行创建EBS卷

然后我们就可以在aws控制台 进入ec2板块->快照 搜索快照

从快照复制卷

创建好的卷需要记住这个可用区

![](https://xzfile.aliyuncs.com/media/upload/picture/20260326201021-c34d5f5f-290c-1.png)

然后创建一个实例镜像啥的选免费套餐就行

记得创建一个密钥对

这里的可用区一定要和卷的可用区一样

![image.png](https://xz.aliyun.com/api/v2/files/1c7930a9-4682-3a64-b327-a7599ae31fc5)

其他的默认，创建实例

不能直接远程登录root

使用他要求的

ec2-user

就行了

然后把刚才的卷挂载到服务器上面

![](https://xzfile.aliyuncs.com/media/upload/picture/20260326201022-c3a60e6f-290c-1.png)

![image.png](https://xz.aliyun.com/api/v2/files/c40636bd-3d5f-3e60-a57c-4e27774c90fa)

列出所有块设备

将xvdb挂载一下

mount /dev/xvdb1 /root/pwnedlabs/

在

/root/pwnedlabs/home/intern/practice\_files

目录下找到了一个

s3\_download\_file.php

s3\_download\_file.php

中包含了一个桶名称为

ecorp-client-data

的AK/SK

在aws cli配置好aksk之后，就可以访问存储桶了

Plunder Public RDS Snapshots

Scenario

Huge Logistics, a global logistics leader, has enlisted your team's expertise for an external security review of their cloud infrastructure. Starting with the provided AWS Account ID, your task is to uncover security flaws within their AWS environment and demonstrate the potential risks they pose. Every finding will bolster their defense against future threats.

靶机只给了一个AWS account ID

看到这个AWS account ID。经过前面两个靶场的经验。我去找了这个ID的EBS快照。但是没找到东西😂

然后又去找了找RDS快照，找到了些东西

下面这是一些AWS的快照类型

AWS几个主要的快照

1\. Amazon EBS 快照（Elastic Block Store Snapshots）

EBS 快照用于

备份 Amazon EBS 卷

，主要有以下类型：

●

手动快照（Manual Snapshots）

○

由用户手动创建的 EBS 卷备份。

●

自动快照（Automated Snapshots）

○

由 AWS Backup 或生命周期策略（Lifecycle Policies）自动创建。

●

增量快照（Incremental Snapshots）

○

AWS 只存储自上次快照以来更改的数据，以节省存储成本。

●

共享快照（Shared Snapshots）

○

你可以与

其他 AWS 账户

共享快照，但加密的快照必须使用相同的 KMS 密钥。

●

公共快照（Public Snapshots）

○

任何 AWS 账户都可以访问的快照。

2\. Amazon RDS 快照（Relational Database Service Snapshots）

RDS 快照用于

备份 AWS RDS 数据库

，主要包括：

●

手动 RDS 快照（Manual DB Snapshots）

○

由用户手动创建的数据库备份，可以保留无限时间。

●

自动 RDS 快照（Automated DB Snapshots）

○

由 AWS 维护，并根据保留策略自动删除。

●

共享 RDS 快照（Shared DB Snapshots）

○

允许你共享数据库快照给其他 AWS 账户。

●

公共 RDS 快照（Public DB Snapshots）

○

任何 AWS 账户都可以访问的数据库快照。

●

跨区域复制 RDS 快照（Cross-Region DB Snapshots）

○

允许你将快照复制到其他 AWS 区域，以提高容灾能力。

3\. Amazon Aurora 快照（Aurora Snapshots）

Aurora 数据库集群也支持快照，与 RDS 类似，包含：

●

手动 Aurora 快照（Manual Aurora Snapshots）

●

自动 Aurora 快照（Automated Aurora Snapshots）

●

共享 Aurora 快照（Shared Aurora Snapshots）

●

公共 Aurora 快照（Public Aurora Snapshots）

●

跨区域复制 Aurora 快照（Cross-Region Aurora Snapshots）

4\. Amazon Redshift 快照

Redshift 是 AWS 的

数据仓库

，它的快照类型包括：

●

手动快照（Manual Snapshots）

●

自动快照（Automated Snapshots）

●

共享快照（Shared Snapshots）

●

跨区域复制快照（Cross-Region Snapshots）

5\. Amazon FSx 快照

Amazon FSx 主要用于

托管文件系统

，支持以下快照：

●

FSx for Windows File Server Snapshots

●

FSx for Lustre Snapshots

6\. Amazon Backup 管理的快照

AWS Backup 提供

集中化的备份管理

，支持：

●

EBS 卷快照

●

RDS/Aurora 数据库快照

●

DynamoDB 备份

●

EFS 备份

●

FSx 备份

●

Storage Gateway 备份

这个AWS account ID 下面有一个RDS的公共快照

![](https://xzfile.aliyuncs.com/media/upload/picture/20260326201022-c3f75737-290c-1.png)

还原快照

![](https://xzfile.aliyuncs.com/media/upload/picture/20260326201023-c44ce5ee-290c-1.png)

随便起一个名称

![](https://xzfile.aliyuncs.com/media/upload/picture/20260326201024-c49f9921-290c-1.png)

新建一个VPC，然后设置公开访问

![](https://xzfile.aliyuncs.com/media/upload/picture/20260326201024-c506f0d6-290c-1.png)

然后换源快照，时间可能稍长一些

![](https://xzfile.aliyuncs.com/media/upload/picture/20260326201025-c55ebe85-290c-1.png)

设置EC2连接

![](https://xzfile.aliyuncs.com/media/upload/picture/20260326201025-c5b72ed7-290c-1.png)

创建一个EC2

![](https://xzfile.aliyuncs.com/media/upload/picture/20260326201026-c61505ef-290c-1.png)

这里的VPC要与数据库在同一个VPC一样

![](https://xzfile.aliyuncs.com/media/upload/picture/20260326201026-c6638e9a-290c-1.png)

然后就可以将数据库连接到EC2上面了

![](https://xzfile.aliyuncs.com/media/upload/picture/20260326201027-c6bf3c00-290c-1.png)

然后修改数据库密码

![](https://xzfile.aliyuncs.com/media/upload/picture/20260326201028-c720a09f-290c-1.png)

![](https://xzfile.aliyuncs.com/media/upload/picture/20260326201028-c77b9f96-290c-1.png)

使用

psql -h <endpoint> -U postgres

连接到数据库

<endpoint>

替换为

![image.png](https://xz.aliyun.com/api/v2/files/91d0a844-d804-34de-8433-1604894ebc8c)

EC2上面没有

psql

使用

apt install -y postgresql-client

命令安装一个

![](https://xzfile.aliyuncs.com/media/upload/picture/20260326201029-c7cd9b37-290c-1.png)

使用

l

列出数据库

![](https://xzfile.aliyuncs.com/media/upload/picture/20260326201029-c8182e3a-290c-1.png)

使用

c 数据库名

来使用数据库

![](https://xzfile.aliyuncs.com/media/upload/picture/20260326201030-c85ed778-290c-1.png)

使用

dt

列出数据表

![](https://xzfile.aliyuncs.com/media/upload/picture/20260326201030-c8a302f4-290c-1.png)

![](https://xzfile.aliyuncs.com/media/upload/picture/20260326201031-c8e400ce-290c-1.png)

AWS S3 Enumeration Basics

Scenario

It's your first day on the red team, and you've been tasked with examining a website that was found in a phished employee's bookmarks. Check it out and see where it leads! In scope is the company's infrastructure, including cloud services.

开启后给了一个地址

![](https://xzfile.aliyuncs.com/media/upload/picture/20260326201031-c92db0e0-290c-1.png)

只是一个静态页面

![](https://xzfile.aliyuncs.com/media/upload/picture/20260326201033-ca106727-290c-1.png)

网站中的一些图片是从

aws s3

存储桶中加载的

![](https://xzfile.aliyuncs.com/media/upload/picture/20260326201033-ca778a96-290c-1.png)

相比前几个靶场 这个连接看起来怪怪的

前几关s3存储桶的格式都是

<桶的名称>.s3.amazonaws.com

但是这个的地址形式竟然是

s3.amazonaws.con/<桶的名称>/

后来了解到 这种形式的老版本的 路径方式访问存储桶。 现在大多用的都是第一个 虚拟托管式 访问存储桶

匿名访问存储桶可以列出一些目录

![](https://xzfile.aliyuncs.com/media/upload/picture/20260326201034-cabace71-290c-1.png)

递归列出目录内容却失败

![](https://xzfile.aliyuncs.com/media/upload/picture/20260326201034-cb01db2a-290c-1.png)

只能匿名访问

shared

与

static

目录

![](https://xzfile.aliyuncs.com/media/upload/picture/20260326201035-cb468e12-290c-1.png)

shared目录汇总有一个zip，下载zip之后。里面有一个poweroffshell脚本。脚本中有一个AK/SK

![](https://xzfile.aliyuncs.com/media/upload/picture/20260326201035-cb94c5b5-290c-1.png)

aws configure

配置好AK/SK

然后就可以递归列出目录下的文件了

![](https://xzfile.aliyuncs.com/media/upload/picture/20260326201036-cbe6e59b-290c-1.png)

注意到

admin

目录下有一个

flag.txt

但是没有权限下载

![](https://xzfile.aliyuncs.com/media/upload/picture/20260326201036-cc2b764b-290c-1.png)

然后在回到刚才的那个powershell脚本上

刚那个powershell脚本会从

export.xml

中读取 密钥数据

然后

migration-files

目录下刚好有一个

test-export.xml

，先试试读取这个文件。

OK, 这个文件可以下载

然后里面还有一个

AWS IT Admin

的

AK/SK

将这个

AK/SK

配置到awscli中

然后就能读取到

flag.txt

了

![](https://xzfile.aliyuncs.com/media/upload/picture/20260326201037-cc71315f-290c-1.png)

SQS and Lambda SQL Injection

Scenario

During a red team engagement you discovered hardcoded AWS credentials and reference to the region eu-north-1. What cloud infrastructure and sensitive information might be accessible using these details? The red team engagement is ending soon and another impactful finding would complete the report!

靶机开启后 给了一个区域地址, AK/SK

![](https://xzfile.aliyuncs.com/media/upload/picture/20260326201037-ccc36ae7-290c-1.png)

将这些信息配置到

[

aws-enumerator

](https://github.com/shabarkin/aws-enumerator)

中, 然后枚举一下有哪些服务，权限。

这个aws-enumerator我第一次按照文档说明的方式安装的时候。安装好久都没成功安装

后来我是将仓库clone到本地然后在本地编译了一下。。。

配置了

AK/SK

信息之后会在当前目录下生成一个

.env

文件存储相关信息

然后使用

aws-enumerator enum -services all

来枚举服务

![](https://xzfile.aliyuncs.com/media/upload/picture/20260326201038-cd0eeecf-290c-1.png)

![](https://xzfile.aliyuncs.com/media/upload/picture/20260326201038-cd55e796-290c-1.png)

用

aws-enumerator dump -services SQS,LAMBDA,STS

来枚举相关权限

![](https://xzfile.aliyuncs.com/media/upload/picture/20260326201039-cdb9881e-290c-1.png)

aws configure

配置好

AK/SK

之后使用

aws lambda list-functions

来列出Lambda函数信息

尝试获取这个函数的内容

aws lambda get-function --function-name huge-logistics-stock

尝试调用这个lambda函数

aws lambda invoke --function-name huge-logistics-stock

传入一个

\--payload

试试

这里如果你使用的是AWS CLI 的版本2 你可能会遇到这个问题, 出现

Invalid base64

的报错

![](https://xzfile.aliyuncs.com/media/upload/picture/20260326201039-ce07dc33-290c-1.png)

这里需要加上

\--cli-binary-format raw-in-base64-out

参数对有效载荷进行编码就可以了

![](https://xzfile.aliyuncs.com/media/upload/picture/20260326201040-ce5bf419-290c-1.png)

这里仍然是

Invalid event parameter!

的报错

使用burp的参数名字典进行参数名枚举

我的枚举脚本

找到了一个名为

DESC

的参数

![](https://xzfile.aliyuncs.com/media/upload/picture/20260326201040-ceb4e179-290c-1.png)

但是它好像是要一个

trackingID

再来看

SQS

服务

使用

aws sqs list-queues

列出所有的队列URL

使用

aws sqs receive-messsage --queue-url https://sqs.eu-north-1.amazonaws.com/254859366442/huge-analytics --message-attribute-names All

查看队列中的消息

多运行几次这个命令

可以看到不同的客户端名称 包括

Goole inc. EY 、Adidas

像SQS 对列中发送一个这样格式的消息，对

trackingID

进行修改。 至于为什么要修改

trackingID

稍后说明

然后运行

aws lambda invoke --cli-binary-format raw-in-base64-out --function-name huge-logistics-stock --payload "{"DESC":"HLT1337"}" output && cat output

调用lambda 函数

你可能会遇到这种情况

![](https://xzfile.aliyuncs.com/media/upload/picture/20260326201041-cf10b0ec-290c-1.png)

这是正常现象 。 多调用几次lambda函数即可

![](https://xzfile.aliyuncs.com/media/upload/picture/20260326201042-cf65e4d8-290c-1.png)

他返回了客户端名称为

Goole Inc.

的所有的纪录

在发一个消息 这次试着将 clientName 字段改一下。 这里我将clientName 字段改成Yliken

这次返回了一个空

![](https://xzfile.aliyuncs.com/media/upload/picture/20260326201042-cfbe00a1-290c-1.png)

所有这个lambda函数可能是查询出某个clientName的所有记录

再向队列中发一条消息

这次在clientName处插入一个

"

号

![](https://xzfile.aliyuncs.com/media/upload/picture/20260326201043-d0131d31-290c-1.png)

出现了

DB error

这就很明显了吧😎

用一个脚本进行sql注入

然后执行

./sql.sh "union select 1,2,3,4; -- a"

手动爆字段 与回显 这里我已经事先字段是 4 个字段了

![](https://xzfile.aliyuncs.com/media/upload/picture/20260326201043-d0788c9e-290c-1.png)

这里1,2,3,4都是回显 我第一次做的时候 只用了4来回显。 现在测试了一下用1 回显

然后就是查表名

![](https://xzfile.aliyuncs.com/media/upload/picture/20260326201044-d0eb16f8-290c-1.png)

查字段名

![](https://xzfile.aliyuncs.com/media/upload/picture/20260326201045-d1565ad5-290c-1.png)

查数据

![](https://xzfile.aliyuncs.com/media/upload/picture/20260326201046-d1ecc681-290c-1.png)

AWS 中的SQS 与 Lambda

AWS Lambda

是Amazon退出的一个无服务器函数计算服务, 你只需要编写代码并上传 Lambda 就会安装代码 逻辑自动进行计算。

SQS(Simple Queue Service)

是Amazon推出的一个完全托管的消息队列服务。

举个例子

一个用户在 电商网站下单之后 服务器可能要经过 应用服务器或许要进行很多更新数据库的操作。但是如果网站的访问量很大的话。 一但某个地方出问题，或者程序处理订单的时间需要很长。导致用户等待结果的时间非常长 导致 用户体验不好。

但是如果在这中间加一个

消息队列

用户下单之后信息 都进入到队列中 ，然后后续的各个操作从队列中取出 信息进行处理。虽然这些操作都不是实时的, 但是能保证这些操作最终都能被执行

"

API网关 -> SQS -> Lambda->DB

" 这种 架构在注重可扩展性与容错性的的系统中被广泛采用

如果我们构造了一个恶意的语句并将其发送到SQS消息队列中, 比如

' select database(); -- a

这可能不会立刻出现SQL注入漏洞

但是当后面某些函数需要对这些数据进行处理并与数据库进行交互的时候,那么这个时候 就可能会导致二次注入

![](https://xzfile.aliyuncs.com/media/upload/picture/20260326201046-d23ebf63-290c-1.png)

在这个Labs 当中. 我们将

Client、Weight、 trackingID

信息发送到消息队列中。 然后

Lambda

服务 执行类似

select \* from TrackingData where clientName = "{}".format(clientName)

的语句 从数据库中查询数据。