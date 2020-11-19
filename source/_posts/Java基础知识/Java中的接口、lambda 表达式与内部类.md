---
title: Java中的接口、lambda 表达式与内部类
tags: [Java]
categories: Java
top: true
date: 2020-05-21 10:09:05
---

# 一、接口
## 1. 接口概念
接口**不是类**，而是对类的一组需求描述，这些类要遵从接口描述的统一格式进行定义，即<font color=Crmison>必须实现</font>接口中定义的所有方法。一个类可以实现（implement)—个或多个接口，并在需要接口的地方,随时使用实现了相应接口的对象。

1. 接口中的所有方法自动地属于 public。因此，在接口中声明方法时，不必提供关键字public。<font color=Crmison>不过，在实现接口时，必须把方法声明为 public;</font>否则，编译器将认为这个方法的访问属性是包可见性，即类的默认访问属性，之后编译器就会给出试图提供更严格的访问权限的警告信息。
2. 接口中的域将被自动设为 public static final。接口绝不能含有实例域。java SE8之后，接口可以定义和实现静态方法(public static)以及默认方法(default)。
5. Java是一种<font color=Crmison>强类型语言</font>：在调用方法的时候，编译器将会检查这个方法是否存在。

```java
//Comparable 接口
public interface Comparable
{s
    ////实现该接口的类必须实现此方法
    int compareTo(Object other);  //实现该方法附加要求：在调用X.compareTo(y)的时候，这个方法必须确实比较两个对象的内容，并返回比较的结果。当x小于y时，返回一个负数；当x等于 时，返回0；
                                 //否则返回一个正数。 
}

class Employee implements Comparable
{
    public int compareTo(Object otherObject) 
    {
        Employee other = (Employee) otherObject;  //需要进行强制类型转换，不推荐
        return Double.compare(salary, other.salary);
    }
}

//在 JavaSE 5.0 中，Comparable 接口已经改进为泛型类型。
public interface Comparable<T> 
{
    int compareTo(T other); 
}

class Employee implements Comparable<Employee>    //推荐方式
{
    public int compareTo(Employee other)
    {
        return Double.compare(salary, other.salary);  //x < y 时，Double.compare(x, y) 调用会返回 -1 ; 如果 x > y 则返回 1。
    }
}

//Arrays 类中的 sort 方法承诺可以对对象数组进行排序，但要求对象所属的类必须实现了 Comparable 接口，因为要向 sort 方法提供对象的比较方式。 
Employee[] staff = new Employee[3];
staff[0] = new Employee ("Harry Hacker" , 35000);
staff[1] = new Employee ("Carl Cracker" , 75000);
staff[2] = new Employee ("Tony Tester" , 38000);
Arrays.sort(staff) ;

static void sort( Object[] a ) //使用 mergesort 算法对数组 a 中的元素进行排序。要求数组中的元素必须属于实现了Comparable 接口的类， 并且元素之间必须是可比较的。

int compareTo(T other) //用这个对象与 other 进行比较。如果这个对象小于 other 则返回负值； 如果相等则返回0；否则返回正值。
static int compare(int x , int y) //如果 x < y 返回一个负整数；如果 x 和 y 相等，则返回 0; 否则返回一个负整数。
static int compare(double x , double y) //如果 x < y 返回一个负数；如果 x 和 y 相等则返回 0; 否则返回一个负数
```

<font color=Crmison>与 equals 方法一样，compareTo方法在继承过程中有可能会出现子类父类对象混合比较的问题。</font>修改方式和equals一样，有两种不同情况，即根据子类语义是否改变来决定是用getClass方法还是instanceof方法检查类型是否一致。

```java
class Manager extends Employee
{
    public int compareTo(Employee other)
    {
        if (getClass() != other.getClass()) throw new ClassCastException();  //若子类语义发生改变
            ...
    }
}
```

## 2. 接口的特性
1. 接口不是类，尤其不能使用 new 运算符实例化一个接口,但可以声明接口的变量，接口变量**必须**引用实现了接口的类对象。
2. 与可以建立类的继承关系一样，接口也可以被扩展。这里允许存在多条从具有较高通用性的接口到较高专用性的接口的链。
3. 接口可以只定义常量而不定义方法，但这样应用接口似乎有点偏离了接口概念的初衷，最好不要这样使用它。

