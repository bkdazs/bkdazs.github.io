---
title: "Cache Data Structures (LRU, LFU)"
weight: 18
---

# Cache Data Structures (LRU, LFU)

---
title: Cache Data Structures - LRU and LFU Caches
topic: Data Structures
difficulty: Medium
tags: [cache, lru, lfu, design, interview-critical]
status: complete
---

## 1. Summary / Quick Reference

### LRU Cache (Least Recently Used)
| Operation | Time | Implementation |
|-----------|------|----------------|
| get() | O(1) | Hash Map + Doubly Linked List |
| put() | O(1) | Hash Map + Doubly Linked List |
| Space | O(capacity) | |

### LFU Cache (Least Frequently Used)
| Operation | Time | Implementation |
|-----------|------|----------------|
| get() | O(1) | 2 Hash Maps + DLL per frequency |
| put() | O(1) | 2 Hash Maps + DLL per frequency |
| Space | O(capacity) | |

---

## 2. LRU Cache

### Concept
- Evict the **least recently used** item when cache is full
- Every access (get/put) makes item "most recently used"

### Implementation Strategy
```
HashMap: key -> Node (for O(1) access)
Doubly Linked List: for O(1) insertion/deletion by recency

Most Recent (head) <-> ... <-> ... <-> Least Recent (tail)
```

### Complete Implementation

```python
from typing import Optional, Dict

class ListNode:
    """Doubly linked list node for LRU Cache."""
    
    def __init__(self, key: int = 0, value: int = 0):
        self.key = key
        self.value = value
        self.prev: Optional['ListNode'] = None
        self.next: Optional['ListNode'] = None


class LRUCache:
    """
    LRU (Least Recently Used) Cache implementation.
    
    Uses HashMap + Doubly Linked List for O(1) operations.
    
    LeetCode 146 - LRU Cache
    """
    
    def __init__(self, capacity: int):
        """
        Initialize LRU cache with given capacity.
        
        Time: O(1)
        """
        self.capacity = capacity
        self.cache: Dict[int, ListNode] = {}
        
        # Dummy head and tail for easier operations
        self.head = ListNode()
        self.tail = ListNode()
        self.head.next = self.tail
        self.tail.prev = self.head
    
    def _add_to_front(self, node: ListNode) -> None:
        """Add node right after head (most recently used)."""
        node.prev = self.head
        node.next = self.head.next
        self.head.next.prev = node
        self.head.next = node
    
    def _remove_node(self, node: ListNode) -> None:
        """Remove node from the list."""
        node.prev.next = node.next
        node.next.prev = node.prev
    
    def _move_to_front(self, node: ListNode) -> None:
        """Move existing node to front (mark as recently used)."""
        self._remove_node(node)
        self._add_to_front(node)
    
    def _remove_tail(self) -> ListNode:
        """Remove and return the least recently used node."""
        node = self.tail.prev
        self._remove_node(node)
        return node
    
    def get(self, key: int) -> int:
        """
        Get value by key.
        
        Returns -1 if key doesn't exist.
        Moves accessed item to front (most recently used).
        
        Time: O(1)
        """
        if key not in self.cache:
            return -1
        
        node = self.cache[key]
        self._move_to_front(node)
        return node.value
    
    def put(self, key: int, value: int) -> None:
        """
        Add or update key-value pair.
        
        If cache is full, evict least recently used item.
        
        Time: O(1)
        """
        if key in self.cache:
            # Update existing
            node = self.cache[key]
            node.value = value
            self._move_to_front(node)
        else:
            # Add new
            if len(self.cache) >= self.capacity:
                # Evict LRU
                lru = self._remove_tail()
                del self.cache[lru.key]
            
            new_node = ListNode(key, value)
            self.cache[key] = new_node
            self._add_to_front(new_node)
    
    def __repr__(self) -> str:
        """String representation showing cache order."""
        result = []
        node = self.head.next
        while node != self.tail:
            result.append(f"{node.key}:{node.value}")
            node = node.next
        return f"LRU[{' -> '.join(result)}]"


# Example usage
if __name__ == "__main__":
    cache = LRUCache(3)
    
    cache.put(1, 1)
    cache.put(2, 2)
    cache.put(3, 3)
    print(cache)  # LRU[3:3 -> 2:2 -> 1:1]
    
    cache.get(1)  # Access key 1, moves to front
    print(cache)  # LRU[1:1 -> 3:3 -> 2:2]
    
    cache.put(4, 4)  # Evicts key 2 (least recently used)
    print(cache)  # LRU[4:4 -> 1:1 -> 3:3]
    
    print(f"get(2): {cache.get(2)}")  # -1 (evicted)
    print(f"get(3): {cache.get(3)}")  # 3
```

