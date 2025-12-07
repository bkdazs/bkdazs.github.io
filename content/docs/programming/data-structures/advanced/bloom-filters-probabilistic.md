---
title: "Bloom Filters & Probabilistic Data Structures"
weight: 16
---

# Bloom Filters & Probabilistic Data Structures

---
title: Bloom Filters and Probabilistic Data Structures
topic: Data Structures
difficulty: Medium
tags: [bloom-filter, count-min-sketch, hyperloglog, probabilistic, interview-concept]
status: complete
---

## 1. Summary / Quick Reference

### Bloom Filter
| Property | Value |
|----------|-------|
| Insert | O(k) where k = hash functions |
| Query | O(k) |
| Space | O(m) bits |
| False Positive | Possible |
| False Negative | Never |

### Count-Min Sketch
| Property | Value |
|----------|-------|
| Insert | O(d) where d = depth |
| Query | O(d) |
| Space | O(w × d) |
| Overcount | Possible |
| Undercount | Never |

### HyperLogLog
| Property | Value |
|----------|-------|
| Insert | O(1) |
| Query | O(m) |
| Space | O(m) registers |
| Accuracy | ~2% error |

---

## 2. Bloom Filter

### What is a Bloom Filter?
A space-efficient probabilistic data structure for set membership queries.
- **Can tell you**: "Definitely NOT in set" or "Possibly in set"
- **Cannot tell you**: "Definitely in set"

### How It Works
1. Initialize a bit array of size m, all zeros
2. Use k different hash functions
3. To insert: set bits at h₁(x), h₂(x), ..., hₖ(x) to 1
4. To query: check if ALL bits at h₁(x), h₂(x), ..., hₖ(x) are 1

```
Insert "apple" with 3 hash functions:
h1("apple") = 2, h2("apple") = 5, h3("apple") = 11

Bit Array: [0 0 1 0 0 1 0 0 0 0 0 1 0 0 0]
              ↑     ↑           ↑
            idx 2  idx 5      idx 11

Query "apple": All bits are 1 → "Possibly in set"
Query "banana": h1=3, h2=7, h3=11 → bit[3]=0 → "Definitely NOT in set"
```

### Implementation

```python
import hashlib
from typing import List, Any
import math

class BloomFilter:
    """
    Bloom Filter for probabilistic set membership testing.
    
    Space Complexity: O(m) bits
    Time Complexity: O(k) per operation
    
    False positive rate ≈ (1 - e^(-kn/m))^k
    where:
        m = number of bits
        n = expected number of elements
        k = number of hash functions
    """
    
    def __init__(self, expected_items: int, false_positive_rate: float = 0.01):
        """
        Initialize Bloom Filter with optimal parameters.
        
        Args:
            expected_items: Expected number of items to insert
            false_positive_rate: Desired false positive probability
        """
        # Optimal number of bits
        self.m = self._optimal_m(expected_items, false_positive_rate)
        # Optimal number of hash functions
        self.k = self._optimal_k(self.m, expected_items)
        
        # Bit array (using list of booleans for simplicity)
        self.bit_array = [False] * self.m
        self.size = 0
    
    @staticmethod
    def _optimal_m(n: int, p: float) -> int:
        """Calculate optimal number of bits."""
        return int(-n * math.log(p) / (math.log(2) ** 2))
    
    @staticmethod
    def _optimal_k(m: int, n: int) -> int:
        """Calculate optimal number of hash functions."""
        return max(1, int(m / n * math.log(2)))
    
    def _hashes(self, item: Any) -> List[int]:
        """Generate k hash values for item."""
        # Use double hashing technique: h(i) = h1 + i * h2
        item_str = str(item).encode('utf-8')
        
        h1 = int(hashlib.md5(item_str).hexdigest(), 16)
        h2 = int(hashlib.sha256(item_str).hexdigest(), 16)
        
        return [(h1 + i * h2) % self.m for i in range(self.k)]
    
    def add(self, item: Any) -> None:
        """Add item to the filter."""
        for idx in self._hashes(item):
            self.bit_array[idx] = True
        self.size += 1
    
    def __contains__(self, item: Any) -> bool:
        """Check if item might be in the filter."""
        return all(self.bit_array[idx] for idx in self._hashes(item))
    
    def might_contain(self, item: Any) -> bool:
        """Alias for __contains__."""
        return item in self
    
    @property
    def estimated_false_positive_rate(self) -> float:
        """Estimate current false positive rate."""
        # p ≈ (1 - e^(-kn/m))^k
        if self.size == 0:
            return 0.0
        return (1 - math.exp(-self.k * self.size / self.m)) ** self.k


# Example usage
if __name__ == "__main__":
    # Create filter expecting 1000 items with 1% FP rate
    bf = BloomFilter(expected_items=1000, false_positive_rate=0.01)
    
    # Add items
    words = ["apple", "banana", "cherry", "date", "elderberry"]
    for word in words:
        bf.add(word)
    
    # Query
    print(f"'apple' in filter: {bf.might_contain('apple')}")      # True
    print(f"'fig' in filter: {bf.might_contain('fig')}")          # False (or rarely True)
    print(f"'banana' in filter: {'banana' in bf}")                # True
    
    print(f"\nFilter stats:")
    print(f"  Bits used: {bf.m}")
    print(f"  Hash functions: {bf.k}")
    print(f"  Items added: {bf.size}")
    print(f"  Est. FP rate: {bf.estimated_false_positive_rate:.4f}")
```

