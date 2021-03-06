# Leetcode.11 Container With Most Water

Given *n* non-negative integers *a1*, *a2*, ..., *an* , where each represents a point at coordinate (*i*, *ai*). *n* vertical lines are drawn such that the two endpoints of line *i* is at (*i*, *ai*) and (*i*, 0). Find two lines, which together with x-axis forms a container, such that the container contains the most water.

**Note:** You may not slant the container and *n* is at least 2.

 

![img](https://s3-lc-upload.s3.amazonaws.com/uploads/2018/07/17/question_11.jpg)

The above vertical lines are represented by array [1,8,6,2,5,4,8,3,7]. In this case, the max area of water (blue section) the container can contain is 49.

 

**Example:**

```
Input: [1,8,6,2,5,4,8,3,7]
Output: 49
```

# Solution

解题思路：

- 对撞指针
- 建立存储空间，记录最值

```python
class Solution:
    def maxArea(self, height: List[int]) -> int:
        l = 0
        r = len(height)-1
        c = self._get_container(l,r,height)
        res = [l, r, c]
        while l < r:
            if height[l] < height[r]:
                l += 1
            else:
                r -= 1
            temp = self._get_container(l,r,height)
            if res[2] < temp:
                res[0] = l
                res[1] = r
                res[2] = temp
        return res[2]
    def _get_container(self, l, r,height):
        return (r - l) * min(height[l], height[r])
```

