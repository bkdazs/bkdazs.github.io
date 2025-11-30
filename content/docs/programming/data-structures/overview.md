---
title: "Data Structures Overview"
topic: "Data Structures"
difficulty: "Foundational"
tags: ["data-structures", "adt", "overview", "complexity"]
status: "complete"
weight: 1
---

# Data Structures Overview

## Short Summary

A **data structure** is a specialized format for organizing, storing, and managing data to enable efficient access and modification. Data structures form the foundation of algorithm design—choosing the right structure can mean the difference between O(n²) and O(n log n) solutions. In interviews, understanding when and why to use each structure is as important as knowing how to implement them.

---

## Why It Matters in Interviews

- **Foundation of Problem Solving:** Nearly every coding problem requires selecting appropriate data structures
- **Time-Space Tradeoffs:** Interviewers assess your ability to reason about complexity implications
- **Real-World Applications:** Data structure choices directly impact system performance in production

---

## Formal Definition / Properties / Invariants

### Abstract Data Type (ADT) vs Data Structure

| Concept | Definition | Example |
|---------|------------|---------|
| **ADT** | Mathematical model defining behavior (operations) without implementation details | Stack ADT: push, pop, peek, isEmpty |
| **Data Structure** | Concrete implementation of an ADT | Array-based Stack, Linked-list Stack |

### Key Properties to Analyze

1. **Access Pattern:** Random vs Sequential
2. **Ordering:** Sorted, Insertion-order, Unordered
3. **Uniqueness:** Allows duplicates or not
4. **Mutability:** Static vs Dynamic sizing
5. **Memory Layout:** Contiguous vs Node-based

---

## Time & Space Complexity

### Complexity Analysis Framework

| Notation | Name | Meaning |
|----------|------|---------|
| O(1) | Constant | Independent of input size |
| O(log n) | Logarithmic | Halving each step |
| O(n) | Linear | Proportional to input |
| O(n log n) | Linearithmic | Efficient sorting bound |
| O(n²) | Quadratic | Nested iterations |
| O(2ⁿ) | Exponential | Combinatorial explosion |

### Space Complexity Considerations

- **In-place:** O(1) auxiliary space
- **Out-of-place:** Additional memory proportional to input
- **Recursion Stack:** Implicit space usage

---

## Typical Operations & Pseudo-Implementation

### Universal Operations

```
ADT Operations:
├── Create/Initialize
├── Insert/Add
├── Delete/Remove
├── Search/Find
├── Access/Get
├── Update/Modify
├── Traverse/Iterate
└── Size/isEmpty
```

### Python: Generic Interface Pattern

```python
from abc import ABC, abstractmethod
from typing import TypeVar, Generic, Optional, Iterator

T = TypeVar('T')

class Collection(ABC, Generic[T]):
    """Abstract base class for collection data structures."""
    
    @abstractmethod
    def add(self, item: T) -> None:
        """Add an item to the collection."""
        pass
    
    @abstractmethod
    def remove(self, item: T) -> bool:
        """Remove an item from the collection. Returns True if found."""
        pass
    
    @abstractmethod
    def contains(self, item: T) -> bool:
        """Check if item exists in the collection."""
        pass
    
    @abstractmethod
    def size(self) -> int:
        """Return the number of items in the collection."""
        pass
    
    @abstractmethod
    def is_empty(self) -> bool:
        """Check if the collection is empty."""
        pass
    
    @abstractmethod
    def __iter__(self) -> Iterator[T]:
        """Return an iterator over the collection."""
        pass


# Example: Testing the interface
def test_collection_interface():
    """
    >>> class SimpleList(Collection[int]):
    ...     def __init__(self):
    ...         self._data = []
    ...     def add(self, item): self._data.append(item)
    ...     def remove(self, item):
    ...         if item in self._data:
    ...             self._data.remove(item)
    ...             return True
    ...         return False
    ...     def contains(self, item): return item in self._data
    ...     def size(self): return len(self._data)
    ...     def is_empty(self): return len(self._data) == 0
    ...     def __iter__(self): return iter(self._data)
    >>> sl = SimpleList()
    >>> sl.is_empty()
    True
    >>> sl.add(5)
    >>> sl.add(10)
    >>> sl.size()
    2
    >>> sl.contains(5)
    True
    >>> sl.remove(5)
    True
    >>> list(sl)
    [10]
    """
    pass

if __name__ == "__main__":
    import doctest
    doctest.testmod()
```

