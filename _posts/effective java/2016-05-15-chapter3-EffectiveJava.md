---
layout: post
title: 《Effective Java》第三章
categories: [Effective Java, Java]
description: 《Effective Java》学习笔记
keywords: Effective Java, Java
autotoc: true
comments: true
---

##第三章 对于所有对象都通用的方法

标签（空格分隔）： Effective Java

---

###第8条：覆盖equals时请遵守通用约定

－ (1). 确定应该/不应该覆盖equals方法的情况

i. 类的每个实例本质上是唯一的

ii. 不关心类是否提供了“逻辑相等“的测试功能。

iii. 超类已经覆盖了equals，并且从超类继承过来的子类也是合适的
例如：从AbstractSet继承equals

iv. 类是私有的或者包级私有的：**此时应该覆盖并抛出异常。**

```java

@override
        public boolean equals(Obeject o) throws Throwable {
           throw new AssertionError();//Method never called
        }
```

- (2). 可以考虑覆盖equals方法：“值类”。程序员只关注是否逻辑相等。

－(3). equals的通用约定：

i. **自反性reflexive, x.equals(x)必须返回true**

违背此条会导致集合不能包含刚加入的方法

ii. **对称性symmetric对于任意非null的x和y，x.equals(y)和y.equals(x)必须返回相同的结果**

iii. **传递性transitivity，对于任何非null的对象x,y和z，如果x.equals(y)和y.equals(z)都返回true，那么x.equals(z)也应该返回true**

一个长例子：

首先：一个普通的point类：

```java

public class Point {

    private final int x;
    private final int y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;

    }

    public boolean equals(Object o) {
        if (o instanceof Point) {
            return false;
        }
        Point p = (Object) o;
        return p.x == x && p.y == y;
    }
}

```

拓展这个类，加入颜色点概念

```java

public class ColorPoint extends Point{

private final Color color;

    public ColorPoint(int x,int y,Color color){
        super(x,y);
        this.color=color;
    }
}

```

提供色盲对比：

```java

//Broken - violates transitivity
    public boolean equals(Object o){
        if(!(o instanceof  Point)){
            return false;
        }

        //if o is a normal Point do a color-bind comparison
        if(!(o instanceof  ColorPoint)){
            return o.equals(this);
        }

        return super.equals(o)(ColorPoint(o)).color=color;
    }

```

牺牲了传递性：两个颜色点等于一个普通点，但是颜色点之间不同

**结论：我们无法在扩展可实例化的类的同时，既增加新的组件，又能保留equals约定。**

一种权宜之计：**使用复合而非继承**。不再让ColorPoint拓展Point，而是在ColorPoint里加入一个私有的Point域以及一个公有的视图

另外的解决方法：**在抽象类的子类里加入新组件不会违反equals约定，因为不能创建超类的实例。**

```java

public class ColorPoint {

    private final Color color;
    private final Point point;

    public ColorPoint(int x, int y, Color color) {
        if (color == null)
            throw new NullPointerException();
        point = new Pont(x, y);
        this.color = color;
    }

    /**
     * returns the point-view of this color point
     *
     * @return
     */
    public Point asPoint() {
        return point;
    }

    public boolean equals(Object o) {
        if (!(o instanceof Point)) {
            return false;
        }

        ColorPoint cp = (ColorPoint) o;
        return cp.point.equals(point) && cp.equals(color);
    }

}

```

例子：TimeStamp和Date类， 不能混合使用

iv.	一致性consistency：如果两个对象相等，那么他们必须始终保持相等，直到其中有一个被修改过。

1.	不可变的类应该满足： 相等的始终相等，不等的始终不等

2.	不能使equals依赖不可靠的资源，例如URL的equals可能需要访问网络

**Equals()方法应该始终对驻留在内存里的对象执行确定性操作**

v. 非空性 null-nullity：需要类型检查，不需要额外代码

```Java

public boolean equals(Object o) {
        if (!(o instanceof MyType)) {
            return false;
        }

```
- (4). 高质量equals方法的诀窍

i.	使用 == 操作符检查“参数是否为自身的引用”：性能优化

ii.	使用instanceof操作符检查“参数是否为正确的类型”：类或者接口

iii.	把参数转化为正确的类型：上一步确保成功

iv.	对于每个关键域，检查是否匹配

- 对象引用域：递归调用equals

- Float/Double：调用Double/Float.compare

- Null域的比较

```java

(field  == null ? o.field ==null:field.equals(o.field))

```

v. 对于不可变的类使用范式

vi. 先从最有可能不一致、开销最低的域比较。不比较属于对象逻辑状态的域，例如Lock。可以适当使用冗余域，能节省时间。

vii.	使用单元测试来检查性质

- (5). 其他的告诫

i.	**必须覆盖hashCode方法**

ii.	**不要企图让equals过于智能**

iii.	**不要把equals里声明的Object替换成其他类型，否则不是override而是重载**


###第9条：覆盖equals时总要覆盖hashCode

－ （1）. 约定

i.	对于任意一个对象，只要对象中涉及equals的关键域没有改变，所有的hashCode必须返回同样的整数结果

ii.	如果两个对象的equals返回相等，那么他们的hashCode也必须相等
违反此条会导致HashMap类无法使用

iii.	如果两个对象的equals返回不等，最好返回截然不同的整数

使散列表有较高的效率

###第10条：始终要覆盖toString()

－ (1).	所有的子类都应该覆盖toString()：让类使用起来更舒适

－ (2). toString应该返回对象所包含的所有值得关注的信息

－ (3). 对象太大或者包含的状态无法用字符串描述时，应该返回摘要

－ (4).是否应该在文档里指定返回值的格式

- 值类：应该，如果指定了格式，就必须坚持，不能随意更改
- toString包含的信息应该有API提供访问
- 应该在文档里表现你的意图，