### LRU Cache with OrderedDict

```python
from collections import OrderedDict

class LRUCacheSimple:
    """
    LRU Cache using Python's OrderedDict.
    
    OrderedDict maintains insertion order and supports move_to_end().
    """
    
    def __init__(self, capacity: int):
        self.capacity = capacity
        self.cache = OrderedDict()
    
    def get(self, key: int) -> int:
        if key not in self.cache:
            return -1
        
        # Move to end (most recently used)
        self.cache.move_to_end(key)
        return self.cache[key]
    
    def put(self, key: int, value: int) -> None:
        if key in self.cache:
            self.cache.move_to_end(key)
            self.cache[key] = value
        else:
            if len(self.cache) >= self.capacity:
                # Remove first (least recently used)
                self.cache.popitem(last=False)
            self.cache[key] = value
```

---

## 3. LFU Cache

### Concept
- Evict the **least frequently used** item when cache is full
- Tie-breaker: among same frequency, evict least recently used

### Implementation Strategy
```
HashMap 1: key -> (value, frequency, node)
HashMap 2: frequency -> DoublyLinkedList of keys
Min Frequency: Track minimum frequency for O(1) eviction

freq_map:
  1 -> [key3 <-> key5]  (used once)
  2 -> [key1 <-> key2]  (used twice)
  5 -> [key4]           (used 5 times)
min_freq = 1
```

### Complete Implementation

```python
from typing import Dict, Optional
from collections import defaultdict

class LFUNode:
    """Node for LFU Cache doubly linked list."""
    
    def __init__(self, key: int = 0, value: int = 0):
        self.key = key
        self.value = value
        self.freq = 1
        self.prev: Optional['LFUNode'] = None
        self.next: Optional['LFUNode'] = None


class DoublyLinkedList:
    """Doubly linked list for managing nodes at same frequency."""
    
    def __init__(self):
        self.head = LFUNode()
        self.tail = LFUNode()
        self.head.next = self.tail
        self.tail.prev = self.head
        self.size = 0
    
    def add_to_front(self, node: LFUNode) -> None:
        """Add node to front (most recently used at this frequency)."""
        node.prev = self.head
        node.next = self.head.next
        self.head.next.prev = node
        self.head.next = node
        self.size += 1
    
    def remove_node(self, node: LFUNode) -> None:
        """Remove node from list."""
        node.prev.next = node.next
        node.next.prev = node.prev
        self.size -= 1
    
    def remove_tail(self) -> LFUNode:
        """Remove and return least recently used node at this frequency."""
        if self.size == 0:
            return None
        node = self.tail.prev
        self.remove_node(node)
        return node
    
    def is_empty(self) -> bool:
        return self.size == 0


class LFUCache:
    """
    LFU (Least Frequently Used) Cache implementation.
    
    Uses HashMap + frequency HashMap with DoublyLinkedLists.
    
    LeetCode 460 - LFU Cache
    """
    
    def __init__(self, capacity: int):
        """
        Initialize LFU cache with given capacity.
        
        Time: O(1)
        """
        self.capacity = capacity
        self.size = 0
        self.min_freq = 0
        
        # key -> LFUNode
        self.key_to_node: Dict[int, LFUNode] = {}
        
        # frequency -> DoublyLinkedList of nodes
        self.freq_to_list: Dict[int, DoublyLinkedList] = defaultdict(DoublyLinkedList)
    
    def _update_freq(self, node: LFUNode) -> None:
        """Update frequency of node (called on access)."""
        freq = node.freq
        
        # Remove from current frequency list
        self.freq_to_list[freq].remove_node(node)
        
        # Update min_freq if necessary
        if freq == self.min_freq and self.freq_to_list[freq].is_empty():
            self.min_freq += 1
        
        # Increment frequency
        node.freq += 1
        
        # Add to new frequency list
        self.freq_to_list[node.freq].add_to_front(node)
    
    def get(self, key: int) -> int:
        """
        Get value by key.
        
        Returns -1 if key doesn't exist.
        Increments frequency of accessed item.
        
        Time: O(1)
        """
        if self.capacity == 0 or key not in self.key_to_node:
            return -1
        
        node = self.key_to_node[key]
        self._update_freq(node)
        return node.value
    
    def put(self, key: int, value: int) -> None:
        """
        Add or update key-value pair.
        
        If cache is full, evict least frequently used item.
        Tie-breaker: least recently used among same frequency.
        
        Time: O(1)
        """
        if self.capacity == 0:
            return
        
        if key in self.key_to_node:
            # Update existing
            node = self.key_to_node[key]
            node.value = value
            self._update_freq(node)
        else:
            # Add new
            if self.size >= self.capacity:
                # Evict LFU (ties broken by LRU within frequency)
                lfu_list = self.freq_to_list[self.min_freq]
                evicted = lfu_list.remove_tail()
                del self.key_to_node[evicted.key]
                self.size -= 1
            
            # Create new node
            new_node = LFUNode(key, value)
            self.key_to_node[key] = new_node
            self.freq_to_list[1].add_to_front(new_node)
            self.min_freq = 1
            self.size += 1


# Example usage
if __name__ == "__main__":
    cache = LFUCache(3)
    
    cache.put(1, 10)  # freq[1] = 1
    cache.put(2, 20)  # freq[2] = 1
    cache.put(3, 30)  # freq[3] = 1
    
    cache.get(1)      # freq[1] = 2
    cache.get(2)      # freq[2] = 2
    
    cache.put(4, 40)  # Evicts key 3 (freq 1, LFU)
    
    print(f"get(3): {cache.get(3)}")  # -1 (evicted)
    print(f"get(4): {cache.get(4)}")  # 40
    
    cache.get(1)      # freq[1] = 3
    cache.put(5, 50)  # Evicts key 4 (freq 1) or key 2 (freq 2)?
                      # key 4 has freq 1, evicted
    
    print(f"get(4): {cache.get(4)}")  # -1 (evicted)
    print(f"get(2): {cache.get(2)}")  # 20
```

