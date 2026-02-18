---
title: Dynamic Programming Overview
topic: Dynamic Programming
difficulty: Medium
tags: [dp, dynamic-programming, memoization, tabulation, optimization]
status: complete
weight: 1
---

# Dynamic Programming (DP) Overview

## 1. Summary / TL;DR

- **DP** solves problems by breaking them into overlapping subproblems and storing results
- Two approaches: **Top-down (Memoization)** and **Bottom-up (Tabulation)**
- Identify DP: **Optimal substructure** + **Overlapping subproblems**
- Framework: Define state → Write recurrence → Handle base cases → Add memoization/build table
- Common patterns: 1D DP, 2D DP, state compression, interval DP, tree DP
- Master the art of **defining states** and **state transitions**

---

## 2. When & Where to Use

| Problem Type | DP Approach | Examples |
|-------------|-------------|----------|
| Optimization (min/max) | DP | Shortest path, minimum cost |
| Counting | DP | Number of ways, paths |
| Yes/No feasibility | DP | Can we achieve X? |
| String matching | 2D DP | Edit distance, LCS |
| Sequences | 1D/2D DP | LIS, subsequences |
| Partitioning | DP | Subset sum, palindrome partitioning |

### Greedy vs DP vs Backtracking

```
Problem requires:
├── Locally optimal = globally optimal → GREEDY
├── Exponential possibilities but overlapping subproblems → DP
├── Find ALL solutions → BACKTRACKING
└── Optimization with overlapping subproblems → DP
```

---

## 3. Time & Space Complexity

| DP Type | Time | Space | Example |
|---------|------|-------|---------|
| 1D DP | O(n) | O(n) or O(1) | Fibonacci, Climbing Stairs |
| 2D DP | O(n×m) | O(n×m) or O(n) | Edit Distance, LCS |
| 3D DP | O(n×m×k) | O(n×m×k) | 3D path problems |
| Bitmask DP | O(2^n × n) | O(2^n) | TSP, subset problems |
| Interval DP | O(n³) | O(n²) | Matrix chain, burst balloons |

### Space Optimization

Many 2D DP problems can be reduced to O(n) space by only keeping the previous row/state.

---

## 4. Core Concepts & Theory

### Two Defining Properties

**1. Optimal Substructure**
- Optimal solution contains optimal solutions to subproblems
- Example: Shortest path A→C through B = shortest(A→B) + shortest(B→C)

**2. Overlapping Subproblems**
- Same subproblems are solved multiple times
- Example: Fibonacci - fib(n-1) and fib(n-2) both compute fib(n-3)

```
Fibonacci without memoization:
                    fib(5)
                   /      \
              fib(4)      fib(3)
             /    \       /    \
         fib(3) fib(2) fib(2) fib(1)
         /    \
     fib(2) fib(1)
     
fib(3) computed 2 times
fib(2) computed 3 times
→ Exponential redundancy!
```

### Top-Down (Memoization) vs Bottom-Up (Tabulation)

**Top-Down (Memoization)**:
- Start from the problem, recursively solve subproblems
- Cache (memoize) results to avoid recomputation
- More intuitive, follows the recurrence naturally
- Uses recursion stack (potential stack overflow for large inputs)

```python
def fib_memo(n, memo={}):
    if n in memo:
        return memo[n]
    if n <= 1:
        return n
    memo[n] = fib_memo(n-1, memo) + fib_memo(n-2, memo)
    return memo[n]
```

**Bottom-Up (Tabulation)**:
- Start from smallest subproblems, build up to solution
- Fill a table iteratively
- Requires understanding the dependency order
- No recursion stack, often faster in practice

```python
def fib_tabulation(n):
    if n <= 1:
        return n
    dp = [0] * (n + 1)
    dp[1] = 1
    for i in range(2, n + 1):
        dp[i] = dp[i-1] + dp[i-2]
    return dp[n]
```

### DP Framework

