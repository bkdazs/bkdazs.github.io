---
title: "Digit Dynamic Programming"
topic: "Counting Numbers with Digit Constraints"
difficulty: "Hard"
tags: ["dp", "digit-dp", "counting", "combinatorics"]
status: "complete"
weight: 8
---

# Digit DP

## Summary / TL;DR

Digit DP solves problems of the form: "Count numbers in range [L, R] satisfying some property based on their digits." The key technique is to count numbers up to N, then use `count(R) - count(L-1)`.

**Key Insight:** Process digits from most significant to least, tracking whether we're still "tight" to the bound and any relevant state about digits seen so far.

---

## When to Use

- **Count numbers in range** with digit constraints
- **Sum of digits** problems
- **Digit properties:** No repeated digits, digits in sorted order, etc.
- **Divisibility** based on digits
- **Palindrome numbers** in a range

### Pattern Recognition

| Clue | Approach |
|------|----------|
| "Count numbers from L to R where..." | Digit DP |
| "Numbers with no repeated digits" | Track used digits |
| "Sum of digits equals/divides..." | Track running sum |
| "Digits in increasing order" | Track last digit |

---

## Big-O Complexity

| Problem Type | Time | Space |
|--------------|------|-------|
| Basic digit DP | O(D × States × 10) | O(D × States) |
| With used mask | O(D × 2^10 × 10) | O(D × 2^10) |

Where D = number of digits (typically ≤ 20).

---

## Core Implementation Templates

### Template 1: Basic Digit DP Framework

```python
from functools import lru_cache

def count_numbers_with_property(n: int) -> int:
    """
    Template for counting numbers from 0 to n with some property.
    
    States:
    - pos: current digit position (0 to len-1)
    - tight: are we still bounded by n?
    - started: have we placed a non-zero digit yet?
    - [additional state based on problem]
    """
    if n < 0:
        return 0
    
    digits = [int(d) for d in str(n)]
    
    @lru_cache(maxsize=None)
    def dp(pos: int, tight: bool, started: bool) -> int:
        """
        pos: current position in digits
        tight: if True, can only use digits up to digits[pos]
        started: if True, we've started (no leading zeros)
        """
        if pos == len(digits):
            return 1 if started else 0  # Count valid numbers
        
        limit = digits[pos] if tight else 9
        result = 0
        
        for d in range(0, limit + 1):
            new_tight = tight and (d == digits[pos])
            new_started = started or (d != 0)
            
            # Add problem-specific conditions here
            # if not valid_digit(d, state):
            #     continue
            
            result += dp(pos + 1, new_tight, new_started)
        
        return result
    
    return dp(0, True, False)


def count_in_range(left: int, right: int) -> int:
    """
    Count numbers in [left, right] with some property.
    """
    return count_numbers_with_property(right) - count_numbers_with_property(left - 1)
```

### Template 2: Count Numbers Without Repeated Digits

```python
def count_numbers_with_unique_digits(n: int) -> int:
    """
    Count numbers from 1 to n where all digits are unique.
    
    Example: 123 is valid, 121 is not (1 repeats)
    
    Time: O(D * 2^10 * 10), Space: O(D * 2^10)
    """
    if n <= 0:
        return 0
    
    digits = [int(d) for d in str(n)]
    
    @lru_cache(maxsize=None)
    def dp(pos: int, mask: int, tight: bool, started: bool) -> int:
        if pos == len(digits):
            return 1 if started else 0
        
        limit = digits[pos] if tight else 9
        result = 0
        
        for d in range(0, limit + 1):
            if started and (mask & (1 << d)):
                continue  # Digit already used
            
            new_mask = mask
            new_started = started or (d != 0)
            
            if new_started:
                new_mask |= (1 << d)
            
            result += dp(
                pos + 1,
                new_mask,
                tight and (d == digits[pos]),
                new_started
            )
        
        return result
    
    return dp(0, 0, True, False)


def count_special_numbers(n: int) -> int:
    """
    LeetCode 2376: Count numbers in [1, n] with all unique digits.
    Same as above, cleaner implementation.
    """
    if n <= 0:
        return 0
    
    s = str(n)
    
    @lru_cache(maxsize=None)
    def dp(pos: int, mask: int, tight: bool, started: bool) -> int:
        if pos == len(s):
            return 1 if started else 0
        
        limit = int(s[pos]) if tight else 9
        count = 0
        
        # Option 1: Don't start yet (leading zero)
        if not started:
            count += dp(pos + 1, mask, False, False)
            # Start with digits 1 to limit
            for d in range(1, limit + 1):
                if not (mask & (1 << d)):
                    count += dp(pos + 1, mask | (1 << d), tight and (d == limit), True)
        else:
            # Already started, pick unused digit
            for d in range(0, limit + 1):
                if not (mask & (1 << d)):
                    count += dp(pos + 1, mask | (1 << d), tight and (d == limit), True)
        
        return count
    
    return dp(0, 0, True, False)
```

