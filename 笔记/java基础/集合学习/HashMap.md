##  HashMap

#### HashMap基本实现原理

​    HashMap是根据哈希表实现的。他的底层是基于数组+链表实现完成的（java6，7）。

Java8对其结构进行了改变。当链表的长度大于8就会将链表转化为红黑树。

##### jdk源码

##### 一、类与基本属性

```java
public class HashMap<K,V>
    extends AbstractMap<K,V>
    implements Map<K,V>, Cloneable, Serializable
```

根据上面的代码，可以看出他继承了AbstractMap类，并实现了Map<K,V>根接口，Cloneable接口（可以被克隆），Serializable（可以序列化）。

```java
/**
     * 
     *默认初始容量，必须是二的整数次幂
     */
    static final int DEFAULT_INITIAL_CAPACITY = 16;
/**
*最大容量，2^30(当初始化容量大于该值，默认设置成改值)
*/
    static final int MAXIMUM_CAPACITY = 1 << 30;
/**
*默认加载因子，大小为0.75
*/
   static final float DEFAULT_LOAD_FACTOR = 0.75f;
/**
*链表树化的临界值，当链表的长度>=8时，链表转化为红黑树
*/
  static final int TREEIFY_THRESHOLD = 8;
/**
*红黑树转化为链表的临界值，当在扩容时，重新计算储存位置，当原有的红黑树的数量<=6时，
*/
 static final int UNTREEIFY_THRESHOLD = 6;
/**
*储存数据的Node数组，长度为2的整数次幂
*/
transient Entry[] table;
/**
*HashMap中存储的key-value对的个数
*/
 transient int size;
/**
* HashMap的临界值，用于判断是否需要调整HashMap的容量（threshold = 容量*加载因子）
*/
   int threshold;
/**
*HashMap被改变的次数
*/
    transient int modCount;
/**
*最小树形化容量，当内部的数组长度小于64时，链表不会转化为树，优先扩充数组
*/
  static final int MIN_TREEIFY_CAPACITY = 64;
```

加载因子的理解：https://my.oschina.net/weiweiblog/blog/612812

> ###### 何为加载因子？
>
> 加载因子是表示Hash表中元素的填满的程度.若:加载因子越大,填满的元素越多,好处是,空间利用率高了,但:冲突的机会加大了.反之,加载因子越小,填满的元素越少,好处是:冲突的机会减小了,但:空间浪费多了.
>
>
> 冲突的机会越大,则查找的成本越高.反之,查找的成本越小.因而,查找时间就越小.
>
> 因此,必须在 "冲突的机会"与"空间利用率"之间寻找一种平衡与折衷. 这种平衡与折衷本质上是数据结构中有名的"时-空"矛盾的平衡与折衷.
>

##### 二、构造方法



```java
     public HashMap(int initialCapacity, float loadFactor) {
         //判断传入大小是否合法
        if (initialCapacity < 0)
            throw new IllegalArgumentException("Illegal initial capacity: " +
                                               initialCapacity);
         //判断传入的大小是否大于MAXIMUM_CAPACITY，若大于则容量值为MAXIMUM_CAPACITY
        if (initialCapacity > MAXIMUM_CAPACITY)
            initialCapacity = MAXIMUM_CAPACITY;
         //判断传入的负载因子值，负载因子必须大于0
        if (loadFactor <= 0 || Float.isNaN(loadFactor))
            throw new IllegalArgumentException("Illegal load factor: " +
                   loadFactor);
         //设置负载因子
    this.loadFactor = loadFactor;
         //保证容量为2的数次幂
        this.threshold = tableSizeFor(initialCapacity);
     }
//tableSizeFor方法
          static final int tableSizeFor(int cap) {
        int n = cap - 1;
        n |= n >>> 1;
        n |= n >>> 2;
        n |= n >>> 4;
        n |= n >>> 8;
        n |= n >>> 16;
        return (n < 0) ? 1 : (n >= MAXIMUM_CAPACITY) ? MAXIMUM_CAPACITY : n + 1;
    }



```
​	在上面代码中tableSizeFor（）方法实现了将传进来的大小参数转化为大于initialCapacity值的最小2的整数次幂的数。

具体时如何实现的呢？

下面就以一个具体的例子来对该方法进行详细的讲解。

假如传入参数为25，该数的二进制码为  11001

 int n = cap - 1;				这行代码防止参数为2的整数次幂，执行完方法后会变成2*cap

n |=n>>>1;        这行代码可以拆解为以下几个步骤

+ n>>>1;    n 无符号右移一位，结果为 01100

+  n |=n>>>1    n与n>>>1进行取 | 运算，结果为  11101   

n|=n>>>2    

