---
title: "Queues"
topic: "Data Structures"
difficulty: "Fundamental"
tags: ["queue", "FIFO", "deque", "priority-queue", "circular-queue"]
status: "complete"
weight: 6
---

# Queues

## Short Summary

A **queue** is a First-In-First-Out (FIFO) data structure supporting enqueue (add to back), dequeue (remove from front), and peek (view front) operations. Queues are fundamental for BFS traversal, task scheduling, buffering, and message passing. The **deque** (double-ended queue) variant allows O(1) operations at both ends, enabling sliding window algorithms.

---

## Why It Matters in Interviews

- **BFS Foundation:** Level-order traversal, shortest path in unweighted graphs
- **Sliding Window:** Deque enables O(n) solutions for window maximum/minimum
- **System Design:** Message queues, task schedulers, rate limiters

---

## Formal Definition / Properties / Invariants

### FIFO Principle
```
Enqueue at BACK, Dequeue from FRONT

FRONT                    BACK
  ↓                        ↓
┌───┬───┬───┬───┬───┐
│ 1 │ 2 │ 3 │ 4 │ 5 │
└───┴───┴───┴───┴───┘
  ↑                   ↑
dequeue()          enqueue(6)

After dequeue(): [2, 3, 4, 5]
After enqueue(6): [2, 3, 4, 5, 6]
```

### Queue Variants

| Type | Description | Operations |
|------|-------------|------------|
| **Simple Queue** | FIFO, one-directional | enqueue, dequeue |
| **Circular Queue** | Fixed-size, wraps around | enqueue, dequeue |
| **Deque** | Double-ended | add/remove both ends |
| **Priority Queue** | Order by priority | insert, extract-min/max |

### Queue ADT Operations

| Operation | Description | Time |
|-----------|-------------|------|
| `enqueue(x)` | Add to back | O(1) |
| `dequeue()` | Remove from front | O(1) |
| `front()` / `peek()` | View front element | O(1) |
| `isEmpty()` | Check if empty | O(1) |
| `size()` | Return element count | O(1) |

---

## Time & Space Complexity

| Implementation | Enqueue | Dequeue | Peek | Space |
|----------------|---------|---------|------|-------|
| Array (naive) | O(1) | O(n) | O(1) | O(n) |
| Circular Array | O(1) | O(1) | O(1) | O(n) |
| Linked List | O(1) | O(1) | O(1) | O(n) + pointers |
| Deque (array) | O(1)* | O(1)* | O(1) | O(n) |

*Amortized O(1)

---

## Typical Operations & Pseudo-Implementation

### Circular Queue

```python
from typing import TypeVar, Generic, Optional

T = TypeVar('T')

class CircularQueue(Generic[T]):
    """
    Fixed-size circular queue implementation.
    
    >>> cq = CircularQueue[int](3)
    >>> cq.enqueue(1)
    True
    >>> cq.enqueue(2)
    True
    >>> cq.enqueue(3)
    True
    >>> cq.enqueue(4)  # Full
    False
    >>> cq.dequeue()
    1
    >>> cq.enqueue(4)  # Now has space
    True
    >>> cq.front()
    2
    """
    
    def __init__(self, capacity: int):
        self._data = [None] * capacity
        self._capacity = capacity
        self._front = 0
        self._size = 0
    
    def __len__(self) -> int:
        return self._size
    
    def is_empty(self) -> bool:
        return self._size == 0
    
    def is_full(self) -> bool:
        return self._size == self._capacity
    
    def enqueue(self, item: T) -> bool:
        """Add to back. O(1). Returns False if full."""
        if self.is_full():
            return False
        back = (self._front + self._size) % self._capacity
        self._data[back] = item
        self._size += 1
        return True
    
    def dequeue(self) -> Optional[T]:
        """Remove from front. O(1). Returns None if empty."""
        if self.is_empty():
            return None
        item = self._data[self._front]
        self._data[self._front] = None
        self._front = (self._front + 1) % self._capacity
        self._size -= 1
        return item
    
    def front(self) -> Optional[T]:
        """View front element. O(1)."""
        if self.is_empty():
            return None
        return self._data[self._front]
    
    def rear(self) -> Optional[T]:
        """View back element. O(1)."""
        if self.is_empty():
            return None
        back = (self._front + self._size - 1) % self._capacity
        return self._data[back]


if __name__ == "__main__":
    import doctest
    doctest.testmod()
```

