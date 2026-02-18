---
title: "Knapsack Variants"
topic: "Knapsack Dynamic Programming"
difficulty: "Medium to Hard"
tags: ["dp", "knapsack", "0-1-knapsack", "unbounded", "subset-sum"]
status: "complete"
weight: 4
---

# Knapsack Variants

## Summary / TL;DR

The Knapsack family of problems involves selecting items with constraints (weight, count, capacity) to optimize some objective (value, count, feasibility). Understanding the three main variants unlocks dozens of DP problems.

**Three Main Variants:**
1. **0/1 Knapsack:** Each item used at most once
2. **Unbounded Knapsack:** Each item can be used unlimited times
3. **Bounded Knapsack:** Each item has a specific quantity limit

---

## When to Use

- **Selection with capacity constraint:** Choose items within weight/cost limit
- **Subset sum variants:** Can we make target sum?
- **Counting combinations:** How many ways to reach target?
- **Partition problems:** Divide array into equal/specific parts

### Pattern Recognition

| Clue | Variant |
|------|---------|
| "Each item can be used once" | 0/1 Knapsack |
| "Unlimited supply" | Unbounded |
| "Limited quantity per item" | Bounded |
| "Subset with target sum" | 0/1 Knapsack |
| "Coins with unlimited supply" | Unbounded |
| "Minimize/maximize selection" | Optimization knapsack |

---

## Big-O Complexity

| Variant | Time | Space | Space Optimized |
|---------|------|-------|-----------------|
| 0/1 Knapsack | O(n×W) | O(n×W) | O(W) |
| Unbounded | O(n×W) | O(W) | Already O(W) |
| Bounded | O(n×W×k) | O(W) | O(W) with optimization |

Where n = items, W = capacity, k = max quantity per item.

---

## Core Implementation Templates

### Template 1: 0/1 Knapsack (Classic)

```python
from typing import List

def knapsack_01(weights: List[int], values: List[int], capacity: int) -> int:
    """
    0/1 Knapsack: Each item used at most once.
    Maximize total value within capacity.
    
    dp[i][w] = max value using first i items with capacity w
    
    Time: O(n*W), Space: O(W)
    """
    n = len(weights)
    
    # Space-optimized: only need previous row
    # IMPORTANT: Iterate capacity in REVERSE for 0/1
    dp = [0] * (capacity + 1)
    
    for i in range(n):
        # Reverse order to avoid using same item twice
        for w in range(capacity, weights[i] - 1, -1):
            dp[w] = max(dp[w], dp[w - weights[i]] + values[i])
    
    return dp[capacity]


def knapsack_01_with_items(weights: List[int], values: List[int], 
                           capacity: int) -> tuple:
    """
    Return max value AND selected items.
    
    Time: O(n*W), Space: O(n*W)
    """
    n = len(weights)
    dp = [[0] * (capacity + 1) for _ in range(n + 1)]
    
    for i in range(1, n + 1):
        for w in range(capacity + 1):
            dp[i][w] = dp[i-1][w]  # Don't take item i
            if weights[i-1] <= w:
                dp[i][w] = max(dp[i][w], 
                              dp[i-1][w - weights[i-1]] + values[i-1])
    
    # Backtrack to find selected items
    selected = []
    w = capacity
    for i in range(n, 0, -1):
        if dp[i][w] != dp[i-1][w]:
            selected.append(i - 1)  # Item index (0-based)
            w -= weights[i-1]
    
    return dp[n][capacity], selected[::-1]
```

### Template 2: Subset Sum (0/1 Variant)

