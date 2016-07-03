---
layout: post
title: 《Java高并发程序设计》第六章、Java8与并发
categories: [多线程]
description: Thread,多线程
keywords: Java, 多线程，Thread
autotoc: true
comments: true
---

本文是《Java高并发程序设计》第六章节学习笔记。

本文讲述内容的完整代码实例见 <https://github.com/sunailian/TouchHigh>。

##6.1 Java8的函数式编程简介

###6.1.1 函数作为一等公民

jQuery中的函数案例：

```
function f1(){
	var n = 1;
	function f2(){
		alert(n);
	}
	return f2;
}
var result = f1();
result(); //1

```

以上result函数指向f2。

- 函数作为参数传递给另外一个函数（参看js中类似$.each(function(){})）；
- 函数可以作为另外一个函数的返回值；（参看上面例子）

这两个是函数式编程的重要特点。

###6.1.2 无副作用
- 显示函数：函数与外界交换数据的唯一渠道就是参数和返回值，不会去读取或者修改函数的外部状态（比如某一个全局状态）。
- 隐士函数：除了参数和返回值外，还会读取或者修改外部信息。

副作用在函数式编程中，需要进行有效的限制。

###6.1.3 申明式的

与申明式相对的是命令式。命令式习惯创建对象和变量，然后去修改它们的值。

命令式：

```Java
public static void imperative(){
	int[] iArr = {1,2,3,4,5,6,7,8,9};
	for(int i = 0;i<iArr.length;i++){
		System.out.println(iArr[i]);
	}
}
```

申明式：

```Java
public static void declarative(){
	int[] iArr = {1,2,3,4,5,6,7,8,9};
	Arrays.stram(iArr).forEach(System.out:println);
}
```

###6.1.4 不变的对象

函数式编程中，几乎所有传递的对象都不会轻易被修改。
```Java
static int[] iArr = {1,2,3,4,5,6,7,8,9};
Arrays.stream(iArr).map((x)->x=x+1).forEach(System.out:println);
System.out.println();
Arrays.stram(iArr).forEach(System.out:println);
```

打印后发现，数组中的元素并没有被加1。

###6.1.5 易于并行

###6.1.6 更少的代码

##6.2 函数式编程基础

###6.2.1 FunctionlInterface注释
Java8中提出了函数式接口的概念：只定义了**单一抽象方法**的接口（可以有多个方法，但只能有一个抽象方法。但是被Object已经实现的方法不能被视为抽象方法，比如equals方法）。

```Java
@FunctionlInterface
public static interface IntHandler(){
	void handle(int i);
	boolean equals(Object obj);
}
```
即使不加注释，只要满足函数式接口的定义，也会被认为是函数式接口。

###6.2.2 接口默认方法

Java8之后，接口可以包含若干个实例方法。这使得接口可以多继承。

增加了**default**关键字，可以在接口内定义实例方法。

```Java
public interface IHorse{
	void eat();
	default void run(){
		System.out.println("horse run");
	}
}

```

比如在`java.util.Comparator`中，Java8就增加了若干个默认方法，用于多个比较器的整合，其中有一个常用的默认方法：

```Java
default Comparator<T> thenComparing(Comparator<? super T> other){
	Objects.requireNonNull(other);
	return (Comparator<T> & Serializable)(c1,c2) -> {
		int res = compare(c1,c2);
		return (res!=0) ? res :other.compare(c1,c2);
	}
}

```
