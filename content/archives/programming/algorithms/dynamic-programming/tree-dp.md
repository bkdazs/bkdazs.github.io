---
title: "Tree DP and Subtree Problems"
topic: "Dynamic Programming on Trees"
difficulty: "Medium to Hard"
tags: ["dp", "tree", "dfs", "subtree", "rerooting"]
status: "complete"
weight: 6
---
# Tree DP and Subtree Problems

## Summary / TL;DR

Tree DP leverages the hierarchical structure of trees to solve optimization and counting problems. Information flows from leaves to root (bottom-up) or from root to leaves (top-down).

**Key Patterns:**
1. **Subtree DP:** Compute answer for each subtree
2. **Path DP:** Longest/max path through nodes
3. **Rerooting:** Answer for each node as root
4. **Matching/Covering:** Select nodes with constraints

---

## When to Use

- **Subtree computations:** Size, sum, height, diameter
- **Tree paths:** Longest path, max sum path
- **Node selection:** Maximum independent set, vertex cover
- **Distance problems:** Sum of distances, farthest nodes
- **Rerooting:** Answer when each node is considered as root

---

## Big-O Complexity

| Problem Type | Time | Space |
|--------------|------|-------|
| Single root DP | O(n) | O(n) |
| Rerooting | O(n) | O(n) |
| Path problems | O(n) | O(n) or O(h) |
| Tree Matching | O(n) | O(n) |

---

## Core Implementation Templates

### Template 1: Basic Subtree DP (Bottom-Up)

```python
from typing import List, Optional
from collections import defaultdict

def tree_subtree_size(n: int, edges: List[List[int]], root: int = 0) -> List[int]:
    """
    Compute size of each subtree.
    subtree_size[v] = 1 + sum(subtree_size[child] for child in children[v])
    
    Time: O(n), Space: O(n)
    """
    # Build adjacency list
    adj = defaultdict(list)
    for u, v in edges:
        adj[u].append(v)
        adj[v].append(u)
    
    subtree_size = [0] * n
    
    def dfs(node: int, parent: int) -> int:
        size = 1
        for neighbor in adj[node]:
            if neighbor != parent:
                size += dfs(neighbor, node)
        subtree_size[node] = size
        return size
    
    dfs(root, -1)
    return subtree_size


def tree_height_and_diameter(n: int, edges: List[List[int]]) -> tuple:
    """
    Compute height of tree and diameter (longest path).
    
    For diameter: at each node, combine two longest paths to children.
    
    Time: O(n), Space: O(n)
    """
    adj = defaultdict(list)
    for u, v in edges:
        adj[u].append(v)
        adj[v].append(u)
    
    diameter = 0
    
    def dfs(node: int, parent: int) -> int:
        nonlocal diameter
        
        # Heights of subtrees
        heights = [0, 0]  # Two longest heights
        
        for neighbor in adj[node]:
            if neighbor != parent:
                h = dfs(neighbor, node) + 1
                if h > heights[0]:
                    heights = [h, heights[0]]
                elif h > heights[1]:
                    heights[1] = h
        
        # Diameter through this node
        diameter = max(diameter, heights[0] + heights[1])
        
        return heights[0]  # Height of subtree rooted at node
    
    height = dfs(0, -1)
    return height, diameter
```

### Template 2: Binary Tree DP (LeetCode Style)

