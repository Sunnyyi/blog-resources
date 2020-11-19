---
title: Java中的集合
tags: [Java]
categories: Java
top: true
date: 2020-05-26 10:09:05
---

集合是Java中非常重要的内容。
# 一、Java 集合框架
## 1. 将集合的接口与实现分离
队列接口指出可以在队列的尾部添加元素， 在队列的头部删除元素，并且可以査找队列中元素的个数。当需要收集对象， 并按照“ 先进先出” 的规则检索对象时就应该使用队列。
```java
//队列接口
public interface Queue<E> 
{
    void add(E element); 
    E remove();
    int size();
}

//队列实现
//循环队列,循环数组要比链表更高效，因此多数人优先选择循环数组。
//循环数组是一个有界集合， 即容量有限。如果程序中要收集的对象数量没有上限， 就最好使用链表来实现。
public class CircularArrayQueue<E> implements Queue<E> 
{
    private int head;
    private int tail;
    CircularArrayQueue(int capacity) { ... }
    public void add(E element) { ... }
    public E remove() { ... }
    public int size() { ... }
    private E[] elements; 
}

//链表队列
public class LinkedListQueue<E> implements Queue<E> 
{
    private Link head;
    private Link tail;
    LinkedListQueue() { ... }
    public void add(E element) { ... }
    public E remove() { ... }
    public int size() { ... } 
}

//使用接口类型存放集合的引用
Queue<Customer> expresslane = new CircularArrayQueue<>(100);
expressLane.add(new Customer("Harry"));

//只需修改接口的引用即可使用另外一种实现
Queue<Custoaer> expressLane = new LinkedListQueue<>();
expressLane.add(new Customer("Harry"));
```

## 2. Collection 接口
Java类库中集合的基本接口，集合中不允许有重复对象。
```java
public interface Collection<E> 
{
    boolean add(E element); //如果添加元素确实改变了集合就返回 true, 如果集合没有发生变化就返回 false。
    Iterator<E> iterator(); //iterator方法用于返回一个实现了 Iterator 接口的对象。可以使用这个迭代器对象依次访问集合中的元素。
    ...
}
```

## 3. 迭代器
1. 迭代器是不断向前滑动的，当读取或删除一个元素后就向后滑动一个位置。
2. 元素被访问的顺序取决于集合类型。 如果对 ArrayList 进行迭代， 迭代器将从索引 0开 始，每迭代一次，索引值加1，如果访问 HashSet 中的元素， 每个元素将会按照某种随机的次序出现。
```java
//Iterator迭代器
public interface Iterator<E> 
{ 
    E next();  //逐个访问集合中的每个元素，在调用 next 之前调用 hasNext方法。如果迭代器对象还有多个供访问的元素， 这个方法就返回 true。
    boolean hasNext();
    void remove();   //remove的是上一个被next的元素，故remove之前必须调用next方法读取。
    default void forEachRemaining(Consumer<? super E> action); 
}

//集合遍历
Collection<String> c = ...;
Iterator<String> iter = c.iterator();
while (iter.hasNext()) 
{
    String element = iter.next();
    do something with element
}

//foreach 遍历：可以与任何实现了 Iterable 接口的对象一起工作
//Collection 接口扩展了 Iterable 接口。因此， 对于标准类库中的任何集合都可以使用“ foreach” 循环。
public interface Iterable<E>
{
    Iterator<E> iterator();
}

for (String element : c) 
{
    do something with element
}

//lambda表达式遍历
iterator.forEachRemaining(element -> do something with element);
```

## 4. 泛型实用方法
1. 由于 Collection 与 Iterator 都是泛型接口，可以编写操作任何集合类型的实用方法。
2. Collection 接口中已经实现了很多默认方法供类库使用者直接调用。

```java
public abstract class Abstracted1ection<E> implements Collection<E>
{
    public abstract Iterator<E> iterator() ;
    public boolean contains(Object obj) 
    {
    for (E element : this) // calls iterator()
        if (element.equals(obj))
        return = true;
        return false;
    }
}
```

