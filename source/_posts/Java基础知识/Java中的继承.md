---
title: Java中的继承
tags: [Java]
categories: Java
top: true
date: 2020-05-01 13:55:39
---

# 一、类、超类和子类
## 1. 定义子类
已存在的类称为超类、基类或父类; 新类称为子类、派生类或孩子类。在设计类时，应该将通用的方法放在超类中， 而将具有特殊用途的方法放在子类中。
```java
public class Manager extends Employee //在 Java 中， 所有的继承都是公有继承
{
    //定义子类特有数据域
    private double bonus;

    //定义子类特有方法
    public void setBonus(double bonus) {  //属于 Employee 类的对象不能使用该方法，但Manager 类自动地继承了超类 Employee 中的方法。
        this.bonus = bonus; 
    }  

    //覆盖超类方法
    public double getSalary()
    {
        double baseSalary = super.getSalary();  //子类不能直接访问超类的私有域，应通过关键字super调用超类的域访问器来访问。
        return baseSalary + bonus;              
    }

    public Manager(String name, double salary, int year, int month, int day) 
    {
        //如果子类的构造器没有显式地调用超类的构造器， 则将自动地调用超类默认（没有参数 )的构造器。
        //如果超类没有不带参数的构造器， 并且在子类的构造器中又没有显式地调用超类的其他构造器， 则 Java 编译器将报告错误。
        //调用构造器的语句只能作为另一个构造器的第一条语句出现。
        super(name, salary, year, month, day);  //通过 super调用超类构造器实现对超类私有域进行初始化
        bonus = 0; 
    }
}

Manager boss = new Manager("Carl Cracker" , 80000, 1987, 12 , 15);
boss.setBonus(5000);

Employee[] staff = new Employee[3];
staff[0] = boss;  //变量 staff[0] 与 boss 引用同一个对象。但编译器将 staff[0]看成 Employee 对象。故staff[0]不能调用子类特有方法setBonus()
staff[1] = new Employee("Harry Hacker", 50000, 1989, 10, 1);
staff[2] = new Employee("Tony Tester" , 40000, 1990, 3, 15);

for(Employee e : staff)         //e 既可以引用 Employee 类型的对象，也可以引用 Manager 类型的对象。
    System.out.println(e.getName() + " " + e.getSalary());  //虚拟机知道e实际引用的对象类型，因此能够正确地调用相应的方法。
```
1. super关键字与this引用不同，它不是一个对象引用，它表示的是当前对象的当前类的父类对象。
2. 在子类中可以增加域、增加方法或覆盖超类的方法，然而绝对不能删除继承的任何域和方法。
3. 在覆盖方法时， 一定要保证返回类型的兼容性。<font color=Crmison>允许子类将覆盖方法的返回类型定义为原返回类型的子类型。</font>称该方法具有**可协变的返回类型**
4. <font color=Crmison>在覆盖一个方法的时候，子类方法不能低于超类方法的可见性。</font> 
5. <font color=Crmison>子类对象引用调用的方法始终都是实例化的子类中的重写方法，只有明确调用了super.xxx关键词或者是子类中没有该方法时，才会去调用父类相同的同名方法。</font>

>**多态**：一个对象变量可以指示多种实际类型的现象被称为多态。
**动态绑定**：在**运行时**能够自动地选择调用哪个方法的现象称为动态绑定。
**静态绑定**：如果是 private 方法、 static 方法、 final 方法或者构造器， 那么**编译器**将可以准确地知道应该调用哪个方法， 我们将这种调用方式称为静态绑定。

## 2. 继承层次
**继承层次**：由一个公共超类派生出来的所有类的集合被称为继承层次。
**继承链**：从某个特定的类到其祖先的路径被称为该类的继承链。
<font color=Crmison>Java 不支持多继承(extends)。</font>

## 3. 多态

1. 即超类变量既可以引用一个超类对象， 也可以引用一个超类的任何一个子类的对象。
2. 不能将一个超类的引用赋给子类变量。

```java
//在 Java 中，子类数组的引用可以转换成超类数组的引用， 而不需要采用强制类型转换。
Manager[] managers = new Manager[10];
Employee[] staff = managers; // OK

//所有数组都要牢记创建它们的元素类型， 并负责监督仅将类型兼容的引用存储到数组中。
//这里使用 new managers[10] 创建的数组是一个经理数组。如果试图存储一个 Employee 类型的引用就会引发 ArrayStoreException 异常。
staff[0] = new Employee("Harry Hacker", . . .); //ArrayStoreException 异常
```

