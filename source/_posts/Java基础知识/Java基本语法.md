---
title: Java基本语法
tags: [Java]
categories: Java
top: true
date: 2020-4-28 12:05:33
---

# 一、Java应用程序的结构
## 1. 程序的组成
```java
public class FirstSample
{
    public static void main(String[] args) {  //要运行java程序，必须有一个main方法，public和static的位置可以互换，但其它部分位置固定
    System.out.println("We will not use 'Hello, World!"');
    } 
}
```
- java区分大小写；
- public关键字为访问修饰符，用于控制程序的其他部分对这段代码的访问级別；
- 关键字 class 表明 Java 程序中的全部内容都包含在类中；
- class 后面紧跟类名，名字必须以字母开头，后面可以跟字母和数字的任意组合，采用驼峰命名法，不能使用java保留字；
- <font color=Crmison>源代码的文件名必须与public类的名字相同，并用 .java 作为扩展名。源文件可以包含多个类定义，但最多只能包含一个public类定义。可以没有public类定义，此时文件名可随意。</font>
- <font color=Crmison>成功编译后会得到一个包含这个类字节码的文件。Java 编译器将字节码文件自动地命名为 ClassName. class, 并与源文件存储在同一个目录下。</font>
- 运行已编译的程序时，Java 虚拟机将从指定类中的 main 方法开始执行,故<font color=Crmison>在类的源文件中必须包含一个 main方法。</font>
- <font color-Crmison>在 Java SE 1.4 及以后的版本中<font color=Crmison>强制 main方法是 public 的。</font>
- Java 中的 <font color=Crmison>main 方法必须是静态static的。</font>
- 在 Java 中，每个句子必须用分号结束。特别需要说明，回车不是语句的结束标志，因此，如果需要可以将一条语句写在多行上。

## 2. 注释
```java
//单行注释

/*比较长的一段话，注意不能嵌套*/

/**
*多行注释，可自动生成文档。
*
*
*/
```

# 二、8种基本数据类型
4种整型，2种浮点型，1种用于表示 Unicode 编码的字符单元的字符类型 char和 1 种用于表示真值的 boolean 类型。
## 1. 整型
![Java整型](Java整型.png)
- <font color=Crmison>int：</font>10<sup>9</sup>以内；
- <font color=Crmison>long：</font>10<sup>10</sup>——10<sup>18</sup>，数值有一个后缀L或l；
- 二进制从Java7开始，要加上前缀0b或0B；八进制加前缀0(容易混淆数值，不推荐)；十六进制加前缀0x或0X；
- 从Java7开始还可以为数字字面量加下划线让程序更易读，Java编译器会去除这些下划线。如1_000_000或0b1111_0100_0010_0200_0000。
- <font color=Crmison>Java中所有的数值类型所占据的字节数量与平台无关</font>，而C和C++会随着平台改变而改变。
- <font color=Crmison>注意!Java 没有任何无符号（unsigned) 形式的 int、 long、short 或 byte 类型。</font>

## 2. 浮点型
### 2.1 两种浮点类型
![浮点型](浮点型.png)
- float 类型的数值有一个后缀 F 或 f (例如，3.14F。) 没有后缀 F 的浮点数值（如 3.14 ) 默认为 double 类型。
- 也可以在浮点数值后面添加后缀 D 或 d (例如，3.14D)。
- 绝大部分应用程序都采用 double 类型。在很多情况下，float 类型的精度很难满足需求。
- <font color=Crmison>可以使用十六进制表示浮点数值。</font>例如，0.125=2<sup>—3</sup>可以表示成 0xl.0p-3。在十六进制表示法中， 使用 p 表示指数， 而不是 e。 注意， 尾数采用十六进制，指数采用十进制。指数的基数是 2。

