---
title: "Graph Traversal - BFS and DFS"
topic: "Algorithms/Graphs"
difficulty: "Medium"
tags: ["graphs", "bfs", "dfs", "traversal", "connected-components"]
status: "complete"
weight: 1
---

# Graph Traversal - BFS and DFS

## 📚 Summary

**Breadth-First Search (BFS)** and **Depth-First Search (DFS)** are fundamental graph traversal algorithms. BFS explores neighbors level by level (using a queue), while DFS explores as deep as possible before backtracking (using recursion or stack).

---

## ⏱️ Complexity Analysis

| Algorithm | Time | Space |
|-----------|------|-------|
| BFS | O(V + E) | O(V) |
| DFS (Recursive) | O(V + E) | O(V) call stack |
| DFS (Iterative) | O(V + E) | O(V) |

Where V = number of vertices, E = number of edges.

---

## 🧠 Core Concepts

### BFS vs DFS Comparison

| Aspect | BFS | DFS |
|--------|-----|-----|
| Data Structure | Queue (FIFO) | Stack/Recursion (LIFO) |
| Path Found | Shortest (unweighted) | Any path |
| Memory | More (stores entire level) | Less (single path) |
| Complete? | Yes | Yes (finite graphs) |
| Use Case | Shortest path, level order | Cycle detection, topological sort |

### Graph Representations

```python
from typing import List, Dict, Set
from collections import defaultdict, deque

# Adjacency List (most common)
graph_adj_list: Dict[int, List[int]] = {
    0: [1, 2],
    1: [0, 3, 4],
    2: [0, 4],
    3: [1],
    4: [1, 2]
}

# Edge List
edges: List[tuple] = [(0, 1), (0, 2), (1, 3), (1, 4), (2, 4)]

# Adjacency Matrix (dense graphs)
adj_matrix = [
    [0, 1, 1, 0, 0],
    [1, 0, 0, 1, 1],
    [1, 0, 0, 0, 1],
    [0, 1, 0, 0, 0],
    [0, 1, 1, 0, 0]
]

def build_graph(edges: List[List[int]], directed: bool = False) -> Dict[int, List[int]]:
    """Build adjacency list from edge list."""
    graph = defaultdict(list)
    for u, v in edges:
        graph[u].append(v)
        if not directed:
            graph[v].append(u)
    return graph
```

---

## 💻 Implementation

### BFS Implementation

```python
def bfs(graph: Dict[int, List[int]], start: int) -> List[int]:
    """
    Breadth-First Search traversal.
    
    Time: O(V + E), Space: O(V)
    
    >>> graph = {0: [1, 2], 1: [0, 3], 2: [0, 3], 3: [1, 2]}
    >>> bfs(graph, 0)
    [0, 1, 2, 3]
    """
    visited = set([start])
    queue = deque([start])
    order = []
    
    while queue:
        node = queue.popleft()
        order.append(node)
        
        for neighbor in graph.get(node, []):
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append(neighbor)
    
    return order


def bfs_levels(graph: Dict[int, List[int]], start: int) -> List[List[int]]:
    """
    BFS returning nodes by level/distance.
    
    >>> graph = {0: [1, 2], 1: [3], 2: [3], 3: []}
    >>> bfs_levels(graph, 0)
    [[0], [1, 2], [3]]
    """
    visited = set([start])
    queue = deque([start])
    levels = []
    
    while queue:
        level_size = len(queue)
        current_level = []
        
        for _ in range(level_size):
            node = queue.popleft()
            current_level.append(node)
            
            for neighbor in graph.get(node, []):
                if neighbor not in visited:
                    visited.add(neighbor)
                    queue.append(neighbor)
        
        levels.append(current_level)
    
    return levels
```

### DFS Implementation

