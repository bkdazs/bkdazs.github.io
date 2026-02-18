---
title: DP Solution Reconstruction & Path Recovery
topic: Algorithms - Dynamic Programming
difficulty: Medium
tags: [dp, reconstruction, path-recovery, backtracking]
status: complete
weight: 19
---

# DP Solution Reconstruction & Path Recovery

## 1. Overview

### Core Concept
DP finds optimal values, but often we need the actual solution (path, sequence, choices). Two main approaches:
1. **Store parent pointers** during DP
2. **Backtrack** through DP table after computation

### When Reconstruction is Needed
| Problem Type | What to Reconstruct |
|--------------|---------------------|
| Shortest path | Actual path |
| LCS/LIS | The subsequence |
| Knapsack | Selected items |
| Edit distance | Sequence of operations |
| Partition | The segments |

---

## 2. LCS Reconstruction

### Basic Approach

```python
from typing import List, Tuple

def lcs_with_reconstruction(text1: str, text2: str) -> Tuple[int, str]:
    """
    LeetCode 1143 - Longest Common Subsequence (with reconstruction)
    
    Returns: (length, actual LCS string)
    
    Time: O(mn)
    Space: O(mn)
    """
    m, n = len(text1), len(text2)
    
    # DP table
    dp = [[0] * (n + 1) for _ in range(m + 1)]
    
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if text1[i - 1] == text2[j - 1]:
                dp[i][j] = dp[i - 1][j - 1] + 1
            else:
                dp[i][j] = max(dp[i - 1][j], dp[i][j - 1])
    
    # Backtrack to find LCS
    lcs = []
    i, j = m, n
    
    while i > 0 and j > 0:
        if text1[i - 1] == text2[j - 1]:
            lcs.append(text1[i - 1])
            i -= 1
            j -= 1
        elif dp[i - 1][j] > dp[i][j - 1]:
            i -= 1
        else:
            j -= 1
    
    return dp[m][n], ''.join(reversed(lcs))


def shortest_common_supersequence(str1: str, str2: str) -> str:
    """
    LeetCode 1092 - Shortest Common Supersequence
    
    Find shortest string containing both str1 and str2 as subsequences.
    Uses LCS reconstruction.
    
    Time: O(mn)
    """
    m, n = len(str1), len(str2)
    
    # Find LCS via DP
    dp = [[0] * (n + 1) for _ in range(m + 1)]
    
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if str1[i - 1] == str2[j - 1]:
                dp[i][j] = dp[i - 1][j - 1] + 1
            else:
                dp[i][j] = max(dp[i - 1][j], dp[i][j - 1])
    
    # Build SCS by backtracking
    result = []
    i, j = m, n
    
    while i > 0 or j > 0:
        if i == 0:
            result.append(str2[j - 1])
            j -= 1
        elif j == 0:
            result.append(str1[i - 1])
            i -= 1
        elif str1[i - 1] == str2[j - 1]:
            result.append(str1[i - 1])
            i -= 1
            j -= 1
        elif dp[i - 1][j] > dp[i][j - 1]:
            result.append(str1[i - 1])
            i -= 1
        else:
            result.append(str2[j - 1])
            j -= 1
    
    return ''.join(reversed(result))


# Test
length, lcs = lcs_with_reconstruction("abcde", "ace")
print(f"LCS length: {length}, LCS: {lcs}")  # 3, "ace"
```

---

## 3. LIS Reconstruction

### O(n²) with Parent Pointers

