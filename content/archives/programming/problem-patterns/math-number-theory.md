---
title: "Math & Number Theory Patterns"
topic: "Problem Patterns"
difficulty: "Medium"
tags: ["math", "number-theory", "gcd", "prime", "combinatorics"]
status: "complete"
weight: 24
---

# Math & Number Theory Patterns

## 📚 Overview

Mathematical patterns appear frequently in coding interviews. Master these fundamental algorithms.

---

## 1️⃣ GCD and LCM

```python
def gcd(a: int, b: int) -> int:
    """Euclidean algorithm - O(log min(a,b))"""
    while b:
        a, b = b, a % b
    return a

def gcd_recursive(a: int, b: int) -> int:
    """Recursive GCD"""
    return a if b == 0 else gcd_recursive(b, a % b)

def lcm(a: int, b: int) -> int:
    """Least Common Multiple"""
    return a * b // gcd(a, b)

# Extended Euclidean Algorithm
def extended_gcd(a: int, b: int) -> tuple[int, int, int]:
    """Returns (gcd, x, y) where ax + by = gcd"""
    if b == 0:
        return a, 1, 0
    
    g, x1, y1 = extended_gcd(b, a % b)
    x = y1
    y = x1 - (a // b) * y1
    return g, x, y
```

### X of a Kind in Deck (LC 914)

```python
def has_groups_size_x(deck: list[int]) -> bool:
    """Can partition deck into groups of size >= 2 with same card"""
    from collections import Counter
    from math import gcd
    from functools import reduce
    
    counts = Counter(deck).values()
    return reduce(gcd, counts) >= 2
```

---

## 2️⃣ Prime Numbers

```python
def is_prime(n: int) -> bool:
    """Check if n is prime - O(√n)"""
    if n < 2:
        return False
    if n == 2:
        return True
    if n % 2 == 0:
        return False
    
    for i in range(3, int(n**0.5) + 1, 2):
        if n % i == 0:
            return False
    return True

def sieve_of_eratosthenes(n: int) -> list[int]:
    """All primes up to n - O(n log log n)"""
    if n < 2:
        return []
    
    is_prime = [True] * (n + 1)
    is_prime[0] = is_prime[1] = False
    
    for i in range(2, int(n**0.5) + 1):
        if is_prime[i]:
            for j in range(i*i, n + 1, i):
                is_prime[j] = False
    
    return [i for i in range(n + 1) if is_prime[i]]

def prime_factorization(n: int) -> dict[int, int]:
    """Get prime factors with their powers"""
    factors = {}
    d = 2
    
    while d * d <= n:
        while n % d == 0:
            factors[d] = factors.get(d, 0) + 1
            n //= d
        d += 1
    
    if n > 1:
        factors[n] = factors.get(n, 0) + 1
    
    return factors
```

### Count Primes (LC 204)

```python
def count_primes(n: int) -> int:
    """Count primes less than n"""
    if n < 2:
        return 0
    
    is_prime = [True] * n
    is_prime[0] = is_prime[1] = False
    
    for i in range(2, int(n**0.5) + 1):
        if is_prime[i]:
            is_prime[i*i::i] = [False] * len(is_prime[i*i::i])
    
    return sum(is_prime)
```

---

## 3️⃣ Modular Arithmetic

```python
MOD = 10**9 + 7

def mod_add(a: int, b: int) -> int:
    return (a + b) % MOD

def mod_sub(a: int, b: int) -> int:
    return (a - b + MOD) % MOD

def mod_mul(a: int, b: int) -> int:
    return (a * b) % MOD

def mod_pow(base: int, exp: int, mod: int = MOD) -> int:
    """Fast exponentiation - O(log exp)"""
    result = 1
    base %= mod
    
    while exp > 0:
        if exp & 1:
            result = result * base % mod
        base = base * base % mod
        exp >>= 1
    
    return result

def mod_inverse(a: int, mod: int = MOD) -> int:
    """Modular inverse using Fermat's little theorem (mod must be prime)"""
    return mod_pow(a, mod - 2, mod)
```

---

## 4️⃣ Combinatorics

