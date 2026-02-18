---
title: "Arrays"
topic: "Data Structures"
difficulty: "Fundamental"
tags: ["arrays", "dynamic-arrays", "contiguous-memory", "random-access"]
status: "complete"
weight: 2
---

# Arrays

## Short Summary

An **array** is a contiguous block of memory storing elements of the same type, providing O(1) random access by index. Arrays are the most fundamental data structure, forming the basis for many others (heaps, hash tables, dynamic arrays). In interviews, arrays test your ability to manipulate indices, handle edge cases, and optimize space/time tradeoffs.

---

## Why It Matters in Interviews

- **Most Common:** 40%+ of coding problems involve array manipulation
- **Foundation:** Understanding arrays is prerequisite for strings, matrices, heaps, and more
- **Pattern Recognition:** Many techniques (two-pointer, sliding window, prefix sum) apply to arrays

---

## Formal Definition / Properties / Invariants

### Static Array
- **Fixed size** determined at creation
- **Contiguous memory** allocation
- **Homogeneous** elements (same type/size)
- **Zero-indexed** (typically)

### Dynamic Array (Python `list`, Java `ArrayList`, C++ `vector`)
- **Resizable** with amortized O(1) append
- **Capacity** vs **Size**: capacity ≥ size
- **Growth factor**: typically 1.5x or 2x on resize

### Key Invariants
```
0 ≤ index < length           # Valid index range
array[i] accessible in O(1)  # Random access
```

---

## Time & Space Complexity

| Operation | Static Array | Dynamic Array | Notes |
|-----------|--------------|---------------|-------|
| Access by index | O(1) | O(1) | Direct memory offset |
| Search (unsorted) | O(n) | O(n) | Linear scan |
| Search (sorted) | O(log n) | O(log n) | Binary search |
| Insert at end | N/A | O(1)* | *Amortized |
| Insert at index | O(n) | O(n) | Shift elements |
| Delete at end | N/A | O(1) | No shift needed |
| Delete at index | O(n) | O(n) | Shift elements |
| Resize | N/A | O(n) | Copy to new array |

**Space Complexity:** O(n) where n = number of elements

### Amortized Analysis for Dynamic Array

When array is full, allocate new array of size 2n and copy elements:
- Individual resize: O(n)
- Amortized over n insertions: O(1) per insert

**Proof Sketch:**
- Cost of n insertions = n (individual inserts) + n (total copies across all resizes)
- Total = 2n → O(1) amortized per operation

---

## Typical Operations & Pseudo-Implementation

### Python Implementation with Type Hints

```python
from typing import TypeVar, Generic, List, Optional, Iterator

T = TypeVar('T')

class DynamicArray(Generic[T]):
    """
    Dynamic array implementation with amortized O(1) append.
    
    >>> arr = DynamicArray[int]()
    >>> arr.append(1)
    >>> arr.append(2)
    >>> arr.append(3)
    >>> len(arr)
    3
    >>> arr[1]
    2
    >>> arr[-1]
    3
    >>> arr[1] = 10
    >>> arr[1]
    10
    >>> arr.pop()
    3
    >>> len(arr)
    2
    """
    
    INITIAL_CAPACITY = 4
    GROWTH_FACTOR = 2
    
    def __init__(self):
        self._capacity: int = self.INITIAL_CAPACITY
        self._size: int = 0
        self._data: List[Optional[T]] = [None] * self._capacity
    
    def __len__(self) -> int:
        """Return number of elements. O(1)"""
        return self._size
    
    def __getitem__(self, index: int) -> T:
        """Get element at index. Supports negative indexing. O(1)"""
        if index < 0:
            index = self._size + index
        if not 0 <= index < self._size:
            raise IndexError(f"Index {index} out of bounds for size {self._size}")
        return self._data[index]  # type: ignore
    
    def __setitem__(self, index: int, value: T) -> None:
        """Set element at index. O(1)"""
        if index < 0:
            index = self._size + index
        if not 0 <= index < self._size:
            raise IndexError(f"Index {index} out of bounds for size {self._size}")
        self._data[index] = value
    
    def append(self, value: T) -> None:
        """Add element at end. O(1) amortized."""
        if self._size == self._capacity:
            self._resize(self._capacity * self.GROWTH_FACTOR)
        self._data[self._size] = value
        self._size += 1
    
    def pop(self) -> T:
        """Remove and return last element. O(1)"""
        if self._size == 0:
            raise IndexError("Pop from empty array")
        self._size -= 1
        value = self._data[self._size]
        self._data[self._size] = None  # Help garbage collection
        # Optionally shrink if too empty
        if self._size < self._capacity // 4 and self._capacity > self.INITIAL_CAPACITY:
            self._resize(self._capacity // 2)
        return value  # type: ignore
    
    def insert(self, index: int, value: T) -> None:
        """Insert at index, shifting elements right. O(n)"""
        if index < 0 or index > self._size:
            raise IndexError(f"Index {index} out of bounds")
        if self._size == self._capacity:
            self._resize(self._capacity * self.GROWTH_FACTOR)
        # Shift elements right
        for i in range(self._size, index, -1):
            self._data[i] = self._data[i - 1]
        self._data[index] = value
        self._size += 1
    
    def remove(self, index: int) -> T:
        """Remove element at index, shifting elements left. O(n)"""
        if index < 0 or index >= self._size:
            raise IndexError(f"Index {index} out of bounds")
        value = self._data[index]
        # Shift elements left
        for i in range(index, self._size - 1):
            self._data[i] = self._data[i + 1]
        self._size -= 1
        self._data[self._size] = None
        return value  # type: ignore
    
    def _resize(self, new_capacity: int) -> None:
        """Resize internal array. O(n)"""
        new_data: List[Optional[T]] = [None] * new_capacity
        for i in range(self._size):
            new_data[i] = self._data[i]
        self._data = new_data
        self._capacity = new_capacity
    
    def __iter__(self) -> Iterator[T]:
        """Iterate over elements. O(n) total."""
        for i in range(self._size):
            yield self._data[i]  # type: ignore
    
    def __repr__(self) -> str:
        return f"DynamicArray({[self._data[i] for i in range(self._size)]})"


if __name__ == "__main__":
    import doctest
    doctest.testmod()
```

