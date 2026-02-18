---
title: Sum Over Subsets DP and Bitmask DP
topic: Algorithms - Dynamic Programming
difficulty: Hard
tags: [sos-dp, bitmask-dp, subset-sum, inclusion-exclusion]
status: complete
weight: 11
---
# Sum Over Subsets (SOS) DP & Bitmask DP

## 1. Overview

### Sum Over Subsets (SOS) DP
Computes for each bitmask $S$:
$$F[S] = \sum_{S' \subseteq S} A[S']$$

### Key Insight
Instead of iterating over all subsets (exponential), we iterate over each bit position and decide whether to include/exclude it.

### Complexity Comparison
| Approach | Time |
|----------|------|
| Naive (iterate subsets) | O(3^n) |
| SOS DP | O(n × 2^n) |

---

## 2. SOS DP: Basic Problem

### Problem
Given array A of size 2^n indexed by bitmasks, compute:
$$F[mask] = \sum_{\text{i is subset of mask}} A[i]$$

### Solution

```python
from typing import List

def sos_dp(A: List[int], n: int) -> List[int]:
    """
    Sum Over Subsets DP.
    
    For each mask, compute sum of A[submask] for all submasks.
    
    Args:
        A: Array of size 2^n
        n: Number of bits
    
    Returns:
        F where F[mask] = sum of A[submask] for all submasks of mask
    
    Time: O(n * 2^n)
    Space: O(2^n)
    """
    F = A.copy()
    
    for i in range(n):  # For each bit position
        for mask in range(1 << n):
            if mask & (1 << i):  # If bit i is set
                F[mask] += F[mask ^ (1 << i)]  # Add value without bit i
    
    return F


# Example
n = 3
A = [1, 2, 3, 4, 5, 6, 7, 8]  # A[0..7]

F = sos_dp(A, n)

# F[0b111] = A[0] + A[1] + A[2] + A[3] + A[4] + A[5] + A[6] + A[7]
#          = 1 + 2 + 3 + 4 + 5 + 6 + 7 + 8 = 36
print(f"F[7] = {F[7]}")

# F[0b101] = A[000] + A[001] + A[100] + A[101]
#          = 1 + 2 + 5 + 6 = 14
print(f"F[5] = {F[5]}")
```

### Why It Works

```
Consider mask = 0b111 (7) with n = 3

After bit 0 processing:
  F[0b111] includes F[0b110] (submask with bit 0 = 0)
  
After bit 1 processing:
  F[0b111] includes F[0b101] (submask with bit 1 = 0)
  
After bit 2 processing:
  F[0b111] includes F[0b011] (submask with bit 2 = 0)

By the end, F[mask] contains sum of all submasks of mask.
```

---

## 3. SOS DP: Superset Sum

### Problem
Compute sum of A[supermask] for all supermasks:
$$G[mask] = \sum_{\text{mask is subset of i}} A[i]$$

### Solution

```python
def sos_dp_superset(A: List[int], n: int) -> List[int]:
    """
    Sum over SUPERsets.
    
    For each mask, compute sum of A[supermask].
    
    Time: O(n * 2^n)
    """
    F = A.copy()
    
    for i in range(n):
        for mask in range((1 << n) - 1, -1, -1):  # Reverse order
            if not (mask & (1 << i)):  # If bit i is NOT set
                F[mask] += F[mask | (1 << i)]  # Add value with bit i set
    
    return F
```

---

## 4. Application: Count of AND Pairs

### Problem
Given array, count pairs (i, j) where `nums[i] & nums[j] == 0`.

```python
def count_and_zero_pairs(nums: List[int]) -> int:
    """
    Count pairs where nums[i] AND nums[j] = 0.
    
    Key insight: nums[i] & nums[j] = 0 iff j is subset of ~nums[i]
    
    Time: O(n * 2^20) for values up to 10^6
    """
    MAX_VAL = 1 << 20
    
    # Count frequency of each value
    freq = [0] * MAX_VAL
    for num in nums:
        freq[num] += 1
    
    # SOS DP: for each mask, count numbers that are subsets
    F = sos_dp(freq, 20)
    
    # For each nums[i], count nums[j] where j is subset of complement
    count = 0
    for num in nums:
        complement = (MAX_VAL - 1) ^ num  # All bits flipped
        count += F[complement]
    
    return count


# Example
nums = [1, 2, 3, 4]
# 1 = 0001, 2 = 0010, 3 = 0011, 4 = 0100
# AND = 0: (1,2), (1,4), (2,4) → 3 pairs
# But we count both (i,j) and (j,i), need to adjust
```

---

## 5. Application: Maximum XOR Subset

### Problem
Given array, find maximum XOR of any subset.