---

## 4. TTL Cache (Time-To-Live)

```python
import time
from typing import Dict, Optional, Tuple

class TTLCache:
    """
    Cache with Time-To-Live expiration.
    
    Items expire after a specified duration.
    """
    
    def __init__(self, capacity: int, ttl_seconds: float):
        """
        Args:
            capacity: Maximum number of items
            ttl_seconds: Time to live in seconds
        """
        self.capacity = capacity
        self.ttl = ttl_seconds
        self.cache: Dict[int, Tuple[int, float]] = {}  # key -> (value, expiry_time)
    
    def _is_expired(self, key: int) -> bool:
        """Check if key has expired."""
        if key not in self.cache:
            return True
        _, expiry = self.cache[key]
        return time.time() > expiry
    
    def _cleanup_expired(self) -> None:
        """Remove all expired entries."""
        current_time = time.time()
        expired_keys = [
            k for k, (_, expiry) in self.cache.items()
            if current_time > expiry
        ]
        for k in expired_keys:
            del self.cache[k]
    
    def get(self, key: int) -> Optional[int]:
        """Get value if exists and not expired."""
        if self._is_expired(key):
            if key in self.cache:
                del self.cache[key]
            return None
        
        return self.cache[key][0]
    
    def put(self, key: int, value: int) -> None:
        """Add or update key-value pair with fresh TTL."""
        if len(self.cache) >= self.capacity and key not in self.cache:
            self._cleanup_expired()
            
            # Still full? Remove oldest
            if len(self.cache) >= self.capacity:
                oldest = min(self.cache.items(), key=lambda x: x[1][1])
                del self.cache[oldest[0]]
        
        expiry_time = time.time() + self.ttl
        self.cache[key] = (value, expiry_time)


# Example
cache = TTLCache(capacity=3, ttl_seconds=5.0)
cache.put(1, 100)
print(f"get(1): {cache.get(1)}")  # 100

# After 5 seconds...
# print(f"get(1): {cache.get(1)}")  # None (expired)
```

---

## 5. Write-Through vs Write-Back Cache

