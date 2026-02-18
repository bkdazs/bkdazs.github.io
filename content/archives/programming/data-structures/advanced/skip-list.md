---
title: "Skip List"
weight: 15
---

# Skip List

---
title: Skip List - Probabilistic Data Structure
topic: Data Structures
difficulty: Medium
tags: [skip-list, probabilistic, ordered-set, interview-concept]
status: complete
---

## 1. Summary / Quick Reference

| Property | Value |
|----------|-------|
| Search | O(log n) average |
| Insert | O(log n) average |
| Delete | O(log n) average |
| Space | O(n) average |
| Worst Case | O(n) - rare |
| Use Case | Ordered set with range queries |

**One-liner**: A probabilistic data structure that uses multiple sorted linked lists layered on top of each other to achieve O(log n) search time without complex balancing.

---

## 2. Conceptual Foundation

### What is a Skip List?
A skip list is a layered linked list where:
- Level 0 contains all elements in sorted order
- Each higher level contains a subset of elements from the level below
- Elements are promoted to higher levels with probability p (typically 0.5)

### Visual Representation
```
Level 3: -∞ ─────────────────────────────── 50 ────────────────── +∞
          │                                   │
Level 2: -∞ ─────── 20 ────────────────────── 50 ─────── 70 ──── +∞
          │          │                         │          │
Level 1: -∞ ─── 10 ─ 20 ─────── 35 ────────── 50 ─ 60 ── 70 ─── +∞
          │     │    │          │              │    │     │
Level 0: -∞ ─ 5 10 ─ 20 ─ 25 ─ 35 ─ 40 ─ 45 ─ 50 ─ 60 ── 70 ── +∞
```

### Why Skip Lists?
| Feature | Skip List | AVL/Red-Black Tree |
|---------|-----------|-------------------|
| Implementation | Simple | Complex |
| Balance | Probabilistic | Deterministic |
| Concurrency | Easy to lock-free | Hard |
| Range Queries | Natural | Need augmentation |
| Cache Locality | Good | Poor |

### Key Properties
1. **Probabilistic balance**: No complex rotations needed
2. **Expected height**: O(log n)
3. **Expected space**: O(n)
4. **Randomization**: Each insertion flips coins

---

## 3. Implementation

### Complete Skip List

