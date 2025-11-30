---
title: "Meet in the Middle"
topic: "Problem Patterns"
difficulty: "Hard"
tags: ["meet-in-middle", "divide-conquer", "optimization", "subset-sum"]
status: "complete"
weight: 9
---

# Meet in the Middle

## 📚 Summary

Meet in the Middle splits a problem into two halves, solves each half independently, then combines results. Reduces O(2^n) to O(2^(n/2)) or O(n²) to O(n log n).

---

## 1️⃣ Core Pattern

```python
"""
Meet in the Middle Template:

1. Split input into two halves
2. Generate all possible states for each half
3. Store first half results in data structure (set, dict, sorted list)
4. For each second half state, find complementary first half state

Time complexity reduction: O(2^n) → O(2^(n/2) * log(2^(n/2))) = O(n * 2^(n/2))

Common use cases:
- Subset sum variants
- Finding pairs that satisfy condition
- Problems with n ≤ 40 (too big for O(2^n), ok for O(2^20))
"""
```

---

## 2️⃣ Subset Sum

### Count Subsets with Target Sum

```python
from collections import Counter

def count_subsets_with_sum(nums: list[int], target: int) -> int:
    """
    Count subsets that sum to target
    O(2^(n/2)) instead of O(2^n)
    """
    n = len(nums)
    mid = n // 2
    
    def generate_sums(arr: list[int]) -> list[int]:
        """Generate all possible subset sums"""
        sums = [0]
        for num in arr:
            sums = sums + [s + num for s in sums]
        return sums
    
    # Generate sums for both halves
    left_sums = generate_sums(nums[:mid])
    right_sums = generate_sums(nums[mid:])
    
    # Count occurrences in right half
    right_count = Counter(right_sums)
    
    # For each left sum, find complement in right
    count = 0
    for left_sum in left_sums:
        complement = target - left_sum
        count += right_count[complement]
    
    return count
```

### Closest Subset Sum

```python
import bisect

def closest_subset_sum(nums: list[int], target: int) -> int:
    """
    Find subset sum closest to target
    """
    n = len(nums)
    mid = n // 2
    
    def generate_sums(arr: list[int]) -> list[int]:
        sums = [0]
        for num in arr:
            sums = sums + [s + num for s in sums]
        return sums
    
    left_sums = generate_sums(nums[:mid])
    right_sums = sorted(generate_sums(nums[mid:]))
    
    closest = float('inf')
    best_sum = 0
    
    for left_sum in left_sums:
        complement = target - left_sum
        
        # Binary search for closest in right_sums
        idx = bisect.bisect_left(right_sums, complement)
        
        # Check idx and idx-1
        for i in [idx - 1, idx]:
            if 0 <= i < len(right_sums):
                total = left_sum + right_sums[i]
                if abs(total - target) < closest:
                    closest = abs(total - target)
                    best_sum = total
    
    return best_sum
```

### Partition Equal Subset Sum with Limited Elements

```python
def can_partition_mitm(nums: list[int]) -> bool:
    """
    Check if array can be partitioned into two equal sum subsets
    Meet in the middle approach for n up to 40
    """
    total = sum(nums)
    if total % 2 != 0:
        return False
    
    target = total // 2
    n = len(nums)
    mid = n // 2
    
    def generate_sums(arr: list[int]) -> set[int]:
        sums = {0}
        for num in arr:
            sums = sums | {s + num for s in sums}
        return sums
    
    left_sums = generate_sums(nums[:mid])
    right_sums = generate_sums(nums[mid:])
    
    # Check if any combination equals target
    for left_sum in left_sums:
        if (target - left_sum) in right_sums:
            return True
    
    return False
```

---

## 3️⃣ Sum of Four Squares (4Sum Variant)

```python
def four_sum_count(nums1: list[int], nums2: list[int], 
                   nums3: list[int], nums4: list[int]) -> int:
    """
    LC 454: Count tuples (i,j,k,l) where nums1[i]+nums2[j]+nums3[k]+nums4[l]=0
    """
    # First half: all sums of nums1 and nums2
    sum_ab = Counter()
    for a in nums1:
        for b in nums2:
            sum_ab[a + b] += 1
    
    # Second half: count complements
    count = 0
    for c in nums3:
        for d in nums4:
            count += sum_ab[-(c + d)]
    
    return count
```

---

## 4️⃣ Maximum XOR Subset

