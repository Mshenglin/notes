### 题目描述

实现一种算法，找出单向链表中倒数第 k 个节点。返回该节点的值。

注意：本题相对原题稍作改动

示例：

输入： 1->2->3->4->5 和 k = 2
输出： 4
说明：

给定的 k 保证是有效的。

### 解题思路

#### 思路一 使用哈希表

将链表的元素以及顺序储存在map中，利用技术顺序将Map中的值取出。

代码如下

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public int kthToLast(ListNode head, int k) {
        HashMap<Integer,Integer> map=new HashMap<>();
        int i=1;
        while(head!=null){
            map.put(i++,head.val);
            head=head.next;
        }
       return map.get(i-k); 
    }
}
```

#### 思路二 使用双指针

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public int kthToLast(ListNode head, int k) {
         ListNode p=head;
         //前指针
        for(int i=0;i<k;i++)
        {
            p=p.next;
        }
        while(p!=null){
            p=p.next;
            head=head.next;
        }
        return head.val;
    }
}
```

