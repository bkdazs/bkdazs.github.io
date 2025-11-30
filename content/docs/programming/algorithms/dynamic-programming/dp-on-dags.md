---
title: DP on DAGs / Topological DP
topic: Algorithms - Dynamic Programming
difficulty: Medium-Hard
tags: [dp, dag, topological-sort, longest-path, shortest-path]
status: complete
weight: 14
---

# DP on DAGs / Topological DP

## 1. Overview

### Core Concept
Any DP problem can be viewed as finding paths in a DAG of states. When the underlying graph structure is explicit (DAG), we can use topological ordering to solve DP efficiently.

### Key Properties
| Property | Description |
|----------|-------------|
| No cycles | DAG guarantees valid DP ordering |
| Topological order | Process nodes in order where dependencies come first |
| Time complexity | O(V + E) for single-source problems |
| Applications | Longest/shortest paths, counting paths, scheduling |

---

## 2. Longest Path in DAG

### Basic Template

```python
from typing import List, Dict
from collections import defaultdict, deque

def longest_path_dag(n: int, edges: List[tuple]) -> int:
    """
    Find longest path in DAG.
    
    Time: O(V + E)
    Space: O(V + E)
    
    Args:
        n: Number of nodes (0 to n-1)
        edges: List of (u, v, weight) tuples
    
    Returns:
        Length of longest path
    """
    # Build adjacency list and compute in-degrees
    graph = defaultdict(list)
    in_degree = [0] * n
    
    for u, v, w in edges:
        graph[u].append((v, w))
        in_degree[v] += 1
    
    # Topological sort using Kahn's algorithm
    queue = deque([i for i in range(n) if in_degree[i] == 0])
    topo_order = []
    
    while queue:
        node = queue.popleft()
        topo_order.append(node)
        for neighbor, _ in graph[node]:
            in_degree[neighbor] -= 1
            if in_degree[neighbor] == 0:
                queue.append(neighbor)
    
    # DP: longest path ending at each node
    dp = [-float('inf')] * n
    
    # Initialize source nodes
    for i in range(n):
        if all(i not in [v for v, _ in graph[j]] for j in range(n) if j != i):
            # Check if node has no incoming edges initially
            pass
    
    # For nodes with no incoming edges, set dp = 0
    for node in topo_order:
        if dp[node] == -float('inf'):
            dp[node] = 0
    
    # Process in topological order
    for node in topo_order:
        for neighbor, weight in graph[node]:
            dp[neighbor] = max(dp[neighbor], dp[node] + weight)
    
    return max(dp)


# Alternative: Single source longest path
def longest_path_from_source(n: int, edges: List[tuple], source: int) -> List[int]:
    """
    Longest path from single source in DAG.
    
    Returns:
        dist[i] = longest path from source to i (-inf if unreachable)
    """
    graph = defaultdict(list)
    in_degree = [0] * n
    
    for u, v, w in edges:
        graph[u].append((v, w))
        in_degree[v] += 1
    
    # Topological sort
    queue = deque([i for i in range(n) if in_degree[i] == 0])
    topo_order = []
    
    while queue:
        node = queue.popleft()
        topo_order.append(node)
        for neighbor, _ in graph[node]:
            in_degree[neighbor] -= 1
            if in_degree[neighbor] == 0:
                queue.append(neighbor)
    
    # DP from source
    dist = [-float('inf')] * n
    dist[source] = 0
    
    for node in topo_order:
        if dist[node] != -float('inf'):
            for neighbor, weight in graph[node]:
                dist[neighbor] = max(dist[neighbor], dist[node] + weight)
    
    return dist


# Test
edges = [(0, 1, 5), (0, 2, 3), (1, 3, 6), (1, 2, 2), (2, 4, 4), (2, 5, 2), (2, 3, 7), (3, 5, 1), (3, 4, -1), (4, 5, -2)]
n = 6
print(longest_path_from_source(n, edges, 1))  # From node 1
```

---

## 3. Shortest Path in DAG

