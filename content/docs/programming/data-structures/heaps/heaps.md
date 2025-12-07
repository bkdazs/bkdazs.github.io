---
title: "Heaps and Priority Queues"
topic: "Data Structures"
difficulty: "Intermediate"
tags: ["heap", "priority-queue", "binary-heap", "heapify", "min-heap", "max-heap"]
status: "complete"
weight: 7
---

# Heaps and Priority Queues

## Short Summary

A **heap** is a complete binary tree satisfying the heap property: in a min-heap, every parent is ≤ its children; in a max-heap, every parent is ≥ its children. Heaps provide O(1) access to the min/max element and O(log n) insertion/extraction, making them ideal for priority queues, sorting (heapsort), and streaming problems (k-th largest, median).

---

## Why It Matters in Interviews

- **Top-K Problems:** Find k largest/smallest elements efficiently
- **Merge K Sorted:** Use heap to track minimum across lists
- **Scheduling:** Task prioritization, Dijkstra's shortest path
- **Streaming Data:** Maintain running median, sliding window statistics

---

## Formal Definition / Properties / Invariants

### Heap Property

| Type | Property | Root |
|------|----------|------|
| **Min-Heap** | parent ≤ children | Minimum element |
| **Max-Heap** | parent ≥ children | Maximum element |

### Complete Binary Tree Property
- All levels filled except possibly last
- Last level filled left-to-right
- Enables array representation with O(1) parent/child calculation

### Array Representation
```
For 0-indexed array:
- Parent of i:      (i - 1) // 2
- Left child of i:  2 * i + 1
- Right child of i: 2 * i + 2

Min-Heap Example:
        1           Index: 0
       / \
      3   2         Index: 1, 2
     / \ / \
    7  4 5  6       Index: 3, 4, 5, 6

Array: [1, 3, 2, 7, 4, 5, 6]
```

---

## Time & Space Complexity

| Operation | Time | Notes |
|-----------|------|-------|
| Get min/max | O(1) | Root access |
| Insert (push) | O(log n) | Bubble up |
| Extract min/max | O(log n) | Bubble down |
| Build heap | O(n) | Bottom-up heapify |
| Heapify single element | O(log n) | Restore heap property |
| Heap sort | O(n log n) | In-place |

**Space Complexity:** O(n) for n elements, O(1) auxiliary for operations

### Why is build heap O(n)?
- Heapify at height h takes O(h) time
- Number of nodes at height h: n / 2^(h+1)
- Total: Σ(n/2^(h+1)) × h = O(n)

---

## Typical Operations & Pseudo-Implementation

### Min-Heap Implementation

```python
from typing import TypeVar, Generic, List, Optional

T = TypeVar('T')

class MinHeap(Generic[T]):
    """
    Min-heap implementation using array.
    
    >>> heap = MinHeap[int]()
    >>> heap.push(3)
    >>> heap.push(1)
    >>> heap.push(2)
    >>> heap.peek()
    1
    >>> heap.pop()
    1
    >>> heap.pop()
    2
    >>> heap.pop()
    3
    >>> heap.is_empty()
    True
    """
    
    def __init__(self):
        self._data: List[T] = []
    
    def __len__(self) -> int:
        return len(self._data)
    
    def is_empty(self) -> bool:
        return len(self._data) == 0
    
    def peek(self) -> T:
        """Return minimum without removing. O(1)"""
        if self.is_empty():
            raise IndexError("Heap is empty")
        return self._data[0]
    
    def push(self, item: T) -> None:
        """Add element. O(log n)"""
        self._data.append(item)
        self._bubble_up(len(self._data) - 1)
    
    def pop(self) -> T:
        """Remove and return minimum. O(log n)"""
        if self.is_empty():
            raise IndexError("Heap is empty")
        
        # Swap root with last element
        min_val = self._data[0]
        last = self._data.pop()
        
        if self._data:
            self._data[0] = last
            self._bubble_down(0)
        
        return min_val
    
    def _bubble_up(self, index: int) -> None:
        """Move element up to maintain heap property."""
        while index > 0:
            parent = (index - 1) // 2
            if self._data[index] < self._data[parent]:
                self._data[index], self._data[parent] = self._data[parent], self._data[index]
                index = parent
            else:
                break
    
    def _bubble_down(self, index: int) -> None:
        """Move element down to maintain heap property."""
        n = len(self._data)
        while True:
            smallest = index
            left = 2 * index + 1
            right = 2 * index + 2
            
            if left < n and self._data[left] < self._data[smallest]:
                smallest = left
            if right < n and self._data[right] < self._data[smallest]:
                smallest = right
            
            if smallest != index:
                self._data[index], self._data[smallest] = self._data[smallest], self._data[index]
                index = smallest
            else:
                break
    
    @classmethod
    def heapify(cls, items: List[T]) -> 'MinHeap[T]':
        """Build heap from list in O(n). Bottom-up heapify."""
        heap = cls()
        heap._data = items.copy()
        
        # Start from last non-leaf node
        for i in range(len(items) // 2 - 1, -1, -1):
            heap._bubble_down(i)
        
        return heap


if __name__ == "__main__":
    import doctest
    doctest.testmod()
```

