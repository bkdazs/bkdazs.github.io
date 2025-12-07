---
title: Trees - General Concepts
topic: Trees
difficulty: Medium
tags: [trees, binary-tree, traversal, recursion, dfs, bfs]
status: complete
weight: 9
---

# Trees - General Concepts

## 1. Summary / TL;DR

- **Trees** are hierarchical data structures with a root node and subtrees of children
- **Binary trees** have at most 2 children per node; used extensively in interviews
- Master 3 traversals (preorder, inorder, postorder) in both recursive and iterative forms
- **Level-order (BFS)** uses queue; **DFS traversals** use stack/recursion
- Common patterns: tree height, diameter, path sum, LCA, serialization
- Most tree problems solved with **recursive thinking**: solve for subtrees, combine results

---

## 2. When & Where to Use

| Scenario | Why Trees? |
|----------|------------|
| Hierarchical data (file systems, DOM, org charts) | Natural parent-child representation |
| Search operations | BST provides O(log n) average search |
| Expression parsing | Expression trees for arithmetic |
| Decision making | Decision trees, game trees |
| Prefix matching | Tries for autocomplete, spell check |
| Priority queues | Heaps (complete binary trees) |
| Database indexing | B-trees, B+ trees |

---

## 3. Time & Space Complexity

### Operations on Binary Trees

| Operation | Time | Space | Notes |
|-----------|------|-------|-------|
| Traversal (all nodes) | O(n) | O(h) | h = height, O(n) worst case |
| Search (unordered) | O(n) | O(h) | Must check all nodes |
| Insert (unordered) | O(1) - O(n) | O(1) | Find position + insert |
| Height calculation | O(n) | O(h) | Visit all nodes |
| Level-order traversal | O(n) | O(w) | w = max width |

### Space Analysis

- **Recursive DFS**: O(h) call stack where h = height
- **Balanced tree**: h = O(log n)
- **Skewed tree**: h = O(n)
- **BFS**: O(w) where w = maximum width (up to n/2 for complete tree)

---

## 4. Core Concepts & Theory

### Tree Terminology

```
           1        <- Root (depth 0, level 0)
         /   \
        2     3     <- Internal nodes (depth 1)
       / \     \
      4   5     6   <- Leaf nodes (depth 2)
         /
        7           <- Leaf (depth 3)
```

| Term | Definition |
|------|------------|
| **Root** | Topmost node with no parent |
| **Leaf** | Node with no children |
| **Internal node** | Node with at least one child |
| **Depth** | Distance from root (edges count) |
| **Height** | Max depth from node to any leaf |
| **Level** | Set of nodes at same depth |
| **Subtree** | Tree consisting of node and all descendants |
| **Degree** | Number of children of a node |
| **Ancestor** | Any node on path from root to node |
| **Descendant** | Any node in subtree rooted at node |

### Types of Binary Trees

```python
# 1. Full Binary Tree - every node has 0 or 2 children
#        1
#       / \
#      2   3
#     / \
#    4   5

# 2. Complete Binary Tree - all levels filled except last, filled left to right
#        1
#       / \
#      2   3
#     / \
#    4   5

# 3. Perfect Binary Tree - all internal nodes have 2 children, all leaves same level
#        1
#       / \
#      2   3
#     / \ / \
#    4  5 6  7

# 4. Balanced Binary Tree - height difference of subtrees <= 1 for all nodes
# Examples: AVL trees, Red-Black trees

# 5. Degenerate/Skewed Tree - each node has only one child (essentially a linked list)
#    1
#     \
#      2
#       \
#        3
```

### Important Properties

```python
# For binary tree with n nodes:
# - Minimum height: floor(log2(n))
# - Maximum height: n - 1 (skewed)

# Perfect binary tree with height h:
# - Number of nodes: 2^(h+1) - 1
# - Number of leaves: 2^h
# - Number of internal nodes: 2^h - 1

# Complete binary tree (array representation):
# - Parent of node at index i: (i - 1) // 2
# - Left child of node at index i: 2*i + 1
# - Right child of node at index i: 2*i + 2
```

---

## 5. Diagrams / Visualizations

### Tree Traversal Orders

