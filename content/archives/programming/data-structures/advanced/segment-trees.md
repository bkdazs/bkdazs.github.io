---
title: "Segment Trees"
weight: 12
---

# Segment Trees

---
title: Segment Trees - Range Query Data Structures
topic: Data Structures
difficulty: Hard
tags: [segment-tree, range-query, lazy-propagation, interview-critical]
status: complete
---

## 1. Summary / Quick Reference

| Property | Value |
|----------|-------|
| Build Time | O(n) |
| Point Update | O(log n) |
| Range Query | O(log n) |
| Range Update (Lazy) | O(log n) |
| Space | O(4n) ≈ O(n) |
| Use Case | Range queries with updates |

**One-liner**: A binary tree where each node stores aggregate information about a range of elements, enabling efficient range queries and updates.

---

## 2. Conceptual Foundation

### What is a Segment Tree?
A segment tree is a binary tree used for storing intervals or segments. It allows querying which segments contain a given point, or querying aggregate values over a range of elements.

### Why Use Segment Trees?
- **Range queries**: Sum, min, max, GCD, etc. over any subarray in O(log n)
- **Point/Range updates**: Modify single elements or ranges efficiently
- **Flexible**: Can handle any associative operation

### Mental Model
```
Array: [1, 3, 5, 7, 9, 11]

Segment Tree (Sum):
                  36 [0-5]
                /        \
          9 [0-2]         27 [3-5]
          /     \         /      \
      4 [0-1]  5 [2]   16 [3-4]  11 [5]
      /    \            /    \
   1 [0]  3 [1]      7 [3]  9 [4]
```

---

## 3. Basic Implementation

### Segment Tree - Sum Query

```python
from typing import List, Optional, Callable

class SegmentTree:
    """
    Segment Tree for range sum queries and point updates.
    
    Time Complexity:
        - Build: O(n)
        - Query: O(log n)
        - Update: O(log n)
    Space Complexity: O(4n)
    """
    
    def __init__(self, arr: List[int]):
        self.n = len(arr)
        self.tree = [0] * (4 * self.n)  # 4n to be safe
        if self.n > 0:
            self._build(arr, 0, 0, self.n - 1)
    
    def _build(self, arr: List[int], node: int, start: int, end: int) -> None:
        """Build the segment tree recursively."""
        if start == end:
            # Leaf node
            self.tree[node] = arr[start]
        else:
            mid = (start + end) // 2
            left_child = 2 * node + 1
            right_child = 2 * node + 2
            
            self._build(arr, left_child, start, mid)
            self._build(arr, right_child, mid + 1, end)
            
            self.tree[node] = self.tree[left_child] + self.tree[right_child]
    
    def update(self, idx: int, val: int) -> None:
        """Update element at index idx to val."""
        self._update(0, 0, self.n - 1, idx, val)
    
    def _update(self, node: int, start: int, end: int, idx: int, val: int) -> None:
        if start == end:
            self.tree[node] = val
        else:
            mid = (start + end) // 2
            left_child = 2 * node + 1
            right_child = 2 * node + 2
            
            if idx <= mid:
                self._update(left_child, start, mid, idx, val)
            else:
                self._update(right_child, mid + 1, end, idx, val)
            
            self.tree[node] = self.tree[left_child] + self.tree[right_child]
    
    def query(self, left: int, right: int) -> int:
        """Query sum in range [left, right]."""
        return self._query(0, 0, self.n - 1, left, right)
    
    def _query(self, node: int, start: int, end: int, left: int, right: int) -> int:
        # No overlap
        if right < start or left > end:
            return 0
        
        # Complete overlap
        if left <= start and end <= right:
            return self.tree[node]
        
        # Partial overlap
        mid = (start + end) // 2
        left_child = 2 * node + 1
        right_child = 2 * node + 2
        
        left_sum = self._query(left_child, start, mid, left, right)
        right_sum = self._query(right_child, mid + 1, end, left, right)
        
        return left_sum + right_sum


# Example usage
if __name__ == "__main__":
    arr = [1, 3, 5, 7, 9, 11]
    st = SegmentTree(arr)
    
    print(f"Sum of range [1, 3]: {st.query(1, 3)}")  # 3+5+7 = 15
    st.update(1, 10)  # Change arr[1] from 3 to 10
    print(f"Sum of range [1, 3] after update: {st.query(1, 3)}")  # 10+5+7 = 22
```

