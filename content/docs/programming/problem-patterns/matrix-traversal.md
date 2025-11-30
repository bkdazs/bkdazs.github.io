---
title: "Matrix Traversal Patterns"
topic: "Problem Patterns"
difficulty: "Medium"
tags: ["matrix", "2d-array", "traversal", "dfs", "bfs", "spiral"]
status: "complete"
weight: 11
---

# Matrix Traversal Patterns

## 📚 Overview

Matrix problems are extremely common in FAANG interviews. Master these traversal patterns.

---

## 1️⃣ Basic Traversals

### Row-Major vs Column-Major

```python
def row_major(matrix: list[list[int]]) -> list[int]:
    """Traverse row by row (standard)"""
    result = []
    for row in matrix:
        for val in row:
            result.append(val)
    return result

def column_major(matrix: list[list[int]]) -> list[int]:
    """Traverse column by column"""
    if not matrix:
        return []
    result = []
    rows, cols = len(matrix), len(matrix[0])
    for c in range(cols):
        for r in range(rows):
            result.append(matrix[r][c])
    return result
```

### Diagonal Traversal

```python
def diagonal_traversal(matrix: list[list[int]]) -> list[int]:
    """Traverse diagonally (LC 498)"""
    if not matrix:
        return []
    
    rows, cols = len(matrix), len(matrix[0])
    result = []
    
    for d in range(rows + cols - 1):
        if d % 2 == 0:
            # Go up
            r = min(d, rows - 1)
            c = d - r
            while r >= 0 and c < cols:
                result.append(matrix[r][c])
                r -= 1
                c += 1
        else:
            # Go down
            c = min(d, cols - 1)
            r = d - c
            while c >= 0 and r < rows:
                result.append(matrix[r][c])
                r += 1
                c -= 1
    
    return result

# Related: LC 498 Diagonal Traverse
```

---

## 2️⃣ Spiral Traversal

### Spiral Matrix (LC 54)

```python
def spiral_order(matrix: list[list[int]]) -> list[int]:
    """Traverse matrix in spiral order"""
    if not matrix:
        return []
    
    result = []
    top, bottom = 0, len(matrix) - 1
    left, right = 0, len(matrix[0]) - 1
    
    while top <= bottom and left <= right:
        # Right
        for c in range(left, right + 1):
            result.append(matrix[top][c])
        top += 1
        
        # Down
        for r in range(top, bottom + 1):
            result.append(matrix[r][right])
        right -= 1
        
        # Left
        if top <= bottom:
            for c in range(right, left - 1, -1):
                result.append(matrix[bottom][c])
            bottom -= 1
        
        # Up
        if left <= right:
            for r in range(bottom, top - 1, -1):
                result.append(matrix[r][left])
            left += 1
    
    return result
```

### Generate Spiral Matrix (LC 59)

```python
def generate_matrix(n: int) -> list[list[int]]:
    """Generate n x n matrix filled spirally 1 to n²"""
    matrix = [[0] * n for _ in range(n)]
    num = 1
    top, bottom, left, right = 0, n - 1, 0, n - 1
    
    while top <= bottom and left <= right:
        for c in range(left, right + 1):
            matrix[top][c] = num
            num += 1
        top += 1
        
        for r in range(top, bottom + 1):
            matrix[r][right] = num
            num += 1
        right -= 1
        
        for c in range(right, left - 1, -1):
            matrix[bottom][c] = num
            num += 1
        bottom -= 1
        
        for r in range(bottom, top - 1, -1):
            matrix[r][left] = num
            num += 1
        left += 1
    
    return matrix
```

---

## 3️⃣ 4-Directional DFS/BFS

### Island Problems Template

```python
def num_islands(grid: list[list[str]]) -> int:
    """Count islands (LC 200)"""
    if not grid:
        return 0
    
    rows, cols = len(grid), len(grid[0])
    count = 0
    
    def dfs(r: int, c: int):
        if r < 0 or r >= rows or c < 0 or c >= cols or grid[r][c] != '1':
            return
        grid[r][c] = '0'  # Mark visited
        dfs(r + 1, c)
        dfs(r - 1, c)
        dfs(r, c + 1)
        dfs(r, c - 1)
    
    for r in range(rows):
        for c in range(cols):
            if grid[r][c] == '1':
                dfs(r, c)
                count += 1
    
    return count
```

### BFS Template (Shortest Path)

```python
from collections import deque

def shortest_path(grid: list[list[int]], start: tuple, end: tuple) -> int:
    """BFS for shortest path in unweighted grid"""
    rows, cols = len(grid), len(grid[0])
    directions = [(0, 1), (0, -1), (1, 0), (-1, 0)]
    
    queue = deque([(start[0], start[1], 0)])
    visited = {start}
    
    while queue:
        r, c, dist = queue.popleft()
        
        if (r, c) == end:
            return dist
        
        for dr, dc in directions:
            nr, nc = r + dr, c + dc
            if 0 <= nr < rows and 0 <= nc < cols and \
               grid[nr][nc] == 0 and (nr, nc) not in visited:
                visited.add((nr, nc))
                queue.append((nr, nc, dist + 1))
    
    return -1
```

---

## 4️⃣ 8-Directional (Chess Knight/King)