```python
def shortest_path_dag(n: int, edges: List[tuple], source: int) -> List[int]:
    """
    Shortest path from source in DAG.
    
    Time: O(V + E)
    
    Note: Works with negative weights (unlike Dijkstra)
    """
    graph = defaultdict(list)
    in_degree = [0] * n
    
    for u, v, w in edges:
        graph[u].append((v, w))
        in_degree[v] += 1
    
    # Topological sort
    queue = deque([i for i in range(n) if in_degree[i] == 0])
    topo_order = []
    
    while queue:
        node = queue.popleft()
        topo_order.append(node)
        for neighbor, _ in graph[node]:
            in_degree[neighbor] -= 1
            if in_degree[neighbor] == 0:
                queue.append(neighbor)
    
    # DP
    dist = [float('inf')] * n
    dist[source] = 0
    
    for node in topo_order:
        if dist[node] != float('inf'):
            for neighbor, weight in graph[node]:
                dist[neighbor] = min(dist[neighbor], dist[node] + weight)
    
    return dist
```

---

## 4. Counting Paths in DAG

### Count All Paths

```python
def count_paths_dag(n: int, edges: List[tuple], source: int, target: int) -> int:
    """
    LeetCode 797 - All Paths From Source to Target (variant)
    
    Count number of paths from source to target in DAG.
    
    Time: O(V + E)
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
    
    # DP: count[i] = number of paths from source to i
    count = [0] * n
    count[source] = 1
    
    for node in topo_order:
        for neighbor in graph[node]:
            count[neighbor] += count[node]
    
    return count[target]


def count_paths_with_mod(n: int, edges: List[tuple], source: int, target: int, MOD: int = 10**9 + 7) -> int:
    """
    Count paths modulo MOD to handle large counts.
    
    LeetCode 1976 - Number of Ways to Arrive at Destination (DAG variant)
    """
    graph = defaultdict(list)
    in_degree = [0] * n
    
    for u, v in edges:
        graph[u].append(v)
        in_degree[v] += 1
    
    queue = deque([i for i in range(n) if in_degree[i] == 0])
    topo_order = []
    
    while queue:
        node = queue.popleft()
        topo_order.append(node)
        for neighbor in graph[node]:
            in_degree[neighbor] -= 1
            if in_degree[neighbor] == 0:
                queue.append(neighbor)
    
    count = [0] * n
    count[source] = 1
    
    for node in topo_order:
        for neighbor in graph[node]:
            count[neighbor] = (count[neighbor] + count[node]) % MOD
    
    return count[target]
```

---

## 5. Course Schedule / Prerequisites Problems

### Can Finish Courses

```python
def can_finish(numCourses: int, prerequisites: List[List[int]]) -> bool:
    """
    LeetCode 207 - Course Schedule
    
    Check if all courses can be finished (no cycle in prerequisite DAG).
    
    Time: O(V + E)
    """
    graph = defaultdict(list)
    in_degree = [0] * numCourses
    
    for course, prereq in prerequisites:
        graph[prereq].append(course)
        in_degree[course] += 1
    
    queue = deque([i for i in range(numCourses) if in_degree[i] == 0])
    count = 0
    
    while queue:
        node = queue.popleft()
        count += 1
        for neighbor in graph[node]:
            in_degree[neighbor] -= 1
            if in_degree[neighbor] == 0:
                queue.append(neighbor)
    
    return count == numCourses


def find_order(numCourses: int, prerequisites: List[List[int]]) -> List[int]:
    """
    LeetCode 210 - Course Schedule II
    
    Return valid course order (topological sort).
    """
    graph = defaultdict(list)
    in_degree = [0] * numCourses
    
    for course, prereq in prerequisites:
        graph[prereq].append(course)
        in_degree[course] += 1
    
    queue = deque([i for i in range(numCourses) if in_degree[i] == 0])
    order = []
    
    while queue:
        node = queue.popleft()
        order.append(node)
        for neighbor in graph[node]:
            in_degree[neighbor] -= 1
            if in_degree[neighbor] == 0:
                queue.append(neighbor)
    
    return order if len(order) == numCourses else []
```

### Minimum Semesters

