---
title: "Sorting Algorithms Overview"
weight: 1
---

# Sorting Algorithms Overview

---
title: "Sorting Algorithms Overview"
topic: "Comprehensive Sorting Techniques"
difficulty: "Easy to Hard"
tags: ["sorting", "quicksort", "mergesort", "heapsort", "comparison-sort"]
status: "complete"
---

## Summary / TL;DR

Sorting is fundamental to algorithm design. Know when to use which algorithm and understand the trade-offs.

| Algorithm | Time (Avg) | Time (Worst) | Space | Stable? | Use Case |
|-----------|-----------|--------------|-------|---------|----------|
| QuickSort | O(n log n) | O(n²) | O(log n) | No | General purpose |
| MergeSort | O(n log n) | O(n log n) | O(n) | Yes | Stable sort, linked lists |
| HeapSort | O(n log n) | O(n log n) | O(1) | No | In-place, guaranteed |
| TimSort | O(n log n) | O(n log n) | O(n) | Yes | Python/Java default |
| Counting | O(n + k) | O(n + k) | O(k) | Yes | Small integer range |
| Radix | O(d(n + k)) | O(d(n + k)) | O(n + k) | Yes | Fixed-width integers |

---

## When to Use

- **QuickSort:** General purpose, in-place needed
- **MergeSort:** Stability required, external sorting
- **HeapSort:** Guaranteed O(n log n), space constraint
- **Counting/Radix:** Known small range of values
- **Insertion Sort:** Small n or nearly sorted data
- **Bucket Sort:** Uniformly distributed data

---

## Core Implementations

### QuickSort

```python
from typing import List
import random

def quicksort(arr: List[int]) -> List[int]:
    """
    Classic QuickSort with random pivot.
    
    Time: O(n log n) average, O(n²) worst
    Space: O(log n) for recursion stack
    """
    def partition(low: int, high: int) -> int:
        # Random pivot for average case O(n log n)
        pivot_idx = random.randint(low, high)
        arr[pivot_idx], arr[high] = arr[high], arr[pivot_idx]
        pivot = arr[high]
        
        i = low - 1
        for j in range(low, high):
            if arr[j] <= pivot:
                i += 1
                arr[i], arr[j] = arr[j], arr[i]
        
        arr[i + 1], arr[high] = arr[high], arr[i + 1]
        return i + 1
    
    def quicksort_helper(low: int, high: int):
        if low < high:
            pi = partition(low, high)
            quicksort_helper(low, pi - 1)
            quicksort_helper(pi + 1, high)
    
    quicksort_helper(0, len(arr) - 1)
    return arr


def quicksort_3way(arr: List[int]) -> List[int]:
    """
    3-way QuickSort (Dutch National Flag).
    Efficient for arrays with many duplicates.
    
    Time: O(n log n) average
    """
    def sort_3way(low: int, high: int):
        if low >= high:
            return
        
        lt, gt = low, high
        pivot = arr[low]
        i = low + 1
        
        while i <= gt:
            if arr[i] < pivot:
                arr[lt], arr[i] = arr[i], arr[lt]
                lt += 1
                i += 1
            elif arr[i] > pivot:
                arr[gt], arr[i] = arr[i], arr[gt]
                gt -= 1
            else:
                i += 1
        
        # arr[low..lt-1] < pivot = arr[lt..gt] < arr[gt+1..high]
        sort_3way(low, lt - 1)
        sort_3way(gt + 1, high)
    
    sort_3way(0, len(arr) - 1)
    return arr
```

### MergeSort

```python
def mergesort(arr: List[int]) -> List[int]:
    """
    Classic MergeSort (stable).
    
    Time: O(n log n) always
    Space: O(n)
    """
    if len(arr) <= 1:
        return arr
    
    mid = len(arr) // 2
    left = mergesort(arr[:mid])
    right = mergesort(arr[mid:])
    
    return merge(left, right)


def merge(left: List[int], right: List[int]) -> List[int]:
    """Merge two sorted arrays."""
    result = []
    i = j = 0
    
    while i < len(left) and j < len(right):
        if left[i] <= right[j]:  # <= for stability
            result.append(left[i])
            i += 1
        else:
            result.append(right[j])
            j += 1
    
    result.extend(left[i:])
    result.extend(right[j:])
    return result


def mergesort_inplace(arr: List[int], left: int = 0, right: int = None) -> None:
    """
    In-place MergeSort (reduces space but still O(n) for merge).
    """
    if right is None:
        right = len(arr) - 1
    
    if left >= right:
        return
    
    mid = (left + right) // 2
    mergesort_inplace(arr, left, mid)
    mergesort_inplace(arr, mid + 1, right)
    
    # In-place merge
    temp = []
    i, j = left, mid + 1
    
    while i <= mid and j <= right:
        if arr[i] <= arr[j]:
            temp.append(arr[i])
            i += 1
        else:
            temp.append(arr[j])
            j += 1
    
    while i <= mid:
        temp.append(arr[i])
        i += 1
    
    while j <= right:
        temp.append(arr[j])
        j += 1
    
    arr[left:right+1] = temp
```

