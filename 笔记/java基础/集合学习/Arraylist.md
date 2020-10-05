### ArrayList

##### 简介

ArrayList，底层是基于一个动态的数组实现，支持随机访问，当数组容量不够的时候会触发扩容机制。

```java
public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable
```

通过上面的代码，ArrayList继承了AbstractList类，实现了List、RandomAccess、Cloneable、Serializable接口。

- 继承AbstractList类和List接口，他提供了增删改查的操作。

- ArrayList 实现了**RandomAccess 接口**， RandomAccess 是一个标志接口，表明实现这个这个接口的 List 集合是支持**快速随机访问**的。在 ArrayList 中，我们即可以通过元素的序号快速获取元素对象，这就是快速随机访问。

- ArrayList 实现了**Cloneable 接口**，即覆盖了函数 clone()，**能被克隆**。

- ArrayList 实现**java.io.Serializable 接口**，这意味着ArrayList**支持序列化**，**能通过序列化去传输**。

  

- 和 Vector 不同，**ArrayList 中的操作不是线程安全的**！所以，建议在单线程中才使用 ArrayList，而在多线程中可以选择 Vector 或者 CopyOnWriteArrayList。

#### ArrayList的核心属性

数组的默认初始容量是10

```java
private static final int DEFAULT_CAPACITY = 10;
```

用来存储的数组

```java
transient Object[] elementData;
```

数组中元素的数量

```java
    private int size;
```

空的数组，用于构造空实例

```java
 private static final Object[] EMPTY_ELEMENTDATA = {};
```

用于默认大小的空实例的共享空数组实例。将其与**EMPTY_ELEMENTDATA**区分开来，以便知道添加第一个元素时需要增加多少。

```java
    private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};
```



#### ArrayList的构造方法

```java
//ArrayList的出入容量的有构造
public ArrayList(int initialCapacity) {
    	//如果传入的容量大于0，使用插入的initialCapacity变量初始化elementData
        if (initialCapacity > 0) {
            this.elementData = new Object[initialCapacity];
        //如果传入的容量的小是0，直接将空实例数组，赋值给存储元素的数组
        } else if (initialCapacity == 0) {
            this.elementData = EMPTY_ELEMENTDATA;
        } 
		//否则传入的参数是不合法的，抛出异常    
    	else {
            throw new IllegalArgumentException("Illegal Capacity: "+
                                               initialCapacity);
        }
    }
	//ArrayList的无参构造，使用默认容量
    public ArrayList() {
        this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
    }
	/**
	*构造一个指定元素集合的元素的列表，按照他们由迭代器返回的顺序
	*/
    public ArrayList(Collection<? extends E> c) {
        //将指定集合转化为数组
        elementData = c.toArray();
        //如果传入的数组长度不为0
        if ((size = elementData.length) != 0) {
            // 在上面的数组转化过程中，返回的不是Object类型
            if (elementData.getClass() != Object[].class)
                将原来不是Object类型的数组元素转化为Object并赋值
                elementData = Arrays.copyOf(elementData, size, Object[].class);
        } else {
            // replace with empty array.
            this.elementData = EMPTY_ELEMENTDATA;
        }
    }

```

#### Arraylist的核心方法

##### trimToSize()

将ArrayList的大小调整为当前数组中存在元素的大小，使用该方法来缩小数组容量。

```java
public void trimToSize() {
        modCount++;
    	//如果数组中当前的容量大于数组中元素的容量
        if (size < elementData.length) {
            //数组容量为0，将空数组赋值为存储元素的数组，否则复制数组并重新分配容量
            elementData = (size == 0)
              ? EMPTY_ELEMENTDATA
              : Arrays.copyOf(elementData, size);
        }
    }
```

##### ArrayList的扩容

具体的扩容方法在注释中有详细解释，需要注意的是对位运算的使用，

