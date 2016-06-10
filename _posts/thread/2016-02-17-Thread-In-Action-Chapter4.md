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

**问题**：当系统需要取得全局锁时，其消耗的资源会比较多。比如ConcurrentHashMap的size()方法，需要获得所有段的锁，然后再求和。

`因此，只有在类似于size()获取全局信息的方法调用并不频繁时，这种减小锁粒度的方法才能真正意义上提高系统的吞吐量`


###3、读写分离锁来替换独占锁

- (1).  在读多写少的场合，使用读写锁对系统的性能是有好处的。读操作本身不会影响数据的完整性和一致性。因此，理论上讲，在大部分情况下，应该可以允许多线程同时读，读写锁正是实现了这种功能。

###4、锁分离

根据应用程序的功能特点，使用类似的分离思想，也可以对独占锁进行分离。典型案例：**LinkedBlocingQueue**

- (1). LinkedBlocingQueue的**take()**和**put()**函数分别实现了取数据和增加数据的功能；

- (2).两个函数虽然都对队列进行了修改，但是LinkedBlocingQueue是基于列表的，因此两个函数分别作用于队列的前端和尾端，理论上说，两者不冲突。

- (3). 如果食用独占锁，那么take()和put()不可能真正的并发，会彼此等待对方释放锁资源。

- (4). JDK采用两把锁，分离了take()和put()操作。

```Java

/** Lock held by take, poll, etc */
private final ReentrantLock takeLock = new ReentrantLock();//take函数需要持有takeLock

/** Wait queue for waiting takes */
private final Condition notEmpty = takeLock.newCondition();

/** Lock held by put, offer, etc */
private final ReentrantLock putLock = new ReentrantLock();//put函数需要持有putLock

/** Wait queue for waiting puts */
private final Condition notFull = putLock.newCondition();

```

以下是take函数的具体实现：

```Java
public E take() throws InterruptedException {
		E x;
		int c = -1;
		final AtomicInteger count = this.count;
		final ReentrantLock takeLock = this.takeLock;
		takeLock.lockInterruptibly();    //不能同时有两个线程同时取数据
		try {
				while (count.get() == 0) {    //如果当前没有可用数据，一直等待
						notEmpty.await();         //等待，put()操作的通知
				}
				x = dequeue();                //取得第一个数据
				c = count.getAndDecrement();  //数量减1，原子操作，因为会和put函数同时访问count，注意c是                 //count减1前的值
				if (c > 1)
						notEmpty.signal();         //通知其他take操作
		} finally {
				takeLock.unlock();             //释放锁
		}
		if (c == capacity)
				signalNotFull();               //通知put操作，已有空余空间
		return x;
}

```

以下是put函数的具体实现：

```Java
public void put(E e) throws InterruptedException {
		if (e == null) throw new NullPointerException();
		// Note: convention in all put/take/etc is to preset local var
		// holding count negative to indicate failure unless set.
		int c = -1;
		Node<E> node = new Node(e);
		final ReentrantLock putLock = this.putLock;
		final AtomicInteger count = this.count;
		putLock.lockInterruptibly();         //不能同时有两个线程同时put
		try {
				/*
				 * Note that count is used in wait guard even though it is
				 * not protected by lock. This works because count can
				 * only decrease at this point (all other puts are shut
				 * out by lock), and we (or some other waiting put) are
				 * signalled if it ever changes from capacity. Similarly
				 * for all other uses of count in other wait guards.
				 */
				while (count.get() == capacity) {    //如果队列满了
						notFull.await();                 //等待
				}
				enqueue(node);                       //插入数据
				c = count.getAndIncrement();         //更新总数，c是加1前的值
				if (c + 1 < capacity)
						notFull.signal();                //有足够的空间，通知其他线程
		} finally {
				putLock.unlock();                    //释放锁
		}
		if (c == 0)
				signalNotEmpty();                   //插入成功后，通知take操作取数据
}

```
