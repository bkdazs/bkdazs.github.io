---
title: "Advanced Graph Algorithms"
topic: "Algorithms/Graphs"
difficulty: "Hard"
tags: ["graphs", "topological-sort", "scc", "mst", "network-flow", "bridges"]
status: "complete"
weight: 3
---

# Advanced Graph Algorithms

## 📚 Summary

This document covers advanced graph algorithms including Topological Sort, Strongly Connected Components (SCCs), Bridges and Articulation Points, Minimum Spanning Trees (MST), and Network Flow.

---

## 1️⃣ Topological Sort

### Concept
Linear ordering of vertices in a DAG such that for every edge (u, v), u comes before v.

### Kahn's Algorithm (BFS-based)

```python
from collections import deque, defaultdict
from typing import List, Dict

def topological_sort_kahn(graph: Dict[int, List[int]], n: int) -> List[int]:
    """
    Topological sort using Kahn's algorithm (BFS).
    Returns empty list if cycle exists.
    
    Time: O(V + E), Space: O(V)
    
    >>> graph = {0: [1, 2], 1: [3], 2: [3], 3: []}
    >>> topological_sort_kahn(graph, 4)
    [0, 1, 2, 3]  # or [0, 2, 1, 3]
    """
    # Calculate in-degrees
    in_degree = [0] * n
    for u in graph:
        for v in graph[u]:
            in_degree[v] += 1
    
    # Start with nodes having in-degree 0
    queue = deque([i for i in range(n) if in_degree[i] == 0])
    result = []
    
    while queue:
        u = queue.popleft()
        result.append(u)
        
        for v in graph.get(u, []):
            in_degree[v] -= 1
            if in_degree[v] == 0:
                queue.append(v)
    
    # Check for cycle
    return result if len(result) == n else []
```

### DFS-based Topological Sort

```python
def topological_sort_dfs(graph: Dict[int, List[int]], n: int) -> List[int]:
    """
    Topological sort using DFS.
    Returns empty list if cycle exists.
    
    Time: O(V + E), Space: O(V)
    """
    WHITE, GRAY, BLACK = 0, 1, 2
    color = [WHITE] * n
    result = []
    has_cycle = False
    
    def dfs(u: int) -> None:
        nonlocal has_cycle
        if has_cycle:
            return
        
        color[u] = GRAY
        
        for v in graph.get(u, []):
            if color[v] == GRAY:
                has_cycle = True
                return
            if color[v] == WHITE:
                dfs(v)
        
        color[u] = BLACK
        result.append(u)
    
    for i in range(n):
        if color[i] == WHITE:
            dfs(i)
    
    return result[::-1] if not has_cycle else []
```

### Course Schedule Application

```python
def find_order(num_courses: int, prerequisites: List[List[int]]) -> List[int]:
    """
    Find order to take courses. prerequisites[i] = [a, b] means take b before a.
    
    >>> find_order(4, [[1,0],[2,0],[3,1],[3,2]])
    [0, 1, 2, 3]  # or [0, 2, 1, 3]
    """
    graph = defaultdict(list)
    in_degree = [0] * num_courses
    
    for course, prereq in prerequisites:
        graph[prereq].append(course)
        in_degree[course] += 1
    
    queue = deque([i for i in range(num_courses) if in_degree[i] == 0])
    result = []
    
    while queue:
        course = queue.popleft()
        result.append(course)
        
        for next_course in graph[course]:
            in_degree[next_course] -= 1
            if in_degree[next_course] == 0:
                queue.append(next_course)
    
    return result if len(result) == num_courses else []
```

---

## 2️⃣ Strongly Connected Components (SCCs)

### Kosaraju's Algorithm

```python
def kosaraju_scc(graph: Dict[int, List[int]], n: int) -> List[List[int]]:
    """
    Find all SCCs using Kosaraju's algorithm.
    
    Time: O(V + E), Space: O(V)
    
    >>> graph = {0: [1], 1: [2], 2: [0, 3], 3: [4], 4: [5], 5: [3]}
    >>> sccs = kosaraju_scc(graph, 6)
    >>> sorted([sorted(scc) for scc in sccs])
    [[0, 1, 2], [3, 4, 5]]
    """
    # Step 1: DFS and record finish order
    visited = [False] * n
    finish_order = []
    
    def dfs1(u: int) -> None:
        visited[u] = True
        for v in graph.get(u, []):
            if not visited[v]:
                dfs1(v)
        finish_order.append(u)
    
    for i in range(n):
        if not visited[i]:
            dfs1(i)
    
    # Step 2: Build reverse graph
    reverse_graph = defaultdict(list)
    for u in graph:
        for v in graph[u]:
            reverse_graph[v].append(u)
    
    # Step 3: DFS on reverse graph in reverse finish order
    visited = [False] * n
    sccs = []
    
    def dfs2(u: int, scc: List[int]) -> None:
        visited[u] = True
        scc.append(u)
        for v in reverse_graph.get(u, []):
            if not visited[v]:
                dfs2(v, scc)
    
    for u in reversed(finish_order):
        if not visited[u]:
            scc = []
            dfs2(u, scc)
            sccs.append(scc)
    
    return sccs
```

