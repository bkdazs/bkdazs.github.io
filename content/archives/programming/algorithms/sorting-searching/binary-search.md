---
title: "Binary Search"
weight: 2
---

# Binary Search

---
title: "Binary Search"
topic: "Binary Search Patterns and Variations"
difficulty: "Easy to Hard"
tags: ["binary-search", "search", "divide-conquer", "sorted-array"]
status: "complete"
---

## Summary / TL;DR

Binary Search is a O(log n) algorithm for searching in sorted arrays. Master three templates to handle all variations.

**Key Insight:** Binary search can find not just exact matches, but boundaries, insertion points, and optimal values in monotonic functions.

---

## When to Use

- **Sorted array search:** Find element or insertion point
- **Monotonic property:** Find first/last True in boolean array
- **Answer space search:** Minimize/maximize value satisfying constraint
- **Rotated arrays:** Modified binary search
- **2D sorted matrices:** Search in row/column sorted matrix

### Pattern Recognition

| Clue | Template |
|------|----------|
| "Find exact element" | Basic binary search |
| "Find first/last" | Lower/upper bound |
| "Minimum value such that..." | Binary search on answer |
| "Maximum speed/capacity" | Binary search on answer |
| "Sorted and rotated" | Modified pivot finding |

---

## Big-O Complexity

| Operation | Time | Space |
|-----------|------|-------|
| Basic search | O(log n) | O(1) |
| Lower/upper bound | O(log n) | O(1) |
| Search on answer | O(log(range) × check) | O(1) |

---

## Three Templates

### Template 1: Basic Binary Search (Find Exact)

```python
from typing import List

def binary_search(nums: List[int], target: int) -> int:
    """
    Find index of target, or -1 if not found.
    
    Use when: Looking for exact match.
    Loop invariant: If target exists, it's in [left, right].
    """
    left, right = 0, len(nums) - 1
    
    while left <= right:  # Note: <=
        mid = left + (right - left) // 2
        
        if nums[mid] == target:
            return mid
        elif nums[mid] < target:
            left = mid + 1
        else:
            right = mid - 1
    
    return -1
```

### Template 2: Lower Bound (Find First ≥ target)

```python
def lower_bound(nums: List[int], target: int) -> int:
    """
    Find first index where nums[i] >= target.
    Returns len(nums) if all elements < target.
    
    Use when: Find insertion point, first occurrence.
    Loop invariant: Answer is in [left, right].
    """
    left, right = 0, len(nums)  # Note: right = len(nums)
    
    while left < right:  # Note: <
        mid = left + (right - left) // 2
        
        if nums[mid] < target:
            left = mid + 1
        else:
            right = mid  # Note: not mid - 1
    
    return left


def upper_bound(nums: List[int], target: int) -> int:
    """
    Find first index where nums[i] > target.
    Returns len(nums) if all elements <= target.
    """
    left, right = 0, len(nums)
    
    while left < right:
        mid = left + (right - left) // 2
        
        if nums[mid] <= target:  # Note: <=
            left = mid + 1
        else:
            right = mid
    
    return left


def find_first_occurrence(nums: List[int], target: int) -> int:
    """Find first occurrence of target."""
    idx = lower_bound(nums, target)
    if idx < len(nums) and nums[idx] == target:
        return idx
    return -1


def find_last_occurrence(nums: List[int], target: int) -> int:
    """Find last occurrence of target."""
    idx = upper_bound(nums, target) - 1
    if idx >= 0 and nums[idx] == target:
        return idx
    return -1


def count_occurrences(nums: List[int], target: int) -> int:
    """Count occurrences of target."""
    return upper_bound(nums, target) - lower_bound(nums, target)
```

### Template 3: Binary Search on Answer

