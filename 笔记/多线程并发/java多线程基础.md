## 该章节学习java中Thread的核心方法

### 1.线程和进程

**进程：**是并发执行的程序在执行过程中分配和管理资源的基本单位，是一个动态概念，竞争计算机系统资源的基本单位。

**线程：**是进程的一个执行单元，是进程内科调度实体。比进程更小的独立运行的基本单位。线程也被称为轻量级进程。

一个程序至少一个进程，一个进程至少一个线程。

### 2.使用多线程

#### 2.1 java实现多线程的两种方式

一种是继承Thread类，一种是实现Runnable接口。

Thread的类声明

```
class Thread implements Runnable
```

Thread也是实现了Runable接口。

使用Thread创建新线程的局限性，是Java只支持单继承，所以为了支持多继承，实现Runnable接口。本质没有区别。

##### 2.1.1继承Thread类

让一个类继承Thread类并实现多线程。

MyThread类

```java
package com.xu.Thread;

public class MyThread extends  Thread{
    
    @Override
    public void run() {
        super.run();
        System.out.println("该线程运行了");
    }
}

```

Run类

```java
ackage com.xu.Thread;

public class Run {
    public static void main(String[] args) {
        MyThread myThread=new MyThread();
        myThread.start();
        System.out.println("out");
    }
}
```

###### 多线程执行的随机性

上面这个实例就是通过继承Thread实现多线程。通过start()方法启动多线程。start()方法通知"线程规划器"，该线程已经准备就绪，让系统安排调用run()方法，据有随机执行的效果。

多线程随机输出的原因：**cpu将时间片分给不同的线程。线程获得时间片后执行任务，所以这些线程交替的执行。**时间片既cpu分配给个线程的时间。

如果我们调用thread.run()就不是使用多线程执行的。而是由main方法调用。所以使用多线程执行run()方法使用thread.start()来启动一个线程。并且执行start()的顺序并不是执行run()的顺序。

在多线程中，代码的运行结果与代码的执行顺序是没有关系的。线程是子任务，CPU会以不确定的方式，调用线程的run()方法。

#### 2.1.2  实现Runnable接口

如果要创建的一个线程类已经有一个父类，就不可以通过继承Thread类来实现多线程，因为java不支持多继承，所以我们要通过实现Runnable接口来实现多线程。

MyRunnable类

```java
package com.xu.Runable;

public class MyRunnable implements Runnable {
    @Override
    public void run() {
        System.out.println("该线程执行中");
    }
}

```

MyRun类

```java
package com.xu.Runable;

public class Run {
    public static void main(String[] args) {
        Runnable myRunnable=new MyRunnable();
        Thread thread=new Thread(myRunnable);
        thread.start();
    }
}
```

上面就是通过Runable创建多线程的方式。

###### Runnable的优点

通过Runnable可以间接的实现多"继承"。

###### 实例变量共享造成的非线程安全问题和解决方案

当多个线程同时去操作一个实例变量，就会造成线程不安全问题。

典型的实例，就是买票系统，当多个线程对总票数操作的时候，就会造成多个线程对其同时操作，共享的票数变量重复。

**解决方案：**通过给run()方法加synchronized关键字。当多个线程同时要执行run()方法，synchronized会锁住当前线程，其他的线程以排队的方式等待，等当前执行的线程释放该锁，让其他线程继续执行。

#### 3  Thread类中的方法

![线程状态](Untitled/1.jpg)

##### 3.1 currentThread()方法

currentThread()该方法返回代码段正在被按一个线程执行。  

###### run()和start()方法的区别

1. run(),立即执行run()方法，不创建新的线程。
2. start(),执行run的时机不确定，启动一个新线程。

##### 3.2  isAlive()方法

该方法时用来判断线程是否处于存活状态。存活状态：线程以启动尚未终止的状态。

