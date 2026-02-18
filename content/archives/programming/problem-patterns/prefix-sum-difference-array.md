---
title: "Prefix Sum & Difference Array Patterns"
topic: "Problem Patterns"
difficulty: "Medium"
tags: ["prefix-sum", "difference-array", "range-query", "cumulative-sum"]
status: "complete"
weight: 14
---

# Prefix Sum & Difference Array Patterns

## 📚 Overview

Prefix sums enable O(1) range sum queries. Difference arrays enable O(1) range updates.

---

## 1️⃣ Basic Prefix Sum

### 1D Prefix Sum

```python
class PrefixSum:
    def __init__(self, nums: list[int]):
        self.prefix = [0]
        for num in nums:
            self.prefix.append(self.prefix[-1] + num)
    
    def range_sum(self, left: int, right: int) -> int:
        """Sum of nums[left:right+1] in O(1)"""
        return self.prefix[right + 1] - self.prefix[left]


# Usage
nums = [1, 2, 3, 4, 5]
ps = PrefixSum(nums)
print(ps.range_sum(1, 3))  # 2 + 3 + 4 = 9
```

### 2D Prefix Sum (LC 304)

```python
class NumMatrix:
    def __init__(self, matrix: list[list[int]]):
        if not matrix:
            return
        
        rows, cols = len(matrix), len(matrix[0])
        # prefix[i][j] = sum of submatrix (0,0) to (i-1,j-1)
        self.prefix = [[0] * (cols + 1) for _ in range(rows + 1)]
        
        for i in range(rows):
            for j in range(cols):
                self.prefix[i+1][j+1] = (
                    matrix[i][j] +
                    self.prefix[i][j+1] +
                    self.prefix[i+1][j] -
                    self.prefix[i][j]
                )
    
    def sumRegion(self, r1: int, c1: int, r2: int, c2: int) -> int:
        """Sum of submatrix (r1,c1) to (r2,c2)"""
        return (
            self.prefix[r2+1][c2+1] -
            self.prefix[r1][c2+1] -
            self.prefix[r2+1][c1] +
            self.prefix[r1][c1]
        )
```

---

## 2️⃣ Subarray Sum Equals K (LC 560)

```python
def subarray_sum(nums: list[int], k: int) -> int:
    """Count subarrays with sum equal to k"""
    from collections import defaultdict
    
    count = 0
    prefix_sum = 0
    # Map: prefix_sum -> frequency
    prefix_count = defaultdict(int)
    prefix_count[0] = 1  # Empty prefix
    
    for num in nums:
        prefix_sum += num
        
        # If (prefix_sum - k) exists, found subarray
        count += prefix_count[prefix_sum - k]
        prefix_count[prefix_sum] += 1
    
    return count
```

### Variations

```python
def subarray_sum_divisible_by_k(nums: list[int], k: int) -> int:
    """Count subarrays with sum divisible by k (LC 974)"""
    from collections import defaultdict
    
    count = 0
    prefix_sum = 0
    remainder_count = defaultdict(int)
    remainder_count[0] = 1
    
    for num in nums:
        prefix_sum += num
        remainder = prefix_sum % k
        
        count += remainder_count[remainder]
        remainder_count[remainder] += 1
    
    return count


def count_nice_subarrays(nums: list[int], k: int) -> int:
    """Count subarrays with exactly k odd numbers (LC 1248)"""
    # Convert to prefix sum: odd=1, even=0
    def at_most_k(k):
        count = 0
        prefix_sum = 0
        left = 0
        
        for right, num in enumerate(nums):
            prefix_sum += num % 2
            
            while prefix_sum > k:
                prefix_sum -= nums[left] % 2
                left += 1
            
            count += right - left + 1
        
        return count
    
    return at_most_k(k) - at_most_k(k - 1)
```

---

## 3️⃣ Product Except Self (LC 238)

```python
def product_except_self(nums: list[int]) -> list[int]:
    """Product of array except self, without division"""
    n = len(nums)
    result = [1] * n
    
    # Left products
    left_product = 1
    for i in range(n):
        result[i] = left_product
        left_product *= nums[i]
    
    # Right products
    right_product = 1
    for i in range(n - 1, -1, -1):
        result[i] *= right_product
        right_product *= nums[i]
    
    return result
```

---

## 4️⃣ Difference Array

### Range Update in O(1)

```python
class DifferenceArray:
    """Efficient range updates, then build final array"""
    
    def __init__(self, n: int):
        self.diff = [0] * (n + 1)
    
    def add_range(self, left: int, right: int, val: int):
        """Add val to all elements in [left, right]"""
        self.diff[left] += val
        self.diff[right + 1] -= val
    
    def get_result(self) -> list[int]:
        """Build final array"""
        result = []
        current = 0
        for i in range(len(self.diff) - 1):
            current += self.diff[i]
            result.append(current)
        return result


# Example: Range Addition (LC 370)
def get_modified_array(length: int, updates: list[list[int]]) -> list[int]:
    diff = DifferenceArray(length)
    for start, end, inc in updates:
        diff.add_range(start, end, inc)
    return diff.get_result()
```

### Car Pooling (LC 1094)

```python
def car_pooling(trips: list[list[int]], capacity: int) -> bool:
    """Check if all trips possible with given capacity"""
    # Max location is 1000
    diff = [0] * 1001
    
    for passengers, start, end in trips:
        diff[start] += passengers
        diff[end] -= passengers  # Passengers leave at 'end'
    
    current = 0
    for d in diff:
        current += d
        if current > capacity:
            return False
    
    return True
```

### Corporate Flight Bookings (LC 1109)