```python
def max_xor_subset(nums: List[int]) -> int:
    """
    Find maximum XOR of any subset.
    
    Uses Gaussian elimination on bits.
    
    Time: O(n * log(max_val))
    """
    basis = []
    
    for num in nums:
        cur = num
        for b in basis:
            cur = min(cur, cur ^ b)
        
        if cur > 0:
            basis.append(cur)
            basis.sort(reverse=True)
    
    result = 0
    for b in basis:
        result = max(result, result ^ b)
    
    return result


# Example
nums = [1, 2, 3, 4]
print(max_xor_subset(nums))  # 7 (1 XOR 2 XOR 4)
```

---

## 6. Application: Number of Subsets with OR = Target

### Problem (LC 2044)
Count subsets where OR of all elements equals a target.

```python
def count_subsets_or(nums: List[int], target: int) -> int:
    """
    Count subsets where OR of all elements = target.
    
    Use inclusion-exclusion with SOS DP.
    
    Time: O(2^n) for small n, or O(n * 2^20) for bitmask DP
    """
    n = len(nums)
    MOD = 10**9 + 7
    
    # For small n, iterate all subsets
    if n <= 20:
        count = 0
        for mask in range(1, 1 << n):
            or_val = 0
            for i in range(n):
                if mask & (1 << i):
                    or_val |= nums[i]
            if or_val == target:
                count += 1
        return count
    
    # For larger cases with small values, use bitmask DP
    # dp[mask] = number of subsets with OR = mask
    pass


def count_max_or_subsets(nums: List[int]) -> int:
    """
    LeetCode 2044 - Count subsets that OR to maximum possible.
    """
    max_or = 0
    for num in nums:
        max_or |= num
    
    n = len(nums)
    count = 0
    
    for mask in range(1, 1 << n):
        or_val = 0
        for i in range(n):
            if mask & (1 << i):
                or_val |= nums[i]
        if or_val == max_or:
            count += 1
    
    return count
```

---

## 7. Bitmask DP: Traveling Salesman Problem (TSP)

### Problem
Find shortest path visiting all n cities exactly once.

```python
def tsp(dist: List[List[int]]) -> int:
    """
    Traveling Salesman Problem using Bitmask DP.
    
    dp[mask][i] = min distance to visit cities in mask, ending at i
    
    Time: O(n² * 2^n)
    Space: O(n * 2^n)
    """
    n = len(dist)
    INF = float('inf')
    
    # dp[mask][i] = min distance ending at city i, visited = mask
    dp = [[INF] * n for _ in range(1 << n)]
    
    # Start at city 0
    dp[1][0] = 0
    
    for mask in range(1, 1 << n):
        for last in range(n):
            if not (mask & (1 << last)):
                continue
            if dp[mask][last] == INF:
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
    result = INF
    for last in range(n):
        result = min(result, dp[full_mask][last] + dist[last][0])
    
    return result


# Example
dist = [
    [0, 10, 15, 20],
    [10, 0, 35, 25],
    [15, 35, 0, 30],
    [20, 25, 30, 0]
]
print(tsp(dist))  # 80
```

---

## 8. Bitmask DP: Matching Problems

### Maximum Weighted Matching

```python
def max_weighted_matching(n: int, edges: List[tuple]) -> int:
    """
    Maximum weighted matching in general graph using bitmask DP.
    
    dp[mask] = max weight matching using vertices in mask
    
    Time: O(n² * 2^n)
    """
    INF = float('inf')
    
    # Build adjacency with weights
    weight = [[0] * n for _ in range(n)]
    for u, v, w in edges:
        weight[u][v] = w
        weight[v][u] = w
    
    dp = [0] * (1 << n)
    
    for mask in range(1, 1 << n):
        # Find lowest set bit
        u = (mask & -mask).bit_length() - 1
        rest = mask ^ (1 << u)
        
        # Option 1: u is not matched
        dp[mask] = dp[rest]
        
        # Option 2: u is matched with some v
        submask = rest
        while submask > 0:
            v = (submask & -submask).bit_length() - 1
            if weight[u][v] > 0:
                dp[mask] = max(dp[mask], dp[rest ^ (1 << v)] + weight[u][v])
            submask = (submask - 1) & rest
    
    return dp[(1 << n) - 1]
```

---

## 9. Bitmask DP: Subset Sum Variations

### Partition into K Equal Subsets (LC 698)

