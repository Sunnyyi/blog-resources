---
title: Java中的异常、断言和日志
tags: [Java]
categories: Java
top: true
date: 2020-05-23 10:09:05
---

# 一、处理错误
## 1. 异常分类
所有的异常都是由 Throwable 继承而来的，异常的继承层次如下：
![异常](异常.png)
1. **Error**: 类层次结构描述了 Java 运行时系统的内部错误和资源耗尽错误。<font color=Crmison>应用程序不应该抛出这种类型的对象,因为我们对其没有任何控制能力。</font>
2. **Exception**：由程序错误导致的异常属于 RuntimeException ; 而程序本身没有问题， 但由于像 I/O 错误这类问题导致的异常属于其他异常。
3. **派生于RuntimeException的异常**：错误的类型转换;数组访问越界;访问 null 指针。<font color=Crmison>这些异常也不该声明，因为这些运行时错误完全在我们的控制之下。</font>
4. **不是派生于RuntimeException的异常**: 试图在文件尾部后面读取数据; 试图打开一个不存在的文件; 试图根据给定的字符串查找 Class 对象，而这个字符串表示的类并不存在。
5. **非受查异常**：派生于Error类或 RuntimeException 类的所有异常称为非受查异常。
6. **受查异常**: 所有其它异常。编译器将核查是否为所有的受査异常提供了异常处理器。否则代码不能通过编译。

## 2. 声明受查异常
1. 一个方法必须声明(**throws**)所有可能抛出的受查异常， 而非受查异常要么不可控制（ Error),要么就应该避免发生（ RuntimeException)。
2. 若方法在运行时真的抛出了一个异常对象，运行时系统就会开始搜索异常处理器，以便知道如何处理它。
3. 如果在子类中覆盖了超类的一个方法， 子类方法中声明的受查异常不能比超类方法中声明的异常更通用和更多。
4. 如果超类方法没有抛出任何受查异常，子类也不能抛出任何受查异常。而是必须捕获所有受查异常。
5. 抛出异常的情况有4种：
- 调用一个抛出受査异常的方法；
- 程序运行过程中发现错误，并且利用throw语句抛出一个受查异常；
- 程序出现错误，例如，a[-1]=0 会抛出一个 ArraylndexOutOfBoundsException 这样的非受查异常。
- Java 虚拟机和运行时库出现的内部错误。
6. 如果类中的一个方法声明将会抛出一个异常， 而这个异常是某个特定类的实例时，则这个方法就有可能抛出一个这个类的异常， 或者这个类的任意一个子类的异常。
7. 在 Java中，没有 throws 说明符的方法将不能抛出任何受查异常。

```java
class MyAnimation
{
    public Image loadlmage(String s) throws IOException,FileNotFoundException,EOFException
    {
        ...
    } 
}
```

## 3. 如何抛出异常
1. 找到一个合适的异常类。
2. 创建这个类的一个对象。
3. 使用**throw**关键字将对象抛出。

如果只是抛出异常而不在任何地方进行捕获，那程序就会终止执行，并在控制台上打印出异常信息，其中包括异常的类型和堆栈的内容。即一旦方法抛出了异常，这个方法就不可能返回到调用者，除非进行异常捕获。
```java
String readData(Scanner in) throws EOFException  //声明异常
{
    while (…) 
    {
        if (!in.hasNext())
        {
            if (n < len)
                throw new EOFException();  //抛出异常
        }
    }
    return s;
}
```

## 4. 创建异常类
```java
class FileFormatException extends IOException
{
    public FileFormatException(){}  //定义的类应该包含两个构造器， 一个是默认的构造器；另一个是带有详细描述信息的构造器
    public FileFormatException(String gripe)   //超类 Throwable 的 toString 方法将会打印出这些详细信息， 这在调试中非常有用
    {
        super(gripe); 
    } 
}

String readData(BufferedReader in) throws FileFormatException
{
    while (…) 
    {
        if (!in.hasNext())
        {
            if (n < len)
                throw new FileFornatException();  //抛出异常
        }
    }
    return s;
}
```
Throwable常用API
```java
import java.lang.Throwable;

Throwable( ) //构造一个新的 Throwabie 对象， 这个对象没有详细的描述信息。
Throwable(String message ) //构造一个新的 throwable 对象， 这个对象带有特定的详细描述信息。习惯上，所有派生的异常类都支持一个默认的构造器和一个带有详细描述信息的构造器。
String getMessage( )  //获得 Throwable 对象的详细描述信息。
```

