---
title: "Union-Find / Disjoint Set Patterns"
topic: "Problem Patterns"
difficulty: "Medium"
tags: ["union-find", "dsu", "disjoint-set", "connected-components"]
status: "complete"
weight: 17
---

# Union-Find / Disjoint Set Union (DSU)

## 📚 Overview

Union-Find is ideal for dynamic connectivity, cycle detection, and component-based problems.

---

## 1️⃣ Basic Implementation

```python
class UnionFind:
    def __init__(self, n: int):
        self.parent = list(range(n))
        self.rank = [0] * n
        self.components = n
    
    def find(self, x: int) -> int:
        """Find with path compression"""
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])
        return self.parent[x]
    
    def union(self, x: int, y: int) -> bool:
        """Union by rank, return False if already connected"""
        px, py = self.find(x), self.find(y)
        if px == py:
            return False
        
        if self.rank[px] < self.rank[py]:
            px, py = py, px
        self.parent[py] = px
        if self.rank[px] == self.rank[py]:
            self.rank[px] += 1
        
        self.components -= 1
        return True
    
    def connected(self, x: int, y: int) -> bool:
        return self.find(x) == self.find(y)
```

---

## 2️⃣ Union-Find with Size

```python
class UnionFindSize:
    def __init__(self, n: int):
        self.parent = list(range(n))
        self.size = [1] * n
        self.components = n
    
    def find(self, x: int) -> int:
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])
        return self.parent[x]
    
    def union(self, x: int, y: int) -> bool:
        px, py = self.find(x), self.find(y)
        if px == py:
            return False
        
        # Union by size
        if self.size[px] < self.size[py]:
            px, py = py, px
        self.parent[py] = px
        self.size[px] += self.size[py]
        self.components -= 1
        return True
    
    def get_size(self, x: int) -> int:
        return self.size[self.find(x)]
```

---

## 3️⃣ Number of Connected Components

### Graph Components (LC 323)

```python
def count_components(n: int, edges: list[list[int]]) -> int:
    """Count connected components in undirected graph"""
    uf = UnionFind(n)
    
    for u, v in edges:
        uf.union(u, v)
    
    return uf.components
```

### Number of Islands (LC 200) - Union-Find

```python
def num_islands(grid: list[list[str]]) -> int:
    if not grid:
        return 0
    
    rows, cols = len(grid), len(grid[0])
    uf = UnionFind(rows * cols)
    
    water = 0
    for r in range(rows):
        for c in range(cols):
            if grid[r][c] == '0':
                water += 1
                continue
            
            idx = r * cols + c
            
            # Connect with right and down neighbors
            if c + 1 < cols and grid[r][c + 1] == '1':
                uf.union(idx, idx + 1)
            if r + 1 < rows and grid[r + 1][c] == '1':
                uf.union(idx, idx + cols)
    
    return uf.components - water
```

---

## 4️⃣ Redundant Connection (LC 684)

```python
def find_redundant_connection(edges: list[list[int]]) -> list[int]:
    """Find edge that creates cycle"""
    n = len(edges)
    uf = UnionFind(n + 1)  # 1-indexed
    
    for u, v in edges:
        if not uf.union(u, v):
            return [u, v]  # Cycle found
    
    return []
```

### Redundant Connection II - Directed (LC 685)

```python
def find_redundant_directed_connection(edges: list[list[int]]) -> list[int]:
    """Redundant edge in directed graph to form tree"""
    n = len(edges)
    parent = [0] * (n + 1)
    
    # Find node with two parents
    candidate1 = candidate2 = None
    for u, v in edges:
        if parent[v] != 0:
            candidate1 = [parent[v], v]
            candidate2 = [u, v]
            break
        parent[v] = u
    
    # Check for cycle
    uf = UnionFind(n + 1)
    for u, v in edges:
        if [u, v] == candidate2:
            continue
        if not uf.union(u, v):
            return candidate1 if candidate1 else [u, v]
    
    return candidate2
```

---

## 5️⃣ Accounts Merge (LC 721)

```python
def accounts_merge(accounts: list[list[str]]) -> list[list[str]]:
    """Merge accounts with same email"""
    from collections import defaultdict
    
    email_to_id = {}
    email_to_name = {}
    uf = UnionFind(len(accounts) * 10)  # Max emails estimate
    
    email_id = 0
    for acc_idx, account in enumerate(accounts):
        name = account[0]
        for email in account[1:]:
            if email not in email_to_id:
                email_to_id[email] = email_id
                email_id += 1
            email_to_name[email] = name
            uf.union(email_to_id[account[1]], email_to_id[email])
    
    # Group emails by root
    groups = defaultdict(list)
    for email, idx in email_to_id.items():
        root = uf.find(idx)
        groups[root].append(email)
    
    # Build result
    result = []
    for emails in groups.values():
        name = email_to_name[emails[0]]
        result.append([name] + sorted(emails))
    
    return result
```

---

## 6️⃣ Largest Component by Common Factor (LC 952)

