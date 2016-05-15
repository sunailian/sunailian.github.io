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

- (1). 确定应该/不应该覆盖equals方法的情况

 + i. 类的每个实例本质上是唯一的

 + ii. 不关心类是否提供了“逻辑相等“的测试功能。

 + iii. 超类已经覆盖了equals，并且从超类继承过来的子类也是合适的
例如：从AbstractSet继承equals

 + iv. 类是私有的或者包级私有的：**此时应该覆盖并抛出异常。**

```java

@override
        public boolean equals(Obeject o) throws Throwable {
           throw new AssertionError();//Method never called
        }
```

- (2). 可以考虑覆盖equals方法：“值类”。程序员只关注是否逻辑相等。

- (3). equals的通用约定：

 + i. **自反性reflexive, x.equals(x)必须返回true**

违背此条会导致集合不能包含刚加入的方法

 + ii. **对称性symmetric对于任意非null的x和y，x.equals(y)和y.equals(x)必须返回相同的结果**

 + iii. **传递性transitivity，对于任何非null的对象x,y和z，如果x.equals(y)和y.equals(z)都返回true，那么x.equals(z)也应该返回true**

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

 + iv.	一致性consistency：如果两个对象相等，那么他们必须始终保持相等，直到其中有一个被修改过。

1.	不可变的类应该满足： 相等的始终相等，不等的始终不等

2.	不能使equals依赖不可靠的资源，例如URL的equals可能需要访问网络

**Equals()方法应该始终对驻留在内存里的对象执行确定性操作**

 + v. 非空性 null-nullity：需要类型检查，不需要额外代码

```Java

public boolean equals(Object o) {
        if (!(o instanceof MyType)) {
            return false;
        }

```

- (4). 高质量equals方法的诀窍

 + i.	使用 == 操作符检查“参数是否为自身的引用”：性能优化

 + ii.	使用instanceof操作符检查“参数是否为正确的类型”：类或者接口

 + iii.	把参数转化为正确的类型：上一步确保成功

 + iv.	对于每个关键域，检查是否匹配

- 对象引用域：递归调用equals

- Float/Double：调用Double/Float.compare

- Null域的比较

```java

(field  == null ? o.field ==null:field.equals(o.field))

```

 + v. 对于不可变的类使用范式

 + vi. 先从最有可能不一致、开销最低的域比较。不比较属于对象逻辑状态的域，例如Lock。可以适当使用冗余域，能节省时间。

 + vii.	使用单元测试来检查性质

- (5). 其他的告诫

 + i.	**必须覆盖hashCode方法**

 + ii.	**不要企图让equals过于智能**

 + iii.	**不要把equals里声明的Object替换成其他类型，否则不是override而是重载**


###第9条：覆盖equals时总要覆盖hashCode

- (1). 约定

 + i.	对于任意一个对象，只要对象中涉及equals的关键域没有改变，所有的hashCode必须返回同样的整数结果

 + ii.	如果两个对象的equals返回相等，那么他们的hashCode也必须相等
违反此条会导致HashMap类无法使用

 + iii.	如果两个对象的equals返回不等，最好返回截然不同的整数

使散列表有较高的效率

###第10条：始终要覆盖toString()

- (1). 所有的子类都应该覆盖toString()：让类使用起来更舒适

－ (2). toString应该返回对象所包含的所有值得关注的信息

－ (3). 对象太大或者包含的状态无法用字符串描述时，应该返回摘要

－ (4).是否应该在文档里指定返回值的格式

- 值类：应该，如果指定了格式，就必须坚持，不能随意更改

- toString包含的信息应该有API提供访问

- 应该在文档里表现你的意图

###第11条：谨慎地覆盖clone()

－ (1). Object的clone方法是受保护的，所以实现cloneable接口并不能调用clone，除非使用反射机制。

- (2). Cloneable接口的作用：**决定了Object中受保护的clone方法的实现行为**

 + i.	**如果实现接口。Clone()会返回这个类的逐级拷贝**

 + ii.	**如果不实现：抛出CloneNotSupportedException**

 + iii.	实现了一种语言之外的机制：**无需调用构造器就可以创建对象**

- (3). 约定（比较弱，不是绝对的要求）

 + i.	x.clone(x) != x

 + ii.	x.clone().getClass == x.getClass()

 + iii.	x.clone().equals(x) == true

- (4).	不调用构造器的规定太强硬：可以使用构造器/类是final的时候可能直接返回构造器创建的对象

- (5). **如果覆盖了非final类的clone方法，就应该返回一个通过调用super.clone()而得到的对象**

- (6).对于实现了Cloneable的类，我们希望它提供公有方法

- (7).例子：

```Java

public PhoneNumber clone() {
        try {
            return (PhoneNumber) super.clone();
        } catch (CloneNotSupportedException e) {
            throw new AssertionError();//can't happen
        }
    }

```

**注意clone返回的是PhoneNumber类：永远不要让客户做任何类库可以为他们做到的事情**

- (8).实际上，clone方法是另一个构造器

 + i.	不能伤害到原始对象

 + ii.	正确创建被克隆对象中的约束条件

 + iii.	例如：Stack类的clone方法应该在栈里递归调用clone

 + iv.	如果某个域是final的，clone方法禁止给elements域赋新值：**可能需要去掉final修饰符**

- (9). 最后一种办法：先调用super.clone，然后把所有的域置成空白状态，然后调用高层方法重现产生对象的状态：性能不高

- (10). Clone不应该在构造的过程中调用新对象的任何非final方法：可能导致不一致

- (11). 公有的clone方法

 + i.	不应该抛出异常

 + ii.	调用super.clone

 + iii.	修正任何需要修正的域

- (12). 如果要为了继承而覆盖clone方法应该

 + i.	声明为protected，抛出异常

 + ii.	不能实现cloneable接口

- (13). Object的clone没有同步，应该实现同步

- (14). 最好提供某些其他的途径来代替对象拷贝，或者不提供类似方法

 + i.	拷贝构造器：public Yum(Yum yum)

 + ii.	拷贝工厂：public static Yum newInstance(Yum yum)

 + iii.	可以带一些其他参数

- (15). 作为一个专门为继承而设计的类，如果不能提供良好的protected方法，子类就不能实现cloneable接口

###第12条：考虑实现Comparable接口

－（1）. 实现Comparable接口表明它的实例具有内在的排序关系。一旦类实现了Comparable接口，就可以和很多泛型算法进行协作。**如果你正在编写一个值类，并且具有非常明显的顺序关系，那么就应该坚决实现这个接口**

- (2). compareTo方法约定

 + i.	将当前对象与参数比较，当对象小于，等于或者大于参数时，返回一个负整数，0和正整数。**无法比较或者类不同时则抛出ClassCastException**。

 + ii.	实现者必须满足自反性，传递性等性质.
与equals相同，**我们无法在扩展可实例化的类的同时，既增加新的组件，又能保留compareTo约定。**权且之计也可以在这里使用

 + iii.	compareTo施加的顺序关系应该同equals保持一致

 + iv.	如果一个域没有实现compareTo或者需要一个非标准的排序，可以使用显式的Comparator来代替

```java

public final class CaseInsensitiveString implements Comparable<CaseInsensitiveString> {
        public int compareTo(CaseInsensitiveString cis) {
            return String.CASE_INSENSITIVE_ORDER.compare(s, cis.s);
        }
    }

```

注意这里实现的接口确保了比较时不能跨类型

- (3). 比较的顺序

 + i.	从最关键的域开始，如果出现非0结果，则比较结束

 + ii.	返回减法结果是确认相关的域不能为负数
