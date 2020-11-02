---
title: MySQL基础概念
date: 2020-11-01
tags:
 - MySQL
categories:
 -  backend
---



## 同步和半同步
> 同步：每次事务commit 后，不接受slave的ack
> 半同步:master会等slave 接收到的事务进入 enqueue队列的ack，再进入下一步



## Dbatman 分片
需要找准分片列
- update/insert 需要带分片列
- 不支持分片事务写，但支持事务读
- 直接更新分片列是不允许的