```python
def dfs_recursive(graph: Dict[int, List[int]], start: int) -> List[int]:
    """
    Depth-First Search using recursion.
    
    Time: O(V + E), Space: O(V) for call stack
    
    >>> graph = {0: [1, 2], 1: [0, 3], 2: [0, 3], 3: [1, 2]}
    >>> dfs_recursive(graph, 0)
    [0, 1, 3, 2]
    """
    visited = set()
    order = []
    
    def dfs(node: int) -> None:
        visited.add(node)
        order.append(node)
        
        for neighbor in graph.get(node, []):
            if neighbor not in visited:
                dfs(neighbor)
    
    dfs(start)
    return order


def dfs_iterative(graph: Dict[int, List[int]], start: int) -> List[int]:
    """
    Depth-First Search using explicit stack.
    
    Time: O(V + E), Space: O(V)
    
    >>> graph = {0: [1, 2], 1: [0, 3], 2: [0, 3], 3: [1, 2]}
    >>> dfs_iterative(graph, 0)
    [0, 2, 3, 1]
    """
    visited = set()
    stack = [start]
    order = []
    
    while stack:
        node = stack.pop()
        
        if node in visited:
            continue
            
        visited.add(node)
        order.append(node)
        
        # Add neighbors in reverse order to match recursive behavior
        for neighbor in reversed(graph.get(node, [])):
            if neighbor not in visited:
                stack.append(neighbor)
    
    return order
```

### Grid BFS/DFS (Common Pattern)

```python
def bfs_grid(grid: List[List[int]], start: tuple) -> int:
    """
    BFS on 2D grid. Returns distance to farthest reachable cell.
    
    Time: O(M*N), Space: O(M*N)
    """
    if not grid or not grid[0]:
        return 0
    
    rows, cols = len(grid), len(grid[0])
    directions = [(0, 1), (0, -1), (1, 0), (-1, 0)]
    
    visited = set([start])
    queue = deque([(start[0], start[1], 0)])  # (row, col, distance)
    max_dist = 0
    
    while queue:
        row, col, dist = queue.popleft()
        max_dist = max(max_dist, dist)
        
        for dr, dc in directions:
            nr, nc = row + dr, col + dc
            
            if (0 <= nr < rows and 0 <= nc < cols and 
                (nr, nc) not in visited and grid[nr][nc] != 0):
                visited.add((nr, nc))
                queue.append((nr, nc, dist + 1))
    
    return max_dist


def dfs_grid(grid: List[List[int]], row: int, col: int, visited: Set) -> int:
    """
    DFS on 2D grid. Returns count of connected cells.
    
    Time: O(M*N), Space: O(M*N)
    """
    if (row < 0 or row >= len(grid) or 
        col < 0 or col >= len(grid[0]) or
        (row, col) in visited or grid[row][col] == 0):
        return 0
    
    visited.add((row, col))
    count = 1
    
    for dr, dc in [(0, 1), (0, -1), (1, 0), (-1, 0)]:
        count += dfs_grid(grid, row + dr, col + dc, visited)
    
    return count
```

---

## 🔍 Worked Examples

### Example 1: Number of Islands

```python
def num_islands(grid: List[List[str]]) -> int:
    """
    Count number of islands ('1's connected 4-directionally).
    
    Time: O(M*N), Space: O(M*N)
    
    >>> grid = [
    ...     ["1","1","0","0","0"],
    ...     ["1","1","0","0","0"],
    ...     ["0","0","1","0","0"],
    ...     ["0","0","0","1","1"]
    ... ]
    >>> num_islands(grid)
    3
    """
    if not grid or not grid[0]:
        return 0
    
    rows, cols = len(grid), len(grid[0])
    islands = 0
    
    def dfs(r: int, c: int) -> None:
        if r < 0 or r >= rows or c < 0 or c >= cols or grid[r][c] != '1':
            return
        
        grid[r][c] = '0'  # Mark as visited
        dfs(r + 1, c)
        dfs(r - 1, c)
        dfs(r, c + 1)
        dfs(r, c - 1)
    
    for r in range(rows):
        for c in range(cols):
            if grid[r][c] == '1':
                islands += 1
                dfs(r, c)
    
    return islands

# Trace for grid:
# [1,1,0,0,0]
# [1,1,0,0,0]
# [0,0,1,0,0]
# [0,0,0,1,1]
#
# Start (0,0): Found '1', islands=1, DFS marks connected 1s as 0
# After DFS from (0,0): grid[0][0], [0][1], [1][0], [1][1] all become 0
# Continue scanning...
# (2,2): Found '1', islands=2, DFS marks it
# (3,3): Found '1', islands=3, DFS marks [3][3] and [3][4]
# Result: 3 islands
```

