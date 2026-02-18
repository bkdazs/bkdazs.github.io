---
title: "Fenwick Tree (Binary Indexed Tree)"
weight: 13
---

# Fenwick Tree (Binary Indexed Tree)

---
title: Fenwick Tree - Binary Indexed Tree
topic: Data Structures
difficulty: Medium
tags: [fenwick-tree, binary-indexed-tree, bit, range-query, interview-important]
status: complete
---

## 1. Summary / Quick Reference

| Property | Value |
|----------|-------|
| Build Time | O(n) or O(n log n) |
| Point Update | O(log n) |
| Prefix Query | O(log n) |
| Range Query | O(log n) |
| Space | O(n) |
| Use Case | Prefix sums with updates |

**One-liner**: A tree structure stored in an array that efficiently computes prefix sums and supports point updates using bit manipulation.

---

## 2. Conceptual Foundation

### What is a Fenwick Tree?
A Fenwick Tree (or Binary Indexed Tree - BIT) is a data structure that:
1. Stores cumulative frequencies/sums
2. Supports efficient prefix sum queries
3. Supports efficient point updates

### Why Use Fenwick Tree over Segment Tree?
| Aspect | Fenwick Tree | Segment Tree |
|--------|--------------|--------------|
| Code Complexity | ~10 lines | ~50 lines |
| Space | O(n) | O(4n) |
| Cache Efficiency | Better | Worse |
| Operations | Prefix queries | Range queries |
| Flexibility | Limited | Very flexible |

### Key Insight: Lowbit Operation
The magic of BIT lies in the `lowbit` function:
- `lowbit(x) = x & (-x)` - extracts the lowest set bit
- Example: `lowbit(12) = lowbit(1100₂) = 100₂ = 4`

```
Index:   1    2    3    4    5    6    7    8
Binary: 001  010  011  100  101  110  111  1000
Lowbit:  1    2    1    4    1    2    1    8
```

### Visual Representation
```
Index:     1    2    3    4    5    6    7    8
Array:    [3]  [2]  [-1] [6]  [5]  [4]  [-3] [3]

BIT[1] = a[1] = 3                    (covers index 1)
BIT[2] = a[1] + a[2] = 5             (covers indices 1-2)
BIT[3] = a[3] = -1                   (covers index 3)
BIT[4] = a[1]+a[2]+a[3]+a[4] = 10    (covers indices 1-4)
BIT[5] = a[5] = 5                    (covers index 5)
BIT[6] = a[5] + a[6] = 9             (covers indices 5-6)
BIT[7] = a[7] = -3                   (covers index 7)
BIT[8] = a[1]+...+a[8] = 19          (covers indices 1-8)

Responsibility structure:
                    [8]
           /        |         \
        [4]       [6]        [7]
       / | \      / \         |
     [1][2][3]  [5][6]       [7]
```

---

## 3. Basic Implementation

### Standard Fenwick Tree (1-indexed)

```python
from typing import List

class FenwickTree:
    """
    Fenwick Tree (Binary Indexed Tree) for prefix sum queries.
    
    Features:
        - Point update: Add value to index
        - Prefix query: Sum of elements [1, i]
        - Range query: Sum of elements [l, r]
    
    Note: Uses 1-based indexing internally.
    
    Time Complexity: O(log n) per operation
    Space Complexity: O(n)
    """
    
    def __init__(self, n: int):
        """Initialize BIT of size n (1-indexed: 1 to n)."""
        self.n = n
        self.tree = [0] * (n + 1)
    
    @classmethod
    def from_array(cls, arr: List[int]) -> 'FenwickTree':
        """Build BIT from array in O(n) time."""
        n = len(arr)
        bit = cls(n)
        
        # Copy array to tree (1-indexed)
        for i in range(n):
            bit.tree[i + 1] = arr[i]
        
        # Transform to BIT in O(n)
        for i in range(1, n + 1):
            parent = i + (i & -i)
            if parent <= n:
                bit.tree[parent] += bit.tree[i]
        
        return bit
    
    def update(self, i: int, delta: int) -> None:
        """Add delta to element at index i (1-indexed)."""
        while i <= self.n:
            self.tree[i] += delta
            i += i & (-i)  # Move to parent
    
    def prefix_sum(self, i: int) -> int:
        """Return sum of elements [1, i] (1-indexed)."""
        total = 0
        while i > 0:
            total += self.tree[i]
            i -= i & (-i)  # Move to previous responsible node
        return total
    
    def range_sum(self, left: int, right: int) -> int:
        """Return sum of elements [left, right] (1-indexed)."""
        return self.prefix_sum(right) - self.prefix_sum(left - 1)
    
    def point_query(self, i: int) -> int:
        """Return value at index i (1-indexed)."""
        return self.range_sum(i, i)


# Example usage
if __name__ == "__main__":
    # Build from array
    arr = [3, 2, -1, 6, 5, 4, -3, 3]
    bit = FenwickTree.from_array(arr)
    
    print(f"Prefix sum [1, 4]: {bit.prefix_sum(4)}")  # 3+2-1+6 = 10
    print(f"Range sum [3, 6]: {bit.range_sum(3, 6)}")  # -1+6+5+4 = 14
    
    # Update: add 5 to index 3
    bit.update(3, 5)
    print(f"Range sum [3, 6] after update: {bit.range_sum(3, 6)}")  # 4+6+5+4 = 19
```