### Scalable Bloom Filter

```python
class ScalableBloomFilter:
    """
    Scalable Bloom Filter that grows as needed.
    
    When current filter is "full", create a new one with 
    tighter FP rate to maintain overall guarantee.
    """
    
    def __init__(self, initial_capacity: int = 1000, 
                 error_rate: float = 0.01,
                 growth_factor: float = 2,
                 tightening_ratio: float = 0.5):
        """
        Args:
            initial_capacity: Initial expected items
            error_rate: Target overall false positive rate
            growth_factor: How much to grow each filter
            tightening_ratio: How much to tighten error rate for new filters
        """
        self.initial_capacity = initial_capacity
        self.error_rate = error_rate
        self.growth_factor = growth_factor
        self.tightening_ratio = tightening_ratio
        
        self.filters: List[BloomFilter] = []
        self._add_filter()
    
    def _add_filter(self) -> None:
        """Add a new filter with tighter parameters."""
        if not self.filters:
            new_filter = BloomFilter(
                self.initial_capacity,
                self.error_rate * (1 - self.tightening_ratio)
            )
        else:
            # Each successive filter is larger with tighter FP rate
            idx = len(self.filters)
            capacity = int(self.initial_capacity * (self.growth_factor ** idx))
            fp_rate = self.error_rate * (self.tightening_ratio ** idx)
            new_filter = BloomFilter(capacity, fp_rate)
        
        self.filters.append(new_filter)
    
    def add(self, item: Any) -> None:
        """Add item, creating new filter if needed."""
        current = self.filters[-1]
        
        # Check if current filter is "full" (near saturation)
        if current.size >= current.m // current.k:
            self._add_filter()
            current = self.filters[-1]
        
        current.add(item)
    
    def __contains__(self, item: Any) -> bool:
        """Check if item might be in any filter."""
        return any(item in bf for bf in self.filters)
```

### Counting Bloom Filter