---

## Key Insights for Correctness

### Choosing the Right Data Structure

```
Decision Framework:
│
├── Need O(1) access by index?
│   └── YES → Array/List
│
├── Need O(1) insert/delete at ends?
│   └── YES → Deque/Linked List
│
├── Need O(1) lookup by key?
│   └── YES → Hash Table
│
├── Need sorted order maintained?
│   └── YES → BST/Balanced Tree/Sorted Array
│
├── Need priority-based access?
│   └── YES → Heap/Priority Queue
│
├── Need LIFO access?
│   └── YES → Stack
│
├── Need FIFO access?
│   └── YES → Queue
│
└── Need set operations (union/intersection)?
    └── YES → Set (Hash-based or Tree-based)
```

---

## Common Implementations

### Data Structure Taxonomy

```
Data Structures
├── Linear
│   ├── Static
│   │   └── Array
│   └── Dynamic
│       ├── Linked List (Singly, Doubly, Circular)
│       ├── Stack
│       ├── Queue
│       └── Deque
│
├── Non-Linear
│   ├── Trees
│   │   ├── Binary Tree
│   │   ├── BST
│   │   ├── Balanced Trees (AVL, Red-Black)
│   │   ├── B-Tree/B+Tree
│   │   ├── Trie
│   │   ├── Segment Tree
│   │   └── Heap
│   └── Graphs
│       ├── Directed/Undirected
│       ├── Weighted/Unweighted
│       └── Cyclic/Acyclic
│
├── Hash-Based
│   ├── Hash Table
│   ├── Hash Set
│   └── Hash Map
│
└── Specialized
    ├── Union-Find
    ├── Bloom Filter
    ├── Skip List
    └── Suffix Array/Tree
```

---

## Step-by-Step Worked Example

### Problem: Choose Data Structure for Different Scenarios

**Scenario 1: LRU Cache**
```
Requirements:
- O(1) get(key)
- O(1) put(key, value)
- Evict least recently used when capacity exceeded

Analysis:
- Need O(1) lookup → Hash Map
- Need to track access order → Doubly Linked List
- Solution: HashMap + Doubly Linked List

Result: O(1) for both operations
```

**Scenario 2: Find Median in Stream**
```
Requirements:
- Insert numbers in stream
- O(log n) insert
- O(1) find median

Analysis:
- Need to track two halves of sorted data
- Quick access to middle elements
- Solution: Two Heaps (Max-heap for lower half, Min-heap for upper half)

Result: O(log n) insert, O(1) median
```

**Scenario 3: Autocomplete System**
```
Requirements:
- Prefix-based search
- Return all words with given prefix
- O(k) where k = prefix length

Analysis:
- Need prefix matching → Trie
- Store words at nodes
- Solution: Trie with word storage at each node

Result: O(k) lookup where k = prefix length
```

---

## Variants & Extensions

### Memory vs Time Tradeoffs

| Priority | Favor | Example |
|----------|-------|---------|
| Fast lookup | More memory | Hash table with low load factor |
| Memory efficiency | Slower operations | Compressed structures |
| Cache efficiency | Contiguous memory | Arrays over linked lists |

### Language-Specific Implementations