# 二、捕获异常
## 1. 捕获异常
如果某个异常发生的时候没有在任何地方进行捕获，那程序就会终止执行，并在控制台上打印出异常信息，其中包括异常的类型和堆栈的内容。
```java
public void read(String filename) {
    try
    {
        InputStream in = new Filei叩utStream(filename);
        int b;
        while ((b = in.read()3 != -1) 
        {
            process input
        } 
    }
    catch (IOException exception) 
    {
        exception.printStackTrace(); 
    } 
}
```
1. 如果在try语句块中的任何代码抛出了一个在 catch 子句中说明的异常类,则程序将跳过 try语句块的其余代码并执行catch 子句中的处理器代码，否则将跳过 catch 子句。
3. 如果方法中的任何代码拋出了一个在 catch 子句中没有声明的异常类型，或者仅仅是声明了可能抛出的异常，那么这个异常就会传递给调用者进行处理。
4. 如果调用了一个抛出受查异常的方法，就必须对它进行处理，或者继续传递。
5. 如果想传递一个异常， 就必须在方法的首部添加一个 throws 说明符， 以便告知调用者这个方法可能会抛出异常。
6. 通常，应该捕获那些知道如何处理的异常，而将那些不知道怎样处理的异常继续进行传递。


## 2. 捕获多个异常
```java
//一个 try 语句块中可以捕获多个异常类型，并对不同类型的异常做出不同的处理。
try
{
    code that might throwexceptions
}
catch (FileNotFoundException e) 
{
    emergency action for missing files
}
catch (UnknownHostException e) 
{
    emergency action for unknown hosts
}
catch (IOException e) 
{
    emergency action for all other I/O problems
}

//当捕获的异常类型彼此之间不存在子类关系且多个异常的处理动作一样时，同一个 catch 子句中可以捕获多个异常类型。
try
{
    code that might throw exceptions
}
catch (FileNotFoundException | UnknownHostException e)  //捕获多个异常不仅会让你的代码看起来更简单，还会更高效。
{
    emergency action for missing files and unknown hosts  //捕获多个异常时， 异常变量隐含为 final 变量。即不能在此处为e赋不同的值。
}
catch (IOException e) 
{
    emergency action for all other I/O problems
}
```

## 3. 再次抛出异常与异常链
```java
//再次抛出异常的基本方法
try
{
access the database
}
catch (SQLException e) 
{
    throw new ServletException("database error: " + e.getMessage()); 
}

//将原始异常设置为新异常的“原因”,这样可以让用户抛出子系统中的高级异常，而不会丢失原始异常的细节。
try
{
    access the database
}
catch (SQLException e)
{
    Throwable se = new ServletException("database error");
    se.initCause(e);
    throw se;
}

Throwable e = se.getCause();  //当捕获到异常时， 就可以使用下面这条语句重新得到原始异常

//只想记录一个异常， 再将它重新抛出，而不做任何改变
public void updateRecord() throws SQLException  //抛出的异常比声明的异常更通用时，Java SE7之前编译器会指出这个方法可以抛出任何 Exception 而不只是 SQLException。
{
    try
    {
        access the database
    }
    catch (Exception e)  //Java SE7之后，编译器会跟踪到 e 来自 try块。假设这个 try 块中仅有的已检査异常是 SQLException 实例，另外，假设 e 在catch 块中未改变，将外围方法声明为 throws 
                        //SQLException 就是合法的。
    {
        logger.log(level, message, e);
        throw e; 
    }
}
```

