---
title: "String DP"
topic: "Dynamic Programming"
difficulty: "Medium-Hard"
tags: ["dp", "strings", "edit-distance", "lcs", "regex", "wildcard"]
status: "complete"
weight: 13
---

# String DP

## 📚 Summary

String DP problems involve comparing, transforming, or matching strings. Common patterns include edit distance, longest common subsequence/substring, and pattern matching.

---

## 1️⃣ Edit Distance (LC 72)

```python
def min_distance(word1: str, word2: str) -> int:
    """
    Minimum operations (insert, delete, replace) to convert word1 to word2
    
    dp[i][j] = min operations to convert word1[:i] to word2[:j]
    
    Time: O(mn), Space: O(mn) or O(min(m,n))
    """
    m, n = len(word1), len(word2)
    
    # dp[i][j] = edit distance for word1[:i] and word2[:j]
    dp = [[0] * (n + 1) for _ in range(m + 1)]
    
    # Base cases
    for i in range(m + 1):
        dp[i][0] = i  # Delete all from word1
    for j in range(n + 1):
        dp[0][j] = j  # Insert all to word1
    
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if word1[i - 1] == word2[j - 1]:
                dp[i][j] = dp[i - 1][j - 1]  # No operation needed
            else:
                dp[i][j] = 1 + min(
                    dp[i - 1][j],     # Delete from word1
                    dp[i][j - 1],     # Insert to word1
                    dp[i - 1][j - 1]  # Replace
                )
    
    return dp[m][n]


def min_distance_optimized(word1: str, word2: str) -> int:
    """Space-optimized O(n) version"""
    m, n = len(word1), len(word2)
    
    # Ensure word2 is shorter for space optimization
    if m < n:
        word1, word2 = word2, word1
        m, n = n, m
    
    prev = list(range(n + 1))
    curr = [0] * (n + 1)
    
    for i in range(1, m + 1):
        curr[0] = i
        for j in range(1, n + 1):
            if word1[i - 1] == word2[j - 1]:
                curr[j] = prev[j - 1]
            else:
                curr[j] = 1 + min(prev[j], curr[j - 1], prev[j - 1])
        prev, curr = curr, prev
    
    return prev[n]


def edit_distance_with_operations(word1: str, word2: str) -> tuple[int, list[str]]:
    """Return distance and list of operations"""
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
                dp[i][j] = 1 + min(dp[i - 1][j], dp[i][j - 1], dp[i - 1][j - 1])
    
    # Backtrack to find operations
    ops = []
    i, j = m, n
    
    while i > 0 or j > 0:
        if i > 0 and j > 0 and word1[i - 1] == word2[j - 1]:
            i -= 1
            j -= 1
        elif i > 0 and j > 0 and dp[i][j] == dp[i - 1][j - 1] + 1:
            ops.append(f"Replace '{word1[i-1]}' at {i-1} with '{word2[j-1]}'")
            i -= 1
            j -= 1
        elif j > 0 and dp[i][j] == dp[i][j - 1] + 1:
            ops.append(f"Insert '{word2[j-1]}' at {i}")
            j -= 1
        else:
            ops.append(f"Delete '{word1[i-1]}' at {i-1}")
            i -= 1
    
    return dp[m][n], ops[::-1]
```

---

## 2️⃣ Longest Common Subsequence (LC 1143)

```python
def longest_common_subsequence(text1: str, text2: str) -> int:
    """
    Length of longest common subsequence
    
    dp[i][j] = LCS length for text1[:i] and text2[:j]
    """
    m, n = len(text1), len(text2)
    dp = [[0] * (n + 1) for _ in range(m + 1)]
    
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if text1[i - 1] == text2[j - 1]:
                dp[i][j] = dp[i - 1][j - 1] + 1
            else:
                dp[i][j] = max(dp[i - 1][j], dp[i][j - 1])
    
    return dp[m][n]


def lcs_with_string(text1: str, text2: str) -> tuple[int, str]:
    """Return LCS length and actual string"""
    m, n = len(text1), len(text2)
    dp = [[0] * (n + 1) for _ in range(m + 1)]
    
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if text1[i - 1] == text2[j - 1]:
                dp[i][j] = dp[i - 1][j - 1] + 1
            else:
                dp[i][j] = max(dp[i - 1][j], dp[i][j - 1])
    
    # Backtrack to find string
    lcs = []
    i, j = m, n
    while i > 0 and j > 0:
        if text1[i - 1] == text2[j - 1]:
            lcs.append(text1[i - 1])
            i -= 1
            j -= 1
        elif dp[i - 1][j] > dp[i][j - 1]:
            i -= 1
        else:
            j -= 1
    
    return dp[m][n], ''.join(reversed(lcs))


def lcs_optimized(text1: str, text2: str) -> int:
    """Space O(min(m,n)) version"""
    if len(text1) < len(text2):
        text1, text2 = text2, text1
    
    n = len(text2)
    prev = [0] * (n + 1)
    curr = [0] * (n + 1)
    
    for c1 in text1:
        for j, c2 in enumerate(text2, 1):
            if c1 == c2:
                curr[j] = prev[j - 1] + 1
            else:
                curr[j] = max(prev[j], curr[j - 1])
        prev, curr = curr, prev
    
    return prev[n]
```