```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right


def max_path_sum(root: Optional[TreeNode]) -> int:
    """
    Maximum sum path in binary tree (any nodes to any nodes).
    
    At each node: max path through it = left_gain + node.val + right_gain
    Return: max gain extending upward (only one direction)
    
    Time: O(n), Space: O(h)
    """
    max_sum = float('-inf')
    
    def dfs(node: Optional[TreeNode]) -> int:
        nonlocal max_sum
        
        if not node:
            return 0
        
        # Max gain from left and right (0 if negative)
        left_gain = max(0, dfs(node.left))
        right_gain = max(0, dfs(node.right))
        
        # Path through current node
        path_sum = left_gain + node.val + right_gain
        max_sum = max(max_sum, path_sum)
        
        # Return max gain extending upward
        return node.val + max(left_gain, right_gain)
    
    dfs(root)
    return max_sum


def diameter_of_binary_tree(root: Optional[TreeNode]) -> int:
    """
    Diameter = longest path between any two nodes (in edges).
    
    Time: O(n), Space: O(h)
    """
    diameter = 0
    
    def depth(node: Optional[TreeNode]) -> int:
        nonlocal diameter
        
        if not node:
            return 0
        
        left_depth = depth(node.left)
        right_depth = depth(node.right)
        
        # Update diameter
        diameter = max(diameter, left_depth + right_depth)
        
        return 1 + max(left_depth, right_depth)
    
    depth(root)
    return diameter


def longest_univalue_path(root: Optional[TreeNode]) -> int:
    """
    Longest path where all nodes have same value.
    
    Time: O(n), Space: O(h)
    """
    longest = 0
    
    def dfs(node: Optional[TreeNode]) -> int:
        nonlocal longest
        
        if not node:
            return 0
        
        left_len = dfs(node.left)
        right_len = dfs(node.right)
        
        # Extend left if same value
        left_ext = left_len + 1 if node.left and node.left.val == node.val else 0
        right_ext = right_len + 1 if node.right and node.right.val == node.val else 0
        
        longest = max(longest, left_ext + right_ext)
        
        return max(left_ext, right_ext)
    
    dfs(root)
    return longest
```

### Template 3: House Robber on Trees

```python
def house_robber_iii(root: Optional[TreeNode]) -> int:
    """
    Rob houses in tree - can't rob directly connected nodes.
    
    Return (rob_this, skip_this) for each node.
    
    Time: O(n), Space: O(h)
    """
    def dfs(node: Optional[TreeNode]) -> tuple:
        if not node:
            return (0, 0)  # (rob, skip)
        
        left = dfs(node.left)
        right = dfs(node.right)
        
        # Rob this node: can't rob children
        rob_this = node.val + left[1] + right[1]
        
        # Skip this node: take best of each child
        skip_this = max(left) + max(right)
        
        return (rob_this, skip_this)
    
    return max(dfs(root))


def max_independent_set(n: int, edges: List[List[int]]) -> int:
    """
    Maximum independent set in tree.
    Select nodes with no two adjacent.
    
    dp[v][0] = max set in subtree(v) if v NOT selected
    dp[v][1] = max set in subtree(v) if v selected
    
    Time: O(n), Space: O(n)
    """
    adj = defaultdict(list)
    for u, v in edges:
        adj[u].append(v)
        adj[v].append(u)
    
    dp = [[0, 0] for _ in range(n)]
    
    def dfs(node: int, parent: int):
        dp[node][1] = 1  # Select this node
        
        for neighbor in adj[node]:
            if neighbor != parent:
                dfs(neighbor, node)
                dp[node][0] += max(dp[neighbor])
                dp[node][1] += dp[neighbor][0]  # Can't select adjacent
    
    dfs(0, -1)
    return max(dp[0])


def min_vertex_cover(n: int, edges: List[List[int]]) -> int:
    """
    Minimum vertex cover: select minimum nodes to cover all edges.
    
    dp[v][0] = min cover in subtree(v) if v NOT in cover
    dp[v][1] = min cover in subtree(v) if v in cover
    
    Time: O(n), Space: O(n)
    """
    adj = defaultdict(list)
    for u, v in edges:
        adj[u].append(v)
        adj[v].append(u)
    
    dp = [[0, 0] for _ in range(n)]
    
    def dfs(node: int, parent: int):
        dp[node][1] = 1  # This node in cover
        
        for neighbor in adj[node]:
            if neighbor != parent:
                dfs(neighbor, node)
                # If node not in cover, children must be
                dp[node][0] += dp[neighbor][1]
                # If node in cover, children can be either
                dp[node][1] += min(dp[neighbor])
    
    dfs(0, -1)
    return min(dp[0])
```

