[nowcoder](https://www.nowcoder.com/practice/6a296eb82cf844ca8539b57c23e6e9bf?tpId=13&&tqId=11182&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

#### 题目描述

输入n个整数，找出其中最小的K个数。例如输入4,5,1,6,2,7,3,8这8个数字，则最小的4个数字是1,2,3,4。

```
输入
[4,5,1,6,2,7,3,8],4
返回值
[1,2,3,4]
```

#### 解题思路

将数组排序返回，应使用时间复杂度较低的排序算法。

堆排序实现

```java
import java.util.ArrayList;
public class Solution {
    public ArrayList<Integer> GetLeastNumbers_Solution(int [] input, int k) {
        ArrayList<Integer> list=new ArrayList<>();
        if(input.length==0||input.length<k){
            return list;
        }
        //使用堆排序
        sort(input,input.length);
        for(int i=0;i<k;i++){
            list.add(input[i]);
        }
        return list;
    }
    //堆排序函数
    public void sort(int[] a,int length){
        //将传入的数组原地建堆
        bulidHeap(a);
        //对数组中的元素进行堆排序
        int k=length-1;
        while(k>0){
            //堆顶的元素是数组中的最大值，将其和堆尾的元素交换
            swap(a,0,k);
            //交换以后剩下的元素不满足堆的要求，所以要堆化
            heapify(a,--k,0);
        }
    }
    
    //将数组传入进行建堆
   public void bulidHeap(int[] arr){
        //完全二叉树的叶子节点不需要堆化（a.length）/2
        for(int i=arr.length-1;i>=0;i--){
            heapify(arr,arr.length-1,i);
        }
    }
    //堆化函数
    public void heapify(int[] arr,int n,int i){
        //用来记录最大值元素
            int max=i;
        while(true){
            //使用完全二叉树的性质，如果从数组下标为0的位置开始存储元素，
            //i*2+1存放左子树i*2+2存放右子树
            if(i*2+1<=n&&arr[i]<arr[i*2+1]){
                max=i*2+1;
            }
            if(i*2+2<=n&& arr[max]<arr[i*2+2]){
                max=i*2+2;
            }
            //如果没有发生交换，说明当前元素是最大值元素,退出循环
            if(max==i){
                break;
            }
            //交换最大值元素和当前下标元素
            swap(arr,i,max);
            //向下继续堆化
            i=max;
        }
    }
    //交换函数
    public void swap(int[] arr,int i,int j){
        int temp=arr[i];
        arr[i]=arr[j];
        arr[j]=temp;
    }
    }
```

