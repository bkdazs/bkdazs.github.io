---
title: "Tree Algorithms - LCA, Binary Lifting, Serialization"
topic: "Trees"
difficulty: "Medium-Hard"
tags: ["tree", "lca", "binary-lifting", "serialization", "morris-traversal"]
status: "complete"
weight: 19
---

# Advanced Tree Algorithms

## 📚 Summary

Advanced tree algorithms including Lowest Common Ancestor (LCA), Binary Lifting for O(log n) ancestor queries, tree serialization/deserialization, and Morris traversal for O(1) space traversal.

---

## 1️⃣ Lowest Common Ancestor (LCA)

### LCA - Basic (LC 236)

```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right


def lowest_common_ancestor(root: TreeNode, p: TreeNode, q: TreeNode) -> TreeNode:
    """
    Find LCA of two nodes p and q
    Time: O(n), Space: O(h)
    """
    if not root or root == p or root == q:
        return root
    
    left = lowest_common_ancestor(root.left, p, q)
    right = lowest_common_ancestor(root.right, p, q)
    
    if left and right:
        return root
    
    return left if left else right


def lca_iterative(root: TreeNode, p: TreeNode, q: TreeNode) -> TreeNode:
    """Iterative with parent pointers"""
    parent = {root: None}
    stack = [root]
    
    # Build parent pointers until we find both nodes
    while p not in parent or q not in parent:
        node = stack.pop()
        if node.left:
            parent[node.left] = node
            stack.append(node.left)
        if node.right:
            parent[node.right] = node
            stack.append(node.right)
    
    # Find ancestors of p
    ancestors = set()
    while p:
        ancestors.add(p)
        p = parent[p]
    
    # Find first common ancestor
    while q not in ancestors:
        q = parent[q]
    
    return q
```

### LCA - BST (LC 235)

```python
def lca_bst(root: TreeNode, p: TreeNode, q: TreeNode) -> TreeNode:
    """
    LCA in BST - exploit BST property
    Time: O(h)
    """
    while root:
        if p.val < root.val and q.val < root.val:
            root = root.left
        elif p.val > root.val and q.val > root.val:
            root = root.right
        else:
            return root
    return None
```

### LCA with Parent Pointers (LC 1650)

```python
class Node:
    def __init__(self, val=0, left=None, right=None, parent=None):
        self.val = val
        self.left = left
        self.right = right
        self.parent = parent


def lca_with_parent(p: 'Node', q: 'Node') -> 'Node':
    """
    LCA when nodes have parent pointers
    Similar to finding intersection of two linked lists
    """
    a, b = p, q
    
    while a != b:
        a = a.parent if a else q
        b = b.parent if b else p
    
    return a


def lca_with_parent_depth(p: 'Node', q: 'Node') -> 'Node':
    """Alternative: equalize depths first"""
    def get_depth(node: 'Node') -> int:
        depth = 0
        while node.parent:
            depth += 1
            node = node.parent
        return depth
    
    depth_p = get_depth(p)
    depth_q = get_depth(q)
    
    # Move deeper node up
    while depth_p > depth_q:
        p = p.parent
        depth_p -= 1
    while depth_q > depth_p:
        q = q.parent
        depth_q -= 1
    
    # Move both up together
    while p != q:
        p = p.parent
        q = q.parent
    
    return p
```

---

## 2️⃣ Binary Lifting

