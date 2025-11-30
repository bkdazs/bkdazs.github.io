---
title: "Binary Search on Answer"
topic: "Problem Patterns"
difficulty: "Medium-Hard"
tags: ["binary-search", "optimization", "monotonic", "minimax"]
status: "complete"
weight: 7
---

# Binary Search on Answer

## 📚 Summary

Binary Search on Answer is used when the answer has a monotonic property - if condition(x) is true, then condition(y) is also true for all y >= x (or y <= x). Instead of searching in the input, we binary search on the answer space.

---

## 1️⃣ Core Pattern

```python
"""
Binary Search on Answer Template:

1. Identify: Answer lies in range [lo, hi]
2. Monotonic: If answer works, all values in one direction also work
3. Check function: Can we achieve this answer?

def binary_search_answer(lo, hi):
    while lo < hi:
        mid = (lo + hi) // 2  # or (lo + hi + 1) // 2
        if is_feasible(mid):
            hi = mid  # or lo = mid
        else:
            lo = mid + 1  # or hi = mid - 1
    return lo

Key insight: Transform optimization to decision problem
"What is the minimum X?" → "Can we achieve X?"
"""
```

---

## 2️⃣ Minimize Maximum (Minimax)

### Split Array Largest Sum (LC 410)

```python
def split_array(nums: list[int], k: int) -> int:
    """
    Split into k subarrays to minimize largest sum
    
    Binary search on the answer (largest sum)
    Range: [max(nums), sum(nums)]
    """
    def can_split(max_sum: int) -> bool:
        """Can we split into <= k subarrays each with sum <= max_sum?"""
        count = 1
        current_sum = 0
        
        for num in nums:
            if current_sum + num > max_sum:
                count += 1
                current_sum = num
                if count > k:
                    return False
            else:
                current_sum += num
        
        return True
    
    lo = max(nums)       # Minimum possible answer
    hi = sum(nums)       # Maximum possible answer
    
    while lo < hi:
        mid = (lo + hi) // 2
        if can_split(mid):
            hi = mid      # Try smaller
        else:
            lo = mid + 1  # Need larger
    
    return lo
```

### Capacity to Ship Packages (LC 1011)

```python
def ship_within_days(weights: list[int], days: int) -> int:
    """
    Minimum ship capacity to deliver all packages in 'days' days
    """
    def can_ship(capacity: int) -> bool:
        day_count = 1
        current_weight = 0
        
        for weight in weights:
            if current_weight + weight > capacity:
                day_count += 1
                current_weight = weight
            else:
                current_weight += weight
        
        return day_count <= days
    
    lo = max(weights)       # Must fit largest package
    hi = sum(weights)       # Ship everything in one day
    
    while lo < hi:
        mid = (lo + hi) // 2
        if can_ship(mid):
            hi = mid
        else:
            lo = mid + 1
    
    return lo
```

### Minimize Maximum Distance to Gas Station (LC 774)

```python
def min_max_gas_dist(stations: list[int], k: int) -> float:
    """
    Add k gas stations to minimize maximum distance between adjacent stations
    """
    def count_stations_needed(max_dist: float) -> int:
        """How many stations needed to achieve max_dist?"""
        count = 0
        for i in range(len(stations) - 1):
            gap = stations[i + 1] - stations[i]
            count += int(gap / max_dist)  # Stations to add in this gap
        return count
    
    lo = 0
    hi = stations[-1] - stations[0]
    
    # Binary search on float
    while hi - lo > 1e-6:
        mid = (lo + hi) / 2
        if count_stations_needed(mid) <= k:
            hi = mid
        else:
            lo = mid
    
    return hi
```

---

## 3️⃣ Maximize Minimum

### Aggressive Cows / Magnetic Force (LC 1552)

```python
def max_distance(position: list[int], m: int) -> int:
    """
    Place m balls to maximize minimum distance between any two
    """
    position.sort()
    
    def can_place(min_dist: int) -> bool:
        """Can we place m balls with at least min_dist between them?"""
        count = 1
        last_pos = position[0]
        
        for pos in position[1:]:
            if pos - last_pos >= min_dist:
                count += 1
                last_pos = pos
                if count == m:
                    return True
        
        return False
    
    lo = 1
    hi = position[-1] - position[0]
    
    while lo < hi:
        mid = (lo + hi + 1) // 2  # Upper mid for maximize
        if can_place(mid):
            lo = mid
        else:
            hi = mid - 1
    
    return lo
```

### Maximize Sweetness of Chocolate (LC 1231)

```python
def maximize_sweetness(sweetness: list[int], k: int) -> int:
    """
    Cut chocolate into k+1 pieces to maximize minimum sweetness
    """
    def can_cut(min_sweet: int) -> bool:
        pieces = 0
        current = 0
        
        for s in sweetness:
            current += s
            if current >= min_sweet:
                pieces += 1
                current = 0
        
        return pieces >= k + 1
    
    lo = min(sweetness)
    hi = sum(sweetness) // (k + 1)
    
    while lo < hi:
        mid = (lo + hi + 1) // 2
        if can_cut(mid):
            lo = mid
        else:
            hi = mid - 1
    
    return lo
```