### 2.2 浮点型的计算
- 所有的浮点数值计算都遵循 IEEE 754 规范。
- 三个特殊浮点值表示溢出和出错情况：
    <font color=Crmison>正无穷大(常量 `Double.POSITIVE_INFINITY`或`Flocat.POSITIVE_INFINITY`)</font>
    <font color=Crmison>负无穷大(`Double.NEGATIVE_INFINITY`或`Float.NEGATIVE_INFINITY`)</font>
    <font color=Crmison>NaN(非数值，`Double.NaN`或`Float.NaN`):注意！所有非数值都认为是不相同的。</font>
    ```java
    if (x = Double.NaN) // is never true 错误判断方法
    if (Double.isNaN(x)) // check whether x is "not a number" 正确判断方法
    ```
- 浮点数值采用二进制系统表示，故无法精确表示1/10，如果在数值计算中不允许有任何舍入误差，就应该使用 BigDecimal类

## 3. Unicode编码与字符编码表
设计 Unicode 编码的目的是将世界不同地区的不同编码标准如美国的 ASCII、 西欧语言中的ISO 8859-1 俄罗斯的 KOI-8、 中国的 GB 18030 和 BIG-5 等统一起来，起初Unicode是固定的16位，而经过一段时间的发展，Unicode 字符超过了 65 536 个，其主要原因是增加了大量的汉语、 日语和韩语中的表意文字。为了解决这个问题，设计了不同的变长编码方式，如UTF-8和UTF-16，要理解这两种编码方式，必须首先理解Unicode码点等含义。
<font color=Crmison>码点：</font>是指与一个编码表中的某个字符对应的代码值。在 Unicode 标准中，码点采用 <font color=Crmison>U+十六进制</font> 书写,Unicode 的码点可以分成 17 个代码级别，从U+10000 到 U+10FFFF。
<font color=Crmison>基本的多语言级别：</font>码点从 U+0000 到 U+FFFF, 其中包括经典的 Unicode 代码；
<font color=Crmison>辅助字符级别：</font>其余的 16个级别的码点从 U+10000 到 U+10FFFF , 其中包括一些辅助字符。

### 3.1 UTF-16字符编码表
UTF-16 编码采用不同长度(2字节和4字节)的编码表示所有 Unicode 码点。在基本的多语言级别中，每个字符用 16 位表示，通常被称为<font color=Crmison>代码单元</font>; 而辅助字符采用一对连续的代码单元进行编码。这样构成的编码值落入基本的多语言级别中空闲的 2048 字节内， 通常被称为替代区域，其中 U+D800 ~ U+DBFF 用于第一个代码单元，U+DC00 ~ U+DFFF 用于第二个代码单元，而这些替代区域Unicode规定不对应与任何字符。这样设计十分巧妙，我们可以从中迅速地知道一个代码单元是一个字符的编码，还是一个辅助字符的第一或第二部分。参见https://zh.wikipedia.org/wiki/UTF-16#从U+0000至U+D7FF以及从U+E000至U+FFFF的码位
<font color=Crmison>UTF-16编码算法：</font>
- 码位减去 0x10000，得到的值的范围为20比特长的 0...0xFFFFF。
- 高位的10比特的值（值的范围为 0...0x3FF）被加上 0xD800 得到第一个码元或称作高位代理，值的范围是 0xD800...0xDBFF。由于高位代理比低位代理的值要小，所以为了避免混淆使用，Unicode标准现在称高位代理为前导代理。
- 低位的10比特的值（值的范围也是 0...0x3FF）被加上 0xDC00 得到第二个码元或称作低位代理，现在值的范围是 0xDC00...0xDFFF。由于低位代理比高位代理的值要大，所以为了避免混淆使用，Unicode标准现在称低位代理为后尾代理。

<font color=Crmison>UTF-16编码示例：</font>
![UTF-16编码示例](UTF-16.png)

### 3.2 UTF-8字符编码表
UTF-8编码也采用不同字节编码所有Unicode码点(1字节-6字节)参见https://zhuanlan.zhihu.com/p/72254734
<font color=Crmison>UTF-16编码规则表：</font>
![UTF-8编码规则表](UTF-8.png)
<font color=Crmison>UTF-16编码示例：</font>
汉字「吕」的 Unicode 编码是 U+5415，对应二进制为 0101010000010101，总共有 15 位。因为两字节最多表示 11 位，三字节最多表示 16 位，所以使用三字节编码。对应二进制拆成（从低位到高位）三部分，分别是 0101, 010000, 010101，再拼上编码前缀得到 11100101, 10010000, 10010101，对应十六进制为 0xe5, 0x90, 0x95，这就是汉字「吕」的 UTF-8 编码。

