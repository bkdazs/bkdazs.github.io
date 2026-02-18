---
title: "Primes and Sieve Algorithms"
topic: "Number Theory"
difficulty: "Medium-Hard"
tags: ["primes", "sieve", "number-theory", "factorization", "math"]
status: "complete"
weight: 1
---

# Primes and Sieve Algorithms

## 📚 Summary

Prime numbers are fundamental in number theory and appear frequently in competitive programming and interviews. The Sieve of Eratosthenes and its variants are essential for efficiently finding primes.

---

## 1️⃣ Primality Testing

### Basic Trial Division

```python
def is_prime_basic(n: int) -> bool:
    """
    Basic primality test - O(√n)
    """
    if n < 2:
        return False
    if n == 2:
        return True
    if n % 2 == 0:
        return False
    
    i = 3
    while i * i <= n:
        if n % i == 0:
            return False
        i += 2
    return True
```

### Optimized 6k±1 Method

```python
def is_prime(n: int) -> bool:
    """
    Optimized primality test using 6k±1 property
    All primes > 3 are of form 6k±1
    O(√n)
    """
    if n <= 1:
        return False
    if n <= 3:
        return True
    if n % 2 == 0 or n % 3 == 0:
        return False
    
    i = 5
    while i * i <= n:
        if n % i == 0 or n % (i + 2) == 0:
            return False
        i += 6
    return True
```

### Miller-Rabin Primality Test (Probabilistic)

```python
import random

def miller_rabin(n: int, k: int = 10) -> bool:
    """
    Miller-Rabin probabilistic primality test
    Accuracy: probability of false positive ≤ 4^(-k)
    O(k * log³n)
    """
    if n < 2:
        return False
    if n == 2 or n == 3:
        return True
    if n % 2 == 0:
        return False
    
    # Write n-1 as 2^r * d where d is odd
    r, d = 0, n - 1
    while d % 2 == 0:
        r += 1
        d //= 2
    
    # Witness loop
    def check_composite(a: int) -> bool:
        x = pow(a, d, n)
        if x == 1 or x == n - 1:
            return False
        for _ in range(r - 1):
            x = pow(x, 2, n)
            if x == n - 1:
                return False
        return True
    
    # Test with k random witnesses
    for _ in range(k):
        a = random.randrange(2, n - 1)
        if check_composite(a):
            return False
    return True


def miller_rabin_deterministic(n: int) -> bool:
    """
    Deterministic Miller-Rabin for n < 3,317,044,064,679,887,385,961,981
    Uses fixed witnesses
    """
    if n < 2:
        return False
    if n == 2:
        return True
    if n % 2 == 0:
        return False
    
    r, d = 0, n - 1
    while d % 2 == 0:
        r += 1
        d //= 2
    
    def check_composite(a: int) -> bool:
        if a >= n:
            return False
        x = pow(a, d, n)
        if x == 1 or x == n - 1:
            return False
        for _ in range(r - 1):
            x = pow(x, 2, n)
            if x == n - 1:
                return False
        return True
    
    # Witnesses that work for different ranges
    witnesses = [2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37]
    
    for a in witnesses:
        if a >= n:
            continue
        if check_composite(a):
            return False
    return True
```

---

## 2️⃣ Sieve of Eratosthenes

### Basic Sieve

```python
def sieve_basic(n: int) -> list[int]:
    """
    Basic Sieve of Eratosthenes
    Returns all primes up to n
    Time: O(n log log n), Space: O(n)
    """
    if n < 2:
        return []
    
    is_prime = [True] * (n + 1)
    is_prime[0] = is_prime[1] = False
    
    p = 2
    while p * p <= n:
        if is_prime[p]:
            # Mark all multiples of p as composite
            for i in range(p * p, n + 1, p):
                is_prime[i] = False
        p += 1
    
    return [i for i in range(n + 1) if is_prime[i]]


def sieve_bool_array(n: int) -> list[bool]:
    """
    Returns boolean array where is_prime[i] = True if i is prime
    """
    if n < 2:
        return [False] * (n + 1)
    
    is_prime = [True] * (n + 1)
    is_prime[0] = is_prime[1] = False
    
    for p in range(2, int(n**0.5) + 1):
        if is_prime[p]:
            for i in range(p * p, n + 1, p):
                is_prime[i] = False
    
    return is_prime
```

