---
title: "Sparse Table"
weight: 14
---

# Sparse Table

---
title: Sparse Table - Static Range Queries
topic: Data Structures
difficulty: Medium
tags: [sparse-table, range-query, rmq, idempotent, interview-useful]
status: complete
---

## 1. Summary / Quick Reference

| Property | Value |
|----------|-------|
| Build Time | O(n log n) |
| Query Time | O(1) for idempotent ops |
| Query Time | O(log n) for non-idempotent ops |
| Space | O(n log n) |
| Update | Not supported (static) |
| Use Case | Static RMQ, GCD, LCA |

**One-liner**: A data structure for answering static range queries in O(1) time using precomputed answers for ranges of length $2^k$.

---

## 2. Conceptual Foundation

### What is a Sparse Table?
A Sparse Table is a data structure that preprocesses an array to answer range queries in O(1) time for **idempotent** operations like min, max, GCD, AND, OR.

### What is an Idempotent Operation?
An operation `f` is idempotent if `f(a, a) = a`. This means overlapping ranges don't affect the result.

| Operation | Idempotent? | Sparse Table Query |
|-----------|-------------|-------------------|
| min | Yes | O(1) |
| max | Yes | O(1) |
| GCD | Yes | O(1) |
| AND | Yes | O(1) |
| OR | Yes | O(1) |
| sum | No | O(log n) |
| XOR | No | O(log n) |

### Key Insight
For any range [l, r], we can cover it with **at most 2** precomputed ranges:
- Find the largest $k$ such that $2^k \leq r - l + 1$
- Use ranges [l, l + 2^k - 1] and [r - 2^k + 1, r]
- These may overlap, but for idempotent operations, that's okay!

```
Query [2, 9]:
Length = 8, k = 3 (since 2^3 = 8)

Array:  [a0] [a1] [a2] [a3] [a4] [a5] [a6] [a7] [a8] [a9]
Range 1:          [------------ 2^3 = 8 ----------]
Range 2:               [------------ 2^3 = 8 ----------]

min([2, 9]) = min(st[3][2], st[3][2])  // Both cover [2, 9]
```

---

## 3. Implementation

### Basic Sparse Table (RMQ - Range Minimum Query)

```python
from typing import List, Callable
import math

class SparseTable:
    """
    Sparse Table for O(1) Range Minimum Queries.
    
    Build: O(n log n) time and space
    Query: O(1) for min/max (idempotent operations)
    
    Note: Does NOT support updates. Use Segment Tree for dynamic arrays.
    """
    
    def __init__(self, arr: List[int]):
        self.n = len(arr)
        if self.n == 0:
            return
        
        # Calculate log values for O(1) lookup
        self.log = [0] * (self.n + 1)
        for i in range(2, self.n + 1):
            self.log[i] = self.log[i // 2] + 1
        
        # Build sparse table
        self.k = self.log[self.n] + 1
        self.table = [[0] * self.n for _ in range(self.k)]
        
        # Initialize first row (ranges of length 1 = 2^0)
        self.table[0] = arr.copy()
        
        # Build table using dynamic programming
        # table[j][i] = min of range starting at i with length 2^j
        for j in range(1, self.k):
            for i in range(self.n - (1 << j) + 1):
                self.table[j][i] = min(
                    self.table[j - 1][i],
                    self.table[j - 1][i + (1 << (j - 1))]
                )
    
    def query_min(self, left: int, right: int) -> int:
        """
        Return minimum value in range [left, right] (0-indexed).
        
        Time: O(1)
        """
        length = right - left + 1
        k = self.log[length]
        
        return min(
            self.table[k][left],
            self.table[k][right - (1 << k) + 1]
        )


# Example usage
if __name__ == "__main__":
    arr = [1, 3, 4, 8, 6, 1, 4, 2]
    st = SparseTable(arr)
    
    print(f"Min in [0, 7]: {st.query_min(0, 7)}")  # 1
    print(f"Min in [1, 4]: {st.query_min(1, 4)}")  # 3
    print(f"Min in [2, 5]: {st.query_min(2, 5)}")  # 1
    print(f"Min in [4, 7]: {st.query_min(4, 7)}")  # 1
```