```python
def minimum_semesters(n: int, relations: List[List[int]]) -> int:
    """
    LeetCode 1136 - Parallel Courses
    
    Minimum semesters to complete all courses (longest path in DAG).
    
    Time: O(V + E)
    """
    graph = defaultdict(list)
    in_degree = [0] * (n + 1)
    
    for prev, next_course in relations:
        graph[prev].append(next_course)
        in_degree[next_course] += 1
    
    # BFS level by level (each level = 1 semester)
    queue = deque([i for i in range(1, n + 1) if in_degree[i] == 0])
    semesters = 0
    courses_taken = 0
    
    while queue:
        semesters += 1
        for _ in range(len(queue)):
            course = queue.popleft()
            courses_taken += 1
            for neighbor in graph[course]:
                in_degree[neighbor] -= 1
                if in_degree[neighbor] == 0:
                    queue.append(neighbor)
    
    return semesters if courses_taken == n else -1


def min_semesters_with_k(n: int, relations: List[List[int]], k: int) -> int:
    """
    LeetCode 1494 - Parallel Courses II
    
    At most k courses per semester.
    Uses bitmask DP when n is small.
    
    Time: O(3^n) with bitmask DP
    """
    prereq = [0] * n  # Bitmask of prerequisites for each course
    
    for prev, next_course in relations:
        prereq[next_course - 1] |= (1 << (prev - 1))
    
    # dp[mask] = min semesters to complete courses in mask
    dp = {0: 0}
    
    full_mask = (1 << n) - 1
    queue = deque([0])
    
    while queue:
        mask = queue.popleft()
        
        if mask == full_mask:
            return dp[mask]
        
        # Find available courses (all prereqs satisfied)
        available = 0
        for i in range(n):
            if not (mask & (1 << i)):  # Not taken
                if (prereq[i] & mask) == prereq[i]:  # All prereqs done
                    available |= (1 << i)
        
        # Try all subsets of available courses of size <= k
        submask = available
        while submask:
            if bin(submask).count('1') <= k:
                new_mask = mask | submask
                if new_mask not in dp or dp[new_mask] > dp[mask] + 1:
                    dp[new_mask] = dp[mask] + 1
                    if new_mask not in dp:
                        queue.append(new_mask)
            submask = (submask - 1) & available
    
    return -1
```

---

## 6. DP on Implicit DAG (State Graph)

### Word Ladder as DAG

```python
def word_ladder_length(beginWord: str, endWord: str, wordList: List[str]) -> int:
    """
    LeetCode 127 - Word Ladder
    
    Model as shortest path in implicit DAG of word states.
    """
    from collections import deque
    
    word_set = set(wordList)
    if endWord not in word_set:
        return 0
    
    queue = deque([(beginWord, 1)])
    visited = {beginWord}
    
    while queue:
        word, steps = queue.popleft()
        
        if word == endWord:
            return steps
        
        for i in range(len(word)):
            for c in 'abcdefghijklmnopqrstuvwxyz':
                new_word = word[:i] + c + word[i+1:]
                if new_word in word_set and new_word not in visited:
                    visited.add(new_word)
                    queue.append((new_word, steps + 1))
    
    return 0


def word_ladder_paths(beginWord: str, endWord: str, wordList: List[str]) -> List[List[str]]:
    """
    LeetCode 126 - Word Ladder II
    
    Find all shortest transformation sequences.
    Build DAG of shortest paths, then backtrack.
    """
    word_set = set(wordList)
    if endWord not in word_set:
        return []
    
    # BFS to build shortest path DAG
    from collections import defaultdict
    
    # parent[word] = list of words that can transform to word in shortest path
    parent = defaultdict(list)
    level = {beginWord: 0}
    
    queue = deque([beginWord])
    found = False
    
    while queue and not found:
        for _ in range(len(queue)):
            word = queue.popleft()
            curr_level = level[word]
            
            for i in range(len(word)):
                for c in 'abcdefghijklmnopqrstuvwxyz':
                    new_word = word[:i] + c + word[i+1:]
                    
                    if new_word == endWord:
                        found = True
                    
                    if new_word in word_set:
                        if new_word not in level:
                            level[new_word] = curr_level + 1
                            queue.append(new_word)
                        
                        if level[new_word] == curr_level + 1:
                            parent[new_word].append(word)
    
    if not found:
        return []
    
    # Backtrack to find all paths
    result = []
    
    def backtrack(word: str, path: List[str]):
        if word == beginWord:
            result.append(path[::-1])
            return
        for prev in parent[word]:
            backtrack(prev, path + [prev])
    
    backtrack(endWord, [endWord])
    return result
```

