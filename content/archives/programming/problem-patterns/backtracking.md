---
title: "Backtracking Patterns"
topic: "Problem Patterns"
difficulty: "Medium"
tags: ["backtracking", "recursion", "permutation", "combination", "subset"]
status: "complete"
weight: 21
---

# Backtracking Patterns

## 📚 Overview

Backtracking systematically explores all possibilities by building solutions incrementally and abandoning paths that don't lead to valid solutions.

---

## 1️⃣ Subsets Pattern

### Subsets (LC 78)

```python
def subsets(nums: list[int]) -> list[list[int]]:
    """Generate all subsets"""
    result = []
    
    def backtrack(start: int, path: list[int]):
        result.append(path[:])  # Add current subset
        
        for i in range(start, len(nums)):
            path.append(nums[i])
            backtrack(i + 1, path)
            path.pop()
    
    backtrack(0, [])
    return result

def subsets_iterative(nums: list[int]) -> list[list[int]]:
    """Iterative approach"""
    result = [[]]
    
    for num in nums:
        result += [subset + [num] for subset in result]
    
    return result
```

### Subsets II - With Duplicates (LC 90)

```python
def subsets_with_dup(nums: list[int]) -> list[list[int]]:
    """Subsets with duplicate elements"""
    nums.sort()  # Sort to handle duplicates
    result = []
    
    def backtrack(start: int, path: list[int]):
        result.append(path[:])
        
        for i in range(start, len(nums)):
            # Skip duplicates at same level
            if i > start and nums[i] == nums[i - 1]:
                continue
            
            path.append(nums[i])
            backtrack(i + 1, path)
            path.pop()
    
    backtrack(0, [])
    return result
```

---

## 2️⃣ Permutations Pattern

### Permutations (LC 46)

```python
def permute(nums: list[int]) -> list[list[int]]:
    """Generate all permutations"""
    result = []
    
    def backtrack(path: list[int], remaining: set):
        if not remaining:
            result.append(path[:])
            return
        
        for num in list(remaining):
            path.append(num)
            remaining.remove(num)
            backtrack(path, remaining)
            remaining.add(num)
            path.pop()
    
    backtrack([], set(nums))
    return result

def permute_swap(nums: list[int]) -> list[list[int]]:
    """Swap-based approach"""
    result = []
    
    def backtrack(start: int):
        if start == len(nums):
            result.append(nums[:])
            return
        
        for i in range(start, len(nums)):
            nums[start], nums[i] = nums[i], nums[start]
            backtrack(start + 1)
            nums[start], nums[i] = nums[i], nums[start]
    
    backtrack(0)
    return result
```

### Permutations II - With Duplicates (LC 47)

```python
def permute_unique(nums: list[int]) -> list[list[int]]:
    """Permutations with duplicates"""
    from collections import Counter
    
    result = []
    counter = Counter(nums)
    
    def backtrack(path: list[int]):
        if len(path) == len(nums):
            result.append(path[:])
            return
        
        for num in counter:
            if counter[num] > 0:
                path.append(num)
                counter[num] -= 1
                backtrack(path)
                counter[num] += 1
                path.pop()
    
    backtrack([])
    return result
```

---

## 3️⃣ Combinations Pattern

### Combinations (LC 77)

```python
def combine(n: int, k: int) -> list[list[int]]:
    """All k-combinations from 1 to n"""
    result = []
    
    def backtrack(start: int, path: list[int]):
        if len(path) == k:
            result.append(path[:])
            return
        
        # Pruning: need at least (k - len(path)) more elements
        remaining = k - len(path)
        for i in range(start, n - remaining + 2):
            path.append(i)
            backtrack(i + 1, path)
            path.pop()
    
    backtrack(1, [])
    return result
```

### Combination Sum (LC 39)

```python
def combination_sum(candidates: list[int], target: int) -> list[list[int]]:
    """Combinations that sum to target (reuse allowed)"""
    result = []
    
    def backtrack(start: int, path: list[int], remaining: int):
        if remaining == 0:
            result.append(path[:])
            return
        
        for i in range(start, len(candidates)):
            if candidates[i] > remaining:
                continue
            
            path.append(candidates[i])
            backtrack(i, path, remaining - candidates[i])  # Same i for reuse
            path.pop()
    
    backtrack(0, [], target)
    return result
```

### Combination Sum II (LC 40)