```java
//接口的声明和引用
Comparable x;
x = new Employee(...);

if (anObject instanceof Comparable) { . . . }  //可以使用instance 检查一个对象是否实现了某个特定的接口

//接口的继承或扩展
public interface Moveable
{
    void move(double x, double y); 
}

public interface Powered extends Moveable
{
    double milesPerGallon();
    double SPEED.LIHIT = 95; //接口不能包含实例域或静态方法，但却可以包含常量
                            //接口中的域将被自动设为 public static final。
}

class Employee implements Cloneable, Comparable  //类可以实现多个接口，却只能拥有一个超类
```

## 3. 接口与抽象类
接口与抽象类的区别在于一个类可以实现多个接口，而一个类只能扩展一个抽象类，Java 的设计者选择了不支持多继承，其主要原因是多继承会让语言本身变得非常复杂，效率也会降低。而接口可以提供多重继承的大多数好处，同时还能避免多重继承的复杂性和低效性。

## 4. 静态方法
在 Java SE 8 中，允许在接口中增加静态方法，但是认为这有违于将接口作为抽象规范的初衷，通常的做法都是将静态方法放在伴随类中，例如java标准库中出现的Collection/Collections 或 Path/Paths等成对的接口和实用工具类。
```java
//可以为 Path 接口增加静态方法，如此伴随类paths就不再必要了
public interface Path
{
    public static Path get(String first, String... more) 
    {
        return FileSystems.getDefault().getPath(first, more);
    }
}
```

## 5. 默认方法
可以为接口方法提供一个默认实现。必须用 default 修饰符标记，默认方法可以调用任何其他方法，**实现该接口的类继承该方法且无需覆盖默认方法**。有些情况下，默认方法可能很有用。
```java
//Collection 接口可以定义一个默认方法isEmpty，这样实现 Collection 的程序员就不用操心实现 isEmpty 方法了。
public interface Collection
{
    int size(); // An abstract method
    default boolean isEmpty()
    {
        return size() = 0; 
    }
}

//没有太大用处， 因为 Comparable 的每一个实际实现都要覆盖这个方法。
public interface Comparable<T>
{
    default int compareTo(T other) 
    {
         return 0;   //所有元素都相等
    }
}

public class Bag implements Collection
```
- 默认方法的一个重要用法是**“接口演化”**,假设之后为Collection接口又增加了一个 stream 非默认方法，那么 Bag 类将不能编译，即为接口增加一个非默认方法不能保证<font color=Crmison>“源代码兼容”</font>。不过， 假设不重新编译这个类，而只是使用原先的一个包含这个类的 JAR 文件。这个类仍能正常加载，程序仍然可以正常构造 Bag 实例，不会出现编译错误(<font color=Crmison>即为接口增加方法可以保证“二进制兼容”</font>)，不过如果程序在运行时在一个 Bag 实例上调用 stream方法，就会出现一个 AbstractMethodError。
- 将方法实现为一个默认方法就可以正常编译Bag类，另外如果没有重新编译而直接加载这个类， 并在一个 Bag 实例上调用 stream 方法， 将调用 Collection.stream 方法。

## 6. 解决默认方法冲突
如果先在一个接口中将一个方法定义为默认方法，然后又在超类或另一个接口中定义了同样的方法，java解决该冲突的规则为：
> 1. <font color=Crmison>超类优先</font>。如果超类提供了一个具体方法，同名而且有相同参数类型的默认方法会被忽略。即"类优先"规则。
2. <font color=Crmison>接口冲突</font>。如果一个超接口提供了一个默认方法，另一个接口提供了一个同名而且参数类型（不论是否是默认参数）相同的方法，必须覆盖这个方法来解决冲突。

若两个接口都没有为**共享方法**提供默认实现，则不会存在冲突。由于“java的类优先规则”，千万不要让一个接口中的默认方法重新定义 Object 类中的某个方法，因为这样的方法绝对无法超越 Object.toString 或 Objects.equals。

# 二、接口示例
##  1. 接口与回调
回调是一种常见的程序设计模式。在这种模式中，可以指出某个特定事件发生时应该采取的动作。
```java
ActionListener listener = new TimePrinter();
Timer t = new Timer(10000, listener);   //定时器需要知道调用哪一个方法，并要求传递的对象所属的类实现了java.awt.event 包的 ActionListener 接口。
t.start();

class TimePrinter implements ActionListener
{
    public void actionPerformed(ActionEvent event) 
    {
        System.out.println("At the tone, the time is " + new Date());
        Toolkit.getDefaultToolkit().beep();   //发出一声铃响。
    } 
}

Timer(int interval , ActionListener listener)  //构造一个定时器， 每隔 interval 毫秒通告 listener—次
```

