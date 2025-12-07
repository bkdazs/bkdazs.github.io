---
title: "Hash Tables, Maps, and Sets"
topic: "Data Structures"
difficulty: "Fundamental"
tags: ["hash-table", "hash-map", "hash-set", "hashing", "collision-resolution"]
status: "complete"
weight: 8
---

# Hash Tables, Maps, and Sets

## Short Summary

A **hash table** is a data structure that maps keys to values using a hash function, providing average O(1) time for insert, delete, and lookup operations. Hash tables are the backbone of dictionaries (`dict`), sets (`set`), and are essential for solving problems requiring fast lookups—from counting frequencies to detecting duplicates to implementing caches.

---

## Why It Matters in Interviews

- **O(1) Lookup:** Transform O(n²) brute force into O(n) solutions
- **Frequency Problems:** Counting, anagrams, top-k, duplicates
- **Two-Pass to One-Pass:** Store seen elements for instant lookup
- **Space-Time Tradeoff:** Trade memory for speed

---

## Formal Definition / Properties / Invariants

### Core Concepts

| Concept | Description |
|---------|-------------|
| **Hash Function** | Maps keys to bucket indices: `h(key) → index` |
| **Load Factor** | α = n/m (elements/buckets), affects performance |
| **Collision** | Two keys hash to same index |
| **Bucket** | Storage location at an index |

### Hash Function Properties
1. **Deterministic:** Same key always produces same hash
2. **Uniform Distribution:** Keys spread evenly across buckets
3. **Efficient:** O(1) to compute

### Common Hash Functions
```python
# Integer hash (simple modulo)
h(k) = k mod m

# String hash (polynomial rolling hash)
h(s) = Σ s[i] * p^i mod m

# Python's built-in (uses SipHash for security)
hash(obj)
```

---

## Time & Space Complexity

| Operation | Average | Worst Case | Notes |
|-----------|---------|------------|-------|
| Insert | O(1) | O(n) | Worst: all collisions |
| Delete | O(1) | O(n) | Need to find key first |
| Search | O(1) | O(n) | Worst: linear probing all |
| Iterate | O(n) | O(n) | Through all entries |

**Space Complexity:** O(n) for n key-value pairs

### Load Factor Impact
- α < 0.7: Good performance
- α > 0.7: Consider resizing
- Python dict: Resizes at 2/3 full

---

## Typical Operations & Pseudo-Implementation

### Hash Table with Chaining

