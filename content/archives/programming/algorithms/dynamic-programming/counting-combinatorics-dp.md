---
title: Counting DP & Combinatorics DP
topic: Algorithms - Dynamic Programming
difficulty: Medium-Hard
tags: [dp, counting, combinatorics, modular-arithmetic, ways]
status: complete
weight: 16
---

# Counting DP & Combinatorics DP

## 1. Overview

### Core Concept
Counting DP deals with problems asking "how many ways" to achieve something. Results often need modular arithmetic due to large counts.

### Key Patterns
| Pattern | Description | Example |
|---------|-------------|---------|
| Ways to reach state | Count paths to target | Unique Paths |
| Partition counting | Ways to partition number | Integer Partition |
| Subset counting | Ways to select elements | Coin Change II |
| Arrangement counting | Permutations with constraints | Beautiful Arrangement |

### Modular Arithmetic Essentials
```python
MOD = 10**9 + 7

# Addition
(a + b) % MOD

# Subtraction (handle negative)
(a - b + MOD) % MOD

# Multiplication
(a * b) % MOD

# Division (multiply by modular inverse)
# a / b = a * pow(b, MOD-2, MOD) when MOD is prime
```

---

## 2. Ways to Reach Target

### Basic Path Counting

```python
from typing import List
from functools import lru_cache

MOD = 10**9 + 7

def unique_paths(m: int, n: int) -> int:
    """
    LeetCode 62 - Unique Paths
    
    Count paths from top-left to bottom-right.
    
    Time: O(mn)
    Space: O(n) with optimization
    """
    dp = [1] * n
    
    for _ in range(1, m):
        for j in range(1, n):
            dp[j] = (dp[j] + dp[j - 1]) % MOD
    
    return dp[n - 1]


def unique_paths_with_obstacles(obstacleGrid: List[List[int]]) -> int:
    """
    LeetCode 63 - Unique Paths II
    
    Count paths avoiding obstacles.
    """
    m, n = len(obstacleGrid), len(obstacleGrid[0])
    
    if obstacleGrid[0][0] == 1:
        return 0
    
    dp = [0] * n
    dp[0] = 1
    
    for i in range(m):
        for j in range(n):
            if obstacleGrid[i][j] == 1:
                dp[j] = 0
            elif j > 0:
                dp[j] = (dp[j] + dp[j - 1]) % MOD
    
    return dp[n - 1]


def count_paths_with_mod(grid: List[List[int]], MOD: int = 10**9 + 7) -> int:
    """
    Count paths in grid where each cell has a value (multiplier or cost).
    
    Time: O(mn)
    """
    m, n = len(grid), len(grid[0])
    dp = [[0] * n for _ in range(m)]
    dp[0][0] = 1
    
    for i in range(m):
        for j in range(n):
            if grid[i][j] == -1:  # Obstacle
                continue
            if i > 0:
                dp[i][j] = (dp[i][j] + dp[i-1][j]) % MOD
            if j > 0:
                dp[i][j] = (dp[i][j] + dp[i][j-1]) % MOD
    
    return dp[m-1][n-1]
```

---

## 3. Coin Change Variants (Counting)

### Count Ways to Make Change

```python
def coin_change_ways(coins: List[int], amount: int) -> int:
    """
    LeetCode 518 - Coin Change II
    
    Count number of combinations to make amount.
    
    Time: O(amount * n)
    Space: O(amount)
    """
    dp = [0] * (amount + 1)
    dp[0] = 1
    
    # Process coins first to avoid counting permutations
    for coin in coins:
        for x in range(coin, amount + 1):
            dp[x] = (dp[x] + dp[x - coin]) % MOD
    
    return dp[amount]


def combination_sum_count(nums: List[int], target: int) -> int:
    """
    LeetCode 377 - Combination Sum IV
    
    Count number of permutations (order matters).
    
    Time: O(target * n)
    """
    dp = [0] * (target + 1)
    dp[0] = 1
    
    # Process amounts first to count permutations
    for x in range(1, target + 1):
        for num in nums:
            if x >= num:
                dp[x] = (dp[x] + dp[x - num]) % MOD
    
    return dp[target]


def num_rolls_to_target(n: int, k: int, target: int) -> int:
    """
    LeetCode 1155 - Number of Dice Rolls With Target Sum
    
    Count ways to roll n dice (k faces) to get target sum.
    
    Time: O(n * target * k)
    """
    dp = [0] * (target + 1)
    dp[0] = 1
    
    for _ in range(n):
        new_dp = [0] * (target + 1)
        for j in range(1, target + 1):
            for face in range(1, min(k, j) + 1):
                new_dp[j] = (new_dp[j] + dp[j - face]) % MOD
        dp = new_dp
    
    return dp[target]


# Test
print(coin_change_ways([1, 2, 5], 5))  # 4
print(combination_sum_count([1, 2, 3], 4))  # 7
```

