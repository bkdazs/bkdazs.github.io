---
title: Constrained Path DP
topic: Algorithms - Dynamic Programming
difficulty: Hard
tags: [dp, path, constraints, graph-dp, state-space]
status: complete
weight: 23
---

# Constrained Path DP

## 1. Overview

This document covers DP approaches for path problems with additional constraints:
- Shortest/longest path with resource limits
- Path counting with state constraints
- Graph traversal with fuel/budget limits
- Multi-criteria path optimization

---

## 2. Core Concepts

### Problem Classification

```
Constrained Path Problems:
├── Resource Constraints
│   ├── Limited Fuel/Budget
│   ├── Maximum Edge Usage
│   └── Capacity Constraints
├── State Constraints
│   ├── Visited Node Tracking
│   ├── Path Properties
│   └── History-Dependent Transitions
├── Multi-Criteria
│   ├── Pareto-Optimal Paths
│   ├── Cost + Time Optimization
│   └── Bi-Objective Shortest Path
└── Graph Structure
    ├── Grid with Obstacles
    ├── DAG with Constraints
    └── General Graph + DP State
```

---

## 3. Shortest Path with Budget Constraint

### CSLP: Constrained Shortest/Longest Path

```python
from typing import List, Tuple, Dict
from collections import defaultdict
import heapq

def shortest_path_with_budget(
    n: int,
    edges: List[Tuple[int, int, int, int]],  # (u, v, cost, reward)
    start: int,
    end: int,
    budget: int
) -> int:
    """
    Find shortest path from start to end spending at most 'budget'.
    
    Args:
        edges: (from, to, cost, time) - cost to traverse, time taken
        budget: Maximum cost allowed
    
    Returns:
        Minimum time to reach end within budget, or -1 if impossible
    
    Time: O(n × budget × log(n × budget))
    Space: O(n × budget)
    """
    # Build adjacency list
    graph = defaultdict(list)
    for u, v, cost, time in edges:
        graph[u].append((v, cost, time))
    
    # dp[node][remaining_budget] = min time to reach node
    INF = float('inf')
    dp = [[INF] * (budget + 1) for _ in range(n)]
    dp[start][budget] = 0
    
    # Priority queue: (time, node, remaining_budget)
    pq = [(0, start, budget)]
    
    while pq:
        time, node, remaining = heapq.heappop(pq)
        
        if node == end:
            return time
        
        if time > dp[node][remaining]:
            continue
        
        for neighbor, cost, edge_time in graph[node]:
            if cost <= remaining:
                new_remaining = remaining - cost
                new_time = time + edge_time
                
                if new_time < dp[neighbor][new_remaining]:
                    dp[neighbor][new_remaining] = new_time
                    heapq.heappush(pq, (new_time, neighbor, new_remaining))
    
    # Check if end is reachable with any budget
    min_time = min(dp[end])
    return min_time if min_time < INF else -1


def cheapest_flights_k_stops(
    n: int,
    flights: List[List[int]],
    src: int,
    dst: int,
    k: int
) -> int:
    """
    LeetCode 787: Cheapest Flights Within K Stops
    
    Find cheapest price from src to dst with at most k stops.
    
    Time: O(k × |E|) using Bellman-Ford variant
    Space: O(n)
    """
    INF = float('inf')
    
    # dp[i] = min cost to reach node i
    dp = [INF] * n
    dp[src] = 0
    
    for _ in range(k + 1):  # k stops = k+1 edges
        temp = dp[:]
        for u, v, price in flights:
            if dp[u] < INF:
                temp[v] = min(temp[v], dp[u] + price)
        dp = temp
    
    return dp[dst] if dp[dst] < INF else -1
```

---

## 4. Path with Fuel Constraints

### LeetCode 1654: Minimum Jumps to Reach Home

