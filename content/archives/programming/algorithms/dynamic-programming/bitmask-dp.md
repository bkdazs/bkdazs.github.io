---
title: "Bitmask Dynamic Programming"
topic: "DP with Bitmask State Representation"
difficulty: "Hard"
tags: ["dp", "bitmask", "tsp", "subset", "combinatorics"]
status: "complete"
weight: 7
---

# Bitmask DP

## Summary / TL;DR

Bitmask DP uses binary numbers to represent subsets, enabling efficient DP on problems involving selection, permutation, or assignment of elements. Each bit represents whether an element is included (1) or not (0).

**Key Insight:** A set of n elements has 2^n subsets, each representable by an n-bit integer.

**When n ≤ 20:** Bitmask DP is feasible (2^20 ≈ 10^6 states).

---

## When to Use

- **Small n (≤ 20):** Problem involves subsets or permutations
- **Assignment problems:** Match items to targets (TSP, task assignment)
- **Subset selection:** Choose subset satisfying constraints
- **Counting:** Count ways to partition or arrange
- **Hamiltonian paths:** Visit all nodes exactly once

### Pattern Recognition

| Clue | Approach |
|------|----------|
| "Visit all exactly once" | TSP-style bitmask |
| "Partition into groups" | Subset DP |
| "Assign n items to n slots" | Bitmask on used items |
| n ≤ 15-20 | Likely bitmask DP |

---

## Big-O Complexity

| Problem Type | Time | Space |
|--------------|------|-------|
| Basic subset DP | O(2^n) | O(2^n) |
| Subset + iteration | O(n × 2^n) | O(2^n) |
| TSP-like | O(n² × 2^n) | O(n × 2^n) |
| Subset sum over subsets | O(3^n) | O(2^n) |

---

## Bitmask Operations Cheat Sheet

```python
# Check if bit i is set
def is_set(mask: int, i: int) -> bool:
    return (mask >> i) & 1 == 1

# Set bit i
def set_bit(mask: int, i: int) -> int:
    return mask | (1 << i)

# Clear bit i
def clear_bit(mask: int, i: int) -> int:
    return mask & ~(1 << i)

# Toggle bit i
def toggle_bit(mask: int, i: int) -> int:
    return mask ^ (1 << i)

# Count set bits
def count_bits(mask: int) -> int:
    return bin(mask).count('1')
    # or: mask.bit_count() in Python 3.10+

# Get lowest set bit
def lowest_bit(mask: int) -> int:
    return mask & (-mask)

# Remove lowest set bit
def remove_lowest(mask: int) -> int:
    return mask & (mask - 1)

# Iterate over all subsets of mask
def iterate_subsets(mask: int):
    subset = mask
    while subset > 0:
        yield subset
        subset = (subset - 1) & mask
    yield 0  # Empty subset

# Check if a is subset of b
def is_subset(a: int, b: int) -> bool:
    return (a & b) == a
```

---

## Core Implementation Templates

### Template 1: Basic Bitmask DP

```python
from typing import List
from functools import lru_cache

def can_partition_k_subsets(nums: List[int], k: int) -> bool:
    """
    Can we partition nums into k subsets with equal sum?
    
    State: (mask, current_bucket_sum)
    Transition: Try adding each unused number to current bucket
    
    Time: O(n * 2^n), Space: O(2^n)
    """
    total = sum(nums)
    if total % k != 0:
        return False
    
    target = total // k
    n = len(nums)
    
    if max(nums) > target:
        return False
    
    # dp[mask] = remaining sum in current bucket
    # -1 means impossible
    dp = [-1] * (1 << n)
    dp[0] = 0
    
    for mask in range(1 << n):
        if dp[mask] == -1:
            continue
        
        for i in range(n):
            if mask & (1 << i):  # Already used
                continue
            
            if dp[mask] + nums[i] <= target:
                new_mask = mask | (1 << i)
                dp[new_mask] = (dp[mask] + nums[i]) % target
    
    return dp[(1 << n) - 1] == 0


def count_subsets_with_sum(nums: List[int], target: int) -> int:
    """
    Count number of subsets with given sum.
    
    Simple iteration over all 2^n subsets.
    
    Time: O(n * 2^n), Space: O(1)
    """
    n = len(nums)
    count = 0
    
    for mask in range(1 << n):
        subset_sum = 0
        for i in range(n):
            if mask & (1 << i):
                subset_sum += nums[i]
        if subset_sum == target:
            count += 1
    
    return count
```

### Template 2: Traveling Salesman Problem (TSP)