```python
from abc import ABC, abstractmethod
from typing import Dict, Optional

class Database:
    """Simulated database."""
    
    def __init__(self):
        self.data: Dict[int, int] = {}
    
    def read(self, key: int) -> Optional[int]:
        return self.data.get(key)
    
    def write(self, key: int, value: int) -> None:
        self.data[key] = value


class CacheStrategy(ABC):
    """Abstract cache strategy."""
    
    @abstractmethod
    def get(self, key: int) -> Optional[int]:
        pass
    
    @abstractmethod
    def put(self, key: int, value: int) -> None:
        pass


class WriteThroughCache(CacheStrategy):
    """
    Write-Through Cache: Write to cache AND database on every write.
    
    Pros: Data consistency, simple
    Cons: Higher write latency
    """
    
    def __init__(self, capacity: int, db: Database):
        self.capacity = capacity
        self.db = db
        self.cache: Dict[int, int] = {}
    
    def get(self, key: int) -> Optional[int]:
        # Check cache first
        if key in self.cache:
            return self.cache[key]
        
        # Cache miss - read from DB
        value = self.db.read(key)
        if value is not None and len(self.cache) < self.capacity:
            self.cache[key] = value
        return value
    
    def put(self, key: int, value: int) -> None:
        # Write to both cache and database
        self.cache[key] = value
        self.db.write(key, value)  # Synchronous write


class WriteBackCache(CacheStrategy):
    """
    Write-Back Cache: Write to cache only, persist to DB later.
    
    Pros: Lower write latency
    Cons: Risk of data loss, complexity
    """
    
    def __init__(self, capacity: int, db: Database):
        self.capacity = capacity
        self.db = db
        self.cache: Dict[int, int] = {}
        self.dirty: set = set()  # Keys modified but not yet persisted
    
    def get(self, key: int) -> Optional[int]:
        if key in self.cache:
            return self.cache[key]
        
        value = self.db.read(key)
        if value is not None and len(self.cache) < self.capacity:
            self.cache[key] = value
        return value
    
    def put(self, key: int, value: int) -> None:
        # Write to cache only
        self.cache[key] = value
        self.dirty.add(key)  # Mark as dirty
        # DB write happens asynchronously or on eviction
    
    def flush(self) -> None:
        """Write all dirty entries to database."""
        for key in self.dirty:
            if key in self.cache:
                self.db.write(key, self.cache[key])
        self.dirty.clear()
    
    def evict(self, key: int) -> None:
        """Evict entry, writing to DB if dirty."""
        if key in self.dirty:
            self.db.write(key, self.cache[key])
            self.dirty.remove(key)
        if key in self.cache:
            del self.cache[key]
```

---

## 6. Multi-Level Cache (L1/L2)

```python
from typing import Optional, List

class MultiLevelCache:
    """
    Multi-Level Cache (L1/L2/L3).
    
    L1: Smallest, fastest
    L2: Medium
    L3: Largest, slowest (before hitting DB)
    
    Read: Check L1 -> L2 -> L3 -> DB
    Write: Write to all levels (write-through)
    """
    
    def __init__(self, l1_capacity: int, l2_capacity: int, l3_capacity: int):
        self.l1 = LRUCache(l1_capacity)
        self.l2 = LRUCache(l2_capacity)
        self.l3 = LRUCache(l3_capacity)
        self.levels = [self.l1, self.l2, self.l3]
    
    def get(self, key: int) -> int:
        """
        Get value, checking each level.
        On hit, promote to higher levels.
        """
        for i, cache in enumerate(self.levels):
            value = cache.get(key)
            if value != -1:
                # Promote to higher levels
                for j in range(i):
                    self.levels[j].put(key, value)
                return value
        
        return -1  # Not found
    
    def put(self, key: int, value: int) -> None:
        """Write to all cache levels."""
        for cache in self.levels:
            cache.put(key, value)


# Example
ml_cache = MultiLevelCache(l1_capacity=2, l2_capacity=4, l3_capacity=8)

ml_cache.put(1, 100)
ml_cache.put(2, 200)
ml_cache.put(3, 300)  # Evicts from L1

print(f"get(3): {ml_cache.get(3)}")  # From L2, promoted to L1
```

---

## 7. Thread-Safe LRU Cache

