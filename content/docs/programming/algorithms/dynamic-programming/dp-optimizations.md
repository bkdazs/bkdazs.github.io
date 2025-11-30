---
title: Dynamic Programming Optimizations
topic: Algorithms - Dynamic Programming
difficulty: Hard
tags: [dp, optimization, monotonic-queue, divide-conquer, convex-hull-trick, knuth]
status: complete
weight: 9
---

# DP Optimization Techniques

## 1. Summary of Optimization Techniques

| Technique | Reduces From | Reduces To | Condition |
|-----------|--------------|------------|-----------|
| Monotonic Queue | O(n²) | O(n) | Sliding window constraint |
| Divide & Conquer | O(n²) | O(n log n) | Quadrangle inequality |
| Knuth's | O(n³) | O(n²) | Optimal partition monotonicity |
| Convex Hull Trick | O(n²) | O(n) or O(n log n) | Linear function optimization |

---

## 2. Monotonic Queue / Deque Optimization

### When to Use
- DP recurrence: `dp[i] = min/max(dp[j] + cost(j, i))` for `j` in a sliding window `[i-k, i-1]`
- The window constraint allows deque optimization

### Classic Problem: Sliding Window Maximum

```python
from typing import List
from collections import deque

def max_sliding_window(nums: List[int], k: int) -> List[int]:
    """
    LeetCode 239 - Sliding Window Maximum
    
    Time: O(n) - each element added/removed from deque once
    Space: O(k)
    """
    result = []
    dq = deque()  # Store indices, values are decreasing
    
    for i in range(len(nums)):
        # Remove elements outside window
        while dq and dq[0] < i - k + 1:
            dq.popleft()
        
        # Remove smaller elements (they'll never be max)
        while dq and nums[dq[-1]] < nums[i]:
            dq.pop()
        
        dq.append(i)
        
        # Window is full, record max
        if i >= k - 1:
            result.append(nums[dq[0]])
    
    return result


# Test
nums = [1, 3, -1, -3, 5, 3, 6, 7]
k = 3
print(max_sliding_window(nums, k))  # [3, 3, 5, 5, 6, 7]
```

### DP with Monotonic Queue: Constrained Subsequence Sum

```python
def constrained_subsequence_sum(nums: List[int], k: int) -> int:
    """
    LeetCode 1425 - Constrained Subsequence Sum
    
    dp[i] = max sum ending at i, with gap ≤ k
    dp[i] = nums[i] + max(0, max(dp[j]) for j in [i-k, i-1])
    
    Time: O(n) with monotonic deque
    """
    n = len(nums)
    dp = [0] * n
    dq = deque()  # Store indices with decreasing dp values
    
    for i in range(n):
        # Remove indices outside window
        while dq and dq[0] < i - k:
            dq.popleft()
        
        # dp[i] = nums[i] + max(0, dp[dq[0]])
        dp[i] = nums[i] + (max(0, dp[dq[0]]) if dq else 0)
        
        # Maintain monotonic decreasing deque
        while dq and dp[dq[-1]] <= dp[i]:
            dq.pop()
        
        dq.append(i)
    
    return max(dp)


# Test
nums = [10, 2, -10, 5, 20]
k = 2
print(constrained_subsequence_sum(nums, k))  # 37 (10 + 2 + 5 + 20)
```

### Jump Game with Cost

```python
def min_cost_jump(cost: List[int], k: int) -> int:
    """
    Minimum cost to reach end, can jump at most k steps.
    
    dp[i] = cost[i] + min(dp[j]) for j in [i-k, i-1]
    
    Time: O(n)
    """
    n = len(cost)
    dp = [float('inf')] * n
    dp[0] = cost[0]
    
    dq = deque([0])  # Monotonic increasing dp values
    
    for i in range(1, n):
        # Remove out of window
        while dq and dq[0] < i - k:
            dq.popleft()
        
        dp[i] = cost[i] + dp[dq[0]]
        
        # Maintain monotonic increasing
        while dq and dp[dq[-1]] >= dp[i]:
            dq.pop()
        
        dq.append(i)
    
    return dp[n - 1]
```

---

## 3. Divide and Conquer DP Optimization

