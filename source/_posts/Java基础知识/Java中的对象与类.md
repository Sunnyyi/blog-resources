---
title: Java中的对象与类
tags: [Java]
categories: Java
top: true
date: 2020-4-29 09:45:33
---

Java 是完全面向对象(OOP)的，而面向对象更加适用于解决规模较大的问题。
# 一、类和对象
## 1. 类
**类**：类是构造对象的模板或蓝图，由类**构造对象**的过程称为**创建类的实例**。
**封装**: 从形式上看，封装不过是将数据和行为组合在一个包中， 并对对象的使用者隐藏了数据的实现方式。
**实例域**：对象中的数据。
**方法**：操纵数据的过程。
**状态**：对于每个特定的类实例（对象）都有一组特定的实例域值。这些值的集合就是这个对象的当前状态。
并不是所有的类都具有面向对象特征，例如Math类只封装了功能，它不需要也不必隐藏数据。

## 2. 对象
三个主要特征：
1. 对象的行为（behavior)—可以对对象施加哪些操作，或可以对对象施加哪些方法。 
2. 对象的状态（state )—对象状态的改变必须通过调用方法实现，否则会破坏封装性。 
3. 对象标识（identity )—每个对象都有一个唯一的身份。

对象与对象变量的区别：
```java
Date deadline;  //变量deadline不是一个对象， 实际上也没有引用对象,因此还不能将任何 Date 方法应用于这个变量上。
deadline = new Date();   //一个对象变量并没有实际包含一个对象，而仅仅引用一个对象。
                        //表达式 new Date() 构造了一个 Date 类型的对象， 并且它的值是对新创建对象的引用。这个引用存储在变量 deadline 中。
Date birthday = new Date();
deadline = birthday;   

deadline = null;  //显式地将对象变量设置为 null,表明这个对象变量目前没有引用任何对象。该对象不能使用任何方法。
                //局部变量不会自动地初始化为 null，而必须通过调用 new 或将它们设置为 null 进行初始化。
```


## 3. 类之间的关系
1. 依赖（“uses-a”）:一个类的方法操纵另一个类的对象，即一个类依赖于另一个类。又称为<font color=Crmison>耦合度</font>，实际开发中应让这种耦合最小。
2. 聚合（“has-a”） :类 A 的对象包含类 B 的对象。
3. 继承（“is-a”）:用于表示特殊与一般关系。如果类 A 扩展类 B, 类 A 不但包含从类 B 继承的方法，还会拥有一些额外的功能。

表示关系的UML符号：
![UML](UML.png)

# 二、使用预定义类
## 1. Java类库中的LocalDate类
```java
Local Date.now(); //使用类中的静态工厂方法
LocalDate.of(1999, 12, 31); //提供年、 月和日来构造对应一个特定日期的对象：

LocalDate newYearsEve = Local Date.of(1999, 12, 31); //将构造的对象保存在一个对象变量中供再次使用。
LocalDate aThousandDaysLater = newYearsEve.plusDays(1000); //得到距当前对象指定天数的一个新日期对象。

LocalDate minusDays(int n) //生成当前日期之后或之前 n 天的日期。

int getYear( ) 
int getMonthValue( ) 
int getDayOfMonth( ) //得到当前日期的年、 月和日。

DayOfWeek getDayOfWeek; //得到当前日期是星期几， 作为 DayOfWeek 类的一个实例返回。 调用 getValue 来得到1 ~ 7 之间的一个数， 表示这是星期几， 1 表示星期一， 7 表示星期日。
```
## 2. CregorianCalendar类
```java
CregorianCalendar someDay = new CregorianCalendar(1999, 11, 31);
someDay.add(Calendar.DAY_0F_M0NTH, 1000);  //更改器方法
year = someDay.get(Calendar.YEAR); //访问器方法
month = someDay.get(Calendar.MONTH)+ 1; 
day = someDay.get(Ca1endar.DAY_0F_M0NTH);   
```