### 0-indexed Fenwick Tree

```python
class FenwickTree0:
    """
    0-indexed Fenwick Tree for more intuitive Python usage.
    """
    
    def __init__(self, n: int):
        self.n = n
        self.tree = [0] * n
    
    @classmethod
    def from_array(cls, arr: List[int]) -> 'FenwickTree0':
        n = len(arr)
        bit = cls(n)
        bit.tree = arr.copy()
        
        for i in range(n):
            parent = i | (i + 1)  # Equivalent to i + lowbit(i+1) - 1 for 0-indexed
            if parent < n:
                bit.tree[parent] += bit.tree[i]
        
        return bit
    
    def update(self, i: int, delta: int) -> None:
        """Add delta to element at index i (0-indexed)."""
        while i < self.n:
            self.tree[i] += delta
            i |= (i + 1)  # Move to parent
    
    def prefix_sum(self, i: int) -> int:
        """Return sum of elements [0, i] (0-indexed, inclusive)."""
        total = 0
        while i >= 0:
            total += self.tree[i]
            i = (i & (i + 1)) - 1  # Move to previous responsible node
        return total
    
    def range_sum(self, left: int, right: int) -> int:
        """Return sum of elements [left, right] (0-indexed)."""
        if left == 0:
            return self.prefix_sum(right)
        return self.prefix_sum(right) - self.prefix_sum(left - 1)
```

---

## 4. Range Update, Point Query

To support **range updates** and **point queries**, we use a difference array technique.

```python
class FenwickTreeRangeUpdate:
    """
    Fenwick Tree supporting:
        - Range update: Add value to all elements in [left, right]
        - Point query: Get value at index i
    
    Uses difference array concept:
        If d[i] = a[i] - a[i-1], then a[i] = sum(d[1..i])
        To add v to range [l, r]: d[l] += v, d[r+1] -= v
    """
    
    def __init__(self, n: int):
        self.n = n
        self.tree = [0] * (n + 2)  # Extra space for safety
    
    def _update(self, i: int, delta: int) -> None:
        while i <= self.n:
            self.tree[i] += delta
            i += i & (-i)
    
    def _prefix_sum(self, i: int) -> int:
        total = 0
        while i > 0:
            total += self.tree[i]
            i -= i & (-i)
        return total
    
    def range_update(self, left: int, right: int, val: int) -> None:
        """Add val to all elements in range [left, right] (1-indexed)."""
        self._update(left, val)
        self._update(right + 1, -val)
    
    def point_query(self, i: int) -> int:
        """Get value at index i (1-indexed)."""
        return self._prefix_sum(i)


# Example
bit = FenwickTreeRangeUpdate(8)
bit.range_update(2, 5, 10)  # Add 10 to indices 2, 3, 4, 5
bit.range_update(4, 7, 5)   # Add 5 to indices 4, 5, 6, 7

print(f"Value at index 1: {bit.point_query(1)}")  # 0
print(f"Value at index 3: {bit.point_query(3)}")  # 10
print(f"Value at index 5: {bit.point_query(5)}")  # 15
print(f"Value at index 6: {bit.point_query(6)}")  # 5
```