```python
def combination_sum2(candidates: list[int], target: int) -> list[list[int]]:
    """Combinations that sum to target (no reuse)"""
    candidates.sort()
    result = []
    
    def backtrack(start: int, path: list[int], remaining: int):
        if remaining == 0:
            result.append(path[:])
            return
        
        for i in range(start, len(candidates)):
            if candidates[i] > remaining:
                break
            
            # Skip duplicates at same level
            if i > start and candidates[i] == candidates[i - 1]:
                continue
            
            path.append(candidates[i])
            backtrack(i + 1, path, remaining - candidates[i])
            path.pop()
    
    backtrack(0, [], target)
    return result
```

### Combination Sum III (LC 216)

```python
def combination_sum3(k: int, n: int) -> list[list[int]]:
    """K numbers from 1-9 that sum to n"""
    result = []
    
    def backtrack(start: int, path: list[int], remaining: int):
        if len(path) == k:
            if remaining == 0:
                result.append(path[:])
            return
        
        for i in range(start, 10):
            if i > remaining:
                break
            
            path.append(i)
            backtrack(i + 1, path, remaining - i)
            path.pop()
    
    backtrack(1, [], n)
    return result
```

---

## 4️⃣ Partition Pattern

### Palindrome Partitioning (LC 131)

```python
def partition(s: str) -> list[list[str]]:
    """All palindrome partitions"""
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

### Restore IP Addresses (LC 93)

```python
def restore_ip_addresses(s: str) -> list[str]:
    """All valid IP addresses"""
    result = []
    
    def is_valid(segment: str) -> bool:
        return len(segment) == 1 or (segment[0] != '0' and int(segment) <= 255)
    
    def backtrack(start: int, path: list[str]):
        if len(path) == 4:
            if start == len(s):
                result.append('.'.join(path))
            return
        
        remaining_parts = 4 - len(path)
        remaining_len = len(s) - start
        
        # Pruning: each part needs 1-3 chars
        if remaining_len < remaining_parts or remaining_len > remaining_parts * 3:
            return
        
        for length in range(1, 4):
            if start + length > len(s):
                break
            
            segment = s[start:start + length]
            if is_valid(segment):
                path.append(segment)
                backtrack(start + length, path)
                path.pop()
    
    backtrack(0, [])
    return result
```

---

## 5️⃣ Board Search Pattern

### Word Search (LC 79)

```python
def exist(board: list[list[str]], word: str) -> bool:
    """Find word in grid (adjacent cells)"""
    rows, cols = len(board), len(board[0])
    
    def backtrack(r: int, c: int, idx: int) -> bool:
        if idx == len(word):
            return True
        
        if (r < 0 or r >= rows or c < 0 or c >= cols or 
            board[r][c] != word[idx]):
            return False
        
        # Mark as visited
        temp = board[r][c]
        board[r][c] = '#'
        
        # Explore neighbors
        found = (backtrack(r + 1, c, idx + 1) or
                 backtrack(r - 1, c, idx + 1) or
                 backtrack(r, c + 1, idx + 1) or
                 backtrack(r, c - 1, idx + 1))
        
        # Restore
        board[r][c] = temp
        return found
    
    for r in range(rows):
        for c in range(cols):
            if backtrack(r, c, 0):
                return True
    
    return False
```

### N-Queens (LC 51)

```python
def solve_n_queens(n: int) -> list[list[str]]:
    """All valid N-Queens placements"""
    result = []
    board = [['.' for _ in range(n)] for _ in range(n)]
    
    cols = set()
    diag1 = set()  # row - col
    diag2 = set()  # row + col
    
    def backtrack(row: int):
        if row == n:
            result.append([''.join(r) for r in board])
            return
        
        for col in range(n):
            if col in cols or (row - col) in diag1 or (row + col) in diag2:
                continue
            
            # Place queen
            board[row][col] = 'Q'
            cols.add(col)
            diag1.add(row - col)
            diag2.add(row + col)
            
            backtrack(row + 1)
            
            # Remove queen
            board[row][col] = '.'
            cols.remove(col)
            diag1.remove(row - col)
            diag2.remove(row + col)
    
    backtrack(0)
    return result
