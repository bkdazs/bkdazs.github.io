---
title: DP with Last Element / Lookback States
topic: Algorithms - Dynamic Programming
difficulty: Medium-Hard
tags: [dp, state-compression, lookback, last-element, history]
status: complete
weight: 18
---

# DP with Last Element / Lookback States

## 1. Overview

### Core Concept
Many DP problems require tracking not just the current position but also information about recent choices. This includes:
- **Last element used**: For alternating/constraint patterns
- **Last k elements**: For sliding window constraints
- **Last value**: For monotonic/ordering constraints

### State Design Patterns
| Pattern | State | Example |
|---------|-------|---------|
| Index + Last | `dp[i][last]` | Paint house |
| Index + Last k | `dp[i][last_k_mask]` | Domino tiling |
| Index + Value | `dp[i][val]` | LIS |
| Index + Count | `dp[i][count]` | K consecutive |

---

## 2. DP with Last Choice

### Paint House / No Adjacent Same

```python
from typing import List
from functools import lru_cache

def paint_house(costs: List[List[int]]) -> int:
    """
    LeetCode 256 - Paint House
    
    Paint n houses with 3 colors, no two adjacent houses same color.
    Minimize total cost.
    
    State: dp[i][c] = min cost to paint houses 0..i with house i painted color c
    
    Time: O(n × 3)
    Space: O(1) with optimization
    """
    if not costs:
        return 0
    
    n = len(costs)
    # dp[c] = min cost ending with color c
    dp = costs[0][:]
    
    for i in range(1, n):
        new_dp = [0, 0, 0]
        new_dp[0] = min(dp[1], dp[2]) + costs[i][0]
        new_dp[1] = min(dp[0], dp[2]) + costs[i][1]
        new_dp[2] = min(dp[0], dp[1]) + costs[i][2]
        dp = new_dp
    
    return min(dp)


def paint_house_ii(costs: List[List[int]]) -> int:
    """
    LeetCode 265 - Paint House II
    
    K colors instead of 3.
    
    Time: O(n × k) with optimization
    """
    if not costs:
        return 0
    
    n, k = len(costs), len(costs[0])
    
    # Track min and second min for O(k) instead of O(k²)
    dp = costs[0][:]
    
    for i in range(1, n):
        # Find min and second min from previous row
        min1_idx = min(range(k), key=lambda x: dp[x])
        min1 = dp[min1_idx]
        min2 = min(dp[j] for j in range(k) if j != min1_idx) if k > 1 else float('inf')
        
        new_dp = [0] * k
        for c in range(k):
            if c == min1_idx:
                new_dp[c] = min2 + costs[i][c]
            else:
                new_dp[c] = min1 + costs[i][c]
        dp = new_dp
    
    return min(dp)


# Test
costs = [[17, 2, 17], [16, 16, 5], [14, 3, 19]]
print(paint_house(costs))  # 10
```

### Wiggle Subsequence

```python
def wiggle_max_length(nums: List[int]) -> int:
    """
    LeetCode 376 - Wiggle Subsequence
    
    Find longest alternating subsequence (up-down or down-up).
    
    State: dp[i][0/1] = length ending at i with last move down(0) or up(1)
    
    Time: O(n)
    """
    if len(nums) < 2:
        return len(nums)
    
    up = 1    # Length of longest ending with up move
    down = 1  # Length of longest ending with down move
    
    for i in range(1, len(nums)):
        if nums[i] > nums[i - 1]:
            up = down + 1
        elif nums[i] < nums[i - 1]:
            down = up + 1
    
    return max(up, down)


def longest_zigzag_path(root) -> int:
    """
    LeetCode 1372 - Longest ZigZag Path in Binary Tree
    
    Alternating left-right path.
    
    State: Track direction of last move.
    """
    max_length = 0
    
    def dfs(node, direction: int, length: int):
        nonlocal max_length
        if not node:
            return
        
        max_length = max(max_length, length)
        
        # direction: 0 = came from left, 1 = came from right
        if direction == 0:  # Last was left, go right
            dfs(node.right, 1, length + 1)
            dfs(node.left, 0, 1)  # Reset
        else:  # Last was right, go left
            dfs(node.left, 0, length + 1)
            dfs(node.right, 1, 1)  # Reset
    
    if root:
        dfs(root.left, 0, 1)
        dfs(root.right, 1, 1)
    
    return max_length
```

