---
title: Convex Hull Trick and Li Chao Tree
topic: Algorithms - Dynamic Programming
difficulty: Hard
tags: [convex-hull-trick, li-chao-tree, dp-optimization, competitive]
status: complete
weight: 10
---

# Convex Hull Trick (CHT) & Li Chao Tree

## 1. Overview

### Convex Hull Trick
Optimizes DP of the form:
```
dp[i] = min/max(dp[j] + m[j] * x[i] + c[j]) for j < i
```
Where we're querying linear functions `y = m[j] * x + c[j]`

### Variants
| Variant | Constraint | Time |
|---------|------------|------|
| Basic CHT | Slopes monotonic | O(n) |
| Dynamic CHT | Any order | O(n log n) |
| Li Chao Tree | Any order, integer coords | O(n log MAX) |

---

## 2. Basic CHT (Monotonic Slopes)

### Intuition
We maintain lines in a "convex hull" structure. For min queries with decreasing slopes:
- Lines form a lower envelope
- Use stack/deque to maintain valid lines
- Query point moves monotonically → pointer/binary search

### Implementation for Minimum (Decreasing Slopes)

```python
from typing import List, Tuple

class ConvexHullTrickMin:
    """
    Convex Hull Trick for minimum queries.
    
    Requirements:
        - Slopes are inserted in DECREASING order
        - Query points can be in any order (use binary search)
        - Or query points in INCREASING order (use pointer)
    
    Time: O(n) total for sorted queries, O(n log n) for arbitrary
    """
    
    def __init__(self):
        self.lines: List[Tuple[int, int]] = []  # (slope, intercept)
        self.ptr = 0
    
    def _bad(self, l1: Tuple[int, int], l2: Tuple[int, int], l3: Tuple[int, int]) -> bool:
        """
        Check if l2 is useless (dominated by l1 and l3).
        
        l2 is bad if intersection of l1,l3 is to the LEFT of l1,l2.
        """
        m1, c1 = l1
        m2, c2 = l2
        m3, c3 = l3
        
        # Intersection of l1 and l2: (c2-c1)/(m1-m2)
        # Intersection of l1 and l3: (c3-c1)/(m1-m3)
        # l2 bad if: (c2-c1)/(m1-m2) >= (c3-c1)/(m1-m3)
        # Cross multiply (careful with signs, assuming m1 > m2 > m3):
        return (c2 - c1) * (m1 - m3) >= (c3 - c1) * (m1 - m2)
    
    def add_line(self, m: int, c: int) -> None:
        """
        Add line y = mx + c.
        
        Slopes must be added in DECREASING order.
        """
        new_line = (m, c)
        
        # Remove dominated lines from back
        while len(self.lines) >= 2 and self._bad(self.lines[-2], self.lines[-1], new_line):
            self.lines.pop()
        
        self.lines.append(new_line)
    
    def query(self, x: int) -> int:
        """
        Query minimum y value at x.
        
        Time: O(log n) with binary search
        """
        if not self.lines:
            return float('inf')
        
        # Binary search for the optimal line
        lo, hi = 0, len(self.lines) - 1
        
        while lo < hi:
            mid = (lo + hi) // 2
            m1, c1 = self.lines[mid]
            m2, c2 = self.lines[mid + 1]
            
            if m1 * x + c1 > m2 * x + c2:
                lo = mid + 1
            else:
                hi = mid
        
        m, c = self.lines[lo]
        return m * x + c
    
    def query_monotonic(self, x: int) -> int:
        """
        Query for monotonically increasing x values.
        
        Time: Amortized O(1)
        """
        if not self.lines:
            return float('inf')
        
        # Move pointer forward while next line is better
        while self.ptr < len(self.lines) - 1:
            m1, c1 = self.lines[self.ptr]
            m2, c2 = self.lines[self.ptr + 1]
            
            if m1 * x + c1 > m2 * x + c2:
                self.ptr += 1
            else:
                break
        
        m, c = self.lines[self.ptr]
        return m * x + c


# Example: dp[i] = min(dp[j] + b[j] * a[i]) for j < i
def solve_dp_cht(a: List[int], b: List[int]) -> int:
    """
    Assumes b is sorted (decreasing for min query).
    """
    n = len(a)
    dp = [0] * n
    cht = ConvexHullTrickMin()
    
    for i in range(n):
        if i > 0:
            dp[i] = cht.query(a[i])
        
        # Add line: y = b[i] * x + dp[i]
        cht.add_line(b[i], dp[i])
    
    return dp[n - 1]
```

### Implementation for Maximum (Increasing Slopes)

