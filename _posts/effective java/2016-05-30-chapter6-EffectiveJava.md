---
layout: post
title: 《Effective Java》第六章
categories: [Effective Java, Java]
description: 《Effective Java》学习笔记
keywords: Effective Java, Java
autotoc: true
comments: true
---

##第六章 注解与枚举

标签（空格分隔）： Effective Java

---

###第30条：用enum常量代替int常量

- (1). 枚举常量：有一组固定的常量组成合法值的类型
- (2). int枚举类型
 + 使用方便性和类型安全方面没帮助，如果将APPLE传入ORANGE也没有任何警告
 + 十分脆弱，如果和枚举常量相关的int类型发生变化，客户端就必须重新编译
 + 没法把枚举常量打印或者遍历

 ```Java

//The int enum pattern - serverely deficient
public static final int APPLE_FUJI = 0;
public static final int APPLE_PIPPEN = 1;


 ```

- (3). String枚举模式，更糟糕
 + 性能问题，依赖字符串比较
 + 导致用户把字符串常量强行编码，可变性差而且容易出错

- (4). 最简单的枚举模式：**通过共有的静态final域为每个枚举常量导出实例的类**

```Java

public enum Apple{FUJI,PIPPIN,GRANNY_SMITH}
public enum Orange{NAVEL,TEMPLE,BLOOD}

```

- (5). 提供了编译时类型安全，自动命名空间隔离
- (6). 可以增加枚举类型的常量而无需重新编译它的客户端代码：隔离层
- (7). 可以调用toString方法将枚举常量转换成可打印的字符串
- (8). **可以添加任意的方法和域：例如使用枚举常量直接获得相关数据**

```Java
// Enum type with data and behavior - Pages 149-150
public enum Planet {
    MERCURY(3.302e+23, 2.439e6),
    VENUS  (4.869e+24, 6.052e6),
    EARTH  (5.975e+24, 6.378e6),
    MARS   (6.419e+23, 3.393e6),
    JUPITER(1.899e+27, 7.149e7),
    SATURN (5.685e+26, 6.027e7),
    URANUS (8.683e+25, 2.556e7),
    NEPTUNE(1.024e+26, 2.477e7);
    private final double mass;           // In kilograms
    private final double radius;         // In meters
    private final double surfaceGravity; // In m / s^2

    // Universal gravitational constant in m^3 / kg s^2
    private static final double G = 6.67300E-11;

    // Constructor
    Planet(double mass, double radius) {
        this.mass = mass;
        this.radius = radius;
        surfaceGravity = G * mass / (radius * radius);
    }

    public double mass()           { return mass; }
    public double radius()         { return radius; }
    public double surfaceGravity() { return surfaceGravity; }

    public double surfaceWeight(double mass) {
        return mass * surfaceGravity;  // F = ma
    }
}

```

为了将数据和枚举常量关联起来，得声明实例域并编写一个带有数据并将数据保存在域中的构造器。枚举类型天生不可变，因此所有域都必须是final。最好设置为私有并提供公有的访问方法。使用方法如下：

```Java

// Takes earth-weight and prints table of weights on all planets - Page 150
public class WeightTable {
   public static void main(String[] args) {
      double earthWeight = Double.parseDouble(args[0]);
      double mass = earthWeight / Planet.EARTH.surfaceGravity();
      for (Planet p : Planet.values())
          System.out.printf("Weight on %s is %f%n",
                            p, p.surfaceWeight(mass));
   }
}

```
Planet.values()返回它的值数组

- (9). 除非迫不得已把枚举方法导出它的客户端，否则都应该声明成私有或者包级私有
- (10). 关联本质上不同的行为：看下面例子

```Java

//Enum type that swithes on its own value - questionable
public enum Operation{
  PLUS,MINUS,TIMES,DIVIDE;

  //Do the arithmetic op represented by this constant
  double apply(double x,double y){
    switch(this){
      case PLUS:return x + y;
      case MINUS:return x - y;
      case TIMES:return x * y;
      case DIVIDE:return x / y;
    }
    throw new AssertionError("Unkown op:"+this);
  }
}

```

这段代码的脆弱之处：

- 如果没有throw语句就不能编译

- 加入新的计算方法时，如果忘记在switch里加入条件，**无法在编译时得到错误**：