## 2. Comparator 接口
利用Arrays.sort的数组和比较器(comparator)版本可以改变String类的比较规则，其中比较器是实现了 Comparator 接口的类的实例。
```java
public interface Comparators
{
    int compare(T first, T second); 
}

class LengthComparator implements Comparator<String> 
{
    public int compare(String first, String second) 
    {
        return first.length() - second.length(); 
    } 
}

String[] friends = { "Peter", "Paul", "Mary" };
Arrays,sort(friends, new LengthComparator());
```

## 3. 对象克隆(Cloneable 接口)
1. 这个接口从超类Object类中继承了一个安全的 clone 方法，它没有指定clone 方法。只是作为一个标记接口，指示类设计者了解克隆过程。
2. clone 方法是 Object 的一个 protected 方法，只能由其子类对象克隆其自己，即子类的方法中只能调用其所属类的clone方法，而不能调用其它子类对象的clone方法。
3. 默认的克隆操作是<font color=Crmison>浅拷贝</font>，并没有克隆对象中引用的其他对象，这样一来，原对象和克隆的对象仍然会**共享一些域信息**。
4. 如果原对象和浅克隆对象共享的子对象是不可变的(或子对象一直包含不变的常量，没有更改器方法会改变它，也没有方法会生成它的引用)，那么这种共享就是安全的。
5. 不过，通常子对象都是可变的，必须重新定义 clone 方法来建立一个<font color=Crmison>深拷贝</font>，同时克隆所有子对象。
6. 即使 clone 的默认（浅拷贝）实现能够满足要求， 还是需要实现 Cloneable 接口， 将 clone重新定义为 public，再调用 super.clone(),否则会生成一个受査异常CloneNotSupportedException。
7. 子类只能调用受保护的 clone 方法来克隆它自己的对象。 必须重新定义 clone 为 public 才能允许所有方法克隆对象。

```java
class Employee implements Cloneable
{
    //如果在一个对象上调用 clone, 但这个对象的类并没有实现 Cloneable 接口，Object 类 的 clone 方法就会拋出异常
    public Employee clone() throws CloneNotSupportedException  
    {
        // call Object.clone()
        Employee cloned = (Employee) super.clone() ; 
        
        // clone mutable fields
        cloned.hireDay = (Date) hireDay.clone();
        return cloned;
    }
}

//所有数组类型都有一个 public 的 clone 方法， 而不是 protected: 可以用这个方法建立一个新数组， 包含原数组所有元素的副本。
int[] luckyNumbers = { 2, 3, 5, 7, 11, 13 };
int[] cloned = luckyNumbers.done();
cloned[5] = 12; // 不会改变luckyNumbers[5]
```

# 三、lambda 表达式
 ## 1. 为什么引入 lambda 表达式
 lambda 表达式是一个可传递的代码块，该代码块可传递到某个对象，可以在以后执行一次或多次，实现回调，类似以上ActionListener 的 actionPerformed方法。Java 是一种面向对象语言，不能直接传递代码段，必须构造一个对象，这个对象的类需要有一个方法能包含所需的代码，利用lambda表达式可以让这种形式的代码块表达的更加简洁。

 ## 2. lambda 表达式的语法
带参数变量的表达式就被称为 lambda 表达式，lambda 表达式就是一个代码块， 以及必须传入代码的变量规范。
```java
//参数， 箭头（->) 以及一个表达式。无需指定 lambda 表达式的返回类型。lambda 表达式的返回类型总是会由上下文推导得出。
(String first, String second) -> first.length() - second.length()  //可以在需要 int 类型结果的上下文中使用。

//代码要完成的计算无法放在一个表达式中，就可以像写方法一样，把这些代码放在 {}中，并包含显式的 return语句。
(String first, String second) -> 
{
    if (first.length() < second.length()) return -1;
    else if (first.length() > second.length()) return 1;
    else return 0; 
}

//lambda 表达式没有参数，仍然要提供空括号，就像无参数方法一样
() -> { for (int i = 100;i >= 0;i-- ) System.out.println(i);}

//如果可以推导出一个 lambda 表达式的参数类型，则可以忽略其类型。
Comparator<String> comp = (first, second) -> first.length() - second.length(); //编译器可以推导出 first 和 second 必然是字符串，因为这个 lambda 表达式将赋给一个字符串比较器。

//如果方法只有一个参数， 而且这个参数的类型可以推导得出，那么甚至还可以省略小括号
ActionListener listener = event -> System.out.println("The time is " + new Date()");
```

