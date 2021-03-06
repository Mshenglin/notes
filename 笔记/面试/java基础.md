### 面向对象

##### 1.面向对象的三大特性？

封装，继承，多态。

封装

它的核心思想是隐藏细节，保证数据安全，不需要关注具体的实现。在平时的开发时，表现为讲对象不需要让外界访问的变量和方法进行私有化，只提供开发者意愿的公有方法来访问这些数据和逻辑。

继承

提高程序的复用性，和扩展性。Java中只支持单继承，父类中被public和protected修饰的方法和属性可以重写，但是构造方法是不可以被继承的。在子类中可以使用super()调用。

多态

同一种行为具体的不同种表现。具体的表现就是同一个接口使用不同的实例对象调用而执行不同的操作。

##### 2.访问权限修饰关键字

|           | 本类中 | 本包中 | 子类 | 外部包 |
| --------- | ------ | ------ | ---- | ------ |
| public    | √      | √      | √    | √      |
| protected | √      | √      | √    | ×      |
| default   | √      | √      | ×    | ×      |
| private   | √      | ×      | ×    | ×      |

通过反射可以调用私有的成员变量。

##### 3.Java异常

Throwable:所有错误或者异常的超类。

两个子类Error和Exception。

Error（错误）：是程序无法处理的。是JVM出现问题。例如Java虚拟机运行时错误，Java内存溢出。

Exception（异常）:是程序本身可以处理的异常。他有一个重要的子类时Exception 类有一个重要的子类 **RuntimeException**。是由于编写程序不当所造成的。例如，**NullPointerException**、**ArrayIndexOutOfBoundsException** 。

##### 4 接口和抽象类的区别？

我的理解是接口时特殊的抽象类。

1.接口的方法默认时public的，所有方法在接口中是不可以实现的（1.8以后可以有默认的实现），而抽象类可以有非抽象方法。

2.接口中，除static和final变量不能有其他变量

3.一个类只能实现一个抽象类，但是可以是实现多个接口。

### Java语法

##### 1.Java泛型了解么？什么是类型擦除？介绍一下常用的通配符？

Java中的泛型是JDK 5以后引入的特性，

##### 2.==和equal的区别

==：判断的是两个对象的内存地址是否相同。

equal():也是用来判断两个对象是否相同，但是他不可以用来比较基本类型。

#### 深拷贝与浅拷贝？

### 包装类与基本类型

##### 1.Int与Integer包装类的比较问题

##### 2.缓冲池？

### 基础知识

##### 1.String，StringBuffer和StringBuilder的区别 **(String是不可变类有什么好处)**

##### 2.抽象类与接口的区别

##### 3.Object类中都有什么方法呢。

##### 4.hashcode()和equals的关系是什么？重写equals一定要重写hashcode吗

##### 5.clone方法是深拷贝还是浅拷贝？如何实现深拷贝？



