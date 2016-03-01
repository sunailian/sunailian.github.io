---
layout: post
title: 《Redis实战》第一章、初始Redis
categories: [Redis]
description: NoSQL,Redis
keywords: NoSQL,Redis
autotoc: true
comments: true
---

本文是《Redis实战》第一章节学习笔记。

本文讲述内容的完整代码实例见 <https://github.com/sunailian/TouchHigh>。

##一、初识Redis

###1、附件特性

  Redis拥有两种不同形式的持久化方法。他们都可以用小而紧凑的格式将存储在内存中的数据写入硬盘：

  - 时间点转储。转储操作既可以在“指定时间段内有指定数量的写操作执行”这一条件被满足时执行，又可以通过调用两条转储到硬盘(dump-to-disk)命令中的任何一条来执行；

  - 将所有修改了数据库的命令都写入一个只追加(append-only)文件里面，用户可以根据数据的重要程度，将只追加写入设置为从不同步(sync)、每秒同步一次或者没写一个命令就同步一次。