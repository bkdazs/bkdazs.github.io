---
title: "Shortest Path Algorithms"
topic: "Algorithms/Graphs"
difficulty: "Medium-Hard"
tags: ["graphs", "shortest-path", "dijkstra", "bellman-ford", "floyd-warshall"]
status: "complete"
weight: 2
---

# Shortest Path Algorithms

## 📚 Summary

Shortest path algorithms find the minimum-cost path between nodes in a graph. The choice of algorithm depends on graph properties: edge weights, negative weights, single-source vs all-pairs, and graph density.

---

## ⏱️ Algorithm Comparison

| Algorithm | Time | Space | Negative Edges | Use Case |
|-----------|------|-------|----------------|----------|
| BFS | O(V + E) | O(V) | No (unweighted) | Unweighted graphs |
| Dijkstra | O((V + E) log V) | O(V) | No | Non-negative weights |
| Bellman-Ford | O(V × E) | O(V) | Yes | Negative weights |
| SPFA | O(V × E) avg O(E) | O(V) | Yes | Sparse graphs |
| Floyd-Warshall | O(V³) | O(V²) | Yes | All-pairs |
| A* | O(E) best case | O(V) | No | Heuristic available |
| 0-1 BFS | O(V + E) | O(V) | 0 or 1 weights | Binary weights |

---

## 💻 Implementations

### Dijkstra's Algorithm

```python
import heapq
from typing import List, Dict, Tuple

def dijkstra(graph: Dict[int, List[Tuple[int, int]]], start: int, n: int) -> List[int]:
    """
    Find shortest paths from start to all nodes.
    Graph format: {node: [(neighbor, weight), ...]}
    
    Time: O((V + E) log V), Space: O(V)
    
    >>> graph = {0: [(1, 4), (2, 1)], 1: [(3, 1)], 2: [(1, 2), (3, 5)], 3: []}
    >>> dijkstra(graph, 0, 4)
    [0, 3, 1, 4]
    """
    dist = [float('inf')] * n
    dist[start] = 0
    
    # Min-heap: (distance, node)
    pq = [(0, start)]
    
    while pq:
        d, u = heapq.heappop(pq)
        
        # Skip if we've found a better path
        if d > dist[u]:
            continue
        
        for v, weight in graph.get(u, []):
            if dist[u] + weight < dist[v]:
                dist[v] = dist[u] + weight
                heapq.heappush(pq, (dist[v], v))
    
    return dist


def dijkstra_with_path(graph: Dict[int, List[Tuple[int, int]]], 
                       start: int, end: int, n: int) -> Tuple[int, List[int]]:
    """
    Find shortest path and reconstruct the path.
    
    Time: O((V + E) log V), Space: O(V)
    """
    dist = [float('inf')] * n
    dist[start] = 0
    parent = [-1] * n
    
    pq = [(0, start)]
    
    while pq:
        d, u = heapq.heappop(pq)
        
        if u == end:
            break
            
        if d > dist[u]:
            continue
        
        for v, weight in graph.get(u, []):
            if dist[u] + weight < dist[v]:
                dist[v] = dist[u] + weight
                parent[v] = u
                heapq.heappush(pq, (dist[v], v))
    
    # Reconstruct path
    if dist[end] == float('inf'):
        return -1, []
    
    path = []
    node = end
    while node != -1:
        path.append(node)
        node = parent[node]
    
    return dist[end], path[::-1]
```

### Bellman-Ford Algorithm