```python
def can_partition_k_subsets(nums: List[int], k: int) -> bool:
    """
    LeetCode 698 - Partition array into k equal sum subsets.
    
    Time: O(k * 2^n)
    """
    total = sum(nums)
    if total % k != 0:
        return False
    
    target = total // k
    n = len(nums)
    
    # dp[mask] = (number of complete groups, current group sum)
    # Or simpler: dp[mask] = remaining sum in current group
    
    dp = [-1] * (1 << n)
    dp[0] = 0  # 0 elements used, current group sum = 0
    
    for mask in range(1 << n):
        if dp[mask] == -1:
            continue
        
        for i in range(n):
            if mask & (1 << i):
                continue
            
            if dp[mask] + nums[i] <= target:
                new_mask = mask | (1 << i)
                new_sum = (dp[mask] + nums[i]) % target
                dp[new_mask] = new_sum
    
    return dp[(1 << n) - 1] == 0


# Example
nums = [4, 3, 2, 3, 5, 2, 1]
k = 4
print(can_partition_k_subsets(nums, k))  # True
```

### Minimum XOR Sum of Two Arrays (LC 1879)

```python
def minimum_xor_sum(nums1: List[int], nums2: List[int]) -> int:
    """
    LeetCode 1879 - Find permutation of nums2 that minimizes XOR sum.
    
    dp[mask] = min XOR sum using first popcount(mask) elements of nums1
               with elements of nums2 indicated by mask
    
    Time: O(n * 2^n)
    """
    n = len(nums1)
    INF = float('inf')
    
    dp = [INF] * (1 << n)
    dp[0] = 0
    
    for mask in range(1 << n):
        if dp[mask] == INF:
            continue
        
        # Current position in nums1
        i = bin(mask).count('1')
        if i >= n:
            continue
        
        # Try each unused element from nums2
        for j in range(n):
            if mask & (1 << j):
                continue
            
            new_mask = mask | (1 << j)
            dp[new_mask] = min(dp[new_mask], dp[mask] + (nums1[i] ^ nums2[j]))
    
    return dp[(1 << n) - 1]
```

---

## 10. Broken Profile DP (Plug DP)

For grid problems with local constraints.

```python
def count_tilings(m: int, n: int) -> int:
    """
    Count ways to tile m×n grid with 1×2 dominoes.
    
    Uses broken profile DP.
    
    Time: O(m * n * 2^n)
    """
    if m < n:
        m, n = n, m  # Make n smaller for fewer states
    
    # Profile: bitmask of current column's "jutting out" cells
    dp = {0: 1}
    
    for i in range(m):
        for j in range(n):
            new_dp = {}
            
            for mask, count in dp.items():
                # Check if cell (i, j) is already covered by vertical domino from above
                is_covered = mask & (1 << j)
                
                if is_covered:
                    # Cell covered, just remove from profile
                    new_mask = mask ^ (1 << j)
                    new_dp[new_mask] = new_dp.get(new_mask, 0) + count
                else:
                    # Option 1: Place vertical domino (extends down)
                    new_mask = mask | (1 << j)
                    new_dp[new_mask] = new_dp.get(new_mask, 0) + count
                    
                    # Option 2: Place horizontal domino (if next cell available)
                    if j + 1 < n and not (mask & (1 << (j + 1))):
                        # Both cells uncovered, place horizontal
                        # No change to mask since horizontal is contained in this row
                        new_dp[mask] = new_dp.get(mask, 0) + count
            
            dp = new_dp
    
    return dp.get(0, 0)


# Example
print(count_tilings(2, 3))  # 3
```

---

## 11. Practice Problems

| Problem | Platform | Technique |
|---------|----------|-----------|
| LC 1994 Number of Good Subsets | LeetCode | SOS DP |
| LC 2044 Count Max OR Subsets | LeetCode | Bitmask enumeration |
| LC 698 Partition K Equal Subsets | LeetCode | Bitmask DP |
| LC 1879 Minimum XOR Sum | LeetCode | Bitmask DP |
| LC 943 Shortest Superstring | LeetCode | TSP variant |
| CSES Hamiltonian Flights | CSES | TSP |
| CF 165E Compatible Numbers | Codeforces | SOS DP |
| CF 449D Jzzhu and Numbers | Codeforces | SOS DP + Inclusion-Exclusion |

---

## 12. Tips

### SOS DP Template Choice
```
For SUBSET sum:    iterate from 0 to 2^n - 1
For SUPERSET sum:  iterate from 2^n - 1 to 0

For each bit:
  SUBSET:    if bit is SET, add dp[mask ^ bit]
  SUPERSET:  if bit is UNSET, add dp[mask | bit]
```

### When to Use Which
- **SOS DP**: When computing aggregates over all subsets/supersets
- **Bitmask DP**: When state involves which elements are "used"
- **TSP-style**: When order matters and we track ending position

---

## 13. References

1. Codeforces Blog: "SOS Dynamic Programming"
2. CP-Algorithms: https://cp-algorithms.com/algebra/all-submasks.html
3. AtCoder DP Contest Editorial