---

## 4. Integer Partitions

### Count Partitions of N

```python
def partition_count(n: int) -> int:
    """
    Count number of ways to partition integer n.
    
    p(n) = number of ways to write n as sum of positive integers
    (order doesn't matter, e.g., 4 = 3+1 = 2+2 = 2+1+1 = 1+1+1+1)
    
    Time: O(n²)
    """
    dp = [0] * (n + 1)
    dp[0] = 1
    
    for i in range(1, n + 1):
        for j in range(i, n + 1):
            dp[j] = (dp[j] + dp[j - i]) % MOD
    
    return dp[n]


def partition_count_at_most_k(n: int, k: int) -> int:
    """
    Count partitions of n using parts at most k.
    
    Time: O(nk)
    """
    dp = [0] * (n + 1)
    dp[0] = 1
    
    for i in range(1, k + 1):
        for j in range(i, n + 1):
            dp[j] = (dp[j] + dp[j - i]) % MOD
    
    return dp[n]


def partition_into_k_parts(n: int, k: int) -> int:
    """
    Count ways to partition n into exactly k parts.
    
    Time: O(nk)
    """
    # dp[i][j] = ways to partition i into j parts
    dp = [[0] * (k + 1) for _ in range(n + 1)]
    dp[0][0] = 1
    
    for i in range(1, n + 1):
        for j in range(1, min(i, k) + 1):
            # Either smallest part is 1: subtract 1 from each part
            # Or smallest part > 1: subtract 1 from value
            dp[i][j] = (dp[i - 1][j - 1] + dp[i - j][j]) % MOD
    
    return dp[n][k]


def partition_distinct_parts(n: int) -> int:
    """
    Count partitions using distinct parts only.
    
    Time: O(n²)
    """
    dp = [0] * (n + 1)
    dp[0] = 1
    
    for i in range(1, n + 1):
        # Process in reverse to ensure each part used at most once
        for j in range(n, i - 1, -1):
            dp[j] = (dp[j] + dp[j - i]) % MOD
    
    return dp[n]


# Test
print(partition_count(5))  # 7: 5, 4+1, 3+2, 3+1+1, 2+2+1, 2+1+1+1, 1+1+1+1+1
```

---

## 5. Catalan Numbers

### Applications of Catalan Numbers

```python
def catalan_number(n: int) -> int:
    """
    Nth Catalan number.
    
    Applications:
    - Number of valid parentheses sequences of length 2n
    - Number of binary trees with n nodes
    - Number of ways to triangulate polygon with n+2 sides
    - Number of monotonic paths in grid not crossing diagonal
    
    Formula: C(n) = C(2n, n) / (n + 1)
    Recurrence: C(n) = sum(C(i) * C(n-1-i)) for i in [0, n-1]
    
    Time: O(n)
    """
    if n <= 1:
        return 1
    
    # Using formula with modular arithmetic
    def mod_inverse(a: int, mod: int = MOD) -> int:
        return pow(a, mod - 2, mod)
    
    # C(2n, n) / (n + 1)
    numerator = 1
    denominator = 1
    
    for i in range(n):
        numerator = numerator * (2 * n - i) % MOD
        denominator = denominator * (i + 1) % MOD
    
    return numerator * mod_inverse(denominator) % MOD * mod_inverse(n + 1) % MOD


def catalan_dp(n: int) -> int:
    """
    Catalan number using DP (recurrence).
    
    Time: O(n²)
    """
    dp = [0] * (n + 1)
    dp[0] = dp[1] = 1
    
    for i in range(2, n + 1):
        for j in range(i):
            dp[i] = (dp[i] + dp[j] * dp[i - 1 - j]) % MOD
    
    return dp[n]


def count_valid_parentheses(n: int) -> int:
    """
    LeetCode 22 related - Count valid parentheses strings of length 2n.
    
    This is the nth Catalan number.
    """
    return catalan_number(n)


def count_bst_structures(n: int) -> int:
    """
    LeetCode 96 - Unique Binary Search Trees
    
    Count structurally unique BSTs with n nodes.
    """
    return catalan_number(n)


def generate_parentheses_count(n: int) -> int:
    """
    Count using DP with open/close tracking.
    
    dp[open][close] = ways to place remaining parentheses
    """
    @lru_cache(maxsize=None)
    def dp(open_count: int, close_count: int) -> int:
        if open_count == 0 and close_count == 0:
            return 1
        
        result = 0
        if open_count > 0:
            result += dp(open_count - 1, close_count)
        if close_count > open_count:
            result += dp(open_count, close_count - 1)
        
        return result % MOD
    
    return dp(n, n)
```