```
Example Tree:
        1
       / \
      2   3
     / \   \
    4   5   6

Preorder (Root, Left, Right):  1, 2, 4, 5, 3, 6
Inorder (Left, Root, Right):   4, 2, 5, 1, 3, 6
Postorder (Left, Right, Root): 4, 5, 2, 6, 3, 1
Level-order (BFS):             1, 2, 3, 4, 5, 6
```

### Visual: Recursive Call Stack for Inorder

```
inorder(1)
├── inorder(2)
│   ├── inorder(4)
│   │   ├── inorder(None) → return
│   │   ├── visit 4
│   │   └── inorder(None) → return
│   ├── visit 2
│   └── inorder(5)
│       ├── inorder(None) → return
│       ├── visit 5
│       └── inorder(None) → return
├── visit 1
└── inorder(3)
    ├── inorder(None) → return
    ├── visit 3
    └── inorder(6)
        ├── inorder(None) → return
        ├── visit 6
        └── inorder(None) → return
```

---

## 6. Implementation (Python)

### TreeNode Definition

```python
from typing import Optional, List
from collections import deque

class TreeNode:
    """Standard binary tree node used by LeetCode."""
    def __init__(self, val: int = 0, left: 'TreeNode' = None, right: 'TreeNode' = None):
        self.val = val
        self.left = left
        self.right = right
    
    def __repr__(self) -> str:
        return f"TreeNode({self.val})"
```

### Recursive Traversals

```python
def preorder_recursive(root: Optional[TreeNode]) -> List[int]:
    """
    Preorder traversal: Root -> Left -> Right
    
    >>> root = TreeNode(1, TreeNode(2, TreeNode(4), TreeNode(5)), TreeNode(3))
    >>> preorder_recursive(root)
    [1, 2, 4, 5, 3]
    """
    result = []
    
    def dfs(node: Optional[TreeNode]) -> None:
        if not node:
            return
        result.append(node.val)  # Process root
        dfs(node.left)           # Traverse left
        dfs(node.right)          # Traverse right
    
    dfs(root)
    return result


def inorder_recursive(root: Optional[TreeNode]) -> List[int]:
    """
    Inorder traversal: Left -> Root -> Right
    For BST, gives sorted order.
    
    >>> root = TreeNode(1, TreeNode(2, TreeNode(4), TreeNode(5)), TreeNode(3))
    >>> inorder_recursive(root)
    [4, 2, 5, 1, 3]
    """
    result = []
    
    def dfs(node: Optional[TreeNode]) -> None:
        if not node:
            return
        dfs(node.left)           # Traverse left
        result.append(node.val)  # Process root
        dfs(node.right)          # Traverse right
    
    dfs(root)
    return result


def postorder_recursive(root: Optional[TreeNode]) -> List[int]:
    """
    Postorder traversal: Left -> Right -> Root
    Used for deletion, expression evaluation.
    
    >>> root = TreeNode(1, TreeNode(2, TreeNode(4), TreeNode(5)), TreeNode(3))
    >>> postorder_recursive(root)
    [4, 5, 2, 3, 1]
    """
    result = []
    
    def dfs(node: Optional[TreeNode]) -> None:
        if not node:
            return
        dfs(node.left)           # Traverse left
        dfs(node.right)          # Traverse right
        result.append(node.val)  # Process root
    
    dfs(root)
    return result
```

### Iterative Traversals (Using Stack)

