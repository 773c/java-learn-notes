# JVM底层分析

## JVM介绍

### 1. JVM的架构模型

> Java编译器输入的指令流基本上是一种基于栈的指令集架构，另外一种指令集架构则是基于寄存器的指令集架构。

#### JVM相关DOS命令：

1. `jps`：查看当前活跃的进程
2. `javap -v -p 类名.class`：解析（反编译）字节码文件
3. `jinfo -flag PermSize（MetaspaceSize） 进程id`：查看PermSize（永久代）的内存大小

### 2. JVM的生命周期

#### 2.1 虚拟机的启动

> Java虚拟机的启动是通过引导类加载器（bootstrap class loader）创建一个初始类（initial class）来完成的，这个类是由虚拟机的具体实现指定的。

#### 2.2 虚拟机的执行

#### 2.3 虚拟机的结束

## 类加载器子系统（Class Loader System）

> `类加载子系统`只负责加载`class文件`，至于是否能成功运行，则由`执行引擎`来决定。
>
> 加载的类的信息存放在一块称为方法区的内存空间。除了类的信息之外，方法区还会存放运行时常量池信息，可能还包括字符串字面量和数字常量。

![image-20200617222138774](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200617222138774.png)

### 1. 类加载过程

![image-20200617143728885](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200617143728885.png)

**`加载（Loading）：`**

* 在内存中生成一个代表这个类的`java.lang.class`对象，作为方法区这个类的各种数据的访问入口

**`链接（Linking）：`**

* **验证（Verify）**：确保class文件的信息符合当前虚拟机的要求。
* **准备（Prepare）**：为`类变量（静态变量）`（不包含常量，如final修饰的static）分配内存并且设置该类变量的默认`初始值`，即零值（false、null）。
* **解析（Resolve）**：将常量池内的符号引用转换为直接引用的过程。

**`初始化（initialization）：`**

* 初始化阶段就是执行类构造器方法`<clinit>()`的过程
* `<clinit>()`不需要定义，是javac编译器自动收集类中的所有`类变量（静态变量）`的赋值动作（显示初始化）和静态代码块中的语句合并而来。
* 构造器方法`<clinit>()`中的指令按语句的源文件中出现的顺序执行。
* `<clinit>()`不同于类的构造器`<init>()`。

````java
public class ClassInitTest{
    private static int num = 1;    
    static{
        num = 2;
        number = 20;
    }
    //linking的prepare时，number => 0,initial => 20 => 10
    private static int number = 10; 
}
````

### 2. 类加载器的分类

> 主要分为两大类，`引导类加载器（BootStrap ClassLoader）`和`自定义类加载器（User-Defined ClassLoader）`

#### 2.1 启动类加载器（BootStrap ClassLoader）

* 这个类加载器是由`C/C++`语言实现的，嵌套在`JVM`内部。
* 它用来加载Java的核心库，用于提供`JVM`自身需要的类。
* 并不继承自`java.lang.ClassLoader`，没有父加载器。
* 用来加载`扩展类加载器`和`应用程序类加载器`。

#### 2.2 扩展类加载器（Extension ClassLoader）

* 由`Java`语言编写。
* 派生于`ClassLoader`类。
* 父类加载器为启动类加载器（BootStrap ClassLoader）。 

#### 2.3 应用程序类加载器（Application System ClassLoader）

* 由`Java`语言编写。
* 派生于`ClassLoader`类。
* 父类加载器为扩展类加载器（Extension ClassLoader）。 
* 该类加载时程序中默认的类加载器，一般来说，Java应用的类都由它来完成加载。
* 通过`ClassLoader.getSystemClassLoader()`可以获取到该类加载器。

### 3. 获取ClassLoader的途径

1. 获取当前类的ClassLoader：`class.getClassLoader();`
2. 获取当前线程上下文的ClassLoader：`Thread.currentThread().getContextClassLoader();`
3. 获取系统的ClassLoader：`ClassLoader.getSystemClassLoader();`
4. 获取调用者的ClassLoader：`DriverManager.getCallerClassLoader();`

### 4. 双亲委派机制

> `JVM`对class文件采用的是`按需加载`的方式，也就是说当需要使用该类时才会将它的class文件加载到内存中。而且加载某个类的class文件时，`JVM`采用的是`双亲委派机制`，即把请求交给`父类处理`，它是一种任务委派模式。

**工作原理：**

