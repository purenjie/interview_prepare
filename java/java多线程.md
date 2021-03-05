[TOC]

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

在统一时刻一个 CPU 只能被一个线程使用，CPU 采用时间片轮转的形式供线程使用。所以线程切换的时候需要当前线程保存自己的状态，得到时间片的线程加载之前保存的状态。任务从保存到再加载的过程就是一次上下文切换。

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

**手写单例模式双重检验锁方式实现**

```java
public class Singleton {
    // 使用 volatile 可以禁止 JVM 的指令重排，保证在多线程环境下也能正常运行。
    private volatile static Singleton uniqueInstance;

    private Singleton() {}

    public  static Singleton getUniqueInstance() {
       //先判断对象是否已经实例过，没有实例化过才进入加锁代码
        if (uniqueInstance == null) {
            //类对象加锁
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

   - **可实现选择性通知（锁可以绑定多个条件）**

   `synchronized`关键字与`wait()`和`notify()`/`notifyAll()`方法相结合可以实现等待/通知机制。`ReentrantLock`类当然也可以实现，但是需要借助于`Condition`接口与`newCondition()`方法。

### volatile 关键字作用

1. 防止 JVM 的指令重排 
2. 保证变量的可见性（在每次访问变量时都会进行一次刷新，因此每次访问都是主存中最新版本）

### 并发编程的三个重要特性

- 原子性（要么都执行，要么都不执行。`synchronized` 可以保证代码片段的原子性。）
- 可见性（当一个变量对共享变量进行了修改，其他线程都是可以看到修改后的最新值。`volatile` 关键字可以保证共享变量的可见性。）
- 有序性（代码在执行的过程中的先后顺序。`volatile` 关键字可以禁止指令进行重排序优化。）

### 说说 synchronized 关键字和 volatile 关键字的区别

- `volatile` 关键字不会进行加锁操作，是线程同步的**轻量级实现**，性能比 `synchronized` 好
- `volatile` 关键字只能用于变量而 `synchronized` 关键字可以修饰方法以及代码块
- `volatile` 关键字能保证数据的可见性，但不能保证数据的原子性。`synchronized` 关键字两者都能保
- `volatile`关键字主要用于解决变量在多个线程之间的可见性，而 `synchronized` 关键字解决的是多个线程之间访问资源的同步性。

### ThreadLocal

#### 简介

`ThreadLocal`类的作用是提供线程内的局部变量，可以将`ThreadLocal`类形象的比喻成存放数据的盒子，盒子中可以存储每个线程的私有数据。

#### ThreadLocal 原理

`Thread`类源代码有一个 `threadLocals` 和一个 `inheritableThreadLocals` 变量，都是 `ThreadLocalMap` 类型。当前线程调用 `ThreadLocal` 类的 `set`或`get`方法时，调用的是`ThreadLocalMap`类对应的 `get()`、`set()`方法。

最终的变量是放在了当前线程的 `ThreadLocalMap` 中，`ThreadLocal`为 key ，Object 对象为 value

#### ThreadLocal 内存泄露问题

`ThreadLocalMap` 中使用的 key 为 `ThreadLocal` 的弱引用,而 value 是强引用。如果 `ThreadLocal` 没有被外部强引用的情况下，在垃圾回收的时候，key 会被清理掉，而 value 不会被清理掉。这样就会造成内存泄露。

`ThreadLocalMap` 在调用 `set()`、`get()`、`remove()` 方法的时候，会清理掉 key 为 null 的记录。使用完 `ThreadLocal`方法后 最好手动调用`remove()`方法。

### 线程池

#### 为什么要用线程池（好处）？

- **降低资源消耗**。通过重复利用已创建的线程降低线程创建和销毁造成的资源消耗。
- **提高响应速度**。当任务到达时，任务可以不需要等到线程创建就能立即执行。
- **提高线程的可管理性**。线程是稀缺资源，如果无限制的创建，不仅会消耗系统资源，还会降低系统的稳定性，使用线程池可以进行统一的分配，调优和监控。

#### 实现 Runnable 接口和 Callable 接口的区别

- `Runnable`自 Java 1.0 以来一直存在；`Callable`在 Java 1.5 中引入，用来处理`Runnable`不支持的用例

- `Runnable` 接口不会返回结果或抛出检查异常；`Callable` 接口可以

如果任务不需要返回结果或抛出异常推荐使用 Runnable` 接口

工具类 `Executors` 可以实现 `Runnable` 对象和 `Callable` 对象之间的相互转换。

