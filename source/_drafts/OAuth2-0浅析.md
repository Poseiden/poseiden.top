---
title: OAuth2.0浅析
tags:
---
## 缘起
不知道朋友们是不是跟我有一样的疑惑，既然是授权服务器给予客户端一个授权码，客户端又用授权码再向授权服务器申请一个令牌。那为什么在用户选择同意授权之后，授权服务器不直接给客户端一个令牌呢？反而需要一个“授权码”来做一次翻译？多一次交互的成本。在这方面，看起来还不如客户端授权模式来的直接。
说到这，就不得不说OAuth中的Auth了。大家都知道，Auth是Authorization的缩写，即授权。

<!--more-->
## OAuth
### Authorization Code Grant
### Client Credentials Grant
## Ref
- [理解OAuth 2.0](https://www.ruanyifeng.com/blog/2014/05/oauth_2_0.html)
- [OAuth2.0入门（一）—— 基本概念详解和图文并茂讲解四种授权类型](https://blog.csdn.net/qq_37771475/article/details/103288957)