```java
//labmda表达式的应用
Arrays.sort(planets, (first, second) -> first.length() - second .length());
Timer t = new Timer(1000, event -> System.out.println ("The time is " + new Date()));

ActionListener listener = event -> 
{
    System.out.println(text);
    Toolkit.getDefaultToolkitO.beep();
};
new Timer(delay, listener).start();
```
<font color=Crmison>如果一个 lambda 表达式只在某些分支返回一个值， 而在另外一些分支不返回值，这是不合法的。</font>

## 3. 函数式接口
1. 对于**只有一个抽象方法的接口**，需要这种接口的**对象**时，就可以提供一个 lambda 表达式，这种接口称为函数式接口。
2. 最好把 lambda 表达式看作是一个函数，而不是一个对象， 另外要接受 lambda 表达式可以传递到函数式接口。
3. 在 Java 中，对 lambda 表达式所能做的也只是能转换为函数式接口。java保持了接口概念而没有增加函数类型。

```java
//此包中定义了很多非常通用的函数式接口。
import java.util.function;

//在底层， Arrays.sort 方法会接收实现了 Comparator<String> 的某个类的对象。在这个对象上调用 compare 方法会执行这个 lambda 表达式的体。
Arrays.sort (words, (first, second) -> first.length() - second.length()) ;

//接口BiFunction<T, U, R> 描述了参数类型为 T 和 U 而且返回类型为 R 的函数。可以将lambda表达式保存在该类型的变量中。
BiFunction<String, String, Integer> comp = (first, second) -> first.length() - second.length();

//接口 Predicate
public interface Predicate<T> 
{
    boolean test(T t); 
    // Additional default and static methods   //可以有其它非抽象方法，但抽象方法只能有一个。
}
//ArrayList 类有一个 removeIf 方法， 它的参数就是一个 Predicate。这个接口专门用来传递lambda 表达式。
list.removeIf(e -> e == null);  //从一个数组列表删除所有 null 值。
```

## 4. 方法引用
1. 有时，可能已经有现成的方法可以完成你想要传递到其他代码的某个动作，故可直接将该方法的表达式传递到此处，该表达式称为方法引用，要用`::` 操作符分隔方法名与对象或类名。
2. 类似于 lambda 表达式，方法引用不能独立存在，总是会转换为函数式接口的实例。
3. 如果有多个同名的重载方法， 编译器就会尝试从上下文中找出你指的那一个方法。
4. 可以在方法引用中使用 this 参数，使用super也是合法的。

```java
//方法引用的格式有三种
//方法引用等价于提供方法参数的 lambda 表达式。
object::instanceMethod 
Class::staticMethod
Class::instanceMethod

//方法引用的应用
Timer t = new Timer(1000, System.out::println);  //System.out::println 等价于 lambda 表达式  x -> System.out.println(x)
Math::pow;  //等价于（x，y) -> Math.pow(x, y)。
Arrays.sort(strings，String::conpareToIgnoreCase)   //String::conpareToIgnoreCase等同于 (x, y) -> x.compareToIgnoreCase(y)

this::equals //等同于 x -> this.equals(x)。
super::instanceMethod
```

## 5. 构造器引用
1. 构造器引用与方法引用很类似，只不过方法名为 new,编译器会根据上下文调用某个确定的构造器。
2. 可以用数组类型建立构造器引用。
3. Java 有一个限制，无法构造泛型类型 T 的数组。

```java
ArrayList<String> names = ...;
Stream<Person> stream = names.stream().map(Person::new);
List<Person> people = stream.collect(Collectors.toList());

int[]::new //等价于 lambda 表达式 x -> new int[x]

Person[] people = stream.toArray(Person[]::new);
```

## 6. 变量作用域
1. lambda 表达式由3部分组成：一个代码块；参数；自由变量的值，指非参数而且不在代码中定义的变量。
2. 自由变量被lambda表达式捕获：表示 lambda 表达式的数据结构必须存储自由变量的值。可以把一个 lambda 表达式转换为包含一个方法的对象，这样自由变量的值就会复制到这个对象的实例变量中。
3. 在 Java 中，lambda 表达式就是**闭包**。
4. 要确保所捕获的变量是**最终变量**，在 lambda 表达式中，只能引用值不会改变的变量(不论是在lambda中改变还是在外部改变)，否则并发执行多个动作时就会不安全。
5. lambda 表达式的体与嵌套块有相同的作用域。在 lambda 表达式中声明与一个局部变量同名的参数或局部变量是不合法的。
6. 在一个 lambda 表达式中使用 this 关键字时， 是指创建这个 lambda 表达式的方法的 this参数。