```python
def tsp_min_cost(dist: List[List[int]]) -> int:
    """
    Classic TSP: Visit all cities exactly once, return to start.
    Minimize total distance.
    
    dp[mask][i] = min cost to visit cities in mask, ending at city i
    
    Time: O(n² * 2^n), Space: O(n * 2^n)
    """
    n = len(dist)
    INF = float('inf')
    
    # dp[mask][i] = min cost to reach city i, having visited cities in mask
    dp = [[INF] * n for _ in range(1 << n)]
    dp[1][0] = 0  # Start at city 0
    
    for mask in range(1 << n):
        for last in range(n):
            if dp[mask][last] == INF:
                continue
            if not (mask & (1 << last)):  # last must be in mask
                continue
            
            for next_city in range(n):
                if mask & (1 << next_city):  # Already visited
                    continue
                
                new_mask = mask | (1 << next_city)
                dp[new_mask][next_city] = min(
                    dp[new_mask][next_city],
                    dp[mask][last] + dist[last][next_city]
                )
    
    # Return to starting city
    full_mask = (1 << n) - 1
    return min(dp[full_mask][i] + dist[i][0] for i in range(n))


def shortest_superstring(words: List[str]) -> str:
    """
    Find shortest string containing all words as substrings.
    This is TSP where edge cost = overlap.
    
    Time: O(n² * 2^n), Space: O(n * 2^n)
    """
    n = len(words)
    
    # Precompute overlaps
    overlap = [[0] * n for _ in range(n)]
    for i in range(n):
        for j in range(n):
            if i != j:
                # Find max overlap: words[i] suffix = words[j] prefix
                for k in range(min(len(words[i]), len(words[j])), 0, -1):
                    if words[i][-k:] == words[j][:k]:
                        overlap[i][j] = k
                        break
    
    # dp[mask][i] = max overlap ending at word i with words in mask
    dp = [[0] * n for _ in range(1 << n)]
    parent = [[-1] * n for _ in range(1 << n)]
    
    for mask in range(1 << n):
        for last in range(n):
            if not (mask & (1 << last)):
                continue
            prev_mask = mask ^ (1 << last)
            if prev_mask == 0:
                continue
            
            for prev in range(n):
                if prev_mask & (1 << prev):
                    new_overlap = dp[prev_mask][prev] + overlap[prev][last]
                    if new_overlap > dp[mask][last]:
                        dp[mask][last] = new_overlap
                        parent[mask][last] = prev
    
    # Find best ending
    full_mask = (1 << n) - 1
    last = max(range(n), key=lambda i: dp[full_mask][i])
    
    # Reconstruct path
    path = []
    mask = full_mask
    while last != -1:
        path.append(last)
        new_last = parent[mask][last]
        mask ^= (1 << last)
        last = new_last
    path = path[::-1]
    
    # Build result string
    result = words[path[0]]
    for i in range(1, len(path)):
        ovlp = overlap[path[i-1]][path[i]]
        result += words[path[i]][ovlp:]
    
    return result
```

### Template 3: Assignment Problems

```python
def min_cost_assignment(cost: List[List[int]]) -> int:
    """
    Assign n workers to n jobs, minimize total cost.
    cost[i][j] = cost for worker i to do job j.
    
    dp[mask] = min cost when jobs in mask are assigned
    
    Time: O(n * 2^n), Space: O(2^n)
    """
    n = len(cost)
    INF = float('inf')
    
    dp = [INF] * (1 << n)
    dp[0] = 0
    
    for mask in range(1 << n):
        if dp[mask] == INF:
            continue
        
        # Number of assigned jobs = number of set bits
        # This tells us which worker to assign next
        worker = bin(mask).count('1')
        
        if worker >= n:
            continue
        
        for job in range(n):
            if mask & (1 << job):  # Job already assigned
                continue
            
            new_mask = mask | (1 << job)
            dp[new_mask] = min(dp[new_mask], dp[mask] + cost[worker][job])
    
    return dp[(1 << n) - 1]


def max_compatibility_score(students: List[List[int]], 
                            mentors: List[List[int]]) -> int:
    """
    Match n students to n mentors maximizing compatibility.
    
    Time: O(n * 2^n), Space: O(2^n)
    """
    n = len(students)
    m = len(students[0])  # Number of questions
    
    # Precompute compatibility scores
    compat = [[0] * n for _ in range(n)]
    for s in range(n):
        for t in range(n):
            compat[s][t] = sum(students[s][i] == mentors[t][i] for i in range(m))
    
    # dp[mask] = max score when mentors in mask are assigned
    dp = [0] * (1 << n)
    
    for mask in range(1, 1 << n):
        student = bin(mask).count('1') - 1  # 0-indexed student
        
        for mentor in range(n):
            if mask & (1 << mentor):
                prev_mask = mask ^ (1 << mentor)
                dp[mask] = max(dp[mask], dp[prev_mask] + compat[student][mentor])
    
    return dp[(1 << n) - 1]
```

