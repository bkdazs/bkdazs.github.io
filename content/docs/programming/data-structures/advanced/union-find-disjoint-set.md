---
title: Union-Find / Disjoint Set Union
topic: Union-Find
difficulty: Medium
tags: [union-find, disjoint-set, dsu, connected-components, graph]
status: complete
weight: 15
---

# Union-Find / Disjoint Set Union (DSU)

## 1. Summary / TL;DR

- **Union-Find** tracks elements partitioned into disjoint (non-overlapping) sets
- Two operations: **Find** (which set?) and **Union** (merge sets)
- With optimizations: near **O(1)** amortized time per operation (inverse Ackermann)
- Key optimizations: **Path Compression** + **Union by Rank/Size**
- Common uses: connected components, Kruskal's MST, cycle detection, dynamic connectivity
- Often the simplest/cleanest solution for problems involving grouping and merging

---

## 2. When & Where to Use

| Scenario | Why Union-Find? |
|----------|----------------|
| Connected components (dynamic) | Add edges, query connectivity |
| Kruskal's MST algorithm | Cycle detection during edge addition |
| Cycle detection in undirected graph | Find if edge connects same component |
| Network connectivity | Friends, social networks |
| Image processing | Connected regions |
| Percolation problems | Grid connectivity |
| Equivalence classes | Group related items |

### Union-Find vs BFS/DFS

| Aspect | Union-Find | BFS/DFS |
|--------|------------|---------|
| Query connectivity | O(α(n)) ≈ O(1) | O(V + E) |
| Add edge | O(α(n)) ≈ O(1) | Rebuild or O(V + E) |
| Dynamic edges | Excellent | Poor |
| Count components | O(n) to count | O(V + E) |
| Find path | Cannot | Can |
| Remove edge | Cannot (without extra work) | Can rebuild |

---

## 3. Time & Space Complexity

| Operation | Naive | Path Compression | PC + Union by Rank |
|-----------|-------|------------------|-------------------|
| Find | O(n) | O(log n) amortized | O(α(n)) ≈ O(1) |
| Union | O(n) | O(log n) amortized | O(α(n)) ≈ O(1) |
| Connected | O(n) | O(log n) amortized | O(α(n)) ≈ O(1) |

**α(n)** is the inverse Ackermann function, which is ≤ 5 for all practical n (even n = 10^80).

**Space**: O(n) for parent and rank arrays

---

## 4. Core Concepts & Theory

### Basic Idea

Each set is represented as a tree:
- **Root** is the representative (identifier) of the set
- **Find**: Follow parent pointers to root
- **Union**: Make one root point to another

```
Initial:  Each element is its own set
{0} {1} {2} {3} {4}

After Union(0,1), Union(2,3):
  0     2
  |     |
  1     3     {4}

After Union(0,2):
    0
   / \
  1   2
      |
      3     {4}
```

### Path Compression

During Find, make every node on path point directly to root.

```
Before Find(4):        After Find(4) with path compression:
    0                       0
    |                    / | \ \
    1                   1  2  3  4
    |
    2
    |
    3
    |
    4

Future Find(4) is O(1)!
```

### Union by Rank/Size

When merging, attach smaller tree under larger one to keep height low.

```
Union by Rank:
Rank = upper bound on height

Without union by rank:     With union by rank:
    0                           2
    |                         / | \
    1    union    2          0  3  4
    |   ------→   |          |
    2            0-1         1
                  |
                 1-3
                  |
                  4

Keeps tree balanced!
```

---

## 5. Diagrams / Visualizations

### Complete Example: Building Connected Components

```
Edges: (0,1), (2,3), (1,2), (4,5), (3,4)

Initial: {0} {1} {2} {3} {4} {5}
parent: [0, 1, 2, 3, 4, 5]

After (0,1): {0,1} {2} {3} {4} {5}
parent: [0, 0, 2, 3, 4, 5]   (1→0)

After (2,3): {0,1} {2,3} {4} {5}
parent: [0, 0, 2, 2, 4, 5]   (3→2)

After (1,2): {0,1,2,3} {4} {5}
parent: [0, 0, 0, 2, 4, 5]   (2→0, or 0→2 depending on rank)

After (4,5): {0,1,2,3} {4,5}
parent: [0, 0, 0, 2, 4, 4]   (5→4)

After (3,4): {0,1,2,3,4,5}
parent: [0, 0, 0, 2, 0, 4]   (4→0)

Final: All connected!
```