```python
import random
from typing import Optional, List, Any, Generator

class SkipListNode:
    """Node in a skip list."""
    
    def __init__(self, key: float, value: Any = None, level: int = 0):
        self.key = key
        self.value = value
        # Array of forward pointers for each level
        self.forward: List[Optional['SkipListNode']] = [None] * (level + 1)
    
    def __repr__(self) -> str:
        return f"Node({self.key})"


class SkipList:
    """
    Skip List - A probabilistic data structure for maintaining sorted data.
    
    Average Time Complexity:
        - Search: O(log n)
        - Insert: O(log n)
        - Delete: O(log n)
    
    Space Complexity: O(n) average
    """
    
    def __init__(self, max_level: int = 16, p: float = 0.5):
        """
        Initialize skip list.
        
        Args:
            max_level: Maximum number of levels (log2(expected size))
            p: Probability of promoting to next level
        """
        self.max_level = max_level
        self.p = p
        self.level = 0  # Current highest level
        
        # Header node with sentinel key
        self.header = SkipListNode(float('-inf'), level=max_level)
        self.size = 0
    
    def _random_level(self) -> int:
        """Generate random level for new node."""
        level = 0
        while random.random() < self.p and level < self.max_level:
            level += 1
        return level
    
    def search(self, key: float) -> Optional[Any]:
        """
        Search for key in skip list.
        
        Time: O(log n) average
        """
        current = self.header
        
        # Start from highest level, go down
        for i in range(self.level, -1, -1):
            while current.forward[i] and current.forward[i].key < key:
                current = current.forward[i]
        
        # Move to level 0
        current = current.forward[0]
        
        if current and current.key == key:
            return current.value
        return None
    
    def insert(self, key: float, value: Any = None) -> None:
        """
        Insert key-value pair into skip list.
        
        Time: O(log n) average
        """
        # Track update positions at each level
        update = [None] * (self.max_level + 1)
        current = self.header
        
        # Find position at each level
        for i in range(self.level, -1, -1):
            while current.forward[i] and current.forward[i].key < key:
                current = current.forward[i]
            update[i] = current
        
        current = current.forward[0]
        
        # Update existing key
        if current and current.key == key:
            current.value = value
            return
        
        # Generate random level for new node
        new_level = self._random_level()
        
        # If new level is higher than current, update header pointers
        if new_level > self.level:
            for i in range(self.level + 1, new_level + 1):
                update[i] = self.header
            self.level = new_level
        
        # Create new node
        new_node = SkipListNode(key, value, new_level)
        
        # Insert node at each level
        for i in range(new_level + 1):
            new_node.forward[i] = update[i].forward[i]
            update[i].forward[i] = new_node
        
        self.size += 1
    
    def delete(self, key: float) -> bool:
        """
        Delete key from skip list.
        
        Time: O(log n) average
        Returns: True if key was found and deleted
        """
        update = [None] * (self.max_level + 1)
        current = self.header
        
        for i in range(self.level, -1, -1):
            while current.forward[i] and current.forward[i].key < key:
                current = current.forward[i]
            update[i] = current
        
        current = current.forward[0]
        
        if current and current.key == key:
            # Remove node from all levels
            for i in range(self.level + 1):
                if update[i].forward[i] != current:
                    break
                update[i].forward[i] = current.forward[i]
            
            # Reduce level if needed
            while self.level > 0 and self.header.forward[self.level] is None:
                self.level -= 1
            
            self.size -= 1
            return True
        
        return False
    
    def __contains__(self, key: float) -> bool:
        """Check if key exists."""
        return self.search(key) is not None
    
    def __len__(self) -> int:
        return self.size
    
    def __iter__(self) -> Generator[tuple, None, None]:
        """Iterate over all key-value pairs in sorted order."""
        current = self.header.forward[0]
        while current:
            yield (current.key, current.value)
            current = current.forward[0]
    
    def display(self) -> None:
        """Print the skip list structure."""
        print(f"Skip List (level={self.level}, size={self.size}):")
        for i in range(self.level, -1, -1):
            print(f"Level {i}: ", end="")
            node = self.header.forward[i]
            while node:
                print(f"{node.key} -> ", end="")
                node = node.forward[i]
            print("None")


# Example usage
if __name__ == "__main__":
    sl = SkipList()
    
    # Insert elements
    for x in [3, 6, 7, 9, 12, 19, 17, 26, 21, 25]:
        sl.insert(x, f"value_{x}")
    
    print("After insertions:")
    sl.display()
    print()
    
    # Search
    print(f"Search 19: {sl.search(19)}")
    print(f"Search 15: {sl.search(15)}")
    print(f"Contains 7: {7 in sl}")
    print()
    
    # Delete
    sl.delete(19)
    print("After deleting 19:")
    sl.display()
    print()
    
    # Iteration
    print("All elements:", list(sl))
```

### Skip List with Range Operations

