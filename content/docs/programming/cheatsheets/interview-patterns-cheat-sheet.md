---
title: "Interview Patterns Cheat Sheet"
topic: "Cheatsheets"
difficulty: "Reference"
tags: ["patterns", "interview", "quick-reference", "cheatsheet"]
status: "complete"
weight: 2
---

# Interview Patterns Quick Reference

## 🎯 Pattern Recognition Guide

### When to Use Each Pattern

| If You See... | Consider Pattern |
|---------------|-----------------|
| Sorted array | Binary Search, Two Pointers |
| Find pairs/triplets | Two Pointers |
| Contiguous subarray | Sliding Window |
| Tree/Graph traversal | BFS, DFS |
| Shortest path | BFS (unweighted), Dijkstra |
| All paths/combinations | Backtracking |
| Optimal substructure | Dynamic Programming |
| Connected components | Union-Find, DFS |
| Prefix sums needed | Prefix Sum Array |
| Next greater/smaller | Monotonic Stack |
| Scheduling problems | Greedy, Heap |
| Stream of data | Heap, Two Heaps |
| Top K elements | Heap, QuickSelect |
| String matching | KMP, Rabin-Karp |
| Linked list cycle | Fast & Slow Pointers |

---

## 🔧 Code Templates

### 1. Binary Search

```python
def binary_search(arr, target):
    left, right = 0, len(arr) - 1
    while left <= right:
        mid = left + (right - left) // 2
        if arr[mid] == target:
            return mid
        elif arr[mid] < target:
            left = mid + 1
        else:
            right = mid - 1
    return -1  # or left for insertion point

# Left boundary (first occurrence)
def left_bound(arr, target):
    left, right = 0, len(arr)
    while left < right:
        mid = left + (right - left) // 2
        if arr[mid] < target:
            left = mid + 1
        else:
            right = mid
    return left
```

### 2. Two Pointers

```python
# Opposite ends (sorted array)
def two_sum_sorted(arr, target):
    left, right = 0, len(arr) - 1
    while left < right:
        total = arr[left] + arr[right]
        if total == target:
            return [left, right]
        elif total < target:
            left += 1
        else:
            right -= 1
    return []

# Same direction
def remove_duplicates(arr):
    if not arr: return 0
    slow = 0
    for fast in range(1, len(arr)):
        if arr[fast] != arr[slow]:
            slow += 1
            arr[slow] = arr[fast]
    return slow + 1
```

### 3. Sliding Window

```python
# Fixed size
def max_sum_k(arr, k):
    window_sum = sum(arr[:k])
    max_sum = window_sum
    for i in range(k, len(arr)):
        window_sum += arr[i] - arr[i - k]
        max_sum = max(max_sum, window_sum)
    return max_sum

# Variable size
def min_subarray_sum(arr, target):
    left = window_sum = 0
    min_len = float('inf')
    for right in range(len(arr)):
        window_sum += arr[right]
        while window_sum >= target:
            min_len = min(min_len, right - left + 1)
            window_sum -= arr[left]
            left += 1
    return min_len if min_len != float('inf') else 0
```

### 4. BFS Template

```python
from collections import deque

def bfs(graph, start):
    visited = {start}
    queue = deque([start])
    while queue:
        node = queue.popleft()
        for neighbor in graph[node]:
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append(neighbor)

# Level-order
def bfs_levels(root):
    if not root: return []
    result, queue = [], deque([root])
    while queue:
        level = []
        for _ in range(len(queue)):
            node = queue.popleft()
            level.append(node.val)
            if node.left: queue.append(node.left)
            if node.right: queue.append(node.right)
        result.append(level)
    return result
```

### 5. DFS Template

```python
# Recursive
def dfs(graph, node, visited):
    if node in visited:
        return
    visited.add(node)
    for neighbor in graph[node]:
        dfs(graph, neighbor, visited)

# Iterative
def dfs_iterative(graph, start):
    visited = set()
    stack = [start]
    while stack:
        node = stack.pop()
        if node in visited:
            continue
        visited.add(node)
        for neighbor in graph[node]:
            stack.append(neighbor)
```

### 6. Backtracking

```python
def backtrack(candidates, target, start, path, result):
    if target == 0:
        result.append(path[:])
        return
    for i in range(start, len(candidates)):
        if candidates[i] > target:
            break
        path.append(candidates[i])
        backtrack(candidates, target - candidates[i], i, path, result)
        path.pop()  # Backtrack!
```

### 7. Dynamic Programming

