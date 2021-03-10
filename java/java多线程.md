<!-- TOC -->

- [为什么程序计数器、虚拟机栈和本地方法栈是线程私有的呢？为什么堆和方法区是线程共享的呢？](#为什么程序计数器虚拟机栈和本地方法栈是线程私有的呢为什么堆和方法区是线程共享的呢)
- [多线程的好处？](#多线程的好处)
- [多线程可能的问题](#多线程可能的问题)
- [线程的生命周期和状态](#线程的生命周期和状态)
- [上下文切换？](#上下文切换)
- [线程死锁](#线程死锁)
  - [死锁情形和条件](#死锁情形和条件)
  - [如何避免死锁？](#如何避免死锁)
- [sleep() 方法和 wait() 方法区别和共同点?](#sleep-方法和-wait-方法区别和共同点)
- [为什么我们调用 start() 方法时会执行 run() 方法，为什么我们不能直接调用 run() 方法？](#为什么我们调用-start-方法时会执行-run-方法为什么我们不能直接调用-run-方法)
- [synchronized 关键字](#synchronized-关键字)
  - [介绍一下 synchronized 关键字](#介绍一下-synchronized-关键字)
  - [synchronized 关键字的使用](#synchronized-关键字的使用)
  - [讲一下 synchronized 关键字的底层原理](#讲一下-synchronized-关键字的底层原理)
  - [谈谈 synchronized 和 ReentrantLock 的共同点和区别？](#谈谈-synchronized-和-reentrantlock-的共同点和区别)
- [volatile 关键字作用](#volatile-关键字作用)
- [并发编程的三个重要特性](#并发编程的三个重要特性)
- [说说 synchronized 关键字和 volatile 关键字的区别](#说说-synchronized-关键字和-volatile-关键字的区别)
- [ThreadLocal](#threadlocal)
  - [简介和应用](#简介和应用)
  - [ThreadLocal 原理](#threadlocal-原理)
  - [ThreadLocal 内存泄露问题](#threadlocal-内存泄露问题)
- [线程池](#线程池)
  - [为什么要用线程池（好处）？](#为什么要用线程池好处)
  - [线程池的创建](#线程池的创建)
  - [设计与实现](#设计与实现)
- [实现 Runnable 接口和 Callable 接口的区别](#实现-runnable-接口和-callable-接口的区别)
- [执行 execute()方法和 submit()方法的区别是什么呢？](#执行-execute方法和-submit方法的区别是什么呢)
- [AtomicInteger 类的原理](#atomicinteger-类的原理)
- [AQS](#aqs)
  - [AQS 介绍](#aqs-介绍)
  - [AQS 原理](#aqs-原理)
  - [AQS 对资源的共享方式](#aqs-对资源的共享方式)
  - [AQS 执行流程](#aqs-执行流程)
  - [AQS 组件总结](#aqs-组件总结)

<!-- /TOC -->
### 为什么程序计数器、虚拟机栈和本地方法栈是线程私有的呢？为什么堆和方法区是线程共享的呢？

程序计数器：为了线程切换后能恢复到正确的执行位置

虚拟机栈和本地方法栈：保证线程中的局部变量不被别的线程访问到

堆和方法区是所有线程共享的资源，其中堆是进程中最大的一块内存，主要用于存放**新创建的对象** (几乎所有对象都在这里分配内存)，方法区主要用于存放**已被加载的类信息、常量、静态变量**、即时编译器编译后的代码等数据。

### 多线程的好处？

- 线程可以看作是轻量级的进程，线程间的切换和调度的成本远远小于进程。
- 一个进程中可以一个线程进行 IO 操作，一个线程执行 CPU 计算，提高 CPU 和 IO 设备的综合利用率

### 多线程可能的问题

并发编程并不总是能提高程序运行速度

内存泄漏、死锁、线程不安全等

### 线程的生命周期和状态

新建、运行、阻塞、等待、超时等待、终止

![线程状态](https://camo.githubusercontent.com/d87b3b516a8138ca3a8a1078d46d63cb25caba590ab0142721014c1949fffc0b/68747470733a2f2f6d792d626c6f672d746f2d7573652e6f73732d636e2d6265696a696e672e616c6979756e63732e636f6d2f31392d312d32392f4a6176612545372542412542462545372541382538422545372539412538342545372538412542362545362538302538312e706e67)

线程在生命周期中并不是固定处于某一个状态而是随着代码的执行在不同状态之间切换。

![状态切换](https://camo.githubusercontent.com/9b25cbe08239220fce6606a1d8755c1361ad3c96f90e76887b582d4ed7e453c5/68747470733a2f2f6d792d626c6f672d746f2d7573652e6f73732d636e2d6265696a696e672e616c6979756e63732e636f6d2f31392d312d32392f4a6176612b2545372542412542462545372541382538422545372538412542362545362538302538312545352538462539382545382542462538312e706e67)

由上图可以看出：线程创建之后它将处于 **NEW（新建）** 状态，调用 `start()` 方法后开始运行，线程这时候处于 **READY（可运行）** 状态。可运行状态的线程获得了 CPU 时间片（timeslice）后就处于 **RUNNING（运行）** 状态。

当线程执行 `wait()`方法之后，线程进入 **WAITING（等待）** 状态。进入等待状态的线程需要依靠其他线程的通知才能够返回到运行状态，而 **TIME_WAITING(超时等待)** 状态相当于在等待状态的基础上增加了超时限制，比如通过 `sleep（long millis）`方法或 `wait（long millis）`方法可以将 Java 线程置于 TIMED WAITING 状态。当超时时间到达后 Java 线程将会返回到 RUNNABLE 状态。当线程调用同步方法时，在没有获取到锁的情况下，线程将会进入到 **BLOCKED（阻塞）** 状态。线程在执行 Runnable 的`run()`方法之后将会进入到 **TERMINATED（终止）** 状态。

### 上下文切换？

在同一时刻一个 CPU 只能被一个线程使用，CPU 采用时间片轮转的形式供线程使用。所以线程切换的时候需要当前线程保存自己的状态，得到时间片的线程加载之前保存的状态。任务从保存到再加载的过程就是一次上下文切换。

### 线程死锁

#### 死锁情形和条件

多个线程同时被阻塞，它们中的一个或者全部都在等待某个资源被释放。

四个条件：互斥、不剥夺、请求和保持、循环等待

#### 如何避免死锁？

破坏其中一个条件即可

1. 破坏互斥条件 ：这个条件我们没有办法破坏，因为我们用锁本来就是想让他们互斥的（临界资源需要互斥访问）。
2. 破坏不剥夺条件 ：申请不到资源，主动释放
3. 破坏请求与保持条件 ：一次性申请所有的资源。
4. 破坏循环等待条件 ：按某一顺序申请资源，释放资源则反序释放。两个线程都按顺序申请 1, 2；释放按照 2, 1 释放。这样另一个线程得不到 1 就一直等待

###  sleep() 方法和 wait() 方法区别和共同点?

**共同点：**

两者都可以暂停线程的执行

**区别：**

`sleep()` 方法没有释放锁；而 `wait()` 方法释放了锁

`sleep() `通常被用于暂停执行；`wait()` 通常用于线程间交互/通信

`sleep() `方法执行完成后，线程会自动苏醒；`wait()` 方法没有指定时间时，调用后线程不会自动苏醒，需要别的线程调用同一个对象上的 `notify() `或者 `notifyAll()` 方法。`wait()` 方法指定时间的话，超时后线程会自动苏醒。

### 为什么我们调用 start() 方法时会执行 run() 方法，为什么我们不能直接调用 run() 方法？

调用 `start()` 方法方可启动线程并使线程进入就绪状态，直接执行 `run()` 方法的话不会以多线程的方式执行。

### synchronized 关键字

#### 介绍一下 synchronized 关键字

`synchronized`关键字可以保证被它修饰的**方法**或者**代码块**在任意时刻只能有一个线程执行，用来解决多线程之间访问资源的同步性。

 Java 早期版本中属于**重量级锁**，监视器锁（monitor）是依赖于底层的操作系统的 `Mutex Lock` 实现，如果要挂起或者唤醒一个线程都需要操作系统从用户态转换到内核态，耗费时间较多。在 Java 6 之后官方从 JVM 层面对 `synchronized` 进行较大优化，如自旋锁、偏向锁、轻量级锁、（适应性自旋锁、锁消除、锁粗化）等技术来减少锁操作的开销。

#### synchronized 关键字的使用

synchronized 关键字最主要的三种使用方式：

1. 修饰实例方法：获取当前对象实例的锁 `synchronized void method() {}`
2. 修饰静态方法：获得当前 class 的锁 `synchronized static void method() {}`
3. 修饰代码块：获得当前对象实例 OR class 的锁 `synchronized(this | object) {}`

- `synchronized` 关键字加到 `static` 静态方法和 `synchronized(class)` 代码块上都是是给 Class 类上锁。
- `synchronized` 关键字加到实例方法上是给对象实例上锁。

> 锁住对象和锁住类的区别
>
> 锁住对象只能保证其他线程不会拿到该对象的锁，但是可以拿到类的其他对象的锁
>
> 锁住类相当于全局锁，该类的所有对象都被上锁

[Java 线程同步：synchronized 锁住的是代码还是对象（代码示例）](https://blog.csdn.net/xiao__gui/article/details/8188833)

**手写单例模式双重检验锁方式实现**

```java
public class Singleton {
    // 使用 volatile 可以禁止 JVM 的指令重排，保证在多线程环境下也能正常运行。
    private volatile static Singleton uniqueInstance;

    private Singleton() {}

    public  static Singleton getUniqueInstance() {
       //先判断对象是否已经实例过，没有实例化过才进入加锁代码
        if (uniqueInstance == null) {
            //类加锁
            synchronized (Singleton.class) {
                if (uniqueInstance == null) {
                    uniqueInstance = new Singleton();
                }
            }
        }
        return uniqueInstance;
    }
}
```

#### 讲一下 synchronized 关键字的底层原理

- 修饰方法 `public synchronized void method() {}`

使用 `ACC_SYNCHRONIZED` 标识，指明了该方法是一个同步方法。JVM 通过该 `ACC_SYNCHRONIZED` 访问标志来辨别一个方法是否声明为同步方法，从而执行相应的同步调用。

- 修饰代码块 `synchronized (this) {}`

使用是 `monitorenter` 和 `monitorexit` 指令，其中 `monitorenter` 指令指向同步代码块的开始位置，`monitorexit` 指令则指明同步代码块的结束位置。

执行`monitorenter`时，会尝试获取对象的锁，由 0 -> 1；执行 `monitorexit` 指令后，将锁计数器设为 0

**两者的本质都是对对象监视器 monitor 的获取**

#### 谈谈 synchronized 和 ReentrantLock 的共同点和区别？

**共同点：**

两者都是**可重入锁**（自己可以再次获取自己的内部锁，获取时锁的计数器自增 1）

**区别：**

1. synchronized 依赖于 JVM；ReentrantLock 依赖于 API（需要 lock() 和 unlock() 方法配合 try/finally 语句块来完成）

2. ReentrantLock 比 synchronized 增加了一些高级功能

   - **等待可中断** 

   通过 `lock.lockInterruptibly()` 正在等待的线程可以选择放弃等待

   - **可实现公平锁** 

    `ReentrantLock`可以指定是公平锁还是非公平锁（通过构造方法）。而`synchronized`只能是非公平锁

   - **可实现选择性通知（锁可以绑定多个条件？）**

   `synchronized`关键字与`wait()`和`notify()`/`notifyAll()`方法相结合可以实现等待/通知机制。`ReentrantLock`类当然也可以实现，但是需要借助于`Condition`接口与`newCondition()`方法。

![](https://p0.meituan.net/travelcube/412d294ff5535bbcddc0d979b2a339e6102264.png)

### volatile 关键字作用

1. 防止 JVM 的指令重排 
2. 保证变量的可见性（在每次访问变量时都会进行一次刷新，因此每次访问都是主存中最新版本）

### 并发编程的三个重要特性

- 原子性（要么都执行，要么都不执行。`synchronized` 可以保证代码片段的原子性。）
- 可见性（当一个线程对共享变量进行了修改，其他线程都是可以看到修改后的最新值。`volatile` 关键字可以保证共享变量的可见性。）
- 有序性（代码在执行的过程中的先后顺序。`volatile` 关键字可以禁止指令进行重排序优化。）

### 说说 synchronized 关键字和 volatile 关键字的区别

- `volatile` 关键字不会进行加锁操作，是线程同步的**轻量级实现**，性能比 `synchronized` 好
- `volatile` 关键字只能用于变量而 `synchronized` 关键字可以修饰方法以及代码块
- `volatile` 关键字能保证数据的可见性，但不能保证数据的原子性。`synchronized` 关键字两者都能保
- `volatile`关键字主要用于解决变量在多个线程之间的可见性，而 `synchronized` 关键字解决的是多个线程之间访问资源的同步性。

### ThreadLocal

> ThreadLocal 类似于监考的考官，线程类似于考生，考官发放试卷之后每个考生有一个自己的副本，考生无法看其他人的试卷。

#### 简介和应用

`ThreadLocal` 是线程的内部存储类，用于存储线程的局部变量。

#### ThreadLocal 原理

`Thread` 类源代码有一个 `threadLocals` 和一个 `inheritableThreadLocals` 变量，都是 `ThreadLocalMap` 类型。当前线程调用 `ThreadLocal` 类的 `set`或`get`方法时，调用的是`ThreadLocalMap`类对应的 `get()`、`set()`方法。

最终的变量是放在了当前线程的 `ThreadLocalMap` 中，`ThreadLocal`为 key ，Object 对象为 value

#### ThreadLocal 内存泄露问题

`ThreadLocalMap` 中使用的 key 为 `ThreadLocal` 的弱引用,而 value 是强引用。如果 `ThreadLocal` 没有被外部强引用的情况下，在垃圾回收的时候，key 会被清理掉，而 value 不会被清理掉。这样就会造成内存泄露。

`ThreadLocalMap` 在调用 `set()`、`get()`、`remove()` 方法的时候，会清理掉 key 为 null 的记录。使用完 `ThreadLocal`方法后 最好手动调用`remove()`方法。

### 线程池

> 是什么？一种基于池化思想的线程管理工具；实现（实现类源码、任务提交和任务执行解耦）
>
> 为什么？3 点好处
>
> 怎么用？维护线程周期（ctl）；管理线程（线程执行流程）；管理任务（任务执行流程）

线程池（Thread Pool）是一种基于池化思想的管理线程的工具。

线程池的核心实现类是 `ThreadPoolExecutor`，一方面**维护自身的生命周期**，另一方面**管理线程和任务**。

线程池在内部实际上构建了一个生产者消费者模型，将**线程**和**任务**两者解耦，从而良好的缓冲任务，复用线程。-> 执行流程

![线程池原理](https://camo.githubusercontent.com/e3c8d64487baa01192ccb6d1023c9d2b231c20c5067dbb8d53511889325c44d7/68747470733a2f2f6d792d626c6f672d746f2d7573652e6f73732d636e2d6265696a696e672e616c6979756e63732e636f6d2f323031392d372f2545352539422542452545382541372541332545372542412542462545372541382538422545362542312541302545352541452539452545372538452542302545352538452539462545372539302538362e706e67)

![ThreadPool](https://img-blog.csdnimg.cn/2018122411100636)

#### 为什么要用线程池（好处）？

- **降低资源消耗**。线程的创建和销毁都需要消耗系统资源，使用线程池重复利用已经创建的线程可以降低资源消耗。
- **提高响应速度**。当任务到达时，任务可以不需要等到线程创建就能立即执行。
- **提高线程的可管理性**。线程是稀缺资源，如果无限制的创建，不仅会消耗系统资源，还会降低系统的稳定性，使用线程池可以进行统一的分配，调优和监控。

#### 线程池的创建

- newFixedThreadPool()
- newSingleThreadExecutor()
- newScheduledThreadPool()
- newCachedThreadPool()

`ThreadPoolExecutor(int corePoolSize, int maximumPoolSize, 
long keepAliveTime, TimeUnit unit, BlockingQueue<Runnable> workQueue, 
ThreadFactory threadFactory, RejectedExecutionHandler handler)`

#### 设计与实现

**总体设计**

![继承关系](https://p1.meituan.net/travelcube/912883e51327e0c7a9d753d11896326511272.png)

顶层接口 Executor 提供了一种思想：将任务提交和任务执行进行解耦。用户只需提供 Runnable 对象，将任务的运行逻辑提交到执行器 (Executor) 中，由 Executor 框架完成线程的调配和任务的执行部分。

ThreadPoolExecutor 将会一方面**维护自身的生命周期**，另一方面同时**管理线程和任务**，使两者良好的结合从而执行并行任务。

![运行流程](https://p0.meituan.net/travelcube/77441586f6b312a54264e3fcf5eebe2663494.png)

线程池在内部实际上构建了一个**生产者消费者模型**，将线程和任务两者解耦，从而良好的缓冲任务，复用线程。

**生命周期管理**

AtomicInteger 类型的 `ctl` 变量对**线程池的运行状态**和**线程池中有效线程数量**进行控制。高 3 位保存 runState，低 29 位保存 workerCount

![运行状态](https://p0.meituan.net/travelcube/62853fa44bfa47d63143babe3b5a4c6e82532.png)

![线程池生命周期](https://p0.meituan.net/travelcube/582d1606d57ff99aa0e5f8fc59c7819329028.png)

**任务管理**

所有任务的调度都是由 `execute` 方法完成的。检查现在线程池的状态，决定接下来执行的流程

![任务调度](https://p0.meituan.net/travelcube/31bad766983e212431077ca8da92762050214.png)

阻塞队列的分类

![阻塞队列](https://p0.meituan.net/travelcube/725a3db5114d95675f2098c12dc331c3316963.png)

`getTask` 方法帮助线程从阻塞队列中获取任务，实现线程管理模块和任务管理模块之间的通信

![获取任务流程](https://p0.meituan.net/travelcube/49d8041f8480aba5ef59079fcc7143b996706.png)

任务拒绝策略

![任务拒绝](https://p0.meituan.net/travelcube/9ffb64cc4c64c0cb8d38dac01c89c905178456.png)

**线程管理**

工作线程 `Worker `**掌握线程的状态**并**维护线程的生命周期**

```java
private final class Worker extends AbstractQueuedSynchronizer implements Runnable{
    final Thread thread;//Worker持有的线程 可用来执行任务
    Runnable firstTask;//初始化的任务，可以为null
}
```

实现了 Runnable 接口，并持有一个线程 thread，一个初始化的任务 firstTask。

线程池使用一张 Hash 表去持有线程的引用，这样可以通过添加引用、移除引用这样的操作来控制线程的生命周期。

线程回收

Worker 是通过继承 AQS，使用 AQS 来实现独占锁这个功能。

线程回收的工作是在 `processWorkerExit` 方法完成的

线程池在执行 `shutdown` 方法或 `tryTerminate` 方法时会调用 interruptIdleWorkers 方法来中断空闲的线程，interruptIdleWorkers 方法会使用 tryLock 方法来判断线程池中的线程是否是空闲状态；如果线程是空闲状态则可以安全回收。

![线程回收](https://p1.meituan.net/travelcube/9d8dc9cebe59122127460f81a98894bb34085.png)

线程增加

通过线程池中的 `addWorker` 方法，该步骤仅仅完成增加线程，并使它运行，最后返回是否成功这个结果。

`addWorker` 方法有两个参数：`firstTask`、`core`。firstTask 参数用于指定新增的线程执行的第一个任务，该参数可以为空；core 参数为 true 表示在新增线程时会判断当前活动线程数是否少于 corePoolSize，false 表示新增线程前需要判断当前活动线程数是否少于 maximumPoolSize

执行任务

在 Worker 类中的 `run` 方法调用了 `runWorker` 方法来执行任务

1.while 循环不断地通过 `getTask ()` 方法获取任务。 2.getTask () 方法从阻塞队列中取任务。 3. 如果线程池正在停止，那么要保证当前线程是中断状态，否则要保证当前线程不是中断状态。 4. 执行任务。 5. 如果 getTask 结果为 null 则跳出循环，执行 `processWorkerExit ()` 方法，销毁线程。

![线程池执行流程](https://p0.meituan.net/travelcube/879edb4f06043d76cea27a3ff358cb1d45243.png)

### 实现 Runnable 接口和 Callable 接口的区别

- `Runnable`自 Java 1.0 以来一直存在；`Callable`在 Java 1.5 中引入，用来处理`Runnable`不支持的用例
- `Runnable` 接口不会返回结果；`Callable` 接口可以返回结果，通过 `FutureTask` 进行封装

如果任务不需要返回结果或抛出异常推荐使用 `Runnable` 接口

工具类 `Executors` 可以实现 `Runnable` 对象和 `Callable` 对象之间的相互转换。

### 执行 execute()方法和 submit()方法的区别是什么呢？

1. `execute()`方法用于提交不需要返回值的任务，所以无法判断任务是否被线程池执行成功与否；
2. `submit()`方法用于提交需要返回值的任务。线程池会返回一个 `Future` 类型的对象，通过这个 `Future` 对象可以判断任务是否执行成功

[如何优雅的使用和理解线程池](https://crossoverjie.top/2018/07/29/java-senior/ThreadPool/)

### AtomicInteger 类的原理

```java
private static final Unsafe unsafe = Unsafe.getUnsafe();
private static final long valueOffset;

static {
try {
valueOffset = unsafe.objectFieldOffset
(AtomicInteger.class.getDeclaredField("value"));
} catch (Exception ex) { throw new Error(ex); }
}

private volatile int value;
```

AtomicInteger 类主要利用 CAS (compare and swap) + volatile 和 native 方法来保证原子操作，从而避免 synchronized 的高开销，执行效率大为提升。

### AQS

#### AQS 介绍

AQS 的全称为（AbstractQueuedSynchronizer），是一个用来构建锁和同步器的框架。ReentrantLock，Semaphore， ReentrantReadWriteLock，SynchronousQueue，FutureTask 等等皆是基于 AQS 的。

#### AQS 原理

AQS 核心思想是，如果被请求的共享资源空闲，那么就将当前请求资源的线程设置为有效的工作线程，将共享资源设置为锁定状态；如果共享资源被占用，就需要一定的阻塞等待唤醒机制来保证锁分配（双向链表）。这个机制主要用的是 CLH 队列的变体实现的，将暂时获取不到锁的线程加入到队列中。

AQS 使用一个 Volatile 的 int 类型的成员变量来表示同步状态，通过内置的 FIFO 队列来完成资源获取的排队工作，通过 CAS 完成对 State 值的修改。

![原理图](https://images.xiaozhuanlan.com/photo/2020/d8497ffb9df13bf7fdef83bc938dd9a5.png)

#### AQS 对资源的共享方式

- **Exclusive**（独占）：只有一个线程能执行，如 `ReentrantLock`。又可分为公平锁和非公平锁：
- **Share**（共享）：多个线程可同时执行，如 `CountDownLatch`、`Semaphore`、 `CyclicBarrier`、`ReadWriteLock` 

```java
isHeldExclusively()//该线程是否正在独占资源。只有用到condition才需要去实现它。
tryAcquire(int)//独占方式。尝试获取资源，成功则返回true，失败则返回false。
tryRelease(int)//独占方式。尝试释放资源，成功则返回true，失败则返回false。
tryAcquireShared(int)//共享方式。尝试获取资源。负数表示失败；0表示成功，但没有剩余可用资源；正数表示成功，且有剩余资源。
tryReleaseShared(int)//共享方式。尝试释放资源，成功则返回true，失败则返回false。
```

#### AQS 执行流程

1. 多个线程并发抢锁
2. 线程一通过 CAS 抢到锁并设置为工作线程
3. 线程二和线程三调用 addWaiter() 加入 FIFO 等待队列

> enq() ：队列为 null 先创建 Node 节点，然后将线程加到队列中

4. 队列中的线程执行 LockSupport.park() 挂起
5. 线程一释放锁，按照公平锁/非公平锁抢占资源

> 公平锁：如果 state=0 则代表此时没有线程持有锁，执行 hasQueuedPredecessors() 判断 AQS 等待队列中是否有元素存在，如果存在其他等待线程，那么自己也会加入到等待队列尾部，做到真正的先来后到，有序加锁。

[【深入 AQS 原理】我画了 35 张图就是为了让你深入 AQS](https://cloud.tencent.com/developer/article/1624354)

#### AQS 组件总结

- Semaphore (信号量)- 允许多个线程同时访问
- CountDownLatch （倒计时器）：常用来控制线程等待，让某一个线程等待直到倒计时结束，再开始执行
- CyclicBarrier (循环栅栏)：和 CountDownLatch 非常类似。让一组线程到达一个屏障（也可以叫同步点）时被阻塞，直到最后一个线程到达屏障时，屏障才会开门。