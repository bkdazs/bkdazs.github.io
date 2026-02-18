---
title: "Monotonic Stack/Queue Patterns"
topic: "Problem Patterns"
difficulty: "Medium"
tags: ["monotonic-stack", "monotonic-queue", "next-greater", "sliding-window"]
status: "complete"
weight: 13
---

# Monotonic Stack/Queue Patterns

## 📚 Overview

Monotonic data structures maintain elements in sorted order (increasing or decreasing). They're powerful for "next greater/smaller" problems.

---

## 1️⃣ Next Greater Element

### Basic Template

```python
def next_greater_element(nums: list[int]) -> list[int]:
    """For each element, find next greater element to the right"""
    n = len(nums)
    result = [-1] * n
    stack = []  # Monotonic decreasing (store indices)
    
    for i in range(n):
        # Pop elements smaller than current
        while stack and nums[stack[-1]] < nums[i]:
            idx = stack.pop()
            result[idx] = nums[i]
        stack.append(i)
    
    return result

# Example: [2, 1, 2, 4, 3] → [4, 2, 4, -1, -1]
```

### Next Greater Element II (Circular - LC 503)

```python
def next_greater_elements_circular(nums: list[int]) -> list[int]:
    """Circular array - wrap around"""
    n = len(nums)
    result = [-1] * n
    stack = []
    
    # Process array twice to handle circular
    for i in range(2 * n):
        idx = i % n
        while stack and nums[stack[-1]] < nums[idx]:
            result[stack.pop()] = nums[idx]
        
        if i < n:  # Only add indices from first pass
            stack.append(i)
    
    return result
```

---

## 2️⃣ Daily Temperatures (LC 739)

```python
def daily_temperatures(temperatures: list[int]) -> list[int]:
    """Days until warmer temperature"""
    n = len(temperatures)
    result = [0] * n
    stack = []  # Monotonic decreasing
    
    for i in range(n):
        while stack and temperatures[stack[-1]] < temperatures[i]:
            idx = stack.pop()
            result[idx] = i - idx  # Days difference
        stack.append(i)
    
    return result
```

---

## 3️⃣ Stock Span Problem (LC 901)

```python
class StockSpanner:
    """Count consecutive days price <= today's price"""
    
    def __init__(self):
        self.stack = []  # (price, span)
    
    def next(self, price: int) -> int:
        span = 1
        
        # Pop all days with lower or equal price
        while self.stack and self.stack[-1][0] <= price:
            span += self.stack.pop()[1]
        
        self.stack.append((price, span))
        return span
```

---

## 4️⃣ Largest Rectangle in Histogram (LC 84)

```python
def largest_rectangle_area(heights: list[int]) -> int:
    """Find largest rectangle that can be formed"""
    stack = []  # Monotonic increasing (indices)
    max_area = 0
    
    for i, h in enumerate(heights):
        start = i
        
        while stack and stack[-1][1] > h:
            idx, height = stack.pop()
            max_area = max(max_area, height * (i - idx))
            start = idx
        
        stack.append((start, h))
    
    # Process remaining
    for idx, height in stack:
        max_area = max(max_area, height * (len(heights) - idx))
    
    return max_area
```

### Alternative with Sentinel

```python
def largest_rectangle_area_v2(heights: list[int]) -> int:
    """Add 0 at both ends to simplify logic"""
    heights = [0] + heights + [0]
    stack = [0]  # Index of sentinel
    max_area = 0
    
    for i in range(1, len(heights)):
        while heights[stack[-1]] > heights[i]:
            h = heights[stack.pop()]
            w = i - stack[-1] - 1
            max_area = max(max_area, h * w)
        stack.append(i)
    
    return max_area
```

---

## 5️⃣ Maximal Rectangle (LC 85)

```python
def maximal_rectangle(matrix: list[list[str]]) -> int:
    """Largest rectangle of 1s in binary matrix"""
    if not matrix:
        return 0
    
    rows, cols = len(matrix), len(matrix[0])
    heights = [0] * cols
    max_area = 0
    
    for row in matrix:
        # Update heights
        for j in range(cols):
            heights[j] = heights[j] + 1 if row[j] == '1' else 0
        
        # Apply histogram algorithm
        max_area = max(max_area, largest_rectangle_area(heights))
    
    return max_area
```

---

## 6️⃣ Trapping Rain Water (LC 42)

### Monotonic Stack Solution

```python
def trap(height: list[int]) -> int:
    """Calculate trapped water"""
    stack = []  # Monotonic decreasing
    water = 0
    
    for i, h in enumerate(height):
        while stack and height[stack[-1]] < h:
            bottom = height[stack.pop()]
            if not stack:
                break
            
            left_idx = stack[-1]
            width = i - left_idx - 1
            bounded_height = min(height[left_idx], h) - bottom
            water += width * bounded_height
        
        stack.append(i)
    
    return water
```

