# DSA Pattern Cheatsheet — Interactive (Java + Problem Statements)

Each pattern has **5 real problems**, each with:
- Problem statement
- Java implementation

At the end is an interactive quiz.

---

## 1. Prefix Sum + HashMap

Used for subarray sum, xor, modulo, or prefix-frequency tasks.

### Subarray Sum Equals K

**Problem:** Count how many subarrays have sum exactly equal to K.
Input: nums = [1,1,1], k = 2 → Output = 2

```java
import java.util.HashMap;
class Solution {
    public int subarraySum(int[] nums, int k) {
        HashMap<Integer,Integer> map = new HashMap<>();
        map.put(0,1);
        int prefix=0, count=0;
        for(int n: nums){
            prefix += n;
            count += map.getOrDefault(prefix - k, 0);
            map.put(prefix, map.getOrDefault(prefix,0)+1);
        }
        return count;
    }
}
```

### Count Subarrays Divisible by K

**Problem:** Count subarrays whose sum is divisible by K.

```java
import java.util.HashMap;
class Solution {
    public int subarraysDivByK(int[] nums, int k) {
        HashMap<Integer,Integer> map = new HashMap<>();
        map.put(0,1);
        int prefix=0, count=0;
        for(int n: nums){
            prefix = (prefix + n) % k;
            if(prefix < 0) prefix += k;
            count += map.getOrDefault(prefix,0);
            map.put(prefix, map.getOrDefault(prefix,0)+1);
        }
        return count;
    }
}
```

### Longest Subarray With Sum 0

**Problem:** Return length of longest subarray with total sum = 0.

```java
import java.util.HashMap;
class Solution {
    public int longestZeroSum(int[] nums) {
        HashMap<Integer,Integer> first = new HashMap<>();
        int prefix=0, best=0;
        first.put(0,-1);
        for(int i=0;i<nums.length;i++){
            prefix += nums[i];
            if(first.containsKey(prefix))
                best = Math.max(best, i-first.get(prefix));
            else first.put(prefix, i);
        }
        return best;
    }
}
```

### Subarray XOR Equals K

**Problem:** Count subarrays whose XOR equals K.

```java
import java.util.HashMap;
class Solution {
    public int subarrayXor(int[] nums, int k) {
        HashMap<Integer,Integer> map = new HashMap<>();
        map.put(0,1);
        int xr=0,count=0;
        for(int n: nums){
            xr ^= n;
            count += map.getOrDefault(xr ^ k, 0);
            map.put(xr, map.getOrDefault(xr,0)+1);
        }
        return count;
    }
}
```

### Equal Number of 0s and 1s

**Problem:** Treat 0 as -1 → find longest subarray where count(0)==count(1).

```java
import java.util.HashMap;
class Solution {
    public int findMaxLength(int[] nums) {
        HashMap<Integer,Integer> map = new HashMap<>();
        map.put(0,-1);
        int sum=0, best=0;
        for(int i=0;i<nums.length;i++){
            sum += (nums[i]==1)?1:-1;
            if(map.containsKey(sum))
                best = Math.max(best, i-map.get(sum));
            else 
                map.put(sum, i);
        }
        return best;
    }
}
```

---

## 2. Sliding Window (Fixed + Variable)

Used for contiguous substring/subarray with constraints.

### Longest Substring w/o Repeating Characters

**Problem:** Given a string, return length of the longest substring with no repeated characters.

```java
import java.util.HashMap;
class Solution {
    public int lengthOfLongestSubstring(String s) {
        HashMap<Character,Integer> map = new HashMap<>();
        int l=0, best=0;
        for(int r=0;r<s.length();r++){
            char c=s.charAt(r);
            if(map.containsKey(c)) l=Math.max(l,map.get(c)+1);
            map.put(c,r);
            best=Math.max(best, r-l+1);
        }
        return best;
    }
}
```

### Longest Substring With K Distinct

**Problem:** Return length of longest substring with at most K distinct characters.

```java
import java.util.HashMap;
class Solution {
    public int lengthKDistinct(String s, int k) {
        HashMap<Character,Integer> map = new HashMap<>();
        int l=0,best=0;
        for(int r=0;r<s.length();r++){
            char c=s.charAt(r);
            map.put(c,map.getOrDefault(c,0)+1);
            while(map.size()>k){
                char d=s.charAt(l++);
                map.put(d,map.get(d)-1);
                if(map.get(d)==0) map.remove(d);
            }
            best=Math.max(best,r-l+1);
        }
        return best;
    }
}
```

### Minimum Window Substring

**Problem:** Find smallest substring of S which contains all characters of T.

```java
import java.util.HashMap;
class Solution {
    public String minWindow(String s, String t) {
        HashMap<Character,Integer> need=new HashMap<>();
        for(char c:t.toCharArray()) need.put(c,need.getOrDefault(c,0)+1);
        int missing=t.length(), l=0,start=0,min=Integer.MAX_VALUE;
        for(int r=0;r<s.length();r++){
            char c=s.charAt(r);
            if(need.containsKey(c) && need.get(c)>0) missing--;
            need.put(c,need.getOrDefault(c,0)-1);
            while(missing==0){
                if(r-l+1<min){ min=r-l+1; start=l; }
                char d=s.charAt(l++);
                need.put(d,need.get(d)+1);
                if(need.get(d)>0) missing++;
            }
        }
        return min==Integer.MAX_VALUE?"":s.substring(start,start+min);
    }
}
```

### Fixed Window: Max Sum Subarray of Size K

**Problem:** Compute maximum sum of any contiguous subarray of size K.

```java
class Solution {
    public int maxSumK(int[] nums, int k) {
        int sum=0;
        for(int i=0;i<k;i++) sum+=nums[i];
        int best=sum;
        for(int i=k;i<nums.length;i++){
            sum+=nums[i]-nums[i-k];
            best=Math.max(best,sum);
        }
        return best;
    }
}
```

### Count Anagram Occurrences

**Problem:** Count number of substrings of S that are anagrams of P.

```java
import java.util.Arrays;
class Solution {
    public int countAnagrams(String s, String p) {
        int[] pc=new int[26], sc=new int[26];
        for(char c:p.toCharArray()) pc[c-'a']++;
        int l=0,count=0;
        for(int r=0;r<s.length();r++){
            sc[s.charAt(r)-'a']++;
            if(r-l+1>p.length()) sc[s.charAt(l++)-'a']--;
            if(Arrays.equals(pc,sc)) count++;
        }
        return count;
    }
}
```

---

## Interactive Quiz

- Q1: Count number of contiguous subarrays whose sum equals K.
- Q2: Find longest substring with at most 2 distinct characters.
- Q3: Given sorted array, remove duplicates in-place.
- Q4: For each day, compute days until warmer temperature.
- Q5: Given meeting intervals, find minimum number of rooms.
- Q6: Find maximum circular subarray sum.

Reply like: `Q1: Prefix Sum`, `Q2: Sliding Window`, ...