### Tarjan's SCC Algorithm

```python
def tarjan_scc(graph: Dict[int, List[int]], n: int) -> List[List[int]]:
    """
    Find all SCCs using Tarjan's algorithm.
    
    Time: O(V + E), Space: O(V)
    """
    index_counter = [0]
    stack = []
    lowlink = [0] * n
    index = [0] * n
    on_stack = [False] * n
    index_initialized = [False] * n
    sccs = []
    
    def strongconnect(v: int) -> None:
        index[v] = index_counter[0]
        lowlink[v] = index_counter[0]
        index_counter[0] += 1
        index_initialized[v] = True
        stack.append(v)
        on_stack[v] = True
        
        for w in graph.get(v, []):
            if not index_initialized[w]:
                strongconnect(w)
                lowlink[v] = min(lowlink[v], lowlink[w])
            elif on_stack[w]:
                lowlink[v] = min(lowlink[v], index[w])
        
        # If v is a root node, pop the stack and generate an SCC
        if lowlink[v] == index[v]:
            scc = []
            while True:
                w = stack.pop()
                on_stack[w] = False
                scc.append(w)
                if w == v:
                    break
            sccs.append(scc)
    
    for v in range(n):
        if not index_initialized[v]:
            strongconnect(v)
    
    return sccs
```

---

## 3️⃣ Bridges and Articulation Points

### Finding Bridges (Tarjan's)

```python
def find_bridges(graph: Dict[int, List[int]], n: int) -> List[tuple]:
    """
    Find all bridges (critical edges) in an undirected graph.
    A bridge is an edge whose removal disconnects the graph.
    
    Time: O(V + E), Space: O(V)
    
    >>> graph = {0: [1, 2], 1: [0, 2], 2: [0, 1, 3], 3: [2]}
    >>> find_bridges(graph, 4)
    [(2, 3)]
    """
    disc = [0] * n  # Discovery time
    low = [0] * n   # Lowest reachable discovery time
    visited = [False] * n
    bridges = []
    timer = [0]
    
    def dfs(u: int, parent: int) -> None:
        visited[u] = True
        disc[u] = low[u] = timer[0]
        timer[0] += 1
        
        for v in graph.get(u, []):
            if not visited[v]:
                dfs(v, u)
                low[u] = min(low[u], low[v])
                
                # Bridge condition: no back edge from subtree
                if low[v] > disc[u]:
                    bridges.append((u, v))
            elif v != parent:
                low[u] = min(low[u], disc[v])
    
    for i in range(n):
        if not visited[i]:
            dfs(i, -1)
    
    return bridges
```

### Finding Articulation Points

```python
def find_articulation_points(graph: Dict[int, List[int]], n: int) -> List[int]:
    """
    Find all articulation points (cut vertices) in an undirected graph.
    An articulation point is a vertex whose removal disconnects the graph.
    
    Time: O(V + E), Space: O(V)
    """
    disc = [0] * n
    low = [0] * n
    visited = [False] * n
    parent = [-1] * n
    ap = set()
    timer = [0]
    
    def dfs(u: int) -> None:
        children = 0
        visited[u] = True
        disc[u] = low[u] = timer[0]
        timer[0] += 1
        
        for v in graph.get(u, []):
            if not visited[v]:
                children += 1
                parent[v] = u
                dfs(v)
                low[u] = min(low[u], low[v])
                
                # Articulation point conditions:
                # 1. Root with 2+ children
                if parent[u] == -1 and children > 1:
                    ap.add(u)
                # 2. Non-root where no back edge from subtree
                if parent[u] != -1 and low[v] >= disc[u]:
                    ap.add(u)
            elif v != parent[u]:
                low[u] = min(low[u], disc[v])
    
    for i in range(n):
        if not visited[i]:
            dfs(i)
    
    return list(ap)
```

---

## 4️⃣ Minimum Spanning Tree (MST)

### Kruskal's Algorithm