### Example 2: Shortest Path in Binary Matrix

```python
def shortest_path_binary_matrix(grid: List[List[int]]) -> int:
    """
    Find shortest path from top-left to bottom-right in binary matrix.
    Can move in 8 directions. 0 is passable, 1 is blocked.
    
    Time: O(N²), Space: O(N²)
    
    >>> grid = [[0,0,0],[1,1,0],[1,1,0]]
    >>> shortest_path_binary_matrix(grid)
    4
    """
    n = len(grid)
    if grid[0][0] == 1 or grid[n-1][n-1] == 1:
        return -1
    
    # 8 directions including diagonals
    directions = [(-1,-1), (-1,0), (-1,1), (0,-1), (0,1), (1,-1), (1,0), (1,1)]
    
    queue = deque([(0, 0, 1)])  # (row, col, path_length)
    grid[0][0] = 1  # Mark as visited
    
    while queue:
        row, col, length = queue.popleft()
        
        if row == n - 1 and col == n - 1:
            return length
        
        for dr, dc in directions:
            nr, nc = row + dr, col + dc
            
            if 0 <= nr < n and 0 <= nc < n and grid[nr][nc] == 0:
                grid[nr][nc] = 1  # Mark as visited
                queue.append((nr, nc, length + 1))
    
    return -1
```

### Example 3: Clone Graph

```python
class Node:
    def __init__(self, val=0, neighbors=None):
        self.val = val
        self.neighbors = neighbors if neighbors else []

def clone_graph(node: 'Node') -> 'Node':
    """
    Create a deep copy of a graph.
    
    Time: O(V + E), Space: O(V)
    """
    if not node:
        return None
    
    # Map from original node to cloned node
    cloned = {}
    
    def dfs(node: 'Node') -> 'Node':
        if node in cloned:
            return cloned[node]
        
        # Create clone
        clone = Node(node.val)
        cloned[node] = clone
        
        # Clone neighbors
        for neighbor in node.neighbors:
            clone.neighbors.append(dfs(neighbor))
        
        return clone
    
    return dfs(node)


def clone_graph_bfs(node: 'Node') -> 'Node':
    """BFS version of clone graph."""
    if not node:
        return None
    
    cloned = {node: Node(node.val)}
    queue = deque([node])
    
    while queue:
        curr = queue.popleft()
        
        for neighbor in curr.neighbors:
            if neighbor not in cloned:
                cloned[neighbor] = Node(neighbor.val)
                queue.append(neighbor)
            cloned[curr].neighbors.append(cloned[neighbor])
    
    return cloned[node]
```

### Example 4: Word Ladder (BFS Shortest Path)

```python
def ladder_length(begin_word: str, end_word: str, word_list: List[str]) -> int:
    """
    Find shortest transformation from begin_word to end_word.
    Each step changes one letter, intermediate words must be in word_list.
    
    Time: O(M² * N) where M = word length, N = word list size
    Space: O(M² * N)
    
    >>> ladder_length("hit", "cog", ["hot","dot","dog","lot","log","cog"])
    5
    """
    if end_word not in word_list:
        return 0
    
    word_set = set(word_list)
    queue = deque([(begin_word, 1)])
    visited = set([begin_word])
    
    while queue:
        word, length = queue.popleft()
        
        if word == end_word:
            return length
        
        # Try changing each position
        for i in range(len(word)):
            for c in 'abcdefghijklmnopqrstuvwxyz':
                next_word = word[:i] + c + word[i+1:]
                
                if next_word in word_set and next_word not in visited:
                    visited.add(next_word)
                    queue.append((next_word, length + 1))
    
    return 0

# Optimization: Bidirectional BFS
def ladder_length_bidirectional(begin_word: str, end_word: str, word_list: List[str]) -> int:
    """Bidirectional BFS for faster convergence."""
    if end_word not in word_list:
        return 0
    
    word_set = set(word_list)
    front = {begin_word}
    back = {end_word}
    visited = set()
    length = 1
    
    while front and back:
        # Always expand smaller set
        if len(front) > len(back):
            front, back = back, front
        
        next_front = set()
        for word in front:
            for i in range(len(word)):
                for c in 'abcdefghijklmnopqrstuvwxyz':
                    next_word = word[:i] + c + word[i+1:]
                    
                    if next_word in back:
                        return length + 1
                    
                    if next_word in word_set and next_word not in visited:
                        visited.add(next_word)
                        next_front.add(next_word)
        
        front = next_front
        length += 1
    
    return 0
```