### Optimized Sieve (Odds Only)

```python
def sieve_optimized(n: int) -> list[int]:
    """
    Optimized sieve - only check odd numbers
    Time: O(n log log n), Space: O(n/2)
    """
    if n < 2:
        return []
    if n == 2:
        return [2]
    
    # Only store odd numbers: is_prime[i] represents 2*i+1
    size = (n - 1) // 2
    is_prime = [True] * (size + 1)
    
    # Sieve odd numbers only
    i = 1
    while (2 * i + 1) ** 2 <= n:
        if is_prime[i]:
            p = 2 * i + 1
            # Start from p*p, mark odd multiples
            j = (p * p - 1) // 2
            while j <= size:
                is_prime[j] = False
                j += p
        i += 1
    
    primes = [2]
    primes.extend(2 * i + 1 for i in range(1, size + 1) if is_prime[i])
    return primes
```

### Segmented Sieve

```python
def segmented_sieve(n: int, segment_size: int = 10**6) -> list[int]:
    """
    Segmented Sieve for large n
    Useful when n is large but we can't allocate O(n) memory
    Time: O(n log log n), Space: O(√n + segment_size)
    """
    if n < 2:
        return []
    
    limit = int(n**0.5) + 1
    
    # Find small primes up to √n using basic sieve
    small_primes = sieve_basic(limit)
    
    primes = list(small_primes)  # Start with small primes
    
    # Process segments
    low = limit + 1
    while low <= n:
        high = min(low + segment_size - 1, n)
        
        # Boolean array for current segment
        is_prime = [True] * (high - low + 1)
        
        # Mark composites using small primes
        for p in small_primes:
            # Find first multiple of p in [low, high]
            start = ((low + p - 1) // p) * p
            if start == p:
                start = p * p
            
            for j in range(start, high + 1, p):
                is_prime[j - low] = False
        
        # Collect primes in this segment
        for i in range(high - low + 1):
            if is_prime[i]:
                primes.append(low + i)
        
        low = high + 1
    
    return primes
```

### Linear Sieve (Euler's Sieve)

```python
def linear_sieve(n: int) -> tuple[list[int], list[int]]:
    """
    Linear Sieve / Euler's Sieve
    Each composite is marked exactly once by its smallest prime factor
    Also computes SPF (Smallest Prime Factor)
    Time: O(n), Space: O(n)
    """
    spf = [0] * (n + 1)  # Smallest Prime Factor
    primes = []
    
    for i in range(2, n + 1):
        if spf[i] == 0:  # i is prime
            spf[i] = i
            primes.append(i)
        
        for p in primes:
            if p > spf[i] or i * p > n:
                break
            spf[i * p] = p
    
    return primes, spf


def get_spf(n: int) -> list[int]:
    """
    Returns Smallest Prime Factor for each number up to n
    """
    spf = list(range(n + 1))  # spf[i] = i initially
    
    for i in range(2, int(n**0.5) + 1):
        if spf[i] == i:  # i is prime
            for j in range(i * i, n + 1, i):
                if spf[j] == j:
                    spf[j] = i
    
    return spf
```

---

## 3️⃣ Prime Factorization

### Basic Factorization

```python
def prime_factorization(n: int) -> dict[int, int]:
    """
    Returns prime factorization as {prime: exponent}
    Time: O(√n)
    """
    factors = {}
    
    # Check for 2
    while n % 2 == 0:
        factors[2] = factors.get(2, 0) + 1
        n //= 2
    
    # Check odd factors
    i = 3
    while i * i <= n:
        while n % i == 0:
            factors[i] = factors.get(i, 0) + 1
            n //= i
        i += 2
    
    # If n is still > 1, it's a prime factor
    if n > 1:
        factors[n] = 1
    
    return factors


def prime_factors_list(n: int) -> list[int]:
    """
    Returns list of prime factors (with repetition)
    """
    factors = []
    
    while n % 2 == 0:
        factors.append(2)
        n //= 2
    
    i = 3
    while i * i <= n:
        while n % i == 0:
            factors.append(i)
            n //= i
        i += 2
    
    if n > 1:
        factors.append(n)
    
    return factors
```