### Template 4: Subset Sum over Subsets (SOS DP)

```python
def sum_over_subsets_dp(arr: List[int]) -> List[int]:
    """
    For each mask, compute sum of arr[subset] for all subsets of mask.
    
    Standard approach: O(n * 2^n) instead of O(3^n)
    
    Time: O(n * 2^n), Space: O(2^n)
    """
    n = len(arr).bit_length() - 1 if arr else 0
    # Assume arr has 2^n elements
    
    dp = arr[:]
    
    for i in range(n):
        for mask in range(1 << n):
            if mask & (1 << i):
                dp[mask] += dp[mask ^ (1 << i)]
    
    return dp


def count_pairs_with_and_zero(nums: List[int]) -> int:
    """
    Count pairs (i, j) where nums[i] AND nums[j] = 0.
    
    For each num, count how many numbers are subsets of its complement.
    Use SOS DP on frequency array.
    
    Time: O(max_val * log(max_val) + n), Space: O(max_val)
    """
    if not nums:
        return 0
    
    max_val = max(nums)
    bits = max_val.bit_length()
    size = 1 << bits
    
    # Frequency array
    freq = [0] * size
    for num in nums:
        freq[num] += 1
    
    # SOS DP: dp[mask] = sum of freq[subset] for all subsets of mask
    dp = freq[:]
    for i in range(bits):
        for mask in range(size):
            if mask & (1 << i):
                dp[mask] += dp[mask ^ (1 << i)]
    
    # For each num, count pairs
    count = 0
    for num in nums:
        complement = (size - 1) ^ num  # All bits flipped within range
        count += dp[complement]
    
    # Each pair counted twice (except num & num = 0 cases)
    # Also counted (i, i) pairs
    for num in nums:
        if num == 0:
            count -= 1  # Remove self-pairing
    
    return count // 2
```

### Template 5: Hamiltonian Path

```python
def count_hamiltonian_paths(graph: List[List[int]]) -> int:
    """
    Count Hamiltonian paths (visit each node exactly once).
    
    dp[mask][v] = number of paths visiting nodes in mask, ending at v
    
    Time: O(n² * 2^n), Space: O(n * 2^n)
    """
    n = len(graph)
    
    # Build adjacency set
    adj = [set() for _ in range(n)]
    for i in range(n):
        for j in range(n):
            if graph[i][j]:
                adj[i].add(j)
    
    # dp[mask][v] = count of paths
    dp = [[0] * n for _ in range(1 << n)]
    
    # Base: single node paths
    for i in range(n):
        dp[1 << i][i] = 1
    
    for mask in range(1 << n):
        for last in range(n):
            if not (mask & (1 << last)):
                continue
            if dp[mask][last] == 0:
                continue
            
            for next_node in adj[last]:
                if mask & (1 << next_node):
                    continue
                
                new_mask = mask | (1 << next_node)
                dp[new_mask][next_node] += dp[mask][last]
    
    # Sum all paths that visit all nodes
    full_mask = (1 << n) - 1
    return sum(dp[full_mask])


def shortest_path_visiting_all_nodes(graph: List[List[int]]) -> int:
    """
    Shortest path visiting all nodes (can revisit).
    Use BFS with state (mask, current_node).
    
    Time: O(n² * 2^n), Space: O(n * 2^n)
    """
    from collections import deque
    
    n = len(graph)
    full_mask = (1 << n) - 1
    
    # BFS: (mask, node, distance)
    queue = deque()
    visited = set()
    
    # Start from each node
    for i in range(n):
        state = (1 << i, i)
        queue.append((1 << i, i, 0))
        visited.add(state)
    
    while queue:
        mask, node, dist = queue.popleft()
        
        if mask == full_mask:
            return dist
        
        for neighbor in graph[node]:
            new_mask = mask | (1 << neighbor)
            state = (new_mask, neighbor)
            
            if state not in visited:
                visited.add(state)
                queue.append((new_mask, neighbor, dist + 1))
    
    return -1
```

### Template 6: Broken Profile DP (Advanced)