## 5. 集合框架中的接口
![集合](集合框架的接口.png)
集合有两个基本接口：Collection 和 Map。
```java
//Collection插入读取（使用迭代器）元素
boolean add(E element)

//Map插入读取元素
V put(K key, V value)
V get(K key)

//List 是一个有序集合，可迭代器访问也可随机访问
void add(int index, E element)
void remove(int index) 
E get(int index) 
E set(int index, E element)

//Listlterator 接口是 Iterator 的一个子接口。它定义了一个方法用于在迭代器位置前面增加一个元素：
void add(E element)

//标记接口 RandomAccess 不包含任何方法，不过可以用它来测试一个特定的集合是否支持高效的随机访问。
if (c instanceof RandomAccess)
{
    use random access algorithm
}
else
{
    use sequential access algorithm
}

//Set 接口等同于 Collection 接口，不过其方法的行为有更严谨的定义。集（set) 的 add方法不允许增加重复的元素。
//要适当地定义集的 equals 方法：只要两个集包含同样的元素就认为是相等的，而不要求这些元素有同样的顺序。
//hashCode 方法的定义要保证包含相同元素的两个集会得到相同的散列码。

//SortedSet 和 SortedMap 接口会提供用于排序的比较器对象，这两个接口定义了可以得到集合子集视图的方法。

//接口 NavigableSet 和 NavigableMap, 其中包含一些用于搜索和遍历有序集和映射的方法,TreeSet 和 TreeMap 类实现了这些接口。
```

# 二、具体的集合
![集合](具体集合1.png)
![集合](具体集合2.png)
以 Map 结尾的类实现了 Map 接口,除此之外，其他类都实现了Collection 接口。
## 1. 链表（LinkedList）
1. Java中的所有链表实际上都是双向链接的，LinkedList类实现了List接口。
2. 链表是一个有序集合,有n+1个位置添加新元素。
3. 迭代器是描述集合中位置的，所以这种依赖于位置的 add 方法（例如将元素添加到链表中间）将由迭代器负责。只有对自然有序的集合使用迭代器添加元素才有实际意义。
4. 在调用remove之前调用next，则删除的是迭代器左侧元素，若在调用remove之前调用previous，则删除的是右侧元素。
5. add 方法只依赖于迭代器的位置， 而 remove 方法依赖于迭代器的状态。

```java
List<String> staff = new LinkedList<>(); // LinkedList implements List
staff.add("Amy");   //LinkedList.add 方法将对象添加到链表的尾部。
staff.add("Bob")；
staff.add("Carl");
Iterator iter = staff.iterator();
String first = iter.next();// visit first element
String second = iter.next(); //visit second element
iter.remove(); // remove last visited element

//子接口ListIterator
interface ListIterator<E> extends Iterator<E>
{
    void add(E element);  //假定添加操作总会改变链表，在当前迭代器位置之前添加一个新对象。

    E previous()   //反向遍历链表
    boolean hasPrevious()
}

ListIterator<String> iter = staff.listIterator();
iter.next();
iter.add("juliet");  //在第二个元素之前添加“Juliet”

//set 方法用一个新元素取代调用 next 或 previous 方法返回的上一个元素。
//添加、删除元素属于结构性修改，而set 方法不被视为结构性修改。
ListIterator<String> iter = list.listIterator();
String oldValue = iter.next(); // returns first element
iter.set(newValue); // sets first element to newValue

//并发修改异常
//解决方法：可以根据需要给容器附加许多的迭代器，但是这些迭代器只能读取列表。另外，再单独附加一个既能读又能写的迭代器。
//检测并发修改的异常： 每个迭代器都维护一个独立的计数值。在每个迭代器方法的开始处检查自己改写操作的计数值是否与集合的改写操作计数值一致。
List<String> list = ...
ListIterator<String> iter1 = list.listlterator();
ListIterator<String> iter2 = list.listlterator();
iter1.next();
iter1.remove();
iter2.next(); // throws ConcurrentModificationException

//访问某个特定元素,效率低
LinkedList<String> list = ...;
String obj = list.get(n);  //每次査找一个元素都要从列表的头部重新开始搜索。LinkedList 对象根本不做任何缓存位置信息的操作。
```

 ## 2. 数组列表（ArrayList）