```python
from typing import TypeVar, Generic, Optional, List, Iterator, Tuple

K = TypeVar('K')
V = TypeVar('V')

class HashNode(Generic[K, V]):
    """Node for separate chaining."""
    def __init__(self, key: K, value: V):
        self.key = key
        self.value = value
        self.next: Optional[HashNode[K, V]] = None


class HashTableChaining(Generic[K, V]):
    """
    Hash table implementation using separate chaining.
    
    >>> ht = HashTableChaining[str, int]()
    >>> ht.put("apple", 1)
    >>> ht.put("banana", 2)
    >>> ht.put("cherry", 3)
    >>> ht.get("banana")
    2
    >>> ht.contains("apple")
    True
    >>> ht.remove("apple")
    True
    >>> ht.get("apple")
    >>> len(ht)
    2
    """
    
    INITIAL_CAPACITY = 16
    LOAD_FACTOR_THRESHOLD = 0.75
    
    def __init__(self, capacity: int = INITIAL_CAPACITY):
        self._capacity = capacity
        self._size = 0
        self._buckets: List[Optional[HashNode[K, V]]] = [None] * capacity
    
    def __len__(self) -> int:
        return self._size
    
    def _hash(self, key: K) -> int:
        """Compute bucket index for key."""
        return hash(key) % self._capacity
    
    def _resize(self) -> None:
        """Double capacity and rehash all entries."""
        old_buckets = self._buckets
        self._capacity *= 2
        self._buckets = [None] * self._capacity
        self._size = 0
        
        for head in old_buckets:
            current = head
            while current:
                self.put(current.key, current.value)
                current = current.next
    
    def put(self, key: K, value: V) -> None:
        """Insert or update key-value pair. O(1) average."""
        if self._size / self._capacity >= self.LOAD_FACTOR_THRESHOLD:
            self._resize()
        
        index = self._hash(key)
        current = self._buckets[index]
        
        # Check if key exists
        while current:
            if current.key == key:
                current.value = value  # Update
                return
            current = current.next
        
        # Insert at head of chain
        new_node = HashNode(key, value)
        new_node.next = self._buckets[index]
        self._buckets[index] = new_node
        self._size += 1
    
    def get(self, key: K) -> Optional[V]:
        """Get value for key. O(1) average."""
        index = self._hash(key)
        current = self._buckets[index]
        
        while current:
            if current.key == key:
                return current.value
            current = current.next
        
        return None
    
    def contains(self, key: K) -> bool:
        """Check if key exists. O(1) average."""
        return self.get(key) is not None
    
    def remove(self, key: K) -> bool:
        """Remove key-value pair. O(1) average. Returns True if found."""
        index = self._hash(key)
        current = self._buckets[index]
        prev = None
        
        while current:
            if current.key == key:
                if prev:
                    prev.next = current.next
                else:
                    self._buckets[index] = current.next
                self._size -= 1
                return True
            prev = current
            current = current.next
        
        return False
    
    def keys(self) -> Iterator[K]:
        """Iterate over all keys."""
        for head in self._buckets:
            current = head
            while current:
                yield current.key
                current = current.next
    
    def items(self) -> Iterator[Tuple[K, V]]:
        """Iterate over all key-value pairs."""
        for head in self._buckets:
            current = head
            while current:
                yield (current.key, current.value)
                current = current.next


if __name__ == "__main__":
    import doctest
    doctest.testmod()
```

### Hash Table with Open Addressing (Linear Probing)

```python
from typing import TypeVar, Generic, Optional, List, Tuple
from enum import Enum

K = TypeVar('K')
V = TypeVar('V')

class SlotState(Enum):
    EMPTY = 1
    OCCUPIED = 2
    DELETED = 3


class HashTableOpenAddressing(Generic[K, V]):
    """
    Hash table using open addressing with linear probing.
    
    >>> ht = HashTableOpenAddressing[str, int]()
    >>> ht.put("a", 1)
    >>> ht.put("b", 2)
    >>> ht.get("a")
    1
    >>> ht.remove("a")
    True
    >>> ht.get("a")
    """
    
    INITIAL_CAPACITY = 16
    LOAD_FACTOR_THRESHOLD = 0.5  # Lower for open addressing
    
    def __init__(self, capacity: int = INITIAL_CAPACITY):
        self._capacity = capacity
        self._size = 0
        self._keys: List[Optional[K]] = [None] * capacity
        self._values: List[Optional[V]] = [None] * capacity
        self._states: List[SlotState] = [SlotState.EMPTY] * capacity
    
    def __len__(self) -> int:
        return self._size
    
    def _hash(self, key: K) -> int:
        return hash(key) % self._capacity
    
    def _probe(self, key: K) -> int:
        """Find slot for key (linear probing)."""
        index = self._hash(key)
        first_deleted = -1
        
        while self._states[index] != SlotState.EMPTY:
            if self._states[index] == SlotState.DELETED:
                if first_deleted == -1:
                    first_deleted = index
            elif self._keys[index] == key:
                return index
            index = (index + 1) % self._capacity
        
        return first_deleted if first_deleted != -1 else index
    
    def put(self, key: K, value: V) -> None:
        """Insert or update. O(1) average."""
        if self._size / self._capacity >= self.LOAD_FACTOR_THRESHOLD:
            self._resize()
        
        index = self._probe(key)
        
        if self._states[index] != SlotState.OCCUPIED:
            self._size += 1
        
        self._keys[index] = key
        self._values[index] = value
        self._states[index] = SlotState.OCCUPIED
    
    def get(self, key: K) -> Optional[V]:
        """Get value. O(1) average."""
        index = self._hash(key)
        
        while self._states[index] != SlotState.EMPTY:
            if self._states[index] == SlotState.OCCUPIED and self._keys[index] == key:
                return self._values[index]
            index = (index + 1) % self._capacity
        
        return None
    
    def remove(self, key: K) -> bool:
        """Remove key. O(1) average."""
        index = self._hash(key)
        
        while self._states[index] != SlotState.EMPTY:
            if self._states[index] == SlotState.OCCUPIED and self._keys[index] == key:
                self._states[index] = SlotState.DELETED
                self._size -= 1
                return True
            index = (index + 1) % self._capacity
        
        return False
    
    def _resize(self) -> None:
        """Double capacity and rehash."""
        old_keys, old_values, old_states = self._keys, self._values, self._states
        self._capacity *= 2
        self._keys = [None] * self._capacity
        self._values = [None] * self._capacity
        self._states = [SlotState.EMPTY] * self._capacity
        self._size = 0
        
        for i, state in enumerate(old_states):
            if state == SlotState.OCCUPIED:
                self.put(old_keys[i], old_values[i])


if __name__ == "__main__":
    import doctest
    doctest.testmod()
```

