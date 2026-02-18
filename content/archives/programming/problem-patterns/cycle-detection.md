---
title: "Cycle Detection Patterns"
topic: "Problem Patterns"
difficulty: "Medium"
tags: ["cycle", "linked-list", "graph", "floyd", "dfs"]
status: "complete"
weight: 12
---

# Cycle Detection Patterns

## 📚 Overview

Cycle detection is fundamental for linked lists, graphs, and array problems.

---

## 1️⃣ Floyd's Cycle Detection (Tortoise & Hare)

### Linked List Cycle (LC 141)

```python
class ListNode:
    def __init__(self, val=0):
        self.val = val
        self.next = None

def has_cycle(head: ListNode) -> bool:
    """Detect cycle using slow/fast pointers"""
    slow = fast = head
    
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        
        if slow == fast:
            return True
    
    return False
```

### Find Cycle Start (LC 142)

```python
def detect_cycle(head: ListNode) -> ListNode:
    """Find node where cycle begins"""
    slow = fast = head
    
    # Phase 1: Detect cycle
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        
        if slow == fast:
            break
    else:
        return None  # No cycle
    
    # Phase 2: Find cycle start
    # Reset slow to head, move both at same speed
    slow = head
    while slow != fast:
        slow = slow.next
        fast = fast.next
    
    return slow
```

### Find Duplicate Number (LC 287)

```python
def find_duplicate(nums: list[int]) -> int:
    """
    Array has n+1 integers in range [1, n]
    Treat index as pointer: nums[i] points to nums[nums[i]]
    """
    # Phase 1: Find intersection
    slow = fast = nums[0]
    while True:
        slow = nums[slow]
        fast = nums[nums[fast]]
        if slow == fast:
            break
    
    # Phase 2: Find cycle start
    slow = nums[0]
    while slow != fast:
        slow = nums[slow]
        fast = nums[fast]
    
    return slow
```

---

## 2️⃣ Graph Cycle Detection - Undirected

### Using DFS with Parent Tracking

```python
def has_cycle_undirected(n: int, edges: list[list[int]]) -> bool:
    """Detect cycle in undirected graph"""
    from collections import defaultdict
    
    graph = defaultdict(list)
    for u, v in edges:
        graph[u].append(v)
        graph[v].append(u)
    
    visited = set()
    
    def dfs(node: int, parent: int) -> bool:
        visited.add(node)
        
        for neighbor in graph[node]:
            if neighbor not in visited:
                if dfs(neighbor, node):
                    return True
            elif neighbor != parent:
                return True  # Back edge found
        
        return False
    
    # Check all components
    for node in range(n):
        if node not in visited:
            if dfs(node, -1):
                return True
    
    return False
```

### Using Union-Find

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
        """Return False if x and y already connected (cycle)"""
        px, py = self.find(x), self.find(y)
        if px == py:
            return False  # Cycle detected
        
        if self.rank[px] < self.rank[py]:
            px, py = py, px
        self.parent[py] = px
        if self.rank[px] == self.rank[py]:
            self.rank[px] += 1
        
        return True


def has_cycle_uf(n: int, edges: list[list[int]]) -> bool:
    uf = UnionFind(n)
    for u, v in edges:
        if not uf.union(u, v):
            return True
    return False
```

---

## 3️⃣ Graph Cycle Detection - Directed

### DFS with Color Marking

```python
def has_cycle_directed(n: int, edges: list[list[int]]) -> bool:
    """
    Colors: 0 = white (unvisited), 1 = gray (in stack), 2 = black (done)
    Cycle exists if we visit a gray node
    """
    from collections import defaultdict
    
    graph = defaultdict(list)
    for u, v in edges:
        graph[u].append(v)
    
    color = [0] * n
    
    def dfs(node: int) -> bool:
        color[node] = 1  # Gray - currently processing
        
        for neighbor in graph[node]:
            if color[neighbor] == 1:  # Back edge to gray node
                return True
            if color[neighbor] == 0:
                if dfs(neighbor):
                    return True
        
        color[node] = 2  # Black - done
        return False
    
    for node in range(n):
        if color[node] == 0:
            if dfs(node):
                return True
    
    return False
```

### Using Topological Sort (Kahn's)

```python
from collections import deque, defaultdict

def has_cycle_kahn(n: int, edges: list[list[int]]) -> bool:
    """If we can't visit all nodes via topo sort, cycle exists"""
    graph = defaultdict(list)
    indegree = [0] * n
    
    for u, v in edges:
        graph[u].append(v)
        indegree[v] += 1
    
    queue = deque([i for i in range(n) if indegree[i] == 0])
    visited = 0
    
    while queue:
        node = queue.popleft()
        visited += 1
        
        for neighbor in graph[node]:
            indegree[neighbor] -= 1
            if indegree[neighbor] == 0:
                queue.append(neighbor)
    
    return visited != n  # Cycle if not all visited