## 4. 理解方法调用
x.f(param)调用过程：
1. 编译器査看对象的声明类型和方法名。编译器将会一一列举所有x所声明类中名为f的方法和其超类中访问属性为 public 且名为 f 的方法（超类的私有方法不可访问）。
2. 编译器进行重载解析。以获得需要调用的方法名字和参数类型。
3. 编译器采用静态绑定或动态绑定方式生成一条调用f(param)的指令。
4. 当程序运行，并且采用动态绑定调用方法时， 虚拟机一定调用与 x **所引用对象的实际类型**最合适的那个类的方法。若没有则在其超类中寻找。

虚拟机预先为每个类创建了一个方法表, 其中列出了所有方法(包括继承的方法)的签名和实际调用的方法。这样一来，在真正调用方法的时候， 虚拟机仅查找这个表就行了。

## 5. 阻止继承：final 类和方法
不允许扩展的类被称为 final 类。
```java
//如果将一个类声明为 final， 只有其中的方法自动地成为 final,而不包括域。
public final class Executive extends Manager
{
    public final String getName(){  //final方法不能被覆盖
        return name;
    }
}
```

## 6. 强制类型转换
将一个超类的引用赋给一个子类变量，必须进行类型转换，这样才能够通过运行时的检査。但是只有引用子类变量的超类引用可以被强制类型转换为子类引用。一般只有在会使用到 Manager 中特有的方法时才需要进行类型转换。
```java
//只能在继承层次内进行类型转换。 
//在将超类转换成子类之前，应该使用instanceof进行检查。
Manager boss = (Manager)staff[0];   //ok
Manager boss = (Manager)staff[1]; // Error  //若未捕获ClassCastException 异常，程序就会终止执行。

if(staff[1] instanceof Manager) 
{
    boss = (Manager)staff[1];
}

x instanceof C  //若x为null则不会产生异常只是返回false。
```

## 7. 抽象类
抽象类一般作为派生其他类的基类，而不作为想使用的特定的实例类。通常它只包含一些通用的属性和方法，而这些通用方法往往只是一个定义不需要具体的实现。
```java
//利用abstract关键字修饰方法就无需实现该方法
//包含一个或多个抽象方法的类本身必须被声明为抽象的。
//抽象类的子类也可以为抽象类
//类即使不含抽象方法，也可以将类声明为抽象类。
public abstract class Person
{
    private String name;

    public Person(String name){
        this.name = name; 
    }

    public abstract String getDescription();  //抽象类不能有函数体
    
    public String getName(){
        return name;
    }
}

//抽象类不能被实例化。可以定义一个抽象类的对象变量，但是不能构造抽象类对象，它只能引用非抽象子类的对象。
Person p = new Student("Vinee Vu" , "Economics");
```

## 8. 受保护访问
1. 由于类中的私有域对所有其他类包括子类都是不可见的，因此若子类的方法想访问超类的某个域或方法，可将超类中的方法或域声明为protected。但子类方法中只能访问属于该类的对象的protected方法而不能访问其它子类对象的受保护方法。
2. 不过<font color=Crmison>子类中的方法只能够访问子类对象中继承的超类的受保护域，而不能访问超类对象中的这个域，如此可避免滥用受保护机制。</font>
3. 在实际应用中，要谨慎使用 **protected 属性**。因为有可能会违背OOP提倡的数据封装原则。
4. **受保护的方法**更具有实际意义。它对于指示那些不提供一般用途而应在子类中重新定义的方法很有用。

## 9. 控制可见性的4个访问修饰符
**private**:仅对本类可见。
**public**:对所有类可见。
**protected**:对本包和所有子类可见。
**默认**：对本包可见。
其中只有public和默认可用于修饰类。

# 二、Object是所有类的超类
1. 可以使用 Object 类型的变量引用任何类型的对象。
2. 在 Java 中，只有基本类型不是对象。
3. 所有的数组类型，不管是对象数组还是基本类型的数组都扩展了 Object 类。

```java
Object obj = new Employee("Harry Hacker", 35000);
Employee e = (Employee)obj ;

Employee[] staff = new Employee[10];
obj = staff; // OK
obj = new int[10]; // OK
```

