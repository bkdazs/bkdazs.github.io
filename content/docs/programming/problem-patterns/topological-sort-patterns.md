---
title: "Topological Sort Patterns"
topic: "Problem Patterns"
difficulty: "Medium"
tags: ["topological-sort", "dag", "ordering", "cycle-detection", "course-schedule"]
status: "complete"
weight: 8
---

# Topological Sort Patterns

## 📚 Summary

Topological Sort orders vertices of a DAG (Directed Acyclic Graph) such that for every edge (u, v), u comes before v. Two main algorithms: Kahn's (BFS) and DFS-based.

---

## 1️⃣ Kahn's Algorithm (BFS)

```python
from collections import deque, defaultdict

def topological_sort_kahn(n: int, edges: list[list[int]]) -> list[int]:
    """
    BFS-based topological sort using in-degree
    Returns empty list if cycle exists
    
    Time: O(V + E), Space: O(V)
    """
    # Build graph and compute in-degrees
    graph = defaultdict(list)
    in_degree = [0] * n
    
    for u, v in edges:
        graph[u].append(v)
        in_degree[v] += 1
    
    # Start with nodes having in-degree 0
    queue = deque([i for i in range(n) if in_degree[i] == 0])
    result = []
    
    while queue:
        node = queue.popleft()
        result.append(node)
        
        for neighbor in graph[node]:
            in_degree[neighbor] -= 1
            if in_degree[neighbor] == 0:
                queue.append(neighbor)
    
    # Check for cycle
    return result if len(result) == n else []
```

---

## 2️⃣ DFS-Based Topological Sort

```python
def topological_sort_dfs(n: int, edges: list[list[int]]) -> list[int]:
    """
    DFS-based topological sort
    Uses finish time ordering (reverse post-order)
    
    Time: O(V + E), Space: O(V)
    """
    graph = defaultdict(list)
    for u, v in edges:
        graph[u].append(v)
    
    WHITE, GRAY, BLACK = 0, 1, 2
    color = [WHITE] * n
    result = []
    has_cycle = [False]
    
    def dfs(node: int):
        if has_cycle[0]:
            return
        
        color[node] = GRAY
        
        for neighbor in graph[node]:
            if color[neighbor] == GRAY:
                has_cycle[0] = True
                return
            if color[neighbor] == WHITE:
                dfs(neighbor)
        
        color[node] = BLACK
        result.append(node)
    
    for i in range(n):
        if color[i] == WHITE:
            dfs(i)
    
    return result[::-1] if not has_cycle[0] else []
```

---

## 3️⃣ Course Schedule Problems

### Can Finish All Courses (LC 207)

```python
def can_finish(num_courses: int, prerequisites: list[list[int]]) -> bool:
    """
    Check if all courses can be finished (no cycle)
    """
    graph = defaultdict(list)
    in_degree = [0] * num_courses
    
    for course, prereq in prerequisites:
        graph[prereq].append(course)
        in_degree[course] += 1
    
    queue = deque([i for i in range(num_courses) if in_degree[i] == 0])
    count = 0
    
    while queue:
        node = queue.popleft()
        count += 1
        
        for neighbor in graph[node]:
            in_degree[neighbor] -= 1
            if in_degree[neighbor] == 0:
                queue.append(neighbor)
    
    return count == num_courses
```

### Course Schedule II - Find Order (LC 210)

```python
def find_order(num_courses: int, prerequisites: list[list[int]]) -> list[int]:
    """
    Return valid course order or empty if impossible
    """
    graph = defaultdict(list)
    in_degree = [0] * num_courses
    
    for course, prereq in prerequisites:
        graph[prereq].append(course)
        in_degree[course] += 1
    
    queue = deque([i for i in range(num_courses) if in_degree[i] == 0])
    order = []
    
    while queue:
        course = queue.popleft()
        order.append(course)
        
        for next_course in graph[course]:
            in_degree[next_course] -= 1
            if in_degree[next_course] == 0:
                queue.append(next_course)
    
    return order if len(order) == num_courses else []
```

### Parallel Course Schedule (LC 1136)

