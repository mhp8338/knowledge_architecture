# leetcode.344 Reverse String

Write a function that reverses a string. The input string is given as an array of characters `char[]`.

Do not allocate extra space for another array, you must do this by **modifying the input array [in-place](https://en.wikipedia.org/wiki/In-place_algorithm)** with O(1) extra memory.

You may assume all the characters consist of [printable ascii characters](https://en.wikipedia.org/wiki/ASCII#Printable_characters). 

**Example 1:**

```
Input: ["h","e","l","l","o"]
Output: ["o","l","l","e","h"]
```

**Example 2:**

```
Input: ["H","a","n","n","a","h"]
Output: ["h","a","n","n","a","H"]
```

# Solution

## 指针交换

```python
class Solution:
    def reverseString(self, s: List[str]) -> None:
        """
        Do not return anything, modify s in-place instead.
        """
        n = len(s)
        l = 0
        r = n - 1
        while l<r:
            s[l],s[r] = s[r],s[l]
            l += 1
            r -= 1
```

## 异或交换

```python
class Solution:
    def reverseString(self, s: List[str]) -> None:
        """
        Do not return anything, modify s in-place instead.
        """
        n = len(s)
        l = 0
        r = n - 1
        while l<r:
            s[l] = chr(ord(s[l]) ^ ord(s[r])) 
            s[r] = chr(ord(s[l]) ^ ord(s[r]))
            s[l] = chr(ord(s[l]) ^ ord(s[r]))
            l += 1
            r -= 1
```