## 4. char类型
- char 数据类型是一个采用 UTF-16 编码表示 Unicode 码点的代码单元。
- char 类型的字面量值要用单引号括起来。
- char 类型的值可以用转义序列\u和十六进制值表示，其范围从 \u0000 到 \uffff。
- 除了转义序列 \u 之外， 还有一些用于表示特殊字符的转义序列：
![转义序列](转义序列.png)
- 所有这些转义序列都可以出现在加引号的字符字面量或字符串中。例如，’\02122' 或 "Hello\n”。转义序列 \u还可以出现在加引号的字符常量或字符串之外（<font color=Crmison>而其他所有转义序列不可以</font>。
- <font color=Crmison>Unicode 转义序列会在解析代码之前得到处理。</font>
```java
public static void main(String\u005B\ u00SD args);  //\u005B 和 \u005D 是 [ 和 ] 的编码。
"\u0022+\u0022";   //""+""  即空串
// \u00A0 is a newline   
//\u00A0为换行符，故会产生一个语法错误
// Look inside c:\users   
//也会产生一个语法错误， 因为 \u 后面并未跟着 4 个十六进制数。 
```
## 5. boolean类型
boolean (布尔）类型有两个值：false 和 true, 用来判定逻辑条件。<font color=Crmison>要注意！整型值和布尔值之间不能进行相互转换。</font>

# 三、变量
## 1. 变量的声明
1. 变量名必须是一个以字母开头并由字母或数字构成的序列。
2. 需要注意，与大多数程序设计语言相比，Java 中“ 字母” 和“ 数字” 的范围更大。字母包括 'A' ~ 'Z'、 'a' ~ 'z'、或在某种语言中表示字母的任何 Unicode 字符。
3. 但 V 和 '©’这样的符号不能出现在变量名中，空格也不行。可以使用 Character 类的`isJavaldentifierStart` 和 `isJavaldentifierPart` 方法来检查哪些 Unicode 字符属于 Java 中的“ 字母”。
4. 另外， 不能使用 Java 保留字作为变量名。

```java
int i,j; //不提倡
int a;
int b; //提倡，可读性强
Box box;
Box aBox;
```

## 2. 变量的初始化
```java
int vacationDays;  //声明一个变量之后，必须用赋值语句对变量进行显式初始化,否则不能使用
vacationDays=12;  //可以将变量的声明和初始化放在同一行中。

//在 Java 中可以将声明放在代码中的任何地方,但应尽可能地靠近变量第一次使用的地方。
int vacationDays = 12;       //在 Java 中， 不区分变量的声明与定义。

```
## 3. 常量
```java
final double CM_PER_INCH = 2.54; //关键字 final 表示这个变量只能被赋值一次。一旦被赋值之后，就不能够再更改了。习惯上,常量名使用全大写。

//类常量的定义位于 main 方法的外部。
public static final double CM_PER_INCH = 2.54; //使用关键字 static final 可设置一个类常量,可使其在一个类中的多个方法中使用
                                            //如果一个常量被声明为 public，那么其他类的方法也可以使用这个常量，ClassName.常量名。
```
<font color=Crmison>const 是 Java 保留的关键字，但目前并没有使用。</font>

# 四、运算符
`+ - * / %`加、减、乘、除、取余数
1. <font color=Crmison>需要注意， 整数被 0 除将会产生一个异常， 而浮点数被 0 除将会得到无穷大或 NaN 结果。</font>
2. 在默认情况下， 虚拟机设计者允许对中间计算结果采用扩展的精度。但是， 对于使用 `strictfp` 关键字标记的方法必须使用严格的浮点计算来生成可再生的结果。
3. 如果将一个类标记为strictfp, 这个类中的所有方法都要使用严格的浮点计算。
```java
public static strictfp void main(String[] args)
```

## 1. 数学函数与数学常量
```java
import static java.lang.Math.*;  //静态导入Math包后，就不需要使用 Math.方法名 来调用了

//解决负整数取余数问题的方法,n%2=-1，若n为负整数
floorMod(position+adjustment,12);  //（对于负除数，floorMod 会得到负数结果，不过这种情况在实际中很少出现。

//三角函数方法
static double  sin(double a ) ： 返回角的三角正弦
static  double cos(double a)  ： 返回角的三角余弦
static  double tan(double  a)  ： 返回角的三角正切
static  double asin(double a) : 返回角的反正弦
static  double acos(double a)  : 返回角的反余弦
static  double atan(double a)  ： 返回角的反正切
static  double toRadians(double a) : 将角转换为弧度
static  doueble toDegrees(double a) : 将弧度转化为角

//指数函数方法
static  double exp(double a) : 用于获取e的a次方；
static  double log(double a) : 即lna;
static  double log10(double a) : 即log10a;
static  double sqrt(double a ):用于取a的平方根；
static  double cbrt(double a) : 用于取a的立方根；
static  double pow(double a, double b) : 用于求a的b次方；

//取整函数方法
static double ceil(double a)：返回大于等于a的整数值，返回值类型为double；
static double floor(double a) : 返回小于等于a的整数值，返回值类型为double；
static double rint(double a) : 返回与a最接近的整数值，返回值类型为double；（如果两个同为整数且同样接近，选取偶数值的那个）
static double random( ):返回带正号的 double 值，该值大于等于 0.0 且小于 1.0。
//四舍五入函数
static long round(double a ): 其值等于Math.floor(a + 0.5)，返回值类型为long;
static int round(float a ): 其值等于Math.floor(a + 0.5)，返回值类型为int;

//求绝对值运算和最值运算，这里的类型就是double，float，int和long类型
static 类型 abs(类型); 返回对应类型的绝对值
static 类型 max(类型1,类型2); 返回对应类型的最大值
static 类型 min(类型1,类型2); 返回对应类型的最小值
```

## 2. 数值类型之间的合法自动转换
![类型转换](类型转换.png)
其中6个实心箭头，表示无信息丢失的转换；有 3 个虚箭头，表示可能有精度损失的转换。当进行二元操作时，会将两个操作数自动转换为同一种类型，再进行计算，转换规则为：
- 如果两个操作数中有一个是 double 类型， 另一个操作数就会转换为 double 类型。
- 否则，如果其中一个操作数是 float 类型，另一个操作数将会转换为 float 类型。
- 否则， 如果其中一个操作数是 long 类型， 另一个操作数将会转换为 long 类型。 
- 否则， 两个操作数都将被转换为 int 类型。

## 3. 强制类型转换cast
当存在信息丢失的可能时，需要进行强制类型转换才可以，比如long--->int,强制类型转换会进行截断。
```java
(目标类型)变量; //不要在 boolean 类型与任何数值类型之间进行强制类型转换， 这样可以防止发生错误。
b?1:0;   //极少情况下使用
```

## 4. 结合赋值运算符
`+= -= *= /= %=`
`%`:<font color=Crmison>取模运算，结果的符号和被除数符号一致</font>
```java
int x;
x += 3.5; //等价 (int)(x+3.5) 运算符得到一个值， 其类型与左侧操作数的类型不同，就会发生强制类型转换。
```

## 5. 自增与自减运算符
```java
int n=12;
n++;    //由于这些运算符会改变变量的值，所以它们的操作数不能是数值。
n--     //不推荐使用++,容易产生bug。
++n;
--n;
```

## 6. 关系运算符和boolean运算符
- 关系运算符：
`== != < > <= >=`
- boolean运算符:
`&& || ! ?:`

## 7. 位运算符
`& | ^(异或) ~ <<(左移) >>(右移)`
1. 利用 & 并结合使用适当的 2 的幂， 可以把其他位掩掉， 而只保留其中的某一位。
2. <font color=Crmison>`>>>` 运算符会用 0 填充高位，这与`>>`不同，它会用符号位填充高位。不存在`<<<`运算符。</font>
3. <font color=Crmison>移位运算符的右操作数要完成模 32 的运算（除非左操作数是 long 类型， 在这种情况下需要对右操作数模 64 )。</font>
4. C/C++中`>>`对于负数生成的结果可能会依赖于具体的实现。Java 则消除了这种不确定性。

