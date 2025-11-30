---
title: "Aho-Corasick and Manacher's Algorithm"
topic: "String Algorithms"
difficulty: "Hard"
tags: ["aho-corasick", "manacher", "palindrome", "multi-pattern", "trie"]
status: "complete"
weight: 2
---

# Aho-Corasick and Manacher's Algorithm

## 📚 Summary

Aho-Corasick enables efficient multi-pattern matching, while Manacher's algorithm finds all palindromic substrings in linear time.

---

## 1️⃣ Aho-Corasick Algorithm

### Trie with Failure Links

```python
from collections import deque, defaultdict

class AhoCorasick:
    """
    Aho-Corasick automaton for multi-pattern string matching
    
    Time: O(n + m + z) where:
    - n = total length of all patterns
    - m = length of text
    - z = number of matches
    
    Space: O(n * alphabet_size)
    """
    def __init__(self):
        self.goto = [{}]      # Goto transitions
        self.fail = [0]       # Failure links
        self.output = [[]]    # Output (pattern indices) at each state
        self.state_count = 1
    
    def add_pattern(self, pattern: str, pattern_idx: int) -> None:
        """Add a pattern to the automaton"""
        state = 0
        
        for char in pattern:
            if char not in self.goto[state]:
                self.goto[state][char] = self.state_count
                self.goto.append({})
                self.fail.append(0)
                self.output.append([])
                self.state_count += 1
            state = self.goto[state][char]
        
        self.output[state].append(pattern_idx)
    
    def build(self) -> None:
        """Build failure links using BFS"""
        queue = deque()
        
        # Initialize failure links for depth 1 states
        for char, state in self.goto[0].items():
            queue.append(state)
            self.fail[state] = 0
        
        # BFS to compute failure links
        while queue:
            current = queue.popleft()
            
            for char, next_state in self.goto[current].items():
                queue.append(next_state)
                
                # Find failure link
                fail_state = self.fail[current]
                while fail_state and char not in self.goto[fail_state]:
                    fail_state = self.fail[fail_state]
                
                self.fail[next_state] = self.goto[fail_state].get(char, 0)
                
                # Merge outputs
                self.output[next_state] = (
                    self.output[next_state] + 
                    self.output[self.fail[next_state]]
                )
    
    def search(self, text: str) -> list[tuple[int, int]]:
        """
        Search for all patterns in text
        Returns list of (position, pattern_idx) tuples
        """
        state = 0
        results = []
        
        for i, char in enumerate(text):
            # Follow failure links until we find a match or reach root
            while state and char not in self.goto[state]:
                state = self.fail[state]
            
            state = self.goto[state].get(char, 0)
            
            # Record all matches at this position
            for pattern_idx in self.output[state]:
                results.append((i, pattern_idx))
        
        return results


def aho_corasick_search(text: str, patterns: list[str]) -> dict[str, list[int]]:
    """
    Find all occurrences of all patterns in text
    Returns {pattern: [positions]}
    """
    ac = AhoCorasick()
    
    for idx, pattern in enumerate(patterns):
        ac.add_pattern(pattern, idx)
    
    ac.build()
    
    matches = ac.search(text)
    
    result = {pattern: [] for pattern in patterns}
    for pos, pattern_idx in matches:
        pattern = patterns[pattern_idx]
        # pos is end position, convert to start position
        result[pattern].append(pos - len(pattern) + 1)
    
    return result
```

### Optimized Aho-Corasick with Dictionary Link

```python
class AhoCorasickOptimized:
    """
    Optimized version with dictionary suffix links
    Allows skipping non-output states
    """
    def __init__(self):
        self.goto = [{}]
        self.fail = [0]
        self.dict_link = [0]  # Suffix link to nearest state with output
        self.output = [[]]
        self.state_count = 1
    
    def add_pattern(self, pattern: str, pattern_idx: int) -> None:
        state = 0
        for char in pattern:
            if char not in self.goto[state]:
                self.goto[state][char] = self.state_count
                self.goto.append({})
                self.fail.append(0)
                self.dict_link.append(0)
                self.output.append([])
                self.state_count += 1
            state = self.goto[state][char]
        self.output[state].append(pattern_idx)
    
    def build(self) -> None:
        queue = deque()
        
        for char, state in self.goto[0].items():
            queue.append(state)
        
        while queue:
            current = queue.popleft()
            
            for char, next_state in self.goto[current].items():
                queue.append(next_state)
                
                fail_state = self.fail[current]
                while fail_state and char not in self.goto[fail_state]:
                    fail_state = self.fail[fail_state]
                
                self.fail[next_state] = self.goto[fail_state].get(char, 0)
                
                # Dictionary link
                if self.output[self.fail[next_state]]:
                    self.dict_link[next_state] = self.fail[next_state]
                else:
                    self.dict_link[next_state] = self.dict_link[self.fail[next_state]]
                
                self.output[next_state] = (
                    self.output[next_state] + 
                    self.output[self.fail[next_state]]
                )
    
    def count_matches(self, text: str) -> int:
        """Count total pattern occurrences"""
        state = 0
        count = 0
        
        for char in text:
            while state and char not in self.goto[state]:
                state = self.fail[state]
            state = self.goto[state].get(char, 0)
            count += len(self.output[state])
        
        return count
```

