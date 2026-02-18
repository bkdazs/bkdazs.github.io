---
title: Graph Representations
topic: Graph Representations
difficulty: Medium
tags: [graph, adjacency-list, adjacency-matrix, edge-list, representation]
status: complete
weight: 17
---

# Graph Representations

## 1. Summary / TL;DR

- **Three main representations**: Adjacency List, Adjacency Matrix, Edge List
- **Adjacency List** is most common: O(V + E) space, best for sparse graphs
- **Adjacency Matrix** is O(V²) space but O(1) edge lookup, best for dense graphs
- Choice affects algorithm performance: BFS/DFS, shortest paths, etc.
- Understand conversions between representations
- Handle **weighted**, **directed/undirected**, and **multigraph** variants

---

## 2. When & Where to Use

| Representation | Best For | Space | Edge Check | Iterate Neighbors |
|---------------|----------|-------|------------|-------------------|
| Adjacency List | Sparse graphs, most algorithms | O(V + E) | O(degree) | O(degree) |
| Adjacency Matrix | Dense graphs, O(1) edge queries | O(V²) | O(1) | O(V) |
| Edge List | Kruskal's MST, simple storage | O(E) | O(E) | O(E) |

### Decision Guide

```
Is graph dense (E ≈ V²)?
├── Yes → Adjacency Matrix
└── No → Adjacency List (default choice)

Need O(1) edge existence check?
├── Yes → Adjacency Matrix or Adjacency Set
└── No → Adjacency List

Graph is input as edge list?
├── Need to process edges in order → Keep Edge List
└── Need graph traversal → Convert to Adjacency List
```

---

## 3. Time & Space Complexity

### Space Complexity

| Representation | Space |
|---------------|-------|
| Adjacency List | O(V + E) |
| Adjacency Matrix | O(V²) |
| Edge List | O(E) |

### Operation Complexity

| Operation | Adj List | Adj Matrix | Edge List |
|-----------|----------|------------|-----------|
| Add vertex | O(1) | O(V²)* | O(1) |
| Add edge | O(1) | O(1) | O(1) |
| Remove edge | O(E) | O(1) | O(E) |
| Check edge exists | O(degree) | O(1) | O(E) |
| Get all neighbors | O(degree) | O(V) | O(E) |
| DFS/BFS | O(V + E) | O(V²) | O(V * E) |

*Requires creating new V×V matrix

---

## 4. Core Concepts & Theory

### Graph Types

```
Undirected Graph:       Directed Graph (Digraph):
    0 --- 1                 0 --→ 1
    |     |                 ↑     |
    |     |                 |     ↓
    3 --- 2                 3 ←-- 2

Weighted Graph:         Multigraph:
    0 -5- 1                 0 === 1  (multiple edges)
    |     |                 |     |
   2|    3|                 |     |
    |     |                 3 --- 2
    3 -1- 2
```

### Adjacency List

Each vertex stores a list of its neighbors.

```
Graph:        Adjacency List:
  0 --- 1     0: [1, 3]
  |     |     1: [0, 2]
  |     |     2: [1, 3]
  3 --- 2     3: [0, 2]
```

### Adjacency Matrix

V×V matrix where M[i][j] = 1 if edge (i,j) exists.

```
Graph:        Adjacency Matrix:
  0 --- 1       0  1  2  3
  |     |     0 [0, 1, 0, 1]
  |     |     1 [1, 0, 1, 0]
  3 --- 2     2 [0, 1, 0, 1]
              3 [1, 0, 1, 0]
```

### Edge List

List of (u, v) or (u, v, weight) tuples.

```
Graph:        Edge List:
  0 --- 1     [(0, 1), (0, 3), (1, 2), (2, 3)]
  |     |     
  |     |     Weighted:
  3 --- 2     [(0, 1, 5), (0, 3, 2), (1, 2, 3), (2, 3, 1)]
```

---

## 5. Diagrams / Visualizations

### Comparison of Representations

