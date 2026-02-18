---
title: "Bit Manipulation Patterns"
topic: "Problem Patterns"
difficulty: "Medium"
tags: ["bit-manipulation", "xor", "bitmask", "binary"]
status: "complete"
weight: 15
---

# Bit Manipulation Patterns

## 📚 Essential Bit Operations

```python
# Basic operations
x & y      # AND - both bits 1
x | y      # OR - either bit 1
x ^ y      # XOR - bits different
~x         # NOT - flip all bits
x << n     # Left shift - multiply by 2^n
x >> n     # Right shift - divide by 2^n

# Useful tricks
x & (x - 1)     # Clear lowest set bit
x & (-x)        # Isolate lowest set bit
x | (x + 1)     # Set lowest unset bit
x ^ (x - 1)     # Mask from lowest set bit to right

# Check operations
x & 1           # Check if odd
x & (1 << n)    # Check if nth bit is set
```

---

## 1️⃣ Single Number Problems

### Single Number I (LC 136)

```python
def single_number(nums: list[int]) -> int:
    """Find number appearing once (others appear twice)"""
    result = 0
    for num in nums:
        result ^= num  # XOR cancels pairs
    return result
```

### Single Number II (LC 137)

```python
def single_number_ii(nums: list[int]) -> int:
    """Find number appearing once (others appear three times)"""
    # Count bits at each position modulo 3
    result = 0
    for i in range(32):
        bit_sum = sum((num >> i) & 1 for num in nums)
        if bit_sum % 3:
            result |= (1 << i)
    
    # Handle negative numbers (Python specific)
    if result >= 2**31:
        result -= 2**32
    
    return result

# Alternative using state machine
def single_number_ii_v2(nums: list[int]) -> int:
    ones = twos = 0
    for num in nums:
        ones = (ones ^ num) & ~twos
        twos = (twos ^ num) & ~ones
    return ones
```

### Single Number III (LC 260)

```python
def single_number_iii(nums: list[int]) -> list[int]:
    """Find two numbers appearing once"""
    # XOR all numbers
    xor_all = 0
    for num in nums:
        xor_all ^= num
    
    # Find differing bit
    diff_bit = xor_all & (-xor_all)
    
    # Separate into groups
    a = b = 0
    for num in nums:
        if num & diff_bit:
            a ^= num
        else:
            b ^= num
    
    return [a, b]
```

---

## 2️⃣ Counting Bits

### Count Bits (LC 338)

```python
def count_bits(n: int) -> list[int]:
    """Count 1s in binary for 0 to n"""
    result = [0] * (n + 1)
    
    for i in range(1, n + 1):
        # i >> 1 is i without last bit
        # i & 1 is 1 if last bit is set
        result[i] = result[i >> 1] + (i & 1)
    
    return result
```

### Hamming Weight (LC 191)

```python
def hamming_weight(n: int) -> int:
    """Count number of 1 bits"""
    count = 0
    while n:
        n &= (n - 1)  # Clear lowest set bit
        count += 1
    return count

# Alternative
def hamming_weight_v2(n: int) -> int:
    return bin(n).count('1')
```

### Hamming Distance (LC 461)

```python
def hamming_distance(x: int, y: int) -> int:
    """Number of differing bits"""
    xor = x ^ y
    count = 0
    while xor:
        xor &= (xor - 1)
        count += 1
    return count
```

---

## 3️⃣ Power of Two

### Is Power of Two (LC 231)

```python
def is_power_of_two(n: int) -> bool:
    """Check if n is power of 2"""
    return n > 0 and (n & (n - 1)) == 0
```

### Is Power of Four (LC 342)

```python
def is_power_of_four(n: int) -> bool:
    """Check if n is power of 4"""
    # Power of 2 AND single bit at even position
    return n > 0 and (n & (n - 1)) == 0 and (n & 0xAAAAAAAA) == 0

# Alternative: n = 4^k means n = 2^(2k), so bit at position 2k
def is_power_of_four_v2(n: int) -> bool:
    return n > 0 and (n & (n - 1)) == 0 and (n - 1) % 3 == 0
```

---

## 4️⃣ Bit Reversal

### Reverse Bits (LC 190)

```python
def reverse_bits(n: int) -> int:
    """Reverse 32-bit integer"""
    result = 0
    for _ in range(32):
        result = (result << 1) | (n & 1)
        n >>= 1
    return result

# Divide and conquer approach
def reverse_bits_v2(n: int) -> int:
    n = ((n & 0xFFFF0000) >> 16) | ((n & 0x0000FFFF) << 16)
    n = ((n & 0xFF00FF00) >> 8) | ((n & 0x00FF00FF) << 8)
    n = ((n & 0xF0F0F0F0) >> 4) | ((n & 0x0F0F0F0F) << 4)
    n = ((n & 0xCCCCCCCC) >> 2) | ((n & 0x33333333) << 2)
    n = ((n & 0xAAAAAAAA) >> 1) | ((n & 0x55555555) << 1)
    return n
```

---

## 5️⃣ Subsets with Bitmask

### Generate All Subsets (LC 78)

```python
def subsets(nums: list[int]) -> list[list[int]]:
    """Generate all subsets using bitmask"""
    n = len(nums)
    result = []
    
    for mask in range(1 << n):  # 0 to 2^n - 1
        subset = []
        for i in range(n):
            if mask & (1 << i):
                subset.append(nums[i])
        result.append(subset)
    
    return result
```