```python
class CountingBloomFilter:
    """
    Counting Bloom Filter - supports deletion.
    
    Instead of bits, uses counters. Increment on add, decrement on delete.
    
    Trade-off: Uses more space (typically 4 bits per counter)
    """
    
    def __init__(self, expected_items: int, false_positive_rate: float = 0.01):
        self.m = self._optimal_m(expected_items, false_positive_rate)
        self.k = self._optimal_k(self.m, expected_items)
        
        # Counter array instead of bits
        self.counters = [0] * self.m
        self.size = 0
    
    @staticmethod
    def _optimal_m(n: int, p: float) -> int:
        return int(-n * math.log(p) / (math.log(2) ** 2))
    
    @staticmethod
    def _optimal_k(m: int, n: int) -> int:
        return max(1, int(m / n * math.log(2)))
    
    def _hashes(self, item: Any) -> List[int]:
        item_str = str(item).encode('utf-8')
        h1 = int(hashlib.md5(item_str).hexdigest(), 16)
        h2 = int(hashlib.sha256(item_str).hexdigest(), 16)
        return [(h1 + i * h2) % self.m for i in range(self.k)]
    
    def add(self, item: Any) -> None:
        """Add item to filter."""
        for idx in self._hashes(item):
            self.counters[idx] += 1
        self.size += 1
    
    def remove(self, item: Any) -> bool:
        """
        Remove item from filter.
        
        Warning: Only remove items that were definitely added!
        """
        if item not in self:
            return False
        
        for idx in self._hashes(item):
            self.counters[idx] = max(0, self.counters[idx] - 1)
        self.size -= 1
        return True
    
    def __contains__(self, item: Any) -> bool:
        """Check if item might be in filter."""
        return all(self.counters[idx] > 0 for idx in self._hashes(item))


# Example
cbf = CountingBloomFilter(1000)
cbf.add("apple")
cbf.add("banana")

print(f"'apple' in filter: {'apple' in cbf}")   # True
cbf.remove("apple")
print(f"'apple' after removal: {'apple' in cbf}")  # False
```

---

## 3. Count-Min Sketch

### What is Count-Min Sketch?
A probabilistic data structure for frequency estimation in streams.
- Answers: "What's the approximate frequency of item x?"
- May **overestimate** but never underestimate

### Implementation

```python
class CountMinSketch:
    """
    Count-Min Sketch for approximate frequency counting.
    
    Parameters:
        width (w): Number of counters per row
        depth (d): Number of hash functions (rows)
    
    Error bound: frequency ≤ true_frequency + ε × N
    where ε = e/w and N = total count
    
    Probability of exceeding bound: δ = e^(-d)
    """
    
    def __init__(self, width: int = 1000, depth: int = 5):
        """
        Args:
            width: Number of counters per hash function
            depth: Number of hash functions
        """
        self.width = width
        self.depth = depth
        
        # 2D array of counters
        self.table = [[0] * width for _ in range(depth)]
        self.total_count = 0
        
        # Generate random seeds for hash functions
        import random
        self.seeds = [random.randint(0, 2**32) for _ in range(depth)]
    
    def _hash(self, item: Any, seed: int) -> int:
        """Hash item with given seed."""
        item_str = str(item).encode('utf-8')
        h = hashlib.md5(item_str + str(seed).encode()).hexdigest()
        return int(h, 16) % self.width
    
    def add(self, item: Any, count: int = 1) -> None:
        """Add item with given count."""
        for i in range(self.depth):
            idx = self._hash(item, self.seeds[i])
            self.table[i][idx] += count
        self.total_count += count
    
    def query(self, item: Any) -> int:
        """
        Query frequency of item (minimum across all hash functions).
        
        Returns: Estimated count (may be overestimate)
        """
        return min(
            self.table[i][self._hash(item, self.seeds[i])]
            for i in range(self.depth)
        )
    
    def estimate(self, item: Any) -> int:
        """Alias for query."""
        return self.query(item)


# Example: Word frequency in stream
cms = CountMinSketch(width=1000, depth=5)

# Simulate stream
words = ["apple"] * 100 + ["banana"] * 50 + ["cherry"] * 25
for word in words:
    cms.add(word)

print(f"Frequency estimates:")
print(f"  'apple': {cms.query('apple')}")    # ≈ 100
print(f"  'banana': {cms.query('banana')}")  # ≈ 50
print(f"  'cherry': {cms.query('cherry')}")  # ≈ 25
print(f"  'date': {cms.query('date')}")      # ≈ 0 (or small due to collisions)
```

