---
title: "2D Dynamic Programming"
topic: "Two-Dimensional DP Problems"
difficulty: "Medium to Hard"
tags: ["dp", "grid", "matrix", "2d-dp", "paths"]
status: "complete"
weight: 3
---

# 2D Dynamic Programming

## Summary / TL;DR

2D DP problems involve states defined by two variables (usually two indices, positions in a grid, or two sequences). The state space is typically O(n×m) and transitions come from adjacent or related states.

**Key Insight:** `dp[i][j]` represents the optimal solution for a subproblem defined by first `i` elements of one dimension and first `j` elements of another.

---

## When to Use

- **Two sequences:** Comparing/aligning two strings or arrays
- **Grid problems:** Paths, minimum cost, counting in 2D matrices
- **Intervals:** Problems on ranges `[i, j]`
- **Two indices:** When answer depends on positions from both ends

### Common Patterns

1. **Grid DP:** `dp[i][j] = f(dp[i-1][j], dp[i][j-1], dp[i-1][j-1])`
2. **Two Sequences:** `dp[i][j]` = answer for `s1[0..i-1]` and `s2[0..j-1]`
3. **Interval DP:** `dp[i][j]` = answer for range `[i, j]`
4. **State Machine:** `dp[i][state]` where state has multiple values

---

## Big-O Complexity

| Problem Type | Time | Space | Space Optimized |
|--------------|------|-------|-----------------|
| Grid DP | O(n×m) | O(n×m) | O(min(n,m)) |
| Two sequences | O(n×m) | O(n×m) | O(min(n,m)) |
| Interval DP | O(n²) or O(n³) | O(n²) | Usually not |
| State machine | O(n×k) | O(n×k) | O(k) |

---

## Core Implementation Templates

### Template 1: Grid Path Problems

```python
from typing import List

def unique_paths(m: int, n: int) -> int:
    """
    Count paths from top-left to bottom-right (only right/down moves).
    dp[i][j] = number of paths to reach (i, j)
    
    Time: O(m*n), Space: O(n)
    """
    # Space-optimized: only need previous row
    dp = [1] * n  # First row: all 1s (only way is go right)
    
    for i in range(1, m):
        for j in range(1, n):
            dp[j] = dp[j] + dp[j-1]  # from top + from left
    
    return dp[n-1]


def unique_paths_with_obstacles(grid: List[List[int]]) -> int:
    """
    Same as above but grid[i][j] = 1 means obstacle.
    
    Time: O(m*n), Space: O(n)
    """
    m, n = len(grid), len(grid[0])
    
    if grid[0][0] == 1 or grid[m-1][n-1] == 1:
        return 0
    
    dp = [0] * n
    dp[0] = 1
    
    for i in range(m):
        for j in range(n):
            if grid[i][j] == 1:
                dp[j] = 0
            elif j > 0:
                dp[j] += dp[j-1]
    
    return dp[n-1]


def min_path_sum(grid: List[List[int]]) -> int:
    """
    Minimum sum path from top-left to bottom-right.
    dp[i][j] = min sum to reach (i, j)
    
    Time: O(m*n), Space: O(n)
    """
    m, n = len(grid), len(grid[0])
    dp = [float('inf')] * n
    dp[0] = 0
    
    for i in range(m):
        dp[0] += grid[i][0]  # First column: only from top
        for j in range(1, n):
            dp[j] = min(dp[j], dp[j-1]) + grid[i][j]
    
    return dp[n-1]


def max_gold_in_mine(grid: List[List[int]]) -> int:
    """
    Collect max gold moving right (can go up-right, right, down-right).
    Can start from any cell in first column.
    
    Time: O(m*n), Space: O(m)
    """
    m, n = len(grid), len(grid[0])
    
    # dp[i] = max gold reaching row i in current column
    prev = [grid[i][0] for i in range(m)]
    
    for j in range(1, n):
        curr = [0] * m
        for i in range(m):
            # From row i-1, i, i+1 of previous column
            candidates = [prev[i]]  # Same row
            if i > 0:
                candidates.append(prev[i-1])  # From above
            if i < m - 1:
                candidates.append(prev[i+1])  # From below
            curr[i] = max(candidates) + grid[i][j]
        prev = curr
    
    return max(prev)
```