### Subsets II with Duplicates (LC 90)

```python
def subsets_with_dup(nums: list[int]) -> list[list[int]]:
    """Generate unique subsets from array with duplicates"""
    nums.sort()
    n = len(nums)
    result = set()
    
    for mask in range(1 << n):
        subset = []
        for i in range(n):
            if mask & (1 << i):
                subset.append(nums[i])
        result.add(tuple(subset))
    
    return [list(s) for s in result]
```

---

## 6️⃣ Bitmask DP

### Travelling Salesman (Held-Karp)

```python
def tsp(dist: list[list[int]]) -> int:
    """Minimum cost to visit all cities and return"""
    n = len(dist)
    # dp[mask][i] = min cost to visit cities in mask, ending at i
    dp = [[float('inf')] * n for _ in range(1 << n)]
    dp[1][0] = 0  # Start at city 0
    
    for mask in range(1 << n):
        for last in range(n):
            if not (mask & (1 << last)):
                continue
            if dp[mask][last] == float('inf'):
                continue
            
            for next_city in range(n):
                if mask & (1 << next_city):
                    continue
                new_mask = mask | (1 << next_city)
                dp[new_mask][next_city] = min(
                    dp[new_mask][next_city],
                    dp[mask][last] + dist[last][next_city]
                )
    
    # Return to start
    full_mask = (1 << n) - 1
    return min(dp[full_mask][i] + dist[i][0] for i in range(1, n))
```

### Partition to K Equal Sum Subsets (LC 698)

```python
def can_partition_k_subsets(nums: list[int], k: int) -> bool:
    total = sum(nums)
    if total % k:
        return False
    
    target = total // k
    n = len(nums)
    nums.sort(reverse=True)
    
    if nums[0] > target:
        return False
    
    # dp[mask] = True if elements in mask can form valid groups
    dp = [False] * (1 << n)
    dp[0] = True
    current_sum = [0] * (1 << n)
    
    for mask in range(1 << n):
        if not dp[mask]:
            continue
        
        for i in range(n):
            if mask & (1 << i):
                continue
            
            next_mask = mask | (1 << i)
            if dp[next_mask]:
                continue
            
            bucket_sum = current_sum[mask] % target
            if bucket_sum + nums[i] <= target:
                dp[next_mask] = True
                current_sum[next_mask] = current_sum[mask] + nums[i]
    
    return dp[(1 << n) - 1]
```

---

## 7️⃣ XOR Properties

### Missing Number (LC 268)

```python
def missing_number(nums: list[int]) -> int:
    """Find missing number in 0 to n"""
    n = len(nums)
    result = n
    for i, num in enumerate(nums):
        result ^= i ^ num
    return result
```

### Find Duplicate (XOR approach won't work if number repeats more than twice)

```python
def find_duplicate_xor_modified(nums: list[int]) -> int:
    """Only works if each number appears exactly twice except one"""
    # For standard LC 287, use Floyd's algorithm instead
    pass
```

---

## 8️⃣ Maximum XOR

### Maximum XOR of Two Numbers (LC 421)

```python
class TrieNode:
    def __init__(self):
        self.children = {}

def find_maximum_xor(nums: list[int]) -> int:
    """Find maximum XOR of any two numbers"""
    # Build trie of binary representations
    root = TrieNode()
    
    for num in nums:
        node = root
        for i in range(31, -1, -1):
            bit = (num >> i) & 1
            if bit not in node.children:
                node.children[bit] = TrieNode()
            node = node.children[bit]
    
    max_xor = 0
    for num in nums:
        node = root
        curr_xor = 0
        for i in range(31, -1, -1):
            bit = (num >> i) & 1
            # Try to go opposite direction for max XOR
            if (1 - bit) in node.children:
                curr_xor |= (1 << i)
                node = node.children[1 - bit]
            else:
                node = node.children[bit]
        max_xor = max(max_xor, curr_xor)
    
    return max_xor
```

---

## 9️⃣ Useful Bit Patterns

```python
# Get/Set/Clear/Toggle nth bit
def get_bit(x, n):
    return (x >> n) & 1

def set_bit(x, n):
    return x | (1 << n)

def clear_bit(x, n):
    return x & ~(1 << n)

def toggle_bit(x, n):
    return x ^ (1 << n)

# Set/Clear rightmost 1
def clear_lowest_set_bit(x):
    return x & (x - 1)

def isolate_lowest_set_bit(x):
    return x & (-x)

# All bits below rightmost 1
def right_propagate(x):
    return x | (x - 1)

# Swap without temp
def swap(a, b):
    a ^= b
    b ^= a
    a ^= b
    return a, b

# Average without overflow
def average(a, b):
    return (a & b) + ((a ^ b) >> 1)
```

---

## 📝 Related Problems

| Problem | LC # | Pattern |
|---------|------|---------|
| Single Number | 136 | XOR |
| Single Number II | 137 | Bit Counting |
| Single Number III | 260 | XOR + Grouping |
| Number of 1 Bits | 191 | Brian Kernighan |
| Counting Bits | 338 | DP |
| Reverse Bits | 190 | Bit Reversal |
| Power of Two | 231 | n & (n-1) |
| Subsets | 78 | Bitmask |
| Missing Number | 268 | XOR |
| Maximum XOR | 421 | Trie |
| Bitwise AND of Range | 201 | Common Prefix |

---

*Last Updated: 2024*
