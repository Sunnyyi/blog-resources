---
title: Java中的泛型程序设计
tags: [Java]
categories: Java
top: true
date: 2020-05-25 10:09:05
---

# 一、定义简单泛型类
1. 一个泛型类就是具有一个或多个**类型变量**的类。
2. 类定义中的类型变量指定方法的返回类型以及域和局部变量的类型。
3. 类型变量使用大写形式，变量 E 表示集合的元素类型，K 和 V 分别表示表的关键字与值的类型，T 可表示任意类型。
4. 用具体的类型替换类型变量就可以实例化泛型类型，换句话说，泛型类可看作普通类的工厂。

```java
//定义简单泛型类
public class Pair<T> 
{
    private T first;
    private T second;
    public Pair() { first = null ; second = null; }
    public Pair(T first, T second) { this.first = first; this.second = second; }
    public T getFirst(){ return first; }
    public T getSecond(){ return second; }
    public void setFirst(T newValue) { first = newValue; }
    public void setSecond(T newValue) { second = newValue; } 
}

//引入多个类型变量
public class Pair<T, U> { . . . }
```

# 二、泛型方法
1. 可以定义一个带有类型参数的简单方法，称为泛型方法。
2. 泛型方法可以定义在普通类中，也可以定义在泛型类中。
3. 当调用一个泛型方法时,在方法名前的尖括号中放入具体的类型,有些情况下具体类型可省略。


```java
class ArrayAlg
{
    public static <T> T getMiddle(T... a) //参数类型放在方法返回值前面
    {
        return a[a.length / 2]; 
    } 
}

//调用泛型方法
String middle = ArrayAlg.<String>getMiddle("John", "Q", "Public");
//编译器能够用 names 的类型（即 String[ ]) 与泛型类型 T[ ]进行匹配并推断出 T 一定是 String。故在这种情况下可省略类型参数。
String middle = ArrayAlg.getMiddle("John", "Q", "Public");

//编译器将会自动打包参数为 1 个Double 和 2 个 Integer 对象，而后寻找这些类的共同超类型为Number 和 Comparable 接口，此时会得到一个错误报告提示可以将结果赋给Number 或 Comparable 接口。
//可以使用这种方式有目的地引入一个错误，并研究编译器对一个泛型方法调用最终推断出哪种类型
double middle = ArrayAlg.getMiddle(3.14, 1729, 0);
```

# 三、类型变量的限定
1. 利用关键字extends来限定类型变量，一个类型变量或通配符可以有多个限定，用`&`分隔，而逗号用来分隔类型变量。
2. <font color=Crmison>可以根据需要拥有多个接口超类型， 但限定中至多有一个类。如果用一个类作为限定，它必须是限定列表中的第一个。</font>

```java
class ArrayAlg
{
    //对T进行限定：表明泛型的min方法只能被实现了 Comparable 接口的类（如 String、 LocalDate 等）的数组调用。否则会产生一个编译错误。
    public static <T extends Comparab1e> T min(T[] a) // almost correct
    {
        if (a null || a.length = 0) return null; 
        T smallest = a[0];
        for (int i = 1; i < a.length; i++)
            if (smallest.compareTo(a[i]) > 0) smallest = a[i];
        return smallest; 
    } 
}

//一个类型变量或通配符可以有多个限定，&隔开
T extends Comparable & Serializable

public static <T extends Comparable> Pair<T> minmax(T[] a)
{
    if (a = null || a.length == 0) return null;
    T min = a[0];
    T max = a[0];
    for (int i = 1; i < a.length; i++)
    {
        if (inin.compareTo(a[i]) > 0) min = a[i]; <2 if (max .coinpareTo(a[i]) < 0) max = a[i];
    }
    return new Pair<>(min, max); 
}
```

# 四、泛型代码和虚拟机
## 1. 类型擦除
虚拟机没有泛型类型对象—所有对象都属于普通类。Java中的泛型是用擦除实现的，即仅于编译时类型检查，在运行时擦除类型信息，这样可以避免运行时代码膨胀。

1. 无论何时定义一个泛型类型， 都自动提供了一个相应的原始类型。
2. 原始类型就是擦除类型变量, 并替换为限定类型（无限定的变量用 Object 替换）后的普通类型。
3. 原始类型用第一个限定的类型变量来替换， 如果没有给定限定就用 Object 替换。
4. 为了提高效率，应该将标签接口（即没有方法的接口）放在边界列表的末尾。