### Linked List Queue

```python
from typing import TypeVar, Generic, Optional

T = TypeVar('T')

class QueueNode(Generic[T]):
    def __init__(self, val: T):
        self.val = val
        self.next: Optional[QueueNode[T]] = None


class LinkedQueue(Generic[T]):
    """
    Queue implementation using singly linked list.
    O(1) for all operations.
    
    >>> q = LinkedQueue[int]()
    >>> q.enqueue(1)
    >>> q.enqueue(2)
    >>> q.enqueue(3)
    >>> q.dequeue()
    1
    >>> q.front()
    2
    >>> len(q)
    2
    """
    
    def __init__(self):
        self._front: Optional[QueueNode[T]] = None
        self._rear: Optional[QueueNode[T]] = None
        self._size: int = 0
    
    def __len__(self) -> int:
        return self._size
    
    def is_empty(self) -> bool:
        return self._front is None
    
    def enqueue(self, item: T) -> None:
        """Add to back. O(1)"""
        new_node = QueueNode(item)
        if self._rear is None:
            self._front = self._rear = new_node
        else:
            self._rear.next = new_node
            self._rear = new_node
        self._size += 1
    
    def dequeue(self) -> T:
        """Remove from front. O(1)"""
        if self.is_empty():
            raise IndexError("Dequeue from empty queue")
        val = self._front.val
        self._front = self._front.next
        if self._front is None:
            self._rear = None
        self._size -= 1
        return val
    
    def front(self) -> T:
        """View front element. O(1)"""
        if self.is_empty():
            raise IndexError("Front from empty queue")
        return self._front.val


if __name__ == "__main__":
    import doctest
    doctest.testmod()
```

### Python Deque (Double-Ended Queue)

```python
from collections import deque
from typing import List

def deque_operations_demo():
    """
    Python collections.deque operations.
    
    >>> dq = deque()
    >>> dq.append(1)      # Add to right
    >>> dq.append(2)
    >>> dq.appendleft(0)  # Add to left
    >>> list(dq)
    [0, 1, 2]
    >>> dq.pop()          # Remove from right
    2
    >>> dq.popleft()      # Remove from left
    0
    >>> list(dq)
    [1]
    >>> dq.extend([2, 3]) # Extend right
    >>> list(dq)
    [1, 2, 3]
    >>> dq.rotate(1)      # Rotate right by 1
    >>> list(dq)
    [3, 1, 2]
    >>> dq.rotate(-1)     # Rotate left by 1
    >>> list(dq)
    [1, 2, 3]
    """
    pass


def bounded_deque_demo():
    """
    Deque with maximum length (useful for sliding window).
    
    >>> dq = deque(maxlen=3)
    >>> dq.append(1)
    >>> dq.append(2)
    >>> dq.append(3)
    >>> list(dq)
    [1, 2, 3]
    >>> dq.append(4)  # Oldest element dropped
    >>> list(dq)
    [2, 3, 4]
    """
    pass


if __name__ == "__main__":
    import doctest
    doctest.testmod()
```

---

## Key Insights for Correctness

### Circular Queue Index Calculation

```
Circular array visualization:

Capacity = 5
Initial state: front=0, size=0

After enqueue(A, B, C):
Index:  [0] [1] [2] [3] [4]
Data:    A   B   C   -   -
         ↑           ↑
       front      rear=(front+size-1)%cap

After dequeue() twice:
Index:  [0] [1] [2] [3] [4]
Data:    -   -   C   -   -
                 ↑
               front=2, size=1

After enqueue(D, E, F):
Index:  [0] [1] [2] [3] [4]
Data:    F   -   C   D   E
         ↑       ↑
       rear    front

Formula for rear position: (front + size - 1) % capacity
Formula for next enqueue:  (front + size) % capacity
```

### BFS with Queue Pattern

