# Leetcode.283 Move Zeroes

Given an array `nums`, write a function to move all `0`'s to the end of it while maintaining the relative order of the non-zero elements.

**Example:**

```
Input: [0,1,0,3,12]
Output: [1,3,12,0,0]
```

**Note**:

1. You must do this **in-place** without making a copy of the array.
2. Minimize the total number of operations.

# Solution

## 移位置零

- 填充
- 移位
- 置零

```python
class Solution:
    def moveZeroes(self, nums: list[int]) -> None:
        """
        填充，移位，置零
        """
        # j指向非0数字将要插入的位置
        j = 0
        n = len(nums)
        for i in range(n):
            if nums[i] != 0:
                nums[j] = nums[i]
                j += 1

        for i in range(j, n):
            nums[i] = 0
```

## 快慢指针

利用一个指针指向列表第一个0，寻找之后非零数字与其交换

```python
class Solution:
    def moveZeroes(self, nums: List[int]) -> None:
        """
        Do not return anything, modify nums in-place instead.
        """
        if not nums:
            return nums
        
        zero = 0
        n = len(nums)
        # zero point first 0
        while zero<n and nums[zero]!=0:
            zero+=1
        
        i = zero +1
        while i<n:
            if nums[i] != 0:
                nums[zero],nums[i] = nums[i],nums[zero]
                zero+=1
            i+=1
```