```python
def preorder_iterative(root: Optional[TreeNode]) -> List[int]:
    """
    Iterative preorder using explicit stack.
    
    >>> root = TreeNode(1, TreeNode(2, TreeNode(4), TreeNode(5)), TreeNode(3))
    >>> preorder_iterative(root)
    [1, 2, 4, 5, 3]
    """
    if not root:
        return []
    
    result = []
    stack = [root]
    
    while stack:
        node = stack.pop()
        result.append(node.val)
        # Push right first so left is processed first
        if node.right:
            stack.append(node.right)
        if node.left:
            stack.append(node.left)
    
    return result


def inorder_iterative(root: Optional[TreeNode]) -> List[int]:
    """
    Iterative inorder using stack.
    Key idea: Go left as far as possible, then process, then go right.
    
    >>> root = TreeNode(1, TreeNode(2, TreeNode(4), TreeNode(5)), TreeNode(3))
    >>> inorder_iterative(root)
    [4, 2, 5, 1, 3]
    """
    result = []
    stack = []
    current = root
    
    while current or stack:
        # Go left as far as possible
        while current:
            stack.append(current)
            current = current.left
        
        # Process current node
        current = stack.pop()
        result.append(current.val)
        
        # Move to right subtree
        current = current.right
    
    return result


def postorder_iterative(root: Optional[TreeNode]) -> List[int]:
    """
    Iterative postorder using two stacks (or reverse of modified preorder).
    
    Method: Do modified preorder (Root-Right-Left), then reverse.
    
    >>> root = TreeNode(1, TreeNode(2, TreeNode(4), TreeNode(5)), TreeNode(3))
    >>> postorder_iterative(root)
    [4, 5, 2, 3, 1]
    """
    if not root:
        return []
    
    result = []
    stack = [root]
    
    while stack:
        node = stack.pop()
        result.append(node.val)
        # Push left first (so right is processed first in modified preorder)
        if node.left:
            stack.append(node.left)
        if node.right:
            stack.append(node.right)
    
    return result[::-1]  # Reverse to get postorder


def postorder_iterative_single_stack(root: Optional[TreeNode]) -> List[int]:
    """
    True iterative postorder with single stack.
    Uses prev pointer to track last processed node.
    """
    if not root:
        return []
    
    result = []
    stack = []
    current = root
    prev = None
    
    while current or stack:
        # Go left as far as possible
        while current:
            stack.append(current)
            current = current.left
        
        current = stack[-1]
        
        # If right child exists and not yet processed
        if current.right and current.right != prev:
            current = current.right
        else:
            # Process current node
            result.append(current.val)
            prev = stack.pop()
            current = None
    
    return result
```

### Level-Order Traversal (BFS)

```python
def level_order(root: Optional[TreeNode]) -> List[List[int]]:
    """
    Level-order traversal using BFS.
    Returns list of levels, each level is a list of values.
    
    >>> root = TreeNode(1, TreeNode(2, TreeNode(4), TreeNode(5)), TreeNode(3))
    >>> level_order(root)
    [[1], [2, 3], [4, 5]]
    """
    if not root:
        return []
    
    result = []
    queue = deque([root])
    
    while queue:
        level_size = len(queue)
        level = []
        
        for _ in range(level_size):
            node = queue.popleft()
            level.append(node.val)
            
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
        
        result.append(level)
    
    return result


def zigzag_level_order(root: Optional[TreeNode]) -> List[List[int]]:
    """
    Zigzag level-order: alternate left-to-right and right-to-left.
    
    >>> root = TreeNode(1, TreeNode(2, TreeNode(4), TreeNode(5)), TreeNode(3, TreeNode(6), TreeNode(7)))
    >>> zigzag_level_order(root)
    [[1], [3, 2], [4, 5, 6, 7]]
    """
    if not root:
        return []
    
    result = []
    queue = deque([root])
    left_to_right = True
    
    while queue:
        level_size = len(queue)
        level = deque()
        
        for _ in range(level_size):
            node = queue.popleft()
            
            if left_to_right:
                level.append(node.val)
            else:
                level.appendleft(node.val)
            
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
        
        result.append(list(level))
        left_to_right = not left_to_right
    
    return result
```

### Common Tree Operations