---

## 3️⃣ Longest Common Substring

```python
def longest_common_substring(text1: str, text2: str) -> int:
    """
    Longest contiguous common substring length
    
    dp[i][j] = length of common substring ending at text1[i-1] and text2[j-1]
    """
    m, n = len(text1), len(text2)
    dp = [[0] * (n + 1) for _ in range(m + 1)]
    max_len = 0
    
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if text1[i - 1] == text2[j - 1]:
                dp[i][j] = dp[i - 1][j - 1] + 1
                max_len = max(max_len, dp[i][j])
            # else: dp[i][j] = 0 (implicit)
    
    return max_len


def longest_common_substring_with_result(text1: str, text2: str) -> tuple[int, str]:
    """Return length and actual substring"""
    m, n = len(text1), len(text2)
    dp = [[0] * (n + 1) for _ in range(m + 1)]
    max_len = 0
    end_idx = 0
    
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if text1[i - 1] == text2[j - 1]:
                dp[i][j] = dp[i - 1][j - 1] + 1
                if dp[i][j] > max_len:
                    max_len = dp[i][j]
                    end_idx = i
    
    return max_len, text1[end_idx - max_len:end_idx]
```

---

## 4️⃣ Wildcard Matching (LC 44)

```python
def is_match_wildcard(s: str, p: str) -> bool:
    """
    Wildcard matching: '?' matches any single char, '*' matches any sequence
    
    dp[i][j] = True if s[:i] matches p[:j]
    """
    m, n = len(s), len(p)
    dp = [[False] * (n + 1) for _ in range(m + 1)]
    
    dp[0][0] = True
    
    # Empty string matches pattern of only '*'s
    for j in range(1, n + 1):
        if p[j - 1] == '*':
            dp[0][j] = dp[0][j - 1]
    
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if p[j - 1] == '*':
                # '*' matches empty or matches s[i-1] and continues
                dp[i][j] = dp[i][j - 1] or dp[i - 1][j]
            elif p[j - 1] == '?' or p[j - 1] == s[i - 1]:
                dp[i][j] = dp[i - 1][j - 1]
    
    return dp[m][n]


def is_match_wildcard_optimized(s: str, p: str) -> bool:
    """Space O(n) version"""
    m, n = len(s), len(p)
    prev = [False] * (n + 1)
    prev[0] = True
    
    for j in range(1, n + 1):
        if p[j - 1] == '*':
            prev[j] = prev[j - 1]
    
    for i in range(1, m + 1):
        curr = [False] * (n + 1)
        for j in range(1, n + 1):
            if p[j - 1] == '*':
                curr[j] = curr[j - 1] or prev[j]
            elif p[j - 1] == '?' or p[j - 1] == s[i - 1]:
                curr[j] = prev[j - 1]
        prev = curr
    
    return prev[n]
```

---

## 5️⃣ Regular Expression Matching (LC 10)