- 一个好的解决方法：**在枚举类型中声明一个抽象的apply方法，并在特定于常量的类主体中，用具体的方法覆盖每个常量的抽象方法。这叫做constant-specific method implementation**

```Java

// Enum type with constant-specific class bodies and data - Page 153

public enum Operation {
    PLUS("+") {
        double apply(double x, double y) { return x + y; }
    },
    MINUS("-") {
        double apply(double x, double y) { return x - y; }
    },
    TIMES("*") {
        double apply(double x, double y) { return x * y; }
    },
    DIVIDE("/") {
        double apply(double x, double y) { return x / y; }
    };
    private final String symbol;
    Operation(String symbol) { this.symbol = symbol; }
    @Override public String toString() { return symbol; }

    abstract double apply(double x, double y);

    // Implementing a fromString method on an enum type - Page 154
    private static final Map<String, Operation> stringToEnum
        = new HashMap<String, Operation>();
    static { // Initialize map from constant name to enum constant
        for (Operation op : values())
            stringToEnum.put(op.toString(), op);
    }
    // Returns Operation for string, or null if string is invalid
    public static Operation fromString(String symbol) {
        return stringToEnum.get(symbol);
    }


    // Test program to perform all operations on given operands
    public static void main(String[] args) {
        double x = Double.parseDouble(args[0]);
        double y = Double.parseDouble(args[1]);
        for (Operation op : Operation.values())
            System.out.printf("%f %s %f = %f%n",
                              x, op, y, op.apply(x, y));
    }
}

```

这样如果忘记添加，就会得到编译错误

- (11). 与toString相对应的fromString方法：使用共有静态常量域+Map

```Java
//Implementing a fromString method on an enum type
private static final Map<String,Operation> stringToEnum = new HashMap<String, Operation>();
static{//Initialize map from constant name to enum constant
  for(Operation op:values()){
    stringToEnum.put(op.toString(),op);
  }

  //Returns Operation for string, or null if string is invalid
  public static Operation fromString(String symbol){
    return stringToEnum.get(symbol);
  }
}
```

- (12). 策略枚举：看下面的案例

```Java
//Enum that switches on its value to share code - questionable
enum PayrollDay{
  MONDAY,TUESDAY,WEDNESDAY,THURSDAY,FRIDAY,SATURDAY,SUNDAY;
  private static final int HOURS_PER_SHIFT = 8;
  double pay(double hoursWorked,double payRate){
    double basePay = hoursWorked * payRate;

    double overtimePay ; //Calculate overtime pay
    switch(this){
      case SATURDAY:
      case SUNDAY:
        overtime = hoursWorked * payRate / 2 ;
      default: //weekdays
        overtime = hoursWorked <= HOURS_PER_SHIFT ? 0 :(hoursWorked - HOURS_PER_SHIFT) * payRate / 2;
      break;
    }
    return basePay + overtimePay;
  }
}

```

这段代码的问题是：**如果忘记在switch里添加计算方法，那么编译不会报错**

我们真正想要的是：**每当添加一个枚举常量，就要强制选择一种加班策略**

**策略模式：把加班工时的计算委托给一个私有嵌套枚举类，安全而灵活**

```Java

// The strategy enum pattern
enum PayrollDay {
    MONDAY(PayType.WEEKDAY), TUESDAY(PayType.WEEKDAY),
    WEDNESDAY(PayType.WEEKDAY), THURSDAY(PayType.WEEKDAY),
    FRIDAY(PayType.WEEKDAY),
    SATURDAY(PayType.WEEKEND), SUNDAY(PayType.WEEKEND);

    private final PayType payType;
    PayrollDay(PayType payType) { this.payType = payType; }

    double pay(double hoursWorked, double payRate) {
        return payType.pay(hoursWorked, payRate);
    }
    // The strategy enum type
    private enum PayType {
        WEEKDAY {
            double overtimePay(double hours, double payRate) {
                return hours <= HOURS_PER_SHIFT ? 0 :
                  (hours - HOURS_PER_SHIFT) * payRate / 2;
            }
        },
        WEEKEND {
            double overtimePay(double hours, double payRate) {
                return hours * payRate / 2;
            }
        };
        private static final int HOURS_PER_SHIFT = 8;

        abstract double overtimePay(double hrs, double payRate);

        double pay(double hoursWorked, double payRate) {
            double basePay = hoursWorked * payRate;
            return basePay + overtimePay(hoursWorked, payRate);
        }
    }
}

```