### Applications

```python
def multi_pattern_count(text: str, patterns: list[str]) -> dict[str, int]:
    """Count occurrences of each pattern"""
    result = aho_corasick_search(text, patterns)
    return {pattern: len(positions) for pattern, positions in result.items()}


def filter_bad_words(text: str, bad_words: list[str]) -> str:
    """Replace bad words with asterisks"""
    ac = AhoCorasick()
    for idx, word in enumerate(bad_words):
        ac.add_pattern(word.lower(), idx)
    ac.build()
    
    matches = ac.search(text.lower())
    
    # Sort by position, handle overlaps
    intervals = []
    for pos, pattern_idx in matches:
        start = pos - len(bad_words[pattern_idx]) + 1
        end = pos
        intervals.append((start, end))
    
    # Merge overlapping intervals
    intervals.sort()
    merged = []
    for start, end in intervals:
        if merged and start <= merged[-1][1] + 1:
            merged[-1] = (merged[-1][0], max(merged[-1][1], end))
        else:
            merged.append((start, end))
    
    # Replace
    result = list(text)
    for start, end in merged:
        for i in range(start, end + 1):
            result[i] = '*'
    
    return ''.join(result)
```

---

## 2️⃣ Manacher's Algorithm

### Finding All Palindromic Substrings

```python
def manacher(s: str) -> list[int]:
    """
    Manacher's algorithm for finding all palindromic substrings
    
    Returns array p where p[i] is the radius of longest palindrome centered at i
    (in the transformed string)
    
    Time: O(n), Space: O(n)
    """
    # Transform: "abc" -> "^#a#b#c#$"
    t = '^#' + '#'.join(s) + '#$'
    n = len(t)
    p = [0] * n
    
    center = right = 0  # Center and right boundary of current palindrome
    
    for i in range(1, n - 1):
        if i < right:
            # Mirror position
            mirror = 2 * center - i
            p[i] = min(right - i, p[mirror])
        
        # Try to expand
        while t[i + p[i] + 1] == t[i - p[i] - 1]:
            p[i] += 1
        
        # Update center and right if we expanded past right
        if i + p[i] > right:
            center = i
            right = i + p[i]
    
    return p


def longest_palindromic_substring(s: str) -> str:
    """
    LC 5: Longest Palindromic Substring using Manacher
    """
    if not s:
        return ""
    
    p = manacher(s)
    
    # Find max in p array
    max_len = 0
    center_idx = 0
    for i, val in enumerate(p):
        if val > max_len:
            max_len = val
            center_idx = i
    
    # Convert back to original indices
    start = (center_idx - max_len) // 2
    return s[start:start + max_len]


def count_palindromic_substrings(s: str) -> int:
    """
    LC 647: Count all palindromic substrings
    """
    p = manacher(s)
    
    # Each p[i] contributes (p[i] + 1) // 2 palindromes
    # (accounting for the #'s in transformed string)
    count = 0
    for i in range(1, len(p) - 1):
        count += (p[i] + 1) // 2
    
    return count
```

### Detailed Implementation

```python
class Manacher:
    """
    Complete Manacher implementation with various utilities
    """
    def __init__(self, s: str):
        self.original = s
        self.transformed = '^#' + '#'.join(s) + '#$'
        self.p = self._compute_p()
    
    def _compute_p(self) -> list[int]:
        t = self.transformed
        n = len(t)
        p = [0] * n
        
        center = right = 0
        
        for i in range(1, n - 1):
            if i < right:
                mirror = 2 * center - i
                p[i] = min(right - i, p[mirror])
            
            while t[i + p[i] + 1] == t[i - p[i] - 1]:
                p[i] += 1
            
            if i + p[i] > right:
                center = i
                right = i + p[i]
        
        return p
    
    def longest_palindrome(self) -> tuple[int, int]:
        """Returns (start, length) of longest palindrome"""
        max_len = max(self.p)
        center_idx = self.p.index(max_len)
        start = (center_idx - max_len) // 2
        return (start, max_len)
    
    def is_palindrome(self, left: int, right: int) -> bool:
        """Check if s[left:right+1] is a palindrome in O(1)"""
        # Convert to transformed indices
        center = left + right + 2  # Center in transformed string
        radius = right - left + 1
        return self.p[center] >= radius
    
    def longest_palindrome_at(self, center: int, is_odd: bool) -> int:
        """
        Get length of longest palindrome centered at given position
        is_odd: True for odd-length palindrome (single center)
        """
        # Convert to transformed index
        trans_center = 2 * center + 2 if is_odd else 2 * center + 3
        return self.p[trans_center]
    
    def all_palindromes(self) -> list[tuple[int, int]]:
        """Get all palindromic substrings as (start, end) pairs"""
        palindromes = []
        s = self.original
        n = len(s)
        
        # Odd length
        for center in range(n):
            radius = self.longest_palindrome_at(center, True)
            for r in range(1, radius + 1):
                palindromes.append((center - r, center + r))
            palindromes.append((center, center))  # Single char
        
        # Even length
        for center in range(n - 1):
            radius = self.longest_palindrome_at(center, False)
            for r in range(1, radius + 1):
                palindromes.append((center - r + 1, center + r))
        
        return palindromes


def palindrome_partitioning_min_cuts(s: str) -> int:
    """
    LC 132: Minimum cuts for palindrome partitioning
    Use Manacher for O(1) palindrome checks
    """
    n = len(s)
    if n == 0:
        return 0
    
    manacher = Manacher(s)
    
    # dp[i] = min cuts for s[0:i+1]
    dp = list(range(n))  # Worst case: cut everywhere
    
    for i in range(n):
        if manacher.is_palindrome(0, i):
            dp[i] = 0
        else:
            for j in range(i):
                if manacher.is_palindrome(j + 1, i):
                    dp[i] = min(dp[i], dp[j] + 1)
    
    return dp[n - 1]
```

