---
layout: post
title: 《Effective Java》第五章
categories: [Effective Java, Java]
description: 《Effective Java》学习笔记
keywords: Effective Java, Java
autotoc: true
comments: true
---

##第五章 泛型

标签（空格分隔）： Effective Java

---

###第23条：不要在新代码中使用原生态类型

- (1). 泛型的原生态类型：List<E>对应的是不带任何实际参数类型的List。如果使用原生态类型，比如只使用List而不是`List<E>`，容易出错。可能在add的时候不会发生错误，但是在get的时候，可能会发生`ClassCastException`

- (2). 泛型的优点：

 + i.	插入元素时自带类型检查
 + ii.	删除元素时不需要进行手工转换
 + iii.	可以使用for-each循环，两种方法

```java

   //for-each loop over a parameterized collection-typesafe
   for(Stamp s:stamps){
       ...
   }

   //或者无论是否使用传统的for循环也一样

   //for loop with parameterized iterator declaration-typesafe
   for(Itrator<Staml> i=stamps.iterator();i.hasNext();){
       Stamp s = i.next();
       ...
   }

```

 + iv. 如果使用原生态类型，就失掉了泛型在安全性和表述性方面的所有优势。`虽然不应该使用像List这种原生态类型，但是可以却允许使用参数话的类型以允许插入任意对象，如List<Object>。相比于原生的，object参数化不会失掉泛型的安全性。`

- (3). 无限制通配符类型
 如果要使用泛型，但不确定或者不关心实际的类型参数，就可以使用问号<?>代替。表明可以持有任何集合。

- (4). 例外
 + i.	类文字中必须使用原生态类型，比如List.class
 + ii.	泛型信息会在运行时被擦除，所以在instanceof后面可以使用Set


 **术语** |  **示例**  
------------|----------------
 参数化的类型|List<String>
实际参数类型 |String
 泛型|List<E>
 形式类型参数|E
 无限制通配符类型|List<?>
 原生态类型|List
 有限制类型参数|`<E extends Number>`
 递归类型限制|`<T extends Comparable<T>>`
 有限制通配符类型|List<? extends Number>
 泛型方法|static <E> List<E> asList(E[] a)
 类型令牌|String.class   

###第24条：消除非受检警告：unchecked warning

- (1). 如果无法消除警告，同时可以证明引起警告的代码是类型安全的。那么可以使用@SupressWarnings(“unchecked”)来禁止警告。必须在每次使用这条注解的后面加注释，说明为什么要这么做
- (2). 应该始终在尽可能小的范围内使用SupressWarnings注解，永远不要在整个类上使用这条注解
- (3). Return不能使用这条注解。应该声明一个局部变量并注解