| ADT | Python | Java | C++ |
|-----|--------|------|-----|
| Dynamic Array | `list` | `ArrayList` | `vector` |
| Linked List | Custom | `LinkedList` | `list` |
| Stack | `list` (append/pop) | `Stack`, `Deque` | `stack` |
| Queue | `collections.deque` | `Queue`, `Deque` | `queue` |
| Hash Map | `dict` | `HashMap` | `unordered_map` |
| Hash Set | `set` | `HashSet` | `unordered_set` |
| Sorted Map | `sortedcontainers.SortedDict` | `TreeMap` | `map` |
| Heap | `heapq` | `PriorityQueue` | `priority_queue` |

---

## Common Interview Problems

| # | Problem | Difficulty | Key Data Structure | LeetCode |
|---|---------|------------|-------------------|----------|
| 1 | Two Sum | Easy | Hash Map | [#1](https://leetcode.com/problems/two-sum/) |
| 2 | LRU Cache | Medium | HashMap + DLL | [#146](https://leetcode.com/problems/lru-cache/) |
| 3 | Find Median from Data Stream | Hard | Two Heaps | [#295](https://leetcode.com/problems/find-median-from-data-stream/) |
| 4 | Implement Trie | Medium | Trie | [#208](https://leetcode.com/problems/implement-trie-prefix-tree/) |
| 5 | Design Twitter | Medium | HashMap + Heap | [#355](https://leetcode.com/problems/design-twitter/) |

---

## Fully Worked Sample Problem

### Problem: Design a Stack with O(1) Min Operation

**Problem Statement:** Design a stack that supports push, pop, top, and retrieving the minimum element in constant time.

**Difficulty:** Medium | [LeetCode #155](https://leetcode.com/problems/min-stack/)

```python
class MinStack:
    """
    Stack supporting O(1) min retrieval.
    
    Approach: Use auxiliary stack to track minimums.
    Each push to aux stack stores the current min at that state.
    
    Time Complexity: O(1) for all operations
    Space Complexity: O(n) for auxiliary stack
    
    >>> stack = MinStack()
    >>> stack.push(-2)
    >>> stack.push(0)
    >>> stack.push(-3)
    >>> stack.get_min()
    -3
    >>> stack.pop()
    >>> stack.top()
    0
    >>> stack.get_min()
    -2
    """
    
    def __init__(self):
        self.stack: list[int] = []
        self.min_stack: list[int] = []  # Tracks min at each state
    
    def push(self, val: int) -> None:
        self.stack.append(val)
        # Push current min to min_stack
        if not self.min_stack or val <= self.min_stack[-1]:
            self.min_stack.append(val)
        else:
            self.min_stack.append(self.min_stack[-1])
    
    def pop(self) -> None:
        if self.stack:
            self.stack.pop()
            self.min_stack.pop()
    
    def top(self) -> int:
        return self.stack[-1] if self.stack else -1
    
    def get_min(self) -> int:
        return self.min_stack[-1] if self.min_stack else -1


# Alternative: Space-optimized version
class MinStackOptimized:
    """
    Space-optimized version storing only when min changes.
    
    >>> stack = MinStackOptimized()
    >>> stack.push(3)
    >>> stack.push(2)
    >>> stack.push(2)
    >>> stack.push(1)
    >>> stack.get_min()
    1
    >>> stack.pop()
    >>> stack.get_min()
    2
    """
    
    def __init__(self):
        self.stack: list[int] = []
        self.min_stack: list[tuple[int, int]] = []  # (value, count)
    
    def push(self, val: int) -> None:
        self.stack.append(val)
        if not self.min_stack or val < self.min_stack[-1][0]:
            self.min_stack.append((val, 1))
        elif val == self.min_stack[-1][0]:
            self.min_stack[-1] = (val, self.min_stack[-1][1] + 1)
    
    def pop(self) -> None:
        if self.stack:
            val = self.stack.pop()
            if val == self.min_stack[-1][0]:
                if self.min_stack[-1][1] == 1:
                    self.min_stack.pop()
                else:
                    self.min_stack[-1] = (val, self.min_stack[-1][1] - 1)
    
    def top(self) -> int:
        return self.stack[-1] if self.stack else -1
    
    def get_min(self) -> int:
        return self.min_stack[-1][0] if self.min_stack else -1


if __name__ == "__main__":
    import doctest
    doctest.testmod()
```

**Pattern(s) Used:** Auxiliary Data Structure, State Tracking

---

## Edge Cases & Pitfalls

- **Empty Structure Operations:** Always check for empty before peek/pop/remove
- **Integer Overflow:** Consider bounds in sum/product operations
- **Null/None Handling:** Distinguish between "not found" and "value is None"
- **Shallow vs Deep Copy:** Understand reference semantics when storing objects
- **Concurrent Modification:** Avoid modifying during iteration
- **Hash Collisions:** Understand implications for hash-based structures

---

## Follow-ups and Optimization Ideas

1. **Thread-Safety:** How to make data structures concurrent-safe?
2. **Persistence:** How to maintain history of all versions?
3. **Cache-Oblivious:** Design structures optimal for any cache size
4. **External Memory:** Handle data larger than RAM
5. **Compression:** Trade computation for memory

---

## Practice Checklist

| # | Problem | Difficulty | Link |
|---|---------|------------|------|
| 1 | Two Sum | Easy | [LC #1](https://leetcode.com/problems/two-sum/) |
| 2 | Valid Parentheses | Easy | [LC #20](https://leetcode.com/problems/valid-parentheses/) |
| 3 | Merge Two Sorted Lists | Easy | [LC #21](https://leetcode.com/problems/merge-two-sorted-lists/) |
| 4 | Min Stack | Medium | [LC #155](https://leetcode.com/problems/min-stack/) |
| 5 | LRU Cache | Medium | [LC #146](https://leetcode.com/problems/lru-cache/) |
| 6 | Implement Queue using Stacks | Easy | [LC #232](https://leetcode.com/problems/implement-queue-using-stacks/) |
| 7 | Kth Largest Element | Medium | [LC #215](https://leetcode.com/problems/kth-largest-element-in-an-array/) |
| 8 | Implement Trie | Medium | [LC #208](https://leetcode.com/problems/implement-trie-prefix-tree/) |
| 9 | Design HashMap | Easy | [LC #706](https://leetcode.com/problems/design-hashmap/) |
| 10 | Serialize and Deserialize BST | Medium | [LC #449](https://leetcode.com/problems/serialize-and-deserialize-bst/) |

---

## Further Reading & References

- [CLRS - Introduction to Algorithms, Chapter 10-14](https://mitpress.mit.edu/books/introduction-algorithms-third-edition)
- [VisuAlgo - Data Structure Visualizations](https://visualgo.net/)
- [GeeksforGeeks - Data Structures](https://www.geeksforgeeks.org/data-structures/)
- [Big-O Cheat Sheet](https://www.bigocheatsheet.com/)
- [cp-algorithms](https://cp-algorithms.com/)

---

## Flashcard Summary

**Q: What is the difference between ADT and Data Structure?**
A: ADT defines what operations are supported (interface); Data Structure defines how it's implemented (concrete).

**Q: When to use Hash Table vs BST?**
A: Hash Table for O(1) average lookup without ordering; BST for O(log n) with sorted order maintained.

**Q: What data structures support O(1) access to minimum?**
A: Min-Heap (O(1) get-min, O(log n) extract), or auxiliary min-tracking in stack.

**Q: How to choose between Array and Linked List?**
A: Array for random access and cache efficiency; Linked List for frequent insertions/deletions at arbitrary positions.

**Q: What is amortized analysis?**
A: Average time per operation over worst-case sequence (e.g., dynamic array resize is O(1) amortized).

**Q: Name a probabilistic data structure.**
A: Bloom Filter—space-efficient set membership with false positives but no false negatives.
