# Leetcode.125 Valid Palindrome

Given a string, determine if it is a palindrome, considering only alphanumeric characters and ignoring cases.

**Note:** For the purpose of this problem, we define empty string as valid palindrome.

**Example 1:**

```
Input: "A man, a plan, a canal: Panama"
Output: true
```

**Example 2:**

```
Input: "race a car"
Output: false
```

 

**Constraints:**

- `s` consists only of printable ASCII characters.

# Solution

## Two Points

```python
class Solution:
    def __init__(self):
        self.alphanum = [0] * 256
        for i in range(26):
            # 转化ascii码
            self.alphanum[ord('a') + i] = 1
        for i in range(10):
            self.alphanum[ord('0') + i] = 1
    def isPalindrome(self, s: str) -> bool:
        s = s.lower()
        r = len(s)-1
        l = 0
        while l < r:
            # 注意索引越界的问题
            while self.alphanum[ord(s[l])] == 0 and l < r:
                l += 1
            while self.alphanum[ord(s[r])] == 0 and l < r:
                r -= 1
            if s[l] != s[r]:
                return False
            else:
                l += 1
                r -= 1       
        return True
```

