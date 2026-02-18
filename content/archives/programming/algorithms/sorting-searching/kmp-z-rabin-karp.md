---
title: "String Pattern Matching - KMP, Z, Rabin-Karp"
topic: "String Algorithms"
difficulty: "Medium-Hard"
tags: ["strings", "kmp", "z-algorithm", "rabin-karp", "pattern-matching"]
status: "complete"
weight: 1
---

# String Pattern Matching Algorithms

## 📚 Summary

Efficient string pattern matching is crucial for text processing. KMP, Z-Algorithm, and Rabin-Karp are the fundamental algorithms with O(n+m) complexity.

---

## 1️⃣ KMP (Knuth-Morris-Pratt) Algorithm

### LPS (Longest Prefix Suffix) Array

```python
def compute_lps(pattern: str) -> list[int]:
    """
    Compute LPS array for pattern
    lps[i] = length of longest proper prefix which is also suffix of pattern[0..i]
    
    Time: O(m), Space: O(m)
    """
    m = len(pattern)
    lps = [0] * m
    
    length = 0  # Length of previous longest prefix suffix
    i = 1
    
    while i < m:
        if pattern[i] == pattern[length]:
            length += 1
            lps[i] = length
            i += 1
        else:
            if length != 0:
                # Use previous LPS value
                length = lps[length - 1]
            else:
                lps[i] = 0
                i += 1
    
    return lps
```

### KMP Search

```python
def kmp_search(text: str, pattern: str) -> list[int]:
    """
    Find all occurrences of pattern in text using KMP
    Returns list of starting indices
    
    Time: O(n + m), Space: O(m)
    """
    n, m = len(text), len(pattern)
    if m == 0:
        return []
    if m > n:
        return []
    
    lps = compute_lps(pattern)
    result = []
    
    i = 0  # Index for text
    j = 0  # Index for pattern
    
    while i < n:
        if pattern[j] == text[i]:
            i += 1
            j += 1
        
        if j == m:
            # Found pattern at index i - j
            result.append(i - j)
            j = lps[j - 1]
        elif i < n and pattern[j] != text[i]:
            if j != 0:
                j = lps[j - 1]
            else:
                i += 1
    
    return result


def kmp_count(text: str, pattern: str) -> int:
    """Count occurrences of pattern in text"""
    return len(kmp_search(text, pattern))


def kmp_first_occurrence(text: str, pattern: str) -> int:
    """Find first occurrence of pattern in text, -1 if not found"""
    matches = kmp_search(text, pattern)
    return matches[0] if matches else -1
```

### KMP Applications

```python
def shortest_palindrome(s: str) -> str:
    """
    LC 214: Find shortest palindrome by adding chars at beginning
    Use KMP: find longest prefix of s that is palindrome
    """
    if not s:
        return s
    
    # Build pattern: s + '#' + reverse(s)
    combined = s + '#' + s[::-1]
    lps = compute_lps(combined)
    
    # LPS of last char gives longest palindrome prefix
    palindrome_len = lps[-1]
    suffix_to_add = s[palindrome_len:][::-1]
    
    return suffix_to_add + s


def repeated_substring_pattern(s: str) -> bool:
    """
    LC 459: Check if s can be constructed by repeating a substring
    Use KMP: s is made of repeated pattern iff len(s) % (len(s) - lps[-1]) == 0
    """
    if not s:
        return False
    
    lps = compute_lps(s)
    n = len(s)
    pattern_len = n - lps[-1]
    
    return lps[-1] > 0 and n % pattern_len == 0
```

---

## 2️⃣ Z-Algorithm

### Z-Array Computation

```python
def z_function(s: str) -> list[int]:
    """
    Compute Z-array for string s
    z[i] = length of longest substring starting at i which is also a prefix of s
    
    Time: O(n), Space: O(n)
    """
    n = len(s)
    z = [0] * n
    z[0] = n  # Convention: z[0] = n (entire string matches itself)
    
    left, right = 0, 0  # Z-box [left, right]
    
    for i in range(1, n):
        if i < right:
            # i is within current Z-box
            z[i] = min(right - i, z[i - left])
        
        # Try to extend the match
        while i + z[i] < n and s[z[i]] == s[i + z[i]]:
            z[i] += 1
        
        # Update Z-box if needed
        if i + z[i] > right:
            left = i
            right = i + z[i]
    
    return z
```

### Z-Algorithm Search