```java
public static void repeatMessage(String text, int delay) 
{
    ActionListener listener = event ->
    {
        System.out.println(text);    //在这里，text 总是指示同一个 String 对象，所以捕获这个变量是合法的。
        Toolkit.getDefaultToolkit().beep();
    };
    new Timer(delay, listener).start();
}

Path first = Paths.get("usr/bin");
Couparator<String> comp = (first, second) -> first.length() - second.length();  //Error

public class Application
{
    public void init() 
    {
        ActionListener listener * event ->
        {
            System.out.print n(this.toString()); //this.toString() 会调用 Application 对象的 toString方法
        }
    }
}
```

## 7. 处理lambda表达式
使用 lambda 表达式的重点是<font color=Crmison>延迟执行</font>，需要延迟执行的原因如下：
>在一个单独的线程中运行代码；
多次运行代码；
在算法的适当位置运行代码（例如， 排序中的比较操作；）
发生某种情况时执行代码（如， 点击了一个按钮， 数据到达， 等等；）
只在必要时才运行代码。

```java
public static void repeat(int n, Runnable action) 
{
    for (int i = 0; i < n; i++) action.run();   //调用 action.run() 时会执行这个 lambda 表达式的主体
}

public interface IntConsumer
{
    void accept(int value); 
}
public static void repeat(int n, IntConsumer action) 
{
    for (int i = 0; i < n; i++) action.accept(i); //告诉这个动作它出现在哪一次迭代中
}

//大多数标准函数式接口都提供了非抽象方法来生成或合并函数。
Predicate.isEqual(a);  //等同于 a::equals
Predicate.isEqual(a).or(Predicate.isEqual(b)); //等同于 x -> a.equals(x) || b.equals(x)
```
如果使用注解@FunctionalInterface标记一个自己设计的接口，该接口只有一个抽象方法，则若无意中增加了另一个非抽象方法， 编译器会产生一个错误消息。另外 javadoc 页里会指出你的接口是一个函数式接口。
![常用函数式接口](接口1.png)
***
![常用函数式接口](接口2.png)

## 8. 再谈 Comparator
Comparator 接口包含很多方便的静态方法来创建比较器。 这些方法可以用于 lambda 表达式或方法引用。静态 comparing 方法取一个“键提取器” 函数，它将类型 T 映射为一个可比较的类型。(如 String)。对要比较的对象应用这个函数， 然后对返回的键完成比较。

```java
Arrays.sort(people,Comparator.comparing(Person::getName)); //按名字对这些对象排序
Arrays.sort(people,Comparator.comparing(Person::getlastName).thenConiparing(Person::getFirstName)); //如果两个人的姓相同， 就会使用第二个比较器。
Arrays.sort(people, Comparator.comparing(Person::getName, (s, t) -> Integer.compare(s.length(), t.length())));  //根据人名长度完成排序

//comparing 和 thenComparing 方法都有变体形式，可以避免 int、 long 或 double 值的装箱。
Arrays.sort(people, Comparator.comparingInt(p -> p.getName().length()));  

Arrays.sort(people, comparing(Person::getMiddleName, nullsFirst(naturalOrder())));  //使用nullsFirst 和 nullsLast 适配器可使在遇到 null 值时不会抛出异常
naturalOrder().reversed(); //让比较器逆序比较
```

# 四、内部类
使用内部类的主要原因：
1. 内部类方法可以访问该类定义所在的作用域中的数据，包括私有的数据。
2. 内部类可以对同一个包中的其他类隐藏起来。
3. 当想要定义一个回调函数且不想编写大量代码时，使用匿名内部类比较便捷。

## 1. 使用内部类访问对象状态
1. 内部类位于外部类的内部，但<font color=Crmison>不是每个外部类对象都有一个内部类对象的实例域，内部类对象是由外部类的方法构造的。</font>
2. 内部类既可以访问自身的数据域，也可以直接访问创建它的外围类对象的所有成员(包括private成员和静态成员)。即使用内部类可以不必提供仅用于访问其他类的访问器。
3. 外部类只能通过创建成员内部类的对象，再通过指向这个对象的引用来访问。
4. 只有内部类可以是私有类，而常规类只可以具有包可见性，或公有可见性。若内部类声明为私有的，则只有外部类的方法才能够构造内部类对象。