### Template 2: Longest Common Subsequence (LCS)

```python
def longest_common_subsequence(text1: str, text2: str) -> int:
    """
    LCS of two strings.
    dp[i][j] = LCS of text1[0..i-1] and text2[0..j-1]
    
    If text1[i-1] == text2[j-1]: dp[i][j] = dp[i-1][j-1] + 1
    Else: dp[i][j] = max(dp[i-1][j], dp[i][j-1])
    
    Time: O(m*n), Space: O(min(m, n))
    """
    # Ensure text1 is longer (for space optimization)
    if len(text1) < len(text2):
        text1, text2 = text2, text1
    
    m, n = len(text1), len(text2)
    prev = [0] * (n + 1)
    
    for i in range(1, m + 1):
        curr = [0] * (n + 1)
        for j in range(1, n + 1):
            if text1[i-1] == text2[j-1]:
                curr[j] = prev[j-1] + 1
            else:
                curr[j] = max(prev[j], curr[j-1])
        prev = curr
    
    return prev[n]


def lcs_with_reconstruction(text1: str, text2: str) -> str:
    """
    Return the actual LCS string.
    
    Time: O(m*n), Space: O(m*n)
    """
    m, n = len(text1), len(text2)
    dp = [[0] * (n + 1) for _ in range(m + 1)]
    
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if text1[i-1] == text2[j-1]:
                dp[i][j] = dp[i-1][j-1] + 1
            else:
                dp[i][j] = max(dp[i-1][j], dp[i][j-1])
    
    # Backtrack to find the actual subsequence
    result = []
    i, j = m, n
    while i > 0 and j > 0:
        if text1[i-1] == text2[j-1]:
            result.append(text1[i-1])
            i -= 1
            j -= 1
        elif dp[i-1][j] > dp[i][j-1]:
            i -= 1
        else:
            j -= 1
    
    return ''.join(reversed(result))


def shortest_common_supersequence(str1: str, str2: str) -> str:
    """
    Shortest string containing both str1 and str2 as subsequences.
    Length = m + n - LCS length
    
    Time: O(m*n), Space: O(m*n)
    """
    m, n = len(str1), len(str2)
    dp = [[0] * (n + 1) for _ in range(m + 1)]
    
    # Compute LCS DP table
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if str1[i-1] == str2[j-1]:
                dp[i][j] = dp[i-1][j-1] + 1
            else:
                dp[i][j] = max(dp[i-1][j], dp[i][j-1])
    
    # Build supersequence from DP table
    result = []
    i, j = m, n
    
    while i > 0 and j > 0:
        if str1[i-1] == str2[j-1]:
            result.append(str1[i-1])
            i -= 1
            j -= 1
        elif dp[i-1][j] > dp[i][j-1]:
            result.append(str1[i-1])
            i -= 1
        else:
            result.append(str2[j-1])
            j -= 1
    
    # Add remaining characters
    while i > 0:
        result.append(str1[i-1])
        i -= 1
    while j > 0:
        result.append(str2[j-1])
        j -= 1
    
    return ''.join(reversed(result))
```

### Template 3: Edit Distance

