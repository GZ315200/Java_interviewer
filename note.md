# hashCode()

- hashCode 是获取哈希码，也称作是散列码，它实际上是返回一个int整数。这个hash码是用来确定对象在哈希表中的索引位置。
- hashCode定义在JDK， Object.java中，所以这意味着任何类都包含有hashCode()函数。
- 散列表存储的是键值对(key - value), 它的特点是： 根据“键”快速的检索出对应的“值”。这其中就利用了散列码。


1. 如果两个对象相等，则hashcode一定也是相同的
2. 两个对象相等,对两个对象分别调用equals方法都返回true
3. 两个对象有相同的hashcode值，它们也不一定是相等的
4. 因此，equals 方法被覆盖过，则 hashCode 方法也必须被覆盖
5. hashCode() 的默认行为是对堆上的对象产生独特值。如果没有重写 hashCode()，则该 class 的两个对象无论如何都不会相等（即使这两个对象指向相同的数据）

---

# JVM 内存模型

## 程序计数器

- 内存空间较小，当前线程所执行的字节码的行号指示器。字节码解释器工作时，通过改变这个计数器的值来选取下一条需要执行的字节码指令，分支，循环，跳转，异常处理，线程恢复等功能都能依赖。线程隔离的，私有的

## Java虚拟机栈
- 与程序计数器一样，Java虚拟机栈也是线程私有的，为局部变量表，存放了编译器可知的各种数据类型，对象引用。

## 本地方法栈

-  和虚拟机堆栈所发挥的作用非常相似，区别是，虚拟机栈为虚拟机执行Java方法（也就是字节码）服务，而本地方法栈则为虚拟机使用到的Native方法服务。

## 堆

- Java内存的最大一块，Java堆是所有线程共享的一块区域，在虚拟机启动时创建。`此内存区域的唯一目的就是存放对象实例，几乎所有的对象实例以及数组都在这里分配`。`Java堆是垃圾收集器管理的主要区域，因此也称作GC堆`。从垃圾回收的角度，由于现在收集器基本都采用分代垃圾收集算法，所以Java堆还可以细分为：新生代和老生代：在细致一点：Eden空间，From Survivor、To Survivor空间。`进一步划分的目的是更好地回收内存，或者更快的分配内存。`

## 方法区

`方法区与Java堆一样，是各个线程共享，它用于存储已被虚拟机加载的类信息、常量、静态变量、即使编译器编译后的代码等数据。`
HotSpot虚拟机方法区也被称为“永久代”

## 运行时常量

运行时常量池是方法区的一部分。Class文件中除了有类的版本、字段、方法、接口等描述信息外，还有常量池信息（用于存放编译期生成的各种字面量和符号引用）

## 直接内存
直接内存并不是虚拟机运行时数据区的一部分，也不是虚拟机规范中定义的内存区域，但是这部分内存也被频繁地使用。而且也可能导致OutOfMemory错误出现

JDK1.4中新加入的NIO(New Input/Output)类，引入了一种基于通道（Channel） 与缓存区（Buffer） 的I/O方式，它可以直接使用Native函数库直接分配堆外内存，然后通过一个存储在java堆中的DirectByteBuffer对象作为这块内存的引用进行操作。这样就能在一些场景中显著提高性能，因为避免了在Java堆和Native堆之间来回复制数据。

`本机直接内存的分配不会收到Java堆的限制，但是，既然是内存就会受到本机总内存大小以及处理器寻址空间的限制。`

---

# 重点问题：
## 对象的创建：

1. 类加载检查

    虚拟机遇到一条new 指令时，首先去检查这个指令的参数是否能在常量池中定位到这个类的符号引用，并且检查这个符号引用代表的类是否已经被加载过、解析和初始化过。如果没有，那必须先执行相应的类加载过程。

2. 分配内存
    
    类加载检查通过后，接下来虚拟机为新生对象分配内存。对象所需的内存大小在类加载完成后便可确定，为对象分配空间的任务等同于把一块确定大小的内存从Java堆中划分出来。分配方式有“指针碰撞” 和 “空闲列表” 两种， 选择那种分配方式有Java堆是否规整决定，而Java 堆是否规整又由所采用的垃圾收集器是否带有压缩整理功能决定。