---

## 4️⃣ Kth Element Problems

### Kth Smallest Element in Sorted Matrix (LC 378)

```python
def kth_smallest(matrix: list[list[int]], k: int) -> int:
    """
    Find kth smallest in row-wise and column-wise sorted matrix
    """
    n = len(matrix)
    
    def count_less_equal(target: int) -> int:
        """Count elements <= target"""
        count = 0
        row, col = n - 1, 0
        
        while row >= 0 and col < n:
            if matrix[row][col] <= target:
                count += row + 1  # All elements in this column up to row
                col += 1
            else:
                row -= 1
        
        return count
    
    lo = matrix[0][0]
    hi = matrix[n - 1][n - 1]
    
    while lo < hi:
        mid = (lo + hi) // 2
        if count_less_equal(mid) >= k:
            hi = mid
        else:
            lo = mid + 1
    
    return lo
```

### Kth Smallest Pair Distance (LC 719)

```python
def smallest_distance_pair(nums: list[int], k: int) -> int:
    """
    Find kth smallest distance among all pairs
    """
    nums.sort()
    n = len(nums)
    
    def count_pairs(max_dist: int) -> int:
        """Count pairs with distance <= max_dist"""
        count = 0
        left = 0
        
        for right in range(n):
            while nums[right] - nums[left] > max_dist:
                left += 1
            count += right - left
        
        return count
    
    lo = 0
    hi = nums[-1] - nums[0]
    
    while lo < hi:
        mid = (lo + hi) // 2
        if count_pairs(mid) >= k:
            hi = mid
        else:
            lo = mid + 1
    
    return lo
```

### Kth Smallest Sum in Two Sorted Arrays

```python
def kth_smallest_sum(nums1: list[int], nums2: list[int], k: int) -> int:
    """
    Find kth smallest sum from pairs (one from each array)
    """
    m, n = len(nums1), len(nums2)
    
    def count_less_equal(target: int) -> int:
        count = 0
        j = n - 1
        
        for i in range(m):
            while j >= 0 and nums1[i] + nums2[j] > target:
                j -= 1
            count += j + 1
        
        return count
    
    lo = nums1[0] + nums2[0]
    hi = nums1[-1] + nums2[-1]
    
    while lo < hi:
        mid = (lo + hi) // 2
        if count_less_equal(mid) >= k:
            hi = mid
        else:
            lo = mid + 1
    
    return lo
```

---

## 5️⃣ Time/Speed Problems

### Koko Eating Bananas (LC 875)

```python
def min_eating_speed(piles: list[int], h: int) -> int:
    """
    Minimum speed k to eat all bananas in h hours
    """
    import math
    
    def hours_needed(k: int) -> int:
        return sum(math.ceil(pile / k) for pile in piles)
    
    lo = 1
    hi = max(piles)
    
    while lo < hi:
        mid = (lo + hi) // 2
        if hours_needed(mid) <= h:
            hi = mid
        else:
            lo = mid + 1
    
    return lo
```

### Minimum Time to Complete Trips (LC 2187)

```python
def minimum_time(time: list[int], total_trips: int) -> int:
    """
    Minimum time for buses to complete total_trips
    """
    def trips_completed(t: int) -> int:
        return sum(t // bus_time for bus_time in time)
    
    lo = 1
    hi = min(time) * total_trips
    
    while lo < hi:
        mid = (lo + hi) // 2
        if trips_completed(mid) >= total_trips:
            hi = mid
        else:
            lo = mid + 1
    
    return lo
```

---

## 6️⃣ Median Finding

### Median of Two Sorted Arrays (LC 4)

```python
def find_median_sorted_arrays(nums1: list[int], nums2: list[int]) -> float:
    """
    Find median of two sorted arrays in O(log(min(m,n)))
    """
    if len(nums1) > len(nums2):
        nums1, nums2 = nums2, nums1
    
    m, n = len(nums1), len(nums2)
    lo, hi = 0, m
    
    while lo <= hi:
        i = (lo + hi) // 2      # Partition in nums1
        j = (m + n + 1) // 2 - i  # Partition in nums2
        
        left1 = nums1[i - 1] if i > 0 else float('-inf')
        right1 = nums1[i] if i < m else float('inf')
        left2 = nums2[j - 1] if j > 0 else float('-inf')
        right2 = nums2[j] if j < n else float('inf')
        
        if left1 <= right2 and left2 <= right1:
            if (m + n) % 2 == 0:
                return (max(left1, left2) + min(right1, right2)) / 2
            return max(left1, left2)
        elif left1 > right2:
            hi = i - 1
        else:
            lo = i + 1
    
    raise ValueError("Arrays not sorted")
```

