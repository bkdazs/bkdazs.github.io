---
title: "1D Dynamic Programming"
topic: "Single-Dimension DP Problems"
difficulty: "Easy to Medium"
tags: ["dp", "memoization", "tabulation", "fibonacci", "climbing-stairs"]
status: "complete"
weight: 2
---

# 1D Dynamic Programming

## Summary / TL;DR

1D DP problems involve state that can be represented by a single variable (usually index or value). These form the foundation for understanding more complex DP patterns.

**Key Insight:** At each position `i`, the answer depends only on previous positions (usually `i-1`, `i-2`, or some earlier subset).

---

## When to Use

- **Optimal Substructure:** Answer at position `i` builds on answers at earlier positions
- **Sequential Decision Making:** Each step has limited choices affecting future states
- **Linear Data:** Working with arrays, strings, or sequences
- **Counting Problems:** Number of ways to reach a state

### Common Patterns

1. **Fibonacci-like:** `dp[i] = dp[i-1] + dp[i-2]`
2. **Max/Min:** `dp[i] = max(dp[i-1], dp[i-2] + val[i])`
3. **Prefix-based:** `dp[i] = best(dp[j]) for j < i` where some condition holds
4. **Kadane's Pattern:** `dp[i] = max(nums[i], dp[i-1] + nums[i])`

---

## Big-O Complexity

| Problem Type | Time | Space | Space Optimized |
|--------------|------|-------|-----------------|
| Fibonacci-like | O(n) | O(n) | O(1) |
| Linear scan | O(n) | O(n) | O(1) |
| Prefix optimization | O(n²) | O(n) | O(1) - O(n) |
| With binary search | O(n log n) | O(n) | - |

---

## Core Implementation Templates

### Template 1: Basic 1D DP with Space Optimization

```python
from typing import List

def fibonacci_dp(n: int) -> int:
    """
    Classic Fibonacci with space optimization.
    dp[i] = dp[i-1] + dp[i-2]
    
    Time: O(n), Space: O(1)
    """
    if n <= 1:
        return n
    
    prev2, prev1 = 0, 1
    for i in range(2, n + 1):
        curr = prev1 + prev2
        prev2, prev1 = prev1, curr
    
    return prev1


def climb_stairs(n: int) -> int:
    """
    Number of ways to climb n stairs (1 or 2 steps at a time).
    dp[i] = dp[i-1] + dp[i-2] (same as Fibonacci)
    
    Time: O(n), Space: O(1)
    """
    if n <= 2:
        return n
    
    prev2, prev1 = 1, 2
    for i in range(3, n + 1):
        curr = prev1 + prev2
        prev2, prev1 = prev1, curr
    
    return prev1


def climb_stairs_k_steps(n: int, k: int) -> int:
    """
    Generalized: can take 1 to k steps at a time.
    dp[i] = sum(dp[i-j] for j in 1..k if i-j >= 0)
    
    Time: O(n*k), Space: O(n) or O(k) with optimization
    """
    if n == 0:
        return 1
    
    # Use sliding window for O(k) space
    dp = [0] * k
    dp[0] = 1  # dp[i % k] represents ways to reach stair i
    
    window_sum = 1
    
    for i in range(1, n + 1):
        new_val = window_sum
        old_val = dp[i % k]
        dp[i % k] = new_val
        window_sum = window_sum - old_val + new_val
    
    return dp[n % k]
```

### Template 2: House Robber Pattern (Skip Element)