### Visual: Union by Rank in Action

```
Union sets of rank 1 and rank 2:

Rank 1:    Rank 2:
  0          3
  |         / \
  1        4   5

Result (attach rank 1 under rank 2):
      3 (rank 2)
    / | \
   0  4  5
   |
   1

Tree height didn't increase!
```

---

## 6. Implementation (Python)

### Basic Union-Find

```python
from typing import List

class UnionFind:
    """
    Union-Find (Disjoint Set Union) with Path Compression and Union by Rank.
    
    >>> uf = UnionFind(5)
    >>> uf.union(0, 1)
    True
    >>> uf.union(2, 3)
    True
    >>> uf.connected(0, 1)
    True
    >>> uf.connected(0, 2)
    False
    >>> uf.union(1, 2)
    True
    >>> uf.connected(0, 3)
    True
    >>> uf.count
    2
    """
    
    def __init__(self, n: int):
        """Initialize n elements, each in its own set."""
        self.parent = list(range(n))  # parent[i] = parent of i
        self.rank = [0] * n            # rank[i] = rank of tree rooted at i
        self.count = n                 # number of disjoint sets
    
    def find(self, x: int) -> int:
        """
        Find root of element x with path compression.
        
        Time: O(α(n)) amortized ≈ O(1)
        """
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])  # Path compression
        return self.parent[x]
    
    def union(self, x: int, y: int) -> bool:
        """
        Union sets containing x and y.
        Returns True if x and y were in different sets.
        
        Time: O(α(n)) amortized ≈ O(1)
        """
        root_x = self.find(x)
        root_y = self.find(y)
        
        if root_x == root_y:
            return False  # Already in same set
        
        # Union by rank: attach smaller tree under larger one
        if self.rank[root_x] < self.rank[root_y]:
            self.parent[root_x] = root_y
        elif self.rank[root_x] > self.rank[root_y]:
            self.parent[root_y] = root_x
        else:
            self.parent[root_y] = root_x
            self.rank[root_x] += 1
        
        self.count -= 1
        return True
    
    def connected(self, x: int, y: int) -> bool:
        """Check if x and y are in the same set."""
        return self.find(x) == self.find(y)
    
    def get_count(self) -> int:
        """Return number of disjoint sets."""
        return self.count
```

### Union-Find with Size (Alternative)

```python
class UnionFindSize:
    """
    Union-Find with Union by Size instead of Rank.
    Useful when you need to track component sizes.
    
    >>> uf = UnionFindSize(5)
    >>> uf.union(0, 1)
    >>> uf.union(2, 3)
    >>> uf.union(1, 2)
    >>> uf.get_size(0)  # Size of component containing 0
    4
    """
    
    def __init__(self, n: int):
        self.parent = list(range(n))
        self.size = [1] * n  # size[i] = size of tree rooted at i
        self.count = n
    
    def find(self, x: int) -> int:
        """Find with path compression."""
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])
        return self.parent[x]
    
    def union(self, x: int, y: int) -> bool:
        """Union by size: attach smaller tree to larger."""
        root_x = self.find(x)
        root_y = self.find(y)
        
        if root_x == root_y:
            return False
        
        # Union by size
        if self.size[root_x] < self.size[root_y]:
            self.parent[root_x] = root_y
            self.size[root_y] += self.size[root_x]
        else:
            self.parent[root_y] = root_x
            self.size[root_x] += self.size[root_y]
        
        self.count -= 1
        return True
    
    def get_size(self, x: int) -> int:
        """Get size of component containing x."""
        return self.size[self.find(x)]
    
    def get_max_size(self) -> int:
        """Get size of largest component."""
        return max(self.size[self.find(i)] for i in range(len(self.parent)))
```

### Find without Path Compression (Iterative)

```python
def find_iterative(parent: List[int], x: int) -> int:
    """
    Iterative find without path compression.
    Useful when you can't modify parent array.
    """
    root = x
    while parent[root] != root:
        root = parent[root]
    return root


def find_with_path_compression_iterative(parent: List[int], x: int) -> int:
    """
    Iterative find WITH path compression.
    Two-pass: find root, then compress path.
    """
    # First pass: find root
    root = x
    while parent[root] != root:
        root = parent[root]
    
    # Second pass: path compression
    while parent[x] != root:
        next_x = parent[x]
        parent[x] = root
        x = next_x
    
    return root
```