---

## 3. DP with Last Value

### Longest Arithmetic Subsequence

```python
def longest_arith_seq_length(nums: List[int]) -> int:
    """
    LeetCode 1027 - Longest Arithmetic Subsequence
    
    State: dp[i][d] = length of arithmetic subsequence ending at i with difference d
    
    Time: O(n²)
    Space: O(n × range(d))
    """
    n = len(nums)
    if n <= 2:
        return n
    
    # dp[i] = {diff: length}
    dp = [{} for _ in range(n)]
    max_len = 2
    
    for i in range(n):
        for j in range(i):
            diff = nums[i] - nums[j]
            # Length at j with this diff, or default 1
            dp[i][diff] = dp[j].get(diff, 1) + 1
            max_len = max(max_len, dp[i][diff])
    
    return max_len


def longest_arith_seq_given_diff(arr: List[int], difference: int) -> int:
    """
    LeetCode 1218 - Longest Arithmetic Subsequence of Given Difference
    
    Fixed difference d.
    
    Time: O(n)
    """
    dp = {}  # dp[val] = length ending with value val
    max_len = 0
    
    for num in arr:
        dp[num] = dp.get(num - difference, 0) + 1
        max_len = max(max_len, dp[num])
    
    return max_len


def number_of_arithmetic_slices(nums: List[int]) -> int:
    """
    LeetCode 446 - Arithmetic Slices II - Subsequence
    
    Count arithmetic subsequences of length >= 3.
    
    Time: O(n²)
    """
    n = len(nums)
    total = 0
    
    # dp[i][d] = count of weak arithmetic subsequences (length >= 2) 
    # ending at i with difference d
    dp = [{} for _ in range(n)]
    
    for i in range(n):
        for j in range(i):
            diff = nums[i] - nums[j]
            
            # Count at j with this diff
            count_j = dp[j].get(diff, 0)
            
            # Add to total: count_j subsequences can be extended to length >= 3
            total += count_j
            
            # Update dp[i][diff]: include subsequences from j, plus new pair (j, i)
            dp[i][diff] = dp[i].get(diff, 0) + count_j + 1
    
    return total
```

---

## 4. DP with Last K Elements

### Delete and Earn

```python
def delete_and_earn(nums: List[int]) -> int:
    """
    LeetCode 740 - Delete and Earn
    
    Picking num deletes all num-1 and num+1.
    
    Transform to House Robber: can't pick adjacent values.
    
    Time: O(n + max(nums))
    """
    if not nums:
        return 0
    
    max_num = max(nums)
    points = [0] * (max_num + 1)
    
    for num in nums:
        points[num] += num
    
    # House robber on points array
    if max_num == 0:
        return 0
    if max_num == 1:
        return points[1]
    
    dp = [0] * (max_num + 1)
    dp[1] = points[1]
    
    for i in range(2, max_num + 1):
        dp[i] = max(dp[i - 1], dp[i - 2] + points[i])
    
    return dp[max_num]


def pizza_with_3n_slices(slices: List[int]) -> int:
    """
    LeetCode 1388 - Pizza With 3n Slices
    
    Pick n non-adjacent slices from circular array of 3n slices.
    
    Time: O(n²)
    """
    def max_sum_non_adjacent(arr: List[int], k: int) -> int:
        """Pick k non-adjacent elements with max sum."""
        n = len(arr)
        # dp[i][j] = max sum picking j elements from first i
        INF = float('inf')
        dp = [[-INF] * (k + 1) for _ in range(n + 1)]
        dp[0][0] = 0
        
        for i in range(1, n + 1):
            for j in range(k + 1):
                # Don't pick i
                dp[i][j] = dp[i - 1][j]
                # Pick i (can't have picked i-1)
                if j > 0 and i >= 2:
                    dp[i][j] = max(dp[i][j], dp[i - 2][j - 1] + arr[i - 1])
                elif j > 0 and i == 1:
                    dp[i][j] = max(dp[i][j], arr[0])
        
        return dp[n][k]
    
    n = len(slices) // 3
    
    # Circular: either exclude first or last element
    return max(
        max_sum_non_adjacent(slices[1:], n),
        max_sum_non_adjacent(slices[:-1], n)
    )
```