```
Example Graph (Directed, Weighted):
    
    0 --5-→ 1
    |       |
   2↓       ↓3
    |       |
    3 ←-1-- 2

═══════════════════════════════════════════════════════

Adjacency List:
  0: [(1, 5), (3, 2)]     # (neighbor, weight)
  1: [(2, 3)]
  2: [(3, 1)]
  3: []

═══════════════════════════════════════════════════════

Adjacency Matrix (with weights, 0 = no edge):
      0   1   2   3
  0 [ 0   5   0   2 ]
  1 [ 0   0   3   0 ]
  2 [ 0   0   0   1 ]
  3 [ 0   0   0   0 ]

═══════════════════════════════════════════════════════

Edge List:
  [(0, 1, 5), (0, 3, 2), (1, 2, 3), (2, 3, 1)]
```

### Memory Layout

```
Adjacency List (Sparse graph: V=4, E=4):
Memory ≈ 4 vertex arrays + 8 edge entries = O(V + E)

[0] → [1, 3]
[1] → [0, 2]
[2] → [1, 3]
[3] → [0, 2]

Adjacency Matrix (Same graph):
Memory = 4 × 4 = 16 entries = O(V²)

[0, 1, 0, 1]
[1, 0, 1, 0]
[0, 1, 0, 1]
[1, 0, 1, 0]

For sparse graphs, adjacency list is much more efficient!
```

---

## 6. Implementation (Python)

### Adjacency List (Default Choice)

```python
from typing import List, Dict, Set, Tuple, Optional
from collections import defaultdict, deque

class GraphAdjList:
    """
    Graph using adjacency list representation.
    
    Best for: Most graph algorithms, sparse graphs
    Space: O(V + E)
    
    >>> g = GraphAdjList()
    >>> g.add_edge(0, 1)
    >>> g.add_edge(0, 2)
    >>> g.add_edge(1, 2)
    >>> g.neighbors(0)
    [1, 2]
    """
    
    def __init__(self, directed: bool = False):
        self.adj: Dict[int, List[int]] = defaultdict(list)
        self.directed = directed
    
    def add_vertex(self, v: int) -> None:
        """Add vertex (useful for isolated vertices)."""
        if v not in self.adj:
            self.adj[v] = []
    
    def add_edge(self, u: int, v: int) -> None:
        """Add edge from u to v."""
        self.adj[u].append(v)
        if not self.directed:
            self.adj[v].append(u)
    
    def remove_edge(self, u: int, v: int) -> None:
        """Remove edge from u to v."""
        if v in self.adj[u]:
            self.adj[u].remove(v)
        if not self.directed and u in self.adj[v]:
            self.adj[v].remove(u)
    
    def has_edge(self, u: int, v: int) -> bool:
        """Check if edge exists. O(degree of u)."""
        return v in self.adj[u]
    
    def neighbors(self, v: int) -> List[int]:
        """Get all neighbors of vertex v."""
        return self.adj[v]
    
    def vertices(self) -> List[int]:
        """Get all vertices."""
        return list(self.adj.keys())
    
    def num_vertices(self) -> int:
        return len(self.adj)
    
    def num_edges(self) -> int:
        total = sum(len(neighbors) for neighbors in self.adj.values())
        return total if self.directed else total // 2
    
    def degree(self, v: int) -> int:
        """Get degree of vertex (out-degree for directed)."""
        return len(self.adj[v])
```

### Adjacency List with Set (O(1) Edge Check)

```python
class GraphAdjSet:
    """
    Graph using adjacency set for O(1) edge existence check.
    
    Trade-off: Slightly more memory, but O(1) has_edge.
    """
    
    def __init__(self, directed: bool = False):
        self.adj: Dict[int, Set[int]] = defaultdict(set)
        self.directed = directed
    
    def add_edge(self, u: int, v: int) -> None:
        self.adj[u].add(v)
        if not self.directed:
            self.adj[v].add(u)
    
    def has_edge(self, u: int, v: int) -> bool:
        """O(1) edge check."""
        return v in self.adj[u]
    
    def remove_edge(self, u: int, v: int) -> None:
        self.adj[u].discard(v)
        if not self.directed:
            self.adj[v].discard(u)
    
    def neighbors(self, v: int) -> Set[int]:
        return self.adj[v]
```

