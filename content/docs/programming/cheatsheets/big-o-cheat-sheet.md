---
title: "Big-O Cheat Sheet"
topic: "Cheatsheets"
difficulty: "Reference"
tags: ["complexity", "big-o", "time", "space", "cheatsheet"]
status: "complete"
weight: 1
---

# Big-O Complexity Cheat Sheet

## ⏱️ Time Complexity Overview

| Notation | Name | Example | Operations (n=1M) |
|----------|------|---------|-------------------|
| O(1) | Constant | Hash lookup | 1 |
| O(log n) | Logarithmic | Binary search | 20 |
| O(n) | Linear | Array scan | 1,000,000 |
| O(n log n) | Linearithmic | Merge sort | 20,000,000 |
| O(n²) | Quadratic | Nested loops | 1,000,000,000,000 |
| O(n³) | Cubic | Matrix multiplication | 10¹⁸ |
| O(2ⁿ) | Exponential | Subsets | 10³⁰¹,030 |
| O(n!) | Factorial | Permutations | ∞ |

---

## 📊 Data Structures

### Arrays

| Operation | Average | Worst |
|-----------|---------|-------|
| Access by index | O(1) | O(1) |
| Search (unsorted) | O(n) | O(n) |
| Search (sorted) | O(log n) | O(log n) |
| Insert at end | O(1)* | O(n) |
| Insert at beginning | O(n) | O(n) |
| Delete at end | O(1) | O(1) |
| Delete at beginning | O(n) | O(n) |

*Amortized for dynamic arrays

### Linked Lists

| Operation | Singly | Doubly |
|-----------|--------|--------|
| Access | O(n) | O(n) |
| Search | O(n) | O(n) |
| Insert at head | O(1) | O(1) |
| Insert at tail | O(n) / O(1)* | O(1) |
| Delete at head | O(1) | O(1) |
| Delete at tail | O(n) | O(1) |
| Delete by value | O(n) | O(n) |

*O(1) if tail pointer maintained

### Stacks & Queues

| Operation | Time |
|-----------|------|
| Push/Enqueue | O(1) |
| Pop/Dequeue | O(1) |
| Peek/Front | O(1) |
| Search | O(n) |

### Hash Tables

| Operation | Average | Worst |
|-----------|---------|-------|
| Insert | O(1) | O(n) |
| Delete | O(1) | O(n) |
| Search | O(1) | O(n) |

### Binary Search Tree (BST)

| Operation | Average | Worst (unbalanced) |
|-----------|---------|---------------------|
| Search | O(log n) | O(n) |
| Insert | O(log n) | O(n) |
| Delete | O(log n) | O(n) |
| Min/Max | O(log n) | O(n) |

### Balanced BST (AVL, Red-Black)

| Operation | Time |
|-----------|------|
| Search | O(log n) |
| Insert | O(log n) |
| Delete | O(log n) |
| Min/Max | O(log n) |

### Heap (Binary)

| Operation | Time |
|-----------|------|
| Insert | O(log n) |
| Extract Min/Max | O(log n) |
| Peek Min/Max | O(1) |
| Build Heap | O(n) |
| Heapify | O(log n) |

### Trie

| Operation | Time (k = key length) |
|-----------|------------------------|
| Insert | O(k) |
| Search | O(k) |
| Delete | O(k) |
| Prefix search | O(k) |

### Union-Find (Disjoint Set)

| Operation | Time (with optimizations) |
|-----------|---------------------------|
| Find | O(α(n)) ≈ O(1) |
| Union | O(α(n)) ≈ O(1) |

*α = inverse Ackermann function

---

## 🔄 Sorting Algorithms

| Algorithm | Best | Average | Worst | Space | Stable |
|-----------|------|---------|-------|-------|--------|
| Bubble Sort | O(n) | O(n²) | O(n²) | O(1) | Yes |
| Selection Sort | O(n²) | O(n²) | O(n²) | O(1) | No |
| Insertion Sort | O(n) | O(n²) | O(n²) | O(1) | Yes |
| Merge Sort | O(n log n) | O(n log n) | O(n log n) | O(n) | Yes |
| Quick Sort | O(n log n) | O(n log n) | O(n²) | O(log n) | No |
| Heap Sort | O(n log n) | O(n log n) | O(n log n) | O(1) | No |
| Counting Sort | O(n + k) | O(n + k) | O(n + k) | O(k) | Yes |
| Radix Sort | O(nk) | O(nk) | O(nk) | O(n + k) | Yes |
| Bucket Sort | O(n + k) | O(n + k) | O(n²) | O(n) | Yes |
| Tim Sort | O(n) | O(n log n) | O(n log n) | O(n) | Yes |

---

## 🔍 Searching Algorithms

