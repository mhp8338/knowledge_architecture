# Leetcode.242 Valid Anagram

Given two strings *s* and *t* , write a function to determine if *t* is an anagram of *s*.

**Example 1:**

```
Input: s = "anagram", t = "nagaram"
Output: true
```

**Example 2:**

```
Input: s = "rat", t = "car"
Output: false
```

**Note:**
You may assume the string contains only lowercase alphabets.

**Follow up:**
What if the inputs contain unicode characters? How would you adapt your solution to such case?

# Solution

```java
class Solution {
    public boolean isAnagram(String s, String t) {
        if(s.length() != t.length()){
            return false;
        }
        int[] freq = new int[26];
        for(char c1:s.toCharArray()){
            freq[c1-'a']++;
        }
        for(char c2:t.toCharArray()){
            if(freq[c2-'a'] == 0){
                return false;
            }else{
                freq[c2-'a']--;
            }
        }
        return true;
    }
}
```

