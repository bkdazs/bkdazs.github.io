---
title: "Sliding Window"
topic: "Algorithms"
difficulty: "Medium"
tags: ["sliding-window", "arrays", "strings", "optimization", "two-pointers"]
status: "complete"
weight: 4
---

# Sliding Window

## 📚 Summary

The **Sliding Window** technique optimizes problems involving contiguous subarrays/substrings by maintaining a "window" that slides over data, avoiding redundant computations. Instead of recalculating from scratch for each position, we add elements entering the window and remove elements leaving it.

---

## ⏱️ Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| Fixed Window | O(n) | O(1) or O(k) |
| Variable Window | O(n) | O(1) to O(n) |
| Brute Force | O(n²) to O(n³) | O(1) |

**Why Sliding Window is Efficient:**
- Processes each element at most twice (enter and leave window)
- Converts O(n×k) problems to O(n)
- Reduces space from O(k) to O(1) in many cases

---

## 🧠 Core Concepts

### 1. Fixed-Size Window

Window size remains constant; slide one position at a time.

```
Array: [1, 3, 2, 6, -1, 4, 1, 8, 2], k=5

Window 1: [1, 3, 2, 6, -1] → Sum = 11
Window 2: [3, 2, 6, -1, 4] → Sum = 14 (remove 1, add 4)
Window 3: [2, 6, -1, 4, 1] → Sum = 12 (remove 3, add 1)
```

### 2. Variable-Size Window

Window expands or contracts based on conditions.

```
Find smallest subarray with sum ≥ target

Array: [2, 1, 5, 2, 3, 2], target = 7

Expand until sum ≥ 7, then contract from left:
[2, 1, 5] → sum=8 ≥ 7, length=3
[1, 5] → sum=6 < 7, expand
[1, 5, 2] → sum=8 ≥ 7, length=3
[5, 2] → sum=7 ≥ 7, length=2 ← minimum!
```

---

## 💻 Implementation

### Fixed Window Template

```python
from typing import List

def fixed_window(arr: List[int], k: int) -> List[int]:
    """
    Process array with fixed window size k.
    Returns result for each window position.
    
    Time: O(n), Space: O(1)
    
    >>> fixed_window([1, 3, 2, 6, -1, 4, 1, 8, 2], 5)
    [2.2, 2.8, 2.4, 3.6, 2.8]  # averages
    """
    n = len(arr)
    if n < k:
        return []
    
    results = []
    window_sum = sum(arr[:k])  # First window
    results.append(window_sum / k)
    
    for i in range(k, n):
        # Slide: remove leftmost, add rightmost
        window_sum = window_sum - arr[i - k] + arr[i]
        results.append(window_sum / k)
    
    return results


def max_sum_subarray_k(arr: List[int], k: int) -> int:
    """
    Find maximum sum of any contiguous subarray of size k.
    
    Time: O(n), Space: O(1)
    
    >>> max_sum_subarray_k([2, 1, 5, 1, 3, 2], 3)
    9
    >>> max_sum_subarray_k([2, 3, 4, 1, 5], 2)
    7
    """
    if len(arr) < k:
        return 0
    
    # Calculate sum of first window
    window_sum = sum(arr[:k])
    max_sum = window_sum
    
    # Slide window from left to right
    for i in range(k, len(arr)):
        window_sum += arr[i] - arr[i - k]
        max_sum = max(max_sum, window_sum)
    
    return max_sum
```

### Variable Window Template - Shrinkable

```python
def variable_window_shrinkable(arr: List[int], condition) -> int:
    """
    Template for finding longest/maximum subarray satisfying condition.
    Window shrinks from left when condition violated.
    
    Pattern: Expand right, shrink left while invalid
    """
    left = 0
    result = 0
    state = initialize_state()
    
    for right in range(len(arr)):
        # Expand window: add arr[right] to state
        update_state_add(state, arr[right])
        
        # Shrink window while condition is violated
        while not is_valid(state, condition):
            update_state_remove(state, arr[left])
            left += 1
        
        # Update result with current valid window
        result = max(result, right - left + 1)
    
    return result


def min_subarray_sum_at_least_target(nums: List[int], target: int) -> int:
    """
    Find minimum length subarray with sum >= target.
    Returns 0 if no such subarray exists.
    
    Time: O(n), Space: O(1)
    
    >>> min_subarray_sum_at_least_target([2, 3, 1, 2, 4, 3], 7)
    2
    >>> min_subarray_sum_at_least_target([1, 4, 4], 4)
    1
    """
    n = len(nums)
    min_length = float('inf')
    window_sum = 0
    left = 0
    
    for right in range(n):
        window_sum += nums[right]
        
        # Shrink window while valid (sum >= target)
        while window_sum >= target:
            min_length = min(min_length, right - left + 1)
            window_sum -= nums[left]
            left += 1
    
    return min_length if min_length != float('inf') else 0
```