```
1. DEFINE STATE
   - What information do we need to solve a subproblem?
   - What are the dimensions? (dp[i], dp[i][j], etc.)
   - What does dp[i] represent?

2. IDENTIFY TRANSITIONS
   - How do we compute dp[i] from smaller subproblems?
   - What are the choices at each step?
   - Write the recurrence relation

3. ESTABLISH BASE CASES
   - What are the trivially solvable cases?
   - Initialize dp array accordingly

4. DETERMINE ORDER
   - What order do we fill the DP table?
   - Ensure subproblems are solved before they're needed

5. EXTRACT ANSWER
   - Where is the final answer? dp[n]? max(dp)? dp[n][m]?
```

---

## 5. Diagrams / Visualizations

### DP Table Filling Pattern

```
1D DP (left to right):
dp: [base] → [compute from left] → [compute from left] → [answer]
     [0]        [1]                   [2]                  [n]

2D DP (row by row, left to right):
    j→  0   1   2   3
i   ┌───┬───┬───┬───┐
↓ 0 │ B │ ← │ ← │ ← │   B = base case
  1 │ ↑ │ ↖ │ ← │ ← │   ← = depends on left
  2 │ ↑ │ ↖ │ ↖ │ ← │   ↑ = depends on above
  3 │ ↑ │ ↖ │ ↖ │ A │   ↖ = depends on both
    └───┴───┴───┴───┘   A = answer
```

### Classic DP Problems Categorized

```
                        DP Problems
                             │
    ┌────────────┬──────────┼──────────┬────────────┐
    │            │          │          │            │
 1D Linear    2D Grid    String DP  Interval DP  State
    │            │          │          │       Compression
    │            │          │          │            │
┌───┴───┐   ┌───┴───┐  ┌───┴───┐  ┌───┴───┐   ┌───┴───┐
Fibonacci  Unique   Edit      Matrix   TSP
Climbing   Paths    Distance  Chain    Hamiltonian
House      Min Path LCS       Burst    Subset DP
Robber     Cost     Palindrome Balloons
```

---

## 6. Implementation (Python)

### Template: Top-Down (Memoization)

```python
from functools import lru_cache
from typing import List

def dp_template_memo(problem_input):
    """
    Top-down DP template using @lru_cache.
    
    Pros: More intuitive, only computes needed subproblems
    Cons: Recursion stack, function call overhead
    """
    
    @lru_cache(maxsize=None)
    def dp(state):
        # Base case
        if is_base_case(state):
            return base_value
        
        # Recurrence: try all choices
        result = initial_value  # 0 for sum, inf for min, -inf for max
        for choice in get_choices(state):
            next_state = transition(state, choice)
            result = combine(result, dp(next_state))
        
        return result
    
    return dp(initial_state)


# Example: Climbing Stairs (LeetCode 70)
def climb_stairs(n: int) -> int:
    """
    Count ways to climb n stairs (1 or 2 steps at a time).
    
    State: dp(i) = number of ways to reach step i
    Transition: dp(i) = dp(i-1) + dp(i-2)
    Base: dp(0) = 1, dp(1) = 1
    
    >>> climb_stairs(3)
    3
    >>> climb_stairs(5)
    8
    """
    @lru_cache(maxsize=None)
    def dp(i):
        if i <= 1:
            return 1
        return dp(i - 1) + dp(i - 2)
    
    return dp(n)
```

### Template: Bottom-Up (Tabulation)

```python
def dp_template_tabulation(problem_input):
    """
    Bottom-up DP template using iterative table filling.
    
    Pros: No recursion stack, often faster
    Cons: Must determine filling order, computes all subproblems
    """
    n = len(problem_input)
    
    # Initialize DP table
    dp = [initial_value] * (n + 1)  # or 2D: [[]] * (m+1)
    
    # Base cases
    dp[0] = base_value_0
    # dp[1] = base_value_1 if needed
    
    # Fill table
    for i in range(start, n + 1):
        for choice in get_choices(i):
            prev_state = transition(i, choice)
            dp[i] = combine(dp[i], dp[prev_state])
    
    return dp[n]  # or max(dp), or specific cell


# Example: Climbing Stairs (Bottom-Up)
def climb_stairs_tabulation(n: int) -> int:
    """
    >>> climb_stairs_tabulation(3)
    3
    """
    if n <= 1:
        return 1
    
    dp = [0] * (n + 1)
    dp[0] = dp[1] = 1
    
    for i in range(2, n + 1):
        dp[i] = dp[i - 1] + dp[i - 2]
    
    return dp[n]


# Space-optimized version
def climb_stairs_optimized(n: int) -> int:
    """
    Space: O(1) instead of O(n)
    Only need previous two values.
    
    >>> climb_stairs_optimized(5)
    8
    """
    if n <= 1:
        return 1
    
    prev2, prev1 = 1, 1
    
    for i in range(2, n + 1):
        current = prev1 + prev2
        prev2 = prev1
        prev1 = current
    
    return prev1
```