## 4. finally 子句
在需要关闭资源时，通常需要使用finally子句。
 ```java
InputStream in = new FileInputStream(. . .);
try
{
    //1
    //可能抛出异常的代码
    //2
}
catch(IOException e)
{
    //3
    //展示异常信息
    //4
}
finally
{ 
    //5
    in.close();
}
//6
 ```
 代码执行顺序有三种情况：
 1. 代码没有抛出异常：执行顺序为 1，2，5，6。
 2. 抛出一个在 catch 子句中捕获的异常：
 - 当catch字句没有抛出异常将执行 1，3，4，5，6。
 - 当catch字句抛出了一个异常，在执行完1，3，5之后异常将被抛回这个方法的调用者。
 3. 代码抛出了一个异常， 但这个异常不是由 catch 子句捕获的：在执行 1，5 后将异常抛给这个方法的调用者。

 ```java
//try 语句可以只有 finally 子句，而没有 catch 子句。
InputStream in = . .
try
{
    code that might throwexceptions
}
finally
{
    in.close();  //不论try中是否遇到异常，该语句都将被执行
}

//解耦合 try/catch 和 try/finally 语句块
InputStrean in = . . .;
//外层的try语句块仅确保报告出现的错误。
try
{
    // 内层的try语句块仅确保关闭输入流。
    try  
    {
        code that might throwexceptions
    }
    finally
    {
        in.close();   //若try语句中和finally语句中同时抛出异常则finally语句中的异常将会覆盖原始异常，转而抛出close方法的异常，此时需要用到带资源的try语句进行处理。
    } 
}
catch (IOException e) 
{
    show error message
}

//在try-catch-finally语句中使用return语句会带来的问题
public static int f(int n) 
{
    try
    {
        int r = n * n;
        return r;    //在方法返回前，finally 子句的内容将被执行。
    }
    finally
    {
        if (n = 2) return 0; //这个返回值覆盖了原始的返回值。
    } 
}
 ```

## 5. 带资源的 try 语句
1. 若资源属于一个实现了 AutoCloseable 接口(close方法声明抛出Exception异常)或其子接口 Closeable 接口(close方法声明抛出IOException异常)的类，则try块退出时，会自动调用 `resource.close()`。
2. 带资源的try语句会抛出原来的异常，而 close方法抛出的异常会“ 被抑制”，被抑制的异常将自动捕获，并由 `addSuppressed` 方法增加到原来的异常。可以调用 `getSuppressed` 方法得到从 close 方法抛出并被抑制的异常列表。
3. 带资源的 try 语句自身也可以有 catch 子句和一个 finally 子句。这些子句会在关闭资源之后执行。

```java
//不论正常退出还是存在异常，都会调用resource.close()方法，就好像使用了fianlly块。
try (Scanner in = new Scanner(new FileInputStream("usr/share/dict/words"), "UTF-8");  //可指定多个资源，不论块如何退出，in和out都会关闭而避免嵌套2个try-finally语句。
    PrintWriter out = new PrintWriter("out.txt"))
{
    while(in.hasNext())
        out.println(in.next().toUpperCase());  
}
```

## 6. 分析堆栈轨迹元素
堆栈轨迹是一个方法调用过程的列表，它包含了程序执行过程中方法调用的特定位置。
```java
//Throwable 类的 printStackTrace 方法访问堆栈轨迹的文本描述信息
Throwable t = new Throwable();
StringWriter out = new StringWri ter(); 
t.printStackTrace(new PrintWriter(out));
String description = out.toString();

//使用 getStackTrace 方法， 它会得到 StackTraceElement 对象的一个数组，可以在程序中分析这个对象数组。
Throwable t = new Throwable();
StackTraceElement[] frames = t.getStackTrace();
for (StackTraceElement frame : frames)
    analyze frame

//静态的 Thread.getAllStackTrace 方法， 它可以产生所有线程的堆栈轨迹 
Map<Thread, StackTraceElement[]> map = Thread.getAllStackTraces();
for (Thread t : map.keySet()) {
    StackTraceElement[] frames = map.get(t);
    analyze frames
}
```

# 三、使用异常机制的技巧
1. 异常处理不能代替简单的测试；
2. 不要过分地细化异常；
3. 利用异常层次结构(应该寻找更加适当的子类或创建自己的异常类)；
4. 不要压制异常(应该将很大概率不会抛出的异常关闭)；
5. **早抛出：**在检测错误时，“ 苛刻 ” 要比放任更好;
6. **晚捕获：**不要羞于传递异常(传递给高层要比捕获异常更加合适)。

```java
public Image loadImage(String s) 
{
    try
    { 
        // code that threatens to throw checked exceptions
    }
    catch (Exception e)  //即使发生了异常也会被忽略。
    {} // so there
}
```

# 四、使用断言
## 1. 断言的概念 
断言(assert)机制允许在测试期间向代码中插入一些检査语句。当代码发布时，这些插入的检测语句将会被自动地移走。
```java
//均对表达式进行检测，若结果为 false, 则抛出一个 AssertionError 异常。
assert 条件;
assert 条件:表达式;  //表达式将被传入 AssertionError 的构造器，并转换成一个消息字符串(表达式的唯一目的)，表达式的值不会被存储。

assert x >= 0; //断言 x 是一个非负数值
assert x >= 0 : x;

//若希望条件也生成错误报告的一部分，就必须将它以字符串的形式传递给 AssertionError 对象。
assert x >= 0 : "x >= 0";
```

