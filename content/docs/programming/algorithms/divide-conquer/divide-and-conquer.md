---
title: "Divide and Conquer"
topic: "Algorithms"
difficulty: "Medium-Hard"
tags: ["divide-conquer", "recursion", "merge", "binary-search"]
status: "complete"
weight: 1
---

# Divide and Conquer

## 📚 Summary

Divide and Conquer splits a problem into smaller subproblems, solves them recursively, and combines results. Key patterns: sorting (merge sort, quick sort), searching, tree algorithms, and optimization problems.

---

## 1️⃣ Core Pattern

```python
"""
Divide and Conquer Template:
1. Base case: Return solution for trivial input
2. Divide: Split problem into subproblems
3. Conquer: Recursively solve subproblems  
4. Combine: Merge subproblem solutions

def divide_conquer(problem):
    if is_base_case(problem):
        return base_solution(problem)
    
    subproblems = divide(problem)
    subresults = [divide_conquer(sub) for sub in subproblems]
    return combine(subresults)
"""
```

---

## 2️⃣ Merge Sort and Variants

### Classic Merge Sort

```python
def merge_sort(arr: list[int]) -> list[int]:
    """
    Time: O(n log n), Space: O(n)
    Stable sort
    """
    if len(arr) <= 1:
        return arr
    
    mid = len(arr) // 2
    left = merge_sort(arr[:mid])
    right = merge_sort(arr[mid:])
    
    return merge(left, right)


def merge(left: list[int], right: list[int]) -> list[int]:
    result = []
    i = j = 0
    
    while i < len(left) and j < len(right):
        if left[i] <= right[j]:
            result.append(left[i])
            i += 1
        else:
            result.append(right[j])
            j += 1
    
    result.extend(left[i:])
    result.extend(right[j:])
    return result


def merge_sort_inplace(arr: list[int], left: int = 0, right: int = None) -> None:
    """In-place merge sort"""
    if right is None:
        right = len(arr) - 1
    
    if left >= right:
        return
    
    mid = (left + right) // 2
    merge_sort_inplace(arr, left, mid)
    merge_sort_inplace(arr, mid + 1, right)
    merge_inplace(arr, left, mid, right)


def merge_inplace(arr: list[int], left: int, mid: int, right: int) -> None:
    """Merge two sorted subarrays in place"""
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
    
    for k, val in enumerate(temp):
        arr[left + k] = val
```

### Count Inversions (LC 493 variant)

```python
def count_inversions(arr: list[int]) -> int:
    """
    Count pairs (i, j) where i < j and arr[i] > arr[j]
    Modified merge sort
    """
    def merge_count(arr: list[int]) -> tuple[list[int], int]:
        if len(arr) <= 1:
            return arr, 0
        
        mid = len(arr) // 2
        left, left_inv = merge_count(arr[:mid])
        right, right_inv = merge_count(arr[mid:])
        
        merged = []
        inversions = left_inv + right_inv
        i = j = 0
        
        while i < len(left) and j < len(right):
            if left[i] <= right[j]:
                merged.append(left[i])
                i += 1
            else:
                merged.append(right[j])
                # All remaining elements in left are greater than right[j]
                inversions += len(left) - i
                j += 1
        
        merged.extend(left[i:])
        merged.extend(right[j:])
        return merged, inversions
    
    _, count = merge_count(arr)
    return count
```

### Reverse Pairs (LC 493)

```python
def reverse_pairs(nums: list[int]) -> int:
    """
    Count pairs (i, j) where i < j and nums[i] > 2 * nums[j]
    """
    def merge_count(start: int, end: int) -> int:
        if start >= end:
            return 0
        
        mid = (start + end) // 2
        count = merge_count(start, mid) + merge_count(mid + 1, end)
        
        # Count reverse pairs before merging
        j = mid + 1
        for i in range(start, mid + 1):
            while j <= end and nums[i] > 2 * nums[j]:
                j += 1
            count += j - (mid + 1)
        
        # Standard merge
        nums[start:end + 1] = sorted(nums[start:end + 1])
        return count
    
    return merge_count(0, len(nums) - 1)
```

