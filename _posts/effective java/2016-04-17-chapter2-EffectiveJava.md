---
layout: post
title: 《Effective Java》第二章
categories: [Effective Java, Java]
description: 《Effective Java》学习笔记
keywords: Effective Java, Java
autotoc: true
comments: true
---

##第二章 创建和销毁对象

标签（空格分隔）： Effective Java

---

###第1条：用静态工厂方法代替构造器

* 类提供一个共有的静态工厂方法，返回类的实例；
* 注意：与工厂模式不同，并不直接对应。；
* 静态工厂方法比起构造器的优势：

  (1)：它们有名称：

  + a)：如果构造器的参数不能正确描述正被返回的对象，具有适当名称的静态方法更容易使用。<br/>

  + b)：当一个类需要多个带有相同签名的构造器时，就可以用静态工厂代替构造器<br/>
例子：BigInterger.probablePrime()

  (2)：不必每次调用它们的时候都创建新的对象：单例模式或者享元模式。<br/>
 + a)：静态工厂方法能为重复的调用返回相同的类，有助于类控制在哪个时间段存在哪些实例。被称为实例受控的类。


  (3)：他们可以返回原返回类型的任何子类型的对象。<br/>

 + a)：API可以返回对象，又不会使对象的类变成共有的，适合基于接口的框架。<br/>

 + b)：被返回的对象由相关的借口精确指定。<br/>

 + c)：共有的静态工厂方法所返回的类不仅可以是非公有的，还可以随着每次调用发生变化，这取决于工厂方法参数值。<br/>

 + d)：静态工厂方法返回的对象所属的类，在编写该静态方法时可以不必存在（留给开发者实现）<br/>

 (4)：在创建参数化实例的时候，它们使代码更为简单：<br/>

```java
Map<String, List<String>> m = new Map<String, List<String>>();
Map<String, List<String>> m = HashMap.newInstance(); //减少一次参数
```    


 * 静态工厂方法的缺点：
 1. 类如果不含公有或者受保护的构造器，就不能被子类化。<br/>
 `可能鼓励程序员使用复合，而非继承`<br/>
 2. 它们与其他静态方法没有任何区别。 <br/>

 + a)：要想查明如何实例化一个类是非常困难的；<br/>

 + b)：静态工厂方法的惯用名称：<br/>

        `ValueOf: 类型转换方法`<br/>
        `getInstance: 返回唯一的实例`<br/>
        `newInstance：保证每个返回的实例都与其他不同`<br/>
        `getType/newType：返回工厂对象的类`<br/>

###第2条：遇到多个构造器参数时要考虑构建器（建造者模式）

（1）. 重叠构造器模式：第一个构造器只有一个必要参数，第二个有一个可选参数，第三个有两个，以此类推，最后一个构造器有全部的可选参数。创建实例的时候，选择最短的列表参数的构造器<br/>
`结论：当有许多参数的时候，客户端代码会很难编写`<br/>

```java
public class NutritionFacts {

    private final int servingSize;
    private final int servings;
    private final int calories;
    private final int fat;
    private final int sodium;
    private final int carbohydrate;

    public NutritionFacts(int servingSize, int servings) {
        this.servingSize = servingSize;
        this.servings = servings;
    }

    public NutritionFacts(int servingSize, int servings, int calories) {
        this.servingSize = servingSize;
        this.servings = servings;
        this.calories = calories;
    }

    public NutritionFacts(int servingSize, int servings, int calories, int fat) {
        this.servingSize = servingSize;
        this.servings = servings;
        this.calories = calories;
        this.fat = fat;
    }

    public NutritionFacts(int servingSize, int servings, int calories, int fat, int sodium) {
        this.servingSize = servingSize;
        this.servings = servings;
        this.calories = calories;
        this.fat = fat;
        this.sodium = sodium;
    }

    public NutritionFacts(int servingSize, int servings, int calories, int fat, int sodium, int carbohydrate) {
        this.servingSize = servingSize;
        this.servings = servings;
        this.calories = calories;
        this.fat = fat;
        this.sodium = sodium;
        this.carbohydrate = carbohydrate;
    }
}
```

（2）. **Java Beans模式**：调用一个无参数构造器来创建对象，然后用setter方法设置每个必要的参数<br/>
**评价**：有严重的缺点，构造过程被分配到几个过程中，JavaBean可能处于不一致的状态。需要程序员付出额外的努力来确保它的线程安全。