ArrayList类实现了List接口，它封装了一个动态再分配的对象数组。
 
## 3. 散列集（HashSet）
1. 在 Java 中，散列表用链表数组实现，散列码是由对象的实例域产生的一个整数。
2. 如果自定义类，就要负责实现这个类的 hashCode 方法，自己实现的 hashCode方法应该与 equals 方法兼容。
3. 散列表可以用来实现Set及HashSet。
4. 装填因子指表大小占预计元素个数的比例，若表中超过该比例的位置已经填入元素，这个表就会用双倍的表长自动地进行再散列。
5. 如果要对散列表再散列， 就需要创建一个表长更大的表，并将所有元素插入到这个新表中，然后丢弃原来的表。

```java
HashSet() //构造一个空散列表。 
HashSet(Collection<? extends E> elements ) //构造一个散列集， 并将集合中的所有元素添加到这个散列集中。 
HashSet(int initialCapacity) //构造一个空的具有指定容量（桶数）的散列集。 
HashSet(int initialCapacity , float loadFactor ) //构造一个具有指定容量和装填因子（一个 0.0 ~ 1.0 之间的数值，确定散列表填充的百分比，当大于这个百分比时，散列表进行再散列）的空散列集。
int hashCode( )  //返回这个对象的散列码,equals 和 hashCode的定义必须兼容，即如果 x.equals(y) 为 true, x.hashCode() 必须等于 y.hashCode()。
```

## 4. 树集（TreeSet）
1. 树集是一个有序集合，可以以任意顺序将元素插入到集合中。在对集合进行遍历时，每个值将自动地按照排序后的顺序呈现。
2. 排序是利用红黑树实现的，即每次将一个元素添加到树中时，都被放置在正确的排序位置上。因此，迭代器总是以排好序的顺序访问每个元素。
3. 要使用树集，必须能够比较元素。这些元素必须实现 Comparable 接口或者构造集时必须提供一个 Comparator。
4. 从 JavaSE 6 起，TreeSet 类实现了 NavigableSet 接口。 这个接口增加了几个便于定位元素以及反向遍历的方法。

## 5. 队列与双端队列（Deque）
Deque 接口由 ArrayDeque 和 LinkedList 类实现。这两个类都提供了双端队列，而且在必要时可以增加队列的长度。

## 6. 优先级队列（PriorityQueue）
1. 优先级队列使用堆对元素进行检索，堆是一个可以自我调整的二叉树，对树执行添加（add) 和删除（remove) 操作，可以让最小的元素移动到根，而不必花费时间对元素进行排序。
2. 无论何时调用 remove 方法，总会删除当前优先级队列中最小的元素。

# 三、映射
映射用来存放键/值对。如果提供了键，就能够查找到值。
## 1. 基本映射操作
1. Java 类库为map接口提供了两个通用的实现：HashMap 和 TreeMap。
2. 键必须是唯一的。不能对同一个键存放两个值。如果对同一个键两次调用 put 方法，第二个值就会取代第一个值。实际上，put 将返回用这个键参数存储的上一个值。

```java
Map<String, Employee> staff = new HashMap<>();
Employee harry = new Employee("Harry Hacker");
staff.put("987-98-9996", harry);

//检索
String id = "987-98-9996"; 
Employee e = staff.get(id);// gets harry,如果在映射中没有与给定键对应的信息， get 将返回 null。
Map<String, Integer> scores = ...;
int score = scores.get(id,0); // 如果get(id)不存在则默认get(0)。

//remove 方法用于从映射中删除给定键对应的元素。size 方法用于返回映射中的元素数。

//lambda表达式遍历映射
scores.forEach((k, v) -> System.out.println("key=" + k + ", value:" + v));
```