### Python dict and set Operations

```python
from typing import Dict, Set, List, DefaultDict
from collections import defaultdict, Counter

def dict_operations_demo():
    """
    Python dictionary operations with complexity.
    
    >>> d = {}
    >>> d['a'] = 1           # Insert O(1)
    >>> d['b'] = 2
    >>> d['a']               # Lookup O(1)
    1
    >>> 'c' in d             # Contains O(1)
    False
    >>> d.get('c', 0)        # Get with default O(1)
    0
    >>> del d['a']           # Delete O(1)
    >>> list(d.keys())       # Keys O(n)
    ['b']
    """
    pass


def set_operations_demo():
    """
    Python set operations with complexity.
    
    >>> s = {1, 2, 3}
    >>> s.add(4)             # Add O(1)
    >>> 3 in s               # Contains O(1)
    True
    >>> s.remove(2)          # Remove O(1)
    >>> s
    {1, 3, 4}
    >>> t = {3, 4, 5}
    >>> s & t                # Intersection O(min(len(s), len(t)))
    {3, 4}
    >>> s | t                # Union O(len(s) + len(t))
    {1, 3, 4, 5}
    >>> s - t                # Difference O(len(s))
    {1}
    >>> s ^ t                # Symmetric difference O(len(s) + len(t))
    {1, 5}
    """
    pass


def defaultdict_demo():
    """
    DefaultDict for automatic default values.
    
    >>> # Group words by first letter
    >>> words = ['apple', 'banana', 'apricot', 'berry']
    >>> groups = defaultdict(list)
    >>> for word in words:
    ...     groups[word[0]].append(word)
    >>> dict(groups)
    {'a': ['apple', 'apricot'], 'b': ['banana', 'berry']}
    
    >>> # Count occurrences
    >>> counter = defaultdict(int)
    >>> for char in 'aabbbcc':
    ...     counter[char] += 1
    >>> dict(counter)
    {'a': 2, 'b': 3, 'c': 2}
    """
    pass


def counter_demo():
    """
    Counter for frequency counting.
    
    >>> from collections import Counter
    >>> c = Counter('aabbbcc')
    >>> c
    Counter({'b': 3, 'a': 2, 'c': 2})
    >>> c.most_common(2)
    [('b', 3), ('a', 2)]
    >>> c['d']  # Missing keys return 0
    0
    >>> c1 = Counter('abc')
    >>> c2 = Counter('bcd')
    >>> c1 + c2  # Add counts
    Counter({'b': 2, 'c': 2, 'a': 1, 'd': 1})
    >>> c1 & c2  # Intersection (min)
    Counter({'b': 1, 'c': 1})
    """
    pass


if __name__ == "__main__":
    import doctest
    doctest.testmod()
```

---

