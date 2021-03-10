<!-- TOC -->

- [说说 List,Set,Map 三者的区别？](#说说-listsetmap-三者的区别)
- [ArrayList 和 Vector](#arraylist-和-vector)
    - [ArrayList 是线程安全的么？哪些是线程安全的？](#arraylist-是线程安全的么哪些是线程安全的)
- [ArrayList 和 LinkedList 的区别？](#arraylist-和-linkedlist-的区别)
- [ArrayList 的扩容机制？](#arraylist-的扩容机制)
- [HashMap 的底层实现？](#hashmap-的底层实现)
- [HashMap 的长度为什么是 2 的幂次方？](#hashmap-的长度为什么是-2-的幂次方)
- [HashMap 多线程操作导致死循环问题](#hashmap-多线程操作导致死循环问题)
- [HashMap 和 Hashtable 的区别?](#hashmap-和-hashtable-的区别)
- [HashMap 和 HashSet 的区别？](#hashmap-和-hashset-的区别)
- [HashMap 和 TreeMap 区别？](#hashmap-和-treemap-区别)
- [ConcurrentHashMap 线程安全的具体实现方式/底层具体实现？](#concurrenthashmap-线程安全的具体实现方式底层具体实现)
- [ConcurrentHashMap 和 Hashtable 的区别？](#concurrenthashmap-和-hashtable-的区别)
- [ArrayList 源码](#arraylist-源码)
- [HashMap 源码（1.8）](#hashmap-源码18)

<!-- /TOC -->

### 说说 List,Set,Map 三者的区别？

|      |   特性   |    是否有序     |         是否可重复         |
| :--: | :------: | :-------------: | :------------------------: |
| List |   顺序   |      有序       |           可重复           |
| Set  | 独一无二 |      无序       |          不可重复          |
| Map  |  键值对  | Key、Value 无序 | Key 不可重复、Value 可重复 |

### ArrayList 和 Vector

- Vector 使用 synchronized 进行同步 ——> 线程安全、执行速度慢。宜使用 ArrayList 由程序员进行同步操作
- 扩容大小：Vector 请求 2 倍容量，ArrayList 为 1.5 倍

——> 继续引出线程安全问题

#### ArrayList 是线程安全的么？哪些是线程安全的？

不是。源码中 `elementData[size++] = e;` 分为两步，`elementData[size]=e ; size++;` 这两步不上锁的情况下可能会出现值覆盖而有些元素值为 `null` 的情况。

**线程安全**

- 使用 `Vector `进行替换

`List arrayList = new ArrayList()` 替换为 `List arrayList = new Vector<>();`

原理：使用了 `synchronized` 关键字进行了加锁处理

```java
public synchronized boolean add(E e) {
        modCount++;
        ensureCapacityHelper(elementCount + 1);
        elementData[elementCount++] = e;
        return true;
 }
```

- 使用 `Collections.synchronizedList(List)` 进行替换

`List arrayList = Collections.synchronizedList(new ArrayList());`

原理：使用 `mutex对象`进行维护处理

```java
// SynchronizedList类如下
static class SynchronizedList<E> extends SynchronizedCollection<E> implements List<E> {
    // 其中的add方法如下
    public void add(int index, E element) {
        synchronized (mutex) {list.add(index, element);}
    }
}
```

- `java.util.concurrent `包中`CopyOnWriteArrayList` 类进行替换

`List arrayList = new CopyOnWriteArrayList<>();`

原理：**读写分离，写上锁，写完修改数组引用**

写操作在一个复制的数组上进行，读操作还是在原始数组中进行，读写分离，互不影响。写操作需要加锁，防止并发写入时导致写入数据丢失。写操作结束之后需要把原始数组指向新的复制数组。

使用场景：读多写少的应用场景

缺陷：不适合内存敏感以及对实时性要求很高的场景

1. 内存占用：在写操作时需要复制一个新的数组，使得内存占用为原来的两倍左右；
2. 数据不一致：读操作不能读取实时性的数据，因为部分写操作的数据还未同步到读数组中。

### ArrayList 和 LinkedList 的区别？

|            | ArrayList    | LinkedList                          |
| ---------- | ------------ | ----------------------------------- |
| 实现方式   | 基于动态数组 | 基于双向链表（JDK1.6 前为循环链表） |
| 访问速度   | 随机访问     | 需要从头开始查找元素                |
| 插入和删除 | 需要移动元素 | 不需要移动元素                      |
| 内存占用   | 较小         | 较大（每个元素存储更多信息）        |

PS：插入和删除受元素位置的影响，取决于操作的元素**离数组末端有多远**。对末端操作时间复杂度都是 O(1)，离末端越远 ArrayList 越耗时。

### ArrayList 的扩容机制？

|               操作                |                ArrayList 容量                |
| :-------------------------------: | :------------------------------------------: |
|             无参构造              |                      0                       |
|          插入第 1 个元素          |                  10（0—>10)                  |
|          插入第 2 个元素          |                      10                      |
|         插入第 11 个元素          |                15（10 × 1.5）                |
|        一次插入 20 个元素         |                 31（30 < 31)                 |
| 插入第 Integer.MAX_VALUE-9 个元素 | Integer.MAX_VALUE-8（MAX_ARRAY_SIZE 默认值） |
| 插入第 Integer.MAX_VALUE-7 个元素 |              Integer.MAX_VALUE               |

在 JDK1.8 中，如果通过无参构造的话，初始数组容量为 0，当真正对数组进行添加时（即添加第一个元素时），才真正分配容量，默认分配容量为 10；当容量不足时（容量为 size，添加第 size+1 个元素时），先判断按照 1.5 倍（位运算）的比例扩容能否满足最低容量要求，若能，则以 1.5 倍扩容，否则以最低容量要求进行扩容。

执行 add (E e) 方法时，先判断 ArrayList 当前容量是否满足 size+1 的容量；
在判断是否满足 size+1 的容量时，先判断 ArrayList 是否为空，若为空，则先初始化 ArrayList 初始容量为 10，再判断初始容量是否满足最低容量要求；若不为空，则直接判断当前容量是否满足最低容量要求；
若满足最低容量要求，则直接添加；若不满足，则先扩容，再添加。

ArrayList 的最大容量为 Integer.MAX_VALUE

[ArrayList 的扩容机制](https://www.cnblogs.com/zeroingToOne/p/9522814.html)

### HashMap 的底层实现？

JDK1.8 之前：数组和链表

JDK1.8 之后：数组和链表/红黑树

具体原则：当链表长度大于阈值（默认为 8）（将链表转换成红黑树前会判断，如果当前数组的长度小于 64，那么会选择先进行数组扩容，而不是转换为红黑树）时，将链表转化为红黑树，以减少搜索时间。

```java
// JDK1.7 hash() 方法
static int hash(int h) {
    h ^= (h >>> 20) ^ (h >>> 12);
    return h ^ (h >>> 7) ^ (h >>> 4);
}

// JDK1.8 hash() 方法
static final int hash(Object key) {
    int h;
    // key.hashCode()：返回散列值也就是hashcode
    // ^ ：按位异或
    // >>>:无符号右移，忽略符号位，空位都以0补齐
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
```

JDK1.7 map.put(key, value) 流程：

1）计算 key 的 hashCode 经过扰动函数得到 hash 值

h = key.hashCode() 

hash = (h >>> 20) ^ (h >>> 12) ^  (h >>> 7) ^ (h >>> 4)

2）hash 值与哈希表长度 -1 做与运算得到节点存放位置 —— hash & (n - 1)

3）判断当前位置 hashCode() （和 equals()）结果，相同时覆盖，不同时拉链法解决冲突（头插法）

> 头插法在多线程下存在链表成环的问题
>
> [HashMap 链表插入方式 → 头插为何改成尾插？](https://www.cnblogs.com/youzhibing/p/13915116.html)

JDK1.8 map.put(key, value) 流程

1）计算 key 的 hashCode 经过扰动函数得到 hash 值

hash = (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16)

> 异或 h>>>16 是为了让 hash 值的高 16 位也参与运算，**减少碰撞，数据分配均匀**。因为后面计算节点位置相与时高 16 位不参与计算。

2）hash 值与哈希表长度 -1 做与运算得到节点存放位置 —— hash & (n - 1)

3）判断当前位置 hashCode() （和 equals()）结果，相同时覆盖，不同时拉链法（尾插法）或红黑树解决冲突）

![JDK1.8 put 流程](https://camo.githubusercontent.com/6e61b336220f0690540fad2acc0d8c19106a32b278768582cb3e973a25a061b6/68747470733a2f2f6d792d626c6f672d746f2d7573652e6f73732d636e2d6265696a696e672e616c6979756e63732e636f6d2f323031392d372f7075742545362539362542392545362542332539352e706e67)

### HashMap 的长度为什么是 2 的幂次方？

求出 hash 值后需要计算存放的索引位置，考虑通过取余来实现，当 HashMap 的长度是 2 的幂次方时，将 hash 值与 n-1 做与运算就可以达到取余的效果，且运算效率更高。

```java
// Returns a power of two size for the given target capacity
static final int tableSizeFor(int cap) {
    int n = cap - 1;
    n |= n >>> 1;
    n |= n >>> 2;
    n |= n >>> 4;
    n |= n >>> 8;
    n |= n >>> 16;
    return (n < 0) ? 1 : (n >= MAXIMUM_CAPACITY) ? MAXIMUM_CAPACITY : n + 1;
}
```

[HashMap 源码详细分析 (JDK1.8)](https://segmentfault.com/a/1190000012926722)

[HashMap(JDK1.8)源码+底层数据结构分析](https://github.com/Snailclimb/JavaGuide/blob/master/docs/java/collection/HashMap(JDK1.8)%E6%BA%90%E7%A0%81+%E5%BA%95%E5%B1%82%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E5%88%86%E6%9E%90.md)

### HashMap 多线程操作导致死循环问题

并发下的 Rehash 会造成元素之间会形成一个循环链表。

详情请查看：https://coolshell.cn/articles/9606.html

### HashMap 和 Hashtable 的区别?

- 线程安全、效率

HashMap 线程不安全，HashTable 线程安全（内部方法使用 `synchronized`）

- NULL 值

 HashMap 可以存储 null 的 key 和 value，HashTable 不允许有 null 键和 null 值（抛出 NullPointerException）

- 迭代器

HashMap 使用 Iterator 迭代器，HashTable 使用 Enumerator 迭代器

- hash 值的计算

HahMap 将 key 的 hashCode() 和高 16 位异或，HashTable 直接使用 key 的 hashCode()

- 初始大小和扩容方式

HashMap 默认数组大小 16 且是懒加载机制，HashTable 默认数组大小 11

HahsMap 扩容大小为 2n，HashTable 为 2n+1

### HashMap 和 HashSet 的区别？

|          |          HashMap          |               HashSet                |
| :------: | :-----------------------: | :----------------------------------: |
| 实现接口 |         Map 接口          |               Set 接口               |
| 存储内容 |          键值对           |                 对象                 |
| 添加方法 |      put(key, value)      |                add(e)                |
| 对象比较 | 使用 key 计算 hash 值比较 | 使用 hashCode() 和 equals() 方法比较 |

### HashMap 和 TreeMap 区别？

TreeMap 主要多了对集合中的元素 `根据键排序的能力` 以及对集合内元素的`搜索的能力` 

[HashMap 和 TreeMap 区别](https://github.com/Snailclimb/JavaGuide/blob/master/docs/java/collection/Java%E9%9B%86%E5%90%88%E6%A1%86%E6%9E%B6%E5%B8%B8%E8%A7%81%E9%9D%A2%E8%AF%95%E9%A2%98.md#143-hashmap-%E5%92%8C-treemap-%E5%8C%BA%E5%88%AB)

### ConcurrentHashMap 线程安全的具体实现方式/底层具体实现？

**JDK1.7**

Segment 数组 + HashEntry 数组 + 链表；分段锁

ConcurrentHashMap 采用分段锁的方式，对整个桶数组进行了分割分段，分出很多 Segment，每个锁维护一个 Segment，这样操作不同的 Segment 的时候就互不影响，提高并发效率。

ConcurrentHashMap 是由 Segment 数组结构和 HashEntry 数组结构组成，Segment 实现了 ReentrantLock，HashEntry 用于存储键值对数据。

![jdk1.7 ConcurrentHashMap](https://camo.githubusercontent.com/5ac8db8f7ce3819adc3865ee5dc1232fb596471d7bfd3059445f5b0c75bc4d07/68747470733a2f2f6d792d626c6f672d746f2d7573652e6f73732d636e2d6265696a696e672e616c6979756e63732e636f6d2f323031392d362f436f6e63757272656e74486173684d61702545352538382538362545362541452542352545392539342538312e6a7067)

**JDK1.8**

Node 数组 + 链表 / 红黑树；CAS 和 synchronized

synchronized 锁粒度更低，优化空间更大（？）

在大量数据操作情况下，ReentrantLock 内存开销更大

![jdk1.8 ConcurrentHashMap](https://github.com/Snailclimb/JavaGuide/raw/master/docs/java/collection/images/java8_concurrenthashmap.png)



### ConcurrentHashMap 和 Hashtable 的区别？

**JDK1.7**

- 底层实现 ——> 执行效率

ConcurrentHashMap 是 Segment 数组 + HashEntry 数组 + 链表；HashTable 是和 HashMap 一样的 数组 + 链表结构。

ConcurrentHashMap 采用分段锁的方式分出多个 Segment，每个 Segment 之间互不影响，和 HashTable 这种通过 synchronized 实现全局锁的方式相比，并发效率更高。

**JDK1.8**

- 底层实现

ConcurrentHashMap 使用 Node 数组+链表+红黑树的数据结构来实现，并发控制使用 synchronized 和 CAS 来操作。

[Java集合框架常见面试题](https://github.com/Snailclimb/JavaGuide/blob/master/docs/java/collection/Java%E9%9B%86%E5%90%88%E6%A1%86%E6%9E%B6%E5%B8%B8%E8%A7%81%E9%9D%A2%E8%AF%95%E9%A2%98.md)

### ArrayList 源码

构造方法

```java
// 无参构造 
// 创建空数组 容量为 0
public ArrayList() {this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;}

// 传入 initialCapacity
// < 0 异常；== 0 创建空数组；> 0 新建长度为 initialCapacity 的 Object 数组.
public ArrayList(int initialCapacity) {
    if (initialCapacity > 0) {
        this.elementData = new Object[initialCapacity];
    } else if (initialCapacity == 0) {
        this.elementData = EMPTY_ELEMENTDATA;
    } else {
        throw new IllegalArgumentException("Illegal Capacity: "+
                                           initialCapacity);
    }
}

// 传入集合
// 集合长度为 0 创建空数组；不空复制集合元素到 elementData 数组
public ArrayList(Collection<? extends E> c) {
    elementData = c.toArray();
    if ((size = elementData.length) != 0) {
        if (elementData.getClass() != Object[].class)
            elementData = Arrays.copyOf(elementData, size, Object[].class);
    } else {
        this.elementData = EMPTY_ELEMENTDATA;
    }
}
```

add(e)

1. 计算容量 `ensureCapacityInternal(size + 1)`
2. 如果 `size + 1` 大于数组长度，调用 `grow()` 扩容
3. `oldCapacity` 右移变为 1.5 倍，如果小于 `size + 1`，`newCapacity = size + 1`
4. 拷贝，扩容，构建新数组

```java
public boolean add(E e) {
    ensureCapacityInternal(size + 1);  // Increments modCount!!
    elementData[size++] = e;//在数组末尾追加一个元素，并修改size
    return true;
}
    private static final int DEFAULT_CAPACITY = 10;
    private void ensureCapacityInternal(int minCapacity) {
        if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
            minCapacity = Math.max(DEFAULT_CAPACITY, minCapacity);
        }

        ensureExplicitCapacity(minCapacity);
    }


private void ensureExplicitCapacity(int minCapacity) {
    modCount++;//如果确定要扩容，会修改modCount 
    if (minCapacity - elementData.length > 0)
        grow(minCapacity);
}

private void grow(int minCapacity) {
    int oldCapacity = elementData.length;
    int newCapacity = oldCapacity + (oldCapacity >> 1);
    if (newCapacity - minCapacity < 0)
        newCapacity = minCapacity;
    if (newCapacity - MAX_ARRAY_SIZE > 0)
        newCapacity = hugeCapacity(minCapacity);
    elementData = Arrays.copyOf(elementData, newCapacity);
}
```

remove(int index)

1. 从头部删除，删除头结点然后移动后面的数据，最后一个元素置空。

2. 从中间指定位置删除，找到要删除数据的位置，删除后，后面的数据移动，最后一个元素置空。

3. 从尾部删除：直接删除尾部数据完成删除操作。

```java
public E remove(int index) {
    rangeCheck(index);//判断是否越界
    modCount++;//修改modeCount 因为结构改变了
    E oldValue = elementData(index);//读出要删除的值
    int numMoved = size - index - 1;
    if (numMoved > 0)
        System.arraycopy(elementData, index+1, elementData, index,
                         numMoved);//用复制 覆盖数组数据
    elementData[--size] = null; // clear to let GC do its work  //置空原尾部数据 不再强引用， 可以GC掉
    return oldValue;
}
```

### HashMap 源码（1.8）

构造方法

1. 传入初始容量 / 初始容量和加载因子
2. 传入键值对
3. 无参构造

```java
public HashMap(int initialCapacity) {this(initialCapacity, DEFAULT_LOAD_FACTOR);}
```

initialCapacity 初始容量（默认 16）

threshold 阈值 `threshold=initialCapacity*loadFactor` 构造方法中直接通过 `tableSizeFor (initialCapacity)` 方法进行了赋值，用来找到大于或等于 initialCapacity 的最小2的幂

loadFactor 加载因子（默认 0.75）

- put(key, value)

1. `put()` 调用 `putVal()` 方法
2. 判断 table 是否初始化，没有的话对数据进行初始化
3. 通过 hash 算法找到下标位置
   - 下标位置为空，直接存放数据
   - 发生碰撞
     - key 值相同，覆盖
     - key 值不同，红黑树 or 链表插入（是否转红黑树）

```java
public V put(K key, V value) {
    return putVal(hash(key), key, value, false, true);
}

final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
               boolean evict) {
    Node<K,V>[] tab; Node<K,V> p; int n, i;
    if ((tab = table) == null || (n = tab.length) == 0)
        //如果table尚未初始化，则此处进行初始化数组，并赋值初始容量，重新计算阈值
        n = (tab = resize()).length;
    if ((p = tab[i = (n - 1) & hash]) == null)
        //通过hash找到下标，如果hash值指定的位置数据为空，则直接将数据存放进去
        tab[i] = newNode(hash, key, value, null);
    else {
        //如果通过hash找到的位置有数据，发生碰撞
        Node<K,V> e; K k;
        if (p.hash == hash &&
            ((k = p.key) == key || (key != null && key.equals(k))))
            //如果需要插入的key和当前hash值指定下标的key一样，先将e数组中已有的数据
            e = p;
        else if (p instanceof TreeNode)
            //如果此时桶中数据类型为 treeNode，使用红黑树进行插入
            e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
        else {
            //此时桶中数据类型为链表
            // 进行循环
            for (int binCount = 0; ; ++binCount) {
                if ((e = p.next) == null) {
                    //如果链表中没有最新插入的节点，将新放入的数据放到链表的末尾
                    p.next = newNode(hash, key, value, null);

                    //如果链表过长，达到树化阈值，将链表转化成红黑树
                    if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                        treeifyBin(tab, hash);
                    break;
                }
                //如果链表中有新插入的节点位置数据不为空，则此时e 赋值为节点的值，跳出循环
                if (e.hash == hash &&
                    ((k = e.key) == key || (key != null && key.equals(k))))
                    break;
                p = e;
            }
        }

        //经过上面的循环后，如果e不为空，则说明上面插入的值已经存在于当前的hashMap中，那么更新指定位置的键值对
        if (e != null) { // existing mapping for key
            V oldValue = e.value;
            if (!onlyIfAbsent || oldValue == null)
                e.value = value;
            afterNodeAccess(e);
            return oldValue;
        }
    }
    ++modCount;
    //如果此时hashMap size大于阈值，则进行扩容
    if (++size > threshold)
        resize();
    afterNodeInsertion(evict);
    return null;
}
```