- (13). 枚举中的switch适合给外部的枚举类型增加特定于常量的方法

```Java
//switch on an enum to simulate a missing method
public static Operation inverse(Operation op){
  switch(op){
    case Plus:return Operation.MINUS;
    case MINUS:return Operation.PLUS;
    case TIMES:return Operation.DIVIDE;
    case DIVIDE:return Operation.TIMES;
    default:throw new AssertionError("Unkown op:"+op);
  }
}

```

- (14). 使用枚举的时候：需要一组固定常量，或者编译时就知道所有可能值的集合

###第31条：用实例域代替序数

- (1). 永远不要使用ordinal()来获得序数。所有的序数都应该保存在实例域里

```Java

// Abuse if ordinal to derive an assosiate value - DON'T DO THIS
public enum Ensemble {
    SOLO, DUET, TRIO, QUARTET, QUINTET,
    SEXTET, SEPTET, OCTET,
    NONET, DECTET;

    private final int numberOfMusicians{return ordinal() +1 };
}

```

- (2). Ordinal()用在enumSet这样的类中，如果不编写这样的类就不要使用这个函数

```Java

// Enum with integer data stored in an instance field
public enum Ensemble {
    SOLO(1), DUET(2), TRIO(3), QUARTET(4), QUINTET(5),
    SEXTET(6), SEPTET(7), OCTET(8), DOUBLE_QUARTET(8),
    NONET(9), DECTET(10), TRIPLE_QUARTET(12);

    private final int numberOfMusicians;
    Ensemble(int size) { this.numberOfMusicians = size; }
    public int numberOfMusicians() { return numberOfMusicians; }
}

```


###第32条：用EnumSet代替位域

- (1). 位域的通常做法

```Java

//Bit field enumeration constants - OBSOLETE
public class Text{
  public static final STYLE_BOLD = 1 << 0; // 1
  public static final STYLE_ITALIC = 1 << 1; // 2
  public static final STYLE_UNDERLINE = 1 << 2; // 4
  public static final STYLE_STRIKETHROUGH = 1 << 3;//8

  //Parameter is bitwise or if zero or more STYLE_constants
  public void applyStyles(int style){...}

}

```

- (2). 位域的不足之处：

 + 使用方便性和类型安全方面没帮助
 + 没法把枚举常量打印或者遍历

 - (3). EnumSet的做法：

 ```Java

 // EnumSet - a modern replacement for bit fields - Page 160
 public class Text {
     public enum Style { BOLD, ITALIC, UNDERLINE, STRIKETHROUGH }

     // Any Set could be passed in, but EnumSet is clearly best
     public void applyStyles(Set<Style> styles) {
         // Body goes here
     }

     // Sample use
     public static void main(String[] args) {
         Text text = new Text();
         text.applyStyles(EnumSet.of(Style.BOLD, Style.ITALIC));
     }
 }

 ```

使用EnumSet更加灵活

###第33条：用EnumMap代替序数索引

- (1). 来看一个错误案例

```Java

public class Herb{
  public enum Type{ANNUAL,PERENNIAL,BIENNIAL}

  private final String name;
  private final Type type;

  Herb(String name,Type type){
    this.name = name;
    this.type = type;
  }

  public String toString(){
    return name;
  }
}


//Using ordinal() to index an array - DON'T DO THIS
Herb[] garden = ...;
Set<Herb> herbsByType = // Indexed by Herb.Type.ordinal()
     (Set<Herb>[])new Set[Herb.Type.values().length];
for (int i =0;i<herbsByType.length ; i++) {
  herbsByType[i] = new HashSet<Herb>();
}

for (Herb h:garden) {
  herbsByType[h.type.ordinal()].add(h);
}

//Print the results
for (int i = 0;i<herbsByType.length ;i++ ) {
  System.out.print("%s:%s%n",Herb.Type.values()[i],herbsByType[i]);
}

```

这段代码的不足之处在于：**使用错误的int值无法在编译时得到警告，难以查错**