## Key Insights for Correctness

### Collision Resolution Strategies

```
1. SEPARATE CHAINING
   - Each bucket is a linked list
   - Unlimited elements per bucket
   - Simple but uses extra memory for pointers
   
   Bucket 0: [A] -> [D] -> None
   Bucket 1: [B] -> None
   Bucket 2: [C] -> [E] -> [F] -> None

2. OPEN ADDRESSING
   Linear Probing: h(k,i) = (h(k) + i) mod m
   - Check h(k), then h(k)+1, h(k)+2, ...
   - Clustering problem: long probe sequences
   
   Quadratic Probing: h(k,i) = (h(k) + c₁i + c₂i²) mod m
   - Better distribution than linear
   - May not visit all slots
   
   Double Hashing: h(k,i) = (h₁(k) + i*h₂(k)) mod m
   - Best distribution
   - More computation

3. ROBIN HOOD HASHING
   - Steal from rich (short probe) to give to poor (long probe)
   - Reduces variance in probe length
```

### Perfect Hashing

```python
# For static key sets, can achieve O(1) worst case
# Two-level hashing: first level directs to second-level tables
# Each second-level table has no collisions
# Space: O(n), Lookup: O(1) guaranteed
```

---

## Common Implementations

### Two Sum Pattern

```python
from typing import List, Tuple, Optional

def two_sum(nums: List[int], target: int) -> Optional[Tuple[int, int]]:
    """
    Find indices of two numbers that sum to target.
    
    Time: O(n), Space: O(n)
    
    >>> two_sum([2, 7, 11, 15], 9)
    (0, 1)
    >>> two_sum([3, 2, 4], 6)
    (1, 2)
    >>> two_sum([3, 3], 6)
    (0, 1)
    """
    seen = {}  # value -> index
    
    for i, num in enumerate(nums):
        complement = target - num
        if complement in seen:
            return (seen[complement], i)
        seen[num] = i
    
    return None


def two_sum_all_pairs(nums: List[int], target: int) -> List[Tuple[int, int]]:
    """
    Find ALL pairs that sum to target.
    
    >>> sorted(two_sum_all_pairs([1, 2, 3, 4, 3], 6))
    [(1, 3), (2, 4)]
    """
    from collections import defaultdict
    
    result = []
    seen = defaultdict(list)  # value -> list of indices
    
    for i, num in enumerate(nums):
        complement = target - num
        if complement in seen:
            for j in seen[complement]:
                result.append((j, i))
        seen[num].append(i)
    
    return result


if __name__ == "__main__":
    import doctest
    doctest.testmod()
```

### Group Anagrams

```python
from typing import List
from collections import defaultdict

def group_anagrams(strs: List[str]) -> List[List[str]]:
    """
    Group strings that are anagrams.
    
    Time: O(n * k) where k = max string length
    Space: O(n * k)
    
    >>> result = group_anagrams(["eat", "tea", "tan", "ate", "nat", "bat"])
    >>> sorted([sorted(g) for g in result])
    [['ate', 'eat', 'tea'], ['bat'], ['nat', 'tan']]
    """
    groups = defaultdict(list)
    
    for s in strs:
        # Use character count as key (more efficient than sorting)
        count = [0] * 26
        for c in s:
            count[ord(c) - ord('a')] += 1
        groups[tuple(count)].append(s)
    
    return list(groups.values())


if __name__ == "__main__":
    import doctest
    doctest.testmod()
```

### Subarray Sum Equals K

```python
from typing import List
from collections import defaultdict

def subarray_sum(nums: List[int], k: int) -> int:
    """
    Count subarrays with sum equal to k.
    
    Key insight: If prefix_sum[j] - prefix_sum[i] = k,
    then sum(nums[i+1:j+1]) = k
    
    Time: O(n), Space: O(n)
    
    >>> subarray_sum([1, 1, 1], 2)
    2
    >>> subarray_sum([1, 2, 3], 3)
    2
    >>> subarray_sum([1, -1, 0], 0)
    3
    """
    count = 0
    prefix_sum = 0
    prefix_counts = defaultdict(int)
    prefix_counts[0] = 1  # Empty prefix has sum 0
    
    for num in nums:
        prefix_sum += num
        # How many previous prefixes have sum = prefix_sum - k?
        count += prefix_counts[prefix_sum - k]
        prefix_counts[prefix_sum] += 1
    
    return count


if __name__ == "__main__":
    import doctest
    doctest.testmod()
```