```python
def lis_with_reconstruction(nums: List[int]) -> Tuple[int, List[int]]:
    """
    Longest Increasing Subsequence with actual sequence.
    
    Time: O(n²)
    """
    if not nums:
        return 0, []
    
    n = len(nums)
    dp = [1] * n
    parent = [-1] * n
    
    for i in range(1, n):
        for j in range(i):
            if nums[j] < nums[i] and dp[j] + 1 > dp[i]:
                dp[i] = dp[j] + 1
                parent[i] = j
    
    # Find end of LIS
    max_len = max(dp)
    end_idx = dp.index(max_len)
    
    # Backtrack
    lis = []
    idx = end_idx
    while idx != -1:
        lis.append(nums[idx])
        idx = parent[idx]
    
    return max_len, lis[::-1]


def lis_binary_search_with_reconstruction(nums: List[int]) -> List[int]:
    """
    O(n log n) LIS with reconstruction.
    
    Track indices of elements that contribute to each length.
    """
    if not nums:
        return []
    
    n = len(nums)
    import bisect
    
    # tails[i] = smallest tail element for LIS of length i+1
    tails = []
    # indices[i] = index in nums of element at tails[i]
    indices = []
    # parent[i] = index of previous element in LIS ending at nums[i]
    parent = [-1] * n
    
    for i, num in enumerate(nums):
        pos = bisect.bisect_left(tails, num)
        
        if pos == len(tails):
            tails.append(num)
            indices.append(i)
        else:
            tails[pos] = num
            indices[pos] = i
        
        # Set parent
        if pos > 0:
            parent[i] = indices[pos - 1]
    
    # Backtrack from last element of LIS
    lis = []
    idx = indices[-1]
    while idx != -1:
        lis.append(nums[idx])
        idx = parent[idx]
    
    return lis[::-1]


# Test
length, lis = lis_with_reconstruction([10, 9, 2, 5, 3, 7, 101, 18])
print(f"LIS length: {length}, LIS: {lis}")  # 4, [2, 3, 7, 18] or [2, 5, 7, 18]
```

---

## 4. Knapsack Reconstruction

### 0/1 Knapsack Items

```python
def knapsack_with_items(weights: List[int], values: List[int], capacity: int) -> Tuple[int, List[int]]:
    """
    0/1 Knapsack with selected items.
    
    Returns: (max_value, list of selected item indices)
    
    Time: O(n × capacity)
    Space: O(n × capacity)
    """
    n = len(weights)
    
    # DP table
    dp = [[0] * (capacity + 1) for _ in range(n + 1)]
    
    for i in range(1, n + 1):
        w, v = weights[i - 1], values[i - 1]
        for c in range(capacity + 1):
            dp[i][c] = dp[i - 1][c]
            if c >= w:
                dp[i][c] = max(dp[i][c], dp[i - 1][c - w] + v)
    
    # Backtrack to find items
    selected = []
    c = capacity
    
    for i in range(n, 0, -1):
        if dp[i][c] != dp[i - 1][c]:
            selected.append(i - 1)  # Item i-1 was selected
            c -= weights[i - 1]
    
    return dp[n][capacity], selected[::-1]


def partition_equal_subset_sum_items(nums: List[int]) -> Tuple[bool, List[int], List[int]]:
    """
    LeetCode 416 - Partition Equal Subset Sum (with actual partition)
    
    Returns: (can_partition, subset1, subset2)
    """
    total = sum(nums)
    if total % 2 != 0:
        return False, [], []
    
    target = total // 2
    n = len(nums)
    
    # DP
    dp = [[False] * (target + 1) for _ in range(n + 1)]
    dp[0][0] = True
    
    for i in range(1, n + 1):
        for s in range(target + 1):
            dp[i][s] = dp[i - 1][s]
            if s >= nums[i - 1]:
                dp[i][s] = dp[i][s] or dp[i - 1][s - nums[i - 1]]
    
    if not dp[n][target]:
        return False, [], []
    
    # Backtrack
    subset1 = []
    s = target
    for i in range(n, 0, -1):
        if s >= nums[i - 1] and dp[i - 1][s - nums[i - 1]]:
            subset1.append(nums[i - 1])
            s -= nums[i - 1]
    
    subset2 = list(nums)
    for item in subset1:
        subset2.remove(item)
    
    return True, subset1, subset2


# Test
max_val, items = knapsack_with_items([2, 3, 4, 5], [3, 4, 5, 6], 5)
print(f"Max value: {max_val}, Items: {items}")
```

---

## 5. Edit Distance Reconstruction

### Sequence of Operations

