# Leetcode.80 | Remove Duplicates from Sorted Array II

Given a sorted array *nums*, remove the duplicates [**in-place**](https://en.wikipedia.org/wiki/In-place_algorithm) such that duplicates appeared at most *twice* and return the new length.

Do not allocate extra space for another array, you must do this by **modifying the input array [in-place](https://en.wikipedia.org/wiki/In-place_algorithm)** with O(1) extra memory.

**Example 1:**

```
Given nums = [1,1,1,2,2,3],

Your function should return length = 5, with the first five elements of nums being 1, 1, 2, 2 and 3 respectively.

It doesn't matter what you leave beyond the returned length.
```

**Example 2:**

```
Given nums = [0,0,1,1,1,1,2,3,3],

Your function should return length = 7, with the first seven elements of nums being modified to 0, 0, 1, 1, 2, 3 and 3 respectively.

It doesn't matter what values are set beyond the returned length.
```

# Solution    

```python
class Solution:
    
    def removeDuplicates(self, nums: List[int]) -> int:
        n = len(nums)
        if 0 <= n <=2:
            return n
        return self._removeDuplicates_k(nums,2,n)
    
    def _removeDuplicates_k(self,nums:List[int],k:int,n:int) -> int:
            #return [0,start)
        start = 0
        for i in range(n): 
            # 循环遍历，每次只找临界重复数字应该插入的位置
            # nums[start-k] != nums[i] -> 检验插入nums[i]后是否满足条件：
            # 不满足就不插入，满足才更新插入
            if start < k or nums[start-k] != nums[i]:
                nums[start] = nums[i]
                start += 1 
        return start
                    
```