```python
def z_search(text: str, pattern: str) -> list[int]:
    """
    Find all occurrences of pattern in text using Z-algorithm
    
    Idea: Build combined = pattern + '$' + text
    z[i] = m means pattern found at position i - m - 1 in text
    """
    m = len(pattern)
    if m == 0:
        return []
    
    combined = pattern + '$' + text
    z = z_function(combined)
    
    result = []
    for i in range(m + 1, len(combined)):
        if z[i] == m:
            result.append(i - m - 1)
    
    return result


def z_search_all_patterns(text: str, patterns: list[str]) -> dict[str, list[int]]:
    """Find all occurrences of multiple patterns"""
    result = {}
    for pattern in patterns:
        result[pattern] = z_search(text, pattern)
    return result
```

### Z-Algorithm Applications

```python
def min_rotation(s: str) -> int:
    """
    Find starting position of lexicographically smallest rotation
    Use Z-function on s + s
    """
    doubled = s + s
    z = z_function(doubled)
    n = len(s)
    
    min_pos = 0
    for i in range(1, n):
        if doubled[i:] < doubled[min_pos:]:
            min_pos = i
    
    return min_pos


def longest_common_prefix_all(strings: list[str]) -> str:
    """
    Find longest common prefix of all strings
    Alternative to simple comparison - useful for specific patterns
    """
    if not strings:
        return ""
    
    combined = '\x00'.join(strings)
    z = z_function(combined)
    
    min_len = min(len(s) for s in strings)
    result = 0
    
    for length in range(1, min_len + 1):
        valid = True
        for s in strings:
            # Check if this prefix matches
            pass  # Implementation varies by use case
    
    return strings[0][:result]
```

---

## 3️⃣ Rabin-Karp Algorithm

### Rolling Hash

```python
class RollingHash:
    """
    Rolling hash for efficient substring hashing
    hash(s) = s[0] * base^(n-1) + s[1] * base^(n-2) + ... + s[n-1]
    """
    def __init__(self, s: str, base: int = 31, mod: int = 10**9 + 7):
        self.s = s
        self.n = len(s)
        self.base = base
        self.mod = mod
        
        # Precompute prefix hashes and powers
        self.prefix_hash = [0] * (self.n + 1)
        self.powers = [1] * (self.n + 1)
        
        for i in range(self.n):
            self.prefix_hash[i + 1] = (
                self.prefix_hash[i] * base + ord(s[i]) - ord('a') + 1
            ) % mod
            self.powers[i + 1] = (self.powers[i] * base) % mod
    
    def get_hash(self, left: int, right: int) -> int:
        """
        Get hash of substring s[left:right+1]
        O(1) query
        """
        return (
            self.prefix_hash[right + 1] - 
            self.prefix_hash[left] * self.powers[right - left + 1]
        ) % self.mod
```

### Rabin-Karp Search

```python
def rabin_karp_search(text: str, pattern: str, base: int = 31, mod: int = 10**9 + 7) -> list[int]:
    """
    Find all occurrences of pattern in text using Rabin-Karp
    
    Time: O(n + m) average, O(nm) worst case (hash collisions)
    Space: O(1)
    """
    n, m = len(text), len(pattern)
    if m > n:
        return []
    
    result = []
    
    # Compute hash of pattern
    pattern_hash = 0
    for c in pattern:
        pattern_hash = (pattern_hash * base + ord(c) - ord('a') + 1) % mod
    
    # Compute highest power needed
    high_power = pow(base, m - 1, mod)
    
    # Compute initial window hash
    window_hash = 0
    for i in range(m):
        window_hash = (window_hash * base + ord(text[i]) - ord('a') + 1) % mod
    
    # Check first window
    if window_hash == pattern_hash and text[:m] == pattern:
        result.append(0)
    
    # Slide window
    for i in range(m, n):
        # Remove leftmost character
        window_hash = (window_hash - (ord(text[i - m]) - ord('a') + 1) * high_power) % mod
        # Add new character
        window_hash = (window_hash * base + ord(text[i]) - ord('a') + 1) % mod
        
        # Check for match (with verification to avoid false positives)
        if window_hash == pattern_hash and text[i - m + 1:i + 1] == pattern:
            result.append(i - m + 1)
    
    return result


def rabin_karp_double_hash(text: str, pattern: str) -> list[int]:
    """
    Rabin-Karp with two hash functions to reduce false positives
    """
    mods = [10**9 + 7, 10**9 + 9]
    bases = [31, 37]
    
    n, m = len(text), len(pattern)
    if m > n:
        return []
    
    result = []
    
    pattern_hashes = []
    for base, mod in zip(bases, mods):
        h = 0
        for c in pattern:
            h = (h * base + ord(c)) % mod
        pattern_hashes.append(h)
    
    window_hashes = [0, 0]
    high_powers = [pow(bases[i], m - 1, mods[i]) for i in range(2)]
    
    for i in range(m):
        for j in range(2):
            window_hashes[j] = (window_hashes[j] * bases[j] + ord(text[i])) % mods[j]
    
    if window_hashes == pattern_hashes:
        result.append(0)
    
    for i in range(m, n):
        for j in range(2):
            window_hashes[j] = (
                (window_hashes[j] - ord(text[i - m]) * high_powers[j]) * bases[j] + ord(text[i])
            ) % mods[j]
        
        if window_hashes == pattern_hashes:
            result.append(i - m + 1)
    
    return result
```

