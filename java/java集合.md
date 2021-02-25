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

原理：**读写分离，写上锁，写完复制**

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

1）计算 key hash 值 

h = key.hashCode() 

hash = (h >>> 20) ^ (h >>> 12) ^  (h >>> 7) ^ (h >>> 4)

2）计算节点放的位置 —— hash & (n - 1)

3）判断当前位置 hashCode() （和 equals()）结果，相同时覆盖，不同时拉链法解决冲突（头插法）

> 头插法在多线程下存在链表成环的问题
>
> [HashMap 链表插入方式 → 头插为何改成尾插？](https://www.cnblogs.com/youzhibing/p/13915116.html)

JDK1.8 map.put(key, value) 流程

1）计算 key hash 值

hash = (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16)

> 异或 h>>>16 是为了让 hash 值的高 16 位也参与运算，**减少碰撞，数据分配均匀**。因为后面计算节点位置相与时高 16 位不参与计算。

2）计算节点放的位置 —— hash & (n - 1)

3）判断当前位置 hashCode() （和 equals()）结果，相同时覆盖，不同时拉链法（尾插法）或红黑树解决冲突

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

主要原因在于并发下的 Rehash 会造成元素之间会形成一个循环链表。

详情请查看：https://coolshell.cn/articles/9606.html

### HashMap 和 Hashtable 的区别?



[Java集合框架常见面试题](https://github.com/Snailclimb/JavaGuide/blob/master/docs/java/collection/Java%E9%9B%86%E5%90%88%E6%A1%86%E6%9E%B6%E5%B8%B8%E8%A7%81%E9%9D%A2%E8%AF%95%E9%A2%98.md)

https://github.com/CyC2018/CS-Notes/blob/master/notes/Java%20%E5%AE%B9%E5%99%A8.md#hashmap



