---
title: Partition DP (Splitting Arrays/Strings)
topic: Algorithms - Dynamic Programming
difficulty: Medium-Hard
tags: [dp, partition, segments, splitting, array-partition]
status: complete
weight: 15
---

# Partition DP

## 1. Overview

### Core Concept
Partition DP deals with problems where we need to split an array or string into k segments (or optimal number of segments) while optimizing some cost function.

### General Form
```
dp[i][k] = optimal cost to partition first i elements into exactly k segments
         = optimize over j: dp[j][k-1] + cost(j+1, i)
```

### Time Complexity
| Approach | Time | Space |
|----------|------|-------|
| Naive | O(n²k) | O(nk) |
| D&C Optimization | O(nk log n) | O(nk) |
| Convex Hull Trick | O(nk) | O(n) |

---

## 2. Basic Partition into K Segments

### Template: Minimize Maximum Segment Sum

```python
from typing import List

def split_array_largest_sum(nums: List[int], k: int) -> int:
    """
    LeetCode 410 - Split Array Largest Sum
    
    Split nums into k non-empty subarrays minimizing the largest sum.
    
    Approach 1: Binary Search + Greedy (preferred)
    Time: O(n log(sum))
    """
    def can_split(max_sum: int) -> bool:
        """Check if array can be split into ≤k subarrays with max sum ≤ max_sum."""
        count = 1
        current_sum = 0
        
        for num in nums:
            if current_sum + num > max_sum:
                count += 1
                current_sum = num
            else:
                current_sum += num
        
        return count <= k
    
    left = max(nums)
    right = sum(nums)
    
    while left < right:
        mid = (left + right) // 2
        if can_split(mid):
            right = mid
        else:
            left = mid + 1
    
    return left


def split_array_largest_sum_dp(nums: List[int], k: int) -> int:
    """
    Approach 2: DP
    
    dp[i][j] = min largest sum to split first i elements into j parts
    
    Time: O(n²k)
    Space: O(nk)
    """
    n = len(nums)
    prefix = [0] * (n + 1)
    for i in range(n):
        prefix[i + 1] = prefix[i] + nums[i]
    
    def range_sum(l: int, r: int) -> int:
        return prefix[r + 1] - prefix[l]
    
    # dp[i][j] = min max segment sum for nums[:i] split into j parts
    INF = float('inf')
    dp = [[INF] * (k + 1) for _ in range(n + 1)]
    dp[0][0] = 0
    
    for i in range(1, n + 1):
        for j in range(1, min(i, k) + 1):
            for p in range(j - 1, i):  # p = end of previous segment
                segment_sum = range_sum(p, i - 1)
                dp[i][j] = min(dp[i][j], max(dp[p][j - 1], segment_sum))
    
    return dp[n][k]


# Test
nums = [7, 2, 5, 10, 8]
k = 2
print(split_array_largest_sum(nums, k))  # 18
```

---

## 3. Minimize Sum of Segment Costs

### Template: Linear Cost Function

```python
def min_cost_partition(nums: List[int], k: int, cost_fn) -> int:
    """
    Generic partition DP with custom cost function.
    
    dp[i][j] = min total cost to partition first i elements into j segments
    
    Time: O(n²k)
    """
    n = len(nums)
    INF = float('inf')
    dp = [[INF] * (k + 1) for _ in range(n + 1)]
    dp[0][0] = 0
    
    for i in range(1, n + 1):
        for j in range(1, min(i, k) + 1):
            for p in range(j - 1, i):
                segment_cost = cost_fn(nums, p, i - 1)
                if dp[p][j - 1] != INF:
                    dp[i][j] = min(dp[i][j], dp[p][j - 1] + segment_cost)
    
    return dp[n][k]


def minimum_difficulty_job_schedule(jobDifficulty: List[int], d: int) -> int:
    """
    LeetCode 1335 - Minimum Difficulty of a Job Schedule
    
    Partition jobs into d days, minimize sum of daily max difficulties.
    
    Time: O(n²d)
    """
    n = len(jobDifficulty)
    if n < d:
        return -1
    
    INF = float('inf')
    dp = [[INF] * (d + 1) for _ in range(n + 1)]
    dp[0][0] = 0
    
    for i in range(1, n + 1):
        for j in range(1, min(i, d) + 1):
            max_diff = 0
            for p in range(i - 1, j - 2, -1):
                max_diff = max(max_diff, jobDifficulty[p])
                if dp[p][j - 1] != INF:
                    dp[i][j] = min(dp[i][j], dp[p][j - 1] + max_diff)
    
    return dp[n][d]


# Test
jobs = [6, 5, 4, 3, 2, 1]
d = 2
print(minimum_difficulty_job_schedule(jobs, d))  # 7 (day1: [6,5,4,3,2], day2: [1])
```

