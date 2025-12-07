---
title: "Linked Lists"
topic: "Data Structures"
difficulty: "Fundamental"
tags: ["linked-list", "singly-linked", "doubly-linked", "circular", "pointers"]
status: "complete"
weight: 4
---

# Linked Lists

## Short Summary

A **linked list** is a linear data structure where elements (nodes) are stored in non-contiguous memory, with each node containing data and a reference (pointer) to the next node. Unlike arrays, linked lists offer O(1) insertion/deletion at known positions but sacrifice O(1) random access. They're fundamental for understanding pointers and memory management, and form the basis for stacks, queues, and more complex structures.

---

## Why It Matters in Interviews

- **Pointer Manipulation:** Tests understanding of references and memory
- **Common Patterns:** Reversal, cycle detection, merge operations appear frequently
- **Foundation:** Building block for LRU Cache, adjacency lists, and hash table chaining

---

## Formal Definition / Properties / Invariants

### Types of Linked Lists

| Type | Description | Use Case |
|------|-------------|----------|
| **Singly Linked** | Each node points to next only | Stack, simple list |
| **Doubly Linked** | Nodes point to both next and prev | LRU cache, deque |
| **Circular** | Last node points back to first | Round-robin scheduling |
| **Circular Doubly** | Doubly linked + circular | Efficient both-end operations |

### Key Invariants
```
head → node1 → node2 → ... → nodeN → null  (Singly)
null ← head ↔ node1 ↔ node2 ↔ ... ↔ tail → null  (Doubly)
```

### Node Structure
```
┌───────────────────┐     ┌───────────────────┐
│  data  │  next ───┼────►│  data  │  next ───┼────► null
└───────────────────┘     └───────────────────┘
    Node 1                     Node 2
```

---

## Time & Space Complexity

| Operation | Singly Linked | Doubly Linked | Notes |
|-----------|---------------|---------------|-------|
| Access by index | O(n) | O(n) | Must traverse |
| Search | O(n) | O(n) | Linear scan |
| Insert at head | O(1) | O(1) | Pointer update |
| Insert at tail | O(n) / O(1)* | O(1)* | *with tail pointer |
| Insert at position | O(n) | O(n) | Find position first |
| Delete at head | O(1) | O(1) | Pointer update |
| Delete at tail | O(n) | O(1)* | Singly needs traversal |
| Delete at position | O(n) | O(n) | Find position first |

**Space Complexity:** O(n) for n nodes + pointer overhead per node

### Memory Overhead
- Singly: 1 pointer per node (8 bytes on 64-bit)
- Doubly: 2 pointers per node (16 bytes on 64-bit)

---

## Typical Operations & Pseudo-Implementation

### Singly Linked List Implementation