```python
def minimum_jumps(
    forbidden: List[int],
    a: int,
    b: int,
    x: int
) -> int:
    """
    LeetCode 1654: Minimum Jumps to Reach Home
    
    Can jump forward 'a' or backward 'b' (can't go backward twice in a row).
    
    State: (position, last_move_was_back)
    """
    from collections import deque
    
    forbidden_set = set(forbidden)
    
    # Upper bound: x + a + b (need room to maneuver)
    max_pos = max(max(forbidden) if forbidden else 0, x) + a + b
    
    # BFS with state (pos, was_back)
    # was_back: True if last move was backward
    visited = set()
    queue = deque([(0, False, 0)])  # (position, was_back, jumps)
    visited.add((0, False))
    
    while queue:
        pos, was_back, jumps = queue.popleft()
        
        if pos == x:
            return jumps
        
        # Forward jump
        forward = pos + a
        if forward <= max_pos and forward not in forbidden_set:
            if (forward, False) not in visited:
                visited.add((forward, False))
                queue.append((forward, False, jumps + 1))
        
        # Backward jump (only if last move wasn't backward)
        if not was_back:
            backward = pos - b
            if backward > 0 and backward not in forbidden_set:
                if (backward, True) not in visited:
                    visited.add((backward, True))
                    queue.append((backward, True, jumps + 1))
    
    return -1


def minimum_fuel_to_reach_capital(
    roads: List[List[int]],
    seats: int
) -> int:
    """
    LeetCode 2477: Minimum Fuel to Report to Capital
    
    Each city has one representative. Cars have 'seats' capacity.
    Minimize total fuel to get everyone to city 0.
    """
    from collections import defaultdict
    
    n = len(roads) + 1
    if n == 1:
        return 0
    
    graph = defaultdict(list)
    for u, v in roads:
        graph[u].append(v)
        graph[v].append(u)
    
    total_fuel = 0
    
    def dfs(node: int, parent: int) -> int:
        """Returns number of people moving through this edge to parent."""
        nonlocal total_fuel
        
        people = 1  # Current node's representative
        
        for neighbor in graph[node]:
            if neighbor != parent:
                people += dfs(neighbor, node)
        
        # How many cars needed to transport 'people' to parent?
        if node != 0:
            cars = (people + seats - 1) // seats  # Ceiling division
            total_fuel += cars
        
        return people
    
    dfs(0, -1)
    return total_fuel
```

---

## 5. Grid Path with Constraints

### Path with Obstacle Removal

```python
from collections import deque

def shortest_path_with_obstacle_removal(
    grid: List[List[int]],
    k: int
) -> int:
    """
    LeetCode 1293: Shortest Path in Grid with Obstacle Elimination
    
    Find shortest path from top-left to bottom-right.
    Can eliminate at most k obstacles.
    
    State: (row, col, obstacles_remaining)
    
    Time: O(m × n × k)
    Space: O(m × n × k)
    """
    m, n = len(grid), len(grid[0])
    
    if k >= m + n - 3:
        return m + n - 2  # Can eliminate all obstacles
    
    # BFS with state (r, c, remaining_k)
    visited = set()
    queue = deque([(0, 0, k, 0)])  # (row, col, k_remaining, distance)
    visited.add((0, 0, k))
    
    while queue:
        r, c, remaining, dist = queue.popleft()
        
        if r == m - 1 and c == n - 1:
            return dist
        
        for dr, dc in [(-1, 0), (1, 0), (0, -1), (0, 1)]:
            nr, nc = r + dr, c + dc
            
            if 0 <= nr < m and 0 <= nc < n:
                new_remaining = remaining - grid[nr][nc]
                
                if new_remaining >= 0 and (nr, nc, new_remaining) not in visited:
                    visited.add((nr, nc, new_remaining))
                    queue.append((nr, nc, new_remaining, dist + 1))
    
    return -1


def min_cost_path_with_direction_changes(
    grid: List[List[int]]
) -> int:
    """
    LeetCode 1368: Minimum Cost to Make at Least One Valid Path
    
    Grid has directions (1=right, 2=left, 3=down, 4=up).
    Cost 1 to change a cell's direction.
    Find min cost to reach bottom-right.
    
    Uses 0-1 BFS.
    """
    m, n = len(grid), len(grid[0])
    directions = {1: (0, 1), 2: (0, -1), 3: (1, 0), 4: (-1, 0)}
    all_dirs = [(0, 1), (0, -1), (1, 0), (-1, 0)]
    
    INF = float('inf')
    dist = [[INF] * n for _ in range(m)]
    dist[0][0] = 0
    
    # 0-1 BFS: deque, add cost 0 at front, cost 1 at back
    dq = deque([(0, 0)])
    
    while dq:
        r, c = dq.popleft()
        
        for i, (dr, dc) in enumerate(all_dirs, 1):
            nr, nc = r + dr, c + dc
            
            if 0 <= nr < m and 0 <= nc < n:
                # Cost 0 if grid direction matches, else 1
                cost = 0 if grid[r][c] == i else 1
                
                if dist[r][c] + cost < dist[nr][nc]:
                    dist[nr][nc] = dist[r][c] + cost
                    if cost == 0:
                        dq.appendleft((nr, nc))
                    else:
                        dq.append((nr, nc))
    
    return dist[m - 1][n - 1]
```

---

## 6. Path Counting with Constraints

