#### Thread线程

##### 一、并发和并行

* **并发**：指两个或多个事件在同一个时间段内`交替执行`
* **并行**：指两个或多个事件在同一个时刻`同时执行`

![image-20200515094234094](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200515094234094.png)

##### 二、进程和线程

* **进程**：是指一个内存中运行的应用程序（任务管理器中的每一个正在运行的应用就是进程），一个应用程序可以同时运行多个进程。进程也是程序的一次执行过程，是系统运行程序的基本单位。

![image-20200515095009475](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200515095009475.png)

* **线程**：线程是进程中的一个执行单元。一个进程中至少有一个线程，当有多个线程时则称之为多线程程序。

![image-20200515100353400](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200515100353400.png)

##### 三、主线程

> 执行主方法（main）的线程称为主线程，也叫单线程

* 首先JVM执行main方法，main方法会进入到`栈内存`中
* 然后JVM会找到操作系统去开辟一条main方法通向CPU的执行路径（即`线程`）
* CPU就可以通过这个路径（`线程`）来执行main方法
* 而这个路径（`线程`）有一个名字，叫main（主）线程

![image-20200515102000694](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200515102000694.png)

##### 四、创建多线程

##### （1）方式一：`继承Thread`

​	**实现步骤：**

1. 创建一个Thread类的子类
2. 在Thread类的子类中重写run方法，设置线程任务
3. 创建Thread类的子类对象
4. 调用子类的start方法，`JVM`就会调用该线程的run方法，开启新的线程。结果为两个线程`并发`的执行（当前main线程和另一个新创建的线程，多次启动一个线程是`非法`的，特别是当线程已经`结束执行`后，不能再重启）



如下为继承Thread的子类`MyThread`

````java
public class MyThread extends Thread {
    @Override
    public void run() {
        for(int i = 0;i<=20;i++){
            System.out.println("run:"+i);
        }
    }
}
````

如下为主方法（`main`）

````java
public class Demo1 {
    public static void main(String[] args) {
        MyThread myThread = new MyThread();
        myThread.start();

        for(int i = 0;i<=10000;i++){
            System.out.println("main:"+i);
        }
    }
}
````

##### （2）方式二：`实现Runnable`

**实现步骤：**

1. 创建一个Runnable接口的实现类`RunnableImpl`
2. 在实现类`RunnableImpl`中重写run方法，设置线程任务
3. 创建实现类对象
4. 创建Thread类对象，通过构造方法传递实现类`RunnableImpl`
5. 调用Thread类中的start方法，开启新的线程



如下为Runnable接口的实现类`RunnableImpl`

````java
public class RunnableImpl implements Runnable {
    @Override
    public void run() {
        for(int i = 0;i<=10000;i++){
            System.out.println("run:"+i);
        }
    }
}
````

如下为主方法（`main`）

````java
public class Demo1 {
    public static void main(String[] args) throws InterruptedException {
        RunnableImpl runnable = new RunnableImpl();
        Thread thread = new Thread(runnable);
        thread.start();
        for(int i = 0;i<=10000;i++){
            System.out.println("main:"+i);
        }
    }
}
````

**实现Runnable接口的好处：**

1. 避免了单继承的局限性
2. 增强了程序的扩展性，降低了程序的耦合性（解耦）

##### （3）方式三：`匿名内部类`

如下为主方法（`main`）

````java
/**
 * 线程的父类是Thread
 */
public class Demo1 {
    public static void main(String[] args) throws InterruptedException {
       new Thread(){
           @Override
           public void run() {
               for(int i = 0;i<=10000;i++){
                   System.out.println("run:"+i);
               }
           }
       }.start();
        for(int i = 0;i<=10000;i++){
            System.out.println("main:"+i);
        }
    }
}

/**
 * 线程的接口是Runnable
 */    
public class Demo1 {
    public static void main(String[] args) throws InterruptedException {
        Thread thread = new Thread(new Runnable() {
            @Override
            public void run() {
                for(int i = 0;i<=10000;i++){
                    System.out.println("run:"+i);
                }
            }
        });
        thread.start();
        for(int i = 0;i<=10000;i++){
            System.out.println("main:"+i);
        }
    }
}
````

##### 五、线程安全问题

![image-20200515165441670](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200515165441670.png)

![image-20200515165451099](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200515165451099.png)

![image-20200515165501999](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200515165501999.png)

**例子：**

如下为Runnable接口的实现类`RunnableImpl`

````java
public class RunnableImpl implements Runnable {
    private int ticket = 100;

    @Override
    public void run() {
        while (true) {
            try {
                Thread.sleep(10);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            if (ticket > 0) {
                System.out.println(Thread.currentThread().getName() + 
                                   ":正在出售" + ticket + "号的票");
                ticket--;
            } else {
                break;
            }
        }
    }
}
````

如下为主方法（`main`）

````java
public class Demo1 {
    public static void main(String[] args) throws InterruptedException {
        RunnableImpl runnable = new RunnableImpl();
        Thread thread0 = new Thread(runnable);
        Thread thread1 = new Thread(runnable);
        Thread thread2 = new Thread(runnable);

        thread0.start();
        thread1.start();
        thread2.start();

    }
}
````

##### 六、线程同步

##### （1）方式一：同步代码块

**格式：**

````java
synchronized(同步锁（对象锁）){
    需要同步操作的代码
}
````

**原理：**

![image-20200515174956930](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200515174956930.png)



##### （2）方式二：同步方法

**格式：**

````java
//run方法调用这个方法即可
public synchronized void method(){
    可能会产生线程安全问题的代码	
}
````

对象锁是谁？

> 对于非static方法，同步锁就是this
>
> 对于static方法，同步锁就是当前所在类的字节码对象（类名.class）

##### （3）方式三：Lock锁机制（JDK1.5之后）

**使用步骤：**

1. 在成员变量位置创建一个`Lock接口`的实现类`ReentrantLock`对象
2. 在可能会出现安全问题的代码前调用Lock接口中的`lock方法`获取锁
3. 在可能会出现安全问题的代码后调用Lock接口中的`unlock方法`释放锁（写在finally可以保证即使有异常也能将锁给释放）

##### 七、线程状态

![image-20200515205255581](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200515205255581.png)