### Template 4: Rerooting Technique

```python
def sum_of_distances_in_tree(n: int, edges: List[List[int]]) -> List[int]:
    """
    For each node, find sum of distances to all other nodes.
    
    Rerooting: First compute for root, then transfer to children.
    When moving root from u to v:
    - Nodes in subtree(v) get closer by 1
    - Nodes outside subtree(v) get farther by 1
    
    answer[v] = answer[u] - size[v] + (n - size[v])
              = answer[u] + n - 2 * size[v]
    
    Time: O(n), Space: O(n)
    """
    adj = defaultdict(list)
    for u, v in edges:
        adj[u].append(v)
        adj[v].append(u)
    
    subtree_size = [0] * n
    answer = [0] * n
    
    # First DFS: compute subtree sizes and answer[0]
    def dfs1(node: int, parent: int, depth: int):
        subtree_size[node] = 1
        answer[0] += depth
        
        for neighbor in adj[node]:
            if neighbor != parent:
                dfs1(neighbor, node, depth + 1)
                subtree_size[node] += subtree_size[neighbor]
    
    # Second DFS: reroot and compute other answers
    def dfs2(node: int, parent: int):
        for neighbor in adj[node]:
            if neighbor != parent:
                # Transfer answer from node to neighbor
                answer[neighbor] = answer[node] + n - 2 * subtree_size[neighbor]
                dfs2(neighbor, node)
    
    dfs1(0, -1, 0)
    dfs2(0, -1)
    
    return answer


def tree_farthest_node_from_each(n: int, edges: List[List[int]]) -> List[int]:
    """
    For each node, find distance to farthest node.
    
    Use rerooting with max instead of sum.
    
    Time: O(n), Space: O(n)
    """
    adj = defaultdict(list)
    for u, v in edges:
        adj[u].append(v)
        adj[v].append(u)
    
    # down[v] = max distance going down in subtree(v)
    # up[v] = max distance going up (through parent)
    down = [0] * n
    down2 = [0] * n  # Second max for handling up through sibling
    up = [0] * n
    
    # First DFS: compute down and down2
    def dfs1(node: int, parent: int):
        for neighbor in adj[node]:
            if neighbor != parent:
                dfs1(neighbor, node)
                d = down[neighbor] + 1
                if d > down[node]:
                    down2[node] = down[node]
                    down[node] = d
                elif d > down2[node]:
                    down2[node] = d
    
    # Second DFS: compute up
    def dfs2(node: int, parent: int):
        for neighbor in adj[node]:
            if neighbor != parent:
                # up[neighbor] = max path going up through node
                if down[neighbor] + 1 == down[node]:
                    # Use second best down
                    up[neighbor] = max(up[node], down2[node]) + 1
                else:
                    up[neighbor] = max(up[node], down[node]) + 1
                dfs2(neighbor, node)
    
    dfs1(0, -1)
    dfs2(0, -1)
    
    # Answer for each node
    return [max(down[i], up[i]) for i in range(n)]
```

### Template 5: Tree Matching and Coloring