```python
def house_robber(nums: List[int]) -> int:
    """
    Maximum money robbing non-adjacent houses.
    dp[i] = max(dp[i-1], dp[i-2] + nums[i])
    
    Either skip current house (take dp[i-1]) or
    rob current house (take dp[i-2] + nums[i])
    
    Time: O(n), Space: O(1)
    """
    if not nums:
        return 0
    if len(nums) == 1:
        return nums[0]
    
    # prev2 = max money up to i-2
    # prev1 = max money up to i-1
    prev2, prev1 = 0, nums[0]
    
    for i in range(1, len(nums)):
        curr = max(prev1, prev2 + nums[i])
        prev2, prev1 = prev1, curr
    
    return prev1


def house_robber_circular(nums: List[int]) -> int:
    """
    Houses arranged in circle (first and last are adjacent).
    Solution: max(rob[0..n-2], rob[1..n-1])
    
    Time: O(n), Space: O(1)
    """
    if len(nums) == 1:
        return nums[0]
    
    def rob_linear(arr: List[int]) -> int:
        prev2, prev1 = 0, 0
        for num in arr:
            curr = max(prev1, prev2 + num)
            prev2, prev1 = prev1, curr
        return prev1
    
    # Either rob houses 0 to n-2, or 1 to n-1
    return max(rob_linear(nums[:-1]), rob_linear(nums[1:]))


def delete_and_earn(nums: List[int]) -> int:
    """
    Delete num, earn num points, but must delete all num-1 and num+1.
    Transform to house robber on value counts.
    
    Time: O(n + max_val), Space: O(max_val)
    """
    if not nums:
        return 0
    
    max_val = max(nums)
    points = [0] * (max_val + 1)
    
    for num in nums:
        points[num] += num
    
    # Now it's house robber on points array
    prev2, prev1 = 0, points[0] if points else 0
    
    for i in range(1, max_val + 1):
        curr = max(prev1, prev2 + points[i])
        prev2, prev1 = prev1, curr
    
    return prev1
```

### Template 3: Kadane's Algorithm (Maximum Subarray)

```python
def max_subarray_kadane(nums: List[int]) -> int:
    """
    Maximum sum of contiguous subarray.
    dp[i] = max(nums[i], dp[i-1] + nums[i])
    
    At each position: extend previous subarray or start new one.
    
    Time: O(n), Space: O(1)
    """
    max_sum = nums[0]
    curr_sum = nums[0]
    
    for i in range(1, len(nums)):
        curr_sum = max(nums[i], curr_sum + nums[i])
        max_sum = max(max_sum, curr_sum)
    
    return max_sum


def max_subarray_with_indices(nums: List[int]) -> tuple:
    """
    Return max sum along with start and end indices.
    
    Time: O(n), Space: O(1)
    """
    max_sum = nums[0]
    curr_sum = nums[0]
    start = end = 0
    temp_start = 0
    
    for i in range(1, len(nums)):
        if nums[i] > curr_sum + nums[i]:
            curr_sum = nums[i]
            temp_start = i
        else:
            curr_sum = curr_sum + nums[i]
        
        if curr_sum > max_sum:
            max_sum = curr_sum
            start = temp_start
            end = i
    
    return max_sum, start, end


def max_circular_subarray(nums: List[int]) -> int:
    """
    Maximum subarray sum in circular array.
    Answer is max(normal_kadane, total_sum - min_subarray)
    
    Special case: if all negative, return max element.
    
    Time: O(n), Space: O(1)
    """
    max_sum = nums[0]
    min_sum = nums[0]
    curr_max = nums[0]
    curr_min = nums[0]
    total = nums[0]
    
    for i in range(1, len(nums)):
        total += nums[i]
        curr_max = max(nums[i], curr_max + nums[i])
        max_sum = max(max_sum, curr_max)
        curr_min = min(nums[i], curr_min + nums[i])
        min_sum = min(min_sum, curr_min)
    
    # If all negative, total - min_sum = 0, return max_sum
    if max_sum < 0:
        return max_sum
    
    return max(max_sum, total - min_sum)


def max_product_subarray(nums: List[int]) -> int:
    """
    Maximum product of contiguous subarray.
    Track both max and min (negative * negative = positive).
    
    Time: O(n), Space: O(1)
    """
    max_prod = nums[0]
    curr_max = nums[0]
    curr_min = nums[0]
    
    for i in range(1, len(nums)):
        num = nums[i]
        
        # Need temp because curr_max is used for curr_min
        temp_max = max(num, curr_max * num, curr_min * num)
        curr_min = min(num, curr_max * num, curr_min * num)
        curr_max = temp_max
        
        max_prod = max(max_prod, curr_max)
    
    return max_prod
```

### Template 4: Min Cost Problems

