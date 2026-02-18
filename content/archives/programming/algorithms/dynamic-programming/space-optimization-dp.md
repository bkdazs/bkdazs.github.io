---
title: Space Optimization and Rolling Arrays
topic: Algorithms - Dynamic Programming
difficulty: Medium
tags: [dp, space-optimization, rolling-array, memory-efficient]
status: complete
weight: 24
---

# Space Optimization and Rolling Arrays

## 1. Overview

This document covers techniques to reduce space complexity in DP:
- Rolling arrays (1D, 2D)
- In-place computation
- State compression
- Memory-efficient recurrences

---

## 2. Core Techniques

### Technique Overview

```
Space Optimization Techniques:
├── Rolling Arrays
│   ├── Two-row optimization
│   ├── Single-row optimization
│   └── Circular buffer
├── In-place Updates
│   ├── Reverse iteration
│   ├── Forward iteration
│   └── Diagonal traversal
├── State Compression
│   ├── Bitmask compression
│   ├── Coordinate compression
│   └── Hash-based states
└── Mathematical Tricks
    ├── Matrix exponentiation
    ├── Recurrence relations
    └── Constant factors
```

---

## 3. Rolling Array Patterns

### Two-Row Rolling Array

```python
from typing import List

def lcs_space_optimized(text1: str, text2: str) -> int:
    """
    LCS with O(min(m, n)) space instead of O(mn).
    
    Original: dp[i][j] = LCS of text1[:i] and text2[:j]
    Only need previous row to compute current row.
    """
    # Ensure text1 is shorter for better space
    if len(text1) > len(text2):
        text1, text2 = text2, text1
    
    m, n = len(text1), len(text2)
    
    # Two rows: previous and current
    prev = [0] * (m + 1)
    curr = [0] * (m + 1)
    
    for j in range(1, n + 1):
        for i in range(1, m + 1):
            if text1[i - 1] == text2[j - 1]:
                curr[i] = prev[i - 1] + 1
            else:
                curr[i] = max(prev[i], curr[i - 1])
        
        # Swap rows
        prev, curr = curr, prev
    
    return prev[m]


def edit_distance_space_optimized(word1: str, word2: str) -> int:
    """
    Edit distance with O(min(m, n)) space.
    """
    if len(word1) > len(word2):
        word1, word2 = word2, word1
    
    m, n = len(word1), len(word2)
    
    prev = list(range(m + 1))  # Base case: distance to empty string
    curr = [0] * (m + 1)
    
    for j in range(1, n + 1):
        curr[0] = j  # Distance from empty string
        
        for i in range(1, m + 1):
            if word1[i - 1] == word2[j - 1]:
                curr[i] = prev[i - 1]
            else:
                curr[i] = 1 + min(
                    prev[i],      # Delete from word1
                    curr[i - 1],  # Insert into word1
                    prev[i - 1]   # Replace
                )
        
        prev, curr = curr, prev
    
    return prev[m]
```

### Single-Row Optimization

```python
def knapsack_01_single_row(weights: List[int], values: List[int], capacity: int) -> int:
    """
    0/1 Knapsack with O(capacity) space.
    
    Key: Process weights in REVERSE to avoid using same item twice.
    """
    dp = [0] * (capacity + 1)
    
    for i in range(len(weights)):
        w, v = weights[i], values[i]
        
        # MUST iterate backwards!
        for c in range(capacity, w - 1, -1):
            dp[c] = max(dp[c], dp[c - w] + v)
    
    return dp[capacity]


def unbounded_knapsack_single_row(weights: List[int], values: List[int], capacity: int) -> int:
    """
    Unbounded knapsack with O(capacity) space.
    
    Key: Process weights FORWARD (can reuse items).
    """
    dp = [0] * (capacity + 1)
    
    for i in range(len(weights)):
        w, v = weights[i], values[i]
        
        # Forward iteration allows item reuse
        for c in range(w, capacity + 1):
            dp[c] = max(dp[c], dp[c - w] + v)
    
    return dp[capacity]


def coin_change_min_coins(coins: List[int], amount: int) -> int:
    """
    Minimum coins to make amount.
    
    O(amount) space, unbounded usage.
    """
    INF = float('inf')
    dp = [INF] * (amount + 1)
    dp[0] = 0
    
    for coin in coins:
        for a in range(coin, amount + 1):
            if dp[a - coin] < INF:
                dp[a] = min(dp[a], dp[a - coin] + 1)
    
    return dp[amount] if dp[amount] < INF else -1
```

---

## 4. Grid DP Space Optimization

### Path Problems