```python
def minimum_semesters(n: int, relations: list[list[int]]) -> int:
    """
    Minimum semesters to finish all courses
    = Longest path in DAG
    """
    graph = defaultdict(list)
    in_degree = [0] * (n + 1)
    
    for prev_course, next_course in relations:
        graph[prev_course].append(next_course)
        in_degree[next_course] += 1
    
    queue = deque([i for i in range(1, n + 1) if in_degree[i] == 0])
    semesters = 0
    courses_taken = 0
    
    while queue:
        semesters += 1
        # Process all courses available this semester
        for _ in range(len(queue)):
            course = queue.popleft()
            courses_taken += 1
            
            for next_course in graph[course]:
                in_degree[next_course] -= 1
                if in_degree[next_course] == 0:
                    queue.append(next_course)
    
    return semesters if courses_taken == n else -1
```

---

## 4️⃣ Alien Dictionary (LC 269)

```python
def alien_order(words: list[str]) -> str:
    """
    Derive alphabet order from sorted alien words
    """
    # Build graph
    graph = defaultdict(set)
    in_degree = {c: 0 for word in words for c in word}
    
    for i in range(len(words) - 1):
        w1, w2 = words[i], words[i + 1]
        min_len = min(len(w1), len(w2))
        
        # Check for invalid case: "abc" before "ab"
        if len(w1) > len(w2) and w1[:min_len] == w2[:min_len]:
            return ""
        
        # Find first difference
        for j in range(min_len):
            if w1[j] != w2[j]:
                if w2[j] not in graph[w1[j]]:
                    graph[w1[j]].add(w2[j])
                    in_degree[w2[j]] += 1
                break
    
    # Topological sort
    queue = deque([c for c in in_degree if in_degree[c] == 0])
    result = []
    
    while queue:
        char = queue.popleft()
        result.append(char)
        
        for neighbor in graph[char]:
            in_degree[neighbor] -= 1
            if in_degree[neighbor] == 0:
                queue.append(neighbor)
    
    return "".join(result) if len(result) == len(in_degree) else ""
```

---

## 5️⃣ All Topological Orders

```python
def all_topological_sorts(n: int, edges: list[list[int]]) -> list[list[int]]:
    """
    Find all valid topological orderings
    Uses backtracking
    """
    graph = defaultdict(list)
    in_degree = [0] * n
    
    for u, v in edges:
        graph[u].append(v)
        in_degree[v] += 1
    
    result = []
    
    def backtrack(path: list[int], visited: list[bool]):
        if len(path) == n:
            result.append(path[:])
            return
        
        for node in range(n):
            if not visited[node] and in_degree[node] == 0:
                # Choose
                visited[node] = True
                path.append(node)
                for neighbor in graph[node]:
                    in_degree[neighbor] -= 1
                
                # Explore
                backtrack(path, visited)
                
                # Unchoose
                visited[node] = False
                path.pop()
                for neighbor in graph[node]:
                    in_degree[neighbor] += 1
    
    backtrack([], [False] * n)
    return result
```

---

## 6️⃣ Longest Path in DAG

```python
def longest_path_dag(n: int, edges: list[list[int]]) -> int:
    """
    Find longest path in DAG
    Use topological sort then DP
    """
    graph = defaultdict(list)
    in_degree = [0] * n
    
    for u, v in edges:
        graph[u].append(v)
        in_degree[v] += 1
    
    # Topological sort
    queue = deque([i for i in range(n) if in_degree[i] == 0])
    topo_order = []
    
    while queue:
        node = queue.popleft()
        topo_order.append(node)
        for neighbor in graph[node]:
            in_degree[neighbor] -= 1
            if in_degree[neighbor] == 0:
                queue.append(neighbor)
    
    # DP for longest path
    dist = [0] * n
    
    for node in topo_order:
        for neighbor in graph[node]:
            dist[neighbor] = max(dist[neighbor], dist[node] + 1)
    
    return max(dist)


def longest_path_weighted_dag(n: int, edges: list[tuple[int, int, int]]) -> list[int]:
    """
    Longest path in weighted DAG
    edges: [(u, v, weight), ...]
    """
    graph = defaultdict(list)
    in_degree = [0] * n
    
    for u, v, w in edges:
        graph[u].append((v, w))
        in_degree[v] += 1
    
    queue = deque([i for i in range(n) if in_degree[i] == 0])
    topo_order = []
    
    while queue:
        node = queue.popleft()
        topo_order.append(node)
        for neighbor, _ in graph[node]:
            in_degree[neighbor] -= 1
            if in_degree[neighbor] == 0:
                queue.append(neighbor)
    
    dist = [float('-inf')] * n
    # Initialize source nodes
    for node in topo_order:
        if dist[node] == float('-inf'):
            dist[node] = 0
    
    for node in topo_order:
        if dist[node] != float('-inf'):
            for neighbor, weight in graph[node]:
                dist[neighbor] = max(dist[neighbor], dist[node] + weight)
    
    return dist
```

