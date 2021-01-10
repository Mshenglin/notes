[nowcoder]()

```java
import java.util.ArrayList;
public class Solution {
    public ArrayList<Integer> FindNumbersWithSum(int [] array,int sum) {
        //用于返回结果集
        ArrayList<Integer> list=new ArrayList<>();
        //i为头向后的指针，j为后往前的指针
        int i=0,j=array.length-1;
        //该值用来保存满足条件的乘积
        long target=Integer.MAX_VALUE;
       //循环结束的条件
        while(i<j){
            //判断当前的两个数是否满足要求
            int temp=array[i]+array[j];
            //如果是小于目标值，左指针移动
            if(temp<sum){
                i++;
            }
            //如果是大于目标值，右指针移动
            else if(temp>sum){
                j--;
            }
            else{
                //判断满足要求的两个元素是否小于乘积最小的要求
                if(array[i]*array[j]<target){
                    //更新乘积最小值结果
                    target=array[i]*array[j];
                    //清除list结果集中的元素
                    list.clear();
                    //将新的加入
                    list.add(array[i]);
                    list.add(array[j]);
                }
                //同时移动两个指针
                i++;
                j--;
            }
        }
        return list;
        
    }
}
```