```python
class ConvexHullTrickMax:
    """
    Convex Hull Trick for maximum queries.
    
    Requirements: Slopes inserted in INCREASING order.
    """
    
    def __init__(self):
        self.lines: List[Tuple[int, int]] = []
        self.ptr = 0
    
    def _bad(self, l1: Tuple[int, int], l2: Tuple[int, int], l3: Tuple[int, int]) -> bool:
        """l2 is dominated for upper envelope."""
        m1, c1 = l1
        m2, c2 = l2
        m3, c3 = l3
        
        # For max (upper envelope), l2 is bad if intersection l1,l3 is RIGHT of l1,l2
        return (c2 - c1) * (m3 - m1) >= (c3 - c1) * (m2 - m1)
    
    def add_line(self, m: int, c: int) -> None:
        new_line = (m, c)
        while len(self.lines) >= 2 and self._bad(self.lines[-2], self.lines[-1], new_line):
            self.lines.pop()
        self.lines.append(new_line)
    
    def query(self, x: int) -> int:
        if not self.lines:
            return float('-inf')
        
        lo, hi = 0, len(self.lines) - 1
        while lo < hi:
            mid = (lo + hi) // 2
            m1, c1 = self.lines[mid]
            m2, c2 = self.lines[mid + 1]
            
            if m1 * x + c1 < m2 * x + c2:
                lo = mid + 1
            else:
                hi = mid
        
        m, c = self.lines[lo]
        return m * x + c
```

---

## 3. Dynamic CHT (Any Order)

When slopes are not monotonic, we use a balanced data structure.

```python
from sortedcontainers import SortedList

class DynamicCHT:
    """
    Dynamic Convex Hull Trick using SortedList.
    
    Supports adding lines in any order.
    
    Time: O(log n) per operation
    """
    
    def __init__(self, maximum: bool = False):
        """
        Args:
            maximum: True for max queries, False for min
        """
        self.maximum = maximum
        # Store lines as (-slope, intercept) for min, or (slope, intercept) for max
        self.lines = SortedList()
    
    def _sign(self) -> int:
        return -1 if self.maximum else 1
    
    def _eval(self, line: Tuple[int, int], x: int) -> int:
        m, c = line
        m = m * self._sign()
        return m * x + c
    
    def _bad(self, l1, l2, l3) -> bool:
        """Check if l2 is dominated."""
        if l1 is None or l3 is None:
            return False
        
        m1, c1 = l1[0] * self._sign(), l1[1]
        m2, c2 = l2[0] * self._sign(), l2[1]
        m3, c3 = l3[0] * self._sign(), l3[1]
        
        # Avoid division
        return (c2 - c1) * (m1 - m3) >= (c3 - c1) * (m1 - m2)
    
    def add_line(self, m: int, c: int) -> None:
        """Add line y = mx + c."""
        m = m * self._sign()
        new_line = (m, c)
        
        # Find position to insert
        idx = self.lines.bisect_left(new_line)
        
        # Check if redundant
        if idx > 0 and idx < len(self.lines):
            left = self.lines[idx - 1]
            right = self.lines[idx]
            if self._bad(left, new_line, right):
                return
        
        # Insert
        self.lines.add(new_line)
        idx = self.lines.index(new_line)
        
        # Remove dominated lines to the left
        while idx > 1 and self._bad(self.lines[idx - 2], self.lines[idx - 1], new_line):
            del self.lines[idx - 1]
            idx -= 1
        
        # Remove dominated lines to the right
        while idx < len(self.lines) - 2 and self._bad(new_line, self.lines[idx + 1], self.lines[idx + 2]):
            del self.lines[idx + 1]
    
    def query(self, x: int) -> int:
        """Query min/max at x."""
        if not self.lines:
            return float('inf') if not self.maximum else float('-inf')
        
        lo, hi = 0, len(self.lines) - 1
        while lo < hi:
            mid = (lo + hi) // 2
            if self._eval(self.lines[mid], x) > self._eval(self.lines[mid + 1], x):
                lo = mid + 1
            else:
                hi = mid
        
        result = self._eval(self.lines[lo], x)
        return result if not self.maximum else result
```

---

## 4. Li Chao Tree

### Concept
A segment tree over the range of query points. Each node stores one line that is optimal at the midpoint.

### Implementation