# 三、用户自定义类
```java
class Employee
{
  //私有数据域
  private String name;
  private double salary;
  private Local Date hireDay;

//构造器与类同名
//构造器总是伴随着 new 操作符的执行被调用，而不能对一个已经存在的对象调用构造器来达到重新设置实例域的目的，否则会产生编译错误。
//每个类可以有一个以上的构造器
//构造器可以有 0 个、1 个或多个参数，没有返回值
  public Employee (String n , double s, int year, int month , int day)  
 {
    name = n;
    salary = s;
    hireDay = Local Date,of(year, month, day);   //注意， 不要在构造器中定义与实例域重名的局部变量。否则实例域会被覆盖从而使构造器失效。
 }

//域访问器
public String getName()
{ 
    return name;
}

public double getSalary()
{
    return salary;
}

public Local Date getHireDay() //注意不要编写返回引用可变对象的访问器方法。否则会破坏封装性，由第三方改变类中的私有状态。该方法就违反了这个设计原则
{                               //如果需要返回一个可变对象的引用， 应该首先对它进行克隆（clone )。
    //return hireDay;
    return (Date)hireDay.clone();
}

//域更改器
public void raiseSalary(double byPercent)   //raiseSalary 方法有两个参数。 第一个参数称为隐式参数， 是出现在方法名前的Employee 类对象。第二个参数位于方法名后面括号中的数值，这是一个显式参数。
 {
 double raise = this.salary * byPercent / 100;  //关键字 this 表示隐式参数
 salary += raise;
 }
}

```
## 1. 多个源文件的使用
在开发中习惯于将每一个类存在一个单独的源文件中，这时既可以显式编译也可以隐式编译。当 Java 编译器发现 EmployeeTest.java 使用 Employee 类时会查找名为 Employee.class 的文件。如果没有找到这个文件， 就会自动地搜索 Employee.java, 然后，对它进行编译。如果 Employee,java 版本较已有的 Employee.class 文件版本新，Java 编译器就会自动地重新编译这个文件。

## 2. 封装
封装一个类一般提供下面三项内容：
1. 私有的数据域；
2. 公有的域访问器方法；
3. 一个公有的域更改器方法。

## 3. 基于类的访问权限
<font color=Crmison>类中的方法可以访问所属类对象的私有特性, 而不仅限于访问隐式参数的私有特性。</font>
```java
class Employee
{
    ...
    public boolean equals(Employee other) {
        return name.equals(other.name);   
    } 
}
```

## 4. 私有方法
绝大多数方法都被设计为公有的，但在某些特殊情况下，也可能将它们设计为私有的。有些辅助方法不应该成为公有接口的一部分，这是由于它们往往与当前的实现机制非常紧密， 或者需要一个特别的协议以及一个特别的调用次序。在 Java 中，为了实现一个私有的方法， 只需将关键字 public 改为 private 即可。只要方法是私有的，类的设计者就可以确信：它不会被外部的其他类操作调用，可以将其删去。如果方法是公有的， 就不能将其删去，因为其他的代码很可能依赖它。

## 5. final 实例域
```java
class Employee
{
    //final 修饰符大都应用于基本 （primitive ) 类型域，或不可变（immutable) 类的域（如果类中的每个方法都不会改变其对象， 这种类就是不可变的类）。
    private final String name; //必须确保在每一个构造器执行之后，这个域的值被设置， 并且在后面的操作中， 不能够再对它进行修改。

    //对于可变的类， 使用 final 修饰符可能会对读者造成混乱
    //final 关键字只是表示存储在 evaluations 变量中的对象引用不会再指示其他 StringBuilder对象。不过这个对象可以更改。
    private final StringBuiIcier evaluations;  

}
```

# 四、静态域与静态方法
## 1. 静态域
```java
class Employee
{
    private static int nextld = 1;  //如果将域定义为 static, 每个类中只有一个这样的域。而每一个对象对于所有的实例域却都有自己的一份拷贝。
    private int id; 
}
```

## 2. 静态常量
静态变量使用得比较少，但静态常量却使用得比较多。静态常量可以被设置为public，因为final常量不允许被修改。
```java
public class Math
{
    public static final double PI = 3.14159265358979323846; 
}

public class System
{
    public static final PrintStream out = . . .; 
}
```

## 3. 静态方法
使用静态方法的两种情况：
1. 一 方法不需要访问对象状态，其所需参数都是通过显式参数提供（例如：Math.pow)。
2. 一个方法只需要访问类的静态域。

```java
//静态方法不能访问实例域， 因为它不能操作对象。但是，静态方法可以访问自身类中的静态域
//可以认为静态方法是没有 this 参数的方法。
//可以使用对象调用静态方法但没必要。因为静态方法与对象实例域毫无关系。
public static int getNextId()
{
    return nextId; // returns static field  
}

//使用类名调用该方法
int n = Employee.getNextld();
```
><font color=Crmison>如果一个域是静态的基本数据类型域，且也没有对它进行初始化，那么它就会获得基本类型的标准初值；如果它是一个对象引用，那么它的默认初始化的值就是null。static关键字不能应用于局部变量，因此它只能作用于域。</font>

