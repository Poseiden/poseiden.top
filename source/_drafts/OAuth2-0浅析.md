---
title: OAuth2.0浅析
tags:
---
### 缘起
不知道朋友们是不是跟我有一样的疑惑，既然是授权服务器给予客户端一个授权码，客户端又用授权码再向授权服务器申请一个令牌。那为什么在用户选择同意授权之后，授权服务器不直接给客户端一个令牌呢？反而需要一个“授权码”来做一次翻译？多一次交互的成本。在这方面，看起来还不如客户端授权模式来的直接。
说到这，就不得不说OAuth中的Auth了。大家都知道，Auth是Authorization的缩写，即授权。
### Authorization Code Grant
### Client Credentials Grant
### Ref