### Count Smaller Numbers After Self (LC 315)

```python
def count_smaller(nums: list[int]) -> list[int]:
    """
    For each element, count elements smaller to its right
    """
    n = len(nums)
    result = [0] * n
    indices = list(range(n))
    
    def merge_sort(start: int, end: int):
        if start >= end:
            return
        
        mid = (start + end) // 2
        merge_sort(start, mid)
        merge_sort(mid + 1, end)
        
        # Count and merge
        temp = []
        i, j = start, mid + 1
        right_count = 0  # Elements from right that are smaller
        
        while i <= mid and j <= end:
            if nums[indices[j]] < nums[indices[i]]:
                right_count += 1
                temp.append(indices[j])
                j += 1
            else:
                result[indices[i]] += right_count
                temp.append(indices[i])
                i += 1
        
        while i <= mid:
            result[indices[i]] += right_count
            temp.append(indices[i])
            i += 1
        
        while j <= end:
            temp.append(indices[j])
            j += 1
        
        indices[start:end + 1] = temp
    
    merge_sort(0, n - 1)
    return result
```

---

## 3️⃣ Quick Select and Quick Sort

### Quick Sort

```python
import random

def quick_sort(arr: list[int]) -> list[int]:
    """
    Average: O(n log n), Worst: O(n²)
    Space: O(log n) for recursion stack
    """
    if len(arr) <= 1:
        return arr
    
    pivot = random.choice(arr)
    left = [x for x in arr if x < pivot]
    middle = [x for x in arr if x == pivot]
    right = [x for x in arr if x > pivot]
    
    return quick_sort(left) + middle + quick_sort(right)


def quick_sort_inplace(arr: list[int], low: int = 0, high: int = None) -> None:
    """In-place quick sort"""
    if high is None:
        high = len(arr) - 1
    
    if low < high:
        pivot_idx = partition(arr, low, high)
        quick_sort_inplace(arr, low, pivot_idx - 1)
        quick_sort_inplace(arr, pivot_idx + 1, high)


def partition(arr: list[int], low: int, high: int) -> int:
    """Lomuto partition scheme with random pivot"""
    pivot_idx = random.randint(low, high)
    arr[pivot_idx], arr[high] = arr[high], arr[pivot_idx]
    pivot = arr[high]
    
    i = low
    for j in range(low, high):
        if arr[j] < pivot:
            arr[i], arr[j] = arr[j], arr[i]
            i += 1
    
    arr[i], arr[high] = arr[high], arr[i]
    return i
```

### Quick Select (Kth Largest - LC 215)

```python
def find_kth_largest(nums: list[int], k: int) -> int:
    """
    Find kth largest element
    Average: O(n), Worst: O(n²)
    """
    def quick_select(left: int, right: int, k_smallest: int) -> int:
        if left == right:
            return nums[left]
        
        pivot_idx = partition_select(left, right)
        
        if k_smallest == pivot_idx:
            return nums[k_smallest]
        elif k_smallest < pivot_idx:
            return quick_select(left, pivot_idx - 1, k_smallest)
        else:
            return quick_select(pivot_idx + 1, right, k_smallest)
    
    def partition_select(left: int, right: int) -> int:
        pivot_idx = random.randint(left, right)
        nums[pivot_idx], nums[right] = nums[right], nums[pivot_idx]
        pivot = nums[right]
        
        store_idx = left
        for i in range(left, right):
            if nums[i] < pivot:
                nums[store_idx], nums[i] = nums[i], nums[store_idx]
                store_idx += 1
        
        nums[store_idx], nums[right] = nums[right], nums[store_idx]
        return store_idx
    
    n = len(nums)
    return quick_select(0, n - 1, n - k)
```

---

## 4️⃣ Binary Search as D&C

### Search in Rotated Array (LC 33)

