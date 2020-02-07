## final关键字

在java中如果你想让一个变量是不变的时候，你可以使用final关键字来修饰它。

#### final的含义

在java中，final的意思是无法改变的，它可以用来修饰方法，属性，类。

##### 1.无法改变的含义

当final修饰一个基本类型的变量时，他表示该变量的数值时永恒不变的。

例如： final  int  a = 2；表示a变量的值是无法改变的

当修饰的时一个引用类型时，当引用一旦初始化指向一个对象时，就无法在把它改为指向另一个变量，但是变量自身还是可以进行修改。

例子：

```java
public class Demo {
    final  StringBuilder  a=new StringBuilder();
    public static void main(String[] args) {
        Demo demo=new Demo();
        StringBuilder b=new StringBuilder();
        //demo.a=b; 这行会报错
        demo.a.append("aaa");
        System.out.println(demo.a);	//aaa
    }
}

```

在上边的例子中，a为已经赋值的引用类型变量。当我们对它进行二次赋值的时候，就会发生错误。但是我们可以修改它的值。



##### 2.空白的final

当我们在一个类中，定义一个final变量时，并没有对它进行直接赋值，这样作的目的时可以增加程序的灵活性。但是，**当我们对变量没有进行初始化，则必须在构造方法中对该变量进行初始化，否则就会报错，这一点非常重要的。**

例子：

```
public class Demo {
    final  int i;
}

```

上面的代码编译器就会报错，必须初始化该变量，为什么会让你必须初始化呢？

我个人的理解是，当我们在一个类中声明一个变量不对它进行初始化，则在创建一个该类的变量时，就会对其赋默认值，当该变量被final修饰时，该值则无法改变，所以在声明变量时就一定要对其初始化。

当代码以下的时候，代码就不会报错，这即保证了值的初始化，同时也可以提高程序的灵活性。

```java
public class Demo {
    final  int i;
    public Demo(int i)
    {
        this.i=i;
    }
}
```

#### final的用法

**final可以用来修饰参数，变量，方法，类**

##### 1.final修饰参数

1. 当final修饰参数时，这意味着你将无法在方法体中改变参数引用所指向的对象。
2. 该特性主要是用来向匿名内部类传递参数。

##### 2.final修饰方法

使用final的原因：

1. 将改方法锁定，防止继承类修改它的含义。(主要原因)
2. 用来提高效率。(早期使用)

##### 3.final和private 

类中所有的private**<u>方法</u>**，都被隐式的指定为final，因为无法取用private方法，所以也不能覆盖它。

##### 4.final类

当使用final来修饰一个类时，这就表名你无法继承该类，表示对该类不进行任何变动。当用final修饰一个类时，则该类中的方法以及属性都会被隐式的指定为final。

##### 5.final和static

当一个变量被final与static同时修饰时，则表示该变量是不变且只有一份，它占据一片不可改变的内存空间。