```python
def unique_paths_space_optimized(m: int, n: int) -> int:
    """
    Count unique paths in m×n grid.
    
    O(min(m, n)) space instead of O(mn).
    """
    if m > n:
        m, n = n, m  # Ensure m is smaller
    
    dp = [1] * m  # First row/column is all 1s
    
    for _ in range(1, n):
        for i in range(1, m):
            dp[i] += dp[i - 1]
    
    return dp[-1]


def min_path_sum_space_optimized(grid: List[List[int]]) -> int:
    """
    Minimum path sum from top-left to bottom-right.
    
    O(n) space where n = number of columns.
    """
    m, n = len(grid), len(grid[0])
    
    dp = [float('inf')] * n
    dp[0] = 0
    
    for i in range(m):
        dp[0] += grid[i][0]
        
        for j in range(1, n):
            if i == 0:
                dp[j] = dp[j - 1] + grid[i][j]
            else:
                dp[j] = min(dp[j], dp[j - 1]) + grid[i][j]
    
    return dp[-1]


def maximal_square_space_optimized(matrix: List[List[str]]) -> int:
    """
    LeetCode 221: Maximal Square
    
    O(n) space.
    """
    if not matrix:
        return 0
    
    m, n = len(matrix), len(matrix[0])
    dp = [0] * (n + 1)
    max_side = 0
    prev = 0  # dp[i-1][j-1]
    
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            temp = dp[j]  # Save before overwriting
            
            if matrix[i - 1][j - 1] == '1':
                dp[j] = min(dp[j - 1], dp[j], prev) + 1
                max_side = max(max_side, dp[j])
            else:
                dp[j] = 0
            
            prev = temp
        
        prev = 0  # Reset for new row
    
    return max_side * max_side
```

### Triangle DP

```python
def minimum_total_triangle(triangle: List[List[int]]) -> int:
    """
    LeetCode 120: Triangle
    
    Minimum path sum from top to bottom.
    
    Can do O(n) space by modifying input or using bottom-up.
    """
    n = len(triangle)
    
    # Bottom-up: dp = last row
    dp = triangle[-1][:]
    
    for i in range(n - 2, -1, -1):
        for j in range(len(triangle[i])):
            dp[j] = triangle[i][j] + min(dp[j], dp[j + 1])
    
    return dp[0]


def minimum_falling_path_sum(matrix: List[List[int]]) -> int:
    """
    LeetCode 931: Minimum Falling Path Sum
    
    Can move down, down-left, or down-right.
    """
    n = len(matrix)
    
    prev = matrix[0][:]
    
    for i in range(1, n):
        curr = [0] * n
        
        for j in range(n):
            left = prev[j - 1] if j > 0 else float('inf')
            middle = prev[j]
            right = prev[j + 1] if j < n - 1 else float('inf')
            
            curr[j] = matrix[i][j] + min(left, middle, right)
        
        prev = curr
    
    return min(prev)
```

---

## 5. Subsequence DP Space Optimization

### LIS Variants

```python
def lis_space_optimized(nums: List[int]) -> int:
    """
    LIS using O(n) space with binary search.
    
    tails[i] = smallest tail of all increasing subsequences of length i+1
    """
    from bisect import bisect_left
    
    tails = []
    
    for num in nums:
        idx = bisect_left(tails, num)
        
        if idx == len(tails):
            tails.append(num)
        else:
            tails[idx] = num
    
    return len(tails)


def longest_common_subsequence_hunt_szymanski(text1: str, text2: str) -> int:
    """
    Hunt-Szymanski algorithm for LCS.
    
    O((n + m) log min(n, m)) time when matches are sparse.
    Uses O(n + m) space.
    """
    from bisect import bisect_left
    from collections import defaultdict
    
    # Build position map for text2
    pos = defaultdict(list)
    for i, c in enumerate(text2):
        pos[c].append(i)
    
    # Process text1, building LIS on matching positions
    tails = []  # tails[i] = smallest ending position of LCS of length i+1
    
    for c in text1:
        if c in pos:
            # Process positions in reverse to avoid conflicts
            for p in reversed(pos[c]):
                idx = bisect_left(tails, p)
                
                if idx == len(tails):
                    tails.append(p)
                elif tails[idx] > p:
                    tails[idx] = p
    
    return len(tails)
```

---

## 6. In-Place DP

### When Input Can Be Modified