```python
def tree_height(root: Optional[TreeNode]) -> int:
    """
    Calculate height of binary tree.
    Height = max number of edges from root to any leaf.
    Empty tree has height -1, single node has height 0.
    
    >>> root = TreeNode(1, TreeNode(2, TreeNode(4)), TreeNode(3))
    >>> tree_height(root)
    2
    """
    if not root:
        return -1
    
    return 1 + max(tree_height(root.left), tree_height(root.right))


def count_nodes(root: Optional[TreeNode]) -> int:
    """
    Count total nodes in binary tree.
    
    >>> root = TreeNode(1, TreeNode(2, TreeNode(4)), TreeNode(3))
    >>> count_nodes(root)
    4
    """
    if not root:
        return 0
    
    return 1 + count_nodes(root.left) + count_nodes(root.right)


def count_leaves(root: Optional[TreeNode]) -> int:
    """Count leaf nodes in binary tree."""
    if not root:
        return 0
    
    if not root.left and not root.right:
        return 1
    
    return count_leaves(root.left) + count_leaves(root.right)


def max_depth(root: Optional[TreeNode]) -> int:
    """
    Maximum depth of binary tree (LeetCode 104).
    Depth = number of nodes on longest path from root to leaf.
    
    >>> root = TreeNode(1, TreeNode(2, TreeNode(4)), TreeNode(3))
    >>> max_depth(root)
    3
    """
    if not root:
        return 0
    
    return 1 + max(max_depth(root.left), max_depth(root.right))


def min_depth(root: Optional[TreeNode]) -> int:
    """
    Minimum depth - shortest path from root to a leaf.
    
    >>> root = TreeNode(1, TreeNode(2, TreeNode(4)), TreeNode(3))
    >>> min_depth(root)
    2
    """
    if not root:
        return 0
    
    # If one child is missing, we must go through the other
    if not root.left:
        return 1 + min_depth(root.right)
    if not root.right:
        return 1 + min_depth(root.left)
    
    return 1 + min(min_depth(root.left), min_depth(root.right))


def is_same_tree(p: Optional[TreeNode], q: Optional[TreeNode]) -> bool:
    """
    Check if two trees are identical (LeetCode 100).
    
    >>> t1 = TreeNode(1, TreeNode(2), TreeNode(3))
    >>> t2 = TreeNode(1, TreeNode(2), TreeNode(3))
    >>> is_same_tree(t1, t2)
    True
    """
    if not p and not q:
        return True
    
    if not p or not q:
        return False
    
    return (p.val == q.val and 
            is_same_tree(p.left, q.left) and 
            is_same_tree(p.right, q.right))


def is_symmetric(root: Optional[TreeNode]) -> bool:
    """
    Check if tree is mirror of itself (LeetCode 101).
    
    >>> root = TreeNode(1, TreeNode(2, TreeNode(3), TreeNode(4)), TreeNode(2, TreeNode(4), TreeNode(3)))
    >>> is_symmetric(root)
    True
    """
    def is_mirror(t1: Optional[TreeNode], t2: Optional[TreeNode]) -> bool:
        if not t1 and not t2:
            return True
        if not t1 or not t2:
            return False
        return (t1.val == t2.val and 
                is_mirror(t1.left, t2.right) and 
                is_mirror(t1.right, t2.left))
    
    return is_mirror(root, root)


def invert_tree(root: Optional[TreeNode]) -> Optional[TreeNode]:
    """
    Invert/mirror a binary tree (LeetCode 226).
    
    >>> root = TreeNode(1, TreeNode(2), TreeNode(3))
    >>> inverted = invert_tree(root)
    >>> inverted.left.val, inverted.right.val
    (3, 2)
    """
    if not root:
        return None
    
    # Swap children
    root.left, root.right = root.right, root.left
    
    # Recursively invert subtrees
    invert_tree(root.left)
    invert_tree(root.right)
    
    return root
```

### Tree Construction