```python
def search_rotated(nums: list[int], target: int) -> int:
    """Search in rotated sorted array"""
    left, right = 0, len(nums) - 1
    
    while left <= right:
        mid = (left + right) // 2
        
        if nums[mid] == target:
            return mid
        
        # Left half is sorted
        if nums[left] <= nums[mid]:
            if nums[left] <= target < nums[mid]:
                right = mid - 1
            else:
                left = mid + 1
        # Right half is sorted
        else:
            if nums[mid] < target <= nums[right]:
                left = mid + 1
            else:
                right = mid - 1
    
    return -1
```

### Find Peak Element (LC 162)

```python
def find_peak_element(nums: list[int]) -> int:
    """Find any peak element index"""
    left, right = 0, len(nums) - 1
    
    while left < right:
        mid = (left + right) // 2
        
        if nums[mid] > nums[mid + 1]:
            right = mid
        else:
            left = mid + 1
    
    return left
```

---

## 5️⃣ Maximum Subarray (LC 53)

```python
def max_subarray_dc(nums: list[int]) -> int:
    """
    Divide and Conquer solution
    Time: O(n log n)
    """
    def helper(left: int, right: int) -> int:
        if left == right:
            return nums[left]
        
        mid = (left + right) // 2
        
        # Max subarray entirely in left half
        left_max = helper(left, mid)
        # Max subarray entirely in right half
        right_max = helper(mid + 1, right)
        # Max subarray crossing the middle
        cross_max = max_crossing_sum(left, mid, right)
        
        return max(left_max, right_max, cross_max)
    
    def max_crossing_sum(left: int, mid: int, right: int) -> int:
        # Max sum from mid going left
        left_sum = float('-inf')
        curr_sum = 0
        for i in range(mid, left - 1, -1):
            curr_sum += nums[i]
            left_sum = max(left_sum, curr_sum)
        
        # Max sum from mid+1 going right
        right_sum = float('-inf')
        curr_sum = 0
        for i in range(mid + 1, right + 1):
            curr_sum += nums[i]
            right_sum = max(right_sum, curr_sum)
        
        return left_sum + right_sum
    
    return helper(0, len(nums) - 1)
```

---

## 6️⃣ Closest Pair of Points

```python
import math

def closest_pair(points: list[tuple[float, float]]) -> float:
    """
    Find minimum distance between any two points
    Time: O(n log n)
    """
    def distance(p1: tuple, p2: tuple) -> float:
        return math.sqrt((p1[0] - p2[0])**2 + (p1[1] - p2[1])**2)
    
    def brute_force(pts: list[tuple]) -> float:
        min_dist = float('inf')
        for i in range(len(pts)):
            for j in range(i + 1, len(pts)):
                min_dist = min(min_dist, distance(pts[i], pts[j]))
        return min_dist
    
    def closest_strip(strip: list[tuple], d: float) -> float:
        """Check points in strip sorted by y"""
        min_dist = d
        strip.sort(key=lambda p: p[1])
        
        for i in range(len(strip)):
            j = i + 1
            while j < len(strip) and (strip[j][1] - strip[i][1]) < min_dist:
                min_dist = min(min_dist, distance(strip[i], strip[j]))
                j += 1
        
        return min_dist
    
    def closest_util(pts_x: list[tuple]) -> float:
        n = len(pts_x)
        
        if n <= 3:
            return brute_force(pts_x)
        
        mid = n // 2
        mid_point = pts_x[mid]
        
        # Divide
        left = closest_util(pts_x[:mid])
        right = closest_util(pts_x[mid:])
        
        d = min(left, right)
        
        # Build strip of points within distance d from mid line
        strip = [p for p in pts_x if abs(p[0] - mid_point[0]) < d]
        
        return min(d, closest_strip(strip, d))
    
    # Sort by x-coordinate
    points_sorted = sorted(points, key=lambda p: p[0])
    return closest_util(points_sorted)
```

---

## 7️⃣ Median of Two Sorted Arrays (LC 4)