```python
def coin_change_count_inplace(coins: List[int], amount: int) -> int:
    """
    Count ways to make amount.
    
    When we can use a single array and it's acceptable.
    """
    dp = [0] * (amount + 1)
    dp[0] = 1
    
    for coin in coins:
        for a in range(coin, amount + 1):
            dp[a] += dp[a - coin]
    
    return dp[amount]


def house_robber_constant_space(nums: List[int]) -> int:
    """
    LeetCode 198: House Robber
    
    O(1) space using two variables.
    """
    if not nums:
        return 0
    if len(nums) == 1:
        return nums[0]
    
    prev2 = 0  # dp[i-2]
    prev1 = 0  # dp[i-1]
    
    for num in nums:
        curr = max(prev1, prev2 + num)
        prev2, prev1 = prev1, curr
    
    return prev1


def fibonacci_constant_space(n: int) -> int:
    """
    Fibonacci with O(1) space.
    """
    if n <= 1:
        return n
    
    a, b = 0, 1
    
    for _ in range(2, n + 1):
        a, b = b, a + b
    
    return b
```

---

## 7. Multi-Dimensional Rolling Arrays

### 3D to 2D Reduction

```python
def interleaving_string_space_optimized(s1: str, s2: str, s3: str) -> bool:
    """
    LeetCode 97: Interleaving String
    
    Standard: O(mn) space
    Optimized: O(min(m, n)) space
    """
    m, n = len(s1), len(s2)
    
    if m + n != len(s3):
        return False
    
    if m < n:
        s1, s2 = s2, s1
        m, n = n, m
    
    # dp[j] = can we form s3[:i+j] using s1[:i] and s2[:j]
    dp = [False] * (n + 1)
    
    for i in range(m + 1):
        for j in range(n + 1):
            if i == 0 and j == 0:
                dp[j] = True
            elif i == 0:
                dp[j] = dp[j - 1] and s2[j - 1] == s3[j - 1]
            elif j == 0:
                dp[j] = dp[j] and s1[i - 1] == s3[i - 1]
            else:
                dp[j] = (
                    (dp[j] and s1[i - 1] == s3[i + j - 1]) or
                    (dp[j - 1] and s2[j - 1] == s3[i + j - 1])
                )
    
    return dp[n]


def longest_palindromic_subsequence_space_optimized(s: str) -> int:
    """
    LeetCode 516: Longest Palindromic Subsequence
    
    Standard: dp[i][j] = LPS of s[i:j+1]
    Optimized: O(n) space with careful iteration.
    """
    n = len(s)
    
    # dp[j] represents dp[i][j] for current i
    dp = [0] * n
    
    for i in range(n - 1, -1, -1):
        new_dp = [0] * n
        new_dp[i] = 1  # Single character
        
        for j in range(i + 1, n):
            if s[i] == s[j]:
                new_dp[j] = dp[j - 1] + 2
            else:
                new_dp[j] = max(dp[j], new_dp[j - 1])
        
        dp = new_dp
    
    return dp[n - 1] if n > 0 else 0
```

---

## 8. State Space Reduction

### Coordinate Compression

```python
def lis_with_large_values(nums: List[int]) -> int:
    """
    LIS when values can be very large (up to 10^9).
    
    Compress coordinates to [0, n-1] range.
    """
    from bisect import bisect_left
    
    # Coordinate compression
    sorted_unique = sorted(set(nums))
    compress = {v: i for i, v in enumerate(sorted_unique)}
    
    # Now use compressed values
    compressed = [compress[x] for x in nums]
    
    # Standard LIS
    tails = []
    for num in compressed:
        idx = bisect_left(tails, num)
        if idx == len(tails):
            tails.append(num)
        else:
            tails[idx] = num
    
    return len(tails)


def sparse_dp_with_dict(operations: List[Tuple[str, int]]) -> int:
    """
    DP when state space is sparse.
    
    Use dictionary instead of array.
    """
    # Example: tracking possible sums
    possible = {0: 1}  # sum -> count
    
    for op, val in operations:
        new_possible = {}
        
        for s, count in possible.items():
            if op == 'add':
                new_sum = s + val
            else:
                new_sum = s - val
            
            new_possible[new_sum] = new_possible.get(new_sum, 0) + count
        
        possible = new_possible
    
    return sum(possible.values())
```

---

## 9. Advanced Techniques

### Divide and Conquer Space Optimization