### Longest Consecutive Sequence

```python
from typing import List

def longest_consecutive(nums: List[int]) -> int:
    """
    Find length of longest consecutive sequence.
    
    Key insight: Only start counting from sequence starts
    (numbers without predecessor in set)
    
    Time: O(n), Space: O(n)
    
    >>> longest_consecutive([100, 4, 200, 1, 3, 2])
    4
    >>> longest_consecutive([0, 3, 7, 2, 5, 8, 4, 6, 0, 1])
    9
    """
    if not nums:
        return 0
    
    num_set = set(nums)
    max_length = 0
    
    for num in num_set:
        # Only start from sequence beginning
        if num - 1 not in num_set:
            current_num = num
            current_length = 1
            
            while current_num + 1 in num_set:
                current_num += 1
                current_length += 1
            
            max_length = max(max_length, current_length)
    
    return max_length


if __name__ == "__main__":
    import doctest
    doctest.testmod()
```

---

## Step-by-Step Worked Example

### Problem: Find All Duplicates in Array

**Input:** `nums = [4, 3, 2, 7, 8, 2, 3, 1]`
**Output:** `[2, 3]`

```
Approach 1: Hash Set
- Iterate through array
- If seen before, it's a duplicate
- Add to seen set

seen = {}
num=4: not in seen, add {4}
num=3: not in seen, add {4,3}
num=2: not in seen, add {4,3,2}
num=7: not in seen, add {4,3,2,7}
num=8: not in seen, add {4,3,2,7,8}
num=2: IN SEEN! duplicate found: [2]
num=3: IN SEEN! duplicate found: [2,3]
num=1: not in seen, add {4,3,2,7,8,1}

Result: [2, 3]

Approach 2: O(1) Space (using input array as hash)
- Since nums in range [1, n], use array indices
- Mark visited by negating value at index (num-1)
- If already negative, it's a duplicate

[4, 3, 2, 7, 8, 2, 3, 1]
 0  1  2  3  4  5  6  7

num=4: index=3, nums[3]=7>0, negate -> [4,3,2,-7,8,2,3,1]
num=3: index=2, nums[2]=2>0, negate -> [4,3,-2,-7,8,2,3,1]
num=2: index=1, nums[1]=3>0, negate -> [4,-3,-2,-7,8,2,3,1]
num=7: index=6, nums[6]=3>0, negate -> [4,-3,-2,-7,8,2,-3,1]
num=8: index=7, nums[7]=1>0, negate -> [4,-3,-2,-7,8,2,-3,-1]
num=2: index=1, nums[1]=-3<0, DUPLICATE: [2]
num=3: index=2, nums[2]=-2<0, DUPLICATE: [2,3]
num=1: index=0, nums[0]=4>0, negate -> [-4,-3,-2,-7,8,2,-3,-1]
```

---

## Variants & Extensions

### Cuckoo Hashing