```java
//泛型类型
public class Interval <T extends Comparable & Serializable> implements Serializable  //可以实现接口
{
    private T lower;
    private T upper;
    public Interval (T first, T second) 
    {
        if (first.compareTo(second) <= 0) { lower = first; upper = second; }
        else { lower = second; upper = first; } 
    } 
}

//对应的原始类型
public class Interval implements Serializable
{
    private Comparable lower;
    private Comparable upper;
    public Interval (Comparable first, Comparable second) { . . . } 
}
```

## 2. 翻译泛型表达式
1. 当程序调用泛型方法时，如果擦除返回类型，编译器插入强制类型转换。
2. 当存取一个泛型域时也要插入强制类型转换。

```java
//编译器把这个方法调用翻译为两条虚拟机指令：对原始方法 Pair.getFirst 的调用;  将返回的 Object 类型强制转换为 Employee 类型。
Pair<Employee> buddies = . .
Employee buddy = buddies.getFirst();  

//假设Pair 类的 first 域和 second 域都是公有的
Employee buddy = buddies.first; //会在结果字节码中插人强制类型转换。
```

## 3. 翻译泛型方法
1. 类型擦除之前认为泛型方法为一个方法族，而擦除类型之后，只剩下一个方法。
2. 类型擦除与多态发生了冲突，要想多态正确的发挥作用，需要编译器在 Datelnterval 类中生成一个桥方法。

```java
class Datelnterval extends Pair<LocalDate>
{
    public void setSecond(LocalDate second)
    {
        if (second.compareTo(getFirst()) >= 0)
            super.setSecond(second);
    }

    //生成桥方法来保持多态
    public void setSecond(Object second) { setSecond((Date) second);
}

Datelnterval interval = new Datelnterval(. . .);
Pair<Loca1Date> pair = interval; 
pair.setSecond(aDate); 

//对于可协变返回类型的方法不能生成桥方法，因为在虚拟机中，用参数类型和返回类型确定一个方法。虚拟机能够正确处理这一情况。
LocalDate getSecond() 
Object getSecond()
```

有关 Java 泛型转换的事实:
1. 虚拟机中没有泛型，只有普通的类和方法。
2. 所有的类型参数都用它们的限定类型替换。
3. 桥方法被合成来保持多态。 
4. 为保持类型安全性，必要时插入强制类型转换。

## 4. 调用遗留代码
1. Java允许泛型代码和遗留代码之间能够互操作。
2. 当利用原始类型操作泛型对象时以及由遗留的类得到一个原始类型的对象时都会产生一个警告，若在设计代码时可以确保不会发生错误，则可以使用注解`@SuppressWarnings("unchecked")`消除这些警告。

# 五、约束与局限性
在使用泛型时需要考虑一些限制，大多数限制都是由类型擦除引起的。
## 1. 不能用基本类型实例化类型参数
1. 不能用基本类型实例化类型参数的原因是类型擦除，擦除之后原始类型中含有Object类型的域，而 Object 不能存储基本类型的值。
2. 这并不是一个致命的缺陷，它与Java 语言中基本类型的独立状态相一致。

## 2. 运行时类型查询只适用于原始类型
因为虚拟机中没有泛型类型，而是总有一个特定的非泛型类型，故运行时类型查询只适用于原始类型。
```java
f(a instanceof Pair<String>) // 会得到一个编译器错误
Pair<String> p = (Pair<String>) a; //会得到一个警告

//getClass 方法总是返回原始类型。
Pair<String> stringPair = . .
Pair<Employee> employeePair = . .
if (stringPair.getClass() == employeePair.getClass()) //比较的结果是 true, 这是因为两次调用 getClass 都将返回 Pair.class。
```

## 3. 不能创建参数化类型的数组
1. <font color=Crmison>因为类型擦除会让数组能够记住它的元素类型的机制失效，往参数化类型数组中添加各种类型后就能够通过数组检查，这违背了泛型设计的原则，故不能创建参数化类型的数组。</font>
2. 只是不允许创建这些数组，而声明类型为 `Pair<String>[]` 的变量仍是合法的,只是不能通过`new Pair<String>[10]` 初始化这个变量。