```python
from typing import TypeVar, Generic, Optional, Iterator

T = TypeVar('T')

class ListNode(Generic[T]):
    """Node for singly linked list."""
    
    def __init__(self, val: T, next: Optional['ListNode[T]'] = None):
        self.val = val
        self.next = next
    
    def __repr__(self) -> str:
        return f"ListNode({self.val})"


class SinglyLinkedList(Generic[T]):
    """
    Singly linked list implementation.
    
    >>> ll = SinglyLinkedList[int]()
    >>> ll.append(1)
    >>> ll.append(2)
    >>> ll.append(3)
    >>> list(ll)
    [1, 2, 3]
    >>> ll.prepend(0)
    >>> list(ll)
    [0, 1, 2, 3]
    >>> ll.delete(2)
    True
    >>> list(ll)
    [0, 1, 3]
    >>> ll.reverse()
    >>> list(ll)
    [3, 1, 0]
    """
    
    def __init__(self):
        self.head: Optional[ListNode[T]] = None
        self.tail: Optional[ListNode[T]] = None
        self._size: int = 0
    
    def __len__(self) -> int:
        return self._size
    
    def __iter__(self) -> Iterator[T]:
        current = self.head
        while current:
            yield current.val
            current = current.next
    
    def is_empty(self) -> bool:
        return self.head is None
    
    def prepend(self, val: T) -> None:
        """Insert at beginning. O(1)"""
        new_node = ListNode(val, self.head)
        self.head = new_node
        if self.tail is None:
            self.tail = new_node
        self._size += 1
    
    def append(self, val: T) -> None:
        """Insert at end. O(1) with tail pointer."""
        new_node = ListNode(val)
        if self.tail is None:
            self.head = self.tail = new_node
        else:
            self.tail.next = new_node
            self.tail = new_node
        self._size += 1
    
    def insert_at(self, index: int, val: T) -> bool:
        """Insert at index. O(n)"""
        if index < 0 or index > self._size:
            return False
        
        if index == 0:
            self.prepend(val)
            return True
        
        current = self.head
        for _ in range(index - 1):
            current = current.next
        
        new_node = ListNode(val, current.next)
        current.next = new_node
        if new_node.next is None:
            self.tail = new_node
        self._size += 1
        return True
    
    def delete(self, val: T) -> bool:
        """Delete first occurrence of value. O(n)"""
        if self.head is None:
            return False
        
        # Delete head
        if self.head.val == val:
            self.head = self.head.next
            if self.head is None:
                self.tail = None
            self._size -= 1
            return True
        
        # Find predecessor of node to delete
        current = self.head
        while current.next and current.next.val != val:
            current = current.next
        
        if current.next is None:
            return False  # Not found
        
        # Delete node
        current.next = current.next.next
        if current.next is None:
            self.tail = current
        self._size -= 1
        return True
    
    def delete_at(self, index: int) -> Optional[T]:
        """Delete at index. O(n)"""
        if index < 0 or index >= self._size or self.head is None:
            return None
        
        if index == 0:
            val = self.head.val
            self.head = self.head.next
            if self.head is None:
                self.tail = None
            self._size -= 1
            return val
        
        current = self.head
        for _ in range(index - 1):
            current = current.next
        
        val = current.next.val
        current.next = current.next.next
        if current.next is None:
            self.tail = current
        self._size -= 1
        return val
    
    def find(self, val: T) -> int:
        """Find index of value. O(n). Returns -1 if not found."""
        current = self.head
        index = 0
        while current:
            if current.val == val:
                return index
            current = current.next
            index += 1
        return -1
    
    def reverse(self) -> None:
        """Reverse list in place. O(n) time, O(1) space."""
        self.tail = self.head
        prev = None
        current = self.head
        
        while current:
            next_node = current.next
            current.next = prev
            prev = current
            current = next_node
        
        self.head = prev
    
    def get_middle(self) -> Optional[ListNode[T]]:
        """Get middle node using slow/fast pointers. O(n)"""
        if not self.head:
            return None
        
        slow = fast = self.head
        while fast.next and fast.next.next:
            slow = slow.next
            fast = fast.next.next
        
        return slow


if __name__ == "__main__":
    import doctest
    doctest.testmod()
```

### Doubly Linked List Implementation