```python
def binary_tree_cameras(root: Optional[TreeNode]) -> int:
    """
    Minimum cameras to cover all nodes.
    Camera covers itself and immediate neighbors.
    
    States: 0=not covered, 1=covered but no camera, 2=has camera
    
    Time: O(n), Space: O(h)
    """
    cameras = 0
    
    def dfs(node: Optional[TreeNode]) -> int:
        nonlocal cameras
        
        if not node:
            return 1  # Null nodes are "covered"
        
        left = dfs(node.left)
        right = dfs(node.right)
        
        # If any child not covered, this node needs camera
        if left == 0 or right == 0:
            cameras += 1
            return 2
        
        # If any child has camera, this node is covered
        if left == 2 or right == 2:
            return 1
        
        # Both children covered but no camera nearby
        return 0
    
    # Root might be uncovered
    if dfs(root) == 0:
        cameras += 1
    
    return cameras


def max_matching_on_tree(n: int, edges: List[List[int]]) -> int:
    """
    Maximum matching: select edges with no shared vertices.
    
    dp[v][0] = max matching in subtree(v) if v not matched
    dp[v][1] = max matching in subtree(v) if v matched (to some child)
    
    Time: O(n), Space: O(n)
    """
    adj = defaultdict(list)
    for u, v in edges:
        adj[u].append(v)
        adj[v].append(u)
    
    dp = [[0, 0] for _ in range(n)]
    
    def dfs(node: int, parent: int):
        sum_unmatched = 0
        max_gain = 0  # Best gain by matching node to one child
        
        for neighbor in adj[node]:
            if neighbor != parent:
                dfs(neighbor, node)
                sum_unmatched += dp[neighbor][0]
                # Gain = match(neighbor) + 1 edge - unmatched(neighbor)
                gain = dp[neighbor][0] + 1 - dp[neighbor][0]  # Actually just 1
                # More correctly: we compare matching node-neighbor vs not
                # gain = 1 + dp[neighbor][0] - max(dp[neighbor])
                gain = 1 + dp[neighbor][0] - dp[neighbor][0]
                max_gain = max(max_gain, 1)
        
        # Actually simpler formulation:
        # dp[node][0] = sum of max(dp[child]) for all children
        # dp[node][1] = pick one child to match, others unmatched
        
        dp[node][0] = 0
        best_match = 0
        for neighbor in adj[node]:
            if neighbor != parent:
                dp[node][0] += max(dp[neighbor])
                # If we match node-neighbor: gain is 1 + (previously took max(child) - now take unmatched)
                potential = 1 + dp[neighbor][0] - max(dp[neighbor])
                best_match = max(best_match, potential)
        
        dp[node][1] = dp[node][0] + best_match
    
    dfs(0, -1)
    return max(dp[0])


def tree_k_coloring(n: int, edges: List[List[int]], k: int) -> int:
    """
    Count ways to color tree with k colors (adjacent nodes different).
    
    For a tree: root has k choices, each child has k-1 choices.
    Total = k * (k-1)^(n-1)
    
    Time: O(n) for verification, O(1) with formula
    """
    if n == 0:
        return 0
    if k == 1:
        return 1 if n == 1 else 0
    
    # Formula: k * (k-1)^(n-1)
    return k * pow(k - 1, n - 1)
```

### Template 6: Tree Path Queries

```python
def count_paths_with_sum(root: Optional[TreeNode], target_sum: int) -> int:
    """
    Count paths (top to bottom) with given sum.
    Use prefix sum with hash map.
    
    Time: O(n), Space: O(n)
    """
    from collections import defaultdict
    
    count = 0
    prefix_sums = defaultdict(int)
    prefix_sums[0] = 1
    
    def dfs(node: Optional[TreeNode], curr_sum: int):
        nonlocal count
        
        if not node:
            return
        
        curr_sum += node.val
        
        # Check if any prefix sum gives us target
        count += prefix_sums[curr_sum - target_sum]
        
        prefix_sums[curr_sum] += 1
        
        dfs(node.left, curr_sum)
        dfs(node.right, curr_sum)
        
        # Backtrack
        prefix_sums[curr_sum] -= 1
    
    dfs(root, 0)
    return count


def longest_path_in_tree(n: int, edges: List[List[int]]) -> int:
    """
    Find the longest path (diameter) using two BFS.
    
    1. BFS from any node to find farthest node u
    2. BFS from u to find farthest node v
    3. Distance u-v is diameter
    
    Time: O(n), Space: O(n)
    """
    from collections import deque
    
    if n <= 1:
        return 0
    
    adj = defaultdict(list)
    for u, v in edges:
        adj[u].append(v)
        adj[v].append(u)
    
    def bfs_farthest(start: int) -> tuple:
        visited = {start}
        queue = deque([(start, 0)])
        farthest_node, max_dist = start, 0
        
        while queue:
            node, dist = queue.popleft()
            if dist > max_dist:
                max_dist = dist
                farthest_node = node
            
            for neighbor in adj[node]:
                if neighbor not in visited:
                    visited.add(neighbor)
                    queue.append((neighbor, dist + 1))
        
        return farthest_node, max_dist
    
    # Find one endpoint of diameter
    u, _ = bfs_farthest(0)
    # Find other endpoint
    v, diameter = bfs_farthest(u)
    
    return diameter
```

