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
 参数化的类型|`List<String>``
实际参数类型 |String
 泛型|`List<E>``
 形式类型参数|E
 无限制通配符类型|List<?>
 原生态类型|List
 有限制类型参数|`<E extends Number>`
 递归类型限制|`<T extends Comparable<T>>`
 有限制通配符类型|List<? extends Number>
 泛型方法|`static <E> List<E> asList(E[] a)``
 类型令牌|String.class   

###第24条：消除非受检警告：unchecked warning

- (1). 如果无法消除警告，同时可以证明引起警告的代码是类型安全的。那么可以使用@SupressWarnings(“unchecked”)来禁止警告。必须在每次使用这条注解的后面加注释，说明为什么要这么做
- (2). 应该始终在尽可能小的范围内使用SupressWarnings注解，永远不要在整个类上使用这条注解
- (3). Return不能使用这条注解。应该声明一个局部变量并注解

###第25条：列表优先于数组

- (1). 数组是协变的：如果Sub是Super的子类型，那么Sub[]也是Super[]的子类型

```java

//Fails at runtime
Object[] objectArray = new Long[1];
objectArray[0]="I don't fit in";//Throw ArrayStoreException

//下面这段代码不合法

//Won't compile
List<Object> ol = new ArrayList<Long>();//Incompatible types
ol.add("I don't fit in")

```

- (2). 数组是具体化的，直到**运行时才知道并检查他们的元素类型约束**。而泛型在编译时强化类型约束，在运行时擦除类型信息，不可具体化，指的是运行时包含的信息比编译时更少。不可具体化的类型的数组转换只能在特殊情况下使用。

- (3). 创建泛型数组是非法的，并非类型安全，会抛出ClassCastException异常。但是创建List<?>或者Map<?,?>的数组是合法的
List<String>是Object的子类，因此List<String>也是Object[]的子类。
**解决方法：优先使用集合类型List<E>**

```java

List<String>[] stringLists = new List<String>[1];
List<Integer> intList = Arrays.asList(42);
Object[] objects = stringLists;
objects[0]=intList;
String s = stringLists[0].get(0);

```

###第26条：优先考虑泛型

- (1). 泛型Stack类的应用

```java

public class Stack {

    private Object[] elements;
    private int size = 0;
    private static final int DEFAULT_INITIAL_CAPACITY = 16;

    public Stack() {
        elements = new Object[DEFAULT_INITIAL_CAPACITY];
    }

    public void push(Object e) {
        ensureCapacity();
        elements[size++] = e;
    }

    public Object pop() {
        if (size == 0)
            throw new EmptyStackException();
        Object result = elements[--size];
        elements[size] = null;
        return result;
    }

    public boolean isEmpty() {
        return size == 0;
    }

    private void ensureCapacity() {
        if (elements.length == size) {
            elements = Arrays.copyOf(elements, 2 * size + 1);
        }
    }
}

```

我们将其改造为使用泛型。将类泛型化的第一个步骤是给它的声明添加一个或者多个参数类型。

```java


public class Stack<E> {

   private E[] elements;
   private int size = 0;
   private static final int DEFAULT_INITIAL_CAPACITY = 16;

   public Stack() {
       elements = new E[DEFAULT_INITIAL_CAPACITY];
   }

   public void push(E e) {
       ensureCapacity();
       elements[size++] = e;
   }

   public E pop() {
       if (size == 0)
           throw new EmptyStackException();
       E result = elements[--size];
       elements[size] = null;
       return result;
   }

   public boolean isEmpty() {
       return size == 0;
   }

   private void ensureCapacity() {
       if (elements.length == size) {
           elements = Arrays.copyOf(elements, 2 * size + 1);
       }
   }
}

```

这里`elements = new E[DEFAULT_INITIAL_CAPACITY]`会报警告，不能创建泛型数组。这里可以改成Object数组，然后强制转化成E。具体实战分析。

- (2). Java并不是天生就支持泛型。很多泛型如ArrayList必须在数组上实现；为了提升性能，很多其他泛型也在数组上实现，例如HashMap类
- (3). 不能创建基本类型的泛型，可以通过装箱基本类型来实现。
- (4). E必须是Delayed的子集，这被称为有限制的类型参数

```Java

class DelayQueue<E extends Delayed> implements BlockingQueue<E>

```

**总而言之，使用泛型比需要在客户端代码中进行转换的类型来的更加安全**

###第27条：优先考虑泛型方法

静态工具方法尤其适用于泛型化。Collections中所有的“算法”方法基本是都泛型化了。

- (1). 一个典型的泛型方法

```Java

public static <E> Set<E> union(Set<E> s1,Set<E> s2){
       Set<E> result = new HashSet<E>(s1);
       result.addAll(s2);
       return result;
}

```

- (2). 泛型方法的特性：**无需明确指定类型参数的值，不像调用泛型构造器一样**。这被称为类型推导 type inference

- (3). **泛型单例工厂**创建许多不可变但适合于多个不同的对象，需要编写一个静态方法，重复的给每个必要的类型参数分发对象。

```Java

//类型参数出现在变量声明的左右两边,显得有些冗余
    Map<String, List<String>> anagrams = new HashMap<String, List<String>>();


    //静态泛型工厂方法
    public static <K, V> HashMap<K, V> newHashMap() {
        return new HashMap<K, V>();
    }

    Map<String, List<String>> anagrams = newHashMap();

```

下文例子中的未受检异常是可以接受的，因为所有的代码都在我们的控制之下。

```Java

    //Gneric singleton factory pattern
    private static UnaryFunction<Object> IDENTITY_FUNCTION=new UnaryFunction<Object>({
            public Object apply(Object arg){
               return arg;
            }
    });

    //IDENTITY_FUNCTION is stateless and its type parameter is unbounded so it's safe
    //to share one instance across all types
    public static <T> UnaryFunction<T> identityFunction(){
        return (UnaryFunction<T>) IDENTITY_FUNCTION;
    }

  ```

- (4). **递归类型限制：通过某个包含该类型本身的表达式来限制类型参数**。

例子：通过Comparable接口拓展的Max方法

```Java

public interface Comparable<T>{
  int compareTo(T o);
}

//Using a recursive type bound to express mutual comparability
public static <T extends Comparable<T>> T max(List<T> list){...}

```

这个方法可以读作：**针对每个可以和自身比较的类型T**

完整方法：

```Java

//Returns the maximum value in a list - use recursive type bound
public static <T extends Comparable<T>> T max(List<T> list){
  Iterator<T> iter = list.iterator();
  T result = iter.next();
  while(iter.hasNext){
    T t = iter.next;
    if (t.compareTo(result)>0) {
      result = t;
    }
  }
  return result;
}


```