```python
class SkipListRange(SkipList):
    """
    Extended Skip List with range query support.
    """
    
    def range_query(self, low: float, high: float) -> List[tuple]:
        """
        Return all key-value pairs in range [low, high].
        
        Time: O(log n + k) where k is number of elements in range
        """
        result = []
        current = self.header
        
        # Find starting position
        for i in range(self.level, -1, -1):
            while current.forward[i] and current.forward[i].key < low:
                current = current.forward[i]
        
        current = current.forward[0]
        
        # Collect elements in range
        while current and current.key <= high:
            result.append((current.key, current.value))
            current = current.forward[0]
        
        return result
    
    def find_min(self) -> Optional[tuple]:
        """Return minimum key-value pair."""
        if self.header.forward[0]:
            node = self.header.forward[0]
            return (node.key, node.value)
        return None
    
    def find_max(self) -> Optional[tuple]:
        """Return maximum key-value pair."""
        current = self.header
        
        for i in range(self.level, -1, -1):
            while current.forward[i]:
                current = current.forward[i]
        
        if current != self.header:
            return (current.key, current.value)
        return None
    
    def floor(self, key: float) -> Optional[tuple]:
        """Return largest key <= given key."""
        current = self.header
        
        for i in range(self.level, -1, -1):
            while current.forward[i] and current.forward[i].key <= key:
                current = current.forward[i]
        
        if current != self.header:
            return (current.key, current.value)
        return None
    
    def ceiling(self, key: float) -> Optional[tuple]:
        """Return smallest key >= given key."""
        current = self.header
        
        for i in range(self.level, -1, -1):
            while current.forward[i] and current.forward[i].key < key:
                current = current.forward[i]
        
        current = current.forward[0]
        
        if current:
            return (current.key, current.value)
        return None


# Example
sl = SkipListRange()
for x in [5, 10, 15, 20, 25, 30, 35, 40]:
    sl.insert(x)

print(f"Range [12, 32]: {sl.range_query(12, 32)}")  # [(15,), (20,), (25,), (30,)]
print(f"Floor(22): {sl.floor(22)}")  # (20,)
print(f"Ceiling(22): {sl.ceiling(22)}")  # (25,)
print(f"Min: {sl.find_min()}")  # (5,)
print(f"Max: {sl.find_max()}")  # (40,)
```

---

## 4. Indexable Skip List

Skip list with O(log n) access by index.

```python
class IndexableSkipListNode:
    """Node with span information for indexing."""
    
    def __init__(self, key: float, value: Any = None, level: int = 0):
        self.key = key
        self.value = value
        self.forward: List[Optional['IndexableSkipListNode']] = [None] * (level + 1)
        # span[i] = number of nodes between this node and forward[i] at level i
        self.span: List[int] = [0] * (level + 1)


class IndexableSkipList:
    """
    Skip List with O(log n) index access.
    
    Maintains span information at each level to enable:
        - get_by_index(i): O(log n)
        - get_rank(key): O(log n)
    """
    
    def __init__(self, max_level: int = 16, p: float = 0.5):
        self.max_level = max_level
        self.p = p
        self.level = 0
        self.header = IndexableSkipListNode(float('-inf'), level=max_level)
        self.size = 0
    
    def _random_level(self) -> int:
        level = 0
        while random.random() < self.p and level < self.max_level:
            level += 1
        return level
    
    def insert(self, key: float, value: Any = None) -> None:
        """Insert with span maintenance."""
        update = [None] * (self.max_level + 1)
        rank = [0] * (self.max_level + 1)
        current = self.header
        
        for i in range(self.level, -1, -1):
            rank[i] = rank[i + 1] if i < self.max_level else 0
            while current.forward[i] and current.forward[i].key < key:
                rank[i] += current.span[i]
                current = current.forward[i]
            update[i] = current
        
        current = current.forward[0]
        
        if current and current.key == key:
            current.value = value
            return
        
        new_level = self._random_level()
        
        if new_level > self.level:
            for i in range(self.level + 1, new_level + 1):
                rank[i] = 0
                update[i] = self.header
                update[i].span[i] = self.size
            self.level = new_level
        
        new_node = IndexableSkipListNode(key, value, new_level)
        
        for i in range(new_level + 1):
            new_node.forward[i] = update[i].forward[i]
            update[i].forward[i] = new_node
            
            # Update spans
            new_node.span[i] = update[i].span[i] - (rank[0] - rank[i])
            update[i].span[i] = rank[0] - rank[i] + 1
        
        # Increment spans for levels above new_level
        for i in range(new_level + 1, self.level + 1):
            update[i].span[i] += 1
        
        self.size += 1
    
    def get_by_index(self, index: int) -> Optional[tuple]:
        """
        Get element at given index (0-based).
        
        Time: O(log n)
        """
        if index < 0 or index >= self.size:
            return None
        
        current = self.header
        traversed = -1
        
        for i in range(self.level, -1, -1):
            while current.forward[i] and traversed + current.span[i] <= index:
                traversed += current.span[i]
                current = current.forward[i]
        
        return (current.key, current.value)
    
    def get_rank(self, key: float) -> int:
        """
        Get 0-based rank of key (-1 if not found).
        
        Time: O(log n)
        """
        current = self.header
        rank = -1
        
        for i in range(self.level, -1, -1):
            while current.forward[i] and current.forward[i].key <= key:
                rank += current.span[i]
                current = current.forward[i]
        
        if current.key == key:
            return rank
        return -1


# Example
isl = IndexableSkipList()
for x in [10, 20, 30, 40, 50]:
    isl.insert(x)

print(f"Element at index 2: {isl.get_by_index(2)}")  # (30, None)
print(f"Rank of 40: {isl.get_rank(40)}")  # 3
```

