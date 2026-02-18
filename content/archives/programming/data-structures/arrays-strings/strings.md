---
title: "Strings"
topic: "Data Structures"
difficulty: "Fundamental"
tags: ["strings", "character-arrays", "immutability", "string-algorithms"]
status: "complete"
weight: 3
---

# Strings

## Short Summary

A **string** is a sequence of characters, typically implemented as an immutable array of characters. String manipulation is fundamental to coding interviews, appearing in ~30% of problems. Key concepts include immutability (in Python/Java), character encoding, and efficient pattern matching. Understanding string operations and their complexities is crucial for optimizing solutions.

---

## Why It Matters in Interviews

- **High Frequency:** Strings appear in nearly a third of interview problems
- **Hidden Complexity:** Immutability means concatenation can be O(n), not O(1)
- **Pattern Matching:** Foundation for advanced algorithms (KMP, Rabin-Karp, tries)

---

## Formal Definition / Properties / Invariants

### String Properties

| Property | Python | Java | C++ |
|----------|--------|------|-----|
| **Immutability** | Immutable | Immutable | Mutable (`std::string`) |
| **Encoding** | Unicode (UTF-8) | UTF-16 | Implementation-dependent |
| **Null-terminated** | No | No | Yes (C-strings) |
| **Random Access** | O(1) | O(1) | O(1) |

### Key Invariants
```
0 ≤ index < len(s)           # Valid index range
s[i] is immutable             # Cannot modify in place (Python/Java)
s1 + s2 creates new string    # Concatenation allocates new memory
```

### Character Encoding
- **ASCII:** 7-bit, 128 characters (0-127)
- **Extended ASCII:** 8-bit, 256 characters
- **Unicode:** Variable-width (UTF-8: 1-4 bytes, UTF-16: 2-4 bytes)

---

## Time & Space Complexity

| Operation | Time | Notes |
|-----------|------|-------|
| Access `s[i]` | O(1) | Direct index |
| Length `len(s)` | O(1) | Stored as metadata |
| Concatenation `s1 + s2` | O(n + m) | Creates new string |
| Substring `s[i:j]` | O(j - i) | Creates new string |
| Search `s.find(t)` | O(n × m) | Naive; O(n + m) with KMP |
| Compare `s1 == s2` | O(min(n, m)) | Character by character |
| `in` check `c in s` | O(n) | Linear scan |
| Split `s.split()` | O(n) | Creates list of strings |
| Join `''.join(list)` | O(total length) | Efficient concatenation |

**Space Complexity:** O(n) where n = number of characters

### StringBuilder Pattern (Critical for Interviews!)

```python
# BAD: O(n²) due to immutability
s = ""
for char in chars:  # n iterations
    s += char       # Each creates new string O(current length)
# Total: O(1 + 2 + 3 + ... + n) = O(n²)

# GOOD: O(n) using list
result = []
for char in chars:
    result.append(char)  # O(1) amortized
s = ''.join(result)      # O(n)
# Total: O(n)
```

---

## Typical Operations & Pseudo-Implementation

### Python String Operations with Complexity

```python
from typing import List, Optional
import string

def string_operations_demo():
    """
    Comprehensive string operations in Python with complexity analysis.
    
    >>> string_operations_demo()
    === Basic Operations ===
    Length: 13
    Access s[0]: H
    Slice s[0:5]: Hello
    Reverse: !dlroW ,olleH
    === Search Operations ===
    Find 'World': 7
    Count 'l': 3
    StartsWith 'Hello': True
    EndsWith '!': True
    === Case Operations ===
    Upper: HELLO, WORLD!
    Lower: hello, world!
    Title: Hello, World!
    Swapcase: hELLO, wORLD!
    === Transform Operations ===
    Strip: Hello, World!
    Replace: Hello, Python!
    Split: ['Hello,', 'World!']
    Join: H-e-l-l-o
    === Check Operations ===
    IsAlpha: False
    IsDigit: False
    IsAlnum: False
    """
    s = "Hello, World!"
    
    print("=== Basic Operations ===")
    print(f"Length: {len(s)}")                # O(1)
    print(f"Access s[0]: {s[0]}")             # O(1)
    print(f"Slice s[0:5]: {s[0:5]}")          # O(k) where k=slice size
    print(f"Reverse: {s[::-1]}")              # O(n)
    
    print("=== Search Operations ===")
    print(f"Find 'World': {s.find('World')}")           # O(n*m)
    print(f"Count 'l': {s.count('l')}")                 # O(n)
    print(f"StartsWith 'Hello': {s.startswith('Hello')}")  # O(k)
    print(f"EndsWith '!': {s.endswith('!')}")           # O(k)
    
    print("=== Case Operations ===")
    print(f"Upper: {s.upper()}")              # O(n)
    print(f"Lower: {s.lower()}")              # O(n)
    print(f"Title: {s.title()}")              # O(n)
    print(f"Swapcase: {s.swapcase()}")        # O(n)
    
    print("=== Transform Operations ===")
    print(f"Strip: {'  Hello, World!  '.strip()}")     # O(n)
    print(f"Replace: {s.replace('World', 'Python')}")  # O(n)
    print(f"Split: {s.split()}")                        # O(n)
    print(f"Join: {'-'.join('Hello')}")                 # O(n)
    
    print("=== Check Operations ===")
    print(f"IsAlpha: {s.isalpha()}")          # O(n)
    print(f"IsDigit: {s.isdigit()}")          # O(n)
    print(f"IsAlnum: {s.isalnum()}")          # O(n)


if __name__ == "__main__":
    import doctest
    doctest.testmod()
```

