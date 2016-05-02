---
layout: post
title: 《Redis实战》第三章、Redis命令
categories: [Redis]
description: NoSQL,Redis
keywords: NoSQL,Redis
autotoc: true
comments: true
---

本文是《Redis实战》第三章节学习笔记。

本文讲述内容的完整代码实例见 <https://github.com/sunailian/TouchHigh>。



##3.1 字符串

字符串有3中类型的值：
- 字节串(byte string)
- 整数
- 浮点数

Redis对字符串的自增和自减命令：

*命令* |  *用例和描述*  
  ------------|----------------
  INCR | INCR key-name -----> 将键存储的值加1
  DECR | DECR key-name -----> 将键存储的值减1
  INCRBY | INCRBY key-name amount -----> 将键存储的值加上整数amount
  DECRBY | DECRBY key-name amount -----> 将键存储的值减去整数amount
  INCRBYFLOAT | INCRBYFLOAT key-name amount -----> 将键存储的值加上浮点数amount，适用于redis2.6以上版本

  **如果用户对一个不存在的键或者一个保存了空串的键执行自增或者自减操作，那么Redis在操作时会将这个键的值当做是0来处理。**

  ## 3.2列表

  具体命令请参考第一章<http://sunhanbin.com/2016/03/01/Redis-In-Action-Chapter1/>