```python
def max_xor_subset(nums: list[int]) -> int:
    """
    Find two disjoint subsets A, B to maximize XOR(A) ^ XOR(B)
    """
    n = len(nums)
    mid = n // 2
    
    def generate_xors(arr: list[int]) -> set[int]:
        """Generate all possible XOR values"""
        xors = {0}
        for num in arr:
            xors = xors | {x ^ num for x in xors}
        return xors
    
    left_xors = generate_xors(nums[:mid])
    right_xors = generate_xors(nums[mid:])
    
    # For disjoint subsets, we need XOR of all elements
    # XOR(A) ^ XOR(B) = XOR(elements in A but not B) ^ XOR(elements in B but not A)
    # This equals XOR of symmetric difference
    
    max_xor = 0
    for left in left_xors:
        for right in right_xors:
            max_xor = max(max_xor, left ^ right)
    
    return max_xor
```

---

## 5️⃣ Number of Ways to Split Array

```python
def ways_to_split(nums: list[int], target: int) -> int:
    """
    Count ways to choose subset from first half and second half
    such that their XOR equals target
    """
    n = len(nums)
    mid = n // 2
    
    def generate_all(arr: list[int]) -> dict:
        """Generate all subsets with their values"""
        results = {0: 1}  # value -> count
        for num in arr:
            new_results = {}
            for val, cnt in results.items():
                new_val = val ^ num
                new_results[val] = new_results.get(val, 0) + cnt
                new_results[new_val] = new_results.get(new_val, 0) + cnt
            results = new_results
        return results
    
    left_xors = generate_all(nums[:mid])
    right_xors = generate_all(nums[mid:])
    
    count = 0
    for left_val, left_cnt in left_xors.items():
        complement = target ^ left_val
        if complement in right_xors:
            count += left_cnt * right_xors[complement]
    
    return count
```

---

## 6️⃣ Pair Sum Problems

### Closest Pair Sum to Target

```python
def closest_pair_sum(arr1: list[int], arr2: list[int], target: int) -> tuple[int, int]:
    """
    Find elements a from arr1, b from arr2 to minimize |a + b - target|
    O(n log n) instead of O(n²)
    """
    arr2_sorted = sorted(arr2)
    closest = float('inf')
    best_pair = (0, 0)
    
    for a in arr1:
        complement = target - a
        idx = bisect.bisect_left(arr2_sorted, complement)
        
        for i in [idx - 1, idx]:
            if 0 <= i < len(arr2_sorted):
                b = arr2_sorted[i]
                diff = abs(a + b - target)
                if diff < closest:
                    closest = diff
                    best_pair = (a, b)
    
    return best_pair
```

### Count Pairs with Sum in Range

```python
def count_pairs_in_range(nums: list[int], lo: int, hi: int) -> int:
    """
    Count pairs (i, j) where lo <= nums[i] + nums[j] <= hi
    """
    n = len(nums)
    mid = n // 2
    
    left = sorted(nums[:mid])
    right = sorted(nums[mid:])
    
    def count_less_equal(target: int) -> int:
        """Count pairs with sum <= target"""
        count = 0
        j = len(right) - 1
        
        for i, val in enumerate(left):
            while j >= 0 and val + right[j] > target:
                j -= 1
            count += j + 1
        
        return count
    
    return count_less_equal(hi) - count_less_equal(lo - 1)
```

---

## 7️⃣ Split Array with Equal Sum (LC 548)

```python
def split_array(nums: list[int]) -> bool:
    """
    Split array into 4 parts with equal sum by removing 3 elements
    nums[0..i-1], nums[i+1..j-1], nums[j+1..k-1], nums[k+1..n-1]
    """
    n = len(nums)
    if n < 7:
        return False
    
    prefix = [0] * (n + 1)
    for i in range(n):
        prefix[i + 1] = prefix[i] + nums[i]
    
    def range_sum(i: int, j: int) -> int:
        return prefix[j + 1] - prefix[i]
    
    # For each j (middle split), find valid i and k
    for j in range(3, n - 3):
        # Collect all valid first part sums for this j
        valid_sums = set()
        
        for i in range(1, j - 1):
            if range_sum(0, i - 1) == range_sum(i + 1, j - 1):
                valid_sums.add(range_sum(0, i - 1))
        
        # Check if any k produces matching sum
        for k in range(j + 2, n - 1):
            if range_sum(j + 1, k - 1) == range_sum(k + 1, n - 1):
                if range_sum(j + 1, k - 1) in valid_sums:
                    return True
    
    return False
```

---

## 8️⃣ Subset Sum with Exactly K Elements