---

## 4. Palindrome Partition Problems

### Minimum Cuts

```python
def min_cut(s: str) -> int:
    """
    LeetCode 132 - Palindrome Partitioning II
    
    Minimum cuts to partition string into palindromes.
    
    Time: O(n²)
    Space: O(n²) for palindrome table, O(n) for DP
    """
    n = len(s)
    
    # Precompute palindrome table
    is_palin = [[False] * n for _ in range(n)]
    for i in range(n):
        is_palin[i][i] = True
    for i in range(n - 1):
        is_palin[i][i + 1] = (s[i] == s[i + 1])
    for length in range(3, n + 1):
        for i in range(n - length + 1):
            j = i + length - 1
            is_palin[i][j] = (s[i] == s[j] and is_palin[i + 1][j - 1])
    
    # dp[i] = min cuts for s[:i]
    dp = list(range(n))  # Worst case: cut after each char
    
    for i in range(n):
        if is_palin[0][i]:
            dp[i] = 0
        else:
            for j in range(i):
                if is_palin[j + 1][i]:
                    dp[i] = min(dp[i], dp[j] + 1)
    
    return dp[n - 1]


def palindrome_partition_iii(s: str, k: int) -> int:
    """
    LeetCode 1278 - Palindrome Partitioning III
    
    Partition into k substrings with minimum character changes
    to make each substring a palindrome.
    
    Time: O(n²k)
    """
    n = len(s)
    
    # cost[i][j] = changes needed to make s[i:j+1] a palindrome
    cost = [[0] * n for _ in range(n)]
    for length in range(2, n + 1):
        for i in range(n - length + 1):
            j = i + length - 1
            cost[i][j] = cost[i + 1][j - 1] + (0 if s[i] == s[j] else 1)
    
    # dp[i][j] = min changes for s[:i] split into j parts
    INF = float('inf')
    dp = [[INF] * (k + 1) for _ in range(n + 1)]
    dp[0][0] = 0
    
    for i in range(1, n + 1):
        for j in range(1, min(i, k) + 1):
            for p in range(j - 1, i):
                if dp[p][j - 1] != INF:
                    dp[i][j] = min(dp[i][j], dp[p][j - 1] + cost[p][i - 1])
    
    return dp[n][k]


# Test
print(min_cut("aab"))  # 1
print(palindrome_partition_iii("abc", 2))  # 1
```

---

## 5. Partition with Variable Number of Segments

### Optimal Number of Partitions

