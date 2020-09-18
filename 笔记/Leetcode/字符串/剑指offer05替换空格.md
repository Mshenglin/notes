#### 题目描述

```
请实现一个函数，把字符串 s 中的每个空格替换成"%20"。
示例 1：
输入：s = "We are happy."
输出："We%20are%20happy."
限制：
0 <= s 的长度 <= 10000
```

#### 解题思路

##### 思路一

我们将字符串遍历，创建一个新的字符数组，通过StringBuilder来实现。

String类常用的Api   char[]  toCharArray();  将字符串转化为字符数组。

```java
class Solution {
    public String replaceSpace(String s) {
        StringBuilder str=new  StringBuilder();
        //遍历字符数组
        for(char c:s.toCharArray()){
          //如果该字符是' '向str追加"%20"
            if(c==' '){
                str.append("%20");
            }
            else
            str.append(c);
        }
        return str.toString();
    }
```

