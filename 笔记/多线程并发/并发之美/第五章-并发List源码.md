#### 5.1CopyOnWriteArrayList简介



#### 5.2源码分析

##### 5.2.1 重要属性

```java
  /** The lock protecting all mutators */
    final transient ReentrantLock lock = new ReentrantLock();

    /** The array, accessed only via getArray/setArray. */
    private transient volatile Object[] array;
```

 ReentrantLock：独占锁的作用是保证只有一个线程对array进行修改。

array：CopyOnWriteArrayList的底层数据结构支持。

##### 5.2.2 初始化

```java
	//无参构造
    public CopyOnWriteArrayList() {
        setArray(new Object[0]);
    }
	//传入一个集合参数，将参数里面的集合元素复制到该List中
    public CopyOnWriteArrayList(Collection<? extends E> c) {
        Object[] elements;
        //如果传入的的集合类型是CopyOnWriteArrayList
        if (c.getClass() == CopyOnWriteArrayList.class)
            elements = ((CopyOnWriteArrayList<?>)c).getArray();
        else {
            elements = c.toArray();
            // c.toArray might (incorrectly) not return Object[] (see 6260652)
            if (elements.getClass() != Object[].class)
                elements = Arrays.copyOf(elements, elements.length, Object[].class);
        }
        setArray(elements);
    }

    public CopyOnWriteArrayList(E[] toCopyIn) {
        setArray(Arrays.copyOf(toCopyIn, toCopyIn.length, Object[].class));
    }

```