### Count-Min Sketch with Conservative Update

```python
class CountMinSketchConservative(CountMinSketch):
    """
    Count-Min Sketch with Conservative Update.
    
    Only increment counters up to the minimum + count.
    Reduces overestimation.
    """
    
    def add(self, item: Any, count: int = 1) -> None:
        """Conservative update: only increment to min + count."""
        indices = [self._hash(item, self.seeds[i]) for i in range(self.depth)]
        
        # Find current estimate
        current_min = min(self.table[i][indices[i]] for i in range(self.depth))
        new_value = current_min + count
        
        # Update only up to new_value
        for i in range(self.depth):
            self.table[i][indices[i]] = max(self.table[i][indices[i]], new_value)
        
        self.total_count += count
```

---

## 4. HyperLogLog

### What is HyperLogLog?
A probabilistic algorithm for cardinality estimation (counting unique elements).
- Answers: "Approximately how many unique items have I seen?"
- Uses O(log log n) bits per register!

### How It Works
1. Hash each item
2. Count leading zeros in hash → estimate of 2^(zeros)
3. Use harmonic mean of many registers for accuracy

```python
import struct

class HyperLogLog:
    """
    HyperLogLog for cardinality estimation.
    
    Space: O(m) where m = 2^p registers
    Accuracy: Standard error ≈ 1.04 / sqrt(m)
    
    For p=14: m=16384, ~2% error, ~12KB space
    """
    
    def __init__(self, precision: int = 14):
        """
        Args:
            precision: Number of bits for register indexing (4-18)
                       Higher = more accurate but more space
        """
        if not 4 <= precision <= 18:
            raise ValueError("Precision must be between 4 and 18")
        
        self.p = precision
        self.m = 1 << precision  # 2^p registers
        self.registers = [0] * self.m
        
        # Alpha correction factor
        if self.m == 16:
            self.alpha = 0.673
        elif self.m == 32:
            self.alpha = 0.697
        elif self.m == 64:
            self.alpha = 0.709
        else:
            self.alpha = 0.7213 / (1 + 1.079 / self.m)
    
    def _hash(self, item: Any) -> int:
        """Generate 64-bit hash."""
        item_bytes = str(item).encode('utf-8')
        h = hashlib.sha256(item_bytes).digest()[:8]
        return struct.unpack('<Q', h)[0]
    
    def _leading_zeros(self, value: int, max_bits: int = 64) -> int:
        """Count leading zeros after the first p bits."""
        if value == 0:
            return max_bits - self.p
        
        count = 0
        mask = 1 << (max_bits - self.p - 1)
        
        while mask > 0 and (value & mask) == 0:
            count += 1
            mask >>= 1
        
        return count + 1  # +1 because we count position of first 1
    
    def add(self, item: Any) -> None:
        """Add item to HyperLogLog."""
        h = self._hash(item)
        
        # First p bits determine register
        register_idx = h >> (64 - self.p)
        
        # Remaining bits used for counting
        remaining = h & ((1 << (64 - self.p)) - 1)
        rank = self._leading_zeros(remaining, 64 - self.p)
        
        self.registers[register_idx] = max(self.registers[register_idx], rank)
    
    def count(self) -> int:
        """
        Estimate cardinality.
        
        Returns: Estimated number of unique items
        """
        # Harmonic mean
        indicator = sum(2.0 ** (-r) for r in self.registers)
        estimate = self.alpha * self.m * self.m / indicator
        
        # Small range correction (linear counting)
        if estimate <= 2.5 * self.m:
            zeros = self.registers.count(0)
            if zeros > 0:
                estimate = self.m * math.log(self.m / zeros)
        
        # Large range correction (not needed for 64-bit hash)
        
        return int(estimate)
    
    def merge(self, other: 'HyperLogLog') -> 'HyperLogLog':
        """Merge two HyperLogLog structures."""
        if self.p != other.p:
            raise ValueError("Precision must match for merge")
        
        result = HyperLogLog(self.p)
        result.registers = [
            max(self.registers[i], other.registers[i])
            for i in range(self.m)
        ]
        return result


# Example
hll = HyperLogLog(precision=14)

# Add items
for i in range(10000):
    hll.add(f"item_{i % 5000}")  # 5000 unique items

print(f"Estimated cardinality: {hll.count()}")  # ≈ 5000
print(f"Space used: {hll.m * 6 // 8} bytes")    # 6 bits per register
```