### Weighted Union-Find (with Edge Weights)

```python
class WeightedUnionFind:
    """
    Union-Find where each node has a weight relative to its parent.
    Useful for problems involving ratios (e.g., currency exchange).
    
    If weight[x] = w, then value(x) = w * value(parent[x])
    
    >>> wuf = WeightedUnionFind(3)
    >>> wuf.union(0, 1, 2.0)  # value(0) = 2 * value(1)
    >>> wuf.union(1, 2, 3.0)  # value(1) = 3 * value(2)
    >>> wuf.query(0, 2)  # value(0) / value(2)
    6.0
    """
    
    def __init__(self, n: int):
        self.parent = list(range(n))
        self.weight = [1.0] * n  # weight[i] = value(i) / value(parent[i])
    
    def find(self, x: int) -> tuple:
        """
        Find root and accumulated weight from x to root.
        Returns (root, weight_to_root)
        """
        if self.parent[x] == x:
            return x, 1.0
        
        root, parent_weight = self.find(self.parent[x])
        self.parent[x] = root
        self.weight[x] *= parent_weight
        return root, self.weight[x]
    
    def union(self, x: int, y: int, w: float) -> bool:
        """
        Union with relationship: value(x) = w * value(y)
        """
        root_x, weight_x = self.find(x)
        root_y, weight_y = self.find(y)
        
        if root_x == root_y:
            return False
        
        # value(x) = weight_x * value(root_x)
        # value(y) = weight_y * value(root_y)
        # value(x) = w * value(y)
        # So: weight_x * value(root_x) = w * weight_y * value(root_y)
        # If we set parent[root_x] = root_y:
        # weight[root_x] = w * weight_y / weight_x
        
        self.parent[root_x] = root_y
        self.weight[root_x] = w * weight_y / weight_x
        return True
    
    def query(self, x: int, y: int) -> float:
        """
        Query value(x) / value(y).
        Returns -1 if x and y are not connected.
        """
        root_x, weight_x = self.find(x)
        root_y, weight_y = self.find(y)
        
        if root_x != root_y:
            return -1.0
        
        return weight_x / weight_y
```

---

## 7. Step-by-Step Worked Example

### Problem: Number of Connected Components (LeetCode 323)

**Problem**: Given n nodes labeled 0 to n-1 and a list of undirected edges, find the number of connected components.

**Example**:
```
n = 5
edges = [[0,1], [1,2], [3,4]]

Components: {0,1,2}, {3,4}
Answer: 2
```

```python
def count_components(n: int, edges: List[List[int]]) -> int:
    """
    Count connected components using Union-Find.
    
    Time: O(E * α(n)) ≈ O(E)
    Space: O(n)
    
    >>> count_components(5, [[0,1], [1,2], [3,4]])
    2
    >>> count_components(5, [[0,1], [1,2], [2,3], [3,4]])
    1
    """
    uf = UnionFind(n)
    
    for u, v in edges:
        uf.union(u, v)
    
    return uf.count
```

**Trace**:
```
Initial: count = 5
parent = [0, 1, 2, 3, 4]

Edge (0,1):
  find(0) = 0, find(1) = 1
  Union: parent[1] = 0, count = 4
  parent = [0, 0, 2, 3, 4]

Edge (1,2):
  find(1) = 0 (with path compression)
  find(2) = 2
  Union: parent[2] = 0, count = 3
  parent = [0, 0, 0, 3, 4]

Edge (3,4):
  find(3) = 3, find(4) = 4
  Union: parent[4] = 3, count = 2
  parent = [0, 0, 0, 3, 3]

Final count = 2 ✓
```

### Problem: Redundant Connection (LeetCode 684)

**Problem**: In a tree with one extra edge (making it a graph with exactly one cycle), find the edge that can be removed to make it a tree.

```python
def find_redundant_connection(edges: List[List[int]]) -> List[int]:
    """
    Find the edge that creates a cycle.
    
    Key insight: When we try to union two nodes that are already 
    in the same component, that edge creates a cycle.
    
    Time: O(n * α(n)) ≈ O(n)
    Space: O(n)
    
    >>> find_redundant_connection([[1,2], [1,3], [2,3]])
    [2, 3]
    >>> find_redundant_connection([[1,2], [2,3], [3,4], [1,4], [1,5]])
    [1, 4]
    """
    n = len(edges)
    uf = UnionFind(n + 1)  # 1-indexed nodes
    
    for u, v in edges:
        if not uf.union(u, v):
            return [u, v]  # Already connected = cycle!
    
    return []
```