```python
def min_cost_climbing_stairs(cost: List[int]) -> int:
    """
    Minimum cost to climb stairs (can climb 1 or 2 steps).
    Pay cost[i] to step on stair i.
    
    dp[i] = min cost to reach stair i
    dp[i] = min(dp[i-1] + cost[i-1], dp[i-2] + cost[i-2])
    
    Time: O(n), Space: O(1)
    """
    n = len(cost)
    prev2, prev1 = 0, 0  # Cost to reach stair 0 and 1
    
    for i in range(2, n + 1):
        curr = min(prev1 + cost[i-1], prev2 + cost[i-2])
        prev2, prev1 = prev1, curr
    
    return prev1


def paint_house(costs: List[List[int]]) -> int:
    """
    Paint n houses with 3 colors. No two adjacent houses same color.
    costs[i][j] = cost to paint house i with color j.
    
    dp[i][c] = min cost to paint houses 0..i with house i being color c
    
    Time: O(n), Space: O(1)
    """
    if not costs:
        return 0
    
    # dp[c] = min cost with last house painted color c
    dp = costs[0][:]  # Initialize with first house costs
    
    for i in range(1, len(costs)):
        new_dp = [0, 0, 0]
        new_dp[0] = min(dp[1], dp[2]) + costs[i][0]
        new_dp[1] = min(dp[0], dp[2]) + costs[i][1]
        new_dp[2] = min(dp[0], dp[1]) + costs[i][2]
        dp = new_dp
    
    return min(dp)


def paint_house_k_colors(costs: List[List[int]]) -> int:
    """
    Generalized: k colors available.
    Optimization: Track min and second_min to get O(nk) time.
    
    Time: O(n*k), Space: O(k) or O(1) with further optimization
    """
    if not costs:
        return 0
    
    n, k = len(costs), len(costs[0])
    
    # Track minimum, its index, and second minimum
    min1, min1_idx, min2 = 0, -1, 0
    
    for i in range(n):
        new_min1, new_min1_idx, new_min2 = float('inf'), -1, float('inf')
        
        for j in range(k):
            # Previous min cost: use min1 unless current color matches
            prev_cost = min1 if j != min1_idx else min2
            curr_cost = prev_cost + costs[i][j]
            
            if curr_cost < new_min1:
                new_min2 = new_min1
                new_min1 = curr_cost
                new_min1_idx = j
            elif curr_cost < new_min2:
                new_min2 = curr_cost
        
        min1, min1_idx, min2 = new_min1, new_min1_idx, new_min2
    
    return min1
```

### Template 5: Jump Game Variations

```python
def can_jump(nums: List[int]) -> bool:
    """
    Can reach last index? nums[i] = max jump length from i.
    Greedy approach works; DP for understanding.
    
    Time: O(n), Space: O(1)
    """
    max_reach = 0
    
    for i in range(len(nums)):
        if i > max_reach:
            return False
        max_reach = max(max_reach, i + nums[i])
        if max_reach >= len(nums) - 1:
            return True
    
    return True


def min_jumps(nums: List[int]) -> int:
    """
    Minimum jumps to reach last index. (Jump Game II)
    BFS-like greedy approach.
    
    Time: O(n), Space: O(1)
    """
    if len(nums) <= 1:
        return 0
    
    jumps = 0
    curr_end = 0      # End of current jump range
    next_end = 0      # Farthest we can reach
    
    for i in range(len(nums) - 1):
        next_end = max(next_end, i + nums[i])
        
        if i == curr_end:
            jumps += 1
            curr_end = next_end
            
            if curr_end >= len(nums) - 1:
                break
    
    return jumps


def min_jumps_dp(nums: List[int]) -> int:
    """
    DP approach for understanding (less efficient).
    dp[i] = min jumps to reach index i
    
    Time: O(n²), Space: O(n)
    """
    n = len(nums)
    dp = [float('inf')] * n
    dp[0] = 0
    
    for i in range(n):
        for j in range(1, nums[i] + 1):
            if i + j < n:
                dp[i + j] = min(dp[i + j], dp[i] + 1)
    
    return dp[n - 1]


def can_reach_target(nums: List[int], start: int) -> bool:
    """
    Jump Game III: Can reach any index with value 0?
    Can jump to i+nums[i] or i-nums[i].
    
    BFS/DFS approach.
    Time: O(n), Space: O(n)
    """
    n = len(nums)
    visited = [False] * n
    stack = [start]
    
    while stack:
        i = stack.pop()
        
        if nums[i] == 0:
            return True
        
        if visited[i]:
            continue
        visited[i] = True
        
        # Try both directions
        if i + nums[i] < n:
            stack.append(i + nums[i])
        if i - nums[i] >= 0:
            stack.append(i - nums[i])
    
    return False
```

### Template 6: Decode Ways / Partition DP