```python
def min_edit_distance(word1: str, word2: str) -> int:
    """
    Minimum operations (insert, delete, replace) to convert word1 to word2.
    
    dp[i][j] = min operations for word1[0..i-1] -> word2[0..j-1]
    
    Time: O(m*n), Space: O(min(m, n))
    """
    m, n = len(word1), len(word2)
    
    # Ensure word2 is shorter for space optimization
    if m < n:
        word1, word2 = word2, word1
        m, n = n, m
    
    prev = list(range(n + 1))  # Base: converting "" to word2[0..j-1]
    
    for i in range(1, m + 1):
        curr = [i] + [0] * n  # Converting word1[0..i-1] to ""
        for j in range(1, n + 1):
            if word1[i-1] == word2[j-1]:
                curr[j] = prev[j-1]  # No operation needed
            else:
                curr[j] = 1 + min(
                    prev[j],      # Delete from word1
                    curr[j-1],    # Insert into word1
                    prev[j-1]     # Replace
                )
        prev = curr
    
    return prev[n]


def one_edit_distance(s: str, t: str) -> bool:
    """
    Check if strings are exactly one edit apart.
    
    Time: O(n), Space: O(1)
    """
    m, n = len(s), len(t)
    
    if abs(m - n) > 1:
        return False
    
    # Ensure s is shorter
    if m > n:
        s, t = t, s
        m, n = n, m
    
    i = j = 0
    found_diff = False
    
    while i < m and j < n:
        if s[i] != t[j]:
            if found_diff:
                return False
            found_diff = True
            
            if m == n:
                i += 1  # Replace: move both
            # Insert: only move j (already different lengths)
        else:
            i += 1
        j += 1
    
    return True  # Extra char at end counts as one edit


def delete_operation_for_two_strings(word1: str, word2: str) -> int:
    """
    Minimum deletions to make two strings equal.
    Answer = m + n - 2 * LCS(word1, word2)
    
    Time: O(m*n), Space: O(n)
    """
    m, n = len(word1), len(word2)
    prev = [0] * (n + 1)
    
    for i in range(1, m + 1):
        curr = [0] * (n + 1)
        for j in range(1, n + 1):
            if word1[i-1] == word2[j-1]:
                curr[j] = prev[j-1] + 1
            else:
                curr[j] = max(prev[j], curr[j-1])
        prev = curr
    
    lcs = prev[n]
    return m + n - 2 * lcs
```

### Template 4: Longest Palindromic Subsequence/Substring

```python
def longest_palindromic_subsequence(s: str) -> int:
    """
    LPS = LCS(s, reverse(s))
    
    Alternative: dp[i][j] = LPS of s[i..j]
    
    Time: O(n²), Space: O(n)
    """
    n = len(s)
    
    # LCS approach with reverse
    prev = [0] * (n + 1)
    
    for i in range(1, n + 1):
        curr = [0] * (n + 1)
        for j in range(1, n + 1):
            # Compare s[i-1] with s[n-j] (reverse)
            if s[i-1] == s[n-j]:
                curr[j] = prev[j-1] + 1
            else:
                curr[j] = max(prev[j], curr[j-1])
        prev = curr
    
    return prev[n]


def longest_palindromic_substring(s: str) -> str:
    """
    Longest contiguous palindrome.
    
    Expand around center approach (O(n²) time, O(1) space).
    """
    if not s:
        return ""
    
    start, max_len = 0, 1
    
    def expand(left: int, right: int) -> tuple:
        while left >= 0 and right < len(s) and s[left] == s[right]:
            left -= 1
            right += 1
        return left + 1, right - left - 1  # start and length
    
    for i in range(len(s)):
        # Odd length palindrome
        l1, len1 = expand(i, i)
        if len1 > max_len:
            start, max_len = l1, len1
        
        # Even length palindrome
        if i + 1 < len(s):
            l2, len2 = expand(i, i + 1)
            if len2 > max_len:
                start, max_len = l2, len2
    
    return s[start:start + max_len]


def count_palindromic_substrings(s: str) -> int:
    """
    Count all palindromic substrings.
    
    Time: O(n²), Space: O(1)
    """
    count = 0
    
    def expand(left: int, right: int) -> int:
        cnt = 0
        while left >= 0 and right < len(s) and s[left] == s[right]:
            cnt += 1
            left -= 1
            right += 1
        return cnt
    
    for i in range(len(s)):
        count += expand(i, i)      # Odd length
        count += expand(i, i + 1)  # Even length
    
    return count


def min_insertions_for_palindrome(s: str) -> int:
    """
    Minimum insertions to make s a palindrome.
    Answer = n - LPS(s)
    
    Time: O(n²), Space: O(n)
    """
    n = len(s)
    prev = [0] * (n + 1)
    
    for i in range(1, n + 1):
        curr = [0] * (n + 1)
        for j in range(1, n + 1):
            if s[i-1] == s[n-j]:
                curr[j] = prev[j-1] + 1
            else:
                curr[j] = max(prev[j], curr[j-1])
        prev = curr
    
    return n - prev[n]
```

### Template 5: Interval DP (Range DP)