### Template 3: Sum of Digits Constraints

```python
def count_numbers_with_digit_sum(n: int, target_sum: int) -> int:
    """
    Count numbers from 0 to n where sum of digits equals target_sum.
    
    Time: O(D * target_sum * 10), Space: O(D * target_sum)
    """
    if n < 0:
        return 0
    
    digits = [int(d) for d in str(n)]
    
    @lru_cache(maxsize=None)
    def dp(pos: int, digit_sum: int, tight: bool, started: bool) -> int:
        if digit_sum > target_sum:
            return 0
        if pos == len(digits):
            return 1 if digit_sum == target_sum and started else 0
        
        limit = digits[pos] if tight else 9
        result = 0
        
        for d in range(0, limit + 1):
            new_started = started or (d != 0)
            new_sum = digit_sum + d if new_started else 0
            
            result += dp(
                pos + 1,
                new_sum,
                tight and (d == digits[pos]),
                new_started
            )
        
        return result
    
    return dp(0, 0, True, False)


def count_integers_with_digit_sum_in_range(num1: str, num2: str, 
                                           min_sum: int, max_sum: int) -> int:
    """
    LeetCode 2719: Count integers in [num1, num2] with digit sum in [min_sum, max_sum].
    
    Time: O(D * max_sum), Space: O(D * max_sum)
    """
    MOD = 10**9 + 7
    
    def count_up_to(s: str) -> int:
        @lru_cache(maxsize=None)
        def dp(pos: int, digit_sum: int, tight: bool, started: bool) -> int:
            if digit_sum > max_sum:
                return 0
            if pos == len(s):
                return 1 if min_sum <= digit_sum <= max_sum and started else 0
            
            limit = int(s[pos]) if tight else 9
            result = 0
            
            for d in range(0, limit + 1):
                new_started = started or (d != 0)
                new_sum = digit_sum + d if new_started else 0
                
                result = (result + dp(
                    pos + 1,
                    new_sum,
                    tight and (d == limit),
                    new_started
                )) % MOD
            
            return result
        
        return dp(0, 0, True, False)
    
    # Subtract 1 from num1 (as string)
    def subtract_one(s: str) -> str:
        s = list(s)
        i = len(s) - 1
        while i >= 0 and s[i] == '0':
            s[i] = '9'
            i -= 1
        if i >= 0:
            s[i] = str(int(s[i]) - 1)
        result = ''.join(s).lstrip('0')
        return result if result else '0'
    
    upper = count_up_to(num2)
    lower = count_up_to(subtract_one(num1))
    
    return (upper - lower + MOD) % MOD
```

### Template 4: Digits in Sorted Order