```java
(n & (1 << 3)) >> 3;

1<<35;  //等价8或者1<<3
```

## 8. 括号与运算符的优先级
![运算符优先级1](运算符优先级1.png)
![运算符优先级2](运算符优先级2.png)

## 9. 枚举类型
用于变量的取值只在一个有限的集合内的情况。
```java
enum Size { SMALL, MEDIUM, LARGE, EXTRA.LARCE }; //定义枚举类型
Size s = Size.MEDIUM; //声明变量，只能存储Size中给定的枚举值或null值，null 表示这个变量没有设置任何值。
```

# 五、字符串
>Java 字符串就是 Unicode 字符序列。
Java 没有内置的字符串类型， 而是在标准 Java 类库中提供了一个预定义类，很自然地叫做 String。
每个用双引号括起来的字符串都是 String类的一个实例。
<font color=Crmison>String 类对象为不可变字符串，不能修改字符串中的任一字符，但是可以修改整个字符串变量，让它引用另外一个字符串。</font>
字符串存放在一个公共存储池(堆)中，字符串变量指向存储池中相应的位置。如果复制一个字符串变量， 原始字符串与复制的字符串共享相同的字符。即：编译器可以让字符串共享。
<font color=Crmison>Java 将自动地进行垃圾回收。 如果修改字符串变量后之前的字符串所占的一块内存不再使用了， 系统最终会将其回收。</font>