### Efficient String Building

```python
class StringBuilder:
    """
    Efficient string builder to avoid O(n²) concatenation.
    
    >>> sb = StringBuilder()
    >>> sb.append("Hello")
    >>> sb.append(" ")
    >>> sb.append("World")
    >>> str(sb)
    'Hello World'
    >>> len(sb)
    11
    """
    
    def __init__(self):
        self._parts: List[str] = []
        self._length: int = 0
    
    def append(self, s: str) -> 'StringBuilder':
        """Append string. O(1) amortized."""
        self._parts.append(s)
        self._length += len(s)
        return self  # Allow chaining
    
    def __str__(self) -> str:
        """Build final string. O(n)."""
        return ''.join(self._parts)
    
    def __len__(self) -> int:
        """Return total length. O(1)."""
        return self._length
    
    def clear(self) -> None:
        """Clear builder. O(1)."""
        self._parts = []
        self._length = 0


if __name__ == "__main__":
    import doctest
    doctest.testmod()
```

---

## Key Insights for Correctness

### String Hashing (Rolling Hash)

```python
def polynomial_hash(s: str, base: int = 31, mod: int = 10**9 + 9) -> int:
    """
    Compute polynomial rolling hash for string.
    
    hash(s) = s[0] + s[1]*base + s[2]*base² + ... + s[n-1]*base^(n-1)
    
    >>> polynomial_hash("abc")
    101851
    >>> polynomial_hash("abc") == polynomial_hash("abc")
    True
    """
    h = 0
    power = 1
    for char in s:
        h = (h + (ord(char) - ord('a') + 1) * power) % mod
        power = (power * base) % mod
    return h
```

### Character Frequency Pattern

```python
from collections import Counter

def char_frequency_patterns():
    """
    Common character frequency operations.
    
    >>> s = "aabbbcc"
    >>> Counter(s)
    Counter({'b': 3, 'a': 2, 'c': 2})
    >>> # Using array for lowercase only (faster)
    >>> freq = [0] * 26
    >>> for c in s: freq[ord(c) - ord('a')] += 1
    >>> freq[:7]  # a-g
    [2, 3, 2, 0, 0, 0, 0]
    """
    pass
```

---

## Common Implementations

### Two-Pointer Techniques for Strings

```python
def is_palindrome(s: str) -> bool:
    """
    Check if string is palindrome using two pointers.
    
    Time: O(n), Space: O(1)
    
    >>> is_palindrome("racecar")
    True
    >>> is_palindrome("hello")
    False
    >>> is_palindrome("A man a plan a canal Panama".replace(" ", "").lower())
    True
    """
    left, right = 0, len(s) - 1
    while left < right:
        if s[left] != s[right]:
            return False
        left += 1
        right -= 1
    return True


def reverse_string_in_place(s: List[str]) -> None:
    """
    Reverse string in place (given as char list).
    
    Time: O(n), Space: O(1)
    
    >>> chars = list("hello")
    >>> reverse_string_in_place(chars)
    >>> ''.join(chars)
    'olleh'
    """
    left, right = 0, len(s) - 1
    while left < right:
        s[left], s[right] = s[right], s[left]
        left += 1
        right -= 1


def reverse_words(s: str) -> str:
    """
    Reverse words in a string.
    
    Time: O(n), Space: O(n)
    
    >>> reverse_words("the sky is blue")
    'blue is sky the'
    >>> reverse_words("  hello world  ")
    'world hello'
    """
    return ' '.join(s.split()[::-1])


if __name__ == "__main__":
    import doctest
    doctest.testmod()
```