---

## Key Insights for Correctness

### Array Access Patterns

```
┌─────────────────────────────────────────────────────────┐
│ Memory Layout: Contiguous allocation                    │
├─────────────────────────────────────────────────────────┤
│ Base Address: 0x1000                                    │
│                                                         │
│  Index:    0      1      2      3      4               │
│          ┌──────┬──────┬──────┬──────┬──────┐          │
│ Address: │0x1000│0x1004│0x1008│0x100C│0x1010│          │
│          │  10  │  20  │  30  │  40  │  50  │          │
│          └──────┴──────┴──────┴──────┴──────┘          │
│                                                         │
│ Address of element[i] = base + (i × element_size)      │
│ Example: element[2] = 0x1000 + (2 × 4) = 0x1008        │
└─────────────────────────────────────────────────────────┘
```

### Cache Efficiency
- Arrays are cache-friendly due to spatial locality
- Sequential access: ~100x faster than random access on modern CPUs
- Prefer arrays over linked lists for iteration-heavy workloads

---

## Common Implementations

### Python Built-in Operations

```python
from typing import List

def array_operations_demo():
    """
    Demonstration of Python list operations with complexity.
    
    >>> array_operations_demo()
    Creation: [1, 2, 3, 4, 5]
    Access arr[2]: 3
    Slice arr[1:4]: [2, 3, 4]
    After append: [1, 2, 3, 4, 5, 6]
    After insert at 0: [0, 1, 2, 3, 4, 5, 6]
    After pop: [0, 1, 2, 3, 4, 5]
    After remove(3): [0, 1, 2, 4, 5]
    Reversed: [5, 4, 2, 1, 0]
    Sorted: [0, 1, 2, 4, 5]
    """
    # Creation - O(n)
    arr: List[int] = [1, 2, 3, 4, 5]
    print(f"Creation: {arr}")
    
    # Access - O(1)
    print(f"Access arr[2]: {arr[2]}")
    
    # Slicing - O(k) where k = slice size
    print(f"Slice arr[1:4]: {arr[1:4]}")
    
    # Append - O(1) amortized
    arr.append(6)
    print(f"After append: {arr}")
    
    # Insert at index - O(n)
    arr.insert(0, 0)
    print(f"After insert at 0: {arr}")
    
    # Pop from end - O(1)
    arr.pop()
    print(f"After pop: {arr}")
    
    # Remove by value - O(n)
    arr.remove(3)
    print(f"After remove(3): {arr}")
    
    # Reverse - O(n)
    arr.reverse()
    print(f"Reversed: {arr}")
    
    # Sort - O(n log n)
    arr.sort()
    print(f"Sorted: {arr}")


# List comprehensions - Pythonic array creation
def list_comprehension_examples():
    """
    >>> squares = [x**2 for x in range(5)]
    >>> squares
    [0, 1, 4, 9, 16]
    >>> evens = [x for x in range(10) if x % 2 == 0]
    >>> evens
    [0, 2, 4, 6, 8]
    >>> matrix = [[i*3+j for j in range(3)] for i in range(3)]
    >>> matrix
    [[0, 1, 2], [3, 4, 5], [6, 7, 8]]
    """
    pass


if __name__ == "__main__":
    import doctest
    doctest.testmod()
```

