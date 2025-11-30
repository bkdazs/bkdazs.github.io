---
title: Scheduling and Resource Allocation DP
topic: Algorithms - Dynamic Programming
difficulty: Hard
tags: [dp, scheduling, resource-allocation, interval-scheduling, job-scheduling]
status: complete
weight: 22
---

# Scheduling and Resource Allocation DP

## 1. Overview

This document covers DP approaches for:
- Job/Task scheduling optimization
- Resource allocation problems
- Weighted interval scheduling
- Machine scheduling variants
- Time-slot allocation

---

## 2. Core Concepts

### Problem Classification

```
Scheduling DP Problems:
├── Single Machine
│   ├── Weighted Job Scheduling
│   ├── Minimum Lateness
│   └── Tardiness Minimization
├── Multiple Machines
│   ├── Load Balancing
│   ├── Makespan Minimization
│   └── Parallel Job Scheduling
├── Interval-based
│   ├── Weighted Interval Scheduling
│   ├── Meeting Rooms
│   └── Activity Selection
└── Resource Constrained
    ├── Task Assignment
    ├── Budget Allocation
    └── Capacity Planning
```

---

## 3. Weighted Job Scheduling

### Problem Definition
Given n jobs with start time, end time, and profit, find maximum profit subset of non-overlapping jobs.

```python
from typing import List, Tuple
import bisect

def weighted_job_scheduling(jobs: List[Tuple[int, int, int]]) -> int:
    """
    Maximum profit from non-overlapping jobs.
    
    Args:
        jobs: List of (start, end, profit)
    
    Returns:
        Maximum achievable profit
    
    Time: O(n log n)
    Space: O(n)
    """
    if not jobs:
        return 0
    
    # Sort by end time
    jobs = sorted(jobs, key=lambda x: x[1])
    n = len(jobs)
    
    # dp[i] = max profit considering jobs 0..i
    dp = [0] * n
    dp[0] = jobs[0][2]
    
    # End times for binary search
    end_times = [jobs[i][1] for i in range(n)]
    
    for i in range(1, n):
        start_i, end_i, profit_i = jobs[i]
        
        # Include this job
        include = profit_i
        # Find latest job that ends before this one starts
        # Need end_time <= start_i
        idx = bisect.bisect_right(end_times, start_i) - 1
        if idx >= 0:
            include += dp[idx]
        
        # Exclude this job
        exclude = dp[i - 1]
        
        dp[i] = max(include, exclude)
    
    return dp[-1]


def weighted_job_scheduling_with_jobs(
    jobs: List[Tuple[int, int, int]]
) -> Tuple[int, List[int]]:
    """
    Return both max profit and selected job indices.
    """
    if not jobs:
        return 0, []
    
    # Sort by end time, keep original indices
    indexed_jobs = [(s, e, p, i) for i, (s, e, p) in enumerate(jobs)]
    indexed_jobs.sort(key=lambda x: x[1])
    n = len(indexed_jobs)
    
    dp = [0] * n
    dp[0] = indexed_jobs[0][2]
    parent = [-1] * n  # For reconstruction
    include = [False] * n
    include[0] = True
    
    end_times = [indexed_jobs[i][1] for i in range(n)]
    
    for i in range(1, n):
        start_i, end_i, profit_i, _ = indexed_jobs[i]
        
        # Include option
        inc_profit = profit_i
        prev_idx = bisect.bisect_right(end_times, start_i) - 1
        if prev_idx >= 0:
            inc_profit += dp[prev_idx]
        
        # Exclude option
        exc_profit = dp[i - 1]
        
        if inc_profit >= exc_profit:
            dp[i] = inc_profit
            include[i] = True
            parent[i] = prev_idx
        else:
            dp[i] = exc_profit
            include[i] = False
            parent[i] = i - 1
    
    # Reconstruct solution
    selected = []
    i = n - 1
    while i >= 0:
        if include[i]:
            selected.append(indexed_jobs[i][3])
            i = parent[i]
        else:
            i -= 1
    
    return dp[-1], selected[::-1]
```

### LeetCode 1235: Maximum Profit in Job Scheduling

```python
def job_scheduling(start: List[int], end: List[int], profit: List[int]) -> int:
    """
    LeetCode 1235: Maximum Profit in Job Scheduling
    
    Time: O(n log n)
    Space: O(n)
    """
    jobs = sorted(zip(start, end, profit), key=lambda x: x[1])
    n = len(jobs)
    
    # dp[i] = (end_time, max_profit up to that point)
    dp = [(0, 0)]  # Dummy entry for binary search
    
    for s, e, p in jobs:
        # Binary search for latest compatible job
        idx = bisect.bisect_right(dp, (s, float('inf'))) - 1
        
        # Max profit if we include this job
        new_profit = dp[idx][1] + p
        
        # Only add if better than current best
        if new_profit > dp[-1][1]:
            dp.append((e, new_profit))
    
    return dp[-1][1]
```