### Python heapq Module

```python
import heapq
from typing import List, Tuple

def heapq_demo():
    """
    Python heapq module (min-heap only).
    
    >>> # Basic operations
    >>> heap = []
    >>> heapq.heappush(heap, 3)
    >>> heapq.heappush(heap, 1)
    >>> heapq.heappush(heap, 2)
    >>> heap[0]  # Peek (O(1))
    1
    >>> heapq.heappop(heap)  # Pop min (O(log n))
    1
    >>> heap
    [2, 3]
    
    >>> # Heapify existing list (O(n))
    >>> nums = [5, 3, 8, 1, 9]
    >>> heapq.heapify(nums)
    >>> nums[0]
    1
    
    >>> # Push and pop in single operation
    >>> heapq.heappushpop([1, 2, 3], 0)  # Push 0, then pop min
    0
    >>> heapq.heapreplace([1, 2, 3], 0)  # Pop min, then push 0
    1
    """
    pass


def max_heap_with_heapq():
    """
    Simulate max-heap by negating values.
    
    >>> heap = []
    >>> for num in [3, 1, 4, 1, 5]:
    ...     heapq.heappush(heap, -num)  # Negate to simulate max-heap
    >>> -heapq.heappop(heap)  # Negate result
    5
    >>> -heapq.heappop(heap)
    4
    """
    pass


def heap_with_custom_key():
    """
    Use tuples for custom ordering.
    
    >>> # Priority queue with (priority, data)
    >>> heap = []
    >>> heapq.heappush(heap, (2, "medium"))
    >>> heapq.heappush(heap, (1, "high"))
    >>> heapq.heappush(heap, (3, "low"))
    >>> heapq.heappop(heap)
    (1, 'high')
    
    >>> # For unstable tie-breaking, add counter
    >>> from itertools import count
    >>> counter = count()
    >>> heap = []
    >>> heapq.heappush(heap, (1, next(counter), "task_a"))
    >>> heapq.heappush(heap, (1, next(counter), "task_b"))
    >>> heapq.heappop(heap)[2]  # task_a comes first
    'task_a'
    """
    pass


if __name__ == "__main__":
    import doctest
    doctest.testmod()
```

---

## Key Insights for Correctness

### Heap Operations Visualization

```
Insert 4 into min-heap [1, 3, 2, 7, 5, 6]:

Step 1: Add at end
        1
       / \
      3   2
     / \ / \
    7  5 6  4

Step 2: Bubble up (4 < 2)
        1
       / \
      3   4
     / \ / \
    7  5 6  2

Step 3: Check parent (4 > 1), done
Final: [1, 3, 4, 7, 5, 6, 2]

---

Extract min from [1, 3, 2, 7, 5, 6]:

Step 1: Remove root, move last to root
        6
       / \
      3   2
     / \
    7  5

Step 2: Bubble down (6 > min(3,2) = 2)
        2
       / \
      3   6
     / \
    7  5

Step 3: Bubble down (6 > min(none) or leaf), done
Final: [2, 3, 6, 7, 5]
```

---

## Common Implementations

### Kth Largest Element