+ n>>>2   对11101    右移两位  结果00111
+ n|=n>>>2    结果为11111

由于n=11111，所以下面的运算不改变他的值

 return (n < 0) ? 1 : (n >= MAXIMUM_CAPACITY) ? MAXIMUM_CAPACITY : n + 1;

若n>MAXIMUM_CAPACITY 则返回MAXIMUM_CAPACITY(2^30),若小于则返回n+1.值为

100000=2^5。既可以保证数值为2的整数次幂。



###### Node类

```java
static class Node<K,V> implements Map.Entry<K,V> { 
//hash(key.hashcode())的值，简称hash值
final int hash; 
final K key; 
V value;    
    //链表的下一个指向
Node<K,V> next;
//构造方法
Node(int hash, K key, V value, Node<K,V> next) {
            this.hash = hash;
            this.key = key;
            this.value = value;
            this.next = next;
        }
    //得到key值
       public final K getKey()        { return key; }
    //得到value值
        public final V getValue()      { return value; }
        public final String toString() { return key + "=" + value; }
        
        public final int hashCode() {
            return Objects.hashCode(key) ^ Objects.hashCode(value);
        }

        public final V setValue(V newValue) {
            V oldValue = value;
            value = newValue;
            return oldValue;
        }
                public final boolean equals(Object o) {
            if (o == this)
                return true;
            if (o instanceof Map.Entry) {
                Map.Entry<?,?> e = (Map.Entry<?,?>)o;
                if (Objects.equals(key, e.getKey()) &&
                    Objects.equals(value, e.getValue()))
                    return true;
            }
            return false;
        }
    }

```

###### hash()方法

```java
static final int hash(Object key) { 
int h;  
return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);}
```

###### put()方法

```java
public V put(K key, V value) { 
    //对key的hashCode()做hash
return putVal(hash(key), key, value, false, true);
}
final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                   boolean evict) {
    
        Node<K,V>[] tab; 
    Node<K,V> p; 
    int n, i;
    //1.判断table为空则调用resize()对其初始化
        if ((tab = table) == null || (n = tab.length) == 0)
            n = (tab = resize()).length;
    //判断数组元素是否为空，若为空创建一个新的元素
        if ((p = tab[i = (n - 1) & hash]) == null)
            tab[i] = newNode(hash, key, value, null);
    
     //若不为空，执行以下操作
        else {
            Node<K,V> e;
            K k;
            //如果key的值相同，直接覆盖
            if (p.hash == hash &&
                ((k = p.key) == key || (key != null && key.equals(k))))
                e = p;
            //判断p是否是红黑树
            else if (p instanceof TreeNode)
                //如果是执行运行以下方法
                e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
            else {
                //如果不是，则p为链表
                //对链表进行遍历
                for (int binCount = 0; ; ++binCount) {
                    //如果p的next为空，将新的值添加到链表后面
                    if ((e = p.next) == null) {
                        p.next = newNode(hash, key, value, null);
                        //如果链表长度大于8，链表转化为红黑树，执行插入操作
                        if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                            treeifyBin(tab, hash);
                        break;
                    }
                   // 找到相同的key值元素，跳出循环
                    if (e.hash == hash &&
                        ((k = e.key) == key || (key != null && key.equals(k))))
                        break;
                    //移动指针，指向p.next
                    p = e;
                }
            }
            //如果上面的节点存在
            if (e != null) { // existing mapping for key
             V oldValue = e.value;  //保存旧的value值
                
                if (!onlyIfAbsent || oldValue == null)
                    e.value = value;  //将新的value值赋值给该节点的value
                
                afterNodeAccess(e);
                //返回旧的值
                return oldValue;
            }
        }
    
        ++modCount;
    //判断是否进行扩容操作
        if (++size > threshold)
            resize();
        afterNodeInsertion(evict);
        return null;
    }
```

再上面的put方法中，使用了resize()方法，下面就对该方法进行分析，如下

###### resize()方法