---

## 4. Multi-Machine Scheduling

### Makespan Minimization

```python
def min_makespan_dp(jobs: List[int], k: int) -> int:
    """
    Minimize makespan (max completion time) on k identical machines.
    
    This is NP-hard in general. DP solution for small k or job counts.
    
    State: dp[mask] = minimum makespan for jobs in mask
    
    Time: O(2^n × n × k) - exponential
    """
    n = len(jobs)
    if n <= k:
        return max(jobs) if jobs else 0
    
    INF = float('inf')
    # dp[mask] = list of k machine loads for min makespan
    # For simplicity, track minimum makespan achievable
    
    from functools import lru_cache
    
    @lru_cache(maxsize=None)
    def solve(mask: int, loads: tuple) -> int:
        """
        mask: which jobs are assigned
        loads: current load on each machine (sorted)
        """
        if mask == (1 << n) - 1:
            return max(loads)
        
        best = INF
        for j in range(n):
            if not (mask & (1 << j)):
                # Assign job j to machine with minimum load
                loads_list = list(loads)
                min_load_idx = loads_list.index(min(loads_list))
                loads_list[min_load_idx] += jobs[j]
                loads_list.sort()
                
                new_makespan = solve(
                    mask | (1 << j),
                    tuple(loads_list)
                )
                best = min(best, new_makespan)
        
        return best
    
    return solve(0, tuple([0] * k))


def min_makespan_dp_optimized(jobs: List[int], k: int) -> int:
    """
    Optimized version using subset sums on machines.
    
    Time: O(k × 2^n × sum(jobs))
    """
    n = len(jobs)
    total = sum(jobs)
    
    # dp[i][s] = can we assign subset of jobs with sum s to first i machines?
    # We need to track achievable sums for each machine count
    
    # Bitmask approach: dp[mask] = set of achievable (m1_load, m2_load, ...)
    # Too expensive for large k
    
    # LPT heuristic for approximation
    jobs_sorted = sorted(jobs, reverse=True)
    loads = [0] * k
    
    for job in jobs_sorted:
        # Assign to least loaded machine
        min_idx = loads.index(min(loads))
        loads[min_idx] += job
    
    return max(loads)
```

---

## 5. Resource Allocation DP

### Budget Allocation Problem

```python
def max_value_allocation(
    budgets: List[int],  # Budget for each category
    items: List[List[Tuple[int, int]]]  # items[i] = [(cost, value), ...]
) -> int:
    """
    Allocate budget across categories to maximize total value.
    
    Each category has its own set of items.
    Can choose at most one item per category.
    
    Args:
        budgets: Budget limit for each category
        items: Available items per category
    
    Returns:
        Maximum achievable value
    """
    n = len(budgets)
    total_budget = sum(budgets)
    
    # dp[i][b] = max value using categories 0..i-1 with total budget b
    dp = [[0] * (total_budget + 1) for _ in range(n + 1)]
    
    for i in range(1, n + 1):
        category_items = items[i - 1]
        
        for b in range(total_budget + 1):
            # Don't pick any item from this category
            dp[i][b] = dp[i - 1][b]
            
            # Try each item in this category
            for cost, value in category_items:
                if cost <= b and cost <= budgets[i - 1]:
                    dp[i][b] = max(dp[i][b], dp[i - 1][b - cost] + value)
    
    return dp[n][total_budget]


def capacity_allocation(
    capacities: List[int],
    demands: List[int],
    rewards: List[List[int]]
) -> int:
    """
    Allocate n resources to m demands.
    
    rewards[i][j] = reward if resource i is allocated to demand j
    capacities[i] = max units resource i can provide
    demands[j] = units needed by demand j
    
    Returns maximum total reward.
    """
    n_resources = len(capacities)
    n_demands = len(demands)
    
    # This is a variant of assignment problem
    # For small instances, use DP with bitmask for fulfilled demands
    
    from functools import lru_cache
    
    @lru_cache(maxsize=None)
    def dp(resource: int, fulfilled_mask: int, remaining_caps: tuple) -> int:
        if resource == n_resources:
            return 0
        
        best = dp(resource + 1, fulfilled_mask, remaining_caps)  # Skip
        
        # Try assigning current resource to each unfulfilled demand
        caps = list(remaining_caps)
        
        for demand in range(n_demands):
            if not (fulfilled_mask & (1 << demand)):
                if caps[resource] >= demands[demand]:
                    # Can fulfill this demand
                    new_caps = caps[:]
                    new_caps[resource] -= demands[demand]
                    
                    reward = rewards[resource][demand]
                    remaining = dp(
                        resource + 1,
                        fulfilled_mask | (1 << demand),
                        tuple(new_caps)
                    )
                    best = max(best, reward + remaining)
        
        return best
    
    return dp(0, 0, tuple(capacities))
```