### Variable Window Template - Non-Shrinkable

```python
def variable_window_non_shrinkable(arr: List[int], condition) -> int:
    """
    Template for finding exact size or count.
    Window only expands, left moves at most once per iteration.
    
    Useful for: "At most K" problems, exact window counting
    """
    left = 0
    result = 0
    state = initialize_state()
    
    for right in range(len(arr)):
        # Expand window
        update_state_add(state, arr[right])
        
        # Move left pointer if needed (at most once)
        if not is_valid(state, condition):
            update_state_remove(state, arr[left])
            left += 1
        
        # Window size is always right - left + 1
        result = max(result, right - left + 1)
    
    return result


def longest_substring_k_distinct(s: str, k: int) -> int:
    """
    Find longest substring with at most k distinct characters.
    
    Time: O(n), Space: O(k)
    
    >>> longest_substring_k_distinct("eceba", 2)
    3
    >>> longest_substring_k_distinct("aa", 1)
    2
    """
    from collections import defaultdict
    
    char_count = defaultdict(int)
    left = 0
    max_length = 0
    
    for right in range(len(s)):
        char_count[s[right]] += 1
        
        # Shrink window while more than k distinct chars
        while len(char_count) > k:
            char_count[s[left]] -= 1
            if char_count[s[left]] == 0:
                del char_count[s[left]]
            left += 1
        
        max_length = max(max_length, right - left + 1)
    
    return max_length
```

---

## 🔍 Worked Examples

### Example 1: Maximum of All Subarrays of Size K

```python
from collections import deque

def sliding_window_maximum(nums: List[int], k: int) -> List[int]:
    """
    Return max element in each window of size k.
    Uses monotonic deque for O(n) time.
    
    Time: O(n), Space: O(k)
    
    >>> sliding_window_maximum([1, 3, -1, -3, 5, 3, 6, 7], 3)
    [3, 3, 5, 5, 6, 7]
    """
    if not nums or k == 0:
        return []
    
    dq = deque()  # Store indices, values are decreasing
    result = []
    
    for i in range(len(nums)):
        # Remove elements outside current window
        while dq and dq[0] < i - k + 1:
            dq.popleft()
        
        # Remove smaller elements (they can't be max)
        while dq and nums[dq[-1]] < nums[i]:
            dq.pop()
        
        dq.append(i)
        
        # Add to result once we have full window
        if i >= k - 1:
            result.append(nums[dq[0]])
    
    return result

# Trace for [1, 3, -1, -3, 5, 3, 6, 7], k=3:
# i=0: dq=[0]
# i=1: nums[1]=3 > nums[0]=1, pop 0, dq=[1]
# i=2: dq=[1, 2], result=[3]
# i=3: dq=[1, 2, 3], but 1 < 3-3+1=1, pop 1, dq=[2, 3], result=[3, 3]
# i=4: nums[4]=5 > all, dq=[4], result=[3, 3, 5]
# i=5: dq=[4, 5], result=[3, 3, 5, 5]
# i=6: nums[6]=6 > all, dq=[6], result=[3, 3, 5, 5, 6]
# i=7: nums[7]=7 > all, dq=[7], result=[3, 3, 5, 5, 6, 7]
```

### Example 2: Longest Substring Without Repeating Characters

