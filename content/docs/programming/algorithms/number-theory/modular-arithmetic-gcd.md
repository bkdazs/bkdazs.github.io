---
title: "Modular Arithmetic and GCD"
topic: "Number Theory"
difficulty: "Medium-Hard"
tags: ["modular-arithmetic", "gcd", "extended-euclid", "modular-inverse", "math"]
status: "complete"
weight: 2
---

# Modular Arithmetic and GCD

## 📚 Summary

Modular arithmetic is essential for handling large numbers in competitive programming. GCD algorithms and modular operations are fundamental building blocks.

---

## 1️⃣ GCD and LCM

### Euclidean Algorithm

```python
def gcd(a: int, b: int) -> int:
    """
    Greatest Common Divisor using Euclidean algorithm
    Time: O(log(min(a,b)))
    """
    while b:
        a, b = b, a % b
    return a


def gcd_recursive(a: int, b: int) -> int:
    """Recursive GCD"""
    return a if b == 0 else gcd_recursive(b, a % b)


# Python built-in
from math import gcd


def lcm(a: int, b: int) -> int:
    """
    Least Common Multiple
    lcm(a,b) * gcd(a,b) = a * b
    """
    return a * b // gcd(a, b)


# Python 3.9+ built-in
from math import lcm


def gcd_of_list(nums: list[int]) -> int:
    """GCD of multiple numbers"""
    from functools import reduce
    return reduce(gcd, nums)


def lcm_of_list(nums: list[int]) -> int:
    """LCM of multiple numbers"""
    from functools import reduce
    return reduce(lcm, nums)
```

### Binary GCD (Stein's Algorithm)

```python
def binary_gcd(a: int, b: int) -> int:
    """
    Binary GCD - faster for large numbers (no division)
    Uses only subtraction and bit operations
    Time: O(log(a) + log(b))
    """
    if a == 0:
        return b
    if b == 0:
        return a
    
    # Find common powers of 2
    shift = 0
    while ((a | b) & 1) == 0:  # Both even
        a >>= 1
        b >>= 1
        shift += 1
    
    # Remove remaining factors of 2 from a
    while (a & 1) == 0:
        a >>= 1
    
    while b:
        while (b & 1) == 0:
            b >>= 1
        
        if a > b:
            a, b = b, a
        
        b -= a
    
    return a << shift
```

---

## 2️⃣ Extended Euclidean Algorithm

### Finding GCD with Coefficients

```python
def extended_gcd(a: int, b: int) -> tuple[int, int, int]:
    """
    Extended Euclidean Algorithm
    Returns (g, x, y) such that a*x + b*y = g = gcd(a, b)
    Time: O(log(min(a,b)))
    """
    if b == 0:
        return (a, 1, 0)
    
    g, x1, y1 = extended_gcd(b, a % b)
    x = y1
    y = x1 - (a // b) * y1
    
    return (g, x, y)


def extended_gcd_iterative(a: int, b: int) -> tuple[int, int, int]:
    """Iterative version of Extended GCD"""
    old_r, r = a, b
    old_s, s = 1, 0
    old_t, t = 0, 1
    
    while r != 0:
        quotient = old_r // r
        old_r, r = r, old_r - quotient * r
        old_s, s = s, old_s - quotient * s
        old_t, t = t, old_t - quotient * t
    
    return (old_r, old_s, old_t)  # gcd, x, y
```

### Solving Linear Diophantine Equations

```python
def solve_diophantine(a: int, b: int, c: int) -> tuple[int, int] | None:
    """
    Solve ax + by = c
    Returns (x, y) if solution exists, None otherwise
    Solution exists iff gcd(a,b) divides c
    """
    g, x0, y0 = extended_gcd(a, b)
    
    if c % g != 0:
        return None
    
    # Scale the solution
    x = x0 * (c // g)
    y = y0 * (c // g)
    
    return (x, y)


def all_solutions_diophantine(a: int, b: int, c: int) -> str:
    """
    General solution formula for ax + by = c
    If (x0, y0) is a particular solution, all solutions are:
    x = x0 + (b/g)*t
    y = y0 - (a/g)*t
    for any integer t
    """
    sol = solve_diophantine(a, b, c)
    if sol is None:
        return "No solution"
    
    x0, y0 = sol
    g = gcd(a, b)
    
    return f"x = {x0} + {b//g}*t, y = {y0} - {a//g}*t for integer t"
```

