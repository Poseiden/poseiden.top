---
title: Let's Encrypt实践指北
date: 2020-02-29 11:36:44
tags:
---

## 初衷
一直以来，对于HTTPS证书的概念都含糊不清，似懂非懂。原因是自己之前比较懒，对于一些需要前置条件（买域名买证书等）才能玩的东西总是积极不起来（对！一定是穷）。而最近刚好有个项目需要配置HTTPS，也购买了域名（其实实践时自己还是买了域名），尤其是知道了本文要介绍的“神器” —— ***Let's Encrypt*** 。大大简化了学习成本和时间。所以趁着一些碎片化的时间，研究了下证书的一些基本概念以及使用，总结下来，以供参考。另外还有一点是，在我查找一些相关文档的过程中发现一个问题，就是由于这方面知识的时效性很差，出现很多信息不对等的情况，所以我将参考过的所有官网文档链接贴在了最后，方便大家在看到这篇文章时，根据链接查看最新官方支持情况。
(**Let's Encrypt** 以下简称 **“LE”**)

<!--more-->

## Let's Encrypt

### 不支持IP绑定
首先说明的是，本来基于成本考虑，是没有打算另购买一个域名来实践HTTPS的，因为市面上的一些主流证书都可以既支持域名，又可以支持公有IP。但是由LE官方论坛得知，目前只支持域名，并也没有计划支持公有IP。所以我就打消了这个念头，转而在阿里云上单独购买了一个域名。

### 证书类型
以下介绍几个关于证书类型的基本概念。
已知的LE现在支持三种证书类型。分别是 **单域名证书**，**SAN证书**和**Wildcard证书**。单域名证书，顾名思义，此证书只包含一个域名，属于基本类型。SAN证书，一张证书可以包含多个域名，早期用于多个子域名申请同一张证书的情况。经实践得知，此种证书在使用客户端申请时最大的弊端需要一次性写出所有的域名，对于后期扩展不太方便。最后一种是通配符证书，是本文详细介绍的对象。此种证书类型是LE后期支持的，使用起来极大方便了小型开发团队和个人开发者。比如针对`.example.com`这个域名，申请通配符证书（表达式为`*.example.com`）后，凡是基于其的子域名，都可以使用这个证书。但为了支持此特性，用于申请证书的客户端也必须要支持ACME的V2版本。（官方推荐的Cerbot客户端在0.22版本后）

需要注意的是，无论哪种证书，根据LE的最新的中文官方文档（2019年2月24日最后更新）所示，单张证书下最多可包含100个子域名，而每个注册域名（顶级域名）的证书数量是50张/每周，综上所述，每周可为5000个不同的子域名申请证书，且在2019年三月后，续期证书也算入域名证书数量内，对于个人或第三方独立开发者的正常使用而言，这个支持量级是足够的。

## ACME 客户端 —— Cerbot
搞清了关于证书方面的知识，那么我们接下来看看如何实践。在客户端方面，LE支持很多种不同的证书申请客户端，官方推荐的为Certbot，但值得注意的是，无论选择哪种客户端，都必须支持 **ACME的v2** 版本，因为从2019年的11月开始，LE将 **停止通过ACMEv1进行账号注册**，计划于2020年的6月开始将停止新域名的验证。

### Certbot 的选择
个人建议在实践时使用更为推荐的certbot-auto客户端，在官方的解释中，certbot-auto相当于certbot的wrapper，使用它能自动选择最新版本的cerbot，对已有cerbot进行升级等操作。并且因为certbot运行时需要用到python环境，所以对应的依赖也能自动装载到python的虚拟环境中。

### Certbot 插件选择
使用Certbot主要分两部分，一部分为申请获取证书，另一部分为在基础设置上安装证书。而Cerbot本身支持很多插件来简化这些操作。详情见下表：

|  Plugin     | Certificate | Install |
| :------     | :--------:  | :---:   |
| apache      | ✅          | ✅      |
| nginx       | ✅          | ✅      |
| webroot     | ✅          | ❌      |
| standalone  | ✅          | ❌      |
| DNS plugins | ✅          | ❌      |
| manual      | ✅          |  ❌     |


在申请证书的过程中，LE需要对该域名的所有权进行验证，而以上几个插件都支持了 http-01 或 dns-01中的一种，亦或是同时支持两种。不同的验证方式会有不同的操作，这个后面会说。

## 实践

好了，上面啰啰嗦嗦说了这么多，下面可以进入到实战环节了。我们以申请通配符证书为例。

1. 首先安装certbot-auto
    ```bash 
    cd ~
    wget https://dl.eff.org/certbot-auto
    sudo mv certbot-auto /usr/local/bin/certbot-auto
    sudo chown root /usr/local/bin/certbot-auto
    sudo chmod 0755 /usr/local/bin/certbot-auto
    /usr/local/bin/certbot-auto --help
    ```

2. 获取证书
    这里背景是这样的，由于我们需要申请通配符证书，LE官方FAQ指出只能通过 `dns-01` 的方式来验证。插件选择 `manual`,表示手动方式来配置。所以就有了以下这条命令。
    ```bash
    certbot-auto certonly  
    \ -d *.your_domain.com --manual --preferred-challenges dns 
    \ --server https://acme-v02.api.letsencrypt.org/directory
    ```
    
    这里几个参数着重说一下：
    - **certonly**: 表示使用certbot只用来申请获取证书，而不做安装操作。
    - **-d**: 域名。这里注意，通配符证书一定配置为 `*.domain.com`，而不是 `domain.com`
    - **--manual**: 手动模式，理论上也可以选择 `DNS plugins`
    - **--preferred-challenges**: 虽然manual模式下是同时支持两种验证方式的，而通配符证书需要采用 `dns-01` 验证方式
    - **--server**: ACME v2 验证使用的具体地址

    </br>

    返回的命令行输出如下：
    </br>
    ```bash
    Saving debug log to /var/log/letsencrypt/letsencrypt.log
    Plugins selected: Authenticator manual, Installer None
    Enter email address (used for urgent renewal and security notices) (Enter 'c' to
    cancel): your_email@gmail.com

    -------------------------------------------------------------------------------
    Please read the Terms of Service at
    https://letsencrypt.org/documents/LE-SA-v1.2-November-15-2017.pdf. You must
    agree in order to register with the ACME server at
    https://acme-v02.api.letsencrypt.org/directory
    -------------------------------------------------------------------------------
    (A)gree/(C)ancel: A

    Plugins selected: Authenticator manual, Installer None
    Obtaining a new certificate
    Performing the following challenges:
    dns-01 challenge for your_domain.com

    -------------------------------------------------------------------------------
    NOTE: The IP of this machine will be publicly logged as having requested this
    certificate. If you're running certbot in manual mode on a machine that is not
    your server, please ensure you're okay with that.

    Are you OK with your IP being logged?
    -------------------------------------------------------------------------------
    (Y)es/(N)o: y
    ```
    从这里开始需要一些交互：
    - 第一个：输入联系人的email，方便以后接受更新证书提醒和安全提示的
    - 第二个：同意条款
    - 第三个：记录此IP为申请证书的机器
    </br>

3.  DNS配置
    上步之后，命令行输出如下：
    ```bash
    -------------------------------------------------------------------------------
    Please deploy a DNS TXT record under the name
    _acme-challenge.your_domain.com with the following value:

    `一串base64编码`

    Before continuing, verify the record is deployed.
    -------------------------------------------------------------------------------
    Press Enter to Continue
    ```
    这时不要着急继续，按照上述提示，需要去你的DNS服务提供商那里手动配置一条记录，用于验证你对此域名的所有权。以Azure为例，如下图。

    ![](https://poseiden-blog.oss-cn-beijing.aliyuncs.com/azure_dns.jpg)

    配置好之后，过一分钟左右，利用dig命令查询一下是否生效：
    ```bash
    $ dig  -t txt  _acme-challenge.your_domain.com @8.8.8.8    

    ;; OPT PSEUDOSECTION:
    ; EDNS: version: 0, flags:; udp: 512
    ;; QUESTION SECTION:
    ;_acme-challenge.archguard.org.        IN      TXT

    ;; ANSWER SECTION:
    _acme-challenge.archguard.org. 599 IN  TXT     "刚才那串base64编码"
    ```
    这里一定注意,有`ANSWER SECTION`才算成功，我第一次配置错了没有出来这个，但也没有注意到，于是敲回车键就挂掉了。不过大家在实践中如果挂掉了也不要担心，重新执行命令即可。
    </br>
4. 成功
   网络没什么问题的话这步就应该已经成功了，输出的信息会提示你证书生成的所在位置。不出意外的话应该在 `/etc/letsencrypt/archive/your_domain.com` 下。这里值得注意的是，LE申请的证书有效期一般都是为三个月，所以到期后需要再次申请，网上相关自动化工具一抓一大把，我就不在这里赘述了。如果遇到问题，可以继续探讨。

## Ref
- [Letsencrypt - FAQ (中英文)](https://letsencrypt.org/docs/faq/)
- [Letsencrypt - Rate Limit (中英文)](https://letsencrypt.org/docs/rate-limits/)
- [Letsencrypt - Challenge Types (中英文)](https://letsencrypt.org/docs/challenge-types/)
- [Letsencrypt - End of Life Plan for ACMEv1](https://community.letsencrypt.org/t/end-of-life-plan-for-acmev1/88430)
- [Certbot - Certbot-auto](https://certbot.eff.org/docs/install.html#certbot-auto)
- [Certbot - Getting certificates and plugins](https://certbot.eff.org/docs/using.html#getting-certificates-and-choosing-plugins)
