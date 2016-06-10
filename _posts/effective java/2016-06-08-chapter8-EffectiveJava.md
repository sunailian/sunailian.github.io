---
layout: post
title: 《Effective Java》第八章
categories: [Effective Java, Java]
description: 《Effective Java》学习笔记
keywords: Effective Java, Java
autotoc: true
comments: true
---

##第八章 方法

标签（空格分隔）： Effective Java

---

###第45条：通用程序设计
- (1). 将局部变量的作用于最小化
 + 要让局部变量的作用域最小化，最有力的方法就是在第一次使用它的地方声明。过早的声明局部变量不仅会使它的作用域过早地扩展，还会让它的结束过晚。如果变量在它的目标使用域之前或者之后被意外使用，会比较糟糕
 + 几乎每个局部变量的声明都应该包含一个初始化表达式。如果还没有足够的信息对这个变量做初始化，那就应该推迟声明，直到可以初始化位置。例外是try-catch块。
 + 如果在循环终止之后不再需要循环变量的内容，for循环就优先于while循环，因为while循环可能会用到循环外的变量。
 + 如果循环测试涉及方法调用，就应该用下面这种方法