### Weighted Adjacency List

```python
class WeightedGraph:
    """
    Weighted graph using adjacency list.
    
    Each neighbor stored as (neighbor, weight) tuple.
    
    >>> g = WeightedGraph()
    >>> g.add_edge(0, 1, 5)
    >>> g.add_edge(0, 2, 3)
    >>> g.neighbors(0)
    [(1, 5), (2, 3)]
    """
    
    def __init__(self, directed: bool = False):
        self.adj: Dict[int, List[Tuple[int, int]]] = defaultdict(list)
        self.directed = directed
    
    def add_edge(self, u: int, v: int, weight: int) -> None:
        """Add weighted edge."""
        self.adj[u].append((v, weight))
        if not self.directed:
            self.adj[v].append((u, weight))
    
    def neighbors(self, v: int) -> List[Tuple[int, int]]:
        """Return list of (neighbor, weight) tuples."""
        return self.adj[v]
    
    def get_weight(self, u: int, v: int) -> Optional[int]:
        """Get weight of edge (u, v)."""
        for neighbor, weight in self.adj[u]:
            if neighbor == v:
                return weight
        return None
```

### Adjacency Matrix

```python
class GraphAdjMatrix:
    """
    Graph using adjacency matrix representation.
    
    Best for: Dense graphs, O(1) edge queries
    Space: O(V²)
    
    >>> g = GraphAdjMatrix(4)
    >>> g.add_edge(0, 1)
    >>> g.add_edge(0, 2)
    >>> g.has_edge(0, 1)
    True
    >>> g.has_edge(0, 3)
    False
    """
    
    def __init__(self, num_vertices: int, directed: bool = False):
        self.n = num_vertices
        self.directed = directed
        self.matrix = [[0] * num_vertices for _ in range(num_vertices)]
    
    def add_edge(self, u: int, v: int, weight: int = 1) -> None:
        """Add edge (with optional weight)."""
        self.matrix[u][v] = weight
        if not self.directed:
            self.matrix[v][u] = weight
    
    def remove_edge(self, u: int, v: int) -> None:
        """Remove edge."""
        self.matrix[u][v] = 0
        if not self.directed:
            self.matrix[v][u] = 0
    
    def has_edge(self, u: int, v: int) -> bool:
        """O(1) edge check."""
        return self.matrix[u][v] != 0
    
    def get_weight(self, u: int, v: int) -> int:
        """Get edge weight (0 if no edge)."""
        return self.matrix[u][v]
    
    def neighbors(self, v: int) -> List[int]:
        """Get all neighbors. O(V)."""
        return [i for i in range(self.n) if self.matrix[v][i] != 0]
    
    def degree(self, v: int) -> int:
        return sum(1 for i in range(self.n) if self.matrix[v][i] != 0)
```

### Building Graph from Common Input Formats