### Alternative Palindrome Methods

```python
def expand_around_center(s: str) -> str:
    """
    Simple O(n²) approach for comparison
    """
    def expand(left: int, right: int) -> int:
        while left >= 0 and right < len(s) and s[left] == s[right]:
            left -= 1
            right += 1
        return right - left - 1
    
    if not s:
        return ""
    
    start = end = 0
    for i in range(len(s)):
        len1 = expand(i, i)      # Odd length
        len2 = expand(i, i + 1)  # Even length
        max_len = max(len1, len2)
        
        if max_len > end - start:
            start = i - (max_len - 1) // 2
            end = i + max_len // 2
    
    return s[start:end + 1]


def longest_palindrome_dp(s: str) -> str:
    """
    DP approach O(n²) time and space
    """
    n = len(s)
    if n < 2:
        return s
    
    # dp[i][j] = True if s[i:j+1] is palindrome
    dp = [[False] * n for _ in range(n)]
    
    start = 0
    max_len = 1
    
    # All single chars are palindromes
    for i in range(n):
        dp[i][i] = True
    
    # Check length 2
    for i in range(n - 1):
        if s[i] == s[i + 1]:
            dp[i][i + 1] = True
            start = i
            max_len = 2
    
    # Check lengths >= 3
    for length in range(3, n + 1):
        for i in range(n - length + 1):
            j = i + length - 1
            if s[i] == s[j] and dp[i + 1][j - 1]:
                dp[i][j] = True
                if length > max_len:
                    start = i
                    max_len = length
    
    return s[start:start + max_len]
```

---

## ⏱️ Complexity Analysis

| Algorithm | Time | Space | Use Case |
|-----------|------|-------|----------|
| Aho-Corasick | O(n+m+z) | O(n*σ) | Multi-pattern matching |
| Manacher | O(n) | O(n) | All palindromes |
| Expand Center | O(n²) | O(1) | Simple longest palindrome |
| DP Palindrome | O(n²) | O(n²) | Substring queries |

Where n = text length, m = total pattern length, z = matches, σ = alphabet size

---

## 📚 Practice Problems

### Aho-Corasick
| # | Problem | Key Concept |
|---|---------|-------------|
| 1 | [LC 1032: Stream of Characters](https://leetcode.com/problems/stream-of-characters/) | Streaming pattern match |
| 2 | [LC 2781: Longest Valid Substring](https://leetcode.com/problems/length-of-the-longest-valid-substring/) | Invalid pattern filtering |

### Manacher / Palindromes
| # | Problem | Key Concept |
|---|---------|-------------|
| 3 | [LC 5: Longest Palindromic Substring](https://leetcode.com/problems/longest-palindromic-substring/) | Manacher |
| 4 | [LC 131: Palindrome Partitioning](https://leetcode.com/problems/palindrome-partitioning/) | Backtracking |
| 5 | [LC 132: Palindrome Partitioning II](https://leetcode.com/problems/palindrome-partitioning-ii/) | Manacher + DP |
| 6 | [LC 214: Shortest Palindrome](https://leetcode.com/problems/shortest-palindrome/) | Longest prefix palindrome |
| 7 | [LC 336: Palindrome Pairs](https://leetcode.com/problems/palindrome-pairs/) | Trie + palindrome check |
| 8 | [LC 647: Palindromic Substrings](https://leetcode.com/problems/palindromic-substrings/) | Count palindromes |
| 9 | [LC 516: Longest Palindromic Subsequence](https://leetcode.com/problems/longest-palindromic-subsequence/) | DP |
| 10 | [LC 1745: Palindrome Partitioning IV](https://leetcode.com/problems/palindrome-partitioning-iv/) | Manacher + check |

---

*Last Updated: 2024*