---

## 6. Counting with Constraints

### Decode Ways

```python
def num_decodings(s: str) -> int:
    """
    LeetCode 91 - Decode Ways
    
    Count ways to decode digit string where 1-26 map to A-Z.
    
    Time: O(n)
    """
    if not s or s[0] == '0':
        return 0
    
    n = len(s)
    dp = [0] * (n + 1)
    dp[0] = 1
    dp[1] = 1
    
    for i in range(2, n + 1):
        # Single digit
        if s[i - 1] != '0':
            dp[i] += dp[i - 1]
        
        # Two digits
        two_digit = int(s[i - 2:i])
        if 10 <= two_digit <= 26:
            dp[i] += dp[i - 2]
    
    return dp[n]


def num_decodings_with_star(s: str) -> int:
    """
    LeetCode 639 - Decode Ways II
    
    '*' can be any digit 1-9.
    
    Time: O(n)
    """
    if not s or s[0] == '0':
        return 0
    
    n = len(s)
    dp = [0] * (n + 1)
    dp[0] = 1
    
    # First character
    if s[0] == '*':
        dp[1] = 9
    elif s[0] != '0':
        dp[1] = 1
    
    for i in range(2, n + 1):
        c1, c2 = s[i - 2], s[i - 1]
        
        # Single digit
        if c2 == '*':
            dp[i] = dp[i - 1] * 9
        elif c2 != '0':
            dp[i] = dp[i - 1]
        
        # Two digits
        if c1 == '*':
            if c2 == '*':
                dp[i] += dp[i - 2] * 15  # 11-19, 21-26
            elif c2 <= '6':
                dp[i] += dp[i - 2] * 2  # 1X, 2X
            else:
                dp[i] += dp[i - 2] * 1  # 1X only
        elif c1 == '1':
            if c2 == '*':
                dp[i] += dp[i - 2] * 9
            else:
                dp[i] += dp[i - 2]
        elif c1 == '2':
            if c2 == '*':
                dp[i] += dp[i - 2] * 6  # 21-26
            elif c2 <= '6':
                dp[i] += dp[i - 2]
        
        dp[i] %= MOD
    
    return dp[n]
```

### Count Sorted Vowel Strings

```python
def count_vowel_strings(n: int) -> int:
    """
    LeetCode 1641 - Count Sorted Vowel Strings
    
    Count strings of length n with vowels in non-decreasing order.
    
    Time: O(n)
    """
    # dp[i] = count of strings ending with ith vowel
    dp = [1, 1, 1, 1, 1]  # a, e, i, o, u
    
    for _ in range(n - 1):
        # Update in reverse: each vowel can be followed by itself or later vowels
        for i in range(3, -1, -1):
            dp[i] += dp[i + 1]
    
    return sum(dp)


def count_sorted_vowel_strings_formula(n: int) -> int:
    """
    Stars and bars: C(n+4, 4)
    """
    # (n+4) * (n+3) * (n+2) * (n+1) / 24
    return (n + 4) * (n + 3) * (n + 2) * (n + 1) // 24
```

---

## 7. Counting Subsequences