```python
def num_decodings(s: str) -> int:
    """
    Decode ways: '1'->'A', '2'->'B', ..., '26'->'Z'
    How many ways to decode string s?
    
    dp[i] = ways to decode s[0..i-1]
    
    Time: O(n), Space: O(1)
    """
    if not s or s[0] == '0':
        return 0
    
    n = len(s)
    prev2, prev1 = 1, 1  # dp[0], dp[1]
    
    for i in range(2, n + 1):
        curr = 0
        
        # Single digit decode (1-9)
        if s[i-1] != '0':
            curr += prev1
        
        # Two digit decode (10-26)
        two_digit = int(s[i-2:i])
        if 10 <= two_digit <= 26:
            curr += prev2
        
        prev2, prev1 = prev1, curr
    
    return prev1


def num_decodings_with_star(s: str) -> int:
    """
    Decode Ways II: '*' can be any digit 1-9.
    
    Time: O(n), Space: O(1)
    """
    MOD = 10**9 + 7
    
    if not s:
        return 0
    
    def count_single(c: str) -> int:
        if c == '*':
            return 9
        return 1 if c != '0' else 0
    
    def count_double(c1: str, c2: str) -> int:
        if c1 == '*' and c2 == '*':
            return 15  # 11-19 (9) + 21-26 (6)
        if c1 == '*':
            if c2 <= '6':
                return 2  # 1X or 2X
            return 1  # Only 1X
        if c2 == '*':
            if c1 == '1':
                return 9  # 11-19
            if c1 == '2':
                return 6  # 21-26
            return 0
        # Both are digits
        two_digit = int(c1 + c2)
        return 1 if 10 <= two_digit <= 26 else 0
    
    prev2, prev1 = 1, count_single(s[0])
    
    for i in range(2, len(s) + 1):
        curr = (count_single(s[i-1]) * prev1 + 
                count_double(s[i-2], s[i-1]) * prev2) % MOD
        prev2, prev1 = prev1, curr
    
    return prev1
```

### Template 7: Word Break

```python
def word_break(s: str, word_dict: List[str]) -> bool:
    """
    Can s be segmented into dictionary words?
    
    dp[i] = True if s[0..i-1] can be segmented
    
    Time: O(n² * m) where m is max word length
    Space: O(n + dict_size)
    """
    word_set = set(word_dict)
    n = len(s)
    dp = [False] * (n + 1)
    dp[0] = True  # Empty string
    
    for i in range(1, n + 1):
        for j in range(i):
            if dp[j] and s[j:i] in word_set:
                dp[i] = True
                break
    
    return dp[n]


def word_break_optimized(s: str, word_dict: List[str]) -> bool:
    """
    Optimized: only check valid word lengths.
    
    Time: O(n * m * k) where k is number of words
    """
    word_set = set(word_dict)
    max_len = max(len(w) for w in word_dict) if word_dict else 0
    n = len(s)
    dp = [False] * (n + 1)
    dp[0] = True
    
    for i in range(1, n + 1):
        for length in range(1, min(i, max_len) + 1):
            if dp[i - length] and s[i - length:i] in word_set:
                dp[i] = True
                break
    
    return dp[n]


def word_break_count(s: str, word_dict: List[str]) -> int:
    """
    Count number of ways to segment s.
    
    Time: O(n² * m), Space: O(n)
    """
    word_set = set(word_dict)
    n = len(s)
    dp = [0] * (n + 1)
    dp[0] = 1
    
    for i in range(1, n + 1):
        for j in range(i):
            if s[j:i] in word_set:
                dp[i] += dp[j]
    
    return dp[n]
```

---

## Worked Examples

### Example 1: Decode Ways

**Problem:** Given string "226", find number of ways to decode.

```python
# String: "226"
# Valid decodings: "BZ" (2,26), "VF" (22,6), "BBF" (2,2,6)

# dp[i] = ways to decode s[0..i-1]
# Initialize: dp[0] = 1 (empty), dp[1] = 1 (s[0]='2' is valid)

# i = 2 (considering '2'):
#   Single: '2' valid -> dp[2] += dp[1] = 1
#   Double: '22' valid (10-26) -> dp[2] += dp[0] = 1
#   dp[2] = 2

# i = 3 (considering '6'):
#   Single: '6' valid -> dp[3] += dp[2] = 2
#   Double: '26' valid (10-26) -> dp[3] += dp[1] = 1
#   dp[3] = 3

# Answer: 3 ways
```

### Example 2: Paint House

