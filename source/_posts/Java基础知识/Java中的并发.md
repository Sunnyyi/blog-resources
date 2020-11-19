---
title: Java中的并发
tags: [Java]
categories: Java
top: true
date: 2020-5-27 12:05:33
---
多进程程序中的每一个进程都拥有自己的一整套变量，而线程则共享数据，共享变量使线程之间的通信比进程之间的通信更有效、 更容易，且在有的操作系统中线程更加轻量级。在实际应用中有很多地方都用到了多线程，例如一个浏览器可以同时下载几幅图片等等。

# 一、创建线程
```java
//在一个单独的线程中执行一个任务的简单过程
Runnable r = () ->     //1. 将任务代码移到实现了 Runnable 接口的类的 run 方法中,由于 Runnable 是一个函数式接口，可以用 lambda 表达式建立一个实例
{
    try
    {
        for (int i = 1; i <= STEPS; i ++) 
        {
            ball.move(comp.getBounds());
            comp.repaint();
            Thread.sleep(DELAY);  //休眠给定的毫秒数，以阻塞线程
        } 
    }
    catch (InterruptedException e) 
    {

    } 
};
Thread t = new Thread(r);     //2. 由 Runnable 创建一个 Thread 对象
t.start();    //3. 启动线程

//函数式接口Runnable
public interface Runnable
{
    void run(); 
}
```

# 二、中断线程  
1. 当线程的 run 方法执行方法体中最后一条语句后， 并经由执行 return 语句返回时，或者出现了在方法中没有捕获的异常时，线程将终止。
2. 没有可以强制线程终止的方法，但是有方法可以用来请求终止线程。

```java
//不断检查当前线程是否被中断
Runnable r = () -> {
    try
    {
        while (!Thread.currentThread().islnterrupted() && more work to do) 
        {
            do more work
        } 
    }
    catch(InterruptedException e) 
    { 
        // thread was interr叩ted during sleep or wait
    }
    finally
    {
        cleanup,if required
    }
    // exiting the run method terminates the thread
};

//对InterruptedException 异常的处理
//在 catch 子句中调用 Thread.currentThread().interrupt() 来设置中断状态。于是，调用者可以对其进行检测。
void mySubTask()
{
    ...
    try { sleep(delay); }
    catch (InterruptedException e) 
    { 
        Thread.currentThread().interrupt(); 
    }
    ...
}

//用 throws InterruptedException 标记你的方法， 让调用者（或者， 最终的 run 方法）去捕获这一异常。
void mySubTask() throws InterruptedException
{
    ...
    sleep(delay);
    ...
}

void interrupt() //向线程发送中断请求。线程的中断状态将被设置为 true。如果目前该线程被一个 sleep调用阻塞，那么，InterruptedException 异常被抛出。 
static boolean interrupted() //测试当前线程（即正在执行这一命令的线程）是否被中断。注意，这是一个静态方法。这一调用会产生副作用—它将当前线程的中断状态重置为 false。 
boolean isInterrupted() //这是一个实例方法，测试线程是否被终止。不像静态的中断方法，这一调用不改变线程的中断状态。 
static Thread currentThread() //返回代表当前执行线程的 Thread 对象。
```