```python
def matrix_chain_multiplication(dims: List[int]) -> int:
    """
    Minimum multiplications to multiply chain of matrices.
    dims[i-1] x dims[i] = dimensions of matrix i.
    
    dp[i][j] = min cost to multiply matrices i to j
    
    Time: O(n³), Space: O(n²)
    """
    n = len(dims) - 1  # Number of matrices
    dp = [[0] * n for _ in range(n)]
    
    # Length of chain
    for length in range(2, n + 1):
        for i in range(n - length + 1):
            j = i + length - 1
            dp[i][j] = float('inf')
            
            # Try all split points
            for k in range(i, j):
                cost = (dp[i][k] + dp[k+1][j] + 
                       dims[i] * dims[k+1] * dims[j+1])
                dp[i][j] = min(dp[i][j], cost)
    
    return dp[0][n-1]


def burst_balloons(nums: List[int]) -> int:
    """
    Maximum coins from bursting all balloons.
    When balloon i is burst, get nums[left] * nums[i] * nums[right].
    
    Key insight: Think of which balloon to burst LAST in range.
    
    Time: O(n³), Space: O(n²)
    """
    # Add boundary 1s
    nums = [1] + nums + [1]
    n = len(nums)
    dp = [[0] * n for _ in range(n)]
    
    # dp[i][j] = max coins bursting all balloons in (i, j) exclusive
    for length in range(2, n):
        for i in range(n - length):
            j = i + length
            for k in range(i + 1, j):
                # k is the LAST balloon burst in range (i, j)
                coins = nums[i] * nums[k] * nums[j]
                dp[i][j] = max(dp[i][j], dp[i][k] + coins + dp[k][j])
    
    return dp[0][n-1]


def min_cost_to_merge_stones(stones: List[int], k: int) -> int:
    """
    Merge k consecutive piles into one. Cost = sum of merged piles.
    Minimum cost to merge all into one pile.
    
    Time: O(n³), Space: O(n²)
    """
    n = len(stones)
    
    # Check if possible: each merge reduces by k-1
    if (n - 1) % (k - 1) != 0:
        return -1
    
    # Prefix sums for range sum
    prefix = [0] * (n + 1)
    for i in range(n):
        prefix[i + 1] = prefix[i] + stones[i]
    
    def range_sum(i: int, j: int) -> int:
        return prefix[j + 1] - prefix[i]
    
    # dp[i][j] = min cost to merge stones[i..j] into as few piles as possible
    dp = [[0] * n for _ in range(n)]
    
    for length in range(k, n + 1):
        for i in range(n - length + 1):
            j = i + length - 1
            dp[i][j] = float('inf')
            
            # Try split points
            for mid in range(i, j, k - 1):
                dp[i][j] = min(dp[i][j], dp[i][mid] + dp[mid + 1][j])
            
            # If this range can be merged into one pile
            if (j - i) % (k - 1) == 0:
                dp[i][j] += range_sum(i, j)
    
    return dp[0][n - 1]


def palindrome_partitioning_ii(s: str) -> int:
    """
    Minimum cuts for palindrome partitioning.
    
    Time: O(n²), Space: O(n²)
    """
    n = len(s)
    
    # is_pal[i][j] = True if s[i..j] is palindrome
    is_pal = [[False] * n for _ in range(n)]
    
    for i in range(n):
        is_pal[i][i] = True
    
    for length in range(2, n + 1):
        for i in range(n - length + 1):
            j = i + length - 1
            if s[i] == s[j]:
                is_pal[i][j] = (length == 2) or is_pal[i+1][j-1]
    
    # dp[i] = min cuts for s[0..i]
    dp = list(range(n))  # Worst case: cut after every char
    
    for i in range(n):
        if is_pal[0][i]:
            dp[i] = 0
        else:
            for j in range(i):
                if is_pal[j+1][i]:
                    dp[i] = min(dp[i], dp[j] + 1)
    
    return dp[n - 1]
```

### Template 6: State Machine DP (Stock Problems)