### Factorization Using SPF (Precomputed)

```python
def factorize_with_spf(n: int, spf: list[int]) -> dict[int, int]:
    """
    Fast factorization using precomputed SPF
    Time: O(log n)
    """
    factors = {}
    
    while n > 1:
        p = spf[n]
        factors[p] = factors.get(p, 0) + 1
        n //= p
    
    return factors


def all_divisors(n: int) -> list[int]:
    """
    Find all divisors of n
    Time: O(√n)
    """
    divisors = []
    i = 1
    while i * i <= n:
        if n % i == 0:
            divisors.append(i)
            if i != n // i:
                divisors.append(n // i)
        i += 1
    return sorted(divisors)


def count_divisors(n: int) -> int:
    """
    Count number of divisors using prime factorization
    If n = p1^a1 * p2^a2 * ... * pk^ak
    Number of divisors = (a1+1) * (a2+1) * ... * (ak+1)
    """
    factors = prime_factorization(n)
    count = 1
    for exp in factors.values():
        count *= (exp + 1)
    return count
```

---

## 4️⃣ Applications

### Count Primes (LeetCode 204)

```python
def count_primes(n: int) -> int:
    """
    Count primes less than n
    """
    if n <= 2:
        return 0
    
    is_prime = [True] * n
    is_prime[0] = is_prime[1] = False
    
    for p in range(2, int(n**0.5) + 1):
        if is_prime[p]:
            for i in range(p * p, n, p):
                is_prime[i] = False
    
    return sum(is_prime)
```

### Sum of Divisors

```python
def sum_of_divisors(n: int) -> int:
    """
    Sum of all divisors using prime factorization
    If n = p^a, sum = (p^(a+1) - 1) / (p - 1)
    """
    factors = prime_factorization(n)
    result = 1
    
    for p, a in factors.items():
        # Geometric series: 1 + p + p² + ... + p^a
        result *= (pow(p, a + 1) - 1) // (p - 1)
    
    return result


def sum_of_divisors_sieve(n: int) -> list[int]:
    """
    Compute sum of divisors for all numbers 1 to n
    Time: O(n log n)
    """
    sigma = [0] * (n + 1)
    
    for i in range(1, n + 1):
        for j in range(i, n + 1, i):
            sigma[j] += i
    
    return sigma
```

### Euler's Totient Function (Phi)

```python
def euler_phi(n: int) -> int:
    """
    Euler's totient function - count numbers coprime to n
    φ(n) = n * ∏(1 - 1/p) for all prime factors p of n
    Time: O(√n)
    """
    result = n
    p = 2
    
    while p * p <= n:
        if n % p == 0:
            # Remove factor p
            while n % p == 0:
                n //= p
            result -= result // p
        p += 1
    
    if n > 1:  # n is a prime factor
        result -= result // n
    
    return result


def phi_sieve(n: int) -> list[int]:
    """
    Compute phi for all numbers 1 to n using sieve
    Time: O(n log log n)
    """
    phi = list(range(n + 1))  # phi[i] = i initially
    
    for i in range(2, n + 1):
        if phi[i] == i:  # i is prime
            for j in range(i, n + 1, i):
                phi[j] -= phi[j] // i
    
    return phi
```

### Prime in Range

```python
def primes_in_range(left: int, right: int) -> list[int]:
    """
    Find all primes in range [left, right]
    Uses segmented sieve
    """
    if right < 2:
        return []
    
    limit = int(right**0.5) + 1
    small_primes = sieve_basic(limit)
    
    size = right - left + 1
    is_prime = [True] * size
    
    # Handle special case for 0 and 1
    if left <= 1:
        for i in range(max(0, 0 - left), min(size, 2 - left)):
            is_prime[i] = False
    
    # Sieve using small primes
    for p in small_primes:
        start = max(p * p, ((left + p - 1) // p) * p)
        for j in range(start, right + 1, p):
            if j != p:  # Don't mark p itself
                is_prime[j - left] = False
    
    return [left + i for i in range(size) if is_prime[i] and left + i >= 2]
```