```python
def min_taps_to_water(n: int, ranges: List[int]) -> int:
    """
    LeetCode 1326 - Minimum Number of Taps to Open
    
    Cover [0, n] with minimum intervals.
    
    Time: O(n)
    """
    # Convert to interval coverage problem
    max_reach = [0] * (n + 1)
    for i, r in enumerate(ranges):
        left = max(0, i - r)
        right = min(n, i + r)
        max_reach[left] = max(max_reach[left], right)
    
    # Greedy interval coverage
    taps = 0
    current_end = 0
    next_end = 0
    
    for i in range(n + 1):
        if i > next_end:
            return -1  # Gap
        if i > current_end:
            taps += 1
            current_end = next_end
        next_end = max(next_end, max_reach[i])
    
    return taps


def video_stitching(clips: List[List[int]], time: int) -> int:
    """
    LeetCode 1024 - Video Stitching
    
    Minimum clips to cover [0, time].
    
    Time: O(n log n)
    """
    clips.sort()
    
    count = 0
    current_end = 0
    i = 0
    n = len(clips)
    
    while current_end < time:
        next_end = current_end
        
        while i < n and clips[i][0] <= current_end:
            next_end = max(next_end, clips[i][1])
            i += 1
        
        if next_end == current_end:
            return -1  # No progress
        
        count += 1
        current_end = next_end
    
    return count
```

---

## 6. Partition Array for Maximum Sum

```python
def max_sum_after_partitioning(arr: List[int], k: int) -> int:
    """
    LeetCode 1043 - Partition Array for Maximum Sum
    
    Partition array where each segment has length ≤ k.
    Each element in segment becomes the max of that segment.
    
    Time: O(nk)
    """
    n = len(arr)
    dp = [0] * (n + 1)
    
    for i in range(1, n + 1):
        max_val = 0
        for j in range(1, min(k, i) + 1):
            max_val = max(max_val, arr[i - j])
            dp[i] = max(dp[i], dp[i - j] + max_val * j)
    
    return dp[n]


# Test
arr = [1, 15, 7, 9, 2, 5, 10]
k = 3
print(max_sum_after_partitioning(arr, k))  # 84
```

---

## 7. Copy Books / Painters Partition

```python
def copy_books(pages: List[int], k: int) -> int:
    """
    Classic: Copy Books / Painters Partition
    
    Minimize maximum pages any one copier handles.
    
    Time: O(n log(sum))
    """
    def feasible(max_pages: int) -> bool:
        copiers = 1
        current = 0
        for p in pages:
            if current + p > max_pages:
                copiers += 1
                current = p
            else:
                current += p
        return copiers <= k
    
    left = max(pages)
    right = sum(pages)
    
    while left < right:
        mid = (left + right) // 2
        if feasible(mid):
            right = mid
        else:
            left = mid + 1
    
    return left


def copy_books_with_order(pages: List[int], k: int) -> List[List[int]]:
    """
    Return the actual partition.
    """
    max_pages = copy_books(pages, k)
    
    # Reconstruct partition
    result = []
    current_group = []
    current_sum = 0
    
    for p in pages:
        if current_sum + p > max_pages and current_group:
            result.append(current_group)
            current_group = []
            current_sum = 0
        current_group.append(p)
        current_sum += p
    
    if current_group:
        result.append(current_group)
    
    return result
```

---

## 8. Partition with Prefix Sum Costs

```python
def split_array_min_sum_squares(nums: List[int], k: int) -> int:
    """
    Split array into k parts minimizing sum of squares of segment sums.
    
    cost(l, r) = (prefix[r+1] - prefix[l])²
    
    Time: O(n²k) or O(nk log n) with D&C optimization
    """
    n = len(nums)
    prefix = [0] * (n + 1)
    for i in range(n):
        prefix[i + 1] = prefix[i] + nums[i]
    
    def segment_cost(l: int, r: int) -> int:
        return (prefix[r + 1] - prefix[l]) ** 2
    
    INF = float('inf')
    dp = [[INF] * (k + 1) for _ in range(n + 1)]
    dp[0][0] = 0
    
    for i in range(1, n + 1):
        for j in range(1, min(i, k) + 1):
            for p in range(j - 1, i):
                if dp[p][j - 1] != INF:
                    dp[i][j] = min(dp[i][j], dp[p][j - 1] + segment_cost(p, i - 1))
    
    return dp[n][k]


def split_array_into_consecutive_subsequences(nums: List[int]) -> bool:
    """
    LeetCode 659 - Split Array into Consecutive Subsequences
    
    Check if array can be split into subsequences of length ≥ 3.
    
    Time: O(n)
    """
    from collections import Counter
    
    freq = Counter(nums)
    need = Counter()  # need[x] = number of subsequences waiting for x
    
    for num in nums:
        if freq[num] == 0:
            continue
        
        freq[num] -= 1
        
        if need[num] > 0:
            # Append to existing subsequence
            need[num] -= 1
            need[num + 1] += 1
        elif freq[num + 1] > 0 and freq[num + 2] > 0:
            # Start new subsequence
            freq[num + 1] -= 1
            freq[num + 2] -= 1
            need[num + 3] += 1
        else:
            return False
    
    return True
```