```python
def edit_distance_with_operations(word1: str, word2: str) -> Tuple[int, List[str]]:
    """
    LeetCode 72 - Edit Distance (with operations)
    
    Returns: (distance, list of operations)
    
    Operations: "INSERT x", "DELETE x", "REPLACE x with y", "KEEP x"
    """
    m, n = len(word1), len(word2)
    
    # DP table
    dp = [[0] * (n + 1) for _ in range(m + 1)]
    
    for i in range(m + 1):
        dp[i][0] = i
    for j in range(n + 1):
        dp[0][j] = j
    
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if word1[i - 1] == word2[j - 1]:
                dp[i][j] = dp[i - 1][j - 1]
            else:
                dp[i][j] = 1 + min(
                    dp[i - 1][j],      # Delete
                    dp[i][j - 1],      # Insert
                    dp[i - 1][j - 1]   # Replace
                )
    
    # Backtrack
    operations = []
    i, j = m, n
    
    while i > 0 or j > 0:
        if i > 0 and j > 0 and word1[i - 1] == word2[j - 1]:
            operations.append(f"KEEP '{word1[i - 1]}'")
            i -= 1
            j -= 1
        elif j > 0 and (i == 0 or dp[i][j] == dp[i][j - 1] + 1):
            operations.append(f"INSERT '{word2[j - 1]}'")
            j -= 1
        elif i > 0 and (j == 0 or dp[i][j] == dp[i - 1][j] + 1):
            operations.append(f"DELETE '{word1[i - 1]}'")
            i -= 1
        else:
            operations.append(f"REPLACE '{word1[i - 1]}' with '{word2[j - 1]}'")
            i -= 1
            j -= 1
    
    return dp[m][n], operations[::-1]


# Test
dist, ops = edit_distance_with_operations("horse", "ros")
print(f"Distance: {dist}")
print("Operations:", ops)
```

---

## 6. Path Reconstruction in Grid DP

### Grid Shortest Path

```python
def min_path_sum_with_path(grid: List[List[int]]) -> Tuple[int, List[Tuple[int, int]]]:
    """
    LeetCode 64 - Minimum Path Sum (with path)
    
    Returns: (min_sum, path as list of coordinates)
    """
    m, n = len(grid), len(grid[0])
    
    # DP
    dp = [[0] * n for _ in range(m)]
    dp[0][0] = grid[0][0]
    
    for i in range(1, m):
        dp[i][0] = dp[i - 1][0] + grid[i][0]
    for j in range(1, n):
        dp[0][j] = dp[0][j - 1] + grid[0][j]
    
    for i in range(1, m):
        for j in range(1, n):
            dp[i][j] = grid[i][j] + min(dp[i - 1][j], dp[i][j - 1])
    
    # Backtrack
    path = [(m - 1, n - 1)]
    i, j = m - 1, n - 1
    
    while i > 0 or j > 0:
        if i == 0:
            j -= 1
        elif j == 0:
            i -= 1
        elif dp[i - 1][j] < dp[i][j - 1]:
            i -= 1
        else:
            j -= 1
        path.append((i, j))
    
    return dp[m - 1][n - 1], path[::-1]


def all_paths_with_target_sum(grid: List[List[int]], target: int) -> List[List[Tuple[int, int]]]:
    """
    Find all paths from top-left to bottom-right with given sum.
    
    Uses DFS with path tracking.
    """
    m, n = len(grid), len(grid[0])
    result = []
    
    def dfs(i: int, j: int, current_sum: int, path: List[Tuple[int, int]]):
        if i == m - 1 and j == n - 1:
            if current_sum + grid[i][j] == target:
                result.append(path + [(i, j)])
            return
        
        current_sum += grid[i][j]
        path = path + [(i, j)]
        
        if i + 1 < m:
            dfs(i + 1, j, current_sum, path)
        if j + 1 < n:
            dfs(i, j + 1, current_sum, path)
    
    dfs(0, 0, 0, [])
    return result
```

---

## 7. Word Break Reconstruction

### All Segmentations

```python
def word_break_all(s: str, wordDict: List[str]) -> List[str]:
    """
    LeetCode 140 - Word Break II
    
    Return all valid segmentations.
    
    Time: O(2^n) worst case, but pruned
    """
    word_set = set(wordDict)
    
    # First check if any segmentation exists
    n = len(s)
    dp = [False] * (n + 1)
    dp[0] = True
    
    for i in range(1, n + 1):
        for j in range(i):
            if dp[j] and s[j:i] in word_set:
                dp[i] = True
                break
    
    if not dp[n]:
        return []
    
    # DFS with memoization for all segmentations
    from functools import lru_cache
    
    @lru_cache(maxsize=None)
    def backtrack(start: int) -> List[str]:
        if start == n:
            return [""]
        
        result = []
        for end in range(start + 1, n + 1):
            word = s[start:end]
            if word in word_set:
                for rest in backtrack(end):
                    if rest:
                        result.append(word + " " + rest)
                    else:
                        result.append(word)
        
        return result
    
    return backtrack(0)


# Test
print(word_break_all("catsanddog", ["cat", "cats", "and", "sand", "dog"]))
# ["cats and dog", "cat sand dog"]
```