```python
def lcs_with_reconstruction_linear_space(text1: str, text2: str) -> str:
    """
    LCS with reconstruction using Hirschberg's algorithm.
    
    O(m + n) space for both computation AND reconstruction.
    """
    def lcs_length_last_row(a: str, b: str) -> List[int]:
        """Compute last row of LCS DP table."""
        m = len(a)
        prev = [0] * (m + 1)
        curr = [0] * (m + 1)
        
        for j in range(len(b)):
            for i in range(m):
                if a[i] == b[j]:
                    curr[i + 1] = prev[i] + 1
                else:
                    curr[i + 1] = max(prev[i + 1], curr[i])
            prev, curr = curr, prev
        
        return prev
    
    def hirschberg(a: str, b: str) -> str:
        """Divide and conquer LCS."""
        m, n = len(a), len(b)
        
        if m == 0:
            return ""
        if n == 0:
            return ""
        if m == 1:
            return a if a in b else ""
        
        # Divide b in half
        mid = n // 2
        
        # Forward pass: LCS length from start
        forward = lcs_length_last_row(a, b[:mid])
        
        # Backward pass: LCS length from end (reverse strings)
        backward = lcs_length_last_row(a[::-1], b[mid:][::-1])
        backward = backward[::-1]
        
        # Find optimal split point in a
        best_k = 0
        best_sum = forward[0] + backward[0]
        
        for k in range(m + 1):
            current_sum = forward[k] + backward[k]
            if current_sum > best_sum:
                best_sum = current_sum
                best_k = k
        
        # Recursively solve subproblems
        left = hirschberg(a[:best_k], b[:mid])
        right = hirschberg(a[best_k:], b[mid:])
        
        return left + right
    
    return hirschberg(text1, text2)
```

### Sliding Window DP

```python
def max_sum_subarray_fixed_length(nums: List[int], k: int) -> int:
    """
    Maximum sum subarray of length k.
    
    O(1) space with sliding window.
    """
    if len(nums) < k:
        return 0
    
    # Initial window
    window_sum = sum(nums[:k])
    max_sum = window_sum
    
    # Slide window
    for i in range(k, len(nums)):
        window_sum += nums[i] - nums[i - k]
        max_sum = max(max_sum, window_sum)
    
    return max_sum


def sliding_window_maximum(nums: List[int], k: int) -> List[int]:
    """
    LeetCode 239: Sliding Window Maximum
    
    O(n) time, O(k) space using monotonic deque.
    """
    from collections import deque
    
    dq = deque()  # Indices of useful elements
    result = []
    
    for i, num in enumerate(nums):
        # Remove elements outside window
        while dq and dq[0] < i - k + 1:
            dq.popleft()
        
        # Remove smaller elements (they're useless)
        while dq and nums[dq[-1]] < num:
            dq.pop()
        
        dq.append(i)
        
        # Add to result once window is full
        if i >= k - 1:
            result.append(nums[dq[0]])
    
    return result
```

---

## 10. Practice Problems

### LeetCode Problems

| # | Problem | Original Space | Optimized Space |
|---|---------|----------------|-----------------|
| 70 | [Climbing Stairs](https://leetcode.com/problems/climbing-stairs/) | O(n) | O(1) |
| 91 | [Decode Ways](https://leetcode.com/problems/decode-ways/) | O(n) | O(1) |
| 120 | [Triangle](https://leetcode.com/problems/triangle/) | O(n²) | O(n) |
| 198 | [House Robber](https://leetcode.com/problems/house-robber/) | O(n) | O(1) |
| 221 | [Maximal Square](https://leetcode.com/problems/maximal-square/) | O(mn) | O(n) |
| 300 | [LIS](https://leetcode.com/problems/longest-increasing-subsequence/) | O(n²) | O(n) |
| 322 | [Coin Change](https://leetcode.com/problems/coin-change/) | - | O(amount) |
| 516 | [Longest Palindromic Subseq](https://leetcode.com/problems/longest-palindromic-subsequence/) | O(n²) | O(n) |
| 931 | [Min Falling Path Sum](https://leetcode.com/problems/minimum-falling-path-sum/) | O(mn) | O(n) |
| 1143 | [LCS](https://leetcode.com/problems/longest-common-subsequence/) | O(mn) | O(min(m,n)) |

---

## 11. Quick Reference

```
Space Optimization Cheat Sheet:

1. dp[i] depends only on dp[i-1]?
   → O(1) space with two variables
   
2. dp[i][j] depends on row i-1?
   → O(n) space with two arrays
   
3. 0/1 Knapsack variant?
   → Process capacity BACKWARD for O(capacity)
   
4. Unbounded Knapsack variant?
   → Process capacity FORWARD for O(capacity)
   
5. dp[i][j] depends on dp[i-1][j-1], dp[i-1][j], dp[i][j-1]?
   → O(n) space, save dp[i-1][j-1] before overwrite
   
6. Need to reconstruct solution?
   → Hirschberg for O(m+n) space LCS
   → Otherwise may need full table

Iteration Direction Rules:
- Forward: When item can be reused (unbounded)
- Backward: When item used once (0/1)
- Row-by-row: When only previous row needed
- Diagonal: Some grid problems
```
