---
title: "Combinatorics"
topic: "Number Theory"
difficulty: "Medium-Hard"
tags: ["combinatorics", "counting", "permutations", "combinations", "catalan"]
status: "complete"
weight: 3
---

# Combinatorics

## 📚 Summary

Combinatorics is the mathematics of counting. Essential for solving problems involving arrangements, selections, and counting patterns.

---

## 1️⃣ Basic Counting Principles

### Permutations

```python
from math import factorial

def permutations_count(n: int, r: int) -> int:
    """
    P(n, r) = n! / (n-r)!
    Number of ways to arrange r items from n items (order matters)
    """
    if r > n:
        return 0
    return factorial(n) // factorial(n - r)


def permutations_with_repetition(n: int, counts: list[int]) -> int:
    """
    Permutations with repeated elements
    n! / (n1! * n2! * ... * nk!)
    Example: "MISSISSIPPI" arrangements
    """
    result = factorial(n)
    for count in counts:
        result //= factorial(count)
    return result
```

### Combinations

```python
def combinations_count(n: int, r: int) -> int:
    """
    C(n, r) = n! / (r! * (n-r)!)
    Number of ways to choose r items from n items (order doesn't matter)
    """
    if r > n:
        return 0
    return factorial(n) // (factorial(r) * factorial(n - r))


# Efficient computation using Pascal's triangle relation
def nCr_efficient(n: int, r: int) -> int:
    """
    C(n, r) = C(n, n-r) and C(n, r) = C(n-1, r-1) + C(n-1, r)
    """
    r = min(r, n - r)  # Optimization
    if r == 0:
        return 1
    
    result = 1
    for i in range(r):
        result = result * (n - i) // (i + 1)
    return result


# With modular arithmetic
def nCr_mod(n: int, r: int, mod: int = 10**9 + 7) -> int:
    if r > n:
        return 0
    if r > n - r:
        r = n - r
    
    num = 1
    den = 1
    for i in range(r):
        num = num * (n - i) % mod
        den = den * (i + 1) % mod
    
    return num * pow(den, mod - 2, mod) % mod
```

### Stars and Bars

```python
def stars_and_bars(n: int, k: int) -> int:
    """
    Number of ways to distribute n identical items into k distinct bins
    = C(n + k - 1, k - 1)
    
    Example: Distribute 5 candies among 3 children
    Stars: ***** (candies)
    Bars: || (dividers between children)
    Arrangements of ****|*|* etc.
    """
    return nCr_efficient(n + k - 1, k - 1)


def stars_and_bars_min(n: int, k: int, min_per_bin: int = 1) -> int:
    """
    Each bin must have at least min_per_bin items
    Give each bin min_per_bin first, then distribute rest
    """
    remaining = n - k * min_per_bin
    if remaining < 0:
        return 0
    return stars_and_bars(remaining, k)
```

---

## 2️⃣ Pascal's Triangle

### Building Pascal's Triangle

```python
def pascals_triangle(n: int) -> list[list[int]]:
    """
    Build Pascal's triangle with n rows
    Row k contains C(k, 0), C(k, 1), ..., C(k, k)
    """
    triangle = [[1]]
    
    for i in range(1, n):
        row = [1]
        for j in range(1, i):
            row.append(triangle[i - 1][j - 1] + triangle[i - 1][j])
        row.append(1)
        triangle.append(row)
    
    return triangle


def pascals_row_mod(n: int, mod: int = 10**9 + 7) -> list[int]:
    """Get n-th row of Pascal's triangle mod m"""
    row = [1]
    for k in range(1, n + 1):
        row.append(row[-1] * (n - k + 1) // k)
    return [x % mod for x in row]
```

### Properties

```python
"""
Pascal's Triangle Properties:

1. Symmetry: C(n, r) = C(n, n-r)
2. Sum of row n: 2^n
3. Hockey stick: C(r,r) + C(r+1,r) + ... + C(n,r) = C(n+1, r+1)
4. Vandermonde: C(m+n, r) = Σ C(m,k) * C(n, r-k)
5. C(n, r) = C(n-1, r-1) + C(n-1, r)
"""
```

---