## 1. String中的各种字符串操作
```java
String greeting = "Hello";
String expletive = "Expletive";

//子串 substring方法
String s = greeting.substring(0, 3); //"Hel"

//拼接 +
String message = expletive + greeting; //当将一个字符串与一个非字符串的值进行拼接时，后者被转换成字符串（任何一个 Java 对象都可以转换成字符串）。
nt age = 13;
String rating = "PC" + age;   //PG13
System.out.println("The answer is " + answer);

//join方法：把多个字符串放在一起， 用一个定界符分隔
String all = String.join(" / ", "S", "M", "L", "XL");  //"S / H / L / XL"

//修改字符串变量，达到修改字符的效果
greeting = greeting.substring(0, 3) + "p!"; //共享享带来的高效率远远胜过于提取、 拼接字符串所带来的低效率。因为对字符串进行的操作往往只是比较。

//检测字符串是否相等
s.equals(t); 
"Hello".equals(greeting);
"Hello".equalsIgnoreCase("hel1o");   //判断是否相等而不区分大小写
if (greeting.compareTo("Hel1o") == 0)

//检测字符串是否放置在同一个位置上
greeting == "Hello";  //可能正确也可能错误，因为实际上只有字符串常量是共享的，而 + 或 substring 等操作产生的结果并不是共享的。

int n = greeting.length(); //返回采用 UTF-16 编码表示的给定字符串所需要的代码单元数量。
int cpCount = greeting.codePointCount(0, greeting.length()); //得到实际的长度，即码点数量

s.charAt(n); //返回位置 n 的代码单元

int index = greeting.offsetByCodePoints(0, i);   //得到第 i 个码点
int cp = greeting.codePointAt(index);  

//遍历字符串的码点
int[] codePoints = str.codePointsO.toArray();
//把一个码点数组转换为一个字符串
String str = new String(codePoints, 0, codePoints.length);

//返回一个新字符串。 这个字符串将原始字符串中的大写字母改为小写，或者将原始字符串中的所有小写字母改成了大写字母。
String toLowerCase( )
String toUpperCase( )

//返回一个新字符串。这个字符串将删除了原始字符串头部和尾部的空格。
String trim( )

//返回一个新字符串。这个字符串用 newString 代替原始字符串中所有的 oldString。可以用 String 或 StringBuilder 对象作为 CharSequence 参数。
String replace( CharSequence oldString,CharSequence newString)
```
Java 中的 String类包含了 50 多个方法。详参https://docs.oracle.com/javase/8/docs/api/