```python
def largest_component_size(nums: list[int]) -> int:
    """Largest component where edges connect numbers sharing factor"""
    from collections import defaultdict
    
    # Factor to index mapping
    factor_to_idx = {}
    uf = UnionFind(len(nums))
    
    def get_factors(n):
        factors = []
        d = 2
        while d * d <= n:
            if n % d == 0:
                factors.append(d)
                while n % d == 0:
                    n //= d
            d += 1
        if n > 1:
            factors.append(n)
        return factors
    
    for i, num in enumerate(nums):
        for factor in get_factors(num):
            if factor in factor_to_idx:
                uf.union(i, factor_to_idx[factor])
            else:
                factor_to_idx[factor] = i
    
    # Count sizes
    count = defaultdict(int)
    for i in range(len(nums)):
        count[uf.find(i)] += 1
    
    return max(count.values())
```

---

## 7️⃣ Graph Valid Tree (LC 261)

```python
def valid_tree(n: int, edges: list[list[int]]) -> bool:
    """Check if edges form valid tree"""
    if len(edges) != n - 1:
        return False
    
    uf = UnionFind(n)
    
    for u, v in edges:
        if not uf.union(u, v):
            return False  # Cycle detected
    
    return True
```

---

## 8️⃣ Satisfiability of Equality Equations (LC 990)

```python
def equations_possible(equations: list[str]) -> bool:
    """Check if equations like "a==b", "a!=c" can all be satisfied"""
    uf = UnionFind(26)  # a-z
    
    # Process equalities first
    for eq in equations:
        if eq[1] == '=':
            x = ord(eq[0]) - ord('a')
            y = ord(eq[3]) - ord('a')
            uf.union(x, y)
    
    # Check inequalities
    for eq in equations:
        if eq[1] == '!':
            x = ord(eq[0]) - ord('a')
            y = ord(eq[3]) - ord('a')
            if uf.connected(x, y):
                return False
    
    return True
```

---

## 9️⃣ Regions Cut By Slashes (LC 959)

```python
def regions_by_slashes(grid: list[str]) -> int:
    """Count regions in grid divided by slashes"""
    n = len(grid)
    # Each cell is divided into 4 triangles: top, right, bottom, left
    # Index: cell(r, c), triangle t -> (r * n + c) * 4 + t
    
    uf = UnionFind(n * n * 4)
    
    for r in range(n):
        for c in range(n):
            idx = (r * n + c) * 4
            char = grid[r][c]
            
            # Connect within cell
            if char == '/':
                uf.union(idx + 0, idx + 3)  # top-left
                uf.union(idx + 1, idx + 2)  # right-bottom
            elif char == '\\':
                uf.union(idx + 0, idx + 1)  # top-right
                uf.union(idx + 2, idx + 3)  # bottom-left
            else:  # space
                uf.union(idx + 0, idx + 1)
                uf.union(idx + 1, idx + 2)
                uf.union(idx + 2, idx + 3)
            
            # Connect with neighbors
            if r > 0:  # Connect bottom with top of cell above
                uf.union(idx + 0, ((r - 1) * n + c) * 4 + 2)
            if c > 0:  # Connect left with right of cell to left
                uf.union(idx + 3, (r * n + c - 1) * 4 + 1)
    
    return uf.components
```

---

## 🔟 Online Queries

### Number of Islands II (LC 305)

```python
def num_islands_2(m: int, n: int, positions: list[list[int]]) -> list[int]:
    """Count islands after each land addition"""
    uf = UnionFind(m * n)
    grid = [[0] * n for _ in range(m)]
    result = []
    islands = 0
    directions = [(0, 1), (0, -1), (1, 0), (-1, 0)]
    
    for r, c in positions:
        if grid[r][c] == 1:  # Already land
            result.append(islands)
            continue
        
        grid[r][c] = 1
        islands += 1
        idx = r * n + c
        
        for dr, dc in directions:
            nr, nc = r + dr, c + dc
            if 0 <= nr < m and 0 <= nc < n and grid[nr][nc] == 1:
                nidx = nr * n + nc
                if uf.union(idx, nidx):
                    islands -= 1
        
        result.append(islands)
    
    return result
```

---

## 📊 Time Complexity

| Operation | Time Complexity |
|-----------|-----------------|
| Find | O(α(n)) ≈ O(1) |
| Union | O(α(n)) ≈ O(1) |
| Initialize | O(n) |

α(n) is inverse Ackermann, practically constant.

---

## 📝 Related Problems

| Problem | LC # | Pattern |
|---------|------|---------|
| Number of Provinces | 547 | Basic DSU |
| Redundant Connection | 684 | Cycle Detection |
| Redundant Connection II | 685 | Directed Graph |
| Accounts Merge | 721 | String DSU |
| Most Stones Removed | 947 | Component Counting |
| Satisfiability of Equations | 990 | Constraint Propagation |
| Regions Cut By Slashes | 959 | Grid DSU |
| Number of Islands II | 305 | Online Queries |
| Graph Valid Tree | 261 | Tree Validation |
| Smallest String With Swaps | 1202 | Group Processing |

---

*Last Updated: 2024*