---

## 5. Range Update, Range Query

For both **range updates** AND **range queries**, we use two BITs.

```python
class FenwickTreeRangeAll:
    """
    Fenwick Tree supporting:
        - Range update: Add value to all elements in [left, right]
        - Range query: Sum of elements in [left, right]
    
    Mathematical insight:
    If we add v to range [l, r], then:
        prefix_sum(i) changes by:
            - 0 if i < l
            - v * (i - l + 1) if l <= i <= r
            - v * (r - l + 1) if i > r
    
    We can express this using two BITs:
        prefix_sum(i) = BIT1[i] * i - BIT2[i]
    
    For range add [l, r, v]:
        BIT1[l] += v, BIT1[r+1] -= v
        BIT2[l] += v * (l-1), BIT2[r+1] -= v * r
    """
    
    def __init__(self, n: int):
        self.n = n
        self.bit1 = [0] * (n + 2)
        self.bit2 = [0] * (n + 2)
    
    def _update(self, bit: List[int], i: int, delta: int) -> None:
        while i <= self.n:
            bit[i] += delta
            i += i & (-i)
    
    def _prefix_sum(self, bit: List[int], i: int) -> int:
        total = 0
        while i > 0:
            total += bit[i]
            i -= i & (-i)
        return total
    
    def range_update(self, left: int, right: int, val: int) -> None:
        """Add val to all elements in range [left, right] (1-indexed)."""
        self._update(self.bit1, left, val)
        self._update(self.bit1, right + 1, -val)
        self._update(self.bit2, left, val * (left - 1))
        self._update(self.bit2, right + 1, -val * right)
    
    def prefix_sum(self, i: int) -> int:
        """Return sum of elements [1, i] (1-indexed)."""
        return self._prefix_sum(self.bit1, i) * i - self._prefix_sum(self.bit2, i)
    
    def range_sum(self, left: int, right: int) -> int:
        """Return sum of elements [left, right] (1-indexed)."""
        return self.prefix_sum(right) - self.prefix_sum(left - 1)


# Example
bit = FenwickTreeRangeAll(8)
bit.range_update(2, 5, 10)  # Add 10 to indices 2-5
# Array: [0, 10, 10, 10, 10, 0, 0, 0]

print(f"Sum [1, 4]: {bit.range_sum(1, 4)}")  # 30
print(f"Sum [3, 6]: {bit.range_sum(3, 6)}")  # 30

bit.range_update(4, 7, 5)   # Add 5 to indices 4-7
# Array: [0, 10, 10, 15, 15, 5, 5, 0]

print(f"Sum [1, 8]: {bit.range_sum(1, 8)}")  # 60
```

---

## 6. 2D Fenwick Tree

```python
class FenwickTree2D:
    """
    2D Fenwick Tree for 2D prefix sum queries.
    
    Time Complexity: O(log n * log m) per operation
    Space Complexity: O(n * m)
    """
    
    def __init__(self, n: int, m: int):
        self.n = n
        self.m = m
        self.tree = [[0] * (m + 1) for _ in range(n + 1)]
    
    def update(self, x: int, y: int, delta: int) -> None:
        """Add delta to element at (x, y) (1-indexed)."""
        i = x
        while i <= self.n:
            j = y
            while j <= self.m:
                self.tree[i][j] += delta
                j += j & (-j)
            i += i & (-i)
    
    def prefix_sum(self, x: int, y: int) -> int:
        """Return sum of elements in rectangle [(1,1), (x,y)]."""
        total = 0
        i = x
        while i > 0:
            j = y
            while j > 0:
                total += self.tree[i][j]
                j -= j & (-j)
            i -= i & (-i)
        return total
    
    def range_sum(self, x1: int, y1: int, x2: int, y2: int) -> int:
        """Return sum of elements in rectangle [(x1,y1), (x2,y2)]."""
        return (self.prefix_sum(x2, y2)
                - self.prefix_sum(x1 - 1, y2)
                - self.prefix_sum(x2, y1 - 1)
                + self.prefix_sum(x1 - 1, y1 - 1))


# Example
bit2d = FenwickTree2D(4, 4)
matrix = [
    [1, 2, 3, 4],
    [5, 6, 7, 8],
    [9, 10, 11, 12],
    [13, 14, 15, 16]
]

# Build
for i in range(4):
    for j in range(4):
        bit2d.update(i + 1, j + 1, matrix[i][j])

# Query
print(f"Sum of [(1,1), (2,2)]: {bit2d.range_sum(1, 1, 2, 2)}")  # 1+2+5+6 = 14
print(f"Sum of [(2,2), (3,3)]: {bit2d.range_sum(2, 2, 3, 3)}")  # 6+7+10+11 = 34
```