## 2. 启用和禁用断言
1. 在默认情况下，断言被禁用。
2. 在启用或禁用断言时不必重新编译程序，因为这是类加载器的功能，当断言被禁用时，类加载器将跳过断言代码，因此，不会降低程序运行的速度。
3. 可以在某个类或整个包中使用断言。
4. 有些类不是由类加载器加载，而是直接由虚拟机加载。可以使用这些开关有选择地启用或禁用那些类中的断言。
5. 启用和禁用所有断言的 -ea 和 -da 开关不能应用到那些没有类加载器的“系统类” 上,此时应使用 -enablesystemassertions/-esa 开关启用断言。

```java
java -enableassertions MyApp
java -ea:MyClass -ea:com.mycompany.mylib...  MyApp //开启 MyClass 类以及在 com.mycompany.mylib 包和它的子包中的所有类的断言。

java -ea:... -da:MyClass MyApp  //用选项 -disableassertions 或 -da 禁用某个特定类和包的断言
```

## 3. 使用断言完成参数检查
1. 断言失败是致命的、不可恢复的错误，有时候会拋出一个断言错误，有时候会产生一个 null 指针异常，这完全取决于类加载器的配置。
2. 断言检查只用于开发和测阶段。

```java
assert a != null; //检查参数是否非空
```

## 4. 为文档假设使用断言
```java
if (i % 3 == 0)
else if (i % 3 = 1)
else // (i % 3 == 2)

//为以上注释使用断言
assert i >= 0;
if (i % 3 == 0)
else if (i % 3 == 1)
else
{
    assert i % 3 == 2; 
}
```
***
```java
import java.lang.ClassLoader;
void setDefaultAssertionStatus( boolean b ) //对于通过类加载器加载的所有类来说， 如果没有显式地说明类或包的断言状态， 就启用或禁用断言。 
void setCIassAssertionStatus(String className , boolean b ) //对于给定的类和它的内部类，启用或禁用断言。
void setPackageAssertionStatus( String packageName , bool ean b ) //对于给定包和其子包中的所有类，启用或禁用断言。 
void clearAssertionStatus() //移去所有类和包的显式断言状态设置， 并禁用所有通过这个类加载器加载的类的断言。
```

# 五、记录日志
## 1. 基本日志
```java
//利用全局日志记录器生成简单的日志记录
Logger.getClobal().info("File->Open menu item selected");

//在适当的地方（如 main 开始）调用将会取消所有的日志
Logger.getClobal().setLevel(Level.OFF);  
```

## 2. 高级日志
1. 不要将所有的日志都记录到一个全局日志记录器中，而是可以自定义日志记录器。
2. 未被任何变量引用的日志记录器可能会被垃圾回收，因此需要用一个静态变量存储日志记录器的一个引用。
3. 日志记录器名也具有层次结构,父与子之间将会共享某些属性，例如，若对父日志记录器设置了日志级别，它的子记录器也会继承这个级别。
4. 默认的日志配置记录了 INFO 或更高级别的所有记录，如果将记录级别设计为 INFO 或者更低， 则需要修改日志处理器的配置。
5. 应该使用 CONFIG、FINE, FINER 和 FINEST 级别来记录那些有助于诊断，但对于程序员又没有太大意义的调试信息。

```java
//创建或获取记录器
private static final Logger myLogger = Logger.getLogger("com.mycompany.myapp");

//7 个日志记录器级别
SEVERE
WARNING
INFO
CONFIG
FINE
FINER
FINEST

//默认情况下，只记录前三个级别。 也可以设置其他的级別，还可以使用Level.ALL 开启所有级别的记录， 或者使用 Level.OFF 关闭所有级别的记录
logger.setLevel(Level.FINE);

//日志记录方法
logger.warning(message);
logger.fine(message);
logger.log(Level.FINE, message);

//默认的日志记录将显示包含日志调用的类名和方法名,但是,如果虚拟机对执行过程进行了优化，就得不到准确的调用信息。此时，可以调用 logp 方法获得调用类和方法的确切位置。
void logp(Level l, String className, String methodName, String message)

//用来跟踪执行流的方法,将生成 FINER 级别和以字符串 ENTRY 和 RETURN 开始的日志记录。
void entering(String dassName , String methodName)
void entering(String className , String methodName , Object param)
void entering(String className , String methodName , Object[] params)
void exiting(String className , String methodName)
void exiting(String className , String methodName , Object result)
   
//记录日志的常见用途是记录那些不可预料的异常。
if (...) 
{
    IOException exception = new IOException(". . .");
    logger.throwing("com•mycompany.mylib.Reader", "read", exception); //调用 throwing 可以记录一条 FINER 级别的记录和一条以 THROW 开始的信息。
    throw exception; 
}

try
{
    ...
}
catch (IOException e) {
    Logger.getLogger("com.mycompany.myapp").log(Level.WARNING , "Reading image", e); 
}
```