```python
"""
Binary Lifting: Preprocess to answer kth ancestor queries in O(log n)

Key idea: 
- up[node][j] = 2^j-th ancestor of node
- up[node][j] = up[up[node][j-1]][j-1]
"""

import math

class BinaryLifting:
    """
    Binary lifting for LCA and kth ancestor queries
    Preprocessing: O(n log n)
    Query: O(log n)
    """
    
    def __init__(self, n: int, parent: list[int], root: int = 0):
        """
        n: number of nodes
        parent: parent[i] = parent of node i (-1 for root)
        """
        self.n = n
        self.LOG = max(1, math.ceil(math.log2(n)))
        
        # up[node][j] = 2^j ancestor of node
        self.up = [[-1] * self.LOG for _ in range(n)]
        self.depth = [0] * n
        
        # Initialize with direct parents
        for i in range(n):
            self.up[i][0] = parent[i]
        
        # Build sparse table
        for j in range(1, self.LOG):
            for i in range(n):
                if self.up[i][j - 1] != -1:
                    self.up[i][j] = self.up[self.up[i][j - 1]][j - 1]
        
        # Compute depths using BFS
        self._compute_depths(root, parent)
    
    def _compute_depths(self, root: int, parent: list[int]):
        """Compute depth of each node"""
        from collections import deque
        
        # Build adjacency list
        children = [[] for _ in range(self.n)]
        for i in range(self.n):
            if parent[i] != -1:
                children[parent[i]].append(i)
        
        queue = deque([root])
        self.depth[root] = 0
        
        while queue:
            node = queue.popleft()
            for child in children[node]:
                self.depth[child] = self.depth[node] + 1
                queue.append(child)
    
    def kth_ancestor(self, node: int, k: int) -> int:
        """Find k-th ancestor of node"""
        for j in range(self.LOG):
            if node == -1:
                break
            if k & (1 << j):
                node = self.up[node][j]
        return node
    
    def lca(self, u: int, v: int) -> int:
        """Find LCA of nodes u and v"""
        # Make u the deeper node
        if self.depth[u] < self.depth[v]:
            u, v = v, u
        
        # Bring u to same depth as v
        diff = self.depth[u] - self.depth[v]
        u = self.kth_ancestor(u, diff)
        
        if u == v:
            return u
        
        # Binary search for LCA
        for j in range(self.LOG - 1, -1, -1):
            if self.up[u][j] != self.up[v][j]:
                u = self.up[u][j]
                v = self.up[v][j]
        
        return self.up[u][0]
    
    def distance(self, u: int, v: int) -> int:
        """Distance between two nodes"""
        ancestor = self.lca(u, v)
        return self.depth[u] + self.depth[v] - 2 * self.depth[ancestor]


# LC 1483: Kth Ancestor of a Tree Node
class TreeAncestor:
    """LeetCode version"""
    
    def __init__(self, n: int, parent: list[int]):
        self.LOG = 20  # Enough for n up to 10^6
        self.up = [[-1] * self.LOG for _ in range(n)]
        
        for i in range(n):
            self.up[i][0] = parent[i]
        
        for j in range(1, self.LOG):
            for i in range(n):
                if self.up[i][j - 1] != -1:
                    self.up[i][j] = self.up[self.up[i][j - 1]][j - 1]
    
    def getKthAncestor(self, node: int, k: int) -> int:
        for j in range(self.LOG):
            if node == -1:
                break
            if k & (1 << j):
                node = self.up[node][j]
        return node
```

---

## 3️⃣ Tree Serialization/Deserialization (LC 297)

### BFS Serialization

```python
from collections import deque

class Codec:
    """Serialize/Deserialize Binary Tree using BFS"""
    
    def serialize(self, root: TreeNode) -> str:
        """Encodes tree to a string"""
        if not root:
            return ""
        
        result = []
        queue = deque([root])
        
        while queue:
            node = queue.popleft()
            if node:
                result.append(str(node.val))
                queue.append(node.left)
                queue.append(node.right)
            else:
                result.append("null")
        
        # Remove trailing nulls
        while result and result[-1] == "null":
            result.pop()
        
        return ",".join(result)
    
    def deserialize(self, data: str) -> TreeNode:
        """Decodes string to tree"""
        if not data:
            return None
        
        values = data.split(",")
        root = TreeNode(int(values[0]))
        queue = deque([root])
        i = 1
        
        while queue and i < len(values):
            node = queue.popleft()
            
            # Left child
            if i < len(values) and values[i] != "null":
                node.left = TreeNode(int(values[i]))
                queue.append(node.left)
            i += 1
            
            # Right child
            if i < len(values) and values[i] != "null":
                node.right = TreeNode(int(values[i]))
                queue.append(node.right)
            i += 1
        
        return root
```

### Preorder Serialization

```python
class CodecPreorder:
    """Serialize using preorder traversal"""
    
    def serialize(self, root: TreeNode) -> str:
        """Preorder serialization"""
        def dfs(node):
            if not node:
                result.append("null")
                return
            result.append(str(node.val))
            dfs(node.left)
            dfs(node.right)
        
        result = []
        dfs(root)
        return ",".join(result)
    
    def deserialize(self, data: str) -> TreeNode:
        """Reconstruct from preorder"""
        values = iter(data.split(","))
        
        def dfs():
            val = next(values)
            if val == "null":
                return None
            node = TreeNode(int(val))
            node.left = dfs()
            node.right = dfs()
            return node
        
        return dfs()
```

### BST Serialization (LC 449)