```python
def corp_flight_bookings(bookings: list[list[int]], n: int) -> list[int]:
    """Count seats booked on each flight"""
    diff = [0] * (n + 1)
    
    for first, last, seats in bookings:
        diff[first - 1] += seats
        diff[last] -= seats
    
    result = []
    current = 0
    for i in range(n):
        current += diff[i]
        result.append(current)
    
    return result
```

---

## 5️⃣ Maximum Sum Subarray

### Kadane's Algorithm (LC 53)

```python
def max_subarray(nums: list[int]) -> int:
    """Maximum sum contiguous subarray"""
    max_sum = current = nums[0]
    
    for num in nums[1:]:
        current = max(num, current + num)
        max_sum = max(max_sum, current)
    
    return max_sum
```

### Maximum Subarray with Indices

```python
def max_subarray_with_indices(nums: list[int]) -> tuple:
    """Return (max_sum, start_index, end_index)"""
    max_sum = current = nums[0]
    max_start = max_end = 0
    temp_start = 0
    
    for i in range(1, len(nums)):
        if nums[i] > current + nums[i]:
            current = nums[i]
            temp_start = i
        else:
            current += nums[i]
        
        if current > max_sum:
            max_sum = current
            max_start = temp_start
            max_end = i
    
    return max_sum, max_start, max_end
```

### Maximum Circular Subarray (LC 918)

```python
def max_subarray_circular(nums: list[int]) -> int:
    """Max sum in circular array"""
    total = sum(nums)
    
    # Case 1: Max subarray doesn't wrap
    max_kadane = kadane_max(nums)
    
    # Case 2: Max subarray wraps = total - min_subarray
    min_kadane = kadane_min(nums)
    
    # If all negative, max_kadane is answer
    if total == min_kadane:
        return max_kadane
    
    return max(max_kadane, total - min_kadane)


def kadane_max(nums):
    max_sum = current = nums[0]
    for num in nums[1:]:
        current = max(num, current + num)
        max_sum = max(max_sum, current)
    return max_sum


def kadane_min(nums):
    min_sum = current = nums[0]
    for num in nums[1:]:
        current = min(num, current + num)
        min_sum = min(min_sum, current)
    return min_sum
```

---

## 6️⃣ Prefix XOR

### XOR Queries of a Subarray (LC 1310)

```python
def xor_queries(arr: list[int], queries: list[list[int]]) -> list[int]:
    """XOR of subarray for each query"""
    # prefix[i] = arr[0] ^ arr[1] ^ ... ^ arr[i-1]
    prefix = [0]
    for num in arr:
        prefix.append(prefix[-1] ^ num)
    
    result = []
    for left, right in queries:
        # arr[left] ^ ... ^ arr[right] = prefix[right+1] ^ prefix[left]
        result.append(prefix[right + 1] ^ prefix[left])
    
    return result
```

### Single Number III (LC 260)

```python
def single_number_iii(nums: list[int]) -> list[int]:
    """Find two numbers appearing once (others appear twice)"""
    # XOR all: gives a ^ b
    xor_all = 0
    for num in nums:
        xor_all ^= num
    
    # Find rightmost set bit (differs between a and b)
    diff_bit = xor_all & (-xor_all)
    
    # Separate into two groups
    a = b = 0
    for num in nums:
        if num & diff_bit:
            a ^= num
        else:
            b ^= num
    
    return [a, b]
```

---

## 7️⃣ Contiguous Array (LC 525)

```python
def find_max_length(nums: list[int]) -> int:
    """Longest subarray with equal 0s and 1s"""
    # Transform: 0 -> -1, 1 -> 1
    # Find longest subarray with sum = 0
    
    max_len = 0
    prefix_sum = 0
    first_occurrence = {0: -1}  # prefix_sum -> first index
    
    for i, num in enumerate(nums):
        prefix_sum += 1 if num == 1 else -1
        
        if prefix_sum in first_occurrence:
            max_len = max(max_len, i - first_occurrence[prefix_sum])
        else:
            first_occurrence[prefix_sum] = i
    
    return max_len
```

---

## 8️⃣ Path Sum in Binary Tree

### Path Sum III (LC 437)

```python
def path_sum(root, target_sum: int) -> int:
    """Count paths summing to target (any direction)"""
    from collections import defaultdict
    
    count = 0
    prefix_count = defaultdict(int)
    prefix_count[0] = 1
    
    def dfs(node, current_sum):
        nonlocal count
        if not node:
            return
        
        current_sum += node.val
        count += prefix_count[current_sum - target_sum]
        prefix_count[current_sum] += 1
        
        dfs(node.left, current_sum)
        dfs(node.right, current_sum)
        
        prefix_count[current_sum] -= 1  # Backtrack
    
    dfs(root, 0)
    return count
```

---

## 📝 Related Problems

| Problem | LC # | Pattern |
|---------|------|---------|
| Range Sum Query - Immutable | 303 | 1D Prefix Sum |
| Range Sum Query 2D | 304 | 2D Prefix Sum |
| Subarray Sum Equals K | 560 | Prefix + HashMap |
| Continuous Subarray Sum | 523 | Prefix Mod |
| Subarray Sums Divisible by K | 974 | Prefix Mod |
| Product of Array Except Self | 238 | Left/Right Products |
| Range Addition | 370 | Difference Array |
| Car Pooling | 1094 | Difference Array |
| Maximum Subarray | 53 | Kadane's |
| Maximum Circular Subarray | 918 | Kadane's + Min |
| Contiguous Array | 525 | Transform + Prefix |
| Path Sum III | 437 | Tree Prefix Sum |

---

*Last Updated: 2024*
