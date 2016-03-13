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

#一、初识Redis

##1、附件特性

  Redis拥有两种不同形式的持久化方法。他们都可以用小而紧凑的格式将存储在内存中的数据写入硬盘：

  - 时间点转储。转储操作既可以在“指定时间段内有指定数量的写操作执行”这一条件被满足时执行，又可以通过调用两条转储到硬盘(dump-to-disk)命令中的任何一条来执行；

  - 将所有修改了数据库的命令都写入一个只追加(append-only)文件里面，用户可以根据数据的重要程度，将只追加写入设置为从不同步(sync)、每秒同步一次或者没写一个命令就同步一次。

##2、Redis的数据结构

   *结构类型* | *结构存储的值* | *结构的读写能力*  
  ------------|----------------|----------------
  STRING      | 可以是字符串、整数或者浮点数| 对整个字符串或者字符串中的一部分执行操作；对整数和浮点数执行自增(incremenmt)或者自减(decrement)
  LIST        |一个链表，链表上每个节点都包含了一个字符串|从链表的两端推入或者弹出元素；根据偏移量对链表进行修剪(trim)；读取单个或者多个元素；根据值查找或者移除元素
  SET        |包含字符串的无序收集器，并且被包含的每个字符串都是独一无二，各不相同的。|添加、获取、移除单个元素；检查一个元素是否存在于集合中；计算交集、并集、差集；从集合里随机获取元素；
  HASH      |包含键值对的无序散列表 |添加、获取、移除单个键值对；获取所有键值对
  ZSET(有序集合)|字符串成员(member)与浮点数分值(score)之间的有序映射，元素的排列顺序由分值的大小决定|添加、获取、删除单个元素；根据分值范围(range)或者成员来添加元素

**启动redis**

- 打开cmd窗口，进入redis解压缩目录；

- 执行redis-server.exe redis.conf命令，启动redis；

- 新开cmd窗口，输入redis-cli.exe -h 127.0.0.1 -p 6379 连接redis

###2.1 Redis的字符串（STRING）

**一、STRING字符串命令**

*命令*|*行为*|
------|------|
GET   |获取存储在给定键中的值
SET   |设置存储在给定键中的值
DEL   |删除存储在给定键中的值（这个命令可以用于所有类型）
GETRANGE key start end |返回 key 中字符串值的子字符
GETSET key value |将给定 key 的值设为 value ，并返回 key 的旧值(old value)。
GETBIT key offset | 对 key 所储存的字符串值，获取指定偏移量上的位(bit)。
MSET key value [key value ...] | 同时设置一个或多个 key-value 对。
MSETNX key value [key value ...]  | 同时设置一个或多个 key-value 对，当且仅当所有给定 key 都不存在。
MGET key1 [key2..] | 获取所有(一个或多个)给定 key 的值。
SETBIT key offset value | 对 key 所储存的字符串值，设置或清除指定偏移量上的位(bit)。
SETEX key seconds value | 将值 value 关联到 key ，并将 key 的过期时间设为 seconds (以秒为单位)。
SETNX key value |只有在 key 不存在时设置 key 的值。
SETRANGE key offset value | 用 value 参数覆写给定 key 所储存的字符串值，从偏移量 offset 开始
STRLEN key | 返回 key 所储存的字符串值的长度
PSETEX key milliseconds value | 这个命令和 SETEX 命令相似，但它以毫秒为单位设置 key 的生存时间，而不是像 SETEX 命令那样，以秒为单位。
INCR key | 将 key 中储存的数字值增一。
INCRBY key increment | 将 key 所储存的值加上给定的增量值（increment） 。
INCRBYFLOAT key increment | 将 key 所储存的值加上给定的浮点增量值（increment） 。
DECR key | 将 key 中储存的数字值减一。
DECRBY key decrement | key 所储存的值减去给定的减量值（decrement） 。
APPEND key value | 如果 key 已经存在并且是一个字符串， APPEND 命令将 value 追加到 key 原来的值的末尾。

###2.2 Redis中的列表（LIST）

一个列表可以有序地存储多个字符串。