---

## 7️⃣ Build Order with Dependencies

### Task Scheduler with Dependencies

```python
def get_build_order(projects: list[str], dependencies: list[tuple[str, str]]) -> list[str]:
    """
    Return valid build order given (dependency, project) pairs
    dependency must be built before project
    """
    graph = defaultdict(list)
    in_degree = {p: 0 for p in projects}
    
    for dep, proj in dependencies:
        graph[dep].append(proj)
        in_degree[proj] += 1
    
    queue = deque([p for p in projects if in_degree[p] == 0])
    result = []
    
    while queue:
        project = queue.popleft()
        result.append(project)
        
        for dependent in graph[project]:
            in_degree[dependent] -= 1
            if in_degree[dependent] == 0:
                queue.append(dependent)
    
    return result if len(result) == len(projects) else []
```

### Sequence Reconstruction (LC 444)

```python
def sequence_reconstruction(nums: list[int], sequences: list[list[int]]) -> bool:
    """
    Check if nums is the only supersequence of sequences
    """
    n = len(nums)
    graph = defaultdict(set)
    in_degree = defaultdict(int)
    
    # Initialize all numbers
    for num in nums:
        in_degree[num] = 0
    
    # Build graph from sequences
    for seq in sequences:
        for i in range(len(seq) - 1):
            if seq[i + 1] not in graph[seq[i]]:
                graph[seq[i]].add(seq[i + 1])
                in_degree[seq[i + 1]] += 1
    
    # Topological sort - should have exactly one choice at each step
    queue = deque([num for num in nums if in_degree[num] == 0])
    result = []
    
    while queue:
        if len(queue) > 1:
            return False  # Multiple valid orderings
        
        node = queue.popleft()
        result.append(node)
        
        for neighbor in graph[node]:
            in_degree[neighbor] -= 1
            if in_degree[neighbor] == 0:
                queue.append(neighbor)
    
    return result == nums
```

---

## 8️⃣ Detect Cycle in Directed Graph

```python
def has_cycle(n: int, edges: list[list[int]]) -> bool:
    """
    Detect cycle using topological sort
    If topo sort doesn't include all nodes, cycle exists
    """
    graph = defaultdict(list)
    in_degree = [0] * n
    
    for u, v in edges:
        graph[u].append(v)
        in_degree[v] += 1
    
    queue = deque([i for i in range(n) if in_degree[i] == 0])
    count = 0
    
    while queue:
        node = queue.popleft()
        count += 1
        
        for neighbor in graph[node]:
            in_degree[neighbor] -= 1
            if in_degree[neighbor] == 0:
                queue.append(neighbor)
    
    return count != n


def find_cycle_dfs(n: int, edges: list[list[int]]) -> list[int]:
    """
    Find and return a cycle if exists
    """
    graph = defaultdict(list)
    for u, v in edges:
        graph[u].append(v)
    
    WHITE, GRAY, BLACK = 0, 1, 2
    color = [WHITE] * n
    parent = [-1] * n
    cycle = []
    
    def dfs(node: int) -> bool:
        color[node] = GRAY
        
        for neighbor in graph[node]:
            if color[neighbor] == GRAY:
                # Found cycle, reconstruct
                cycle.append(neighbor)
                curr = node
                while curr != neighbor:
                    cycle.append(curr)
                    curr = parent[curr]
                cycle.append(neighbor)
                return True
            
            if color[neighbor] == WHITE:
                parent[neighbor] = node
                if dfs(neighbor):
                    return True
        
        color[node] = BLACK
        return False
    
    for i in range(n):
        if color[i] == WHITE:
            if dfs(i):
                return cycle[::-1]
    
    return []
```

---

## 9️⃣ Special Cases

### Lexicographically Smallest Topo Sort