```python
import heapq
from typing import List

def find_kth_largest(nums: List[int], k: int) -> int:
    """
    Find kth largest element using min-heap of size k.
    
    Time: O(n log k)
    Space: O(k)
    
    >>> find_kth_largest([3, 2, 1, 5, 6, 4], 2)
    5
    >>> find_kth_largest([3, 2, 3, 1, 2, 4, 5, 5, 6], 4)
    4
    """
    # Maintain min-heap of k largest elements
    # Root will be kth largest
    heap = []
    
    for num in nums:
        if len(heap) < k:
            heapq.heappush(heap, num)
        elif num > heap[0]:
            heapq.heapreplace(heap, num)  # More efficient than push+pop
    
    return heap[0]


def find_kth_largest_quickselect(nums: List[int], k: int) -> int:
    """
    Alternative: QuickSelect (average O(n), worst O(n²)).
    
    >>> find_kth_largest_quickselect([3, 2, 1, 5, 6, 4], 2)
    5
    """
    import random
    
    k = len(nums) - k  # Convert to kth smallest
    
    def quickselect(left: int, right: int) -> int:
        pivot_idx = random.randint(left, right)
        pivot = nums[pivot_idx]
        
        # Move pivot to end
        nums[pivot_idx], nums[right] = nums[right], nums[pivot_idx]
        
        # Partition
        store_idx = left
        for i in range(left, right):
            if nums[i] < pivot:
                nums[store_idx], nums[i] = nums[i], nums[store_idx]
                store_idx += 1
        
        nums[store_idx], nums[right] = nums[right], nums[store_idx]
        
        if store_idx == k:
            return nums[store_idx]
        elif store_idx < k:
            return quickselect(store_idx + 1, right)
        else:
            return quickselect(left, store_idx - 1)
    
    return quickselect(0, len(nums) - 1)


if __name__ == "__main__":
    import doctest
    doctest.testmod()
```

### Merge K Sorted Lists

```python
import heapq
from typing import List, Optional

class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next


def merge_k_lists(lists: List[Optional[ListNode]]) -> Optional[ListNode]:
    """
    Merge k sorted linked lists using min-heap.
    
    Time: O(n log k) where n = total elements, k = number of lists
    Space: O(k) for heap
    
    >>> # Helper to create and convert lists
    >>> def to_list(node):
    ...     result = []
    ...     while node:
    ...         result.append(node.val)
    ...         node = node.next
    ...     return result
    >>> l1 = ListNode(1, ListNode(4, ListNode(5)))
    >>> l2 = ListNode(1, ListNode(3, ListNode(4)))
    >>> l3 = ListNode(2, ListNode(6))
    >>> result = merge_k_lists([l1, l2, l3])
    >>> to_list(result)
    [1, 1, 2, 3, 4, 4, 5, 6]
    """
    # Min-heap with (value, list_index, node)
    # list_index for tie-breaking (nodes aren't comparable)
    heap = []
    
    for i, lst in enumerate(lists):
        if lst:
            heapq.heappush(heap, (lst.val, i, lst))
    
    dummy = ListNode()
    current = dummy
    
    while heap:
        val, i, node = heapq.heappop(heap)
        current.next = node
        current = current.next
        
        if node.next:
            heapq.heappush(heap, (node.next.val, i, node.next))
    
    return dummy.next


if __name__ == "__main__":
    import doctest
    doctest.testmod()
```

### Find Median from Data Stream

```python
import heapq

class MedianFinder:
    """
    Find median in streaming data using two heaps.
    
    Approach: 
    - max_heap stores smaller half (negate for max behavior)
    - min_heap stores larger half
    - Balance: |max_heap| == |min_heap| or |max_heap| == |min_heap| + 1
    
    Time: O(log n) add, O(1) find
    Space: O(n)
    
    >>> mf = MedianFinder()
    >>> mf.add_num(1)
    >>> mf.add_num(2)
    >>> mf.find_median()
    1.5
    >>> mf.add_num(3)
    >>> mf.find_median()
    2.0
    """
    
    def __init__(self):
        self.max_heap = []  # Smaller half (negated values)
        self.min_heap = []  # Larger half
    
    def add_num(self, num: int) -> None:
        # Add to max_heap first
        heapq.heappush(self.max_heap, -num)
        
        # Move largest from max_heap to min_heap
        heapq.heappush(self.min_heap, -heapq.heappop(self.max_heap))
        
        # Balance: max_heap should have equal or one more element
        if len(self.min_heap) > len(self.max_heap):
            heapq.heappush(self.max_heap, -heapq.heappop(self.min_heap))
    
    def find_median(self) -> float:
        if len(self.max_heap) > len(self.min_heap):
            return -self.max_heap[0]
        return (-self.max_heap[0] + self.min_heap[0]) / 2


if __name__ == "__main__":
    import doctest
    doctest.testmod()
```