---

## 7. Alien Dictionary (Build and Solve DAG)

```python
def alien_order(words: List[str]) -> str:
    """
    LeetCode 269 - Alien Dictionary
    
    Given sorted alien words, find character order.
    Build DAG from adjacent word comparisons, then topological sort.
    
    Time: O(total characters)
    """
    # Build graph
    graph = defaultdict(set)
    in_degree = {c: 0 for word in words for c in word}
    
    for i in range(len(words) - 1):
        w1, w2 = words[i], words[i + 1]
        min_len = min(len(w1), len(w2))
        
        # Invalid case: prefix is longer
        if len(w1) > len(w2) and w1[:min_len] == w2[:min_len]:
            return ""
        
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
        c = queue.popleft()
        result.append(c)
        for neighbor in graph[c]:
            in_degree[neighbor] -= 1
            if in_degree[neighbor] == 0:
                queue.append(neighbor)
    
    if len(result) != len(in_degree):
        return ""  # Cycle detected
    
    return ''.join(result)
```

---

## 8. Longest Increasing Path in Matrix

```python
def longest_increasing_path(matrix: List[List[int]]) -> int:
    """
    LeetCode 329 - Longest Increasing Path in Matrix
    
    Matrix cells form implicit DAG based on value ordering.
    Use DFS with memoization (equivalent to DP on DAG).
    
    Time: O(mn)
    Space: O(mn)
    """
    if not matrix or not matrix[0]:
        return 0
    
    m, n = len(matrix), len(matrix[0])
    memo = {}
    
    def dfs(i: int, j: int) -> int:
        if (i, j) in memo:
            return memo[(i, j)]
        
        result = 1
        for di, dj in [(0, 1), (0, -1), (1, 0), (-1, 0)]:
            ni, nj = i + di, j + dj
            if 0 <= ni < m and 0 <= nj < n and matrix[ni][nj] > matrix[i][j]:
                result = max(result, 1 + dfs(ni, nj))
        
        memo[(i, j)] = result
        return result
    
    return max(dfs(i, j) for i in range(m) for j in range(n))


def longest_increasing_path_topo(matrix: List[List[int]]) -> int:
    """
    Alternative: Explicit topological sort approach.
    
    Build DAG explicitly and process in topological order.
    """
    if not matrix or not matrix[0]:
        return 0
    
    m, n = len(matrix), len(matrix[0])
    
    # Compute out-degree for each cell
    out_degree = [[0] * n for _ in range(m)]
    
    for i in range(m):
        for j in range(n):
            for di, dj in [(0, 1), (0, -1), (1, 0), (-1, 0)]:
                ni, nj = i + di, j + dj
                if 0 <= ni < m and 0 <= nj < n and matrix[ni][nj] > matrix[i][j]:
                    out_degree[i][j] += 1
    
    # Start from cells with out_degree = 0 (sinks)
    queue = deque()
    for i in range(m):
        for j in range(n):
            if out_degree[i][j] == 0:
                queue.append((i, j))
    
    # Process in reverse topological order
    dp = [[1] * n for _ in range(m)]
    length = 0
    
    while queue:
        length += 1
        for _ in range(len(queue)):
            i, j = queue.popleft()
            for di, dj in [(0, 1), (0, -1), (1, 0), (-1, 0)]:
                ni, nj = i + di, j + dj
                if 0 <= ni < m and 0 <= nj < n and matrix[ni][nj] < matrix[i][j]:
                    dp[ni][nj] = max(dp[ni][nj], dp[i][j] + 1)
                    out_degree[ni][nj] -= 1
                    if out_degree[ni][nj] == 0:
                        queue.append((ni, nj))
    
    return max(max(row) for row in dp)
```

---

## 9. Build Order / Task Scheduling