---

## 🎯 Pattern Recognition

### Use BFS When:
- Finding **shortest path** in unweighted graphs
- Level-order traversal needed
- Finding nodes within K distance
- Multi-source BFS (start from multiple nodes)

### Use DFS When:
- Exploring all paths
- Detecting cycles
- Topological sorting
- Finding connected components
- Backtracking problems

---

## 🧩 Problem Variations

### Multi-Source BFS

```python
def walls_and_gates(rooms: List[List[int]]) -> None:
    """
    Fill each empty room with distance to nearest gate.
    INF = 2147483647, 0 = gate, -1 = wall.
    
    Time: O(M*N), Space: O(M*N)
    """
    if not rooms or not rooms[0]:
        return
    
    INF = 2147483647
    rows, cols = len(rooms), len(rooms[0])
    queue = deque()
    
    # Start BFS from all gates simultaneously
    for r in range(rows):
        for c in range(cols):
            if rooms[r][c] == 0:
                queue.append((r, c))
    
    while queue:
        r, c = queue.popleft()
        
        for dr, dc in [(0, 1), (0, -1), (1, 0), (-1, 0)]:
            nr, nc = r + dr, c + dc
            
            if (0 <= nr < rows and 0 <= nc < cols and 
                rooms[nr][nc] == INF):
                rooms[nr][nc] = rooms[r][c] + 1
                queue.append((nr, nc))
```

### DFS with Path Tracking

```python
def all_paths_source_target(graph: List[List[int]]) -> List[List[int]]:
    """
    Find all paths from node 0 to node n-1.
    
    Time: O(2^N * N), Space: O(N)
    """
    n = len(graph)
    result = []
    
    def dfs(node: int, path: List[int]) -> None:
        if node == n - 1:
            result.append(path[:])
            return
        
        for neighbor in graph[node]:
            path.append(neighbor)
            dfs(neighbor, path)
            path.pop()
    
    dfs(0, [0])
    return result
```

### Cycle Detection

```python
def has_cycle_undirected(graph: Dict[int, List[int]], n: int) -> bool:
    """
    Detect cycle in undirected graph using DFS.
    
    Time: O(V + E), Space: O(V)
    """
    visited = set()
    
    def dfs(node: int, parent: int) -> bool:
        visited.add(node)
        
        for neighbor in graph.get(node, []):
            if neighbor not in visited:
                if dfs(neighbor, node):
                    return True
            elif neighbor != parent:
                return True  # Back edge found
        
        return False
    
    for node in range(n):
        if node not in visited:
            if dfs(node, -1):
                return True
    
    return False


def has_cycle_directed(graph: Dict[int, List[int]], n: int) -> bool:
    """
    Detect cycle in directed graph using DFS with coloring.
    WHITE=0 (unvisited), GRAY=1 (in progress), BLACK=2 (done)
    
    Time: O(V + E), Space: O(V)
    """
    WHITE, GRAY, BLACK = 0, 1, 2
    color = [WHITE] * n
    
    def dfs(node: int) -> bool:
        color[node] = GRAY
        
        for neighbor in graph.get(node, []):
            if color[neighbor] == GRAY:
                return True  # Back edge (cycle)
            if color[neighbor] == WHITE and dfs(neighbor):
                return True
        
        color[node] = BLACK
        return False
    
    for node in range(n):
        if color[node] == WHITE:
            if dfs(node):
                return True
    
    return False
```

---

## ⚠️ Common Pitfalls

### 1. Forgetting to Mark Visited Before Enqueueing

```python
# WRONG - can add same node multiple times
queue.append(neighbor)
# ... later
visited.add(node)

# CORRECT - mark visited when adding to queue
if neighbor not in visited:
    visited.add(neighbor)
    queue.append(neighbor)
```

### 2. Grid Boundary Checks

```python
# WRONG - checking after access
grid[nr][nc]
if 0 <= nr < rows and 0 <= nc < cols:

# CORRECT - check bounds first
if 0 <= nr < rows and 0 <= nc < cols:
    grid[nr][nc]
```