### Generic Sparse Table

```python
from typing import List, Callable, TypeVar, Generic

T = TypeVar('T')

class GenericSparseTable(Generic[T]):
    """
    Generic Sparse Table supporting any idempotent operation.
    
    Examples of idempotent operations:
        - min: lambda a, b: a if a < b else b
        - max: lambda a, b: a if a > b else b
        - gcd: math.gcd
        - bitwise AND: lambda a, b: a & b
        - bitwise OR: lambda a, b: a | b
    """
    
    def __init__(self, arr: List[T], func: Callable[[T, T], T]):
        """
        Args:
            arr: Input array
            func: Idempotent binary function
        """
        self.n = len(arr)
        self.func = func
        
        if self.n == 0:
            return
        
        # Precompute logs
        self.log = [0] * (self.n + 1)
        for i in range(2, self.n + 1):
            self.log[i] = self.log[i // 2] + 1
        
        # Build sparse table
        self.k = self.log[self.n] + 1
        self.table = [[None] * self.n for _ in range(self.k)]
        
        # Initialize base case
        for i in range(self.n):
            self.table[0][i] = arr[i]
        
        # Fill table
        for j in range(1, self.k):
            for i in range(self.n - (1 << j) + 1):
                self.table[j][i] = self.func(
                    self.table[j - 1][i],
                    self.table[j - 1][i + (1 << (j - 1))]
                )
    
    def query(self, left: int, right: int) -> T:
        """Query range [left, right] in O(1)."""
        length = right - left + 1
        k = self.log[length]
        
        return self.func(
            self.table[k][left],
            self.table[k][right - (1 << k) + 1]
        )


# Example: Range Maximum Query
def test_max():
    arr = [1, 3, 4, 8, 6, 1, 4, 2]
    st = GenericSparseTable(arr, max)
    print(f"Max in [0, 7]: {st.query(0, 7)}")  # 8
    print(f"Max in [2, 5]: {st.query(2, 5)}")  # 8

# Example: Range GCD Query
import math
def test_gcd():
    arr = [12, 18, 24, 36, 48]
    st = GenericSparseTable(arr, math.gcd)
    print(f"GCD of [0, 4]: {st.query(0, 4)}")  # 6
    print(f"GCD of [1, 3]: {st.query(1, 3)}")  # 6

# Example: Range AND Query
def test_and():
    arr = [15, 12, 14, 8, 10]
    st = GenericSparseTable(arr, lambda a, b: a & b)
    print(f"AND of [0, 4]: {st.query(0, 4)}")  # 8
```

---

## 4. Non-Idempotent Operations (Sum)

For non-idempotent operations like sum, we can't use overlapping ranges. Instead, we decompose the range into non-overlapping powers of 2.

```python
class SparseTableSum:
    """
    Sparse Table for range sum (non-idempotent).
    
    Query: O(log n) - must decompose into non-overlapping ranges
    
    Note: For sum, prefix sums are usually better (O(1) query).
    This implementation is for educational purposes.
    """
    
    def __init__(self, arr: List[int]):
        self.n = len(arr)
        if self.n == 0:
            return
        
        self.log = [0] * (self.n + 1)
        for i in range(2, self.n + 1):
            self.log[i] = self.log[i // 2] + 1
        
        self.k = self.log[self.n] + 1
        self.table = [[0] * self.n for _ in range(self.k)]
        
        self.table[0] = arr.copy()
        
        for j in range(1, self.k):
            for i in range(self.n - (1 << j) + 1):
                self.table[j][i] = (
                    self.table[j - 1][i] +
                    self.table[j - 1][i + (1 << (j - 1))]
                )
    
    def query_sum(self, left: int, right: int) -> int:
        """
        Return sum of range [left, right].
        
        Time: O(log n) - decompose into non-overlapping ranges
        """
        total = 0
        length = right - left + 1
        
        j = self.log[length]
        while j >= 0 and left <= right:
            if (1 << j) <= right - left + 1:
                total += self.table[j][left]
                left += (1 << j)
            j -= 1
        
        return total


# Example
arr = [1, 2, 3, 4, 5]
st = SparseTableSum(arr)
print(f"Sum [0, 4]: {st.query_sum(0, 4)}")  # 15
print(f"Sum [1, 3]: {st.query_sum(1, 3)}")  # 9
```

