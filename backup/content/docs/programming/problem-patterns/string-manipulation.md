---
title: "String Manipulation Patterns"
topic: "Problem Patterns"
difficulty: "Medium"
tags: ["string", "substring", "palindrome", "anagram", "pattern-matching"]
status: "complete"
weight: 18
---

# String Manipulation Patterns

## 📚 Overview

String problems appear frequently in interviews. Master these patterns for substring, palindrome, and pattern matching problems.

---

## 1️⃣ Two Pointer String Patterns

### Valid Palindrome (LC 125)

```python
def is_palindrome(s: str) -> bool:
    """Check if string is palindrome (alphanumeric only)"""
    left, right = 0, len(s) - 1
    
    while left < right:
        while left < right and not s[left].isalnum():
            left += 1
        while left < right and not s[right].isalnum():
            right -= 1
        
        if s[left].lower() != s[right].lower():
            return False
        
        left += 1
        right -= 1
    
    return True
```

### Valid Palindrome II (LC 680)

```python
def valid_palindrome(s: str) -> bool:
    """Can be palindrome by removing at most one character"""
    def is_palindrome_range(left: int, right: int) -> bool:
        while left < right:
            if s[left] != s[right]:
                return False
            left += 1
            right -= 1
        return True
    
    left, right = 0, len(s) - 1
    
    while left < right:
        if s[left] != s[right]:
            # Try removing either character
            return is_palindrome_range(left + 1, right) or \
                   is_palindrome_range(left, right - 1)
        left += 1
        right -= 1
    
    return True
```

---

## 2️⃣ Expand Around Center

### Longest Palindromic Substring (LC 5)

```python
def longest_palindrome(s: str) -> str:
    """Find longest palindromic substring"""
    if not s:
        return ""
    
    def expand(left: int, right: int) -> str:
        while left >= 0 and right < len(s) and s[left] == s[right]:
            left -= 1
            right += 1
        return s[left + 1:right]
    
    result = ""
    for i in range(len(s)):
        # Odd length palindrome
        odd = expand(i, i)
        if len(odd) > len(result):
            result = odd
        
        # Even length palindrome
        even = expand(i, i + 1)
        if len(even) > len(result):
            result = even
    
    return result
```

### Palindromic Substrings Count (LC 647)

```python
def count_substrings(s: str) -> int:
    """Count all palindromic substrings"""
    count = 0
    
    def expand(left: int, right: int) -> int:
        cnt = 0
        while left >= 0 and right < len(s) and s[left] == s[right]:
            cnt += 1
            left -= 1
            right += 1
        return cnt
    
    for i in range(len(s)):
        count += expand(i, i)      # Odd
        count += expand(i, i + 1)  # Even
    
    return count
```

---

## 3️⃣ Sliding Window for Strings

### Longest Substring Without Repeating Characters (LC 3)

```python
def length_of_longest_substring(s: str) -> int:
    """Longest substring with unique characters"""
    char_index = {}
    max_len = 0
    left = 0
    
    for right, char in enumerate(s):
        if char in char_index and char_index[char] >= left:
            left = char_index[char] + 1
        
        char_index[char] = right
        max_len = max(max_len, right - left + 1)
    
    return max_len
```

### Minimum Window Substring (LC 76)

```python
def min_window(s: str, t: str) -> str:
    """Minimum window containing all characters of t"""
    from collections import Counter
    
    if not t or not s:
        return ""
    
    need = Counter(t)
    have = {}
    formed = 0
    required = len(need)
    
    result = ""
    min_len = float('inf')
    left = 0
    
    for right, char in enumerate(s):
        have[char] = have.get(char, 0) + 1
        
        if char in need and have[char] == need[char]:
            formed += 1
        
        while formed == required:
            if right - left + 1 < min_len:
                min_len = right - left + 1
                result = s[left:right + 1]
            
            left_char = s[left]
            have[left_char] -= 1
            if left_char in need and have[left_char] < need[left_char]:
                formed -= 1
            left += 1
    
    return result
```

