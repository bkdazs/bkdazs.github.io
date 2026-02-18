---
title: "Advanced Graph Algorithms"
topic: "Graphs"
difficulty: "Hard"
tags: ["graph", "tarjan", "bridges", "articulation", "bipartite", "euler"]
status: "complete"
weight: 4
---

# Advanced Graph Algorithms

## 📚 Summary

This covers advanced graph algorithms: bridges and articulation points (Tarjan's), bipartite matching, Eulerian paths/circuits, 0-1 BFS, and multi-source BFS patterns.

---

## 1️⃣ Bridges and Articulation Points (Tarjan's Algorithm)

```python
"""
Bridge: Edge whose removal disconnects graph
Articulation Point: Vertex whose removal disconnects graph

Tarjan's Algorithm uses DFS discovery times and low-link values:
- disc[u]: Discovery time of vertex u
- low[u]: Minimum discovery time reachable from subtree rooted at u

Bridge (u,v): low[v] > disc[u] (no back edge from v's subtree)
Articulation Point u:
  - If u is root: has 2+ children in DFS tree
  - If u is not root: has child v where low[v] >= disc[u]
"""

from collections import defaultdict


def find_bridges(n: int, edges: list[list[int]]) -> list[list[int]]:
    """
    Find all bridges in undirected graph
    Time: O(V + E)
    
    LC 1192: Critical Connections in a Network
    """
    graph = defaultdict(list)
    for u, v in edges:
        graph[u].append(v)
        graph[v].append(u)
    
    disc = [-1] * n
    low = [-1] * n
    bridges = []
    time = [0]
    
    def dfs(u: int, parent: int):
        disc[u] = low[u] = time[0]
        time[0] += 1
        
        for v in graph[u]:
            if disc[v] == -1:  # Tree edge
                dfs(v, u)
                low[u] = min(low[u], low[v])
                
                # Bridge condition
                if low[v] > disc[u]:
                    bridges.append([u, v])
            elif v != parent:  # Back edge
                low[u] = min(low[u], disc[v])
    
    # Handle disconnected graph
    for i in range(n):
        if disc[i] == -1:
            dfs(i, -1)
    
    return bridges


def find_articulation_points(n: int, edges: list[list[int]]) -> list[int]:
    """
    Find all articulation points in undirected graph
    Time: O(V + E)
    """
    graph = defaultdict(list)
    for u, v in edges:
        graph[u].append(v)
        graph[v].append(u)
    
    disc = [-1] * n
    low = [-1] * n
    is_ap = [False] * n
    time = [0]
    
    def dfs(u: int, parent: int):
        disc[u] = low[u] = time[0]
        time[0] += 1
        children = 0
        
        for v in graph[u]:
            if disc[v] == -1:
                children += 1
                dfs(v, u)
                low[u] = min(low[u], low[v])
                
                # Articulation point conditions
                if parent == -1 and children > 1:
                    is_ap[u] = True
                if parent != -1 and low[v] >= disc[u]:
                    is_ap[u] = True
            elif v != parent:
                low[u] = min(low[u], disc[v])
    
    for i in range(n):
        if disc[i] == -1:
            dfs(i, -1)
    
    return [i for i in range(n) if is_ap[i]]


def find_bridges_and_articulation(n: int, edges: list[list[int]]):
    """Find both bridges and articulation points"""
    graph = defaultdict(list)
    for u, v in edges:
        graph[u].append(v)
        graph[v].append(u)
    
    disc = [-1] * n
    low = [-1] * n
    bridges = []
    is_ap = [False] * n
    time = [0]
    
    def dfs(u: int, parent: int):
        disc[u] = low[u] = time[0]
        time[0] += 1
        children = 0
        
        for v in graph[u]:
            if disc[v] == -1:
                children += 1
                dfs(v, u)
                low[u] = min(low[u], low[v])
                
                if low[v] > disc[u]:
                    bridges.append((u, v))
                
                if parent == -1 and children > 1:
                    is_ap[u] = True
                if parent != -1 and low[v] >= disc[u]:
                    is_ap[u] = True
            elif v != parent:
                low[u] = min(low[u], disc[v])
    
    for i in range(n):
        if disc[i] == -1:
            dfs(i, -1)
    
    articulation_points = [i for i in range(n) if is_ap[i]]
    return bridges, articulation_points
```

---

## 2️⃣ Strongly Connected Components (Tarjan's)

```python
def tarjan_scc(n: int, graph: dict) -> list[list[int]]:
    """
    Find all SCCs in directed graph using Tarjan's algorithm
    Time: O(V + E)
    """
    disc = [-1] * n
    low = [-1] * n
    on_stack = [False] * n
    stack = []
    sccs = []
    time = [0]
    
    def dfs(u: int):
        disc[u] = low[u] = time[0]
        time[0] += 1
        stack.append(u)
        on_stack[u] = True
        
        for v in graph.get(u, []):
            if disc[v] == -1:
                dfs(v)
                low[u] = min(low[u], low[v])
            elif on_stack[v]:
                low[u] = min(low[u], disc[v])
        
        # If u is root of SCC
        if low[u] == disc[u]:
            scc = []
            while True:
                node = stack.pop()
                on_stack[node] = False
                scc.append(node)
                if node == u:
                    break
            sccs.append(scc)
    
    for i in range(n):
        if disc[i] == -1:
            dfs(i)
    
    return sccs


def kosaraju_scc(n: int, graph: dict) -> list[list[int]]:
    """
    Kosaraju's algorithm for SCCs
    Time: O(V + E)
    """
    # Step 1: Fill order by finish time
    visited = [False] * n
    order = []
    
    def dfs1(u: int):
        visited[u] = True
        for v in graph.get(u, []):
            if not visited[v]:
                dfs1(v)
        order.append(u)
    
    for i in range(n):
        if not visited[i]:
            dfs1(i)
    
    # Step 2: Build reverse graph
    rev_graph = defaultdict(list)
    for u in graph:
        for v in graph[u]:
            rev_graph[v].append(u)
    
    # Step 3: DFS in reverse finish order
    visited = [False] * n
    sccs = []
    
    def dfs2(u: int, scc: list):
        visited[u] = True
        scc.append(u)
        for v in rev_graph.get(u, []):
            if not visited[v]:
                dfs2(v, scc)
    
    for u in reversed(order):
        if not visited[u]:
            scc = []
            dfs2(u, scc)
            sccs.append(scc)
    
    return sccs
```

---

## 3️⃣ Bipartite Matching (Hungarian Algorithm)

```python
def max_bipartite_matching(n: int, m: int, edges: list[tuple[int, int]]) -> int:
    """
    Maximum matching in bipartite graph
    n: left vertices (0 to n-1)
    m: right vertices (0 to m-1)
    edges: (left, right) pairs
    
    Time: O(V * E)
    """
    graph = defaultdict(list)
    for u, v in edges:
        graph[u].append(v)
    
    match_left = [-1] * n   # match_left[u] = matched right vertex
    match_right = [-1] * m  # match_right[v] = matched left vertex
    
    def dfs(u: int, visited: set) -> bool:
        """Try to find augmenting path from u"""
        for v in graph[u]:
            if v in visited:
                continue
            visited.add(v)
            
            # If v is free or we can find alternating path
            if match_right[v] == -1 or dfs(match_right[v], visited):
                match_left[u] = v
                match_right[v] = u
                return True
        return False
    
    result = 0
    for u in range(n):
        if dfs(u, set()):
            result += 1
    
    return result


# Hopcroft-Karp for O(E * sqrt(V))
from collections import deque

def hopcroft_karp(n: int, m: int, edges: list[tuple[int, int]]) -> int:
    """
    Hopcroft-Karp algorithm for maximum bipartite matching
    Time: O(E * sqrt(V))
    """
    graph = defaultdict(list)
    for u, v in edges:
        graph[u].append(v)
    
    match_left = [-1] * n
    match_right = [-1] * m
    dist = [0] * n
    
    def bfs() -> bool:
        """Find shortest augmenting paths"""
        queue = deque()
        for u in range(n):
            if match_left[u] == -1:
                dist[u] = 0
                queue.append(u)
            else:
                dist[u] = float('inf')
        
        found = False
        while queue:
            u = queue.popleft()
            for v in graph[u]:
                next_u = match_right[v]
                if next_u == -1:
                    found = True
                elif dist[next_u] == float('inf'):
                    dist[next_u] = dist[u] + 1
                    queue.append(next_u)
        
        return found
    
    def dfs(u: int) -> bool:
        """Find augmenting path using DFS"""
        for v in graph[u]:
            next_u = match_right[v]
            if next_u == -1 or (dist[next_u] == dist[u] + 1 and dfs(next_u)):
                match_left[u] = v
                match_right[v] = u
                return True
        dist[u] = float('inf')
        return False
    
    result = 0
    while bfs():
        for u in range(n):
            if match_left[u] == -1 and dfs(u):
                result += 1
    
    return result
```

---

## 4️⃣ Eulerian Path and Circuit

```python
"""
Eulerian Path: Visit every edge exactly once
Eulerian Circuit: Eulerian path that starts and ends at same vertex

For UNDIRECTED graph:
- Eulerian Circuit exists: All vertices have even degree
- Eulerian Path exists: Exactly 0 or 2 vertices have odd degree

For DIRECTED graph:
- Eulerian Circuit: All vertices have equal in-degree and out-degree
- Eulerian Path: At most one vertex has out-degree - in-degree = 1 (start)
                 At most one vertex has in-degree - out-degree = 1 (end)
"""

def eulerian_path_undirected(n: int, edges: list[list[int]]) -> list[int]:
    """
    Find Eulerian path in undirected graph using Hierholzer's algorithm
    Returns empty list if no Eulerian path exists
    """
    if not edges:
        return [0] if n > 0 else []
    
    # Build adjacency list with edge tracking
    graph = defaultdict(list)
    for u, v in edges:
        graph[u].append([v, False])  # [neighbor, used]
        graph[v].append([u, False])
    
    # Count odd degree vertices
    odd_vertices = [v for v in graph if len(graph[v]) % 2 == 1]
    
    if len(odd_vertices) not in [0, 2]:
        return []  # No Eulerian path
    
    # Start from odd degree vertex if exists, else any vertex with edges
    start = odd_vertices[0] if odd_vertices else next(iter(graph))
    
    # Hierholzer's algorithm
    stack = [start]
    path = []
    
    while stack:
        u = stack[-1]
        found_edge = False
        
        for i, (v, used) in enumerate(graph[u]):
            if not used:
                # Mark edge as used (both directions)
                graph[u][i][1] = True
                # Find and mark reverse edge
                for j, (w, used2) in enumerate(graph[v]):
                    if w == u and not used2:
                        graph[v][j][1] = True
                        break
                
                stack.append(v)
                found_edge = True
                break
        
        if not found_edge:
            path.append(stack.pop())
    
    return path[::-1]


def eulerian_path_directed(n: int, edges: list[list[int]]) -> list[int]:
    """
    Find Eulerian path in directed graph
    LC 332: Reconstruct Itinerary (variant)
    """
    if not edges:
        return [0] if n > 0 else []
    
    graph = defaultdict(list)
    in_degree = defaultdict(int)
    out_degree = defaultdict(int)
    
    for u, v in edges:
        graph[u].append(v)
        out_degree[u] += 1
        in_degree[v] += 1
    
    # Check Eulerian path conditions
    start_vertices = []
    end_vertices = []
    
    all_vertices = set(in_degree.keys()) | set(out_degree.keys())
    
    for v in all_vertices:
        diff = out_degree[v] - in_degree[v]
        if diff == 1:
            start_vertices.append(v)
        elif diff == -1:
            end_vertices.append(v)
        elif diff != 0:
            return []  # No Eulerian path
    
    if len(start_vertices) > 1 or len(end_vertices) > 1:
        return []
    
    if len(start_vertices) != len(end_vertices):
        return []
    
    # Determine start
    if start_vertices:
        start = start_vertices[0]
    else:
        start = edges[0][0]
    
    # Hierholzer's algorithm
    stack = [start]
    path = []
    
    while stack:
        u = stack[-1]
        if graph[u]:
            v = graph[u].pop()
            stack.append(v)
        else:
            path.append(stack.pop())
    
    return path[::-1]


def reconstruct_itinerary(tickets: list[list[str]]) -> list[str]:
    """
    LC 332: Reconstruct Itinerary
    Given airline tickets, find itinerary starting from "JFK"
    Use lexically smallest path when multiple options exist
    """
    import heapq
    
    graph = defaultdict(list)
    for src, dst in tickets:
        heapq.heappush(graph[src], dst)
    
    path = []
    
    def dfs(airport: str):
        while graph[airport]:
            next_airport = heapq.heappop(graph[airport])
            dfs(next_airport)
        path.append(airport)
    
    dfs("JFK")
    return path[::-1]
```

---

## 5️⃣ 0-1 BFS

```python
"""
0-1 BFS: BFS when edge weights are only 0 or 1
Uses deque instead of priority queue
- 0-weight edges: add to front
- 1-weight edges: add to back
Time: O(V + E), better than Dijkstra's O((V+E) log V)
"""

from collections import deque

def zero_one_bfs(n: int, graph: dict, start: int) -> list[int]:
    """
    Shortest paths when edges have weight 0 or 1
    graph[u] = [(v, weight), ...]
    """
    dist = [float('inf')] * n
    dist[start] = 0
    
    dq = deque([start])
    
    while dq:
        u = dq.popleft()
        
        for v, weight in graph[u]:
            new_dist = dist[u] + weight
            if new_dist < dist[v]:
                dist[v] = new_dist
                if weight == 0:
                    dq.appendleft(v)  # Front for 0-weight
                else:
                    dq.append(v)      # Back for 1-weight
    
    return dist


def minimum_obstacle_removal(grid: list[list[int]]) -> int:
    """
    LC 2290: Minimum Obstacle Removal to Reach Corner
    0 = empty, 1 = obstacle
    """
    m, n = len(grid), len(grid[0])
    dist = [[float('inf')] * n for _ in range(m)]
    dist[0][0] = grid[0][0]
    
    dq = deque([(0, 0)])
    
    while dq:
        i, j = dq.popleft()
        
        for di, dj in [(0, 1), (0, -1), (1, 0), (-1, 0)]:
            ni, nj = i + di, j + dj
            if 0 <= ni < m and 0 <= nj < n:
                new_dist = dist[i][j] + grid[ni][nj]
                if new_dist < dist[ni][nj]:
                    dist[ni][nj] = new_dist
                    if grid[ni][nj] == 0:
                        dq.appendleft((ni, nj))
                    else:
                        dq.append((ni, nj))
    
    return dist[m - 1][n - 1]


def shortest_path_alternating_colors(n: int, red_edges: list, blue_edges: list) -> list[int]:
    """
    LC 1129: Shortest Path with Alternating Colors
    Must alternate colors on path
    """
    RED, BLUE = 0, 1
    
    graph = defaultdict(lambda: defaultdict(list))
    for u, v in red_edges:
        graph[u][RED].append(v)
    for u, v in blue_edges:
        graph[u][BLUE].append(v)
    
    # dist[node][color] = shortest path ending with that color
    INF = float('inf')
    dist = [[INF, INF] for _ in range(n)]
    dist[0] = [0, 0]
    
    # BFS with state (node, last_color)
    queue = deque([(0, RED), (0, BLUE)])
    
    while queue:
        u, last_color = queue.popleft()
        next_color = 1 - last_color
        
        for v in graph[u][next_color]:
            if dist[u][last_color] + 1 < dist[v][next_color]:
                dist[v][next_color] = dist[u][last_color] + 1
                queue.append((v, next_color))
    
    result = []
    for i in range(n):
        min_dist = min(dist[i])
        result.append(min_dist if min_dist != INF else -1)
    
    return result
```

---

## 6️⃣ Multi-Source BFS

```python
def multi_source_bfs(grid: list[list[int]], sources: list[tuple[int, int]]) -> list[list[int]]:
    """
    BFS from multiple sources simultaneously
    Returns distance from nearest source for each cell
    """
    m, n = len(grid), len(grid[0])
    dist = [[float('inf')] * n for _ in range(m)]
    queue = deque()
    
    # Initialize all sources
    for i, j in sources:
        dist[i][j] = 0
        queue.append((i, j))
    
    while queue:
        i, j = queue.popleft()
        for di, dj in [(0, 1), (0, -1), (1, 0), (-1, 0)]:
            ni, nj = i + di, j + dj
            if 0 <= ni < m and 0 <= nj < n and dist[ni][nj] == float('inf'):
                dist[ni][nj] = dist[i][j] + 1
                queue.append((ni, nj))
    
    return dist


def walls_and_gates(rooms: list[list[int]]) -> None:
    """
    LC 286: Walls and Gates
    -1 = wall, 0 = gate, INF = empty
    Fill each empty with distance to nearest gate
    """
    if not rooms:
        return
    
    m, n = len(rooms), len(rooms[0])
    INF = 2147483647
    
    # Find all gates
    queue = deque()
    for i in range(m):
        for j in range(n):
            if rooms[i][j] == 0:
                queue.append((i, j))
    
    while queue:
        i, j = queue.popleft()
        for di, dj in [(0, 1), (0, -1), (1, 0), (-1, 0)]:
            ni, nj = i + di, j + dj
            if 0 <= ni < m and 0 <= nj < n and rooms[ni][nj] == INF:
                rooms[ni][nj] = rooms[i][j] + 1
                queue.append((ni, nj))


def rotting_oranges(grid: list[list[int]]) -> int:
    """
    LC 994: Rotting Oranges
    Multi-source BFS from all rotten oranges
    """
    m, n = len(grid), len(grid[0])
    queue = deque()
    fresh = 0
    
    for i in range(m):
        for j in range(n):
            if grid[i][j] == 2:
                queue.append((i, j))
            elif grid[i][j] == 1:
                fresh += 1
    
    if fresh == 0:
        return 0
    
    minutes = 0
    while queue:
        minutes += 1
        for _ in range(len(queue)):
            i, j = queue.popleft()
            for di, dj in [(0, 1), (0, -1), (1, 0), (-1, 0)]:
                ni, nj = i + di, j + dj
                if 0 <= ni < m and 0 <= nj < n and grid[ni][nj] == 1:
                    grid[ni][nj] = 2
                    fresh -= 1
                    queue.append((ni, nj))
    
    return minutes - 1 if fresh == 0 else -1
```

---

## ⏱️ Complexity

| Algorithm | Time | Space |
|-----------|------|-------|
| Tarjan's Bridges/AP | O(V + E) | O(V) |
| Tarjan's SCC | O(V + E) | O(V) |
| Bipartite Matching | O(V × E) | O(V) |
| Hopcroft-Karp | O(E × √V) | O(V) |
| Eulerian Path | O(V + E) | O(E) |
| 0-1 BFS | O(V + E) | O(V) |
| Multi-Source BFS | O(V + E) | O(V) |

---

## 📚 Practice Problems

| # | Problem | Key Concept |
|---|---------|-------------|
| 1 | [LC 1192: Critical Connections](https://leetcode.com/problems/critical-connections-in-a-network/) | Bridges |
| 2 | [LC 332: Reconstruct Itinerary](https://leetcode.com/problems/reconstruct-itinerary/) | Eulerian path |
| 3 | [LC 753: Cracking Safe](https://leetcode.com/problems/cracking-the-safe/) | Eulerian |
| 4 | [LC 2290: Min Obstacle Removal](https://leetcode.com/problems/minimum-obstacle-removal-to-reach-corner/) | 0-1 BFS |
| 5 | [LC 1129: Alternating Colors](https://leetcode.com/problems/shortest-path-with-alternating-colors/) | Modified BFS |
| 6 | [LC 286: Walls and Gates](https://leetcode.com/problems/walls-and-gates/) | Multi-source |
| 7 | [LC 994: Rotting Oranges](https://leetcode.com/problems/rotting-oranges/) | Multi-source |
| 8 | [LC 934: Shortest Bridge](https://leetcode.com/problems/shortest-bridge/) | Multi-source |
| 9 | [LC 1263: Min Moves Box](https://leetcode.com/problems/minimum-moves-to-move-a-box-to-their-target-location/) | 0-1 BFS |
| 10 | [LC 1368: Min Cost Grid](https://leetcode.com/problems/minimum-cost-to-make-at-least-one-valid-path-in-a-grid/) | 0-1 BFS |
| 11 | [LC 317: Shortest Distance Buildings](https://leetcode.com/problems/shortest-distance-from-all-buildings/) | Multi-source |
| 12 | [LC 1162: As Far from Land](https://leetcode.com/problems/as-far-from-land-as-possible/) | Multi-source |
| 13 | [LC 1926: Nearest Exit Maze](https://leetcode.com/problems/nearest-exit-from-entrance-in-maze/) | BFS |
| 14 | [LC 2577: Min Time Grid](https://leetcode.com/problems/minimum-time-to-visit-a-cell-in-a-grid/) | Modified Dijkstra |
| 15 | [LC 785: Is Graph Bipartite](https://leetcode.com/problems/is-graph-bipartite/) | Bipartite check |

---

*Last Updated: 2024*
