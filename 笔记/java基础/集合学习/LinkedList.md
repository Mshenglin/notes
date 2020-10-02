### LinkedList

#### 概述

LinkedList是基于双向链表实现的。它支持快速的添加元素和删除元素，但是不支持随机访问，下面通过具体的源码分析。通过类的定义，它实现了List、Deque、Cloneable、Serializable接口。

```java
public class LinkedList<E>
    extends AbstractSequentialList<E>
    implements List<E>, Deque<E>, Cloneable, java.io.Serializable
```

#### 属性

LinkedList是链表实现，在它内部定义了节点类，前驱指针，后驱指针，以及储存元素的item.

```java
 private static class Node<E> {
        E item;
        Node<E> next;
        Node<E> prev;

        Node(Node<E> prev, E element, Node<E> next) {
            this.item = element;
            this.next = next;
            this.prev = prev;
        }
    }
```

几个重要的属性

```java
//链表中存在元素的个数 
transient int size = 0;

    /**
     *头节点
     */
    transient Node<E> first;

    /**
     *尾节点
     */
    transient Node<E> last;
```

#### 构造方法

提供两个构造方法，一个是无参构造，一个是传入指定collection的元素列表，按照迭代器迭代的顺序返回给链表中，他是通过首先调用自己的无参构造构造空链表，然后将通过addAll()传入的参数添加到该列表中。

```java
public LinkedList() {
    }
    public LinkedList(Collection<? extends E> c) {
        this();
        addAll(c);
    }
```

#### 常用方法

##### addAll()

上面构造方法中使用到了该方法，下面对其分析

```java
/**
  *添加添加指定 collection 中的所有元素到此列表的结尾，顺序是指定 collection 的迭代器返回这些元素的顺序。
  */
public boolean addAll(Collection<? extends E> c) {
        return addAll(size, c);
    }

    /**
     *
     */
    public boolean addAll(int index, Collection<? extends E> c) {
        //检测返回的元素下标是否合法
        checkPositionIndex(index);
		//将传入的集合转化为数组
        Object[] a = c.toArray();
        //得到数组的长度
        int numNew = a.length;
        //如果数组中没有元素，返回false
        if (numNew == 0)
            return false;
        //找到要插入元素的前驱节点和后驱节点
        Node<E> pred, succ;
        //如果要插入的节点在链表的尾部
        if (index == size) {
            //后驱节点是null,前驱节点是链表的最后一个节点
            succ = null;
            pred = last;
        } else {
            //否则，调用node()方法得到后驱指针，然后得到前驱指针
            succ = node(index);
            pred = succ.prev;
        }
		//遍历转化的数组，存入链表中
        for (Object o : a) {
            //得到当前元素的值
            @SuppressWarnings("unchecked") E e = (E) o;
            //创建新的节点并通过上面得到的前驱节点将元素存入链表中
            Node<E> newNode = new Node<>(pred, e, null);
            //当链表尾空，当前节点就是头
            if (pred == null)
                first = newNode;
            else
                //将前驱节点的后区指针指向当前节点
                pred.next = newNode;
            //更新前驱指针，用来下一个元素的连接
            pred = newNode;
        }
        //如果插入元素在尾部，更新尾指针
        if (succ == null) {
            last = pred;
        } else {
            //否则，将插入的链表与先前的链表连接
            pred.next = succ;
            succ.prev = pred;
        }
	//更新size
        size += numNew;
        modCount++;
        return true;
    }
//查找指定索引位置的节点
Node<E> node(int index) {
        // assert isElementIndex(index);
    	//如果索引在链表长度的1/2以内
        if (index < (size >> 1)) {
            //正向遍历查找
            Node<E> x = first;
            for (int i = 0; i < index; i++)
                x = x.next;
            return x;
        } else {
            //否则反向遍历查找
            Node<E> x = last;
            for (int i = size - 1; i > index; i--)
                x = x.prev;
            return x;
        }
    }
```

通过上面的源码分析addAll()使用四个步骤：

1. 判断索引是否合法
2. 将集合元素转化为数组
3. 找到要插入位置的前驱和后驱指针
4. 遍历插入

##### 添加操作

添加操作有以下几种，链表头添加addFirst()、链表尾添加addLast()、链表中指定索引位置添加，使用add方法默认是向链表尾添加元素。