## 2. 更新映射项
```java
counts.put(word, counts.get(word)+ 1); //当第一次添加 word 时，get 会返回 null, 因此会出现一个 NullPointerException 异常。

//解决方法
counts,put(word, counts.getOrDefault(word, 0)+ 1);  //若word第一次添加，则利用默认值+1

counts.putlfAbsent(word, 0);  //只有当键原先存在时才会放入一个值。
counts.put(word, counts.get(word)+ 1);

counts.merge(word, 1, Integer::sum);  //如果键原先不存在，将把word与1关联，否则将原值和1求和。
```

## 3. 映射视图
1. 映射有三种视图：键集、 值集合（不是一个集） 以及键/值对集。
2. 不能向键集视图和键值对视图中增加元素，但可以删除，若试图调用 add方法， 它会抛出一个 UnsupportedOperationException。

```java
//三种视图
Set<K> keySet()  //keySet是实现了 Set 接口的另外某个类的对象。
Collection<V> values()
Set<Map.Entry<K, V>> entrySet()

//视图遍历
//键视图
Set<String> keys = map.keySet();
for (String key : keys) 
{
    do something with key
}

//键值对视图
for (Map.Entry<String, Employee> entry : staff.entrySet()) 
{
    String k = entry.getKey();
    Employee v = entry.getValue();
    do something with k, v
}

counts.forEach((k，v) -> {do somethingwith k, v });
```

## 4. 弱散列映射(WeakHashMap)
1. WeakHashMap用来配合垃圾回收器删除无用映射对象。
2. WeakHashMap 使用弱引用 （ weak references) 保存键。WeakReference 对象将引用保存到另外一个对象中，在这里，就是散列键。
3. 如果某个对象只能由 WeakReference 引用，垃圾回收器仍然回收它，但要将引用这个对象的弱引用放人队列中。WeakHashMap 将周期性地检查队列，以便找出新添加的弱引用，然后由WeakHashMap 删除对应的条目。

## 5. 链接散列集（LinkedHashSet）与链接映射（LinkedHashMap）
1. LinkedHashSet 和 LinkedHashMap 类用来记住插入元素项的顺序，当条目插入到表中时，就会并入到双向链表中。
2. LinkedHashSet 按照插入顺序来访问元素项。
3. LinkedHashMap 按照“最近最少使用”原则来访问元素项，每次调用 get 或put访问一个元素后，该元素就被从当前位置删除并放到链表的尾部。当在表中找不到元素项且表又已经满时，可以将迭代器加入到表中，并将枚举的前几个元素删除掉。这些是近期最少使用的几个元素。

```java
//构造散列映射表
LinkedHashMap<K, V>(initialCapacity, loadFactor, true)

//构造一个覆盖了removeEldestEntry方法的子类以实现自动化删除最近最少使用元素
Map<K, V> cache = new LinkedHashMap<>(128, 0.75F, true) 
{
    protected boolean removeEldestEntry(Map.Entry<K, V> eldest) 
    {
        return size() > 100; 
    } 
}();
```

## 6. 枚举集（EnumSet）与枚举映射（EnumMap）
1. 所有的枚举类型都扩展于泛型 Enum 类。
2. EnumSet 是一个枚举类型元素集的高效实现，内部用位序列实现。如果对应的值在集中，则相应的位被置为 1。
3. EnumMap 是一个键类型为枚举类型的映射。它可以直接且高效地用一个值数组实现。

```java
enum Weekday { MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY };
EnumSet<Weekday> always = EnumSet.allOf(Weekday.class);
EnumSet<Weekday> never = EnumSet.noneOf(Weekday.class);
EnumSet<Weekday> workday = EnumSet.range(Weekday.MONDAY, Weekday.FRIDAY);
EnumSet<Weekday> mwf = EnumSet.of(Weekday.MONDAY, Weekday.WEDNESDAY, Weekday.FRIDAY);

EnumMap<Weekday, Employee> personlnCharge = new EnumMapo(Weekday.class);
```

