---
layout: post
title: 《Effective Java》第七章
categories: [Effective Java, Java]
description: 《Effective Java》学习笔记
keywords: Effective Java, Java
autotoc: true
comments: true
---

##第七章 方法

标签（空格分隔）： Effective Java

---

###第38条：检查参数的有效性

- (1). 必须在文档中指明所有的限制，并且在方法体的开头检查限制。如果传递了无效的参数，那么方法应该很快失败并且抛出适当的异常
 + 对于公有的方法，要用JavaDoc的@throws标签在文档中说明违反参数限制会抛出的异常；对于未导出的方法，应该抛出断言

 ```Java

 /**
  *
  *@throws ArithmeticException if m is less than or equal to 0
  */
public BigInteger mod(BigInteger m){
  if(m.signum() <=0)
    throw new AirthmeticException("Modulus <=0: "+ m);
    ...
}

//

 ```

- (2). 如果方法并没有使用到参数，而是保存了参数，那么参数检查格外重要。例子：构造器，这样可以避免构造出来的对象违反类的约束条件
- (3). 例外：如果检查工作非常昂贵，或者是不切实际的，或者有效性检查已经在计算过程中完成。
 + 计算过程中抛出的错误的异常，应该用异常转译技术转换为正确的异常
 + 因地制宜最重要

###第39条：必要时进行保护性拷贝

- (1). Java是安全语言，自动免疫缓冲区溢出，数组越界等内存破坏错误，但是**有时需要假设客户端会尽其所能来破坏这个类的约束条件，因此必须保护性的设计程序**

- (2). 下面这段程序虽然看上去是final不可变的，但是date本身可变，而且只有引用，所以不安全。

```Java
//Broken "immutable" time period class
public final class Period{
  private final Date start;
  private final Date end;

  public Period(Date start,Date end){
    if(start.compareTo(end)>0)
      throw new IllegalArgumentException(start + " after " + end);
    this.start = start;
    this.end = end;
  }

  public Date start(){
    return start;
  }

  public Date end(){
    return end;
  }
  ...//Remainder omitted
}


//Attack the internals of a Period instance
Date start = new Date();
Date end = new Date();
Period p = new Period(start,end);
end.setYear(78);//Modifies internals of p!

```

- (3). 为了免受这种攻击，对于构造器的每个可变参数进行保护性拷贝是必要的，并且使用**备份对象作为实例组件，而不是原始对象**

```Java
//Repaired Constructor - make defensive copies of parameters
public Period(Date start,Date end){
  this.start = new Date(start.getTime());
  this.end = new Date(end.getTime());

  if(this.start.compareTo(this.end)>0)
    throw new IllegalArgumentException(start + " after "+ end);
}

```

注意：**为了防止TOCTOU攻击，即攻击发生在检查参数到保护性拷贝之间，保护性拷贝应该在参数检查之前，并且应该检查拷贝得到的备份**

- (4). **对于参数类型可以被不信任方子类化的参数，不要调用clone进行拷贝，因为clone可能已经被恶意代码覆盖**
- (5). **访问方法：应该访问私有组件的拷贝，而不是直接返回其引用**

```Java
public Date start(){
  return new Date(start.getTime());
}

public Date end(){
  return new Date(end.getTime());
}

```

- (6). 每当你编写的方法需要客户提供对象进入内部数据结构中，则应该考虑是否容忍对象的改变。不能则必须进行保护性拷贝。返回的对象也应该是拷贝或者是不可变视图
- (7). 真正启示：**对象内部应该尽可能使用不可变组件**
- (8). 如果类和客户端是同一个包的双方，那么不进行拷贝也可以，但是必须在文档中说明
- (9). 如果违反类的约束条件只能伤害到客户端本身，那么也可以不进行拷贝。例子：包装类


###第40条：谨慎的设计方法签名
- (1). 谨慎地选择方法的名称
 + 应该易于理解，并且与同一个包内其他名称风格一致
 + 应该尽可能选择和大众认知一致的名字
- (2). **不要过于追求便利的方法**
 + 只有一项操作经常被用到时才提供快捷方式。如果不能确定就不要提供快捷访问
- (3). **避免过长的参数列表**
 + 目标是4个，越少越好
 + 相同类型的长参数列表格外有害
 + 缩短参数列表的方法‘
   A. 分解方法。每个方法只需要参数的子集。可能有助于提升正交性
   B. 创建辅助类，用来保存参数的分组。一般静态类。如果一个频繁出现的参数序列（纸牌的点数和花色）是一个独特的实体（一张纸牌）的代表，就应该使用静态类来表示这个实体
   C. 从对象创建到方法调用都使用builder模式
- (4). 参数类型优先考虑接口而不是类。减少客户端调用函数的限制
- (5). **对于boolean参数，优先使用两个元素的枚举类型**

```Java
public enum TemperatureScale{FAHRENHEIT,CELSIUS}
```

- (6). **要考虑API的性能后果**。使公有类变成可变的会导致大量的保护性拷贝