### When to Use
- DP recurrence: `dp[i][j] = min(dp[i-1][k] + cost(k, j))` for `k < j`
- The optimal `k` for `dp[i][j]` is monotonic: `opt[i][j] ≤ opt[i][j+1]`
- Called **Quadrangle Inequality**: `cost(a, c) + cost(b, d) ≤ cost(a, d) + cost(b, c)` for `a ≤ b ≤ c ≤ d`

### Template

```python
def dp_divide_conquer(n: int, m: int, cost) -> int:
    """
    Divide and Conquer DP Optimization.
    
    dp[i][j] = min over k of (dp[i-1][k] + cost(k+1, j))
    
    Time: O(nm log n) instead of O(nm²)
    
    Args:
        n: Number of positions
        m: Number of groups
        cost: cost(l, r) function
    """
    INF = float('inf')
    dp = [[INF] * (n + 1) for _ in range(m + 1)]
    dp[0][0] = 0
    
    def solve(layer: int, lo: int, hi: int, opt_lo: int, opt_hi: int):
        """
        Compute dp[layer][lo..hi] with optimal k in [opt_lo, opt_hi].
        """
        if lo > hi:
            return
        
        mid = (lo + hi) // 2
        best_k = opt_lo
        best_val = INF
        
        # Find optimal k for dp[layer][mid]
        for k in range(opt_lo, min(mid, opt_hi + 1)):
            val = dp[layer - 1][k] + cost(k + 1, mid)
            if val < best_val:
                best_val = val
                best_k = k
        
        dp[layer][mid] = best_val
        
        # Recurse with restricted optimal ranges
        solve(layer, lo, mid - 1, opt_lo, best_k)
        solve(layer, mid + 1, hi, best_k, opt_hi)
    
    for i in range(1, m + 1):
        solve(i, 1, n, 0, n - 1)
    
    return dp[m][n]


# Example: Split array into m groups minimizing max sum
def split_array(nums: List[int], m: int) -> int:
    """
    LeetCode 410 - Split Array Largest Sum (can also use binary search)
    
    Here we show D&C DP approach.
    """
    n = len(nums)
    prefix = [0] * (n + 1)
    for i in range(n):
        prefix[i + 1] = prefix[i] + nums[i]
    
    def cost(l: int, r: int) -> int:
        return prefix[r] - prefix[l - 1]
    
    # For this problem, we need max, not sum
    # Standard D&C DP works for sum cost
    # For max-sum problem, binary search is simpler
    pass
```

### CSES: Movie Festival II (Interval Scheduling)

```python
def max_non_overlapping_intervals_dc(intervals: List[tuple], k: int) -> int:
    """
    Select at most k non-overlapping intervals with maximum count.
    
    Uses D&C DP optimization.
    """
    # Sort by end time
    intervals.sort(key=lambda x: x[1])
    n = len(intervals)
    
    # Precompute: for each interval, last non-overlapping interval
    import bisect
    ends = [iv[1] for iv in intervals]
    prev = []
    for i, (start, end) in enumerate(intervals):
        p = bisect.bisect_right(ends, start) - 1
        prev.append(p)
    
    # dp[i][j] = max intervals using first j intervals with at most i groups
    # D&C optimization applies if we reformulate properly
    pass
```

---

## 4. Knuth's Optimization

### When to Use
- DP recurrence: `dp[i][j] = min(dp[i][k] + dp[k+1][j]) + cost(i, j)` for `i ≤ k < j`
- Optimal split point is monotonic: `opt[i][j-1] ≤ opt[i][j] ≤ opt[i+1][j]`
- Applicable to: Optimal BST, Matrix Chain, some interval DPs

### Template