---

## 9. Partition Labels

```python
def partition_labels(s: str) -> List[int]:
    """
    LeetCode 763 - Partition Labels
    
    Partition string so each letter appears in at most one part.
    Maximize number of parts.
    
    Time: O(n)
    """
    # Find last occurrence of each character
    last = {c: i for i, c in enumerate(s)}
    
    result = []
    start = 0
    end = 0
    
    for i, c in enumerate(s):
        end = max(end, last[c])
        if i == end:
            result.append(end - start + 1)
            start = end + 1
    
    return result


def max_number_of_substrings(s: str) -> List[str]:
    """
    LeetCode 1520 - Maximum Number of Non-Overlapping Substrings
    
    Partition into maximum substrings where each contains all occurrences
    of its characters.
    
    Time: O(n * 26)
    """
    n = len(s)
    
    # Find first and last occurrence of each character
    first = {}
    last = {}
    for i, c in enumerate(s):
        if c not in first:
            first[c] = i
        last[c] = i
    
    # Extend intervals
    def get_interval(c: str) -> tuple:
        l, r = first[c], last[c]
        i = l
        while i <= r:
            char = s[i]
            if first[char] < l:
                return None  # Can't form valid interval starting at l
            r = max(r, last[char])
            i += 1
        return (l, r)
    
    # Get all valid intervals
    intervals = []
    for c in first:
        interval = get_interval(c)
        if interval:
            intervals.append(interval)
    
    # Greedy: select non-overlapping intervals, prefer shorter ones ending earlier
    intervals.sort(key=lambda x: x[1])
    
    result = []
    prev_end = -1
    
    for l, r in intervals:
        if l > prev_end:
            result.append(s[l:r + 1])
            prev_end = r
    
    return result


# Test
print(partition_labels("ababcbacadefegdehijhklij"))  # [9, 7, 8]
```

---

## 10. D&C Optimization for Partition DP

```python
def split_array_dc_optimization(nums: List[int], k: int) -> int:
    """
    Partition array into k parts minimizing sum of segment costs.
    Uses Divide and Conquer optimization when optimal split is monotonic.
    
    Time: O(nk log n)
    
    Requirement: opt[i][j] ≤ opt[i][j+1] (monotonicity)
    """
    n = len(nums)
    prefix = [0] * (n + 1)
    for i in range(n):
        prefix[i + 1] = prefix[i] + nums[i]
    
    def cost(l: int, r: int) -> int:
        # Example: sum of squares
        total = prefix[r + 1] - prefix[l]
        return total * total
    
    INF = float('inf')
    dp = [[INF] * (n + 1) for _ in range(k + 1)]
    dp[0][0] = 0
    
    def solve(layer: int, lo: int, hi: int, opt_lo: int, opt_hi: int):
        if lo > hi:
            return
        
        mid = (lo + hi) // 2
        best_opt = opt_lo
        
        for opt in range(opt_lo, min(mid, opt_hi + 1)):
            val = dp[layer - 1][opt] + cost(opt, mid - 1)
            if val < dp[layer][mid]:
                dp[layer][mid] = val
                best_opt = opt
        
        solve(layer, lo, mid - 1, opt_lo, best_opt)
        solve(layer, mid + 1, hi, best_opt, opt_hi)
    
    for layer in range(1, k + 1):
        solve(layer, layer, n, layer - 1, n - 1)
    
    return dp[k][n]
```