## 4. 工厂方法
工厂方法是静态方法的另一种常见用途，类似 LocalDate 和 NumberFormat 的类使用静态工厂方法来构造对象。
```java
NumberFormat currencyFormatter = NumberFormat.getCurrencylnstance();
NumberFormat percentFormatter = NumberFormat.getPercentlnstance()；
double x = 0.1;
System.out.println(currencyFormatter.format(x)); // prints $O.10
System.out.println(percentFomatter.format(x)); // prints 10%
```
使用工厂方法的两个原因：
1. 无法命名构造器。构造器的名字必须与类名相同。但是， 这里希望将得到的货币实例和百分比实例采用不用的名字。
2. 当使用构造器时，无法改变所构造的对象类型。而 Factory 方法将返回一个 DecimalFormat类对象，这是 NumberFormat 的子类。

## 5. main方法
1. main方法也是一种静态方法，不能操作所在类的实例域。静态的main 方法将执行并创建程序所需要的对象。
2. 每一个类可以有一个 main 方法。这是一个常用于对类进行单元测试的技巧。可独立的对类进行测试。

# 五、方法参数
1. **按值调用**：表示方法接收的是调用者提供的值，即形参为实参的一个拷贝，对形参的修改不能改变实参的值。
2. **按引用调用**：表示方法接收的是调用者提供的变量地址。
3. Java 总是采用<font color=Crmison>按值调用</font>，方法不能修改传递给它的任何参数变量的内容。

```java
public static void tripieValue(double x) // doesn't work
{ 
    x = 3 * x;      
}
double percent = 10;  
tripieValue(percent);    //一个方法不能修改一个基本数据类型的参数（即数值型或布尔型）。

public static void tripleSalary (Employee x) // works
{ 
    x.raiseSa1ary(200);  
}
harry = new Employee(. . .);
tripleSalary(harry);     //一个方法可以改变一个对象参数的状态。

public static void swap(Employee x , Employee y) // doesn't work
{
    Employee temp = x; 
    x = y; 
    y = temp;        //一个方法不能让对象参数引用一个新的对象。
}
```

# 六、对象构造
## 1. 重载
1. **方法签名**：要完整地描述一个方法，需要指出方法名以及参数类型。这叫做方法的签名。返回类型不是方法签名的一部分。
2. **重载**：多个方法有相同的名字、 不同的参数，便产生了重载。不能有两个名字相同、 参数类型也相同却返回不同类型值的方法。
3. **重载解析**:通过用各个方法给出的参数类型与特定方法调用所使用的值类型进行匹配来挑选出相应的方法。如果编译器找不到匹配的参数， 就会产生编译时错误。

## 2. 默认域初始化
1. 如果在构造器中没有显式地给域赋予初值，那么就会被自动地赋为默认值： 数值为 0、布尔值为 false、 对象引用为 null。
2. <font color=Crmison>域与局部变量不同，</font>必须明确地初始化方法中的局部变量。

## 3. 无参数的构造器
1. 如果在编写一个类时没有编写构造器， 那么系统就会提供一个无参数构造器。这个构造器将所有的实例域设置为默认值。
2. 如果类中提供了至少一个构造器， 但是没有提供无参数的构造器， 则在构造对象时如果没有提供参数就会被视为不合法。此时必须显式提供一个不带参数的构造器才可以将所有的实例域设置为默认值。

## 4. 显式域初始化
```java
class Employee{
    //初始值为常量值，用在当一个类的所有构造器都希望把相同的值赋予某个特定的实例域时。
    //在执行构造器之前，先执行赋值操作。
    private String name ="";  
    
    //初始值不一定是常量值
    private static int nextId;  //静态域在类加载时即被加载且未被赋初值的会被赋予默认值。
    private int id = assignId();

    private static int assignId()
    {
        int r = nextId;
        nextId++;
        return r;
    }

    //使用初始化块
    //在一个类的声明中，可以包含多个代码块
    //首先运行初始化块，然后才运行构造器的主体部分。
    //不常见
    {
        id = nextld;
        nextld++;
    }


    //在类第一次加载的时候， 将会进行静态域的初始化
    //所有的静态初始化语句以及静态初始化块都将依照类定义的顺序执行。
    static
    {
        Random generator = new Random();
        nextld = generator.nextInt(lOOOO); 
    }

    //从 Java SE 7 以后，java 程序首先会检查是否有一个 main 方法。
    static
    {
        System.out.println("Hel1o, World"); 
    }
}
```
域初始化的顺序很复杂，调用构造器的具体处理步骤：
1. 初始化父类中的静态成员变量和静态代码块 ； 
2. 初始化子类中的静态成员变量和静态代码块 ； 
3. 初始化父类的普通成员变量和代码块，再执行父类的构造方法；
4. 初始化子类的普通成员变量和代码块，再执行子类的构造方法；