---

## Step-by-Step Worked Example

### Problem: Top K Frequent Elements

**Input:** `nums = [1,1,1,2,2,3]`, `k = 2`
**Output:** `[1, 2]`

```
Step 1: Count frequencies
  freq = {1: 3, 2: 2, 3: 1}

Step 2: Use min-heap of size k to track top k
  Process (3, 1): heap = [(3, 1)]
  Process (2, 2): heap = [(2, 2), (3, 1)]  # size = k
  Process (1, 3): 1 < heap[0]=2? No, skip
  
Wait, we want most frequent, so we should push (freq, num)
and keep smallest freq at root to potentially replace.

Correct approach:
  heap = []
  Process 1 (freq=3): heap = [(3, 1)]
  Process 2 (freq=2): heap = [(2, 2), (3, 1)]
  Process 3 (freq=1): 1 > heap[0]=2? No
                       1 < heap[0]=2? Yes, but we want high freq
                       
Actually: min-heap keeps k largest frequencies.
  Push (freq, num), if size > k, pop smallest.
  
  Process 1 (freq=3): heap = [(3, 1)]
  Process 2 (freq=2): heap = [(2, 2), (3, 1)]
  Process 3 (freq=1): len=2=k, 1 < heap[0]=2? Yes, don't add
  
Result: [2, 1] (extract from heap)
```

---

## Variants & Extensions

### D-ary Heap

```python
class DaryHeap:
    """
    D-ary min-heap: each node has d children.
    
    Tradeoff: 
    - Decrease key faster (shallower tree)
    - Extract min slower (more comparisons per level)
    
    Optimal d depends on cache line size.
    
    >>> heap = DaryHeap(4)  # 4-ary heap
    >>> heap.push(5)
    >>> heap.push(3)
    >>> heap.push(7)
    >>> heap.push(1)
    >>> heap.pop()
    1
    """
    
    def __init__(self, d: int = 2):
        self.d = d
        self._data = []
    
    def _parent(self, i: int) -> int:
        return (i - 1) // self.d
    
    def _child(self, i: int, k: int) -> int:
        """Return kth child (0-indexed)."""
        return self.d * i + k + 1
    
    def push(self, val: int) -> None:
        self._data.append(val)
        self._bubble_up(len(self._data) - 1)
    
    def pop(self) -> int:
        if not self._data:
            raise IndexError("Empty heap")
        val = self._data[0]
        last = self._data.pop()
        if self._data:
            self._data[0] = last
            self._bubble_down(0)
        return val
    
    def _bubble_up(self, i: int) -> None:
        while i > 0:
            p = self._parent(i)
            if self._data[i] < self._data[p]:
                self._data[i], self._data[p] = self._data[p], self._data[i]
                i = p
            else:
                break
    
    def _bubble_down(self, i: int) -> None:
        n = len(self._data)
        while True:
            smallest = i
            for k in range(self.d):
                c = self._child(i, k)
                if c < n and self._data[c] < self._data[smallest]:
                    smallest = c
            if smallest != i:
                self._data[i], self._data[smallest] = self._data[smallest], self._data[i]
                i = smallest
            else:
                break


if __name__ == "__main__":
    import doctest
    doctest.testmod()
```

### Indexed Priority Queue (for Dijkstra with decrease-key)

