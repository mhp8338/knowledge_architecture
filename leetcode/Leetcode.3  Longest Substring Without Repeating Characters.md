# Leetcode.3  Longest Substring Without Repeating Characters

Given a string, find the length of the **longest substring** without repeating characters.

**Example 1:**

```
Input: "abcabcbb"
Output: 3 
Explanation: The answer is "abc", with the length of 3. 
```

**Example 2:**

```
Input: "bbbbb"
Output: 1
Explanation: The answer is "b", with the length of 1.
```

**Example 3:**

```
Input: "pwwkew"
Output: 3
Explanation: The answer is "wke", with the length of 3. 
             Note that the answer must be a substring, "pwke" is a subsequence and not a substring.
```

# Solution

解题思路：

- 双指针滑动窗口
- map记录

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        char[] sa = s.toCharArray();
        // [l,r]
        int l = 0;
        int r = -1;
        int res = 0;
        Map<Character,Integer> temp = new HashMap<>();
        while(r+1<sa.length){
            if(temp.getOrDefault(sa[r+1],0) == 0){
                temp.put(sa[++r],1);
                res = Math.max(r-l+1,res);
            }else{
                temp.put(sa[l],temp.get(sa[l])-1);
                l++;
            }
            
        }
        return res;
    }
}
```

解题思路：

- 数组记录

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        int[] freq = new int[256];
        int l = 0;
        int r = -1;
        int res = 0;
        while(r+1 < s.length()){
            if(freq[s.charAt(r+1)] == 0){
                freq[s.charAt(++r)]++;
                res = Math.max(res,r-l+1);
            }else{
                freq[s.charAt(l++)]--;
            }
            res = Math.max(res,r-l+1);
        }
        return res;
    }
}
```