### HeapSort

```python
def heapsort(arr: List[int]) -> List[int]:
    """
    HeapSort using max-heap.
    
    Time: O(n log n) always
    Space: O(1) in-place
    Not stable.
    """
    n = len(arr)
    
    def heapify(heap_size: int, root: int):
        largest = root
        left = 2 * root + 1
        right = 2 * root + 2
        
        if left < heap_size and arr[left] > arr[largest]:
            largest = left
        if right < heap_size and arr[right] > arr[largest]:
            largest = right
        
        if largest != root:
            arr[root], arr[largest] = arr[largest], arr[root]
            heapify(heap_size, largest)
    
    # Build max-heap
    for i in range(n // 2 - 1, -1, -1):
        heapify(n, i)
    
    # Extract elements
    for i in range(n - 1, 0, -1):
        arr[0], arr[i] = arr[i], arr[0]
        heapify(i, 0)
    
    return arr
```

### Counting Sort

```python
def counting_sort(arr: List[int]) -> List[int]:
    """
    Counting Sort for non-negative integers.
    
    Time: O(n + k) where k = max value
    Space: O(k)
    Stable.
    """
    if not arr:
        return arr
    
    max_val = max(arr)
    count = [0] * (max_val + 1)
    
    # Count occurrences
    for num in arr:
        count[num] += 1
    
    # Cumulative count (for stability)
    for i in range(1, len(count)):
        count[i] += count[i - 1]
    
    # Build output
    output = [0] * len(arr)
    for num in reversed(arr):  # Reversed for stability
        output[count[num] - 1] = num
        count[num] -= 1
    
    return output


def counting_sort_negative(arr: List[int]) -> List[int]:
    """
    Counting Sort supporting negative integers.
    """
    if not arr:
        return arr
    
    min_val, max_val = min(arr), max(arr)
    range_size = max_val - min_val + 1
    count = [0] * range_size
    
    for num in arr:
        count[num - min_val] += 1
    
    result = []
    for i, c in enumerate(count):
        result.extend([i + min_val] * c)
    
    return result
```

### Radix Sort

```python
def radix_sort(arr: List[int]) -> List[int]:
    """
    Radix Sort (LSD - Least Significant Digit first).
    
    Time: O(d * (n + k)) where d = digits, k = base (10)
    Space: O(n + k)
    Stable.
    """
    if not arr:
        return arr
    
    max_val = max(arr)
    
    # Process each digit
    exp = 1
    while max_val // exp > 0:
        arr = counting_sort_by_digit(arr, exp)
        exp *= 10
    
    return arr


def counting_sort_by_digit(arr: List[int], exp: int) -> List[int]:
    """Counting sort based on digit at exp position."""
    n = len(arr)
    output = [0] * n
    count = [0] * 10
    
    for num in arr:
        digit = (num // exp) % 10
        count[digit] += 1
    
    for i in range(1, 10):
        count[i] += count[i - 1]
    
    for num in reversed(arr):
        digit = (num // exp) % 10
        output[count[digit] - 1] = num
        count[digit] -= 1
    
    return output


def radix_sort_negative(arr: List[int]) -> List[int]:
    """
    Radix Sort supporting negative integers.
    Separate negatives, sort, then combine.
    """
    negatives = [-x for x in arr if x < 0]
    non_negatives = [x for x in arr if x >= 0]
    
    sorted_neg = radix_sort(negatives)
    sorted_non_neg = radix_sort(non_negatives)
    
    return [-x for x in reversed(sorted_neg)] + sorted_non_neg
```

### Bucket Sort

```python
def bucket_sort(arr: List[float]) -> List[float]:
    """
    Bucket Sort for uniformly distributed data in [0, 1).
    
    Time: O(n) average, O(n²) worst
    Space: O(n)
    """
    if not arr:
        return arr
    
    n = len(arr)
    buckets = [[] for _ in range(n)]
    
    # Distribute into buckets
    for num in arr:
        idx = int(num * n)
        if idx == n:  # Handle edge case for 1.0
            idx = n - 1
        buckets[idx].append(num)
    
    # Sort individual buckets
    for bucket in buckets:
        bucket.sort()  # Or use insertion sort
    
    # Concatenate
    result = []
    for bucket in buckets:
        result.extend(bucket)
    
    return result


def bucket_sort_general(arr: List[int], bucket_count: int = None) -> List[int]:
    """
    Bucket Sort for general integers.
    """
    if not arr:
        return arr
    
    min_val, max_val = min(arr), max(arr)
    if min_val == max_val:
        return arr
    
    if bucket_count is None:
        bucket_count = len(arr)
    
    bucket_size = (max_val - min_val) / bucket_count + 1
    buckets = [[] for _ in range(bucket_count)]
    
    for num in arr:
        idx = int((num - min_val) / bucket_size)
        buckets[idx].append(num)
    
    result = []
    for bucket in buckets:
        bucket.sort()
        result.extend(bucket)
    
    return result
```

