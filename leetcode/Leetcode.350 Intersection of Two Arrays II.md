# Leetcode.350 Intersection of Two Arrays II

Given two arrays, write a function to compute their intersection.

**Example 1:**

```
Input: nums1 = [1,2,2,1], nums2 = [2,2]
Output: [2,2]
```

**Example 2:**

```
Input: nums1 = [4,9,5], nums2 = [9,4,9,8,4]
Output: [4,9]
```

**Note:**

- Each element in the result should appear as many times as it shows in both arrays.
- The result can be in any order.

**Follow up:**

- What if the given array is already sorted? How would you optimize your algorithm?
- What if *nums1*'s size is small compared to *nums2*'s size? Which algorithm is better?
- What if elements of *nums2* are stored on disk, and the memory is limited such that you cannot load all elements into the memory at once?

# Solution

## 利用集合解决

```java
class Solution {
    public int[] intersect(int[] nums1, int[] nums2) {
        Map<Integer,Integer> map = new HashMap<>();
        List<Integer> res = new ArrayList<>();
        for(int i:nums1){
            map.put(i,map.getOrDefault(i,0)+1);
        }
        for(int j:nums2){
            if(map.containsKey(j) && map.get(j)>0){
                res.add(j);
                map.put(j,map.get(j)-1);
            }
        }
        return res.stream().mapToInt(Integer::intValue).toArray();
    }
}
```

## 变成有序数组解决

```java
class Solution {
    public int[] intersect(int[] nums1, int[] nums2) {
        Arrays.sort(nums1);
        Arrays.sort(nums2);
        int i = 0;
        int j = 0;
        List<Integer> res = new ArrayList<>();
        while(i<nums1.length && j<nums2.length){
            if(nums1[i] > nums2[j]){
                j++;
            }else if(nums1[i]<nums2[j]){
                i++;
            }else{
                res.add(nums1[i]);
                i++;
                j++;
            }
        }
        return res.stream().mapToInt(Integer::intValue).toArray();
    }
}
```

