---
title: Advanced DP Optimizations
topic: Algorithms - Dynamic Programming
difficulty: Hard
tags: [dp, optimization, smawk, bitset, segment-tree, bit, memoization]
status: complete
weight: 21
---

# Advanced DP Optimizations

## 1. Overview

This document covers advanced optimization techniques beyond the basics in `09_DP-Optimizations.md`:
- SMAWK algorithm for monotone matrix problems
- Bitset/word-parallel DP speedups
- DP + Segment Tree/BIT combinations
- Memoization strategies and compression tricks

---

## 2. SMAWK Algorithm

### When to Use
- Finding row minima in totally monotone matrix
- DP where optimal transition point is monotonic across rows
- Reduces O(nm) to O(n + m)

### Concept

```
A matrix A is totally monotone if:
For any i1 < i2 and j1 < j2:
  A[i1][j1] > A[i1][j2] implies A[i2][j1] > A[i2][j2]

This means column minima positions increase as we go down rows.
```

### Implementation

```python
from typing import List, Callable

def smawk(row_indices: List[int], col_indices: List[int], 
          lookup: Callable[[int, int], int]) -> List[int]:
    """
    SMAWK algorithm for finding row minima in totally monotone matrix.
    
    Args:
        row_indices: Rows to find minima for
        col_indices: Columns to search
        lookup: Function lookup(i, j) returns matrix value at (i, j)
    
    Returns:
        result[i] = column index of minimum in row row_indices[i]
    
    Time: O(n + m)
    """
    # REDUCE: Remove columns that can never be optimal
    def reduce(rows: List[int], cols: List[int]) -> List[int]:
        new_cols = []
        for c in cols:
            while new_cols:
                r = rows[len(new_cols) - 1]
                if lookup(r, new_cols[-1]) < lookup(r, c):
                    break
                new_cols.pop()
            if len(new_cols) < len(rows):
                new_cols.append(c)
        return new_cols
    
    # Base case
    if len(row_indices) == 0:
        return []
    
    # Reduce columns
    cols = reduce(row_indices, col_indices)
    
    # Recursively solve for odd rows
    odd_rows = row_indices[1::2]
    if odd_rows:
        sub_result = smawk(odd_rows, cols, lookup)
        odd_minima = {odd_rows[i]: sub_result[i] for i in range(len(odd_rows))}
    else:
        odd_minima = {}
    
    # Fill in even rows using constraint from odd rows
    result = []
    col_idx = 0
    
    for i, row in enumerate(row_indices):
        if i % 2 == 1:
            # Odd row - already computed
            result.append(odd_minima[row])
        else:
            # Even row - search between bounds
            # Lower bound: start of cols
            # Upper bound: minimum column of next odd row (if exists)
            if i + 1 < len(row_indices):
                upper = odd_minima[row_indices[i + 1]]
            else:
                upper = cols[-1]
            
            # Find minimum in [col_idx position, upper position]
            best_col = cols[col_idx]
            best_val = lookup(row, best_col)
            
            while col_idx < len(cols) - 1 and cols[col_idx] < upper:
                col_idx += 1
                val = lookup(row, cols[col_idx])
                if val < best_val:
                    best_val = val
                    best_col = cols[col_idx]
            
            result.append(best_col)
    
    return result


def dp_with_smawk(costs: List[int], k: int) -> int:
    """
    Partition array into k segments minimizing cost.
    Cost(i, j) = sum(costs[i:j+1])² (quadratic cost satisfies monotonicity)
    
    Time: O(nk) instead of O(n²k)
    """
    n = len(costs)
    prefix = [0] * (n + 1)
    for i in range(n):
        prefix[i + 1] = prefix[i] + costs[i]
    
    def segment_cost(l: int, r: int) -> int:
        s = prefix[r + 1] - prefix[l]
        return s * s
    
    INF = float('inf')
    dp = [[INF] * (n + 1) for _ in range(k + 1)]
    dp[0][0] = 0
    
    for layer in range(1, k + 1):
        # Define lookup for current layer
        def lookup(i: int, j: int) -> int:
            if j >= i or dp[layer - 1][j] == INF:
                return INF
            return dp[layer - 1][j] + segment_cost(j, i - 1)
        
        # Use SMAWK to find optimal transitions
        rows = list(range(layer, n + 1))
        cols = list(range(n + 1))
        minima = smawk(rows, cols, lookup)
        
        for idx, row in enumerate(rows):
            best_col = minima[idx]
            dp[layer][row] = lookup(row, best_col)
    
    return dp[k][n]
```

---

## 3. Bitset DP Speedup

### Concept
Using bitwise operations to parallelize DP transitions. Works when state can be represented as bits and transitions can be expressed as bit operations.