3. 初始化零值
4. 设置对象头
5. 执行init方法



### 介绍一下Java的内存模型

回答：
- Java内存模型有 程序计数器，本地方法栈， Java虚拟机栈，Java堆，方法区，运行时常量池，直接内存

### Java对象的创建过程



## JDK 1.8 之前的堆内存

Eden -> S1 -> S2 -> Old Generation -> Perm
|      新生代   |       老年代     |  |永久代|
|       Java程序可以使用的堆空间大小    |   
|             整个JVM堆空间大小             |



## 值得注意
在JDK1.8之后，移除永久代。取而代之的是一个叫元空间的区域（Metaspace）,
永久代使用的是JVM的堆内存空间，而元空间使用的是物理内存，直接受到本机的物理内存限制。





# 集合

- List 

    List 


- Set

    ``` java

    //此equals方法说明，如果两个元素地址相同则返回true，如果地址不同但内容相同也视为true
    public boolean equals(Object o) {
        //  比较当前对象和传入的对象地址是否相同
        if (o == this)
            return true;

        // 检查传入对象的类型是否为Set
        if (!(o instanceof Set))
            return false;

        // 强转为集合类型
        Collection<?> c = (Collection<?>) o;
        // 比较当前对象和传入对象的size大小是否相同
        if (c.size() != size())
            return false;
        try {
            // 比较当前对象与内容是否包含传入元素的内容
            return containsAll(c);
        } catch (ClassCastException unused)   {
            return false;
        } catch (NullPointerException unused) {
            return false;
        }
    }


    //将当前的所有元素遍历出来，把所有元素的hashcode相加，如果两个Set对象内容相同，则hashcode也相同

     public int hashCode() {
        int h = 0;
        Iterator<E> i = iterator();
        while (i.hasNext()) {
            E obj = i.next();
            if (obj != null)
                h += obj.hashCode();
        }
        return h;
    }


- LinkedList

- `remove ( obejct o)` 从此列表中移除首次出现的制定元素（如果存在）

- 适合实现栈和队列，是因为它底层继承了Deque接口

``` java
public class LinkedList<E>
    extends AbstractSequentialList<E>
    implements List<E>, Deque<E>, Cloneable, java.io.Serializable
```


- Vector

``` java
public class Vector<E>
    extends AbstractList<E>
    implements List<E>, RandomAccess, Cloneable, java.io.Serializable


    // Vector 增加值
     public synchronized boolean add(E e) {
        modCount++;
        ensureCapacityHelper(elementCount + 1);
        elementData[elementCount++] = e;
        return true;
    }

