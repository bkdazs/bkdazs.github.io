---
title: "Interval DP"
topic: "Dynamic Programming"
difficulty: "Hard"
tags: ["dp", "interval", "matrix-chain", "palindrome", "burst-balloons"]
status: "complete"
weight: 12
---

# Interval DP

## 📚 Summary

Interval DP solves problems by considering all possible intervals [i, j] and combining solutions of smaller intervals. The key pattern is iterating by interval length.

---

## 1️⃣ Core Pattern

```python
"""
Interval DP Template:
- dp[i][j] = optimal solution for interval [i, j]
- Iterate by length, then by starting position
- Try all split points k in [i, j-1]

for length in range(2, n + 1):       # Interval length
    for i in range(n - length + 1):  # Start position
        j = i + length - 1           # End position
        for k in range(i, j):        # Split point
            dp[i][j] = combine(dp[i][k], dp[k+1][j])
"""
```

---

## 2️⃣ Matrix Chain Multiplication

```python
def matrix_chain_order(dims: list[int]) -> int:
    """
    Minimum cost to multiply chain of matrices
    dims[i-1] x dims[i] is dimension of matrix i
    
    dp[i][j] = min cost to multiply matrices i to j
    
    Time: O(n³), Space: O(n²)
    """
    n = len(dims) - 1  # Number of matrices
    
    # dp[i][j] = min operations for matrices i to j
    dp = [[0] * n for _ in range(n)]
    
    # Length of chain
    for length in range(2, n + 1):
        for i in range(n - length + 1):
            j = i + length - 1
            dp[i][j] = float('inf')
            
            # Try all split points
            for k in range(i, j):
                # Cost = left chain + right chain + multiplication
                cost = dp[i][k] + dp[k + 1][j] + dims[i] * dims[k + 1] * dims[j + 1]
                dp[i][j] = min(dp[i][j], cost)
    
    return dp[0][n - 1]


def matrix_chain_with_order(dims: list[int]) -> tuple[int, str]:
    """Return optimal cost and parenthesization"""
    n = len(dims) - 1
    dp = [[0] * n for _ in range(n)]
    split = [[0] * n for _ in range(n)]
    
    for length in range(2, n + 1):
        for i in range(n - length + 1):
            j = i + length - 1
            dp[i][j] = float('inf')
            
            for k in range(i, j):
                cost = dp[i][k] + dp[k + 1][j] + dims[i] * dims[k + 1] * dims[j + 1]
                if cost < dp[i][j]:
                    dp[i][j] = cost
                    split[i][j] = k
    
    def build_solution(i: int, j: int) -> str:
        if i == j:
            return f"M{i}"
        k = split[i][j]
        return f"({build_solution(i, k)} × {build_solution(k + 1, j)})"
    
    return dp[0][n - 1], build_solution(0, n - 1)
```

---

## 3️⃣ Burst Balloons (LC 312)

```python
def max_coins(nums: list[int]) -> int:
    """
    Burst balloons to maximize coins
    When balloon i is burst, get nums[i-1] * nums[i] * nums[i+1] coins
    
    Key insight: Think of which balloon to burst LAST in interval [i,j]
    
    dp[i][j] = max coins from bursting all balloons in (i, j) exclusive
    """
    # Add boundary balloons
    nums = [1] + nums + [1]
    n = len(nums)
    
    dp = [[0] * n for _ in range(n)]
    
    # Length of interval (excluding boundaries)
    for length in range(1, n - 1):
        for left in range(0, n - length - 1):
            right = left + length + 1
            
            # Try each balloon as the LAST one to burst
            for k in range(left + 1, right):
                # Coins from bursting k last
                coins = nums[left] * nums[k] * nums[right]
                # Plus coins from left and right subproblems
                coins += dp[left][k] + dp[k][right]
                dp[left][right] = max(dp[left][right], coins)
    
    return dp[0][n - 1]


def max_coins_memo(nums: list[int]) -> int:
    """Memoization version"""
    nums = [1] + nums + [1]
    n = len(nums)
    memo = {}
    
    def dp(left: int, right: int) -> int:
        if left + 1 >= right:
            return 0
        
        if (left, right) in memo:
            return memo[(left, right)]
        
        result = 0
        for k in range(left + 1, right):
            coins = nums[left] * nums[k] * nums[right]
            coins += dp(left, k) + dp(k, right)
            result = max(result, coins)
        
        memo[(left, right)] = result
        return result
    
    return dp(0, n - 1)
```