### Classic DP Problems

#### House Robber (1D DP)

```python
def rob(nums: List[int]) -> int:
    """
    Rob houses (can't rob adjacent). Maximize money.
    
    State: dp[i] = max money robbing houses 0..i
    Transition: dp[i] = max(dp[i-1], dp[i-2] + nums[i])
    - Skip house i: dp[i-1]
    - Rob house i: dp[i-2] + nums[i]
    
    Time: O(n), Space: O(1)
    
    >>> rob([2, 7, 9, 3, 1])
    12
    """
    if not nums:
        return 0
    if len(nums) == 1:
        return nums[0]
    
    # Space-optimized
    prev2 = 0  # dp[i-2]
    prev1 = nums[0]  # dp[i-1]
    
    for i in range(1, len(nums)):
        current = max(prev1, prev2 + nums[i])
        prev2 = prev1
        prev1 = current
    
    return prev1
```

#### Unique Paths (2D DP)

```python
def unique_paths(m: int, n: int) -> int:
    """
    Count paths from top-left to bottom-right (only right/down).
    
    State: dp[i][j] = number of paths to reach (i, j)
    Transition: dp[i][j] = dp[i-1][j] + dp[i][j-1]
    Base: dp[0][j] = dp[i][0] = 1
    
    Time: O(m×n), Space: O(n)
    
    >>> unique_paths(3, 7)
    28
    """
    # Space-optimized: only need previous row
    dp = [1] * n
    
    for i in range(1, m):
        for j in range(1, n):
            dp[j] = dp[j] + dp[j - 1]  # above + left
    
    return dp[n - 1]
```

#### Longest Increasing Subsequence (LIS)

```python
def length_of_lis(nums: List[int]) -> int:
    """
    Find length of longest increasing subsequence.
    
    State: dp[i] = length of LIS ending at index i
    Transition: dp[i] = max(dp[j] + 1) for all j < i where nums[j] < nums[i]
    Answer: max(dp)
    
    Time: O(n²), Space: O(n)
    
    >>> length_of_lis([10, 9, 2, 5, 3, 7, 101, 18])
    4
    """
    if not nums:
        return 0
    
    n = len(nums)
    dp = [1] * n  # Every element is LIS of length 1 by itself
    
    for i in range(1, n):
        for j in range(i):
            if nums[j] < nums[i]:
                dp[i] = max(dp[i], dp[j] + 1)
    
    return max(dp)


def length_of_lis_binary_search(nums: List[int]) -> int:
    """
    Optimized LIS using binary search.
    
    Maintain array of smallest ending elements for each length.
    
    Time: O(n log n), Space: O(n)
    
    >>> length_of_lis_binary_search([10, 9, 2, 5, 3, 7, 101, 18])
    4
    """
    import bisect
    
    tails = []  # tails[i] = smallest ending element for LIS of length i+1
    
    for num in nums:
        pos = bisect.bisect_left(tails, num)
        if pos == len(tails):
            tails.append(num)  # Extend LIS
        else:
            tails[pos] = num  # Replace with smaller ending
    
    return len(tails)
```

#### Coin Change (Unbounded Knapsack)

```python
def coin_change(coins: List[int], amount: int) -> int:
    """
    Minimum coins to make amount. Return -1 if impossible.
    
    State: dp[i] = min coins to make amount i
    Transition: dp[i] = min(dp[i - coin] + 1) for each coin
    Base: dp[0] = 0
    
    Time: O(amount × coins), Space: O(amount)
    
    >>> coin_change([1, 2, 5], 11)
    3
    >>> coin_change([2], 3)
    -1
    """
    dp = [float('inf')] * (amount + 1)
    dp[0] = 0
    
    for i in range(1, amount + 1):
        for coin in coins:
            if coin <= i and dp[i - coin] != float('inf'):
                dp[i] = min(dp[i], dp[i - coin] + 1)
    
    return dp[amount] if dp[amount] != float('inf') else -1
```