```python
def length_of_longest_substring(s: str) -> int:
    """
    Find length of longest substring without repeating characters.
    
    Time: O(n), Space: O(min(m, n)) where m is charset size
    
    >>> length_of_longest_substring("abcabcbb")
    3
    >>> length_of_longest_substring("bbbbb")
    1
    >>> length_of_longest_substring("pwwkew")
    3
    """
    char_index = {}  # char -> last seen index
    left = 0
    max_length = 0
    
    for right, char in enumerate(s):
        # If char seen and within current window
        if char in char_index and char_index[char] >= left:
            left = char_index[char] + 1
        
        char_index[char] = right
        max_length = max(max_length, right - left + 1)
    
    return max_length

# Trace for "abcabcbb":
# right=0, 'a': char_index={a:0}, left=0, len=1
# right=1, 'b': char_index={a:0, b:1}, left=0, len=2
# right=2, 'c': char_index={a:0, b:1, c:2}, left=0, len=3
# right=3, 'a': 'a' at 0 >= left=0, left=1, char_index={a:3, b:1, c:2}, len=3
# right=4, 'b': 'b' at 1 >= left=1, left=2, char_index={a:3, b:4, c:2}, len=3
# right=5, 'c': 'c' at 2 >= left=2, left=3, char_index={a:3, b:4, c:5}, len=3
# right=6, 'b': 'b' at 4 >= left=3, left=5, len=2
# right=7, 'b': 'b' at 6 >= left=5, left=7, len=1
# Result: 3
```

### Example 3: Minimum Window Substring

```python
from collections import Counter

def min_window(s: str, t: str) -> str:
    """
    Find minimum window in s containing all characters of t.
    
    Time: O(|s| + |t|), Space: O(|s| + |t|)
    
    >>> min_window("ADOBECODEBANC", "ABC")
    'BANC'
    >>> min_window("a", "a")
    'a'
    >>> min_window("a", "aa")
    ''
    """
    if not s or not t or len(s) < len(t):
        return ""
    
    # Count chars needed from t
    need = Counter(t)
    required = len(need)  # Unique chars in t
    
    # Window state
    formed = 0  # Unique chars in window with required count
    window_counts = {}
    
    left = 0
    min_len = float('inf')
    result = (0, 0)  # (start, end)
    
    for right in range(len(s)):
        # Add character from right
        char = s[right]
        window_counts[char] = window_counts.get(char, 0) + 1
        
        # Check if frequency matches requirement
        if char in need and window_counts[char] == need[char]:
            formed += 1
        
        # Try to shrink window
        while left <= right and formed == required:
            # Update result if this window is smaller
            if right - left + 1 < min_len:
                min_len = right - left + 1
                result = (left, right)
            
            # Remove leftmost character
            char = s[left]
            window_counts[char] -= 1
            if char in need and window_counts[char] < need[char]:
                formed -= 1
            left += 1
    
    return "" if min_len == float('inf') else s[result[0]:result[1] + 1]
```

### Example 4: Subarrays with K Different Integers

```python
def subarrays_with_k_distinct(nums: List[int], k: int) -> int:
    """
    Count subarrays with exactly K different integers.
    Uses: exactly(k) = atMost(k) - atMost(k-1)
    
    Time: O(n), Space: O(n)
    
    >>> subarrays_with_k_distinct([1, 2, 1, 2, 3], 2)
    7
    >>> subarrays_with_k_distinct([1, 2, 1, 3, 4], 3)
    3
    """
    def at_most_k(nums: List[int], k: int) -> int:
        """Count subarrays with at most k distinct integers."""
        from collections import defaultdict
        
        count = defaultdict(int)
        left = 0
        result = 0
        
        for right in range(len(nums)):
            count[nums[right]] += 1
            
            while len(count) > k:
                count[nums[left]] -= 1
                if count[nums[left]] == 0:
                    del count[nums[left]]
                left += 1
            
            # All subarrays ending at right with at most k distinct
            result += right - left + 1
        
        return result
    
    return at_most_k(nums, k) - at_most_k(nums, k - 1)

# For [1, 2, 1, 2, 3], k=2:
# atMost(2) = 12: [1], [2], [1], [2], [3], [1,2], [2,1], [1,2], [2,3], [1,2,1], [2,1,2], [1,2,1,2]
# atMost(1) = 5: [1], [2], [1], [2], [3]
# exactly(2) = 12 - 5 = 7
```

---

## 🎯 Pattern Recognition

### When to Use Fixed Window
- "Find X in subarrays of size K"
- "Maximum/minimum of windows"
- Moving average calculations
- String permutation matching

### When to Use Variable Window
- "Longest/shortest subarray with property X"
- "Minimum window containing Y"
- "At most K distinct elements"
- Sum/product constraints

### Key Identifiers
1. **Contiguous elements** - subarrays, substrings
2. **Optimization** - longest, shortest, maximum, minimum
3. **Window property** - sum, distinct count, frequency