## 5. 参数名
```java
public Employee(String aNaie, double aSalary) {
    name = aName ;
    salary = aSalary; 
}

public Employee(String naie, double salary) {
    this.name = name;
    this,sal ary = salary; 
}
```

## 6. 利用this调用同一个类中的另一个构造器
```java
//可有效减少代码重复
public Employee(double s) { // calls Employee(String, double)
    this("Employee #" + nextld, s);
    nextld++; 
}
```

# 七、包
使用包的主要原因是确保类名的唯一性，而不会产生冲突。
## 1. 类的导入
一个类可以使用所属包中的所有类， 以及其他包中的公有类。
```java
java.time.LocalDate today = java.tine.LocalDate.now();  //繁琐

//import 语句应该位于源文件的顶部(但位于 package 语句的后面)。
//只能使用星号（*) 导入一个包， 而不能使用 import java.* 或import java.*.* 导入以 java 为前缀的所有包。
import java.util.*;
LocalDate today = Local Date.now();

//此时若在程序直接使用 Date 类的时候， 就会出现一个编译错误，因为二者均包含Date类
import java.util.*;
import java.sql.*;

//可明确指出使用哪个包中的Date
import java.util.*;
import java.sql.*;
import java.util.Date;

//若两个Date均需要使用应添加上完整包名
java.util.Date deadline = new java.util.Date();
java.sql.Date today = new java.sql.Date(...);
```
## 2. 静态导入
```java
import static java.lang.System.*;
out.println("Goodbye, World!"); // System.out
exit(0); //System.exit
```

## 3. 将类放入包中
如果没有在源文件中放置 package 语句， 这个源文件中的类就被放置在一个默认包(没有名字)中。
![包目录](包目录.png)
```java
package com.horstmann.corejava;
public class Employee
{

}

//编译
javac PackageTest.java
javac com/horstmann/corejava/Employee.java
```
<font color=Crmison>编译器在编译源文件的时候不检查目录结构。</font>如果源文件不在指定package中且不依赖于其他包， 就不会出现编译错误。但是， 最终的程序将无法运行，因为虚拟机找不到类。

## 4. 包作用域
1. 标记为 public 的部分可以被任意的类使用；
2. 标记为 private 的部分只能被定义它们的类使用。
3. 如果没有指定 public 或 private , 这个部分（类、方法或变量）可以被同一个包中的所有方法访问。

# 八、类路径
类路径是所有包含类文件的路径的集合。
类路径包含三种情况：
1. 基目录 /home/user/classdir 或 c:\classes
2. 当前目录 (.); 
3. JAR 文件 /home/user/archives/archive.jar 或c:\archives\archive.jar

类路径所列出的目录和归档文件是搜寻类的起始点。

## 1. 虚拟机搜寻类文件的过程
1. 首先要查看存储在 jre/lib 和jre/lib/ext 目录下的归档文件中所存放的系统类文件。
2. 若找不到相应类文件，再查看类路径。

>/home/user/classdir/com/horstmann/corejava/Employee.class
com/horstmann/corejava/Employee.class 从当前目录开始
com/horstmann/corejava/Employee.class inside /home/user/archives/archive.jar

## 2. 编译器搜寻类文件的过程
1. 如果引用了一个类，而没有指出这个类所在的包， 那么编译器将首先查找包含这个类的包，并询查所有的 import 指令，确定其中是否包含了被引用的类。
2. 如果找到了一个以上的类， 就会产生编译错误。
3. 编译器还要查看源文件是否比类文件新，如果是这样的话，那么源文件就会被自动地重新编译。

