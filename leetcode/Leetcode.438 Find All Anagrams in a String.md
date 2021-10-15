# Leetcode.438 Find All Anagrams in a String

Given a string **s** and a **non-empty** string **p**, find all the start indices of **p**'s anagrams in **s**.

Strings consists of lowercase English letters only and the length of both strings **s** and **p** will not be larger than 20,100.

The order of output does not matter.

**Example 1:**

```
Input:
s: "cbaebabacd" p: "abc"

Output:
[0, 6]

Explanation:
The substring with start index = 0 is "cba", which is an anagram of "abc".
The substring with start index = 6 is "bac", which is an anagram of "abc".
```

**Example 2:**

```
Input:
s: "abab" p: "ab"

Output:
[0, 1, 2]

Explanation:
The substring with start index = 0 is "ab", which is an anagram of "ab".
The substring with start index = 1 is "ba", which is an anagram of "ab".
The substring with start index = 2 is "ab", which is an anagram of "ab".
```

# Solution

```java
class Solution {
    public List<Integer> findAnagrams(String s, String p) {
        int sl = s.length();
        int pl = p.length();
        if(sl < pl){
            return new ArrayList<>();
        }
        
        int[] freq = new int[26];
        for(int i=0;i<pl;i++){
            freq[p.charAt(i) - 'a']++;
        }
        // [l,r]
        int l = 0;
        int r = -1;
        int co = p.length();
        List<Integer> res = new ArrayList<>();
        while(r+1 < sl){
            if(freq[s.charAt(r+1) - 'a'] > 0){
                freq[s.charAt(++r) - 'a']--;               
                co--; 
            }else{
                // co控制满足条件的情况，当遇到不满足的时候
                freq[s.charAt(l++) - 'a']++;
                co++;
            }
            if(co == 0){
                res.add(l);
            }
            
        }
        return res;
    }
}
```