***
内部类能够访问外部类成员的原因是内部类的对象总有一个隐式引用，它指向了创建它的外部类对象，这个引用在内部类的定义中是不可见的。外围类的引用在构造器中设置。编译器修改了所有的内部类的构造器，添加一个外围类引用的参数。因为 TimePrinter 类没有定义构造器，所以编译器为这个类生成了一个默认的构造器。
```java
class TalkingClock
{
    private int interval;
    private boolean beep;

    public TalkingClock(int interval, boolean beep) 
    {
        this.interval = interval;
        this.beep = beep;
    }

    public void start() 
    {
        ActionListener listener = new TimePrinter();   //当在 start 方法中创建了 TimePrinter 对象后，编译器就会将 this 引用传递给当前的语音时钟的构造器:
                                                            //ActionListener listener = new TimePrinter(this);
        Timer t = new Timer(interval, listener); t.start();
    }

    public class TimePrinter implements ActionListener
    {
        public void actionPerformed(ActionEvent event)   //因为 TimePrinter 类没有定义构造器，所以编译器为这个类生成了一个默认的构造器：
        {                                                //public TimePrinter(TalkingGock clock){outer = clock;}
            System.out.println("At the tone, the time is " + new Date());
            if(beep) Toolkit.getDefaultToolkit().beep();   //if(beep)等价于outer.beep  outer 不是 Java 的关键字,只是用它说明内部类中的机制
        }
    }
}
```

## 2. 内部类的特殊语法规则
```java
//使用外围类引用的正规语法
OuterClass.this

public void actionPerformed(ActionEvent event) 
{
    if(TalkingClock.this.beep) Toolkit.getDefaultToolkit().beep(); 
}

//明确地编写内部对象的构造器语法规则
outerObject.new InnerClass(construction parameters)

ActionListener listener = this.new TimePrinter();  //this 限定词是多余的,不过，可以通过显式地命名将外围类引用设置为其他的对象。

TalkingClock jabberer = new TalkingClock(1000, true);
TalkingClock.TimePrinter listener = jabberer.new TimePrinter();

//在外围类的作用域之外，可以这样引用内部类：
OuterClass.InnerClass
```

1. 内部类中声明的所有静态域都必须是 final,因为对于每个外部对象，会分别有一个单独的内部类实例。如果这个域不是 final,它可能就不是唯一的。
2. <font color=Crmison>内部类不能有 static 方法。</font>

## 3. 内部类是否有用、必要和安全
当使用了内部类的时候，编译器做了这样一件事：它在外围类添加了一个静态方法 `static boolean access$0(外部类);`内部类方法将调用这个函数。这个是有风险的，因为任何人都可以通过access$0方法很容易的读取到外围类的私有域。黑客可以使用十六进制编辑器轻松创建一个用虚拟机指令调用这个函数的类文件。即如果内部类访问了私有数据域，就有可能通过附加在外围类所在的包中的其他类访问它们，应慎用。

## 4. 局部内部类
1. 局部内部类是定义在一个方法或者一个作用域里面的类，它和成员内部类的区别在于局部内部类的访问仅限于方法内或者该作用域内。局部类有一个优势， 即对外部世界可以完全地隐藏起来。
2. 局部类不能用 public 或 private 访问说明符进行声明。它的作用域被限定在声明这个局部类的块中。

```java
public void start() 
{
    class TimePrinter implements ActionListener
    {
        public void actionPerformed(ActionEvent event) 
        {
            System.out.println("At the tone, the time is " + new Date());
            if(beep) Toolkit.getDefaultToolkit.beep(); 
        } 
    }
    ActionListener listener = new TimePrinter();
    Timer t = new Timer(interval, listener); 
    t.start(); 
}
```

## 5. 由外部方法访问变量
//局部类不仅能够访问包含它们的外部类，还可以访问局部变量。不过，那些局部变量必须<font color=Crmison>事实上为</font> final，一旦赋值就绝不会改变。
```java
public void start(int interval, boolean beep)
{
    class TimePrinter implements ActionListener
    {
        public void actionPerformed(ActionEvent event) 
        {
            System.out.println("At the tone, the tiie is " + new Date());
            if(beep) Toolkit.getDefaultToolkit.beep(); 
        } 
    }
    ActionListener listener = new TimePrinter();
    Timer t = new Timer(interval, listener); 
    t.start();  //将调用actionPerformed方法，此时局部变量beep已经被销毁，因此编译器会在局部类内部自动生成一个beep的拷贝final boolean val$beep;当创建一个对象的时候，beep 就会
                //被传递给构造器，并存储在 val$beep 域中。
}
```