```python
def subset_sum_k_elements(nums: list[int], k: int, target: int) -> bool:
    """
    Check if subset of exactly k elements sums to target
    """
    n = len(nums)
    mid = n // 2
    
    def generate_sums_with_count(arr: list[int]) -> dict[tuple[int, int], int]:
        """Generate (sum, count) pairs"""
        results = {(0, 0): 1}  # (sum, count) -> frequency
        
        for num in arr:
            new_results = dict(results)
            for (s, c), freq in results.items():
                new_key = (s + num, c + 1)
                new_results[new_key] = new_results.get(new_key, 0) + freq
            results = new_results
        
        return results
    
    left_sums = generate_sums_with_count(nums[:mid])
    right_sums = generate_sums_with_count(nums[mid:])
    
    for (left_sum, left_count), _ in left_sums.items():
        complement_sum = target - left_sum
        complement_count = k - left_count
        
        if (complement_sum, complement_count) in right_sums:
            return True
    
    return False
```

---

## 9️⃣ Application: Closest Sum to Zero

```python
def closest_to_zero(nums: list[int]) -> list[int]:
    """
    Find subset with sum closest to zero
    Return the subset
    """
    n = len(nums)
    mid = n // 2
    
    def generate_all_subsets(arr: list[int]) -> list[tuple[int, tuple]]:
        """Generate (sum, indices) for all subsets"""
        results = [(0, ())]
        
        for i, num in enumerate(arr):
            new_results = []
            for s, indices in results:
                new_results.append((s, indices))
                new_results.append((s + num, indices + (i,)))
            results = new_results
        
        return results
    
    left_subsets = generate_all_subsets(nums[:mid])
    right_subsets = sorted(generate_all_subsets(nums[mid:]))
    
    # Sort by sum for binary search
    right_sums = [s for s, _ in right_subsets]
    
    best_sum = float('inf')
    best_indices = ((), ())
    
    for left_sum, left_indices in left_subsets:
        # Find right subset closest to -left_sum
        target = -left_sum
        idx = bisect.bisect_left(right_sums, target)
        
        for i in [idx - 1, idx]:
            if 0 <= i < len(right_subsets):
                right_sum, right_indices = right_subsets[i]
                total = left_sum + right_sum
                
                if abs(total) < abs(best_sum):
                    best_sum = total
                    best_indices = (left_indices, right_indices)
    
    # Reconstruct result
    left_indices, right_indices = best_indices
    result = [nums[i] for i in left_indices]
    result += [nums[mid + i] for i in right_indices]
    
    return result
```

---

## 🔟 Template Code

```python
def meet_in_middle_template(nums: list[int], target: int):
    """
    Generic Meet in the Middle template
    """
    n = len(nums)
    mid = n // 2
    
    # Step 1: Generate all states for left half
    def generate_states(arr: list[int]) -> dict:
        states = {initial_state: initial_value}
        for element in arr:
            new_states = {}
            for state, value in states.items():
                # Keep original state
                new_states[state] = combine(new_states.get(state), value)
                # Add element to state
                new_state = update_state(state, element)
                new_value = update_value(value, element)
                new_states[new_state] = combine(new_states.get(new_state), new_value)
            states = new_states
        return states
    
    left_states = generate_states(nums[:mid])
    right_states = generate_states(nums[mid:])
    
    # Step 2: Find matching pairs
    result = initial_result
    for left_state, left_value in left_states.items():
        complement = get_complement(target, left_state)
        if complement in right_states:
            result = update_result(result, left_value, right_states[complement])
    
    return result
```

---

## ⏱️ Complexity

| Approach | Brute Force | Meet in Middle |
|----------|-------------|----------------|
| Subset Sum | O(2^n) | O(2^(n/2) * log) |
| All Pairs | O(n²) | O(n log n) |
| 4Sum Count | O(n⁴) | O(n²) |

Space: O(2^(n/2)) for storing half's states

---

## 📚 Practice Problems

| # | Problem | Key Concept |
|---|---------|-------------|
| 1 | [LC 454: 4Sum II](https://leetcode.com/problems/4sum-ii/) | Classic MITM |
| 2 | [LC 805: Split Array Same Average](https://leetcode.com/problems/split-array-with-same-average/) | Subset sum |
| 3 | [LC 1755: Closest Subsequence Sum](https://leetcode.com/problems/closest-subsequence-sum/) | Closest sum |
| 4 | [LC 2035: Partition Into Two Groups](https://leetcode.com/problems/partition-array-into-two-arrays-to-minimize-sum-difference/) | Min diff |
| 5 | [LC 548: Split Array Four Parts](https://leetcode.com/problems/split-array-with-equal-sum/) | Four parts |
| 6 | [LC 1074: Number of Submatrices Sum Target](https://leetcode.com/problems/number-of-submatrices-that-sum-to-target/) | 2D variant |
| 7 | [CF: MITM Set](https://codeforces.com/problemset?tags=meet-in-the-middle) | Various |
| 8 | Closest Subset Sum | Template |
| 9 | Max XOR Two Subsets | XOR variant |
| 10 | Subset Sum K Elements | Constrained |

---

*Last Updated: 2024*