## 1. equals方法
是基类Object中的一个可覆盖方法，在基类中，该方法与`==`运算符等价，比较的都是对象的内存地址， 可在子类中覆盖改写该方法。
```java
public class Employee
{
    //equals方法改写原则
    public boolean equals(Object otherObject) { 
        if (this == otherObject) return true;  //若引用同一个对象则返回true,实际是比较两个对象的默认hashcode是否相等
        if (otherObject == null) return false; //若显示参数为null则返回false

        //比较 this 与 otherObject 是否属于同一个类。如果 equals 的语义在每个子类中有所改变，就使用 getClass 检测。
        //如果所有的子类都拥有统一的语义，就使用 instanceof 检测。
        //if (!(otherObject instanceof ClassName)) return false;
        if (getClass() != otherObject.getClass()) return false;   //若两个对象所属的类不同则返回false，getClass 方法将返回一个对象所属的类
                                                                
        //比较两个相同类的不为null的对象的数据域是否相等
        Employee other = (Employee)otherObject; 

        //如果两个参数都为 null， Objects.equals(a，b) 调用将返回 true; 如果其中一个参数为 null ,则返回 false ; 否则， 如果两个参数都不为 null，则调用 a.equals(b)
        // //使用 =比较基本类型域，使用 equals 比较对象域。
        return Objects.equals(name,other.name) && salary == other.salary 
                && Object.equals(hireDay,other.hireDay);
}

public class Manager extends Employee
{
    public boolean equals(Object otherObject) {
        if (!super.equals(otherObject)) return false; //在子类中定义 equals 方法时，首先调用超类的 equals,若检测失败对象就不可能相等。
        Manager other = (Manager)otherObject;          
        return bonus == other.bonus; 
    } 
}
```

## 2. 相等测试与继承
设计equals方法的原则：
**自反性：**对于任何非空引用 x, x.equals(x) 应该返回 true。
**对称性：**对于任何引用 x 和 y, 当且仅当 y.equals(x) 返回 true , x.equals(y) 也应该返回 true。
**传递性：**对于任何引用 x、y 和 z, 如果 x.equals(y) 返回 true， y.equals(z) 返回 true, x.equals(z) 也应该返回 true。
**一致性：**如果 x 和 y 引用的对象没有发生变化，反复调用 x.equals(y) 应该返回同样的结果。
对于任意非空引用 x, x.equals(null) 应该返回 false。

```java
static boolean equals(type[]a , type[] b)   //如果两个数组长度相同， 并且在对应的位置上数据元素也均相同
static boolean equals(Object a, Object b)  //如果a和b都为null，返回true;如果只有其中之一为 null，则返回false;否则返回a.equals(b)。
```

## 3. hashCode方法
由于 hashCode方法定义在 Object 类中， 因此每个对象都有一个默认的散列码，其值为对象的存储地址。
```java
//String的散列码是由内容导出的
int hash = 0;
for (int i = 0; i < length0；i++)
    hash = 31 * hash + charAt(i);

//StringBuilder 类中没有定义hashCode 方法

//在重写hashcode方法时可以调用 Objects.hash并提供多个参数，这个方法会对各个参数调用 Objects.hashCode，并组合这些散列值。
public int hashCode()
{
    return Objects,hash(name, salary, hireDay); 
}

static int hash(Object . .. objects) //返回一个散列码，由提供的所有对象的散列码组合而得到。
static int hashCode(Object a )  //如果 a 为 null 返回 0， 否则返回 a.hashCode()

static int hashCode((int11ong|short|byte|double|f1oat|char|boolean) value) //返回给定值的散列码。

static int hashCode(type[] a ) //计算数组 a 的散列码。这个散列码由数组元素的散列码组合得到。
```
java规定<font color=Crmison>如果重新定义 equals方法，就必须重新定义 hashCode 方法。</font>因为Hashcode是用于散列数据的快速存取的，如利用 HashSet/HashMap/HashTable类来存储数据时，都是根据存储对象的hashcode值来判断是否相同。如果我们对一个对象重写了 equals方法，意思是只要对象的成员变量的值相等那么equals就返回true，但不重写hashcode方法，那么我们再new一个新的对象的时候，当原对象.equals(新对象)等于true的时候，两者的hashcode值是不相等的。由此产生了理解上的不一致，比如在存储散列集合（如Set类）的时候，将会存储了两个一样的对象，导致混淆，因此，<font color=Crmison>也就必须重写hashcode方法,且Equals与hashCode 的定义必须一致：如果 x.equals(y) 返回 true, 那么 x.hashCode( )就必须与 y.hashCode( ) 具有相同的值。</font>