## 2. 空串与null串
```java
String e = ""; // 空串,它是长度为0的字符串，有自己的内容(空)
String str=null; //这表示目前没有任何对象与该变量关联

if (str == null)  //检查一个字符串是否为 null

if (str != null && str.length() != 0) //检查一个字符串既不是 null 也不为空串，注意！首先要检查 str 不为 null。因为如果在一个 mill 值上调用方法， 会出现错误。
```

## 3. 使用StringBuilder类构建字符串
```java
StringBuilder builder = new StringBuilder();
builder.append(ch); // appends a single character
bui1der.append(str); // appends a string
String completedString = builder.toString();
```
1. StringBuffer是StringBuilder的前身，其效率稍有些低， 但允许采用<font color=Crmison>多线程的方式</font>执行添加或删除字符的操作。
2. 如果所有字符串在一个<font color=Crmison>单线程中编辑 （通常都是这样)，</font>则应该用 StringBuilder 替代它。

# 六、输入输出
## 1. 读取输入
```java
import java.util.*; //当使用的类不是定义在基本java.lang 包中时，一定要使用import指示字将相应的包加载进来。

//构造一个 Scanner 对象，并与“ 标准输人流” System.in 关联。
Scanner in = new Scanner(System.in);

//使用 Scanner 类的各种方法实现输入
String name = in.nextLine();   //nextLine()可读入空白符
String firstName = in.next();  //以空白符结束
int age = in.nextlnt();   //读取一个整数
double num = nextDouble();  //读取一个浮点数

boolean hasNext( ) //检测输人中是否还有其他单词。

//检测是否还有表示整数或浮点数的下一个字符序列。
boolean hasNextInt()
boolean hasNextDouble()


//用Console类从控制台读取密码
Console cons = System.console();
String username = cons.readLine("User name: ")；
char[] passwd = cons.readPassword("Password:");  //为了安全起见， 返回的密码存放在一维字符数组中， 而不是字符串中。在对密码进行处理之后，应该马上用一个填充值覆盖数组元素

//显示字符串 prompt 并且读取用户输入，直到输入行结束。args 参数可以用来提供输人格式。有关这部分内容将在下一节中介绍。
static char[] readPassword(String prompt, Object...args)
static String readLine(String prompt, Object...args)
```
采用 Console 对象处理输入不如采用 Scanner 方便。每次只能读取一行输入， 而没有能够读取一个单词或一个数值的方法。

## 2. 格式化输出
```java
double x = 10000.0 / 3.0;
System.out.print(x);
System.out.printf("%8.2f", x); //Java SE5.0沿用了C中的printf方法用于对输出进行各种格式控制

//使用静态的 String.format 方法创建一个格式化的字符串， 而不打印输出
String message = String.format("Hello, %s. Next year, you'll be %d", name , age);
```
![printf转换符](printf转换符.png)
还可以在printf中给出控制格式化输出的各种标志:
![printf标志](printf标志.png)
printf方法中日期与时间的格式化选项，格式包括两个字母， 以 t 开始， 以表中的任意字母结束：
```java
System.out.printf("%tc", new Date());  //打印当前完整的日期和时间

//使用格式化的参数索引
System.out.printf("l$s %2$tB %2$te, %2$tY", "Due date:", new DateQ);  //%1$表示对第1个参数格式化， Due date: February 9, 2015
System.out .printf("%s %tB %<te, %<tY", "Due date:", new DateO); //< 示前而格式说明中的参数将被再次使用
```
![日期和时间](日期和时间.png)
![日期和时间](日期和时间1.png)
以上有关printf方法中日期与时间的格式化选项已过时，新代码应使用java.time 包中的方法。
printf格式说明符的语法图：
![printf语法](printf语法.png)
<font color=Crmison>许多格式化规则是本地环境特有的。例如，在德国，组分隔符是句号而不是逗号，Monday 被格式化为 Montag,java可以控制控制应用的国际化行为。</font>