```java
public class NutritionFacts {

    private   int servingSize;
    private   int servings;
    private   int calories;
    private   int fat;
    private   int sodium;
    private   int carbohydrate;

    public void setCarbohydrate(int carbohydrate) {
        this.carbohydrate = carbohydrate;
    }

    public void setServingSize(int servingSize) {
        this.servingSize = servingSize;
    }

    public void setServings(int servings) {
        this.servings = servings;
    }

    public void setCalories(int calories) {
        this.calories = calories;
    }

    public void setFat(int fat) {
        this.fat = fat;
    }

    public void setSodium(int sodium) {
        this.sodium = sodium;
    }
}
```

（3）. **建造者方法**：既能保证安全性，又能保证可读性。**最好一开始就使用Builder模式.**

```java
public class NutritionFacts3 {

    private final int servingSize;
    private final int servings;
    private final int calories;
    private final int fat;
    private final int sodium;
    private final int carbohydrate;

    public static class Builder {
        //required parameters
        private final int servingSize;
        private final int servings;

        //optional parameters
        private final int calories = 0;
        private final int fat = 0;
        private final int sodium = 0;
        private final int carbohydrate = 0;

        public Builder(int servingSize, int servings) {
            this.servingSize = servingSize;
            this.servings = servings;
        }

        public Builder calories(int val) {
            calories = val;
            return this;
        }

        public Builder fat(int val) {
            fat = val;
            return this;
        }

        public Builder carbohydrate(int val) {
            carbohydrate = val;
            return this;
        }

        public Builder sodium(int val) {
            sodium = val;
            return this;
        }

        public NutritionFacts3 build() {
            return new NutritionFacts3(this);
        }

    }


    private NutritionFacts3(Builder builder) {
        servings = builder.servings;
        servingSize = builder.servingSize;
        calories = builder.calories;
        fat = builder.fat;
        sodium = builder.sodium;
        carbohydrate = builder.carbohydrate;
    }
}


```
客户端调用：

```java
public class Client {

    NutritionFacts3 cocaCola = new NutritionFacts3.Builder(240, 80).calories(10).
            carbohydrate(20).fat(30).sodium(40).build();
}


```

 先调用类的builder方法创建一个builder，再用setter设置各个参数（注意使用return this;可以构造参数链），最后调用builder返回一个类。

a)    Builder可以进行域的检查，是否违反约束条件；<br/>
b)	Builder可以有多个可变参数；<br/>
c)	Builder方法可以自动填充域，也可以返回不同的对象；<br/>
d)	使用泛型的builder；<br/>

 ```java

 public interface Builder<T>{
        public T builder();
 }

```
e)	Java传统的抽象工厂实现是Class对象，用newInstance()来build。；<br/>
**评价**：newInstance()会主动调用无参数的构造函数，而且没有编译时错误，只能在runtime抛出异常。这破坏了编译时的异常检查。；<br/>
f)	Builder模式的不足之处：必须先构建Builder对象。可能有性能问题，必须在有很多参数时才适合使用。；<br/>


###第3条：用私有构造器或者枚举类型强化Singleton属性

```java

public class Evils{
    public static final Evils INSTANTCE=new Evils();
    private Evils(){...}

    public void leaveTheBuilding(){...}
}

```

- (1)：注意客户端可以使用**反射机制**来调用私有的构造方法：应该在类被要求创造第二个实例的时候抛出异常 AccessibleObject.setAccessble()<br/>
- (2)：公有属性的好处：组成类的成员的声明很清楚地表明了这个类是一个Singleton<br/>
- (3)：工厂方法的好处：它提供了灵活性。在不改变API的前提下可以改变该类是否为Singleton的想法，或者修改成每一个调用的线程都返回一个唯一的实例。<br/>
- (4)：序列化：仅仅加上implements Serializable是不够的，需要所有域都是瞬时的，而且提供一个**readResolve()**方法防止假冒对象。<br/>

```java

private readResolve(){
    return INSTANCE;
}

```

－ (5). 编写单个元素的枚举类型：更加简洁，无偿提供序列化机制，而且绝对防止多次实例化，是目前最好的方法。

```java

publ enum Evlils{
    INSTANCE;

}

```

###第4条：通过私有构造器强化不可被实例化的类的特性

- (1)：让工具类包含私有构造器，应该加上注释<br/>
- (2)：副作用：使该类不能被子类化<br/>

```java

public class UtilityClass {

    private UtilityClass(){...}
}

```

###第5条：避免创建不必要的对象

- (1)：如果对象不可变，就始终可以重用

```

String s = new String("aaa");//Don't do this!!

String s = "aaa";

```

- (2)：对于同时提供了静态工厂和构造器的不可变类，通常使用**静态工厂而不是构造器，以避免创建不必要的对象**；
eg：Boolean.valueOf(String)总是优先于Boolean(String)；

