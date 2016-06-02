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