## 7. 标识散列映射（IdentityHashMap）
1. 类 IdentityHashMap的键的散列值不是用 hashCode 函数计算的，而是用 System.identityHashCode 方法由Object.hashCode 方法根据对象的内存地址来计算散列码时所使用的方式计算的。
2. 在对两个对象进行比较时， IdentityHashMap 类使用 ==, 而不使用 equals，即不同的键对象， 即使内容相同， 也被视为是不同的对象。
3. 在实现对象遍历算法（如对象串行化）时， 这个类非常有用， 可以用来跟踪每个对象的遍历状况。

# 四、视图与包装器
映射类中的keySet 方法返回一个实现 Set接口的类对象， 这个类的方法对原映射进行操作。这种集合称为视图。
## 1. 轻量级集合包装器(Collections)
Collections 类包含很多实用方法， 这些方法的参数和返回值都是集合，如空集、 列表、 映射等。
```java
//返回一个包装了普通 Java 数组的 List 包装器。
Card[] cardDeck = new Card[52];
List<Card> cardList = Arrays.asList(cardDeck);  //是一个视图对象， 带有访问底层数组的 get 和 set 方法。改变数组大小的所有方法（例如，与迭代器相关的 add 和 remove 方法）都会抛出一个
                                                //Unsupported OperationException 异常。
List<String> names = Arrays.asList("Amy" , "Bob", "Carl "); //返回一个实现了 List 接口的不可修改的对象
List<String> settings = Collections.nCopies(100, "DEFAULT"); //创建一个包含100个字符串的List, 每个串都被设置为“DEFAULT”
```

## 2. 子范围视图
可以为很多集合建立子范围视图，将任何操作应用于子范围，并且能够自动地反映整个列表的情况。
```java
List group2 = staff.subList(10, 20); //返回索引[10.20)之间的子范围元素视图
group2.clear();  //删除子视图元素，反映到整个列表

//对于有序集和映射， 可以使用排序顺序而不是元素位置建立子范围。
//返回大于等于 from 且小于 to 的所有元素子集。
SortedSet< E> subSet(E from, E to)
SortedSet< E> headSet(E to)
SortedSet< E> tail Set(E from)

SortedMap<K, V> subMap(K from, K to)
SortedMap<K, V> headMap(K to)
SortedMap<K, V> tailMap(K from)

//NavigableSet 接口赋予子范围操作更多的控制能力。
NavigableSet<E> subSet(E from, boolean fromInclusive, E to, boolean toInclusive)
NavigableSet<E> headSet(E to, boolean tolncIusive)
Navigab1eSet<E> tailSet(E from, boolean fromInclusive)
```

## 3. 不可修改的视图
1. Collections 还有几个方法，用于产生集合的不可修改视图,这些视图对现有集合增加了一个运行时的检查。如果发现试图对集合进行修改， 就抛出一个异常，同时这个集合将保持未修改的状态。
2. 由于视图只是包装了接口而不是实际的集合对象，所以只能访问接口中定义的方法。

```java
//以下方法可获得一个不可修改视图
//它的 equals 方法不调用底层集合的 equals 方法。它继承了 Object 类的 equals 方法， 这个方法只是检测两个对象是否是同一个对象。
Collections.unmodifiableCollection  

//使用底层集合的 equals 方法和 hashCode 方法。
Collections.unmodifiableList
Collections.unmodifiableSet

Collections.unmodifiableSortedSet
Collections.unmodifiableNavigableSet
Collections.unmodifiableMap
Collections.unmodifiableSortedMap
Collections.unmodifiableNavigableMap
```

## 4. 同步视图
1. 类库的设计者使用视图机制来确保常规集合的线程安全，而不是实现线程安全的集合类。
2. 同步操作即在另一个线程调用另一个方法之前，刚才的方法调用必须彻底完成。

```java
//Collections 类的静态 synchronizedMap方法可以将任何一个映射表转换成具有同步访问方法的 Map
Map<String, Employee> map = Collections.synchronizedMap(new HashMap<String, Employee>());
```