---

## Step-by-Step Worked Example

### Problem: Rotate Array Right by K Steps

**Input:** `nums = [1, 2, 3, 4, 5, 6, 7]`, `k = 3`
**Output:** `[5, 6, 7, 1, 2, 3, 4]`

```
Step 1: Handle k > n case
  k = k % n = 3 % 7 = 3
  
Step 2: Reverse entire array
  [1, 2, 3, 4, 5, 6, 7] → [7, 6, 5, 4, 3, 2, 1]
  
Step 3: Reverse first k elements
  [7, 6, 5, 4, 3, 2, 1] → [5, 6, 7, 4, 3, 2, 1]
        ↑───↑
       k elements
       
Step 4: Reverse remaining n-k elements
  [5, 6, 7, 4, 3, 2, 1] → [5, 6, 7, 1, 2, 3, 4]
              ↑───────↑
             n-k elements

Final: [5, 6, 7, 1, 2, 3, 4] ✓
```

**Why this works:** Reversing achieves the rotation through a composition of reflections.

---

## Variants & Extensions

### 2D Arrays (Matrices)

```python
from typing import List

def matrix_operations():
    """
    2D array operations and traversal patterns.
    
    >>> matrix = [[1,2,3], [4,5,6], [7,8,9]]
    >>> # Row-major traversal
    >>> [matrix[i][j] for i in range(3) for j in range(3)]
    [1, 2, 3, 4, 5, 6, 7, 8, 9]
    >>> # Column-major traversal
    >>> [matrix[i][j] for j in range(3) for i in range(3)]
    [1, 4, 7, 2, 5, 8, 3, 6, 9]
    >>> # Diagonal traversal
    >>> [matrix[i][i] for i in range(3)]
    [1, 5, 9]
    >>> # Anti-diagonal
    >>> [matrix[i][2-i] for i in range(3)]
    [3, 5, 7]
    """
    pass


def transpose(matrix: List[List[int]]) -> List[List[int]]:
    """
    Transpose matrix (swap rows and columns).
    
    >>> transpose([[1,2,3], [4,5,6]])
    [[1, 4], [2, 5], [3, 6]]
    """
    if not matrix:
        return []
    rows, cols = len(matrix), len(matrix[0])
    return [[matrix[i][j] for i in range(rows)] for j in range(cols)]
```

### Circular Arrays

```python
class CircularArray:
    """
    Array with wrap-around indexing.
    
    >>> ca = CircularArray([1, 2, 3, 4, 5])
    >>> ca[7]  # 7 % 5 = 2
    3
    >>> ca[-1]  # Wraps to last element
    5
    """
    def __init__(self, data: List[int]):
        self.data = data
        self.n = len(data)
    
    def __getitem__(self, index: int) -> int:
        return self.data[index % self.n]
```

---

## Common Interview Problems