```java
//如果需要，增加这个ArrayList实例的容量，以确保它至少可以容纳最小容量参数指定的元素数量。minCapacity所需要的最小容量
public void ensureCapacity(int minCapacity) {
    	//设置最小长度的值，如果是true，设置minExpand为0，如果是false，设置minExpand为10（默认容量）
        int minExpand = (elementData != DEFAULTCAPACITY_EMPTY_ELEMENTDATA)
            // any size if not default element table
            ? 0
            // larger than default for default empty table. It's already
            // supposed to be at default size.
            : DEFAULT_CAPACITY;
    	//如果已有的最大容量小于传入的最小容量，将传入的变量传递给下一个函数
        if (minCapacity > minExpand) {
            ensureExplicitCapacity(minCapacity);
        }
    }
       //得到最小扩容量
private void ensureCapacityInternal(int minCapacity) {
       //如果存储元素的数组等于 DEFAULTCAPACITY_EMPTY_ELEMENTDATA数组
        if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
            //获取默认容量和传入参数的最大值
            minCapacity = Math.max(DEFAULT_CAPACITY, minCapacity);
        }
    	//
        ensureExplicitCapacity(minCapacity);
    }
//
private void ensureExplicitCapacity(int minCapacity) {
    	//用于迭代器验证
        modCount++;
        // overflow-conscious code
    	//如果确定最小容量大于存储元素的个数（就是要存储的元素个数第时机的容量多），触发扩容
        if (minCapacity - elementData.length > 0)
            grow(minCapacity);
    }
	//要分配的最大数组大小
 private static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;
	//扩容函数，min是上面函数传递过来的数组最小需要扩容
  private void grow(int minCapacity) {
      	//oldCapacity为旧容量，newCapacity为新容量
        int oldCapacity = elementData.length;
      	//newCapacity设置为原数组的1.5倍。oldCapacity >> 1,等价于oldCapacity/2，位运算效率更高
        int newCapacity = oldCapacity + (oldCapacity >> 1);
      	//如果newCapacity下于传入的最小扩容容量，使用传入的最小扩容容量
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;
      	//新的容量大于定义的数组最大容量，则新容量是Integer.MAX_VALUE，否则，新容量大小则为 MAX_ARRAY_SIZE
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity);
        // minCapacity is usually close to size, so this is a win:
      	//旧数组搬移到新设定长度的数组中
        elementData = Arrays.copyOf(elementData, newCapacity);
    }

    private static int hugeCapacity(int minCapacity) {
        //输入不合法，抛出异常
        if (minCapacity < 0) // overflow
            throw new OutOfMemoryError();
        //如果true,返回Integer.MAX_VALUE，否则，返回MAX_ARRAY_SIZE
        return (minCapacity > MAX_ARRAY_SIZE) ?
            Integer.MAX_VALUE :
            MAX_ARRAY_SIZE;
    }
```

##### 一些常用的方法

```java
 /**
     * Returns the number of elements in this list.
     *返回数组元素的个数
     * 
     */
    public int size() {
        return size;
    }

    /**
     * Returns <tt>true</tt> if this list contains no elements.
     *判断数组是否为空
     * @return <tt>true</tt> if this list contains no elements
     */
    public boolean isEmpty() {
        return size == 0;
    }

    /**
  	 *判断数组是否包含传入的元素
     */
    public boolean contains(Object o) {
        return indexOf(o) >= 0;
    }

    /**
     *正向查找一个元素第一次出现在数组的位置。如果存在返回索引值，如果不存在，返回-1
     */
    public int indexOf(Object o) {
        if (o == null) {
            for (int i = 0; i < size; i++)
                if (elementData[i]==null)
                    return i;
        } else {
            for (int i = 0; i < size; i++)
                if (o.equals(elementData[i]))
                    return i;
        }
        return -1;
    }

    /**
     *查找一个元素最后一次出现在数组的位置。如果存在返回索引值，如果不存在，返回-1
     */
    public int lastIndexOf(Object o) {
        if (o == null) {
            for (int i = size-1; i >= 0; i--)
                if (elementData[i]==null)
                    return i;
        } else {
            for (int i = size-1; i >= 0; i--)
                if (o.equals(elementData[i]))
                    return i;
        }
        return -1;
    }

    /**
     *返回数组的浅拷贝
     */
    public Object clone() {
        try {
            ArrayList<?> v = (ArrayList<?>) super.clone();
            v.elementData = Arrays.copyOf(elementData, size);
            v.modCount = 0;
            return v;
        } catch (CloneNotSupportedException e) {
            // this shouldn't happen, since we are Cloneable
            throw new InternalError(e);
        }
    }

    /**
     *将ArrayList返回到一个数组中，返回的数组是独立的，你可以修改，不会对原List由影响
     */
    public Object[] toArray() {
        return Arrays.copyOf(elementData, size);
    }
```