```python
class UnionFind:
    def __init__(self, n: int):
        self.parent = list(range(n))
        self.rank = [0] * n
    
    def find(self, x: int) -> int:
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])
        return self.parent[x]
    
    def union(self, x: int, y: int) -> bool:
        px, py = self.find(x), self.find(y)
        if px == py:
            return False
        if self.rank[px] < self.rank[py]:
            px, py = py, px
        self.parent[py] = px
        if self.rank[px] == self.rank[py]:
            self.rank[px] += 1
        return True


def kruskal_mst(edges: List[tuple], n: int) -> tuple:
    """
    Find MST using Kruskal's algorithm.
    edges = [(u, v, weight), ...]
    
    Time: O(E log E), Space: O(V)
    
    >>> edges = [(0, 1, 10), (0, 2, 6), (0, 3, 5), (1, 3, 15), (2, 3, 4)]
    >>> kruskal_mst(edges, 4)
    (19, [(2, 3, 4), (0, 3, 5), (0, 1, 10)])
    """
    # Sort edges by weight
    edges = sorted(edges, key=lambda x: x[2])
    
    uf = UnionFind(n)
    mst_edges = []
    mst_weight = 0
    
    for u, v, w in edges:
        if uf.union(u, v):
            mst_edges.append((u, v, w))
            mst_weight += w
            
            if len(mst_edges) == n - 1:
                break
    
    return mst_weight, mst_edges
```

### Prim's Algorithm

```python
import heapq

def prim_mst(graph: Dict[int, List[tuple]], n: int) -> tuple:
    """
    Find MST using Prim's algorithm.
    graph = {node: [(neighbor, weight), ...]}
    
    Time: O(E log V), Space: O(V)
    """
    visited = [False] * n
    mst_weight = 0
    mst_edges = []
    
    # Start from node 0: (weight, node, parent)
    pq = [(0, 0, -1)]
    
    while pq and len(mst_edges) < n - 1:
        w, u, parent = heapq.heappop(pq)
        
        if visited[u]:
            continue
        
        visited[u] = True
        mst_weight += w
        
        if parent != -1:
            mst_edges.append((parent, u, w))
        
        for v, weight in graph.get(u, []):
            if not visited[v]:
                heapq.heappush(pq, (weight, v, u))
    
    return mst_weight, mst_edges


def min_cost_connect_points(points: List[List[int]]) -> int:
    """
    Find minimum cost to connect all points with Manhattan distance.
    
    Time: O(N² log N), Space: O(N²)
    
    >>> points = [[0,0],[2,2],[3,10],[5,2],[7,0]]
    >>> min_cost_connect_points(points)
    20
    """
    n = len(points)
    
    # Build complete graph with Manhattan distances
    edges = []
    for i in range(n):
        for j in range(i + 1, n):
            dist = abs(points[i][0] - points[j][0]) + abs(points[i][1] - points[j][1])
            edges.append((dist, i, j))
    
    # Kruskal's
    edges.sort()
    uf = UnionFind(n)
    total_cost = 0
    edges_used = 0
    
    for w, u, v in edges:
        if uf.union(u, v):
            total_cost += w
            edges_used += 1
            if edges_used == n - 1:
                break
    
    return total_cost
```

---

## 5️⃣ Network Flow

### Ford-Fulkerson with BFS (Edmonds-Karp)

```python
def max_flow_bfs(capacity: List[List[int]], source: int, sink: int) -> int:
    """
    Find maximum flow using Edmonds-Karp (BFS-based Ford-Fulkerson).
    capacity[u][v] = capacity of edge u→v.
    
    Time: O(V × E²), Space: O(V²)
    
    >>> cap = [[0, 16, 13, 0, 0, 0],
    ...        [0, 0, 10, 12, 0, 0],
    ...        [0, 4, 0, 0, 14, 0],
    ...        [0, 0, 9, 0, 0, 20],
    ...        [0, 0, 0, 7, 0, 4],
    ...        [0, 0, 0, 0, 0, 0]]
    >>> max_flow_bfs(cap, 0, 5)
    23
    """
    n = len(capacity)
    residual = [row[:] for row in capacity]
    
    def bfs() -> List[int]:
        """Find augmenting path using BFS. Returns parent array."""
        visited = [False] * n
        parent = [-1] * n
        queue = deque([source])
        visited[source] = True
        
        while queue:
            u = queue.popleft()
            
            for v in range(n):
                if not visited[v] and residual[u][v] > 0:
                    visited[v] = True
                    parent[v] = u
                    if v == sink:
                        return parent
                    queue.append(v)
        
        return parent
    
    max_flow = 0
    
    while True:
        parent = bfs()
        
        if parent[sink] == -1:
            break
        
        # Find bottleneck
        path_flow = float('inf')
        v = sink
        while v != source:
            u = parent[v]
            path_flow = min(path_flow, residual[u][v])
            v = u
        
        # Update residual graph
        v = sink
        while v != source:
            u = parent[v]
            residual[u][v] -= path_flow
            residual[v][u] += path_flow
            v = u
        
        max_flow += path_flow
    
    return max_flow
```