```python
class CuckooHashTable:
    """
    Cuckoo hashing: O(1) worst-case lookup.
    Uses two hash functions and two tables.
    If collision, kick out existing and rehash.
    
    >>> ht = CuckooHashTable()
    >>> ht.insert("a", 1)
    >>> ht.insert("b", 2)
    >>> ht.get("a")
    1
    """
    
    MAX_KICKS = 500
    
    def __init__(self, capacity: int = 16):
        self._capacity = capacity
        self._table1 = [None] * capacity
        self._table2 = [None] * capacity
    
    def _hash1(self, key) -> int:
        return hash(key) % self._capacity
    
    def _hash2(self, key) -> int:
        return (hash(key) // self._capacity) % self._capacity
    
    def get(self, key):
        """O(1) worst case lookup."""
        idx1 = self._hash1(key)
        if self._table1[idx1] and self._table1[idx1][0] == key:
            return self._table1[idx1][1]
        
        idx2 = self._hash2(key)
        if self._table2[idx2] and self._table2[idx2][0] == key:
            return self._table2[idx2][1]
        
        return None
    
    def insert(self, key, value) -> bool:
        """Insert with potential kicking."""
        for _ in range(self.MAX_KICKS):
            idx1 = self._hash1(key)
            if self._table1[idx1] is None:
                self._table1[idx1] = (key, value)
                return True
            
            # Kick out existing
            key, value, self._table1[idx1] = self._table1[idx1][0], self._table1[idx1][1], (key, value)
            
            idx2 = self._hash2(key)
            if self._table2[idx2] is None:
                self._table2[idx2] = (key, value)
                return True
            
            # Kick out from table2
            key, value, self._table2[idx2] = self._table2[idx2][0], self._table2[idx2][1], (key, value)
        
        # Need to resize
        return False


if __name__ == "__main__":
    import doctest
    doctest.testmod()
```

### Consistent Hashing (for distributed systems)

```python
import bisect
from typing import List, Optional
import hashlib

class ConsistentHash:
    """
    Consistent hashing for distributed systems.
    Minimizes rehashing when nodes are added/removed.
    """
    
    def __init__(self, nodes: List[str] = None, replicas: int = 100):
        self.replicas = replicas
        self.ring = []  # Sorted list of hashes
        self.hash_to_node = {}  # hash -> node mapping
        
        if nodes:
            for node in nodes:
                self.add_node(node)
    
    def _hash(self, key: str) -> int:
        """Hash function returning integer."""
        return int(hashlib.md5(key.encode()).hexdigest(), 16)
    
    def add_node(self, node: str) -> None:
        """Add node with virtual replicas."""
        for i in range(self.replicas):
            virtual_node = f"{node}:{i}"
            h = self._hash(virtual_node)
            bisect.insort(self.ring, h)
            self.hash_to_node[h] = node
    
    def remove_node(self, node: str) -> None:
        """Remove node and its replicas."""
        for i in range(self.replicas):
            virtual_node = f"{node}:{i}"
            h = self._hash(virtual_node)
            self.ring.remove(h)
            del self.hash_to_node[h]
    
    def get_node(self, key: str) -> Optional[str]:
        """Get node responsible for key."""
        if not self.ring:
            return None
        
        h = self._hash(key)
        idx = bisect.bisect(self.ring, h)
        
        if idx == len(self.ring):
            idx = 0
        
        return self.hash_to_node[self.ring[idx]]
```

---

## Common Interview Problems