## 3️⃣ Inclusion-Exclusion Principle

### Basic Inclusion-Exclusion

```python
def count_divisible(n: int, divisors: list[int]) -> int:
    """
    Count numbers from 1 to n divisible by at least one divisor
    Using inclusion-exclusion
    
    |A ∪ B| = |A| + |B| - |A ∩ B|
    |A ∪ B ∪ C| = |A| + |B| + |C| - |A ∩ B| - |A ∩ C| - |B ∩ C| + |A ∩ B ∩ C|
    """
    from math import lcm
    from functools import reduce
    
    k = len(divisors)
    total = 0
    
    for mask in range(1, 1 << k):
        # Find LCM of selected divisors
        selected = [divisors[i] for i in range(k) if mask & (1 << i)]
        current_lcm = reduce(lcm, selected)
        
        # Count numbers divisible by this LCM
        count = n // current_lcm
        
        # Add or subtract based on number of elements (parity)
        if bin(mask).count('1') % 2 == 1:
            total += count
        else:
            total -= count
    
    return total


def count_coprime(n: int, m: int) -> int:
    """
    Count numbers from 1 to n that are coprime to m
    = n - (numbers divisible by any prime factor of m)
    """
    # Get prime factors of m
    prime_factors = []
    temp = m
    d = 2
    while d * d <= temp:
        if temp % d == 0:
            prime_factors.append(d)
            while temp % d == 0:
                temp //= d
        d += 1
    if temp > 1:
        prime_factors.append(temp)
    
    return n - count_divisible(n, prime_factors)
```

### Derangements

```python
def derangements(n: int) -> int:
    """
    Count permutations with no fixed points
    D(n) = (n-1) * (D(n-1) + D(n-2))
    D(n) = n! * Σ (-1)^k / k! for k = 0 to n
    
    Also: D(n) ≈ n! / e
    """
    if n == 0:
        return 1
    if n == 1:
        return 0
    
    dp = [0] * (n + 1)
    dp[0] = 1
    dp[1] = 0
    
    for i in range(2, n + 1):
        dp[i] = (i - 1) * (dp[i - 1] + dp[i - 2])
    
    return dp[n]


def derangements_mod(n: int, mod: int = 10**9 + 7) -> int:
    """Derangements with modular arithmetic"""
    if n == 0:
        return 1
    if n == 1:
        return 0
    
    prev2, prev1 = 1, 0
    for i in range(2, n + 1):
        curr = (i - 1) * (prev1 + prev2) % mod
        prev2, prev1 = prev1, curr
    
    return prev1
```

---

## 4️⃣ Catalan Numbers

### Definition and Formula

```python
def catalan(n: int) -> int:
    """
    n-th Catalan number
    C(n) = C(2n, n) / (n+1) = (2n)! / ((n+1)! * n!)
    
    C(0) = 1, C(1) = 1, C(2) = 2, C(3) = 5, C(4) = 14, ...
    """
    return factorial(2 * n) // (factorial(n + 1) * factorial(n))


def catalan_dp(n: int) -> int:
    """
    Using recurrence: C(n) = Σ C(i) * C(n-1-i) for i = 0 to n-1
    """
    if n <= 1:
        return 1
    
    dp = [0] * (n + 1)
    dp[0] = dp[1] = 1
    
    for i in range(2, n + 1):
        for j in range(i):
            dp[i] += dp[j] * dp[i - 1 - j]
    
    return dp[n]


def catalan_mod(n: int, mod: int = 10**9 + 7) -> int:
    """Catalan number mod p"""
    # C(n) = C(2n, n) / (n+1)
    num = 1
    for i in range(n + 2, 2 * n + 1):
        num = num * i % mod
    
    den = factorial(n) % mod
    return num * pow(den, mod - 2, mod) % mod
```

### Applications of Catalan Numbers