**Problem:** Paint 3 houses with costs [[17,2,17],[16,16,5],[14,3,19]]

```python
# Initial: dp = [17, 2, 17] (cost to paint house 0)

# House 1: costs = [16, 16, 5]
#   Color 0: min(dp[1], dp[2]) + 16 = min(2, 17) + 16 = 18
#   Color 1: min(dp[0], dp[2]) + 16 = min(17, 17) + 16 = 33
#   Color 2: min(dp[0], dp[1]) + 5 = min(17, 2) + 5 = 7
#   dp = [18, 33, 7]

# House 2: costs = [14, 3, 19]
#   Color 0: min(33, 7) + 14 = 7 + 14 = 21
#   Color 1: min(18, 7) + 3 = 7 + 3 = 10
#   Color 2: min(18, 33) + 19 = 18 + 19 = 37
#   dp = [21, 10, 37]

# Answer: min(21, 10, 37) = 10
```

### Example 3: Maximum Product Subarray

**Problem:** Find max product in [-2, 3, -4]

```python
# nums = [-2, 3, -4]

# i = 0: num = -2
#   curr_max = -2, curr_min = -2
#   max_prod = -2

# i = 1: num = 3
#   candidates: 3, (-2)*3=-6, (-2)*3=-6
#   curr_max = max(3, -6, -6) = 3
#   curr_min = min(3, -6, -6) = -6
#   max_prod = max(-2, 3) = 3

# i = 2: num = -4
#   candidates: -4, 3*(-4)=-12, (-6)*(-4)=24
#   curr_max = max(-4, -12, 24) = 24
#   curr_min = min(-4, -12, 24) = -12
#   max_prod = max(3, 24) = 24

# Answer: 24 (subarray [3, -4] starting from [-2, 3])
# Wait, that's wrong. Let's trace again.
# Actually [−2, 3, −4] gives (-2) * 3 * (-4) = 24 ✓
```

---

## Edge Cases & Gotchas

```python
# 1. Empty input
def handle_empty(nums: List[int]) -> int:
    if not nums:
        return 0  # or appropriate default
    # ... rest of logic

# 2. Single element
def single_element(nums: List[int]) -> int:
    if len(nums) == 1:
        return nums[0]

# 3. All same values
# Usually works but verify transition logic

# 4. Negative numbers in product
# Track both max AND min (negative * negative = positive)

# 5. Zeros in product subarray
def max_product_with_zeros(nums: List[int]) -> int:
    # Reset curr_max and curr_min when encountering 0
    max_prod = float('-inf')
    curr_max = curr_min = 1
    
    for num in nums:
        if num == 0:
            max_prod = max(max_prod, 0)
            curr_max = curr_min = 1
        else:
            temp = max(num, curr_max * num, curr_min * num)
            curr_min = min(num, curr_max * num, curr_min * num)
            curr_max = temp
            max_prod = max(max_prod, curr_max)
    
    return max_prod

# 6. Decode ways with leading zeros
def decode_with_zeros(s: str) -> int:
    if s[0] == '0':
        return 0  # Invalid encoding
    # Handle internal zeros in transitions

# 7. Integer overflow (in other languages)
# Python handles big integers, but be careful in interviews
```

---

## Interview Tips & Communication

### Framework for 1D DP Problems

1. **Identify the State:**
   - What does `dp[i]` represent?
   - Usually: answer for first `i` elements, or answer ending at index `i`

2. **Define Base Case:**
   - What is `dp[0]`? (empty or first element)
   - May need `dp[1]` for Fibonacci-like problems

3. **Write Recurrence:**
   - How does `dp[i]` relate to previous values?
   - List all choices/transitions

4. **Optimize Space:**
   - If only need last k values, use rolling variables
   - O(n) → O(k) or O(1)

### Key Phrases for Interviews

- "Let me define what dp[i] represents: the optimal solution for the first i elements."
- "The recurrence is: at position i, I can either... or..."
- "I can optimize space because I only need the last two values."
- "This follows the Kadane's pattern: extend or start new."

---

## Common Variations & Extensions

### 1. Counting vs. Optimization

```python
# Counting: dp[i] += dp[j] (sum of ways)
# Optimization: dp[i] = max/min(dp[j]) + cost
```

### 2. Forward vs. Backward DP