## 3. 修改日志管理器配置
1. 可以通过编辑配置文件来修改日志系统的各种属性。在默认情况下，配置文件存在于：`jre/lib/1ogging.properties`。
2. 也可将 `java.util.logging.config.file` 特性设置为配置文件的存储位置来使用另一个配置文件。
3. 日志记录并不将消息发送到控制台上，这是处理器的任务。
4. 在曰志管理器配置的属性设置不是系统属性，不会对日志记录器产生任何影响。

```java
//日志管理器在 VM 启动过程中初始化，这在 main 执行之前完成
System.setProperty("java.util.logging.config.file",file);  //在main中调用该方法后也会调用 `LogManager.readConfiguration()` 来重新初始化曰志管理器。

java -Djava.util.logging.config.file=configFile MainClass  //使用另一个配置文件启动应用程序

.level=INFO  //编辑配置文件，修改默认的日志记录级别
com.mycompany.myapp.level=FINE  //指定自己的日志记录级别

java.util.logging.ConsoleHandler.level=FINE  //设置处理器级别以在控制台上看到 FINE 级别的消息
```

## 4. 本地化
1. 本地化的应用程序包含资源包中的本地特定信息,资源包由各个地区的映射集合组成，日志消息本地化后让全球的用户都可以阅读它。。
2. 每个资源包都有一个名字，要想将映射添加到一个资源包中，需要为每个地区创建一个文件(例如com/mycompany/logmessages_en.properties)，这些文件都是纯文本文件。
3. 可以将这些文件与应用程序的类文件放在一起， 以便 ResourceBundle 类自动地对它们进行定位。

```java
//资源包中的映射
readingFile=Achtung! Datei wird eingelesen
renamingFile=Datei wird umbenannt
...

//请求日志记录器时指定一个资源包
Logger logger = Logger.getLogger(loggerName , "com.mycompany.logmessages");

//为日志消息指定资源包的关键字
logger.info("readingFile");

//若需要在本地化的消息中增加一些参数， 因此，消息应该包括占位符{0}、{1}
Reading file {0}.
Achtung! Datei {0} wird eingelesen.
//调用下面的一个方法向占位符传递具体的值
logger.log(Level.INFO, "readingFile", fileName);
logger.log(Level.INFO, "renamingFile", new Object[] { oldName , newName });
```

## 5. 处理器
1. 默认情况下日志记录器将记录发送到 ConsoleHandler 中， 并由它输出到 System.err流中。
2. 其他情况下，日志记录器还会将记录发送到父处理器中，而最终的处理器（命名为“ ”,所有日志记录器的父类）有一个 ConsoleHandler。
3. 对于一个要被记录的日志记录，它的日志记录级别必须高于日志记录器和处理器的阈值。

```java
//日志管理器配置文件设置的默认控制台处理器的日志记录级别
java.util.logging.ConsoleHandler.level =INFO

//修改配置文件中的默认日志记录级别和处理器级别来改变处理器的默认设置
Logger logger = Logger.getLogger("com.mycompany.myapp");
logger.setLevel(Level.FINE); 
logger.setUseParentHandlers(false); //原始日志记录器将会把所有等于或高于 INFO 级別的记录发送到控制台,为了避免看到重复记录，应该将useParentHandlers 属性设置为 false。
Handler handler = new ConsoleHandler();
handler.setLevel(Level.FINE); 
logger.addHandler(handler);

//直接将记录发送到默认文件的处理器，即用户主目录的 javan.log 文件中， n 是文件名的唯一编号。也可发送到其他地方，但需要添加其它的处理器，如FileHandler(特定文件)、SocketHandler(特定主机)。
//默认情况下， 记录被格式化为 XML
FileHandler handler = new FileHandler(); 
logger.addHandler(handler);
```