---

## 8. Common Mistakes

1. **Off-by-one with node numbering**
   ```python
   # If nodes are 1-indexed, create uf with n+1
   uf = UnionFind(n + 1)  # Not UnionFind(n)!
   ```

2. **Forgetting path compression**
   ```python
   # Wrong: no path compression
   def find(self, x):
       while self.parent[x] != x:
           x = self.parent[x]
       return x
   
   # Correct: with path compression
   def find(self, x):
       if self.parent[x] != x:
           self.parent[x] = self.find(self.parent[x])
       return self.parent[x]
   ```

3. **Not using union by rank/size**
   ```python
   # Wrong: always attach to root_x
   def union(self, x, y):
       root_x, root_y = self.find(x), self.find(y)
       self.parent[root_y] = root_x  # Can create long chains!
   
   # Correct: union by rank
   ```

4. **Incorrect size tracking**
   ```python
   # Wrong: updating size of wrong root
   self.size[root_x] += self.size[root_y]  # Before changing parent!
   self.parent[root_y] = root_x
   
   # Correct: update size of new root AFTER checking which becomes root
   ```

5. **Not counting correctly**
   ```python
   # Wrong: only decrement if union actually happens
   def union(self, x, y):
       root_x, root_y = self.find(x), self.find(y)
       self.parent[root_y] = root_x
       self.count -= 1  # Wrong if root_x == root_y!
   
   # Correct
   def union(self, x, y):
       root_x, root_y = self.find(x), self.find(y)
       if root_x == root_y:
           return False
       self.parent[root_y] = root_x
       self.count -= 1
       return True
   ```

---

## 9. Variations & Optimizations

### Union-Find with Rollback (Offline)

```python
class UnionFindRollback:
    """
    Union-Find that supports rollback of union operations.
    Useful for divide & conquer on queries.
    
    Note: Cannot use path compression (to enable rollback).
    Uses union by rank only.
    """
    
    def __init__(self, n: int):
        self.parent = list(range(n))
        self.rank = [0] * n
        self.history = []  # (root_x, root_y, rank_changed)
    
    def find(self, x: int) -> int:
        """Find WITHOUT path compression."""
        while self.parent[x] != x:
            x = self.parent[x]
        return x
    
    def union(self, x: int, y: int) -> bool:
        root_x = self.find(x)
        root_y = self.find(y)
        
        if root_x == root_y:
            return False
        
        # Union by rank
        rank_changed = False
        if self.rank[root_x] < self.rank[root_y]:
            root_x, root_y = root_y, root_x
        
        self.parent[root_y] = root_x
        if self.rank[root_x] == self.rank[root_y]:
            self.rank[root_x] += 1
            rank_changed = True
        
        self.history.append((root_x, root_y, rank_changed))
        return True
    
    def rollback(self) -> None:
        """Undo the last union operation."""
        if not self.history:
            return
        
        root_x, root_y, rank_changed = self.history.pop()
        self.parent[root_y] = root_y
        if rank_changed:
            self.rank[root_x] -= 1
```

### Union-Find for 2D Grid

```python
class UnionFind2D:
    """
    Union-Find for 2D grid problems.
    Maps (row, col) to 1D index.
    
    >>> uf = UnionFind2D(3, 3)
    >>> uf.union(0, 0, 0, 1)  # Connect (0,0) and (0,1)
    >>> uf.connected(0, 0, 0, 1)
    True
    """
    
    def __init__(self, rows: int, cols: int):
        self.rows = rows
        self.cols = cols
        n = rows * cols
        self.parent = list(range(n))
        self.rank = [0] * n
        self.count = n
    
    def _index(self, r: int, c: int) -> int:
        return r * self.cols + c
    
    def find(self, r: int, c: int) -> int:
        return self._find(self._index(r, c))
    
    def _find(self, x: int) -> int:
        if self.parent[x] != x:
            self.parent[x] = self._find(self.parent[x])
        return self.parent[x]
    
    def union(self, r1: int, c1: int, r2: int, c2: int) -> bool:
        return self._union(self._index(r1, c1), self._index(r2, c2))
    
    def _union(self, x: int, y: int) -> bool:
        root_x = self._find(x)
        root_y = self._find(y)
        
        if root_x == root_y:
            return False
        
        if self.rank[root_x] < self.rank[root_y]:
            self.parent[root_x] = root_y
        elif self.rank[root_x] > self.rank[root_y]:
            self.parent[root_y] = root_x
        else:
            self.parent[root_y] = root_x
            self.rank[root_x] += 1
        
        self.count -= 1
        return True
    
    def connected(self, r1: int, c1: int, r2: int, c2: int) -> bool:
        return self.find(r1, c1) == self.find(r2, c2)
```