```python
def count_non_decreasing_digit_numbers(n: int) -> int:
    """
    Count numbers where digits are non-decreasing (e.g., 1234, 1137, 555).
    
    State: last digit used
    
    Time: O(D * 10), Space: O(D * 10)
    """
    if n <= 0:
        return 0
    
    digits = [int(d) for d in str(n)]
    
    @lru_cache(maxsize=None)
    def dp(pos: int, last: int, tight: bool, started: bool) -> int:
        if pos == len(digits):
            return 1 if started else 0
        
        limit = digits[pos] if tight else 9
        result = 0
        
        # If not started, can skip (leading zero)
        if not started:
            result += dp(pos + 1, 0, False, False)
            start_digit = 1
        else:
            start_digit = last  # Must be >= last digit
        
        for d in range(start_digit, limit + 1):
            result += dp(
                pos + 1,
                d,
                tight and (d == digits[pos]),
                True
            )
        
        return result
    
    return dp(0, 0, True, False)


def count_strictly_increasing_digit_numbers(n: int) -> int:
    """
    Count numbers where digits are strictly increasing (e.g., 1234, 159).
    """
    if n <= 0:
        return 0
    
    digits = [int(d) for d in str(n)]
    
    @lru_cache(maxsize=None)
    def dp(pos: int, last: int, tight: bool, started: bool) -> int:
        if pos == len(digits):
            return 1 if started else 0
        
        limit = digits[pos] if tight else 9
        result = 0
        
        if not started:
            result += dp(pos + 1, -1, False, False)
            start_digit = 1
        else:
            start_digit = last + 1  # Must be > last digit
        
        for d in range(start_digit, limit + 1):
            result += dp(
                pos + 1,
                d,
                tight and (d == digits[pos]),
                True
            )
        
        return result
    
    return dp(0, -1, True, False)
```

### Template 5: Divisibility Constraints

```python
def count_numbers_divisible_by_k(n: int, k: int) -> int:
    """
    Count numbers from 1 to n divisible by k.
    
    Track remainder of number formed so far.
    
    Time: O(D * k * 10), Space: O(D * k)
    """
    if n <= 0:
        return 0
    
    digits = [int(d) for d in str(n)]
    
    @lru_cache(maxsize=None)
    def dp(pos: int, remainder: int, tight: bool, started: bool) -> int:
        if pos == len(digits):
            return 1 if remainder == 0 and started else 0
        
        limit = digits[pos] if tight else 9
        result = 0
        
        for d in range(0, limit + 1):
            new_started = started or (d != 0)
            new_rem = (remainder * 10 + d) % k if new_started else 0
            
            result += dp(
                pos + 1,
                new_rem,
                tight and (d == digits[pos]),
                new_started
            )
        
        return result
    
    return dp(0, 0, True, False)


def count_beautiful_numbers(low: int, high: int, k: int) -> int:
    """
    LeetCode 2827: Count numbers in [low, high] where:
    - Divisible by k
    - Sum of odd-position digits == sum of even-position digits
    """
    def count_up_to(n: int) -> int:
        if n <= 0:
            return 0
        
        digits = [int(d) for d in str(n)]
        
        @lru_cache(maxsize=None)
        def dp(pos: int, rem: int, diff: int, tight: bool, started: bool) -> int:
            """
            rem: current number mod k
            diff: (sum of odd positions) - (sum of even positions)
            """
            if pos == len(digits):
                return 1 if rem == 0 and diff == 0 and started else 0
            
            limit = digits[pos] if tight else 9
            result = 0
            
            for d in range(0, limit + 1):
                new_started = started or (d != 0)
                new_rem = (rem * 10 + d) % k if new_started else 0
                
                # Odd position (1-indexed): add to diff
                # Even position: subtract from diff
                if new_started:
                    if pos % 2 == 0:  # Odd position (0-indexed even)
                        new_diff = diff + d
                    else:
                        new_diff = diff - d
                else:
                    new_diff = 0
                
                result += dp(
                    pos + 1,
                    new_rem,
                    new_diff,
                    tight and (d == limit),
                    new_started
                )
            
            return result
        
        return dp(0, 0, 0, True, False)
    
    return count_up_to(high) - count_up_to(low - 1)
```

### Template 6: Palindrome Numbers

