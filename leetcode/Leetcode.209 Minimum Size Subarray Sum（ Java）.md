#  Leetcode.209 Minimum Size Subarray Sum（ Java）

Given an array of **n** positive integers and a positive integer **s**, find the minimal length of a **contiguous** subarray of which the sum ≥ **s**. If there isn't one, return 0 instead.

**Example:** 

```
Input: s = 7, nums = [2,3,1,2,4,3]
Output: 2
Explanation: the subarray [4,3] has the minimal length under the problem constraint.
```

**Follow up:**

If you have figured out the *O*(*n*) solution, try coding another solution of which the time complexity is *O*(*n* log *n*). 



# Solution

## 1. 滑动窗口

```java
class Solution {
    public int minSubArrayLen(int s, int[] nums) {
        // [l,r]
        int l = 0;
        int r = -1;
        int sum = 0;
        int res = nums.length + 1;
        while(l<nums.length){
            if(r+1<nums.length && sum < s){
                sum += nums[++r];
            }else{
                sum -= nums[l++];
            }
            //  每次改变做一些判断
            if (sum >= s){
                res = Math.min(res, r - l + 1);
            }
        }
        return res == nums.length+1 ? 0 : res;
    }
}
```

## 2. 二分查找法

主要思路：利用二分法查找符合条件的框最小尺寸

相当于在$[l,r]$中查找size位置，满足条件，最后得到的$size+1$才是真正的尺寸

## 3. 得到sum数组，利用二分查找法寻找目标数

```Java
class Solution {
    public int minSubArrayLen(int s, int[] nums) {
        // sum[i] ==> nums[0]+..+nums[i-1]
        int[] sum = new int[nums.length+1];
        sum[0] = 0;
        int res = nums.length + 1;
        for(int i=1;i<=nums.length;i++){
            sum[i] = sum[i-1] + nums[i-1];
        }
        // sum[r] - sum[l] = nums[l] +..+ nums[r-1]
        for(int l=0;l<nums.length;l++){
            int r = findLowerBound(sum,sum[l]+s);
            if(r != sum.length){
                res = Math.min(res,r-1-l+1);
            }
        }
        
        return res == nums.length+1 ? 0 : res;
    }
    private int findLowerBound(int[] sum, int aim){
        // [l,r) find aim
        int r = sum.length;
        int l = 0;
        while (l != r){
            int mid = l + (r - l) / 2;
            if (sum[mid] >= aim){
                r = mid;
            }else{
                l = mid+1;
            }
        }
        return r;
        
    }
}
```

## 4. 优化：设定二分的左区间

```java
class Solution {
    public int minSubArrayLen(int s, int[] nums) {
        // sum[i] ==> nums[0]+..+nums[i-1]
        int[] sum = new int[nums.length+1];
        sum[0] = 0;
        int res = nums.length + 1;
        for(int i=1;i<=nums.length;i++){
            sum[i] = sum[i-1] + nums[i-1];
        }
        // sum[r] - sum[l] = nums[l] +..+ nums[r-1]
        for(int l=0;l<nums.length;l++){
            int r = findLowerBound(sum,sum[l]+s,l+1);
            if(r != sum.length){
                res = Math.min(res,r-1-l+1);
            }
        }
        
        return res == nums.length+1 ? 0 : res;
    }
    private int findLowerBound(int[] sum, int aim,int l){
        // [l,r) find aim
        int r = sum.length;
        while (l != r){
            int mid = l + (r - l) / 2;
            if (sum[mid] >= aim){
                r = mid;
            }else{
                l = mid+1;
            }
        }
        return r;
        
    }
}
```