| # | Problem | Difficulty | Approach | Time | LeetCode |
|---|---------|------------|----------|------|----------|
| 1 | Two Sum | Easy | Hash Map | O(n) | [#1](https://leetcode.com/problems/two-sum/) |
| 2 | Best Time to Buy and Sell Stock | Easy | Track Min | O(n) | [#121](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/) |
| 3 | Contains Duplicate | Easy | Set | O(n) | [#217](https://leetcode.com/problems/contains-duplicate/) |
| 4 | Product of Array Except Self | Medium | Prefix/Suffix | O(n) | [#238](https://leetcode.com/problems/product-of-array-except-self/) |
| 5 | Maximum Subarray | Medium | Kadane's | O(n) | [#53](https://leetcode.com/problems/maximum-subarray/) |
| 6 | 3Sum | Medium | Two Pointers | O(n²) | [#15](https://leetcode.com/problems/3sum/) |
| 7 | Container With Most Water | Medium | Two Pointers | O(n) | [#11](https://leetcode.com/problems/container-with-most-water/) |
| 8 | Rotate Array | Medium | Reverse | O(n) | [#189](https://leetcode.com/problems/rotate-array/) |
| 9 | Merge Intervals | Medium | Sort + Merge | O(n log n) | [#56](https://leetcode.com/problems/merge-intervals/) |
| 10 | First Missing Positive | Hard | Cycle Sort | O(n) | [#41](https://leetcode.com/problems/first-missing-positive/) |

---

## Fully Worked Sample Problem

### Problem: Product of Array Except Self

**Problem Statement:** Given an integer array `nums`, return an array `answer` such that `answer[i]` is equal to the product of all the elements of `nums` except `nums[i]`. Solve it in O(n) time without using division.

**Difficulty:** Medium | [LeetCode #238](https://leetcode.com/problems/product-of-array-except-self/)

```python
from typing import List

def product_except_self(nums: List[int]) -> List[int]:
    """
    Calculate product of all elements except self.
    
    Approach: Use prefix and suffix products.
    - prefix[i] = product of all elements before index i
    - suffix[i] = product of all elements after index i
    - result[i] = prefix[i] * suffix[i]
    
    Time Complexity: O(n) - two passes
    Space Complexity: O(1) - output array not counted
    
    >>> product_except_self([1, 2, 3, 4])
    [24, 12, 8, 6]
    >>> product_except_self([-1, 1, 0, -3, 3])
    [0, 0, 9, 0, 0]
    >>> product_except_self([2, 3])
    [3, 2]
    """
    n = len(nums)
    result = [1] * n
    
    # First pass: calculate prefix products
    # result[i] = product of nums[0] * nums[1] * ... * nums[i-1]
    prefix = 1
    for i in range(n):
        result[i] = prefix
        prefix *= nums[i]
    
    # Second pass: multiply by suffix products
    # result[i] *= nums[i+1] * nums[i+2] * ... * nums[n-1]
    suffix = 1
    for i in range(n - 1, -1, -1):
        result[i] *= suffix
        suffix *= nums[i]
    
    return result


def product_except_self_with_visualization(nums: List[int]) -> List[int]:
    """
    Same algorithm with step-by-step visualization.
    
    >>> product_except_self_with_visualization([1, 2, 3, 4])
    Initial: [1, 2, 3, 4]
    <BLANKLINE>
    Pass 1 - Prefix Products:
    i=0: prefix=1, result=[1, 1, 1, 1]
    i=1: prefix=1, result=[1, 1, 1, 1]
    i=2: prefix=2, result=[1, 1, 2, 1]
    i=3: prefix=6, result=[1, 1, 2, 6]
    <BLANKLINE>
    Pass 2 - Suffix Products:
    i=3: suffix=1, result=[1, 1, 2, 6]
    i=2: suffix=4, result=[1, 1, 8, 6]
    i=1: suffix=12, result=[1, 12, 8, 6]
    i=0: suffix=24, result=[24, 12, 8, 6]
    <BLANKLINE>
    Final: [24, 12, 8, 6]
    [24, 12, 8, 6]
    """
    n = len(nums)
    result = [1] * n
    
    print(f"Initial: {nums}")
    print(f"\nPass 1 - Prefix Products:")
    
    prefix = 1
    for i in range(n):
        result[i] = prefix
        print(f"i={i}: prefix={prefix}, result={result}")
        prefix *= nums[i]
    
    print(f"\nPass 2 - Suffix Products:")
    
    suffix = 1
    for i in range(n - 1, -1, -1):
        result[i] *= suffix
        print(f"i={i}: suffix={suffix}, result={result}")
        suffix *= nums[i]
    
    print(f"\nFinal: {result}")
    return result


# Unit tests
def test_product_except_self():
    assert product_except_self([1, 2, 3, 4]) == [24, 12, 8, 6]
    assert product_except_self([0, 1, 2]) == [2, 0, 0]
    assert product_except_self([0, 0]) == [0, 0]
    assert product_except_self([5]) == [1]  # Edge case: single element
    print("All tests passed!")


if __name__ == "__main__":
    import doctest
    doctest.testmod()
    test_product_except_self()
```

**Pattern(s) Used:** Prefix Sum (adapted for products), Space Optimization

---

## Edge Cases & Pitfalls

- **Empty Array:** Check `if not nums` before processing
- **Single Element:** Return `[1]` (product of nothing)
- **Contains Zero:** 
  - One zero: all products except that position are 0
  - Multiple zeros: all products are 0
- **Integer Overflow:** Python handles arbitrary precision, but consider in other languages
- **Negative Numbers:** Product sign alternates; handle carefully in comparisons
- **Off-by-One Errors:** Verify loop bounds, especially for `range(n-1, -1, -1)`
- **In-Place Modification:** Be careful if input array shouldn't be modified

---

## Follow-ups and Optimization Ideas

1. **With Division:** O(n) with single pass - calculate total product, divide by each element (handle zeros separately)
2. **Parallel Version:** Prefix sums can be parallelized using work-efficient parallel scan
3. **Streaming:** Handle infinite stream with sliding window approach
4. **Sparse Arrays:** Skip zeros intelligently
5. **Segment Tree:** For range product queries with updates

---

## Practice Checklist

| # | Problem | Difficulty | Link | Pattern |
|---|---------|------------|------|---------|
| 1 | Two Sum | Easy | [LC #1](https://leetcode.com/problems/two-sum/) | Hash Map |
| 2 | Best Time to Buy and Sell Stock | Easy | [LC #121](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/) | Track Min |
| 3 | Contains Duplicate | Easy | [LC #217](https://leetcode.com/problems/contains-duplicate/) | Set |
| 4 | Maximum Subarray | Medium | [LC #53](https://leetcode.com/problems/maximum-subarray/) | Kadane |
| 5 | Product of Array Except Self | Medium | [LC #238](https://leetcode.com/problems/product-of-array-except-self/) | Prefix |
| 6 | Maximum Product Subarray | Medium | [LC #152](https://leetcode.com/problems/maximum-product-subarray/) | DP |
| 7 | Find Minimum in Rotated Sorted Array | Medium | [LC #153](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/) | Binary Search |
| 8 | Search in Rotated Sorted Array | Medium | [LC #33](https://leetcode.com/problems/search-in-rotated-sorted-array/) | Binary Search |
| 9 | 3Sum | Medium | [LC #15](https://leetcode.com/problems/3sum/) | Two Pointers |
| 10 | Container With Most Water | Medium | [LC #11](https://leetcode.com/problems/container-with-most-water/) | Two Pointers |
| 11 | Merge Intervals | Medium | [LC #56](https://leetcode.com/problems/merge-intervals/) | Sort + Merge |
| 12 | Insert Interval | Medium | [LC #57](https://leetcode.com/problems/insert-interval/) | Merge |
| 13 | Subarray Sum Equals K | Medium | [LC #560](https://leetcode.com/problems/subarray-sum-equals-k/) | Prefix + Hash |
| 14 | Trapping Rain Water | Hard | [LC #42](https://leetcode.com/problems/trapping-rain-water/) | Two Pointers/DP |
| 15 | First Missing Positive | Hard | [LC #41](https://leetcode.com/problems/first-missing-positive/) | Cycle Sort |
| 16 | Median of Two Sorted Arrays | Hard | [LC #4](https://leetcode.com/problems/median-of-two-sorted-arrays/) | Binary Search |
| 17 | Sliding Window Maximum | Hard | [LC #239](https://leetcode.com/problems/sliding-window-maximum/) | Monotonic Deque |
| 18 | Minimum Window Substring | Hard | [LC #76](https://leetcode.com/problems/minimum-window-substring/) | Sliding Window |
| 19 | Longest Consecutive Sequence | Medium | [LC #128](https://leetcode.com/problems/longest-consecutive-sequence/) | Set |
| 20 | Next Permutation | Medium | [LC #31](https://leetcode.com/problems/next-permutation/) | Array Manipulation |

---

## Further Reading & References

- [CLRS Chapter 10.1 - Elementary Data Structures](https://mitpress.mit.edu/books/introduction-algorithms-third-edition)
- [Python List Implementation (CPython)](https://github.com/python/cpython/blob/main/Objects/listobject.c)
- [GeeksforGeeks - Array Data Structure](https://www.geeksforgeeks.org/array-data-structure/)
- [VisuAlgo - Array](https://visualgo.net/en/list)
- [LeetCode Array Problems](https://leetcode.com/tag/array/)

---

## Flashcard Summary

**Q: What is the time complexity of accessing array element by index?**
A: O(1) - direct memory offset calculation: base + index × element_size

**Q: Why is dynamic array append O(1) amortized?**
A: Resize doubles capacity; total copy cost over n insertions is O(n), so O(1) per insert.

**Q: How to rotate array right by k in O(n) time and O(1) space?**
A: Reverse entire array, reverse first k elements, reverse remaining n-k elements.

**Q: When to use array vs linked list?**
A: Array: random access, cache-friendly iteration. Linked list: frequent middle insertions/deletions.

**Q: How to find product of array except self without division?**
A: Two passes: first stores prefix products, second multiplies by suffix products.

**Q: What is cycle sort and when is it useful?**
A: Place each element at its correct position in O(n); useful for finding missing/duplicate in range [1, n].