```python
def distinct_subsequences(s: str, t: str) -> int:
    """
    LeetCode 115 - Distinct Subsequences
    
    Count subsequences of s that equal t.
    
    Time: O(mn)
    """
    m, n = len(s), len(t)
    dp = [[0] * (n + 1) for _ in range(m + 1)]
    
    # Empty t is subsequence of any prefix of s
    for i in range(m + 1):
        dp[i][0] = 1
    
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            dp[i][j] = dp[i - 1][j]  # Don't use s[i-1]
            if s[i - 1] == t[j - 1]:
                dp[i][j] += dp[i - 1][j - 1]  # Use s[i-1]
            dp[i][j] %= MOD
    
    return dp[m][n]


def count_distinct_subsequences(s: str) -> int:
    """
    LeetCode 940 - Distinct Subsequences II
    
    Count distinct non-empty subsequences of s.
    
    Time: O(n)
    """
    n = len(s)
    dp = [0] * (n + 1)
    dp[0] = 1  # Empty subsequence
    
    last = {}  # Last position of each character
    
    for i in range(1, n + 1):
        c = s[i - 1]
        dp[i] = 2 * dp[i - 1] % MOD  # Double (with/without current)
        
        if c in last:
            dp[i] = (dp[i] - dp[last[c] - 1] + MOD) % MOD  # Remove duplicates
        
        last[c] = i
    
    return (dp[n] - 1 + MOD) % MOD  # Exclude empty


def count_palindromic_subsequences(s: str) -> int:
    """
    LeetCode 730 - Count Different Palindromic Subsequences
    
    Count distinct palindromic subsequences.
    
    Time: O(n²)
    """
    n = len(s)
    dp = [[0] * n for _ in range(n)]
    
    # Single characters
    for i in range(n):
        dp[i][i] = 1
    
    for length in range(2, n + 1):
        for i in range(n - length + 1):
            j = i + length - 1
            
            if s[i] == s[j]:
                lo, hi = i + 1, j - 1
                
                # Find first and last occurrence of s[i] in (i, j)
                while lo <= hi and s[lo] != s[i]:
                    lo += 1
                while lo <= hi and s[hi] != s[i]:
                    hi -= 1
                
                if lo > hi:
                    # No s[i] in between
                    dp[i][j] = 2 * dp[i + 1][j - 1] + 2
                elif lo == hi:
                    # One s[i] in between
                    dp[i][j] = 2 * dp[i + 1][j - 1] + 1
                else:
                    # Multiple s[i] in between
                    dp[i][j] = 2 * dp[i + 1][j - 1] - dp[lo + 1][hi - 1]
            else:
                dp[i][j] = dp[i + 1][j] + dp[i][j - 1] - dp[i + 1][j - 1]
            
            dp[i][j] = (dp[i][j] + MOD) % MOD
    
    return dp[0][n - 1]
```

---

## 8. Combinatorics Formulas

### Pascal's Triangle / Binomial Coefficients

```python
def build_pascal(n: int) -> List[List[int]]:
    """
    Build Pascal's triangle up to row n.
    
    Time: O(n²)
    """
    C = [[0] * (n + 1) for _ in range(n + 1)]
    
    for i in range(n + 1):
        C[i][0] = 1
        for j in range(1, i + 1):
            C[i][j] = (C[i - 1][j - 1] + C[i - 1][j]) % MOD
    
    return C


def nCr(n: int, r: int) -> int:
    """
    Compute C(n, r) with modular arithmetic.
    
    Time: O(r)
    """
    if r > n or r < 0:
        return 0
    if r == 0 or r == n:
        return 1
    
    # C(n, r) = n! / (r! * (n-r)!)
    numerator = 1
    denominator = 1
    
    for i in range(r):
        numerator = numerator * (n - i) % MOD
        denominator = denominator * (i + 1) % MOD
    
    return numerator * pow(denominator, MOD - 2, MOD) % MOD


def factorial_and_inverse(n: int) -> tuple:
    """
    Precompute factorials and inverse factorials.
    
    Time: O(n)
    """
    fact = [1] * (n + 1)
    for i in range(1, n + 1):
        fact[i] = fact[i - 1] * i % MOD
    
    inv_fact = [1] * (n + 1)
    inv_fact[n] = pow(fact[n], MOD - 2, MOD)
    for i in range(n - 1, -1, -1):
        inv_fact[i] = inv_fact[i + 1] * (i + 1) % MOD
    
    return fact, inv_fact


# Usage for fast nCr
fact, inv_fact = factorial_and_inverse(10**6)

def fast_nCr(n: int, r: int) -> int:
    if r > n or r < 0:
        return 0
    return fact[n] * inv_fact[r] % MOD * inv_fact[n - r] % MOD
```