---

## Worked Examples

### Example 1: House Robber III

```
Tree:     3
         / \
        2   3
         \   \
          3   1

DFS bottom-up:

Node 3 (leaf): rob=3, skip=0 -> (3, 0)
Node 1 (leaf): rob=1, skip=0 -> (1, 0)
Node 2: 
  rob = 2 + skip(child3) = 2 + 0 = 2
  skip = max(child3) = max(3,0) = 3
  -> (2, 3)
Node 3 (right child of root):
  rob = 3 + skip(child1) = 3 + 0 = 3
  skip = max(child1) = max(1,0) = 1
  -> (3, 1)
Node 3 (root):
  rob = 3 + skip(2) + skip(3) = 3 + 3 + 1 = 7
  skip = max(2,3) + max(3,1) = 3 + 3 = 6
  -> (7, 6)

Answer: max(7, 6) = 7
Path: Rob root(3), leaf(3), leaf(1) = 3 + 3 + 1 = 7
```

### Example 2: Sum of Distances in Tree

```
Tree: 0 -- 1 -- 2
      |
      3

n = 4

First DFS from 0:
- subtree_size[2] = 1
- subtree_size[1] = 2 (itself + node 2)
- subtree_size[3] = 1
- subtree_size[0] = 4

answer[0] = depth(1) + depth(2) + depth(3)
          = 1 + 2 + 1 = 4

Second DFS (rerooting):
- answer[1] = answer[0] + n - 2*size[1]
            = 4 + 4 - 2*2 = 4
- answer[2] = answer[1] + n - 2*size[2]
            = 4 + 4 - 2*1 = 6
- answer[3] = answer[0] + n - 2*size[3]
            = 4 + 4 - 2*1 = 6

Result: [4, 4, 6, 6]

Verification:
- From 0: to 1(1) + 2(2) + 3(1) = 4 ✓
- From 1: to 0(1) + 2(1) + 3(2) = 4 ✓
- From 2: to 0(2) + 1(1) + 3(3) = 6 ✓
- From 3: to 0(1) + 1(2) + 2(3) = 6 ✓
```

---

## Edge Cases & Gotchas

```python
# 1. Empty tree
def handle_empty(root: Optional[TreeNode]) -> int:
    if not root:
        return 0

# 2. Single node
def handle_single(root: Optional[TreeNode]) -> int:
    if not root.left and not root.right:
        return root.val  # or appropriate base

# 3. Negative values in path sum
# Make sure to handle negative values correctly
# max(0, child_gain) might not apply if we need to include path

# 4. Disconnected components (forest)
# Run DFS/DP from each component

# 5. Stack overflow on deep trees
# Use iterative approach or increase recursion limit
import sys
sys.setrecursionlimit(10**6)

# 6. Undirected vs directed tree representation
# For undirected, always track parent to avoid revisiting
```

---

## Interview Tips & Communication

### Framework for Tree DP

1. **Identify direction:**
   - Bottom-up: Info flows from leaves to root
   - Top-down: Info flows from root to leaves
   - Both: Rerooting problems

2. **Define state:**
   - "dp[node] represents [what] for subtree rooted at node"
   - Consider if state needs multiple values (e.g., selected/not selected)

3. **Write recurrence:**
   - How does dp[node] depend on dp[children]?

4. **Handle edge cases:**
   - Null nodes, single node, negative values

### Key Phrases

- "I'll use post-order traversal to compute bottom-up."
- "At each node, I need to track two states: whether it's selected or not."
- "This requires rerooting - first compute answer for root, then transfer to children."
- "The diameter is the max of (left_height + right_height) across all nodes."

