---
title: "Mastering Dynamic Programming: A Complete Guide"
date: 2025-11-28
draft: false
description: "Learn dynamic programming from basics to advanced techniques with practical examples and LeetCode problems."
categories:
  - Programming
tags:
  - algorithms
  - dynamic-programming
  - leetcode
  - interviews
  - python
series:
  - "Interview Prep"
cover:
  image: ""
  alt: "Dynamic Programming"
  caption: "Master the art of DP for coding interviews"
  relative: false
ShowToc: true
TocOpen: true
---

## Introduction

Dynamic Programming (DP) is one of the most powerful algorithmic techniques for solving optimization problems. It's a favorite topic in technical interviews at FAANG companies.

## Core Concepts

### What is Dynamic Programming?

DP is an optimization technique that:

1. **Breaks down problems** into smaller subproblems
2. **Stores solutions** to avoid redundant computation (memoization)
3. **Builds up** the final solution from subproblem solutions

### When to Use DP?

Look for these characteristics:

- ✅ **Optimal substructure**: Optimal solution contains optimal solutions to subproblems
- ✅ **Overlapping subproblems**: Same subproblems are solved multiple times

## Classic DP Patterns

### 1. Fibonacci-style (1D DP)

```python
def climb_stairs(n: int) -> int:
    """
    LeetCode 70: Climbing Stairs
    
    You can climb 1 or 2 steps. Count ways to reach the top.
    """
    if n <= 2:
        return n
    
    # Space optimized: O(1)
    prev2, prev1 = 1, 2
    
    for _ in range(3, n + 1):
        curr = prev1 + prev2
        prev2, prev1 = prev1, curr
    
    return prev1

# Time: O(n), Space: O(1)
```

### 2. Grid DP (2D)

```python
def unique_paths(m: int, n: int) -> int:
    """
    LeetCode 62: Unique Paths
    
    Count paths from top-left to bottom-right in a grid.
    """
    dp = [[1] * n for _ in range(m)]
    
    for i in range(1, m):
        for j in range(1, n):
            dp[i][j] = dp[i-1][j] + dp[i][j-1]
    
    return dp[m-1][n-1]

# Time: O(m*n), Space: O(m*n) -> can optimize to O(n)
```

### 3. Knapsack Pattern

```python
def knapsack_01(weights: list, values: list, capacity: int) -> int:
    """
    Classic 0/1 Knapsack Problem
    
    Maximize value within weight capacity.
    """
    n = len(weights)
    dp = [0] * (capacity + 1)
    
    for i in range(n):
        # Process in REVERSE for 0/1 knapsack
        for w in range(capacity, weights[i] - 1, -1):
            dp[w] = max(dp[w], dp[w - weights[i]] + values[i])
    
    return dp[capacity]
```

### 4. Longest Increasing Subsequence

```python
from bisect import bisect_left

def length_of_lis(nums: list) -> int:
    """
    LeetCode 300: Longest Increasing Subsequence
    
    O(n log n) solution using binary search.
    """
    tails = []
    
    for num in nums:
        idx = bisect_left(tails, num)
        if idx == len(tails):
            tails.append(num)
        else:
            tails[idx] = num
    
    return len(tails)
```

## DP Problem Categories

| Pattern | Example Problems | Key Insight |
|---------|-----------------|-------------|
| Linear DP | House Robber, Decode Ways | `dp[i]` depends on previous elements |
| Grid DP | Unique Paths, Min Path Sum | Process row by row |
| Knapsack | Coin Change, Partition Equal | Include/exclude decisions |
| String DP | LCS, Edit Distance | 2D table for two strings |
| Interval DP | Burst Balloons, MCM | Choose split point |
| Tree DP | House Robber III | DFS with states |
| Bitmask DP | TSP, Assignment | States as bitmasks |

## Interview Tips

1. **Start with brute force**: Understand the recursive structure
2. **Identify state**: What information defines a subproblem?
3. **Write recurrence**: How do subproblems relate?
4. **Add memoization**: Top-down with caching
5. **Convert to bottom-up**: If needed for space optimization

## Conclusion

DP takes practice. Start with classic problems and gradually work up to harder variants. The patterns will become second nature with enough practice.

## Resources

- [LeetCode DP Study Plan](https://leetcode.com/study-plan/dynamic-programming/)
- [NeetCode DP Playlist](https://neetcode.io/practice)