---

## 8. Matrix Chain Reconstruction

### Optimal Parenthesization

```python
def matrix_chain_order(dims: List[int]) -> Tuple[int, str]:
    """
    Matrix Chain Multiplication with optimal order.
    
    dims = [d0, d1, d2, ..., dn] for n matrices
    Matrix i has dimensions dims[i-1] × dims[i]
    
    Returns: (min_cost, parenthesization string)
    """
    n = len(dims) - 1  # Number of matrices
    
    # dp[i][j] = min cost to multiply matrices i..j
    dp = [[0] * n for _ in range(n)]
    # split[i][j] = optimal split point
    split = [[0] * n for _ in range(n)]
    
    for length in range(2, n + 1):
        for i in range(n - length + 1):
            j = i + length - 1
            dp[i][j] = float('inf')
            
            for k in range(i, j):
                cost = dp[i][k] + dp[k + 1][j] + dims[i] * dims[k + 1] * dims[j + 1]
                if cost < dp[i][j]:
                    dp[i][j] = cost
                    split[i][j] = k
    
    # Reconstruct parenthesization
    def build_string(i: int, j: int) -> str:
        if i == j:
            return f"M{i + 1}"
        
        k = split[i][j]
        left = build_string(i, k)
        right = build_string(k + 1, j)
        return f"({left} × {right})"
    
    return dp[0][n - 1], build_string(0, n - 1)


# Test
# Matrices: 10×30, 30×5, 5×60
dims = [10, 30, 5, 60]
cost, order = matrix_chain_order(dims)
print(f"Cost: {cost}, Order: {order}")
```

---

## 9. Practice Problems

### LeetCode Problems

| # | Problem | Reconstruction Type | Difficulty |
|---|---------|---------------------|------------|
| 72 | [Edit Distance](https://leetcode.com/problems/edit-distance/) | Operations | Hard |
| 78 | [Subsets](https://leetcode.com/problems/subsets/) | All subsets | Medium |
| 300 | [Longest Increasing Subsequence](https://leetcode.com/problems/longest-increasing-subsequence/) | Sequence | Medium |
| 416 | [Partition Equal Subset Sum](https://leetcode.com/problems/partition-equal-subset-sum/) | Items | Medium |
| 472 | [Concatenated Words](https://leetcode.com/problems/concatenated-words/) | Words | Hard |
| 518 | [Coin Change II](https://leetcode.com/problems/coin-change-ii/) | Count (no reconstruction) | Medium |
| 1092 | [Shortest Common Supersequence](https://leetcode.com/problems/shortest-common-supersequence/) | String | Hard |
| 1143 | [Longest Common Subsequence](https://leetcode.com/problems/longest-common-subsequence/) | Sequence | Medium |
| 140 | [Word Break II](https://leetcode.com/problems/word-break-ii/) | All segmentations | Hard |
| 126 | [Word Ladder II](https://leetcode.com/problems/word-ladder-ii/) | All paths | Hard |

---

## 10. Key Patterns Summary

```
Reconstruction Approaches:

1. Parent Pointer Method:
   - Store parent[i] = previous state that led to i
   - Backtrack from final state following pointers
   - Space: O(n) additional
   
2. Backtracking from DP Table:
   - Compute full DP table
   - Trace back by comparing adjacent values
   - No additional space needed
   
3. DFS with Pruning:
   - First compute DP to know if solution exists
   - Then DFS only valid paths
   - For "all solutions" problems

Reconstruction Direction:
- Most problems: backtrack from end to start
- Then reverse the result
- Some problems: forward tracking with path list

Common Pitfalls:
- Off-by-one errors in indices
- Forgetting to reverse final result
- Not handling ties (multiple optimal paths)
- Memory issues for "all paths" problems
```

---

## 11. References

1. CLRS - Chapter 15: Dynamic Programming (Reconstruction sections)
2. CP-Algorithms: DP Optimization and Reconstruction
3. LeetCode Discussion: Path Recovery in DP
4. Competitive Programming 3 - DP Reconstruction Techniques