---

## Practice Problems

### Basic Tree DP

| # | Problem | Key Concept |
|---|---------|-------------|
| 1 | [Diameter of Binary Tree](https://leetcode.com/problems/diameter-of-binary-tree/) | Path through node |
| 2 | [Binary Tree Maximum Path Sum](https://leetcode.com/problems/binary-tree-maximum-path-sum/) | Max path sum |
| 3 | [House Robber III](https://leetcode.com/problems/house-robber-iii/) | Select/skip DP |
| 4 | [Longest Univalue Path](https://leetcode.com/problems/longest-univalue-path/) | Same-value path |
| 5 | [Maximum Difference Between Node and Ancestor](https://leetcode.com/problems/maximum-difference-between-node-and-ancestor/) | Track min/max |

### Subtree Problems

| # | Problem | Key Concept |
|---|---------|-------------|
| 6 | [Count Good Nodes in Binary Tree](https://leetcode.com/problems/count-good-nodes-in-binary-tree/) | Track max in path |
| 7 | [Maximum Product of Splitted Binary Tree](https://leetcode.com/problems/maximum-product-of-splitted-binary-tree/) | Subtree sums |
| 8 | [Distribute Coins in Binary Tree](https://leetcode.com/problems/distribute-coins-in-binary-tree/) | Excess flow |
| 9 | [Binary Tree Pruning](https://leetcode.com/problems/binary-tree-pruning/) | Remove subtrees |
| 10 | [Delete Nodes And Return Forest](https://leetcode.com/problems/delete-nodes-and-return-forest/) | Forest creation |

### Path Problems

| # | Problem | Key Concept |
|---|---------|-------------|
| 11 | [Path Sum III](https://leetcode.com/problems/path-sum-iii/) | Prefix sum in tree |
| 12 | [Sum Root to Leaf Numbers](https://leetcode.com/problems/sum-root-to-leaf-numbers/) | Digit paths |
| 13 | [Longest ZigZag Path](https://leetcode.com/problems/longest-zigzag-path-in-a-binary-tree/) | Direction tracking |
| 14 | [Count Paths That Can Form a Palindrome](https://leetcode.com/problems/count-paths-that-can-form-a-palindrome-in-a-tree/) | Bitmask paths |

### Rerooting/All Nodes

| # | Problem | Key Concept |
|---|---------|-------------|
| 15 | [Sum of Distances in Tree](https://leetcode.com/problems/sum-of-distances-in-tree/) | Classic rerooting |
| 16 | [Tree with Smallest Sum of Distances](https://leetcode.com/problems/minimum-height-trees/) | Find centers |
| 17 | [Count Number of Possible Root Nodes](https://leetcode.com/problems/count-number-of-possible-root-nodes/) | Root counting |
| 18 | [Find Distance in a Binary Tree](https://leetcode.com/problems/find-distance-in-a-binary-tree/) | LCA + distance |

### Advanced Tree DP

| # | Problem | Key Concept |
|---|---------|-------------|
| 19 | [Binary Tree Cameras](https://leetcode.com/problems/binary-tree-cameras/) | State machine |
| 20 | [Number of Good Leaf Nodes Pairs](https://leetcode.com/problems/number-of-good-leaf-nodes-pairs/) | Leaf distances |
| 21 | [Maximum Sum BST in Binary Tree](https://leetcode.com/problems/maximum-sum-bst-in-binary-tree/) | BST validation + sum |
| 22 | [Linked List in Binary Tree](https://leetcode.com/problems/linked-list-in-binary-tree/) | Pattern matching |

---

## References

- CLRS: Chapter 24.3 (Tree algorithms)
- [CP Algorithms: Tree DP](https://cp-algorithms.com/graph/tree_dp.html)
- [Codeforces: Rerooting Technique](https://codeforces.com/blog/entry/68138)
- [USACO Guide: Tree DP](https://usaco.guide/gold/dp-trees)
