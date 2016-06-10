---
layout: post
title: 《Java高并发程序设计》第四章、锁的优化及注意事项
categories: [多线程]
description: Thread,多线程
keywords: Java, 多线程，Thread
autotoc: true
comments: true
---

本文是《Java高并发程序设计》第四章节学习笔记。

本文讲述内容的完整代码实例见 <https://github.com/sunailian/TouchHigh>。

##一、有助于提高“锁”性能的几点建议

###1、减少锁持有时间

```java

public synchronized void syncMethod(){
	otherCode1();
	mutexMethod();
	otherCode2();
}

```

上述同步方法，假设只有metuxMethod()需要同步，那么当otherCode1()和otherCode2()分别是重量级的方法时，则会花费较长的CPU时间。

以下是优化后的代码：

```java
public  void syncMethod(){
	otherCode1();
	synchronized(this){
      mutexMethod();
    }
	otherCode2();
}
```

**只在必要时进行同步，这样能明显减少线程持有锁的时间，提高系统的吞吐量。**

**减少锁的持有时间有助于降低锁冲突的可能性，进而提升系统的并发能力**

###2、减小锁粒度

- (1). 典型场景：ConcurrentHashMap

如果对HashMap的整个put和get方法加锁，粒度太大。对于ConcurrentHashMap，它内部进一步细分了若干个小的HashMap，称之为段（Segment）。默认情况下，一个ConcurrentHashMap被进一步细分为16个段。


- (2). ConcurrentHashMap首先根据hashcode得到该表项应该被存放到哪个段中，然后对该段加锁，并完成put()操作。在多线程环境中，如果多个线程同时进行put()操作，只要被加入的表项不存放在同一个段中，则线程间便可以做到真正的并行。


```Java
@SuppressWarnings("unchecked")
	 public V put(K key, V value) {
			 Segment<K,V> s;
			 if (value == null)
					 throw new NullPointerException();
			 int hash = hash(key);
			 int j = (hash >>> segmentShift) & segmentMask;
			 if ((s = (Segment<K,V>)UNSAFE.getObject          // nonvolatile; recheck
						(segments, (j << SSHIFT) + SBASE)) == null) //  in ensureSegment
					 s = ensureSegment(j);
			 return s.put(key, hash, value, false);
	 }

```

**问题**：当系统需要取得全局锁时，其消耗的资源会比较多。