## 4. toString方法
Object 类定义了 toString 方法， 用来打印输出对象所属的类名和散列码即`类名@散列码`。toString方法是一种非常有用的调试工具。在标准类库中，许多类都定义了 toString方 法， 以便用户能够获得一些有关对象状态的必要信息。因此java建议为自定义的每一个类增加 toString 方法。
 ```java
System.out.println(System.out);  //因为 PrintStream 类的设计者没有覆盖 toString方法故得到java.io.PrintStream@2f6684的结果。

public String toString()
{
    return getClass().getName()  //getClaSS( ).getName( ) 获得类名的字符
串
        + "[name=" + name
        + ",salary=" + salary
        + ",hireDay=" + hireDay
        + "]"; 
}

public class Manager extends Employee
{
    public String toString()
    {
        return super.toString()
        + "[bonus=" + bonus
        + "]"; 
    } 
}

Point p = new Point(10, 20);          
String message = "The current position is " + p;  //此处编译器会自动的调用p.toString()方法，以便获得这个对象的字符串描述。

""+x;  //可替代x.toString()的调用，与 toString 不同的是，如果 x 是基本类型，这条语句照样能够执行。

int[] luckyNumbers = { 2, 3, 5, 7, 11, 13 } ;  
String s = "" + luckyNumbers;       //[I@la46e30（前缀 [I 表明是一个整型数组）。
String s = Arrays.toString(luckyNumbers);   //[2,3,5,7,11,13]

Arrays.deepToString;  //打印多维数组

Class getSuperclass( );  //以 Class 对象的形式返回这个类的超类信息。
 ```

# 三、泛型数组列表ArrayList
1. ArrayList 是一个采用类型参数的泛型类,它在添加或删除元素时， 具有自动调节数组容量的功能。<font color=Crmison><>中的类型不能是基本数据类型。</font>
2. <font color=Crmison>元素在集合中有序，指的是元素插入过程中记录了元素的插入顺序。</font>
## 1. 创建ArrayList
```java
ArrayList<Employee> staff = new ArrayList<Employee>();  //构造一个空数组列表
ArrayList<Employee> staff = new ArrayList<>(); //可以结合new操作符使用“菱形”语法,编译器会检查新值是什么。如果赋值给一个变量，或传递到某个方法，或者从某个方法返回，编译器会检査这个变量、 参数或
                                                //方法的泛型类型，然后将这个类型放在<>中。

//在 Java SE 5.0 以后的版本中， 没有后缀 <...> 仍然可以使用ArrayList, 它将被认为是一个删去了类型参數的“ 原始” 类型。

//添加对象
staff.add(new Employee("Harry Hacker", ...)); //在数组列表的尾端添加一个元素，如果调用add且内部数组已经满了，数组列表就将自动地创建一个更大的数组，并将所有的对象从较小的数组中拷贝到较大的数组中。

staff.ensureCapacity(l00);  //可以指定数组大小，则在100次调用add之间不用重新分配空间。

ArrayList<Employee> staff = new ArrayList<>(l00); //可以把初始容量传递给 ArrayList 构造器。表明它拥有保存100个元素的潜力，重新分配空间的话会超过100

staff.size();  //返回数组列表中包含的实际元素数目

void trimToSize( )  //将数组列表的存储容量削减到当前尺寸。垃圾回收器将回收多余的存储空间。应该在确认不会添加任何元素时，再调用该方法否则添加元素时需要花时间再次移动存储块。
```

## 2. 访问数组列表元素
```java
staff.set(i, harry);  //设置第i个元素，只有i小于或等于数组列表中当前实际元素个数时才可以调用。

Employee e = staff.get(i);  //获得数组列表的元素

//灵活扩展数组
ArrayList<X> list = new ArrayList<>();
while (. . .) 
{ 
    x = . .;
    list.add(x); 
}

//方便访问数组
X[] a = new X[list.size()];
list.toArray(a); 

staff.add(i,e);  //在数组列表的中间插入元素
Employee e = staff.remove(i);  //从数组列表中间删除一个元素。

for (Employee e : staff)   //可以使用“ foreach” 循环遍历数组列表：
```