---

## 5. Stock Problems (State Machine DP)

### Best Time to Buy/Sell Stock with States

```python
def max_profit_with_cooldown(prices: List[int]) -> int:
    """
    LeetCode 309 - Best Time to Buy and Sell Stock with Cooldown
    
    States: hold, sold, rest
    - hold: holding stock
    - sold: just sold (must rest next)
    - rest: not holding, can buy
    
    Time: O(n)
    """
    if not prices:
        return 0
    
    hold = -prices[0]  # Bought on day 0
    sold = 0           # Can't have sold on day 0
    rest = 0           # Starting state
    
    for i in range(1, len(prices)):
        prev_hold, prev_sold, prev_rest = hold, sold, rest
        
        hold = max(prev_hold, prev_rest - prices[i])  # Keep or buy
        sold = prev_hold + prices[i]                   # Sell
        rest = max(prev_rest, prev_sold)              # Rest or was sold
    
    return max(sold, rest)


def max_profit_k_transactions(k: int, prices: List[int]) -> int:
    """
    LeetCode 188 - Best Time to Buy and Sell Stock IV
    
    At most k transactions.
    
    State: dp[i][j][0/1] = max profit with i days, j transactions used, holding(1) or not(0)
    
    Time: O(nk)
    """
    if not prices or k == 0:
        return 0
    
    n = len(prices)
    
    # Optimization: if k >= n/2, can do unlimited transactions
    if k >= n // 2:
        return sum(max(0, prices[i] - prices[i - 1]) for i in range(1, n))
    
    # dp[j][0] = max profit with j transactions, not holding
    # dp[j][1] = max profit with j transactions, holding
    dp = [[0, -float('inf')] for _ in range(k + 1)]
    
    for price in prices:
        for j in range(k, 0, -1):
            dp[j][0] = max(dp[j][0], dp[j][1] + price)      # Sell
            dp[j][1] = max(dp[j][1], dp[j - 1][0] - price)  # Buy
    
    return dp[k][0]


def max_profit_with_fee(prices: List[int], fee: int) -> int:
    """
    LeetCode 714 - Best Time to Buy and Sell Stock with Transaction Fee
    
    Time: O(n)
    """
    hold = -prices[0]  # After buying
    cash = 0           # Not holding
    
    for i in range(1, len(prices)):
        hold = max(hold, cash - prices[i])
        cash = max(cash, hold + prices[i] - fee)
    
    return cash
```

---

## 6. Consecutive Elements Constraints

### Max Consecutive Ones III

```python
def longest_ones(nums: List[int], k: int) -> int:
    """
    LeetCode 1004 - Max Consecutive Ones III
    
    Max consecutive 1s with at most k flips.
    
    Sliding window approach (not strictly DP).
    
    Time: O(n)
    """
    left = 0
    zeros = 0
    max_len = 0
    
    for right in range(len(nums)):
        if nums[right] == 0:
            zeros += 1
        
        while zeros > k:
            if nums[left] == 0:
                zeros -= 1
            left += 1
        
        max_len = max(max_len, right - left + 1)
    
    return max_len


def longest_subarray_limit(nums: List[int], limit: int) -> int:
    """
    LeetCode 1438 - Longest Continuous Subarray With Absolute Diff Limit
    
    Max-min ≤ limit.
    
    Time: O(n) with monotonic deques
    """
    from collections import deque
    
    max_dq = deque()  # Decreasing
    min_dq = deque()  # Increasing
    left = 0
    result = 0
    
    for right in range(len(nums)):
        while max_dq and nums[right] > max_dq[-1]:
            max_dq.pop()
        while min_dq and nums[right] < min_dq[-1]:
            min_dq.pop()
        
        max_dq.append(nums[right])
        min_dq.append(nums[right])
        
        while max_dq[0] - min_dq[0] > limit:
            if nums[left] == max_dq[0]:
                max_dq.popleft()
            if nums[left] == min_dq[0]:
                min_dq.popleft()
            left += 1
        
        result = max(result, right - left + 1)
    
    return result
```