```python
def build_order(projects: List[str], dependencies: List[tuple]) -> List[str]:
    """
    Classic interview problem: Find valid build order.
    
    Args:
        projects: List of project names
        dependencies: (a, b) means a depends on b (build b first)
    
    Returns:
        Valid build order or empty list if impossible
    """
    graph = defaultdict(list)
    in_degree = {p: 0 for p in projects}
    
    for dependent, dependency in dependencies:
        graph[dependency].append(dependent)
        in_degree[dependent] += 1
    
    queue = deque([p for p in projects if in_degree[p] == 0])
    order = []
    
    while queue:
        project = queue.popleft()
        order.append(project)
        for neighbor in graph[project]:
            in_degree[neighbor] -= 1
            if in_degree[neighbor] == 0:
                queue.append(neighbor)
    
    return order if len(order) == len(projects) else []


def earliest_completion_time(n: int, times: List[int], dependencies: List[tuple]) -> List[int]:
    """
    Compute earliest completion time for each task.
    
    Args:
        n: Number of tasks (0 to n-1)
        times: times[i] = time to complete task i
        dependencies: (a, b) means a must complete before b starts
    
    Returns:
        completion[i] = earliest time task i can complete
    """
    graph = defaultdict(list)
    in_degree = [0] * n
    
    for before, after in dependencies:
        graph[before].append(after)
        in_degree[after] += 1
    
    # earliest[i] = earliest start time
    earliest_start = [0] * n
    
    queue = deque([i for i in range(n) if in_degree[i] == 0])
    
    while queue:
        task = queue.popleft()
        for neighbor in graph[task]:
            # Update earliest start time of neighbor
            earliest_start[neighbor] = max(
                earliest_start[neighbor],
                earliest_start[task] + times[task]
            )
            in_degree[neighbor] -= 1
            if in_degree[neighbor] == 0:
                queue.append(neighbor)
    
    return [earliest_start[i] + times[i] for i in range(n)]
```

---

## 10. Critical Path Method (CPM)

```python
def critical_path(n: int, durations: List[int], edges: List[tuple]) -> tuple:
    """
    Find critical path in project network.
    
    Returns:
        (project_duration, critical_tasks)
    """
    # Forward pass: earliest times
    graph = defaultdict(list)
    reverse_graph = defaultdict(list)
    in_degree = [0] * n
    
    for u, v in edges:
        graph[u].append(v)
        reverse_graph[v].append(u)
        in_degree[v] += 1
    
    # Earliest start/finish
    earliest_start = [0] * n
    earliest_finish = [0] * n
    
    queue = deque([i for i in range(n) if in_degree[i] == 0])
    topo_order = []
    
    while queue:
        node = queue.popleft()
        topo_order.append(node)
        earliest_finish[node] = earliest_start[node] + durations[node]
        
        for neighbor in graph[node]:
            earliest_start[neighbor] = max(earliest_start[neighbor], earliest_finish[node])
            in_degree[neighbor] -= 1
            if in_degree[neighbor] == 0:
                queue.append(neighbor)
    
    project_duration = max(earliest_finish)
    
    # Backward pass: latest times
    latest_finish = [project_duration] * n
    latest_start = [project_duration] * n
    
    for node in reversed(topo_order):
        if not graph[node]:  # Sink
            latest_finish[node] = project_duration
        else:
            latest_finish[node] = min(latest_start[neighbor] for neighbor in graph[node])
        latest_start[node] = latest_finish[node] - durations[node]
    
    # Critical tasks: no slack (earliest == latest)
    critical_tasks = [i for i in range(n) if earliest_start[i] == latest_start[i]]
    
    return project_duration, critical_tasks
```

---

## 11. DP over DAG States

### Job Sequencing with DAG Constraints