---

## 5. Concurrent Skip List

Lock-free skip list for concurrent access.

```python
import threading
from typing import Optional

class ConcurrentSkipList:
    """
    Thread-safe Skip List using fine-grained locking.
    
    Note: For production, consider lock-free implementations
    using CAS operations.
    """
    
    def __init__(self, max_level: int = 16, p: float = 0.5):
        self.max_level = max_level
        self.p = p
        self.level = 0
        self.header = SkipListNode(float('-inf'), level=max_level)
        self.size = 0
        
        # Lock for level changes
        self.level_lock = threading.Lock()
        # Fine-grained locks for each node (simplified version)
        self.global_lock = threading.RLock()
    
    def search(self, key: float) -> Optional[Any]:
        """Thread-safe search."""
        with self.global_lock:
            current = self.header
            
            for i in range(self.level, -1, -1):
                while current.forward[i] and current.forward[i].key < key:
                    current = current.forward[i]
            
            current = current.forward[0]
            
            if current and current.key == key:
                return current.value
            return None
    
    def insert(self, key: float, value: Any = None) -> None:
        """Thread-safe insert."""
        with self.global_lock:
            update = [None] * (self.max_level + 1)
            current = self.header
            
            for i in range(self.level, -1, -1):
                while current.forward[i] and current.forward[i].key < key:
                    current = current.forward[i]
                update[i] = current
            
            current = current.forward[0]
            
            if current and current.key == key:
                current.value = value
                return
            
            new_level = self._random_level()
            
            if new_level > self.level:
                for i in range(self.level + 1, new_level + 1):
                    update[i] = self.header
                self.level = new_level
            
            new_node = SkipListNode(key, value, new_level)
            
            for i in range(new_level + 1):
                new_node.forward[i] = update[i].forward[i]
                update[i].forward[i] = new_node
            
            self.size += 1
    
    def _random_level(self) -> int:
        level = 0
        while random.random() < self.p and level < self.max_level:
            level += 1
        return level
```

---

## 6. Applications

### 6.1 Ordered Set Implementation

```python
class OrderedSet:
    """Ordered set using skip list."""
    
    def __init__(self):
        self.skiplist = SkipList()
    
    def add(self, item: float) -> None:
        if item not in self.skiplist:
            self.skiplist.insert(item)
    
    def remove(self, item: float) -> None:
        self.skiplist.delete(item)
    
    def __contains__(self, item: float) -> bool:
        return item in self.skiplist
    
    def __iter__(self):
        for key, _ in self.skiplist:
            yield key
    
    def __len__(self) -> int:
        return len(self.skiplist)


# Example
oset = OrderedSet()
for x in [5, 2, 8, 1, 9]:
    oset.add(x)

print(f"Sorted elements: {list(oset)}")  # [1, 2, 5, 8, 9]
```