- (3)：重用一直不会被改变的可变对象


```
public class Person {

    private final Date birthDate;

    //Don't do this!!
    public boolean is baby() {
        Calendar cal = Calendar.getInstance(TimeZone.getTimeZone("GMT"));
        cal.set(1946, Calendar.JANUARY, 1, 0, 0);
        Date boomStart = cal.getTime();
        cal.set(1965, Calendar.JANUARY, 1, 0, 0);
        Date boomEnd = cal.getTime();
        return birthDate.compareTo(boomStart) >= 0 && birthDate.compareTo(boomEnd) < 0;
    }

}


```

以上错误代码：**创建了不必要的Date对象。**

```java

public class Person {

    private final Date birthDate;
    private final Date BOOM_START;
    private final Date BOOM_END;

    static {
        Calendar cal = Calendar.getInstance(TimeZone.getTimeZone("GMT"));
        cal.set(1946, Calendar.JANUARY, 1, 0, 0);
        BOOM_START = cal.getTime();
        cal.set(1965, Calendar.JANUARY, 1, 0, 0);
        BOOM_END = cal.getTime();
    }

    public boolean isbaby() {

        return birthDate.compareTo(boomStart) >= 0 && birthDate.compareTo(boomEnd) < 0;
    }

}

```

使用Static语句块能避免这个问题。

- (4)：适配器对象：将功能委托给后备对象，为后备对象提供接口。
       例如Map接口的keySet()函数返回时不创建新的实例
- (5)：自动装箱：优先使用基本类型而非装箱基本类型
- (6)：通过创建对象来提升程序的清晰性，简洁性和功能性，通常是好事。
- (7)：除非对象池里的对象非常重量级（数据库连接池），不然不要通过使用对象池来避免创建对象


###第6条：消除过期对象的引用

- (1)：过期引用

+ i.	栈内部维护着对过期对象的过期引用，永远也不会被解除。导致内存泄露；

```java
public Object pop() {
        if (size == 0)
            throw new EmptyStackException();
        Object result = elements[--size];
        elements[size] = null;//消除过期对象引用
        return result;
}

```

+ ii.	清空过期对象的好处：错误引用时立即抛出异常<br/>
+ iii. 清空对象应该是一种例外而非规范。最好的方法是让包含此引用的变量结束其生命周期。<br/>
+ iv.	只要类自己管理内存，程序员就应该警惕内存泄露。<br/>

- (2)：	缓存

+ i.	可以考虑使用WeakHashMap来代表缓存；<br/>
+ ii.	使用后台线程来定期清理；<br/>
+ iii.	对于更复杂的缓存，使用java.lang.ref<br/>

- (3)：	监听器和回调：只保留弱引用

###第7条：避免使用终结方法finalize()

- (1):**终结方法不可预测，很危险**

+ i.不能保证会及时被执行，甚至不能保证执行：time-critical的任务不应该由终结方法执行。例如关闭已经打开的文件（文件描述符是很有限的资源）

+ ii.不能保证在所有的JVM上都得到同样的体验

+ iii.可能随意延迟其实例的回收过程

+ iv. **不应该依赖终结方法来更新重要的持久状态，例如分布式系统的永久锁**

+ v.不要使用System.gc()等函数**

- (2). 如果在终结方法执行时抛出未捕获异常，则此异常会被忽略，终结方法也将终止
- (3). **非常严重的性能损失**
- (4). 使用显式的终止方法，并要求客户端在每个实例不再有用时调用这个方法。该实例必须记录自己是否被终止，终止后的调用要抛出异常。

例子：InputStream的close函数， Timer的cancel函数等
应该在try-catch块的finally部分调用这个显式终止方法

```java

Foo foo=new Foo();

    try{

    }catch{

    }finally{
        foo.terminate()
    }

 ```

 - (5).终结方法的正确使用

+ i.在对象的所有者忘记调用显式终止方法时，充当安全网函数。

+ ii.对象的本地对等体：终结方法释放本地对等体的重要资源

- (6). 子类覆盖终结方法时，需要显式调用父类的终结方法

 ```java

 @override
   protected Object finalize() throws Throwable {
        try {

        }finally {
            super.finalize();
        }
   }

```

- (7). 终结方法守卫者：匿名内部类，终结它的外围实例。用于子类忘记调用父类的终结方法的一种补救措施

```java

public class Foo {


    private final Object finalizerGuration = new Object() {
        @override
        protected Object finalize() throws Throwable {
            try {

            } finally {
                super.finalize();
            }
        }
    }
}

```

对于每一个带有自定义终结方法的非final公有类，都应该考虑使用这个方法。