## 3. 文件输入与输出
```java
//若读取文件时不设置字符编码，则会使用运行该java程序的默认编码，若在不同的机器上运行可能会有不同的表现
//可使用Scannner的任意方法对文件进行读取
Scanner in = new Scanner(Paths.get("myfile.txt"), "UTF-8"); //如果文件名中包含反斜杠符号，需要在每个反斜杠之前再加一个额外的反斜杠：“ c:\\mydirectory\\myfile.txt ”

//写入文件
//可以像输出到 System.out—样使用 print、 println 以及 printf命令。
PrintWriter out = new PrintWriter("myfile.txt", "UTF-8");
```
<font color=Crmison>如果用一个不存在的文件构造一个Scanner,或者用一个不能被创建的文件名构造一个PrintWriter,那么就会发生异常。Java 编译器认为这些异常比“被零除”异常更严重。</font>

# 七、控制流程
## 1. 块作用域
1. 块，即复合语句，是由一对大括号括起来的若干条简单的 Java 语句。
2. 一个块可以嵌套在另一个块中。
3. 但是，<font color=Crmison>不能在嵌套的两个块中声明同名的变量，否则无法通过编译。</font>

```java
{
    int n;
    {
        int k;
    }
}
```

## 2. 条件语句
```java
if(condition){...}

if(condition){
    ...
}else{
    ...
}

if(condition1){
    ...
}else if(condition2){
    ...
}else if(condition3){
    ...
}
```
## 3. 循环
```java
while(condition){
    ...
}

do{
    ...
}while(condition);

for (int i = 1; i <= 10; i++){
    ...
}

for (double x = 0; x != 10; x += 0 .1) //由于浮点数的舍入误差，该循环永远不可能结束
```

## 4. switch语句
```java
switch (choice) {   //case标签范围：类型为 char、byte、 short 或 int 的常量表达式。
    case 1:         //枚举常量。
        ...         //从 Java SE 7开始， case 标签还可以是字符串字面量。
        break;
    case 2:
        ...
        break;
    case 3:
        ...
        break;
    case 4:
        ...
        break;
    default:
        ... 
        break; 
}

Size sz = . . .;
switch (sz) {
    case SMALL: // 无需使用Size.SMALL
        ...
        break;
    ...
}
```

## 5. 中断控制流程语句
```java
//不带标签的break语句，用于跳出当前循环
while(c){
    ...
    break;
}

//带标签的break语句，用于跳出多重嵌套的循环语句,也可以应用到任何语句中。
//标签必须放在希望跳出的最外层循环之前， 并且必须紧跟一个冒号。
read_data:
while(...){
    for(...){
        ...
        break read_data;
    }
}

label:
{
    ...
    if(condition) break label;
    ...
}

//continue语句跳过本次循环剩余部分，进入下一次循环
for(...){
    ...
    break;
}
```

# 八、大数值
Biglnteger 和 BigDecimaL 这两个类可以处理包含任意长度数字序列的数值。Biglnteger 类实现了任意精度的整数运算， BigDecimal 实现了任意精度的浮点数运算，类提供的方法参见java文档https://docs.oracle.com/javase/8/docs/api/。
```java
import java.math.*

Biglnteger a = Biglnteger.valueOf(100); //将普通的数值转换为大数值
Biglnteger c = a.add(b); // c = a + b
Biglnteger d = c.multiply(b.add(Biglnteger.valueOf(2))); // d = c * (b + 2) 大数值不能使用 + 和 *
```
<font color=Crmison>与 C++ 不同， Java 没有提供运算符重载功能。 程序员无法重定义 `+` 和 `*` 运算符，虽然为字符串的连接重栽了`+` 运算符，但没有重载其他的运算符。</font>