### Derangements

```python
def derangement_count(n: int) -> int:
    """
    Count derangements (permutations with no fixed points).
    
    D(n) = (n-1) * (D(n-1) + D(n-2))
    
    Time: O(n)
    """
    if n == 0:
        return 1
    if n == 1:
        return 0
    
    dp = [0] * (n + 1)
    dp[0] = 1
    dp[1] = 0
    
    for i in range(2, n + 1):
        dp[i] = (i - 1) * (dp[i - 1] + dp[i - 2]) % MOD
    
    return dp[n]
```

---

## 9. Counting Arrangements

```python
def beautiful_arrangement(n: int) -> int:
    """
    LeetCode 526 - Beautiful Arrangement
    
    Count permutations where perm[i] divides i or i divides perm[i].
    
    Time: O(n! / some pruning) with backtracking
    Or O(n * 2^n) with bitmask DP
    """
    @lru_cache(maxsize=None)
    def dp(pos: int, mask: int) -> int:
        if pos > n:
            return 1
        
        count = 0
        for num in range(1, n + 1):
            if not (mask & (1 << num)):
                if num % pos == 0 or pos % num == 0:
                    count += dp(pos + 1, mask | (1 << num))
        
        return count
    
    return dp(1, 0)


def count_arrangements_with_constraints(n: int, constraints: List[tuple]) -> int:
    """
    Count permutations satisfying given constraints.
    
    constraints: list of (i, j) meaning position i must have value ≤ j
    """
    @lru_cache(maxsize=None)
    def dp(pos: int, mask: int) -> int:
        if pos == n:
            return 1
        
        count = 0
        for val in range(n):
            if not (mask & (1 << val)):
                # Check constraints
                valid = True
                for i, max_val in constraints:
                    if i == pos and val > max_val:
                        valid = False
                        break
                
                if valid:
                    count += dp(pos + 1, mask | (1 << val))
        
        return count % MOD
    
    return dp(0, 0)
```

---

## 10. Counting Paths with Constraints

```python
def knight_dialer(n: int) -> int:
    """
    LeetCode 935 - Knight Dialer
    
    Count n-digit numbers possible with knight moves on phone keypad.
    
    Time: O(n)
    """
    moves = {
        0: [4, 6],
        1: [6, 8],
        2: [7, 9],
        3: [4, 8],
        4: [0, 3, 9],
        5: [],
        6: [0, 1, 7],
        7: [2, 6],
        8: [1, 3],
        9: [2, 4]
    }
    
    dp = [1] * 10
    
    for _ in range(n - 1):
        new_dp = [0] * 10
        for digit in range(10):
            for next_digit in moves[digit]:
                new_dp[next_digit] = (new_dp[next_digit] + dp[digit]) % MOD
        dp = new_dp
    
    return sum(dp) % MOD


def count_vowel_permutation(n: int) -> int:
    """
    LeetCode 1220 - Count Vowel Permutation
    
    Count strings following vowel transition rules.
    
    Time: O(n)
    """
    # Transitions: a->e, e->a,i, i->all except i, o->i,u, u->a
    a = e = i = o = u = 1
    
    for _ in range(n - 1):
        a, e, i, o, u = (e + i + u) % MOD, (a + i) % MOD, (e + o) % MOD, i, (i + o) % MOD
    
    return (a + e + i + o + u) % MOD


def domino_tromino_tiling(n: int) -> int:
    """
    LeetCode 790 - Domino and Tromino Tiling
    
    Count ways to tile 2×n board with dominoes and trominoes.
    
    Time: O(n)
    """
    if n <= 2:
        return n
    
    # dp[i] = ways to completely tile 2×i board
    # f[i] = ways to tile with one cell missing
    
    dp = [0] * (n + 1)
    dp[0] = dp[1] = 1
    dp[2] = 2
    
    for i in range(3, n + 1):
        dp[i] = (2 * dp[i - 1] + dp[i - 3]) % MOD
    
    return dp[n]
```

---

## 11. Practice Problems

### LeetCode Problems

