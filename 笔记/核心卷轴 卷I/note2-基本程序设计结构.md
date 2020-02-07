#### 数据类型的转换

##### 隐式转换

**精度损失**

1. 当从低位向高位转化的时候,该过程不会发生精度损失。（同种类型中）
2. 当浮点型与整型进行转化时，int-->float会有精度损失。long-->double会有精度损失

**两数进行二元操作**

1. 两数中由一个是double类型，则结果为double类型
2. 否则，两数中有一个数为float，则运算结果为float
3. 否则，两数中有一个数为long，则运算结果为long
4. 否则，结果将会转化为int

##### 显示转换

语法：

```java 
float   a=3.12;
int b=(int)a;
```

在上面的过程中，进行强制转换，会有精度的损失。

转换的方法：通过截断小数部分进行将浮点型转换为整型。

如果要进行四舍五入的操作，需要使用Math.round()方法。

例子

```java
float   a=3.12;
int b=(int)Math.round(a);
```

#### 大数值处理

当基本的整型以及浮点型不可以满足要求时，可以使用BigInteger和BigDecimal类。这两各类可以处理任意任意长度的数据。BigInteger用来处理任意精度的整数运算，BigDecimal用来处理任意精度的浮点型运算。

注意：**进行大数值的运算时，不可以使用运算符进行运算。**

例子：

```java
 int a=1<<1;
        System.out.println(a);
        //将基本的数据类型转化为大数值
        BigInteger b=BigInteger.valueOf(a);ss
        //对大值进行运算，b*（b+1）
        BigInteger multiply = b.multiply(b.add(BigInteger.valueOf(1)));
        System.out.println(multiply);
		//输出结果6
```