```python
def count_palindromes_up_to(n: int) -> int:
    """
    Count palindrome numbers from 1 to n.
    
    Two approaches:
    1. Generate all palindromes and count
    2. Digit DP with mirroring constraint
    """
    if n < 1:
        return 0
    
    count = 0
    
    # Generate odd-length palindromes: d, aba, abcba, ...
    # First half determines the palindrome
    for length in range(1, len(str(n)) + 1):
        half_len = (length + 1) // 2
        
        start = 10 ** (half_len - 1) if half_len > 1 else 1
        end = 10 ** half_len
        
        for first_half in range(start, end):
            s = str(first_half)
            if length % 2 == 0:
                palindrome = s + s[::-1]
            else:
                palindrome = s + s[-2::-1]
            
            num = int(palindrome)
            if num <= n:
                count += 1
            else:
                break
    
    return count


def count_palindromes_in_range_dp(left: int, right: int) -> int:
    """
    Digit DP approach for palindrome counting.
    Track first half and ensure second half mirrors.
    """
    def count_up_to(n: int) -> int:
        if n < 1:
            return 0
        
        s = str(n)
        length = len(s)
        
        @lru_cache(maxsize=None)
        def dp(pos: int, tight: bool, first_half: tuple) -> int:
            if pos == length:
                return 1
            
            limit = int(s[pos]) if tight else 9
            result = 0
            
            mid = (length - 1) // 2
            
            for d in range(0 if pos > 0 else 1, limit + 1):  # No leading zero
                if pos <= mid:
                    # Building first half
                    new_half = first_half + (d,)
                    result += dp(pos + 1, tight and (d == limit), new_half)
                else:
                    # Must match mirrored position
                    mirror_pos = length - 1 - pos
                    if first_half[mirror_pos] == d:
                        result += dp(pos + 1, tight and (d == limit), first_half)
            
            return result
        
        return dp(0, True, ())
    
    return count_up_to(right) - count_up_to(left - 1)
```

---

## Worked Examples

### Example 1: Count Numbers with Unique Digits ≤ 123

```
n = 123, digits = [1, 2, 3]

dp(0, mask=0, tight=True, started=False):
  d=0: Not started, dp(1, 0, False, False)
  d=1: Started, dp(1, 0010, True, True)

dp(1, mask=0, tight=False, started=False):
  ... counts all 2-digit numbers with unique digits starting fresh

dp(1, mask=0010, tight=True, started=True):
  d=0: mask has 0? No. dp(2, 0011, False, True)
  d=1: mask has 1? Yes! Skip.
  d=2: mask has 2? No. dp(2, 0110, True, True)

dp(2, mask=0110, tight=True, started=True):
  d=0: mask has 0? No. Count it.
  d=1: mask has 1? Yes! Skip.
  d=2: mask has 2? Yes! Skip.
  d=3: mask has 3? No. Count it.

Total: 110 numbers (1-9: 9, 10-99: 81, 100-123: 20)
```

### Example 2: Count Numbers Divisible by 7 ≤ 100

```
n = 100, k = 7

dp(0, rem=0, tight=True, started=False):
  d=0: dp(1, 0, False, False)  → not started
  d=1: dp(1, 1, True, True)    → number starts with 1

dp(1, rem=1, tight=True, started=True):
  d=0: new_rem = (1*10+0)%7 = 3, dp(2, 3, False, True)
  ...

Eventually count: 7, 14, 21, 28, 35, 42, 49, 56, 63, 70, 77, 84, 91, 98
= 14 numbers
```

---

## Edge Cases & Gotchas

```python
# 1. n = 0
# Usually return 0 (no positive integers)

# 2. Leading zeros
# Use 'started' flag; don't count "007" as valid

# 3. Very large n (string input)
def handle_large_n(n: str):
    # Process as string, not int
    pass

# 4. Range queries [L, R]
# Always use count(R) - count(L-1)
# Be careful with L=0 or L=1

# 5. Off-by-one in positions
# pos=0 is most significant digit
# Mirror positions: mirror_pos = len - 1 - pos

# 6. Integer overflow
# Use modular arithmetic when counting
MOD = 10**9 + 7
```

---

## Interview Tips & Communication

### Framework

1. **Recognize pattern:** "Count numbers in range with digit property"