```python
def is_match_regex(s: str, p: str) -> bool:
    """
    Regex matching: '.' matches any char, '*' matches zero or more of preceding
    
    dp[i][j] = True if s[:i] matches p[:j]
    """
    m, n = len(s), len(p)
    dp = [[False] * (n + 1) for _ in range(m + 1)]
    
    dp[0][0] = True
    
    # Patterns like a*, a*b*, a*b*c* can match empty string
    for j in range(2, n + 1, 2):
        if p[j - 1] == '*':
            dp[0][j] = dp[0][j - 2]
    
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if p[j - 1] == '*':
                # Zero occurrences of preceding char
                dp[i][j] = dp[i][j - 2]
                
                # One or more occurrences
                if p[j - 2] == '.' or p[j - 2] == s[i - 1]:
                    dp[i][j] = dp[i][j] or dp[i - 1][j]
            elif p[j - 1] == '.' or p[j - 1] == s[i - 1]:
                dp[i][j] = dp[i - 1][j - 1]
    
    return dp[m][n]


def is_match_regex_memo(s: str, p: str) -> bool:
    """Memoization version - often clearer"""
    memo = {}
    
    def dp(i: int, j: int) -> bool:
        if (i, j) in memo:
            return memo[(i, j)]
        
        if j == len(p):
            return i == len(s)
        
        first_match = i < len(s) and (p[j] == '.' or p[j] == s[i])
        
        if j + 1 < len(p) and p[j + 1] == '*':
            # Skip pattern (zero match) or match one and continue
            result = dp(i, j + 2) or (first_match and dp(i + 1, j))
        else:
            result = first_match and dp(i + 1, j + 1)
        
        memo[(i, j)] = result
        return result
    
    return dp(0, 0)
```

---

## 6️⃣ Distinct Subsequences (LC 115)

```python
def num_distinct(s: str, t: str) -> int:
    """
    Count distinct subsequences of s that equal t
    
    dp[i][j] = number of ways to form t[:j] from s[:i]
    """
    m, n = len(s), len(t)
    dp = [[0] * (n + 1) for _ in range(m + 1)]
    
    # Empty t can be formed from any prefix of s in exactly 1 way
    for i in range(m + 1):
        dp[i][0] = 1
    
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            # Don't use s[i-1]
            dp[i][j] = dp[i - 1][j]
            
            # Use s[i-1] if it matches t[j-1]
            if s[i - 1] == t[j - 1]:
                dp[i][j] += dp[i - 1][j - 1]
    
    return dp[m][n]


def num_distinct_optimized(s: str, t: str) -> int:
    """Space O(n) version"""
    n = len(t)
    dp = [0] * (n + 1)
    dp[0] = 1
    
    for c in s:
        # Iterate backwards to avoid overwriting
        for j in range(n, 0, -1):
            if c == t[j - 1]:
                dp[j] += dp[j - 1]
    
    return dp[n]
```

---

## 7️⃣ Interleaving String (LC 97)

```python
def is_interleave(s1: str, s2: str, s3: str) -> bool:
    """
    Check if s3 is an interleaving of s1 and s2
    
    dp[i][j] = True if s3[:i+j] is interleaving of s1[:i] and s2[:j]
    """
    m, n = len(s1), len(s2)
    
    if m + n != len(s3):
        return False
    
    dp = [[False] * (n + 1) for _ in range(m + 1)]
    dp[0][0] = True
    
    # Fill first row (using only s2)
    for j in range(1, n + 1):
        dp[0][j] = dp[0][j - 1] and s2[j - 1] == s3[j - 1]
    
    # Fill first column (using only s1)
    for i in range(1, m + 1):
        dp[i][0] = dp[i - 1][0] and s1[i - 1] == s3[i - 1]
    
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            # Use s1[i-1] or s2[j-1] for s3[i+j-1]
            dp[i][j] = (
                (dp[i - 1][j] and s1[i - 1] == s3[i + j - 1]) or
                (dp[i][j - 1] and s2[j - 1] == s3[i + j - 1])
            )
    
    return dp[m][n]


def is_interleave_optimized(s1: str, s2: str, s3: str) -> bool:
    """Space O(n) version"""
    m, n = len(s1), len(s2)
    
    if m + n != len(s3):
        return False
    
    dp = [False] * (n + 1)
    
    for i in range(m + 1):
        for j in range(n + 1):
            if i == 0 and j == 0:
                dp[j] = True
            elif i == 0:
                dp[j] = dp[j - 1] and s2[j - 1] == s3[j - 1]
            elif j == 0:
                dp[j] = dp[j] and s1[i - 1] == s3[i - 1]
            else:
                dp[j] = (
                    (dp[j] and s1[i - 1] == s3[i + j - 1]) or
                    (dp[j - 1] and s2[j - 1] == s3[i + j - 1])
                )
    
    return dp[n]
```

---

## 8️⃣ Shortest Common Supersequence (LC 1092)