```python
def bellman_ford(edges: List[Tuple[int, int, int]], n: int, start: int) -> List[int]:
    """
    Find shortest paths with negative weights. Detects negative cycles.
    Edges format: [(u, v, weight), ...]
    
    Time: O(V × E), Space: O(V)
    
    Returns distances, or None if negative cycle exists.
    """
    dist = [float('inf')] * n
    dist[start] = 0
    
    # Relax all edges V-1 times
    for _ in range(n - 1):
        updated = False
        for u, v, w in edges:
            if dist[u] != float('inf') and dist[u] + w < dist[v]:
                dist[v] = dist[u] + w
                updated = True
        
        # Early termination if no updates
        if not updated:
            break
    
    # Check for negative cycles
    for u, v, w in edges:
        if dist[u] != float('inf') and dist[u] + w < dist[v]:
            return None  # Negative cycle detected
    
    return dist


def find_negative_cycle(edges: List[Tuple[int, int, int]], n: int) -> List[int]:
    """
    Find and return a negative cycle if one exists.
    
    Time: O(V × E), Space: O(V)
    """
    dist = [0] * n  # Start from all zeros
    parent = [-1] * n
    last_updated = -1
    
    for i in range(n):
        last_updated = -1
        for u, v, w in edges:
            if dist[u] + w < dist[v]:
                dist[v] = dist[u] + w
                parent[v] = u
                last_updated = v
    
    if last_updated == -1:
        return []  # No negative cycle
    
    # Find a node in the cycle
    node = last_updated
    for _ in range(n):
        node = parent[node]
    
    # Reconstruct the cycle
    cycle = []
    curr = node
    while True:
        cycle.append(curr)
        curr = parent[curr]
        if curr == node:
            cycle.append(curr)
            break
    
    return cycle[::-1]
```

### Floyd-Warshall Algorithm

```python
def floyd_warshall(graph: List[List[int]]) -> List[List[int]]:
    """
    Find shortest paths between all pairs of vertices.
    Graph is adjacency matrix. INF for no edge.
    
    Time: O(V³), Space: O(V²)
    
    >>> graph = [[0, 5, float('inf'), 10],
    ...          [float('inf'), 0, 3, float('inf')],
    ...          [float('inf'), float('inf'), 0, 1],
    ...          [float('inf'), float('inf'), float('inf'), 0]]
    >>> result = floyd_warshall(graph)
    >>> result[0][3]  # Shortest 0 → 3
    9
    """
    n = len(graph)
    # Create a copy of the distance matrix
    dist = [row[:] for row in graph]
    
    # Consider each vertex as intermediate
    for k in range(n):
        for i in range(n):
            for j in range(n):
                if dist[i][k] + dist[k][j] < dist[i][j]:
                    dist[i][j] = dist[i][k] + dist[k][j]
    
    return dist


def floyd_warshall_with_path(graph: List[List[int]]) -> Tuple[List[List[int]], List[List[int]]]:
    """
    Floyd-Warshall with path reconstruction.
    
    Time: O(V³), Space: O(V²)
    """
    n = len(graph)
    dist = [row[:] for row in graph]
    
    # next[i][j] = first node after i on path to j
    next_node = [[j if graph[i][j] != float('inf') else -1 
                  for j in range(n)] for i in range(n)]
    
    for k in range(n):
        for i in range(n):
            for j in range(n):
                if dist[i][k] + dist[k][j] < dist[i][j]:
                    dist[i][j] = dist[i][k] + dist[k][j]
                    next_node[i][j] = next_node[i][k]
    
    return dist, next_node


def reconstruct_path(next_node: List[List[int]], i: int, j: int) -> List[int]:
    """Reconstruct path from i to j using next_node matrix."""
    if next_node[i][j] == -1:
        return []
    
    path = [i]
    while i != j:
        i = next_node[i][j]
        path.append(i)
    
    return path
```

### SPFA (Shortest Path Faster Algorithm)

```python
from collections import deque

def spfa(graph: Dict[int, List[Tuple[int, int]]], start: int, n: int) -> List[int]:
    """
    Optimized Bellman-Ford using queue. Average O(E), worst O(VE).
    Can detect negative cycles.
    
    Time: O(V × E) worst, O(E) average
    Space: O(V)
    """
    dist = [float('inf')] * n
    dist[start] = 0
    in_queue = [False] * n
    count = [0] * n  # Number of times node added to queue
    
    queue = deque([start])
    in_queue[start] = True
    
    while queue:
        u = queue.popleft()
        in_queue[u] = False
        
        for v, w in graph.get(u, []):
            if dist[u] + w < dist[v]:
                dist[v] = dist[u] + w
                
                if not in_queue[v]:
                    queue.append(v)
                    in_queue[v] = True
                    count[v] += 1
                    
                    # Negative cycle detection
                    if count[v] >= n:
                        return None
    
    return dist
```

### 0-1 BFS (Deque-based)

