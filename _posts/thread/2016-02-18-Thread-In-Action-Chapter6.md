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

```Java
Comparator<String> cmp = Comparator.comparingInt(String::length).thenComparing(String.CASE_INSENSITIVE_ORDER);

```

###6.2.3 lambda表达式

lambda表达式：匿名函数，是一段没有函数名的函数体，可以作为参数直接传递给相关的调用者。

非常类似匿名内部类：

```Java
List<Integer> numbers = Arrays.asList(1,2,3,4,5,6);
numbers.forEach((Integer value) -> System.out.println(value));
```

可以访问外部的局部变量，但必须声明为**final**：

```Java
final int num = 2;
Function<Integer,Integer> stringConverter = (from) - >from * num;
System.out.println(stringConverter.apply(3));//6
```

###6.2.4 方法引用

- 静态方法引用：ClassName::methodName
- 实例上的实例方法引用：instanceReference::methodName
- 超类上的实例方法引用：super::methodName
- 类型上的实例方法引用：ClassName::methodName
- 构造方法引用：Class::new
- 数组构造方法引用：TypeName[]::new

###6.3 一步步走入函数式编程

```Java
static int[] arr = {1,2,3,4,5,6,7,8,9};

public static void main(String[] args){
	for(int i:arr){
		System.out.println(i);
	}


  //函数式编程
  Arrays.stream(arr).forEach(new IntConsumer(){
		@Override
		public void accept(int value){
			System.out.println(value);
		}
	});

	//简化：参数是从上下文推导出来的，省略IntConsumer
	Arrays.stream(arr).forEach((final int x) -> {
		System.out.println(x);
	});

	//简化：参数类型也是推导出来的，省略int
	Arrays.stream(arr).forEach((x) -> {
		System.out.println(x);
	});

	//简化：参数申明和接口实现放在一起(lambda表达式)
	Arrays.stream(arr).forEach((x) -> System.out.println(x));

	//简化：Java8支持方法引用
	Arrays.stream(arr).forEach(System.out::println);

}
```

**注意：Arrays.stream()方法返回了一个流对象。类似于集合或者数组，流对象也是一个对象的集合，它将给予我们遍历处理流内元素的功能**

**Arrays.stream()还支持DoubleStream、LongStream和普通对象的流**


##6.4  并行流与并行排序

###6.4.1 使用并行流过滤数据

场景：统计1～1000000内所有的质数数量

```Java
//判断是否为质数
public class PrimeUtil{
	public static boolean isPrime(int number){
		int tmp = number;
		if(tmp < 2){
			return false;
		}
		for(int i = 2; Math.sqrt(tmp) >=i;i++){
			if(tmp % i ==0){
				return false;
			}
		}
		return true;
	}
}

//串行过滤
IntStream.range(1,1000000).filter(PrimeUtil::isPrime).count();

//并行过滤 ：PrimeUtil.isPrime会被多线程并发调用
IntStream.range(1,1000000).parallel().filter(PrimeUtil::isPrime).count();
```

###6.4.2 从集合得到并行流

可以从集合中得到一个流或者并行流。

场景：统计集合内所有学生的平均分

```Java
List<Student> ss = new ArrayList<Student>();
double ave = ss.stream().mapToInt(s -> s.score).average().getAsDouble();

//改成并行：从集合中获取并行流即可
double ave = ss.parallelStream().mapToInt(s -> s.score).average().getAsDouble();
```


###6.4.3 并行排序

```Java
int[] arr = new int[1000000];
Arrays.parallelSort(arr);

//Java8 增加的Arrays的功能：setAll(int[] array,IntUnaryPperator generator)
//eg:给数组中每一个元素都附上一个随机数
Random r = new Random();
Arrays.setAll(arr,(i)->r.nextInt());

//改成并行：
Arrays.parallelSetAll(arr,(i)->r.nextInt());

```

##6.5 增强的Future：CompeletableFuture