```python
def shortest_common_supersequence(str1: str, str2: str) -> str:
    """
    Shortest string that has both str1 and str2 as subsequences
    
    Length = len(str1) + len(str2) - LCS_length
    """
    m, n = len(str1), len(str2)
    
    # First compute LCS DP table
    dp = [[0] * (n + 1) for _ in range(m + 1)]
    
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if str1[i - 1] == str2[j - 1]:
                dp[i][j] = dp[i - 1][j - 1] + 1
            else:
                dp[i][j] = max(dp[i - 1][j], dp[i][j - 1])
    
    # Backtrack to build SCS
    result = []
    i, j = m, n
    
    while i > 0 and j > 0:
        if str1[i - 1] == str2[j - 1]:
            result.append(str1[i - 1])
            i -= 1
            j -= 1
        elif dp[i - 1][j] > dp[i][j - 1]:
            result.append(str1[i - 1])
            i -= 1
        else:
            result.append(str2[j - 1])
            j -= 1
    
    # Add remaining characters
    while i > 0:
        result.append(str1[i - 1])
        i -= 1
    while j > 0:
        result.append(str2[j - 1])
        j -= 1
    
    return ''.join(reversed(result))
```

---

## 9️⃣ Delete Operation for Two Strings (LC 583)

```python
def min_delete_distance(word1: str, word2: str) -> int:
    """
    Minimum deletions to make two strings equal
    = len(word1) + len(word2) - 2 * LCS_length
    """
    m, n = len(word1), len(word2)
    dp = [[0] * (n + 1) for _ in range(m + 1)]
    
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if word1[i - 1] == word2[j - 1]:
                dp[i][j] = dp[i - 1][j - 1] + 1
            else:
                dp[i][j] = max(dp[i - 1][j], dp[i][j - 1])
    
    lcs_len = dp[m][n]
    return m + n - 2 * lcs_len
```

---

## 🔟 Minimum ASCII Delete Sum (LC 712)

```python
def minimum_delete_sum(s1: str, s2: str) -> int:
    """
    Minimum ASCII sum of deleted characters to make strings equal
    
    dp[i][j] = min delete sum for s1[:i] and s2[:j]
    """
    m, n = len(s1), len(s2)
    dp = [[0] * (n + 1) for _ in range(m + 1)]
    
    # Base cases: delete all from one string
    for i in range(1, m + 1):
        dp[i][0] = dp[i - 1][0] + ord(s1[i - 1])
    for j in range(1, n + 1):
        dp[0][j] = dp[0][j - 1] + ord(s2[j - 1])
    
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if s1[i - 1] == s2[j - 1]:
                dp[i][j] = dp[i - 1][j - 1]
            else:
                dp[i][j] = min(
                    dp[i - 1][j] + ord(s1[i - 1]),  # Delete from s1
                    dp[i][j - 1] + ord(s2[j - 1])   # Delete from s2
                )
    
    return dp[m][n]
```

---

## 1️⃣1️⃣ Word Break Problems

### Word Break (LC 139)

```python
def word_break(s: str, word_dict: list[str]) -> bool:
    """
    Check if s can be segmented into dictionary words
    
    dp[i] = True if s[:i] can be segmented
    """
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


def word_break_optimized(s: str, word_dict: list[str]) -> bool:
    """Optimize by checking only valid word lengths"""
    word_set = set(word_dict)
    max_len = max(len(w) for w in word_dict)
    n = len(s)
    dp = [False] * (n + 1)
    dp[0] = True
    
    for i in range(1, n + 1):
        for j in range(max(0, i - max_len), i):
            if dp[j] and s[j:i] in word_set:
                dp[i] = True
                break
    
    return dp[n]
```

### Word Break II (LC 140)

```python
def word_break_2(s: str, word_dict: list[str]) -> list[str]:
    """
    Return all possible sentences from word break
    """
    word_set = set(word_dict)
    memo = {}
    
    def backtrack(start: int) -> list[list[str]]:
        if start in memo:
            return memo[start]
        
        if start == len(s):
            return [[]]
        
        results = []
        for end in range(start + 1, len(s) + 1):
            word = s[start:end]
            if word in word_set:
                for rest in backtrack(end):
                    results.append([word] + rest)
        
        memo[start] = results
        return results
    
    return [' '.join(words) for words in backtrack(0)]
```

---

## 1️⃣2️⃣ Palindrome-Related String DP

### Longest Palindromic Substring (LC 5)