---

## 5. Sparse Table for Index Queries

Often we need to return the **index** of the minimum/maximum, not just the value.

```python
class SparseTableIndex:
    """
    Sparse Table that returns the INDEX of the minimum element.
    Useful for LCA (Lowest Common Ancestor) and RMQ applications.
    """
    
    def __init__(self, arr: List[int]):
        self.n = len(arr)
        self.arr = arr
        
        if self.n == 0:
            return
        
        self.log = [0] * (self.n + 1)
        for i in range(2, self.n + 1):
            self.log[i] = self.log[i // 2] + 1
        
        self.k = self.log[self.n] + 1
        # Store indices instead of values
        self.table = [[0] * self.n for _ in range(self.k)]
        
        # Initialize with indices
        for i in range(self.n):
            self.table[0][i] = i
        
        # Build table storing indices of minimum
        for j in range(1, self.k):
            for i in range(self.n - (1 << j) + 1):
                left_idx = self.table[j - 1][i]
                right_idx = self.table[j - 1][i + (1 << (j - 1))]
                
                if self.arr[left_idx] <= self.arr[right_idx]:
                    self.table[j][i] = left_idx
                else:
                    self.table[j][i] = right_idx
    
    def query_min_index(self, left: int, right: int) -> int:
        """Return INDEX of minimum element in [left, right]."""
        length = right - left + 1
        k = self.log[length]
        
        left_idx = self.table[k][left]
        right_idx = self.table[k][right - (1 << k) + 1]
        
        if self.arr[left_idx] <= self.arr[right_idx]:
            return left_idx
        return right_idx
    
    def query_min_value(self, left: int, right: int) -> int:
        """Return VALUE of minimum element in [left, right]."""
        return self.arr[self.query_min_index(left, right)]


# Example
arr = [1, 3, 4, 8, 6, 1, 4, 2]
st = SparseTableIndex(arr)

idx = st.query_min_index(2, 6)
print(f"Index of min in [2, 6]: {idx}")  # 5
print(f"Value: {arr[idx]}")  # 1
```

---

## 6. Applications

### 6.1 LCA using Sparse Table + Euler Tour

