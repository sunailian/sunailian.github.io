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

###第28条：利用有限制通配符来提升API的灵活性

- (1). 假设我们有一个泛型Number的Stack，我们想把Integer加入这个Stack中，尽管直观上是可以的，并且push(intVal)也是可以的，但是下面这个方法是错误的

```Java
//pushAll method without wildcard type -deficient!
public void pushAll(Iterable<E> src){
  for(E e : src){
    push(e);
  }
}

```

因为泛型是不可具体化的类，Iterable<Integer>并不是Iterable<Number>的子类
使用有限制的通配符：<? extends E>

```Java
//wildcard type for parameter that serves as an E producer
public void pushAll(Iterable<? extends E> src){
  for(E e : src){
    push(e);
  }
}

```
- (2). 相对应的，如果想把元素pop到一个给定的集合，那么下面这个方法是错误的

```Java
//popAll method without wildcard type - deficient
public void popAll(Collection<E> dst){
  while(!isEmpty){
    dst.add(pop());
  }
}

```

原因相同。此时应该使用有限制的通配符：<? super E>

```Java

//wildcard type for parameter that serves as an E consumer
public void popAll(Collection<? super E> dst){
  while(!isEmpty)
      dst.add(pop());
}

```

- (3). 原则：PECS， producer extends；consumer super。【函数调用者】生产数据给方法所在的实例：extends；【函数调用者】从实例里获得数据：super

- (4). 因此，之前的合并集合操作，其签名应该是

```Java

public static <E> Set<E> union (Set<? extends E> s1,Set<? extends E> s2)

```

- (5). 下面这个方法是错误的，类型推导无法得出正确的结果

```Java
Set<Integer> integers = ....;
Set<Double> doubles = ... ;
Set<Number> numbers = union(integers,doubles);

```

这个时候我们需要显式声明类型:

```Java
Set<Number> numbers = Union.<Number> union(integers,double);

```

- (6). 之前的max操作，其签名应该声明为

```Java
public static <T extends Comparable<? super T>> T max(List<? extends T> list)

```

为了从初始声明中得到修改后的版本，要应用pecs转换两次。最直接的上运用到参数list。它产生T实例，因此将类型从List<T> 改成 List<? extends T>。更灵活的是运用到类型参数T.这是我们第一次见到将通配符运用到类型参数，最初T被指定用来扩展Comparable<T>，但是T的Comparable消费T实例（并产生表示顺序关系的整值）。因此，参数化类型Comparable<T>被有限制通配符类型Comparable<? super T>取代。comparable始终是消费者，因此`使用时始终应该是Comparable<? super T>优先于Comparable<T>`。对于comparator也是一样。

- (7). 无限制的通配符与无限制的类型参数

```Java

//Two possible declarations for the swap method
public static <E> void swap(List<E> list,int i,int j);
public static void swap(List<?> list,int i,int j);

```
 + i.	如果类型参数只出现一次，那就应该使用通配符
 + ii.	无限制的类型参数阻止调用者将任何非null的元素加入已有集合。也即List<?>不能放入任何参数
 + iii.	解决方法：使用辅助方法。把泛型声明暴露给用户，在内部使用私有的实现

```Java
public static void swap(List<?> list,int i,int j){
  swapHelper(list,i,j);
}

//private helper method for wildcard capture
private static <E> void swapHelper(List<E> list,int i,int j){
  list.set(i,list.set(j,list.get(i)));
}

```

###第29条：优先考虑类型安全的异构容器

- (1). 问题：使用类型安全的方式访问数据库的所有列，避免使用原生态
解决：**将Key参数化，而不是将container参数化**
**使用Class<T>, T这样的键值对，这被称为类型安全的异构容器**

```Java

//Typesafe heterogeneous container pattern - implementation
public class Favorites{
   private Map<Class<?>,Object> favorites = new HashMap<Class<?>,Object>();

   public <T> void putFavorite(Class<T> type,T instance){
     if(type == null){
       throw new NullPointerException("Type is Null");
     }
     favorites.put(type,instance);
   }

   public <T> getFavorite(Class<T> type){
     return type.cast(favorites.get(type));
   }

}


//Typesafe heterogeneous container pattern -client
public static void public static void main(String[] args) {
  Favorites f = new Favorites();
  f.putFavorite(String.class,"Java");
  f.putFavorite(Integer.class,0xcafebabe);
  f.putFavorite(Class.class,Favorites.class);
  String favoriteString = f.getFavorite(String.class);
  int favoriteInteger = f.getFavorite(Integer.class);
  Class<?> favoriteClass = f.getFavorite(Class.class);
  System.out.printf("%s %x %s%n",favoriteString,favoriteInteger,favoriteClass.getName());
}

```

**注意：**

 + i.	Map的值是Object，这样不保证键值一样，但是我们的代码确保了这一点
 + ii.	getFavorite方法做了类型转换

- (2). 该容器的局限性

 + i.	放入时有可能破坏键值约定
      解决方法：**放入时进行转换，同时节省了检查指针为空**

```Java

//Achiving runtime type safety with a dynamic cast
public <T> void putFavorite(Class<T> type,T instance){
  favorites.put(type,type.cast(instance));
}

```      

 + iii.	不能用在不可具体化的类上，例如List<String>.class是不存在的

- (3). 使用注解API
