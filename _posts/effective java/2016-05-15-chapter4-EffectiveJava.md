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

- (1). 封装：模块之间只通过API通信，内部实现细节被隐藏。

 + i.	解耦合，使模块并行开发

 + ii.	能够快速调节性能

 + iii.	降低了构建大型系统的风险

- (2). 第一规则：**尽可能使每个类或者成员不被外界访问**

 + i.	对于顶级的非嵌套类和接口只有两种访问级别：**package-private**和**public**

 + ii.	通过把类做成包级私有的，累就不是API的一部分，因此可以进行删改，否则就必须永远支持以确保兼容性

 + iii.	如果某个包级私有的类只在一个类内部被用到，就应该考虑使它成为私有嵌套类（24条）

 + iv.	只有同一个包里的其他类真正需要访问一个成员的时候，才应该删除private修饰符，把它变成包级私有

 + v.	**受保护的成员也是API的一部分，需要永远得到支持**

 + vi.	如果一个类实现了一个接口，所有相关的类方法也必须实现接口的公有函数

 + vii. **不能为了测试，而将类或者成员变成包的API的一部分。应该把测试程序当做包的一部分运行**

 + viii.	**实例类绝不能公开**

- 等于放弃对该值进行限制的能力，也不是线程安全的

- 同样的逻辑也适用于静态域。可以加上final修饰符之后暴露常量。常量应该大写并用下划线分开（54条）

- 具有公开的静态final数组，或者返回这种域的方法，**几乎都是错误的**。

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

###第14条：在公有类中使用访问方法而不是公有域

- (1). 如果类可以在它所在的包的外部进行访问，就提供访问方法。以保留将来改变类的内部表示法的灵活性

- (2).如果类是包级私有或者嵌套私有的，暴露数据域并没有本质的错误

- (3).公有类的域如果是不可变的，危害会小些

```java

public final class Time {


    private static final int HOURS_PER_DAY = 24;
    private static final int MINUTES_PER_HOUR = 60;

    public final int hour;


    public final int minute;

    public Time(int hour, int minute) {
        if (hour < 0 || hour >= HOURS_PER_DAY)
            throw new IllegalArgumentException("HOUR:" + hour);
        if (minute < 0 || minute > MINUTES_PER_HOUR)
            throw new IllegalArgumentException("Min:" + minute);
        this.hour = hour;
        this.minute = minute;
    }
}

```

###第15条：使可变性最小化

- (1). 不可变类比可变类更容易设计，实现和使用。

- (2).五条原则

 + i.	不要提供任何会修改对象状态的方法

 + ii.	保证类不会被扩展：一般做法是让这个类成为final

 + iii.	使所有的域都是final的

 + iv.	使所有的域都成为私有的

 + v.	确保对于任何可变组件的互斥访问：如果类具有指向可变变量的域，则必须确保该类的客户端无法获得指向这些对象的引用。并且，永远不要用客户端提供的对象引用来初始化这样的域，也不要提供任何访问方法返回该对象引用

- (3). 例子：Complex类，这个不可变类的运算返回新的实例而不是改变内部状态

- (4). 不可变类的优势

 + i.	比较简单，确保构造器建立了这个类的约束关系，那么可确保这些约束关系在整个生命周期都不会变化。可靠地使用一个可变类是非常困难的

 + ii.	不可变对象本质上是线程安全的，他们不要求同步。它们可以被自由的共享

- 不可变的类可以提供静态工厂，把频繁请求的实例缓存起来，当现有实例符合请求的时候就不必创建新的实例

- 永远不用进行保护性拷贝

- 可以共享这些类的内部信息

 + iii.	不可变对象为其他的对象提供了大量的“构件”

 + iv.	不可变类的真正缺点是：对于每一个不同的类都需要一个单独的对象

例如：BigInteger类，几百万位只有一位不同

- 猜测一下经常会用到哪些多步骤的操作，然后将它们作为基本类型提供。不可变的类在内部可以更灵活

- 最好的办法是提供一个公有的可变配套类：String的配套类是StringBuilder

- 类绝对不允许自身被子类化：final，或者所有构造器包级私有
   第二种方法比较灵活，适合之后的改良

- (5). 真正的要求：没有一个方法能够对对象产生外部可见的改变

 + i.	许多对象拥有一个或者多个非final类，存储一些开销昂贵的计算结果

- (6). 告诫

 + i.	如果选择让不可变类实现Serializable接口，并且它包含一个或者多个指向可变对象的域，就必须提供显式的readResolve或者readObject方法

 + ii.	坚决不要为get方法添加setter：除非有很好的理由让类成为可变的，否则就应该是不可变

 + iii.	如果类不能做成不可变的，依然应该尽可能限制它的可变性。除非有令人信服的原因把域做成非final的，否则每个域都必须是final的

 + iv.	构造器应该创建完全初始化的对象，建立其所有的约束关系，不要提供额外的公有初始化方法，也不要提供重新初始化方法

###第16条：复合优先于实现继承

- (1).继承打破了封装性。超类变化的时候会影响子类，除非是专门为继承设计的超类，有良好的拓展性和文档

例子：HashMap类拓展导致重复计数

- (2). 超类的自用性是实现细节，不保证在所有的发行版本中都保持一致

- (3). 超类在后续的发行版本获得新的方法，会影响子类。例如：子类有一个签名相同但是返回值不同的函数，这样会导致子类无法通过编译。如果返回值相同，那么等同于override。也不能保证子类的方法能够满足超类的约定

- (4). 复合与转发方法
灵活，健壮，InstrumentedSet类实现了Set接口并且拥有私有Set成员