```python
import threading
from typing import Dict, Optional

class ThreadSafeLRUCache:
    """
    Thread-safe LRU Cache using locks.
    """
    
    def __init__(self, capacity: int):
        self.capacity = capacity
        self.cache = OrderedDict()
        self.lock = threading.RLock()
    
    def get(self, key: int) -> int:
        with self.lock:
            if key not in self.cache:
                return -1
            self.cache.move_to_end(key)
            return self.cache[key]
    
    def put(self, key: int, value: int) -> None:
        with self.lock:
            if key in self.cache:
                self.cache.move_to_end(key)
                self.cache[key] = value
            else:
                if len(self.cache) >= self.capacity:
                    self.cache.popitem(last=False)
                self.cache[key] = value


class ReadWriteLockLRUCache:
    """
    LRU Cache with read-write locks for better concurrency.
    
    Multiple readers OR one writer at a time.
    """
    
    def __init__(self, capacity: int):
        self.capacity = capacity
        self.cache = OrderedDict()
        self.read_lock = threading.Lock()
        self.write_lock = threading.Lock()
        self.readers = 0
    
    def _acquire_read(self):
        self.read_lock.acquire()
        self.readers += 1
        if self.readers == 1:
            self.write_lock.acquire()
        self.read_lock.release()
    
    def _release_read(self):
        self.read_lock.acquire()
        self.readers -= 1
        if self.readers == 0:
            self.write_lock.release()
        self.read_lock.release()
    
    def get(self, key: int) -> int:
        # Note: move_to_end is a write, so we need write lock
        self.write_lock.acquire()
        try:
            if key not in self.cache:
                return -1
            self.cache.move_to_end(key)
            return self.cache[key]
        finally:
            self.write_lock.release()
    
    def put(self, key: int, value: int) -> None:
        self.write_lock.acquire()
        try:
            if key in self.cache:
                self.cache.move_to_end(key)
                self.cache[key] = value
            else:
                if len(self.cache) >= self.capacity:
                    self.cache.popitem(last=False)
                self.cache[key] = value
        finally:
            self.write_lock.release()
```

---

## 8. Cache Metrics & Monitoring

```python
from dataclasses import dataclass

@dataclass
class CacheMetrics:
    hits: int = 0
    misses: int = 0
    evictions: int = 0
    
    @property
    def hit_rate(self) -> float:
        total = self.hits + self.misses
        return self.hits / total if total > 0 else 0.0


class InstrumentedLRUCache(LRUCacheSimple):
    """LRU Cache with metrics tracking."""
    
    def __init__(self, capacity: int):
        super().__init__(capacity)
        self.metrics = CacheMetrics()
    
    def get(self, key: int) -> int:
        result = super().get(key)
        if result == -1:
            self.metrics.misses += 1
        else:
            self.metrics.hits += 1
        return result
    
    def put(self, key: int, value: int) -> None:
        was_full = len(self.cache) >= self.capacity
        had_key = key in self.cache
        
        super().put(key, value)
        
        if was_full and not had_key:
            self.metrics.evictions += 1
    
    def get_metrics(self) -> CacheMetrics:
        return self.metrics


# Example
cache = InstrumentedLRUCache(2)
cache.put(1, 1)
cache.put(2, 2)
cache.get(1)  # hit
cache.get(3)  # miss
cache.put(3, 3)  # eviction

metrics = cache.get_metrics()
print(f"Hit rate: {metrics.hit_rate:.2%}")  # 50%
print(f"Evictions: {metrics.evictions}")     # 1
```

---

## 9. LeetCode Problems

| # | Problem | Difficulty | Concept |
|---|---------|------------|---------|
| 146 | LRU Cache | Medium | HashMap + DLL |
| 460 | LFU Cache | Hard | Multiple HashMaps + DLL |
| 1472 | Design Browser History | Medium | Stack/List operations |
| 355 | Design Twitter | Medium | Time-based cache |
| 588 | Design In-Memory File System | Hard | Trie + Cache |
| 362 | Design Hit Counter | Medium | Circular buffer |

---

## 10. Interview Questions

### Q1: When to use LRU vs LFU?

| Scenario | Best Choice |
|----------|-------------|
| Temporal locality (recent = likely again) | LRU |
| Frequency matters more | LFU |
| Scanning workload (one-time access) | LRU |
| Skewed access patterns | LFU |
| Simple to implement | LRU |

### Q2: How would you implement distributed cache?

**Answer:**
1. **Consistent hashing** for partition
2. **Replication** for availability
3. **Cache invalidation protocol** (TTL, event-driven)
4. **CAP trade-offs** (typically AP for cache)

### Q3: Cache invalidation strategies?

**Answer:**
1. **TTL (Time-To-Live)**: Automatic expiration
2. **Write-through**: Update cache on write
3. **Write-behind**: Async update
4. **Event-driven**: Pub/sub invalidation

### Q4: What is cache stampede and how to prevent?

**Answer:** Multiple requests hit DB when cache expires.

**Solutions:**
1. **Mutex/lock**: Only one rebuilds cache
2. **Background refresh**: Preemptive refresh before expiry
3. **Stale-while-revalidate**: Serve stale, refresh async

---

## 11. References

1. LeetCode 146, 460 official solutions
2. "Designing Data-Intensive Applications" - Martin Kleppmann
3. Redis documentation on eviction policies
4. Facebook TAO cache paper