### Insertion Sort (for small arrays)

```python
def insertion_sort(arr: List[int]) -> List[int]:
    """
    Insertion Sort - efficient for small arrays.
    
    Time: O(n²) worst, O(n) best (nearly sorted)
    Space: O(1)
    Stable.
    """
    for i in range(1, len(arr)):
        key = arr[i]
        j = i - 1
        
        while j >= 0 and arr[j] > key:
            arr[j + 1] = arr[j]
            j -= 1
        
        arr[j + 1] = key
    
    return arr
```

---

## Comparison Table

| Algorithm | Best | Average | Worst | Space | Stable | In-Place |
|-----------|------|---------|-------|-------|--------|----------|
| QuickSort | O(n log n) | O(n log n) | O(n²) | O(log n) | No | Yes |
| MergeSort | O(n log n) | O(n log n) | O(n log n) | O(n) | Yes | No |
| HeapSort | O(n log n) | O(n log n) | O(n log n) | O(1) | No | Yes |
| TimSort | O(n) | O(n log n) | O(n log n) | O(n) | Yes | No |
| Insertion | O(n) | O(n²) | O(n²) | O(1) | Yes | Yes |
| Selection | O(n²) | O(n²) | O(n²) | O(1) | No | Yes |
| Bubble | O(n) | O(n²) | O(n²) | O(1) | Yes | Yes |
| Counting | O(n+k) | O(n+k) | O(n+k) | O(k) | Yes | No |
| Radix | O(d(n+k)) | O(d(n+k)) | O(d(n+k)) | O(n+k) | Yes | No |
| Bucket | O(n+k) | O(n+k) | O(n²) | O(n) | Yes | No |

---

## Specialized Applications

### Sort Colors (Dutch National Flag)

```python
def sort_colors(nums: List[int]) -> None:
    """
    Sort array containing only 0, 1, 2 in-place.
    One-pass, O(1) space.
    """
    low, mid, high = 0, 0, len(nums) - 1
    
    while mid <= high:
        if nums[mid] == 0:
            nums[low], nums[mid] = nums[mid], nums[low]
            low += 1
            mid += 1
        elif nums[mid] == 1:
            mid += 1
        else:  # nums[mid] == 2
            nums[mid], nums[high] = nums[high], nums[mid]
            high -= 1
```

### Custom Comparator Sorting

```python
from functools import cmp_to_key

def largest_number(nums: List[int]) -> str:
    """
    Arrange numbers to form largest number.
    """
    def compare(x: str, y: str) -> int:
        if x + y > y + x:
            return -1
        elif x + y < y + x:
            return 1
        return 0
    
    nums_str = [str(n) for n in nums]
    nums_str.sort(key=cmp_to_key(compare))
    
    result = ''.join(nums_str)
    return '0' if result[0] == '0' else result
```

---

## Interview Tips

### When Asked "Which Sort?"

1. **General purpose:** QuickSort (with random pivot)
2. **Stability needed:** MergeSort or TimSort
3. **Space constraint:** HeapSort
4. **Small range integers:** Counting Sort
5. **Small array or nearly sorted:** Insertion Sort
6. **Linked list:** MergeSort

### Key Points to Mention

- "I'll use QuickSort with random pivot to avoid O(n²) worst case."
- "MergeSort guarantees O(n log n) and is stable."
- "For integers in range [0, k], Counting Sort gives O(n + k)."
- "Python's built-in sort uses TimSort, which is O(n log n) and stable."

---

## Practice Problems

| # | Problem | Key Concept |
|---|---------|-------------|
| 1 | [Sort an Array](https://leetcode.com/problems/sort-an-array/) | Implement sorting |
| 2 | [Sort Colors](https://leetcode.com/problems/sort-colors/) | Dutch Flag |
| 3 | [Largest Number](https://leetcode.com/problems/largest-number/) | Custom comparator |
| 4 | [H-Index](https://leetcode.com/problems/h-index/) | Counting sort |
| 5 | [Maximum Gap](https://leetcode.com/problems/maximum-gap/) | Radix/Bucket sort |
| 6 | [Sort List](https://leetcode.com/problems/sort-list/) | MergeSort linked list |
| 7 | [Insertion Sort List](https://leetcode.com/problems/insertion-sort-list/) | Insertion on list |
| 8 | [Relative Sort Array](https://leetcode.com/problems/relative-sort-array/) | Custom order |
| 9 | [Wiggle Sort II](https://leetcode.com/problems/wiggle-sort-ii/) | Three-way partition |
| 10 | [Kth Largest Element](https://leetcode.com/problems/kth-largest-element-in-an-array/) | QuickSelect |

---

## References

- CLRS: Chapters 6-8 (Sorting)
- [Visualgo: Sorting](https://visualgo.net/en/sorting)
- [Python Timsort](https://en.wikipedia.org/wiki/Timsort)
