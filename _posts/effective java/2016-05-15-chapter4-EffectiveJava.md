---
layout: post
title: 《Effective Java》第四章
categories: [Effective Java, Java]
description: 《Effective Java》学习笔记
keywords: Effective Java, Java
autotoc: true
comments: true
---

##第四章 类和接口

标签（空格分隔）： Effective Java

---

###第3条：使类和成员的可访问性最小化

－ (1). 封装：模块之间只通过API通信，内部实现细节被隐藏。

i.	解耦合，使模块并行开发

ii.	能够快速调节性能

iii.	降低了构建大型系统的风险

- (2). 第一规则：**尽可能使每个类或者成员不被外界访问**

i.	对于顶级的非嵌套类和接口只有两种访问级别：**package-private**和**public**

ii.	通过把类做成包级私有的，累就不是API的一部分，因此可以进行删改，否则就必须永远支持以确保兼容性

iii.	如果某个包级私有的类只在一个类内部被用到，就应该考虑使它成为私有嵌套类（24条）

iv.	只有同一个包里的其他类真正需要访问一个成员的时候，才应该删除private修饰符，把它变成包级私有

v.	**受保护的成员也是API的一部分，需要永远得到支持**

vi.	如果一个类实现了一个接口，所有相关的类方法也必须实现接口的公有函数

vii. **不能为了测试，而将类或者成员变成包的API的一部分。应该把测试程序当做包的一部分运行**

viii.	**实例类绝不能公开**

－ 等于放弃对该值进行限制的能力，也不是线程安全的
－ 同样的逻辑也适用于静态域。可以加上final修饰符之后暴露常量。常量应该大写并用下划线分开（54条）

－ 具有公开的静态final数组，或者返回这种域的方法，**几乎都是错误的**。

   A. 把公有数组变成私有的，并增加公有的不可变列表

```java

private static final Thing[] PRIVATE_VALUES={...};
    public static final List<Thing> VALUES=Collections.unmodifiableList(Arrays.asList(PRIVATE_VALUES));

```

  B. 访问方法返回私有数组的拷贝

  ```java
  private static final Thing[] PRIVATE_VALUES={...};
    public static final Thing[] values(){
        return PRIVATE_VALUES.clone();
  }

```