## 6. 过滤器
1. 默认情况下，过滤器根据日志记录的级别进行过滤。每个日志记录器和处理器都可以有一个可选的过滤器来完成附加的过滤。
2. 也可自定义过滤器，并调用setFilter 方法将一个过滤器安装到一个日志记录器或处理器中，注意一时刻最多只能有一个过滤器。

```java
//通过实现 Filter 接口并定义下列方法来自定义过滤器。
boolean isLoggable(LogRecord record)  //可以利用自己喜欢的标准，对日志记录进行分析，返回 true 表示这些记录应该包含在日志中。
```

## 7. 格式化器
对日志记录自定义格式。
```java
//扩展 Formatter 类并覆盖下面这个方法实现自定义日志记录的格式
String format(LogRecord record)  //根据自己的愿望对记录中的信息进行格式化，并返冋结果字符串。
String formatMessage(LogRecord record)  //这个方法对记录中的部分消息进行格式化、 参数替换和本地化应用操作，可在format方法中调用

//要覆盖下面两个方法，实现在已格式化的记录的前后加上一个头部和尾部
String getHead (Handler h)
String getTail (Handler h)

//最后，调用 setFormatter 方法将格式化器安装到处理器中。
```

# 六、调试技巧
1. 可以调用合适的方法打印或记录任意变量的值；
2. 在每一个类中放置一个单独的 main方法对每一个类进行单元测试；
3. 使用JUnit单元测试框架，详见http://junit.org 
4. 使用日志代理截获方法调用， 并进行日志记录，然后调用超类中的方法。
5. 利用 Throwable 类提供的 printStackTace 方法。
6. —般来说，堆栈轨迹显示在 System.err 上。也可以利用 `printStackTrace(PrintWriter s)`方法将它发送到一个文件中，还可将其捕获到一个字符串中。
7. 将一个程序中的错误信息保存在一个文件中。
8. 不让非捕获异常的堆栈轨迹出现在 System.err 中。
9. 用 -verbose 标志启动 Java 虚拟机观察类的加载过程有助于诊断由于类路径引发的问题。
10. -Xlint 选项告诉编译器对一些普遍容易出现的代码逻辑问题进行检査。
11. java 虚拟机增加了对 Java 应用程序进行监控和管理的支持，例如jconsole 图形工具，对于像应用程序服务器这样大型的、 长时间运行的 Java 程序来说特别重要。
12. 使用 jmap 实用工具获得一个堆的转储并进行探查，其中显示了堆中的每个对象。
13. 使用 -Xprof 标志运行 Java 虚拟机，就会运行一个基本的剖析器来跟踪那些代码中经常被调用的方法。剖析信息将发送给 System.out。输出结果中还会显示哪些方法是由即时编译器编译的。

```java
System.out.println("x=" + x);
Logger.getClobal().info("nx=" + x);

//日志代理
Random generator = new
Random() 
{
    public double nextDouble()    //当调用 nextDouble 方法时， 就会产生一个日志消息。
    {
        double result = super.nextDouble()
        {
            Logger.getClobal().info("nextDouble: " + result);
            return result; 
        }
    }
};

Thread.duapStack();  //无须捕获异常，在代码的任何位置插入下面这条语句就可以获得堆栈轨迹

//将对战轨迹捕获到一个字符串中
StringWriter out = new StringWriter();
new Throwable().printStackTrace(new PrintWriter(out));
String description = out.toString();

//将一个程序中的错误信息保存在一个文件中。
java MyProgram 2> errors.txt //捕获错误流
java MyProgram 1> errors.txt 2>&1 //在同一个文件中同时捕获 System.en•和 System.out

//改变非捕获异常的处理器
Thread.setDefaultUncaughtExceptionHandler(
    new Thread.UncaughtExceptionHandler() {
        public void uncaughtException(Thread t, Throwable e)
        {
            save information in logfile
        };
    });

//对代码的简单逻辑进行检查
javac -Xlint:fallthrough

//显示虚拟机性能的统计结果
jconsole processID

//转储堆中的内容进行探查
jmap -dump:format=b, file=dumpFileName processID
jhat dumpFileName
//通过浏览器进人丨oCalhOSt:7000, 将会运行一个网络应用程序，借此探查转储对象时堆的内容。


```
