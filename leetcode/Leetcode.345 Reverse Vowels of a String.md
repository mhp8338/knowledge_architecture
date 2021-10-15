# Leetcode.345 Reverse Vowels of a String

Write a function that takes a string as input and reverse only the vowels of a string.

**Example 1:**

```
Input: "hello"
Output: "holle"
```

**Example 2:**

```
Input: "leetcode"
Output: "leotcede"
```

**Note:**
The vowels does not include the letter "y".

# Solution

```python
class Solution:
    def reverseVowels(self, s: str) -> str:
        s = list(s)
        vowels = ('a','e','i','o','u')
        n = len(s)
        l = 0
        r = n - 1
        while l<r:
            while s[l].lower() not in vowels and l < n-1:
                l += 1 
            while s[r].lower() not in vowels and r > 0:
                r -= 1
            if l >= r:
                break
            
            s[l] = chr(ord(s[l]) ^ ord(s[r]))
            s[r] = chr(ord(s[l]) ^ ord(s[r]))
            s[l] = chr(ord(s[l]) ^ ord(s[r]))
            # s[l],s[r] = s[r],s[l]
            
            l += 1
            r -= 1
        return "".join(s)
```