### Find All Anagrams (LC 438)

```python
def find_anagrams(s: str, p: str) -> list[int]:
    """Find all start indices of anagrams of p in s"""
    from collections import Counter
    
    if len(p) > len(s):
        return []
    
    p_count = Counter(p)
    s_count = Counter(s[:len(p)])
    
    result = []
    if s_count == p_count:
        result.append(0)
    
    for i in range(len(p), len(s)):
        # Add new char
        s_count[s[i]] += 1
        
        # Remove old char
        old = s[i - len(p)]
        s_count[old] -= 1
        if s_count[old] == 0:
            del s_count[old]
        
        if s_count == p_count:
            result.append(i - len(p) + 1)
    
    return result
```

---

## 4️⃣ String Matching

### KMP Algorithm

```python
def kmp_search(text: str, pattern: str) -> int:
    """KMP pattern matching - O(n + m)"""
    if not pattern:
        return 0
    
    # Build failure function
    def build_lps(p: str) -> list[int]:
        lps = [0] * len(p)
        length = 0
        i = 1
        
        while i < len(p):
            if p[i] == p[length]:
                length += 1
                lps[i] = length
                i += 1
            elif length > 0:
                length = lps[length - 1]
            else:
                lps[i] = 0
                i += 1
        
        return lps
    
    lps = build_lps(pattern)
    i = j = 0
    
    while i < len(text):
        if text[i] == pattern[j]:
            i += 1
            j += 1
            if j == len(pattern):
                return i - j
        elif j > 0:
            j = lps[j - 1]
        else:
            i += 1
    
    return -1
```

### Rabin-Karp (Rolling Hash)

```python
def rabin_karp(text: str, pattern: str) -> int:
    """Rabin-Karp using rolling hash"""
    if len(pattern) > len(text):
        return -1
    
    BASE = 26
    MOD = 10**9 + 7
    
    # Compute hash for pattern and first window
    pattern_hash = 0
    window_hash = 0
    power = 1
    
    for i in range(len(pattern)):
        pattern_hash = (pattern_hash * BASE + ord(pattern[i])) % MOD
        window_hash = (window_hash * BASE + ord(text[i])) % MOD
        if i < len(pattern) - 1:
            power = (power * BASE) % MOD
    
    # Slide window
    for i in range(len(text) - len(pattern) + 1):
        if pattern_hash == window_hash:
            if text[i:i + len(pattern)] == pattern:
                return i
        
        if i < len(text) - len(pattern):
            # Update hash
            window_hash = (window_hash - ord(text[i]) * power) % MOD
            window_hash = (window_hash * BASE + ord(text[i + len(pattern)])) % MOD
    
    return -1
```

---

## 5️⃣ Palindrome Partitioning

### Palindrome Partitioning (LC 131)

```python
def partition(s: str) -> list[list[str]]:
    """All ways to partition into palindromes"""
    result = []
    
    def is_palindrome(start: int, end: int) -> bool:
        while start < end:
            if s[start] != s[end]:
                return False
            start += 1
            end -= 1
        return True
    
    def backtrack(start: int, path: list[str]):
        if start == len(s):
            result.append(path[:])
            return
        
        for end in range(start, len(s)):
            if is_palindrome(start, end):
                path.append(s[start:end + 1])
                backtrack(end + 1, path)
                path.pop()
    
    backtrack(0, [])
    return result
```

### Min Cuts for Palindrome Partitioning (LC 132)

