# Leetcode.215 Kth Largest Element in an Array

Find the **k**th largest element in an unsorted array. Note that it is the kth largest element in the sorted order, not the kth distinct element.

**Example 1:**

```
Input: [3,2,1,5,6,4] and k = 2
Output: 5
```

**Example 2:**

```
Input: [3,2,3,1,2,4,5,5,6] and k = 4
Output: 4
```

**Note:**
You may assume k is always valid, 1 ≤ k ≤ array's length.

# Solution

## 快排partition的思想

- 时间复杂度：O(logn)
- 空间复杂度：O(1)

```python
import random

class Solution:
    def findKthLargest(self, nums: List[int], k: int) -> int:
        n = len(nums)
        left = 0
        right = n - 1
        target = n - k
        while True:
            po = self._partional(left,right,nums)
            if po == target:
                return nums[po]
            elif po > target:
                right = po - 1
            else:
                left = po + 1
                   
    def _partional(self, start: int, end: int, nums: List[int]) -> int:
        a = random.randint(start,end)
        nums[start],nums[a] = nums[a],nums[start]
        r = nums[start]
        # [right,end] > r
        # [start + 1,left] < r
        # [left+1,i] = r
        right = end + 1
        left = start
        i = start
        while i < right:
            if nums[i] > r:
                right -= 1
                nums[right],nums[i] = nums[i], nums[right]
            elif nums[i] < r:
                left += 1
                nums[left], nums[i] = nums[i], nums[left]
                i += 1
            else:
                i += 1
        nums[left],nums[start] = nums[start],nums[left]
        left -= 1
        return left+1
```

## 堆的思想