```python
class LiChaoTree:
    """
    Li Chao Tree for dynamic line insertion and min queries.
    
    Time: O(log RANGE) per operation
    Space: O(RANGE) in worst case, but typically much less
    """
    
    def __init__(self, lo: int, hi: int, maximum: bool = False):
        """
        Args:
            lo: Minimum query value
            hi: Maximum query value
            maximum: True for max queries
        """
        self.lo = lo
        self.hi = hi
        self.maximum = maximum
        self.tree = {}  # node_id -> (m, c)
        self.INF = float('inf')
    
    def _better(self, v1: int, v2: int) -> bool:
        """Is v1 better than v2?"""
        if self.maximum:
            return v1 > v2
        return v1 < v2
    
    def _eval(self, line: Tuple[int, int], x: int) -> int:
        if line is None:
            return -self.INF if self.maximum else self.INF
        m, c = line
        return m * x + c
    
    def _add_line(self, node: int, lo: int, hi: int, m: int, c: int) -> None:
        """Add line y = mx + c to subtree rooted at node."""
        if lo > hi:
            return
        
        mid = (lo + hi) // 2
        
        new_line = (m, c)
        old_line = self.tree.get(node)
        
        if old_line is None:
            self.tree[node] = new_line
            return
        
        new_mid = self._eval(new_line, mid)
        old_mid = self._eval(old_line, mid)
        
        new_lo = self._eval(new_line, lo)
        old_lo = self._eval(old_line, lo)
        
        # Determine which line to keep at this node
        if self._better(new_mid, old_mid):
            self.tree[node] = new_line
            new_line, old_line = old_line, new_line
            new_lo, old_lo = old_lo, new_lo
        
        # The line NOT stored at this node might be better in a subtree
        if self._better(new_lo, old_lo):
            self._add_line(2 * node, lo, mid, new_line[0], new_line[1])
        else:
            self._add_line(2 * node + 1, mid + 1, hi, new_line[0], new_line[1])
    
    def add_line(self, m: int, c: int) -> None:
        """Add line y = mx + c."""
        self._add_line(1, self.lo, self.hi, m, c)
    
    def _query(self, node: int, lo: int, hi: int, x: int) -> int:
        """Query at x."""
        if lo > hi:
            return -self.INF if self.maximum else self.INF
        
        line = self.tree.get(node)
        result = self._eval(line, x)
        
        if lo == hi:
            return result
        
        mid = (lo + hi) // 2
        
        if x <= mid:
            child_result = self._query(2 * node, lo, mid, x)
        else:
            child_result = self._query(2 * node + 1, mid + 1, hi, x)
        
        if self._better(child_result, result):
            return child_result
        return result
    
    def query(self, x: int) -> int:
        """Query minimum/maximum at x."""
        return self._query(1, self.lo, self.hi, x)


# Example usage
def dp_with_li_chao(n: int, a: List[int], b: List[int], c: List[int]) -> int:
    """
    dp[i] = min(dp[j] + b[j] * a[i] + c[j]) for j < i
    
    Rewrite as: dp[i] = min(b[j] * a[i] + (dp[j] + c[j]))
    Line for j: y = b[j] * x + (dp[j] + c[j])
    Query at x = a[i]
    """
    tree = LiChaoTree(-10**9, 10**9)
    dp = [0] * n
    
    for i in range(n):
        if i > 0:
            dp[i] = tree.query(a[i])
        
        # Add line for position i
        tree.add_line(b[i], dp[i] + c[i])
    
    return dp[n - 1]
```

### Persistent Li Chao Tree

```python
class PersistentLiChaoTree:
    """
    Persistent Li Chao Tree - maintains history of all versions.
    
    Useful for range queries on lines.
    """
    
    def __init__(self, lo: int, hi: int):
        self.lo = lo
        self.hi = hi
        self.nodes = [(None, None, None)]  # (line, left_child, right_child)
        self.roots = [0]  # Root of each version
    
    def _eval(self, line, x: int) -> int:
        if line is None:
            return float('inf')
        return line[0] * x + line[1]
    
    def _add(self, node: int, lo: int, hi: int, m: int, c: int) -> int:
        """Returns new node index."""
        new_line = (m, c)
        
        if node == 0 or node >= len(self.nodes):
            # Create new node with just this line
            new_id = len(self.nodes)
            self.nodes.append((new_line, 0, 0))
            return new_id
        
        old_line, left, right = self.nodes[node]
        
        mid = (lo + hi) // 2
        
        if old_line is None:
            new_id = len(self.nodes)
            self.nodes.append((new_line, left, right))
            return new_id
        
        new_mid = self._eval(new_line, mid)
        old_mid = self._eval(old_line, mid)
        
        if new_mid < old_mid:
            # New line is better at mid, swap
            new_line, old_line = old_line, new_line
        
        new_lo = self._eval(new_line, lo)
        old_lo = self._eval(old_line, lo)
        
        # Determine which subtree to recurse
        if lo == hi:
            new_id = len(self.nodes)
            self.nodes.append((old_line, left, right))
            return new_id
        
        if new_lo < old_lo:
            new_left = self._add(left, lo, mid, new_line[0], new_line[1])
            new_id = len(self.nodes)
            self.nodes.append((old_line, new_left, right))
        else:
            new_right = self._add(right, mid + 1, hi, new_line[0], new_line[1])
            new_id = len(self.nodes)
            self.nodes.append((old_line, left, new_right))
        
        return new_id
    
    def add_line(self, version: int, m: int, c: int) -> int:
        """Add line to version, return new version."""
        root = self.roots[version]
        new_root = self._add(root, self.lo, self.hi, m, c)
        self.roots.append(new_root)
        return len(self.roots) - 1
    
    def _query(self, node: int, lo: int, hi: int, x: int) -> int:
        if node == 0 or node >= len(self.nodes):
            return float('inf')
        
        line, left, right = self.nodes[node]
        result = self._eval(line, x)
        
        if lo == hi:
            return result
        
        mid = (lo + hi) // 2
        
        if x <= mid:
            child_result = self._query(left, lo, mid, x)
        else:
            child_result = self._query(right, mid + 1, hi, x)
        
        return min(result, child_result)
    
    def query(self, version: int, x: int) -> int:
        root = self.roots[version]
        return self._query(root, self.lo, self.hi, x)
```