##### 增加元素的操作

```java
 //将传入的参数追加在list的结尾
public boolean add(E e) {
    	//检查是否需要扩容
        ensureCapacityInternal(size + 1);  // Increments modCount!!
    	//相当于对数组的复制操作，并维护size
        elementData[size++] = e;
        return true;
    }

    /**
  	 *向list的指定位置添加元素
     */
    public void add(int index, E element) {
        //判断索引是否合法
        rangeCheckForAdd(index);
        //数组是否需要扩容
        ensureCapacityInternal(size + 1);  // Increments modCount!!
        //在指定位置添加元素，并将后面的数据迁移
        System.arraycopy(elementData, index, elementData, index + 1,
                         size - index);
        elementData[index] = element;
        //size维护
        size++;
    }
	//判断索引合法函数
private void rangeCheckForAdd(int index) {
        if (index > size || index < 0)
            throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
    }

```

##### 删除元素的操作

```java
//将指定索引元素删除，并返回删除元素的值，同时进行数据的搬移
public E remove(int index) {
        //检查输入索引是否合法
        rangeCheck(index);
        modCount++;
    	//保存删除元素的值	
        E oldValue = elementData(index);
		//数据的迁移	
        int numMoved = size - index - 1;
        if (numMoved > 0)
           System.arraycopy(elementData, index+1, elementData, index,
                             numMoved);
    	//将最后一个位置元素赋值为null，等待垃圾回收
        elementData[--size] = null; // clear to let GC do its work

        return oldValue;
    }

    /**
     *删除数组中传入的元素，如果没有该元素返回false，删除成功返回true
     */
    public boolean remove(Object o) {
        //遍历查找
        if (o == null) {
            for (int index = 0; index < size; index++)
                if (elementData[index] == null) {
                    fastRemove(index);
                    return true;
                }
        } else {
            for (int index = 0; index < size; index++)
                if (o.equals(elementData[index])) {
                    fastRemove(index);
                    return true;
                }
        }
        return false;
    }

    /*
     *跳过边界检查且不返回已删除值的私有移除方法。
     */
    private void fastRemove(int index) {
        modCount++;
        int numMoved = size - index - 1;
        if (numMoved > 0)
            System.arraycopy(elementData, index+1, elementData, index,
                             numMoved);
        elementData[--size] = null; // clear to let GC do its work
    }

    /**
     * 删除list中的所有元素
     */
    public void clear() {
        modCount++;

        // clear to let GC do its work
        for (int i = 0; i < size; i++)
            elementData[i] = null;

        size = 0;
    }
```

##### 修改元素

```java
public E set(int index, E element) {
        rangeCheck(index);

        E oldValue = elementData(index);
        elementData[index] = element;
        return oldValue;
    }
```

##### 查找元素

```java
E elementData(int index) {
        return (E) elementData[index];
    }

    /**
     * Returns the element at the specified position in this list.
     *
     * @param  index index of the element to return
     * @return the element at the specified position in this list
     * @throws IndexOutOfBoundsException {@inheritDoc}
     */
    public E get(int index) {
        //边界检查
        rangeCheck(index);
		//返回数组对应下标的元素
        return elementData(index);
    }
```

##### System.arraycopy()和Arrays.copyOf()方法