```python
def build_from_edge_list(n: int, edges: List[List[int]], 
                         directed: bool = False) -> Dict[int, List[int]]:
    """
    Build adjacency list from edge list.
    Common LeetCode input format.
    
    >>> edges = [[0,1], [0,2], [1,2]]
    >>> g = build_from_edge_list(3, edges)
    >>> sorted(g[0])
    [1, 2]
    """
    graph = defaultdict(list)
    
    # Initialize all vertices (important for isolated nodes)
    for i in range(n):
        graph[i]
    
    for edge in edges:
        u, v = edge[0], edge[1]
        graph[u].append(v)
        if not directed:
            graph[v].append(u)
    
    return graph


def build_weighted_from_edge_list(n: int, edges: List[List[int]], 
                                  directed: bool = False) -> Dict[int, List[Tuple[int, int]]]:
    """
    Build weighted adjacency list from edge list.
    
    >>> edges = [[0, 1, 5], [0, 2, 3], [1, 2, 1]]
    >>> g = build_weighted_from_edge_list(3, edges)
    >>> g[0]
    [(1, 5), (2, 3)]
    """
    graph = defaultdict(list)
    
    for i in range(n):
        graph[i]
    
    for edge in edges:
        u, v, w = edge[0], edge[1], edge[2]
        graph[u].append((v, w))
        if not directed:
            graph[v].append((u, w))
    
    return graph


def build_from_adjacency_matrix(matrix: List[List[int]]) -> Dict[int, List[int]]:
    """
    Convert adjacency matrix to adjacency list.
    
    >>> matrix = [[0, 1, 1], [1, 0, 1], [1, 1, 0]]
    >>> g = build_from_adjacency_matrix(matrix)
    >>> sorted(g[0])
    [1, 2]
    """
    n = len(matrix)
    graph = defaultdict(list)
    
    for i in range(n):
        for j in range(n):
            if matrix[i][j] != 0:
                graph[i].append(j)
    
    return graph


def to_adjacency_matrix(graph: Dict[int, List[int]], n: int) -> List[List[int]]:
    """
    Convert adjacency list to adjacency matrix.
    """
    matrix = [[0] * n for _ in range(n)]
    
    for u in graph:
        for v in graph[u]:
            matrix[u][v] = 1
    
    return matrix
```

### Implicit Graph (Grid as Graph)

```python
def grid_neighbors(grid: List[List[int]], r: int, c: int, 
                   directions: int = 4) -> List[Tuple[int, int]]:
    """
    Get valid neighbors in a grid.
    Treats grid as implicit graph without building adjacency list.
    
    >>> grid = [[1, 0], [1, 1]]
    >>> grid_neighbors(grid, 0, 0)
    [(1, 0)]
    """
    rows, cols = len(grid), len(grid[0])
    
    if directions == 4:
        deltas = [(0, 1), (0, -1), (1, 0), (-1, 0)]
    else:  # 8 directions
        deltas = [(0, 1), (0, -1), (1, 0), (-1, 0),
                  (1, 1), (1, -1), (-1, 1), (-1, -1)]
    
    neighbors = []
    for dr, dc in deltas:
        nr, nc = r + dr, c + dc
        if 0 <= nr < rows and 0 <= nc < cols and grid[nr][nc] != 0:
            neighbors.append((nr, nc))
    
    return neighbors


def bfs_on_grid(grid: List[List[int]], start: Tuple[int, int]) -> List[Tuple[int, int]]:
    """
    BFS on grid without explicit graph construction.
    
    >>> grid = [[1, 1, 1], [0, 1, 0], [1, 1, 1]]
    >>> visited = bfs_on_grid(grid, (0, 0))
    >>> (2, 2) in visited
    True
    """
    rows, cols = len(grid), len(grid[0])
    visited = set()
    queue = deque([start])
    visited.add(start)
    
    while queue:
        r, c = queue.popleft()
        
        for dr, dc in [(0, 1), (0, -1), (1, 0), (-1, 0)]:
            nr, nc = r + dr, c + dc
            if (0 <= nr < rows and 0 <= nc < cols and 
                grid[nr][nc] != 0 and (nr, nc) not in visited):
                visited.add((nr, nc))
                queue.append((nr, nc))
    
    return list(visited)
```

---

## 7. Step-by-Step Worked Example

### Problem: Clone Graph (LeetCode 133)

Build a deep copy of a graph given the reference to a node.

