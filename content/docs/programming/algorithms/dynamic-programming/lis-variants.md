---
title: "LIS and Variants"
topic: "Longest Increasing Subsequence Patterns"
difficulty: "Medium to Hard"
tags: ["dp", "lis", "binary-search", "patience-sort", "subsequence"]
status: "complete"
weight: 5
---
# Longest Increasing Subsequence (LIS) and Variants

## Summary / TL;DR

LIS is a fundamental DP problem with multiple solution approaches. Understanding LIS unlocks many subsequence-based problems.

**Three Approaches:**
1. **DP O(n²):** Classic, easy to understand
2. **Binary Search O(n log n):** Optimal time, uses patience sort
3. **Segment Tree O(n log n):** For complex variants

**Key Insight:** LIS length = longest chain where each element > previous.

---

## When to Use

- **Increasing/Decreasing subsequences:** Longest strictly/non-strictly increasing
- **Chain problems:** Maximum chain of compatible elements
- **Envelope/Box stacking:** Multi-dimensional sorting + LIS
- **Scheduling:** Maximum non-overlapping intervals
- **Divisibility chains:** Elements where each divides next

---

## Big-O Complexity

| Approach | Time | Space | When to Use |
|----------|------|-------|-------------|
| DP (basic) | O(n²) | O(n) | Small n, need reconstruction |
| Binary Search | O(n log n) | O(n) | Large n, just need length |
| Segment Tree | O(n log n) | O(n) | Complex queries/updates |

---

## Core Implementation Templates

### Template 1: O(n²) DP Solution

```python
from typing import List
import bisect

def lis_dp(nums: List[int]) -> int:
    """
    Classic O(n²) DP approach.
    dp[i] = length of LIS ending at index i
    
    Time: O(n²), Space: O(n)
    """
    if not nums:
        return 0
    
    n = len(nums)
    dp = [1] * n  # Each element is LIS of length 1
    
    for i in range(1, n):
        for j in range(i):
            if nums[j] < nums[i]:
                dp[i] = max(dp[i], dp[j] + 1)
    
    return max(dp)


def lis_with_reconstruction(nums: List[int]) -> List[int]:
    """
    Return the actual LIS (one of them if multiple).
    
    Time: O(n²), Space: O(n)
    """
    if not nums:
        return []
    
    n = len(nums)
    dp = [1] * n
    parent = [-1] * n  # Track previous element in LIS
    
    for i in range(1, n):
        for j in range(i):
            if nums[j] < nums[i] and dp[j] + 1 > dp[i]:
                dp[i] = dp[j] + 1
                parent[i] = j
    
    # Find index with max LIS length
    max_len = max(dp)
    idx = dp.index(max_len)
    
    # Reconstruct
    result = []
    while idx != -1:
        result.append(nums[idx])
        idx = parent[idx]
    
    return result[::-1]


def count_lis(nums: List[int]) -> int:
    """
    Count number of LIS.
    
    Time: O(n²), Space: O(n)
    """
    if not nums:
        return 0
    
    n = len(nums)
    length = [1] * n  # LIS length ending at i
    count = [1] * n   # Number of LIS ending at i
    
    for i in range(1, n):
        for j in range(i):
            if nums[j] < nums[i]:
                if length[j] + 1 > length[i]:
                    length[i] = length[j] + 1
                    count[i] = count[j]
                elif length[j] + 1 == length[i]:
                    count[i] += count[j]
    
    max_len = max(length)
    return sum(c for l, c in zip(length, count) if l == max_len)
```

### Template 2: O(n log n) Binary Search (Patience Sort)