上面再分析源码的时候可以看到这两个方法被调用，下面就对这两个方法分析。

###### System.arraycopy()

被native修饰，所以我们对其中的参数进行分析。

```java
     * @param      src      the source array.（源数组）
     * @param      srcPos   starting position in the source array.（源数组的起始位置）
     * @param      dest     the destination array.（目标数组）
     * @param      destPos  starting position in the destination data.（目标数组的起始位置）
     * @param      length   the number of array elements to be copied.（要复制的数组元素的数目）
public static native void arraycopy(Object src,  int  srcPos,
                                        Object dest, int destPos,
                                        int length);
```

分析完参数后，找源码中具体的使用，再add方法中，只用 System.arraycopy()实现自我数组的迁移。

```java
     /* 在此列表中的指定位置插入指定的元素。 
     *先调用 rangeCheckForAdd 对index进行界限检查；然后调用 ensureCapacityInternal 方法保证capacity足够大；
     *再将从index开始之后的所有成员后移一个位置；将element插入index位置；最后size加1。
     */
    public void add(int index, E element) {
        rangeCheckForAdd(index);

        ensureCapacityInternal(size + 1);  // Increments modCount!!
        //arraycopy()方法实现数组自己复制自己
        //elementData:源数组;index:源数组中的起始位置;elementData：目标数组；index + 1：目标数组中的起始位置； size - index：要复制的数组元素的数量；
        System.arraycopy(elementData, index, elementData, index + 1, size - index);
        elementData[index] = element;
        size++;
    }
```

###### Arrays.copyOf()

通过源码，他也是调用了System.arraycopy()方法，但是他在自己的方法体内创建了一个数组，然后通过System.arraycopy()完成数组的拷贝。

```java
public static <T,U> T[] copyOf(U[] original, int newLength, Class<? extends T[]> newType) {
        @SuppressWarnings("unchecked")
        T[] copy = ((Object)newType == (Object)Object[].class)
            ? (T[]) new Object[newLength]
            : (T[]) Array.newInstance(newType.getComponentType(), newLength);
        System.arraycopy(original, 0, copy, 0,
                         Math.min(original.length, newLength));
        return copy;
    }
```

下面是源码中的使用实例

```java
public Object[] toArray() {
    //elementData：要复制的数组；size：要复制的长度
        return Arrays.copyOf(elementData, size);
    }
```

###### 两者联系与区别

**联系：** 看两者源代码可以发现`copyOf()`内部调用了`System.arraycopy()`方法

 **区别：**

1. arraycopy()需要目标数组，将原数组拷贝到你自己定义的数组里，而且可以选择拷贝的起点和长度以及放入新数组中的位置。
2. copyOf()是系统自动在内部新建一个数组，并返回该数组。、

##### 序列化和反序列化

ArrayList 基于数组实现，并且具有动态扩容特性，因此保存元素的数组不一定都会被使用，那么就没必要全部进行序列化。

保存元素的数组 elementData 使用 transient 修饰，该关键字声明数组默认不会被序列化。

```java
transient Object[] elementData; // non-private to simplify nested class access
```

ArrayList 实现了 writeObject() 和 readObject() 来控制只序列化数组中有元素填充那部分内容。

##### fail-fast

###### 百度百科

**fail-fast 机制是java集合(Collection)中的一种错误机制。**当多个线程对同一个集合的内容进行操作时，就可能会产生fail-fast事件。例如：当某一个线程A通过iterator去遍历某集合的过程中，若该集合的内容被其他线程所改变了；那么线程A访问集合时，就会抛出ConcurrentModificationException异常，产生fail-fast事件。

再源码中会看到很多的mount++，他是用来记录对ArraryList结构发生的次数。结构发生变化是指添加或者删除至少一个元素的所有操作，或者是调整内部数组的大小，仅仅只是设置元素的值不算结构发生变化。

当在序列化或者迭代的操作时候，需要比较操作前后的modCount 是否改变，如果改变则抛出异常。