### K Consecutive Wins/Losses

```python
def k_concatenation_max_sum(arr: List[int], k: int) -> int:
    """
    LeetCode 1191 - Maximum Subarray Sum in Circular Array
    
    Array repeated k times.
    
    Time: O(n)
    """
    MOD = 10**9 + 7
    
    def kadane(nums):
        max_sum = curr = 0
        for num in nums:
            curr = max(0, curr + num)
            max_sum = max(max_sum, curr)
        return max_sum
    
    total = sum(arr)
    single_max = kadane(arr)
    
    if k == 1:
        return single_max % MOD
    
    double_max = kadane(arr + arr)
    
    if total > 0:
        return (double_max + (k - 2) * total) % MOD
    else:
        return double_max % MOD


def find_max_consecutive_k(nums: List[int], k: int) -> int:
    """
    Maximum sum of exactly k consecutive elements.
    
    Time: O(n)
    """
    n = len(nums)
    if n < k:
        return -1
    
    window_sum = sum(nums[:k])
    max_sum = window_sum
    
    for i in range(k, n):
        window_sum += nums[i] - nums[i - k]
        max_sum = max(max_sum, window_sum)
    
    return max_sum
```

---

## 7. Profile DP (Last Row State)

### Domino Tiling

```python
def num_tilings(n: int) -> int:
    """
    LeetCode 790 - Domino and Tromino Tiling
    
    Track last column state (which cells are filled).
    
    Time: O(n)
    """
    MOD = 10**9 + 7
    
    if n <= 2:
        return n
    
    # f[i] = ways to fully tile 2×i board
    # g[i] = ways to tile 2×i board with one cell missing in last column
    
    f = [0] * (n + 1)
    g = [0] * (n + 1)
    
    f[1] = 1
    f[2] = 2
    g[2] = 2  # Two ways to have one missing
    
    for i in range(3, n + 1):
        f[i] = (f[i - 1] + f[i - 2] + g[i - 1]) % MOD
        g[i] = (2 * f[i - 2] + g[i - 1]) % MOD
    
    return f[n]


def strange_printer(s: str) -> int:
    """
    LeetCode 664 - Strange Printer
    
    Track printed state.
    
    Time: O(n³)
    """
    n = len(s)
    
    # Remove consecutive duplicates
    chars = []
    for c in s:
        if not chars or chars[-1] != c:
            chars.append(c)
    
    m = len(chars)
    if m == 0:
        return 0
    
    # dp[i][j] = min turns to print chars[i:j+1]
    dp = [[0] * m for _ in range(m)]
    
    for i in range(m):
        dp[i][i] = 1
    
    for length in range(2, m + 1):
        for i in range(m - length + 1):
            j = i + length - 1
            dp[i][j] = dp[i][j - 1] + 1
            
            for k in range(i, j):
                if chars[k] == chars[j]:
                    dp[i][j] = min(dp[i][j], dp[i][k] + (dp[k + 1][j - 1] if k + 1 <= j - 1 else 0))
    
    return dp[0][m - 1]
```

---

## 8. History-Dependent DP

### Student Attendance Record

