#### 5.1类、超类、子类

##### 	5.1.1定义子类

​	通过extends关键字实现子类继承父类

##### 	5.1.2覆盖方法

- 子类默认继承父类的属性和方法。（私有域除外）
- 子类可以复写超类的方法。
- 在子类中，如果想调用父类的方法，要通过super关键字来调用。

##### 5.1.3子类构造器

- 通过super调用超类的构造器。使用super调用构造器的语句必须是子类构造器的第一句。

- 如果子类构造器没有显示的调用超类的构造器，则将会调用超类默认的无参构造，如果超类没有无参构造，在子类的构造器中又没有显示的调用其他构造器，则j a v a编译器就会报错。

  ###### this关键字的作用

  1. 引用隐式参数
  2. 调用本类的构造方法

  ###### super 关键字的作用

  1. 调用超类的方法
  2. 调用超类的构造器

##### 5.1.5多态

多态的调用 

 父类的引用指向子类

不可以将一个超类的引用赋值给子类变量

子类数组的引用可以转化为超类数组的引用

##### 5.1.6理解方法的调用

x.f(args)  （x为类c的对象）

1.方法的重载

2.方法的复写

##### 5.1.7阻止继承：final类和方法

将方法和类声明成final的作用：确保他们不会再子类中改变语义。

当一个类被final修饰的时候，表示该类不可以被继承。其中的方法都会自动的成为final方法。

##### 5.1.8强制类型转换

进行类型转换的原因：再暂时忽略对象的实际类型之后，使用对象的全部功能。

强制转换的使用场景：

- 只能在继承层次上使用
- 将超类转换为子类之前，应该使用instanceof进行检查。

##### 5.1.9抽象类

##### 5.1.10受保护访问

仅对本类可见——private

对所有类可见——public

对子类可见——protected

对本包可见——默认

#### 5.2 Object:类所有类的超类

**object**是所有类的父类，如果没有明确的超类，Object就是该类的超类。

##### equals()方法

equal方法用于一个对象是否等于另一个对象，在Object中的equals这个方法判断两个对象是否具有相同的引用。

###### 重写equals()方法

​	显示参数为otherObject

1. 检测this与otherObject是否引用了同一个对象
2. 检测otherObject是否为null
3. 比较this与otherObject是否属于同一个类
4. 将otherObject转化为相应的类类型
5. 现在将所有需要比较的域进行比较，使用==比较基本类型，使用equals比较对象域，如果所有的域都相同，则返回true，否则返回false。

举例

```java
public class Students {
int age;
@Override
public boolean equals(Object obj) {
	if (this == obj)
		return true;
	if (obj == null)
		return false;
	if (getClass() != obj.getClass())
		return false;
	Students other = (Students) obj;
	if (age != other.age)
		return false;
	return true;
}
}

```

##### hashcode()方法

hash code码，又称为散列码。是由对象导出的整型值，散列码是没有规律的。由于hashCode方法定义在Object类中，因此每个对象都会有一个默认的散列码，其值为对象的储存地址。

String类重写了hashCode方法

```java
public int hashCode() {
        int h = hash;
        if (h == 0 && value.length > 0) {
            char val[] = value;

            for (int i = 0; i < value.length; i++) {
                h = 31 * h + val[i];
            }
            hash = h;
        }
        return h;
    }
```

##### equals与hashcode定义必须一致

如果x.equals(y)为true。则x.hashCode()必须与y.hashCode()值相同。

##### toString()方法

返回对象值的字符串

#### 5.3 泛型数组的列表

##### 5.3.1访问数组列表元素

#### 对象包装器与自动装箱

有时候，将基本类型转化为对象。所有的基本类型都有一个与一个之对应的包装类型。

| 基本类型 | 包装类型  |
| :------- | --------- |
| int      | Integer   |
| long     | Long      |
| float    | Float     |
| double   | Double    |
| short    | Short     |
| char     | Character |
| byte     | Byte      |
| boolean  | Boolean   |

##### 自动装箱与自动拆箱

```java
ArrayList<Integer> list=new ArrayList<>();
list.add(3);
int n=list.get(i);
```

list.add(3)当向元素中添加元素的时候，将自动的转变为list.add(Integer.valueoOf(3)),这个将基本数据类型装换为队形包装类型的过程称为自动装箱。

相反的，将一个Integer对象赋值为int值时，就会发生自动拆箱。

int n=list.get(i);实际执行的操作时，int n=list.get(i).intValue();将包装类型拆箱为基本类型。

```java
        Integer a=new Integer(3);
        int b=3;
        Integer c=new Integer(b);
        Integer d=3;
        Integer f=3;
        System.out.println(c==b);//true
        System.out.println(a==c);//false
        System.out.println(a==d);//false
        System.out.println(b==d);//true
        System.out.println(d==f);//true
```

c==b,b发生自动拆箱，基本数据类型比较值。

a==c,使用new 关键字，比较时内存地址，返回false

a==d

###### 自动装箱使用说明

1. 包装类引用可以为null,所以自动装箱有可能会抛出NullPointerException异常。
2. 如果一个条件表达式中混用Double和Integer混用，Integer会自动拆箱为int，提升为double ,在装箱为Double
3. 装箱和拆箱是编译器认可的，而不是虚拟机，在编译器编译的过程中，就会插入必要的方法的调用。

#### 5.7反射

##### 5.7.1 Class类

获取类的三种方法

##### 5.7.2捕获异常

##### 5.7.3利用反射分析类的能力

##### 5.7.4在运行时使用反射分析对象

```java
public class People {   
private  String name;
 public static void main(String[] args) throws NoSuchFieldException, IllegalAccessException {
        People ma = new People("ma");
        Class aClass = ma.getClass();
        Field name = aClass.getDeclaredField("name");
        Object o = name.get(ma);
        System.out.println(o);
    }
}
```

##### 5.7.5使用反射编写泛型数组代码