```

---

## 4️⃣ Find All Nodes in Cycles

### Nodes in All Cycles (Directed Graph)

```python
def find_all_cycle_nodes(n: int, edges: list[list[int]]) -> set:
    """Find all nodes that are part of any cycle"""
    from collections import defaultdict
    
    graph = defaultdict(list)
    for u, v in edges:
        graph[u].append(v)
    
    # 0=unvisited, 1=in_stack, 2=done
    color = [0] * n
    on_cycle = set()
    
    def dfs(node: int, path: list) -> bool:
        color[node] = 1
        path.append(node)
        
        for neighbor in graph[node]:
            if color[neighbor] == 1:  # Found cycle
                # All nodes from neighbor to end of path are in cycle
                cycle_start = path.index(neighbor)
                on_cycle.update(path[cycle_start:])
                return True
            elif color[neighbor] == 0:
                if dfs(neighbor, path):
                    return True
        
        path.pop()
        color[node] = 2
        return False
    
    for node in range(n):
        if color[node] == 0:
            dfs(node, [])
    
    return on_cycle
```

---

## 5️⃣ Cycle Length

### Length of Cycle in Linked List

```python
def cycle_length(head: ListNode) -> int:
    """Return length of cycle, 0 if no cycle"""
    slow = fast = head
    
    # Find meeting point
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        
        if slow == fast:
            # Count cycle length
            length = 1
            current = slow.next
            while current != slow:
                length += 1
                current = current.next
            return length
    
    return 0
```

---

## 6️⃣ Happy Number (Implicit Cycle)

### LC 202

```python
def is_happy(n: int) -> bool:
    """
    Happy number: sum of squares of digits eventually reaches 1
    If not happy, enters a cycle
    """
    def get_next(num: int) -> int:
        total = 0
        while num > 0:
            digit = num % 10
            total += digit * digit
            num //= 10
        return total
    
    slow = fast = n
    while True:
        slow = get_next(slow)
        fast = get_next(get_next(fast))
        
        if fast == 1:
            return True
        if slow == fast:
            return False
```

---

## 7️⃣ Course Schedule (Prerequisites Cycle)

### LC 207 & 210

```python
def can_finish(num_courses: int, prerequisites: list[list[int]]) -> bool:
    """Can complete all courses? (No cycle in prerequisites)"""
    from collections import defaultdict, deque
    
    graph = defaultdict(list)
    indegree = [0] * num_courses
    
    for course, prereq in prerequisites:
        graph[prereq].append(course)
        indegree[course] += 1
    
    queue = deque([i for i in range(num_courses) if indegree[i] == 0])
    completed = 0
    
    while queue:
        course = queue.popleft()
        completed += 1
        
        for next_course in graph[course]:
            indegree[next_course] -= 1
            if indegree[next_course] == 0:
                queue.append(next_course)
    
    return completed == num_courses


def find_order(num_courses: int, prerequisites: list[list[int]]) -> list[int]:
    """Return valid course order, empty if impossible"""
    from collections import defaultdict, deque
    
    graph = defaultdict(list)
    indegree = [0] * num_courses
    
    for course, prereq in prerequisites:
        graph[prereq].append(course)
        indegree[course] += 1
    
    queue = deque([i for i in range(num_courses) if indegree[i] == 0])
    order = []
    
    while queue:
        course = queue.popleft()
        order.append(course)
        
        for next_course in graph[course]:
            indegree[next_course] -= 1
            if indegree[next_course] == 0:
                queue.append(next_course)
    
    return order if len(order) == num_courses else []
```

---

## 📝 Related Problems

| Problem | LC # | Pattern |
|---------|------|---------|
| Linked List Cycle | 141 | Floyd's Algorithm |
| Linked List Cycle II | 142 | Find cycle start |
| Find the Duplicate Number | 287 | Array as linked list |
| Happy Number | 202 | Implicit cycle |
| Course Schedule | 207 | Directed cycle (topo sort) |
| Course Schedule II | 210 | Topo sort order |
| Redundant Connection | 684 | Union-Find cycle |
| Redundant Connection II | 685 | Directed + Union-Find |
| Graph Valid Tree | 261 | Undirected cycle |
| Detect Cycle in 2D Grid | 1559 | DFS with direction |

---

*Last Updated: 2024*