```python
def check_record(n: int) -> int:
    """
    LeetCode 552 - Student Attendance Record II
    
    Count valid records: at most 1 'A', no 3+ consecutive 'L'.
    
    State: dp[i][a][l] = ways with i days, a absences, l consecutive lates at end
    
    Time: O(n)
    """
    MOD = 10**9 + 7
    
    # dp[a][l] where a ∈ {0,1}, l ∈ {0,1,2}
    dp = [[0] * 3 for _ in range(2)]
    dp[0][0] = 1  # Start: 0 absences, 0 trailing lates
    
    for _ in range(n):
        new_dp = [[0] * 3 for _ in range(2)]
        
        for a in range(2):
            for l in range(3):
                if dp[a][l] == 0:
                    continue
                
                # Add 'P' (present): reset consecutive lates
                new_dp[a][0] = (new_dp[a][0] + dp[a][l]) % MOD
                
                # Add 'A' (absent): if a == 0
                if a == 0:
                    new_dp[1][0] = (new_dp[1][0] + dp[a][l]) % MOD
                
                # Add 'L' (late): if l < 2
                if l < 2:
                    new_dp[a][l + 1] = (new_dp[a][l + 1] + dp[a][l]) % MOD
        
        dp = new_dp
    
    total = 0
    for a in range(2):
        for l in range(3):
            total = (total + dp[a][l]) % MOD
    
    return total


# Test
print(check_record(2))  # 8
```

---

## 9. Practice Problems

### LeetCode Problems

| # | Problem | Pattern | Difficulty |
|---|---------|---------|------------|
| 121 | [Best Time to Buy Stock](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/) | State DP | Easy |
| 122 | [Best Time to Buy Stock II](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/) | Greedy/DP | Medium |
| 123 | [Best Time to Buy Stock III](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iii/) | K=2 trans | Hard |
| 188 | [Best Time to Buy Stock IV](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iv/) | K trans | Hard |
| 256 | [Paint House](https://leetcode.com/problems/paint-house/) | Last color | Medium |
| 265 | [Paint House II](https://leetcode.com/problems/paint-house-ii/) | K colors | Hard |
| 309 | [Best Time with Cooldown](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/) | State machine | Medium |
| 376 | [Wiggle Subsequence](https://leetcode.com/problems/wiggle-subsequence/) | Up/down | Medium |
| 446 | [Arithmetic Slices II](https://leetcode.com/problems/arithmetic-slices-ii-subsequence/) | Last diff | Hard |
| 552 | [Student Attendance II](https://leetcode.com/problems/student-attendance-record-ii/) | A count + L streak | Hard |
| 664 | [Strange Printer](https://leetcode.com/problems/strange-printer/) | Print state | Hard |
| 714 | [Best Time with Fee](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/) | State DP | Medium |
| 740 | [Delete and Earn](https://leetcode.com/problems/delete-and-earn/) | Transform | Medium |
| 790 | [Domino Tromino Tiling](https://leetcode.com/problems/domino-and-tromino-tiling/) | Profile | Medium |
| 1027 | [Longest Arithmetic Subsequence](https://leetcode.com/problems/longest-arithmetic-subsequence/) | Last diff | Medium |
| 1218 | [Longest Arith Seq Given Diff](https://leetcode.com/problems/longest-arithmetic-subsequence-of-given-difference/) | Value map | Medium |
| 1372 | [Longest ZigZag Path](https://leetcode.com/problems/longest-zigzag-path-in-a-binary-tree/) | Direction | Medium |
| 1388 | [Pizza With 3n Slices](https://leetcode.com/problems/pizza-with-3n-slices/) | Non-adjacent | Hard |

---

## 10. Key Patterns Summary

```
Lookback State DP Framework:

1. Last element/choice matters for constraint:
   dp[i][last] = optimal value with position i and last choice 'last'
   
2. Last k elements matter:
   dp[i][state] where state encodes last k choices (often bitmask)
   
3. Consecutive count matters:
   dp[i][count] = optimal with count consecutive same at position i
   
4. Value-based lookback:
   dp[i][val] or map: dp = {val: best_value}
   
5. State machine (hold/sold/rest):
   Each state = different "mode" of being
   Transitions based on actions

Space Optimization:
- Often only need dp[i-1] → dp[i], so O(states) space
- For value maps, can sometimes use single map updated in place

Time Complexity:
- Usually O(n × states) where states is small
- For value-based: O(n × range(values)) or O(n²) with map
```

---

## 11. References

1. USACO Guide: State Machine DP
2. LeetCode Premium: Stock Problems Series
3. Codeforces: Profile DP Tutorial
4. CP-Algorithms: DP with State Compression