```python
class Combinatorics:
    """Precompute factorials for nCr calculations"""
    def __init__(self, n: int, mod: int = 10**9 + 7):
        self.mod = mod
        self.fact = [1] * (n + 1)
        self.inv_fact = [1] * (n + 1)
        
        for i in range(1, n + 1):
            self.fact[i] = self.fact[i-1] * i % mod
        
        self.inv_fact[n] = pow(self.fact[n], mod - 2, mod)
        for i in range(n - 1, -1, -1):
            self.inv_fact[i] = self.inv_fact[i+1] * (i+1) % mod
    
    def nCr(self, n: int, r: int) -> int:
        """Combinations - n choose r"""
        if r < 0 or r > n:
            return 0
        return self.fact[n] * self.inv_fact[r] % self.mod * self.inv_fact[n-r] % self.mod
    
    def nPr(self, n: int, r: int) -> int:
        """Permutations"""
        if r < 0 or r > n:
            return 0
        return self.fact[n] * self.inv_fact[n-r] % self.mod
```

### Unique Paths (LC 62)

```python
def unique_paths(m: int, n: int) -> int:
    """Using combinatorics: C(m+n-2, m-1)"""
    from math import comb
    return comb(m + n - 2, m - 1)
```

---

## 5️⃣ Integer Operations

### Reverse Integer (LC 7)

```python
def reverse(x: int) -> int:
    """Reverse digits with overflow check"""
    INT_MAX = 2**31 - 1
    INT_MIN = -2**31
    
    sign = 1 if x >= 0 else -1
    x = abs(x)
    
    result = 0
    while x:
        digit = x % 10
        x //= 10
        
        # Overflow check
        if result > (INT_MAX - digit) // 10:
            return 0
        
        result = result * 10 + digit
    
    return sign * result
```

### Palindrome Number (LC 9)

```python
def is_palindrome(x: int) -> bool:
    """Check if number is palindrome without string conversion"""
    if x < 0 or (x % 10 == 0 and x != 0):
        return False
    
    reversed_half = 0
    while x > reversed_half:
        reversed_half = reversed_half * 10 + x % 10
        x //= 10
    
    return x == reversed_half or x == reversed_half // 10
```

### Add Digits (LC 258)

```python
def add_digits(num: int) -> int:
    """Digital root - sum digits until single digit"""
    if num == 0:
        return 0
    return 1 + (num - 1) % 9  # Formula for digital root
```

---

## 6️⃣ Power and Sqrt

### Pow(x, n) (LC 50)

```python
def my_pow(x: float, n: int) -> float:
    """Calculate x^n - O(log n)"""
    if n < 0:
        x = 1 / x
        n = -n
    
    result = 1.0
    while n:
        if n & 1:
            result *= x
        x *= x
        n >>= 1
    
    return result
```

### Sqrt(x) (LC 69)

```python
def my_sqrt(x: int) -> int:
    """Integer square root using binary search"""
    if x < 2:
        return x
    
    left, right = 1, x // 2
    
    while left <= right:
        mid = (left + right) // 2
        square = mid * mid
        
        if square == x:
            return mid
        elif square < x:
            left = mid + 1
        else:
            right = mid - 1
    
    return right

def my_sqrt_newton(x: int) -> int:
    """Newton's method"""
    if x < 2:
        return x
    
    r = x
    while r * r > x:
        r = (r + x // r) // 2
    
    return r
```

---

## 7️⃣ Bit Counting

### Counting Bits (LC 338)

```python
def count_bits(n: int) -> list[int]:
    """Count 1s in binary for 0 to n"""
    result = [0] * (n + 1)
    
    for i in range(1, n + 1):
        result[i] = result[i >> 1] + (i & 1)
    
    return result
```

### Hamming Weight (LC 191)

```python
def hamming_weight(n: int) -> int:
    """Count 1 bits"""
    count = 0
    while n:
        count += n & 1
        n >>= 1
    return count

def hamming_weight_trick(n: int) -> int:
    """Brian Kernighan's algorithm"""
    count = 0
    while n:
        n &= n - 1  # Clear rightmost 1
        count += 1
    return count
```

---

## 8️⃣ Sequences

### Fibonacci (LC 509)

```python
def fib(n: int) -> int:
    """Fibonacci number - O(n) time, O(1) space"""
    if n < 2:
        return n
    
    a, b = 0, 1
    for _ in range(2, n + 1):
        a, b = b, a + b
    
    return b

def fib_matrix(n: int) -> int:
    """Matrix exponentiation - O(log n)"""
    if n < 2:
        return n
    
    def multiply(A: list[list[int]], B: list[list[int]]) -> list[list[int]]:
        return [
            [A[0][0]*B[0][0] + A[0][1]*B[1][0], A[0][0]*B[0][1] + A[0][1]*B[1][1]],
            [A[1][0]*B[0][0] + A[1][1]*B[1][0], A[1][0]*B[0][1] + A[1][1]*B[1][1]]
        ]
    
    def power(M: list[list[int]], p: int) -> list[list[int]]:
        if p == 1:
            return M
        
        if p % 2 == 0:
            half = power(M, p // 2)
            return multiply(half, half)
        else:
            return multiply(M, power(M, p - 1))
    
    M = [[1, 1], [1, 0]]
    result = power(M, n)
    return result[0][1]
```

