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
 + iv. 如果使用原生态类型，就失掉了泛型在安全性和表述性方面的所有优势。`虽然不应该使用像List这种原生态类型，但是可以却允许使用参数话的类型以允许插入任意对象，如List<Object>。相比于原生的，object参数化不会失掉泛型的安全性。`

- (3). 无限制通配符类型
 如果要使用泛型，但不确定或者不关心实际的类型参数，就可以使用问号<?>代替。表明可以持有任何集合。

 **术语** |  **示例**  
------------|----------------
 参数化的类型|List<String>
实际参数类型 |String
 泛型|List<E>
 形式类型参数|E
 无限制通配符类型|List<?>
 原生态类型|List
 有限制类型参数|`<E extends Number>`
 递归类型限制|<T extends Comparable<T>>
 有限制通配符类型|List<? extends Number>
 泛型方法|static <E> List<E> asList(E[] a)
 类型令牌|String.class   