```python
class CodecBST:
    """
    BST serialization - no need for null markers
    Using preorder, BST property determines structure
    """
    
    def serialize(self, root: TreeNode) -> str:
        """Preorder without null markers"""
        if not root:
            return ""
        
        result = []
        
        def preorder(node):
            if node:
                result.append(str(node.val))
                preorder(node.left)
                preorder(node.right)
        
        preorder(root)
        return ",".join(result)
    
    def deserialize(self, data: str) -> TreeNode:
        """Reconstruct BST from preorder"""
        if not data:
            return None
        
        values = [int(x) for x in data.split(",")]
        
        def build(min_val: float, max_val: float) -> TreeNode:
            if not values or values[0] < min_val or values[0] > max_val:
                return None
            
            val = values.pop(0)
            node = TreeNode(val)
            node.left = build(min_val, val)
            node.right = build(val, max_val)
            return node
        
        return build(float('-inf'), float('inf'))
```

---

## 4️⃣ Morris Traversal

```python
"""
Morris Traversal: O(1) space tree traversal
Uses threading to return from leaves without stack

Key idea: Temporarily modify tree by creating links from 
rightmost node of left subtree back to current node
"""

def morris_inorder(root: TreeNode) -> list[int]:
    """
    Inorder traversal with O(1) space
    Time: O(n)
    """
    result = []
    current = root
    
    while current:
        if not current.left:
            # No left subtree - visit and go right
            result.append(current.val)
            current = current.right
        else:
            # Find inorder predecessor (rightmost in left subtree)
            predecessor = current.left
            while predecessor.right and predecessor.right != current:
                predecessor = predecessor.right
            
            if not predecessor.right:
                # Create thread back to current
                predecessor.right = current
                current = current.left
            else:
                # Thread exists - we've returned
                # Remove thread and visit current
                predecessor.right = None
                result.append(current.val)
                current = current.right
    
    return result


def morris_preorder(root: TreeNode) -> list[int]:
    """Preorder with O(1) space"""
    result = []
    current = root
    
    while current:
        if not current.left:
            result.append(current.val)
            current = current.right
        else:
            predecessor = current.left
            while predecessor.right and predecessor.right != current:
                predecessor = predecessor.right
            
            if not predecessor.right:
                # Visit before going left (preorder)
                result.append(current.val)
                predecessor.right = current
                current = current.left
            else:
                predecessor.right = None
                current = current.right
    
    return result


def flatten_to_linked_list_morris(root: TreeNode) -> None:
    """
    LC 114: Flatten tree to linked list in-place
    Morris-based O(1) space solution
    """
    current = root
    
    while current:
        if current.left:
            # Find rightmost node in left subtree
            rightmost = current.left
            while rightmost.right:
                rightmost = rightmost.right
            
            # Connect rightmost to current's right
            rightmost.right = current.right
            # Move left subtree to right
            current.right = current.left
            current.left = None
        
        current = current.right


def recover_bst_morris(root: TreeNode) -> None:
    """
    LC 99: Recover BST with O(1) space
    Two nodes are swapped - find and fix them
    """
    first = second = prev = None
    current = root
    
    while current:
        if not current.left:
            # Process current
            if prev and prev.val > current.val:
                if not first:
                    first = prev
                second = current
            prev = current
            current = current.right
        else:
            predecessor = current.left
            while predecessor.right and predecessor.right != current:
                predecessor = predecessor.right
            
            if not predecessor.right:
                predecessor.right = current
                current = current.left
            else:
                predecessor.right = None
                # Process current
                if prev and prev.val > current.val:
                    if not first:
                        first = prev
                    second = current
                prev = current
                current = current.right
    
    # Swap values
    if first and second:
        first.val, second.val = second.val, first.val
```

---

## 5️⃣ Tree Distance Problems

### Diameter of Tree (LC 543)

```python
def diameter_of_binary_tree(root: TreeNode) -> int:
    """
    Longest path between any two nodes
    """
    diameter = 0
    
    def height(node: TreeNode) -> int:
        nonlocal diameter
        if not node:
            return 0
        
        left_height = height(node.left)
        right_height = height(node.right)
        
        # Update diameter
        diameter = max(diameter, left_height + right_height)
        
        return 1 + max(left_height, right_height)
    
    height(root)
    return diameter
```

### Distance Between Nodes

