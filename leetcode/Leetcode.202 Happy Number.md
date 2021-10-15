# Leetcode.202 Happy Number

Write an algorithm to determine if a number `n` is "happy".

A happy number is a number defined by the following process: Starting with any positive integer, replace the number by the sum of the squares of its digits, and repeat the process until the number equals 1 (where it will stay), or it **loops endlessly in a cycle** which does not include 1. Those numbers for which this process **ends in 1** are happy numbers.

Return True if `n` is a happy number, and False if not.

**Example:** 

```
Input: 19
Output: true
Explanation: 
12 + 92 = 82
82 + 22 = 68
62 + 82 = 100
12 + 02 + 02 = 1
```

# Solution

如果是快乐数，则最终趋向1；

如果不是快乐数，则最终陷入死循环；

## Set 判定循环

```java
class Solution {
    public boolean isHappy(int n) {
        //如果非快乐数将无限循环
        Set<Integer> s = new HashSet<>();
        while(true){
            int sum = 0;
            while(n>0){
                int p = n % 10;
                sum += Math.pow(p,2);
                n = n / 10;
            }
            if(sum == 1){
                return true;
            }
            if(s.contains(sum)){
                return false;
            }else{
                s.add(sum);
            }
            n = sum;
        }
    }
}
```

## 快慢指针判定循环

```java
class Solution {
    public boolean isHappy(int n) {
        //快慢指针是两个独立的指针，是差两倍，而不是多两步
        //因此需要设置两个变量，将他们分离
        int x = n;
        int y = n;
        while(true){
            int slow = next(x);
            int fast = next(next(y));
            if(fast == 1 || slow == 1){
                return true;
            }
            if(fast == slow){
                return false;
            }
            x = slow;
            y = fast;
        }
    }
    private int next(int n){
        int sum = 0;
        while(n>0){
            int p = n % 10;
            sum += Math.pow(p,2);
            n /= 10;
        }
        return sum;
    }
}
```

