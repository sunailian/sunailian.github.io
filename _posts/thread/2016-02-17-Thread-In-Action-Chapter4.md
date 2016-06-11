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

##4.1、有助于提高“锁”性能的几点建议

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

###5、锁粗化

虽然我们会要求每个线程持有锁的时间尽可能短，但如果对同一个锁不停的请求、同步和释放，本身也是非常耗费性能的事情。

因此，虚拟机在遇到一连串连续地对同一锁不断进行请求和释放的操作时，便会把所有的锁操作整合成对锁的一次请求，从而减少对锁的请求同步次数。这个就叫做**锁粗化**

```Java
public void  demoMethod(){
	synchronized(lock){
		// do sth
	}

//做其他不需要的同步的工作，但是很快能执行完毕
	synchronized(lock){
		//do sth
	}
}

```
上述代码可以整合成：

```Java
public void  demoMethod(){
  //整合成一次锁请求
	synchronized(lock){
		//do sth
		//做其他不需要的同步的工作，但是很快能执行完毕
	}
}

```

尤其当在循环内请求锁时，合理进行**锁粗化**

```Java
for (int i = 0;i<CIRCLE ;i++ ) {
	synchronized(lock){

	}
}
```

修改成：

```Java
synchronized(lock){
	for (int i = 0;i<CIRCLE ;i++ ) {

	}
}

```

##4.2 Java虚拟机对锁优化所做的努力

JDK对锁的优化策略：

###1、锁偏向

- (1). 核心思想：如果一个线程获得了锁，那么锁就进入偏向模式。当这个线程再次请求锁时，无须再做任何同步操作。

- (2). 对于几乎没有锁竞争的场合，比较有效果，因为多次请求极有可能是同一个线程

- (3). 对于锁竞争激烈的场合，效果不佳。

- (4). 可以使用-XX:+UseBiasedLocking开启偏向锁。

###2、轻量级锁

###3、自旋锁

###4、锁消除


##4.3 人手一支笔：ThreadLocal

如果说使用锁是第一种思路，那么**ThreadLocal**就是第二种思路了。

###1、ThreadLocal简单使用

以下是简单的示例：

```Java
private static final SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
public static class ParseDate implements Runnable{
	int i = 0;
	public ParseDate(int i){this.i = i;}

	public void run(){
		try{
			Date t = sdf.parse("2015-03-29 13:20:"+i%60);
			System.out.println(i + ":" + t);
		}catch(ParseException e){
			e.printStackTrace();
		}
	}
}

public static void main(String[] args){
	ExecutorService es = Executors.newFixedThreadPool(10);
	for(int i = 0;i<1000;i++){
		ex.execute(new ParseDate(i))
	}
}

```

上述代码可能会抛出异常，因为SimpleDateFormat是**非线程安全**的。

我们可以在`sdf.parse()`方法前加锁。当然也可以使用ThreadLocal：

```Java
static ThreadLocal<SimpleDateFormat> tl = new ThreadLocal<SimpleDateFormat>();
public static class ParseDate implements Runnable{
	int i = 0;
	public ParseDate(int i){this.i = i;}

	public void run(){
		try{
			if(tl.get()==null){
				tl.set(new SimpleDateFormat("yyyy-MM-dd HH:mm:ss"));//手工去设置不同的对象实例保证线程安全
			}
			Date t = tl.get().parse("2015-03-29 13:20:"+i%60);

		}catch(ParseException e){
			e.printStackTrace();
		}
	}
}


```

**注意：为每一个线程分配不同的对象，需要在应用层面保证。ThreadLocal只是起到了简单的容器作用。**

###2、ThreadLocal实现原理

主要关注ThreadLocal的`set()`和`get()`方法

方法源码：

```Java
public void set(T value) {
		Thread t = Thread.currentThread();
		ThreadLocalMap map = getMap(t);  //可以理解为类似HashMap，虽然不是Map。定义在ThreadLocal内部，每个Thread持有ThreadLocal.ThreadLocalMap
		if (map != null)
				map.set(this, value);
		else
				createMap(t, value);
}

public T get() {
		Thread t = Thread.currentThread();
		ThreadLocalMap map = getMap(t);
		if (map != null) {
				ThreadLocalMap.Entry e = map.getEntry(this);
				if (e != null)
						return (T)e.value;
		}
		return setInitialValue();
}
```