### Two Pointer Solution

```python
def trap_two_pointer(height: list[int]) -> int:
    left, right = 0, len(height) - 1
    left_max = right_max = 0
    water = 0
    
    while left < right:
        if height[left] < height[right]:
            if height[left] >= left_max:
                left_max = height[left]
            else:
                water += left_max - height[left]
            left += 1
        else:
            if height[right] >= right_max:
                right_max = height[right]
            else:
                water += right_max - height[right]
            right -= 1
    
    return water
```

---

## 7️⃣ Monotonic Queue - Sliding Window Maximum (LC 239)

```python
from collections import deque

def max_sliding_window(nums: list[int], k: int) -> list[int]:
    """Find max in each window of size k"""
    result = []
    dq = deque()  # Monotonic decreasing (store indices)
    
    for i, num in enumerate(nums):
        # Remove elements outside window
        while dq and dq[0] <= i - k:
            dq.popleft()
        
        # Remove smaller elements (they can never be max)
        while dq and nums[dq[-1]] < num:
            dq.pop()
        
        dq.append(i)
        
        # Add result once window is complete
        if i >= k - 1:
            result.append(nums[dq[0]])
    
    return result
```

---

## 8️⃣ Sum of Subarray Minimums (LC 907)

```python
def sum_subarray_mins(arr: list[int]) -> int:
    """Sum of minimum elements of all subarrays"""
    MOD = 10**9 + 7
    n = len(arr)
    
    # For each element, find how many subarrays it's the minimum
    # left[i] = elements to left until smaller element
    # right[i] = elements to right until smaller or equal
    
    left = [0] * n  # Distance to previous smaller
    right = [0] * n  # Distance to next smaller or equal
    stack = []
    
    # Previous smaller
    for i in range(n):
        while stack and arr[stack[-1]] >= arr[i]:
            stack.pop()
        left[i] = i - stack[-1] if stack else i + 1
        stack.append(i)
    
    stack = []
    
    # Next smaller or equal
    for i in range(n - 1, -1, -1):
        while stack and arr[stack[-1]] > arr[i]:
            stack.pop()
        right[i] = stack[-1] - i if stack else n - i
        stack.append(i)
    
    # Each element contributes arr[i] * left[i] * right[i]
    result = sum(arr[i] * left[i] * right[i] for i in range(n))
    return result % MOD
```

---

## 9️⃣ Remove K Digits (LC 402)

```python
def remove_k_digits(num: str, k: int) -> str:
    """Remove k digits to make smallest number"""
    stack = []
    
    for digit in num:
        # Remove larger digits from stack
        while k > 0 and stack and stack[-1] > digit:
            stack.pop()
            k -= 1
        stack.append(digit)
    
    # Remove remaining from end
    stack = stack[:-k] if k else stack
    
    # Remove leading zeros
    return ''.join(stack).lstrip('0') or '0'
```

---

## 🔟 132 Pattern (LC 456)

```python
def find132pattern(nums: list[int]) -> bool:
    """Find i < j < k where nums[i] < nums[k] < nums[j]"""
    n = len(nums)
    if n < 3:
        return False
    
    stack = []  # Monotonic decreasing, tracking potential nums[j]
    third = float('-inf')  # nums[k] - second largest so far
    
    # Traverse from right
    for i in range(n - 1, -1, -1):
        if nums[i] < third:
            return True
        
        while stack and stack[-1] < nums[i]:
            third = stack.pop()  # Update third (nums[k])
        
        stack.append(nums[i])
    
    return False
```

---

## 📝 Pattern Recognition

| When to Use | Stack Type |
|-------------|-----------|
| Next Greater | Decreasing |
| Next Smaller | Increasing |
| Previous Greater | Decreasing (right to left) |
| Previous Smaller | Increasing (right to left) |

---

## 📝 Related Problems

| Problem | LC # | Pattern |
|---------|------|---------|
| Next Greater Element I | 496 | Basic NGE |
| Next Greater Element II | 503 | Circular NGE |
| Daily Temperatures | 739 | Days until warmer |
| Stock Span | 901 | Online monotonic |
| Largest Rectangle in Histogram | 84 | Classic |
| Maximal Rectangle | 85 | Histogram per row |
| Trapping Rain Water | 42 | Two approaches |
| Sliding Window Maximum | 239 | Monotonic deque |
| Sum of Subarray Minimums | 907 | Contribution technique |
| Sum of Subarray Ranges | 2104 | Min + Max contribution |
| Remove K Digits | 402 | Greedy with stack |
| 132 Pattern | 456 | Right to left |
| Online Stock Span | 901 | Streaming data |

---

*Last Updated: 2024*