### Generic Segment Tree

```python
from typing import List, Callable, TypeVar, Generic

T = TypeVar('T')

class GenericSegmentTree(Generic[T]):
    """
    Generic Segment Tree supporting any associative operation.
    
    Args:
        arr: Input array
        merge: Associative binary operation (e.g., min, max, sum, gcd)
        identity: Identity element for the operation
    """
    
    def __init__(self, arr: List[T], merge: Callable[[T, T], T], identity: T):
        self.n = len(arr)
        self.merge = merge
        self.identity = identity
        self.tree = [identity] * (4 * self.n)
        if self.n > 0:
            self._build(arr, 0, 0, self.n - 1)
    
    def _build(self, arr: List[T], node: int, start: int, end: int) -> None:
        if start == end:
            self.tree[node] = arr[start]
        else:
            mid = (start + end) // 2
            left_child = 2 * node + 1
            right_child = 2 * node + 2
            
            self._build(arr, left_child, start, mid)
            self._build(arr, right_child, mid + 1, end)
            
            self.tree[node] = self.merge(self.tree[left_child], self.tree[right_child])
    
    def update(self, idx: int, val: T) -> None:
        self._update(0, 0, self.n - 1, idx, val)
    
    def _update(self, node: int, start: int, end: int, idx: int, val: T) -> None:
        if start == end:
            self.tree[node] = val
        else:
            mid = (start + end) // 2
            left_child = 2 * node + 1
            right_child = 2 * node + 2
            
            if idx <= mid:
                self._update(left_child, start, mid, idx, val)
            else:
                self._update(right_child, mid + 1, end, idx, val)
            
            self.tree[node] = self.merge(self.tree[left_child], self.tree[right_child])
    
    def query(self, left: int, right: int) -> T:
        return self._query(0, 0, self.n - 1, left, right)
    
    def _query(self, node: int, start: int, end: int, left: int, right: int) -> T:
        if right < start or left > end:
            return self.identity
        
        if left <= start and end <= right:
            return self.tree[node]
        
        mid = (start + end) // 2
        left_child = 2 * node + 1
        right_child = 2 * node + 2
        
        left_result = self._query(left_child, start, mid, left, right)
        right_result = self._query(right_child, mid + 1, end, left, right)
        
        return self.merge(left_result, right_result)


# Example: Range Minimum Query
def test_range_min():
    arr = [1, 3, 2, 7, 9, 11]
    st = GenericSegmentTree(arr, min, float('inf'))
    print(f"Min in range [1, 4]: {st.query(1, 4)}")  # min(3, 2, 7, 9) = 2

# Example: Range Maximum Query
def test_range_max():
    arr = [1, 3, 2, 7, 9, 11]
    st = GenericSegmentTree(arr, max, float('-inf'))
    print(f"Max in range [1, 4]: {st.query(1, 4)}")  # max(3, 2, 7, 9) = 9

# Example: Range GCD Query
import math
def test_range_gcd():
    arr = [12, 18, 24, 36]
    st = GenericSegmentTree(arr, math.gcd, 0)
    print(f"GCD in range [0, 3]: {st.query(0, 3)}")  # gcd(12, 18, 24, 36) = 6
```

---

## 4. Lazy Propagation

### Concept
When we need to update a **range** of elements (not just a single element), we use **lazy propagation** to defer updates until they're needed.

**Key Idea**: Instead of updating all affected nodes immediately, we store "pending updates" in a lazy array and propagate them downward only when necessary.

### Range Update with Lazy Propagation