```Java

//Using an EnumMap to assosiate data with an enum
Map<Herb.Type,Set<Herb>> herbsByType = new EnumMap<Herb.Type,Set<Herb>>(Herb.Type.class);
for(Herb.Type t : Herb.Type.values()){
  herbsByType.put(t,new HashSet<Herb>());
}

for (Herb h : garden ) {
  herbsByType.get(h.type).add(h);
}

System.out.println(herbsByType);

```

用EnumMap改写过的版本没有安全转换，不会出现索引问题

- (2). 一个更复杂的例子：给定两个状态，求出两个状态之间转换的行为

###第34条：使用接口模拟可伸缩的枚举

- (1). 可伸缩性：**让一个枚举类型去拓展另一个枚举类型。目前还没有很好的方法来枚举基本类型的所有元素及其扩展。**
- (2). 典型用例：操作码，opcode。枚举加接口实现。枚举类型虽然不可拓展，但是接口是可以拓展的。如果想写一个“子类”，那么重新编写一个枚举类并且实现接口即可。

```Java

// Emulated extension enum - Page 166-167

import java.util.*;

public enum ExtendedOperation implements Operation {
    EXP("^") {
        public double apply(double x, double y) {
            return Math.pow(x, y);
        }
    },
    REMAINDER("%") {
        public double apply(double x, double y) {
            return x % y;
        }
    };

    private final String symbol;
    ExtendedOperation(String symbol) {
        this.symbol = symbol;
    }
    @Override public String toString() {
        return symbol;
    }

    // Test class to exercise all operations in "extension enum" - Page 167
    public static void main(String[] args) {
        double x = Double.parseDouble(args[0]);
        double y = Double.parseDouble(args[1]);
        test(ExtendedOperation.class, x, y);

        System.out.println();  // Print a blank line between tests
        test2(Arrays.asList(ExtendedOperation.values()), x, y);
    }

    // test parameter is a bounded type token  (Item 29)
    private static <T extends Enum<T> & Operation> void test(
            Class<T> opSet, double x, double y) {
        for (Operation op : opSet.getEnumConstants())
            System.out.printf("%f %s %f = %f%n",
                              x, op, y, op.apply(x, y));
    }

    // test parameter is a bounded wildcard type (Item 28)
    private static void test2(Collection<? extends Operation> opSet,
                              double x, double y) {
        for (Operation op : opSet)
            System.out.printf("%f %s %f = %f%n",
                              x, op, y, op.apply(x, y));
    }
}

```

- (2). 使用测试函数遍历所有枚举
 + 方法1：使用有限制的类型令牌

 ```Java
public static void main(String[] args){
  double x = Double.parseDouble(args[0]);
  double y = Double.parseDouble(args[1]);
  test(ExtendedOperation.class,x,y);
}

private static <T extends Enum<T> & Operation> void test(Class<T> opSet,double x,double y){
  for(Operation op : opSet.getEnumConstants()){
    System.out.printf("%f %s %f = %f%n",x,op,y,op.apply(x,y));
  }
}

 ```

 + 方法2：使用有限制的通配符

```Java
public static void main(String[] args){
  double x = Double.parseDouble(args[0]);
  double y = Double.parseDouble(args[1]);
  test(ExtendedOperation.class,x,y);
}

private static  void test(Collection<? extends Operation> opSet,double x,double y){
  for(Operation op : opSet.getEnumConstants()){
    System.out.printf("%f %s %f = %f%n",x,op,y,op.apply(x,y));
  }
}

```

- (3). values()方法是编译器插入到enum定义中的static方法，所以，当你将enum实例向上转型为父类Enum时，values()就不可访问了。解决办法：在Class中有一个getEnumConstants()方法，所以即便Enum接口中没有values()方法，我们仍然可以通过Class对象取得所有的enum实例


###第35条：注解优先于命名模式

- (1). 命名模式的缺点
 + 拼写错误会导致失败而且没有任何提示。例子：Junit要求用户以test开始，但是拼写错误会导致JUint不报错也不执行相应的测试程序
 + 命名模式不能用在特定的程序元素上。例子：在类名前加test不能自动执行这个类的所有方法。
 + 命名模式不能把参数值和程序元素关联起来。如果测试在抛出某个特定异常时才算通过，就不能把异常编码进函数名中。
- (2). 使用注解

```Java

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface Test{

}

```

- 第一行注解：test注解在运行时保留
- 第二行注解：test注解只有在方法中才是合法的，不能用在其他程序元素上
- 注意：**test注解只能用在无参数的静态方法上**