| # | Problem | Pattern | Difficulty |
|---|---------|---------|------------|
| 62 | [Unique Paths](https://leetcode.com/problems/unique-paths/) | Grid counting | Medium |
| 63 | [Unique Paths II](https://leetcode.com/problems/unique-paths-ii/) | Grid + obstacles | Medium |
| 70 | [Climbing Stairs](https://leetcode.com/problems/climbing-stairs/) | Fibonacci | Easy |
| 91 | [Decode Ways](https://leetcode.com/problems/decode-ways/) | Constrained counting | Medium |
| 96 | [Unique Binary Search Trees](https://leetcode.com/problems/unique-binary-search-trees/) | Catalan | Medium |
| 115 | [Distinct Subsequences](https://leetcode.com/problems/distinct-subsequences/) | Subsequence counting | Hard |
| 377 | [Combination Sum IV](https://leetcode.com/problems/combination-sum-iv/) | Permutation counting | Medium |
| 494 | [Target Sum](https://leetcode.com/problems/target-sum/) | Subset sum counting | Medium |
| 518 | [Coin Change II](https://leetcode.com/problems/coin-change-ii/) | Combination counting | Medium |
| 526 | [Beautiful Arrangement](https://leetcode.com/problems/beautiful-arrangement/) | Arrangement counting | Medium |
| 576 | [Out of Boundary Paths](https://leetcode.com/problems/out-of-boundary-paths/) | Grid paths | Medium |
| 629 | [K Inverse Pairs Array](https://leetcode.com/problems/k-inverse-pairs-array/) | Counting | Hard |
| 639 | [Decode Ways II](https://leetcode.com/problems/decode-ways-ii/) | Wildcard counting | Hard |
| 730 | [Count Different Palindromic Subsequences](https://leetcode.com/problems/count-different-palindromic-subsequences/) | Palindrome counting | Hard |
| 790 | [Domino and Tromino Tiling](https://leetcode.com/problems/domino-and-tromino-tiling/) | Tiling | Medium |
| 935 | [Knight Dialer](https://leetcode.com/problems/knight-dialer/) | State machine | Medium |
| 940 | [Distinct Subsequences II](https://leetcode.com/problems/distinct-subsequences-ii/) | Distinct counting | Hard |
| 1155 | [Number of Dice Rolls](https://leetcode.com/problems/number-of-dice-rolls-with-target-sum/) | Sum counting | Medium |
| 1220 | [Count Vowel Permutation](https://leetcode.com/problems/count-vowel-permutation/) | State machine | Hard |
| 1269 | [Number of Ways to Stay in Same Place](https://leetcode.com/problems/number-of-ways-to-stay-in-the-same-place-after-some-steps/) | Position counting | Hard |
| 1411 | [Number of Ways to Paint N×3 Grid](https://leetcode.com/problems/number-of-ways-to-paint-n-3-grid/) | Coloring | Hard |
| 1641 | [Count Sorted Vowel Strings](https://leetcode.com/problems/count-sorted-vowel-strings/) | Combinatorics | Medium |
| 1866 | [Number of Ways to Rearrange Sticks](https://leetcode.com/problems/number-of-ways-to-rearrange-sticks-with-k-sticks-visible/) | Stirling | Hard |

---

## 12. Key Patterns Summary

```
Counting DP Decision Framework:

1. Counting combinations (order doesn't matter)?
   → Process items first: for item in items: for val in range(...)
   
2. Counting permutations (order matters)?
   → Process values first: for val in range(...): for item in items
   
3. Distinct subsequences/subsets?
   → Track last occurrence to avoid duplicates
   
4. Counting with complex constraints?
   → Use multi-dimensional DP tracking constraint states
   
5. Large numbers?
   → Use modular arithmetic (MOD = 10^9 + 7)
   
6. Need binomial coefficients?
   → Precompute factorials and inverse factorials
   
7. Catalan-related problem?
   → Recognize pattern: matched parentheses, BSTs, triangulations

Always remember:
- Addition/subtraction: (a ± b + MOD) % MOD
- Multiplication: (a * b) % MOD
- Division: a * pow(b, MOD-2, MOD) % MOD
```

---

## 13. References

1. CLRS - Chapter 15: Dynamic Programming
2. CP-Algorithms: Combinatorics
3. Art of Problem Solving: Counting Techniques
4. Codeforces: Combinatorics Tutorial