```python
def lis_binary_search(nums: List[int]) -> int:
    """
    O(n log n) using binary search (patience sorting).
    
    tails[i] = smallest ending element of all increasing subsequences of length i+1
    
    Invariant: tails is always sorted.
    
    Time: O(n log n), Space: O(n)
    """
    if not nums:
        return 0
    
    tails = []  # tails[i] = min tail for LIS of length i+1
    
    for num in nums:
        # Find position to replace or extend
        pos = bisect.bisect_left(tails, num)
        
        if pos == len(tails):
            tails.append(num)  # Extend LIS
        else:
            tails[pos] = num   # Replace with smaller tail
    
    return len(tails)


def lis_non_strictly_increasing(nums: List[int]) -> int:
    """
    Longest non-decreasing subsequence (allows equal elements).
    Use bisect_right instead of bisect_left.
    
    Time: O(n log n), Space: O(n)
    """
    if not nums:
        return 0
    
    tails = []
    
    for num in nums:
        pos = bisect.bisect_right(tails, num)  # Right for non-strict
        
        if pos == len(tails):
            tails.append(num)
        else:
            tails[pos] = num
    
    return len(tails)


def lis_with_reconstruction_nlogn(nums: List[int]) -> List[int]:
    """
    O(n log n) with reconstruction.
    Track indices and parents.
    
    Time: O(n log n), Space: O(n)
    """
    if not nums:
        return []
    
    n = len(nums)
    tails = []      # Store indices
    parent = [-1] * n
    
    for i, num in enumerate(nums):
        pos = bisect.bisect_left([nums[t] for t in tails], num)
        
        if pos == len(tails):
            tails.append(i)
        else:
            tails[pos] = i
        
        if pos > 0:
            parent[i] = tails[pos - 1]
    
    # Reconstruct from last element in tails
    result = []
    idx = tails[-1]
    while idx != -1:
        result.append(nums[idx])
        idx = parent[idx]
    
    return result[::-1]
```

### Template 3: LIS Variants

```python
def longest_decreasing_subsequence(nums: List[int]) -> int:
    """
    LDS = LIS of negated/reversed array.
    
    Time: O(n log n), Space: O(n)
    """
    return lis_binary_search([-x for x in nums])


def longest_bitonic_subsequence(nums: List[int]) -> int:
    """
    Longest subsequence that first increases then decreases.
    = max(LIS_left[i] + LDS_right[i] - 1) for all i
    
    Time: O(n log n), Space: O(n)
    """
    if not nums:
        return 0
    
    n = len(nums)
    
    # LIS ending at each index (left to right)
    lis_left = [1] * n
    tails = []
    for i, num in enumerate(nums):
        pos = bisect.bisect_left(tails, num)
        if pos == len(tails):
            tails.append(num)
        else:
            tails[pos] = num
        lis_left[i] = pos + 1
    
    # LIS starting at each index (right to left) = LDS
    lis_right = [1] * n
    tails = []
    for i in range(n - 1, -1, -1):
        num = nums[i]
        pos = bisect.bisect_left(tails, num)
        if pos == len(tails):
            tails.append(num)
        else:
            tails[pos] = num
        lis_right[i] = pos + 1
    
    # Combine: bitonic = increasing + decreasing at peak
    max_len = 0
    for i in range(n):
        max_len = max(max_len, lis_left[i] + lis_right[i] - 1)
    
    return max_len


def longest_alternating_subsequence(nums: List[int]) -> int:
    """
    Longest subsequence alternating between increase and decrease.
    dp[i][0] = length ending with nums[i] after increase
    dp[i][1] = length ending with nums[i] after decrease
    
    Time: O(n), Space: O(1)
    """
    if not nums:
        return 0
    
    up = 1    # Length ending with upward movement
    down = 1  # Length ending with downward movement
    
    for i in range(1, len(nums)):
        if nums[i] > nums[i-1]:
            up = down + 1
        elif nums[i] < nums[i-1]:
            down = up + 1
    
    return max(up, down)


def minimum_deletions_to_make_sorted(nums: List[int]) -> int:
    """
    Minimum deletions to make array sorted.
    = n - LIS_length (for strictly increasing)
    
    Time: O(n log n), Space: O(n)
    """
    return len(nums) - lis_binary_search(nums)
```

### Template 4: Russian Doll Envelopes (2D LIS)

