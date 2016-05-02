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



##3.1 字符串（STRING）

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

##3.2列表（LIST）

  具体命令请参考第一章<http://sunhanbin.com/2016/03/01/Redis-In-Action-Chapter1/>

##3.3 集合（SET）

  具体命令请参考第一章<http://sunhanbin.com/2016/03/01/Redis-In-Action-Chapter1/>

##3.4 散列（HASH）

  具体命令请参考第一章<http://sunhanbin.com/2016/03/01/Redis-In-Action-Chapter1/>

  尽管有**HGETALL**存在，但HKEYS和HVALUES也是非常有用的：如果散列包含的值非常大，那么可以先使用**HKEYS**取出散列包含的所有键，再用**HGET**一个个地取出键对应的值，从而避免因为一次获取多个大的体积的值而导致服务器阻塞。


##3.5有序集合（ZSET）