```

- 


- Hashtable

```java
public class Hashtable<K,V>
    extends Dictionary<K,V>
    implements Map<K,V>, Cloneable, java.io.Serializable {

// Hashtable put方法都是synchronized 线程安全的
        public synchronized V put(K key, V value) {
        // Make sure the value is not null
        if (value == null) {
            throw new NullPointerException();
        }

        // Makes sure the key is not already in the hashtable.
        Entry<?,?> tab[] = table;
        int hash = key.hashCode();
        int index = (hash & 0x7FFFFFFF) % tab.length;
        @SuppressWarnings("unchecked")
        Entry<K,V> entry = (Entry<K,V>)tab[index];
        for(; entry != null ; entry = entry.next) {
            if ((entry.hash == hash) && entry.key.equals(key)) {
                V old = entry.value;
                entry.value = value;
                return old;
            }
        }

        addEntry(hash, key, value, index);
        return null;
    }
```


- Properties

内部继承Hashtable，拥有hashtable的所有属性和方法
建议使用setProperty 进行值的增加

setProperty 是一个线程安全的方法。
 
   ``` java
    public synchronized Object setProperty(String key, String value) {
        return put(key, value);
    }
```

- TreeSet

``` java
public class TreeSet<E> extends AbstractSet<E>
    implements NavigableSet<E>, Cloneable, java.io.Serializable
{
```



- ArrayList 与 LinkedList的区别

    ArrayList 底层使用的是`数组`（`存储数据效率高，插入删除特定位置效率低`），LinkedList底层使用的是`双向循环链表数据结构`（插入、删除效率高）。采用链表存储，插入、删除元素时间复杂度不受元素位置的影响，都是近似O(1) 而数组近似o(n),因此数据特别多，而且经常性的插入删除元素建议使用LinkedList，一般程序只用ArrayList就够了。

- ArrayList 与 Vector 的区别

    Vector类的所有方法都是线程同步的，所以可以用多个线程去访问Vector对象。代码在同步操作的时候消耗的时间较长。ArrayList则不同

- HashMap 和 ConcurrentHashMap区别
    1. ConcurrentHashMap对整个桶数组进行了分割分段(Segment)，然后在每一个分段上都用lock锁进行保护，相对于Hashtable的synchronized锁的粒度更精确了一些，并发性能更好，而HashMap没有锁的机制，不是线程安全的。
    （JDK1.8之后的ConcurrentHashMap启用了一种全新的方式实现，利用CAS算法）
    2. HashMap的键值对允许null值，但是ConcurrentHashMap都不允许。


### HashSet如何检查重复

    当把对象存入HashSet时，HashSet会先计算对象的hashcode值来判断对象的加入位置，同时也会与其他加入的对象的hashcode值做比较，如果没有相符的hashcode，HashSet会假设对象没有重复出现。但是如果发现有相同hashcode值的对象，这时会调用equals()方法来检查hashcode相等的对象是否真的相同。如果两者相同，HashSet就不会让加入操作成功。

 ### hashCode() 与 equals() 规定

    1. 如果两个对象相等，则hashcode一定相同
    2. 如果对象相等，两个equals方法比较返回true
    3. 两个对象有相同的hashcode值，它们也不一定是相等的。
    4. 综上，equals方法被覆盖过，hashcode方法也必须被覆盖
    5. hashcode的默认行为是对堆上的对象产生独特值。如果没有重写hashcode，则该class的两个对象无论如果都不会相等。

###  == 与 equals区别
    1. == 是判断两个变量或实例是不是指向同一个内存空间
    2. equals是判断两个变量或实例所指向的内存空间的值是不是相同
    3. == 是指对内存地址进行进行比较
    4. equals是对字符串的内容进行比较
    5. == 指引用是否相同
    6. equals判断值是否相同

 
 ### Comparable 和 comparator区别

    1. Comparable接口实际出自java.lang包 它有一个compareTo(Object obj)方法来排序
    2. comparator接口实际上是出自java.util 它有一个compare(Object o1,object o2 )方法进行排序。

    一般我们需要对一个集合使用自定义排序时，我们需要重写compareTo方法或compare方法，当我们需要对某一个集合实现两种排序方式，


### 如何对Object的list排序
- 对objets数组进行排序，我们可以Arrays.sort()方法
- 对objects的集合进行排序，使用Collections.sort()方法




### TreeMap： 红黑树（自平衡的排序二叉树）， 可被排序

### 最好在add大量元素之前用 `ensureCapacity` 方法，以减少增量重新分配的次数

### Arrays.copyOf()方法主要是为了给原有数组扩容

### System.arraycopy() 需要目标数组，将原数组拷贝到你自己定义的数组里或者原数组，而且可以选择拷贝的起点和长度以及放入新数组中的位置 

### copyOf() 是系统自动在内部新建一个数组，并返回该数组。


---


## `Synchronized` 关键字的使用、底层原理、JDK1.6之后的底层优化以及和ReenTrantLock的对比

### Synchronized 关键字最主要的三种使用方式总结

- 修饰实例方法，作用与当前对象实例加锁，进入同步代码前要获取当前对象实例的锁。
- 修饰静态方法，作用于当前类对象加锁，进入同步代码前要获得当前类对象的锁。
- 修饰代码块，指定加锁对象，给定对象加锁，进入同步代码库前要获得给定对象的锁。但是`synchronized(class)`代码块上是给Class类上锁。



### 使用双重校验锁写单例模式

``` java
public class Singleton {
     //声明成 volatile
    private volatile static Singleton instance; 
   
    private Singleton (){}

    public static Singleton getSingleton() {
        if (instance == null) {                         
            synchronized (Singleton.class) {
                if (instance == null) {       
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
   
}
```
` instance = new Singleton();`代码分三步走。
1. 为instance分配地址
2. 初始化instance
3. 将instance指向分配的内存地址

JVM具有指令重新排序的特性。在多线程情况下，比如，线程T1执行了1和3，此时T2调用getSingleton()方法后发现instance不为空，因此返回instance，但此时instance还未被初始化。

`volatile` 修饰的变量存在先行发生原则，也就是说它的写操作都先行发生于它的读操作。

    使用 volatile 的主要原因是其另一个特性：禁止指令重排序优化。也就是说，在 volatile 变量的赋值操作后面会有一个内存屏障（生成的汇编代码上），读操作不会被重排序到内存屏障之前。比如上面的例子，取操作必须在执行完 1-2-3 之后或者 1-3-2 之后，不存在执行到 1-3 然后取到值的情况。从「先行发生原则」的角度理解的话，就是对于一个 volatile 变量的写操作都先行发生于后面对这个变量的读操作（这里的“后面”是时间上的先后顺序）。


##  Synchronized 底层原理

### `Synchronized` 同步语句块

1. `Synchronized`同步语句块的实现使用的`monotroenter`和`monitorexit`执行。其中，`monotroenter`指令指向同步代码块的开始位置，`monitorexit`指令则指明同步代码块的结束位置。
### `Synchronized` 修饰方法的情况：

`Synchronized`修饰的方法并没有`monotroenter`和`monitorexit`指令，取而代之的却是`ACC_SYNCHRONIZED`标识，该标识指明了该方法是一个同步方法，JVM通过`ACC_SYNCHRONIZED`访问标识来辨别一个方法是否申明为同步方法，从而执行相应的同步调用。


### JDK1.6之后的底层优化

1. 偏向锁： 偏向锁在无竞争的情况下会把整个同步都消除掉
2. 轻量级锁： 在没有多线程竞争的前提下，减少传统的重量级锁使用操作系统互斥量产生的性能消耗，因为使用轻量级锁时，不需要申请互斥量。另外，轻量级的加锁和解锁都用到了CAS操作。
3. 自旋锁和自适应自旋：一般线程持有锁的时间都是不长，所以仅仅为了这一点时间去挂起线程/恢复线程得不偿失。为了让一个线程等待，我们只需要让线程执行一个忙循环（自旋），。在JDK1.6中引入了自适应的自旋锁。自适应的自旋锁带来的改进就是：自选锁的时间不在固定了，而是和前一次同一个锁上的自旋时间以及锁的拥有者的状态来决定。


### Synchronized 和 ReenTrantLock的对比

- 两者都是可重入锁
- Synchronized 依赖于JVM而ReenTrantLock依赖于API
- 相比于Synchronized， ReenTrantLock 增加了一些高级功能。主要来说主要有三点

1. 等待可中断
2. 可实现公平锁
3. 可实现选择通知（锁可以绑定多个条件）


`线程对象可以注册在指定的Condition中，从而可以有选择性的进行线程通知，在调度线程上更加灵活。 在使用notify/notifyAll()方法进行通知时，被通知的线程是由 JVM 选择的，用ReentrantLock类结合Condition实例可以实现“选择性通知”`


### 如果执行notifyAll()方法的话就会通知所有处于等待状态的线程这样会造成很大的效率问题

### Condition实例的signalAll()方法 只会唤醒注册在该Condition实例中的所有等待线程。


## 对象的创建

类加载检查 --> 分配内存 --> 初始化零值 --> 设置对象头 --> 执行init()方法



### 类加载检查

    虚拟机遇到一条new指令时，首先检查这个指令的参数是否能在常量池中定位到这个类的符号引用，并且检查这个符号引用代表的类是否已经被加载过、解析和初始化过。如果没有，那必须执行相应的类加载过程。

### 分配内存

类加载检查后，虚拟机将为新生对象分配内存。分配方式有“`指针碰撞`” 和 “`空闲列表`”两种，选择哪种分配方式`由Java堆是否规整决定`，而`Java堆是否规整又由所采用的垃圾收集器是否带有压缩整理功能决定`。



## 内存分配的两种方式：

1. 指针碰撞：

    适用场合，堆内存规整（即没有内存碎片）

-  原理

    用过的内存全部整合到一边，没有用过的内存放到一边，中间有一个分界值指针，只需要向着没有用过的内存方向将该指针移动到对象内存大小位置即可。
- GC收集器
    Serial、ParNew

2. 空闲列表
    适用场合：堆内存不规整的情况下
    - 原理：虚拟机会维护一个列表，该列表中会记录哪些内存块是可用的，在分配的时候，找一块足够大的内存块来划分给对象实例，然后更新列表记录。
    - GC收集器
    CMS

## 内存分配并发问题


CAS操作保证数据更新操作的原子性， CompareAndSwap()

- `CAS+失败重试`
    
    CAS是乐观锁的一种实现方式。所谓乐观锁就是，每次不加锁而是假设没有冲突而去完成某项操作，如果因为冲突失败就重试，直到成功为止。`虚拟机采用 CAS 配上失败重试的方式保证更新操作的原子性。`
- `TLAB`: 

    为每一个线程预先在Eden区分配一块内存，JVM在给线程中的对象分配内存时，首先在TLAB分配，当对象大于TLAB中的剩余内存或TLAB的内存已经用尽时，再采用上述的CAS进行内存分配


轻量级, `它的本意是在没有多线程竞争的前提下，减少传统的重量级锁使用操作系统互斥量产生的性能消耗，因为使用轻量级锁时，不需要申请互斥量。`

## 对象的访问定位

对象的使用方式由虚拟机实现而定，主流方式：

1. 使用句柄
2. 直接指针


## 垃圾回收算法

1. 标记-清除算法

    算法分为“标记”和“清除”阶段：首先标记出所有需要回收的对象，在标记完成后统一回收所有被标记的对象。它是最基础的收集算法，效率也很高，但是会带来两个明显的问题：

- 效率问题
- 空间问题（标记清除后会产生大量不连续的碎片）

2. 复制算法

    为了解决效率问题，“复制”收集算法出现了。它可以将内存分为大小相同的两块，每次使用其中的一块。当这一块的内存使用完后，就将还存活的对象复制到另一块去，然后再把使用的空间一次清理掉。这样就使每次的内存回收都是对内存区间的一半进行回收。

3. 标记-整理算法

    根据老年代的特点特出的一种标记算法，标记过程仍然与“标记-清除”算法一样，但后续步骤不是直接对可回收对象回收，而是让所有存活的对象向一端移动，然后直接清理掉端边界以外的内存。

4. 分代收集算法

    `比如在新生代中，每次收集都会有大量对象死去，所以可以选择复制算法，只需要付出少量对象的复制成本就可以完成每次垃圾收集。而老年代的对象存活几率是比较高的，而且没有额外的空间对它进行分配担保，所以我们必须选择“标记-清除”或“标记-整理”算法进行垃圾收集。`



---

# Zookeeper概览

`Zookeeper的设计目标是将那些复杂且容易出错的分布式一致性服务封装起来，构建一个高效可靠的原语集，并以一系列简单易用的接口提供给用户使用。`


Zookeeper是一个典型的分布式数据一致性解决方案，分布式应用程序可以基于Zookeeper实现诸如数据发布/订阅、负载均衡、命名服务、分布式协调/通知、集群管理、Master选举、分布式锁和分布式队列等功能。

典型案例，`担任服务生产者和服务消费者的注册中心(提供发布订阅服务)`

zookeeper 1.集群，容错、负载均衡 2. 配置文件的集中管理 3. 集群入口

zookeeper 底层提供两个功能， 
1. 管理（存储，读取）用户程序提交的数据。
2. 为用户程序提供数据节点监听服务