```python
def zero_one_bfs(graph: Dict[int, List[Tuple[int, int]]], start: int, n: int) -> List[int]:
    """
    BFS for graphs with edge weights 0 or 1.
    Add 0-weight edges to front, 1-weight to back.
    
    Time: O(V + E), Space: O(V)
    
    >>> graph = {0: [(1, 0), (2, 1)], 1: [(2, 1), (3, 0)], 2: [(3, 1)], 3: []}
    >>> zero_one_bfs(graph, 0, 4)
    [0, 0, 1, 0]
    """
    dist = [float('inf')] * n
    dist[start] = 0
    
    dq = deque([start])
    
    while dq:
        u = dq.popleft()
        
        for v, w in graph.get(u, []):
            if dist[u] + w < dist[v]:
                dist[v] = dist[u] + w
                
                if w == 0:
                    dq.appendleft(v)  # 0-weight: add to front
                else:
                    dq.append(v)       # 1-weight: add to back
    
    return dist
```

### A* Algorithm

```python
def a_star(graph: Dict[int, List[Tuple[int, int]]], 
           start: int, goal: int, 
           heuristic: callable) -> Tuple[int, List[int]]:
    """
    A* search with heuristic function.
    heuristic(node) should be admissible (never overestimate).
    
    Time: O(E) best case, depends on heuristic
    Space: O(V)
    """
    import heapq
    
    # g[n] = cost from start to n
    g = {start: 0}
    # f[n] = g[n] + h[n] (estimated total cost)
    f = {start: heuristic(start)}
    parent = {start: None}
    
    # Priority queue: (f_score, node)
    open_set = [(f[start], start)]
    closed_set = set()
    
    while open_set:
        _, current = heapq.heappop(open_set)
        
        if current == goal:
            # Reconstruct path
            path = []
            while current is not None:
                path.append(current)
                current = parent[current]
            return g[goal], path[::-1]
        
        if current in closed_set:
            continue
        closed_set.add(current)
        
        for neighbor, weight in graph.get(current, []):
            if neighbor in closed_set:
                continue
            
            tentative_g = g[current] + weight
            
            if neighbor not in g or tentative_g < g[neighbor]:
                parent[neighbor] = current
                g[neighbor] = tentative_g
                f[neighbor] = tentative_g + heuristic(neighbor)
                heapq.heappush(open_set, (f[neighbor], neighbor))
    
    return -1, []  # No path found


# Example: Grid-based A* with Manhattan distance
def grid_a_star(grid: List[List[int]], start: Tuple[int, int], 
                goal: Tuple[int, int]) -> int:
    """
    A* on a grid. 0 = passable, 1 = blocked.
    Uses Manhattan distance heuristic.
    """
    rows, cols = len(grid), len(grid[0])
    
    def heuristic(pos: Tuple[int, int]) -> int:
        return abs(pos[0] - goal[0]) + abs(pos[1] - goal[1])
    
    g = {start: 0}
    open_set = [(heuristic(start), start)]
    closed_set = set()
    
    while open_set:
        _, current = heapq.heappop(open_set)
        
        if current == goal:
            return g[goal]
        
        if current in closed_set:
            continue
        closed_set.add(current)
        
        r, c = current
        for dr, dc in [(0, 1), (0, -1), (1, 0), (-1, 0)]:
            nr, nc = r + dr, c + dc
            neighbor = (nr, nc)
            
            if (0 <= nr < rows and 0 <= nc < cols and 
                grid[nr][nc] == 0 and neighbor not in closed_set):
                
                tentative_g = g[current] + 1
                
                if neighbor not in g or tentative_g < g[neighbor]:
                    g[neighbor] = tentative_g
                    f = tentative_g + heuristic(neighbor)
                    heapq.heappush(open_set, (f, neighbor))
    
    return -1
```

---

## 🔍 Worked Examples

### Example 1: Network Delay Time