```python
def can_partition(nums: List[int]) -> bool:
    """
    Can array be partitioned into two equal sum subsets?
    This is subset sum where target = total_sum / 2.
    
    Time: O(n*sum), Space: O(sum)
    """
    total = sum(nums)
    
    if total % 2 != 0:
        return False
    
    target = total // 2
    dp = [False] * (target + 1)
    dp[0] = True
    
    for num in nums:
        # Reverse to ensure each num used once
        for s in range(target, num - 1, -1):
            dp[s] = dp[s] or dp[s - num]
    
    return dp[target]


def subset_sum_count(nums: List[int], target: int) -> int:
    """
    Count subsets with given sum.
    
    Time: O(n*target), Space: O(target)
    """
    dp = [0] * (target + 1)
    dp[0] = 1  # Empty subset
    
    for num in nums:
        for s in range(target, num - 1, -1):
            dp[s] += dp[s - num]
    
    return dp[target]


def target_sum_ways(nums: List[int], target: int) -> int:
    """
    Assign + or - to each number to reach target.
    
    Let P = sum of positive, N = sum of negative
    P - N = target
    P + N = total_sum
    => P = (target + total_sum) / 2
    
    This becomes subset sum for P.
    
    Time: O(n*sum), Space: O(sum)
    """
    total = sum(nums)
    
    # Check feasibility
    if (target + total) % 2 != 0 or abs(target) > total:
        return 0
    
    new_target = (target + total) // 2
    
    dp = [0] * (new_target + 1)
    dp[0] = 1
    
    for num in nums:
        for s in range(new_target, num - 1, -1):
            dp[s] += dp[s - num]
    
    return dp[new_target]


def minimum_subset_sum_difference(nums: List[int]) -> int:
    """
    Partition into two subsets with minimum absolute difference.
    
    Find subset with sum closest to total/2.
    
    Time: O(n*sum), Space: O(sum)
    """
    total = sum(nums)
    target = total // 2
    
    dp = [False] * (target + 1)
    dp[0] = True
    
    for num in nums:
        for s in range(target, num - 1, -1):
            dp[s] = dp[s] or dp[s - num]
    
    # Find largest achievable sum <= target
    for s in range(target, -1, -1):
        if dp[s]:
            return total - 2 * s


def last_stone_weight_ii(stones: List[int]) -> int:
    """
    Smash stones, final weight = |stone1 - stone2|.
    Find minimum possible final weight.
    
    Same as minimum subset sum difference!
    
    Time: O(n*sum), Space: O(sum)
    """
    total = sum(stones)
    target = total // 2
    
    dp = [False] * (target + 1)
    dp[0] = True
    
    for stone in stones:
        for s in range(target, stone - 1, -1):
            dp[s] = dp[s] or dp[s - stone]
    
    for s in range(target, -1, -1):
        if dp[s]:
            return total - 2 * s
```

### Template 3: Unbounded Knapsack

```python
def knapsack_unbounded(weights: List[int], values: List[int], 
                       capacity: int) -> int:
    """
    Unbounded Knapsack: Each item can be used unlimited times.
    
    Key difference from 0/1: Iterate capacity FORWARD.
    
    Time: O(n*W), Space: O(W)
    """
    dp = [0] * (capacity + 1)
    
    for w in range(1, capacity + 1):
        for i in range(len(weights)):
            if weights[i] <= w:
                dp[w] = max(dp[w], dp[w - weights[i]] + values[i])
    
    return dp[capacity]


def coin_change_min_coins(coins: List[int], amount: int) -> int:
    """
    Minimum coins to make amount. Unlimited supply of each coin.
    
    Time: O(n*amount), Space: O(amount)
    """
    dp = [float('inf')] * (amount + 1)
    dp[0] = 0
    
    for a in range(1, amount + 1):
        for coin in coins:
            if coin <= a and dp[a - coin] != float('inf'):
                dp[a] = min(dp[a], dp[a - coin] + 1)
    
    return dp[amount] if dp[amount] != float('inf') else -1


def coin_change_ways(coins: List[int], amount: int) -> int:
    """
    Number of COMBINATIONS to make amount (order doesn't matter).
    
    Key: Loop coins OUTER, amount INNER to avoid counting permutations.
    
    Time: O(n*amount), Space: O(amount)
    """
    dp = [0] * (amount + 1)
    dp[0] = 1
    
    for coin in coins:  # Outer loop on coins
        for a in range(coin, amount + 1):
            dp[a] += dp[a - coin]
    
    return dp[amount]


def combination_sum_iv(nums: List[int], target: int) -> int:
    """
    Number of PERMUTATIONS to make target (order matters).
    
    Key: Loop target OUTER, nums INNER to count all orderings.
    
    Time: O(n*target), Space: O(target)
    """
    dp = [0] * (target + 1)
    dp[0] = 1
    
    for t in range(1, target + 1):  # Outer loop on target
        for num in nums:
            if num <= t:
                dp[t] += dp[t - num]
    
    return dp[target]


def perfect_squares(n: int) -> int:
    """
    Minimum perfect squares that sum to n.
    
    Unbounded knapsack with items = {1, 4, 9, 16, ...}
    
    Time: O(n*sqrt(n)), Space: O(n)
    """
    dp = [float('inf')] * (n + 1)
    dp[0] = 0
    
    for i in range(1, n + 1):
        j = 1
        while j * j <= i:
            dp[i] = min(dp[i], dp[i - j * j] + 1)
            j += 1
    
    return dp[n]


def integer_break(n: int) -> int:
    """
    Break n into at least two positive integers to maximize product.
    
    Similar to unbounded knapsack.
    
    Time: O(n²), Space: O(n)
    """
    if n <= 3:
        return n - 1
    
    dp = [0] * (n + 1)
    dp[1] = 1
    
    for i in range(2, n + 1):
        for j in range(1, i):
            # j is one part, i-j is other (can be broken further or not)
            dp[i] = max(dp[i], max(j, dp[j]) * max(i - j, dp[i - j]))
    
    return dp[n]
```