1. 如果一个类加载器收到了类加载的请求，它不会自己先去加载，而是把请求委托给父类去执行加载。
2. 如果父类还存在父类，则继续向上委托，直到到达顶层的启动类加载器。
3. 如果父类加载器可以完成类加载的任务，则成功返回，如果无法完成则由自己去完成加载。

**优势：**

1. 避免类的重复加载
2. 保护程序的安全，防止核心API被随意篡改

## 运行时数据区（Runtime Data Area）

### 1. 虚拟机栈

> 每个线程都会创建一个虚拟机栈，每个虚拟机栈由一个个的`栈帧`组成，`一个栈帧`就对应Java中的`一个方法`。线程是私有的，生命周期与线程一致。不存在`GC`（垃圾回收），但存在`OOM`（内存溢出）

#### 1.1 设置栈内存大小

使用参数`-Xss256k`，在VM Option里写入即可。

#### 1.2 栈帧

![image-20200618124103430](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200618124103430.png)

![image-20200618124024947](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200618124024947.png)

* 局部变量表

  > 它是一个数字数组，主要用于存储方法形参和局部变量。
  >
  > 由于线程是私有的，所以不存在安全问题。
  >
  > 局部变量表所需的容量大小是在编译期确定下来的。
  >
  > 最基本的存储单元是slot（变量槽），32位以内的类只占用一个slot，64位的类型（long和double）占用两个slot。

  **为什么this不能用在静态的方法（类方法）中？**

  `因为在静态方法的局部变量表中不存在this变量`

  `在实例方法中，this会作为变量被放在局部变量表中index0的位置上`

* 操作数栈（或表达式栈）

  > 在方法执行的过程中，根据字节码指令，往栈中写入数据或提取数据，即入栈、出栈。

* 动态链接（或指向运行时常量池的方法引用）

  > 每一个栈帧内部都包含一个指向运行时常量池中该栈帧所属方法的引用。包含这个引用目的就是为了支持当前方法的代码能够实现动态链接。
  >
  > 在Java源文件被编译成字节码文件时，所有的变量和方法引用都作为符号引用（#）保存在class文件的常量池里（`Constant pool`）

  **解析字节码文件命令：**`javap -v -p 类名.class`

* 方法返回地址（或方法正常退出或者异常退出的定义）

* 一些附加信息

#### 1.3 本地方法接口

> 简单来说，就是Java调用非Java代码的接口。本地接口的作用就是融合不同的编程语言为Java所用。

### 2. 堆

> 存储对象和数组的。

#### 2.1 内存细分

Java7之前内存逻辑分为三部分：新生代、老年代、永久代

Java8之后内存逻辑分为三部分：新生代、老年代、元空间

#### 2.2 设置堆内存大小

`-Xms20m`：设置堆空间（新生代+老年代）的初始化大小

`-Xmx20m`：设置堆空间（新生代+老年代）的最大内存大小

初始化大小和最大内存大小最好一致。

默认堆空间的大小：物理电脑内存大小/64

默认堆空间的最大内存：物理电脑内存大小/4

#### 2.3 年轻代与老年代

![image-20200618172256684](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200618172256684.png)

![image-20200618180652316](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200618180652316.png)

年轻代和老年代内存分配比例默认为：`1：2` ，即`-XX:NewRatio=2`

Eden区和survivor0、survivor1的内存分配比例默认为：`8：1：1` ，即`-XX:SurvivorRatio=8`

**Eden区：**

* 几乎所有的`Java对象`都是在Eden区被new出来的。
* 绝大部分的Java对象的销毁都在新生代进行了。

### 3. 方法区

![image-20200618210149950](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200618210149950.png)

> JDK7之前，习惯将方法区称为永久代，JDK8之后则称之为元空间。

#### 3.1 永久代和元空间的区别：

#### `元空间使用的不是虚拟机内存，而是本地内存。`

#### 3.2 设置方法区大小

JDK7之前：

`-XX:PermSize`：设置永久代初始内存空间，默认是`20.75M`

`XX:MaxPermSize`：设置永久代最大内存空间，32位默认是`64M`，64位是`82M`

JDK8之后：

`-XX:MetaspaceSize`：设置元空间初始内存空间，默认是`21M`

`XX:MaxMetaspaceSize`：设置元空间最大内存空间，默认是`-1`，即没有限制

#### 3.3 方法区存储什么

* 类型信息
* 常量
* 静态变量
* 即使编译器编译后的代码缓存

#### 3.4 运行时常量池

**常量池**

> 是字节码文件的一部分，用于存放编译器生成的各种字面量和符号引用，这部分内容将在类被加载的时候存放到方法区的运行时常量池中。

#### 3.5 方法区的演进细节