```python
def find_median_sorted_arrays(nums1: list[int], nums2: list[int]) -> float:
    """
    Find median of two sorted arrays
    Time: O(log(min(m, n)))
    """
    # Ensure nums1 is smaller
    if len(nums1) > len(nums2):
        nums1, nums2 = nums2, nums1
    
    m, n = len(nums1), len(nums2)
    left, right = 0, m
    
    while left <= right:
        partition1 = (left + right) // 2
        partition2 = (m + n + 1) // 2 - partition1
        
        max_left1 = float('-inf') if partition1 == 0 else nums1[partition1 - 1]
        min_right1 = float('inf') if partition1 == m else nums1[partition1]
        
        max_left2 = float('-inf') if partition2 == 0 else nums2[partition2 - 1]
        min_right2 = float('inf') if partition2 == n else nums2[partition2]
        
        if max_left1 <= min_right2 and max_left2 <= min_right1:
            if (m + n) % 2 == 0:
                return (max(max_left1, max_left2) + min(min_right1, min_right2)) / 2
            else:
                return max(max_left1, max_left2)
        elif max_left1 > min_right2:
            right = partition1 - 1
        else:
            left = partition1 + 1
    
    raise ValueError("Input arrays are not sorted")
```

---

## 8️⃣ Construct Trees

### Build Binary Tree from Preorder/Inorder (LC 105)

```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right


def build_tree_pre_in(preorder: list[int], inorder: list[int]) -> TreeNode:
    """Build tree from preorder and inorder traversals"""
    if not preorder or not inorder:
        return None
    
    # First element of preorder is root
    root = TreeNode(preorder[0])
    mid = inorder.index(preorder[0])
    
    # Divide and conquer
    root.left = build_tree_pre_in(preorder[1:mid + 1], inorder[:mid])
    root.right = build_tree_pre_in(preorder[mid + 1:], inorder[mid + 1:])
    
    return root


def build_tree_pre_in_optimized(preorder: list[int], inorder: list[int]) -> TreeNode:
    """Optimized with hashmap"""
    inorder_map = {val: idx for idx, val in enumerate(inorder)}
    
    def build(pre_start: int, pre_end: int, in_start: int, in_end: int) -> TreeNode:
        if pre_start > pre_end:
            return None
        
        root_val = preorder[pre_start]
        root = TreeNode(root_val)
        
        mid = inorder_map[root_val]
        left_size = mid - in_start
        
        root.left = build(pre_start + 1, pre_start + left_size, in_start, mid - 1)
        root.right = build(pre_start + left_size + 1, pre_end, mid + 1, in_end)
        
        return root
    
    return build(0, len(preorder) - 1, 0, len(inorder) - 1)
```

### Build Tree from Inorder/Postorder (LC 106)

```python
def build_tree_in_post(inorder: list[int], postorder: list[int]) -> TreeNode:
    """Build tree from inorder and postorder traversals"""
    inorder_map = {val: idx for idx, val in enumerate(inorder)}
    
    def build(in_start: int, in_end: int, post_start: int, post_end: int) -> TreeNode:
        if in_start > in_end:
            return None
        
        root_val = postorder[post_end]
        root = TreeNode(root_val)
        
        mid = inorder_map[root_val]
        left_size = mid - in_start
        
        root.left = build(in_start, mid - 1, post_start, post_start + left_size - 1)
        root.right = build(mid + 1, in_end, post_start + left_size, post_end - 1)
        
        return root
    
    return build(0, len(inorder) - 1, 0, len(postorder) - 1)
```

---

## 9️⃣ Different Ways to Add Parentheses (LC 241)