```java
final Node<K,V>[] resize() {
    //该方法使用情景：1.初始化化哈希表 2.容量不够，进行扩容
Node<K,V>[] oldTab = table;
int oldCap = (oldTab == null) ? 0 : oldTab.length;   //数组的长度 
int oldThr = threshold; 
int newCap, newThr = 0;
 if (oldCap > 0) {
     //若扩容前超过最大值，就不进行扩充
            if (oldCap >= MAXIMUM_CAPACITY) {
                threshold = Integer.MAX_VALUE;
                return oldTab;
            }
     //若没有超过最大值，则将newCap设置成oldCap的二倍，且 
    // DEFAULT_INITIAL_CAPACITY   <= newCap < MAXIMUM_CAPACITY
            else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
                     oldCap >= DEFAULT_INITIAL_CAPACITY)
                //将threshold扩大为原来的二倍
                newThr = oldThr << 1; // double threshold
        }
    //针对第一种情况：初始化哈希表
    //如果数组没有被创建，但是已经指定了threshold(调用有参构造)，threshold为数组的长度
    else if (oldThr > 0) // ？？ initial capacity was placed in threshold（初始容量设置成临界值）
            newCap = oldThr;
        else {  
            //使用默认初始化
           // zero initial threshold signifies using defaults
            newCap = DEFAULT_INITIAL_CAPACITY;
            newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);
        }
    //   ？？
        if (newThr == 0) {
            //
            float ft = (float)newCap * loadFactor;
            newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ?
                      (int)ft : Integer.MAX_VALUE);
        }
    //新的数组长度已经被建立
        threshold = newThr;
        @SuppressWarnings({"rawtypes","unchecked"})
    //新建一个table数组
            Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
        table = newTab;
    /**
    *  下面将会是将原来的数组转移到新的数组，table与oldTab转换
    */
        if (oldTab != null) {
            //遍历原数组
            for (int j = 0; j < oldCap; ++j) {
                
                Node<K,V> e;
                //取到数组的第j个索引元素
                if ((e = oldTab[j]) != null) {
                    oldTab[j] = null;//释放资源
                    if (e.next == null)//如果该位置只有一个元素
                        newTab[e.hash & (newCap - 1)] = e;//重新计算hash，由于数组的长度发生变化
                    //如果不为空，则判断是否是红黑树，如果是执行split(方法)
                    else if (e instanceof TreeNode)
                        ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
                    //该节点为链表
                    else { // preserve order
                        //创建四个Node的空对象
                        Node<K,V> loHead = null, loTail = null;
                        Node<K,V> hiHead = null, hiTail = null;
                        Node<K,V> next;
                        //遍历链表
                        do {
                            
                            next = e.next;
                            /**
                            *此处将一个链表拆分成两个，一个存在newTab[j]位置，另一个存在* 								*newtable[j+oldCap]位置，为何会加在该位置，下面讲解
                            */
                            //(e.hash & oldCap) == 0时，存在loHead为头的节点中
                            if ((e.hash & oldCap) == 0) {
                              //  链表的添加操作
                                if (loTail == null)
                                    loHead = e;
                                else
                                {loTail.next = e;}
                                loTail = e;
                            }
                            //否则存在hiHead为头的节点中
                            else {
                                if (hiTail == null)
                                    hiHead = e;
                                else
                                    hiTail.next = e;
                                hiTail = e;
                            }
                        } while ((e = next) != null);//终止条件，向后移动旧数组链表的位置
                        //将链表添加到新的table中
                        if (loTail != null) {
                            loTail.next = null;
                            newTab[j] = loHead;
                        }
                        if (hiTail != null) {
                            hiTail.next = null;
                            newTab[j + oldCap] = hiHead;
                        }
                    }
                }
            }
        }
        return newTab;
    }
```

为何当(e.hash & oldCap) == 1时，就会存在newTab[j+oldtable]呢？

原文地址：

> 首先我们要明确三点:
>
> 1. oldCap一定是2的整数次幂, 这里假设是2^m
> 2. newCap是oldCap的两倍, 则会是2^(m+1)
> 3. hash对数组大小取模`(n - 1) & hash` 其实就是取hash的低`m`位
>
> 例如:
> 我们假设 oldCap = 16, 即 2^4,
> 16 - 1 = 15, 二进制表示为 `0000 0000 0000 0000 0000 0000 0000 1111`
> 可见除了低4位, 其他位置都是0（简洁起见，高位的0后面就不写了）, 则 `(16-1) & hash` 自然就是取hash值的低4位,我们假设它为 `abcd`.
>
> 以此类推, 当我们将oldCap扩大两倍后, 新的index的位置就变成了 `(32-1) & hash`, 其实就是取 hash值的低5位. 那么对于同一个Node, 低5位的值无外乎下面两种情况:
>
> ```
> 0abcd
> 1abcd
> ```
>
> 其中, `0abcd`与原来的index值一致, 而`1abcd` = `0abcd + 10000` = `0abcd + oldCap`
>
> 故虽然数组大小扩大了一倍，但是同一个`key`在新旧table中对应的index却存在一定联系： 要么一致，要么相差一个 `oldCap`。
>
> 而新旧index是否一致就体现在hash值的第4位(我们把最低为称作第0位), 怎么拿到这一位的值呢, 只要:
>
> ```
> hash & 0000 0000 0000 0000 0000 0000 0001 0000
> ```
>
> 上式就等效于
>
> ```
> hash & oldCap
> ```
>
> 故得出结论:
>
> > 如果 `(e.hash & oldCap) == 0` 则该节点在新表的下标位置与旧表一致都为 `j`
> > 如果 `(e.hash & oldCap) == 1` 则该节点在新表的下标位置 `j + oldCap`