---

## 🧩 Problem Variations

### Variation 1: Count Subarrays with Sum Equals K

```python
def subarray_sum_equals_k(nums: List[int], k: int) -> int:
    """
    Count subarrays with sum equal to k.
    Note: Uses prefix sum + hashmap, not pure sliding window
    (because array can have negative numbers).
    
    Time: O(n), Space: O(n)
    
    >>> subarray_sum_equals_k([1, 1, 1], 2)
    2
    >>> subarray_sum_equals_k([1, 2, 3], 3)
    2
    """
    count = 0
    prefix_sum = 0
    prefix_count = {0: 1}  # Empty prefix has sum 0
    
    for num in nums:
        prefix_sum += num
        # If (prefix_sum - k) exists, we found subarrays
        if prefix_sum - k in prefix_count:
            count += prefix_count[prefix_sum - k]
        prefix_count[prefix_sum] = prefix_count.get(prefix_sum, 0) + 1
    
    return count
```

### Variation 2: Longest Repeating Character Replacement

```python
def character_replacement(s: str, k: int) -> int:
    """
    Longest substring where you can replace at most k characters
    to make all characters the same.
    
    Time: O(n), Space: O(26) = O(1)
    
    >>> character_replacement("ABAB", 2)
    4
    >>> character_replacement("AABABBA", 1)
    4
    """
    count = {}
    left = 0
    max_count = 0  # Count of most frequent char in window
    result = 0
    
    for right in range(len(s)):
        count[s[right]] = count.get(s[right], 0) + 1
        max_count = max(max_count, count[s[right]])
        
        # Window size - max_count = chars to replace
        # If > k, shrink window
        while right - left + 1 - max_count > k:
            count[s[left]] -= 1
            left += 1
        
        result = max(result, right - left + 1)
    
    return result
```

### Variation 3: Fruit Into Baskets (At Most 2 Types)

```python
def total_fruit(fruits: List[int]) -> int:
    """
    Find longest subarray with at most 2 distinct values.
    
    Time: O(n), Space: O(1)
    
    >>> total_fruit([1, 2, 1])
    3
    >>> total_fruit([0, 1, 2, 2])
    3
    >>> total_fruit([1, 2, 3, 2, 2])
    4
    """
    basket = {}
    left = 0
    max_fruits = 0
    
    for right, fruit in enumerate(fruits):
        basket[fruit] = basket.get(fruit, 0) + 1
        
        while len(basket) > 2:
            basket[fruits[left]] -= 1
            if basket[fruits[left]] == 0:
                del basket[fruits[left]]
            left += 1
        
        max_fruits = max(max_fruits, right - left + 1)
    
    return max_fruits
```

---

## ⚠️ Common Pitfalls

### 1. Off-by-One Errors

```python
# WRONG: Window size calculation
length = right - left  # Missing + 1

# CORRECT
length = right - left + 1
```

### 2. Not Handling Edge Cases

```python
def fixed_window_safe(arr: List[int], k: int) -> List[int]:
    if not arr or k <= 0 or k > len(arr):
        return []
    # ... rest of implementation
```

### 3. Forgetting to Update State When Shrinking

```python
# WRONG
while condition:
    left += 1  # Forgot to update state!

# CORRECT
while condition:
    window_state.remove(arr[left])
    left += 1
```

### 4. Using Sliding Window for Non-Positive Numbers

```python
# Sliding window for sum problems only works with positive numbers
# For arrays with negative numbers, use prefix sum + hashmap
```

---

## 🔗 Related Techniques

| Technique | Use Case |
|-----------|----------|
| Two Pointers | Sorted arrays, pair finding |
| Prefix Sum | Range sum queries, negative numbers |
| Monotonic Queue | Maximum/minimum in window |
| Hash Map | Frequency counting, anagram matching |

---

## 📚 Practice Problems

