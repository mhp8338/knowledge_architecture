

# leetcode.167 Two Sum II - Input array is sorted

Given an array of integers that is already ***sorted in ascending order\***, find two numbers such that they add up to a specific target number.

The function twoSum should return indices of the two numbers such that they add up to the target, where index1 must be less than index2.

**Note:**

- Your returned answers (both index1 and index2) are not zero-based.
- You may assume that each input would have *exactly* one solution and you may not use the *same* element twice.

**Example:**

```
Input: numbers = [2,7,11,15], target = 9
Output: [1,2]
Explanation: The sum of 2 and 7 is 9. Therefore index1 = 1, index2 = 2.
```

# Solution

## Two Points

```python
class Solution:
    def twoSum(self, numbers: List[int], target: int) -> List[int]:
        l = 0
        r = len(numbers)-1
        while l < r:
            if numbers[l] + numbers[r] > target:
                r -= 1
            elif numbers[l] + numbers[r] < target:
                l += 1
            else:
                return [l+1,r+1]
```

## Hash

```python
class Solution:
    def twoSum(self, numbers: List[int], target: int) -> List[int]:
        temp = {}
        for i,v in enumerate(numbers):
            if target - v in temp:
                return [temp[target - v]+1,i+1]
            if v not in temp:
                temp[v] = i
```

## Binary Search

```python
class Solution:
    def twoSum(self, numbers: List[int], target: int) -> List[int]:
        for i in range(len(numbers)-1):
            aim = target - numbers[i]
            index = self._binary_search(numbers, i+1, aim)
            if index != -1:
                return [i + 1,index + 1]
    def _binary_search(self, num: List[int], l: int,aim: int) -> int:
        r = len(num) - 1
        while l <= r:
            mid = l + (r - l) // 2
            if num[mid] > aim:
                r = mid - 1
            elif num[mid] < aim:
                l = mid + 1
            else:
                return mid
        return -1
```