---

## 3️⃣ Modular Arithmetic Basics

### Basic Operations

```python
MOD = 10**9 + 7

def mod_add(a: int, b: int, mod: int = MOD) -> int:
    """(a + b) mod m"""
    return ((a % mod) + (b % mod)) % mod


def mod_sub(a: int, b: int, mod: int = MOD) -> int:
    """(a - b) mod m"""
    return ((a % mod) - (b % mod) + mod) % mod


def mod_mul(a: int, b: int, mod: int = MOD) -> int:
    """(a * b) mod m"""
    return ((a % mod) * (b % mod)) % mod


def mod_pow(base: int, exp: int, mod: int = MOD) -> int:
    """
    Fast modular exponentiation: base^exp mod m
    Time: O(log exp)
    """
    result = 1
    base %= mod
    
    while exp > 0:
        if exp & 1:
            result = (result * base) % mod
        exp >>= 1
        base = (base * base) % mod
    
    return result


# Python built-in - more efficient
pow(base, exp, mod)
```

### Properties

```python
"""
Modular Arithmetic Properties:
1. (a + b) mod m = ((a mod m) + (b mod m)) mod m
2. (a - b) mod m = ((a mod m) - (b mod m) + m) mod m
3. (a * b) mod m = ((a mod m) * (b mod m)) mod m
4. (a ^ b) mod m = ((a mod m) ^ b) mod m

Division requires modular inverse!
(a / b) mod m = (a * b^(-1)) mod m
"""
```

---

## 4️⃣ Modular Inverse

### Using Extended GCD

```python
def mod_inverse_extended_gcd(a: int, mod: int) -> int | None:
    """
    Modular inverse using Extended GCD
    Returns x such that (a * x) mod m = 1
    Exists only if gcd(a, m) = 1
    Time: O(log m)
    """
    g, x, _ = extended_gcd(a, mod)
    
    if g != 1:
        return None  # Inverse doesn't exist
    
    return (x % mod + mod) % mod
```

### Using Fermat's Little Theorem

```python
def mod_inverse_fermat(a: int, mod: int) -> int:
    """
    Modular inverse using Fermat's Little Theorem
    a^(-1) ≡ a^(p-2) (mod p) when p is prime
    Only works when mod is PRIME
    Time: O(log mod)
    """
    return pow(a, mod - 2, mod)


# Common usage with MOD = 10^9 + 7 (prime)
MOD = 10**9 + 7

def mod_div(a: int, b: int, mod: int = MOD) -> int:
    """
    (a / b) mod m where m is prime
    """
    return (a * pow(b, mod - 2, mod)) % mod
```

### Precomputing Inverses

```python
def precompute_inverses(n: int, mod: int) -> list[int]:
    """
    Precompute modular inverses for 1 to n
    Time: O(n)
    
    Formula: inv[i] = -(mod // i) * inv[mod % i] mod m
    """
    inv = [0] * (n + 1)
    inv[1] = 1
    
    for i in range(2, n + 1):
        inv[i] = (mod - (mod // i) * inv[mod % i] % mod) % mod
    
    return inv


def precompute_factorial_inverse(n: int, mod: int) -> tuple[list[int], list[int]]:
    """
    Precompute factorials and their inverses for nCr
    """
    fact = [1] * (n + 1)
    for i in range(1, n + 1):
        fact[i] = fact[i - 1] * i % mod
    
    inv_fact = [1] * (n + 1)
    inv_fact[n] = pow(fact[n], mod - 2, mod)
    
    for i in range(n - 1, -1, -1):
        inv_fact[i] = inv_fact[i + 1] * (i + 1) % mod
    
    return fact, inv_fact
```

---

## 5️⃣ Modular Combinations

### nCr mod p