```python
def binary_search_on_answer(low: int, high: int, is_valid) -> int:
    """
    Find minimum value where is_valid returns True.
    Assumes: is_valid is monotonic (F,F,...,F,T,T,...,T)
    
    Use when: Find minimum/maximum value satisfying constraint.
    """
    while low < high:
        mid = low + (high - low) // 2
        
        if is_valid(mid):
            high = mid      # mid might be answer, search lower
        else:
            low = mid + 1   # mid not valid, search higher
    
    return low


def binary_search_max_valid(low: int, high: int, is_valid) -> int:
    """
    Find maximum value where is_valid returns True.
    Assumes: is_valid is monotonic (T,T,...,T,F,F,...,F)
    """
    while low < high:
        mid = low + (high - low + 1) // 2  # Note: +1 to avoid infinite loop
        
        if is_valid(mid):
            low = mid       # mid is valid, search higher
        else:
            high = mid - 1  # mid not valid, search lower
    
    return low
```

---

## Common Patterns

### Pattern 1: Search in Rotated Sorted Array

```python
def search_rotated(nums: List[int], target: int) -> int:
    """
    Search in rotated sorted array (no duplicates).
    Example: [4,5,6,7,0,1,2], target=0 -> 4
    
    Time: O(log n)
    """
    left, right = 0, len(nums) - 1
    
    while left <= right:
        mid = left + (right - left) // 2
        
        if nums[mid] == target:
            return mid
        
        # Determine which half is sorted
        if nums[left] <= nums[mid]:  # Left half is sorted
            if nums[left] <= target < nums[mid]:
                right = mid - 1
            else:
                left = mid + 1
        else:  # Right half is sorted
            if nums[mid] < target <= nums[right]:
                left = mid + 1
            else:
                right = mid - 1
    
    return -1


def search_rotated_with_duplicates(nums: List[int], target: int) -> bool:
    """
    Search in rotated sorted array with duplicates.
    
    Time: O(log n) average, O(n) worst
    """
    left, right = 0, len(nums) - 1
    
    while left <= right:
        mid = left + (right - left) // 2
        
        if nums[mid] == target:
            return True
        
        # Handle duplicates: can't determine sorted half
        if nums[left] == nums[mid] == nums[right]:
            left += 1
            right -= 1
        elif nums[left] <= nums[mid]:  # Left half sorted
            if nums[left] <= target < nums[mid]:
                right = mid - 1
            else:
                left = mid + 1
        else:  # Right half sorted
            if nums[mid] < target <= nums[right]:
                left = mid + 1
            else:
                right = mid - 1
    
    return False


def find_minimum_rotated(nums: List[int]) -> int:
    """
    Find minimum in rotated sorted array.
    
    Time: O(log n)
    """
    left, right = 0, len(nums) - 1
    
    while left < right:
        mid = left + (right - left) // 2
        
        if nums[mid] > nums[right]:
            left = mid + 1  # Min is in right half
        else:
            right = mid     # Mid could be min
    
    return nums[left]
```

### Pattern 2: Find Peak Element

```python
def find_peak_element(nums: List[int]) -> int:
    """
    Find a peak element (greater than neighbors).
    nums[-1] = nums[n] = -∞
    
    Time: O(log n)
    """
    left, right = 0, len(nums) - 1
    
    while left < right:
        mid = left + (right - left) // 2
        
        if nums[mid] < nums[mid + 1]:
            left = mid + 1  # Peak is on the right
        else:
            right = mid     # Mid could be peak
    
    return left


def find_peak_in_mountain_array(arr: List[int]) -> int:
    """
    Mountain array: strictly increasing then strictly decreasing.
    Find the peak index.
    """
    left, right = 0, len(arr) - 1
    
    while left < right:
        mid = left + (right - left) // 2
        
        if arr[mid] < arr[mid + 1]:
            left = mid + 1
        else:
            right = mid
    
    return left
```

### Pattern 3: Binary Search on Answer

