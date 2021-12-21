[leetcode](https://leetcode-cn.com/problems/lru-cache/)

使用双链表加哈希表实现

```java
class LRUCache {
    /**
    *使用哈希表来加快节点的查找
    */
    private HashMap<Integer,Node> map;
    private DoubleList cache;
    private int cap;
    public LRUCache(int capacity) {
        map=new HashMap<>();
        cache=new DoubleList();
        this.cap=capacity;
    }
    /**
    *将两种数据结构操作再封装一个API
    *
    *将某个key提升为最近使用
    */
    public void makeRecently(int key){
        Node x=map.get(key);
        //在链表中删除该节点，并将它添加到链表的尾部
        cache.remove(x);
        cache.addLast(x);
    }
    /**
    *添加某个最近使用的元素
    */
    public void addRecently(int key,int value){
        Node x=new Node(key,value);
        //链表的尾部就是最近使用的元素所以将他加入
        cache.addLast(x);
        //并将节点加入Map中
        map.put(key,x);
    }
    /**
    *删除某个key
    */
    public void deleteKey(int key){
        Node x=map.get(key);
        //从链表中删除
        cache.remove(x);
        //从map中删除
        map.remove(key);
    }
    /**
    *删除最久未使用的元素
    */
    public void removeLeastRecently(){
        //链表的表头就是最久未使用的元素
        Node frist=cache.removeFrist();
        //同时删除map中的元素
        map.remove(frist.key);
    }
    public int get(int key) {
            if(!map.containsKey(key)){
                return -1;
            }
            //将该数据提升为最近使用
            makeRecently(key);
            return map.get(key).val;
    }
    
    public void put(int key, int value) {
        if(map.containsKey(key)){
            //删除节点
            deleteKey(key);
            //添加该元素到最近使用
            addRecently(key,value);
            return;
        }
        //如果容量满了
        if(cap==cache.size()){
            //移除最久未使用元素
            removeLeastRecently();
        }
        //添加最近使用元素
        addRecently(key,value);
    }

    /**
     *有时序，所以要结合哈希表和双向链表来实现
     *定义双向链表,靠近尾部的为最近使用的，头部的为最久未使用
     */
     //内部类链表的节点
     class Node{
         //节点包含的字段
         int key,val;
         //节点的两个指针
         Node prev,next;
         //构造函数
         public Node(int key,int value){
             this.key=key;
             this.val=value;
         }
    }
    /**
    *构造双向链表
    */
    class DoubleList{
        //记录头尾指针节点
        Node head,tail;
        //链表的节点数
        int size;
        //构造函数
        public DoubleList(){
            //初始化双向链表的数据
            head=new Node(0,0);
            tail=new Node(0,0);
            //改变节点的指针
            head.next=tail;
            tail.prev=head;
            size=0;
        }
         //双向链表的一些基本操作
         //向链表的尾部添加一个元素
    public void addLast(Node x){
        x.prev=tail.prev;
        x.next=tail;
        tail.prev.next=x;
        tail.prev=x;
        //维护size
        size++;
    }
    /**
    *删除链表中的节点
    */
    public void remove(Node x){
        x.prev.next=x.next;
        x.next.prev=x.prev;
        size--;
    }
         /**
         *删除链表中的头节点，并返回该节点
         */
         public Node removeFrist(){
             //特殊情况判断
             if(head.next==tail){
                 //链表中没有数据
                 return null;
             }
             Node frist=head.next;
             remove(frist);
             return frist;
         }
         /**
         *返回链表的长度
         */
         public int size(){
             return size;
         }
     }
     /**
     *
     */
}

/**
 * Your LRUCache object will be instantiated and called as such:
 * LRUCache obj = new LRUCache(capacity);
 * int param_1 = obj.get(key);
 * obj.put(key,value);
 */
lancetang@tencent.com 
```