### Template 4: Bounded Knapsack

```python
def knapsack_bounded(weights: List[int], values: List[int], 
                     quantities: List[int], capacity: int) -> int:
    """
    Bounded Knapsack: Each item i has quantities[i] copies.
    
    Naive: Treat each copy as separate item → O(n*sum(quantities)*W)
    Better: Binary decomposition → O(n*log(max_qty)*W)
    
    Time: O(n*W*log(max_qty)), Space: O(W)
    """
    dp = [0] * (capacity + 1)
    
    for i in range(len(weights)):
        # Binary decomposition: split quantities[i] into powers of 2
        qty = quantities[i]
        k = 1
        while qty > 0:
            take = min(k, qty)
            w = weights[i] * take
            v = values[i] * take
            
            # 0/1 knapsack for this bundle
            for c in range(capacity, w - 1, -1):
                dp[c] = max(dp[c], dp[c - w] + v)
            
            qty -= take
            k *= 2
    
    return dp[capacity]


def ones_and_zeros(strs: List[str], m: int, n: int) -> int:
    """
    Find largest subset where total 0s <= m and total 1s <= n.
    This is 2D 0/1 knapsack (two constraints).
    
    Time: O(len*m*n), Space: O(m*n)
    """
    dp = [[0] * (n + 1) for _ in range(m + 1)]
    
    for s in strs:
        zeros = s.count('0')
        ones = s.count('1')
        
        # Reverse iteration for 0/1 knapsack
        for i in range(m, zeros - 1, -1):
            for j in range(n, ones - 1, -1):
                dp[i][j] = max(dp[i][j], dp[i - zeros][j - ones] + 1)
    
    return dp[m][n]


def profitable_schemes(n: int, min_profit: int, 
                       group: List[int], profit: List[int]) -> int:
    """
    Count schemes with at most n members and at least minProfit.
    2D knapsack: members constraint + profit target.
    
    Time: O(schemes*n*minProfit), Space: O(n*minProfit)
    """
    MOD = 10**9 + 7
    
    # dp[i][j] = ways to achieve exactly j profit with i members
    dp = [[0] * (min_profit + 1) for _ in range(n + 1)]
    dp[0][0] = 1
    
    for g, p in zip(group, profit):
        # New dp to avoid using same scheme twice
        for i in range(n, g - 1, -1):
            for j in range(min_profit, -1, -1):
                # If we take this scheme
                new_profit = min(j + p, min_profit)
                dp[i][new_profit] = (dp[i][new_profit] + dp[i - g][j]) % MOD
    
    # Sum all ways that achieve min_profit with any number of members
    result = 0
    for i in range(n + 1):
        result = (result + dp[i][min_profit]) % MOD
    
    return result
```