### 3. DFS Stack Overflow

```python
# For very deep graphs, use iterative DFS
import sys
sys.setrecursionlimit(10**6)  # Or use iterative version
```

---

## 📚 Practice Problems

### Easy
| # | Problem | Key Concept |
|---|---------|-------------|
| 1 | [LC 733: Flood Fill](https://leetcode.com/problems/flood-fill/) | Grid DFS |
| 2 | [LC 463: Island Perimeter](https://leetcode.com/problems/island-perimeter/) | Grid traversal |
| 3 | [LC 997: Find Town Judge](https://leetcode.com/problems/find-the-town-judge/) | In/out degree |

### Medium
| # | Problem | Key Concept |
|---|---------|-------------|
| 4 | [LC 200: Number of Islands](https://leetcode.com/problems/number-of-islands/) | Connected components |
| 5 | [LC 695: Max Area of Island](https://leetcode.com/problems/max-area-of-island/) | DFS with counting |
| 6 | [LC 994: Rotting Oranges](https://leetcode.com/problems/rotting-oranges/) | Multi-source BFS |
| 7 | [LC 286: Walls and Gates](https://leetcode.com/problems/walls-and-gates/) | Multi-source BFS |
| 8 | [LC 130: Surrounded Regions](https://leetcode.com/problems/surrounded-regions/) | Boundary DFS |
| 9 | [LC 417: Pacific Atlantic Water Flow](https://leetcode.com/problems/pacific-atlantic-water-flow/) | Two-source DFS |
| 10 | [LC 542: 01 Matrix](https://leetcode.com/problems/01-matrix/) | Multi-source BFS |
| 11 | [LC 1091: Shortest Path in Binary Matrix](https://leetcode.com/problems/shortest-path-in-binary-matrix/) | BFS 8-directions |
| 12 | [LC 127: Word Ladder](https://leetcode.com/problems/word-ladder/) | BFS shortest path |
| 13 | [LC 133: Clone Graph](https://leetcode.com/problems/clone-graph/) | DFS/BFS with mapping |
| 14 | [LC 207: Course Schedule](https://leetcode.com/problems/course-schedule/) | Cycle detection |
| 15 | [LC 210: Course Schedule II](https://leetcode.com/problems/course-schedule-ii/) | Topological sort |
| 16 | [LC 797: All Paths Source Target](https://leetcode.com/problems/all-paths-from-source-to-target/) | DFS all paths |
| 17 | [LC 841: Keys and Rooms](https://leetcode.com/problems/keys-and-rooms/) | DFS reachability |
| 18 | [LC 1306: Jump Game III](https://leetcode.com/problems/jump-game-iii/) | BFS on array |

### Hard
| # | Problem | Key Concept |
|---|---------|-------------|
| 19 | [LC 126: Word Ladder II](https://leetcode.com/problems/word-ladder-ii/) | BFS + DFS backtrack |
| 20 | [LC 329: Longest Increasing Path](https://leetcode.com/problems/longest-increasing-path-in-a-matrix/) | DFS + memoization |
| 21 | [LC 785: Is Graph Bipartite?](https://leetcode.com/problems/is-graph-bipartite/) | BFS/DFS coloring |
| 22 | [LC 827: Making A Large Island](https://leetcode.com/problems/making-a-large-island/) | DFS + union |
| 23 | [LC 934: Shortest Bridge](https://leetcode.com/problems/shortest-bridge/) | DFS + BFS |
| 24 | [LC 1192: Critical Connections](https://leetcode.com/problems/critical-connections-in-a-network/) | Tarjan's bridges |
| 25 | [LC 1162: As Far from Land as Possible](https://leetcode.com/problems/as-far-from-land-as-possible/) | Multi-source BFS |

---

## 🔑 Key Takeaways

1. **BFS guarantees shortest path** in unweighted graphs
2. **DFS is simpler** but doesn't guarantee shortest path
3. **Mark visited early** (when adding to queue/stack) to avoid duplicates
4. **Multi-source BFS** starts from all sources simultaneously
5. **Grid problems** are just graphs with 4 or 8 neighbors

---

*Last Updated: 2024*