### Anagram and Permutation Checking

```python
from collections import Counter
from typing import List

def is_anagram(s: str, t: str) -> bool:
    """
    Check if two strings are anagrams.
    
    Time: O(n), Space: O(k) where k = alphabet size
    
    >>> is_anagram("anagram", "nagaram")
    True
    >>> is_anagram("rat", "car")
    False
    """
    return Counter(s) == Counter(t)


def is_anagram_array(s: str, t: str) -> bool:
    """
    Anagram check using fixed-size array (faster for ASCII).
    
    >>> is_anagram_array("anagram", "nagaram")
    True
    >>> is_anagram_array("rat", "car")
    False
    """
    if len(s) != len(t):
        return False
    
    count = [0] * 26
    for c1, c2 in zip(s, t):
        count[ord(c1) - ord('a')] += 1
        count[ord(c2) - ord('a')] -= 1
    
    return all(c == 0 for c in count)


def group_anagrams(strs: List[str]) -> List[List[str]]:
    """
    Group strings that are anagrams of each other.
    
    Time: O(n * k log k) where n = number of strings, k = max length
    Space: O(n * k)
    
    >>> result = group_anagrams(["eat", "tea", "tan", "ate", "nat", "bat"])
    >>> sorted([sorted(g) for g in result])
    [['ate', 'eat', 'tea'], ['bat'], ['nat', 'tan']]
    """
    from collections import defaultdict
    
    groups = defaultdict(list)
    for s in strs:
        # Use sorted string as key
        key = ''.join(sorted(s))
        groups[key].append(s)
    
    return list(groups.values())


def group_anagrams_optimal(strs: List[str]) -> List[List[str]]:
    """
    Group anagrams using character count as key (avoids sorting).
    
    Time: O(n * k) where n = number of strings, k = max length
    Space: O(n * k)
    
    >>> result = group_anagrams_optimal(["eat", "tea", "tan", "ate", "nat", "bat"])
    >>> sorted([sorted(g) for g in result])
    [['ate', 'eat', 'tea'], ['bat'], ['nat', 'tan']]
    """
    from collections import defaultdict
    
    groups = defaultdict(list)
    for s in strs:
        # Use character count tuple as key
        count = [0] * 26
        for c in s:
            count[ord(c) - ord('a')] += 1
        groups[tuple(count)].append(s)
    
    return list(groups.values())


if __name__ == "__main__":
    import doctest
    doctest.testmod()
```

---

## Step-by-Step Worked Example

### Problem: Longest Substring Without Repeating Characters

**Input:** `"abcabcbb"`
**Output:** `3` (The answer is "abc")

```
Using Sliding Window with Hash Set:

Step-by-step visualization:
s = "abcabcbb"
     ↑
     left

State: window = {}, left = 0, max_len = 0

i=0, char='a': 'a' not in window
     window = {'a'}, max_len = max(0, 0-0+1) = 1
     
i=1, char='b': 'b' not in window  
     window = {'a','b'}, max_len = max(1, 1-0+1) = 2
     
i=2, char='c': 'c' not in window
     window = {'a','b','c'}, max_len = max(2, 2-0+1) = 3
     
i=3, char='a': 'a' in window!
     Remove from left until 'a' removed
     Remove 'a', left = 1
     window = {'b','c','a'}, max_len = max(3, 3-1+1) = 3
     
i=4, char='b': 'b' in window!
     Remove 'b', left = 2  
     window = {'c','a','b'}, max_len = max(3, 4-2+1) = 3
     
i=5, char='c': 'c' in window!
     Remove 'c', left = 3
     window = {'a','b','c'}, max_len = max(3, 5-3+1) = 3
     
i=6, char='b': 'b' in window!
     Remove 'a', left = 4
     Remove 'b', left = 5
     window = {'c','b'}, max_len = max(3, 6-5+1) = 3
     
i=7, char='b': 'b' in window!
     Remove 'c', left = 6
     Remove 'b', left = 7
     window = {'b'}, max_len = max(3, 7-7+1) = 3

Final Answer: 3
```