```python
def max_profit_with_cooldown(prices: List[int]) -> int:
    """
    Buy and sell with 1-day cooldown after selling.
    
    States:
    - hold: have stock
    - sold: just sold (cooldown tomorrow)
    - rest: no stock, can buy
    
    Time: O(n), Space: O(1)
    """
    if len(prices) < 2:
        return 0
    
    hold = -prices[0]  # Bought on day 0
    sold = 0           # Just sold
    rest = 0           # Resting
    
    for i in range(1, len(prices)):
        new_hold = max(hold, rest - prices[i])  # Keep or buy
        new_sold = hold + prices[i]              # Sell
        new_rest = max(rest, sold)               # Rest or after cooldown
        
        hold, sold, rest = new_hold, new_sold, new_rest
    
    return max(sold, rest)


def max_profit_with_fee(prices: List[int], fee: int) -> int:
    """
    Buy and sell with transaction fee.
    
    States:
    - hold: have stock
    - cash: no stock
    
    Time: O(n), Space: O(1)
    """
    hold = -prices[0]
    cash = 0
    
    for i in range(1, len(prices)):
        hold = max(hold, cash - prices[i])
        cash = max(cash, hold + prices[i] - fee)
    
    return cash


def max_profit_k_transactions(prices: List[int], k: int) -> int:
    """
    At most k transactions.
    
    dp[t][i] = max profit with at most t transactions up to day i
    
    Time: O(n*k), Space: O(k)
    """
    n = len(prices)
    if n < 2 or k == 0:
        return 0
    
    # If k >= n/2, unlimited transactions
    if k >= n // 2:
        return sum(max(0, prices[i] - prices[i-1]) for i in range(1, n))
    
    # dp[j] = max profit with at most j transactions
    dp = [0] * (k + 1)
    # min_cost[j] = min cost to make j-th buy
    min_cost = [float('inf')] * (k + 1)
    
    for price in prices:
        for j in range(1, k + 1):
            min_cost[j] = min(min_cost[j], price - dp[j-1])
            dp[j] = max(dp[j], price - min_cost[j])
    
    return dp[k]
```

### Template 7: 2D Grid - Maximal Rectangle/Square

```python
def maximal_square(matrix: List[List[str]]) -> int:
    """
    Find largest square of 1s.
    dp[i][j] = side length of largest square with bottom-right at (i,j)
    
    Time: O(m*n), Space: O(n)
    """
    if not matrix:
        return 0
    
    m, n = len(matrix), len(matrix[0])
    prev = [0] * n
    max_side = 0
    
    for i in range(m):
        curr = [0] * n
        for j in range(n):
            if matrix[i][j] == '1':
                if i == 0 or j == 0:
                    curr[j] = 1
                else:
                    curr[j] = min(prev[j], curr[j-1], prev[j-1]) + 1
                max_side = max(max_side, curr[j])
        prev = curr
    
    return max_side * max_side


def maximal_rectangle(matrix: List[List[str]]) -> int:
    """
    Find largest rectangle of 1s.
    Use histogram approach for each row.
    
    Time: O(m*n), Space: O(n)
    """
    if not matrix:
        return 0
    
    m, n = len(matrix), len(matrix[0])
    heights = [0] * n
    max_area = 0
    
    def largest_rectangle_in_histogram(heights: List[int]) -> int:
        stack = [-1]
        max_area = 0
        
        for i, h in enumerate(heights):
            while stack[-1] != -1 and heights[stack[-1]] >= h:
                height = heights[stack.pop()]
                width = i - stack[-1] - 1
                max_area = max(max_area, height * width)
            stack.append(i)
        
        while stack[-1] != -1:
            height = heights[stack.pop()]
            width = len(heights) - stack[-1] - 1
            max_area = max(max_area, height * width)
        
        return max_area
    
    for i in range(m):
        for j in range(n):
            heights[j] = heights[j] + 1 if matrix[i][j] == '1' else 0
        max_area = max(max_area, largest_rectangle_in_histogram(heights))
    
    return max_area


def count_square_submatrices(matrix: List[List[int]]) -> int:
    """
    Count all square submatrices with all 1s.
    
    Time: O(m*n), Space: O(n)
    """
    if not matrix:
        return 0
    
    m, n = len(matrix), len(matrix[0])
    prev = [0] * n
    total = 0
    
    for i in range(m):
        curr = [0] * n
        for j in range(n):
            if matrix[i][j] == 1:
                if i == 0 or j == 0:
                    curr[j] = 1
                else:
                    curr[j] = min(prev[j], curr[j-1], prev[j-1]) + 1
                total += curr[j]  # Each dp[i][j] counts squares ending here
        prev = curr
    
    return total
```