```python
from collections import deque
from typing import List, Optional

def bfs_level_order(root) -> List[List[int]]:
    """
    BFS traversal template.
    Queue stores nodes at current level.
    
    >>> # Represents tree:
    >>> #     3
    >>> #    / \\
    >>> #   9  20
    >>> #     /  \\
    >>> #    15   7
    >>> class TreeNode:
    ...     def __init__(self, val, left=None, right=None):
    ...         self.val = val
    ...         self.left = left
    ...         self.right = right
    >>> root = TreeNode(3, TreeNode(9), TreeNode(20, TreeNode(15), TreeNode(7)))
    >>> bfs_level_order(root)
    [[3], [9, 20], [15, 7]]
    """
    if not root:
        return []
    
    result = []
    queue = deque([root])
    
    while queue:
        level_size = len(queue)
        level = []
        
        for _ in range(level_size):
            node = queue.popleft()
            level.append(node.val)
            
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
        
        result.append(level)
    
    return result


if __name__ == "__main__":
    import doctest
    doctest.testmod()
```

---

## Common Implementations

### Sliding Window Maximum (Monotonic Deque)

```python
from collections import deque
from typing import List

def max_sliding_window(nums: List[int], k: int) -> List[int]:
    """
    Find maximum in each sliding window of size k.
    
    Key insight: Maintain monotonically decreasing deque of indices.
    Front of deque is always the maximum.
    
    Time: O(n) - each element added and removed at most once
    Space: O(k) - deque stores at most k indices
    
    >>> max_sliding_window([1, 3, -1, -3, 5, 3, 6, 7], 3)
    [3, 3, 5, 5, 6, 7]
    >>> max_sliding_window([1], 1)
    [1]
    >>> max_sliding_window([7, 2, 4], 2)
    [7, 4]
    """
    if not nums or k == 0:
        return []
    
    result = []
    dq = deque()  # Store indices, values are monotonically decreasing
    
    for i in range(len(nums)):
        # Remove indices outside current window
        while dq and dq[0] < i - k + 1:
            dq.popleft()
        
        # Remove smaller elements (they'll never be max)
        while dq and nums[dq[-1]] < nums[i]:
            dq.pop()
        
        dq.append(i)
        
        # Start recording results when window is complete
        if i >= k - 1:
            result.append(nums[dq[0]])
    
    return result


def max_sliding_window_visualized(nums: List[int], k: int) -> List[int]:
    """
    Same algorithm with visualization.
    
    >>> max_sliding_window_visualized([1, 3, -1, -3, 5, 3, 6, 7], 3)
    i=0, num=1: deque=[0], window incomplete
    i=1, num=3: remove 1<3, deque=[1], window incomplete
    i=2, num=-1: deque=[1, 2], window [1,3,-1], max=3
    i=3, num=-3: deque=[1, 2, 3], window [3,-1,-3], max=3
    i=4, num=5: remove all<5, deque=[4], window [-1,-3,5], max=5
    i=5, num=3: deque=[4, 5], window [-3,5,3], max=5
    i=6, num=6: remove 3<6, remove 5<6, deque=[6], window [5,3,6], max=6
    i=7, num=7: remove 6<7, deque=[7], window [3,6,7], max=7
    [3, 3, 5, 5, 6, 7]
    """
    result = []
    dq = deque()
    
    for i in range(len(nums)):
        while dq and dq[0] < i - k + 1:
            dq.popleft()
        
        removed = []
        while dq and nums[dq[-1]] < nums[i]:
            removed.append(nums[dq.pop()])
        
        dq.append(i)
        
        output = f"i={i}, num={nums[i]}: "
        if removed:
            output += f"remove {','.join(map(str, removed))}<{nums[i]}, "
        output += f"deque={list(dq)}"
        
        if i >= k - 1:
            result.append(nums[dq[0]])
            output += f", window {nums[i-k+1:i+1]}, max={nums[dq[0]]}"
        else:
            output += ", window incomplete"
        
        print(output)
    
    return result


if __name__ == "__main__":
    import doctest
    doctest.testmod()
```

### Stack Using Queues

