---
title: Apollo之GraphQL订阅
tags: graphql
---

GraphQL 的操作类型除了*query*,  *mutation*外，还有订阅 *subscription*！

GraphQL 订阅是一种将数据从服务器推送到选择侦听服务器实时消息的客户端的方式。订阅需要服务器端的支持，在这里只讨论Apollo Client端的配置与使用；