## 3. 类型化与原始数组列表的兼容性
```java
public class EmployeeDB
{
    public void update(ArrayList list) { . . . }
    public ArrayList find(String query) { . . . } 
}

ArrayList<Employee〉staff = . . .;   //可以将一个类型化的数组列表传递给 update 方法,无需任何类型转换，但是这样不安全
employeeDB.update(staff);   //也可以将 staff 对象传递给 update 方法。

@SuppressWarnings("unchecked") ArrayList<Employee> result = employeeDB.find(query);  //将一个原始 ArrayList 赋给一个类型化 ArrayList 会得到一个警告。一旦能确保不会造成严重的后果，可以用@SuppressWamings("unchecked") 标注来标记这个变量能够接受类型转换。
```

# 四、对象包装器与自动装箱
1. **包装器：**所有的基本类型都有一个与之对应的类，称为包装器。Integer、Long、Float、Double、Short、Byte、Character、Void 和 Boolean (前 6 个类派生于公共的超类 Number)。<font color=Crmison>对象包装器是不可变的，不可改变其值，还是final的，不可定义其子类。</font>
2. 自动装箱规范要求 boolean、byte、char 127， 介于 -128 ~ 127 之间的 short 和int 被包装到**固定的对象**中。此时若用`==`比较两个值相同的包装器对象则一定为true。
3. 装箱和拆箱是编译器认可的，而不是虚拟机。编译器在生成类的字节码时，插入必要的方法调用。虚拟机只是执行这些字节码。

```java
ArrayList<Integer> list = new ArrayList<>();   //效率远低于int[],仅在构造小型集合时使用它。

list.add(3);  //自动装箱；会自动变换为list.add(Integer.valueOf(3));
int n = list.get(i);  //自动拆箱；会自动翻译成 int n = list.get(i).intValue();

//算术表达式中也能自动装箱和拆箱
Integer n = 3;
n++;    //编译器会自动地插入一条对象拆箱的指令， 然后进行自增计算， 最后再将结果装箱。

Integer a = 1000;
Integer b = 1000;
if (a == b)    //一般为false，而java中若将经常出现的值包装到同一个对象中，这种比较就有可能成立。为了避免这种不确定性，一般在两个包装器对象比较时调用 equals 方法。

Integer n = null;  //可以引用null
System.out.println(2 * n);  //会抛出一个 NullPointerException 异常

Integer n = 1;
Double x = 2.0;
System.out.println(true ? n : x);  //输出1.0；如果在一个条件表达式中混合使用 Integer 和 Double 类型， Integer 值就会拆箱，提升为 double, 再装箱为 Double。

int x = Integer.parseInt(s);  //将字符串转换成整型。

public static void triple(IntHolder x)   //org.omg.CORBA 包中定义的持有者类型， 包括 IntHolder、BooleanHolder 等，都包含一个公有域值，可以改变x的值。
{
     x.value = 3 * x.value; 
}

static String toString(int i )  //以一个新 String 对象的形式返回给定数值 i 的十进制表示。  
static String toString(int i ,int radix )  //返回数值 i 的基于给定 radix 参数进制的表示。

static int parseInt(String s,int radix)

Static Integer valueOf(String s, int radix)

Number parse(String s)  //返回数字值，假设给定的 String 表示了一个数值。
```

# 五、参数数量可变的方法
也称为变参方法。
```java
//printf方法的定义
public class PrintStream
{
    public PrintStream printf(String fmt , Object... args)   //... 是 Java 代码的一部分，它表明这个方法可以接收任意数量的对象（除 fmt参数之外）。Object…参数类型与 Object[]完全一样。
        {                                                   //如果调用者提供的是整型数组或者其他基本类型的值，自动装箱功能将把它们转换成对象。
            return format(fmt, args);                       //现在将扫描 fmt 字符串， 并将第 i 个格式说明符与 args[i] 的值匹配起来。
        } 
}
System.out.printf("%d %s", new Object[] { new Integer(n), "widgets" } ); //编译器需要对 printf 的每次调用进行转换， 以便将参数绑定到数组上，并在必要的时候进行自动装箱.

//自定义变参方法:计算若干个数值的最大值。
public static double max (double... values) 
{
    double largest = Double.NECATIVEJNFINITY;
    for (double v : values) 
        if (v > largest) largest = v;
    return largest; 
}
double m = max(3.1, 40.4, -5);

public static void main(String... args)
```
允许将一个数组传递给可变参数方法的**最后一个参数**,因此，可以将已经存在且最后一个参数是数组的方法重新定义为可变参数的方法，而不会破坏任何已经存在的代码。