---

## Worked Examples

### Example 1: LCS of "ABCDGH" and "AEDFHR"

```
       ""  A  E  D  F  H  R
   ""   0  0  0  0  0  0  0
    A   0  1  1  1  1  1  1
    B   0  1  1  1  1  1  1
    C   0  1  1  1  1  1  1
    D   0  1  1  2  2  2  2
    G   0  1  1  2  2  2  2
    H   0  1  1  2  2  3  3

LCS = "ADH" (length 3)

Trace back:
- dp[6][6]=3, 'H'!='R', go to max(dp[5][6], dp[6][5])=3 at dp[6][5]
- dp[6][5]=3, 'H'=='H', take 'H', go to dp[5][4]
- dp[5][4]=2, 'G'!='F', go to max(dp[4][4], dp[5][3])=2 at dp[4][4]
- dp[4][4]=2, 'D'!='F', go to dp[4][3]
- dp[4][3]=2, 'D'=='D', take 'D', go to dp[3][2]
- dp[3][2]=1, 'C'!='E', go to dp[2][2]
- dp[2][2]=1, 'B'!='E', go to dp[1][2]
- dp[1][2]=1, 'A'!='E', go to dp[1][1]
- dp[1][1]=1, 'A'=='A', take 'A', go to dp[0][0]
- Done! Reverse: "ADH"
```

### Example 2: Edit Distance "horse" → "ros"

```
        ""  r  o  s
    ""   0  1  2  3
    h    1  1  2  3
    o    2  2  1  2
    r    3  2  2  2
    s    4  3  3  2
    e    5  4  4  3

Operations (trace back from dp[5][3]=3):
- dp[5][3]=3: 'e'!='s', came from dp[4][3]+1 (delete 'e')
- dp[4][3]=2: 's'=='s', came from dp[3][2] (match)
- dp[3][2]=2: 'r'!='o', came from dp[2][2]+1 (delete 'r')
- dp[2][2]=1: 'o'=='o', came from dp[1][1] (match)
- dp[1][1]=1: 'h'!='r', came from dp[0][0]+1 (replace 'h' with 'r')

Operations: replace 'h'→'r', delete 'r', delete 'e' = 3 ops
```

---

## Edge Cases & Gotchas

```python
# 1. Empty strings/arrays
def handle_empty(s1: str, s2: str) -> int:
    if not s1:
        return len(s2)  # All insertions
    if not s2:
        return len(s1)  # All deletions

# 2. Single row/column grid
def grid_single_row(grid: List[List[int]]) -> int:
    if len(grid) == 1:
        return sum(grid[0])

# 3. Space optimization direction
# When using 1D array, be careful about update order:
# - Left-to-right if dp[j] depends on dp[j-1] and prev[j]
# - Right-to-left if dp[j] depends on prev[j] and prev[j+1]

# 4. Index off-by-one
# Common: dp[i][j] for s1[0..i-1] and s2[0..j-1]
# Make sure dp dimensions are (m+1) x (n+1)

# 5. Interval DP base cases
def interval_base(dp: List[List[int]], n: int):
    # Length 1 intervals
    for i in range(n):
        dp[i][i] = 0  # or appropriate base value
```

---

## Interview Tips & Communication

### Framework for 2D DP Problems

1. **Identify the two dimensions:**
   - Two strings → indices i, j
   - Grid → row, column
   - Range → start, end indices

2. **Define state clearly:**
   - "dp[i][j] represents the answer for text1[0..i-1] and text2[0..j-1]"

3. **Write transition:**
   - Consider all cases (match/mismatch, directions, etc.)

4. **Space optimization:**
   - If only need previous row/column, optimize to O(n)

### Key Phrases

- "This is a classic 2D DP problem where dp[i][j] represents..."
- "The recurrence has two cases: if characters match... otherwise..."
- "I can optimize space by only keeping the previous row."
- "For interval DP, I'll iterate by increasing length."

---

## Practice Problems