### Template 5: Rod Cutting

```python
def rod_cutting(prices: List[int], n: int) -> int:
    """
    Cut rod of length n to maximize profit.
    prices[i] = price for length i+1.
    
    This is unbounded knapsack where:
    - Items = lengths 1 to n
    - Values = prices
    - Capacity = rod length n
    
    Time: O(n²), Space: O(n)
    """
    dp = [0] * (n + 1)
    
    for length in range(1, n + 1):
        for cut in range(1, length + 1):
            if cut <= len(prices):
                dp[length] = max(dp[length], dp[length - cut] + prices[cut - 1])
    
    return dp[n]


def rod_cutting_with_cuts(prices: List[int], n: int) -> tuple:
    """
    Return max profit AND the cuts made.
    
    Time: O(n²), Space: O(n)
    """
    dp = [0] * (n + 1)
    parent = [0] * (n + 1)  # Track which cut was made
    
    for length in range(1, n + 1):
        for cut in range(1, length + 1):
            if cut <= len(prices):
                if dp[length - cut] + prices[cut - 1] > dp[length]:
                    dp[length] = dp[length - cut] + prices[cut - 1]
                    parent[length] = cut
    
    # Reconstruct cuts
    cuts = []
    remaining = n
    while remaining > 0:
        cuts.append(parent[remaining])
        remaining -= parent[remaining]
    
    return dp[n], cuts
```

### Template 6: Partition Equal Subset Sum Variations

```python
def can_partition_k_subsets(nums: List[int], k: int) -> bool:
    """
    Can array be partitioned into k equal sum subsets?
    
    Uses bitmask DP or backtracking.
    
    Time: O(n * 2^n), Space: O(2^n)
    """
    total = sum(nums)
    if total % k != 0:
        return False
    
    target = total // k
    n = len(nums)
    
    # dp[mask] = remaining sum in current subset
    # -1 means impossible
    dp = [-1] * (1 << n)
    dp[0] = 0
    
    for mask in range(1 << n):
        if dp[mask] == -1:
            continue
        
        for i in range(n):
            if mask & (1 << i):
                continue
            
            if nums[i] + dp[mask] <= target:
                new_mask = mask | (1 << i)
                dp[new_mask] = (dp[mask] + nums[i]) % target
    
    return dp[(1 << n) - 1] == 0


def partition_to_k_equal_sum_subsets_backtrack(nums: List[int], k: int) -> bool:
    """
    Backtracking approach (often faster for small k).
    
    Time: O(k * 2^n) average, Space: O(n)
    """
    total = sum(nums)
    if total % k != 0:
        return False
    
    target = total // k
    nums.sort(reverse=True)  # Optimization: try larger first
    
    if nums[0] > target:
        return False
    
    buckets = [0] * k
    
    def backtrack(index: int) -> bool:
        if index == len(nums):
            return all(b == target for b in buckets)
        
        seen = set()  # Avoid duplicate bucket states
        for i in range(k):
            if buckets[i] in seen:
                continue
            if buckets[i] + nums[index] <= target:
                seen.add(buckets[i])
                buckets[i] += nums[index]
                if backtrack(index + 1):
                    return True
                buckets[i] -= nums[index]
        
        return False
    
    return backtrack(0)
```

---

## Worked Examples

### Example 1: 0/1 Knapsack

**Problem:** Items with weights [2, 3, 4, 5] and values [3, 4, 5, 6], capacity = 5

