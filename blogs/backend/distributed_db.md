---
title: MySQL分布式事务实现原理
date: 2020-11-01
tags:
 - MySQL
 - 分布式
categories:
 -  backend
---


# MySQL分布式事务实现原理

## 概述
- TCC(Try-Confirm-Cancel)事务，在第一阶段尝试预留变更需要的所有资源（prepare），根据第一阶段的结果，第二阶段对预留的资源进行操作（提交）或者释放预留的资源（回滚）。
- SAGA事务，每个参与者都定义一个正向行为（变更）和一个反向行为（补偿），分布式事务按照既定顺序执行正向行为直到全部成功（提交），如果中间发生错误，则逆序执行对应的反向行为（回滚）。

## 架构

## 流程

0. 开启事务，获取唯一事务id，流转到rpc中

1. SQL是否是update/insert?

1.1 否：直接执行

1.2 是：先执行select * for update 锁住记录，where语法需要构造，构成beforeImage

1.3 处理语句，只执行，不进行提交

1.4 select * .. where pk = ? and sharding_key= ? 根据id搜索出结果afterImage

1.5 保存image

2 构造全局锁 （db_table_pk_shardkey）

2.1 注册全局锁，
2.1.1 为何会冲突？因为下一步马上就是提交，提交之后锁失效

2.2 注册成功后返回分支事务id，写入事务表

3. 执行下一节点，执行失败前面陆续回滚



## 问题
可能会脏读，因为前一个节点的数据可能将会回滚。
解决部分方案：使用cas，尽量带上原值进行update