---

## Variants & Extensions

### Unicode and Internationalization

```python
def unicode_aware_length(s: str) -> int:
    """
    Handle Unicode strings properly.
    
    >>> unicode_aware_length("Hello")
    5
    >>> unicode_aware_length("你好")  # Chinese
    2
    >>> unicode_aware_length("👋🌍")  # Emojis
    2
    """
    return len(s)  # Python 3 handles Unicode natively


def normalize_unicode(s: str) -> str:
    """
    Normalize Unicode string for comparison.
    
    >>> import unicodedata
    >>> s1 = "café"  # Single character é
    >>> s2 = "cafe\u0301"  # e + combining accent
    >>> s1 == s2
    False
    >>> unicodedata.normalize('NFC', s1) == unicodedata.normalize('NFC', s2)
    True
    """
    import unicodedata
    return unicodedata.normalize('NFC', s)
```

### String Compression

```python
def compress(chars: List[str]) -> int:
    """
    Compress string in place: ["a","a","b","b","c","c","c"] -> ["a","2","b","2","c","3"]
    
    Time: O(n), Space: O(1)
    
    >>> chars = list("aabbccc")
    >>> length = compress(chars)
    >>> chars[:length]
    ['a', '2', 'b', '2', 'c', '3']
    >>> length
    6
    """
    if not chars:
        return 0
    
    write = 0  # Position to write
    read = 0   # Position to read
    
    while read < len(chars):
        char = chars[read]
        count = 0
        
        # Count consecutive characters
        while read < len(chars) and chars[read] == char:
            read += 1
            count += 1
        
        # Write character
        chars[write] = char
        write += 1
        
        # Write count if > 1
        if count > 1:
            for digit in str(count):
                chars[write] = digit
                write += 1
    
    return write


if __name__ == "__main__":
    import doctest
    doctest.testmod()
```

---

## Common Interview Problems