```
Items: w=[2,3,4,5], v=[3,4,5,6], W=5

Initialize: dp = [0, 0, 0, 0, 0, 0]

Item 0 (w=2, v=3): (reverse from 5 to 2)
  dp[5] = max(0, dp[3]+3) = max(0, 0+3) = 3
  dp[4] = max(0, dp[2]+3) = max(0, 0+3) = 3
  dp[3] = max(0, dp[1]+3) = max(0, 0+3) = 3
  dp[2] = max(0, dp[0]+3) = max(0, 0+3) = 3
  dp = [0, 0, 3, 3, 3, 3]

Item 1 (w=3, v=4): (reverse from 5 to 3)
  dp[5] = max(3, dp[2]+4) = max(3, 3+4) = 7
  dp[4] = max(3, dp[1]+4) = max(3, 0+4) = 4
  dp[3] = max(3, dp[0]+4) = max(3, 0+4) = 4
  dp = [0, 0, 3, 4, 4, 7]

Item 2 (w=4, v=5): (reverse from 5 to 4)
  dp[5] = max(7, dp[1]+5) = max(7, 0+5) = 7
  dp[4] = max(4, dp[0]+5) = max(4, 0+5) = 5
  dp = [0, 0, 3, 4, 5, 7]

Item 3 (w=5, v=6): (reverse from 5 to 5)
  dp[5] = max(7, dp[0]+6) = max(7, 0+6) = 7
  dp = [0, 0, 3, 4, 5, 7]

Answer: dp[5] = 7 (items 0 and 1: w=2+3=5, v=3+4=7)
```

### Example 2: Coin Change Combinations vs Permutations

**Problem:** coins = [1, 2, 5], amount = 5