```python
def count_tilings_2xn(n: int) -> int:
    """
    Count ways to tile 2×n board with 1×2 dominoes.
    
    Using broken profile DP (profile is just 2 bits here).
    
    Time: O(n * 4), Space: O(4)
    """
    if n == 0:
        return 1
    
    # profile: which cells in current column are filled
    # 00 = neither, 01 = bottom, 10 = top, 11 = both
    dp = [0] * 4
    dp[3] = 1  # Start with "previous column" fully filled
    
    for col in range(n):
        new_dp = [0] * 4
        
        for prev_profile in range(4):
            if dp[prev_profile] == 0:
                continue
            
            # Current column profile based on what extends from previous
            # If prev bit is 0, cell is empty; if 1, filled by horizontal domino
            curr_need = prev_profile ^ 3  # Flip: what's empty in current col
            
            # Try to fill current column
            # Use DFS/recursion or enumerate possibilities
            def fill(row: int, profile: int, count: int):
                if row == 2:
                    new_dp[profile] += count
                    return
                
                if curr_need & (1 << row):
                    # This cell is empty, must fill
                    # Option 1: Vertical domino (if row+1 also empty)
                    if row + 1 < 2 and (curr_need & (1 << (row + 1))):
                        fill(row + 2, profile, count)
                    # Option 2: Horizontal domino to next column
                    fill(row + 1, profile | (1 << row), count)
                else:
                    # Cell already filled by horizontal from prev
                    fill(row + 1, profile, count)
            
            fill(0, 0, dp[prev_profile])
        
        dp = new_dp
    
    return dp[0]  # Final column should have nothing extending


def domino_tromino_tiling(n: int) -> int:
    """
    Tile 2×n board with dominoes (2×1) and trominoes (L-shaped).
    
    DP with profile states.
    
    Time: O(n), Space: O(1)
    """
    MOD = 10**9 + 7
    
    if n == 1:
        return 1
    if n == 2:
        return 2
    
    # f[i] = ways to fully tile 2×i
    # p[i] = ways to tile with one cell sticking out
    
    f = [0] * (n + 1)
    p = [0] * (n + 1)
    
    f[1] = 1
    f[2] = 2
    p[2] = 2
    
    for i in range(3, n + 1):
        f[i] = (f[i-1] + f[i-2] + 2 * p[i-1]) % MOD
        p[i] = (p[i-1] + f[i-2]) % MOD
    
    return f[n]
```

---

## Worked Examples

### Example 1: TSP with 4 Cities

```
Distance matrix:
     0   1   2   3
0  [ 0, 10, 15, 20]
1  [10,  0, 35, 25]
2  [15, 35,  0, 30]
3  [20, 25, 30,  0]

Initial: dp[0001][0] = 0 (at city 0, visited {0})

mask=0001 (city 0):
  -> dp[0011][1] = dp[0001][0] + dist[0][1] = 0 + 10 = 10
  -> dp[0101][2] = dp[0001][0] + dist[0][2] = 0 + 15 = 15
  -> dp[1001][3] = dp[0001][0] + dist[0][3] = 0 + 20 = 20

mask=0011 (cities 0,1):
  -> dp[0111][2] = min(∞, dp[0011][1] + dist[1][2]) = 10 + 35 = 45
  -> dp[1011][3] = min(∞, dp[0011][1] + dist[1][3]) = 10 + 25 = 35

mask=0101 (cities 0,2):
  -> dp[0111][1] = min(45, dp[0101][2] + dist[2][1]) = min(45, 15+35) = 45
  -> dp[1101][3] = min(∞, dp[0101][2] + dist[2][3]) = 15 + 30 = 45

... (continue for all masks)

Final: dp[1111][i] + dist[i][0] for all i

Best tour: 0→1→3→2→0 or similar with cost 80
```

### Example 2: Partition into K Equal Subsets

```
nums = [4, 3, 2, 3, 5, 2, 1], k = 4
total = 20, target = 5

Process masks, tracking current bucket sum mod 5:

mask=0: dp[0]=0 (no elements, bucket sum=0)

mask=1 (add 4): dp[1]=(0+4)%5=4
mask=2 (add 3): dp[2]=(0+3)%5=3
...
mask=3 (4,3): dp[3]=(4+3)%5=2  or  =(3+4)%5=2
...

Valid if dp[1111111] == 0 (all buckets complete)
```

---

## Edge Cases & Gotchas