###### hash()方法以及为何要将数值设置为2的整数次幂

```java
static final int hash(Object key) { 
int h;   
return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
```

hash()方法将key的hashcode值进行无符号的右移16位，并对其异或运算，增加他的随机度。让他的高位也参与运算，变相的保留了他的高位特性。

```java
 n = (tab = resize()).length;//Hash表的长度
if ((p = tab[i = (n - 1) & hash]) == null)
```

上面这行代码决定要插入元素的具体位置。

i=(n-1)&hash等价于 i=hash % n；

举个例子说明为什么相同。假如长度为8，则二进制码为0000 1000

得到一个随机的hash为0010 1101 = 61

i=(n-1)&hash =（0000 0111）&（0010 1101）= 0000 0101=5

 i=hash % n=61% 8 = 5

因为效率的原因，所以采用i=(n-1)&hash

> 为什么hashmap是二的幂次方
>
> > 在计算插入元素在hash桶数组的索引时第三步，为了使元素分布的更加均匀，用取模操作，但是传统取模操作效率低，然后优化成h&(length-1)，设置成2幂次方，是因为2的幂次方-1后的值每一位上都是1，然后与第二步计算出的h值与的时候，最终的结果只和key的hashcode值本身有关，这样不会造成空间浪费并且分布均匀。
> >
> > 如果不是2的幂次方， 如果length不为2的幂，比如15。那么length-1的2进制就会变成1110。在h为随机数的情况下，和1110做&操作。尾数永远为0。那么0001、1001、1101等尾数为1的位置就永远不可能被entry占用。这样会造成浪费，不随机等问题。  

###### remove()方法

```java
final Node<K,V> removeNode(int hash, Object key, Object value,                           boolean matchValue, boolean movable) { 
Node<K,V>[] tab; 
    Node<K,V> p;
    int n, index;
    //
     if ((tab = table) != null && (n = tab.length) > 0 &&
            (p = tab[index = (n - 1) & hash]) != null) {
            Node<K,V> node = null, e;
         K k;
         V v;
            if (p.hash == hash &&
                ((k = p.key) == key || (key != null && key.equals(k))))
                node = p; //保存该节点
            else if ((e = p.next) != null) {
                /**
                *如果不是单个元素，
                */
                //如果是树的话，进行树的操作
                if (p instanceof TreeNode)
                    node = ((TreeNode<K,V>)p).getTreeNode(hash, key);
                else {
                    //是链表时，遍历链表元素，逐个比较
                    do {
                      
                        if (e.hash == hash &&
                            ((k = e.key) == key ||
                             (key != null && key.equals(k)))) {
                            node = e;
                            break;
                        }
                        p = e;
                    } while ((e = e.next) != null);
                }
            }
         //删除目标节点
            if (node != null && (!matchValue || (v = node.value) == value ||
                                 (value != null && value.equals(v)))) {
                //执行树操作
                if (node instanceof TreeNode)
                    ((TreeNode<K,V>)node).removeTreeNode(this, tab, movable);
                //是链表的第一个元素时
                else if (node == p)
                    tab[index] = node.next;
                //在链表的中间元素时
                else
                    p.next = node.next;
                ++modCount;
                --size;
                afterNodeRemoval(node);
                return node;
            }
        }
        return null;
    }

```

###### get()方法

```java
public V get(Object key) { 
    //传入key值
Node<K,V> e;  
return (e = getNode(hash(key), key)) == null ? null : e.value;}

 final Node<K,V> getNode(int hash, Object key) {
        Node<K,V>[] tab;
       	Node<K,V> first, e; 
     	int n;
    	 K k;
     //原Hash表不为空，长度不为零,tab[(n - 1) & hash]位置的元素不为空
     if ((tab = table) != null && (n = tab.length) > 0 &&
            (first = tab[(n - 1) & hash]) != null) {
            //如果第一个元素为所找元素，即直接返回第一个元素
            if (first.hash == hash && // always check first node
                ((k = first.key) == key || (key != null && key.equals(k))))
                return first;
         //如果该位置的元素不是一个的时候
            if ((e = first.next) != null) {
                //判断是否是树
                if (first instanceof TreeNode)
                    return ((TreeNode<K,V>)first).getTreeNode(hash, key);
                //是链表时，遍历逐一比较，如果找到返回该元素，如果不是返回null
                do {
                    if (e.hash == hash &&
                        ((k = e.key) == key || (key != null && key.equals(k))))
                        return e;
                } while ((e = e.next) != null);
            }
        }
        return null;
    }
```

