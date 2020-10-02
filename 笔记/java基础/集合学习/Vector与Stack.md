### Vactor

##### ArrayList与Vactor的对比

他与ArrayList相似，底层都是使用数组实现。

下面是他们的不同点

- Vactor使用synchronized关键字实现同步，ArrayList如果要实现同步需要使用, ` Collections.synchronizedList()`实现同步。
- Vactor在扩容容量上不同，ArrayList扩容是原始容量的1.5倍，Vactor是初始容量的2倍(默认情况下)。

下面看一个特殊情况

如果在初始化 Vector的时候，确定capacityIncrement的值，在扩容的时候，`  int newCapacity = oldCapacity + ((capacityIncrement > 0) ?capacityIncrement : oldCapacity);`就会将新的容量扩大到`oldCapacity +capacityIncrement `的大小，而不是原来的二倍。

```java
//
protected int capacityIncrement;
//传入两个参数的构造方法
public Vector(int initialCapacity, int capacityIncrement) {
        super();
        if (initialCapacity < 0)
            throw new IllegalArgumentException("Illegal Capacity: "+
                                               initialCapacity);
        this.elementData = new Object[initialCapacity];
        this.capacityIncrement = capacityIncrement;
    }

//扩容
private void grow(int minCapacity) {
        // overflow-conscious code
        int oldCapacity = elementData.length;
        int newCapacity = oldCapacity + ((capacityIncrement > 0) ?
                                         capacityIncrement : oldCapacity);
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity);
        elementData = Arrays.copyOf(elementData, newCapacity);
    }
```

### Stack

#### 简述

**Stack**是java实现的一个栈。特征是先进后出。

#### 源码

**Stack**继承了Vector类，并对其进行扩展。

```java
public class Stack<E> extends Vector<E>
```

##### 扩展方法

```java
  public Stack() {
    }
    //向栈中加入一个元素
     public E push(E item) {
        addElement(item);

        return item;
    }
    //移除栈的第一个元素并返回该元素
    public synchronized E pop() {
        E       obj;
        int     len = size();
        obj = peek();
        removeElementAt(len - 1);

        return obj;
    }
    //查看栈的第一个元素，不移除
    public synchronized E peek() {
        int     len = size();
        if (len == 0)
            throw new EmptyStackException();
        return elementAt(len - 1);
    }
    //堆是否为空
    public boolean empty() {
        return size() == 0;
    }
```

### 总结

在java类集中，ArrayList、Statck、Vector都是通过数组实现的。

ArrayList优点：

- 支持随机访问，依靠索引查找元素效率高。

ArrayList缺点：

- 由于数组的内存连续，所以在删除、插入一个元素在中间位置时，会耗费性能。

ArrayList和Vector的区别在开头已经写过，这就不重复了。