### Subset Sum with Bitset

```python
def subset_sum_bitset(nums: List[int], target: int) -> bool:
    """
    Check if any subset sums to target.
    
    Standard DP: O(n × target)
    With bitset: O(n × target / 64) due to word-level parallelism
    
    In Python, we use integers as arbitrary-length bitsets.
    """
    # dp is a bitmask where bit i is set if sum i is achievable
    dp = 1  # Bit 0 set (empty subset has sum 0)
    
    for num in nums:
        # Shift dp by num and OR with original
        # This sets bit (i + num) for each bit i that was set
        dp |= (dp << num)
    
    return bool(dp & (1 << target))


def count_subset_sums_bitset(nums: List[int], target: int) -> int:
    """
    Count subsets with given sum.
    
    Uses array of counts but with bitset optimization for achievability check.
    """
    max_sum = sum(nums)
    dp = [0] * (max_sum + 1)
    dp[0] = 1
    
    for num in nums:
        # Process in reverse to avoid using same element twice
        for s in range(max_sum, num - 1, -1):
            dp[s] += dp[s - num]
    
    return dp[target] if target <= max_sum else 0


def knapsack_bitset(weights: List[int], capacity: int) -> int:
    """
    Maximum items that fit in knapsack (unweighted version).
    
    Time: O(n × capacity / 64)
    """
    # dp = achievable weights as bitmask
    dp = 1  # Weight 0 is achievable
    
    for w in weights:
        if w <= capacity:
            # Can add this item to any achievable weight
            dp |= (dp << w)
            # Mask to capacity
            dp &= (1 << (capacity + 1)) - 1
    
    # Find maximum achievable weight
    result = 0
    temp = dp
    bit = 0
    while temp:
        if temp & 1:
            result = bit
        temp >>= 1
        bit += 1
    
    return result
```

### LCS with Bitset

```python
def lcs_bitset(s1: str, s2: str) -> int:
    """
    LCS using bit-parallel algorithm.
    
    Standard: O(mn)
    Bit-parallel: O(mn / 64)
    
    Based on Crochemore et al. algorithm.
    """
    if len(s1) > len(s2):
        s1, s2 = s2, s1
    
    m, n = len(s1), len(s2)
    
    # Precompute character masks for s1
    # pattern_mask[c] has bit i set if s1[i] == c
    from collections import defaultdict
    pattern_mask = defaultdict(int)
    for i, c in enumerate(s1):
        pattern_mask[c] |= (1 << i)
    
    # DP using bit manipulation
    # Column j of DP table represented as bits
    dp = 0
    
    for c in s2:
        match = pattern_mask[c]
        # Bit manipulation to compute next column
        # This is the core of the algorithm
        x = dp | match
        dp = x & ((x - (dp << 1 | 1)) ^ x)
    
    return bin(dp).count('1')
```

---

## 4. DP with Segment Tree

### Range Minimum/Maximum Queries in DP

```python
class SegmentTree:
    """
    Segment Tree for range minimum queries with point updates.
    """
    def __init__(self, n: int, identity=float('inf'), op=min):
        self.n = n
        self.identity = identity
        self.op = op
        self.tree = [identity] * (2 * n)
    
    def update(self, i: int, val):
        i += self.n
        self.tree[i] = val
        while i > 1:
            i //= 2
            self.tree[i] = self.op(self.tree[2 * i], self.tree[2 * i + 1])
    
    def query(self, l: int, r: int):
        """Query [l, r)"""
        l += self.n
        r += self.n
        result = self.identity
        while l < r:
            if l & 1:
                result = self.op(result, self.tree[l])
                l += 1
            if r & 1:
                r -= 1
                result = self.op(result, self.tree[r])
            l //= 2
            r //= 2
        return result


def lis_with_segtree(nums: List[int]) -> int:
    """
    LIS using segment tree for O(n log n).
    
    dp[v] = max LIS length ending with value v
    For each num, query max in [0, num-1] and update dp[num]
    
    Time: O(n log M) where M is value range
    """
    if not nums:
        return 0
    
    # Coordinate compression
    sorted_unique = sorted(set(nums))
    val_to_idx = {v: i for i, v in enumerate(sorted_unique)}
    m = len(sorted_unique)
    
    # Segment tree for max query
    st = SegmentTree(m, identity=0, op=max)
    
    max_len = 0
    for num in nums:
        idx = val_to_idx[num]
        # Query max LIS length for values < num
        if idx > 0:
            best = st.query(0, idx)
        else:
            best = 0
        
        # Update current value's LIS length
        new_len = best + 1
        st.update(idx, new_len)
        max_len = max(max_len, new_len)
    
    return max_len


def longest_increasing_path_segtree(matrix: List[List[int]]) -> int:
    """
    LeetCode 329 variant using segment tree.
    
    Process cells in order of value, query/update based on neighbors.
    """
    if not matrix or not matrix[0]:
        return 0
    
    m, n = len(matrix), len(matrix[0])
    
    # Sort cells by value
    cells = [(matrix[i][j], i, j) for i in range(m) for j in range(n)]
    cells.sort()
    
    # dp[i][j] = LIS ending at (i, j)
    dp = [[0] * n for _ in range(m)]
    max_len = 0
    
    # Process in increasing value order
    i = 0
    while i < len(cells):
        # Group cells with same value
        j = i
        while j < len(cells) and cells[j][0] == cells[i][0]:
            j += 1
        
        # Query neighbors for each cell in group
        updates = []
        for k in range(i, j):
            val, r, c = cells[k]
            best = 0
            for dr, dc in [(-1, 0), (1, 0), (0, -1), (0, 1)]:
                nr, nc = r + dr, c + dc
                if 0 <= nr < m and 0 <= nc < n and matrix[nr][nc] < val:
                    best = max(best, dp[nr][nc])
            updates.append((r, c, best + 1))
        
        # Apply updates
        for r, c, length in updates:
            dp[r][c] = length
            max_len = max(max_len, length)
        
        i = j
    
    return max_len
```