```

### Sudoku Solver (LC 37)

```python
def solve_sudoku(board: list[list[str]]) -> None:
    """Solve Sudoku in-place"""
    rows = [set() for _ in range(9)]
    cols = [set() for _ in range(9)]
    boxes = [set() for _ in range(9)]
    
    # Initialize sets with existing numbers
    for r in range(9):
        for c in range(9):
            if board[r][c] != '.':
                num = board[r][c]
                rows[r].add(num)
                cols[c].add(num)
                boxes[(r // 3) * 3 + c // 3].add(num)
    
    def backtrack(r: int, c: int) -> bool:
        if r == 9:
            return True
        
        next_r, next_c = (r, c + 1) if c < 8 else (r + 1, 0)
        
        if board[r][c] != '.':
            return backtrack(next_r, next_c)
        
        box_idx = (r // 3) * 3 + c // 3
        
        for num in '123456789':
            if num in rows[r] or num in cols[c] or num in boxes[box_idx]:
                continue
            
            # Place number
            board[r][c] = num
            rows[r].add(num)
            cols[c].add(num)
            boxes[box_idx].add(num)
            
            if backtrack(next_r, next_c):
                return True
            
            # Remove number
            board[r][c] = '.'
            rows[r].remove(num)
            cols[c].remove(num)
            boxes[box_idx].remove(num)
        
        return False
    
    backtrack(0, 0)
```

---

## 6️⃣ Path Finding

### Path Sum II (LC 113)

```python
def path_sum(root, target_sum: int) -> list[list[int]]:
    """Root-to-leaf paths with target sum"""
    result = []
    
    def backtrack(node, remaining: int, path: list[int]):
        if not node:
            return
        
        path.append(node.val)
        
        if not node.left and not node.right and remaining == node.val:
            result.append(path[:])
        else:
            backtrack(node.left, remaining - node.val, path)
            backtrack(node.right, remaining - node.val, path)
        
        path.pop()
    
    backtrack(root, target_sum, [])
    return result
```

### Unique Paths III (LC 980)

```python
def unique_paths_iii(grid: list[list[int]]) -> int:
    """Paths visiting all empty cells exactly once"""
    rows, cols = len(grid), len(grid[0])
    empty_count = 0
    start_r, start_c = 0, 0
    
    for r in range(rows):
        for c in range(cols):
            if grid[r][c] == 1:
                start_r, start_c = r, c
            elif grid[r][c] == 0:
                empty_count += 1
    
    result = 0
    
    def backtrack(r: int, c: int, remaining: int):
        nonlocal result
        
        if grid[r][c] == 2:
            if remaining == 0:
                result += 1
            return
        
        # Mark as visited
        temp = grid[r][c]
        grid[r][c] = -1
        
        for dr, dc in [(0, 1), (0, -1), (1, 0), (-1, 0)]:
            nr, nc = r + dr, c + dc
            if 0 <= nr < rows and 0 <= nc < cols and grid[nr][nc] != -1:
                backtrack(nr, nc, remaining - 1)
        
        # Restore
        grid[r][c] = temp
    
    backtrack(start_r, start_c, empty_count + 1)  # +1 for starting cell
    return result
```

---

## 7️⃣ Expression Problems

### Generate Parentheses (LC 22)

```python
def generate_parenthesis(n: int) -> list[str]:
    """Generate all valid parentheses"""
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

### Expression Add Operators (LC 282)

```python
def add_operators(num: str, target: int) -> list[str]:
    """Insert +, -, * to reach target"""
    result = []
    n = len(num)
    
    def backtrack(idx: int, path: str, value: int, prev: int):
        if idx == n:
            if value == target:
                result.append(path)
            return
        
        for i in range(idx, n):
            # Skip leading zeros
            if i > idx and num[idx] == '0':
                break
            
            curr = int(num[idx:i + 1])
            
            if idx == 0:
                backtrack(i + 1, str(curr), curr, curr)
            else:
                # Addition
                backtrack(i + 1, path + '+' + str(curr), value + curr, curr)
                # Subtraction
                backtrack(i + 1, path + '-' + str(curr), value - curr, -curr)
                # Multiplication (need to undo previous and apply *)
                backtrack(i + 1, path + '*' + str(curr), 
                         value - prev + prev * curr, prev * curr)
    
    backtrack(0, '', 0, 0)
    return result
```

### Letter Combinations of Phone (LC 17)

```python
def letter_combinations(digits: str) -> list[str]:
    """Letter combinations for phone digits"""
    if not digits:
        return []
    
    phone = {
        '2': 'abc', '3': 'def', '4': 'ghi', '5': 'jkl',
        '6': 'mno', '7': 'pqrs', '8': 'tuv', '9': 'wxyz'
    }
    
    result = []
    
    def backtrack(idx: int, path: list[str]):
        if idx == len(digits):
            result.append(''.join(path))
            return
        
        for letter in phone[digits[idx]]:
            path.append(letter)
            backtrack(idx + 1, path)
            path.pop()
    
    backtrack(0, [])
    return result
```

---

## 8️⃣ Constraint Satisfaction

### Word Search II (LC 212)

```python
def find_words(board: list[list[str]], words: list[str]) -> list[str]:
    """Find all words from list in board"""
    # Build Trie
    trie = {}
    for word in words:
        node = trie
        for char in word:
            node = node.setdefault(char, {})
        node['$'] = word  # End marker with word
    
    rows, cols = len(board), len(board[0])
    result = []
    
    def backtrack(r: int, c: int, parent: dict):
        char = board[r][c]
        node = parent.get(char)
        
        if not node:
            return
        
        if '$' in node:
            result.append(node['$'])
            del node['$']  # Avoid duplicates
        
        board[r][c] = '#'  # Mark visited
        
        for dr, dc in [(0, 1), (0, -1), (1, 0), (-1, 0)]:
            nr, nc = r + dr, c + dc
            if 0 <= nr < rows and 0 <= nc < cols and board[nr][nc] != '#':
                backtrack(nr, nc, node)
        
        board[r][c] = char  # Restore
        
        # Prune empty branches
        if not node:
            del parent[char]
    
    for r in range(rows):
        for c in range(cols):
            backtrack(r, c, trie)
    
    return result
```

---

## 9️⃣ Optimization Backtracking

### Partition to K Equal Sum Subsets (LC 698)

```python
def can_partition_k_subsets(nums: list[int], k: int) -> bool:
    """Can partition into k equal sum subsets"""
    total = sum(nums)
    if total % k != 0:
        return False
    
    target = total // k
    nums.sort(reverse=True)  # Optimization: try larger first
    
    if nums[0] > target:
        return False
    
    buckets = [0] * k
    
    def backtrack(idx: int) -> bool:
        if idx == len(nums):
            return all(b == target for b in buckets)
        
        seen = set()  # Skip duplicate bucket values
        
        for i in range(k):
            if buckets[i] in seen:
                continue
            if buckets[i] + nums[idx] <= target:
                seen.add(buckets[i])
                buckets[i] += nums[idx]
                if backtrack(idx + 1):
                    return True
                buckets[i] -= nums[idx]
        
        return False
    
    return backtrack(0)
```

### Matchsticks to Square (LC 473)

```python
def makesquare(matchsticks: list[int]) -> bool:
    """Form square with all matchsticks"""
    total = sum(matchsticks)
    if total % 4 != 0:
        return False
    
    side = total // 4
    matchsticks.sort(reverse=True)
    
    if matchsticks[0] > side:
        return False
    
    sides = [0] * 4
    
    def backtrack(idx: int) -> bool:
        if idx == len(matchsticks):
            return all(s == side for s in sides)
        
        seen = set()
        
        for i in range(4):
            if sides[i] in seen or sides[i] + matchsticks[idx] > side:
                continue
            
            seen.add(sides[i])
            sides[i] += matchsticks[idx]
            
            if backtrack(idx + 1):
                return True
            
            sides[i] -= matchsticks[idx]
        
        return False
    
    return backtrack(0)
```

---

## 📊 Backtracking Template

```python
def backtrack_template(input):
    result = []
    
    def backtrack(state, choices, path):
        # Base case: found valid solution
        if is_solution(state):
            result.append(path[:])  # Copy path
            return
        
        # Pruning: abandon invalid paths early
        if not is_valid(state):
            return
        
        for choice in choices:
            # Make choice
            path.append(choice)
            update_state(state, choice)
            
            # Recurse
            backtrack(state, remaining_choices(choices, choice), path)
            
            # Undo choice (backtrack)
            path.pop()
            restore_state(state, choice)
    
    backtrack(initial_state, all_choices, [])
    return result
```

---

## 📝 Related Problems

| Problem | LC # | Pattern |
|---------|------|---------|
| Subsets | 78 | Subsets |
| Subsets II | 90 | Subsets + Dedup |
| Permutations | 46 | Permutations |
| Permutations II | 47 | Permutations + Dedup |
| Combinations | 77 | Combinations |
| Combination Sum | 39 | Combinations + Reuse |
| Combination Sum II | 40 | Combinations + Dedup |
| Palindrome Partitioning | 131 | Partition |
| N-Queens | 51 | Board Constraint |
| Sudoku Solver | 37 | Board Constraint |
| Word Search | 79 | Grid DFS |
| Generate Parentheses | 22 | Expression |
| Letter Combinations | 17 | Expression |
| Expression Add Operators | 282 | Expression |
| Word Search II | 212 | Trie + DFS |

---

*Last Updated: 2024*