```python
def build_tree_from_list(values: List[Optional[int]]) -> Optional[TreeNode]:
    """
    Build tree from level-order list (LeetCode format).
    None represents missing nodes.
    
    >>> root = build_tree_from_list([1, 2, 3, None, 5])
    >>> root.val, root.left.val, root.right.val
    (1, 2, 3)
    """
    if not values:
        return None
    
    root = TreeNode(values[0])
    queue = deque([root])
    i = 1
    
    while queue and i < len(values):
        node = queue.popleft()
        
        # Left child
        if i < len(values) and values[i] is not None:
            node.left = TreeNode(values[i])
            queue.append(node.left)
        i += 1
        
        # Right child
        if i < len(values) and values[i] is not None:
            node.right = TreeNode(values[i])
            queue.append(node.right)
        i += 1
    
    return root


def build_tree_preorder_inorder(preorder: List[int], inorder: List[int]) -> Optional[TreeNode]:
    """
    Construct tree from preorder and inorder traversal (LeetCode 105).
    
    Key insight: 
    - First element of preorder is root
    - Elements to left of root in inorder are left subtree
    - Elements to right of root in inorder are right subtree
    
    >>> preorder = [3, 9, 20, 15, 7]
    >>> inorder = [9, 3, 15, 20, 7]
    >>> root = build_tree_preorder_inorder(preorder, inorder)
    >>> root.val
    3
    """
    if not preorder or not inorder:
        return None
    
    # Create index map for O(1) lookup in inorder
    inorder_map = {val: idx for idx, val in enumerate(inorder)}
    
    def build(pre_start: int, pre_end: int, in_start: int, in_end: int) -> Optional[TreeNode]:
        if pre_start > pre_end:
            return None
        
        # Root is first element of preorder
        root_val = preorder[pre_start]
        root = TreeNode(root_val)
        
        # Find root position in inorder
        root_idx = inorder_map[root_val]
        
        # Calculate size of left subtree
        left_size = root_idx - in_start
        
        # Build left subtree
        root.left = build(pre_start + 1, pre_start + left_size, in_start, root_idx - 1)
        
        # Build right subtree
        root.right = build(pre_start + left_size + 1, pre_end, root_idx + 1, in_end)
        
        return root
    
    return build(0, len(preorder) - 1, 0, len(inorder) - 1)


def build_tree_inorder_postorder(inorder: List[int], postorder: List[int]) -> Optional[TreeNode]:
    """
    Construct tree from inorder and postorder traversal (LeetCode 106).
    
    >>> inorder = [9, 3, 15, 20, 7]
    >>> postorder = [9, 15, 7, 20, 3]
    >>> root = build_tree_inorder_postorder(inorder, postorder)
    >>> root.val
    3
    """
    if not inorder or not postorder:
        return None
    
    inorder_map = {val: idx for idx, val in enumerate(inorder)}
    
    def build(in_start: int, in_end: int, post_start: int, post_end: int) -> Optional[TreeNode]:
        if in_start > in_end:
            return None
        
        # Root is last element of postorder
        root_val = postorder[post_end]
        root = TreeNode(root_val)
        
        # Find root in inorder
        root_idx = inorder_map[root_val]
        
        # Left subtree size
        left_size = root_idx - in_start
        
        # Build subtrees
        root.left = build(in_start, root_idx - 1, post_start, post_start + left_size - 1)
        root.right = build(root_idx + 1, in_end, post_start + left_size, post_end - 1)
        
        return root
    
    return build(0, len(inorder) - 1, 0, len(postorder) - 1)
```

### Serialization and Deserialization

```python
class Codec:
    """
    Serialize and deserialize binary tree (LeetCode 297).
    Uses preorder traversal with 'null' markers for missing nodes.
    """
    
    def serialize(self, root: Optional[TreeNode]) -> str:
        """Encodes a tree to a single string."""
        result = []
        
        def preorder(node: Optional[TreeNode]) -> None:
            if not node:
                result.append("null")
                return
            result.append(str(node.val))
            preorder(node.left)
            preorder(node.right)
        
        preorder(root)
        return ",".join(result)
    
    def deserialize(self, data: str) -> Optional[TreeNode]:
        """Decodes your encoded data to tree."""
        values = data.split(",")
        self.index = 0
        
        def build() -> Optional[TreeNode]:
            if self.index >= len(values) or values[self.index] == "null":
                self.index += 1
                return None
            
            node = TreeNode(int(values[self.index]))
            self.index += 1
            node.left = build()
            node.right = build()
            return node
        
        return build()
```

---

## 7. Step-by-Step Worked Example

### Problem: Binary Tree Diameter (LeetCode 543)

**Problem**: Find the diameter of a binary tree. The diameter is the length of the longest path between any two nodes. This path may or may not pass through the root.

**Example**:
```
        1
       / \
      2   3
     / \
    4   5

Diameter = 3 (path: 4 -> 2 -> 1 -> 3 or 5 -> 2 -> 1 -> 3)
```

**Approach**: The diameter through any node is (height of left subtree) + (height of right subtree). We need to track the maximum diameter seen across all nodes.