```python
class Node:
    def __init__(self, val=0, neighbors=None):
        self.val = val
        self.neighbors = neighbors if neighbors else []

def clone_graph(node: Optional[Node]) -> Optional[Node]:
    """
    Deep copy a graph using BFS.
    
    Approach:
    1. Use hashmap to track original → clone mapping
    2. BFS to visit all nodes
    3. For each node, create clone and connect to cloned neighbors
    
    Time: O(V + E)
    Space: O(V)
    """
    if not node:
        return None
    
    # Map original node to its clone
    clones = {node: Node(node.val)}
    queue = deque([node])
    
    while queue:
        original = queue.popleft()
        clone = clones[original]
        
        for neighbor in original.neighbors:
            if neighbor not in clones:
                # Create clone for unvisited neighbor
                clones[neighbor] = Node(neighbor.val)
                queue.append(neighbor)
            
            # Connect clone to cloned neighbor
            clone.neighbors.append(clones[neighbor])
    
    return clones[node]
```

**Trace through example**:
```
Original Graph:
1 --- 2
|     |
4 --- 3

Step 1: Start with node 1
  clones = {1: Clone(1)}
  queue = [1]

Step 2: Process node 1
  original = 1, clone = Clone(1)
  neighbors of 1: [2, 4]
  
  2 not in clones → create Clone(2), add to queue
  4 not in clones → create Clone(4), add to queue
  
  Clone(1).neighbors = [Clone(2), Clone(4)]
  clones = {1: Clone(1), 2: Clone(2), 4: Clone(4)}
  queue = [2, 4]

Step 3: Process node 2
  neighbors of 2: [1, 3]
  
  1 in clones → just connect
  3 not in clones → create Clone(3), add to queue
  
  Clone(2).neighbors = [Clone(1), Clone(3)]
  queue = [4, 3]

Step 4: Process node 4
  neighbors of 4: [1, 3]
  
  1 in clones → just connect
  3 in clones → just connect
  
  Clone(4).neighbors = [Clone(1), Clone(3)]
  queue = [3]

Step 5: Process node 3
  neighbors of 3: [2, 4]
  
  Both in clones → just connect
  
  Clone(3).neighbors = [Clone(2), Clone(4)]
  queue = []

Done! Return Clone(1)
```

---

## 8. Common Mistakes

1. **Forgetting to handle directed vs undirected**
   ```python
   # Undirected: add both directions
   graph[u].append(v)
   graph[v].append(u)  # Don't forget!
   
   # Directed: add only one direction
   graph[u].append(v)
   ```

2. **Not initializing all vertices**
   ```python
   # Wrong: isolated vertices might be missing
   for u, v in edges:
       graph[u].append(v)
   
   # Correct: initialize all vertices first
   for i in range(n):
       graph[i] = []
   for u, v in edges:
       graph[u].append(v)
   ```

3. **Index errors with 0-indexed vs 1-indexed**
   ```python
   # If input is 1-indexed nodes [1..n]
   # Either: create graph with n+1 size
   graph = [[] for _ in range(n + 1)]
   
   # Or: subtract 1 from all node indices
   graph[u - 1].append(v - 1)
   ```

4. **Modifying graph during iteration**
   ```python
   # Wrong: modifying while iterating
   for neighbor in graph[node]:
       if condition:
           graph[node].remove(neighbor)  # Danger!
   
   # Correct: iterate over copy or collect to remove
   to_remove = [n for n in graph[node] if condition]
   for n in to_remove:
       graph[node].remove(n)
   ```

5. **Using wrong complexity algorithm for representation**
   ```python
   # Wrong: O(V) neighbor iteration on matrix for sparse graph
   for v in range(n):  # O(V) even if few neighbors
       if matrix[u][v]:
           process(v)
   
   # Better: use adjacency list for sparse graphs
   for v in adj_list[u]:  # O(degree) 
       process(v)
   ```

---

## 9. Variations & Optimizations

### Compressed Sparse Row (CSR) Format