```python
def longest_palindrome(s: str) -> str:
    """
    dp[i][j] = True if s[i:j+1] is palindrome
    """
    n = len(s)
    if n < 2:
        return s
    
    dp = [[False] * n for _ in range(n)]
    start = 0
    max_len = 1
    
    # All single characters are palindromes
    for i in range(n):
        dp[i][i] = True
    
    # Check length 2
    for i in range(n - 1):
        if s[i] == s[i + 1]:
            dp[i][i + 1] = True
            start = i
            max_len = 2
    
    # Check lengths 3 and above
    for length in range(3, n + 1):
        for i in range(n - length + 1):
            j = i + length - 1
            if s[i] == s[j] and dp[i + 1][j - 1]:
                dp[i][j] = True
                start = i
                max_len = length
    
    return s[start:start + max_len]


def longest_palindrome_expand(s: str) -> str:
    """Expand around center - O(n²) time, O(1) space"""
    def expand(left: int, right: int) -> tuple[int, int]:
        while left >= 0 and right < len(s) and s[left] == s[right]:
            left -= 1
            right += 1
        return left + 1, right - 1
    
    start = end = 0
    for i in range(len(s)):
        # Odd length
        l1, r1 = expand(i, i)
        # Even length
        l2, r2 = expand(i, i + 1)
        
        if r1 - l1 > end - start:
            start, end = l1, r1
        if r2 - l2 > end - start:
            start, end = l2, r2
    
    return s[start:end + 1]
```

---

## ⏱️ Complexity Summary

| Problem | Time | Space |
|---------|------|-------|
| Edit Distance | O(mn) | O(mn) or O(n) |
| LCS | O(mn) | O(mn) or O(n) |
| Wildcard Matching | O(mn) | O(mn) or O(n) |
| Regex Matching | O(mn) | O(mn) |
| Distinct Subsequences | O(mn) | O(n) |
| Interleaving String | O(mn) | O(n) |
| Word Break | O(n²) | O(n) |

---

## 📚 Practice Problems

| # | Problem | Key Concept |
|---|---------|-------------|
| 1 | [LC 72: Edit Distance](https://leetcode.com/problems/edit-distance/) | Classic |
| 2 | [LC 1143: LCS](https://leetcode.com/problems/longest-common-subsequence/) | Subsequence |
| 3 | [LC 44: Wildcard Matching](https://leetcode.com/problems/wildcard-matching/) | Pattern |
| 4 | [LC 10: Regular Expression](https://leetcode.com/problems/regular-expression-matching/) | Regex |
| 5 | [LC 115: Distinct Subsequences](https://leetcode.com/problems/distinct-subsequences/) | Counting |
| 6 | [LC 97: Interleaving String](https://leetcode.com/problems/interleaving-string/) | Merge check |
| 7 | [LC 1092: Shortest Common Supersequence](https://leetcode.com/problems/shortest-common-supersequence/) | Construction |
| 8 | [LC 583: Delete Operation](https://leetcode.com/problems/delete-operation-for-two-strings/) | Two strings |
| 9 | [LC 712: Min ASCII Delete](https://leetcode.com/problems/minimum-ascii-delete-sum-for-two-strings/) | Weighted |
| 10 | [LC 139: Word Break](https://leetcode.com/problems/word-break/) | Dictionary |
| 11 | [LC 140: Word Break II](https://leetcode.com/problems/word-break-ii/) | All solutions |
| 12 | [LC 5: Longest Palindromic Substring](https://leetcode.com/problems/longest-palindromic-substring/) | Palindrome |
| 13 | [LC 516: Longest Palindromic Subsequence](https://leetcode.com/problems/longest-palindromic-subsequence/) | Palindrome |
| 14 | [LC 647: Palindromic Substrings](https://leetcode.com/problems/palindromic-substrings/) | Count |
| 15 | [LC 1312: Min Insertions for Palindrome](https://leetcode.com/problems/minimum-insertion-steps-to-make-a-string-palindrome/) | Transform |
| 16 | [LC 392: Is Subsequence](https://leetcode.com/problems/is-subsequence/) | Basic |
| 17 | [LC 1035: Uncrossed Lines](https://leetcode.com/problems/uncrossed-lines/) | LCS variant |
| 18 | [LC 1458: Max Dot Product](https://leetcode.com/problems/max-dot-product-of-two-subsequences/) | LCS variant |
| 19 | [LC 1216: Valid Palindrome III](https://leetcode.com/problems/valid-palindrome-iii/) | K deletions |
| 20 | [LC 87: Scramble String](https://leetcode.com/problems/scramble-string/) | Advanced |

---

*Last Updated: 2024*