```python
def diameter_of_binary_tree(root: Optional[TreeNode]) -> int:
    """
    Calculate diameter of binary tree.
    Diameter = longest path between any two nodes (in edges).
    
    Time: O(n) - visit each node once
    Space: O(h) - recursion stack
    
    >>> root = build_tree_from_list([1, 2, 3, 4, 5])
    >>> diameter_of_binary_tree(root)
    3
    """
    max_diameter = 0
    
    def height(node: Optional[TreeNode]) -> int:
        """Return height and update max_diameter."""
        nonlocal max_diameter
        
        if not node:
            return 0
        
        # Calculate heights of subtrees
        left_height = height(node.left)
        right_height = height(node.right)
        
        # Diameter through this node
        diameter_through_node = left_height + right_height
        max_diameter = max(max_diameter, diameter_through_node)
        
        # Return height of this subtree
        return 1 + max(left_height, right_height)
    
    height(root)
    return max_diameter
```

**Trace through example**:
```
height(1):
├── height(2):
│   ├── height(4): left=0, right=0, diameter=0, return 1
│   ├── height(5): left=0, right=0, diameter=0, return 1
│   └── left=1, right=1, diameter=2, return 2
├── height(3): left=0, right=0, diameter=0, return 1
└── left=2, right=1, diameter=3, return 3

max_diameter = 3 ✓
```

---

## 8. Common Mistakes

1. **Forgetting base case for None nodes**
   ```python
   # Wrong
   def height(node):
       return 1 + max(height(node.left), height(node.right))  # Crashes on None
   
   # Correct
   def height(node):
       if not node:
           return 0  # or -1 depending on definition
       return 1 + max(height(node.left), height(node.right))
   ```

2. **Confusing height vs depth**
   ```python
   # Height: max edges from node DOWN to leaf
   # Depth: edges from root DOWN to node
   # They're different perspectives!
   ```

3. **Off-by-one errors in height/depth**
   ```python
   # Some definitions: height of single node = 0, height of empty = -1
   # Other definitions: height of single node = 1, height of empty = 0
   # Be consistent and match problem requirements!
   ```

4. **Stack overflow on unbalanced trees**
   ```python
   # Recursive solution fails on very deep trees (10000+ nodes in line)
   # Use iterative approach for safety
   import sys
   sys.setrecursionlimit(10001)  # Increase if needed
   ```

5. **Modifying tree during traversal**
   ```python
   # Wrong - modifying while iterating
   def remove_leaves(node):
       if node.left and not node.left.left and not node.left.right:
           node.left = None  # May cause issues
   
   # Better - use postorder to process children first
   ```

6. **Incorrect BFS level separation**
   ```python
   # Wrong - no level separation
   while queue:
       node = queue.popleft()
       # Process...
   
   # Correct - process entire level
   while queue:
       level_size = len(queue)
       for _ in range(level_size):
           node = queue.popleft()
           # Process...
   ```

---

## 9. Variations & Optimizations

### Morris Traversal (O(1) Space)

```python
def morris_inorder(root: Optional[TreeNode]) -> List[int]:
    """
    Inorder traversal using O(1) extra space.
    Uses threading: temporarily modifies tree structure.
    
    Key idea: For each node, make it the right child of its 
    inorder predecessor, so we can return to it after processing left subtree.
    
    Time: O(n), Space: O(1)
    """
    result = []
    current = root
    
    while current:
        if not current.left:
            # No left subtree, process current and go right
            result.append(current.val)
            current = current.right
        else:
            # Find inorder predecessor (rightmost in left subtree)
            predecessor = current.left
            while predecessor.right and predecessor.right != current:
                predecessor = predecessor.right
            
            if not predecessor.right:
                # Make current the right child of predecessor (create thread)
                predecessor.right = current
                current = current.left
            else:
                # Thread exists, we've returned - remove thread and process
                predecessor.right = None
                result.append(current.val)
                current = current.right
    
    return result
```

### Iterative Level-Order with Depth

