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


## 日志
| 名字 | 作用 | 发生时间 | 相关参数 |
| ---- | ---- | ---- | ---- |
| redo | 确保事务持久性，脏页未写入磁盘就发生重启，记录了物理信息，页的修改 | 事务开始时，每次执行sql都会进行记录，不需要提交 | innodb_log_file_size重做日志大小,Innodb_log_buffer日志缓冲，每次执行会先写入buffer，每次commit必定刷新到日志文件 or buffer不足 or master 每秒刷新 | 
| undo | 提供MVCC多版本并发控制读，保存了事务前一个版本的snapshot，用于实现隔离性，放的是逻辑日志，同样也会异步清理，一般只保存上个事务版本 | 需要独立的表空间 | 
| binlog | 也是逻辑日志，用于主从复制，只包括增删改，并且可以用于时间点的还原 | 事务提交的时候将sql都刷入 | 二阶段提交，commit时需要先写redo log 再写 binlog才算真正成功 | 