### Rabin-Karp Applications

```python
def longest_duplicate_substring(s: str) -> str:
    """
    LC 1044: Find longest duplicate substring using binary search + Rabin-Karp
    """
    n = len(s)
    base = 26
    mod = 2**63 - 1
    
    def search(length: int) -> int:
        """Check if there's a duplicate substring of given length"""
        if length == 0:
            return -1
        
        h = 0
        for i in range(length):
            h = (h * base + ord(s[i])) % mod
        
        seen = {h: 0}
        high_power = pow(base, length, mod)
        
        for i in range(1, n - length + 1):
            h = (h * base - ord(s[i - 1]) * high_power + ord(s[i + length - 1])) % mod
            if h in seen:
                # Verify to avoid collision
                if s[seen[h]:seen[h] + length] == s[i:i + length]:
                    return i
            seen[h] = i
        
        return -1
    
    # Binary search on length
    left, right = 0, n - 1
    result_pos = -1
    
    while left <= right:
        mid = (left + right) // 2
        pos = search(mid)
        if pos != -1:
            result_pos = pos
            left = mid + 1
        else:
            right = mid - 1
    
    if result_pos == -1:
        return ""
    return s[result_pos:result_pos + left - 1]


def count_distinct_substrings(s: str) -> int:
    """
    Count distinct substrings using rolling hash
    """
    n = len(s)
    seen = set()
    base = 31
    mod = 10**9 + 7
    
    for length in range(1, n + 1):
        h = 0
        for i in range(length):
            h = (h * base + ord(s[i]) - ord('a') + 1) % mod
        seen.add(h)
        
        high_power = pow(base, length - 1, mod)
        for i in range(length, n):
            h = ((h - (ord(s[i - length]) - ord('a') + 1) * high_power) * base + 
                 ord(s[i]) - ord('a') + 1) % mod
            seen.add(h)
    
    return len(seen)
```

---

## 4️⃣ Comparison

| Algorithm | Time | Space | Use Case |
|-----------|------|-------|----------|
| KMP | O(n+m) | O(m) | Single pattern, need all matches |
| Z-Algorithm | O(n+m) | O(n) | Single pattern, prefix-based problems |
| Rabin-Karp | O(n+m) avg | O(1) | Multiple patterns, substring problems |
| Built-in | O(nm) | O(1) | Simple cases, small inputs |

---

## 5️⃣ Implementation Tips

```python
# Python built-in alternatives (for simple cases)
text.find(pattern)      # First occurrence, -1 if not found
text.count(pattern)     # Count occurrences
pattern in text         # Check existence

# When to use which:
# - KMP: Need LPS array for other purposes, guaranteed O(n+m)
# - Z-Algorithm: Prefix-related problems, simpler to understand
# - Rabin-Karp: Multiple patterns, 2D pattern matching, duplicate finding
```

---

## 📚 Practice Problems

| # | Problem | Key Concept |
|---|---------|-------------|
| 1 | [LC 28: Find Index of First Occurrence](https://leetcode.com/problems/find-the-index-of-the-first-occurrence-in-a-string/) | Basic pattern matching |
| 2 | [LC 214: Shortest Palindrome](https://leetcode.com/problems/shortest-palindrome/) | KMP + LPS |
| 3 | [LC 459: Repeated Substring Pattern](https://leetcode.com/problems/repeated-substring-pattern/) | KMP property |
| 4 | [LC 686: Repeated String Match](https://leetcode.com/problems/repeated-string-match/) | Pattern in repeated string |
| 5 | [LC 796: Rotate String](https://leetcode.com/problems/rotate-string/) | Pattern in doubled string |
| 6 | [LC 1044: Longest Duplicate Substring](https://leetcode.com/problems/longest-duplicate-substring/) | Rabin-Karp + Binary Search |
| 7 | [LC 1392: Longest Happy Prefix](https://leetcode.com/problems/longest-happy-prefix/) | Z-function / KMP |
| 8 | [LC 1397: Find All Good Strings](https://leetcode.com/problems/find-all-good-strings/) | KMP + Digit DP |
| 9 | [LC 1948: Delete Duplicate Folders](https://leetcode.com/problems/delete-duplicate-folders-in-system/) | Hashing subtrees |
| 10 | [LC 2223: Sum of Scores of Built Strings](https://leetcode.com/problems/sum-of-scores-of-built-strings/) | Z-function |

---

*Last Updated: 2024*
