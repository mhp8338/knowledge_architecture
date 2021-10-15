# Leetcode.290 Word Pattern

Given a `pattern` and a string `str`, find if `str` follows the same pattern.

Here **follow** means a full match, such that there is a bijection between a letter in `pattern` and a **non-empty** word in `str`.

**Example 1:**

```
Input: pattern = "abba", str = "dog cat cat dog"
Output: true
```

**Example 2:**

```
Input:pattern = "abba", str = "dog cat cat fish"
Output: false
```

**Example 3:**

```
Input: pattern = "aaaa", str = "dog cat cat dog"
Output: false
```

**Example 4:**

```
Input: pattern = "abba", str = "dog dog dog dog"
Output: false
```

**Notes:**
You may assume `pattern` contains only lowercase letters, and `str` contains lowercase letters that may be separated by a single space.

# Solution

## 单表

```java
class Solution {
    /*
    time: O(n^2)
    space: O(n)
    */
    public boolean wordPattern(String pattern, String str) {
        Map<Character,String> map = new HashMap<>();
        char[] p = pattern.toCharArray();
        String[] s = str.split("\\s");
        if(p.length != s.length){
            return false;
        }
        for(int i=0;i<s.length;i++){
            if(map.containsKey(p[i])){
                if(!map.get(p[i]).equals(s[i])){
                    return false;
                }
            }else if(map.containsValue(s[i])){
                //O(n)
                return false;
            }else{
                map.put(p[i],s[i]);
            }
        }
        
        return true;
        
    }
}
```

###  比较目标出现索引对象

```java
class Solution {
    public boolean wordPattern(String pattern, String str) {
        String[] words = str.split(" ");
        if (words.length != pattern.length()) {
            return false;
        }
        Map<Object,Integer> index = new HashMap<>();
        // 使用int的时候装包会在[-128,127]不回产生新的对象，超过该值则会产生对象
        // 使用Integer,装包直接采用该对象，所以引用的是相同的对象
        for (Integer i=0; i<words.length; ++i) {
            // == 表示引用相同
            if (index.put(pattern.charAt(i), i) != index.put(words[i], i)) {
                return false;
            }
        }
        return true;
    }
    
}
```



## 双表

```java
class Solution {
    /*
    time: O(n)
    space: O(n)
    */
    public boolean wordPattern(String pattern, String str) {
        Map<Character,String> map1 = new HashMap<>();
        Map<String,Character> map2 = new HashMap<>();
        char[] p = pattern.toCharArray();
        String[] s = str.split("\\s");
        if(p.length != s.length){
            return false;
        }
        for(int i=0;i<s.length;i++){
            if(map1.containsKey(p[i]) && map2.containsKey(s[i])){
                if(!map1.get(p[i]).equals(s[i]) || map2.get(s[i]) != p[i]){
                    return false;
                }
            }else if(!map1.containsKey(p[i]) && !map2.containsKey(s[i])){
                map1.put(p[i],s[i]);
                map2.put(s[i],p[i]);
            }else{
                return false;
            }
        }
        
        return true;
        
    }
}
```