---

## 5. Cuckoo Filter

### What is Cuckoo Filter?
An improvement over Bloom Filter that supports deletion and has better space efficiency for low FP rates.

```python
class CuckooFilter:
    """
    Cuckoo Filter - supports insertion, lookup, and deletion.
    
    Better than Bloom Filter when:
        - Need deletion support
        - FP rate < 3%
    
    Space: ~12 bits per item (vs 10 bits for Bloom at 1% FP)
    """
    
    def __init__(self, capacity: int, bucket_size: int = 4, 
                 fingerprint_size: int = 8, max_kicks: int = 500):
        """
        Args:
            capacity: Maximum number of items
            bucket_size: Items per bucket
            fingerprint_size: Bits per fingerprint
            max_kicks: Maximum relocations before failure
        """
        self.bucket_size = bucket_size
        self.fingerprint_size = fingerprint_size
        self.max_kicks = max_kicks
        
        # Number of buckets (power of 2)
        self.num_buckets = 1
        while self.num_buckets < capacity // bucket_size:
            self.num_buckets <<= 1
        
        self.buckets = [[None] * bucket_size for _ in range(self.num_buckets)]
        self.size = 0
    
    def _fingerprint(self, item: Any) -> int:
        """Generate fingerprint for item."""
        h = hashlib.sha256(str(item).encode()).hexdigest()
        fp = int(h[:self.fingerprint_size // 4], 16)
        # Ensure fingerprint is never 0
        return fp if fp != 0 else 1
    
    def _hash(self, item: Any) -> int:
        """Primary hash for bucket index."""
        h = hashlib.md5(str(item).encode()).hexdigest()
        return int(h, 16) % self.num_buckets
    
    def _alt_index(self, index: int, fingerprint: int) -> int:
        """Calculate alternate index using partial-key cuckoo hashing."""
        # i2 = i1 XOR hash(fingerprint)
        fp_hash = hashlib.md5(str(fingerprint).encode()).hexdigest()
        return (index ^ (int(fp_hash, 16) % self.num_buckets)) % self.num_buckets
    
    def insert(self, item: Any) -> bool:
        """
        Insert item into filter.
        
        Returns: True if successful, False if filter is full
        """
        fingerprint = self._fingerprint(item)
        i1 = self._hash(item)
        i2 = self._alt_index(i1, fingerprint)
        
        # Try to insert in either bucket
        for bucket_idx in [i1, i2]:
            bucket = self.buckets[bucket_idx]
            for j in range(self.bucket_size):
                if bucket[j] is None:
                    bucket[j] = fingerprint
                    self.size += 1
                    return True
        
        # Both buckets full, need to relocate
        import random
        idx = random.choice([i1, i2])
        
        for _ in range(self.max_kicks):
            # Pick random entry from bucket
            j = random.randrange(self.bucket_size)
            fingerprint, self.buckets[idx][j] = self.buckets[idx][j], fingerprint
            
            # Find alternate bucket
            idx = self._alt_index(idx, fingerprint)
            
            # Try to insert
            bucket = self.buckets[idx]
            for j in range(self.bucket_size):
                if bucket[j] is None:
                    bucket[j] = fingerprint
                    self.size += 1
                    return True
        
        # Failed to insert (filter is full)
        return False
    
    def lookup(self, item: Any) -> bool:
        """Check if item might be in filter."""
        fingerprint = self._fingerprint(item)
        i1 = self._hash(item)
        i2 = self._alt_index(i1, fingerprint)
        
        return (fingerprint in self.buckets[i1] or 
                fingerprint in self.buckets[i2])
    
    def delete(self, item: Any) -> bool:
        """
        Delete item from filter.
        
        Returns: True if found and deleted
        """
        fingerprint = self._fingerprint(item)
        i1 = self._hash(item)
        i2 = self._alt_index(i1, fingerprint)
        
        for bucket_idx in [i1, i2]:
            bucket = self.buckets[bucket_idx]
            for j in range(self.bucket_size):
                if bucket[j] == fingerprint:
                    bucket[j] = None
                    self.size -= 1
                    return True
        
        return False
    
    def __contains__(self, item: Any) -> bool:
        return self.lookup(item)


# Example
cf = CuckooFilter(capacity=10000)

# Insert items
for i in range(1000):
    cf.insert(f"item_{i}")

print(f"'item_500' in filter: {cf.lookup('item_500')}")  # True
print(f"'item_9999' in filter: {cf.lookup('item_9999')}")  # False

# Delete
cf.delete("item_500")
print(f"'item_500' after delete: {cf.lookup('item_500')}")  # False
```