列表命令：

*命令*|*行为*
------|------
LPUSH key value1 [value2] |将一个或多个值插入到列表头部
LPUSHX key value |
RPUSH |将给定值推入列表的右端
RPUSHX key value |为已存在的列表添加值
LRANGE|获取列表在给定范围上的所有值（**使用开始索引为0，结束索引为-1可以获取所有的值**）
LINDEX|获取列表在给定位置上的单个元素
LPOP  |从列表的左端弹出一个值，并返回被弹出的值
BLPOP key1 [key2 ] timeout | 移出并获取列表的第一个元素， 如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止。
BRPOP key1 [key2 ] timeout | 移出并获取列表的最后一个元素， 如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止。
BRPOPLPUSH source destination timeout |从列表中弹出一个值，将弹出的元素插入到另外一个列表中并返回它； 如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止。
LINDEX key index | 通过索引获取列表中的元素
LINSERT key BEFORE|AFTER pivot value | 在列表的元素前或者后插入元素
LLEN key | 获取列表长度
LPOP key | 移出并获取列表的第一个元素
LREM key count value |移除列表元素
LSET key index value |通过索引设置列表元素的值
LTRIM key start stop |对一个列表进行修剪(trim)，就是说，让列表只保留指定区间内的元素，不在指定区间之内的元素都将被删除。
RPOP key |移除并获取列表最后一个元素
RPOPLPUSH source destination |移除列表的最后一个元素，并将该元素添加到另一个列表并返回


###2.3 Redis的集合（SET）

Redis的集合和列表都可以存储多个字符串，它们之间的不同在于：*列表可以存储多个相同的字符串，而集合则通过使用散列表来保证自己存储的每个字符串都是各不相同的（这些散列表只有键，但没有与键相关联的值）*

*命令（key即集合名称）*|*行为*
------|------
SADD key member1 [member2] |向集合添加一个或多个成员
CARD key |获取集合的成员数
SDIFF key1 [key2] |返回给定所有集合的差集
SDIFFSTORE destination key1 [key2] |返回给定所有集合的差集并存储在 destination 中
SINTER key1 [key2] |返回给定所有集合的交集
SINTERSTORE destination key1 [key2] |返回给定所有集合的交集并存储在 destination 中
SISMEMBER key member |判断 member 元素是否是集合 key 的成员
SMEMBERS key |返回集合中的所有成员
SMOVE source destination member |将 member 元素从 source 集合移动到 destination 集合
SPOP key |移除并返回集合中的一个随机元素
SRANDMEMBER key [count] |返回集合中一个或多个随机数
SREM key member1 [member2] |移除集合中一个或多个成员
SUNION key1 [key2] |返回所有给定集合的并集
SUNIONSTORE destination key1 [key2] |所有给定集合的并集存储在 destination 集合中
SSCAN key cursor [MATCH pattern] [COUNT count] |迭代集合中的元素

###2.4 Redis的散列（HASH）

*命令（key即散列名称）*|*行为*
------|------
HDEL key field2 [field2] |删除一个或多个哈希表字段
HEXISTS key field |查看哈希表 key 中，指定的字段是否存在。
HGET key field |获取存储在哈希表中指定字段的值
HGETALL key |获取在哈希表中指定 key 的所有字段和值
HINCRBY key field increment |为哈希表 key 中的指定字段的整数值加上增量 increment 。
HINCRBYFLOAT key field increment |为哈希表 key 中的指定字段的浮点数值加上增量 increment 。
HKEYS key | 获取所有哈希表中的字段
HLEN key |获取哈希表中字段的数量
HMGET key field1 [field2] |获取所有给定字段的值
HMSET key field1 value1 [field2 value2 ] |同时将多个 field-value (域-值)对设置到哈希表 key 中。
HSET key field value | 将哈希表 key 中的字段 field 的值设为 value 。
HSETNX key field value |只有在字段 field 不存在时，设置哈希表字段的值。
HVALS key | 获取哈希表中所有值
HSCAN key cursor [MATCH pattern] [COUNT count] |迭代哈希表中的键值对。