```python
from typing import List

class LazySegmentTree:
    """
    Segment Tree with Lazy Propagation for range updates.
    
    Supports:
        - Range sum queries
        - Range addition updates
    
    Time Complexity: O(log n) for both query and range update
    Space Complexity: O(4n)
    """
    
    def __init__(self, arr: List[int]):
        self.n = len(arr)
        self.tree = [0] * (4 * self.n)
        self.lazy = [0] * (4 * self.n)  # Lazy propagation array
        if self.n > 0:
            self._build(arr, 0, 0, self.n - 1)
    
    def _build(self, arr: List[int], node: int, start: int, end: int) -> None:
        if start == end:
            self.tree[node] = arr[start]
        else:
            mid = (start + end) // 2
            left_child = 2 * node + 1
            right_child = 2 * node + 2
            
            self._build(arr, left_child, start, mid)
            self._build(arr, right_child, mid + 1, end)
            
            self.tree[node] = self.tree[left_child] + self.tree[right_child]
    
    def _push_down(self, node: int, start: int, end: int) -> None:
        """Propagate lazy updates to children."""
        if self.lazy[node] != 0:
            mid = (start + end) // 2
            left_child = 2 * node + 1
            right_child = 2 * node + 2
            
            # Apply lazy value to children
            self.tree[left_child] += self.lazy[node] * (mid - start + 1)
            self.tree[right_child] += self.lazy[node] * (end - mid)
            
            # Propagate lazy value to children's lazy
            self.lazy[left_child] += self.lazy[node]
            self.lazy[right_child] += self.lazy[node]
            
            # Clear current lazy value
            self.lazy[node] = 0
    
    def update_range(self, left: int, right: int, val: int) -> None:
        """Add val to all elements in range [left, right]."""
        self._update_range(0, 0, self.n - 1, left, right, val)
    
    def _update_range(self, node: int, start: int, end: int, 
                      left: int, right: int, val: int) -> None:
        # No overlap
        if right < start or left > end:
            return
        
        # Complete overlap
        if left <= start and end <= right:
            self.tree[node] += val * (end - start + 1)
            self.lazy[node] += val
            return
        
        # Partial overlap - push down and recurse
        self._push_down(node, start, end)
        
        mid = (start + end) // 2
        left_child = 2 * node + 1
        right_child = 2 * node + 2
        
        self._update_range(left_child, start, mid, left, right, val)
        self._update_range(right_child, mid + 1, end, left, right, val)
        
        self.tree[node] = self.tree[left_child] + self.tree[right_child]
    
    def query(self, left: int, right: int) -> int:
        """Query sum in range [left, right]."""
        return self._query(0, 0, self.n - 1, left, right)
    
    def _query(self, node: int, start: int, end: int, left: int, right: int) -> int:
        # No overlap
        if right < start or left > end:
            return 0
        
        # Complete overlap
        if left <= start and end <= right:
            return self.tree[node]
        
        # Partial overlap - push down and recurse
        self._push_down(node, start, end)
        
        mid = (start + end) // 2
        left_child = 2 * node + 1
        right_child = 2 * node + 2
        
        left_sum = self._query(left_child, start, mid, left, right)
        right_sum = self._query(right_child, mid + 1, end, left, right)
        
        return left_sum + right_sum


# Example usage
if __name__ == "__main__":
    arr = [1, 3, 5, 7, 9, 11]
    st = LazySegmentTree(arr)
    
    print(f"Initial sum [0, 5]: {st.query(0, 5)}")  # 36
    st.update_range(1, 4, 10)  # Add 10 to indices 1, 2, 3, 4
    print(f"After adding 10 to [1,4]: {st.query(0, 5)}")  # 36 + 40 = 76
    print(f"Sum of [2, 3]: {st.query(2, 3)}")  # (5+10) + (7+10) = 32
```

### Range Assignment with Lazy Propagation