```python
from typing import TypeVar, Generic, Optional, Iterator

T = TypeVar('T')

class DoublyListNode(Generic[T]):
    """Node for doubly linked list."""
    
    def __init__(self, val: T, 
                 prev: Optional['DoublyListNode[T]'] = None,
                 next: Optional['DoublyListNode[T]'] = None):
        self.val = val
        self.prev = prev
        self.next = next


class DoublyLinkedList(Generic[T]):
    """
    Doubly linked list with sentinel nodes for cleaner operations.
    
    >>> dll = DoublyLinkedList[int]()
    >>> dll.append(1)
    >>> dll.append(2)
    >>> dll.prepend(0)
    >>> list(dll)
    [0, 1, 2]
    >>> dll.pop()
    2
    >>> dll.pop_left()
    0
    >>> list(dll)
    [1]
    """
    
    def __init__(self):
        # Sentinel nodes simplify edge cases
        self._head = DoublyListNode(None)  # Dummy head
        self._tail = DoublyListNode(None)  # Dummy tail
        self._head.next = self._tail
        self._tail.prev = self._head
        self._size = 0
    
    def __len__(self) -> int:
        return self._size
    
    def __iter__(self) -> Iterator[T]:
        current = self._head.next
        while current != self._tail:
            yield current.val
            current = current.next
    
    def _add_between(self, val: T, predecessor: DoublyListNode[T], 
                     successor: DoublyListNode[T]) -> DoublyListNode[T]:
        """Insert between two nodes. O(1)"""
        new_node = DoublyListNode(val, predecessor, successor)
        predecessor.next = new_node
        successor.prev = new_node
        self._size += 1
        return new_node
    
    def _remove_node(self, node: DoublyListNode[T]) -> T:
        """Remove a node. O(1)"""
        predecessor = node.prev
        successor = node.next
        predecessor.next = successor
        successor.prev = predecessor
        self._size -= 1
        return node.val
    
    def prepend(self, val: T) -> None:
        """Insert at beginning. O(1)"""
        self._add_between(val, self._head, self._head.next)
    
    def append(self, val: T) -> None:
        """Insert at end. O(1)"""
        self._add_between(val, self._tail.prev, self._tail)
    
    def pop_left(self) -> T:
        """Remove from beginning. O(1)"""
        if self._size == 0:
            raise IndexError("pop from empty list")
        return self._remove_node(self._head.next)
    
    def pop(self) -> T:
        """Remove from end. O(1)"""
        if self._size == 0:
            raise IndexError("pop from empty list")
        return self._remove_node(self._tail.prev)
    
    def move_to_end(self, node: DoublyListNode[T]) -> None:
        """Move existing node to end. O(1) - useful for LRU cache."""
        # Remove from current position
        node.prev.next = node.next
        node.next.prev = node.prev
        
        # Add at end
        node.prev = self._tail.prev
        node.next = self._tail
        self._tail.prev.next = node
        self._tail.prev = node


if __name__ == "__main__":
    import doctest
    doctest.testmod()
```

---

## Key Insights for Correctness

### Pointer Manipulation Visualization

```
=== Insertion at Position ===

Before: A → B → D → null
        Insert C after B

Step 1: Find B (predecessor)
Step 2: Create C with next pointing to D
        C → D
Step 3: Update B's next to C
        A → B → C → D → null

=== Deletion ===

Before: A → B → C → null
        Delete B

Step 1: Find predecessor (A)
Step 2: Update A's next to skip B
        A → C → null
        B → C (B is now unreachable, garbage collected)
```

### Common Patterns

1. **Dummy Head:** Simplifies edge cases for insertion/deletion at head
2. **Two Pointers:** Fast/slow for cycle detection, middle finding
3. **Prev Pointer:** Track predecessor during traversal for deletion

---

## Common Implementations

### Essential Linked List Operations