```python
def knuth_optimization(n: int, cost) -> int:
    """
    Knuth's Optimization for interval DP.
    
    dp[i][j] = min over k of (dp[i][k] + dp[k+1][j]) + cost(i, j)
    
    Time: O(n²) instead of O(n³)
    
    Condition: opt[i][j-1] ≤ opt[i][j] ≤ opt[i+1][j]
    """
    INF = float('inf')
    dp = [[0] * n for _ in range(n)]
    opt = [[0] * n for _ in range(n)]
    
    # Initialize: single elements
    for i in range(n):
        opt[i][i] = i
    
    # Fill by increasing length
    for length in range(2, n + 1):
        for i in range(n - length + 1):
            j = i + length - 1
            dp[i][j] = INF
            
            # Search only in [opt[i][j-1], opt[i+1][j]]
            lo = opt[i][j - 1] if j > 0 else i
            hi = opt[i + 1][j] if i + 1 < n else j - 1
            
            for k in range(lo, min(hi + 1, j)):
                val = dp[i][k] + dp[k + 1][j] + cost(i, j)
                if val < dp[i][j]:
                    dp[i][j] = val
                    opt[i][j] = k
    
    return dp[0][n - 1]


# Example: Optimal Binary Search Tree
def optimal_bst(keys: List[int], freq: List[int]) -> int:
    """
    Build BST minimizing weighted search cost.
    
    dp[i][j] = min cost for keys[i..j]
    """
    n = len(keys)
    
    # Prefix sum of frequencies
    prefix = [0] * (n + 1)
    for i in range(n):
        prefix[i + 1] = prefix[i] + freq[i]
    
    def cost(i: int, j: int) -> int:
        return prefix[j + 1] - prefix[i]
    
    return knuth_optimization(n, cost)
```

---

## 5. Convex Hull Trick (CHT)

### When to Use
- DP recurrence: `dp[i] = min(dp[j] + b[j] * a[i])` for `j < i`
- We're optimizing over linear functions: `f_j(x) = b[j] * x + dp[j]`
- Query value at `x = a[i]`

### Case 1: Slopes are Monotonic (Stack-based)

```python
def convex_hull_trick_monotonic(a: List[int], b: List[int]) -> int:
    """
    CHT when slopes b[j] are monotonically decreasing (for min).
    
    dp[i] = min(dp[j] + b[j] * a[i]) for j < i
    
    Time: O(n)
    """
    n = len(a)
    dp = [0] * n
    
    # Stack of (slope, intercept) pairs
    # Represents lower envelope of lines
    hull = []  # [(m, c)] where line is y = mx + c
    
    def bad(l1, l2, l3):
        """Check if l2 is dominated by l1 and l3."""
        # l2 is useless if intersection of l1,l3 is left of l1,l2
        m1, c1 = l1
        m2, c2 = l2
        m3, c3 = l3
        # Avoid division: (c3-c1)/(m1-m3) <= (c2-c1)/(m1-m2)
        return (c3 - c1) * (m1 - m2) <= (c2 - c1) * (m1 - m3)
    
    ptr = 0
    
    for i in range(n):
        # Query: find best line for x = a[i]
        if hull:
            while ptr < len(hull) - 1:
                m1, c1 = hull[ptr]
                m2, c2 = hull[ptr + 1]
                if m1 * a[i] + c1 > m2 * a[i] + c2:
                    ptr += 1
                else:
                    break
            
            m, c = hull[ptr]
            dp[i] = m * a[i] + c
        
        # Insert new line (b[i], dp[i])
        new_line = (b[i], dp[i])
        while len(hull) >= 2 and bad(hull[-2], hull[-1], new_line):
            hull.pop()
        hull.append(new_line)
    
    return dp[n - 1]
```

### Case 2: General (Li Chao Tree)