# 三、线程状态（6种）
- New (新创建） 
- Runnable (可运行） 
- Blocked (被阻塞） 
- Waiting (等待） 
- Timed waiting (计时等待） 
- Terminated (被终止）

线程状态间的转换：
![线程状态转换](线程状态转换.png)
```java
Thread.State getState()  //得到这一线程的状态：NEW、RUNNABLE、BLOCKED、WAITING、TIMED_WAITING或 TERMINATED 之一。
```

## 1. 新创建线程
在`newThread(r)`之后还未运行之前，还有一些基础工作要做，这个状态称为new状态，即新创建状态。

## 2. 可运行线程
1. 一旦调用 start 方法，线程就处于 runnable 状态。
2. 然而为了让其它线程获得运行机会，运行中的线程可能被中断，线程调度的细节依赖于操作系统提供的服务(抢占式调度或协作式调度)。
3. 现在所有的桌面以及服务器操作系统都使用抢占式调度（时间片和优先级调度），像手机这样的小型设备可能使用协作式调度（线程只有在调用 yield 方法、 或者被阻塞或等待时，线程才失去控制权。）
4. 故在任何给定时刻，一个可运行的线程可能正在运行也可能没有运行。

## 3. 被阻塞线程和等待线程
1. 当线程处于被阻塞或等待状态时，它暂时不活动。它不运行任何代码且消耗最少的资源。直到线程调度器重新激活它。
2. 线程转换为被阻塞状态或等待状态的时机：
   - 当一个线程试图获取一个内部的对象锁而该锁被其他线程持有，则该线程进入阻塞状。当所有其他线程释放该锁，并且线程调度器允许本线程持有它的时候，该线程将变成非阻塞状态。
   - 当线程等待另一个线程通知调度器一个条件时，它自己进入等待状态。在调用 `Object.wait` 方法或 `Thread.join` 方法， 或者是等待 `java.util.concurrent` 库中的 Lock 或 Condition 时， 就会出现这种情况。注意等待和阻塞状态是不同的。
   - 有几个方法有一个超时参数。调用它们导致线程进入**计时等待**（ timed waiting ) 状态。这一状态将一直保持到超时期满或者接收到适当的通知。带有超时参数的方法有 `Thread.sleep` 和 `Object.wait`、`Threadjoin`、 `Lock.tryLock` 以及 `Condition.await` 的计时版。

## 4. 被终止的线程
线程被终止的两个原因：
- 因为 run 方法正常退出而自然死亡。
- 因为一个没有捕获的异常终止了 run 方法而意外死亡。

# 四、线程属性
## 1. 线程优先级
1. 在 Java 程序设计语言中，每一个线程有一个优先级。
2. 默认情况下，一个线程继承它的父线程的优先级。可以用 `setPriority` 方法提高或降低任何一个线程的优先级。
3. 可以将优先级设置为在 MIN_PRIORITY (在 Thread 类中定义为 1 ) 与 MAX_PRIORITY (定义为 10 ) 之间的任何值。一般使用 NORM_PRIORITY （默认优先级，被定义为 5）。
4. 线程优先级是高度依赖于系统的。当虚拟机依赖于宿主机平台的线程实现机制时， Java 线程的优先级被映射到宿主机平台的优先级上。

```java
static void yield( ) //导致当前执行线程处于让步状态。如果有其他的可运行线程具有至少与此线程同样高的优先级，那么这些线程接下来会被调度。注意，这是一个静态方法。
```

## 2. 守护线程（daemon）
1. 守护线程的唯一用途是为其他线程提供服务。
2. 当只剩下守护线程时，虚拟机就退出了，由于如果只剩下守护线程，就没必要继续运行程序了。
3. 守护线程应该永远不去访问固有资源， 如文件、 数据库，因为它会在任何时候甚至在一个操作的中间发生中断。

```java
void setDaemon( boolean isDaemon )   //标识该线程为守护线程或用户线程。这一方法必须在线程启动之前调用。
```

## 3. 未捕获异常处理器
1. 线程的 run方法不能抛出任何受查异常，不需要任何 catch子句来处理可以被传播的异常。相反，就在线程死亡之前，异常被传递到一个用于未捕获异常的处理器。
2. 该处理器必须属于一个实现 Thread.UncaughtExceptionHandler 接口的类。

```java
//设置或获取未捕获异常的默认处理器。
static void setDefaultUncaughtExceptionHandler(Thread.UncaughtExceptionHandler handler)
static Thread.UncaughtExceptionHandler getDefaultUncaughtExceptionHandler() 

//设置或获取未捕获异常的处理器。如果没有安装处理器，则将线程组对象作为处理器。
void setUncaughtExceptionHandler(Thread.UncaughtExceptionHandlerhandler)
Thread.UncaughtExceptionHandler getUncaughtExceptionHandler()

//Thread.UncaughtExceptionHandler 接口中的唯一方法
void UncaughtException(Thread t, Throwable e)  //当一个线程因未捕获异常而终止， 按规定要将客户报告记录到日志中。
```

# 五、同步
## 1. 竞争条件
往往线程中的某个修改操作不是原子操作，则当线程1在计算的过程中被剥夺运行权，并在线程2修改完成同一项内容后被唤醒时，线程1对该项做的修改就会擦除线程2对该项做出的修改，此时程序就会出现错误。如果能够确保线程在失去控制之前方法运行完成，那么程序就不会因为竞争条件而产生错误。
指令`accounts[to] += amount;`就不是一个原子操作：
- 将 accounts[to] 加载到寄存器。
- 增加 amount。
- 将结果写回 accounts[to]。

## 2. 锁对象（ReentrantLock）
1. ReentrantLock 保护代码块的结构确保任何时刻只有一个线程进入**临界区**。一旦一个线程封锁了锁对象，其他任何线程都无法通过 lock 语句。当其他线程调用 lock 时，它们被阻塞，直到第一个线程释放锁对象。
2. 必须在finally子句中包括解锁 unlock 操作，如果在临界区的代码抛出异常，锁必须被释放。否则，其他线程将永远阻塞。
3. 如果使用锁，就不能使用带资源的 try 语句。
4. 加了锁的类的每一个对象都有自己的 ReentrantLock 锁对象。
5. 锁是**可重入的**， 因为线程可以重复地获得**已经持有的锁**。锁保持一个**持有计数**来跟踪对 lock 方法的嵌套调用，当该计数为0时，线程才释放锁。

```java
//用 ReentrantLock 保护代码块的基本结构
myLock.lock(); // a ReentrantLock object
try
{
    临界区  //临界区的代码要仔细设计，避免因为异常的抛出而跳出临界区，否则finally 子句释放锁之后会使对象可能处于一种受损状态
}
finally
{
    myLock.unlock();  // 确保锁被释放，即使抛出异常
}

//应用：使用一个锁来保护 Bank 类的 transfer 方法
public class Bank
{
    //构建一个可以被用来保护临界区的可重入锁对象。
    private Lock bankLock = new ReentrantLock(); // ReentrantLock 实现了 Lock 接口
    ...

    public void transfer(int from, int to, int amount) 
    {
        //获取这个锁；如果锁同时被另一个线程拥有则发生阻塞。
        bankLock.lock();
        try
        {
            System.out.print(Thread.currentThread());
            accounts[from] -= amount;
            System.out.printf(" %10.2f from %d to %d", amount, from, to);
            accounts[to] += amount;
            System.out.printf(" Total Balance: %10.2f%n", getTotalBalance());  //transfer 方法调用 getTotalBalance 方法，这也会封锁 bankLock 对象，此时 bankLock对象的持有计数为 2。
        }
        finally
        {
            //释放这个锁。
            banklock.unlock();
        }
    }
}

ReentrantLock(boolean fair) //构建一个带有公平策略的锁。一个公平锁偏爱等待时间最长的线程。但是，这一公平的保证将大大降低性能。所以， 默认情况下， 锁没有被强制为公平的。
```

## 3. 条件对象
1. 线程进入临界区后发现需要满足某一条件之后才能执行，但由于判断条件和执行语句之间可能被其它线程打断，故为了避免错误需要使用一个条件对象来管理这些线程。
2. 使用条件对象的await方法使当前需要某条件的线程阻塞并放弃锁。
3. 等待获得锁的线程和调用 await 方法的线程存在本质上的不同。一旦一个线程调用 await 方法，它进入该条件的**等待集**。**当锁可用时**，该线程不能马上解除阻塞。相反，它处于阻塞状态，直到另一个线程调用同一条件上的 signalAll 方法时为止。
4. 当调用条件对象的signalALL方法时会重新激活因为这一条件而等待的**所有线程**。当这些线程从等待集当中移出时，它们再次成为可运行的，调度器将再次激活它们。同时，它们将试图重新进入该对象。一旦锁成为可用的，它们中的某个将从 await 调用返回， 获得该锁并从被阻塞的地方继续执行。
5. 如果所有其他线程被阻塞， 最后一个活动线程在解除其他线程的阻塞状态之前就调用 await 方法，那么它也被阻塞，这会导致**死锁**现象，并使该程序被挂起。
6.  经验上讲，在对象的状态有利于等待线程的方向改变时调用 signalAll。
7. 条件对象的另一个方法 signal, 是**随机解除**等待集中**某个**线程的阻塞状态。这更加有效，但存在危险。如果随机选择的线程发现自己仍然不能运行，那么它再次被阻塞。如果没有其他线程再次调用 signal, 那么系统就死锁了。
8. 当一个线程拥有某个条件的锁时，它仅仅可以在该条件上调用 await、signalAll 或signal 方法。
9. 锁可以拥有一个或多个相关的条件对象。

```java
public class Bank
{
    private final double[] accounts = new double[n];
    private Lock bankLock = new ReentrantLock();
    private Condition sufficientFunds = = bankLock.newCondition();

    public void transfer(int from, int to, double amount) throws InterruptedException
    {
        bankLock.lock();
        try
        {
            while (accounts [from] < amount)
                sufficientFunds.await();  //等待满足某条件
            System.out.print(Thread.currentThread());
            accounts[from] -= amount;  
            System.out.printf(" %10.2f from %d to %d", amount, from, to);
            accounts[to] += amount;
            System.out.printf(" Total Balance: %10.2f%n", getTotalBalance());
            sufficientFunds.signalAll();  //释放所有等待集
        }
        finally
        {
            bankLock.unlock();
        }
}
```

## 4. synchronized 关键字(内部对象锁)
1. 从 1.0 版开始，Java中的每一个对象都有一个内部锁和一个内部条件。如果一个方法用 synchronized 关键字声明，那么对象的锁将保护整个方法。也就是说，要调用该方法，线程必须获得内部的对象锁。
2. 内部对象锁**只有一个相关条件**。wait 方法添加一个线程到等待集中，notifyAll/notify 方法解除等待线程的阻塞状态。
3. 将静态方法声明为 synchronized 也是合法的。如果调用这种方法，该方法获得相关的类对象的内部锁，且没有其他线程可以调用**同一个类的这个或任何其他的同步静态方法**。
4. 实际应用中应尽量使用synchronized 关键字，只有在特别需要 Lock/Condition 结构提供的独有特性时，才使用 Lock/Condition。

```java
//内部锁的基本结构,以下两种结构等价
public synchronized void method()
{
    method body
}


public void method()
{
    this.intrinsicLock.lock();
    try
    {
        method body
    }
    finally 
    { 
        this.intrinsicLock.unlock(); 
    } 
}

//应用
class Bank
{
    private double[] accounts;
    public synchronized void transfer(int from，int to, int amount) throws InterruptedException
    {
        while (accounts[from] < amount)
            wait(); // 类似await
        accounts[from] -= amount ;
        accounts[to] += amount ;
        notifyAll();// 类似signalAll
    }
    public synchronized double getTotalBalance() { . . . } 
}
```

## 5. 同步阻塞
通过进入一个同步阻塞也可以获得每个java对象持有的锁。
```java
//基本结构
synchronized (obj) //获得obj的锁
{
    critical section
}

//应用
public void transfer(Vector<Double> accounts, int from, int to, int amount) 
{
    synchronized (accounts) 
    {
        accounts.set(from, accounts.get(from) - amount);
        accounts.set(to, accounts.get(to) + amount); 
    }
    Systen.out.print1n(. . .); 
}
```

## 6. 监视器概念
1. 监视器可以在不需要程序员考虑如何加锁的情况下，就可以保证多线程的安全性，可用synchronized 关键字来实现。
2. 监视器应满足如下特性：
    - 监视器是只包含私有域的类。
    - 每个监视器类的对象有一个相关的锁。 
    - 使用该锁对所有的方法进行加锁。换句话说，如果客户端调用 对象的某个方法, 那么对象的锁是在方法调用开始时自动获得， 并且当方法返回时自动释放该锁。因为所有的域是私有的，这样的安排可以确保一个线程在对对象操作时， 没有其他线程能访问该域。 
    - 该锁可以有任意多个相关条件。

## 7. Volatile 域
1. volatile 关键字为实例域的同步访问提供了一种免锁机制。
2. 如果声明一个域为 volatile ,那么**编译器**和**虚拟机**就知道该域是可能被另一个线程并发更新的。
3. Volatile 变量不能提供原子性，不能保证读取、 翻转和写入不被中断，故该变量只用于原子操作时可声明为Volatile。
4. 一旦一个共享变量（类的成员变量、类的静态成员变量）被volatile修饰之后：
    - 保证了不同线程对这个变量进行操作时的可见性，即一个线程修改了某个变量的值，这新值对其他线程来说是立即可见的。
    - 禁止进行指令重排序。

## 8. final 变量
还有一种情况可以安全地访问一个共享域， 即这个域声明为 final 时。
```java
final Map<String, Double> accounts = new HashKap<>(); //其他线程会在构造函数完成构造之后才看到这个 accounts 变量。否则不能保证其他线程看到的是 accounts 更新后的值，它们可能都只是看到 null , 而不是新构造的 HashMap。
```

## 9. 原子性
```java 
public static AtomicLong nextNumber = new AtomicLong(); 
long id = nextNumber.incrementAndGet();  //以原子方式将 AtomicLong 自增，并返回自增后的值。

do {
    oldValue = largest.get();
    newValue = Math.max (oldValue , observed); 
}while (llargest.compareAndSet(oldValue, newValue));  //如果另一个线程也在更新 largest，就可能阻止这个线程更新。这样一来，compareAndSet会返回 false, 而不会设置新值。

//lambda 表达式更新变量
largest.updateAndGet(x -> Math .max(x, observed));
largest.accumulateAndCet(observed, Math::max);

//累加
LongAccumulator adder = new LongAccumulator(Long::sum, 0); 
adder.accumulate(value);
```

## 10. 死锁
1. 锁和条件不能解决多线程中的所有问题，在程序运行的过程中有可能所有的线程都会被阻塞，这样的状态就称为死锁，当程序进入死锁状态时程序就会被挂起。
2. 当程序挂起时，键入 `CTRL+\`, 将得到一个所有线程的列表。每一个线程有一个栈踪迹，告诉你线程被阻塞的位置。
3. Java 编程语言中没有任何东西可以避免或打破这种死锁现象。只能仔细设计程序，以确保不会出现死锁。

## 11. 线程局部变量
由于在线程间共享变量有死锁的风险，且使用同步机制会产生很大的开销，故对于那些不需要共享的变量就可以使用线程局部变量为各个线程提供各自的实例。
```java
//使用ThreadLocal辅助类构造SimpleDateFormat的线程局部变量
public static final ThreadLocal<SimpleDateFormat> dateFormat =ThreadLocal.withInitial(() -> new SimpleDateFormat("yyyy-MM-dd"));
String dateStamp = dateFormat.get().format(new Date());

//使用ThreadLocalRandom类为各个线程提供一个单独的随机数生成器
int random = ThreadLocalRandom.current().nextlnt(upperBound);  //ThreadLocalRandom.current() 调用会返回特定于当前线程的 Random 类实例。
```

## 12. 锁测试与超时
```java
//tryLock 方法试图申请一个锁， 在成功获得锁后返回 true, 否则，立即返回false, 而且线程可以立即离开去做其他事情。
if (myLock.tryLock()) 
{ 
    try { . . . }
    finally { myLock.unlock(); } }
    else
    // do something else
}

//调用 tryLock 时，使用超时参数,如果线程在等待期间被中断，将抛出InterruptedException 异常。这是一个非常有用的特性，因为允许程序打破死锁。
//TimeUnit 是一 枚举类型，可以取的值包括 SECONDS、MILLISECONDS, MICROSECONDS和 NANOSECONDS
//也可以调用 locklnterruptibly 方法。它就相当于一个超时设为无限的 tryLock 方法。
if (myLock.tryLock(100, TimeUnit.MILLISECONDS))

//在等待一个条件时， 也可以提供一个超时,
//可以使用 awaitUninterruptibly 方法代替 await,这会使等待的线程被中断时继续等待下去。
myCondition.await(100, TimeUnit.MILLISECONDS))
```

## 13. 读 / 写锁
如果很多线程从一个数据结构读取数据而很少线程修改其中数据的话，使用ReentrantReadWriteLock 类是十分有用的。
```java
//使用读/写锁的步骤
//1. 构造一个 ReentrantReadWriteLock 对象
private ReentrantReadWriteLock rwl = new ReentrantReadWriteLock();
//2. 抽取读锁和写锁
private Lock readLock = rwl . readLock();
private Lock writeLock = rwl .writeLock();
//3. 对所有的获取方法加读锁
public double getTotalBalance()
{
    readLock.lock();
    try { . . . }
    finally { readLock.unlock(); } 
}
//4. 对所有的修改方法加写锁
public void transfer(. . .)
{
    writeLock.lock();
    try { . . . }
    finally { writeLock.unlock(); } 
}
```

# 六、阻塞队列
阻塞队列是一个支持两个附加操作的队列。这两个附加的操作是：在队列为空时，获取元素的线程会等待队列变为非空。当队列满时，存储元素的线程会等待队列可用。阻塞队列常用于生产者和消费者的场景，生产者是往队列里添加元素的线程，消费者是从队列里拿元素的线程。阻塞队列就是生产者存放元素的容器，而消费者也只从容器里拿元素。
![阻塞队列方法](阻塞队列方法.png)

# 七、线程安全的集合
## 1. 高效的映射、集和队列
1. `java.util.concurrent` 包提供了映射、 有序集和队列的高效实现：`ConcurrentHashMap`、`ConcurrentSkipListMap`、`ConcurrentSkipListSet` 和 `ConcurrentLinkedQueue`。
2. 这些集合使用复杂的算法，通过允许并发地访问数据结构的不同部分来使竞争极小化。
3. size 方法不必在常量时间内操作,通常需要遍历来确定集合当前的大小。

## 2. 映射条目的原子更新
```java
//replace操作
do
{
    oldValue = map.get(word);
    newValue = oldValue = null ? 1 : oldValue + 1; 
    } while (!map.replace(word, oldValue, newValue));    
}

//使用一个 ConcurrentHashMap<String, AtomicLong>或者使用 ConcurrentHashMap<String，LongAdder>
map.putIfAbsent(word, new LongAdder()).increment();

//调用 compute 方法
map.compute(word , (k, v) -> v = null ? 1: v + 1);  
map.computelfAbsent(word , k -> new LongAdder()).increment();

map.merge(word, 1L, (existingValue, newValue) -> existingValue + newValue);
map.merge(word, 1L, Long::sum);
```

## 3. 对并发散列映射的批操作
```java
//search操作
String result = map.search(threshold, (k, v) -> v > 1000 ? k : null);
map.forEach(threshold, (k, v) -> System.out.println(k + " -> " + v)); //只为各个映射条目提供一个消费者函数
map.forEach(threshold, (k, v) -> k + " -> " + v, System.out::println);  //添加一个转换器函数，这个函数要先提供， 其结果会传递到消费者
map.forEach(threshold, (k, v) -> v > 1000 ? k + " -> " + v : null, System.out::println); //转换器可以用作为一个过滤器，过滤结果为null的值

//reduce操作，利用一个累加函数组合其输入
Long sum = map.reduceValues(threshold, Long::sum); //计算所有值的总和
Integer maxlength = map.reduceKeys(threshold, String::length, Integer::max);  //提供一个转换器函数，计算最长的键的长度
Long count = map. reduceValues(threshold, v -> v > 1000 ? 1L : null , Long::sum);  //转换器可以作为一个过滤器，通过返回 null 来排除不想要的输入。
```

## 4. 并发集视图
可以由ConcurrentHashMap的某些操作得到一个线程安全的集合。
```java
//newKeySet 方法生成这个映射的键集。
Set<String> words = ConcurrentHashMap.<String>newKeySet();  

//keySet 方法，包含一个默认值，可以在为集增加元素时使用
Set<String> words = map.keySet(1L);
words.add("Java");   //如果 "Java" 在 words 中不存在， 现在它会有一个值 1。
```

## 5. 写数组的拷贝
1. CopyOnWriteArrayList 和 CopyOnWriteArraySet 是线程安全的集合，其中所有的修改线程对底层数组进行复制。
2. 如果数组后来被修改了，迭代器仍然引用旧数组，但是，集合的数组已经被替换了。因而，旧的迭代器拥有一致的（可能过时的）视图，访问它无须任何同步开销。

## 6. 并行数组算法
Java SE 8中，Arrays 类提供了大量并行化操作。
```java
//静态 Arrays.parallelSort 方法对一个基本类型值或对象的数组排序。
String contents = new String(Files.readAllBytes(Paths.get("alice.txt")), StandardCharsets.UTF_8);  //将文件中的内容读入string中 
String[] words = contents.split("[\\P{L}]+"); // 按非字母拆分
Arrays.parallelSort(words):

Arrays.parallelSort(words, Comparator.comparing(String::length)); //提供一个 Comparator进行排序
values.parallelSort(values, length / 2, values,length); //提供一个排序的边界

//parallelSetAll 方法会用由一个函数计算得到的值填充一个数组。
Arrays.parallelSetAll(values, i-> i % 10); // 以 0 12 3 4 5 6 7 8 9 0 12 . . .来填充值

//parallelPrefix 方法，它会用对应一个给定结合操作的前缀的累加结果替换各个数组元素。
//操作并行完成，即经过log(n)步之后，这个过程结束。
Arrays.parallelPrefix(values, (x, y) -> x * y); //若数组为[1,2,3,4,...],执行该操作后数组将包含[1, 1x 2, 1x 2 x 3, l x 2 x 3 x 4, . . .]
```

## 7. 较早的线程安全集合
1. Java的初始版本中，Vector 和 Hashtable 类就提供了线程安全的动态数组和散列表的实现。
2. ArrayList 和 HashMap 类不是线程安全的。
3. 任何集合类都可以通过使用**同步包装器**变成线程安全的。

```java
//同步包装器，结果集合的方法使用锁加以保护，提供了线程安全访问
List<E> synchArrayList = Collections.synchronizedList(new ArrayList<E>());
Map<K , V> synchHashMap = Collections.synchronizedMap(new HashMap<K , V>());

//如果在另一个线程可能进行修改时要对集合进行迭代，仍然需要使用“ 客户端” 锁定：
//如果使用“ foreach” 循环必须使用同样的代码， 因为循环使用了迭代器。
//如果在迭代过程中，别的线程修改集合，迭代器会失效，抛出 ConcurrentModificationException 异常,因此并发的修改可以被可靠地检测出来。
synchronized (synchHashMap) 
{
    Iterator<K> iter = synchHashMap.keySet().iterator();
    while (iter.hasNext());
}
```

# 八、Callable 与 Future
```java
//Callable接口封装一个异步运行的任务
public interface Callable<V> 
{ 
    V call() throws Exception; 
}

//Future 保存异步计算的结果。
public interface Future<V> { 
    V get() throws . . .;
    V get(long timeout, TimeUnit unit) throws . . .
    void cancel(boolean mayInterrupt);
    boolean isCancelled();
    boolean isDone(); 
}

//FutureTask 包装器可将 Callable转换成 Future 和 Runnable,它同时实现二者的接口。
Callable<Integer> myComputation = . . .;
FutureTask<Integer> task = new FutureTask<Integer>(myComputation);
Thread t = new Thread(task); // it's a Runnable
t.start();
Integer result = task.get(); // it's a Future
```

# 九、执行器
执行器（ Executor) 类有许多静态工厂方法用来构建线程池：
![执行器](执行器.png)

## 1. 线程池
使用线程池的步骤：
1. 调用 Executors 类中静态的方法 newCachedThreadPool 或 newFixedThreadPool。
2. 调用 submit 提交 Runnable 或 Callable 对象。
3. 如果想要取消一个任务，或如果提交 Callable 对象，那就要保存好返回的 Future对象。
4. 当不再提交任何任务时，调用 shutdown。

```java
ExecutorService newCachedThreadPool() //返回一个带缓存的线程池， 该池在必要的时候创建线程， 在线程空闲 60 秒之后终止线程。 
ExecutorService newFixedThreadPool(int threads) //返回一个线程池， 该池中的线程数由参数指定。 
ExecutorService newSingleThreadExecutor() //返回一个执行器， 它在一个单个的线程中依次执行各个任务。

Future<T > submit(Cal1able<T> task) 
Future< T > submit(Runnable task, T result) 
Future<?> submit(Runnable task) //提交指定的任务去执行。 

void shutdown() //关闭服务，会先完成已经提交的任务而不再接收新的任务。
int getLargestPoolSize()  //返回线程池在该执行器生命周期中的最大尺寸。
```

## 2. 预定执行
ScheduledExecutorService 接口具有为预定执行或重复执行任务而设计的方法。

## 3. 控制任务组
使用执行器可以控制一组相关任务：
```java
//invokeAll 方法提交所有对象到一个 Callable 对象的集合中
List<Callab1e<T>> tasks = . . .;
List<Future<T>> results = executor.invokeAll(tasks);
for (Future<T> result : results)
    processFurther(result.get());

//用 ExecutorCompletionService 来进行排列
ExecutorCompletionService<T> service = new ExecutorCompletionServiceo(executor);
for (Callable<T> task : tasks) service,submit(task);
for (int i = 0; i < tasks.size(); i ++) processFurther(service.take().get());
```

## 4. Fork-Join 框架
1. Fork-join 框架专门用来完成计算密集型任务，如图像或视频处理，它通过将任务分解为多个子任务来执行，最后合并其结果。
2. Fork-join 框架使用了一种有效的智能方法称为**工作密取**来平衡可用线程的工作负载，每个工作线程都有一个双端队列来完成任务。

## 5. 可完成Future
可完成future是可以组合的，利用可完成 fiiture，可以指定你希望做什么，以及希望以什么顺序执行这些工作。
```java
ConipletableFuture<String> contents = readPage(url);
CompletableFuture<List<URL>> links = contents.thenApply(Parser::getlinks);
```
![为 CompletableFuture<T> 对象增加一个动作](1.png)
![组合多个组合对象](2.png)

# 十、同步器
![同步器](3.png)

## 1. 信号量
信号量作为**同步原语**可以被有效地实现， 并且有足够的能力解决许多常见的线程同步问题。

## 2. 倒计时门栓(CountDownLatch)
一个倒计时门栓让一个线程集等待直到计数变为 0。倒计时门栓是一次性的。一旦计数为 0, 就不能再重用了。

## 3. 障栅(CyclicBarrier 类)
1. CyclicBarrier 类实现了一个集结点称为障栅，当一个线程完成了它的那部分任务后就让它运行到障栅处。一旦所有的线程都到达了这个障栅，障栅就撤销，线程就可以继续运行。
2. 如果任何一个在障栅上等待的线程离开了障栅，那么障栅就被破坏了，所有其他线程的 await 方法抛出 BrokenBarrierException 异常。
3. 障栅被称为是循环的（ cyclic), 因为可以在所有等待线程被释放后被重用。
4. Phaser 类增加了更大的灵活性，允许改变不同阶段中参与线程的个数。

```java
//首先， 构造一个障栅， 并给出参与的线程数
CyclicBarrier barrier = new CydicBarrier(nthreads);

//每一个线程做一些工作，完成后在障栅上调用 await
public void run()
{
    doWork();
    bamer.await();
    ...
}

//await 方法有一个可选的超时参数：
barrier.await(100, TimeUnit.MILLISECONDS);

//可以提供一个可选的障栅动作（ barrier action), 当所有线程到达障栅的时候就会执行这一动作。
//该动作可以收集那些单个线程的运行结果。
Runnable barrierAction = . . .;
CyclicBarrier barrier = new Cyc1icBarrier(nthreads, barrierAction);
```

## 4. 交换器（Exchanger）
当两个线程在同一个数据缓冲区的两个实例上工作完成后，就可以使用交换器来相互交换缓冲区。

## 5. 同步队列（SynchronousQueue）
1. 同步队列是一种将生产者与消费者线程配对的机制。当一个线程调用 SynchronousQueue的 put 方法时，它会阻塞直到另一个线程调用 take 方法为止，数据仅仅沿一个方向传递。
2. 即使 SynchronousQueue 类实现了 BlockingQueue 接口， 概念上讲，它依然不是一个队列。它没有包含任何元素，它的 size方法总是返回 0。