```Java

// Program containing marker annotations - Page 170
public class Sample {
    @Test public static void m1() { }  // Test should pass
    public static void m2() { }
    @Test public static void m3() {    // Test Should fail
        throw new RuntimeException("Boom");
    }
    public static void m4() { }
    @Test public void m5() { } // INVALID USE: nonstatic method
    public static void m6() { }
    @Test public static void m7() {    // Test should fail
        throw new RuntimeException("Crash");
    }
    public static void m8() { }
}

```

Sample类有8个静态方法，其中4个被注解位测试。这4个中有2个抛出了异常：m3和m7。另外两个则没有：m1和m5。但是其中有一个没有抛出异常的被注解方法：m5，是一个实例方法，因此不属于注解的有效使用。总之，Sample包含4项测试：一项会通过，两项会失效，另一项无效。没有用Test注解进行标注的4个方法会被测试工具忽略。

- (3). 添加对抛出特定异常的testcase的检查

```Java

// Annotation type with an array parameter -  Page 173

import java.lang.annotation.*;

/**
 * Indicates that the annotated method is a test method that
 * must throw the any of the designated exceptions to succeed.
 */

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface ExceptionTest {
    Class<? extends Exception> value();
}


// Program containing annotations with a parameter - Page 172

import java.util.*;

public class Sample2 {
    @ExceptionTest(ArithmeticException.class)
    public static void m1() {  // Test should pass
        int i = 0;
        i = i / i;
    }
    @ExceptionTest(ArithmeticException.class)
    public static void m2() {  // Should fail (wrong exception)
        int[] a = new int[0];
        int i = a[1];
    }
    @ExceptionTest(ArithmeticException.class)
    public static void m3() { }  // Should fail (no exception)


}


```

注意：有可能注解参数在编译时是有效的，但是表示特定异常类型的类文件在运行时不再存在，此时会抛出TypeNotPresentException

- (4). 添加对抛出任意一种指定异常的testcase的支持

```Java

// Code containing an annotation with an array parameter - Page 174
@ExceptionTest({ IndexOutOfBoundsException.class,
            NullPointerException.class })
public static void doublyBad() {
    List<String> list = new ArrayList<String>();

    // The spec permits this method to throw either
    // IndexOutOfBoundsException or NullPointerException
    list.addAll(5, null);
}

```

###第36：坚持使用override注解

```Java

//Can you spot the bug
public class Bigram{
  private final char first;
  private final char second;

  public Bigram(char first,char second){
    this.first = first;
    this.second = second;
  }

  public boolean equals(Bigram b){
    return b.first ==first && b.second == second;
  }

  public int hashCode(){
    return 31 * first * second;
  }

  public static void main(String[] args) {
    Set<Bigram> s = new HashSet<Bigram>();
    for (int i = 0;i<10 ;i++ ) {
      for (char ch = 'a';ch <= 'z' ;ch++ ) {
        s.add(new Bigram(ch,ch));
      }
      System.out.println(s.size());
    }
  }
}

```

- (1). 这个程序的错误在于：他没有覆盖equals而是重载了equals
- (2). **应该在你想要覆盖超类声明的每个方法声明中使用@override注解**。覆盖抽象方法或者接口时也最好加注解，这样能防止添加新方法

###第37条：用标记接口定义类型
- (1). 标记接口是没有包含方法声明的接口。例子：Serializable接口
- (2). 标记接口可以不改进任何方法的契约，只表示整个对象的限制条件，或者表明实例能够利用其它某个类的方法进行处理。
- (3). 标记接口相对于标记注解的优势
 + 标记接口定义的类型是由被标记类的实例实现的；标记接口没有定义这样的类型
如果参数没有实现Serializable接口，ObjectOutputStream.write会在运行时失败
 + 标记接口可以被更加精确的锁定
- (3). 标记注解的优势
 + 可以通过默认的方法添加一个或者多个注解类型元素，给被使用的注解类型添加更多的信息
 + 它们是更大注解机制的一部分，在支持注解的框架中具有一致性
- (4). 适用场合
 + 如果标记是应用在任何程序元素而非类或者接口，那么应该使用注解。
 + 如果标记永远只用于限制特殊元素的接口，那么应该使用标记接口。