```python
class LazySegmentTreeAssign:
    """
    Segment Tree with Lazy Propagation for range assignment.
    
    Supports:
        - Range sum queries
        - Range assignment updates (set all elements in range to value)
    """
    
    def __init__(self, arr: List[int]):
        self.n = len(arr)
        self.tree = [0] * (4 * self.n)
        self.lazy = [None] * (4 * self.n)  # None means no pending assignment
        if self.n > 0:
            self._build(arr, 0, 0, self.n - 1)
    
    def _build(self, arr: List[int], node: int, start: int, end: int) -> None:
        if start == end:
            self.tree[node] = arr[start]
        else:
            mid = (start + end) // 2
            left_child = 2 * node + 1
            right_child = 2 * node + 2
            
            self._build(arr, left_child, start, mid)
            self._build(arr, right_child, mid + 1, end)
            
            self.tree[node] = self.tree[left_child] + self.tree[right_child]
    
    def _push_down(self, node: int, start: int, end: int) -> None:
        """Propagate lazy assignment to children."""
        if self.lazy[node] is not None:
            mid = (start + end) // 2
            left_child = 2 * node + 1
            right_child = 2 * node + 2
            
            # Apply assignment to children
            self.tree[left_child] = self.lazy[node] * (mid - start + 1)
            self.tree[right_child] = self.lazy[node] * (end - mid)
            
            # Set children's lazy value
            self.lazy[left_child] = self.lazy[node]
            self.lazy[right_child] = self.lazy[node]
            
            # Clear current lazy value
            self.lazy[node] = None
    
    def assign_range(self, left: int, right: int, val: int) -> None:
        """Set all elements in range [left, right] to val."""
        self._assign_range(0, 0, self.n - 1, left, right, val)
    
    def _assign_range(self, node: int, start: int, end: int,
                      left: int, right: int, val: int) -> None:
        if right < start or left > end:
            return
        
        if left <= start and end <= right:
            self.tree[node] = val * (end - start + 1)
            self.lazy[node] = val
            return
        
        self._push_down(node, start, end)
        
        mid = (start + end) // 2
        left_child = 2 * node + 1
        right_child = 2 * node + 2
        
        self._assign_range(left_child, start, mid, left, right, val)
        self._assign_range(right_child, mid + 1, end, left, right, val)
        
        self.tree[node] = self.tree[left_child] + self.tree[right_child]
    
    def query(self, left: int, right: int) -> int:
        return self._query(0, 0, self.n - 1, left, right)
    
    def _query(self, node: int, start: int, end: int, left: int, right: int) -> int:
        if right < start or left > end:
            return 0
        
        if left <= start and end <= right:
            return self.tree[node]
        
        self._push_down(node, start, end)
        
        mid = (start + end) // 2
        left_child = 2 * node + 1
        right_child = 2 * node + 2
        
        return (self._query(left_child, start, mid, left, right) +
                self._query(right_child, mid + 1, end, left, right))
```

---

## 5. Iterative Segment Tree (Efficient)

```python
class IterativeSegmentTree:
    """
    Iterative (bottom-up) Segment Tree.
    More cache-friendly and faster in practice.
    Uses 2n space instead of 4n.
    """
    
    def __init__(self, arr: List[int]):
        self.n = len(arr)
        self.tree = [0] * (2 * self.n)
        
        # Initialize leaves
        for i in range(self.n):
            self.tree[self.n + i] = arr[i]
        
        # Build internal nodes
        for i in range(self.n - 1, 0, -1):
            self.tree[i] = self.tree[2 * i] + self.tree[2 * i + 1]
    
    def update(self, idx: int, val: int) -> None:
        """Update element at index idx to val."""
        # Update leaf
        pos = idx + self.n
        self.tree[pos] = val
        
        # Update ancestors
        while pos > 1:
            pos //= 2
            self.tree[pos] = self.tree[2 * pos] + self.tree[2 * pos + 1]
    
    def query(self, left: int, right: int) -> int:
        """Query sum in range [left, right]."""
        result = 0
        left += self.n
        right += self.n + 1  # Make right exclusive
        
        while left < right:
            if left & 1:  # left is right child
                result += self.tree[left]
                left += 1
            if right & 1:  # right is right child
                right -= 1
                result += self.tree[right]
            left //= 2
            right //= 2
        
        return result


# Example usage
if __name__ == "__main__":
    arr = [1, 3, 5, 7, 9, 11]
    st = IterativeSegmentTree(arr)
    
    print(f"Sum of range [1, 3]: {st.query(1, 3)}")  # 15
    st.update(2, 10)  # Change arr[2] from 5 to 10
    print(f"Sum of range [1, 3] after update: {st.query(1, 3)}")  # 20
```