```python
class LiChaoTree:
    """
    Li Chao Tree for dynamic convex hull trick.
    
    Supports:
        - Add line y = mx + c
        - Query minimum y at x
    
    Time: O(log MAXVAL) per operation
    """
    
    def __init__(self, lo: int, hi: int):
        self.lo = lo
        self.hi = hi
        self.tree = {}  # node_id -> (m, c)
        self.INF = float('inf')
    
    def _add(self, node: int, lo: int, hi: int, m: int, c: int):
        """Add line y = mx + c to subtree."""
        if lo > hi:
            return
        
        mid = (lo + hi) // 2
        
        if node not in self.tree:
            self.tree[node] = (m, c)
            return
        
        om, oc = self.tree[node]  # Old line
        
        # Evaluate at midpoint
        new_mid = m * mid + c
        old_mid = om * mid + oc
        
        # Compare at endpoints
        new_lo = m * lo + c
        old_lo = om * lo + oc
        
        if new_mid < old_mid:
            # New line is better at mid, swap
            self.tree[node] = (m, c)
            m, c = om, oc
        
        # Decide which subtree might have the old line as winner
        if new_lo < old_lo:
            self._add(2 * node, lo, mid, m, c)
        else:
            self._add(2 * node + 1, mid + 1, hi, m, c)
    
    def add(self, m: int, c: int):
        """Add line y = mx + c."""
        self._add(1, self.lo, self.hi, m, c)
    
    def _query(self, node: int, lo: int, hi: int, x: int) -> int:
        """Query minimum y at x."""
        if lo > hi or node not in self.tree:
            return self.INF
        
        m, c = self.tree[node]
        result = m * x + c
        
        if lo == hi:
            return result
        
        mid = (lo + hi) // 2
        
        if x <= mid:
            result = min(result, self._query(2 * node, lo, mid, x))
        else:
            result = min(result, self._query(2 * node + 1, mid + 1, hi, x))
        
        return result
    
    def query(self, x: int) -> int:
        """Query minimum y at x."""
        return self._query(1, self.lo, self.hi, x)


# Example usage
def dp_with_cht(n: int, a: List[int], b: List[int], c: List[int]) -> int:
    """
    dp[i] = min(dp[j] + b[j] * a[i] + c[j]) for j < i
          = min(b[j] * a[i] + (dp[j] + c[j]))
    
    Line for j: y = b[j] * x + (dp[j] + c[j])
    Query at x = a[i]
    """
    tree = LiChaoTree(-10**9, 10**9)
    dp = [0] * n
    
    for i in range(n):
        if i > 0:
            dp[i] = tree.query(a[i])
        
        tree.add(b[i], dp[i] + c[i])
    
    return dp[n - 1]
```

---

## 6. Aliens Trick (WQS Binary Search)

### When to Use
- Problem: Select exactly k items optimizing some cost
- Key insight: Binary search on Lagrange multiplier

```python
def aliens_trick(n: int, k: int, cost_fn) -> int:
    """
    Aliens Trick / WQS Binary Search
    
    Original: dp[i][j] = best cost using exactly j items from first i
    With penalty λ: dp[i] = min cost using any number, minus λ per item
    
    Binary search on λ to get exactly k items.
    
    Time: O(n log(MAX_COST) * T(dp))
    """
    
    def dp_with_penalty(penalty: float) -> tuple:
        """
        Solve DP with penalty per item selected.
        Returns (cost, count) where cost excludes penalty.
        """
        # Implement your DP here with modified cost
        # Each item costs: original_cost - penalty
        pass
    
    # Binary search on penalty
    lo, hi = -10**9, 10**9
    
    while hi - lo > 1e-9:
        mid = (lo + hi) / 2
        cost, count = dp_with_penalty(mid)
        
        if count >= k:
            lo = mid
        else:
            hi = mid
    
    cost, count = dp_with_penalty(lo)
    return int(cost + lo * k)
```

---

## 7. 1D/1D DP Optimization Summary

```
Given: dp[i] = min/max(f(dp[j], i, j)) for j in [l(i), r(i)]

Techniques by structure:

1. f(dp[j], i, j) = dp[j] + cost(j, i) where cost is additive
   → Monotonic Queue if [l(i), r(i)] is sliding window

2. f(dp[j], i, j) = dp[j] + cost(j, i), optimal j monotonic
   → Divide & Conquer DP

3. f(dp[j], i, j) = dp[j] + b[j] * a[i] (linear in query point)
   → Convex Hull Trick

4. Interval DP with monotonic optimal split
   → Knuth's Optimization
```

---

## 8. Practice Problems

| Problem | Technique | Difficulty |
|---------|-----------|------------|
| LC 239 Sliding Window Maximum | Monotonic Queue | Medium |
| LC 1425 Constrained Subsequence Sum | Monotonic Queue + DP | Hard |
| LC 1687 Delivering Boxes | Monotonic Queue DP | Hard |
| CSES Array Division | D&C DP | Hard |
| CF IOI 2016 Aliens | Aliens Trick | Hard |
| POJ 3017 Cut the Sequence | CHT | Hard |
| SPOJ NKLEAVES | Knuth | Hard |

---

## 9. References

1. CP-Algorithms: https://cp-algorithms.com/dynamic_programming/divide-and-conquer-dp.html
2. Codeforces Blog on DP Optimizations
3. "Looking for a Challenge?" - Springer
4. IOI 2016 Aliens Problem Analysis