```python
class GraphCSR:
    """
    Compressed Sparse Row format for memory-efficient graph storage.
    
    Used in scientific computing and large-scale graphs.
    Even more compact than adjacency list for large graphs.
    
    For graph:
    0 -> [1, 2]
    1 -> [2]
    2 -> [0, 3]
    3 -> [3]
    
    row_ptr = [0, 2, 3, 5, 6]  # Start index in col_idx for each vertex
    col_idx = [1, 2, 2, 0, 3, 3]  # All edges concatenated
    """
    
    def __init__(self, n: int, edges: List[Tuple[int, int]]):
        # Count degrees
        degrees = [0] * n
        for u, v in edges:
            degrees[u] += 1
        
        # Build row_ptr
        self.row_ptr = [0] * (n + 1)
        for i in range(n):
            self.row_ptr[i + 1] = self.row_ptr[i] + degrees[i]
        
        # Build col_idx
        self.col_idx = [0] * len(edges)
        current = [0] * n
        for u, v in edges:
            idx = self.row_ptr[u] + current[u]
            self.col_idx[idx] = v
            current[u] += 1
    
    def neighbors(self, v: int) -> List[int]:
        """Get neighbors of vertex v."""
        start = self.row_ptr[v]
        end = self.row_ptr[v + 1]
        return self.col_idx[start:end]
```

### Incidence Matrix

```python
def build_incidence_matrix(n: int, edges: List[Tuple[int, int]]) -> List[List[int]]:
    """
    Build incidence matrix: V × E matrix.
    M[v][e] = 1 if vertex v is incident to edge e.
    
    Used in network flow algorithms.
    
    >>> edges = [(0, 1), (0, 2), (1, 2)]
    >>> matrix = build_incidence_matrix(3, edges)
    >>> matrix[0]  # Vertex 0 is in edges 0 and 1
    [1, 1, 0]
    """
    m = len(edges)
    matrix = [[0] * m for _ in range(n)]
    
    for e_idx, (u, v) in enumerate(edges):
        matrix[u][e_idx] = 1
        matrix[v][e_idx] = 1  # -1 for directed out
    
    return matrix
```

### Graph with Labels/Attributes

```python
from dataclasses import dataclass

@dataclass
class Edge:
    target: int
    weight: int = 1
    label: str = ""

@dataclass
class Vertex:
    id: int
    label: str = ""
    attributes: dict = None

class AttributedGraph:
    """
    Graph with vertex and edge attributes.
    Useful for modeling real-world networks.
    """
    
    def __init__(self):
        self.vertices: Dict[int, Vertex] = {}
        self.edges: Dict[int, List[Edge]] = defaultdict(list)
    
    def add_vertex(self, id: int, label: str = "", **attrs) -> None:
        self.vertices[id] = Vertex(id, label, attrs)
    
    def add_edge(self, u: int, v: int, weight: int = 1, label: str = "") -> None:
        self.edges[u].append(Edge(v, weight, label))
    
    def get_vertex_attr(self, v: int, attr: str):
        return self.vertices[v].attributes.get(attr)
```

---

## 10. Interview Tips

### What Interviewers Look For

1. **Know the trade-offs**: When to use list vs matrix
2. **Handle edge cases**: Empty graph, disconnected components, self-loops
3. **Convert between formats**: Often given edge list, need adjacency list
4. **Implicit graphs**: Recognize when grid = graph

### Common Patterns

```python
# Pattern 1: Build graph from edges
graph = defaultdict(list)
for u, v in edges:
    graph[u].append(v)
    graph[v].append(u)  # If undirected

# Pattern 2: BFS/DFS traversal
visited = set()
def dfs(node):
    visited.add(node)
    for neighbor in graph[node]:
        if neighbor not in visited:
            dfs(neighbor)

# Pattern 3: Grid as implicit graph
for dr, dc in [(0,1), (0,-1), (1,0), (-1,0)]:
    nr, nc = r + dr, c + dc
    if 0 <= nr < rows and 0 <= nc < cols:
        # process neighbor

# Pattern 4: Count connected components
count = 0
for node in range(n):
    if node not in visited:
        dfs(node)
        count += 1
```

### Quick Decision Guide

```
Given edge list → Convert to adjacency list (default)
Need O(1) edge check → Adjacency matrix or adjacency set
Dense graph (E ≈ V²) → Adjacency matrix might be okay
Grid problem → Use implicit graph (no conversion needed)
Weighted graph → Store (neighbor, weight) tuples
```