```python
def max_envelopes(envelopes: List[List[int]]) -> int:
    """
    Maximum envelopes that can be nested.
    envelope A fits in B if A.width < B.width AND A.height < B.height.
    
    Key insight:
    1. Sort by width ascending
    2. For same width, sort by height DESCENDING (to prevent same-width nesting)
    3. Find LIS on heights
    
    Time: O(n log n), Space: O(n)
    """
    if not envelopes:
        return 0
    
    # Sort: width ascending, height descending for same width
    envelopes.sort(key=lambda x: (x[0], -x[1]))
    
    # LIS on heights
    heights = [e[1] for e in envelopes]
    
    tails = []
    for h in heights:
        pos = bisect.bisect_left(tails, h)
        if pos == len(tails):
            tails.append(h)
        else:
            tails[pos] = h
    
    return len(tails)


def max_height_stacking_boxes(boxes: List[List[int]]) -> int:
    """
    Stack boxes where smaller box has all dimensions strictly smaller.
    Can rotate boxes (use all 3 rotations).
    
    Time: O(n² log n), Space: O(n)
    """
    # Generate all rotations (3 per box)
    rotations = []
    for l, w, h in boxes:
        # base: (length, width), stack on height
        rotations.append((min(l, w), max(l, w), h))
        rotations.append((min(l, h), max(l, h), w))
        rotations.append((min(w, h), max(w, h), l))
    
    # Remove duplicates
    rotations = list(set(rotations))
    
    # Sort by base area
    rotations.sort(key=lambda x: x[0] * x[1])
    
    n = len(rotations)
    dp = [r[2] for r in rotations]  # Height when this box is on top
    
    for i in range(1, n):
        for j in range(i):
            # Box j can be under box i if strictly smaller in both base dimensions
            if (rotations[j][0] < rotations[i][0] and 
                rotations[j][1] < rotations[i][1]):
                dp[i] = max(dp[i], dp[j] + rotations[i][2])
    
    return max(dp)
```

### Template 5: LIS with Special Constraints

```python
def longest_divisible_subset(nums: List[int]) -> List[int]:
    """
    Largest subset where every pair (a, b) has a % b == 0 or b % a == 0.
    
    Sort array, then if a | b and b | c, then a | c.
    This becomes LIS with divisibility condition.
    
    Time: O(n²), Space: O(n)
    """
    if not nums:
        return []
    
    nums.sort()
    n = len(nums)
    
    dp = [1] * n       # Length of longest divisible subset ending at i
    parent = [-1] * n  # Previous index in the subset
    
    for i in range(1, n):
        for j in range(i):
            if nums[i] % nums[j] == 0:
                if dp[j] + 1 > dp[i]:
                    dp[i] = dp[j] + 1
                    parent[i] = j
    
    # Find max and reconstruct
    max_idx = dp.index(max(dp))
    result = []
    while max_idx != -1:
        result.append(nums[max_idx])
        max_idx = parent[max_idx]
    
    return result[::-1]


def length_of_lis_with_k_exceptions(nums: List[int], k: int) -> int:
    """
    LIS but can have up to k positions where nums[i] >= nums[i+1].
    
    dp[i][j] = LIS length ending at i with j exceptions used
    
    Time: O(n² * k), Space: O(n * k)
    """
    if not nums:
        return 0
    
    n = len(nums)
    dp = [[1] * (k + 1) for _ in range(n)]
    
    for i in range(1, n):
        for j in range(i):
            for exceptions in range(k + 1):
                if nums[j] < nums[i]:
                    # Normal increasing, no exception needed
                    dp[i][exceptions] = max(dp[i][exceptions], 
                                           dp[j][exceptions] + 1)
                elif exceptions > 0:
                    # Use an exception
                    dp[i][exceptions] = max(dp[i][exceptions], 
                                           dp[j][exceptions - 1] + 1)
    
    return max(dp[i][k] for i in range(n))


def longest_chain_of_pairs(pairs: List[List[int]]) -> int:
    """
    Maximum chain where for (a, b) -> (c, d), we need b < c.
    
    Sort by second element, then greedy or LIS.
    
    Time: O(n log n), Space: O(n)
    """
    if not pairs:
        return 0
    
    # Sort by end of interval (second element)
    pairs.sort(key=lambda x: x[1])
    
    # Greedy: always pick interval with smallest end
    count = 1
    curr_end = pairs[0][1]
    
    for i in range(1, len(pairs)):
        if pairs[i][0] > curr_end:
            count += 1
            curr_end = pairs[i][1]
    
    return count
```

### Template 6: Delete Operations to Make Sorted