### Easy
| # | Problem | Key Concept |
|---|---------|-------------|
| 1 | [LC 643: Maximum Average Subarray I](https://leetcode.com/problems/maximum-average-subarray-i/) | Fixed window |
| 2 | [LC 219: Contains Duplicate II](https://leetcode.com/problems/contains-duplicate-ii/) | Fixed window + set |
| 3 | [LC 1456: Max Vowels in Substring](https://leetcode.com/problems/maximum-number-of-vowels-in-a-substring-of-given-length/) | Fixed window count |
| 4 | [LC 1984: Minimum Difference](https://leetcode.com/problems/minimum-difference-between-highest-and-lowest-of-k-scores/) | Sorted + fixed window |

### Medium
| # | Problem | Key Concept |
|---|---------|-------------|
| 5 | [LC 3: Longest Substring Without Repeating](https://leetcode.com/problems/longest-substring-without-repeating-characters/) | Variable + hashmap |
| 6 | [LC 209: Minimum Size Subarray Sum](https://leetcode.com/problems/minimum-size-subarray-sum/) | Variable + shrink |
| 7 | [LC 424: Longest Repeating Character Replacement](https://leetcode.com/problems/longest-repeating-character-replacement/) | Variable + frequency |
| 8 | [LC 438: Find All Anagrams](https://leetcode.com/problems/find-all-anagrams-in-a-string/) | Fixed + frequency match |
| 9 | [LC 567: Permutation in String](https://leetcode.com/problems/permutation-in-string/) | Fixed + anagram |
| 10 | [LC 713: Subarray Product Less Than K](https://leetcode.com/problems/subarray-product-less-than-k/) | Variable + product |
| 11 | [LC 904: Fruit Into Baskets](https://leetcode.com/problems/fruit-into-baskets/) | At most 2 distinct |
| 12 | [LC 1004: Max Consecutive Ones III](https://leetcode.com/problems/max-consecutive-ones-iii/) | Variable + flip k zeros |
| 13 | [LC 1052: Grumpy Bookstore Owner](https://leetcode.com/problems/grumpy-bookstore-owner/) | Fixed window optimization |
| 14 | [LC 1208: Get Equal Substrings](https://leetcode.com/problems/get-equal-substrings-within-budget/) | Variable with budget |
| 15 | [LC 1423: Maximum Points from Cards](https://leetcode.com/problems/maximum-points-you-can-obtain-from-cards/) | Inverse window |

### Hard
| # | Problem | Key Concept |
|---|---------|-------------|
| 16 | [LC 76: Minimum Window Substring](https://leetcode.com/problems/minimum-window-substring/) | Variable + char match |
| 17 | [LC 239: Sliding Window Maximum](https://leetcode.com/problems/sliding-window-maximum/) | Monotonic deque |
| 18 | [LC 480: Sliding Window Median](https://leetcode.com/problems/sliding-window-median/) | Two heaps |
| 19 | [LC 992: Subarrays with K Different Integers](https://leetcode.com/problems/subarrays-with-k-different-integers/) | Exactly K = atMost(K) - atMost(K-1) |
| 20 | [LC 995: Minimum Number of K Consecutive Bit Flips](https://leetcode.com/problems/minimum-number-of-k-consecutive-bit-flips/) | Greedy + window |
| 21 | [LC 1074: Number of Submatrices That Sum to Target](https://leetcode.com/problems/number-of-submatrices-that-sum-to-target/) | 2D prefix + window |
| 22 | [LC 2009: Minimum Operations to Make Array Continuous](https://leetcode.com/problems/minimum-number-of-operations-to-make-array-continuous/) | Sort + window |
| 23 | [LC 2302: Count Subarrays with Score Less Than K](https://leetcode.com/problems/count-subarrays-with-score-less-than-k/) | Variable + product constraint |
| 24 | [LC 2444: Count Subarrays with Fixed Bounds](https://leetcode.com/problems/count-subarrays-with-fixed-bounds/) | Track positions |
| 25 | [LC 2831: Find Longest Equal Subarray](https://leetcode.com/problems/find-the-longest-equal-subarray/) | Group indices + window |

---

## 📝 Interview Tips

1. **Clarify constraints** - positive only? distinct? sorted?
2. **Identify window type** - fixed vs variable
3. **Define window state** - sum, count, frequency map
4. **Handle edge cases** - empty input, k > n, all same elements
5. **Optimize space** - often O(1) is possible with careful tracking

---

## 🔑 Key Takeaways

1. **Fixed window**: Use when window size is given
2. **Variable window**: Expand until invalid, then shrink
3. **"At most K"**: Count all valid windows ending at each position
4. **"Exactly K"**: Use `atMost(K) - atMost(K-1)`
5. **Negative numbers**: May need prefix sum instead of sliding window

---

*Last Updated: 2024*