```python
class IndexedMinPQ:
    """
    Indexed min priority queue supporting decrease-key.
    Maps keys to priorities, allows updating priorities.
    """
    
    def __init__(self, max_n: int):
        self.max_n = max_n
        self.n = 0
        self.pq = [0] * (max_n + 1)      # Binary heap indices
        self.qp = [-1] * (max_n + 1)     # Inverse: qp[pq[i]] = i
        self.keys = [None] * (max_n + 1)  # Priority values
    
    def is_empty(self) -> bool:
        return self.n == 0
    
    def contains(self, i: int) -> bool:
        return self.qp[i] != -1
    
    def insert(self, i: int, key: float) -> None:
        self.n += 1
        self.qp[i] = self.n
        self.pq[self.n] = i
        self.keys[i] = key
        self._swim(self.n)
    
    def decrease_key(self, i: int, key: float) -> None:
        """Decrease priority of key i. O(log n)"""
        self.keys[i] = key
        self._swim(self.qp[i])
    
    def del_min(self) -> int:
        """Remove and return index of minimum. O(log n)"""
        min_idx = self.pq[1]
        self._exch(1, self.n)
        self.n -= 1
        self._sink(1)
        self.qp[min_idx] = -1
        self.keys[min_idx] = None
        return min_idx
    
    def _swim(self, k: int) -> None:
        while k > 1 and self._greater(k // 2, k):
            self._exch(k, k // 2)
            k = k // 2
    
    def _sink(self, k: int) -> None:
        while 2 * k <= self.n:
            j = 2 * k
            if j < self.n and self._greater(j, j + 1):
                j += 1
            if not self._greater(k, j):
                break
            self._exch(k, j)
            k = j
    
    def _greater(self, i: int, j: int) -> bool:
        return self.keys[self.pq[i]] > self.keys[self.pq[j]]
    
    def _exch(self, i: int, j: int) -> None:
        self.pq[i], self.pq[j] = self.pq[j], self.pq[i]
        self.qp[self.pq[i]] = i
        self.qp[self.pq[j]] = j
```

---

## Common Interview Problems