```python
def minimum_operations_to_make_sorted(nums: List[int]) -> int:
    """
    Minimum deletions to make array strictly increasing.
    = n - LIS
    """
    return len(nums) - lis_binary_search(nums)


def minimum_insertions_to_make_sorted(nums: List[int]) -> int:
    """
    This is actually different - we can insert any element.
    With unlimited insertions, answer is always 0 (fill gaps).
    
    If inserting only from nums: still 0.
    """
    return 0


def make_array_strictly_increasing(arr1: List[int], arr2: List[int]) -> int:
    """
    Replace elements in arr1 with elements from arr2 to make arr1 strictly increasing.
    Minimize replacements. Return -1 if impossible.
    
    DP with states: (index in arr1, previous value used)
    
    Time: O(n * m * log m), Space: O(n * m)
    """
    arr2 = sorted(set(arr2))  # Sort and dedupe
    n = len(arr1)
    
    # dp[prev] = min operations to make arr1[0..i-1] strictly increasing
    # where the last element is prev
    INF = float('inf')
    dp = {-1: 0}  # Start: no elements, "previous" is -1
    
    for i in range(n):
        new_dp = {}
        
        for prev, ops in dp.items():
            # Option 1: Keep arr1[i] if it's > prev
            if arr1[i] > prev:
                key = arr1[i]
                new_dp[key] = min(new_dp.get(key, INF), ops)
            
            # Option 2: Replace arr1[i] with smallest arr2 element > prev
            idx = bisect.bisect_right(arr2, prev)
            if idx < len(arr2):
                key = arr2[idx]
                new_dp[key] = min(new_dp.get(key, INF), ops + 1)
        
        dp = new_dp
        if not dp:
            return -1
    
    return min(dp.values()) if dp else -1
```

---

## Worked Examples

### Example 1: LIS Binary Search Trace

**Input:** nums = [10, 9, 2, 5, 3, 7, 101, 18]

```
Process each number, maintain 'tails' array:

num=10: tails=[], bisect_left([],10)=0, append -> tails=[10]
num=9:  tails=[10], bisect_left([10],9)=0, replace -> tails=[9]
num=2:  tails=[9], bisect_left([9],2)=0, replace -> tails=[2]
num=5:  tails=[2], bisect_left([2],5)=1, append -> tails=[2,5]
num=3:  tails=[2,5], bisect_left([2,5],3)=1, replace -> tails=[2,3]
num=7:  tails=[2,3], bisect_left([2,3],7)=2, append -> tails=[2,3,7]
num=101: tails=[2,3,7], bisect_left=3, append -> tails=[2,3,7,101]
num=18: tails=[2,3,7,101], bisect_left=3, replace -> tails=[2,3,7,18]

Final: len(tails) = 4

Note: tails = [2,3,7,18] is NOT an actual LIS!
One actual LIS: [2,5,7,101] or [2,3,7,101] or [2,3,7,18]
```

### Example 2: Russian Doll Envelopes

**Input:** envelopes = [[5,4], [6,4], [6,7], [2,3]]

```
Step 1: Sort by (width asc, height desc)
[2,3], [5,4], [6,7], [6,4]  # (6,7) before (6,4) due to desc height

Step 2: Extract heights
heights = [3, 4, 7, 4]

Step 3: LIS on heights
num=3: tails=[3]
num=4: tails=[3,4]
num=7: tails=[3,4,7]
num=4: tails=[3,4,7], bisect_left=1, replace 4 -> tails=[3,4,7]
       (actually no change since 4 is already there)

LIS length = 3

Answer: 3 envelopes can be nested: [2,3] -> [5,4] -> [6,7]

Why descending height for same width?
- [6,4] and [6,7] have same width, can't nest
- Descending ensures we don't pick both in our LIS
```

---

## Edge Cases & Gotchas

```python
# 1. Empty array
def handle_empty(nums: List[int]) -> int:
    if not nums:
        return 0

# 2. Single element
def handle_single(nums: List[int]) -> int:
    if len(nums) == 1:
        return 1

# 3. All same elements (strictly increasing)
# nums = [5, 5, 5, 5] -> LIS = 1 (strictly increasing)

# 4. All same elements (non-strictly)
# Use bisect_right for non-strictly increasing

# 5. Strictly vs Non-strictly increasing
def lis_strict(nums: List[int]) -> int:
    # bisect_left: strictly increasing
    pass

def lis_non_strict(nums: List[int]) -> int:
    # bisect_right: non-strictly increasing (allows equal)
    pass

# 6. Descending array
# nums = [5, 4, 3, 2, 1] -> LIS = 1

# 7. Already sorted
# nums = [1, 2, 3, 4, 5] -> LIS = 5
```

---

## Interview Tips & Communication

### Framework for LIS Problems