---

## 4️⃣ Palindrome Problems

### Longest Palindromic Subsequence (LC 516)

```python
def longest_palindrome_subseq(s: str) -> int:
    """
    dp[i][j] = length of longest palindromic subsequence in s[i:j+1]
    """
    n = len(s)
    dp = [[0] * n for _ in range(n)]
    
    # Base case: single characters
    for i in range(n):
        dp[i][i] = 1
    
    # Fill by increasing length
    for length in range(2, n + 1):
        for i in range(n - length + 1):
            j = i + length - 1
            
            if s[i] == s[j]:
                dp[i][j] = dp[i + 1][j - 1] + 2
            else:
                dp[i][j] = max(dp[i + 1][j], dp[i][j - 1])
    
    return dp[0][n - 1]


def longest_palindrome_subseq_optimized(s: str) -> int:
    """Space-optimized O(n) version"""
    n = len(s)
    dp = [1] * n  # Current row
    
    for i in range(n - 2, -1, -1):
        prev = 0  # dp[i+1][i] which is 0
        for j in range(i + 1, n):
            temp = dp[j]
            if s[i] == s[j]:
                dp[j] = prev + 2
            else:
                dp[j] = max(dp[j], dp[j - 1])
            prev = temp
    
    return dp[n - 1]
```

### Minimum Insertions for Palindrome (LC 1312)

```python
def min_insertions(s: str) -> int:
    """
    Minimum insertions to make s a palindrome
    = n - longest palindromic subsequence
    """
    n = len(s)
    dp = [[0] * n for _ in range(n)]
    
    for i in range(n):
        dp[i][i] = 1
    
    for length in range(2, n + 1):
        for i in range(n - length + 1):
            j = i + length - 1
            if s[i] == s[j]:
                dp[i][j] = dp[i + 1][j - 1] + 2
            else:
                dp[i][j] = max(dp[i + 1][j], dp[i][j - 1])
    
    return n - dp[0][n - 1]
```

### Palindrome Partitioning II (LC 132)

```python
def min_cut(s: str) -> int:
    """
    Minimum cuts to partition s into palindromes
    """
    n = len(s)
    
    # is_palindrome[i][j] = True if s[i:j+1] is palindrome
    is_pal = [[False] * n for _ in range(n)]
    
    for i in range(n):
        is_pal[i][i] = True
    
    for length in range(2, n + 1):
        for i in range(n - length + 1):
            j = i + length - 1
            if s[i] == s[j]:
                is_pal[i][j] = (length == 2) or is_pal[i + 1][j - 1]
    
    # dp[i] = min cuts for s[0:i+1]
    dp = list(range(n))  # Worst case: cut everywhere
    
    for i in range(n):
        if is_pal[0][i]:
            dp[i] = 0
        else:
            for j in range(i):
                if is_pal[j + 1][i]:
                    dp[i] = min(dp[i], dp[j] + 1)
    
    return dp[n - 1]
```

---

## 5️⃣ Optimal BST

```python
def optimal_bst(keys: list[int], freq: list[int]) -> int:
    """
    Optimal Binary Search Tree
    Minimize expected search cost given key frequencies
    
    dp[i][j] = min cost BST for keys[i:j+1]
    """
    n = len(keys)
    
    # Prefix sum of frequencies for range sum
    prefix = [0] * (n + 1)
    for i in range(n):
        prefix[i + 1] = prefix[i] + freq[i]
    
    def freq_sum(i: int, j: int) -> int:
        return prefix[j + 1] - prefix[i]
    
    dp = [[0] * n for _ in range(n)]
    
    # Base case: single keys
    for i in range(n):
        dp[i][i] = freq[i]
    
    # Fill by increasing length
    for length in range(2, n + 1):
        for i in range(n - length + 1):
            j = i + length - 1
            dp[i][j] = float('inf')
            
            # Try each key as root
            for r in range(i, j + 1):
                left = dp[i][r - 1] if r > i else 0
                right = dp[r + 1][j] if r < j else 0
                cost = left + right + freq_sum(i, j)
                dp[i][j] = min(dp[i][j], cost)
    
    return dp[0][n - 1]
```