```python
def koko_eating_bananas(piles: List[int], h: int) -> int:
    """
    Minimum eating speed to finish all piles in h hours.
    
    Binary search on answer: find minimum k where hours(k) <= h.
    
    Time: O(n * log(max(piles)))
    """
    def can_finish(k: int) -> bool:
        hours = sum((pile + k - 1) // k for pile in piles)
        return hours <= h
    
    left, right = 1, max(piles)
    
    while left < right:
        mid = left + (right - left) // 2
        
        if can_finish(mid):
            right = mid
        else:
            left = mid + 1
    
    return left


def minimum_days_to_make_bouquets(bloom_day: List[int], m: int, k: int) -> int:
    """
    Minimum days to make m bouquets, each needs k adjacent flowers.
    
    Binary search on days.
    
    Time: O(n * log(max(bloom_day)))
    """
    if m * k > len(bloom_day):
        return -1
    
    def can_make(day: int) -> bool:
        bouquets = 0
        adjacent = 0
        for bloom in bloom_day:
            if bloom <= day:
                adjacent += 1
                if adjacent == k:
                    bouquets += 1
                    adjacent = 0
            else:
                adjacent = 0
        return bouquets >= m
    
    left, right = min(bloom_day), max(bloom_day)
    
    while left < right:
        mid = left + (right - left) // 2
        
        if can_make(mid):
            right = mid
        else:
            left = mid + 1
    
    return left


def ship_within_days(weights: List[int], days: int) -> int:
    """
    Minimum ship capacity to ship all packages within days.
    
    Time: O(n * log(sum(weights)))
    """
    def can_ship(capacity: int) -> bool:
        day_count = 1
        current_load = 0
        for w in weights:
            if current_load + w > capacity:
                day_count += 1
                current_load = 0
            current_load += w
        return day_count <= days
    
    left = max(weights)       # Must carry heaviest package
    right = sum(weights)      # Ship all in one day
    
    while left < right:
        mid = left + (right - left) // 2
        
        if can_ship(mid):
            right = mid
        else:
            left = mid + 1
    
    return left


def split_array_largest_sum(nums: List[int], k: int) -> int:
    """
    Split array into k subarrays minimizing maximum subarray sum.
    
    Binary search on the maximum sum.
    
    Time: O(n * log(sum(nums)))
    """
    def can_split(max_sum: int) -> bool:
        count = 1
        current_sum = 0
        for num in nums:
            if current_sum + num > max_sum:
                count += 1
                current_sum = 0
            current_sum += num
        return count <= k
    
    left = max(nums)
    right = sum(nums)
    
    while left < right:
        mid = left + (right - left) // 2
        
        if can_split(mid):
            right = mid
        else:
            left = mid + 1
    
    return left
```

### Pattern 4: Search in 2D Matrix

```python
def search_matrix(matrix: List[List[int]], target: int) -> bool:
    """
    Search in matrix where each row is sorted and 
    first element of each row > last element of previous row.
    
    Treat as 1D array.
    Time: O(log(m*n))
    """
    if not matrix or not matrix[0]:
        return False
    
    m, n = len(matrix), len(matrix[0])
    left, right = 0, m * n - 1
    
    while left <= right:
        mid = left + (right - left) // 2
        value = matrix[mid // n][mid % n]
        
        if value == target:
            return True
        elif value < target:
            left = mid + 1
        else:
            right = mid - 1
    
    return False


def search_matrix_2(matrix: List[List[int]], target: int) -> bool:
    """
    Search in matrix where rows and columns are sorted.
    Start from top-right corner.
    
    Time: O(m + n)
    """
    if not matrix or not matrix[0]:
        return False
    
    m, n = len(matrix), len(matrix[0])
    row, col = 0, n - 1
    
    while row < m and col >= 0:
        if matrix[row][col] == target:
            return True
        elif matrix[row][col] < target:
            row += 1
        else:
            col -= 1
    
    return False
```

### Pattern 5: Find Kth Element

