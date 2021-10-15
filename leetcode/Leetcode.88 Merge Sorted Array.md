# Leetcode.88 Merge Sorted Array

Given two sorted integer arrays *nums1* and *nums2*, merge *nums2* into *nums1* as one sorted array.

**Note:**

- The number of elements initialized in *nums1* and *nums2* are *m* and *n* respectively.
- You may assume that *nums1* has enough space (size that is **equal** to *m* + *n*) to hold additional elements from *nums2*.

**Example:**

```
Input:
nums1 = [1,2,3,0,0,0], m = 3
nums2 = [2,5,6],       n = 3

Output: [1,2,2,3,5,6]
```

 

**Constraints:**

- `-10^9 <= nums1[i], nums2[i] <= 10^9`
- `nums1.length == m + n`
- `nums2.length == n`



# Solution

```python
class Solution:
    def merge(self, nums1: List[int], m: int, nums2: List[int], n: int) -> None:
        """
        Do not return anything, modify nums1 in-place instead.
        """
        end = m + n - 1
        while m > 0 and n > 0:
            if nums1[m-1] > nums2[n-1]:
                nums1[end] = nums1[m-1]
                end -= 1
                m -= 1
            elif nums1[m-1] < nums2[n-1]:
                nums1[end] = nums2[n-1]
                end -= 1
                n -= 1
            else:
                assert nums1[m-1] == nums2[n-1]
                nums1[end] = nums1[m-1]
                m -= 1
                end -= 1
                nums1[end] = nums2[n-1]
                n -= 1
                end -= 1
        # 不需要判断m，由于都是存在nums1里边的，快排也可以借鉴这个思路
        while n > 0:
            nums1[end] = nums2[n-1]
            end -= 1
            n -= 1
        
```