可以发现，**由于变量都是维护在ThreadLocal内部的（ThreadLocalMap），这也意味着只要线程不退出，对象的引用将一直存在。**

 当线程退出时，Thread类会进行一些清理工作，其中就包括清理ThreadLocalMap：

 ```Java
 /**
  * This method is called by the system to give a Thread
  * a chance to clean up before it actually exits.
  */
 private void exit() {
 		if (group != null) {
 				group.threadTerminated(this);
 				group = null;
 		}
 		/* Aggressively null out all reference fields: see bug 4006245 */
 		target = null;
 		/* Speed the release of some of these resources */
		//注意以下部分：
 		threadLocals = null;
 		inheritableThreadLocals = null;
 		inheritedAccessControlContext = null;
 		blocker = null;
 		uncaughtExceptionHandler = null;
 }
 ```

 - 如果使用线程池，那么当前线程未必回退出（比如固定大小的线程池，线程总是存在）。**如果这样，将一些大的对象设置到ThreadLocal中（它实际保存在线程池有的threadLocals Map内），可能会使系统内出现内存泄漏的可能（如果设置了对象到ThreadLocal中，但是不清理它，在你使用几次后，这个对象再也不使用了，但是它却无法被回收）**

 - 如果希望及时回收对象，最好使用ThreadLocal.remove()方法移除变量。

 - 另外，JDK也可能允许你使用类似obj=null之类的代码去加速垃圾回收。即手工将ThreadLocal变量设置为null，这么做有用的原因是：因为ThreadLocalMap其实更加类似**WeakHashMap**。

- 虚拟机在垃圾回收时，如果发现是弱引用，就会立即回收。

- ThreadLocal内部是由一系列的Entry构成，每一个Entry都是WeakReference<ThreadLocal>

```Java
/**
				* The entries in this hash map extend WeakReference, using
				* its main ref field as the key (which is always a
				* ThreadLocal object).  Note that null keys (i.e. entry.get()
				* == null) mean that the key is no longer referenced, so the
				* entry can be expunged from table.  Such entries are referred to
				* as "stale entries" in the code that follows.
				*/
			 static class Entry extends WeakReference<ThreadLocal> {
					 /** The value associated with this ThreadLocal. */
					 Object value;

					 Entry(ThreadLocal k, Object v) {
							 super(k);
							 value = v;
					 }
			 }
```

这里虽然使用了ThreadLocal作为Map的key，但是实际上，**它并未真正的持有ThreadLocal的引用。而当ThreadLocal的外部强引用被回收时，ThreadLocalMap中的key就会变成null**。

##4.4 无锁

###1、与众不同的并发策略：比较交换（CAS）

**CAS(V,E,N)**

- V：要更新的变量
- E：预期值
- N：新的值

当V==E：将V的值设置为N。否则说明有其他线程更新了该值，当前线程不做任何处理。

###2、无锁的线程安全整数：AtomicInteger

AtomicInteger是可见的且线程安全的。

```Java
public final int	get()                       //取得当前值
public final int	getAndAdd(int delta)       //当前值加delta，返回旧值
public final int	addAndGet(int delta)       //当前值加delta，返回新值
public final int	getAndIncrement()          //当前值加1，返回旧值
public final int	getAndDecrement()          //当前值减1，返回旧值
public final int	incrementAndGet()          //当前值加1，返回新值
public final int	decrementAndGet()           //当前值减1，返回新值
public final int	getAndSet(int newValue)    //设置新值，并返回旧值
public final void	set(int newValue)           //设置当前值
public final boolean	compareAndSet(int expect, int update) //如果当前值为expect，则设置为update
public final void	lazySet(int newValue)
public final boolean	weakCompareAndSet(int expect, int update)
public final int	intValue()            
public final double	doubleValue()
public final float	floatValue()   
public final long	longValue()
public final String	toString()
```

就内部实现上来说，AtomicInteger中保存着一个核心字段：

```Java
private volatile int value;   //代表AtomicInteger当前实际值
```

以及还有一个：

```Java
private static final long valueOffset; //保存value字段在AtomicInteger对象中的偏移量
```

以下分析下incrementAndGet的源码：
```Java
/**
 * Atomically increments by one the current value.
 *
 * @return the updated value
 */
public final int incrementAndGet() {
		for (;;) {
				int current = get();
				int next = current + 1;
				if (compareAndSet(current, next))
						return next;
		}
}
```

这里使用了**for死循环**，也就是说当**compareAndSet比较失败时，说明有其它线程更改了值，则再次进入循环重试，直到成功。**

 ###3、Java中的指针：Unsafe类

 看下incrementAndGet方法中的**compareAndSet方法**的源码：
 ```Java
public final boolean compareAndSet(int expect,int update){
	return unsafe.compareAndSwapInt(this,valueOffset,expect,update);
}
 ```

 Unsafe封装了一些类似指针的操作。它是一个JDK内部使用的专属类。我们自己的应用程序无法直接调用。


 ###4、无锁的对象引用：AtomicReference