```python
def level_order_with_depth(root: Optional[TreeNode]) -> List[tuple]:
    """Return (value, depth) pairs in level order."""
    if not root:
        return []
    
    result = []
    queue = deque([(root, 0)])
    
    while queue:
        node, depth = queue.popleft()
        result.append((node.val, depth))
        
        if node.left:
            queue.append((node.left, depth + 1))
        if node.right:
            queue.append((node.right, depth + 1))
    
    return result
```

### Boundary Traversal

```python
def boundary_of_binary_tree(root: Optional[TreeNode]) -> List[int]:
    """
    Return boundary values: left boundary + leaves + right boundary (reversed).
    """
    if not root:
        return []
    
    result = [root.val]
    
    def add_left_boundary(node):
        if not node or (not node.left and not node.right):
            return
        result.append(node.val)
        if node.left:
            add_left_boundary(node.left)
        else:
            add_left_boundary(node.right)
    
    def add_leaves(node):
        if not node:
            return
        if not node.left and not node.right:
            result.append(node.val)
            return
        add_leaves(node.left)
        add_leaves(node.right)
    
    def add_right_boundary(node):
        if not node or (not node.left and not node.right):
            return
        if node.right:
            add_right_boundary(node.right)
        else:
            add_right_boundary(node.left)
        result.append(node.val)  # Add in reverse
    
    add_left_boundary(root.left)
    add_leaves(root.left)
    add_leaves(root.right)
    add_right_boundary(root.right)
    
    return result
```

---

## 10. Interview Tips

### What Interviewers Look For

1. **Recursive thinking**: Can you break down tree problems into subproblems?
2. **Traversal mastery**: Know all 4 traversals cold (recursive and iterative)
3. **Edge case handling**: Empty tree, single node, skewed trees
4. **Space-time analysis**: Account for recursion stack in space complexity

### Communication Pattern

```
1. Clarify: "Is this a binary tree? Are values unique? Can tree be empty?"

2. Identify pattern: "This looks like a postorder problem because we need 
   children's results before processing parent."

3. Walk through approach: "I'll use DFS. For each node, I'll compute X from 
   left subtree, Y from right subtree, then combine for current node."

4. Code with commentary: Explain key decisions while writing

5. Test with example: Trace through recursion tree
```

### Quick Decision Framework

```
BFS (Level-order) when:
- Need level-by-level processing
- Shortest path in unweighted tree
- Need to find something at minimum depth

DFS (Pre/In/Post) when:
- Need to process all paths
- Problem requires bottom-up computation (postorder)
- Need to maintain state along path (preorder)
- BST operations (inorder for sorted order)
```

---

## 11. Related Patterns

| Pattern | Description | Example Problems |
|---------|-------------|------------------|
| Tree Recursion | Solve by combining subtree results | Height, diameter, balanced check |
| Tree BFS | Level-by-level traversal | Level order, minimum depth |
| Tree DFS | Path-based traversal | Path sum, root-to-leaf paths |
| Divide & Conquer | Split into subtrees | Construct from traversals |
| Two Pointers on Tree | Use two traversals simultaneously | Flatten to linked list |

---

## 12. References