```python
class ModularCombinations:
    """
    Precompute factorials for fast nCr mod p
    """
    def __init__(self, n: int, mod: int = 10**9 + 7):
        self.mod = mod
        self.fact = [1] * (n + 1)
        self.inv_fact = [1] * (n + 1)
        
        for i in range(1, n + 1):
            self.fact[i] = self.fact[i - 1] * i % mod
        
        self.inv_fact[n] = pow(self.fact[n], mod - 2, mod)
        for i in range(n - 1, -1, -1):
            self.inv_fact[i] = self.inv_fact[i + 1] * (i + 1) % mod
    
    def nCr(self, n: int, r: int) -> int:
        """Returns n choose r mod p"""
        if r < 0 or r > n:
            return 0
        return self.fact[n] * self.inv_fact[r] % self.mod * self.inv_fact[n - r] % self.mod
    
    def nPr(self, n: int, r: int) -> int:
        """Returns n permute r mod p"""
        if r < 0 or r > n:
            return 0
        return self.fact[n] * self.inv_fact[n - r] % self.mod


# Usage
comb = ModularCombinations(10**5)
print(comb.nCr(100, 50))
```

### Lucas' Theorem

```python
def lucas_nCr(n: int, r: int, p: int) -> int:
    """
    nCr mod p using Lucas' theorem
    Works when p is prime and n, r can be very large
    Time: O(p + log_p(n))
    
    Lucas: C(n,r) ≡ ∏ C(n_i, r_i) (mod p)
    where n = n_k*p^k + ... + n_0 and r = r_k*p^k + ... + r_0
    """
    # Precompute factorials mod p
    fact = [1] * p
    for i in range(1, p):
        fact[i] = fact[i - 1] * i % p
    
    def nCr_small(n: int, r: int) -> int:
        if r > n:
            return 0
        return fact[n] * pow(fact[r], p - 2, p) % p * pow(fact[n - r], p - 2, p) % p
    
    result = 1
    while n > 0 or r > 0:
        result = result * nCr_small(n % p, r % p) % p
        n //= p
        r //= p
    
    return result
```

---

## 6️⃣ Chinese Remainder Theorem

### CRT for Two Equations

```python
def chinese_remainder_two(r1: int, m1: int, r2: int, m2: int) -> tuple[int, int] | None:
    """
    Find x such that:
    x ≡ r1 (mod m1)
    x ≡ r2 (mod m2)
    
    Returns (x, lcm(m1, m2)) or None if no solution
    """
    g, p, q = extended_gcd(m1, m2)
    
    if (r2 - r1) % g != 0:
        return None
    
    lcm_val = m1 * m2 // g
    x = (r1 + m1 * p * ((r2 - r1) // g)) % lcm_val
    
    return (x, lcm_val)
```

### General CRT

```python
def chinese_remainder(remainders: list[int], moduli: list[int]) -> tuple[int, int] | None:
    """
    Find x such that:
    x ≡ r[i] (mod m[i]) for all i
    
    Returns (x, lcm of all moduli) or None if no solution
    """
    if len(remainders) != len(moduli):
        return None
    
    x, m = remainders[0], moduli[0]
    
    for i in range(1, len(remainders)):
        result = chinese_remainder_two(x, m, remainders[i], moduli[i])
        if result is None:
            return None
        x, m = result
    
    return (x, m)


def chinese_remainder_coprime(remainders: list[int], moduli: list[int]) -> int:
    """
    CRT when all moduli are pairwise coprime
    Faster version using formula:
    x = Σ r_i * M_i * y_i (mod M)
    where M = ∏m_i, M_i = M/m_i, and y_i = M_i^(-1) mod m_i
    """
    M = 1
    for m in moduli:
        M *= m
    
    x = 0
    for r, m in zip(remainders, moduli):
        Mi = M // m
        yi = pow(Mi, -1, m)  # Python 3.8+
        x = (x + r * Mi * yi) % M
    
    return x
```

---

## 7️⃣ Applications

### Count Numbers in Range with Given Remainders