```python
# 1D DP
def climb_stairs(n):
    if n <= 2: return n
    dp = [0] * (n + 1)
    dp[1], dp[2] = 1, 2
    for i in range(3, n + 1):
        dp[i] = dp[i-1] + dp[i-2]
    return dp[n]

# 2D DP
def unique_paths(m, n):
    dp = [[1] * n for _ in range(m)]
    for i in range(1, m):
        for j in range(1, n):
            dp[i][j] = dp[i-1][j] + dp[i][j-1]
    return dp[m-1][n-1]
```

### 8. Union-Find

```python
class UnionFind:
    def __init__(self, n):
        self.parent = list(range(n))
        self.rank = [0] * n
    
    def find(self, x):
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])
        return self.parent[x]
    
    def union(self, x, y):
        px, py = self.find(x), self.find(y)
        if px == py: return False
        if self.rank[px] < self.rank[py]:
            px, py = py, px
        self.parent[py] = px
        if self.rank[px] == self.rank[py]:
            self.rank[px] += 1
        return True
```

### 9. Heap Operations

```python
import heapq

# Min heap (default)
heap = []
heapq.heappush(heap, 3)
heapq.heappush(heap, 1)
min_val = heapq.heappop(heap)  # 1

# Max heap (negate values)
max_heap = []
heapq.heappush(max_heap, -3)
max_val = -heapq.heappop(max_heap)

# Top K smallest
top_k = heapq.nsmallest(k, arr)

# Top K largest
top_k = heapq.nlargest(k, arr)
```

### 10. Monotonic Stack

```python
def next_greater(nums):
    result = [-1] * len(nums)
    stack = []
    for i, num in enumerate(nums):
        while stack and nums[stack[-1]] < num:
            result[stack.pop()] = num
        stack.append(i)
    return result
```

### 11. Prefix Sum

```python
def prefix_sum(nums):
    prefix = [0] * (len(nums) + 1)
    for i in range(len(nums)):
        prefix[i + 1] = prefix[i] + nums[i]
    return prefix

# Range sum query [i, j]
def range_sum(prefix, i, j):
    return prefix[j + 1] - prefix[i]
```

### 12. Trie

```python
class TrieNode:
    def __init__(self):
        self.children = {}
        self.is_end = False

class Trie:
    def __init__(self):
        self.root = TrieNode()
    
    def insert(self, word):
        node = self.root
        for c in word:
            if c not in node.children:
                node.children[c] = TrieNode()
            node = node.children[c]
        node.is_end = True
    
    def search(self, word):
        node = self.root
        for c in word:
            if c not in node.children:
                return False
            node = node.children[c]
        return node.is_end
```

---

## 📋 Common Problem Types

### Array Problems
- Two Sum → Hash Map O(n)
- Three Sum → Sort + Two Pointers O(n²)
- Kadane's → Max Subarray Sum O(n)
- Product Except Self → Prefix/Suffix O(n)

### String Problems
- Valid Anagram → Frequency Count O(n)
- Longest Palindrome → Expand from Center O(n²)
- String Matching → KMP O(n + m)

### Linked List Problems
- Reverse → Iterative O(n)
- Detect Cycle → Fast/Slow O(n)
- Merge Two Sorted → Two Pointers O(n + m)

### Tree Problems
- Inorder/Preorder/Postorder → DFS O(n)
- Level Order → BFS O(n)
- Lowest Common Ancestor → DFS O(n)
- Diameter → DFS with max tracking O(n)

### Graph Problems
- Number of Islands → DFS/BFS O(m × n)
- Clone Graph → DFS + Hash Map O(V + E)
- Course Schedule → Topological Sort O(V + E)

### DP Problems
- Fibonacci → 1D DP O(n)
- LCS → 2D DP O(n × m)
- Coin Change → 1D DP O(n × amount)
- Knapsack → 2D DP O(n × W)

---

## ⚡ Quick Formulas

```python
# GCD
import math
gcd = math.gcd(a, b)

# LCM
lcm = (a * b) // math.gcd(a, b)

# Power mod
pow(base, exp, mod)

# Combinations
from math import comb
c = comb(n, k)

# Permutations
from math import perm
p = perm(n, k)

# Check power of 2
is_power_of_2 = n > 0 and (n & (n - 1)) == 0

# Count set bits
bin(n).count('1')
n.bit_count()  # Python 3.10+

# Bit operations
n & (n - 1)    # Remove lowest set bit
n & -n         # Get lowest set bit
n | (n + 1)    # Set lowest unset bit
```

---

## 🎲 Edge Cases Checklist

- [ ] Empty input
- [ ] Single element
- [ ] Two elements
- [ ] All same elements
- [ ] Already sorted (ascending/descending)
- [ ] Negative numbers
- [ ] Zeros
- [ ] Integer overflow
- [ ] Duplicates
- [ ] Large input size

---

*Last Updated: 2024*