---

## 11. Related Patterns

| Pattern | Description | Example |
|---------|-------------|---------|
| BFS/DFS | Graph traversal | Connected components |
| Topological Sort | DAG ordering | Course scheduling |
| Shortest Path | Path finding | Dijkstra, BFS |
| Union-Find | Connectivity | Connected components |
| Bipartite Check | 2-coloring | BFS with colors |

---

## 12. References

- **CLRS**: Chapter 22 (Elementary Graph Algorithms)
- **Sedgewick**: Algorithms 4th Ed., Chapter 4
- **Visualization**: [VisuAlgo Graph](https://visualgo.net/en/graphds)

---

## 13. Practice Problems

### Easy
| # | Problem | Key Concepts | LeetCode |
|---|---------|--------------|----------|
| 1 | Find Center of Star Graph | Degree analysis | [1791](https://leetcode.com/problems/find-center-of-star-graph/) |
| 2 | Find if Path Exists | BFS/DFS basics | [1971](https://leetcode.com/problems/find-if-path-exists-in-graph/) |

### Medium
| # | Problem | Key Concepts | LeetCode |
|---|---------|--------------|----------|
| 3 | Clone Graph | BFS + hashmap | [133](https://leetcode.com/problems/clone-graph/) |
| 4 | Number of Islands | Grid as graph | [200](https://leetcode.com/problems/number-of-islands/) |
| 5 | Course Schedule | Directed graph, cycle | [207](https://leetcode.com/problems/course-schedule/) |
| 6 | Graph Valid Tree | Connected + no cycle | [261](https://leetcode.com/problems/graph-valid-tree/) |
| 7 | Number of Connected Components | Union-Find or DFS | [323](https://leetcode.com/problems/number-of-connected-components-in-an-undirected-graph/) |
| 8 | Minimum Height Trees | Graph center | [310](https://leetcode.com/problems/minimum-height-trees/) |
| 9 | Is Graph Bipartite | 2-coloring | [785](https://leetcode.com/problems/is-graph-bipartite/) |
| 10 | Find Eventual Safe States | Reverse graph | [802](https://leetcode.com/problems/find-eventual-safe-states/) |

### Hard
| # | Problem | Key Concepts | LeetCode |
|---|---------|--------------|----------|
| 11 | Word Ladder II | BFS + path tracking | [126](https://leetcode.com/problems/word-ladder-ii/) |
| 12 | Reconstruct Itinerary | Eulerian path | [332](https://leetcode.com/problems/reconstruct-itinerary/) |
| 13 | Alien Dictionary | Topological sort | [269](https://leetcode.com/problems/alien-dictionary/) |

---

## 14. Key Takeaways

1. **Adjacency List is default choice**: O(V + E) space, efficient for sparse graphs

2. **Know the trade-offs**:
   - List: Better space, slower edge check
   - Matrix: O(V²) space, O(1) edge check

3. **Build graph correctly**: Handle directed/undirected, initialize all vertices

4. **Grid = implicit graph**: Often don't need explicit construction

5. **Common input format**: Edge list → convert to adjacency list

---

## 15. Spaced Repetition Prompts

1. **Q**: What is the space complexity of adjacency list vs adjacency matrix?
   **A**: List: O(V + E). Matrix: O(V²). List is better for sparse graphs.

2. **Q**: How do you build an undirected graph from an edge list?
   **A**: For each edge (u, v): graph[u].append(v) AND graph[v].append(u).

3. **Q**: When would you prefer adjacency matrix over adjacency list?
   **A**: When you need O(1) edge existence checks, or for dense graphs where E ≈ V².

4. **Q**: How do you represent a weighted graph with adjacency list?
   **A**: Store (neighbor, weight) tuples: graph[u].append((v, weight)).

5. **Q**: How do you treat a grid as a graph for BFS/DFS?
   **A**: Each cell is a vertex. Neighbors are adjacent cells (4 or 8 directions). No explicit graph construction needed.