---

## 5. DP with Binary Indexed Tree (BIT)

```python
class BIT:
    """
    Binary Indexed Tree for prefix maximum queries.
    """
    def __init__(self, n: int):
        self.n = n
        self.tree = [0] * (n + 1)
    
    def update(self, i: int, val: int):
        """Update index i with max(current, val)."""
        i += 1  # 1-indexed
        while i <= self.n:
            self.tree[i] = max(self.tree[i], val)
            i += i & (-i)
    
    def query(self, i: int) -> int:
        """Query max in prefix [0, i]."""
        i += 1  # 1-indexed
        result = 0
        while i > 0:
            result = max(result, self.tree[i])
            i -= i & (-i)
        return result


def lis_with_bit(nums: List[int]) -> int:
    """
    LIS using BIT for O(n log n).
    """
    if not nums:
        return 0
    
    # Coordinate compression
    sorted_unique = sorted(set(nums))
    val_to_idx = {v: i for i, v in enumerate(sorted_unique)}
    m = len(sorted_unique)
    
    bit = BIT(m)
    
    max_len = 0
    for num in nums:
        idx = val_to_idx[num]
        # Query max LIS length for values < num
        best = bit.query(idx - 1) if idx > 0 else 0
        new_len = best + 1
        bit.update(idx, new_len)
        max_len = max(max_len, new_len)
    
    return max_len


def count_inversions_bit(nums: List[int]) -> int:
    """
    Count inversions using BIT.
    
    Inversion: i < j but nums[i] > nums[j]
    
    Time: O(n log n)
    """
    # Coordinate compression
    sorted_unique = sorted(set(nums))
    val_to_idx = {v: i for i, v in enumerate(sorted_unique)}
    m = len(sorted_unique)
    
    # BIT for counting elements seen
    tree = [0] * (m + 1)
    
    def update(i: int):
        i += 1
        while i <= m:
            tree[i] += 1
            i += i & (-i)
    
    def query(i: int) -> int:
        i += 1
        result = 0
        while i > 0:
            result += tree[i]
            i -= i & (-i)
        return result
    
    inversions = 0
    for i, num in enumerate(nums):
        idx = val_to_idx[num]
        # Count elements greater than num seen so far
        total_seen = i
        smaller_or_equal = query(idx)
        greater = total_seen - smaller_or_equal
        inversions += greater
        update(idx)
    
    return inversions
```

---

## 6. Memoization and Compression Tricks

### State Compression

```python
def compress_states(states: List[tuple]) -> tuple:
    """
    Compress arbitrary states to consecutive integers.
    
    Returns: (state_to_id, id_to_state)
    """
    state_to_id = {}
    id_to_state = []
    
    for state in states:
        if state not in state_to_id:
            state_to_id[state] = len(id_to_state)
            id_to_state.append(state)
    
    return state_to_id, id_to_state


def dp_with_rolling_array(nums: List[int]) -> int:
    """
    Space optimization: keep only necessary rows.
    
    Example: LCS with O(min(m, n)) space instead of O(mn)
    """
    # When dp[i] only depends on dp[i-1], use two arrays
    # When dp[i][j] depends on dp[i-1][j-1], dp[i-1][j], dp[i][j-1],
    # process carefully in right order
    pass


def memoization_with_hashable_state():
    """
    For complex states, use tuples or frozen structures.
    """
    from functools import lru_cache
    
    @lru_cache(maxsize=None)
    def dp(i: int, j: int, state: tuple) -> int:
        # state must be hashable (tuple, frozenset, etc.)
        pass
    
    # For mutable states, convert to tuple before caching
    def dp_wrapper(i: int, j: int, state: list) -> int:
        return dp(i, j, tuple(state))


def sparse_dp(n: int, transitions: List[tuple]) -> dict:
    """
    For sparse state spaces, use dictionary instead of array.
    
    Useful when most states are unreachable.
    """
    dp = {0: 0}  # Initial state
    
    for iteration in range(n):
        new_dp = {}
        for state, value in dp.items():
            for delta, cost in transitions:
                new_state = state + delta
                if new_state not in new_dp or new_dp[new_state] > value + cost:
                    new_dp[new_state] = value + cost
        dp = new_dp
    
    return dp
```