```python
def distance_between_nodes(root: TreeNode, p: int, q: int) -> int:
    """
    Distance between two nodes
    = depth(p) + depth(q) - 2 * depth(LCA)
    """
    def find_lca_and_depths(node, target1, target2, depth):
        if not node:
            return None, -1, -1
        
        if node.val == target1:
            d1 = depth
        else:
            d1 = -1
        
        if node.val == target2:
            d2 = depth
        else:
            d2 = -1
        
        left_lca, left_d1, left_d2 = find_lca_and_depths(
            node.left, target1, target2, depth + 1
        )
        right_lca, right_d1, right_d2 = find_lca_and_depths(
            node.right, target1, target2, depth + 1
        )
        
        # Combine depths
        d1 = max(d1, left_d1, right_d1)
        d2 = max(d2, left_d2, right_d2)
        
        # Determine LCA
        if left_lca:
            lca = left_lca
        elif right_lca:
            lca = right_lca
        elif (d1 != -1 and d2 != -1 and 
              (node.val == target1 or node.val == target2 or 
               (left_d1 != -1 or left_d2 != -1) and (right_d1 != -1 or right_d2 != -1))):
            lca = node
        else:
            lca = None
        
        return lca, d1, d2
    
    lca, depth1, depth2 = find_lca_and_depths(root, p, q, 0)
    
    # Find LCA depth
    def get_depth(node, target, depth):
        if not node:
            return -1
        if node.val == target:
            return depth
        left = get_depth(node.left, target, depth + 1)
        if left != -1:
            return left
        return get_depth(node.right, target, depth + 1)
    
    lca_depth = get_depth(root, lca.val, 0)
    return depth1 + depth2 - 2 * lca_depth
```

---

## 6️⃣ Tree Isomorphism and Subtree

### Subtree of Another Tree (LC 572)

```python
def is_subtree(root: TreeNode, subRoot: TreeNode) -> bool:
    """Check if subRoot is subtree of root"""
    def is_same(t1: TreeNode, t2: TreeNode) -> bool:
        if not t1 and not t2:
            return True
        if not t1 or not t2:
            return False
        return (t1.val == t2.val and 
                is_same(t1.left, t2.left) and 
                is_same(t1.right, t2.right))
    
    def dfs(node: TreeNode) -> bool:
        if not node:
            return False
        if is_same(node, subRoot):
            return True
        return dfs(node.left) or dfs(node.right)
    
    return dfs(root)


def is_subtree_merkle(root: TreeNode, subRoot: TreeNode) -> bool:
    """O(n) using Merkle hashing"""
    def get_hash(node: TreeNode) -> str:
        if not node:
            return "#"
        left_hash = get_hash(node.left)
        right_hash = get_hash(node.right)
        return f"({left_hash}{node.val}{right_hash})"
    
    def find_hash(node: TreeNode, target: str) -> bool:
        if not node:
            return False
        if get_hash(node) == target:
            return True
        return find_hash(node.left, target) or find_hash(node.right, target)
    
    target_hash = get_hash(subRoot)
    return find_hash(root, target_hash)
```

---

## ⏱️ Complexity Summary

| Algorithm | Preprocessing | Query | Space |
|-----------|--------------|-------|-------|
| LCA Basic | - | O(n) | O(h) |
| Binary Lifting | O(n log n) | O(log n) | O(n log n) |
| Morris Traversal | - | O(n) | O(1) |
| Serialization | O(n) | - | O(n) |

---

## 📚 Practice Problems

| # | Problem | Key Concept |
|---|---------|-------------|
| 1 | [LC 236: LCA Binary Tree](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/) | Basic LCA |
| 2 | [LC 235: LCA BST](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-search-tree/) | BST LCA |
| 3 | [LC 1650: LCA III](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree-iii/) | Parent pointers |
| 4 | [LC 1483: Kth Ancestor](https://leetcode.com/problems/kth-ancestor-of-a-tree-node/) | Binary lifting |
| 5 | [LC 297: Serialize Tree](https://leetcode.com/problems/serialize-and-deserialize-binary-tree/) | Serialization |
| 6 | [LC 449: Serialize BST](https://leetcode.com/problems/serialize-and-deserialize-bst/) | BST serialization |
| 7 | [LC 114: Flatten to List](https://leetcode.com/problems/flatten-binary-tree-to-linked-list/) | Morris variant |
| 8 | [LC 99: Recover BST](https://leetcode.com/problems/recover-binary-search-tree/) | Morris + swapped |
| 9 | [LC 543: Diameter](https://leetcode.com/problems/diameter-of-binary-tree/) | Distance |
| 10 | [LC 572: Subtree](https://leetcode.com/problems/subtree-of-another-tree/) | Isomorphism |
| 11 | [LC 1123: LCA Deepest Leaves](https://leetcode.com/problems/lowest-common-ancestor-of-deepest-leaves/) | LCA variant |
| 12 | [LC 1644: LCA II](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree-ii/) | May not exist |
| 13 | [LC 1676: LCA IV](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree-iv/) | Multiple nodes |
| 14 | [LC 863: All Nodes Distance K](https://leetcode.com/problems/all-nodes-distance-k-in-binary-tree/) | Distance |
| 15 | [LC 979: Distribute Coins](https://leetcode.com/problems/distribute-coins-in-binary-tree/) | Post-order |

---

*Last Updated: 2024*