* 首先，只有`HotSpot`才有永久代
* `JDK6之前`：有永久代，静态变量是存储在永久代上的

![image-20200619153512314](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200619153512314.png)

* `JDK7`：有永久代，但已逐步去永久代，字符串常量池和静态变量保存在堆中

![image-20200619153545342](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200619153545342.png)

* `JDK8之后`：没有永久代，类型信息、字段、方法、常量保存在本地内存的元空间，字符串常量池和静态变量保存在堆中

![image-20200619153633446](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200619153633446.png)

## 对象的创建过程

### 1. 创建对象的方式

1. `new`
2. `Class的newInstance()`：反射的方式，只能调用空参的构造器，权限只能是public
3. `Constructor的newInstance(Xxx)`：反射的方式，可以调用空参和有参，权限没有要求
4. `使用clone()`：不调用任何构造器，需要实现Cloneable()接口，实现clone()方法
5. `使用反序列化`

### 2. 对象创建的步骤

1. 判断对象对应的类是否加载
2. 为对象分配内存
3. 处理并发安全问题
4. 对所有属性的进行默认初始化（`对所有属性（成员变量）进行默认初始化，除了类变量，因为类变量在类加载的时候就进行了初始化了`）
5. 设置对象的对象头
6. 执行init方法进行显示初始化、代码块初始化、构造器初始化

### 3. 对象的内存布局

![image-20200619170206968](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200619170206968.png)

1. `对象头`：
   * 运行时元数据（Mark Word）：哈希值、GC分代年龄、锁状态标志、线程持有的锁
   * 类型指针：指向方法区（元空间）所对应的类型
   * 如果是数组，则需要额外记录数组的长度
2. `实例数据`：它是对象正在存储的有效信息，包含父类继承下来的和本身拥有的字段
3. `对齐填充`：不是必须的，没什么含义，仅仅起到占位符的作用

### 4. 对象访问定位

1. 句柄访问
2. 直接指针（`HotSpot采用`）

## 执行引擎

> 将字节码文件装载到JVM内存中，并不能直接运行在操作系统上，而是需要将字节码指令解释/编译为对应平台上的本地机器指令才可以。简单来说，执行引擎相当于将高级语言翻译成机器语言的译者。

![image-20200619180856487](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200619180856487.png)

### 1. 设置

`-Xint`：只使用解释器

`-Xcomp`：只使用JIT编译器

`-Xmixed`：混合使用

## 字符串常量池（StringTable）

### 1. 字符串拼接操作

* 常量与常量的拼接结果咋常量池，原理是编译期优化

  ````java
  String s1 = "aaabbb";
  String s2 = "aaa" + "bbb";
  s1 == s2	//true
  ````

* 常量池中不会存在相同内存的常量

* 只要其中有一个是变量，结果就在堆中。相当于new String()，原理是StringBulider

  ````java
  String s1 = "aaabbb";
  String s2 = "aaa";
  String s3 = s2 + "bbb";
  s1 == s3	//false
  ````

* 如果拼接的结果调用intern()方法，如果常量池中存在，则返回该地址，不存在则往常量池中添加。

**案例：**

![image-20200619215411075](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200619215411075.png)

`只有将s.intern()赋值给一个String s1对象，s1对象才会指向常量池。`

`因为变量拼接字符串底层使用StringBulider().toString()，虽然toString() new了一个String，但是并不会向常量池中添加字符串，所以在`

`JDK6及之前，当s.intern()时，就只是在常量池中创建字符串，所以结果为false`

![image-20200619225132769](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200619225132769.png)

`JDK7及之后，当s.intern()时，如果常量池中没有该字符串，但是有new String("字符串"),就会将常量池中的字符串指向该地址，结果为true。如果是先String s ="字符串"，后s.intern()。则s.intern（）没有作用，结果为false`

![image-20200619225030500](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200619225030500.png)

## 垃圾回收器

### 1. 什么是垃圾

> 垃圾是指在运行程序中没有任何指针指向的对象

* Java堆是垃圾回收器的工作重点
* 频繁回收新生代
* 较少回收老年代
* 基本不动永久代（方法区或元空间）

### 2. 垃圾回收步骤

#### 2.1 标记阶段

**引用计数算法**

> 对于一个对象A，只要有任何一个对象引用了A，则引用计数器就加1，如果没有引用则减1。只要对象A的引用计数器的值为0，则表示对象A没有被使用，可进行回收

**可达性分析算法**（或根搜索算法，追踪性垃圾收集）（`JAVA选择`）