```java
//链表头添加
public void addFirst(E e) {
        linkFirst(e);
    }
    private void linkFirst(E e) 
    	//
        final Node<E> f = first;
		//创建新的节点，并将新节点的后继指针指向头节点
        final Node<E> newNode = new Node<>(null, e, f);
		//更新头节点为新创建的节点
        first = newNode;
		//如果链表中没有元素，尾节点也指向该节点
        if (f == null)
            last = newNode;
        else
            //否则将头节点的前驱节点指向新的节点
            f.prev = newNode;
		//维护size和modCount
        size++;
        modCount++;
    }
	//链表尾添加，使用add方法默认是向链表尾添加元素
    public void addLast(E e) {
        linkLast(e);
    }
    public boolean add(E e) {
        linkLast(e);
        return true;
    }
   //向链表尾添加元素
    void linkLast(E e) {
        //得到链表尾的元素
        final Node<E> l = last;
        //创建新的节点，并将他的前驱指针指向尾节点
        final Node<E> newNode = new Node<>(l, e, null);
         //更新尾节点
        last = newNode;
        //如果链表为空
        if (l == null)
            first = newNode;
        else
            l.next = newNode;
        size++;
        modCount++;
    }
	//指定索引位置添加元素
	public void add(int index, E element) {
        //检查传入的索引是否合法
        checkPositionIndex(index);
		//如果位置在连via哦的尾部
        if (index == size)
            linkLast(element);
        else
            //否则传入要添加的元素以及对应索引的节点
            linkBefore(element, node(index));
    }
     void linkBefore(E e, Node<E> succ) {
        // assert succ != null;
         //保存当前节点的其拿去节点
        final Node<E> pred = succ.prev;
         //创建新的节点，并将他们连在链表中
        final Node<E> newNode = new Node<>(pred, e, succ);
         //维护双向链表的完整性
        succ.prev = newNode;
         //如果链表为空
        if (pred == null)
            first = newNode;
        else
            pred.next = newNode;
        size++;
        modCount++;
    }
```

##### 删除操作

###### 删除头节点

**remove() ,removeFirst(),pop()**都是删除链表的头节点

```java
public E remove() {
        return removeFirst();
    }
    public E removeFirst() {
        final Node<E> f = first;
        if (f == null)
            throw new NoSuchElementException();
        return unlinkFirst(f);
    }
    public E pop() {
        return removeFirst();
    }
//删除链表头节点的核心方法，传入的是要删除的节点
    private E unlinkFirst(Node<E> f) {
        // assert f == first && f != null;
        //保存该元素的值，用来返回
        final E element = f.item;
        //保存该节点的后指针节点，删除该节点后，他就是头节点
        final Node<E> next = f.next;
        //对删除的节点赋空值
        f.item = null;
        f.next = null; // help GC
        first = next;
        //如果链表只有一个节点的情况
        if (next == null)
            last = null;
        else
            //否则释放新头节点的前驱指针
            next.prev = null;
        //维护变量
        size--;
        modCount++;
        //返回删除元素的值
        return element;
    }

```

###### 删除尾节点

**removeLast(),pollLast()**都是删除尾节点

```java
public E removeLast() {
        final Node<E> l = last;
        if (l == null)
            throw new NoSuchElementException();
        return unlinkLast(l);
    }
public E pollLast() {
        final Node<E> l = last;
        return (l == null) ? null : unlinkLast(l);
    }
    private E unlinkLast(Node<E> l) {
        // assert l == last && l != null;
         //保存该元素的值，用来返回
        final E element = l.item;
         //保存该节点的前指针节点，删除该节点后，他就是尾节点
        //后面与上面的差不多
        final Node<E> prev = l.prev;
        l.item = null;
        l.prev = null; // help GC
        last = prev;
        if (prev == null)
            first = null;
        else
            prev.next = null;
        size--;
        modCount++;
        return element;
    }
```

###### 小结

删除链表的头和尾通用步骤：

1. 保存元素用于返回。
2. 保存他的前驱(后驱)指针。
3. 将该节点的值赋值为null。
4. 判断链表是否为空，如果是空，那么更新尾节点(头节点)，否则将新头节点的前驱指针赋值空(新头节点的后驱指针赋值空)。
5. 维护变量。

###### 删除指定元素

```java
//删除指定索引的元素
public E remove(int index) {
    //检查传入索引是否合法
        checkElementIndex(index);
        return unlink(node(index));
    }
	//删除指定对象的元素
    public boolean remove(Object o) {
        //如果传入的对象为null
        if (o == null) {
            //遍历链表，逐个对比，如果找到，删除返回true
            for (Node<E> x = first; x != null; x = x.next) {
                if (x.item == null) {
                    unlink(x);
                    return true;
                }
            }
        } else {
            for (Node<E> x = first; x != null; x = x.next) {
                if (o.equals(x.item)) {
                    unlink(x);
                    return true;
                }
            }
        }
        return false;
    }
//链表中间元素的删除操作
     E unlink(Node<E> x) {
        // assert x != null;
         //保存元素用于返回
        final E element = x.item;
         //保存当前节点的前后指针
        final Node<E> next = x.next; //后继节点
        final Node<E> prev = x.prev;//前驱节点
		//删除前驱节点
        if (prev == null) {
            //要删除的节点是头节点，让头节点的指向该节点的后继节点
            first = next;
        } else {
            //否则，将前驱节点的后继节点指向后继节点
            prev.next = next;
            x.prev = null;
        }
         //删除后继节点
        if (next == null) {
            //要删除的节点是尾节点，将尾节点赋值给前驱节点
            last = prev;
        } else {
            //否则，将后继节点的前驱指针指向该节点的前驱节点
            next.prev = prev;
            x.next = null;
        }
         //值赋值为空
        x.item = null;
        size--;
        modCount++;
        return element;
    }
```

##### 小结

对LinkedList源码进行分析，也是对链表的操作的复习。LinkedList不是线程安全的，因为他不同步，可以使用`Collections.synchronizedList();` 得到一个线程安全的 ArrayList。

```java
List<String> list = new LinkedList<>();
List<String> synList = Collections.synchronizedList(list);
```