| Algorithm | Time | Space | Notes |
|-----------|------|-------|-------|
| Linear Search | O(n) | O(1) | Unsorted |
| Binary Search | O(log n) | O(1) | Sorted |
| Ternary Search | O(log₃ n) | O(1) | Unimodal |
| Jump Search | O(√n) | O(1) | Sorted |
| Interpolation Search | O(log log n) | O(1) | Uniform dist |
| Exponential Search | O(log n) | O(1) | Unbounded |

---

## 📈 Graph Algorithms

### Traversal

| Algorithm | Time | Space |
|-----------|------|-------|
| BFS | O(V + E) | O(V) |
| DFS | O(V + E) | O(V) |

### Shortest Path

| Algorithm | Time | Space | Notes |
|-----------|------|-------|-------|
| BFS (unweighted) | O(V + E) | O(V) | Unweighted |
| Dijkstra | O((V + E) log V) | O(V) | Non-negative |
| Bellman-Ford | O(V × E) | O(V) | Negative edges |
| Floyd-Warshall | O(V³) | O(V²) | All pairs |
| A* | O(E) best | O(V) | With heuristic |

### Minimum Spanning Tree

| Algorithm | Time | Space |
|-----------|------|-------|
| Kruskal | O(E log E) | O(V) |
| Prim | O(E log V) | O(V) |

### Other

| Algorithm | Time | Space |
|-----------|------|-------|
| Topological Sort | O(V + E) | O(V) |
| Tarjan SCC | O(V + E) | O(V) |
| Kosaraju SCC | O(V + E) | O(V) |

---

## 🧮 Dynamic Programming

| Problem | Time | Space |
|---------|------|-------|
| Fibonacci | O(n) | O(1) or O(n) |
| Longest Common Subsequence | O(n × m) | O(n × m) or O(min(n,m)) |
| Longest Increasing Subsequence | O(n log n) | O(n) |
| Edit Distance | O(n × m) | O(n × m) or O(min(n,m)) |
| 0/1 Knapsack | O(n × W) | O(W) |
| Coin Change | O(n × amount) | O(amount) |
| Matrix Chain Multiplication | O(n³) | O(n²) |

---

## 📝 String Algorithms

| Algorithm | Time | Space |
|-----------|------|-------|
| Naive Pattern Match | O(n × m) | O(1) |
| KMP | O(n + m) | O(m) |
| Rabin-Karp | O(n + m) avg | O(1) |
| Z-Algorithm | O(n + m) | O(n + m) |
| Suffix Array | O(n log n) | O(n) |
| Manacher (Palindromes) | O(n) | O(n) |

---

## 🎯 Common Patterns

| Pattern | Typical Complexity |
|---------|-------------------|
| Two Pointers | O(n) |
| Sliding Window | O(n) |
| Binary Search | O(log n) |
| BFS/DFS | O(V + E) |
| Backtracking | O(k^n) or O(n!) |
| Dynamic Programming | O(n²) or O(n × m) |
| Divide & Conquer | O(n log n) |
| Greedy | O(n log n) |

---

## ⚠️ Interview Complexity Guidelines

| Time Constraint | Target Complexity | Examples |
|-----------------|-------------------|----------|
| n ≤ 10 | O(n!), O(2ⁿ) | Permutations, subsets |
| n ≤ 20 | O(2ⁿ) | Bitmask DP |
| n ≤ 500 | O(n³) | Floyd-Warshall |
| n ≤ 5,000 | O(n²) | 2D DP |
| n ≤ 100,000 | O(n log n) | Sorting, heap |
| n ≤ 1,000,000 | O(n) | Linear scan |
| n ≤ 10⁹ | O(log n), O(1) | Binary search, math |

---

## 📐 Space Complexity Tips

1. **In-place algorithms** use O(1) extra space
2. **Recursive algorithms** use O(depth) stack space
3. **Memoization** typically uses O(states) space
4. **BFS** uses O(width) for queue
5. **DFS** uses O(depth) for stack/recursion

---

## 🧪 Quick Reference

```python
# O(1) - Constant
hash_table[key]
array[index]

# O(log n) - Logarithmic
while n > 0:
    n //= 2

# O(n) - Linear
for item in array:
    process(item)

# O(n log n) - Linearithmic
array.sort()

# O(n²) - Quadratic
for i in range(n):
    for j in range(n):
        process(i, j)

# O(2ⁿ) - Exponential
def subsets(n):
    if n == 0: return [[]]
    rest = subsets(n - 1)
    return rest + [s + [n] for s in rest]

# O(n!) - Factorial
def permutations(arr):
    if len(arr) <= 1:
        return [arr]
    # ... generate all orderings
```

---

*Last Updated: 2024*
