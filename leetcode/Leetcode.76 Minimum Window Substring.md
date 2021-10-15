# Leetcode.76 Minimum Window Substring

Given a string S and a string T, find the minimum window in S which will contain all the characters in T in complexity O(n).

**Example:**

```
Input: S = "ADOBECODEBANC", T = "ABC"
Output: "BANC"
```

**Note:**

- If there is no such window in S that covers all characters in T, return the empty string `""`.
- If there is such window, you are guaranteed that there will always be only one unique minimum window in S.

# Solution

```java
class Solution {
    public String minWindow(String s, String t) {
        int sl = s.length();
        int tl = t.length();
        if(sl == 0 || tl == 0 || sl < tl){
            return "";
        }
        String res = "";
        int l = 0;
        int r = 0;
        int[] freq = new int[256];
        int co = 0;
        int len = s.length() + 1;
        for(char c: t.toCharArray()){
            freq[c]++;
        }
        while(r<s.length()){
            //不存在的置为-1
            freq[s.charAt(r)]--;
            if(freq[s.charAt(r)] >= 0){
                co ++;
            }
            // 当找到substring需要夹逼找到最短的字串
            if(co == tl){
                while(freq[s.charAt(l)]<0){
                    freq[s.charAt(l++)]++;
                }
                //找到了更新
                if(r - l + 1 < len){
                    len = r - l + 1;
                    res = s.substring(l,r+1);
                }
                //破坏循环继续找下一个
                freq[s.charAt(l)]++;
                l++;
                co--;
            }
            r++;
        }
        return res;
    }
}
```