```python
from typing import Optional

class ListNode:
    def __init__(self, val: int = 0, next: 'ListNode' = None):
        self.val = val
        self.next = next


def reverse_list(head: Optional[ListNode]) -> Optional[ListNode]:
    """
    Reverse linked list iteratively.
    
    Time: O(n), Space: O(1)
    
    >>> # Create: 1 -> 2 -> 3
    >>> head = ListNode(1, ListNode(2, ListNode(3)))
    >>> reversed_head = reverse_list(head)
    >>> [reversed_head.val, reversed_head.next.val, reversed_head.next.next.val]
    [3, 2, 1]
    """
    prev = None
    current = head
    
    while current:
        next_temp = current.next  # Save next
        current.next = prev       # Reverse pointer
        prev = current            # Move prev forward
        current = next_temp       # Move current forward
    
    return prev


def reverse_list_recursive(head: Optional[ListNode]) -> Optional[ListNode]:
    """
    Reverse linked list recursively.
    
    Time: O(n), Space: O(n) stack
    
    >>> head = ListNode(1, ListNode(2, ListNode(3)))
    >>> reversed_head = reverse_list_recursive(head)
    >>> [reversed_head.val, reversed_head.next.val, reversed_head.next.next.val]
    [3, 2, 1]
    """
    # Base case
    if not head or not head.next:
        return head
    
    # Recursive case
    new_head = reverse_list_recursive(head.next)
    head.next.next = head  # Reverse pointer
    head.next = None       # Prevent cycle
    
    return new_head


def has_cycle(head: Optional[ListNode]) -> bool:
    """
    Detect cycle using Floyd's algorithm (slow/fast pointers).
    
    Time: O(n), Space: O(1)
    
    >>> # Create: 1 -> 2 -> 3 -> 2 (cycle)
    >>> node1 = ListNode(1)
    >>> node2 = ListNode(2)
    >>> node3 = ListNode(3)
    >>> node1.next = node2
    >>> node2.next = node3
    >>> node3.next = node2  # Cycle
    >>> has_cycle(node1)
    True
    >>> # No cycle
    >>> linear = ListNode(1, ListNode(2, ListNode(3)))
    >>> has_cycle(linear)
    False
    """
    if not head or not head.next:
        return False
    
    slow = head
    fast = head.next
    
    while slow != fast:
        if not fast or not fast.next:
            return False
        slow = slow.next
        fast = fast.next.next
    
    return True


def find_cycle_start(head: Optional[ListNode]) -> Optional[ListNode]:
    """
    Find the node where cycle begins.
    
    Floyd's algorithm: After detection, move one pointer to head
    and advance both by 1 until they meet.
    
    Time: O(n), Space: O(1)
    """
    if not head or not head.next:
        return None
    
    # Phase 1: Detect cycle
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        if slow == fast:
            break
    else:
        return None  # No cycle
    
    # Phase 2: Find cycle start
    # Distance from head to cycle start = distance from meeting point to cycle start
    slow = head
    while slow != fast:
        slow = slow.next
        fast = fast.next
    
    return slow


def merge_two_sorted_lists(l1: Optional[ListNode], 
                           l2: Optional[ListNode]) -> Optional[ListNode]:
    """
    Merge two sorted linked lists.
    
    Time: O(n + m), Space: O(1)
    
    >>> l1 = ListNode(1, ListNode(2, ListNode(4)))
    >>> l2 = ListNode(1, ListNode(3, ListNode(4)))
    >>> merged = merge_two_sorted_lists(l1, l2)
    >>> result = []
    >>> while merged:
    ...     result.append(merged.val)
    ...     merged = merged.next
    >>> result
    [1, 1, 2, 3, 4, 4]
    """
    dummy = ListNode()
    current = dummy
    
    while l1 and l2:
        if l1.val <= l2.val:
            current.next = l1
            l1 = l1.next
        else:
            current.next = l2
            l2 = l2.next
        current = current.next
    
    # Attach remaining
    current.next = l1 if l1 else l2
    
    return dummy.next


def get_middle(head: Optional[ListNode]) -> Optional[ListNode]:
    """
    Find middle node (left-middle if even length).
    
    Time: O(n), Space: O(1)
    
    >>> # 1 -> 2 -> 3 -> 4 -> 5
    >>> head = ListNode(1, ListNode(2, ListNode(3, ListNode(4, ListNode(5)))))
    >>> get_middle(head).val
    3
    >>> # 1 -> 2 -> 3 -> 4
    >>> head = ListNode(1, ListNode(2, ListNode(3, ListNode(4))))
    >>> get_middle(head).val
    2
    """
    slow = fast = head
    while fast.next and fast.next.next:
        slow = slow.next
        fast = fast.next.next
    return slow


def remove_nth_from_end(head: Optional[ListNode], n: int) -> Optional[ListNode]:
    """
    Remove nth node from end in single pass.
    
    Time: O(length), Space: O(1)
    
    >>> head = ListNode(1, ListNode(2, ListNode(3, ListNode(4, ListNode(5)))))
    >>> result = remove_nth_from_end(head, 2)  # Remove 4
    >>> values = []
    >>> while result:
    ...     values.append(result.val)
    ...     result = result.next
    >>> values
    [1, 2, 3, 5]
    """
    dummy = ListNode(0, head)
    first = second = dummy
    
    # Advance first n+1 steps
    for _ in range(n + 1):
        first = first.next
    
    # Move both until first reaches end
    while first:
        first = first.next
        second = second.next
    
    # Skip the target node
    second.next = second.next.next
    
    return dummy.next


if __name__ == "__main__":
    import doctest
    doctest.testmod()
```

---

## Step-by-Step Worked Example

### Problem: Reverse Nodes in k-Group

**Input:** `head = [1,2,3,4,5]`, `k = 2`
**Output:** `[2,1,4,3,5]`