# 九、数组
## 1. 数组的创建/初始化/匿名数组
```java
int[] a = new int[100];  //也可用int a[];声明数组，但是不推荐，创建一个数字数组则所有元素会被初始化为0
boolean[] b = new boolean[100]; //元素初始化为 false
String[] names = new String[10]; //对象数组的元素则初始化为一个特殊值 null, 这表示这些元素（还）未存放任何对象。

int actualSize = . . .;
Employee[] staff = new Employee[actualSize];   //Java允许在运行时确定数组的大小。

a.length; //获取数组中元素个数

int[] smallPrimes = { 2, 3, 5, 7, 11, 13 }; //创建数组的同时进行初始化
small Primes = new int[] { 17, 19, 23, 29, 31, 37 };  //匿名数组初始化，可以在不创建新变量的情况下重新初始化一个数组
new elementType[0];  //创建一个长度为 0 的数组,与null不同。
```
如果经常需要在运行过程中扩展数组的大小， 应该使用数组列表（array list)。

## 2. 数组的for each循环
```java
for (int element : a)
    System.out.println(element);

System.out.println(Arrays.toString(a));  //以[2,3,5,7,11,13]方式打印数组
```

## 3. 数组拷贝
```java
int[] luckyNumbers = smallPrimes;  //将一个数组变量拷贝给另一个数组变量。这时，两个变量将引用同一个数组
luckyNumbers[5] = 12;   

//如果数组元素是数值型，那么多余的元素将被赋值为 0 ; 
//如果数组元素是布尔型，则将赋值为 false。
//相反，如果长度小于原始数组的长度，则只拷贝最前面的数据元素。
int[] copiedLuckyNumbers = Arrays.copyOf(luckyNumbers, luckyNumbers.length*2); //将一个数组的所有值拷贝到一个新的数组中去,第二个参数为新数组大小，该方法常用来增加数组大小
```
<font color=Crmison>java中没有指针运算，不能通过 a+1 得到数组的下一个元素。[]被预定义为检查数组边界。</font>

## 4. 命令行参数
main函数中的String[] args表明main 方法将接收一个字符串数组， 也就是命令行参数。
```java
public class Message{
    public static void main(String[] args) {
        ...
    }
}

//在命令行输入命令可读入内容到args数组中,程序名并没有存储在 args 数组中
java Message -g cruel world   //args[0]:"-g"  args[l]:"cruel"  args[2]:"cruel" "world"
```

## 4. 数组排序
```java
import java.util.Arrays;

static String toString(type[] a) //类型为 int、long、short、 char、 byte、boolean、float 或 double 的数组。

static type copyOf(type[]a, int length)
static type copyOfRange(type[]a , int start, int end)

static void sort(type[] a) //采用优化的快速排序算法对数组进行排序。

static int binarySearch(type[]a , type v)    //采用二分搜索算法查找值 v
static int binarySearch(type[]a, int start, int end, type v)  //类型为 int、 long、 short、 char、 byte、 boolean 、 float 或 double 的有序数组。

static void fill(type[]a , type v) //将数组的所有数据元素值设置为 V

static boolean equals(type[]a, type[]b) //如果两个数组大小相同， 并且下标相同的元素都对应相等， 返回 true。
```

## 5. 多维数组
```java
//创建
double[][] balances = new double[100][100];
int[][] magicSquare = { {16, 3, 2, 13}， {5, 10, 11, 8}, (9, 6, 7, 12}, {4, 15, 14, 1} };

//遍历
for (double[] row : a)
    for (double value : row)
        .......

//快速打印
System.out.println(Arrays.deepToString(a)); //输出[[16, B, 2, 13], [5, 10, 11, 8], [9, 6, 7, 12], [4, 15, 14, 1]]

//交换二维数组中的两行
double[] temp = balances[i];
balances[i] = balances[i + 1];
balances[i + 1] = temp;
```

## 6. 不规则数组
```java
//首先分配一个具有所含行数的数组
int[][] odds = new int[NMAX + 1][];
//接下来，分配这些行
for (int n = 0; n <= NMAX ; n++)
    odds[n] = new int[n + 1];                                                                                              
```