| # | Problem | Difficulty | Approach | Time | LeetCode |
|---|---------|------------|----------|------|----------|
| 1 | Two Sum | Easy | Hash Map | O(n) | [#1](https://leetcode.com/problems/two-sum/) |
| 2 | Contains Duplicate | Easy | Hash Set | O(n) | [#217](https://leetcode.com/problems/contains-duplicate/) |
| 3 | Valid Anagram | Easy | Frequency Count | O(n) | [#242](https://leetcode.com/problems/valid-anagram/) |
| 4 | Group Anagrams | Medium | Hash Map | O(n·k) | [#49](https://leetcode.com/problems/group-anagrams/) |
| 5 | Top K Frequent Elements | Medium | Hash + Heap | O(n log k) | [#347](https://leetcode.com/problems/top-k-frequent-elements/) |
| 6 | Subarray Sum Equals K | Medium | Prefix Sum + Hash | O(n) | [#560](https://leetcode.com/problems/subarray-sum-equals-k/) |
| 7 | Longest Consecutive Sequence | Medium | Hash Set | O(n) | [#128](https://leetcode.com/problems/longest-consecutive-sequence/) |
| 8 | 4Sum II | Medium | Hash Map | O(n²) | [#454](https://leetcode.com/problems/4sum-ii/) |
| 9 | Design HashMap | Easy | Chaining | O(1) avg | [#706](https://leetcode.com/problems/design-hashmap/) |
| 10 | LRU Cache | Medium | Hash + DLL | O(1) | [#146](https://leetcode.com/problems/lru-cache/) |

---

## Fully Worked Sample Problem

### Problem: First Unique Character in a String

**Problem Statement:** Given a string `s`, find the first non-repeating character and return its index. If it doesn't exist, return -1.

**Difficulty:** Easy | [LeetCode #387](https://leetcode.com/problems/first-unique-character-in-a-string/)

```python
from collections import Counter

def first_uniq_char(s: str) -> int:
    """
    Find index of first unique character.
    
    Approach: Two passes - count frequencies, then find first with count 1.
    
    Time: O(n)
    Space: O(1) - at most 26 characters
    
    >>> first_uniq_char("leetcode")
    0
    >>> first_uniq_char("loveleetcode")
    2
    >>> first_uniq_char("aabb")
    -1
    """
    # Count frequencies
    count = Counter(s)
    
    # Find first unique
    for i, char in enumerate(s):
        if count[char] == 1:
            return i
    
    return -1


def first_uniq_char_single_pass(s: str) -> int:
    """
    Alternative: Track first occurrence index.
    
    >>> first_uniq_char_single_pass("leetcode")
    0
    >>> first_uniq_char_single_pass("loveleetcode")
    2
    """
    first_index = {}  # char -> first index (or -1 if repeated)
    
    for i, char in enumerate(s):
        if char not in first_index:
            first_index[char] = i
        else:
            first_index[char] = -1  # Mark as repeated
    
    min_index = len(s)
    for char, idx in first_index.items():
        if idx != -1:
            min_index = min(min_index, idx)
    
    return min_index if min_index < len(s) else -1


def first_uniq_char_array(s: str) -> int:
    """
    Using fixed-size array for lowercase letters.
    
    >>> first_uniq_char_array("leetcode")
    0
    >>> first_uniq_char_array("loveleetcode")
    2
    """
    count = [0] * 26
    
    for char in s:
        count[ord(char) - ord('a')] += 1
    
    for i, char in enumerate(s):
        if count[ord(char) - ord('a')] == 1:
            return i
    
    return -1


# Unit tests
def test_first_uniq_char():
    assert first_uniq_char("leetcode") == 0
    assert first_uniq_char("loveleetcode") == 2
    assert first_uniq_char("aabb") == -1
    assert first_uniq_char("") == -1
    assert first_uniq_char("z") == 0
    print("All tests passed!")


if __name__ == "__main__":
    import doctest
    doctest.testmod()
    test_first_uniq_char()
```

**Pattern(s) Used:** Frequency Counting, Hash Map

---

## Edge Cases & Pitfalls

- **Mutable Keys:** Keys must be hashable (immutable). Lists can't be dict keys
- **Hash Collision:** Don't assume O(1) is guaranteed; worst case is O(n)
- **Custom Objects:** Implement `__hash__` and `__eq__` for custom class keys
- **Iteration Order:** Python 3.7+ dicts maintain insertion order
- **Default Values:** Use `dict.get(key, default)` to avoid KeyError
- **Negative Indices:** Array-based hashing with negative numbers needs offset

---

## Follow-ups and Optimization Ideas

1. **Bloom Filter:** Probabilistic set membership with no false negatives
2. **Count-Min Sketch:** Approximate frequency counting in streaming
3. **HyperLogLog:** Cardinality estimation with minimal memory
4. **Perfect Hashing:** O(1) worst-case for static sets
5. **Thread-Safe Maps:** ConcurrentHashMap patterns

---

## Practice Checklist

| # | Problem | Difficulty | Link | Pattern |
|---|---------|------------|------|---------|
| 1 | Two Sum | Easy | [LC #1](https://leetcode.com/problems/two-sum/) | Hash Map |
| 2 | Contains Duplicate | Easy | [LC #217](https://leetcode.com/problems/contains-duplicate/) | Hash Set |
| 3 | Valid Anagram | Easy | [LC #242](https://leetcode.com/problems/valid-anagram/) | Frequency |
| 4 | First Unique Character | Easy | [LC #387](https://leetcode.com/problems/first-unique-character-in-a-string/) | Frequency |
| 5 | Intersection of Two Arrays | Easy | [LC #349](https://leetcode.com/problems/intersection-of-two-arrays/) | Set |
| 6 | Happy Number | Easy | [LC #202](https://leetcode.com/problems/happy-number/) | Set Cycle |
| 7 | Isomorphic Strings | Easy | [LC #205](https://leetcode.com/problems/isomorphic-strings/) | Two Maps |
| 8 | Word Pattern | Easy | [LC #290](https://leetcode.com/problems/word-pattern/) | Bijection |
| 9 | Group Anagrams | Medium | [LC #49](https://leetcode.com/problems/group-anagrams/) | Hash Map |
| 10 | Longest Consecutive Sequence | Medium | [LC #128](https://leetcode.com/problems/longest-consecutive-sequence/) | Set |
| 11 | Subarray Sum Equals K | Medium | [LC #560](https://leetcode.com/problems/subarray-sum-equals-k/) | Prefix Hash |
| 12 | Contiguous Array | Medium | [LC #525](https://leetcode.com/problems/contiguous-array/) | Prefix Hash |
| 13 | Find All Duplicates | Medium | [LC #442](https://leetcode.com/problems/find-all-duplicates-in-an-array/) | Index Hash |
| 14 | 4Sum II | Medium | [LC #454](https://leetcode.com/problems/4sum-ii/) | Hash Map |
| 15 | Design HashMap | Easy | [LC #706](https://leetcode.com/problems/design-hashmap/) | Implementation |
| 16 | Design HashSet | Easy | [LC #705](https://leetcode.com/problems/design-hashset/) | Implementation |
| 17 | LRU Cache | Medium | [LC #146](https://leetcode.com/problems/lru-cache/) | Hash + DLL |
| 18 | LFU Cache | Hard | [LC #460](https://leetcode.com/problems/lfu-cache/) | Multi-Hash |
| 19 | Copy List with Random Pointer | Medium | [LC #138](https://leetcode.com/problems/copy-list-with-random-pointer/) | Hash Map |
| 20 | Minimum Window Substring | Hard | [LC #76](https://leetcode.com/problems/minimum-window-substring/) | Hash + Window |

---

## Further Reading & References

- [CLRS Chapter 11 - Hash Tables](https://mitpress.mit.edu/books/introduction-algorithms-third-edition)
- [Python dict Implementation](https://docs.python.org/3/library/stdtypes.html#dict)
- [GeeksforGeeks - Hashing](https://www.geeksforgeeks.org/hashing-data-structure/)
- [VisuAlgo - Hash Table](https://visualgo.net/en/hashtable)
- [Robin Hood Hashing](https://www.sebastiansylvan.com/post/robin-hood-hashing-should-be-your-default-hash-table-implementation/)

---

## Flashcard Summary

**Q: What is a hash collision and how is it resolved?**
A: When two keys hash to the same index. Resolved by chaining (linked list at index) or open addressing (probing for next empty slot).

**Q: What is the load factor and why does it matter?**
A: α = n/m (elements/buckets). High load factor increases collisions; resize when α > threshold (typically 0.7).

**Q: Time complexity of hash table operations?**
A: Average O(1), worst O(n) when all keys collide.

**Q: How to use hash map for two-sum problem?**
A: Store seen values with indices; for each num, check if (target - num) exists in map.

**Q: Why can't lists be dictionary keys in Python?**
A: Lists are mutable; mutating a key would break the hash invariant. Use tuples instead.

**Q: What's the difference between set.discard() and set.remove()?**
A: `remove()` raises KeyError if element not found; `discard()` silently does nothing.