```python
# Forward: dp[i] depends on dp[i-1], dp[i-2], ...
# Backward: dp[i] depends on dp[i+1], dp[i+2], ...
# Usually equivalent, choose based on problem structure
```

### 3. State Augmentation

```python
# Sometimes need extra state dimension
# Example: "minimum cost if last action was X"
# dp[i][0] = best if didn't take action at i
# dp[i][1] = best if took action at i
```

---

## Related Patterns

- **2D DP:** Two dimensions of state (next topic)
- **Knapsack:** Weight/capacity constraint
- **LIS:** Longest Increasing Subsequence
- **State Machine DP:** Multiple states to track

---

## Practice Problems

### Easy

| # | Problem | Key Concept |
|---|---------|-------------|
| 1 | [Climbing Stairs](https://leetcode.com/problems/climbing-stairs/) | Fibonacci DP |
| 2 | [Fibonacci Number](https://leetcode.com/problems/fibonacci-number/) | Basic DP |
| 3 | [Min Cost Climbing Stairs](https://leetcode.com/problems/min-cost-climbing-stairs/) | Min cost DP |
| 4 | [Tribonacci Number](https://leetcode.com/problems/n-th-tribonacci-number/) | Extended Fibonacci |
| 5 | [Get Maximum in Generated Array](https://leetcode.com/problems/get-maximum-in-generated-array/) | Recurrence relation |
| 6 | [Divisor Game](https://leetcode.com/problems/divisor-game/) | Game theory DP |

### Medium

| # | Problem | Key Concept |
|---|---------|-------------|
| 7 | [House Robber](https://leetcode.com/problems/house-robber/) | Skip element DP |
| 8 | [House Robber II](https://leetcode.com/problems/house-robber-ii/) | Circular array |
| 9 | [Maximum Subarray](https://leetcode.com/problems/maximum-subarray/) | Kadane's algorithm |
| 10 | [Maximum Product Subarray](https://leetcode.com/problems/maximum-product-subarray/) | Track max and min |
| 11 | [Jump Game](https://leetcode.com/problems/jump-game/) | Reachability |
| 12 | [Jump Game II](https://leetcode.com/problems/jump-game-ii/) | Min jumps (greedy) |
| 13 | [Decode Ways](https://leetcode.com/problems/decode-ways/) | Partition counting |
| 14 | [Word Break](https://leetcode.com/problems/word-break/) | String partition |
| 15 | [Perfect Squares](https://leetcode.com/problems/perfect-squares/) | Unbounded knapsack-like |
| 16 | [Coin Change](https://leetcode.com/problems/coin-change/) | Unbounded knapsack |
| 17 | [Delete and Earn](https://leetcode.com/problems/delete-and-earn/) | Transform to House Robber |
| 18 | [Maximum Length of Subarray With Positive Product](https://leetcode.com/problems/maximum-length-of-subarray-with-positive-product/) | Track positive/negative lengths |
| 19 | [Best Time to Buy and Sell Stock](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/) | One transaction |
| 20 | [Maximum Sum Circular Subarray](https://leetcode.com/problems/maximum-sum-circular-subarray/) | Kadane + circular |
| 21 | [Paint House](https://leetcode.com/problems/paint-house/) | Adjacent constraint |

### Hard

| # | Problem | Key Concept |
|---|---------|-------------|
| 22 | [Jump Game III](https://leetcode.com/problems/jump-game-iii/) | BFS/DFS variant |
| 23 | [Decode Ways II](https://leetcode.com/problems/decode-ways-ii/) | Wildcard handling |
| 24 | [Paint House II](https://leetcode.com/problems/paint-house-ii/) | K colors optimization |
| 25 | [Word Break II](https://leetcode.com/problems/word-break-ii/) | All partitions (backtrack) |
| 26 | [Best Time to Buy and Sell Stock III](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iii/) | Two transactions |
| 27 | [Best Time to Buy and Sell Stock IV](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iv/) | K transactions |
| 28 | [Trapping Rain Water](https://leetcode.com/problems/trapping-rain-water/) | Prefix/suffix DP |

---

## References

- CLRS: Chapter 15 (Dynamic Programming)
- [LeetCode DP Study Plan](https://leetcode.com/study-plan/dynamic-programming/)
- [Grokking Dynamic Programming Patterns](https://www.educative.io/courses/grokking-dynamic-programming-patterns-for-coding-interviews)
- [Codeforces DP Tutorial](https://codeforces.com/blog/entry/67679)