```python
def max_profit_jobs(jobs: List[tuple], dependencies: List[tuple]) -> int:
    """
    Select subset of jobs with maximum profit.
    Dependencies: (a, b) means if we do b, we must do a first.
    
    This is DAG DP where states are subsets (for small n).
    """
    n = len(jobs)
    profit = [j[0] for j in jobs]
    
    # Build dependency graph
    prereq = [0] * n  # Bitmask of prerequisites
    for before, after in dependencies:
        prereq[after] |= (1 << before)
    
    # dp[mask] = max profit for subset mask
    dp = {0: 0}
    
    def is_valid(mask: int, job: int) -> bool:
        """Check if all prerequisites of job are in mask."""
        return (prereq[job] & mask) == prereq[job]
    
    for mask in range(1 << n):
        if mask not in dp:
            continue
        
        for job in range(n):
            if mask & (1 << job):
                continue
            if is_valid(mask, job):
                new_mask = mask | (1 << job)
                new_profit = dp[mask] + profit[job]
                if new_mask not in dp or dp[new_mask] < new_profit:
                    dp[new_mask] = new_profit
    
    return max(dp.values())
```

---

## 12. Practice Problems

### LeetCode Problems

| # | Problem | Pattern | Difficulty |
|---|---------|---------|------------|
| 207 | [Course Schedule](https://leetcode.com/problems/course-schedule/) | Cycle detection | Medium |
| 210 | [Course Schedule II](https://leetcode.com/problems/course-schedule-ii/) | Topological sort | Medium |
| 269 | [Alien Dictionary](https://leetcode.com/problems/alien-dictionary/) | Build + topo sort | Hard |
| 329 | [Longest Increasing Path in Matrix](https://leetcode.com/problems/longest-increasing-path-in-matrix/) | Implicit DAG | Hard |
| 797 | [All Paths From Source to Target](https://leetcode.com/problems/all-paths-from-source-to-target/) | Path enumeration | Medium |
| 802 | [Find Eventual Safe States](https://leetcode.com/problems/find-eventual-safe-states/) | Reverse DAG | Medium |
| 1136 | [Parallel Courses](https://leetcode.com/problems/parallel-courses/) | Longest path | Medium |
| 1203 | [Sort Items by Groups](https://leetcode.com/problems/sort-items-by-groups-respecting-dependencies/) | Multi-level topo | Hard |
| 1462 | [Course Schedule IV](https://leetcode.com/problems/course-schedule-iv/) | Reachability | Medium |
| 1494 | [Parallel Courses II](https://leetcode.com/problems/parallel-courses-ii/) | DAG + Bitmask | Hard |
| 1857 | [Largest Color Value in a Directed Graph](https://leetcode.com/problems/largest-color-value-in-a-directed-graph/) | DP on DAG | Hard |
| 1976 | [Number of Ways to Arrive at Destination](https://leetcode.com/problems/number-of-ways-to-arrive-at-destination/) | Count paths | Medium |
| 2050 | [Parallel Courses III](https://leetcode.com/problems/parallel-courses-iii/) | Longest path | Hard |
| 2115 | [Find All Possible Recipes](https://leetcode.com/problems/find-all-possible-recipes-from-given-supplies/) | DAG traversal | Medium |
| 2192 | [All Ancestors of a Node](https://leetcode.com/problems/all-ancestors-of-a-node-in-a-directed-acyclic-graph/) | DAG ancestry | Medium |

### Advanced Problems

| Problem | Source | Pattern |
|---------|--------|---------|
| PERT/CPM Analysis | Classic | Critical path |
| Longest Path | CSES | DAG DP |
| Shortest Routes I | CSES | Dijkstra/DAG |
| Game Routes | CSES | Count paths |
| Investigation | CSES | Count + min |

---

## 13. Key Patterns Summary

```
DAG DP Decision Framework:

1. Is the graph explicitly a DAG?
   → Use topological sort + linear DP
   
2. Is there an implicit DAG (states ordered by some property)?
   → DFS with memoization OR explicit topo sort
   
3. Need shortest/longest path?
   → Initialize sources, propagate in topo order
   
4. Need to count paths?
   → DP: count[v] = sum(count[u] for u in predecessors)
   
5. Need all paths?
   → Build parent pointers, backtrack from target
   
6. Cycle detection needed?
   → Kahn's (check if all nodes processed) or DFS (back edges)

Time Complexity: Always O(V + E) for DAG DP!
```

---

## 14. References

1. CLRS - Chapter 24: Single-Source Shortest Paths (DAG section)
2. CP-Algorithms: Topological Sorting
3. CSES Problem Set - Graph Algorithms
4. LeetCode Course Schedule Series