```python
def find_kth_smallest(matrix: List[List[int]], k: int) -> int:
    """
    Find kth smallest element in sorted matrix.
    Rows and columns are sorted.
    
    Binary search on value + count elements <= mid.
    
    Time: O(n * log(max - min))
    """
    n = len(matrix)
    
    def count_less_equal(val: int) -> int:
        count = 0
        row, col = n - 1, 0
        while row >= 0 and col < n:
            if matrix[row][col] <= val:
                count += row + 1
                col += 1
            else:
                row -= 1
        return count
    
    left, right = matrix[0][0], matrix[n-1][n-1]
    
    while left < right:
        mid = left + (right - left) // 2
        
        if count_less_equal(mid) < k:
            left = mid + 1
        else:
            right = mid
    
    return left


def find_median_of_two_sorted_arrays(nums1: List[int], nums2: List[int]) -> float:
    """
    Find median of two sorted arrays.
    
    Binary search on partition point.
    
    Time: O(log(min(m, n)))
    """
    # Ensure nums1 is shorter
    if len(nums1) > len(nums2):
        nums1, nums2 = nums2, nums1
    
    m, n = len(nums1), len(nums2)
    half = (m + n + 1) // 2
    
    left, right = 0, m
    
    while left <= right:
        i = left + (right - left) // 2
        j = half - i
        
        left1 = nums1[i - 1] if i > 0 else float('-inf')
        right1 = nums1[i] if i < m else float('inf')
        left2 = nums2[j - 1] if j > 0 else float('-inf')
        right2 = nums2[j] if j < n else float('inf')
        
        if left1 <= right2 and left2 <= right1:
            if (m + n) % 2 == 0:
                return (max(left1, left2) + min(right1, right2)) / 2
            return max(left1, left2)
        elif left1 > right2:
            right = i - 1
        else:
            left = i + 1
    
    return 0.0
```

### Pattern 6: Float Binary Search

```python
def sqrt_float(x: float, precision: float = 1e-10) -> float:
    """
    Find square root using binary search on floats.
    """
    if x < 0:
        raise ValueError("Cannot compute sqrt of negative number")
    if x < 1:
        left, right = x, 1
    else:
        left, right = 1, x
    
    while right - left > precision:
        mid = (left + right) / 2
        if mid * mid < x:
            left = mid
        else:
            right = mid
    
    return (left + right) / 2


def minimize_max_distance(stations: List[int], k: int) -> float:
    """
    Add k gas stations to minimize max distance between adjacent stations.
    
    Binary search on the max distance.
    
    Time: O(n * log(max_gap / precision))
    """
    def can_achieve(max_dist: float) -> bool:
        needed = 0
        for i in range(1, len(stations)):
            gap = stations[i] - stations[i - 1]
            # Need ceil(gap / max_dist) - 1 stations
            needed += int(gap / max_dist)
        return needed <= k
    
    left = 0
    right = max(stations[i] - stations[i-1] for i in range(1, len(stations)))
    
    while right - left > 1e-6:
        mid = (left + right) / 2
        if can_achieve(mid):
            right = mid
        else:
            left = mid
    
    return right
```

---

## Python's bisect Module

```python
import bisect

# bisect_left: index of first element >= target
idx = bisect.bisect_left([1, 2, 4, 4, 5], 4)   # 2

# bisect_right: index of first element > target
idx = bisect.bisect_right([1, 2, 4, 4, 5], 4)  # 4

# insort_left: insert maintaining sort order
arr = [1, 2, 4, 5]
bisect.insort_left(arr, 3)  # arr = [1, 2, 3, 4, 5]

# Custom key (Python 3.10+)
bisect.bisect_left(arr, x, key=lambda a: a[0])
```

---

## Edge Cases & Gotchas

```python
# 1. Empty array
if not nums:
    return -1

# 2. Integer overflow in mid calculation
mid = left + (right - left) // 2  # NOT (left + right) // 2

# 3. Off-by-one in bounds
# Template 1: left <= right, left/right = mid ± 1
# Template 2: left < right, right = mid (for lower bound)

# 4. Choosing template
# Exact match: Template 1 (left <= right)
# Find boundary: Template 2 (left < right)

# 5. Finding max valid (avoid infinite loop)
mid = left + (right - left + 1) // 2  # +1 for max search

# 6. Float precision
while right - left > 1e-9:  # Use precision threshold

# 7. Search space definition
# Always verify: is left/right inclusive or exclusive?
```

---

## Interview Tips

### Framework

1. **Define search space:** What range are we searching?
2. **Define condition:** What makes a value valid?
3. **Choose template:** Exact match or boundary?
4. **Handle edges:** Empty array, single element