## 5. 受查视图
受查视图可以在调用add 方法时检测插入的对象是否属于给定的类。如果不属于给定的类，就立即抛出一个 ClassCastException。这样做的好处是错误可以在正确的位置得以报告。
```java
ArrayList<String> strings = new ArrayListo()
ArrayList rawList = strings; // 仅出现警告
rawList.add(new Date()); // now strings contains a Date object!

//解决方法：使用受查视图
List<String> safestrings = Collections.checkedList(strings，String,class);
ArrayList rawList = safestrings;
rawList.add(new Date());// 抛出异常 ClassCastException
```

# 五、算法
## 1. 排序与混排
1. java中对列表的排序直接将所有元素转入一个数组，对数组进行排序，然后再将排序后的序列复制回列表。
2. 不能将不可修改列表如unmodifiableList 列表传递给排序算法，传递的列表必须是可修改的，但不必是可以改变大小的。
    - 如果列表支持 set 方法，则是可修改的。
    - 如果列表支持 add 和 remove 方法， 则是可改变大小的。

```java
List<String> staff = new LinkedList<>();
fill collection
Collections.sort(staff);   //方法假定列表元素实现了 Comparable 接口

staff.sort(Comparator.comparingDouble(Employee::getSalary));  //使用 List 接口的 sort方法并传入一个 Comparator 对象。

staff.sort(Comparator.reverseOrder());  //按降序排列
staff.sort(Comparator.comparingDouble(Employee::getSalary).reversed())  //按工资逆序排序

ArrayList<Card> cards = ...;
Collections.shuffle(cards);  //随机地混排列表中元素的顺序,如果提供的列表没有实现 RandomAccess 接口，shuffle 方法将元素复制到数组中，然后打乱数组元素的顺序，最后再将打乱顺序后的元素复制回列表。
```

## 2. 二分查找（binarySearch）
1. 要使用二分查找，必须提供排好序的集合以及要查找的元素，且该集合必须实现List接口。
2. 如果集合没有采用 Comparable 接口的 compareTo 方法进行排序， 就还要提供一个比较器对象。
3. 只有采用随机访问，二分査找才有意义，若果为 binarySearch 算法提供一个链表，它将自动地变为线性查找。

```java
//返回值 >=0 ,表示匹配对象的索引。
//返回负值，表示没有匹配的元素。应该将这个键插人到列表索引（-i - l）的位置上，以保持列表的有序性。
i = Collections.binarySearch(c, element); 
i = Collections.binarySearch(c, element, comparator);
c.get(i);  

if (i < 0) 
    c.add(-i - 1, element);
```

## 3. 简单算法
Collections 类中提供的各种简单算法可以增加代码的可读性。
```java
Collections.replaceAll ("C++", "Java");

words.removelf(w -> w.length() <= 3);
words.replaceAll(String::toLowerCase);
```

## 4. 批操作
```java
//从 coll1 中删除 coll2 中出现的所有元素。
colll.removeAll(coll2);

//求coll1和coll2的交集
coll1.retainAll(coll2);

Set<String> result = new HashSet<>(a);
result.retainAll(b);

//在视图上批量操作
Map<String, Employee> staffMap = ...;
Set<String> terminatedIDs = ...;
staffMap.keySet().removeAll(terminatedIDs);
relocated.addAll(staff.subList(0, 10));
staff.subList(0, 10).clear();
```

## 5. 集合与数组的转换
```java
//把一个数组转换为集合
String[] values = ...;
HashSet<String> staff = new HashSet<>(Arrays.asList(values));

//从集合得到数组
Object[] values = staff.toArray();   //返回的数组是一个 Object[] 数组，不能使用强制类型转换

//提供一个所需类型而且长度为 0 的数组
String[] values = staff.toArray(new String[0]);  // 返回的数组就会创建为相同的数组类型

//以构造一个指定大小的数组
staff.toArray(new String[staff.size()]);  //这种情况不会创建新数组
```