### Number of Islands using Union-Find

```python
def num_islands_union_find(grid: List[List[str]]) -> int:
    """
    Count number of islands using Union-Find.
    
    Alternative to BFS/DFS approach.
    
    >>> grid = [
    ...     ["1","1","0","0","0"],
    ...     ["1","1","0","0","0"],
    ...     ["0","0","1","0","0"],
    ...     ["0","0","0","1","1"]
    ... ]
    >>> num_islands_union_find(grid)
    3
    """
    if not grid or not grid[0]:
        return 0
    
    rows, cols = len(grid), len(grid[0])
    
    # Count land cells
    land_count = sum(1 for r in range(rows) for c in range(cols) if grid[r][c] == '1')
    
    if land_count == 0:
        return 0
    
    uf = UnionFind(rows * cols)
    uf.count = land_count  # Start with land_count components
    
    def index(r, c):
        return r * cols + c
    
    for r in range(rows):
        for c in range(cols):
            if grid[r][c] == '1':
                # Union with right and down neighbors (avoid double counting)
                if c + 1 < cols and grid[r][c + 1] == '1':
                    uf.union(index(r, c), index(r, c + 1))
                if r + 1 < rows and grid[r + 1][c] == '1':
                    uf.union(index(r, c), index(r + 1, c))
    
    return uf.count
```

---

## 10. Interview Tips

### What Interviewers Look For

1. **Know when to use it**: Dynamic connectivity, grouping, cycle detection
2. **Implement correctly**: Path compression + union by rank
3. **Analyze complexity**: Explain why O(α(n)) ≈ O(1)
4. **Handle edge cases**: Empty input, single element, already connected

### Common Follow-up Questions

```
Q: Why is the complexity nearly O(1)?
A: With path compression and union by rank, each operation is O(α(n)) 
   where α is inverse Ackermann function, which is ≤ 5 for any practical n.

Q: Can Union-Find detect cycles?
A: Yes! When union(x, y) is called and x, y are already in same set,
   adding edge (x, y) would create a cycle.

Q: Can Union-Find find the path between two nodes?
A: No, it only tracks set membership, not the actual connections.
   Use BFS/DFS for path finding.

Q: How to track component sizes?
A: Use union by size variant and maintain size array.
```

### Quick Pattern Recognition

```
See "connected components"? → Union-Find or BFS/DFS
See "dynamic connectivity" (add edges)? → Union-Find
See "grouping items by some relation"? → Union-Find
See "cycle in undirected graph"? → Union-Find
See "Kruskal's MST"? → Union-Find
See "equivalence classes"? → Union-Find
```

---

## 11. Related Patterns

| Pattern | Description | Example |
|---------|-------------|---------|
| Connected Components | Group connected nodes | Number of Islands |
| Cycle Detection | Find if adding edge creates cycle | Redundant Connection |
| Dynamic Connectivity | Answer connectivity queries after updates | Graph Valid Tree |
| MST | Build minimum spanning tree | Kruskal's Algorithm |
| Equivalence | Group by transitive relation | Accounts Merge |

---

## 12. References