---

## 7️⃣ Allocation Problems

### Allocate Books (LC variant)

```python
def allocate_books(books: list[int], students: int) -> int:
    """
    Allocate books to students to minimize maximum pages
    Each student gets contiguous books
    """
    if len(books) < students:
        return -1
    
    def can_allocate(max_pages: int) -> bool:
        student_count = 1
        current_pages = 0
        
        for pages in books:
            if current_pages + pages > max_pages:
                student_count += 1
                current_pages = pages
                if student_count > students:
                    return False
            else:
                current_pages += pages
        
        return True
    
    lo = max(books)
    hi = sum(books)
    
    while lo < hi:
        mid = (lo + hi) // 2
        if can_allocate(mid):
            hi = mid
        else:
            lo = mid + 1
    
    return lo
```

### Painter's Partition

```python
def min_time_to_paint(boards: list[int], painters: int) -> int:
    """
    Minimum time to paint all boards with painters
    Each painter paints contiguous boards
    """
    def can_paint(max_time: int) -> bool:
        painter_count = 1
        current_time = 0
        
        for board in boards:
            if current_time + board > max_time:
                painter_count += 1
                current_time = board
            else:
                current_time += board
        
        return painter_count <= painters
    
    lo = max(boards)
    hi = sum(boards)
    
    while lo < hi:
        mid = (lo + hi) // 2
        if can_paint(mid):
            hi = mid
        else:
            lo = mid + 1
    
    return lo
```

---

## 8️⃣ Pattern Recognition

```python
"""
When to use Binary Search on Answer:

1. "Minimize the maximum" → Search on max value
2. "Maximize the minimum" → Search on min value  
3. "Kth smallest/largest" → Search on value, count <= or >=
4. "Minimum time/speed" → Search on time/speed
5. "Can we achieve X?" → Decision problem hints at binary search

Identify monotonic property:
- If we can achieve X, can we achieve X+1? (or X-1?)
- Answer space is bounded [lo, hi]
- There's a clear check function

Template selection:
- Minimize: while lo < hi, hi = mid
- Maximize: while lo < hi, lo = mid (use hi + 1) // 2)
"""
```

---

## ⏱️ Complexity

| Problem Type | Time | Space |
|-------------|------|-------|
| Split Array | O(n log S) | O(1) |
| Kth in Matrix | O(n log R) | O(1) |
| Kth Pair Distance | O(n log R) | O(1) |
| Eating Bananas | O(n log M) | O(1) |

Where S = sum, R = range, M = max element, n = array length

---

## 📚 Practice Problems

| # | Problem | Type |
|---|---------|------|
| 1 | [LC 410: Split Array Largest Sum](https://leetcode.com/problems/split-array-largest-sum/) | Minimax |
| 2 | [LC 1011: Ship Packages](https://leetcode.com/problems/capacity-to-ship-packages-within-d-days/) | Minimax |
| 3 | [LC 774: Min Max Gas Station](https://leetcode.com/problems/minimize-max-distance-to-gas-station/) | Minimax |
| 4 | [LC 1552: Magnetic Force](https://leetcode.com/problems/magnetic-force-between-two-balls/) | Maximin |
| 5 | [LC 1231: Divide Chocolate](https://leetcode.com/problems/divide-chocolate/) | Maximin |
| 6 | [LC 378: Kth in Sorted Matrix](https://leetcode.com/problems/kth-smallest-element-in-a-sorted-matrix/) | Kth element |
| 7 | [LC 719: Kth Pair Distance](https://leetcode.com/problems/find-k-th-smallest-pair-distance/) | Kth element |
| 8 | [LC 875: Koko Bananas](https://leetcode.com/problems/koko-eating-bananas/) | Time |
| 9 | [LC 2187: Min Time Trips](https://leetcode.com/problems/minimum-time-to-complete-trips/) | Time |
| 10 | [LC 4: Median Two Arrays](https://leetcode.com/problems/median-of-two-sorted-arrays/) | Median |
| 11 | [LC 1482: Min Days Bouquets](https://leetcode.com/problems/minimum-number-of-days-to-make-m-bouquets/) | Time |
| 12 | [LC 1283: Smallest Divisor](https://leetcode.com/problems/find-the-smallest-divisor-given-a-threshold/) | Minimax |
| 13 | [LC 2064: Min Capacity Distribute](https://leetcode.com/problems/minimized-maximum-of-products-distributed-to-any-store/) | Minimax |
| 14 | [LC 1870: Min Speed Arrive](https://leetcode.com/problems/minimum-speed-to-arrive-on-time/) | Speed |
| 15 | [LC 2226: Max Candies Allocated](https://leetcode.com/problems/maximum-candies-allocated-to-k-children/) | Maximin |

---

*Last Updated: 2024*