---

## 5️⃣ Advanced Topics

### Prime Counting Function Approximation

```python
import math

def prime_pi_approx(n: int) -> int:
    """
    Approximate number of primes ≤ n
    π(n) ≈ n / ln(n)
    """
    if n < 2:
        return 0
    return int(n / math.log(n))


def nth_prime_approx(n: int) -> int:
    """
    Approximate the n-th prime
    p_n ≈ n * ln(n)
    """
    if n < 1:
        return 0
    return int(n * math.log(n))
```

### Goldbach's Conjecture Check

```python
def goldbach_partition(n: int, is_prime: list[bool]) -> tuple[int, int] | None:
    """
    Find two primes that sum to n (Goldbach's conjecture)
    Every even integer > 2 is the sum of two primes
    """
    if n <= 2 or n % 2 == 1:
        return None
    
    for p in range(2, n // 2 + 1):
        if is_prime[p] and is_prime[n - p]:
            return (p, n - p)
    
    return None
```

### Twin Primes

```python
def twin_primes(n: int) -> list[tuple[int, int]]:
    """
    Find all twin prime pairs up to n
    Twin primes: primes p where p+2 is also prime
    """
    is_prime = sieve_bool_array(n)
    twins = []
    
    for p in range(2, n - 1):
        if is_prime[p] and is_prime[p + 2]:
            twins.append((p, p + 2))
    
    return twins
```

---

## ⏱️ Complexity Analysis

| Algorithm | Time | Space | Use Case |
|-----------|------|-------|----------|
| Trial Division | O(√n) | O(1) | Single primality test |
| 6k±1 Method | O(√n) | O(1) | Optimized single test |
| Miller-Rabin | O(k log³n) | O(1) | Large n, probabilistic |
| Basic Sieve | O(n log log n) | O(n) | All primes up to n |
| Segmented Sieve | O(n log log n) | O(√n) | Very large n, memory limited |
| Linear Sieve | O(n) | O(n) | Need SPF for factorization |
| Factorization | O(√n) | O(log n) | Single number |
| Factorization (SPF) | O(log n) | O(n) | Multiple numbers |

---

## 📚 Practice Problems

### Easy-Medium
| # | Problem | Key Concept |
|---|---------|-------------|
| 1 | [LC 204: Count Primes](https://leetcode.com/problems/count-primes/) | Sieve |
| 2 | [LC 263: Ugly Number](https://leetcode.com/problems/ugly-number/) | Factorization |
| 3 | [LC 264: Ugly Number II](https://leetcode.com/problems/ugly-number-ii/) | DP with primes |
| 4 | [LC 1175: Prime Arrangements](https://leetcode.com/problems/prime-arrangements/) | Count primes |

### Medium-Hard
| # | Problem | Key Concept |
|---|---------|-------------|
| 5 | [LC 866: Prime Palindrome](https://leetcode.com/problems/prime-palindrome/) | Generate + test |
| 6 | [LC 952: Largest Component by Common Factor](https://leetcode.com/problems/largest-component-size-by-common-factor/) | SPF + Union-Find |
| 7 | [LC 2507: Smallest Value After Replacing](https://leetcode.com/problems/smallest-value-after-replacing-with-sum-of-prime-factors/) | Factorization |
| 8 | [LC 2523: Closest Prime Numbers](https://leetcode.com/problems/closest-prime-numbers-in-range/) | Segmented sieve |
| 9 | [LC 2601: Prime Subtraction Operation](https://leetcode.com/problems/prime-subtraction-operation/) | Binary search + primes |
| 10 | [LC 2709: Greatest Common Divisor Traversal](https://leetcode.com/problems/greatest-common-divisor-traversal/) | SPF + Union-Find |

---

## 🔑 Key Takeaways

1. **Basic primality**: O(√n) trial division works for n ≤ 10^12
2. **Sieve**: Use for multiple queries or counting primes up to n
3. **Linear sieve**: Get SPF array for O(log n) factorization
4. **Segmented sieve**: When n is large but memory is limited
5. **Miller-Rabin**: For very large n or when deterministic check is slow

---

*Last Updated: 2024*