---

## 6. Advanced Applications

### 6.1 Range Maximum Subarray Sum (Kadane + Segment Tree)

```python
from typing import NamedTuple

class Node(NamedTuple):
    total: int      # Total sum of segment
    prefix: int     # Maximum prefix sum
    suffix: int     # Maximum suffix sum
    max_sum: int    # Maximum subarray sum in segment

class MaxSubarraySumTree:
    """
    Segment Tree for range maximum subarray sum query.
    
    For each node, we store:
    - Total sum of the segment
    - Maximum prefix sum
    - Maximum suffix sum
    - Maximum subarray sum
    """
    
    def __init__(self, arr: List[int]):
        self.n = len(arr)
        self.tree = [Node(0, 0, 0, 0)] * (4 * self.n)
        if self.n > 0:
            self._build(arr, 0, 0, self.n - 1)
    
    def _make_leaf(self, val: int) -> Node:
        return Node(val, val, val, val)
    
    def _merge(self, left: Node, right: Node) -> Node:
        total = left.total + right.total
        prefix = max(left.prefix, left.total + right.prefix)
        suffix = max(right.suffix, right.total + left.suffix)
        max_sum = max(left.max_sum, right.max_sum, left.suffix + right.prefix)
        return Node(total, prefix, suffix, max_sum)
    
    def _build(self, arr: List[int], node: int, start: int, end: int) -> None:
        if start == end:
            self.tree[node] = self._make_leaf(arr[start])
        else:
            mid = (start + end) // 2
            left_child = 2 * node + 1
            right_child = 2 * node + 2
            
            self._build(arr, left_child, start, mid)
            self._build(arr, right_child, mid + 1, end)
            
            self.tree[node] = self._merge(self.tree[left_child], self.tree[right_child])
    
    def query(self, left: int, right: int) -> int:
        """Return maximum subarray sum in range [left, right]."""
        node = self._query(0, 0, self.n - 1, left, right)
        return node.max_sum
    
    def _query(self, node: int, start: int, end: int, left: int, right: int) -> Node:
        if right < start or left > end:
            return Node(0, float('-inf'), float('-inf'), float('-inf'))
        
        if left <= start and end <= right:
            return self.tree[node]
        
        mid = (start + end) // 2
        left_child = 2 * node + 1
        right_child = 2 * node + 2
        
        left_result = self._query(left_child, start, mid, left, right)
        right_result = self._query(right_child, mid + 1, end, left, right)
        
        return self._merge(left_result, right_result)


# Example
arr = [-2, 1, -3, 4, -1, 2, 1, -5, 4]
tree = MaxSubarraySumTree(arr)
print(f"Max subarray sum in [0, 8]: {tree.query(0, 8)}")  # 6 (subarray [4, -1, 2, 1])
print(f"Max subarray sum in [3, 6]: {tree.query(3, 6)}")  # 6 (subarray [4, -1, 2, 1])
```

### 6.2 Persistent Segment Tree