```
Initial: 1 → 2 → 3 → 4 → 5 → null

Step 1: Process first group [1,2]
        Before: dummy → 1 → 2 → 3 → 4 → 5
        Reverse 1,2: dummy → 2 → 1 → 3 → 4 → 5
        
Step 2: Process second group [3,4]
        Before: 2 → 1 → 3 → 4 → 5
                        ↑
                     prev_end
        Reverse 3,4: 2 → 1 → 4 → 3 → 5
        
Step 3: Check third group [5]
        Only 1 node, less than k
        Don't reverse, keep as is
        
Final: 2 → 1 → 4 → 3 → 5 → null
```

---

## Variants & Extensions

### Circular Linked List

```python
class CircularLinkedList:
    """
    Circular singly linked list where last node points to first.
    
    >>> cll = CircularLinkedList()
    >>> cll.append(1)
    >>> cll.append(2)
    >>> cll.append(3)
    >>> cll.traverse()
    [1, 2, 3]
    >>> cll.rotate()  # Move head to next
    >>> cll.traverse()
    [2, 3, 1]
    """
    
    def __init__(self):
        self.head = None
        self._size = 0
    
    def append(self, val):
        new_node = ListNode(val)
        if not self.head:
            self.head = new_node
            new_node.next = self.head
        else:
            current = self.head
            while current.next != self.head:
                current = current.next
            current.next = new_node
            new_node.next = self.head
        self._size += 1
    
    def traverse(self):
        if not self.head:
            return []
        result = [self.head.val]
        current = self.head.next
        while current != self.head:
            result.append(current.val)
            current = current.next
        return result
    
    def rotate(self):
        """Move head to next node (rotate left by 1)."""
        if self.head and self.head.next:
            self.head = self.head.next


if __name__ == "__main__":
    import doctest
    doctest.testmod()
```

### Skip Pointers (for LRU Cache context)

```python
class LRUNode:
    """Node for LRU cache with key-value and skip pointers."""
    
    def __init__(self, key: int, val: int):
        self.key = key
        self.val = val
        self.prev = None
        self.next = None
```

---

## Common Interview Problems