```python
def count_paths_with_length_constraint(
    n: int,
    edges: List[Tuple[int, int]],
    start: int,
    end: int,
    max_length: int
) -> int:
    """
    Count paths from start to end with at most max_length edges.
    
    dp[v][k] = number of paths to v using exactly k edges
    
    Time: O(max_length × |E|)
    """
    MOD = 10**9 + 7
    graph = defaultdict(list)
    for u, v in edges:
        graph[u].append(v)
    
    # dp[v] = number of paths to v with current number of edges
    dp = [0] * n
    dp[start] = 1
    
    total_paths = 0
    if start == end:
        total_paths = 1
    
    for length in range(1, max_length + 1):
        new_dp = [0] * n
        for u in range(n):
            if dp[u] > 0:
                for v in graph[u]:
                    new_dp[v] = (new_dp[v] + dp[u]) % MOD
        dp = new_dp
        total_paths = (total_paths + dp[end]) % MOD
    
    return total_paths


def count_restricted_paths(n: int, edges: List[List[int]]) -> int:
    """
    LeetCode 1786: Number of Restricted Paths
    
    Path from 1 to n where each step goes to node with smaller
    distance to n.
    """
    from collections import defaultdict
    import heapq
    
    MOD = 10**9 + 7
    
    # Build graph
    graph = defaultdict(list)
    for u, v, w in edges:
        graph[u].append((v, w))
        graph[v].append((u, w))
    
    # Dijkstra from n to get distances
    INF = float('inf')
    dist = [INF] * (n + 1)
    dist[n] = 0
    pq = [(0, n)]
    
    while pq:
        d, u = heapq.heappop(pq)
        if d > dist[u]:
            continue
        for v, w in graph[u]:
            if dist[u] + w < dist[v]:
                dist[v] = dist[u] + w
                heapq.heappush(pq, (dist[v], v))
    
    # DP: count paths where we always decrease distance
    # Process nodes in increasing order of distance to n
    nodes = sorted(range(1, n + 1), key=lambda x: dist[x])
    
    dp = [0] * (n + 1)
    dp[n] = 1
    
    for u in nodes:
        for v, _ in graph[u]:
            if dist[v] < dist[u]:  # Restricted condition
                dp[u] = (dp[u] + dp[v]) % MOD
    
    return dp[1]
```

---

## 7. Multi-Criteria Path Optimization

```python
def pareto_optimal_paths(
    n: int,
    edges: List[Tuple[int, int, int, int]],  # (u, v, cost, time)
    start: int,
    end: int
) -> List[Tuple[int, int]]:
    """
    Find all Pareto-optimal (cost, time) pairs for paths from start to end.
    
    A path is Pareto-optimal if no other path is better in both cost and time.
    """
    graph = defaultdict(list)
    for u, v, cost, time in edges:
        graph[u].append((v, cost, time))
    
    # BFS/DFS tracking (cost, time) pairs at each node
    # Use set of non-dominated pairs
    
    def is_dominated(pair: Tuple[int, int], others: set) -> bool:
        cost, time = pair
        for other_cost, other_time in others:
            if other_cost <= cost and other_time <= time:
                if other_cost < cost or other_time < time:
                    return True
        return False
    
    def add_pair(pair: Tuple[int, int], pairs: set) -> bool:
        """Add pair if not dominated, remove pairs it dominates."""
        if is_dominated(pair, pairs):
            return False
        
        # Remove dominated pairs
        to_remove = set()
        for other in pairs:
            if is_dominated(other, {pair}):
                to_remove.add(other)
        pairs -= to_remove
        pairs.add(pair)
        return True
    
    # Dijkstra-like exploration
    # State: (node, (cost, time))
    pareto = defaultdict(set)
    pareto[start].add((0, 0))
    
    # Priority queue by cost+time (heuristic)
    pq = [(0, start, 0, 0)]  # (priority, node, cost, time)
    
    while pq:
        _, node, cost, time = heapq.heappop(pq)
        
        if (cost, time) not in pareto[node]:
            continue
        
        for neighbor, edge_cost, edge_time in graph[node]:
            new_cost = cost + edge_cost
            new_time = time + edge_time
            
            if add_pair((new_cost, new_time), pareto[neighbor]):
                priority = new_cost + new_time
                heapq.heappush(pq, (priority, neighbor, new_cost, new_time))
    
    return list(pareto[end])


def shortest_path_visiting_all_nodes(graph: List[List[int]]) -> int:
    """
    LeetCode 847: Shortest Path Visiting All Nodes
    
    Find shortest path that visits every node.
    
    State: (current_node, visited_mask)
    """
    n = len(graph)
    target = (1 << n) - 1
    
    # BFS
    queue = deque()
    visited = set()
    
    # Start from each node
    for i in range(n):
        state = (i, 1 << i)
        queue.append((*state, 0))  # (node, mask, dist)
        visited.add(state)
    
    while queue:
        node, mask, dist = queue.popleft()
        
        if mask == target:
            return dist
        
        for neighbor in graph[node]:
            new_mask = mask | (1 << neighbor)
            state = (neighbor, new_mask)
            
            if state not in visited:
                visited.add(state)
                queue.append((neighbor, new_mask, dist + 1))
    
    return -1
```

---

## 8. Constrained Path in Special Graphs