```python
def diff_ways_to_compute(expression: str) -> list[int]:
    """
    Return all possible results from different parenthesizations
    """
    memo = {}
    
    def compute(expr: str) -> list[int]:
        if expr in memo:
            return memo[expr]
        
        # Base case: just a number
        if expr.isdigit():
            return [int(expr)]
        
        # Handle multi-digit numbers
        if expr.lstrip('-').isdigit():
            return [int(expr)]
        
        results = []
        
        for i, char in enumerate(expr):
            if char in '+-*':
                # Divide at operator
                left_results = compute(expr[:i])
                right_results = compute(expr[i + 1:])
                
                # Combine all possibilities
                for left in left_results:
                    for right in right_results:
                        if char == '+':
                            results.append(left + right)
                        elif char == '-':
                            results.append(left - right)
                        elif char == '*':
                            results.append(left * right)
        
        memo[expr] = results
        return results
    
    return compute(expression)
```

---

## 🔟 Beautiful Array (LC 932)

```python
def beautiful_array(n: int) -> list[int]:
    """
    Construct array where no arithmetic progression exists
    for any i < j < k: A[k]*2 != A[i] + A[j]
    
    Key insight: 
    - Odd + Even = Odd (never equals 2*anything)
    - If A is beautiful, 2*A-1 (all odd) and 2*A (all even) are beautiful
    """
    memo = {1: [1]}
    
    def solve(n: int) -> list[int]:
        if n in memo:
            return memo[n]
        
        # Split into odds and evens
        odds = solve((n + 1) // 2)  # Ceil
        evens = solve(n // 2)
        
        # Transform: odds become 2*x-1, evens become 2*x
        result = [2 * x - 1 for x in odds] + [2 * x for x in evens]
        
        # Keep only values <= n
        result = [x for x in result if x <= n]
        
        memo[n] = result
        return result
    
    return solve(n)
```

---

## ⏱️ Complexity Summary

| Algorithm | Time | Space |
|-----------|------|-------|
| Merge Sort | O(n log n) | O(n) |
| Quick Sort | O(n log n) avg | O(log n) |
| Quick Select | O(n) avg | O(1) |
| Closest Pair | O(n log n) | O(n) |
| Median Arrays | O(log min(m,n)) | O(1) |
| Build Tree | O(n) | O(n) |

---

## 📚 Practice Problems

| # | Problem | Key Concept |
|---|---------|-------------|
| 1 | [LC 53: Maximum Subarray](https://leetcode.com/problems/maximum-subarray/) | Crossing sum |
| 2 | [LC 215: Kth Largest](https://leetcode.com/problems/kth-largest-element-in-an-array/) | Quick select |
| 3 | [LC 4: Median of Two Arrays](https://leetcode.com/problems/median-of-two-sorted-arrays/) | Binary partition |
| 4 | [LC 315: Count Smaller](https://leetcode.com/problems/count-of-smaller-numbers-after-self/) | Merge count |
| 5 | [LC 493: Reverse Pairs](https://leetcode.com/problems/reverse-pairs/) | Merge count |
| 6 | [LC 105: Build Tree Pre+In](https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/) | Tree build |
| 7 | [LC 106: Build Tree In+Post](https://leetcode.com/problems/construct-binary-tree-from-inorder-and-postorder-traversal/) | Tree build |
| 8 | [LC 241: Different Ways](https://leetcode.com/problems/different-ways-to-add-parentheses/) | Expression |
| 9 | [LC 932: Beautiful Array](https://leetcode.com/problems/beautiful-array/) | Construction |
| 10 | [LC 23: Merge K Lists](https://leetcode.com/problems/merge-k-sorted-lists/) | Merge |
| 11 | [LC 33: Search Rotated](https://leetcode.com/problems/search-in-rotated-sorted-array/) | Binary search |
| 12 | [LC 162: Find Peak](https://leetcode.com/problems/find-peak-element/) | Binary search |
| 13 | [LC 169: Majority Element](https://leetcode.com/problems/majority-element/) | Boyer-Moore |
| 14 | [LC 973: K Closest Points](https://leetcode.com/problems/k-closest-points-to-origin/) | Quick select |
| 15 | [LC 327: Count Range Sum](https://leetcode.com/problems/count-of-range-sum/) | Merge count |

---

*Last Updated: 2024*