```python
from typing import List, Optional
from collections import defaultdict

class LCAEulerTour:
    """
    Lowest Common Ancestor using Euler Tour + Sparse Table RMQ.
    
    Build: O(n log n)
    Query: O(1)
    
    Approach:
    1. Euler tour of tree captures traversal order
    2. For each node, record first occurrence in tour
    3. LCA(u, v) = node with minimum depth between first(u) and first(v)
    """
    
    def __init__(self, n: int, edges: List[tuple]):
        self.n = n
        self.adj = defaultdict(list)
        
        for u, v in edges:
            self.adj[u].append(v)
            self.adj[v].append(u)
        
        # Euler tour arrays
        self.euler = []      # Euler tour sequence
        self.depth = []      # Depth at each position in tour
        self.first = [-1] * n  # First occurrence of each node
        
        # Build Euler tour
        self._euler_tour(0, -1, 0)
        
        # Build sparse table on depths
        self._build_sparse_table()
    
    def _euler_tour(self, node: int, parent: int, d: int) -> None:
        self.first[node] = len(self.euler)
        self.euler.append(node)
        self.depth.append(d)
        
        for neighbor in self.adj[node]:
            if neighbor != parent:
                self._euler_tour(neighbor, node, d + 1)
                self.euler.append(node)
                self.depth.append(d)
    
    def _build_sparse_table(self) -> None:
        m = len(self.euler)
        
        self.log = [0] * (m + 1)
        for i in range(2, m + 1):
            self.log[i] = self.log[i // 2] + 1
        
        self.k = self.log[m] + 1
        self.table = [[0] * m for _ in range(self.k)]
        
        # Store indices
        for i in range(m):
            self.table[0][i] = i
        
        for j in range(1, self.k):
            for i in range(m - (1 << j) + 1):
                left_idx = self.table[j - 1][i]
                right_idx = self.table[j - 1][i + (1 << (j - 1))]
                
                if self.depth[left_idx] < self.depth[right_idx]:
                    self.table[j][i] = left_idx
                else:
                    self.table[j][i] = right_idx
    
    def _rmq(self, left: int, right: int) -> int:
        """Return index of minimum depth in [left, right]."""
        length = right - left + 1
        k = self.log[length]
        
        left_idx = self.table[k][left]
        right_idx = self.table[k][right - (1 << k) + 1]
        
        if self.depth[left_idx] < self.depth[right_idx]:
            return left_idx
        return right_idx
    
    def lca(self, u: int, v: int) -> int:
        """Return LCA of nodes u and v."""
        left = self.first[u]
        right = self.first[v]
        
        if left > right:
            left, right = right, left
        
        return self.euler[self._rmq(left, right)]


# Example: Tree
#       0
#      /|\
#     1 2 3
#    /|   |
#   4 5   6
edges = [(0, 1), (0, 2), (0, 3), (1, 4), (1, 5), (3, 6)]
lca = LCAEulerTour(7, edges)

print(f"LCA(4, 5): {lca.lca(4, 5)}")  # 1
print(f"LCA(4, 6): {lca.lca(4, 6)}")  # 0
print(f"LCA(2, 3): {lca.lca(2, 3)}")  # 0
print(f"LCA(4, 2): {lca.lca(4, 2)}")  # 0
```

### 6.2 Range GCD Queries

```python
import math

def solve_range_gcd():
    """
    Problem: Given array, answer multiple GCD queries.
    
    Example: Array [12, 18, 24, 36, 6]
    Query(0, 2) = GCD(12, 18, 24) = 6
    Query(1, 4) = GCD(18, 24, 36, 6) = 6
    """
    arr = [12, 18, 24, 36, 6]
    st = GenericSparseTable(arr, math.gcd)
    
    queries = [(0, 2), (1, 4), (0, 4), (2, 3)]
    for l, r in queries:
        print(f"GCD of [{l}, {r}]: {st.query(l, r)}")


# solve_range_gcd()
```

### 6.3 Longest Increasing Subsequence in Range

```python
def lis_in_range():
    """
    Use sparse table with custom merge for range LIS queries.
    This is a simplified version - full LIS needs segment tree.
    """
    pass  # Complex - typically done with segment tree
```

---

## 7. Disjoint Sparse Table (Faster Build)