```python
def min_cut(s: str) -> int:
    """Minimum cuts for palindrome partitioning"""
    n = len(s)
    
    # Precompute palindrome table
    is_palindrome = [[False] * n for _ in range(n)]
    for i in range(n):
        is_palindrome[i][i] = True
    for i in range(n - 1):
        is_palindrome[i][i + 1] = s[i] == s[i + 1]
    for length in range(3, n + 1):
        for i in range(n - length + 1):
            j = i + length - 1
            is_palindrome[i][j] = s[i] == s[j] and is_palindrome[i + 1][j - 1]
    
    # DP for min cuts
    dp = list(range(n))  # dp[i] = min cuts for s[0:i+1]
    
    for i in range(1, n):
        if is_palindrome[0][i]:
            dp[i] = 0
        else:
            for j in range(i):
                if is_palindrome[j + 1][i]:
                    dp[i] = min(dp[i], dp[j] + 1)
    
    return dp[n - 1]
```

---

## 6️⃣ String Building & Transformation

### Longest Common Prefix (LC 14)

```python
def longest_common_prefix(strs: list[str]) -> str:
    """Find longest common prefix"""
    if not strs:
        return ""
    
    prefix = strs[0]
    
    for s in strs[1:]:
        while not s.startswith(prefix):
            prefix = prefix[:-1]
            if not prefix:
                return ""
    
    return prefix
```

### Group Anagrams (LC 49)

```python
def group_anagrams(strs: list[str]) -> list[list[str]]:
    """Group strings that are anagrams"""
    from collections import defaultdict
    
    groups = defaultdict(list)
    
    for s in strs:
        key = tuple(sorted(s))
        groups[key].append(s)
    
    return list(groups.values())
```

### Encode and Decode Strings (LC 271)

```python
class Codec:
    def encode(self, strs: list[str]) -> str:
        """Encode list of strings to single string"""
        return ''.join(f'{len(s)}#{s}' for s in strs)
    
    def decode(self, s: str) -> list[str]:
        """Decode string back to list"""
        result = []
        i = 0
        
        while i < len(s):
            j = s.index('#', i)
            length = int(s[i:j])
            result.append(s[j + 1:j + 1 + length])
            i = j + 1 + length
        
        return result
```

---

## 7️⃣ String to Integer

### Atoi Implementation (LC 8)

```python
def my_atoi(s: str) -> int:
    """String to integer with overflow handling"""
    INT_MAX = 2**31 - 1
    INT_MIN = -2**31
    
    i = 0
    n = len(s)
    
    # Skip whitespace
    while i < n and s[i] == ' ':
        i += 1
    
    if i == n:
        return 0
    
    # Handle sign
    sign = 1
    if s[i] == '-':
        sign = -1
        i += 1
    elif s[i] == '+':
        i += 1
    
    # Parse digits
    result = 0
    while i < n and s[i].isdigit():
        digit = int(s[i])
        
        # Check overflow
        if result > (INT_MAX - digit) // 10:
            return INT_MAX if sign == 1 else INT_MIN
        
        result = result * 10 + digit
        i += 1
    
    return sign * result
```

---

## 8️⃣ Parentheses Problems

### Valid Parentheses (LC 20)

```python
def is_valid(s: str) -> bool:
    """Check balanced parentheses"""
    stack = []
    mapping = {')': '(', '}': '{', ']': '['}
    
    for char in s:
        if char in mapping:
            if not stack or stack.pop() != mapping[char]:
                return False
        else:
            stack.append(char)
    
    return len(stack) == 0
```

### Generate Parentheses (LC 22)

```python
def generate_parenthesis(n: int) -> list[str]:
    """Generate all valid parentheses combinations"""
    result = []
    
    def backtrack(path: list[str], open_count: int, close_count: int):
        if len(path) == 2 * n:
            result.append(''.join(path))
            return
        
        if open_count < n:
            path.append('(')
            backtrack(path, open_count + 1, close_count)
            path.pop()
        
        if close_count < open_count:
            path.append(')')
            backtrack(path, open_count, close_count + 1)
            path.pop()
    
    backtrack([], 0, 0)
    return result
```

### Longest Valid Parentheses (LC 32)