# 六、枚举类
所有的枚举类型都是 Enum 类的子类。它们继承了这个类的许多方法。
```java
public enum Size { SMALL , MEDIUM, LARGE, EXTRAJARGE };  //实际上定义了一个类刚好有4个实例，由于Enum中equlas方法直接用==实现，故在比较两个枚举变量值时直接使用==即可

//可以在枚举类型中添加一些构造器、 方法和域。
public enum Size
{
    SMALL("S"), MEDIUM("M"), LARGE("L"), EXTRA_LARGE("XL");

    private String abbreviation;

    private Size(String abbreviation) { this,abbreviation = abbreviation; }
    public String getAbbreviation() { return abbreviation; } 
}

Size.SMALL.toString();  //返回字符串"SMALL"。
Size s = Enum.valueOf(Size,class, "SMALL");  //将 s 设置成 Size.SMALL

Size[] values = Size.values();  //返回一个包含全部枚举值的数组。

int ordinal ()  //返回枚举常量在 enum 声明中的位置，位置从 0 开始计数。
int compareTo( E other ) //如果枚举常量出现在 Other 之前， 则返回一个负值；如果 this=other，则返回 0; 否则，返回正值。枚举常量的出现次序在 enum 声明中给出。
```

# 七、反射
能够分析类能力的程序称为反射，反射库提供了一个非常丰富且精心设计的工具集，使用它的主要人员是工具构造者，而不是应用程序员。反射的功能：
1. 在运行时分析类的能力。
2. 在运行时查看对象，例如，编写一个 toString 方法供所有类使用。
3. 实现通用的数组操作代码。
4. 利用 Method 对象， 这个对象很像C++中的函数指针。

## 1. Class类
Class类中保存着Java 运行时系统始终为所有对象维护的一个被称为运行时的类型标识，虚拟机利用运行时类型信息选择相应的方法执行。通过Class类可以访问这些信息。一个Class对象将表示**一个特定类的属性**。
Class 类实际上是一个**泛型类**。例如， Employee.class 的类型是 Class&lt;Employee&gt;,但是它将已经抽象的概念更加复杂化了。在大多数实际问题中， 可以忽略类型参数， 而使用原始的 Class 类。
```java
Employee e;
Class cl = e.getClass(); //返回一个 Class 类型的实例。

System.out.println(e.getClass().getName() + " " + e.getName()); //Class的 getName返回类的名字。返回 Employee Harry Hacker或Manager Harry Hacker

Random generator = new Random():
Class cl = generator.getClass();
String name = cl.getName(); // "java.util .Random"。如果类在一个包里，包的名字也作为类名的一部分

String className = "java.util.Random";
Class cl = Class.forName(className);   //获得类名对应的 Class 对象。
                                        //这个方法只有在 dassName 是类名或接口名时才能够执行。否则将抛出一个 checked exception(已检查异常），在使用时必须提供一个异常处理器。

Class dl = Random.class; // if you import java.util
Class cl 2 = int.class;   //一个 Class 对象实际上表示的是一个类型，而这个类型未必一定是一种类。
Class cl 3 = Double[].class;   //通过T.class获得Class对象。

Double[ ] class.getName( ) //返回 [Ijava.lang.Double;
int[ ].class.getName( ) //返回 [I  

if (e.getClass() == Employee.class)  //可以使用==比较两个Class对象

e.getClass().newInstance(); //来动态地创建一个与类e具有相同类型的实例

String s = "java.util.Random";
Object m = Class.forName(s).newInstance();
```

## 2. 捕获异常
若程序中没有提供捕获异常的处理器对异常情况进行处理，程序在运行时发生异常就会终止执行，并在控制台上打印一条信息给出异常类型。
**已检查异常**：编译器将会检查是否为调用了抛出已检查异常方法的相关代码提供了异常处理器，否则将不能通过编译。
**未检查异常**：编译器不要求强制处置的异常，虽然你有可能出现错误，但是我不会在编译的时候检查，需要自己精心编写代码来避免。例如访问 null 引用等。
```java
try
{
    String name = . . .; 
    Class cl = Class.forName(name); //如果类名不存在， 则将跳过 try 块中的剩余代码，程序直接进人 catch 子句，否则跳过catch子句的处理器代码。
}
catch (Exception e) 
{
     e.printStackTrace();   //利用Throwable 类（Exception类的超类）的 printStackTrace 方法打印出栈的轨迹。
}
```