### Key Phrases

- "I'll use binary search since the array is sorted."
- "This is a binary search on answer problem - I'm searching for the minimum/maximum value satisfying the constraint."
- "I'll use lower_bound to find the first position >= target."
- "The search space is [min, max] and I'll check if each mid value works."

---

## Practice Problems

### Basic Binary Search

| # | Problem | Key Concept |
|---|---------|-------------|
| 1 | [Binary Search](https://leetcode.com/problems/binary-search/) | Basic template |
| 2 | [Search Insert Position](https://leetcode.com/problems/search-insert-position/) | Lower bound |
| 3 | [First Bad Version](https://leetcode.com/problems/first-bad-version/) | Find first True |
| 4 | [Sqrt(x)](https://leetcode.com/problems/sqrtx/) | Integer sqrt |
| 5 | [Find First and Last Position](https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array/) | Lower/upper bound |

### Rotated Arrays

| # | Problem | Key Concept |
|---|---------|-------------|
| 6 | [Search in Rotated Sorted Array](https://leetcode.com/problems/search-in-rotated-sorted-array/) | Modified search |
| 7 | [Search in Rotated Sorted Array II](https://leetcode.com/problems/search-in-rotated-sorted-array-ii/) | With duplicates |
| 8 | [Find Minimum in Rotated Sorted Array](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/) | Find pivot |
| 9 | [Find Minimum in Rotated Sorted Array II](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array-ii/) | With duplicates |

### Peak Finding

| # | Problem | Key Concept |
|---|---------|-------------|
| 10 | [Find Peak Element](https://leetcode.com/problems/find-peak-element/) | Local maximum |
| 11 | [Peak Index in a Mountain Array](https://leetcode.com/problems/peak-index-in-a-mountain-array/) | Mountain peak |
| 12 | [Find in Mountain Array](https://leetcode.com/problems/find-in-mountain-array/) | Search after peak |

### Binary Search on Answer

| # | Problem | Key Concept |
|---|---------|-------------|
| 13 | [Koko Eating Bananas](https://leetcode.com/problems/koko-eating-bananas/) | Minimum speed |
| 14 | [Capacity To Ship Packages](https://leetcode.com/problems/capacity-to-ship-packages-within-d-days/) | Minimum capacity |
| 15 | [Split Array Largest Sum](https://leetcode.com/problems/split-array-largest-sum/) | Minimize max |
| 16 | [Minimum Days for Bouquets](https://leetcode.com/problems/minimum-number-of-days-to-make-m-bouquets/) | Minimum days |
| 17 | [Magnetic Force Between Balls](https://leetcode.com/problems/magnetic-force-between-two-balls/) | Maximum minimum |

### 2D Search

| # | Problem | Key Concept |
|---|---------|-------------|
| 18 | [Search a 2D Matrix](https://leetcode.com/problems/search-a-2d-matrix/) | Flattened array |
| 19 | [Search a 2D Matrix II](https://leetcode.com/problems/search-a-2d-matrix-ii/) | Row/column sorted |
| 20 | [Kth Smallest Element in Sorted Matrix](https://leetcode.com/problems/kth-smallest-element-in-a-sorted-matrix/) | Count approach |

### Advanced

| # | Problem | Key Concept |
|---|---------|-------------|
| 21 | [Median of Two Sorted Arrays](https://leetcode.com/problems/median-of-two-sorted-arrays/) | Partition |
| 22 | [Find K-th Smallest Pair Distance](https://leetcode.com/problems/find-k-th-smallest-pair-distance/) | Count pairs |
| 23 | [Minimize Max Distance to Gas Station](https://leetcode.com/problems/minimize-max-distance-to-gas-station/) | Float search |
| 24 | [Russian Doll Envelopes](https://leetcode.com/problems/russian-doll-envelopes/) | LIS + binary search |

---

## References

- [Binary Search Tutorial](https://leetcode.com/explore/learn/card/binary-search/)
- [Binary Search Templates](https://leetcode.com/discuss/general-discussion/786126/)
- CLRS: Chapter 12 (Binary Search Trees)