```java
//table 的类型是 Pair[] 可以把它转换为 Object[] 
Pair<String>[] table = new Pair<String>[10]; // 不能创建参数化类型的数组
Object[] objarray = table;

//类型擦除使数组能够记住它的元素类型的机制失效了，即以下赋值能够通过存储检查，
objarray[0] = "Hello";  //运行时会导致一个类型错误，违背了泛型设计的原则。
objarray[0] = new Pair<Employee>();

//可以声明通配类型的数组， 然后进行类型转换,但这样是不安全的
//如果在 table[0] 中存储一个 Pair<Employee>, 然后对 table[0].getFirst() 调用一个 String 方法， 会得到一个 ClassCastException 异常。
Pair<String>[] table = (Pair<String>[]) new Pair<?>[10];

//收集参数化类型对象的唯一安全有效的方法
ArrayList:ArrayList<Pair<String>>
```

## 4. Varargs 警告
1. 向参数个数可变的方法传递一个泛型类型的实例时，Java 虚拟机必须建立一个 `Pair<String>` 数组，这就违反了虚拟机中没有泛型的规则，但是对于这种情况只会得到一个警告而不是错误。
2. 可增加注解`@SuppressWamings("unchecked"。)`或`@SafeVarargs`来抑制这个警告，之后就可以提供泛型类型来调用这个方法了。
3. 对于只需要读取参数数组元素的所有方法，都可以使用这两个注解。

```java
@SafeVarargs
public static <T> void addAll(Collections coll, T... ts) 
{
    for (t : ts) coll.add(t);
}
Col1ection<Pair<String>> table = . . .;
Pair<String> pairl = . . .;
Pair<String> pair2 = . .
addAll(table, pairl, pair2);

@SafeVarargs static <E> E[] array(E... array) { return array;}
Pair<String>[] table = array(pairl, pair2);
Object[] objarray = table;
objarray[0] = new Pair<Employee>();  //能顺利运行而不会出现 ArrayStoreException 异常（因为数组存储只会检查擦除的类型) 但在处理 table[0] 时会在别处得到一个异常。
```

## 5. 不能实例化类型变量
1. 不能使用像 `new T(...)` `new T[...]` 或 `T.class` 这样的表达式中的类型变量。
2. 解决办法是让调用者提供一个构造器表达式或者通过反射调用 `Class.newInstance` 方法来构造泛型对象。

```java
// Error
public Pair() { first = new T(); second = new T(); }
//解决方法
Pair<String> p = Pair.makePair(String::new);
public static <T> Pair<T> makePair(Supplier<T> constr) 
{
    return new Pair<>(constr.get(), constr.get()); 
}

// Error
first = T.class.newInstance(); 
//解决方法
public static <T> Pair<T> makePair(Class<T> cl) {
    try { return new Pair(cl.newInstance(), cl.newInstance()); }
    catch (Exception ex) { return null; } 
}
Pair<String> p = Pair.makePair(String.class);
```

## 6. 不能构造泛型数组
因为数组中的类型在虚拟机中会被擦除。
```java 
// Error
public static <T extends Comparable〉 T[] minmax(T[] a) { T[] mm = new T[2]; . . . }  //类型擦除会让这个方法永远构造 Comparable[2] 数组。
//Error
public static <T extends Comparable〉T[] minmax(T... a) {
Object[] mm = new Object[2];
return (T[]) mm; // compiles with warning
}
String[] ss = ArrayAlg.minmax("Tom", "Dick", "Harry");

//解决方法
//用户提供一个数组构造器表达式
public static <T extends Comparable〉T[] minmax(IntFunction<TD> constr, T... a) 
{ 
    T[] mm = constr.apply(2); 
}
String口 ss = ArrayAlg.minmax (String[]::new，"Tom", "Dick", "Harry");
//利用反射构造
public static <T extends Comparable>T[] minmax(T... a) 
{ 
    T[] mm = (T[]) Array.newlnstance(a.getClass().getComponentType(), 2);
}

//toArray方法
Object[] toArray()
T[] toArray(T[] result) //如果数组足够大， 就使用这个数组。 否则， 用 result 的成分类型构造一个足够大的新数组。
```

## 7. 泛型类的静态上下文中类型变量无效
不能在静态域或方法中引用类型变量。因为由于类型擦除静态域的特定功能也会消失。
```java
public class Singleton<T> 
{
    private static T singleInstance; // Error
    public static T getSingleInstance() // Error
    {
        if (singleinstance == null) 
            construct new instance of T
        return singlelnstance; 
    } 
}
```

## 8. 不能抛出或捕获泛型类的实例
1. 既不能抛出也不能捕获泛型类对象。
2. 泛型类扩展 Throwable 是不合法的。
3. catch 子句中不能使用类型变量。