```python
from collections import deque

class StackUsingQueues:
    """
    Implement stack using two queues.
    Push: O(n), Pop: O(1)
    
    >>> s = StackUsingQueues()
    >>> s.push(1)
    >>> s.push(2)
    >>> s.top()
    2
    >>> s.pop()
    2
    >>> s.empty()
    False
    """
    
    def __init__(self):
        self.q1 = deque()
        self.q2 = deque()
    
    def push(self, x: int) -> None:
        """Push element. O(n)"""
        self.q2.append(x)
        # Move all elements from q1 to q2
        while self.q1:
            self.q2.append(self.q1.popleft())
        # Swap q1 and q2
        self.q1, self.q2 = self.q2, self.q1
    
    def pop(self) -> int:
        """Pop top element. O(1)"""
        return self.q1.popleft()
    
    def top(self) -> int:
        """Get top element. O(1)"""
        return self.q1[0]
    
    def empty(self) -> bool:
        """Check if empty. O(1)"""
        return len(self.q1) == 0


if __name__ == "__main__":
    import doctest
    doctest.testmod()
```

---

## Step-by-Step Worked Example

### Problem: Number of Islands (BFS)

**Input:**
```
grid = [
  ["1","1","0","0","0"],
  ["1","1","0","0","0"],
  ["0","0","1","0","0"],
  ["0","0","0","1","1"]
]
```
**Output:** `3`

```
BFS flood fill approach:
- Iterate through grid
- When we find unvisited '1', start BFS to mark entire island
- Count number of BFS calls

Island 1: Starting at (0,0)
  Queue: [(0,0)]
  Process (0,0), mark visited, add neighbors (0,1), (1,0)
  Queue: [(0,1), (1,0)]
  Process (0,1), add (1,1)
  Queue: [(1,0), (1,1)]
  Process (1,0), (1,1) already queued
  Queue: [(1,1)]
  Process (1,1), no new neighbors
  Queue: []
  Island 1 complete

Island 2: Starting at (2,2)
  Queue: [(2,2)]
  Process (2,2), no '1' neighbors
  Island 2 complete

Island 3: Starting at (3,3)
  Queue: [(3,3)]
  Process (3,3), add (3,4)
  Queue: [(3,4)]
  Process (3,4), no new neighbors
  Island 3 complete

Total: 3 islands
```

---

## Variants & Extensions

### Priority Queue (Heap-based)

```python
import heapq
from typing import List, Tuple, TypeVar, Generic

T = TypeVar('T')

class PriorityQueue(Generic[T]):
    """
    Min-heap based priority queue.
    
    >>> pq = PriorityQueue[str]()
    >>> pq.push(3, "three")
    >>> pq.push(1, "one")
    >>> pq.push(2, "two")
    >>> pq.pop()
    (1, 'one')
    >>> pq.pop()
    (2, 'two')
    >>> pq.peek()
    (3, 'three')
    """
    
    def __init__(self):
        self._heap: List[Tuple[int, int, T]] = []
        self._index = 0  # For stable sort
    
    def push(self, priority: int, item: T) -> None:
        """Add with priority. O(log n)"""
        heapq.heappush(self._heap, (priority, self._index, item))
        self._index += 1
    
    def pop(self) -> Tuple[int, T]:
        """Remove and return min priority item. O(log n)"""
        if not self._heap:
            raise IndexError("Pop from empty queue")
        priority, _, item = heapq.heappop(self._heap)
        return (priority, item)
    
    def peek(self) -> Tuple[int, T]:
        """View min priority item. O(1)"""
        if not self._heap:
            raise IndexError("Peek from empty queue")
        priority, _, item = self._heap[0]
        return (priority, item)
    
    def __len__(self) -> int:
        return len(self._heap)
    
    def is_empty(self) -> bool:
        return len(self._heap) == 0


if __name__ == "__main__":
    import doctest
    doctest.testmod()
```

### Monotonic Queue (for Sliding Window)

```python
from collections import deque
from typing import List

class MonotonicQueue:
    """
    Monotonically decreasing deque for O(1) max queries.
    
    >>> mq = MonotonicQueue()
    >>> mq.push(3)
    >>> mq.push(1)
    >>> mq.push(2)
    >>> mq.max()
    3
    >>> mq.pop(3)  # Remove 3 (if at front)
    >>> mq.max()
    2
    """
    
    def __init__(self):
        self.dq = deque()
    
    def push(self, val: int) -> None:
        """Add element, maintaining decreasing order. O(1) amortized."""
        while self.dq and self.dq[-1] < val:
            self.dq.pop()
        self.dq.append(val)
    
    def pop(self, val: int) -> None:
        """Remove element if it's at front. O(1)"""
        if self.dq and self.dq[0] == val:
            self.dq.popleft()
    
    def max(self) -> int:
        """Return maximum element. O(1)"""
        return self.dq[0]


if __name__ == "__main__":
    import doctest
    doctest.testmod()
```