```python
# 8 directions (king moves)
DIRECTIONS_8 = [
    (-1, -1), (-1, 0), (-1, 1),
    (0, -1),           (0, 1),
    (1, -1),  (1, 0),  (1, 1)
]

# Knight moves (LC 688, 935)
KNIGHT_MOVES = [
    (-2, -1), (-2, 1), (-1, -2), (-1, 2),
    (1, -2), (1, 2), (2, -1), (2, 1)
]

def knight_probability(n: int, k: int, row: int, column: int) -> float:
    """Probability knight stays on board (LC 688)"""
    from functools import lru_cache
    
    @lru_cache(maxsize=None)
    def dp(r: int, c: int, moves: int) -> float:
        if r < 0 or r >= n or c < 0 or c >= n:
            return 0
        if moves == 0:
            return 1
        
        prob = 0
        for dr, dc in KNIGHT_MOVES:
            prob += dp(r + dr, c + dc, moves - 1) / 8
        return prob
    
    return dp(row, column, k)
```

---

## 5️⃣ Flood Fill / Connected Components

### Flood Fill (LC 733)

```python
def flood_fill(image: list[list[int]], sr: int, sc: int, color: int) -> list[list[int]]:
    """Change connected region to new color"""
    rows, cols = len(image), len(image[0])
    original = image[sr][sc]
    
    if original == color:
        return image
    
    def dfs(r: int, c: int):
        if r < 0 or r >= rows or c < 0 or c >= cols:
            return
        if image[r][c] != original:
            return
        
        image[r][c] = color
        dfs(r + 1, c)
        dfs(r - 1, c)
        dfs(r, c + 1)
        dfs(r, c - 1)
    
    dfs(sr, sc)
    return image
```

### Max Area of Island (LC 695)

```python
def max_area_of_island(grid: list[list[int]]) -> int:
    rows, cols = len(grid), len(grid[0])
    max_area = 0
    
    def dfs(r: int, c: int) -> int:
        if r < 0 or r >= rows or c < 0 or c >= cols or grid[r][c] != 1:
            return 0
        grid[r][c] = 0
        return 1 + dfs(r+1, c) + dfs(r-1, c) + dfs(r, c+1) + dfs(r, c-1)
    
    for r in range(rows):
        for c in range(cols):
            if grid[r][c] == 1:
                max_area = max(max_area, dfs(r, c))
    
    return max_area
```

---

## 6️⃣ Matrix Rotation

### Rotate 90° Clockwise (LC 48)

```python
def rotate(matrix: list[list[int]]) -> None:
    """Rotate matrix 90° clockwise in-place"""
    n = len(matrix)
    
    # Transpose
    for i in range(n):
        for j in range(i + 1, n):
            matrix[i][j], matrix[j][i] = matrix[j][i], matrix[i][j]
    
    # Reverse each row
    for row in matrix:
        row.reverse()

# 90° counter-clockwise: reverse rows, then transpose
# 180°: reverse rows, then reverse each row
```

---

## 7️⃣ Matrix Search

### Search 2D Matrix (LC 74)

```python
def search_matrix(matrix: list[list[int]], target: int) -> bool:
    """Binary search in row-wise & column-wise sorted matrix"""
    if not matrix:
        return False
    
    rows, cols = len(matrix), len(matrix[0])
    left, right = 0, rows * cols - 1
    
    while left <= right:
        mid = (left + right) // 2
        val = matrix[mid // cols][mid % cols]
        
        if val == target:
            return True
        elif val < target:
            left = mid + 1
        else:
            right = mid - 1
    
    return False
```

### Search 2D Matrix II (LC 240)

```python
def search_matrix_ii(matrix: list[list[int]], target: int) -> bool:
    """Search in row-wise AND column-wise sorted matrix"""
    if not matrix:
        return False
    
    rows, cols = len(matrix), len(matrix[0])
    r, c = 0, cols - 1  # Start from top-right
    
    while r < rows and c >= 0:
        if matrix[r][c] == target:
            return True
        elif matrix[r][c] > target:
            c -= 1
        else:
            r += 1
    
    return False
```

---

## 8️⃣ Word Search

### Word Search (LC 79)

```python
def exist(board: list[list[str]], word: str) -> bool:
    rows, cols = len(board), len(board[0])
    
    def dfs(r: int, c: int, idx: int) -> bool:
        if idx == len(word):
            return True
        if r < 0 or r >= rows or c < 0 or c >= cols:
            return False
        if board[r][c] != word[idx]:
            return False
        
        temp = board[r][c]
        board[r][c] = '#'  # Mark visited
        
        found = (dfs(r+1, c, idx+1) or dfs(r-1, c, idx+1) or
                 dfs(r, c+1, idx+1) or dfs(r, c-1, idx+1))
        
        board[r][c] = temp  # Backtrack
        return found
    
    for r in range(rows):
        for c in range(cols):
            if dfs(r, c, 0):
                return True
    return False
```

---

## 📝 Related Problems

| Problem | LC # | Pattern |
|---------|------|---------|
| Spiral Matrix | 54 | Spiral |
| Spiral Matrix II | 59 | Spiral |
| Diagonal Traverse | 498 | Diagonal |
| Number of Islands | 200 | DFS/BFS |
| Max Area of Island | 695 | DFS |
| Surrounded Regions | 130 | DFS boundary |
| Flood Fill | 733 | DFS |
| Rotate Image | 48 | Rotation |
| Search 2D Matrix | 74 | Binary Search |
| Search 2D Matrix II | 240 | Staircase Search |
| Word Search | 79 | Backtracking |
| Word Search II | 212 | Trie + Backtracking |
| Shortest Path in Binary Matrix | 1091 | BFS |
| Pacific Atlantic Water Flow | 417 | Multi-source BFS |
| Walls and Gates | 286 | Multi-source BFS |

---

*Last Updated: 2024*