| # | Problem | Difficulty | Approach | Time | LeetCode |
|---|---------|------------|----------|------|----------|
| 1 | Reverse Linked List | Easy | Iterative/Recursive | O(n) | [#206](https://leetcode.com/problems/reverse-linked-list/) |
| 2 | Merge Two Sorted Lists | Easy | Two Pointers | O(n+m) | [#21](https://leetcode.com/problems/merge-two-sorted-lists/) |
| 3 | Linked List Cycle | Easy | Floyd's | O(n) | [#141](https://leetcode.com/problems/linked-list-cycle/) |
| 4 | Middle of Linked List | Easy | Slow/Fast | O(n) | [#876](https://leetcode.com/problems/middle-of-the-linked-list/) |
| 5 | Remove Nth Node From End | Medium | Two Pointers | O(n) | [#19](https://leetcode.com/problems/remove-nth-node-from-end-of-list/) |
| 6 | Reorder List | Medium | Split + Reverse + Merge | O(n) | [#143](https://leetcode.com/problems/reorder-list/) |
| 7 | Add Two Numbers | Medium | Carry Propagation | O(n) | [#2](https://leetcode.com/problems/add-two-numbers/) |
| 8 | Copy List with Random Pointer | Medium | HashMap/Interleave | O(n) | [#138](https://leetcode.com/problems/copy-list-with-random-pointer/) |
| 9 | LRU Cache | Medium | HashMap + DLL | O(1) | [#146](https://leetcode.com/problems/lru-cache/) |
| 10 | Reverse Nodes in k-Group | Hard | Iterative Reversal | O(n) | [#25](https://leetcode.com/problems/reverse-nodes-in-k-group/) |

---

## Fully Worked Sample Problem

### Problem: LRU Cache

**Problem Statement:** Design a data structure that follows the constraints of a Least Recently Used (LRU) cache. Implement `get` and `put` operations in O(1) time.

**Difficulty:** Medium | [LeetCode #146](https://leetcode.com/problems/lru-cache/)

```python
class LRUCache:
    """
    LRU Cache using HashMap + Doubly Linked List.
    
    Key insight: HashMap for O(1) lookup, DLL for O(1) reordering.
    Most recently used at tail, least recently used at head.
    
    Time Complexity: O(1) for both get and put
    Space Complexity: O(capacity)
    
    >>> cache = LRUCache(2)
    >>> cache.put(1, 1)
    >>> cache.put(2, 2)
    >>> cache.get(1)
    1
    >>> cache.put(3, 3)  # Evicts key 2
    >>> cache.get(2)
    -1
    >>> cache.put(4, 4)  # Evicts key 1
    >>> cache.get(1)
    -1
    >>> cache.get(3)
    3
    >>> cache.get(4)
    4
    """
    
    class DListNode:
        def __init__(self, key: int = 0, val: int = 0):
            self.key = key
            self.val = val
            self.prev = None
            self.next = None
    
    def __init__(self, capacity: int):
        self.capacity = capacity
        self.cache = {}  # key -> node
        
        # Sentinel nodes
        self.head = self.DListNode()  # Dummy head (LRU end)
        self.tail = self.DListNode()  # Dummy tail (MRU end)
        self.head.next = self.tail
        self.tail.prev = self.head
    
    def _add_to_tail(self, node: 'DListNode') -> None:
        """Add node right before tail (most recently used)."""
        node.prev = self.tail.prev
        node.next = self.tail
        self.tail.prev.next = node
        self.tail.prev = node
    
    def _remove_node(self, node: 'DListNode') -> None:
        """Remove node from current position."""
        node.prev.next = node.next
        node.next.prev = node.prev
    
    def _move_to_tail(self, node: 'DListNode') -> None:
        """Move existing node to tail (mark as most recently used)."""
        self._remove_node(node)
        self._add_to_tail(node)
    
    def get(self, key: int) -> int:
        """Get value and mark as recently used. O(1)"""
        if key not in self.cache:
            return -1
        
        node = self.cache[key]
        self._move_to_tail(node)  # Mark as recently used
        return node.val
    
    def put(self, key: int, value: int) -> None:
        """Insert or update key-value pair. O(1)"""
        if key in self.cache:
            # Update existing
            node = self.cache[key]
            node.val = value
            self._move_to_tail(node)
        else:
            # Insert new
            if len(self.cache) >= self.capacity:
                # Evict LRU (node after head)
                lru = self.head.next
                self._remove_node(lru)
                del self.cache[lru.key]
            
            # Add new node
            new_node = self.DListNode(key, value)
            self.cache[key] = new_node
            self._add_to_tail(new_node)


# Visualization of LRU Cache state:
"""
After: put(1,1), put(2,2)
head ↔ [1,1] ↔ [2,2] ↔ tail
       LRU      MRU

After: get(1)  (access key 1)
head ↔ [2,2] ↔ [1,1] ↔ tail
       LRU      MRU

After: put(3,3) (capacity exceeded, evict LRU=2)
head ↔ [1,1] ↔ [3,3] ↔ tail
       LRU      MRU
"""


if __name__ == "__main__":
    import doctest
    doctest.testmod()
```

**Pattern(s) Used:** Hash Map + Doubly Linked List, Sentinel Nodes

---

## Edge Cases & Pitfalls

- **Empty List:** Check `head is None` before operations
- **Single Node:** Ensure operations work when `head == tail`
- **Null Pointer:** Always check `node.next` before accessing `node.next.next`
- **Losing Reference:** Save `next` before modifying pointers in reversal
- **Off-by-One:** In nth-from-end, use dummy head for edge case when removing first node
- **Memory Leaks:** In languages without GC, explicitly free deleted nodes
- **Cycle Creation:** Accidentally creating cycles when manipulating pointers

---

## Follow-ups and Optimization Ideas

1. **XOR Linked List:** Store `prev XOR next` to save space (interview trivia)
2. **Unrolled Linked List:** Store arrays of elements per node for better cache
3. **Skip List:** Add express lanes for O(log n) search
4. **Lock-Free Lists:** Concurrent linked lists using CAS operations
5. **Persistent Lists:** Immutable lists with structural sharing

---

## Practice Checklist

| # | Problem | Difficulty | Link | Pattern |
|---|---------|------------|------|---------|
| 1 | Reverse Linked List | Easy | [LC #206](https://leetcode.com/problems/reverse-linked-list/) | Iterative |
| 2 | Merge Two Sorted Lists | Easy | [LC #21](https://leetcode.com/problems/merge-two-sorted-lists/) | Two Pointers |
| 3 | Linked List Cycle | Easy | [LC #141](https://leetcode.com/problems/linked-list-cycle/) | Floyd's |
| 4 | Palindrome Linked List | Easy | [LC #234](https://leetcode.com/problems/palindrome-linked-list/) | Fast/Slow + Reverse |
| 5 | Middle of Linked List | Easy | [LC #876](https://leetcode.com/problems/middle-of-the-linked-list/) | Fast/Slow |
| 6 | Remove Nth From End | Medium | [LC #19](https://leetcode.com/problems/remove-nth-node-from-end-of-list/) | Two Pointers |
| 7 | Add Two Numbers | Medium | [LC #2](https://leetcode.com/problems/add-two-numbers/) | Carry |
| 8 | Linked List Cycle II | Medium | [LC #142](https://leetcode.com/problems/linked-list-cycle-ii/) | Floyd's |
| 9 | Reorder List | Medium | [LC #143](https://leetcode.com/problems/reorder-list/) | Split/Reverse/Merge |
| 10 | Copy List with Random Pointer | Medium | [LC #138](https://leetcode.com/problems/copy-list-with-random-pointer/) | HashMap |
| 11 | Sort List | Medium | [LC #148](https://leetcode.com/problems/sort-list/) | Merge Sort |
| 12 | Intersection of Two Lists | Easy | [LC #160](https://leetcode.com/problems/intersection-of-two-linked-lists/) | Two Pointers |
| 13 | Flatten Multilevel DLL | Medium | [LC #430](https://leetcode.com/problems/flatten-a-multilevel-doubly-linked-list/) | DFS |
| 14 | Rotate List | Medium | [LC #61](https://leetcode.com/problems/rotate-list/) | Length + Modulo |
| 15 | LRU Cache | Medium | [LC #146](https://leetcode.com/problems/lru-cache/) | HashMap + DLL |
| 16 | Swap Nodes in Pairs | Medium | [LC #24](https://leetcode.com/problems/swap-nodes-in-pairs/) | Pointer Manipulation |
| 17 | Reverse Nodes in k-Group | Hard | [LC #25](https://leetcode.com/problems/reverse-nodes-in-k-group/) | k-Reversal |
| 18 | Merge k Sorted Lists | Hard | [LC #23](https://leetcode.com/problems/merge-k-sorted-lists/) | Heap/Divide Conquer |
| 19 | Reverse Between | Medium | [LC #92](https://leetcode.com/problems/reverse-linked-list-ii/) | Partial Reversal |
| 20 | Design Browser History | Medium | [LC #1472](https://leetcode.com/problems/design-browser-history/) | DLL Design |

---

## Further Reading & References

- [CLRS Chapter 10.2 - Linked Lists](https://mitpress.mit.edu/books/introduction-algorithms-third-edition)
- [VisuAlgo - Linked List](https://visualgo.net/en/list)
- [GeeksforGeeks - Linked List](https://www.geeksforgeeks.org/data-structures/linked-list/)
- [LeetCode Linked List Problems](https://leetcode.com/tag/linked-list/)
- [Floyd's Cycle Detection Algorithm](https://en.wikipedia.org/wiki/Cycle_detection#Floyd's_tortoise_and_hare)

---

## Flashcard Summary

**Q: Time complexity of insertion at head vs tail for singly linked list?**
A: Head: O(1). Tail: O(n) without tail pointer, O(1) with tail pointer.

**Q: How does Floyd's cycle detection work?**
A: Slow moves 1 step, fast moves 2 steps. If cycle exists, they will meet. To find cycle start, move one pointer to head, then advance both by 1.

**Q: Why use dummy head node?**
A: Simplifies edge cases when inserting/deleting at head; don't need special case for empty list.

**Q: How to reverse a linked list in O(n) time, O(1) space?**
A: Iteratively: track prev, current, next; reverse pointer direction; advance pointers.

**Q: What data structure combination enables O(1) LRU Cache?**
A: HashMap (O(1) lookup by key) + Doubly Linked List (O(1) move to end, O(1) remove from front).

**Q: How to find middle of linked list in single pass?**
A: Fast/slow pointers: slow moves 1, fast moves 2. When fast reaches end, slow is at middle.