```python
def shortest_path_in_weighted_grid_with_traps(
    grid: List[List[int]],
    traps: List[Tuple[int, int, int]]  # (row, col, damage)
) -> int:
    """
    Shortest path with health constraint.
    
    Must have health > 0 at all times.
    """
    m, n = len(grid), len(grid[0])
    
    # Calculate max possible damage
    max_damage = sum(d for _, _, d in traps)
    trap_map = {(r, c): d for r, c, d in traps}
    
    # Binary search on initial health needed
    def can_reach(health: int) -> bool:
        # BFS with health tracking
        visited = {}  # (r, c) -> max health remaining
        queue = deque([(0, 0, health)])
        visited[(0, 0)] = health
        
        while queue:
            r, c, h = queue.popleft()
            
            if r == m - 1 and c == n - 1:
                return True
            
            for dr, dc in [(-1, 0), (1, 0), (0, -1), (0, 1)]:
                nr, nc = r + dr, c + dc
                
                if 0 <= nr < m and 0 <= nc < n:
                    new_h = h - trap_map.get((nr, nc), 0)
                    
                    if new_h > 0:
                        if (nr, nc) not in visited or visited[(nr, nc)] < new_h:
                            visited[(nr, nc)] = new_h
                            queue.append((nr, nc, new_h))
        
        return False
    
    # Binary search
    lo, hi = 1, max_damage + 1
    while lo < hi:
        mid = (lo + hi) // 2
        if can_reach(mid):
            hi = mid
        else:
            lo = mid + 1
    
    return lo if can_reach(lo) else -1


def dungeon_game(dungeon: List[List[int]]) -> int:
    """
    LeetCode 174: Dungeon Game
    
    Minimum initial health to reach bottom-right with health > 0 always.
    
    DP from end to start.
    """
    m, n = len(dungeon), len(dungeon[0])
    
    # dp[i][j] = min health needed to reach end from (i, j)
    INF = float('inf')
    dp = [[INF] * (n + 1) for _ in range(m + 1)]
    dp[m][n - 1] = dp[m - 1][n] = 1  # Need at least 1 health at end
    
    for i in range(m - 1, -1, -1):
        for j in range(n - 1, -1, -1):
            min_health_after = min(dp[i + 1][j], dp[i][j + 1])
            dp[i][j] = max(1, min_health_after - dungeon[i][j])
    
    return dp[0][0]
```

---

## 9. Practice Problems

### LeetCode Problems

| # | Problem | Constraint Type | Difficulty |
|---|---------|-----------------|------------|
| 174 | [Dungeon Game](https://leetcode.com/problems/dungeon-game/) | Health | Hard |
| 787 | [Cheapest Flights K Stops](https://leetcode.com/problems/cheapest-flights-within-k-stops/) | Stops | Medium |
| 847 | [Shortest Path Visiting All](https://leetcode.com/problems/shortest-path-visiting-all-nodes/) | Visit All | Hard |
| 1129 | [Shortest Path Alternating](https://leetcode.com/problems/shortest-path-with-alternating-colors/) | Color | Medium |
| 1293 | [Shortest Path Obstacle Elim](https://leetcode.com/problems/shortest-path-in-a-grid-with-obstacles-elimination/) | Obstacles | Hard |
| 1368 | [Min Cost Valid Path](https://leetcode.com/problems/minimum-cost-to-make-at-least-one-valid-path-in-a-grid/) | Direction | Hard |
| 1654 | [Minimum Jumps Home](https://leetcode.com/problems/minimum-jumps-to-reach-home/) | Direction | Medium |
| 1786 | [Restricted Paths](https://leetcode.com/problems/number-of-restricted-paths-from-first-to-last-node/) | Decreasing | Medium |
| 2477 | [Min Fuel to Capital](https://leetcode.com/problems/minimum-fuel-to-report-to-capital/) | Capacity | Medium |

---

## 10. Key Patterns Summary

```
Constrained Path DP Decision Tree:

1. Resource limit (budget/fuel)?
   → Add resource as DP dimension: dp[node][resource]
   → Use Dijkstra with (node, resource) as state

2. Step/stop limit?
   → Bellman-Ford variant (k iterations)
   → BFS with step counter

3. Must visit all nodes?
   → Bitmask for visited: dp[node][visited_mask]

4. Health/survival constraint?
   → Work backwards: dp[i][j] = min health to survive
   → Binary search on initial health

5. Direction/history constraint?
   → Add history to state: (pos, last_direction)
   → 0-1 BFS for mixed cost edges

6. Multi-objective optimization?
   → Track Pareto frontier of (cost1, cost2) pairs
   → Cannot reduce to single objective

Complexity:
- With budget B: O(n × B × log(n × B))
- With k steps: O(k × |E|)
- With bitmask: O(2^n × n²)
- Pareto-optimal: Can be exponential in worst case
```