#### Edit Distance (2D String DP)

```python
def min_distance(word1: str, word2: str) -> int:
    """
    Minimum edit operations (insert, delete, replace) to convert word1 to word2.
    
    State: dp[i][j] = min edits to convert word1[0:i] to word2[0:j]
    Transition:
    - If word1[i-1] == word2[j-1]: dp[i][j] = dp[i-1][j-1]
    - Else: dp[i][j] = 1 + min(
        dp[i-1][j],    # delete from word1
        dp[i][j-1],    # insert into word1
        dp[i-1][j-1]   # replace
      )
    
    Time: O(m×n), Space: O(n)
    
    >>> min_distance("horse", "ros")
    3
    """
    m, n = len(word1), len(word2)
    
    # Space-optimized: only need previous row
    prev = list(range(n + 1))  # Base: converting "" to word2[0:j]
    
    for i in range(1, m + 1):
        curr = [i] + [0] * n  # Base: converting word1[0:i] to ""
        
        for j in range(1, n + 1):
            if word1[i - 1] == word2[j - 1]:
                curr[j] = prev[j - 1]
            else:
                curr[j] = 1 + min(prev[j], curr[j - 1], prev[j - 1])
        
        prev = curr
    
    return prev[n]
```

---

## 7. Step-by-Step Worked Example

### Problem: Maximum Subarray (Kadane's Algorithm)

**Problem**: Find the contiguous subarray with the maximum sum.

```
nums = [-2, 1, -3, 4, -1, 2, 1, -5, 4]

Expected: 6 (subarray [4, -1, 2, 1])
```

**State Definition**: `dp[i]` = maximum sum of subarray ending at index i

**Recurrence**: 
```
dp[i] = max(nums[i], dp[i-1] + nums[i])
       = max(start fresh at i, extend previous subarray)
```

**Base Case**: `dp[0] = nums[0]`

```python
def max_subarray(nums: List[int]) -> int:
    """
    Kadane's Algorithm for maximum subarray sum.
    
    >>> max_subarray([-2, 1, -3, 4, -1, 2, 1, -5, 4])
    6
    """
    max_sum = current = nums[0]
    
    for i in range(1, len(nums)):
        current = max(nums[i], current + nums[i])
        max_sum = max(max_sum, current)
    
    return max_sum
```

**Trace**:
```
i=0: nums[0]=-2, current=-2, max_sum=-2
i=1: nums[1]=1, current=max(1, -2+1)=1, max_sum=1
i=2: nums[2]=-3, current=max(-3, 1-3)=-2, max_sum=1
i=3: nums[3]=4, current=max(4, -2+4)=4, max_sum=4
i=4: nums[4]=-1, current=max(-1, 4-1)=3, max_sum=4
i=5: nums[5]=2, current=max(2, 3+2)=5, max_sum=5
i=6: nums[6]=1, current=max(1, 5+1)=6, max_sum=6
i=7: nums[7]=-5, current=max(-5, 6-5)=1, max_sum=6
i=8: nums[8]=4, current=max(4, 1+4)=5, max_sum=6

Answer: 6 ✓
```

---

## 8. Common Mistakes

1. **Wrong state definition**
   ```python
   # Mistake: dp[i] = max sum in nums[0:i] (doesn't capture ending position)
   # This doesn't tell us whether to extend or start fresh
   
   # Correct: dp[i] = max sum of subarray ENDING at i
   # Now we can decide: extend (dp[i-1] + nums[i]) or start fresh (nums[i])
   ```

2. **Missing base cases**
   ```python
   # Wrong: forgetting to handle empty or single element
   def dp_func(nums):
       dp = [0] * len(nums)
       for i in range(1, len(nums)):  # What about dp[0]?
           ...
   
   # Correct: always initialize base cases
   dp[0] = nums[0]  # or whatever the base case is
   ```

3. **Wrong recurrence direction**
   ```python
   # Wrong: using dp[i+1] before it's computed (in bottom-up)
   for i in range(n):
       dp[i] = dp[i+1] + something  # dp[i+1] not computed yet!
   
   # Correct: use only already-computed values
   for i in range(n-1, -1, -1):  # or change recurrence
       dp[i] = dp[i+1] + something
   ```

