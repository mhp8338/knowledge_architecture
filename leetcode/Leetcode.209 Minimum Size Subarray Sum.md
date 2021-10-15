#  Leetcode.209 Minimum Size Subarray Sum

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

#### Python 

- 时间复杂度：$O(n)$
- 空间复杂度：$O(1)$

```python
class Solution:
    def minSubArrayLen(self, s: int, nums: List[int]) -> int:
        n = len(nums)
        # 判断为空的时候
        if n == 0:
            return 0
        # res：（1）记录结果值 （2）处理没有结果时的情况
        res = n+1
        l = 0
        r = 0
        su = nums[l]
        while l < n:
            if su >= s:
                res = min(r - l + 1, res)
                su -= nums[l]
                l += 1
            elif r < n-1:
                r += 1
                su += nums[r]
            else:
                break
        if res == n+1:
            return 0
        return res
```

## 2. 二分查找法

主要思路：利用二分法查找符合条件的框最小尺寸

相当于在$[l,r]$中查找size位置，满足条件，最后得到的$size+1$才是真正的尺寸

```python
class Solution:
    def minSubArrayLen(self, s: int, nums: List[int]) -> int:
        l = 0
        r = len(nums) - 1
        res = 0
        while l <= r:
            size = l + (r - l)//2
            if self._find_aim(s, nums, size):
                r = size-1
                res = size + 1
            else:
                l = size+1
        return 0 if res == 0 else res
                
    def _find_aim(self,s,nums,size):
        sum = 0
        for i in range(len(nums)):
            sum += nums[i]
            if i > size:
                sum -= nums[i-size-1]
            if sum >= s:
                return True
        return False
```

## 3. 得到sum数组，利用二分查找法寻找目标数

#### Java

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

##### 优化：设定二分的左区间

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