```python
class PersistentSegmentTree:
    """
    Persistent Segment Tree - maintains history of all versions.
    
    Use case: Query on historical versions of the array.
    
    Time: O(log n) per operation
    Space: O(n + m log n) where m is number of updates
    """
    
    def __init__(self, arr: List[int]):
        self.n = len(arr)
        self.nodes = []  # [(left_child, right_child, value)]
        self.roots = []  # Root of each version
        
        if self.n > 0:
            root = self._build(arr, 0, self.n - 1)
            self.roots.append(root)
    
    def _new_node(self, left: int, right: int, value: int) -> int:
        """Create a new node and return its index."""
        self.nodes.append([left, right, value])
        return len(self.nodes) - 1
    
    def _build(self, arr: List[int], start: int, end: int) -> int:
        if start == end:
            return self._new_node(-1, -1, arr[start])
        
        mid = (start + end) // 2
        left_child = self._build(arr, start, mid)
        right_child = self._build(arr, mid + 1, end)
        value = self.nodes[left_child][2] + self.nodes[right_child][2]
        
        return self._new_node(left_child, right_child, value)
    
    def update(self, version: int, idx: int, val: int) -> int:
        """
        Update index idx to val based on version.
        Returns new version number.
        """
        new_root = self._update(self.roots[version], 0, self.n - 1, idx, val)
        self.roots.append(new_root)
        return len(self.roots) - 1
    
    def _update(self, node: int, start: int, end: int, idx: int, val: int) -> int:
        if start == end:
            return self._new_node(-1, -1, val)
        
        mid = (start + end) // 2
        left_child, right_child, _ = self.nodes[node]
        
        if idx <= mid:
            new_left = self._update(left_child, start, mid, idx, val)
            new_right = right_child
        else:
            new_left = left_child
            new_right = self._update(right_child, mid + 1, end, idx, val)
        
        new_value = self.nodes[new_left][2] + self.nodes[new_right][2]
        return self._new_node(new_left, new_right, new_value)
    
    def query(self, version: int, left: int, right: int) -> int:
        """Query sum in range [left, right] for given version."""
        return self._query(self.roots[version], 0, self.n - 1, left, right)
    
    def _query(self, node: int, start: int, end: int, left: int, right: int) -> int:
        if right < start or left > end:
            return 0
        
        if left <= start and end <= right:
            return self.nodes[node][2]
        
        mid = (start + end) // 2
        left_child, right_child, _ = self.nodes[node]
        
        return (self._query(left_child, start, mid, left, right) +
                self._query(right_child, mid + 1, end, left, right))


# Example: Time travel queries
arr = [1, 2, 3, 4, 5]
pst = PersistentSegmentTree(arr)

# Version 0: [1, 2, 3, 4, 5]
print(f"v0 sum [0, 4]: {pst.query(0, 0, 4)}")  # 15

# Version 1: [1, 10, 3, 4, 5]
v1 = pst.update(0, 1, 10)
print(f"v1 sum [0, 4]: {pst.query(v1, 0, 4)}")  # 23

# Can still query old version
print(f"v0 sum [0, 4] (unchanged): {pst.query(0, 0, 4)}")  # 15
```

---

## 7. 2D Segment Tree

```python
class SegmentTree2D:
    """
    2D Segment Tree for 2D range sum queries.
    
    Time: O(log n * log m) per query/update
    Space: O(4n * 4m)
    """
    
    def __init__(self, matrix: List[List[int]]):
        if not matrix or not matrix[0]:
            return
        
        self.n = len(matrix)
        self.m = len(matrix[0])
        self.tree = [[0] * (4 * self.m) for _ in range(4 * self.n)]
        self._build_x(matrix, 0, 0, self.n - 1)
    
    def _build_x(self, matrix: List[List[int]], vx: int, tlx: int, trx: int) -> None:
        if tlx == trx:
            self._build_y(matrix[tlx], vx, 0, 0, self.m - 1)
        else:
            mid = (tlx + trx) // 2
            self._build_x(matrix, 2 * vx + 1, tlx, mid)
            self._build_x(matrix, 2 * vx + 2, mid + 1, trx)
            
            # Merge y-trees
            for i in range(4 * self.m):
                self.tree[vx][i] = self.tree[2 * vx + 1][i] + self.tree[2 * vx + 2][i]
    
    def _build_y(self, row: List[int], vx: int, vy: int, tly: int, try_: int) -> None:
        if tly == try_:
            self.tree[vx][vy] = row[tly]
        else:
            mid = (tly + try_) // 2
            self._build_y(row, vx, 2 * vy + 1, tly, mid)
            self._build_y(row, vx, 2 * vy + 2, mid + 1, try_)
            self.tree[vx][vy] = self.tree[vx][2 * vy + 1] + self.tree[vx][2 * vy + 2]
    
    def query(self, x1: int, y1: int, x2: int, y2: int) -> int:
        """Query sum in rectangle [(x1, y1), (x2, y2)]."""
        return self._query_x(0, 0, self.n - 1, x1, x2, y1, y2)
    
    def _query_x(self, vx: int, tlx: int, trx: int, x1: int, x2: int, y1: int, y2: int) -> int:
        if x1 > trx or x2 < tlx:
            return 0
        
        if x1 <= tlx and trx <= x2:
            return self._query_y(vx, 0, 0, self.m - 1, y1, y2)
        
        mid = (tlx + trx) // 2
        return (self._query_x(2 * vx + 1, tlx, mid, x1, x2, y1, y2) +
                self._query_x(2 * vx + 2, mid + 1, trx, x1, x2, y1, y2))
    
    def _query_y(self, vx: int, vy: int, tly: int, try_: int, y1: int, y2: int) -> int:
        if y1 > try_ or y2 < tly:
            return 0
        
        if y1 <= tly and try_ <= y2:
            return self.tree[vx][vy]
        
        mid = (tly + try_) // 2
        return (self._query_y(vx, 2 * vy + 1, tly, mid, y1, y2) +
                self._query_y(vx, 2 * vy + 2, mid + 1, try_, y1, y2))
```