```python
# 1. n too large
def check_n(n: int):
    if n > 20:
        # Bitmask DP infeasible
        raise ValueError("n too large for bitmask DP")

# 2. Empty set
# mask = 0 is valid and represents empty set
# dp[0] often has special initialization

# 3. Overflow in counting
MOD = 10**9 + 7
# Use modular arithmetic

# 4. Off-by-one in bit indices
# bit i represents element at index i (0-indexed)

# 5. Iterating in correct order
# For some problems, process masks in increasing order of bit count
from functools import cmp_to_key
masks = sorted(range(1 << n), key=lambda x: bin(x).count('1'))
```

---

## Interview Tips & Communication

### Framework

1. **Verify n is small enough** (≤ 20)

2. **Define state:**
   - What does the bitmask represent?
   - What additional state is needed?

3. **Transitions:**
   - How do we go from one state to another?
   - What are valid transitions?

4. **Base case and final answer**

### Key Phrases

- "Since n is small (≤ 20), I can use bitmask DP with 2^n states."
- "Each bit represents whether an element is included in our selection."
- "The transition adds one element at a time, changing one bit."
- "I'll iterate masks in order so smaller subsets are computed first."

---

## Practice Problems

### Basic Bitmask DP

| # | Problem | Key Concept |
|---|---------|-------------|
| 1 | [Partition to K Equal Sum Subsets](https://leetcode.com/problems/partition-to-k-equal-sum-subsets/) | Subset partitioning |
| 2 | [Matchsticks to Square](https://leetcode.com/problems/matchsticks-to-square/) | 4 equal subsets |
| 3 | [Beautiful Arrangement](https://leetcode.com/problems/beautiful-arrangement/) | Counting arrangements |
| 4 | [Subsets](https://leetcode.com/problems/subsets/) | Generate all subsets |
| 5 | [Subsets II](https://leetcode.com/problems/subsets-ii/) | With duplicates |

### TSP and Path Problems

| # | Problem | Key Concept |
|---|---------|-------------|
| 6 | [Shortest Path Visiting All Nodes](https://leetcode.com/problems/shortest-path-visiting-all-nodes/) | BFS + bitmask |
| 7 | [Find the Shortest Superstring](https://leetcode.com/problems/find-the-shortest-superstring/) | TSP with strings |
| 8 | [Number of Squareful Arrays](https://leetcode.com/problems/number-of-squareful-arrays/) | Hamiltonian path |
| 9 | [Minimum Cost to Connect Two Groups](https://leetcode.com/problems/minimum-cost-to-connect-two-groups-of-points/) | Assignment |

### Advanced Bitmask

| # | Problem | Key Concept |
|---|---------|-------------|
| 10 | [Maximum Students Taking Exam](https://leetcode.com/problems/maximum-students-taking-exam/) | Row-by-row bitmask |
| 11 | [Parallel Courses II](https://leetcode.com/problems/parallel-courses-ii/) | Course scheduling |
| 12 | [Minimum XOR Sum of Two Arrays](https://leetcode.com/problems/minimum-xor-sum-of-two-arrays/) | Assignment |
| 13 | [Maximum AND Sum of Array](https://leetcode.com/problems/maximum-and-sum-of-array/) | Slot assignment |
| 14 | [Distribute Repeating Integers](https://leetcode.com/problems/distribute-repeating-integers/) | Subset assignment |

### Tiling Problems

| # | Problem | Key Concept |
|---|---------|-------------|
| 15 | [Domino and Tromino Tiling](https://leetcode.com/problems/domino-and-tromino-tiling/) | Profile DP |
| 16 | [Number of Ways to Build House of Cards](https://leetcode.com/problems/number-of-ways-to-build-house-of-cards/) | Triangle stacking |

### Counting and SOS

| # | Problem | Key Concept |
|---|---------|-------------|
| 17 | [Count Number of Maximum Bitwise-OR Subsets](https://leetcode.com/problems/count-number-of-maximum-bitwise-or-subsets/) | OR of subsets |
| 18 | [Can I Win](https://leetcode.com/problems/can-i-win/) | Game theory |
| 19 | [Stickers to Spell Word](https://leetcode.com/problems/stickers-to-spell-word/) | Character counting |
| 20 | [Maximum Compatibility Score Sum](https://leetcode.com/problems/maximum-compatibility-score-sum/) | Assignment |

---

## References

- [CP Algorithms: Bitmask DP](https://cp-algorithms.com/algebra/all-submasks.html)
- [USACO Guide: Bitmask DP](https://usaco.guide/gold/dp-bitmasks)
- [SOS DP Tutorial](https://codeforces.com/blog/entry/45223)
- [TopCoder: Bitmask Tutorial](https://www.topcoder.com/community/competitive-programming/tutorials/a-bit-of-fun-fun-with-bits/)