**Combinations (order doesn't matter):**
```
Loop coins outer:

coin=1: dp = [1, 1, 1, 1, 1, 1]
coin=2: dp = [1, 1, 2, 2, 3, 3]
coin=5: dp = [1, 1, 2, 2, 3, 4]

Combinations: {1,1,1,1,1}, {1,1,1,2}, {1,2,2}, {5} = 4 ways ✓
```

**Permutations (order matters):**
```
Loop amount outer:

a=1: dp[1] = dp[0] = 1 → [1,1,0,0,0,0]
a=2: dp[2] = dp[1]+dp[0] = 1+1 = 2 → [1,1,2,0,0,0]
a=3: dp[3] = dp[2]+dp[1] = 2+1 = 3 → [1,1,2,3,0,0]
a=4: dp[4] = dp[3]+dp[2] = 3+2 = 5 → [1,1,2,3,5,0]
a=5: dp[5] = dp[4]+dp[3]+dp[0] = 5+3+1 = 9 → [1,1,2,3,5,9]

Permutations: 9 ways (includes [1,1,1,2], [1,1,2,1], [1,2,1,1], [2,1,1,1] as different)
```

---

## Loop Order Cheat Sheet

| Problem Type | Outer Loop | Inner Loop | Direction |
|--------------|------------|------------|-----------|
| 0/1 Knapsack | Items | Capacity | **Reverse** |
| Unbounded (value) | Capacity | Items | Forward |
| Combinations (coins) | Items | Amount | Forward |
| Permutations (coins) | Amount | Items | Forward |
| Subset Sum | Items | Target | **Reverse** |

**Why Reverse for 0/1?**
- Ensures each item used at most once
- dp[w] computed before dp[w - weight[i]]
- Forward would allow same item multiple times

---

## Edge Cases & Gotchas

```python
# 1. Zero capacity/target
def handle_zero_target(target: int) -> int:
    if target == 0:
        return 1  # Empty subset for counting problems
        return 0  # Zero value for optimization problems

# 2. Negative numbers in subset sum
def subset_sum_with_negatives(nums: List[int], target: int) -> bool:
    # Shift everything to be non-negative
    min_val = min(nums)
    if min_val < 0:
        nums = [x - min_val for x in nums]
        target -= min_val * len(nums)
    # Now proceed with standard subset sum

# 3. Large capacity
# If capacity >> sum of weights, consider sparse DP or meet-in-middle

# 4. Floating point weights/values
# Usually need to scale to integers

# 5. Empty items list
def handle_empty_items(weights: List[int], capacity: int) -> int:
    if not weights:
        return 0

# 6. Target Sum with all zeros
def target_sum_zeros(nums: List[int], target: int) -> int:
    zeros = nums.count(0)
    # Each zero can be + or -: 2^zeros ways for zero sum
    # Multiply by ways to make target from non-zeros
```

---

## Interview Tips & Communication

### Framework for Knapsack Problems

1. **Identify the variant:**
   - "Each item once" → 0/1
   - "Unlimited supply" → Unbounded
   - "Limited quantities" → Bounded

2. **Define state:**
   - "dp[w] = max value achievable with capacity w"
   - Or: "dp[w] = can we achieve sum w?"

3. **Determine loop order:**
   - 0/1: Reverse capacity
   - Unbounded: Forward capacity
   - Combinations: Items outer
   - Permutations: Target outer

4. **Space optimization:**
   - 2D → 1D by careful loop ordering

### Key Phrases

- "This is a classic 0/1 knapsack where each item can be used at most once."
- "I'll iterate capacity in reverse to ensure items aren't reused."
- "For counting combinations, I loop items outer to avoid counting orderings."
- "This transforms to subset sum by setting target to (total + target) / 2."

---

## Practice Problems

### 0/1 Knapsack

| # | Problem | Key Concept |
|---|---------|-------------|
| 1 | [Partition Equal Subset Sum](https://leetcode.com/problems/partition-equal-subset-sum/) | Subset sum |
| 2 | [Target Sum](https://leetcode.com/problems/target-sum/) | Transform to subset |
| 3 | [Last Stone Weight II](https://leetcode.com/problems/last-stone-weight-ii/) | Min subset diff |
| 4 | [Ones and Zeroes](https://leetcode.com/problems/ones-and-zeroes/) | 2D knapsack |
| 5 | [Profitable Schemes](https://leetcode.com/problems/profitable-schemes/) | 2D with threshold |
| 6 | [Partition to K Equal Sum Subsets](https://leetcode.com/problems/partition-to-k-equal-sum-subsets/) | Backtrack/bitmask |

### Unbounded Knapsack

| # | Problem | Key Concept |
|---|---------|-------------|
| 7 | [Coin Change](https://leetcode.com/problems/coin-change/) | Min coins |
| 8 | [Coin Change II](https://leetcode.com/problems/coin-change-ii/) | Count combinations |
| 9 | [Combination Sum IV](https://leetcode.com/problems/combination-sum-iv/) | Count permutations |
| 10 | [Perfect Squares](https://leetcode.com/problems/perfect-squares/) | Min squares |
| 11 | [Integer Break](https://leetcode.com/problems/integer-break/) | Max product |
| 12 | [Minimum Cost For Tickets](https://leetcode.com/problems/minimum-cost-for-tickets/) | Travel days |

### Bounded Knapsack

| # | Problem | Key Concept |
|---|---------|-------------|
| 13 | [Bounded Knapsack](https://www.geeksforgeeks.org/bounded-knapsack-problem/) | Binary decomposition |
| 14 | [Shopping Offers](https://leetcode.com/problems/shopping-offers/) | Bundles |
| 15 | [Number of Dice Rolls With Target Sum](https://leetcode.com/problems/number-of-dice-rolls-with-target-sum/) | Dice faces |

### Classic Problems

| # | Problem | Key Concept |
|---|---------|-------------|
| 16 | [Rod Cutting](https://www.geeksforgeeks.org/cutting-a-rod-dp-13/) | Unbounded |
| 17 | [Egg Drop](https://leetcode.com/problems/super-egg-drop/) | Two dimensions |
| 18 | [Maximize The Profit](https://leetcode.com/problems/maximize-the-profit-as-the-salesman/) | Weighted intervals |
| 19 | [House Robber](https://leetcode.com/problems/house-robber/) | Skip constraint |
| 20 | [Maximum Profit in Job Scheduling](https://leetcode.com/problems/maximum-profit-in-job-scheduling/) | Weighted scheduling |

---

## References

- CLRS: Chapter 16.2 (0-1 Knapsack Problem)
- [USACO Guide: Knapsack](https://usaco.guide/gold/knapsack)
- [Grokking: Knapsack Patterns](https://www.educative.io/courses/grokking-the-coding-interview)
- [CP Algorithms: Knapsack](https://cp-algorithms.com/dynamic_programming/knapsack.html)
