- [Java 接口和抽象类有什么区别？](#java-接口和抽象类有什么区别)
- [String，StringBuilder 和 StringBuffer 的区别？](#stringstringbuilder-和-stringbuffer-的区别)
- [final、finally、finalize 三个关键字的区别？](#finalfinallyfinalize-三个关键字的区别)
- [== 和 equals 的区别，给段代码判断下 “==” 和 equals 的返回值](#-和-equals-的区别给段代码判断下--和-equals-的返回值)
- [Java 基本数据类型有几种，每一种的区别是什么？](#java-基本数据类型有几种每一种的区别是什么)
- [值传递和引用传递的区别？](#值传递和引用传递的区别)
- [Java 的异常体系？受检查和非受检查异常区别？](#java-的异常体系受检查和非受检查异常区别)
- [序列化和反序列化](#序列化和反序列化)
- [反射和泛型](#反射和泛型)



### Java 接口和抽象类有什么区别？

|             |           抽象类           |                接口                |
| ----------- | :------------------------: | :--------------------------------: |
| 成员变量    |       可定义实例字段       |          不可定义实例字段          |
| 构造器      |        可以有构造器        |            不能有构造器            |
| main 方法   |      可以有 main 方法      |          不能有 main 方法          |
| 非抽象方法  |      可定义非抽象方法      |   可定义 default 方法（Java 8）    |
| 修饰符      | public、protected、default |            只有 public             |
| 实现 & 继承 |  只能 extends 一个 class   |   可以 implements 多个 interface   |
| 速度        |             快             | 慢（需要时间寻找在类中实现的方法） |

接口不是类，而是对类的一组需求描述，这些类要遵从接口描述的统一格式进行定义。

接口可以提供多重继承的大多数好处，同时还能避免多重继承的复杂性和低效性。

*注：接口中可以定义静态的 main 方法并成功运行，但是不符合接口的设计原则，也没有什么意义。*

### String，StringBuilder 和 StringBuffer 的区别？

|               | 可变性 | 线程安全 |             速度              |
| :-----------: | :----: | :------: | :---------------------------: |
|    String     | 不可变 |   安全   |   慢（每次操作生成新对象）    |
| StringBuilder |  可变  |  不安全  |              快               |
| StringBuffer  |  可变  |   安全   | 中（synchronized 关键字同步） |

使用情形：

字符串不变——> String

字符串改变 & 单线程——> StringBuilder

字符串改变 & 多线程——> StringBuffer

### final、finally、finalize 三个关键字的区别？

**final**——修饰符，可以修饰 `数据`、`方法` 和 `类`

数据：修饰 `基本类型` 时表示变量数值不可变（用于定义常量）；修饰 `引用类型` 时表示引用变量不可变（即引用变量指向的内存地址不变）

方法：修饰方法时表示该方法不能被子类重写（Override）

类：修饰类时表示类不允许被继承

**finally**——异常处理的语句结构，表示总是执行的部分

try 和 finally 同时有 return 语句时，try 的 return 语句不会执行

**finalize**——object 类的一个方法，用于实例被垃圾回收器回收时

当垃圾回收器确定不存在对该对象的有更多引用时，对象的垃圾回收器就会调用这个方法。所有 object 都继承了 finalize（）方法

```java
protected void finalize() throws Throwable {}
```

### == 和 equals 的区别，给段代码判断下 “==” 和 equals 的返回值

|          |             ==             |     equals()     |
| -------- | :------------------------: | :--------------: |
| 使用范围 | 基本数据类型、引用数据类型 |   引用数据类型   |
| 比较方法 |     基本数据类型比较值     |  未重写时同 ==   |
|          |  引用数据类型比较内存地址  | 重写后按重写逻辑 |

```java
// equals 方法没有被重写
public boolean equals(Object obj) {return (this == obj);}

// equals 方法被重写;String 类中方法
@Override 
public boolean equals(Object other) {
    if (other == this) {
        return true;
    }
    if (other instanceof String) { // 两个字符串值比较
        String s = (String)other;
        int count = this.count;
        if (s.count != count) {
            return false;
        }
        if (hashCode() != s.hashCode()) {
            return false;
        }
        char[] value1 = value;
        int offset1 = offset;
        char[] value2 = s.value;
        int offset2 = s.offset;
        for (int end = offset1 + count; offset1 < end; ) {
            if (value1[offset1] != value2[offset2]) {
                return false;
            }
            offset1++;
            offset2++;
        }
        return true;
    } else {
        return false;
    }
}
```

### Java 基本数据类型有几种，每一种的区别是什么？

8 种

| 基本类型 | 大小 (字节) | 默认值       | 封装类    |
| -------- | ----------- | ------------ | --------- |
| byte     | 1           | (byte)0      | Byte      |
| short    | 2           | (short)0     | Short     |
| int      | 4           | 0            | Integer   |
| long     | 8           | 0L           | Long      |
| float    | 4           | 0.0f         | Float     |
| double   | 8           | 0.0d         | Double    |
| boolean  | 1/8         | false        | Boolean   |
| char     | 2           | \u0000(null) | Character |

### 值传递和引用传递的区别？

值传递（call by value）：表示方法接收的是调用者提供的值。
引用传递 (call by reference) ：表示方法接收的是调用者提供的变量地址。

Java 程序设计语言总是采用按值调用，也就是说，**方法得到的是所有参数值的一个拷贝**，特别是，方法不能修改传递给它的任何参数变量的内容

Java 中方法参数的使用情况：

- 一个方法不能修改一个基本数据类型的参数（即数值型或布尔型）
- 一个方法可以改变一个对象参数的状态（Employee raiseSalary ()）
- 一个方法不能让对象参数引用一个新的对象（因为传到方法内会拷贝一份引用，不会影响引用指向的对象）

*注：Java 会 `复制` 传入方法的参数值，基本数据类型时复制数字（或字符等）值，引用数据类型时复制参数存储的内存地址。**无论如何在方法内部的参数都是原来的副本，即多了一个指向对象的指针**，不可能改变原来变量的内容。*

### Java 的异常体系？受检查和非受检查异常区别？

- 异常体系

![java异常体系.png](https://i.loli.net/2021/02/17/cR71wEThqND3mtg.png)

1. Error 与 Exception

Error 是**程序无法处理**的错误，由 JVM 产生和抛出的。比如 OutOfMemoryError、ThreadDeath 等。这些异常发生时，Java 虚拟机（JVM）一般会选择线程终止。

Exception 是**程序本身可以处理**的异常，这种异常分两大类运行时异常和非运行时异常。程序中应当尽可能去处理这些异常。

2. Exception：运行时异常和非运行时异常

运行时异常都是 RuntimeException 类及其子类异常，程序中**可以选择捕获处理，也可以不处理**。（这些异常一般是由程序逻辑错误引起的，程序应该从逻辑角度尽可能避免这类异常的发生。）

非运行时异常是 RuntimeException 以外的异常，**必须进行处理的异常**，如果不处理，程序就不能编译通过。

- 受查异常和非受查异常的区别

非受查（unchecked）异常：派生于 Error 类或 RuntimeException 类的所有异常

受查（checked）异常：除非受查异常外的所有其他异常

如果调用了一个抛出受查异常的方法，就必须对它进行处理，或者继续传递。通常，应该捕获（try...catch）那些知道如何处理的异常，而将那些不知道怎样处理的异常继续进行传递（throws）。

对于一个异常，根据受查异常和非受查异常的不同有不同的处理方式

非受查（unchecked）异常处理

1. 不处理
2. try...catch 捕获
3. 继续抛出 throws

受查（checked）异常处理

1. try...catch 捕获
2. 继续抛出 throws

[Java 异常体系 (美团面试)](https://www.cnblogs.com/aspirant/p/10790803.html)

[Java 检查异常和非检查异常区别](https://blog.csdn.net/tanga842428/article/details/52751303)

### 序列化和反序列化

- 是什么？

序列化：把 Java 对象转换为字节序列的过程

反序列化：把字节序列恢复为 Java 对象的过程

- 为什么？（好处）

序列化可以统一成字节流进行传输和保存

对象序列化可以实现分布式对象（RMI）

Java 对象序列化不仅保留一个对象的数据，而且递归保存对象引用的每个对象的数据

序列化可以将内存中的类写入文件或数据库中

- 如何实现

```java
package serialize;

import java.io.*;

public class ObjectOutputStreamTest {
    public static void main(String[] args) throws Exception{
        //序列化后生成指定文件路径
        File file = new File("person.ser");
        ObjectOutputStream oos = null;
        //装饰流（流）
        oos = new ObjectOutputStream(new FileOutputStream(file));
        //实例化类
        Person person = new Person("solejay", 25);
        oos.writeObject(person);
        oos.close();

        //反序列化
        File file1 = new File("person.ser");
        ObjectInputStream ois = new ObjectInputStream(new FileInputStream(file1));
        Person person1 = (Person)ois.readObject();
        System.out.println(person1.toString());
    }
}
```

[Java 序列化与反序列化三连问：是什么？为什么要？如何做？](https://www.cnblogs.com/javazhiyin/p/11841374.html)

[Java 对象的序列化（Serialization）和反序列化详解](https://blog.csdn.net/yaomingyang/article/details/79321939)

### 反射和泛型

[CS-Notes 反射](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java%20%E5%9F%BA%E7%A1%80.md#toc30)