### Catalan Numbers

```python
def catalan(n: int) -> int:
    """nth Catalan number"""
    if n <= 1:
        return 1
    
    catalan = [0] * (n + 1)
    catalan[0] = catalan[1] = 1
    
    for i in range(2, n + 1):
        for j in range(i):
            catalan[i] += catalan[j] * catalan[i - j - 1]
    
    return catalan[n]

# Applications: Valid parentheses, BST count, paths in grid
```

---

## 9️⃣ Geometry

### Valid Square (LC 593)

```python
def valid_square(p1: list[int], p2: list[int], p3: list[int], p4: list[int]) -> bool:
    """Check if four points form a square"""
    def dist_sq(a, b):
        return (a[0] - b[0])**2 + (a[1] - b[1])**2
    
    points = [p1, p2, p3, p4]
    distances = []
    
    for i in range(4):
        for j in range(i + 1, 4):
            distances.append(dist_sq(points[i], points[j]))
    
    distances.sort()
    
    # 4 equal sides, 2 equal diagonals, diagonals = 2 * sides
    return (distances[0] > 0 and
            distances[0] == distances[1] == distances[2] == distances[3] and
            distances[4] == distances[5] and
            distances[4] == 2 * distances[0])
```

### Max Points on a Line (LC 149)

```python
def max_points(points: list[list[int]]) -> int:
    """Maximum points on a line"""
    from math import gcd
    from collections import defaultdict
    
    if len(points) <= 2:
        return len(points)
    
    def get_slope(p1, p2):
        dx = p2[0] - p1[0]
        dy = p2[1] - p1[1]
        
        if dx == 0:
            return (0, 1)  # Vertical line
        if dy == 0:
            return (1, 0)  # Horizontal line
        
        # Normalize slope
        g = gcd(abs(dx), abs(dy))
        dx, dy = dx // g, dy // g
        
        if dx < 0:
            dx, dy = -dx, -dy
        
        return (dy, dx)
    
    max_count = 1
    
    for i in range(len(points)):
        slopes = defaultdict(int)
        
        for j in range(i + 1, len(points)):
            slope = get_slope(points[i], points[j])
            slopes[slope] += 1
            max_count = max(max_count, slopes[slope] + 1)
    
    return max_count
```

---

## 🔟 Random & Probability

### Shuffle an Array (LC 384)

```python
import random

class Solution:
    def __init__(self, nums: list[int]):
        self.original = nums[:]
        self.array = nums
    
    def reset(self) -> list[int]:
        self.array = self.original[:]
        return self.array
    
    def shuffle(self) -> list[int]:
        """Fisher-Yates shuffle"""
        for i in range(len(self.array) - 1, 0, -1):
            j = random.randint(0, i)
            self.array[i], self.array[j] = self.array[j], self.array[i]
        return self.array
```

### Random Pick with Weight (LC 528)

```python
class RandomPickWeight:
    def __init__(self, w: list[int]):
        self.prefix = []
        total = 0
        for weight in w:
            total += weight
            self.prefix.append(total)
    
    def pickIndex(self) -> int:
        import random
        import bisect
        
        target = random.random() * self.prefix[-1]
        return bisect.bisect_right(self.prefix, target)
```

---

## 📝 Related Problems

| Problem | LC # | Pattern |
|---------|------|---------|
| Count Primes | 204 | Sieve |
| Pow(x, n) | 50 | Fast Power |
| Sqrt(x) | 69 | Binary Search |
| Reverse Integer | 7 | Digit Manipulation |
| Palindrome Number | 9 | Digit Manipulation |
| Unique Paths | 62 | Combinatorics |
| Counting Bits | 338 | Bit DP |
| Fibonacci | 509 | Sequence |
| Max Points on Line | 149 | Geometry |
| Shuffle Array | 384 | Fisher-Yates |
| Random Pick Weight | 528 | Prefix Sum |
| Happy Number | 202 | Cycle Detection |
| Ugly Number II | 264 | DP |
| Super Ugly Number | 313 | Heap |

---

*Last Updated: 2024*