---

## 11. Practice Problems

### LeetCode Problems

| # | Problem | Pattern | Difficulty |
|---|---------|---------|------------|
| 132 | [Palindrome Partitioning II](https://leetcode.com/problems/palindrome-partitioning-ii/) | Min cuts | Hard |
| 410 | [Split Array Largest Sum](https://leetcode.com/problems/split-array-largest-sum/) | Binary search / DP | Hard |
| 659 | [Split Array into Consecutive Subsequences](https://leetcode.com/problems/split-array-into-consecutive-subsequences/) | Greedy | Medium |
| 763 | [Partition Labels](https://leetcode.com/problems/partition-labels/) | Greedy | Medium |
| 813 | [Largest Sum of Averages](https://leetcode.com/problems/largest-sum-of-averages/) | Partition DP | Medium |
| 1013 | [Partition Array Into Three Parts](https://leetcode.com/problems/partition-array-into-three-parts-with-equal-sum/) | Prefix sum | Easy |
| 1024 | [Video Stitching](https://leetcode.com/problems/video-stitching/) | Interval cover | Medium |
| 1043 | [Partition Array for Maximum Sum](https://leetcode.com/problems/partition-array-for-maximum-sum/) | DP | Medium |
| 1105 | [Filling Bookcase Shelves](https://leetcode.com/problems/filling-bookcase-shelves/) | DP | Medium |
| 1278 | [Palindrome Partitioning III](https://leetcode.com/problems/palindrome-partitioning-iii/) | DP | Hard |
| 1326 | [Minimum Number of Taps](https://leetcode.com/problems/minimum-number-of-taps-to-open-to-water-a-garden/) | Interval cover | Hard |
| 1335 | [Minimum Difficulty of a Job Schedule](https://leetcode.com/problems/minimum-difficulty-of-a-job-schedule/) | Partition DP | Hard |
| 1416 | [Restore The Array](https://leetcode.com/problems/restore-the-array/) | Counting DP | Hard |
| 1477 | [Find Two Non-overlapping Subarrays](https://leetcode.com/problems/find-two-non-overlapping-sub-arrays-each-with-target-sum/) | Partition + min | Medium |
| 1520 | [Maximum Number of Non-Overlapping Substrings](https://leetcode.com/problems/maximum-number-of-non-overlapping-substrings/) | Intervals | Hard |
| 1745 | [Palindrome Partitioning IV](https://leetcode.com/problems/palindrome-partitioning-iv/) | DP | Hard |
| 2369 | [Check if Valid Partition Exists](https://leetcode.com/problems/check-if-there-is-a-valid-partition-for-the-array/) | DP | Medium |

### Classic Problems

| Problem | Pattern | Notes |
|---------|---------|-------|
| Painters Partition | Binary search | Same as Split Array |
| Copy Books | Binary search | Classic partition |
| Egg Drop | Partition DP | dp[eggs][floors] |
| Matrix Chain Multiplication | Interval DP | Related to partition |

---

## 12. Key Patterns Summary

```
Partition DP Decision Tree:

1. Minimize maximum segment value?
   → Binary search + greedy feasibility check
   
2. Minimize/maximize sum of segment costs?
   → Standard partition DP: O(n²k)
   → Can optimize with D&C if monotonicity holds
   
3. Find number of ways to partition?
   → Counting DP: dp[i][k] = sum over valid splits
   
4. Variable number of partitions (minimize count)?
   → Often greedy interval covering
   
5. Each element in exactly one partition?
   → Usually dp[i] = best for first i elements
   
6. Cost function structure:
   - Linear: May use prefix sums
   - Quadratic: May use CHT
   - Max/Min based: Binary search often works
```

---

## 13. References

1. CP-Algorithms: Divide and Conquer DP
2. USACO Guide: Range DP
3. Codeforces Blog: Partition DP Problems
4. LeetCode Premium: Dynamic Programming Patterns