- **CLRS**: Chapter 10 (Elementary Data Structures), Chapter 12 (Binary Search Trees)
- **LeetCode**: Tree category (100+ problems)
- **Grokking**: Tree BFS, Tree DFS patterns
- **Visualizations**: [VisuAlgo Trees](https://visualgo.net/en/bst)

---

## 13. Practice Problems

### Easy
| # | Problem | Key Concepts | LeetCode |
|---|---------|--------------|----------|
| 1 | Maximum Depth of Binary Tree | Basic recursion | [104](https://leetcode.com/problems/maximum-depth-of-binary-tree/) |
| 2 | Invert Binary Tree | Recursive swap | [226](https://leetcode.com/problems/invert-binary-tree/) |
| 3 | Same Tree | Two-tree recursion | [100](https://leetcode.com/problems/same-tree/) |
| 4 | Symmetric Tree | Mirror check | [101](https://leetcode.com/problems/symmetric-tree/) |
| 5 | Path Sum | Root-to-leaf sum | [112](https://leetcode.com/problems/path-sum/) |
| 6 | Minimum Depth of Binary Tree | BFS/DFS with early exit | [111](https://leetcode.com/problems/minimum-depth-of-binary-tree/) |
| 7 | Count Complete Tree Nodes | Binary search on tree | [222](https://leetcode.com/problems/count-complete-tree-nodes/) |

### Medium
| # | Problem | Key Concepts | LeetCode |
|---|---------|--------------|----------|
| 8 | Binary Tree Level Order Traversal | BFS with level tracking | [102](https://leetcode.com/problems/binary-tree-level-order-traversal/) |
| 9 | Binary Tree Zigzag Level Order | BFS with direction toggle | [103](https://leetcode.com/problems/binary-tree-zigzag-level-order-traversal/) |
| 10 | Construct Binary Tree from Preorder and Inorder | Divide & conquer | [105](https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/) |
| 11 | Construct Binary Tree from Inorder and Postorder | Divide & conquer | [106](https://leetcode.com/problems/construct-binary-tree-from-inorder-and-postorder-traversal/) |
| 12 | Flatten Binary Tree to Linked List | Preorder to linked list | [114](https://leetcode.com/problems/flatten-binary-tree-to-linked-list/) |
| 13 | Binary Tree Right Side View | Level order, last element | [199](https://leetcode.com/problems/binary-tree-right-side-view/) |
| 14 | Lowest Common Ancestor of Binary Tree | Tree recursion | [236](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/) |
| 15 | Path Sum II | DFS with path tracking | [113](https://leetcode.com/problems/path-sum-ii/) |
| 16 | Path Sum III | Prefix sum on tree | [437](https://leetcode.com/problems/path-sum-iii/) |
| 17 | Binary Tree Maximum Path Sum | Global max tracking | [124](https://leetcode.com/problems/binary-tree-maximum-path-sum/) |
| 18 | Diameter of Binary Tree | Height with diameter tracking | [543](https://leetcode.com/problems/diameter-of-binary-tree/) |
| 19 | Populating Next Right Pointers | Level linking | [116](https://leetcode.com/problems/populating-next-right-pointers-in-each-node/) |

### Hard
| # | Problem | Key Concepts | LeetCode |
|---|---------|--------------|----------|
| 20 | Serialize and Deserialize Binary Tree | Preorder + null markers | [297](https://leetcode.com/problems/serialize-and-deserialize-binary-tree/) |
| 21 | Binary Tree Cameras | Greedy postorder | [968](https://leetcode.com/problems/binary-tree-cameras/) |
| 22 | Vertical Order Traversal | BFS + coordinate sorting | [987](https://leetcode.com/problems/vertical-order-traversal-of-a-binary-tree/) |

---

## 14. Key Takeaways

1. **Master the 4 traversals** - preorder, inorder, postorder, level-order in both recursive and iterative forms

2. **Think recursively** - Most tree problems are solved by:
   - Base case: what happens for empty/leaf node?
   - Recursive case: how to combine left and right subtree results?

3. **Know when to use BFS vs DFS**:
   - BFS: level-by-level processing, minimum depth
   - DFS: path exploration, need to backtrack

4. **Space complexity includes call stack** - O(h) for balanced tree, O(n) for skewed

5. **Common patterns**:
   - Height/depth calculation
   - Path sum problems
   - Tree construction from traversals
   - Serialization/deserialization

---

## 15. Spaced Repetition Prompts

1. **Q**: What are the 4 binary tree traversal orders and their use cases?
   **A**: Preorder (Root-L-R): copy tree, serialize. Inorder (L-Root-R): BST sorted order. Postorder (L-R-Root): deletion, expression evaluation. Level-order: shortest path, level processing.

2. **Q**: How do you convert recursive DFS to iterative?
   **A**: Use explicit stack. For preorder: push root, pop and process, push right then left. For inorder: go left while possible (pushing), pop and process, go right.

3. **Q**: What's the space complexity of recursive tree traversal?
   **A**: O(h) where h is height. O(log n) for balanced, O(n) for skewed/worst case.

4. **Q**: How do you construct a tree from preorder + inorder?
   **A**: First element of preorder is root. Find root in inorder - left part is left subtree, right part is right subtree. Recurse.

5. **Q**: What's the key insight for tree diameter problem?
   **A**: Diameter through any node = left_height + right_height. Track max diameter globally while computing heights bottom-up.