## 3. 利用反射分析类的能力
在 java.lang.reflect 包中有三个类 Field、 Method 和 Constructor 分别用于描述类的域、方法和构造器。
```java
import java.lang.Class;

Field getField(String name )   //返回指定名称的公有域
Field[] getDeclaredFields()  //返回类中声明的给定名称的域， 或者包含声明的全部域的数组。

//如果类中没有域， 或者 Class 对象描述的是基本类型或数组类型， 这些方法将返回一个长度为 0 的数组。
Field[] getFields()  //getFields 方法将返回一个包含 Field 对象的数组， 这些对象记录了这个类或其超类的公有域。
Filed[] getDeclaredFie1ds()  //getDeclaredField 方法也将返回包含 Field 对象的数组， 这些对象记录了这个类的全部域。

//返回包含 Method 对象的数组
//getMethods 将返回所有的公有方法， 包括从超类继承来的公有方法；
//getDeclaredMethods 返回这个类或接口的全部方法， 但不包括由超类继承了的方法。
Method[] getMethods()
Method[] getDeclareMethods()

//返回包含 Constructor 对象的数组
Constructor[] getConstructors()  //返回公有构造器
Constructor[] getDeclaredConstructors()  //返回所有构造器
```
```java
import java.lang.reflect.Field;
import java.lang.reflect.Method;
import java.lang.reflect.Constructor;

Class getDeclaringClass( )  //返冋一个用于描述类中定义的构造器、 方法或域的 Class 对象。
Class[] getExceptionTypes ( ) //( 在 Constructor 和 Method 类中）返回一个用于描述方法抛出的异常类型的 Class 对象数组。
int getModifiers( )  //返回一个用于描述构造器、 方法或域的修饰符的整型数值。使用 Modifier 类中的这个方法可以分析这个返回值。用不同的位开关描述 public 和 static 这样的修饰符使用状况
String getName( ) //返冋一个用于描述构造器、 方法或域名的字符串。
Class[] getParameterTypes ( ) //( 在 Constructor 和 Method 类 中）返回一个用于描述参数类型的 Class 对象数组。 
Class getReturnType( )  //( 在 Method 类中）返回一个用于描述返H类型的 Class 对象。

static String toString(int modifiers ) //返回对应 modifiers 中设置的修饰符的字符串表示。

//这些方法将检测方法名中对应的修饰符在 modifiers 值中的位，即方法和构造器是否是public、 private 或 final。
static boolean isAbstract(int modifiers )
static boolean isFinal (int modifiers )
static boolean islnterface(int modifiers )
static boolean isNative(int modifiers )
static boolean isPrivate(int modifiers )
static boolean isProtected(int modifiers )
static boolean isPublic(int modifiers )
static boolean isStatic(int modifiers )
static boolean isStrict(int modifiers )
static boolean isSynchronized(int modifiers )
static boolean isVolati1e(int modifiers )
```

## 4. 在运行时使用反射分析对象
1. 在编写程序时， 如果知道想要査看的域名和类型，查看指定的域是一件很容易的事情。而利用反射机制可以查看在编译时还不清楚的对象域,从而<font color=Crmison>进一步查看运行时数据域的实际内容。</font>
2. 反射机制的默认行为受限于 Java 的访问控制。除非拥有访问权限，否则Java 安全机制只允许査看任意对象有哪些域， 而不允许读取它们的值。可利用需要调用 Field、 Method 或Constructor 对象的 setAccessible 方法来覆盖访问控制。
3. 当调用get方法获得域值时参数为基本数据类型，则反射机制将会自动地将这个域值打包到相应的对象包装器中。也可以调用Field 类中的 getDouble 等方法。
```java
Employee harry = new Employee("Harry Hacker", 35000, 10, 1, 1989);
Class cl = harry.getClass; 
Field f = cl .getDeclaredField("name"); 

f.setAtcessible(true);   //覆盖访问控制

Object v = f.get(harry);  //获得name域运行时的具体值为"Harry Hacker"
f.set(obj,value);  //将 obj 对象的 f 域设置成新值

public String toString()   
{
    return new ObjectAnalyzer().toString(this);  //利用ObjectAnalyzer类的toString方法可为每个自定义类重写一个通用的toString方法，很方便。将打印类的所有信息，包括运行时的具体域值。
}

boolean isAccessible( )  //返回反射对象的可访问标志的值。
static void setAccessible(AccessibleObject[] array,boolean flag)  //是一种设置对象数组可访问标志的快捷方法。
```