---

## Common Interview Problems

| # | Problem | Difficulty | Approach | Time | LeetCode |
|---|---------|------------|----------|------|----------|
| 1 | Implement Queue using Stacks | Easy | Two stacks | O(1)* | [#232](https://leetcode.com/problems/implement-queue-using-stacks/) |
| 2 | Design Circular Queue | Medium | Circular array | O(1) | [#622](https://leetcode.com/problems/design-circular-queue/) |
| 3 | Number of Islands | Medium | BFS | O(m×n) | [#200](https://leetcode.com/problems/number-of-islands/) |
| 4 | Binary Tree Level Order | Medium | BFS | O(n) | [#102](https://leetcode.com/problems/binary-tree-level-order-traversal/) |
| 5 | Sliding Window Maximum | Hard | Monotonic deque | O(n) | [#239](https://leetcode.com/problems/sliding-window-maximum/) |
| 6 | Rotting Oranges | Medium | Multi-source BFS | O(m×n) | [#994](https://leetcode.com/problems/rotting-oranges/) |
| 7 | Shortest Path in Binary Matrix | Medium | BFS | O(n²) | [#1091](https://leetcode.com/problems/shortest-path-in-binary-matrix/) |
| 8 | Open the Lock | Medium | BFS | O(10⁴) | [#752](https://leetcode.com/problems/open-the-lock/) |
| 9 | Design Hit Counter | Medium | Queue/Deque | O(1)* | [#362](https://leetcode.com/problems/design-hit-counter/) |
| 10 | Task Scheduler | Medium | Priority Queue | O(n) | [#621](https://leetcode.com/problems/task-scheduler/) |

---

## Fully Worked Sample Problem

### Problem: Rotting Oranges

**Problem Statement:** You are given an m x n grid where each cell can have one of three values: 0 (empty), 1 (fresh orange), 2 (rotten orange). Every minute, any fresh orange adjacent (4-directionally) to a rotten orange becomes rotten. Return the minimum number of minutes until no cell has a fresh orange. Return -1 if impossible.

**Difficulty:** Medium | [LeetCode #994](https://leetcode.com/problems/rotting-oranges/)

```python
from collections import deque
from typing import List

def oranges_rotting(grid: List[List[int]]) -> int:
    """
    Multi-source BFS from all rotten oranges simultaneously.
    
    Key insight: Add all initially rotten oranges to queue first.
    Each BFS level represents one minute.
    
    Time Complexity: O(m × n)
    Space Complexity: O(m × n) for queue
    
    >>> oranges_rotting([[2,1,1],[1,1,0],[0,1,1]])
    4
    >>> oranges_rotting([[2,1,1],[0,1,1],[1,0,1]])
    -1
    >>> oranges_rotting([[0,2]])
    0
    """
    if not grid:
        return 0
    
    rows, cols = len(grid), len(grid[0])
    queue = deque()
    fresh_count = 0
    
    # Initialize: find all rotten oranges and count fresh
    for r in range(rows):
        for c in range(cols):
            if grid[r][c] == 2:
                queue.append((r, c, 0))  # (row, col, time)
            elif grid[r][c] == 1:
                fresh_count += 1
    
    # Edge case: no fresh oranges
    if fresh_count == 0:
        return 0
    
    directions = [(0, 1), (0, -1), (1, 0), (-1, 0)]
    max_time = 0
    
    # BFS
    while queue:
        r, c, time = queue.popleft()
        max_time = max(max_time, time)
        
        for dr, dc in directions:
            nr, nc = r + dr, c + dc
            
            # Check bounds and if fresh orange
            if 0 <= nr < rows and 0 <= nc < cols and grid[nr][nc] == 1:
                grid[nr][nc] = 2  # Mark as rotten
                fresh_count -= 1
                queue.append((nr, nc, time + 1))
    
    return max_time if fresh_count == 0 else -1


def oranges_rotting_visualized(grid: List[List[int]]) -> int:
    """
    Same solution with step-by-step visualization.
    
    >>> grid = [[2,1,1],[1,1,0],[0,1,1]]
    >>> oranges_rotting_visualized(grid)
    Initial grid:
    [2, 1, 1]
    [1, 1, 0]
    [0, 1, 1]
    Fresh count: 6, Rotten sources: [(0, 0)]
    <BLANKLINE>
    Minute 0: Processing (0, 0)
      Rotting (0, 1), (1, 0)
    Minute 1: Processing (0, 1)
      Rotting (0, 2), (1, 1)
    Minute 1: Processing (1, 0)
    Minute 2: Processing (0, 2)
    Minute 2: Processing (1, 1)
      Rotting (2, 1)
    Minute 3: Processing (2, 1)
      Rotting (2, 2)
    <BLANKLINE>
    Final: 4 minutes, 0 fresh remaining
    4
    """
    from copy import deepcopy
    grid = deepcopy(grid)
    
    rows, cols = len(grid), len(grid[0])
    queue = deque()
    fresh_count = 0
    rotten_sources = []
    
    for r in range(rows):
        for c in range(cols):
            if grid[r][c] == 2:
                queue.append((r, c, 0))
                rotten_sources.append((r, c))
            elif grid[r][c] == 1:
                fresh_count += 1
    
    print("Initial grid:")
    for row in grid:
        print(row)
    print(f"Fresh count: {fresh_count}, Rotten sources: {rotten_sources}")
    print()
    
    if fresh_count == 0:
        return 0
    
    directions = [(0, 1), (0, -1), (1, 0), (-1, 0)]
    max_time = 0
    
    while queue:
        r, c, time = queue.popleft()
        max_time = max(max_time, time)
        
        new_rotten = []
        for dr, dc in directions:
            nr, nc = r + dr, c + dc
            if 0 <= nr < rows and 0 <= nc < cols and grid[nr][nc] == 1:
                grid[nr][nc] = 2
                fresh_count -= 1
                queue.append((nr, nc, time + 1))
                new_rotten.append((nr, nc))
        
        output = f"Minute {time}: Processing ({r}, {c})"
        if new_rotten:
            output += f"\n  Rotting {', '.join(str(p) for p in new_rotten)}"
        print(output)
    
    print(f"\nFinal: {max_time} minutes, {fresh_count} fresh remaining")
    return max_time if fresh_count == 0 else -1


# Unit tests
def test_oranges_rotting():
    assert oranges_rotting([[2,1,1],[1,1,0],[0,1,1]]) == 4
    assert oranges_rotting([[2,1,1],[0,1,1],[1,0,1]]) == -1
    assert oranges_rotting([[0,2]]) == 0
    assert oranges_rotting([[0]]) == 0
    assert oranges_rotting([[1]]) == -1
    assert oranges_rotting([[2,2],[1,1]]) == 1
    print("All tests passed!")


if __name__ == "__main__":
    import doctest
    doctest.testmod()
    test_oranges_rotting()
```

**Pattern(s) Used:** Multi-source BFS, Level-by-Level Processing

---

## Edge Cases & Pitfalls

- **Empty Queue:** Check before dequeue/peek operations
- **Circular Queue Full:** Distinguish full from empty (both have front == back)
- **BFS Visited Check:** Mark visited when adding to queue, not when processing
- **Multi-source BFS:** Initialize queue with all sources for simultaneous expansion
- **Deque Direction:** Remember `append`/`pop` for right, `appendleft`/`popleft` for left
- **Off-by-One in Circular:** Use modulo arithmetic carefully

---

## Follow-ups and Optimization Ideas

1. **Thread-Safe Queue:** Producer-consumer with locks or lock-free
2. **Blocking Queue:** Wait when empty, used in thread pools
3. **Priority Deque:** Min/max at both ends
4. **Streaming Median:** Two heaps (max-heap + min-heap)
5. **Rate Limiter:** Sliding window with queue of timestamps

---

## Practice Checklist

| # | Problem | Difficulty | Link | Pattern |
|---|---------|------------|------|---------|
| 1 | Implement Queue using Stacks | Easy | [LC #232](https://leetcode.com/problems/implement-queue-using-stacks/) | Two Stacks |
| 2 | Implement Stack using Queues | Easy | [LC #225](https://leetcode.com/problems/implement-stack-using-queues/) | Two Queues |
| 3 | Design Circular Queue | Medium | [LC #622](https://leetcode.com/problems/design-circular-queue/) | Circular Array |
| 4 | Design Circular Deque | Medium | [LC #641](https://leetcode.com/problems/design-circular-deque/) | Circular Array |
| 5 | Number of Islands | Medium | [LC #200](https://leetcode.com/problems/number-of-islands/) | BFS |
| 6 | Binary Tree Level Order | Medium | [LC #102](https://leetcode.com/problems/binary-tree-level-order-traversal/) | BFS |
| 7 | Binary Tree Zigzag Level Order | Medium | [LC #103](https://leetcode.com/problems/binary-tree-zigzag-level-order-traversal/) | BFS + Deque |
| 8 | Rotting Oranges | Medium | [LC #994](https://leetcode.com/problems/rotting-oranges/) | Multi-source BFS |
| 9 | Walls and Gates | Medium | [LC #286](https://leetcode.com/problems/walls-and-gates/) | Multi-source BFS |
| 10 | Sliding Window Maximum | Hard | [LC #239](https://leetcode.com/problems/sliding-window-maximum/) | Monotonic Deque |
| 11 | Shortest Path in Binary Matrix | Medium | [LC #1091](https://leetcode.com/problems/shortest-path-in-binary-matrix/) | BFS |
| 12 | Open the Lock | Medium | [LC #752](https://leetcode.com/problems/open-the-lock/) | BFS |
| 13 | Word Ladder | Hard | [LC #127](https://leetcode.com/problems/word-ladder/) | BFS |
| 14 | Minimum Knight Moves | Medium | [LC #1197](https://leetcode.com/problems/minimum-knight-moves/) | BFS |
| 15 | Shortest Bridge | Medium | [LC #934](https://leetcode.com/problems/shortest-bridge/) | DFS + BFS |
| 16 | 01 Matrix | Medium | [LC #542](https://leetcode.com/problems/01-matrix/) | Multi-source BFS |
| 17 | As Far from Land as Possible | Medium | [LC #1162](https://leetcode.com/problems/as-far-from-land-as-possible/) | Multi-source BFS |
| 18 | Task Scheduler | Medium | [LC #621](https://leetcode.com/problems/task-scheduler/) | Priority Queue |
| 19 | Design Hit Counter | Medium | [LC #362](https://leetcode.com/problems/design-hit-counter/) | Queue |
| 20 | Moving Average from Data Stream | Easy | [LC #346](https://leetcode.com/problems/moving-average-from-data-stream/) | Queue |

---

## Further Reading & References

- [CLRS Chapter 10.1 - Stacks and Queues](https://mitpress.mit.edu/books/introduction-algorithms-third-edition)
- [Python collections.deque](https://docs.python.org/3/library/collections.html#collections.deque)
- [VisuAlgo - Queue](https://visualgo.net/en/list)
- [GeeksforGeeks - Queue](https://www.geeksforgeeks.org/queue-data-structure/)
- [LeetCode Queue Problems](https://leetcode.com/tag/queue/)

---

## Flashcard Summary

**Q: What is FIFO?**
A: First-In-First-Out: the oldest element is removed first (like a line).

**Q: How does circular queue handle wrap-around?**
A: Use modulo: `rear = (front + size) % capacity`. Distinguishes full vs empty by tracking size.

**Q: Why use deque for sliding window maximum?**
A: Monotonic deque maintains decreasing order; front is always max. O(1) amortized per element.

**Q: Difference between queue in BFS vs DFS?**
A: BFS uses queue (FIFO) for level-order; DFS uses stack (LIFO) or recursion.

**Q: What is multi-source BFS?**
A: Initialize queue with all source nodes. Expands from all sources simultaneously.

**Q: Time complexity of monotonic deque operations?**
A: Amortized O(1) - each element pushed and popped at most once across all operations.