### Grid DP

| # | Problem | Key Concept |
|---|---------|-------------|
| 1 | [Unique Paths](https://leetcode.com/problems/unique-paths/) | Basic grid DP |
| 2 | [Unique Paths II](https://leetcode.com/problems/unique-paths-ii/) | With obstacles |
| 3 | [Minimum Path Sum](https://leetcode.com/problems/minimum-path-sum/) | Min cost path |
| 4 | [Triangle](https://leetcode.com/problems/triangle/) | Top-down grid |
| 5 | [Dungeon Game](https://leetcode.com/problems/dungeon-game/) | Reverse DP |
| 6 | [Cherry Pickup](https://leetcode.com/problems/cherry-pickup/) | Two paths simultaneously |
| 7 | [Maximal Square](https://leetcode.com/problems/maximal-square/) | Square detection |
| 8 | [Maximal Rectangle](https://leetcode.com/problems/maximal-rectangle/) | Histogram approach |

### Two Sequences

| # | Problem | Key Concept |
|---|---------|-------------|
| 9 | [Longest Common Subsequence](https://leetcode.com/problems/longest-common-subsequence/) | Classic LCS |
| 10 | [Edit Distance](https://leetcode.com/problems/edit-distance/) | Levenshtein distance |
| 11 | [Delete Operation for Two Strings](https://leetcode.com/problems/delete-operation-for-two-strings/) | LCS variant |
| 12 | [Shortest Common Supersequence](https://leetcode.com/problems/shortest-common-supersequence/) | LCS + reconstruction |
| 13 | [Distinct Subsequences](https://leetcode.com/problems/distinct-subsequences/) | Counting DP |
| 14 | [Interleaving String](https://leetcode.com/problems/interleaving-string/) | Three strings |
| 15 | [Wildcard Matching](https://leetcode.com/problems/wildcard-matching/) | Pattern matching |
| 16 | [Regular Expression Matching](https://leetcode.com/problems/regular-expression-matching/) | Complex pattern |

### Palindrome

| # | Problem | Key Concept |
|---|---------|-------------|
| 17 | [Longest Palindromic Substring](https://leetcode.com/problems/longest-palindromic-substring/) | Expand around center |
| 18 | [Longest Palindromic Subsequence](https://leetcode.com/problems/longest-palindromic-subsequence/) | LCS with reverse |
| 19 | [Palindromic Substrings](https://leetcode.com/problems/palindromic-substrings/) | Count all |
| 20 | [Palindrome Partitioning II](https://leetcode.com/problems/palindrome-partitioning-ii/) | Min cuts |

### Interval DP

| # | Problem | Key Concept |
|---|---------|-------------|
| 21 | [Burst Balloons](https://leetcode.com/problems/burst-balloons/) | Last element chosen |
| 22 | [Minimum Cost to Merge Stones](https://leetcode.com/problems/minimum-cost-to-merge-stones/) | K-way merge |
| 23 | [Strange Printer](https://leetcode.com/problems/strange-printer/) | Range printing |
| 24 | [Minimum Score Triangulation](https://leetcode.com/problems/minimum-score-triangulation-of-polygon/) | Polygon triangulation |

### State Machine (Stock)

| # | Problem | Key Concept |
|---|---------|-------------|
| 25 | [Best Time to Buy and Sell Stock](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/) | One transaction |
| 26 | [Best Time to Buy and Sell Stock II](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/) | Unlimited |
| 27 | [Best Time to Buy and Sell Stock III](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iii/) | Two transactions |
| 28 | [Best Time to Buy and Sell Stock IV](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iv/) | K transactions |
| 29 | [Best Time to Buy and Sell Stock with Cooldown](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/) | State machine |
| 30 | [Best Time to Buy and Sell Stock with Transaction Fee](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/) | With fee |

---

## References

- CLRS: Chapter 15 (Dynamic Programming)
- [LeetCode 2D DP Patterns](https://leetcode.com/discuss/study-guide/1000929)
- [Edit Distance Tutorial](https://web.stanford.edu/class/cs124/lec/med.pdf)
- [Matrix Chain Multiplication](https://en.wikipedia.org/wiki/Matrix_chain_multiplication)