## 6. 匿名内部类
1. 如果只创建这个类的一个对象，就不必命名了，称为匿名内部类。
2. 匿名内部类没有类名故不能有构造器，而是将构造器参数传递给超类构造器。<font color=Crmison>且在内部类实现接口的时候，不能有任何构造参数。</font>
3. 使用匿名内部类的解决方案比较简短、更切实际、更易于理解。
4. 使用匿名内部类可以实现事件监听器和其他回调，但不如lambda表达式简洁。

```java
public void start(int interval, boolean beep) 
{
    ActionListener listener = new ActionListener() //创建一个实现 ActionListener 接口的类的新对象，需要实现的方法 actionPerformed 定义在括号内。
    {
        public void actionPerformed(ActionEvent event) 
        {
            System.out.println("At the tone, the time is " + new Date())；
            if (beep) Toolkit.getDefaultToolkit().beep(); 
        } 
    };
    Timer t = new Timer(interval, listener); 
    t.start(); 
}

//通用语法
new SuperType(construction parameters) 
{
    inner class methods and data   //SuperType 可以是接口也可以是类。若是接口则必须实现它，若是类，则内部类就要扩展它。
}

Person queen = new Person("Mary"); // 一个Person对象
Person count = new Person("Dracula") { . . . };// 一个扩展了Person类的内部类对象
```
***
**双括号初始化技巧**
```java
//如果不再需要这个数组列表，最好让它作为一个匿名列表。
invite(new ArrayList<String>() {{ add("Harry"); add("Tony"); }});  //外层括号建立了 ArrayList 的一个匿名子类。内层括号则是一个对象构造块

//生成日志或调试消息时， 通常希望包含当前类的类名
//静态方法没有 this，在静态方法中获取所在类的类名方式如下：
new Object(){}.getClass().getEnclosingClass();  //new Object(){} 会建立 Object 的一个匿名子类的一个匿名对象，getEnclosingClass则得到其外围类，也就是包含这个静态方法的类。
```

## 7. 静态内部类
1. 静态内部类使用static关键字声明，就不会对外部类对象产生引用，使用静态内部类通常只是为了把一个类隐藏在另外一个类的内部。
2. 只有内部类可以声明为 static。
3. 静态内部类对象是在静态方法中构造的。
4. 与常规内部类不同，静态内部类可以有静态域和方法。
5. 声明在接口中的内部类自动成为 static 和 public 类。

```java
class ArrayAlg
{
    public static class Pair
    {
        private double first;
        private double second;
        
        public Pair(double f, double s)
        {
            first = f;
            second = s;
        }

        public static Pair minmax(doublet[] values)  //ArrayAlg.Pair p = ArrayAlg.minmax(d);
        {
            double min = Double.POSITIVE_NFINITY;
            double max = Double.NEGATIVE_INFINITY;
            for (double v : values)
            {
                if (min > v) min = v;
                if (max < v) max = v;
            }
            return new Pair(min, max);
        }
    }
}
```

# 五、代理
## 1. 何时使用代理
1. 代理类可以实现指定的接口，可以在运行时创建全新的类。这样就可以在编译时获得一个表示接口对象的确切类型。
2. 不能在运行时定义<font color=Crmison>指定接口和Object类中的全部方法</font>，而是要提供一个调用处理器。调用处理器是实现了 InvocationHandler 接口的类对象。
3. 无论何时调用代理对象的方法，调用处理器的 invoke 方法都会被调用，并向其传递Method 对象和原始的调用参数。 调用处理器必须给出处理调用的方式。

```java
Object invoke(Object proxy, Method method, Object[] args)  //实现调用处理器接口中的方法
```

## 2. 创建代理对象
创建代理对象需要使用Proxy 类的 newProxylnstance 方法，它包含三个参数
- 一个类加载器，用 null 可表示使用默认的类加载器。
- 一个 Class 对象数组， 每个元素都是需要实现的接口。
- 一个调用处理器。