4. **Integer overflow / wrong initial value**
   ```python
   # Wrong for minimum DP
   dp[i] = min(dp[i], something)  # dp[i] starts as 0, always wins!
   
   # Correct
   dp = [float('inf')] * n
   dp[0] = 0  # base case
   ```

5. **Off-by-one errors in 2D DP**
   ```python
   # When using 1-indexed DP array
   # dp[i][j] corresponds to word1[i-1] and word2[j-1]
   if word1[i-1] == word2[j-1]:  # NOT word1[i] == word2[j]
   ```

---

## 9. Variations & Optimizations

### Space Optimization Techniques

```python
# 1D DP: Keep only needed previous states
# Instead of dp = [0] * n, use prev and curr variables

# 2D DP: Keep only previous row
# Instead of dp[m][n], use prev_row and curr_row

# Diagonal DP: Keep diagonal elements only
# For problems like longest palindromic substring
```

### State Compression

```python
# Use bitmask for subset state
# dp[mask] where mask represents which elements are used

def tsp(graph):
    """Traveling Salesman with bitmask DP."""
    n = len(graph)
    ALL_VISITED = (1 << n) - 1
    
    @lru_cache(maxsize=None)
    def dp(mask, pos):
        if mask == ALL_VISITED:
            return graph[pos][0]  # Return to start
        
        result = float('inf')
        for next_city in range(n):
            if not (mask & (1 << next_city)):  # Not visited
                new_mask = mask | (1 << next_city)
                result = min(result, graph[pos][next_city] + dp(new_mask, next_city))
        
        return result
    
    return dp(1, 0)  # Start at city 0, only city 0 visited
```

---

## 10. Interview Tips

### How to Approach DP Problems

```
1. START SIMPLE
   - Can I solve with brute force/recursion?
   - What are the choices at each step?

2. IDENTIFY SUBPROBLEMS
   - What information defines a subproblem?
   - What's the minimum state needed?

3. FIND RECURRENCE
   - How do subproblems relate?
   - What are the choices and their costs?

4. DETERMINE ORDER
   - What order to compute subproblems?
   - What are the base cases?

5. OPTIMIZE
   - Can I reduce space?
   - Is there a better algorithm? (Binary search, etc.)
```

### Common State Definitions

```python
# 1D: Position/index
dp[i] = answer for first i elements, or ending at i

# 2D: Two indices or index + resource
dp[i][j] = answer for subarray/string from i to j
dp[i][k] = answer at position i with k resources used

# Bitmask: Subset state
dp[mask] = answer when elements in mask are processed

# Multiple dimensions: Multiple constraints
dp[i][j][k] = answer at position i, with j of X, k of Y
```

### Quick Pattern Recognition

```
"Number of ways" → Counting DP (add options)
"Minimum/Maximum" → Optimization DP (min/max of options)
"Is it possible" → Boolean DP (OR of options)
"Longest/Shortest" → Usually 1D or 2D DP
"Subset" → May need bitmask DP
"Two strings" → Usually 2D DP
```

---

## 11. Related Patterns

| Pattern | Characteristics | Examples |
|---------|-----------------|----------|
| Linear DP | State depends on previous elements | Fibonacci, House Robber |
| Grid DP | State is (row, col) | Unique Paths, Min Path Sum |
| String DP | Two strings/indices | Edit Distance, LCS |
| Interval DP | Subarray [i, j] | Matrix Chain, Burst Balloons |
| Tree DP | DP on tree structure | House Robber III |
| Bitmask DP | Subset as bitmask | TSP, Partition |

---

## 12. References