## 5. 使用反射编写泛型数组代码
将一个 Employee[]临时地转换成 Object[] 数组， 然后再把它转换回来是可以的，但一从开始就是 Object[] 的数组却永远不能转换成 Employee[] 数组。为了编写这类通用的数组代码，需要能够创建与原数组类型相同的新数组。
```java
//注意，这个 CopyOf 方法可以用来扩展任意类型的数组， 而不仅是对象数组。
//应该将 goodCopyOf 的参数声明为 Object 类型，而不要声明为对象型数组（Object[])。整型数组类型 int[] 可以被转换成 Object，但不能转换成对象数组。
public static Object goodCopyOf(Object a, int newLength) 
{
    Class cl = a.getClass();
    if (!cl.isArray()) return null;
    Class componentType = cl.getComponentType();
    int length = Array.getLength(a);
    Object newArray = Array.newInstance(componentType, newLength):
    System.arraycopy(a, 0, newArray, 0, Math.min(length, newLength));
    return newArray; 
}

static Object get(Object array,int index)  //( xxx 是 boolean、byte、 char、 double、 float、 int、 long、 short 之中的一种基本类型。)
static xxx getXxx(Object array,int index) //这些方法将返回存储在给定位置上的给定数组的内容。

static void set(Object array,int index,Object newValue)  //( xxx 是 boolean、 byte、char、double、float、 int、 long、 short 之中的一种基本类型。)
static setXxx(Object array,int index,xxx newValue)      //这些方法将一个新值存储到给定位置上的给定数组中。

static int getLength(Object array)  //返回数组的长度。

static Object newInstance(Class componentType,int length)
static Object newInstance(Class componentType,int[] lengths)  //返回一个具有给定类型、给定维数的新数组。
```

## 6. 利用反射调用任意方法
利用反射中的Method类的invoke方法可以回调任意对象的任意方法。
```java
Object invoke(Object obj, Object... args)  //第一个参数是隐式参数， 其余的对象提供了显式参数，对于静态方法，第一个参数可以被忽略， 即可以将它设置为 null。
                                           //如果返回类型是基本类型， invoke 方法会返回其包装器类型。必须相应地完成类型转换。
Method getMethod(String name, Class... parameterTypes)  //利用Class类中的getMethod方法获得Method对象
                                                        //也可以通过调用 getDeclareMethods 方法， 然后对返回的 Method 对象数组进行查找， 直到发现想要的方法为止。

Method ml = Employee.class.getMethod("getName");
Method m2 = Employee.class.getMethod("raiseSalary", double.class);

String n = (String) ml.invoke(harry);  
double s = (Double) m2.invoke(harry);
f.invoke(null, x);          //有可能存在若干个相同名字的方法，鉴于此，还必须提供想要的方法的参数类型。
```
反射的优缺点：
1. 反射对于编写系统程序来说极其实用，但是通常不适于编写应用程序。
2. 如果在调用方法的时候提供了一个错误的参数，那么 invoke 方法将会抛出一个异常；
3. 且使用反射获得方法指针的代码要比仅仅直接调用方法明显慢一些。
4. 反射是很脆弱的，即编译器很难帮助人们发现程序中的错误， 因此只有在运行时才发现错误并导致异常。

因此建议仅在必要的时候才使用 Method 对象，而最好使用接口以及 Java SE8 中 的 lambda 表达式，它们的代码的执行速度更快，更易于维护。

# 八、继承的设计技巧
1. 将公共操作和域放在超类；
2. 不要使用受保护的域；
3. 使用继承实现**严格**“ is-a” 关系，即超类中不能存在子类不需要的域；
4. 除非**所有**继承的方法都有意义，否则不要使用继承；
5. 在覆盖方法时，不要改变预期的行为；
6. 使用多态(具有动态邦迪机制)，而非类型信息；
7. 不要过多地使用反射。