---

## 6. Interval Scheduling Variants

### Weighted Activity Selection

```python
def weighted_activity_selection(
    activities: List[Tuple[int, int, int]]
) -> Tuple[int, List[int]]:
    """
    Select maximum weight subset of non-overlapping activities.
    
    Similar to weighted job scheduling.
    """
    return weighted_job_scheduling_with_jobs(activities)


def activity_selection_with_deadline(
    activities: List[Tuple[int, int, int, int]]
) -> int:
    """
    Activities have (start, duration, deadline, profit).
    Must complete before deadline. Maximize profit.
    
    Args:
        activities: List of (start, duration, deadline, profit)
    """
    # Convert to (start, end, profit) where end = min(start+duration, deadline)
    jobs = []
    for start, duration, deadline, profit in activities:
        end = start + duration
        if end <= deadline:  # Only feasible activities
            jobs.append((start, end, profit))
    
    return weighted_job_scheduling(jobs)
```

### Meeting Room Scheduling

```python
def min_meeting_rooms(intervals: List[List[int]]) -> int:
    """
    LeetCode 253: Meeting Rooms II
    
    Minimum rooms needed to schedule all meetings.
    
    Time: O(n log n)
    """
    if not intervals:
        return 0
    
    # Use sweep line or heap
    events = []
    for start, end in intervals:
        events.append((start, 1))   # Meeting starts
        events.append((end, -1))    # Meeting ends
    
    events.sort()
    
    max_rooms = 0
    current_rooms = 0
    
    for time, delta in events:
        current_rooms += delta
        max_rooms = max(max_rooms, current_rooms)
    
    return max_rooms


def schedule_meetings_maximize_count(
    intervals: List[List[int]]
) -> List[int]:
    """
    Schedule maximum number of meetings (unweighted).
    Greedy: sort by end time, greedily take non-overlapping.
    """
    indexed = [(e, s, i) for i, (s, e) in enumerate(intervals)]
    indexed.sort()
    
    selected = []
    last_end = -1
    
    for end, start, idx in indexed:
        if start >= last_end:
            selected.append(idx)
            last_end = end
    
    return selected
```

---

## 7. Task Assignment with Dependencies

```python
from collections import defaultdict, deque

def min_time_with_dependencies(
    tasks: List[int],  # Task durations
    dependencies: List[Tuple[int, int]],  # (a, b) means a must finish before b
    num_workers: int
) -> int:
    """
    Minimum time to complete all tasks with dependencies.
    
    Uses topological sort + simulation/DP.
    """
    n = len(tasks)
    graph = defaultdict(list)
    in_degree = [0] * n
    
    for a, b in dependencies:
        graph[a].append(b)
        in_degree[b] += 1
    
    # earliest[i] = earliest time task i can start
    earliest = [0] * n
    
    # Topological order
    queue = deque([i for i in range(n) if in_degree[i] == 0])
    order = []
    
    while queue:
        task = queue.popleft()
        order.append(task)
        
        for next_task in graph[task]:
            in_degree[next_task] -= 1
            # Update earliest start time
            earliest[next_task] = max(
                earliest[next_task],
                earliest[task] + tasks[task]
            )
            if in_degree[next_task] == 0:
                queue.append(next_task)
    
    if len(order) != n:
        return -1  # Cycle detected
    
    # With unlimited workers: max(earliest[i] + tasks[i])
    unlimited_time = max(earliest[i] + tasks[i] for i in range(n))
    
    if num_workers >= n:
        return unlimited_time
    
    # With limited workers: simulation
    import heapq
    
    # (finish_time, worker_id)
    workers = [(0, i) for i in range(num_workers)]
    heapq.heapify(workers)
    
    # Tasks ready to execute (sorted by earliest start)
    ready = [(earliest[t], t) for t in range(n) if in_degree[t] == 0]
    heapq.heapify(ready)
    
    in_degree = [0] * n
    for a, b in dependencies:
        in_degree[b] += 1
    
    finish_time = [0] * n
    
    while ready:
        earliest_start, task = heapq.heappop(ready)
        
        # Get earliest available worker
        worker_free, worker_id = heapq.heappop(workers)
        
        # Task starts at max(earliest possible, worker available)
        start = max(earliest_start, worker_free)
        end = start + tasks[task]
        finish_time[task] = end
        
        # Worker becomes available at end
        heapq.heappush(workers, (end, worker_id))
        
        # Update dependent tasks
        for next_task in graph[task]:
            in_degree[next_task] -= 1
            if in_degree[next_task] == 0:
                # Can now be scheduled
                next_earliest = max(
                    finish_time[dep] 
                    for dep, _ in dependencies 
                    if _ == next_task
                )
                heapq.heappush(ready, (next_earliest, next_task))
    
    return max(finish_time)


def min_cost_task_assignment(
    workers: int,
    tasks: int,
    cost: List[List[int]]
) -> int:
    """
    Assign tasks to workers minimizing total cost.
    cost[i][j] = cost of worker i doing task j
    
    Hungarian algorithm or DP with bitmask.
    """
    if tasks > 20:
        # Use Hungarian algorithm for larger instances
        raise ValueError("Use Hungarian algorithm for tasks > 20")
    
    INF = float('inf')
    
    # dp[mask] = min cost to complete tasks in mask
    dp = [INF] * (1 << tasks)
    dp[0] = 0
    
    for mask in range(1 << tasks):
        if dp[mask] == INF:
            continue
        
        # Which worker will do the next task?
        num_assigned = bin(mask).count('1')
        if num_assigned >= workers:
            continue
        
        worker = num_assigned
        
        for task in range(tasks):
            if not (mask & (1 << task)):
                new_mask = mask | (1 << task)
                dp[new_mask] = min(
                    dp[new_mask],
                    dp[mask] + cost[worker][task]
                )
    
    return dp[(1 << tasks) - 1]
```