```python
"""
Catalan Number Applications:

1. Valid Parentheses: Ways to arrange n pairs of parentheses
   Example: n=3 → ((())), (()()), (())(), ()(()), ()()()

2. Binary Search Trees: Number of BSTs with n nodes

3. Full Binary Trees: Trees with n+1 leaves

4. Paths on Grid: Monotonic paths from (0,0) to (n,n) not crossing diagonal

5. Triangulations: Ways to triangulate a convex polygon with n+2 sides

6. Non-crossing Partitions: Ways to connect 2n points on a circle

7. Stack Permutations: Ways to sort using a stack
"""


def count_valid_parentheses(n: int) -> int:
    """Count valid parentheses with n pairs"""
    return catalan(n)


def count_bsts(n: int) -> int:
    """Count unique BSTs with n nodes (LC 96)"""
    return catalan(n)


def monotonic_paths(n: int) -> int:
    """
    Count paths from (0,0) to (n,n) with moves right/up
    that don't cross the diagonal y=x
    """
    return catalan(n)
```

---

## 5️⃣ Stirling Numbers

### Stirling Numbers of the Second Kind

```python
def stirling_second(n: int, k: int) -> int:
    """
    S(n, k) = Number of ways to partition n elements into k non-empty subsets
    
    Recurrence: S(n, k) = k * S(n-1, k) + S(n-1, k-1)
    """
    if n == 0 and k == 0:
        return 1
    if n == 0 or k == 0:
        return 0
    
    dp = [[0] * (k + 1) for _ in range(n + 1)]
    dp[0][0] = 1
    
    for i in range(1, n + 1):
        for j in range(1, min(i, k) + 1):
            dp[i][j] = j * dp[i - 1][j] + dp[i - 1][j - 1]
    
    return dp[n][k]


def bell_number(n: int) -> int:
    """
    B(n) = Total number of partitions of n elements
    B(n) = Σ S(n, k) for k = 0 to n
    """
    total = 0
    for k in range(n + 1):
        total += stirling_second(n, k)
    return total
```

### Stirling Numbers of the First Kind

```python
def stirling_first_unsigned(n: int, k: int) -> int:
    """
    |s(n, k)| = Number of permutations of n elements with exactly k cycles
    
    Recurrence: |s(n, k)| = (n-1) * |s(n-1, k)| + |s(n-1, k-1)|
    """
    if n == 0 and k == 0:
        return 1
    if n == 0 or k == 0:
        return 0
    
    dp = [[0] * (k + 1) for _ in range(n + 1)]
    dp[0][0] = 1
    
    for i in range(1, n + 1):
        for j in range(1, min(i, k) + 1):
            dp[i][j] = (i - 1) * dp[i - 1][j] + dp[i - 1][j - 1]
    
    return dp[n][k]
```

---

## 6️⃣ Burnside's Lemma (Counting with Symmetry)

```python
from math import gcd

def necklace_count(n: int, k: int) -> int:
    """
    Count distinct necklaces with n beads and k colors
    Using Burnside's lemma for rotational symmetry
    
    Number of distinct necklaces = (1/n) * Σ k^gcd(i, n) for i = 1 to n
    """
    total = 0
    for i in range(1, n + 1):
        total += k ** gcd(i, n)
    return total // n


def necklace_with_reflection(n: int, k: int) -> int:
    """
    Count necklaces considering both rotation and reflection
    """
    # Rotations contribution
    total = necklace_count(n, k) * n
    
    # Reflections contribution
    if n % 2 == 0:
        # n/2 reflections through opposite vertices
        total += (n // 2) * k ** (n // 2 + 1)
        # n/2 reflections through edge midpoints
        total += (n // 2) * k ** (n // 2)
    else:
        # n reflections through a vertex and opposite edge midpoint
        total += n * k ** ((n + 1) // 2)
    
    return total // (2 * n)
```

---

## 7️⃣ Common Patterns

### Counting Paths in Grid

```python
def grid_paths(m: int, n: int) -> int:
    """
    Count paths from (0,0) to (m-1, n-1) with only right/down moves
    = C(m+n-2, m-1) = C(m+n-2, n-1)
    """
    return nCr_efficient(m + n - 2, m - 1)


def grid_paths_with_obstacles(grid: list[list[int]]) -> int:
    """
    Count paths with obstacles (1 = obstacle)
    DP approach
    """
    m, n = len(grid), len(grid[0])
    if grid[0][0] == 1 or grid[m - 1][n - 1] == 1:
        return 0
    
    dp = [[0] * n for _ in range(m)]
    dp[0][0] = 1
    
    for i in range(m):
        for j in range(n):
            if grid[i][j] == 1:
                dp[i][j] = 0
            else:
                if i > 0:
                    dp[i][j] += dp[i - 1][j]
                if j > 0:
                    dp[i][j] += dp[i][j - 1]
    
    return dp[m - 1][n - 1]
```