使用代理可机制可解决的问题，例：
- 路由对远程服务器的方法调用。
- 在程序运行期间，将用户接口事件与动作关联起来。
- 为调试， 跟踪方法调用。

```java
class TraceHandler implements InvocationHandler
{
    private Object target;
    public TraceHandler(Object t) {
        target = t;
        public Object invoke (Object proxy, Method m, Object[] args) throws Throwable
        {    // 打印方法名和参数
            System.out.print(target);  //打印隐式参数
            System,out.print("." + m.getName() + "(");  //打印方法名
            if (args != null) {   //打印显式参数
                for (int i = 0; i < args.length; i++) 
                {
                    System,out.print(args[i]);
                    if (i < args.length - 1) System.out.print(", ");
                }
            }
            System,out.println(")");

            // 调用真正的方法
            return m.invoke(target, args);
        }
}

//构造用于跟踪方法调用的代理对象
Object value = ...
InvocationHandler handler = new TraceHandler(value) ; // 为一个或多个接口构造代理
Class[] interfaces = new Class[] {Comparable.class};
Object proxy = Proxy.newProxylnstance(null , interfaces, handler); //无论何时用 proxy 调用某个方法， 这个方法的名字和参数就会打印出来，之后再用 value 调用它。

//应用：使用代理对象对二分查找过程进行跟踪。
Object[] elements = new Object[1000]; 
for (int i = 0; i < elements.length; i ++) 
{
    Integer value = i + 1;  //Integer 类实现了 Comparable 接口。代理对象属于在运行时定义的类（它有一个名字， 如 $ProxyO) 这个类也实现了Comparable 接口,且会调用代理对象处理器的invoke方法。
    InvocationHandler handler = new TraceHandler(value);
    Object proxy = Proxy.newProxyInstance(null, new Class[]{ Comparable.class },handler);
    elements[i] = proxy //数组代理
}
Integer key = new Random().nextlnt(elements.length) + 1;   
int result = Arrays.binarySearch(elements, key) ; //查找元素,由于数组中填充了代理对象， 所以 compareTo 调用了 TraceHander 类中的 invoke 方法。这个方法打印出了方法名和参数，之后用包装好的 
                                                //Integer 对象调用 compareTo。
if (result >= 0) System.out.println(elements[result]); //打印元素

//打印结果
500.compareTo(288) 2SO.compareTo(288)
375.compareTo(288)
312. compareTo(288)
281.compareTo(288)
296.compareTo(288)
288.compareTo(288)
288.toString()   //即使不属于 Comparable 接口，toString 方法也被代理，实际上有相当一部分的Object 方法都被代理。
```
Integer 类实际上实现了`Comparable<Integer>`。然而，在运行时，所有的泛型类都被取消，代理将它们构造为原 `Comparable` 类的类对象。

## 3. 代理类的特性
1. 代理类是在程序运行过程中创建的，一旦被创建，就变成了常规类。
2. 所有的代理类都扩展于 Proxy 类。一个代理类只有一个实例域———调用处理器，它定义在 Proxy 的超类中。
3. 为了履行代理对象的职责，所需要的任何附加数据都必须存储在调用处理器中。
4. 所有的代理类都覆盖了 Object 类中的方法 toString、 equals 和 hashCode。如同所有的代理方法一样，这些方法仅仅调用了调用处理器的 invoke，Object 类中的其他方法（如 clone和 getClass) 没有被重新定义。
5. 没有定义代理类的名字，Sun 虚拟机中的 Proxy类将生成一个以字符串 $Proxy 开头的类名。
6. 对于特定的类加载器和预设的一组接口来说，只能有一个代理类。
7. 代理类一定是 public 和 final。

```java
import java.lang.reflect.InvocationHandler
//如果使用同一个类加载器和接口数组调用两次 newProxylustance方法的话， 那么只能够得到同一个类的两个对象
Class proxyClass = Proxy.getProxyClass(null, interfaces);  //可以利用 getProxyClass方法获得这个类

Object invoke(Object proxy,Method method,Object[] args)  //定义了代理对象调用方法时希望执行的动作。
static Class<?> getProxyClass(ClassLoader loader, Class<?>...interfaces)  //返回实现指定接口的代理类。
static Object newProxyInstance(ClassLoader loader, Class<?>[] interfaces, InvocationHandler handler)  //构造实现指定接口的代理类的一个新实例。所有方法会调用给定处理器对象的 invoke 方法。
static boolean isProxyClass(Class<?> cl)  //如果 cl 是一个代理类则返回 true。
```