## 6. 编写自己的算法
如果编写自己的算法（实际上，是以集合作为参数的任何方法，) 应该尽可能地使用接口，而不要使用具体的实现。
```java
//使用接口作为参数
void fillMenu(]Menu menu, Collection<]MenuItem> items) 
{
    for (JMenuItem item : items)
        menu.add(item); 
}

//返回接口,在这种情况下，调用者返回的对象是一个不可修改的列表。
List<]MenuItem> getAllItems(final JHenu menu) 
{
    return new
    AbstractList<>() 
    {
        public JMenuItem get(int i) 
        {
            return menu.getltem(i); 
        }
        public int size()
        {
            return menu.getltemCount(); 
        } 
    }; 
}
```

# 六、遗留的集合
在集合框架出现之前已经存在大量“遗留的” 容器类。这些类已经集成到集合框架中。
![遗留类](集合框架中的遗留类.png)

## 1. Hashtable 类
1. Hashtable 类与 HashMap 类的作用一样，它们拥有相同的接口。
2. Hashtable 和Vector类的方法是同步的，如果对同步性或与遗留代码的兼容性没有任何要求，就应该使用 HashMap。如果需要并发访问， 则要使用 ConcurrentHashMap。

## 2. 枚举（Enumeration）
```java
//遗留集合使用 Enumeration 接口对元素序列进行遍历。
Enumeration<Employee> e = staff.elements();
whi1e (e.HasMoreElements()) 
{
    Employee e = e.nextElement(); 
}

//遗留方法，其参数是枚举类型的。
List<InputStream> streams = ...;
SequenceInputStream in = new SequencelnputStream(Collections.enumeration(streams)); 
```

## 3. 属性映射（Properties）
属性映射是一个类型非常特殊的映射结构,通常用于程序的特殊配置选项。
- 键与值都是字符串。 
- 表可以保存到一个文件中，也可以从文件中加载。 
- 使用一个默认的辅助表。

```java
Properties() //创建一个空的属性映射。 
Properties(Properties defaults)  //创建一个带有一组默认值的空的属性映射。 
String getProperty(String key) //获得属性的对应关系；返回与键对应的字符串。 如果在映射中不存在，返回默认表中与这个键对应的字符串。 
String getProperty(String key, String defaultValue) //获得在键没有找到时具有的默认值属性；它将返回与键对应的字符串，如果在映射中不存在，就返回默认的字符串。 
void load(InputStream in)  //从 InputStream 加载属性映射。 
void store(OutputStream out, String commentstring) //把属性映射存储到 OutputStream。
```

## 4. 栈（Stack）
Stack类扩展于Vector类，Vector 类并不太令人满意，它可以让栈使用不属于栈操作的 insert 和 remove 方法，即可以在任何地方进行插入或删除操作，而不仅仅是在栈顶。
```java
E push(E item) //将 item 压入桟并返回 item。  
E pop() //弹出并返回栈顶的 item。如果栈为空，请不要调用这个方法。 
E peek() //返回栈顶元素，但不弹出。如果栈为空，请不要调用这个方法。
```

## 5. 位集（BitSet）
1. BitSet 类用于存放一个位序列（它不是数学上的集，称为位向量或位数组更为合适)。如果需要高效地存储位序列（例如，标志）就可以使用位集。
2. 由于位集将位包装在字节里，所以使用位集要比使用 Boolean 对象的 ArrayList 更加高效。

```java
bucketOfBits.get(i) //如果第 i 位处于“开” 状态，就返回 true; 否则返回 false。
bucketOfBits.set(i) //将第 i 位置为“开” 状态。
bucketOfBits.clear(i)  //将第 i 位置为“关” 状态。

BitSet(int initialCapacity) //创建一个位集。 
int length() //返回位集的“逻辑长度”， 即 1 加上位集的最高设置位的索引。
void and(BitSet set ) //这个位集与另一个位集进行逻辑“ AND”。 
void or(BitSet set ) //这个位集与另一个位集进行逻辑“ OR”。 
void xor(BitSet set ) //这个位集与另一个位集进行逻辑“ X0R” 异或。 
void andNot(BitSet set) //清除这个位集中对应另一个位集中设置的所有位。
```