---

## 8. Practice Problems

### LeetCode Problems

| # | Problem | Pattern | Difficulty |
|---|---------|---------|------------|
| 253 | [Meeting Rooms II](https://leetcode.com/problems/meeting-rooms-ii/) | Interval Scheduling | Medium |
| 435 | [Non-overlapping Intervals](https://leetcode.com/problems/non-overlapping-intervals/) | Interval Selection | Medium |
| 630 | [Course Schedule III](https://leetcode.com/problems/course-schedule-iii/) | Scheduling + Greedy | Hard |
| 1235 | [Max Profit Job Scheduling](https://leetcode.com/problems/maximum-profit-in-job-scheduling/) | Weighted Scheduling | Hard |
| 1353 | [Maximum Number of Events](https://leetcode.com/problems/maximum-number-of-events-that-can-be-attended/) | Greedy Scheduling | Medium |
| 1751 | [Max Events II](https://leetcode.com/problems/maximum-number-of-events-that-can-be-attended-ii/) | DP Scheduling | Hard |
| 2008 | [Maximum Earnings From Taxi](https://leetcode.com/problems/maximum-earnings-from-taxi/) | DP Scheduling | Medium |
| 2054 | [Two Best Non-Overlapping Events](https://leetcode.com/problems/two-best-non-overlapping-events/) | Binary Search + DP | Medium |

### Classic Problems

| Problem | Type | Technique |
|---------|------|-----------|
| Weighted Interval Scheduling | Single Machine | DP + Binary Search |
| Makespan Minimization | Multi-Machine | Bitmask DP / Heuristic |
| Job Shop Scheduling | Multi-Machine | Complex DP |
| Resource-Constrained Scheduling | Resource | DP + Constraints |
| Project Scheduling | Dependencies | DAG DP |

---

## 9. Key Patterns Summary

```
Scheduling DP Decision Tree:

1. Non-overlapping intervals + weights?
   → Weighted Interval Scheduling: Sort by end, DP + binary search

2. Minimize resources (rooms/machines)?
   → Greedy sweep line or heap

3. Multiple machines, minimize makespan?
   → Small n: Bitmask DP
   → Large n: LPT heuristic

4. Task dependencies?
   → Topological sort + DP

5. Resource constraints?
   → Multi-dimensional DP or constraint-based search

Key Optimizations:
- Binary search for finding compatible jobs: O(n log n)
- Sort by end time for interval problems
- Use heap for simulation with limited resources
- Bitmask DP for assignment problems (n ≤ 20)
```

---

## 10. Template: Generic Scheduling DP

```python
def generic_scheduling_dp(jobs: List[dict]) -> int:
    """
    Template for scheduling DP problems.
    
    jobs = [{'start': s, 'end': e, 'value': v, ...}, ...]
    """
    # 1. Sort by end time
    jobs = sorted(jobs, key=lambda x: x['end'])
    n = len(jobs)
    
    # 2. Build auxiliary structure for binary search
    end_times = [jobs[i]['end'] for i in range(n)]
    
    # 3. DP with binary search
    dp = [0] * (n + 1)
    
    for i in range(1, n + 1):
        job = jobs[i - 1]
        
        # Find latest compatible job
        idx = bisect.bisect_right(end_times, job['start'], 0, i - 1)
        
        # Include vs exclude
        include = job['value'] + dp[idx]
        exclude = dp[i - 1]
        
        dp[i] = max(include, exclude)
    
    return dp[n]
```