```python
def longest_valid_parentheses(s: str) -> int:
    """Find length of longest valid parentheses substring"""
    max_len = 0
    stack = [-1]  # Base index
    
    for i, char in enumerate(s):
        if char == '(':
            stack.append(i)
        else:
            stack.pop()
            if not stack:
                stack.append(i)  # New base
            else:
                max_len = max(max_len, i - stack[-1])
    
    return max_len
```

---

## 9️⃣ Word Problems

### Word Break (LC 139)

```python
def word_break(s: str, word_dict: list[str]) -> bool:
    """Can s be segmented into dictionary words"""
    word_set = set(word_dict)
    n = len(s)
    dp = [False] * (n + 1)
    dp[0] = True
    
    for i in range(1, n + 1):
        for j in range(i):
            if dp[j] and s[j:i] in word_set:
                dp[i] = True
                break
    
    return dp[n]
```

### Word Ladder (LC 127)

```python
def ladder_length(begin_word: str, end_word: str, word_list: list[str]) -> int:
    """Shortest transformation sequence length"""
    from collections import deque
    
    word_set = set(word_list)
    if end_word not in word_set:
        return 0
    
    queue = deque([(begin_word, 1)])
    visited = {begin_word}
    
    while queue:
        word, length = queue.popleft()
        
        if word == end_word:
            return length
        
        for i in range(len(word)):
            for c in 'abcdefghijklmnopqrstuvwxyz':
                next_word = word[:i] + c + word[i + 1:]
                if next_word in word_set and next_word not in visited:
                    visited.add(next_word)
                    queue.append((next_word, length + 1))
    
    return 0
```

---

## 🔟 Advanced String DP

### Edit Distance (LC 72)

```python
def min_distance(word1: str, word2: str) -> int:
    """Minimum operations to transform word1 to word2"""
    m, n = len(word1), len(word2)
    dp = [[0] * (n + 1) for _ in range(m + 1)]
    
    for i in range(m + 1):
        dp[i][0] = i
    for j in range(n + 1):
        dp[0][j] = j
    
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if word1[i - 1] == word2[j - 1]:
                dp[i][j] = dp[i - 1][j - 1]
            else:
                dp[i][j] = 1 + min(
                    dp[i - 1][j],      # Delete
                    dp[i][j - 1],      # Insert
                    dp[i - 1][j - 1]   # Replace
                )
    
    return dp[m][n]
```

### Distinct Subsequences (LC 115)

```python
def num_distinct(s: str, t: str) -> int:
    """Count distinct subsequences of s that equal t"""
    m, n = len(s), len(t)
    dp = [[0] * (n + 1) for _ in range(m + 1)]
    
    # Empty t can be formed from any prefix of s
    for i in range(m + 1):
        dp[i][0] = 1
    
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if s[i - 1] == t[j - 1]:
                dp[i][j] = dp[i - 1][j - 1] + dp[i - 1][j]
            else:
                dp[i][j] = dp[i - 1][j]
    
    return dp[m][n]
```

---

## 📝 Related Problems

| Problem | LC # | Pattern |
|---------|------|---------|
| Longest Palindromic Substring | 5 | Expand Around Center |
| Valid Palindrome II | 680 | Two Pointers |
| Minimum Window Substring | 76 | Sliding Window |
| Find All Anagrams | 438 | Sliding Window |
| Group Anagrams | 49 | Hash Map |
| Palindrome Partitioning | 131 | Backtracking |
| Word Break | 139 | DP |
| Word Ladder | 127 | BFS |
| Edit Distance | 72 | 2D DP |
| Longest Valid Parentheses | 32 | Stack |
| Generate Parentheses | 22 | Backtracking |
| Longest Common Prefix | 14 | Iteration |
| KMP Pattern Matching | - | Pattern Matching |
| Distinct Subsequences | 115 | 2D DP |
| Implement strStr | 28 | Pattern Matching |

---

*Last Updated: 2024*