1. **Recognize LIS pattern:**
   - "Longest subsequence with ordering constraint"
   - "Maximum chain of compatible elements"

2. **Choose approach:**
   - Small n or need reconstruction: O(n²) DP
   - Large n, just length: O(n log n) binary search

3. **Handle variants:**
   - Strictly vs non-strictly increasing
   - 2D: Sort one dimension, LIS on other

### Key Phrases

- "This is essentially finding the LIS after sorting by one dimension."
- "I'll use patience sort with binary search for O(n log n) time."
- "The key insight is that the tails array represents potential endings."
- "For same first dimension, I sort descending to prevent invalid matches."

---

## Practice Problems

### Basic LIS

| # | Problem | Key Concept |
|---|---------|-------------|
| 1 | [Longest Increasing Subsequence](https://leetcode.com/problems/longest-increasing-subsequence/) | Classic LIS |
| 2 | [Number of Longest Increasing Subsequence](https://leetcode.com/problems/number-of-longest-increasing-subsequence/) | Count LIS |
| 3 | [Increasing Triplet Subsequence](https://leetcode.com/problems/increasing-triplet-subsequence/) | Length 3 only |
| 4 | [Longest Continuous Increasing Subsequence](https://leetcode.com/problems/longest-continuous-increasing-subsequence/) | Contiguous |

### 2D LIS Variants

| # | Problem | Key Concept |
|---|---------|-------------|
| 5 | [Russian Doll Envelopes](https://leetcode.com/problems/russian-doll-envelopes/) | Sort + LIS |
| 6 | [Maximum Height by Stacking Cuboids](https://leetcode.com/problems/maximum-height-by-stacking-cuboids/) | 3D stacking |
| 7 | [Maximum Length of Pair Chain](https://leetcode.com/problems/maximum-length-of-pair-chain/) | Interval chain |
| 8 | [Longest String Chain](https://leetcode.com/problems/longest-string-chain/) | String ordering |

### LIS with Conditions

| # | Problem | Key Concept |
|---|---------|-------------|
| 9 | [Largest Divisible Subset](https://leetcode.com/problems/largest-divisible-subset/) | Divisibility |
| 10 | [Longest Arithmetic Subsequence](https://leetcode.com/problems/longest-arithmetic-subsequence/) | Fixed difference |
| 11 | [Longest Arithmetic Subsequence of Given Difference](https://leetcode.com/problems/longest-arithmetic-subsequence-of-given-difference/) | Specific diff |
| 12 | [Longest Ideal Subsequence](https://leetcode.com/problems/longest-ideal-subsequence/) | Limited gap |

### Bitonic / Alternating

| # | Problem | Key Concept |
|---|---------|-------------|
| 13 | [Wiggle Subsequence](https://leetcode.com/problems/wiggle-subsequence/) | Alternating |
| 14 | [Longest Bitonic Subsequence](https://www.geeksforgeeks.org/longest-bitonic-subsequence-dp-15/) | Inc then Dec |
| 15 | [Minimum Number of Removals to Make Mountain Array](https://leetcode.com/problems/minimum-number-of-removals-to-make-mountain-array/) | Bitonic variant |

### Modification Problems

| # | Problem | Key Concept |
|---|---------|-------------|
| 16 | [Delete Operation for Two Strings](https://leetcode.com/problems/delete-operation-for-two-strings/) | Two arrays |
| 17 | [Make Array Strictly Increasing](https://leetcode.com/problems/make-array-strictly-increasing/) | Replace elements |
| 18 | [Minimum Operations to Make the Array Increasing](https://leetcode.com/problems/minimum-operations-to-make-the-array-increasing/) | Increment only |
| 19 | [Find Number of LIS](https://leetcode.com/problems/number-of-longest-increasing-subsequence/) | Count all |
| 20 | [Best Team With No Conflicts](https://leetcode.com/problems/best-team-with-no-conflicts/) | 2D variant |

---

## References

- CLRS: Problem 15-4 (Longest Increasing Subsequence)
- [Patience Sorting and LIS](https://en.wikipedia.org/wiki/Patience_sorting)
- [CP Algorithms: LIS](https://cp-algorithms.com/sequences/longest_increasing_subsequence.html)
- [MIT OCW: LIS](https://ocw.mit.edu/courses/6-006-introduction-to-algorithms-fall-2011/)