---

## 7. Applications

### 7.1 Count Inversions

```python
def count_inversions(arr: List[int]) -> int:
    """
    Count inversions using Fenwick Tree.
    Inversion: pair (i, j) where i < j but arr[i] > arr[j]
    
    Time: O(n log n)
    Space: O(max element) or O(n) with coordinate compression
    """
    if not arr:
        return 0
    
    # Coordinate compression
    sorted_unique = sorted(set(arr))
    rank = {v: i + 1 for i, v in enumerate(sorted_unique)}
    
    n = len(sorted_unique)
    bit = FenwickTree(n)
    inversions = 0
    
    # Process from right to left
    for i in range(len(arr) - 1, -1, -1):
        r = rank[arr[i]]
        # Count elements smaller than arr[i] seen so far (to the right)
        inversions += bit.prefix_sum(r - 1)
        bit.update(r, 1)
    
    return inversions


# Example
arr = [8, 4, 2, 1]
print(f"Inversions in {arr}: {count_inversions(arr)}")  # 6
# Pairs: (8,4), (8,2), (8,1), (4,2), (4,1), (2,1)
```

### 7.2 Count Smaller Numbers After Self (LC 315)

```python
def count_smaller(nums: List[int]) -> List[int]:
    """
    For each element, count elements smaller to its right.
    
    Time: O(n log n)
    Space: O(n)
    """
    if not nums:
        return []
    
    # Coordinate compression
    sorted_unique = sorted(set(nums))
    rank = {v: i + 1 for i, v in enumerate(sorted_unique)}
    
    n = len(sorted_unique)
    bit = FenwickTree(n)
    result = []
    
    # Process from right to left
    for i in range(len(nums) - 1, -1, -1):
        r = rank[nums[i]]
        result.append(bit.prefix_sum(r - 1))
        bit.update(r, 1)
    
    return result[::-1]


# Example
nums = [5, 2, 6, 1]
print(f"Smaller numbers after self: {count_smaller(nums)}")  # [2, 1, 1, 0]
```

### 7.3 Range Frequency Query

```python
class RangeFrequencyQuery:
    """
    Answer queries: "How many times does value appear in range [left, right]?"
    
    Approach: For each unique value, maintain a BIT of its positions.
    
    Time: O(n log n) build, O(log n) per query
    Space: O(n)
    """
    
    def __init__(self, arr: List[int]):
        self.n = len(arr)
        from collections import defaultdict
        
        # For each value, store sorted list of its positions
        self.positions = defaultdict(list)
        for i, val in enumerate(arr):
            self.positions[val].append(i)
    
    def query(self, left: int, right: int, value: int) -> int:
        """Count occurrences of value in [left, right]."""
        import bisect
        
        pos = self.positions[value]
        return bisect.bisect_right(pos, right) - bisect.bisect_left(pos, left)


# Example
arr = [1, 2, 1, 2, 1, 3, 1]
rfq = RangeFrequencyQuery(arr)
print(f"Count of 1 in [0, 4]: {rfq.query(0, 4, 1)}")  # 3
print(f"Count of 2 in [1, 5]: {rfq.query(1, 5, 2)}")  # 2
```

### 7.4 Order Statistics (Kth Smallest)