```java
// Error 不能扩展Throwable
public class Problem<T> extends Exception { /* . . . */ } 

//Error 不能编译
public static <T extends Throwable〉void doWork(Class<T> t) 
{
    try
    {
        do work
    }
    catch (T e) // Error 不能捕获类型变量
    {
        Logger,global.info(...) 
    } 
}

//在异常规范中使用类型变量是允许的
public static <T extends Throwable> void doWork(T t) throws T // OK
{
    try
    {
        do work
    }
    catch (Throwable realCause) 
    { 
        t.initCause(realCause);
        throw t; 
    }
}
```

## 9. 可以消除对受查异常的检查
1. 泛型可以消除java异常处理中必须为所有受查异常提供一个处理器的限制，可利用注解`@SupressWarnings("unchecked")`来实现。
2. 通过使用泛型类、擦除和 `@SuppressWarnings` 注解， 就能消除 Java 类型系统的部分基本限制。

```java
public abstract class Block
{
    public abstract void body() throws Exception;
    public Thread toThread()
    {
        return new Thread() 
        {
            public void run()   //run方法不会介意受查异常Exception
            {
                try
                {
                    body();
                }
                catch (Throwable t) 
                {
                    //以下代码会把所有异常都转换为编译器所认为的非受查异常。
                    Block.<RuntimeException>throwAs(t); 
                } 
            } 
        }; 
    }

    @SuppressWamings("unchecked")
    public static <T extends Throwable> void throwAs(Throwable e) throws T 
    {
        throw (T) e; 
    }   
}

public class Test
{
    public static void main(String[] args) 
    {
        new Block()
        {
            public void body() throws Exception
            {
                Scanner in = new Scanner(new FileC'ququx") , "UTF-8");
                while (in.hasNext())
                System.out.println(in.next()); 
            } 
        } 
        .toThread().start(); 
    } 
}
```

## 10. 注意擦除后的冲突
1. 泛型类在擦除后可能会与Object类中的方法产生冲突，解决方法是重新命名引发错误的方法。
2. 要想支持擦除的转换，就需要强行限制一个类或类型变量不能同时成为两个接口类型的子类，而这两个接口是同一接口的不同参数化。

```java
public class Pair<T> 
{
    public boolean equals(T value) { return first.equals(value) && second.equals(value); }
}
//考虑Pair<String>，它有两个equals方法，这两个方法在方法擦除后会产生冲突
//解决方法是重新命名equals
boolean equals(String) // 定义在Pair<T>中，擦除后变为boolean equals(Object)
boolean equals(Object) // 继承自Object

//Error，因为Manager 会实现 Comparable<Employee> 和 Comparable<Manager>, 这是同一接口的不同参数化。
//实现了 C0mpamble<X> 的类可以获得一个桥方法，对于不同类型的 X 不能有两个这样的方法。
class Employee implements Comparable<Employee> { . . . }
class Manager extends Employee implements Comparable<Manager> { . . . } // Error
public int compareTo(Object other) { return compareTo((X) other); }
```

# 六、泛型类型的继承规则
1. 处于类型安全的考虑，Java规定无论 S 与 T 有什么联系，通常，Pair<S> 与 Pair<T>没有什么联系。
2. 泛型类可以扩展或实现其他的泛型类。如ArrayList<T> 类实现 List<T> 接口。这意味着， 一个 ArrayList<Manager> 可以被转换为一个 List<Manager>。但是，一个 ArrayList<Manager> 不是一个ArrayList<Employee> 或 List<Employee>。

```java
Pair<Manager> managerBuddies = new Pair<>(ceo, cfo);
Pair<Employee> employeeBuddies = managerBuddies; // 非法
employeeBuddies.setFirst(lowlyEmployee);

Pair<Manager> managerBuddies = new Pair<>(ceo, cfo);
Pair rawBuddies = managerBuddies; // OK
rawBuddies.setFirst(new File(". . .")); // 只会出现编译警告
```