## 3. 设置类路径
```java
//采用 -classpath (或 -cp) 选项指定类路径
java -classpath /home/user/dassdir: .:/home/user/archives/archive.jar HyProg
java -classpath c:\classdir; .;c:\archives\archive.jar MyProg

//设置 CLASSPATH 环境变量
export CLASSPATH=/home/user/classdir:.:/ home/user/archives/archive.jar  //Bourne Again shell ( bash)
set CLASSPATH=c:\classdir;.;c:\archives\archive.jar //Windows shell
```

# 九、文档注释
利用JDK中的javadoc命令工具，可以由源文件生成一个 HTML 注释(/** */)文档。文档注释与源代码在同一个文件中，在修改源代码的同时， 重新运行 javadoc 就可以轻而易举地保持两者的一致性。

## 1. 注释的插入
javadoc抽取信息生成文档的位置如下，也应当在这些位置编写注释：
1. 包 
2. 公有类与接口
3. 公有的和受保护的构造器及方法
4. 公有的和受保护的域

文档注释的格式：
```java
/**
* 自由格式文本：标记如@author或 @param等
* 第一句应该是一个概要性的句子。javadoc 实用程序自动地将这些句子抽取出来形成概要页。
* 在自由格式文本中，可以使用 HTML 修饰符，如<em></em>等
*/
```

## 2. 类注释
类注释必须放在 import 语句之后，类定义之前。

## 3. 方法注释
每一个方法注释必须放在所描述的方法之前。可以使用以下标记：
`@param`:对当前方法的“ param” （参数）部分添加一个条目。这个描述可以占据多行， 并可以使用 HTML 标记。一个方法的所有 @param 标记必须放在一起。
`@return`:这个标记将对当前方法添加“ return” （返回）部分。这个描述可以跨越多行， 并可以使用 HTML 标记。
`©throws`:这个标记将添加一个注释， 用于表示这个方法有可能抛出异常。

## 4. 域注释
只需要对公有域（通常指的是静态常量）建立文档。

## 5. 通用注释
用于类文档的注释中。
```java
/**
@author 姓名
@version 文本
@since 文本
@deprecated 文本
@see 引用   @see com.horstraann.corejava.Employee#raiseSalary(double) 
            @see <a href="m«w.horstmann . com/corejava. htinl ">The Core ]ava home page</a>
            Isee "Core Java 2 volume 2n
{@link package.class#feature label }
*/
```

## 6. 包与概述注释
要想产生包注释，就需要在每一个包目录中添加一个单独的文件。有两种方式添加：
1. 提供一个以 package.html 命名的 HTML 文件。在标记 &lt;body&gt;—&lt;/body&gt; 之间的所有文本都会被抽取出来。
2. 提供一个以 package-info.java 命名的 Java 文件。这个文件必须包含一个初始的以 /** 和 */ 界定的 Javadoc 注释， 跟随在一个包语句之后。它不应该包含更多的代码或注释。

还可以为所有的源文件提供一个概述性的注释。名为overview,html 的文件中，这个文件位于包含所有源文件的父目录中。标记&lt;body&gt;—&lt;/body&gt;间的所有文本将被抽取出来。

## 7. 注释的抽取
详见http://docs.oracle.com/javase/8/docs/guides/javadoc
```java
//docDirectory为HTML文件的存放目录
1. 切换到包含想要生成文档的源文件目录。
2. javadoc -d docDirectory nameOfPackage   //如果省略了 -d docDirectory 选项， 那 HTML 文件就会被提取到当前目录下。
3. javadoc -d docDirectory nameOfPackage1 nameOfPackage2 . . .
4. javadoc -d docDirectory *.java  //文件在默认包中

//可以使用 -author 和 -version 选项在文档中包含@author 和@version 标记（默认情况下，这些标记会被省略)。

//使用-link为标准类添加超链接
javadoc -link http://docs.oracle.eom/:javase/8/docs/api *.java  //所有的标准类库类都会自动地链接到 Oracle 网站的文档。

//使用 -linksource 选项，则每个源文件被转换为 HTML (不对代码着色，但包含行编号) 并且每个类和方法名将转变为指向源代码的超链接。
```

# 十、类设计技巧
1. 一定要保证数据私有
2. 一定要对数据初始化
3. 不要在类中使用过多的基本类型
4. 不是所有的域都需要独立的域访问器和域更改器
5. 将职责过多的类进行分解
6. 类名和方法名要能够体现它们的职责
7. 优先使用不可变的类