### Bipartite Matching

```python
def max_bipartite_matching(graph: Dict[int, List[int]], 
                           n_left: int, n_right: int) -> int:
    """
    Find maximum matching in bipartite graph.
    graph[u] = list of nodes in right partition that u can match to.
    
    Time: O(V × E), Space: O(V)
    """
    match_right = [-1] * n_right
    
    def dfs(u: int, visited: List[bool]) -> bool:
        for v in graph.get(u, []):
            if visited[v]:
                continue
            visited[v] = True
            
            # If v is unmatched or we can find alternative for current match
            if match_right[v] == -1 or dfs(match_right[v], visited):
                match_right[v] = u
                return True
        
        return False
    
    matching = 0
    for u in range(n_left):
        visited = [False] * n_right
        if dfs(u, visited):
            matching += 1
    
    return matching
```

---

## 🔍 Worked Examples

### Example: Critical Connections in a Network

```python
def critical_connections(n: int, connections: List[List[int]]) -> List[List[int]]:
    """
    Find all critical connections (bridges) in a network.
    
    Time: O(V + E), Space: O(V + E)
    
    >>> critical_connections(4, [[0,1],[1,2],[2,0],[1,3]])
    [[1, 3]]
    """
    graph = defaultdict(list)
    for u, v in connections:
        graph[u].append(v)
        graph[v].append(u)
    
    disc = [0] * n
    low = [0] * n
    visited = [False] * n
    result = []
    timer = [1]
    
    def dfs(u: int, parent: int) -> None:
        visited[u] = True
        disc[u] = low[u] = timer[0]
        timer[0] += 1
        
        for v in graph[u]:
            if not visited[v]:
                dfs(v, u)
                low[u] = min(low[u], low[v])
                if low[v] > disc[u]:
                    result.append([u, v])
            elif v != parent:
                low[u] = min(low[u], disc[v])
    
    dfs(0, -1)
    return result
```

---

## 📚 Practice Problems

### Topological Sort
| # | Problem | Key Concept |
|---|---------|-------------|
| 1 | [LC 207: Course Schedule](https://leetcode.com/problems/course-schedule/) | Cycle detection |
| 2 | [LC 210: Course Schedule II](https://leetcode.com/problems/course-schedule-ii/) | Topo sort |
| 3 | [LC 269: Alien Dictionary](https://leetcode.com/problems/alien-dictionary/) | Build graph + topo |
| 4 | [LC 444: Sequence Reconstruction](https://leetcode.com/problems/sequence-reconstruction/) | Unique topo sort |
| 5 | [LC 1136: Parallel Courses](https://leetcode.com/problems/parallel-courses/) | Level-based topo |

### SCCs, Bridges, Articulation Points
| # | Problem | Key Concept |
|---|---------|-------------|
| 6 | [LC 1192: Critical Connections](https://leetcode.com/problems/critical-connections-in-a-network/) | Bridges |
| 7 | [LC 1568: Min Days to Disconnect Island](https://leetcode.com/problems/minimum-number-of-days-to-disconnect-island/) | Articulation |
| 8 | [LC 928: Minimize Malware Spread II](https://leetcode.com/problems/minimize-malware-spread-ii/) | Critical nodes |

### MST
| # | Problem | Key Concept |
|---|---------|-------------|
| 9 | [LC 1584: Min Cost Connect Points](https://leetcode.com/problems/min-cost-to-connect-all-points/) | Kruskal/Prim |
| 10 | [LC 1135: Connecting Cities Min Cost](https://leetcode.com/problems/connecting-cities-with-minimum-cost/) | MST |
| 11 | [LC 1489: Find Critical Pseudo-Critical Edges](https://leetcode.com/problems/find-critical-and-pseudo-critical-edges-in-minimum-spanning-tree/) | MST analysis |

### Network Flow
| # | Problem | Key Concept |
|---|---------|-------------|
| 12 | [LC 1066: Campus Bikes II](https://leetcode.com/problems/campus-bikes-ii/) | Assignment |
| 13 | [LC 1349: Max Students Taking Exam](https://leetcode.com/problems/maximum-students-taking-exam/) | Bipartite match |

---

## 🔑 Key Takeaways

1. **Topological Sort**: Only for DAGs, use for dependency ordering
2. **SCCs**: Find cycles in directed graphs, useful for 2-SAT
3. **Bridges/Articulation**: Find critical edges/nodes in networks
4. **MST**: Use Kruskal for sparse, Prim for dense graphs
5. **Max Flow**: Edmonds-Karp for small graphs, Dinic for larger

---

*Last Updated: 2024*