```python
def count_numbers_with_remainder(n: int, r: int, m: int) -> int:
    """
    Count numbers in [1, n] that have remainder r when divided by m
    """
    if r == 0:
        return n // m
    return (n - r) // m + 1 if n >= r else 0
```

### Matrix Exponentiation

```python
def matrix_mult(A: list[list[int]], B: list[list[int]], mod: int) -> list[list[int]]:
    """Multiply two matrices mod m"""
    n = len(A)
    m = len(B[0])
    k = len(B)
    
    C = [[0] * m for _ in range(n)]
    for i in range(n):
        for j in range(m):
            for l in range(k):
                C[i][j] = (C[i][j] + A[i][l] * B[l][j]) % mod
    return C


def matrix_pow(M: list[list[int]], p: int, mod: int) -> list[list[int]]:
    """
    Matrix exponentiation: M^p mod m
    Time: O(n³ log p)
    """
    n = len(M)
    result = [[1 if i == j else 0 for j in range(n)] for i in range(n)]
    
    while p > 0:
        if p & 1:
            result = matrix_mult(result, M, mod)
        M = matrix_mult(M, M, mod)
        p >>= 1
    
    return result


def fibonacci_mod(n: int, mod: int = 10**9 + 7) -> int:
    """
    Find n-th Fibonacci number mod m using matrix exponentiation
    F(n) = [[1,1],[1,0]]^n * [[1],[0]]
    Time: O(log n)
    """
    if n <= 1:
        return n
    
    M = [[1, 1], [1, 0]]
    result = matrix_pow(M, n - 1, mod)
    
    return result[0][0]
```

### Discrete Logarithm (Baby-Step Giant-Step)

```python
def baby_giant(a: int, b: int, m: int) -> int | None:
    """
    Find x such that a^x ≡ b (mod m)
    Baby-step Giant-step algorithm
    Time: O(√m), Space: O(√m)
    """
    from math import ceil, sqrt
    
    n = ceil(sqrt(m)) + 1
    
    # Baby step: store a^j for j = 0, 1, ..., n-1
    an = pow(a, n, m)
    baby = {}
    val = 1
    for j in range(n):
        baby[val] = j
        val = (val * a) % m
    
    # Giant step: check a^(n*i) * b for i = 0, 1, ..., n
    val = b
    for i in range(n + 1):
        if val in baby:
            ans = i * n + baby[val]
            if ans > 0:
                return ans
        val = (val * an) % m
    
    return None
```

---

## ⏱️ Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| GCD (Euclidean) | O(log min(a,b)) | O(1) |
| Extended GCD | O(log min(a,b)) | O(1) |
| Mod Inverse (Fermat) | O(log m) | O(1) |
| Precompute Inverses | O(n) | O(n) |
| nCr with precompute | O(1) | O(n) |
| Lucas' Theorem | O(p + log n) | O(p) |
| CRT | O(n log M) | O(1) |
| Matrix Power | O(k³ log p) | O(k²) |

---

## 📚 Practice Problems

| # | Problem | Key Concept |
|---|---------|-------------|
| 1 | [LC 50: Pow(x, n)](https://leetcode.com/problems/powx-n/) | Fast exponentiation |
| 2 | [LC 509: Fibonacci Number](https://leetcode.com/problems/fibonacci-number/) | Matrix exponentiation |
| 3 | [LC 1015: Smallest Integer Divisible by K](https://leetcode.com/problems/smallest-integer-divisible-by-k/) | Modular arithmetic |
| 4 | [LC 1073: Adding Two Negabinary Numbers](https://leetcode.com/problems/adding-two-negabinary-numbers/) | Number systems |
| 5 | [LC 1250: Check If Good Array](https://leetcode.com/problems/check-if-it-is-a-good-array/) | GCD property |
| 6 | [LC 1916: Ways to Build Rooms](https://leetcode.com/problems/count-ways-to-build-rooms-in-an-ant-colony/) | Modular inverse |
| 7 | [LC 2550: Count Collisions of Monkeys](https://leetcode.com/problems/count-collisions-of-monkeys-on-a-polygon/) | Modular exponentiation |

---

*Last Updated: 2024*