### 6.2 Leaderboard / Ranking System

```python
class Leaderboard:
    """
    Leaderboard using indexable skip list.
    
    Operations:
        - add_score(player_id, score)
        - get_rank(player_id)
        - get_top_k(k)
    """
    
    def __init__(self):
        self.scores = IndexableSkipList()
        self.player_scores = {}  # player_id -> score
    
    def add_score(self, player_id: int, score: int) -> None:
        """Add or update player score."""
        if player_id in self.player_scores:
            # Remove old score (negative for descending order)
            old_score = self.player_scores[player_id]
            # Would need delete with compound key...
        
        # Insert with negative score for descending order
        self.scores.insert(-score, player_id)
        self.player_scores[player_id] = score
    
    def get_top_k(self, k: int) -> List[tuple]:
        """Get top k players."""
        result = []
        for i in range(min(k, self.scores.size)):
            item = self.scores.get_by_index(i)
            if item:
                result.append((item[1], -item[0]))  # (player_id, score)
        return result
```

---

## 7. Complexity Analysis

### Expected Height
- Probability that a node is at level k: $p^k$
- Expected number of nodes at level k: $n \cdot p^k$
- Expected height: $\log_{1/p}(n) = O(\log n)$

### Expected Space
- Total expected nodes across all levels: $n \cdot \sum_{k=0}^{\infty} p^k = n / (1-p)$
- With p = 0.5: 2n pointers = O(n)

### Search Complexity
- At each level, expected comparisons: 1/p
- Total levels: O(log n)
- Expected comparisons: O(log n / p) = O(log n)

---

## 8. Comparison with Other Data Structures

| Operation | Skip List | AVL Tree | Red-Black | Hash Table |
|-----------|-----------|----------|-----------|------------|
| Search | O(log n)* | O(log n) | O(log n) | O(1)* |
| Insert | O(log n)* | O(log n) | O(log n) | O(1)* |
| Delete | O(log n)* | O(log n) | O(log n) | O(1)* |
| Range Query | O(log n + k) | O(log n + k) | O(log n + k) | O(n) |
| Find Min/Max | O(1) | O(log n) | O(log n) | O(n) |
| Ordered Iter | O(n) | O(n) | O(n) | O(n log n) |
| Implementation | Simple | Complex | Complex | Simple |

*Average case, worst case may differ

---

## 9. Interview Questions

### Q1: Why use Skip List over balanced BST?
**Answer**:
1. Simpler implementation (no rotations)
2. Better for concurrent access (easier lock-free)
3. Good cache locality (linked structure can be optimized)
4. Natural support for range queries

### Q2: What's the probability distribution for level assignment?
**Answer**: Geometric distribution with parameter p.
- P(level = k) = p^k (1-p)
- Expected level = p / (1-p)

### Q3: Real-world uses of Skip Lists?
**Answer**:
1. Redis sorted sets (ZSET)
2. LevelDB / RocksDB MemTable
3. Lucene
4. Apache HBase

---

## 10. Practice Problems

| Problem | Concept |
|---------|---------|
| Design Twitter Timeline | Skip list for ordered tweets |
| Implement LRU Cache with ordering | Skip list + hash map |
| Range Sum with updates | Indexable skip list |
| Design Search Autocomplete | Skip list with prefix matching |

---

## 11. References

1. William Pugh, "Skip Lists: A Probabilistic Alternative to Balanced Trees" (1990)
2. Redis ZSET implementation: https://github.com/redis/redis
3. "The Art of Multiprocessor Programming" - Lock-free Skip Lists
4. CP-Algorithms: https://cp-algorithms.com/data_structures/randomized_heap.html