```python
def network_delay_time(times: List[List[int]], n: int, k: int) -> int:
    """
    Find time for signal to reach all nodes from node k.
    times[i] = [ui, vi, wi] means edge from ui to vi with weight wi.
    
    Time: O((V + E) log V), Space: O(V + E)
    
    >>> times = [[2,1,1],[2,3,1],[3,4,1]]
    >>> network_delay_time(times, 4, 2)
    2
    """
    # Build adjacency list
    graph = defaultdict(list)
    for u, v, w in times:
        graph[u].append((v, w))
    
    # Dijkstra from k
    dist = {k: 0}
    pq = [(0, k)]
    
    while pq:
        d, u = heapq.heappop(pq)
        
        if d > dist.get(u, float('inf')):
            continue
        
        for v, w in graph[u]:
            if d + w < dist.get(v, float('inf')):
                dist[v] = d + w
                heapq.heappush(pq, (dist[v], v))
    
    if len(dist) != n:
        return -1  # Not all nodes reachable
    
    return max(dist.values())
```

### Example 2: Cheapest Flights Within K Stops

```python
def find_cheapest_price(n: int, flights: List[List[int]], 
                        src: int, dst: int, k: int) -> int:
    """
    Find cheapest flight from src to dst with at most k stops.
    
    Time: O(E × K), Space: O(V)
    
    >>> flights = [[0,1,100],[1,2,100],[0,2,500]]
    >>> find_cheapest_price(3, flights, 0, 2, 1)
    200
    >>> find_cheapest_price(3, flights, 0, 2, 0)
    500
    """
    # Use modified Bellman-Ford with at most k+1 relaxations
    dist = [float('inf')] * n
    dist[src] = 0
    
    for _ in range(k + 1):
        # Important: use previous distances to avoid using updated values
        prev_dist = dist[:]
        
        for u, v, w in flights:
            if prev_dist[u] != float('inf'):
                dist[v] = min(dist[v], prev_dist[u] + w)
    
    return dist[dst] if dist[dst] != float('inf') else -1


def find_cheapest_price_dijkstra(n: int, flights: List[List[int]], 
                                  src: int, dst: int, k: int) -> int:
    """
    Alternative using modified Dijkstra.
    Track (cost, node, stops_remaining).
    """
    graph = defaultdict(list)
    for u, v, w in flights:
        graph[u].append((v, w))
    
    # (cost, node, stops)
    pq = [(0, src, k + 1)]
    visited = {}  # node -> min stops used to reach it
    
    while pq:
        cost, node, stops = heapq.heappop(pq)
        
        if node == dst:
            return cost
        
        if stops <= 0:
            continue
        
        # Only skip if we've reached this node with more stops remaining
        if node in visited and visited[node] >= stops:
            continue
        visited[node] = stops
        
        for neighbor, price in graph[node]:
            heapq.heappush(pq, (cost + price, neighbor, stops - 1))
    
    return -1
```

### Example 3: Path with Minimum Effort

```python
def minimum_effort_path(heights: List[List[int]]) -> int:
    """
    Find path from top-left to bottom-right minimizing maximum absolute 
    difference between consecutive cells.
    
    Time: O(M × N × log(max_height)), Space: O(M × N)
    """
    rows, cols = len(heights), len(heights[0])
    
    # Dijkstra with effort as the cost
    dist = [[float('inf')] * cols for _ in range(rows)]
    dist[0][0] = 0
    
    pq = [(0, 0, 0)]  # (effort, row, col)
    
    while pq:
        effort, r, c = heapq.heappop(pq)
        
        if r == rows - 1 and c == cols - 1:
            return effort
        
        if effort > dist[r][c]:
            continue
        
        for dr, dc in [(0, 1), (0, -1), (1, 0), (-1, 0)]:
            nr, nc = r + dr, c + dc
            
            if 0 <= nr < rows and 0 <= nc < cols:
                # Effort = max of current effort and this edge's effort
                new_effort = max(effort, abs(heights[nr][nc] - heights[r][c]))
                
                if new_effort < dist[nr][nc]:
                    dist[nr][nc] = new_effort
                    heapq.heappush(pq, (new_effort, nr, nc))
    
    return 0
```

---

## 🎯 Algorithm Selection Guide

```
Is graph weighted?
├── No (unweighted) → BFS
└── Yes
    ├── Are there negative weights?
    │   ├── No → Dijkstra
    │   └── Yes
    │       ├── Need all-pairs? → Floyd-Warshall
    │       └── Single-source → Bellman-Ford or SPFA
    │
    ├── Are weights only 0 or 1? → 0-1 BFS
    │
    ├── Have a good heuristic? → A*
    │
    └── Need all-pairs shortest path? → Floyd-Warshall
```