---

## 6️⃣ Stone Game Variants

### Stone Game (LC 877)

```python
def stone_game(piles: list[int]) -> bool:
    """
    Two players pick from ends, maximize score
    Return True if player 1 wins
    
    dp[i][j] = max score difference (current player - opponent) for piles[i:j+1]
    """
    n = len(piles)
    dp = [[0] * n for _ in range(n)]
    
    for i in range(n):
        dp[i][i] = piles[i]
    
    for length in range(2, n + 1):
        for i in range(n - length + 1):
            j = i + length - 1
            # Take left or right, opponent plays optimally
            dp[i][j] = max(
                piles[i] - dp[i + 1][j],
                piles[j] - dp[i][j - 1]
            )
    
    return dp[0][n - 1] > 0
```

### Predict the Winner (LC 486)

```python
def predict_winner(nums: list[int]) -> bool:
    """
    Same as stone game but can tie
    """
    n = len(nums)
    dp = [[0] * n for _ in range(n)]
    
    for i in range(n):
        dp[i][i] = nums[i]
    
    for length in range(2, n + 1):
        for i in range(n - length + 1):
            j = i + length - 1
            dp[i][j] = max(
                nums[i] - dp[i + 1][j],
                nums[j] - dp[i][j - 1]
            )
    
    return dp[0][n - 1] >= 0  # >= 0 means player 1 wins or ties
```

### Minimum Cost to Merge Stones (LC 1000)

```python
def merge_stones(stones: list[int], k: int) -> int:
    """
    Merge k consecutive piles into one, cost = sum of stones
    Minimum cost to merge all into one pile
    
    dp[i][j][m] = min cost to merge stones[i:j+1] into m piles
    """
    n = len(stones)
    
    # Check if possible
    if (n - 1) % (k - 1) != 0:
        return -1
    
    # Prefix sums
    prefix = [0] * (n + 1)
    for i in range(n):
        prefix[i + 1] = prefix[i] + stones[i]
    
    def range_sum(i: int, j: int) -> int:
        return prefix[j + 1] - prefix[i]
    
    # dp[i][j] = min cost to merge [i,j] into as few piles as possible
    INF = float('inf')
    dp = [[0] * n for _ in range(n)]
    
    for length in range(2, n + 1):
        for i in range(n - length + 1):
            j = i + length - 1
            dp[i][j] = INF
            
            # Split into two parts, left becomes 1 pile
            for mid in range(i, j, k - 1):
                dp[i][j] = min(dp[i][j], dp[i][mid] + dp[mid + 1][j])
            
            # If can merge into single pile, add merge cost
            if (j - i) % (k - 1) == 0:
                dp[i][j] += range_sum(i, j)
    
    return dp[0][n - 1]
```

---

## 7️⃣ Boolean Parenthesization

```python
def count_eval(s: str, result: bool) -> int:
    """
    Count ways to parenthesize boolean expression to get result
    s contains 0, 1, &, |, ^
    """
    n = len(s)
    
    # dp[i][j] = (true_count, false_count) for expression s[i:j+1]
    memo = {}
    
    def dp(i: int, j: int) -> tuple[int, int]:
        if i == j:
            val = int(s[i])
            return (1, 0) if val else (0, 1)
        
        if (i, j) in memo:
            return memo[(i, j)]
        
        true_count = false_count = 0
        
        # Try each operator as root
        for k in range(i + 1, j, 2):  # Operators at odd positions
            op = s[k]
            left_t, left_f = dp(i, k - 1)
            right_t, right_f = dp(k + 1, j)
            
            if op == '&':
                true_count += left_t * right_t
                false_count += left_t * right_f + left_f * right_t + left_f * right_f
            elif op == '|':
                true_count += left_t * right_t + left_t * right_f + left_f * right_t
                false_count += left_f * right_f
            elif op == '^':
                true_count += left_t * right_f + left_f * right_t
                false_count += left_t * right_t + left_f * right_f
        
        memo[(i, j)] = (true_count, false_count)
        return memo[(i, j)]
    
    t, f = dp(0, n - 1)
    return t if result else f
```