---

## 5. Applications

### 5.1 Covered Walkway (IOI 2014)

```python
def covered_walkway(n: int, c: int, positions: List[int]) -> int:
    """
    Build roofs to cover n positions. Roof from i to j costs c + (p[j] - p[i])².
    Minimize total cost.
    
    dp[i] = min cost to cover first i positions
    dp[i] = min(dp[j] + c + (p[i] - p[j+1])²) for j < i
    
    Expand: dp[j] + c + p[i]² - 2*p[i]*p[j+1] + p[j+1]²
          = (-2*p[j+1]) * p[i] + (dp[j] + p[j+1]² + c) + p[i]²
    
    Line: slope = -2*p[j+1], intercept = dp[j] + p[j+1]² + c
    Query at: p[i]
    """
    dp = [0] * (n + 1)
    tree = LiChaoTree(0, 2 * 10**9)
    
    for i in range(1, n + 1):
        # Query
        query_x = positions[i - 1]
        dp[i] = tree.query(query_x) + query_x * query_x
        
        # Add line for next iteration
        # For j = i, we add line for covering positions up to i
        slope = -2 * positions[i - 1]
        intercept = dp[i] + positions[i - 1] ** 2 + c
        tree.add_line(slope, intercept)
    
    return dp[n]
```

### 5.2 USACO Tallest Fence

```python
def tallest_fence_cht(heights: List[int]) -> int:
    """
    Find contiguous subarray where (sum of heights) * (length) is maximized.
    
    Use CHT with prefix sums.
    """
    n = len(heights)
    prefix = [0] * (n + 1)
    for i in range(n):
        prefix[i + 1] = prefix[i] + heights[i]
    
    # For subarray [j, i]: area = (prefix[i+1] - prefix[j]) * (i - j + 1)
    # Maximize over all j <= i
    
    # Rewrite: prefix[i+1]*i - prefix[i+1]*j - prefix[j]*i + prefix[j]*j + something
    # This doesn't directly fit CHT, need different formulation
    
    # Alternative: ternary search or different DP
    pass
```

---

## 6. Practice Problems

| Problem | Platform | Technique |
|---------|----------|-----------|
| Covered Walkway | IOI 2014 | Basic CHT |
| Commando | APIO 2010 | CHT |
| Land Acquisition | USACO | CHT |
| NKLEAVES | SPOJ | CHT + Knuth |
| Product Sum | Codeforces | Li Chao Tree |
| Frog 3 | AtCoder DP | Basic CHT |

---

## 7. Tips and Tricks

### Choosing the Right Variant

```
1. Slopes monotonic, queries monotonic?
   → Basic CHT with pointer: O(n)

2. Slopes monotonic, queries arbitrary?
   → Basic CHT with binary search: O(n log n)

3. Slopes arbitrary, integer coordinates?
   → Li Chao Tree: O(n log MAX)

4. Slopes arbitrary, need dynamic structure?
   → Dynamic CHT with balanced BST: O(n log n)

5. Need persistence?
   → Persistent Li Chao Tree: O(n log MAX)
```

### Common Mistakes
1. Getting the sign wrong for max vs min
2. Integer overflow in cross-multiplication
3. Off-by-one in query ranges
4. Forgetting to handle empty hull

---

## 8. References

1. CP-Algorithms: https://cp-algorithms.com/geometry/convex_hull_trick.html
2. Li Chao original paper
3. Codeforces blog: "Convex hull trick and Li Chao tree"
4. KACTL (KTH Algorithm Competition Template Library)