- **CLRS**: Chapter 21 (Data Structures for Disjoint Sets)
- **Sedgewick**: Algorithms 4th Ed., Chapter 1.5
- **cp-algorithms**: [DSU](https://cp-algorithms.com/data_structures/disjoint_set_union.html)
- **LeetCode**: Union Find tag (40+ problems)

---

## 13. Practice Problems

### Easy
| # | Problem | Key Concepts | LeetCode |
|---|---------|--------------|----------|
| 1 | Find if Path Exists in Graph | Basic connectivity | [1971](https://leetcode.com/problems/find-if-path-exists-in-graph/) |
| 2 | Number of Provinces | Connected components | [547](https://leetcode.com/problems/number-of-provinces/) |

### Medium
| # | Problem | Key Concepts | LeetCode |
|---|---------|--------------|----------|
| 3 | Number of Connected Components | Count components | [323](https://leetcode.com/problems/number-of-connected-components-in-an-undirected-graph/) |
| 4 | Redundant Connection | Cycle detection | [684](https://leetcode.com/problems/redundant-connection/) |
| 5 | Graph Valid Tree | Tree = n-1 edges, connected | [261](https://leetcode.com/problems/graph-valid-tree/) |
| 6 | Number of Islands II | Dynamic island counting | [305](https://leetcode.com/problems/number-of-islands-ii/) |
| 7 | Accounts Merge | Group by common email | [721](https://leetcode.com/problems/accounts-merge/) |
| 8 | Longest Consecutive Sequence | Union consecutive numbers | [128](https://leetcode.com/problems/longest-consecutive-sequence/) |
| 9 | Satisfiability of Equality Equations | Parse and union | [990](https://leetcode.com/problems/satisfiability-of-equality-equations/) |
| 10 | Most Stones Removed | Same row/col = connected | [947](https://leetcode.com/problems/most-stones-removed-with-same-row-or-column/) |
| 11 | Smallest String With Swaps | Group and sort | [1202](https://leetcode.com/problems/smallest-string-with-swaps/) |
| 12 | Regions Cut By Slashes | Expand and union | [959](https://leetcode.com/problems/regions-cut-by-slashes/) |
| 13 | Evaluate Division | Weighted union-find | [399](https://leetcode.com/problems/evaluate-division/) |
| 14 | Lexicographically Smallest Equivalent String | Union characters | [1061](https://leetcode.com/problems/lexicographically-smallest-equivalent-string/) |
| 15 | Minimize Malware Spread | Component analysis | [924](https://leetcode.com/problems/minimize-malware-spread/) |

### Hard
| # | Problem | Key Concepts | LeetCode |
|---|---------|--------------|----------|
| 16 | Redundant Connection II | Directed graph cycle | [685](https://leetcode.com/problems/redundant-connection-ii/) |
| 17 | Swim in Rising Water | Binary search + UF | [778](https://leetcode.com/problems/swim-in-rising-water/) |
| 18 | Checking Existence of Edge Length Limited Paths | Offline queries | [1697](https://leetcode.com/problems/checking-existence-of-edge-length-limited-paths/) |
| 19 | Making A Large Island | Precompute components | [827](https://leetcode.com/problems/making-a-large-island/) |
| 20 | Number of Good Paths | Union with values | [2421](https://leetcode.com/problems/number-of-good-paths/) |

---

## 14. Key Takeaways

1. **Near O(1) operations**: With path compression + union by rank, operations are O(α(n)) ≈ O(1)

2. **Perfect for dynamic connectivity**: When edges are added and you need to query connectivity

3. **Simpler than BFS/DFS for grouping problems**: Often cleaner code, better complexity

4. **Two key optimizations**:
   - Path compression: flatten trees during find
   - Union by rank/size: attach smaller tree to larger

5. **Cannot find paths or remove edges**: Only tracks set membership

6. **Common applications**:
   - Connected components
   - Cycle detection
   - Kruskal's MST
   - Equivalence classes

---

## 15. Spaced Repetition Prompts

1. **Q**: What are the two key optimizations in Union-Find?
   **A**: Path compression (make all nodes on find path point directly to root) and Union by rank/size (attach smaller tree under larger one).

2. **Q**: What is the time complexity of optimized Union-Find operations?
   **A**: O(α(n)) where α is inverse Ackermann function, which is ≤ 5 for all practical n. Essentially O(1).

3. **Q**: How do you detect a cycle in an undirected graph using Union-Find?
   **A**: When you try to union two nodes that are already in the same component (find(x) == find(y) before union), adding that edge would create a cycle.

4. **Q**: When would you use Union-Find vs BFS/DFS?
   **A**: Union-Find: dynamic connectivity, adding edges, only need connectivity (not path). BFS/DFS: need actual path, static graph, need to process graph structure.

5. **Q**: How do you implement path compression?
   **A**: In find(x), recursively find root, then set parent[x] = root. This flattens the tree so future finds are O(1).