```python
class DisjointSparseTable:
    """
    Disjoint Sparse Table - O(n log n) build, O(1) query.
    Works for ANY associative operation, not just idempotent!
    
    Key insight: For each level, divide into blocks and precompute
    prefix/suffix within each block.
    """
    
    def __init__(self, arr: List[int], func: Callable[[int, int], int], identity: int):
        self.n = len(arr)
        self.func = func
        self.identity = identity
        
        if self.n == 0:
            return
        
        self.log = [0] * (self.n + 1)
        for i in range(2, self.n + 1):
            self.log[i] = self.log[i // 2] + 1
        
        self.k = self.log[self.n] + 1
        
        # For each level, store prefix and suffix products
        self.table = [[identity] * self.n for _ in range(self.k)]
        
        self._build(arr)
    
    def _build(self, arr: List[int]) -> None:
        # Level 0: just the elements
        for i in range(self.n):
            self.table[0][i] = arr[i]
        
        # Higher levels
        for j in range(1, self.k):
            block_size = 1 << j
            
            for block_start in range(0, self.n, block_size):
                mid = block_start + (block_size >> 1)
                block_end = min(block_start + block_size, self.n)
                
                # Build suffix products from mid-1 down to block_start
                if mid > 0 and mid - 1 >= block_start:
                    self.table[j][mid - 1] = arr[mid - 1]
                    for i in range(mid - 2, block_start - 1, -1):
                        self.table[j][i] = self.func(arr[i], self.table[j][i + 1])
                
                # Build prefix products from mid to block_end-1
                if mid < block_end:
                    self.table[j][mid] = arr[mid]
                    for i in range(mid + 1, block_end):
                        self.table[j][i] = self.func(self.table[j][i - 1], arr[i])
    
    def query(self, left: int, right: int) -> int:
        """Query range [left, right] in O(1)."""
        if left == right:
            return self.table[0][left]
        
        # Find level where left and right are in different blocks
        j = self.log[left ^ right] + 1
        
        return self.func(self.table[j][left], self.table[j][right])


# Example: Range XOR (non-idempotent!)
arr = [1, 2, 3, 4, 5, 6, 7, 8]
dst = DisjointSparseTable(arr, lambda a, b: a ^ b, 0)

print(f"XOR [0, 7]: {dst.query(0, 7)}")  # 1^2^3^4^5^6^7^8 = 0
print(f"XOR [1, 4]: {dst.query(1, 4)}")  # 2^3^4^5 = 2
```

---

## 8. Comparison with Other Data Structures

| Data Structure | Build | Query | Update | Space |
|----------------|-------|-------|--------|-------|
| Naive | O(n) | O(n) | O(1) | O(n) |
| Prefix Sum | O(n) | O(1) | O(n) | O(n) |
| Sparse Table | O(n log n) | O(1) | ❌ | O(n log n) |
| Segment Tree | O(n) | O(log n) | O(log n) | O(n) |
| Sqrt Decomp | O(n) | O(√n) | O(1) | O(n) |

### When to Use Sparse Table
1. ✅ Static array (no updates)
2. ✅ Many queries
3. ✅ Need O(1) query time
4. ✅ Idempotent operation (min, max, GCD, AND, OR)

### When NOT to Use
1. ❌ Array changes frequently
2. ❌ Sum queries (use prefix sum)
3. ❌ Space is critical
4. ❌ Need persistence

---

## 9. LeetCode Problems

| # | Problem | Difficulty | Key Technique |
|---|---------|------------|---------------|
| 239 | Sliding Window Maximum | Hard | Can use Sparse Table |
| 654 | Maximum Binary Tree | Medium | RMQ + Sparse Table |
| 1521 | Find Value of Mysterious Function | Hard | Sparse Table for AND |
| 1906 | Min Absolute Difference Queries | Medium | Sparse Table variant |
| 2470 | Number of Subarrays With LCM Equal to K | Medium | Sparse Table for LCM |

---

## 10. Interview Tips

### Quick Facts to Remember
1. **Build**: O(n log n) time and space
2. **Query**: O(1) for idempotent operations
3. **Static only**: No updates supported
4. **Key formula**: $table[j][i] = f(table[j-1][i], table[j-1][i + 2^{j-1}])$

### Common Mistakes
1. Off-by-one in log computation
2. Using for non-idempotent operations expecting O(1)
3. Forgetting to handle edge cases (empty array, single element)

### Interview Approach
1. Recognize static range query pattern
2. Mention O(n log n) preprocessing trade-off
3. Compare with segment tree if updates needed
4. For LCA problems, mention Euler tour + RMQ reduction

---

## 11. References

1. CP-Algorithms: https://cp-algorithms.com/data_structures/sparse-table.html
2. TopCoder Tutorial on RMQ
3. "Fischer-Heun Structure" for O(n) space RMQ
4. Competitive Programming 3 - Steven Halim