| # | Problem | Difficulty | Approach | Time | LeetCode |
|---|---------|------------|----------|------|----------|
| 1 | Kth Largest Element | Medium | Min-heap size k | O(n log k) | [#215](https://leetcode.com/problems/kth-largest-element-in-an-array/) |
| 2 | Top K Frequent Elements | Medium | Heap/Bucket sort | O(n log k) | [#347](https://leetcode.com/problems/top-k-frequent-elements/) |
| 3 | Merge K Sorted Lists | Hard | Min-heap | O(n log k) | [#23](https://leetcode.com/problems/merge-k-sorted-lists/) |
| 4 | Find Median from Data Stream | Hard | Two heaps | O(log n) | [#295](https://leetcode.com/problems/find-median-from-data-stream/) |
| 5 | K Closest Points to Origin | Medium | Max-heap size k | O(n log k) | [#973](https://leetcode.com/problems/k-closest-points-to-origin/) |
| 6 | Task Scheduler | Medium | Max-heap + cooldown | O(n) | [#621](https://leetcode.com/problems/task-scheduler/) |
| 7 | Reorganize String | Medium | Max-heap greedy | O(n log 26) | [#767](https://leetcode.com/problems/reorganize-string/) |
| 8 | Smallest Range Covering K Lists | Hard | Min-heap | O(n log k) | [#632](https://leetcode.com/problems/smallest-range-covering-elements-from-k-lists/) |
| 9 | Trapping Rain Water II | Hard | Min-heap BFS | O(mn log mn) | [#407](https://leetcode.com/problems/trapping-rain-water-ii/) |
| 10 | Sliding Window Median | Hard | Two heaps + lazy delete | O(n log k) | [#480](https://leetcode.com/problems/sliding-window-median/) |

---

## Fully Worked Sample Problem

### Problem: Top K Frequent Elements

**Problem Statement:** Given an integer array `nums` and an integer `k`, return the `k` most frequent elements.

**Difficulty:** Medium | [LeetCode #347](https://leetcode.com/problems/top-k-frequent-elements/)

```python
import heapq
from collections import Counter
from typing import List

def top_k_frequent(nums: List[int], k: int) -> List[int]:
    """
    Find k most frequent elements using min-heap.
    
    Time: O(n log k) - n elements, heap of size k
    Space: O(n) for frequency map
    
    >>> sorted(top_k_frequent([1,1,1,2,2,3], 2))
    [1, 2]
    >>> top_k_frequent([1], 1)
    [1]
    >>> sorted(top_k_frequent([4,1,-1,2,-1,2,3], 2))
    [-1, 2]
    """
    # Count frequencies
    freq = Counter(nums)
    
    # Use min-heap of size k
    # Heap contains (frequency, element)
    heap = []
    
    for num, count in freq.items():
        heapq.heappush(heap, (count, num))
        if len(heap) > k:
            heapq.heappop(heap)  # Remove least frequent
    
    return [num for count, num in heap]


def top_k_frequent_bucket_sort(nums: List[int], k: int) -> List[int]:
    """
    Alternative: Bucket sort approach.
    
    Time: O(n)
    Space: O(n)
    
    >>> sorted(top_k_frequent_bucket_sort([1,1,1,2,2,3], 2))
    [1, 2]
    """
    freq = Counter(nums)
    
    # Bucket: index = frequency, value = list of numbers with that frequency
    buckets = [[] for _ in range(len(nums) + 1)]
    
    for num, count in freq.items():
        buckets[count].append(num)
    
    # Collect k elements from highest frequency buckets
    result = []
    for i in range(len(buckets) - 1, 0, -1):
        for num in buckets[i]:
            result.append(num)
            if len(result) == k:
                return result
    
    return result


def top_k_frequent_quickselect(nums: List[int], k: int) -> List[int]:
    """
    Alternative: QuickSelect on frequencies.
    
    Average Time: O(n)
    Space: O(n)
    
    >>> sorted(top_k_frequent_quickselect([1,1,1,2,2,3], 2))
    [1, 2]
    """
    import random
    
    freq = Counter(nums)
    unique = list(freq.keys())
    n = len(unique)
    
    def partition(left: int, right: int, pivot_idx: int) -> int:
        pivot_freq = freq[unique[pivot_idx]]
        unique[pivot_idx], unique[right] = unique[right], unique[pivot_idx]
        store_idx = left
        
        for i in range(left, right):
            if freq[unique[i]] < pivot_freq:
                unique[store_idx], unique[i] = unique[i], unique[store_idx]
                store_idx += 1
        
        unique[store_idx], unique[right] = unique[right], unique[store_idx]
        return store_idx
    
    def quickselect(left: int, right: int, k_smallest: int) -> None:
        if left == right:
            return
        
        pivot_idx = random.randint(left, right)
        pivot_idx = partition(left, right, pivot_idx)
        
        if k_smallest == pivot_idx:
            return
        elif k_smallest < pivot_idx:
            quickselect(left, pivot_idx - 1, k_smallest)
        else:
            quickselect(pivot_idx + 1, right, k_smallest)
    
    # Find n-k smallest = k largest
    quickselect(0, n - 1, n - k)
    return unique[n - k:]


# Unit tests
def test_top_k_frequent():
    assert sorted(top_k_frequent([1,1,1,2,2,3], 2)) == [1, 2]
    assert top_k_frequent([1], 1) == [1]
    assert sorted(top_k_frequent([1,2], 2)) == [1, 2]
    print("All tests passed!")


if __name__ == "__main__":
    import doctest
    doctest.testmod()
    test_top_k_frequent()
```

**Pattern(s) Used:** Min-Heap of size K, Frequency Counting, Bucket Sort

---

## Edge Cases & Pitfalls

- **Empty Heap:** Check before peek/pop operations
- **Max-Heap in Python:** Must negate values (`heapq` is min-heap only)
- **Tie-Breaking:** Use tuples with counter for stable ordering of equal priorities
- **Duplicate Values:** Handle appropriately based on problem (unique vs all)
- **K > N:** Handle when k exceeds number of unique elements
- **Heapify vs Push Loop:** `heapify` is O(n), n pushes is O(n log n)

---

## Follow-ups and Optimization Ideas

1. **Lazy Deletion:** Mark deleted instead of immediate removal (sliding window)
2. **Fibonacci Heap:** O(1) decrease-key for Dijkstra optimization
3. **Pairing Heap:** Simpler alternative to Fibonacci heap
4. **External Heap:** For data larger than memory
5. **Concurrent Priority Queue:** Thread-safe implementations

---

## Practice Checklist

| # | Problem | Difficulty | Link | Pattern |
|---|---------|------------|------|---------|
| 1 | Kth Largest Element | Medium | [LC #215](https://leetcode.com/problems/kth-largest-element-in-an-array/) | Min-heap K |
| 2 | Top K Frequent Elements | Medium | [LC #347](https://leetcode.com/problems/top-k-frequent-elements/) | Heap + Freq |
| 3 | K Closest Points | Medium | [LC #973](https://leetcode.com/problems/k-closest-points-to-origin/) | Max-heap K |
| 4 | Sort Characters By Frequency | Medium | [LC #451](https://leetcode.com/problems/sort-characters-by-frequency/) | Heap |
| 5 | Kth Smallest in Sorted Matrix | Medium | [LC #378](https://leetcode.com/problems/kth-smallest-element-in-a-sorted-matrix/) | Min-heap |
| 6 | Find K Pairs with Smallest Sums | Medium | [LC #373](https://leetcode.com/problems/find-k-pairs-with-smallest-sums/) | Min-heap |
| 7 | Merge K Sorted Lists | Hard | [LC #23](https://leetcode.com/problems/merge-k-sorted-lists/) | Min-heap |
| 8 | Find Median from Data Stream | Hard | [LC #295](https://leetcode.com/problems/find-median-from-data-stream/) | Two Heaps |
| 9 | Sliding Window Median | Hard | [LC #480](https://leetcode.com/problems/sliding-window-median/) | Two Heaps |
| 10 | IPO | Hard | [LC #502](https://leetcode.com/problems/ipo/) | Two Heaps |
| 11 | Task Scheduler | Medium | [LC #621](https://leetcode.com/problems/task-scheduler/) | Max-heap |
| 12 | Reorganize String | Medium | [LC #767](https://leetcode.com/problems/reorganize-string/) | Max-heap |
| 13 | Ugly Number II | Medium | [LC #264](https://leetcode.com/problems/ugly-number-ii/) | Min-heap |
| 14 | Super Ugly Number | Medium | [LC #313](https://leetcode.com/problems/super-ugly-number/) | Min-heap |
| 15 | Last Stone Weight | Easy | [LC #1046](https://leetcode.com/problems/last-stone-weight/) | Max-heap |
| 16 | Furthest Building You Can Reach | Medium | [LC #1642](https://leetcode.com/problems/furthest-building-you-can-reach/) | Min-heap |
| 17 | Minimum Cost to Connect Sticks | Medium | [LC #1167](https://leetcode.com/problems/minimum-cost-to-connect-sticks/) | Min-heap |
| 18 | Smallest Range Covering K Lists | Hard | [LC #632](https://leetcode.com/problems/smallest-range-covering-elements-from-k-lists/) | Min-heap |
| 19 | Trapping Rain Water II | Hard | [LC #407](https://leetcode.com/problems/trapping-rain-water-ii/) | Min-heap BFS |
| 20 | Swim in Rising Water | Hard | [LC #778](https://leetcode.com/problems/swim-in-rising-water/) | Min-heap Dijkstra |

---

## Further Reading & References

- [CLRS Chapter 6 - Heapsort](https://mitpress.mit.edu/books/introduction-algorithms-third-edition)
- [Python heapq Documentation](https://docs.python.org/3/library/heapq.html)
- [VisuAlgo - Binary Heap](https://visualgo.net/en/heap)
- [GeeksforGeeks - Heap](https://www.geeksforgeeks.org/heap-data-structure/)
- [cp-algorithms - Heap](https://cp-algorithms.com/data_structures/heap.html)

---

## Flashcard Summary

**Q: Why is build-heap O(n) instead of O(n log n)?**
A: Bottom-up heapify: most nodes near leaves have O(1) heapify cost. Sum of heights is O(n).

**Q: How to implement max-heap with Python heapq?**
A: Negate values on push and negate again on pop. `heapq` only provides min-heap.

**Q: What's the time complexity of finding kth largest with heap of size k?**
A: O(n log k) - each of n elements may require O(log k) heap operation.

**Q: How does two-heap median finding work?**
A: Max-heap for smaller half, min-heap for larger half. Balance sizes. Median is root(s).

**Q: When to use heap vs sorting for top-k problems?**
A: Heap is O(n log k), sorting is O(n log n). Heap better when k << n or streaming data.

**Q: What's the difference between heappush+heappop vs heapreplace?**
A: `heapreplace` pops first then pushes (faster, one sift). `heappush+heappop` may differ for edge cases.