---

## ⚠️ Common Pitfalls

### 1. Using Dijkstra with Negative Weights

```python
# WRONG - Dijkstra can give incorrect results with negative weights
# Example: A → B (weight 2), A → C (weight 3), C → B (weight -2)
# Dijkstra: dist[B] = 2 (direct)
# Correct: dist[B] = 1 (via C)
```

### 2. Forgetting to Skip Processed Nodes

```python
# WRONG - processing same node multiple times
while pq:
    d, u = heapq.heappop(pq)
    for v, w in graph[u]:
        ...

# CORRECT - skip if already processed with better distance
while pq:
    d, u = heapq.heappop(pq)
    if d > dist[u]:  # Already found better path
        continue
    for v, w in graph[u]:
        ...
```

### 3. Modifying Distance Array During Bellman-Ford Iteration

```python
# WRONG for "at most k edges" problems
for u, v, w in edges:
    dist[v] = min(dist[v], dist[u] + w)  # Uses updated dist[u]

# CORRECT - use previous iteration's values
prev = dist[:]
for u, v, w in edges:
    dist[v] = min(dist[v], prev[u] + w)
```

---

## 📚 Practice Problems

### Easy
| # | Problem | Key Concept |
|---|---------|-------------|
| 1 | [LC 1091: Shortest Path Binary Matrix](https://leetcode.com/problems/shortest-path-in-binary-matrix/) | BFS |
| 2 | [LC 1129: Shortest Path Alternating Colors](https://leetcode.com/problems/shortest-path-with-alternating-colors/) | BFS with state |

### Medium
| # | Problem | Key Concept |
|---|---------|-------------|
| 3 | [LC 743: Network Delay Time](https://leetcode.com/problems/network-delay-time/) | Dijkstra |
| 4 | [LC 787: Cheapest Flights Within K Stops](https://leetcode.com/problems/cheapest-flights-within-k-stops/) | Modified BF |
| 5 | [LC 1514: Path with Maximum Probability](https://leetcode.com/problems/path-with-maximum-probability/) | Modified Dijkstra |
| 6 | [LC 1631: Path With Minimum Effort](https://leetcode.com/problems/path-with-minimum-effort/) | Dijkstra on grid |
| 7 | [LC 1334: Find City with Smallest Neighbors](https://leetcode.com/problems/find-the-city-with-the-smallest-number-of-neighbors-at-a-threshold-distance/) | Floyd-Warshall |
| 8 | [LC 1162: As Far from Land as Possible](https://leetcode.com/problems/as-far-from-land-as-possible/) | Multi-source BFS |
| 9 | [LC 778: Swim in Rising Water](https://leetcode.com/problems/swim-in-rising-water/) | Dijkstra/Binary Search |
| 10 | [LC 1293: Shortest Path in Grid with Obstacles](https://leetcode.com/problems/shortest-path-in-a-grid-with-obstacles-elimination/) | BFS with state |

### Hard
| # | Problem | Key Concept |
|---|---------|-------------|
| 11 | [LC 499: The Maze III](https://leetcode.com/problems/the-maze-iii/) | Dijkstra + path |
| 12 | [LC 505: The Maze II](https://leetcode.com/problems/the-maze-ii/) | Dijkstra |
| 13 | [LC 882: Reachable Nodes in Subdivided Graph](https://leetcode.com/problems/reachable-nodes-in-subdivided-graph/) | Dijkstra |
| 14 | [LC 1368: Min Cost to Make Valid Path](https://leetcode.com/problems/minimum-cost-to-make-at-least-one-valid-path-in-a-grid/) | 0-1 BFS |
| 15 | [LC 2203: Minimum Weighted Subgraph](https://leetcode.com/problems/minimum-weighted-subgraph-with-the-required-paths/) | Modified Dijkstra |

---

## 🔑 Key Takeaways

1. **Dijkstra** for non-negative weights, single source
2. **Bellman-Ford** when negative weights exist or need to detect negative cycles
3. **Floyd-Warshall** for all-pairs shortest paths
4. **0-1 BFS** for binary-weighted graphs
5. **A*** when a good heuristic is available

---

*Last Updated: 2024*