---

## 6. Comparison of Probabilistic Data Structures

| Feature | Bloom Filter | Counting BF | Cuckoo Filter | Count-Min | HyperLogLog |
|---------|--------------|-------------|---------------|-----------|-------------|
| Use Case | Membership | Membership | Membership | Frequency | Cardinality |
| Delete | ❌ | ✅ | ✅ | ❌ | ❌ |
| False Positive | ✅ | ✅ | ✅ | N/A | N/A |
| False Negative | ❌ | ❌ | ❌ | N/A | N/A |
| Space (1% FP) | ~10 bits/item | ~40 bits/item | ~12 bits/item | - | - |
| Merge | ✅ (OR) | ✅ (ADD) | ❌ | ✅ | ✅ |

---

## 7. Real-World Applications

### 1. Bloom Filter Use Cases
- Database query optimization (avoid disk reads)
- Spell checkers (is this word in dictionary?)
- Network routers (is this packet malicious?)
- Bitcoin (SPV client block filtering)
- Chrome Safe Browsing (malicious URL check)

### 2. Count-Min Sketch Use Cases
- Heavy hitters detection (top-k items)
- Network traffic monitoring
- Click stream analysis
- Natural language processing

### 3. HyperLogLog Use Cases
- Unique visitor counting (web analytics)
- Cardinality estimation in databases
- Redis PFCOUNT command
- Network flow analysis

---

## 8. LeetCode & Interview Questions

| Problem | Data Structure | Concept |
|---------|----------------|---------|
| Design an efficient set | Bloom Filter | Trade-offs |
| Top K frequent elements | Count-Min Sketch | Streaming |
| Count unique users | HyperLogLog | Cardinality |
| URL shortener with cache | Bloom Filter | Cache miss |

### Common Interview Questions

**Q1: When would you use a Bloom Filter over a Hash Set?**
- Memory constrained scenarios
- Acceptable false positives, no false negatives
- Very large datasets that don't fit in memory

**Q2: How do you choose Bloom Filter parameters?**
- Start with desired FP rate and expected elements
- m ≈ -n × ln(p) / (ln 2)²
- k ≈ (m/n) × ln 2

**Q3: What's the trade-off between Count-Min Sketch width and depth?**
- More width → lower error per count
- More depth → lower probability of high error
- Space = O(width × depth)

---

## 9. References

1. "Space/Time Trade-offs in Hash Coding with Allowable Errors" - Bloom (1970)
2. "An Improved Data Stream Summary: The Count-Min Sketch" - Cormode & Muthukrishnan
3. "HyperLogLog: the analysis of a near-optimal cardinality estimation algorithm" - Flajolet et al.
4. "Cuckoo Filter: Practically Better Than Bloom" - Fan et al.
5. Redis documentation on HyperLogLog