- **CLRS**: Chapter 15 (Dynamic Programming)
- **cp-algorithms**: [DP Introduction](https://cp-algorithms.com/dynamic_programming/intro-to-dp.html)
- **LeetCode**: DP tag (300+ problems)
- **Grokking**: Dynamic Programming Patterns

---

## 13. Practice Problems

### Linear DP
| # | Problem | Key Pattern | LeetCode |
|---|---------|-------------|----------|
| 1 | Climbing Stairs | Fibonacci-like | [70](https://leetcode.com/problems/climbing-stairs/) |
| 2 | House Robber | Skip or take | [198](https://leetcode.com/problems/house-robber/) |
| 3 | Maximum Subarray | Kadane | [53](https://leetcode.com/problems/maximum-subarray/) |
| 4 | Decode Ways | Count valid | [91](https://leetcode.com/problems/decode-ways/) |
| 5 | Jump Game | Reachability | [55](https://leetcode.com/problems/jump-game/) |
| 6 | Longest Increasing Subsequence | LIS | [300](https://leetcode.com/problems/longest-increasing-subsequence/) |

### 2D/Grid DP
| # | Problem | Key Pattern | LeetCode |
|---|---------|-------------|----------|
| 7 | Unique Paths | Count paths | [62](https://leetcode.com/problems/unique-paths/) |
| 8 | Minimum Path Sum | Grid min cost | [64](https://leetcode.com/problems/minimum-path-sum/) |
| 9 | Triangle | Variable width grid | [120](https://leetcode.com/problems/triangle/) |
| 10 | Dungeon Game | Reverse DP | [174](https://leetcode.com/problems/dungeon-game/) |

### String DP
| # | Problem | Key Pattern | LeetCode |
|---|---------|-------------|----------|
| 11 | Edit Distance | Two string comparison | [72](https://leetcode.com/problems/edit-distance/) |
| 12 | Longest Common Subsequence | LCS | [1143](https://leetcode.com/problems/longest-common-subsequence/) |
| 13 | Longest Palindromic Substring | Expand / DP | [5](https://leetcode.com/problems/longest-palindromic-substring/) |
| 14 | Word Break | Can partition | [139](https://leetcode.com/problems/word-break/) |

### Knapsack Variants
| # | Problem | Key Pattern | LeetCode |
|---|---------|-------------|----------|
| 15 | Coin Change | Unbounded | [322](https://leetcode.com/problems/coin-change/) |
| 16 | Coin Change II | Count ways | [518](https://leetcode.com/problems/coin-change-ii/) |
| 17 | Partition Equal Subset Sum | 0/1 Knapsack | [416](https://leetcode.com/problems/partition-equal-subset-sum/) |
| 18 | Target Sum | 0/1 with +/- | [494](https://leetcode.com/problems/target-sum/) |

### Advanced DP
| # | Problem | Key Pattern | LeetCode |
|---|---------|-------------|----------|
| 19 | Burst Balloons | Interval DP | [312](https://leetcode.com/problems/burst-balloons/) |
| 20 | Regular Expression Matching | Pattern matching | [10](https://leetcode.com/problems/regular-expression-matching/) |

---

## 14. Key Takeaways

1. **DP = Recursion + Memoization**: Break problem into overlapping subproblems

2. **State definition is key**: Choose minimal state that captures all needed information

3. **Two approaches**: Top-down (memoization) is often easier to write; bottom-up can be faster and allows space optimization

4. **Common patterns**: Linear, grid, string, interval, tree, bitmask

5. **Space optimization**: Many problems can be optimized from O(n²) to O(n) or O(1)

6. **Practice identifying DP**: Look for optimal substructure and overlapping subproblems

---

## 15. Spaced Repetition Prompts

1. **Q**: What are the two defining properties of DP problems?
   **A**: Optimal substructure (optimal solution contains optimal sub-solutions) and overlapping subproblems (same subproblems solved multiple times).

2. **Q**: What's the difference between memoization and tabulation?
   **A**: Memoization is top-down (recursive with caching), tabulation is bottom-up (iterative table filling). Memoization only computes needed subproblems; tabulation computes all subproblems.

3. **Q**: What are the 5 steps of the DP framework?
   **A**: 1) Define state, 2) Identify transitions/recurrence, 3) Establish base cases, 4) Determine computation order, 5) Extract final answer.

4. **Q**: How do you optimize 2D DP to 1D space?
   **A**: If dp[i][j] only depends on dp[i-1][...], keep only previous row. Use two arrays (prev, curr) or single array with careful ordering.

5. **Q**: What's the state and recurrence for Coin Change?
   **A**: State: dp[i] = min coins for amount i. Recurrence: dp[i] = min(dp[i-coin] + 1) for each coin ≤ i. Base: dp[0] = 0.