# 七、通配符类型
## 1. 通配符的概念
1. 通配符类型中，允许类型参数变化。
2. 通配符限定与类型变量限定十分类似，而且还可以指定一个超类型限定。
```java
//表示任何泛型 Pair 类型， 它的类型参数是 Employee 的子类
//类型 Pair<Manager> 和Pair<Manager> 是 Pair<? extends Employee> 的子类型
//但是类型 Pair<Manager> 和Pair<Manager>之间没有任何关系
public static void printBuddies(Pair<? extends Employee> p)   //子类型限定通配符

//将 getFirst 的返回值赋给一个 Employee 的引用完全合法。
//但不能调用setFirst方法
Pair<Manager> managerBuddies = new Pair<>(ceo, cfo);
Pair<? extends Employee> wildcardBuddies = managerBuddies; // OK
wildcardBuddies.setFirst(lowlyEmployee); // 会产生编译错误

? extends Employee getFirst()
void setFirst(? extends Employee)
```

## 2. 通配符的超类型限定
```java
带有超类型限定的通配符可以向泛型对象写入（set方法），带有子类型限定的通配符可以从泛型对象读取（get方法）。
void setFirst(? super Manager) 
? super Manager getFirst()

public static void minmaxBonus(Manager[] a, Pair<? super Manager> result) 
{
    if (a.length == 0) return;
    Manager rain = a[0];
    Manager max = a[0];
    for (int i *1; i < a.length; i++) 
    {
        if (min.getBonus() > a[i].getBonus()) rain = a[i];
        if (max.getBonus() < a[i].getBonus()) max = a[i]; 
    }
    result.setFirst(min);
    result.setSecond(max); 
}

public static <T extends Conparable<? super T> T min(T[] a)

default boolean removeIf(Predicated<? super E> filter)
{
    ArrayList<Employee> staff = . .;
    Predicate<Object> oddHashCode = obj -> obj.hashCode() %2 != 0;
    staff.removelf(oddHashCode):
}
```

## 3. 无限定通配符
```java
//getFirst 的返回值只能赋给一个 Object。setFirst 方法不能被调用， 甚至不能用 Object 调 用。

? getFirst()
void setFirst(?)

//可以调用 setFirst(null)。
public static boolean hasNulls(Pair<?> p) 
{
    return p.getFirst() = null || p.getSecond() =null; 
}

//通过将 hasNulls 转换成泛型方法，可以避免使用通配符类型,
public static <T> boolean hasNulls(Pair<T> p)
```

## 4. 通配符捕获
```java
//通配符不是类型变量，因此，不能在编写代码中使用“？”作为一种类型。
? t = p.getFirst(); // Error

//通配符捕获
public static <T> void swapHelper(Pair<T> p) 
{ 
    T t = p.getFirst(); 
    p.setFirst(p.getSecond());
    p.setSecond(t); 
}

public static void swap(Pair<?> p) { swapHelper(p); }  //参数T 捕获通配符

public static void maxminBonus(Manager ] a, Pair<? super Manager> result) 
{
    minmaxBonus(a, result);
    PairAlg.swap(result); // OK swapHelper captures wildcard type
}
```

# 八、反射和泛型
## 1. 泛型 Class 类
//反射不能获得泛型类型参数的太多信息，因为它们会被擦除。
```java
T newInstance() //返回无参数构造器构造的一个新实例。
T cast(Object obj) //如果 obj 为 null 或有可能转换成类型 T， 则 返 回 obj ; 否 则 拋 出 BadCastException异常。
T[ ] getEnumConstants( ) //如果 T 是枚举类型， 则返回所有值组成的数组，否则返回 null。
Class<? super T> getSuperclass( ) //返回这个类的超类。如果 T 不是一个类或 Object 类， 则返回 null。
Constructor<T> getConstructor(Class... parameterTypes) 
Constructor<T> getDeclaredConstructor(Class... parameterTypes)  //获得公有的构造器， 或带有给定参数类型的构造器。
T newlnstance(0bject... parameters) //返回用指定参数构造的新实例。
```

## 2. 使用 Class<T> 参数进行类型匹配
```java
//Employee.class 是类型 Class<Employee> 的一个对象。makePair 方法的类型参数 T 同 Employee匹配， 并且编译器可以推断出这个方法将返回一个 Pair<Employee>。
public static <T> Pai r<T> makePair(Class<T> c) throws InstantiationException, IllegalAccessException
{
return new Pairo(c.newInstance(), c.newInstance()); 
}
makePair(Employee.class)
```

## 3. 虚拟机中的泛型类型信息
可以使用反射 API 来确定： 
- 这个泛型方法有一个叫做 T 的类型参数。
这个类型参数有一个子类型限定，其自身又是一个泛型类型。 
这个限定类型有一个通配符参数。
这个通配符参数有一个超类型限定。 
这个泛型方法有一个泛型数组参数。