---

## 8. LeetCode Problems

| # | Problem | Difficulty | Key Technique |
|---|---------|------------|---------------|
| 307 | Range Sum Query - Mutable | Medium | Basic Segment Tree |
| 303 | Range Sum Query - Immutable | Easy | Prefix Sum (compare) |
| 304 | Range Sum Query 2D - Immutable | Medium | 2D Prefix Sum |
| 308 | Range Sum Query 2D - Mutable | Hard | 2D Segment Tree |
| 315 | Count of Smaller Numbers After Self | Hard | Segment Tree |
| 327 | Count of Range Sum | Hard | Segment Tree / Merge Sort |
| 493 | Reverse Pairs | Hard | Segment Tree / BIT |
| 699 | Falling Squares | Hard | Coordinate Compression + Segment Tree |
| 715 | Range Module | Hard | Segment Tree with Intervals |
| 732 | My Calendar III | Hard | Segment Tree |
| 850 | Rectangle Area II | Hard | Line Sweep + Segment Tree |
| 1157 | Online Majority Element In Subarray | Hard | Segment Tree + Randomization |
| 1649 | Create Sorted Array through Instructions | Hard | Segment Tree / BIT |
| 2286 | Booking Concert Tickets in Groups | Hard | Segment Tree |

---

## 9. Interview Tips

### When to Use Segment Trees
1. Range queries with **updates** (not just read-only)
2. Need O(log n) for both query and update
3. Associative operations: sum, min, max, GCD, XOR, AND, OR

### When NOT to Use
1. Read-only queries → Prefix sum is simpler
2. Only point queries → Direct array access
3. Simple cumulative queries → BIT (Fenwick) is simpler

### Common Mistakes
1. Off-by-one errors in range boundaries
2. Forgetting to push down lazy values
3. Using wrong array size (use 4n to be safe)
4. Not handling empty ranges

### Complexity Comparison

| Data Structure | Build | Point Update | Range Update | Range Query |
|----------------|-------|--------------|--------------|-------------|
| Array | O(n) | O(1) | O(n) | O(n) |
| Prefix Sum | O(n) | O(n) | O(n) | O(1) |
| Segment Tree | O(n) | O(log n) | O(n) | O(log n) |
| Lazy Segment Tree | O(n) | O(log n) | O(log n) | O(log n) |
| Fenwick Tree (BIT) | O(n) | O(log n) | O(log n)* | O(log n) |

*BIT range update requires difference array technique

---

## 10. References

1. CP-Algorithms: https://cp-algorithms.com/data_structures/segment_tree.html
2. CLRS Chapter 14 - Augmenting Data Structures
3. Competitive Programming 3 - Steven Halim
4. Codeforces EDU: https://codeforces.com/edu/course/2/lesson/4