```python
class OrderStatisticsBIT:
    """
    Support operations:
        - Insert value
        - Delete value
        - Find kth smallest
    
    Time: O(log n) per operation (with coordinate compression)
    """
    
    def __init__(self, max_val: int):
        self.n = max_val
        self.tree = [0] * (self.n + 1)
    
    def _update(self, i: int, delta: int) -> None:
        while i <= self.n:
            self.tree[i] += delta
            i += i & (-i)
    
    def _prefix_sum(self, i: int) -> int:
        total = 0
        while i > 0:
            total += self.tree[i]
            i -= i & (-i)
        return total
    
    def insert(self, val: int) -> None:
        """Insert value (1-indexed)."""
        self._update(val, 1)
    
    def delete(self, val: int) -> None:
        """Delete value (1-indexed)."""
        self._update(val, -1)
    
    def kth_smallest(self, k: int) -> int:
        """Find kth smallest value using binary search on BIT."""
        pos = 0
        cur_sum = 0
        
        # Binary search
        log = self.n.bit_length()
        for i in range(log, -1, -1):
            if pos + (1 << i) <= self.n and cur_sum + self.tree[pos + (1 << i)] < k:
                pos += (1 << i)
                cur_sum += self.tree[pos]
        
        return pos + 1


# Example
bit = OrderStatisticsBIT(100)
for val in [5, 2, 8, 3, 9, 1]:
    bit.insert(val)

print(f"1st smallest: {bit.kth_smallest(1)}")  # 1
print(f"3rd smallest: {bit.kth_smallest(3)}")  # 3
print(f"5th smallest: {bit.kth_smallest(5)}")  # 8
```

---

## 8. Comparison with Segment Tree

| Feature | Fenwick Tree | Segment Tree |
|---------|--------------|--------------|
| Implementation | ~15 lines | ~50 lines |
| Space | O(n) | O(4n) |
| Point Update | O(log n) | O(log n) |
| Range Query (prefix) | O(log n) | O(log n) |
| Range Query (arbitrary) | O(2 log n) | O(log n) |
| Range Update | Needs 2 BITs | With lazy prop |
| Operations | Sum, XOR | Any associative |
| Cache Performance | Better | Worse |

**When to choose Fenwick Tree:**
- Only need prefix sums or can reduce to prefix sums
- Memory is tight
- Simpler implementation preferred
- Sum/XOR operations only

**When to choose Segment Tree:**
- Need arbitrary range queries (min, max)
- Need lazy propagation
- Need more complex merge operations
- Need persistence

---

## 9. LeetCode Problems

| # | Problem | Difficulty | Key Technique |
|---|---------|------------|---------------|
| 307 | Range Sum Query - Mutable | Medium | Basic BIT |
| 315 | Count of Smaller Numbers After Self | Hard | BIT + Coord Compression |
| 327 | Count of Range Sum | Hard | BIT + Merge Sort |
| 493 | Reverse Pairs | Hard | BIT / Merge Sort |
| 1409 | Queries on Permutation with Key | Medium | BIT for order |
| 1649 | Create Sorted Array through Instructions | Hard | BIT + Coord Compression |
| 2179 | Count Good Triplets in Array | Hard | BIT |
| 2426 | Number of Pairs Satisfying Inequality | Hard | BIT / Merge Sort |
| 308 | Range Sum Query 2D - Mutable | Hard | 2D BIT |

---

## 10. Interview Tips

### Quick Implementation Checklist
1. **1-indexed or 0-indexed?** - Decide upfront, 1-indexed is cleaner
2. **Array size** - Use `n + 1` for 1-indexed
3. **Lowbit operation** - `i & (-i)`
4. **Update direction** - Upward: `i += i & (-i)`
5. **Query direction** - Downward: `i -= i & (-i)`

### Common Patterns
1. **Inversion counting** - Process right-to-left, query prefix
2. **Rank queries** - Coordinate compression + BIT
3. **2D problems** - Nested BITs
4. **Range update** - Difference array technique

### Red Flags (Use Segment Tree Instead)
- Need min/max queries
- Need non-invertible operations
- Need lazy propagation
- Need persistence

---

## 11. References

1. Peter Fenwick's Original Paper (1994)
2. CP-Algorithms: https://cp-algorithms.com/data_structures/fenwick.html
3. TopCoder Tutorial on BIT
4. Competitive Programming 3 - Steven Halim