### Memory-Efficient Techniques

```python
def dp_with_generators(n: int):
    """
    Use generators to avoid storing all intermediate results.
    """
    def compute_row(prev_row, i):
        # Compute current row from previous
        yield from (
            prev_row[j] + some_cost(i, j)
            for j in range(len(prev_row))
        )
    
    row = [initial_values]
    for i in range(n):
        row = list(compute_row(row, i))
    
    return row


def dp_with_file_backed_storage(n: int, m: int):
    """
    For very large DP tables, use file-backed storage.
    
    Memory-mapped files allow access like arrays but stored on disk.
    """
    import mmap
    import os
    
    # Create memory-mapped file
    filename = '/tmp/dp_table.bin'
    size = n * m * 4  # 4 bytes per int
    
    with open(filename, 'wb') as f:
        f.write(b'\x00' * size)
    
    with open(filename, 'r+b') as f:
        mm = mmap.mmap(f.fileno(), size)
        
        # Access like array (need to pack/unpack)
        import struct
        
        def get(i, j):
            offset = (i * m + j) * 4
            return struct.unpack('i', mm[offset:offset+4])[0]
        
        def set(i, j, val):
            offset = (i * m + j) * 4
            mm[offset:offset+4] = struct.pack('i', val)
        
        # Use get/set for DP computation
        mm.close()
    
    os.remove(filename)
```

---

## 7. Practice Problems

### Advanced Optimization Problems

| Problem | Technique | Source | Difficulty |
|---------|-----------|--------|------------|
| Knuth-Morris-Pratt | Bitset | Classic | Hard |
| LCS | Bit-parallel | Research | Hard |
| Matrix Path | Segment Tree | CSES | Hard |
| Range LIS | BIT | AtCoder | Hard |
| Convex DP | SMAWK | CF | Very Hard |
| Alien DP | WQS Binary Search | IOI | Very Hard |

### LeetCode Problems

| # | Problem | Optimization | Difficulty |
|---|---------|--------------|------------|
| 300 | [LIS](https://leetcode.com/problems/longest-increasing-subsequence/) | Binary search/BIT | Medium |
| 329 | [Longest Increasing Path](https://leetcode.com/problems/longest-increasing-path-in-matrix/) | Topological + Memo | Hard |
| 354 | [Russian Doll Envelopes](https://leetcode.com/problems/russian-doll-envelopes/) | LIS + Binary Search | Hard |
| 673 | [Number of LIS](https://leetcode.com/problems/number-of-longest-increasing-subsequence/) | BIT/Segment Tree | Medium |
| 1235 | [Max Profit Job Scheduling](https://leetcode.com/problems/maximum-profit-in-job-scheduling/) | Binary search + DP | Hard |

---

## 8. Key Patterns Summary

```
Advanced Optimization Decision Tree:

1. Is there a monotone matrix structure?
   → SMAWK: O(n + m) row minima
   
2. Can states be represented as bits?
   → Bitset: O(n/64) speedup
   
3. Need range queries in DP transitions?
   → Segment Tree: O(log n) per query
   → BIT: O(log n) per query (simpler, prefix only)
   
4. Very large state space?
   → Dictionary for sparse states
   → State compression for coordinate mapping
   
5. Memory issues?
   → Rolling array (keep only needed rows)
   → Generator-based computation
   → File-backed storage for massive problems

When to Use Each:
- SMAWK: n, m > 10^5 with quadrangle inequality
- Bitset: Subset/string problems, n × m > 10^7
- Segment Tree: Multiple DP dimensions with range queries
- BIT: Single dimension prefix queries
- Sparse DP: When < 10% of states are reachable
```

---

## 9. References

1. "SMAWK Algorithm" - Aggarwal et al.
2. "Bit-Parallel LCS" - Crochemore et al.
3. CP-Algorithms: Segment Tree, BIT
4. Codeforces: DP Optimization Contest Editorials
5. "Introduction to Algorithms" - DP Chapter