```python
import heapq

def smallest_topo_sort(n: int, edges: list[list[int]]) -> list[int]:
    """
    Return lexicographically smallest topological ordering
    Use min-heap instead of queue
    """
    graph = defaultdict(list)
    in_degree = [0] * n
    
    for u, v in edges:
        graph[u].append(v)
        in_degree[v] += 1
    
    heap = [i for i in range(n) if in_degree[i] == 0]
    heapq.heapify(heap)
    result = []
    
    while heap:
        node = heapq.heappop(heap)
        result.append(node)
        
        for neighbor in graph[node]:
            in_degree[neighbor] -= 1
            if in_degree[neighbor] == 0:
                heapq.heappush(heap, neighbor)
    
    return result if len(result) == n else []
```

### Sort Items by Groups (LC 1203)

```python
def sort_items(n: int, m: int, group: list[int], before_items: list[list[int]]) -> list[int]:
    """
    Sort items respecting both group and item dependencies
    """
    # Assign unique groups to ungrouped items
    group_id = m
    for i in range(n):
        if group[i] == -1:
            group[i] = group_id
            group_id += 1
    
    # Build item and group graphs
    item_graph = defaultdict(list)
    item_indegree = [0] * n
    group_graph = defaultdict(list)
    group_indegree = defaultdict(int)
    
    for item in range(n):
        for before in before_items[item]:
            item_graph[before].append(item)
            item_indegree[item] += 1
            
            if group[before] != group[item]:
                group_graph[group[before]].append(group[item])
                group_indegree[group[item]] += 1
    
    def topo_sort(nodes, graph, indegree):
        queue = deque([n for n in nodes if indegree[n] == 0])
        result = []
        while queue:
            node = queue.popleft()
            result.append(node)
            for neighbor in graph[node]:
                indegree[neighbor] -= 1
                if indegree[neighbor] == 0:
                    queue.append(neighbor)
        return result if len(result) == len(nodes) else []
    
    # Sort groups
    all_groups = set(group)
    group_order = topo_sort(all_groups, group_graph, group_indegree)
    if not group_order:
        return []
    
    # Group items by group
    group_items = defaultdict(list)
    for item in range(n):
        group_items[group[item]].append(item)
    
    # Sort items within each group
    result = []
    for g in group_order:
        items = group_items[g]
        sorted_items = topo_sort(items, item_graph, 
                                 {i: item_indegree[i] for i in items})
        if len(sorted_items) != len(items):
            return []
        result.extend(sorted_items)
    
    return result
```

---

## ⏱️ Complexity

All topological sort algorithms: **O(V + E)** time, **O(V)** space

---

## 📚 Practice Problems

| # | Problem | Key Concept |
|---|---------|-------------|
| 1 | [LC 207: Course Schedule](https://leetcode.com/problems/course-schedule/) | Cycle detection |
| 2 | [LC 210: Course Schedule II](https://leetcode.com/problems/course-schedule-ii/) | Find order |
| 3 | [LC 269: Alien Dictionary](https://leetcode.com/problems/alien-dictionary/) | Build graph |
| 4 | [LC 310: Min Height Trees](https://leetcode.com/problems/minimum-height-trees/) | Center finding |
| 5 | [LC 444: Sequence Reconstruction](https://leetcode.com/problems/sequence-reconstruction/) | Unique order |
| 6 | [LC 802: Find Safe States](https://leetcode.com/problems/find-eventual-safe-states/) | Reverse topo |
| 7 | [LC 1136: Parallel Courses](https://leetcode.com/problems/parallel-courses/) | Level BFS |
| 8 | [LC 1203: Sort Items by Groups](https://leetcode.com/problems/sort-items-by-groups-respecting-dependencies/) | Two-level |
| 9 | [LC 1462: Course Schedule IV](https://leetcode.com/problems/course-schedule-iv/) | Reachability |
| 10 | [LC 2115: Find All Recipes](https://leetcode.com/problems/find-all-possible-recipes-from-given-supplies/) | Dependencies |
| 11 | [LC 329: Longest Increasing Path](https://leetcode.com/problems/longest-increasing-path-in-a-matrix/) | DAG + DP |
| 12 | [LC 2050: Parallel Courses III](https://leetcode.com/problems/parallel-courses-iii/) | Longest path |
| 13 | [LC 1857: Largest Color in Graph](https://leetcode.com/problems/largest-color-value-in-a-directed-graph/) | Topo + DP |
| 14 | [LC 2392: Build Matrix](https://leetcode.com/problems/build-a-matrix-with-conditions/) | Two dimensions |
| 15 | [LC 1059: All Paths Source Lead](https://leetcode.com/problems/all-paths-from-source-lead-to-destination/) | DFS verify |

---

*Last Updated: 2024*