### Counting Subsequences

```python
def count_distinct_subsequences(s: str, t: str) -> int:
    """
    Count distinct subsequences of s that equal t
    LC 115
    """
    m, n = len(s), len(t)
    dp = [[0] * (n + 1) for _ in range(m + 1)]
    
    # Empty t can be formed by any prefix of s
    for i in range(m + 1):
        dp[i][0] = 1
    
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            dp[i][j] = dp[i - 1][j]  # Don't use s[i-1]
            if s[i - 1] == t[j - 1]:
                dp[i][j] += dp[i - 1][j - 1]  # Use s[i-1]
    
    return dp[m][n]


def count_palindromic_subsequences(s: str) -> int:
    """
    Count distinct palindromic subsequences
    LC 730
    """
    n = len(s)
    MOD = 10**9 + 7
    
    dp = [[0] * n for _ in range(n)]
    
    for i in range(n):
        dp[i][i] = 1
    
    for length in range(2, n + 1):
        for i in range(n - length + 1):
            j = i + length - 1
            if s[i] == s[j]:
                left, right = i + 1, j - 1
                while left <= right and s[left] != s[i]:
                    left += 1
                while left <= right and s[right] != s[i]:
                    right -= 1
                
                if left > right:
                    dp[i][j] = dp[i + 1][j - 1] * 2 + 2
                elif left == right:
                    dp[i][j] = dp[i + 1][j - 1] * 2 + 1
                else:
                    dp[i][j] = dp[i + 1][j - 1] * 2 - dp[left + 1][right - 1]
            else:
                dp[i][j] = dp[i + 1][j] + dp[i][j - 1] - dp[i + 1][j - 1]
            
            dp[i][j] = (dp[i][j] + MOD) % MOD
    
    return dp[0][n - 1]
```

---

## ⏱️ Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| nCr (direct) | O(r) | O(1) |
| Pascal's Triangle | O(n²) | O(n²) |
| Catalan | O(n) | O(1) |
| Stirling Second | O(nk) | O(nk) |
| Inclusion-Exclusion | O(2^k) | O(k) |

---

## 📚 Practice Problems

| # | Problem | Key Concept |
|---|---------|-------------|
| 1 | [LC 62: Unique Paths](https://leetcode.com/problems/unique-paths/) | Grid paths = nCr |
| 2 | [LC 63: Unique Paths II](https://leetcode.com/problems/unique-paths-ii/) | Grid paths with obstacles |
| 3 | [LC 96: Unique BSTs](https://leetcode.com/problems/unique-binary-search-trees/) | Catalan numbers |
| 4 | [LC 115: Distinct Subsequences](https://leetcode.com/problems/distinct-subsequences/) | Counting subsequences |
| 5 | [LC 377: Combination Sum IV](https://leetcode.com/problems/combination-sum-iv/) | Combinations with repetition |
| 6 | [LC 518: Coin Change 2](https://leetcode.com/problems/coin-change-2/) | Stars and bars |
| 7 | [LC 634: Find the Derangement](https://leetcode.com/problems/find-the-derangement-of-an-array/) | Derangements |
| 8 | [LC 1359: Count Valid Courier Placements](https://leetcode.com/problems/count-all-valid-pickup-and-delivery-options/) | Interleaving count |
| 9 | [LC 1569: Number of Ways to Reorder Array](https://leetcode.com/problems/number-of-ways-to-reorder-array-to-get-same-bst/) | BST + combinatorics |
| 10 | [LC 2400: Ways to Reach a Position](https://leetcode.com/problems/number-of-ways-to-reach-a-position-after-exactly-k-steps/) | Pascal's triangle |

---

*Last Updated: 2024*