2. **Define state:**
   - pos (current digit)
   - tight (bounded by n?)
   - started (leading zeros?)
   - Problem-specific state

3. **Transitions:**
   - For each possible digit d
   - Update state accordingly

4. **Range handling:**
   - `count(R) - count(L-1)`

### Key Phrases

- "I'll use digit DP, processing digits from most significant to least."
- "The tight flag tracks whether we're still bounded by n."
- "I'll track [state] to handle the constraint about [property]."
- "For a range [L, R], I'll compute count(R) - count(L-1)."

---

## Practice Problems

### Basic Digit DP

| # | Problem | Key Concept |
|---|---------|-------------|
| 1 | [Count Special Integers](https://leetcode.com/problems/count-special-integers/) | Unique digits |
| 2 | [Numbers At Most N Given Digit Set](https://leetcode.com/problems/numbers-at-most-n-given-digit-set/) | Limited digits |
| 3 | [Count Numbers with Unique Digits](https://leetcode.com/problems/count-numbers-with-unique-digits/) | Combinatorics |
| 4 | [Rotated Digits](https://leetcode.com/problems/rotated-digits/) | Digit transformation |

### Sum of Digits

| # | Problem | Key Concept |
|---|---------|-------------|
| 5 | [Sum of Digits in Base K](https://leetcode.com/problems/sum-of-digits-in-base-k/) | Base conversion |
| 6 | [Count Integers With Even Digit Sum](https://leetcode.com/problems/count-integers-with-even-digit-sum/) | Digit sum parity |
| 7 | [Count of Integers](https://leetcode.com/problems/count-of-integers/) | Digit sum range |

### Sorted Digits

| # | Problem | Key Concept |
|---|---------|-------------|
| 8 | [Non-decreasing Digit Numbers](https://www.geeksforgeeks.org/count-numbers-n-digits-digits-form-strictly-increasing-sequence/) | Monotonic |
| 9 | [Count Stepping Numbers](https://leetcode.com/problems/count-stepping-numbers-in-range/) | Adjacent diff = 1 |
| 10 | [Digit Count in Range](https://leetcode.com/problems/digit-count-in-range/) | Specific digit |

### Divisibility

| # | Problem | Key Concept |
|---|---------|-------------|
| 11 | [Numbers With Repeated Digits](https://leetcode.com/problems/numbers-with-repeated-digits/) | Complement counting |
| 12 | [K-th Smallest in Lexicographical Order](https://leetcode.com/problems/k-th-smallest-in-lexicographical-order/) | Lex order |
| 13 | [Clumsy Factorial](https://leetcode.com/problems/clumsy-factorial/) | Operation sequence |

### Palindromes

| # | Problem | Key Concept |
|---|---------|-------------|
| 14 | [Palindromic Substrings](https://leetcode.com/problems/palindromic-substrings/) | Not digit DP but related |
| 15 | [Super Palindromes](https://leetcode.com/problems/super-palindromes/) | Generate + check |
| 16 | [Find the Closest Palindrome](https://leetcode.com/problems/find-the-closest-palindrome/) | Construction |
| 17 | [Strictly Palindromic Number](https://leetcode.com/problems/strictly-palindromic-number/) | Multi-base |

### Advanced

| # | Problem | Key Concept |
|---|---------|-------------|
| 18 | [Number of Digit One](https://leetcode.com/problems/number-of-digit-one/) | Count specific digit |
| 19 | [Digit Count](https://www.geeksforgeeks.org/digit-dp-introduction/) | Introduction |
| 20 | [Count Numbers with Given Properties](https://codeforces.com/problemset/problem/628/D) | Complex constraints |

---

## References

- [CP Algorithms: Digit DP](https://cp-algorithms.com/dynamic_programming/digit-dp.html)
- [Codeforces: Digit DP Tutorial](https://codeforces.com/blog/entry/53960)
- [USACO Guide: Digit DP](https://usaco.guide/gold/digit-dp)
- [TopCoder: Digit DP](https://www.topcoder.com/community/competitive-programming/tutorials/digit-dp/)
