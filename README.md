# tidbticloud
自己的tidb私有云
By jiangming rqy osdba


# 概述

在本次 Hackathon 中，我们支持 TiDB 和 TiKV 作为 ticloud 平台的后端，让 TiDB 和 TiKV 的用户也能享受到k8s平台带来的便利。

# 动机：
而对于每一个数据库用户而言，数据库备份/恢复也是一项刚需，对于企业而言，数据就是最基础最底层的资产，没有数据支撑，很多决策就缺乏强有力的依据。于企业而言，数据是资产但却很容易因为日常操作，机器损坏等各种原因导致数据的流失，很多数据的流失是不可逆的，可能会直接导致企业无法估量的损失。在此背景下，数据备份恢复是防范灾难于未然的强力手段，亦是保护企业重要资产的重要方式。据非官方统计，互联网头部企业中约有 11% 的成本分配在了数据备份管理上。


于每一个数据库用户而言，处理慢sql也是用户的日常工作。经常会发生tidb卡顿，在论坛提问题的用户很多遇到的就是这种情况。


mysql支撑起了当今互联网最赚钱的场景，在数据量越来越大的情况下mysql有点力不从心，所以才有了当下tidb的火爆现象。
但学习tidb成本较高，让当下的从业者望而却步，不断有用户通过论坛，群组等咨询tidb数据库的各种问题，我们把数据库团队的专业经验，嵌入到这个web应用里面。能相当于一个dba在管理应用。
做一个全面托管在云端的 SaaS 服务，把服务器一站式托管数据库的备份/恢复，支持 Time Travel （恢复到任意时间点），并且底层基于 S3 存储，存储成本极低。本地存在minio上。
简单的把tidb部署起来，部署在k8s上。让更多的用户参与使用，帮助用户tidb使用门槛高，成本高，专业性欠缺等会造成的使用难，费用贵等的基本问题。

当下的从业环境下，很多反馈在某些企业推广tidb遇到的最大问题是tidb部署问题和日常使用的各种，因其企业内部不具备专业的人员，无法独立完成部署，无法独立完成tidb的日常运维，直接导致tidb推广过程中的劝退。但对于我们能够专业使用tidb的人而言，使用tidb与使用mysql也是存在差异的，假设不具备专业经验的开发人员，是无法将mysql业务顺利迁移到tidb,更无法保障tidb数据库的稳定运行，影响tidb的用户体验。但其实，用k8s部署把细节隐藏后，就可以批量复制优化好的环境。

# 目标：
本项目解决这些问题
### 1.tidb数据库多数据库部署麻烦的难题。如果你在企业里面维护100个tidb就会发现tidb和mysql不一样，mysql一台机器就够了，而tidb稳定使用需要最少9台服务器。还需要haproy做代理。这种部署的复杂程度会劝退很多人。而且在企业环境中，dev，test01，test02，uat01，uat02，prod这几个流水线下来dba的工作其实很累。对tidb在公司推广来说无疑是一座大山。
### 2.部署完成后dev test01 test02 uat01 uat02 需要接入报警系统。这个对没有经验的人来说是噩梦。
### 3.部署完成后各个环境的tidb运行会遇到卡顿 表结构同步等问题。tidb其实对于烂sql来说特别不友好，tidb会无限重启oom。我们需要限制slow sql的执行时间。怎么样寻找慢sql并解决。我们引入了远程数据库支援系统。rustdesk。让原先几千元一天的数据库维护变成免费搞定。不增加企业成本。
### 4.以上就是我实际在企业中用tidb遇到的问题。在工作上我都通过命令行解决了。现在需要增加工作效率。所以做了这个。

# 动机：
我们的目标
过去做商业数据库的商业模式基本上就是收服务费，被保护的场景越重要，能收的服务费就越多。但是这个思路在“开源+云”时代发生了重大的变化：
就是帮助tidb在企业中推广应用。过去数据库是一个神秘的东西。公司会觉得他很重要。会付给dba很多薪资。生活中的确也是很重要的，一个核心数据库的宕机会让企业整个公司业务无法开展。整个必胜客收银都无法使用。而现在用tidb就能完全搞定。

### soft会变成一个特别普及的东西；软件开发也经历了3个阶段。付费软件--》共享软件（先使用后付费）--》免费软件（不要钱，黑盒不安全）--》开源软件（不要钱，大家一起修改）
### 开源数据库超过了闭源数据库，它的成熟度已经基本上能够满足大多数核心的业务场景需求，开源数据库用户变成了一个非常庞大的群体；
### k8s有一个好处，它把交付实现了标准化，并且使用的门槛降低了。当年 ToC 端出现了微信、支付宝等快速支付渠道，将支付门槛变得特别低，才有了爱奇艺这些订阅模式。我们现在数据库也可以先给人用，简单问题他自己解决。复杂问题交由tidb远程解决。疑难问题就交给专家解决。

所以，基于这三个前提，你就会找到一个新思路：找到用户旅程中的通用路径，先不管核心不核心，极致优化用户（开发者）体验，然后利用云的基础设施把成本做低，最后利用流量入口 + PLG 走病毒式传播的路线。Cloud 这个项目的关键在于——第一，企业级用户进入tidb的成本能不能找到一个低门槛， 比如 SaaS；第二，这个模式一定要特别轻，不能特别贵，必须得找到一个非常自动化的切入点。因为一旦涉及到人工提供服务，这个事情就废了；第三，这个东西的用户群体要足够广， Database 本身是一个使用群体特别广的东西，而在 Database 里其实有一些东西的使用群体也特别广，那就是备份恢复。高可用。

在 ToC 领域里，关于消费者有一个冲动消费的心理学，这需要有几个前提：第一，你马上就能理解这个东西是什么；第二，好像我一定需要；第三就是这东西得特便宜。这些都具备了，消费者就可能产生冲动消费的心理。所以，我们借用了 iCloud 换手机的概念，给大家制造一个错觉，这个东西我一定需要。因为大家都需要 iCloud ，我们就把这个概念平移到 DBA 领域。如果你是一个普通消费者，其实很容易就被这样的概念吸引。

挑战：
看起来不硬核，但工程度复杂