---

## 8️⃣ Remove Boxes (LC 546)

```python
def remove_boxes(boxes: list[int]) -> int:
    """
    Remove consecutive same-colored boxes, get k² points for k boxes
    
    dp[i][j][k] = max points for boxes[i:j+1] with k extra boxes 
                  of same color as boxes[i] attached to left
    """
    n = len(boxes)
    memo = {}
    
    def dp(i: int, j: int, k: int) -> int:
        if i > j:
            return 0
        
        if (i, j, k) in memo:
            return memo[(i, j, k)]
        
        # Optimization: merge consecutive same-colored boxes
        while i < j and boxes[i] == boxes[i + 1]:
            i += 1
            k += 1
        
        # Option 1: Remove boxes[i] along with k extra boxes
        result = (k + 1) ** 2 + dp(i + 1, j, 0)
        
        # Option 2: Find same color later, merge them
        for m in range(i + 1, j + 1):
            if boxes[m] == boxes[i]:
                # Remove boxes[i+1:m] first, then merge boxes[i] with boxes[m:]
                result = max(result, dp(i + 1, m - 1, 0) + dp(m, j, k + 1))
        
        memo[(i, j, k)] = result
        return result
    
    return dp(0, n - 1, 0)
```

---

## ⏱️ Complexity

| Problem | Time | Space |
|---------|------|-------|
| Matrix Chain | O(n³) | O(n²) |
| Burst Balloons | O(n³) | O(n²) |
| Palindrome Subseq | O(n²) | O(n²) or O(n) |
| Optimal BST | O(n³) | O(n²) |
| Stone Game | O(n²) | O(n²) |
| Remove Boxes | O(n⁴) | O(n³) |

---

## 📚 Practice Problems

| # | Problem | Key Concept |
|---|---------|-------------|
| 1 | [LC 312: Burst Balloons](https://leetcode.com/problems/burst-balloons/) | Classic interval |
| 2 | [LC 516: Longest Palindromic Subsequence](https://leetcode.com/problems/longest-palindromic-subsequence/) | Palindrome |
| 3 | [LC 1312: Min Insertions for Palindrome](https://leetcode.com/problems/minimum-insertion-steps-to-make-a-string-palindrome/) | Palindrome |
| 4 | [LC 132: Palindrome Partitioning II](https://leetcode.com/problems/palindrome-partitioning-ii/) | Min cuts |
| 5 | [LC 877: Stone Game](https://leetcode.com/problems/stone-game/) | Game theory |
| 6 | [LC 486: Predict the Winner](https://leetcode.com/problems/predict-the-winner/) | Game theory |
| 7 | [LC 1000: Merge Stones](https://leetcode.com/problems/minimum-cost-to-merge-stones/) | K-way merge |
| 8 | [LC 546: Remove Boxes](https://leetcode.com/problems/remove-boxes/) | 3D interval |
| 9 | [LC 1039: Min Score Triangulation](https://leetcode.com/problems/minimum-score-triangulation-of-polygon/) | Polygon |
| 10 | [LC 1547: Min Cost to Cut Stick](https://leetcode.com/problems/minimum-cost-to-cut-a-stick/) | Cutting |
| 11 | [LC 664: Strange Printer](https://leetcode.com/problems/strange-printer/) | Printing |
| 12 | [LC 1246: Palindrome Removal](https://leetcode.com/problems/palindrome-removal/) | Remove intervals |
| 13 | [LC 87: Scramble String](https://leetcode.com/problems/scramble-string/) | String interval |
| 14 | [LC 1335: Min Difficulty of Job Schedule](https://leetcode.com/problems/minimum-difficulty-of-a-job-schedule/) | Partition |
| 15 | [LC 1278: Palindrome Partitioning III](https://leetcode.com/problems/palindrome-partitioning-iii/) | K partitions |

---

*Last Updated: 2024*