| # | Problem | Difficulty | Approach | Time | LeetCode |
|---|---------|------------|----------|------|----------|
| 1 | Valid Anagram | Easy | Hash/Sort | O(n) | [#242](https://leetcode.com/problems/valid-anagram/) |
| 2 | Valid Palindrome | Easy | Two Pointers | O(n) | [#125](https://leetcode.com/problems/valid-palindrome/) |
| 3 | Reverse String | Easy | Two Pointers | O(n) | [#344](https://leetcode.com/problems/reverse-string/) |
| 4 | Longest Substring Without Repeating | Medium | Sliding Window | O(n) | [#3](https://leetcode.com/problems/longest-substring-without-repeating-characters/) |
| 5 | String to Integer (atoi) | Medium | State Machine | O(n) | [#8](https://leetcode.com/problems/string-to-integer-atoi/) |
| 6 | Longest Palindromic Substring | Medium | Expand Around Center/DP | O(n²) | [#5](https://leetcode.com/problems/longest-palindromic-substring/) |
| 7 | Group Anagrams | Medium | Hash Map | O(n·k) | [#49](https://leetcode.com/problems/group-anagrams/) |
| 8 | Minimum Window Substring | Hard | Sliding Window | O(n) | [#76](https://leetcode.com/problems/minimum-window-substring/) |
| 9 | Edit Distance | Hard | DP | O(n·m) | [#72](https://leetcode.com/problems/edit-distance/) |
| 10 | Regular Expression Matching | Hard | DP | O(n·m) | [#10](https://leetcode.com/problems/regular-expression-matching/) |

---

## Fully Worked Sample Problem

### Problem: Longest Palindromic Substring

**Problem Statement:** Given a string `s`, return the longest palindromic substring in `s`.

**Difficulty:** Medium | [LeetCode #5](https://leetcode.com/problems/longest-palindromic-substring/)

```python
def longest_palindrome(s: str) -> str:
    """
    Find longest palindromic substring using expand around center.
    
    Approach: Each position (and gap) can be center of a palindrome.
    Expand outward while characters match.
    
    Time Complexity: O(n²) - n centers, O(n) expansion each
    Space Complexity: O(1) - only tracking indices
    
    >>> longest_palindrome("babad")
    'bab'
    >>> longest_palindrome("cbbd")
    'bb'
    >>> longest_palindrome("a")
    'a'
    >>> longest_palindrome("ac")
    'a'
    """
    if not s:
        return ""
    
    def expand_around_center(left: int, right: int) -> str:
        """Expand while palindrome condition holds."""
        while left >= 0 and right < len(s) and s[left] == s[right]:
            left -= 1
            right += 1
        # Return palindrome (left+1 to right-1 inclusive)
        return s[left + 1:right]
    
    longest = ""
    
    for i in range(len(s)):
        # Odd length palindrome (single center)
        odd = expand_around_center(i, i)
        if len(odd) > len(longest):
            longest = odd
        
        # Even length palindrome (gap center)
        even = expand_around_center(i, i + 1)
        if len(even) > len(longest):
            longest = even
    
    return longest


def longest_palindrome_dp(s: str) -> str:
    """
    Alternative: Dynamic Programming approach.
    
    dp[i][j] = True if s[i:j+1] is palindrome
    
    Time: O(n²), Space: O(n²)
    
    >>> longest_palindrome_dp("babad")
    'bab'
    >>> longest_palindrome_dp("cbbd")
    'bb'
    """
    n = len(s)
    if n < 2:
        return s
    
    # dp[i][j] indicates whether s[i:j+1] is palindrome
    dp = [[False] * n for _ in range(n)]
    
    start = 0
    max_len = 1
    
    # All substrings of length 1 are palindromes
    for i in range(n):
        dp[i][i] = True
    
    # Check length 2 substrings
    for i in range(n - 1):
        if s[i] == s[i + 1]:
            dp[i][i + 1] = True
            start = i
            max_len = 2
    
    # Check lengths 3 to n
    for length in range(3, n + 1):
        for i in range(n - length + 1):
            j = i + length - 1
            
            # s[i:j+1] is palindrome if s[i] == s[j] and s[i+1:j] is palindrome
            if s[i] == s[j] and dp[i + 1][j - 1]:
                dp[i][j] = True
                if length > max_len:
                    start = i
                    max_len = length
    
    return s[start:start + max_len]


def longest_palindrome_manacher(s: str) -> str:
    """
    Manacher's Algorithm - O(n) time complexity.
    
    >>> longest_palindrome_manacher("babad")
    'bab'
    >>> longest_palindrome_manacher("cbbd")
    'bb'
    """
    # Transform string: "abc" -> "#a#b#c#"
    t = '#' + '#'.join(s) + '#'
    n = len(t)
    
    # p[i] = radius of palindrome centered at i
    p = [0] * n
    center = right = 0
    
    for i in range(n):
        # Mirror of i with respect to center
        if i < right:
            mirror = 2 * center - i
            p[i] = min(right - i, p[mirror])
        
        # Expand around i
        while (i + p[i] + 1 < n and i - p[i] - 1 >= 0 and 
               t[i + p[i] + 1] == t[i - p[i] - 1]):
            p[i] += 1
        
        # Update center and right boundary
        if i + p[i] > right:
            center = i
            right = i + p[i]
    
    # Find maximum palindrome
    max_len, center_idx = max((p[i], i) for i in range(n))
    start = (center_idx - max_len) // 2
    
    return s[start:start + max_len]


# Unit tests
def test_longest_palindrome():
    test_cases = [
        ("babad", ["bab", "aba"]),  # Multiple valid answers
        ("cbbd", ["bb"]),
        ("a", ["a"]),
        ("", [""]),
        ("abcba", ["abcba"]),
        ("abaaba", ["abaaba"]),
    ]
    
    for s, expected in test_cases:
        result = longest_palindrome(s)
        assert result in expected, f"Failed for {s}: got {result}"
    print("All tests passed!")


if __name__ == "__main__":
    import doctest
    doctest.testmod()
    test_longest_palindrome()
```

**Pattern(s) Used:** Expand Around Center, Dynamic Programming

---

## Edge Cases & Pitfalls

- **Empty String:** Return empty or handle specially
- **Single Character:** Always a palindrome of length 1
- **All Same Characters:** Entire string is palindrome
- **Immutability Trap:** Don't use `+=` in loops for building strings
- **Unicode:** Consider normalization for comparison
- **Case Sensitivity:** Clarify if comparison is case-insensitive
- **Whitespace:** Clarify handling (strip, preserve, ignore?)
- **Off-by-One:** String slicing `s[i:j]` excludes index `j`

---

## Follow-ups and Optimization Ideas

1. **Count All Palindromic Substrings:** Modify expand method to count instead
2. **Longest Palindrome by Deleting/Adding:** Edit distance variant
3. **Palindrome Partitioning:** DP with backtracking
4. **Streaming Palindrome Check:** Rolling hash approach
5. **Parallel Processing:** Divide and conquer for very long strings

---

## Practice Checklist

| # | Problem | Difficulty | Link | Pattern |
|---|---------|------------|------|---------|
| 1 | Valid Palindrome | Easy | [LC #125](https://leetcode.com/problems/valid-palindrome/) | Two Pointers |
| 2 | Valid Anagram | Easy | [LC #242](https://leetcode.com/problems/valid-anagram/) | Hash Count |
| 3 | Reverse String | Easy | [LC #344](https://leetcode.com/problems/reverse-string/) | Two Pointers |
| 4 | First Unique Character | Easy | [LC #387](https://leetcode.com/problems/first-unique-character-in-a-string/) | Hash Map |
| 5 | Implement strStr() | Easy | [LC #28](https://leetcode.com/problems/find-the-index-of-the-first-occurrence-in-a-string/) | KMP/Rabin-Karp |
| 6 | Longest Substring Without Repeating | Medium | [LC #3](https://leetcode.com/problems/longest-substring-without-repeating-characters/) | Sliding Window |
| 7 | Longest Palindromic Substring | Medium | [LC #5](https://leetcode.com/problems/longest-palindromic-substring/) | Expand/DP |
| 8 | Group Anagrams | Medium | [LC #49](https://leetcode.com/problems/group-anagrams/) | Hash Map |
| 9 | Palindromic Substrings | Medium | [LC #647](https://leetcode.com/problems/palindromic-substrings/) | Expand Center |
| 10 | Decode Ways | Medium | [LC #91](https://leetcode.com/problems/decode-ways/) | DP |
| 11 | Word Break | Medium | [LC #139](https://leetcode.com/problems/word-break/) | DP |
| 12 | Encode and Decode Strings | Medium | [LC #271](https://leetcode.com/problems/encode-and-decode-strings/) | Design |
| 13 | Longest Repeating Character Replacement | Medium | [LC #424](https://leetcode.com/problems/longest-repeating-character-replacement/) | Sliding Window |
| 14 | Minimum Window Substring | Hard | [LC #76](https://leetcode.com/problems/minimum-window-substring/) | Sliding Window |
| 15 | Edit Distance | Hard | [LC #72](https://leetcode.com/problems/edit-distance/) | DP |
| 16 | Regular Expression Matching | Hard | [LC #10](https://leetcode.com/problems/regular-expression-matching/) | DP |
| 17 | Wildcard Matching | Hard | [LC #44](https://leetcode.com/problems/wildcard-matching/) | DP |
| 18 | Palindrome Pairs | Hard | [LC #336](https://leetcode.com/problems/palindrome-pairs/) | Trie/Hash |
| 19 | Word Search II | Hard | [LC #212](https://leetcode.com/problems/word-search-ii/) | Trie + Backtrack |
| 20 | Shortest Palindrome | Hard | [LC #214](https://leetcode.com/problems/shortest-palindrome/) | KMP |

---

## Further Reading & References

- [Python String Methods Documentation](https://docs.python.org/3/library/stdtypes.html#string-methods)
- [GeeksforGeeks - String Data Structure](https://www.geeksforgeeks.org/string-data-structure/)
- [LeetCode String Problems](https://leetcode.com/tag/string/)
- [cp-algorithms - String Processing](https://cp-algorithms.com/string/)
- [Unicode HOWTO - Python](https://docs.python.org/3/howto/unicode.html)

---

## Flashcard Summary

**Q: Why is string concatenation in a loop O(n²)?**
A: Strings are immutable; each `+=` creates a new string, copying all previous characters.

**Q: How to efficiently build a string from parts?**
A: Use a list to collect parts, then `''.join(list)` - O(n) total.

**Q: What's the time complexity of `s.find(t)`?**
A: O(n × m) naive; O(n + m) with KMP or Rabin-Karp.

**Q: How does expand around center work for palindromes?**
A: For each position, expand outward while `s[left] == s[right]`; check both odd and even length centers.

**Q: When to use character array vs Counter for frequency?**
A: Array `[0]*26` for lowercase ASCII (faster, O(1) lookup); Counter for Unicode or unknown alphabet.

**Q: What's the key insight for "Longest Substring Without Repeating"?**
A: Sliding window with set; when duplicate found, shrink window from left until valid.
