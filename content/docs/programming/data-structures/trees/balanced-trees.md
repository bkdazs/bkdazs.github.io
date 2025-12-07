---
title: Balanced Trees (AVL, Red-Black)
topic: Balanced Trees
difficulty: Hard
tags: [avl, red-black, balanced-bst, self-balancing, rotations]
status: complete
weight: 11
---

# Balanced Trees (AVL, Red-Black)

## 1. Summary / TL;DR

- **Balanced BSTs** guarantee O(log n) height, ensuring O(log n) worst-case operations
- **AVL Trees**: Strictly balanced (height difference ≤ 1), faster lookups
- **Red-Black Trees**: Relaxed balance (height ≤ 2log n), faster insertions/deletions
- **Rotations** are key operations: single (left/right) and double (left-right/right-left)
- Used in: TreeMap/TreeSet (Java), map/set (C++ STL), databases, schedulers
- For interviews: Understand concepts and rotations; rarely asked to implement fully

---

## 2. When & Where to Use

| Data Structure | Best For | Real-World Usage |
|----------------|----------|------------------|
| AVL Tree | Read-heavy workloads | Databases, lookup tables |
| Red-Black Tree | Insert/delete-heavy workloads | C++ STL, Java TreeMap, Linux kernel |
| Splay Tree | Frequently accessed items | Caches, recent items |
| B-Tree | Disk-based storage | Databases, file systems |

### Why Balance Matters

```
Unbalanced BST (worst case):     Balanced BST:
         1                           4
          \                        /   \
           2                      2     6
            \                    / \   / \
             3                  1   3 5   7
              \
               4            Height: O(log n)
                \           Operations: O(log n)
                 5
                  \
                   6
                    \
                     7
                     
Height: O(n)
Operations: O(n)
```

---

## 3. Time & Space Complexity

### Comparison of Balanced BSTs

| Operation | AVL Tree | Red-Black Tree | Splay Tree |
|-----------|----------|----------------|------------|
| Search | O(log n) | O(log n) | O(log n) amortized |
| Insert | O(log n) | O(log n) | O(log n) amortized |
| Delete | O(log n) | O(log n) | O(log n) amortized |
| Rotations (Insert) | ≤ 2 | ≤ 2 | O(log n) |
| Rotations (Delete) | O(log n) | ≤ 3 | O(log n) |
| Height | ≤ 1.44 log n | ≤ 2 log n | O(n) worst |

**Space**: O(n) for all balanced BSTs

---

## 4. Core Concepts & Theory

### AVL Tree

Named after inventors **A**delson-**V**elsky and **L**andis (1962).

**AVL Property**: For every node, the heights of left and right subtrees differ by at most 1.

**Balance Factor** = height(left subtree) - height(right subtree)
- Valid values: -1, 0, +1
- If |BF| > 1, tree needs rebalancing

```
Valid AVL:           Invalid AVL:
     10 (BF=0)           10 (BF=-2)
    /  \                    \
   5    15 (BF=0)            20 (BF=-1)
                               \
                                30

Heights: left=1, right=1    Heights: left=0, right=2
BF = 1 - 1 = 0 ✓            BF = 0 - 2 = -2 ✗
```

### Red-Black Tree

**Red-Black Properties** (invariants):
1. Every node is either red or black
2. Root is always black
3. All leaves (NIL/null) are black
4. Red node cannot have red child (no red-red)
5. Every path from root to leaf has same number of black nodes (black-height)

```
         8(B)
        /    \
      4(R)   12(R)
     /  \    /   \
   2(B) 6(B) 10(B) 14(B)

B = Black, R = Red
Black-height from root: 2 (each path has 2 black nodes excluding root)
```

### Rotations

The fundamental operation for rebalancing.

**Right Rotation** (at node y):
```
      y                x
     / \              / \
    x   C    →       A   y
   / \                  / \
  A   B                B   C
```

**Left Rotation** (at node x):
```
    x                  y
   / \                / \
  A   y      →       x   C
     / \            / \
    B   C          A   B
```

---

## 5. Diagrams / Visualizations

### AVL Rotation Cases

```
Case 1: Left-Left (LL) - Single Right Rotation
Insert 5 into:
      30                    20
     /                     /  \
    20          →         10   30
   /                     /
  10                    5
 /
5

Case 2: Right-Right (RR) - Single Left Rotation
Insert 35 into:
  10                       20
    \                     /  \
     20         →       10   30
       \                       \
        30                      35
          \
           35

Case 3: Left-Right (LR) - Double Rotation (Left then Right)
Insert 15 into:
      30                   30                    20
     /                    /                     /  \
    10          →        20         →         10   30
      \                 /
       20              10
      /                  \
     15                   15

Case 4: Right-Left (RL) - Double Rotation (Right then Left)
Insert 25 into:
  10                     10                      20
    \                      \                    /  \
     30         →           20      →         10   30
    /                         \
   20                          30
     \                        /
      25                     25
```

### Red-Black Insert Rebalancing

```
Case 1: Uncle is Red → Recolor
        G(B)                    G(R)
       /   \                   /   \
     P(R)   U(R)    →       P(B)   U(B)
     /                       /
   N(R)                    N(R)

Then check G's parent (may need to continue up)

Case 2: Uncle is Black, Node is outer child → Rotate
        G(B)                    P(B)
       /   \                   /   \
     P(R)   U(B)    →       N(R)   G(R)
     /                               \
   N(R)                              U(B)

Case 3: Uncle is Black, Node is inner child → Double Rotate
        G(B)                    G(B)                    N(B)
       /   \                   /   \                   /   \
     P(R)   U(B)    →       N(R)   U(B)    →       P(R)   G(R)
       \                   /                               \
       N(R)              P(R)                              U(B)
```

---

## 6. Implementation (Python)

### AVL Tree Node

```python
from typing import Optional, List

class AVLNode:
    """AVL Tree node with height tracking."""
    
    def __init__(self, val: int):
        self.val = val
        self.left: Optional['AVLNode'] = None
        self.right: Optional['AVLNode'] = None
        self.height: int = 1  # Height of subtree rooted at this node
    
    def __repr__(self) -> str:
        return f"AVLNode({self.val}, h={self.height})"
```

### Complete AVL Tree Implementation

```python
class AVLTree:
    """
    AVL Tree implementation with automatic balancing.
    
    Maintains AVL property: |balance_factor| <= 1 for all nodes.
    """
    
    def __init__(self):
        self.root: Optional[AVLNode] = None
    
    def _get_height(self, node: Optional[AVLNode]) -> int:
        """Get height of node (0 for None)."""
        return node.height if node else 0
    
    def _get_balance(self, node: Optional[AVLNode]) -> int:
        """Get balance factor: height(left) - height(right)."""
        if not node:
            return 0
        return self._get_height(node.left) - self._get_height(node.right)
    
    def _update_height(self, node: AVLNode) -> None:
        """Update height based on children's heights."""
        node.height = 1 + max(self._get_height(node.left),
                             self._get_height(node.right))
    
    def _right_rotate(self, y: AVLNode) -> AVLNode:
        """
        Right rotation at node y.
        
              y                x
             / \              / \
            x   C    →       A   y
           / \                  / \
          A   B                B   C
        
        Returns new root of subtree (x).
        """
        x = y.left
        B = x.right
        
        # Perform rotation
        x.right = y
        y.left = B
        
        # Update heights (y first, then x)
        self._update_height(y)
        self._update_height(x)
        
        return x
    
    def _left_rotate(self, x: AVLNode) -> AVLNode:
        """
        Left rotation at node x.
        
            x                  y
           / \                / \
          A   y      →       x   C
             / \            / \
            B   C          A   B
        
        Returns new root of subtree (y).
        """
        y = x.right
        B = y.left
        
        # Perform rotation
        y.left = x
        x.right = B
        
        # Update heights (x first, then y)
        self._update_height(x)
        self._update_height(y)
        
        return y
    
    def insert(self, val: int) -> None:
        """
        Insert value into AVL tree.
        
        Time: O(log n)
        
        >>> avl = AVLTree()
        >>> for v in [10, 20, 30, 40, 50, 25]: avl.insert(v)
        >>> avl.inorder()
        [10, 20, 25, 30, 40, 50]
        """
        self.root = self._insert(self.root, val)
    
    def _insert(self, node: Optional[AVLNode], val: int) -> AVLNode:
        """Recursive insert with rebalancing."""
        # 1. Standard BST insert
        if not node:
            return AVLNode(val)
        
        if val < node.val:
            node.left = self._insert(node.left, val)
        else:
            node.right = self._insert(node.right, val)
        
        # 2. Update height
        self._update_height(node)
        
        # 3. Get balance factor
        balance = self._get_balance(node)
        
        # 4. Rebalance if needed (4 cases)
        
        # Left-Left Case
        if balance > 1 and val < node.left.val:
            return self._right_rotate(node)
        
        # Right-Right Case
        if balance < -1 and val > node.right.val:
            return self._left_rotate(node)
        
        # Left-Right Case
        if balance > 1 and val > node.left.val:
            node.left = self._left_rotate(node.left)
            return self._right_rotate(node)
        
        # Right-Left Case
        if balance < -1 and val < node.right.val:
            node.right = self._right_rotate(node.right)
            return self._left_rotate(node)
        
        return node
    
    def delete(self, val: int) -> None:
        """
        Delete value from AVL tree.
        
        Time: O(log n)
        """
        self.root = self._delete(self.root, val)
    
    def _delete(self, node: Optional[AVLNode], val: int) -> Optional[AVLNode]:
        """Recursive delete with rebalancing."""
        # 1. Standard BST delete
        if not node:
            return None
        
        if val < node.val:
            node.left = self._delete(node.left, val)
        elif val > node.val:
            node.right = self._delete(node.right, val)
        else:
            # Node to delete found
            if not node.left:
                return node.right
            elif not node.right:
                return node.left
            
            # Two children: get inorder successor
            successor = self._get_min_node(node.right)
            node.val = successor.val
            node.right = self._delete(node.right, successor.val)
        
        # 2. Update height
        self._update_height(node)
        
        # 3. Get balance factor
        balance = self._get_balance(node)
        
        # 4. Rebalance if needed
        
        # Left-Left Case
        if balance > 1 and self._get_balance(node.left) >= 0:
            return self._right_rotate(node)
        
        # Left-Right Case
        if balance > 1 and self._get_balance(node.left) < 0:
            node.left = self._left_rotate(node.left)
            return self._right_rotate(node)
        
        # Right-Right Case
        if balance < -1 and self._get_balance(node.right) <= 0:
            return self._left_rotate(node)
        
        # Right-Left Case
        if balance < -1 and self._get_balance(node.right) > 0:
            node.right = self._right_rotate(node.right)
            return self._left_rotate(node)
        
        return node
    
    def _get_min_node(self, node: AVLNode) -> AVLNode:
        """Get node with minimum value in subtree."""
        current = node
        while current.left:
            current = current.left
        return current
    
    def search(self, val: int) -> bool:
        """Search for value in AVL tree."""
        return self._search(self.root, val)
    
    def _search(self, node: Optional[AVLNode], val: int) -> bool:
        if not node:
            return False
        if val == node.val:
            return True
        elif val < node.val:
            return self._search(node.left, val)
        else:
            return self._search(node.right, val)
    
    def inorder(self) -> List[int]:
        """Return inorder traversal (sorted)."""
        result = []
        self._inorder(self.root, result)
        return result
    
    def _inorder(self, node: Optional[AVLNode], result: List[int]) -> None:
        if node:
            self._inorder(node.left, result)
            result.append(node.val)
            self._inorder(node.right, result)
    
    def get_height(self) -> int:
        """Return height of tree."""
        return self._get_height(self.root)
    
    def is_balanced(self) -> bool:
        """Verify AVL property holds for entire tree."""
        return self._is_balanced(self.root)
    
    def _is_balanced(self, node: Optional[AVLNode]) -> bool:
        if not node:
            return True
        
        balance = self._get_balance(node)
        if abs(balance) > 1:
            return False
        
        return self._is_balanced(node.left) and self._is_balanced(node.right)
```

### Red-Black Tree Node

```python
class Color:
    RED = True
    BLACK = False

class RBNode:
    """Red-Black Tree node."""
    
    def __init__(self, val: int, color: bool = Color.RED):
        self.val = val
        self.color = color
        self.left: Optional['RBNode'] = None
        self.right: Optional['RBNode'] = None
        self.parent: Optional['RBNode'] = None
    
    def __repr__(self) -> str:
        c = "R" if self.color == Color.RED else "B"
        return f"RBNode({self.val}, {c})"
```

### Simplified Red-Black Tree (Left-Leaning)

```python
class LeftLeaningRedBlackTree:
    """
    Left-Leaning Red-Black Tree (LLRB).
    
    Simplified variant where red links lean left.
    Easier to implement while maintaining O(log n) guarantees.
    
    Based on Robert Sedgewick's 2008 paper.
    """
    
    def __init__(self):
        self.root: Optional[RBNode] = None
    
    def _is_red(self, node: Optional[RBNode]) -> bool:
        """Check if node is red (None is black)."""
        if not node:
            return False
        return node.color == Color.RED
    
    def _rotate_left(self, h: RBNode) -> RBNode:
        """
        Rotate left at h.
        
            h                  x
           / \                / \
          A   x(R)    →    h(R)  C
             / \          / \
            B   C        A   B
        """
        x = h.right
        h.right = x.left
        x.left = h
        x.color = h.color
        h.color = Color.RED
        return x
    
    def _rotate_right(self, h: RBNode) -> RBNode:
        """
        Rotate right at h.
        
              h               x
             / \             / \
          x(R)  C    →      A  h(R)
         / \                  / \
        A   B                B   C
        """
        x = h.left
        h.left = x.right
        x.right = h
        x.color = h.color
        h.color = Color.RED
        return x
    
    def _flip_colors(self, h: RBNode) -> None:
        """
        Flip colors of h and its children.
        Used when both children are red.
        
            h(B)              h(R)
           /   \             /   \
        l(R)   r(R)  →    l(B)   r(B)
        """
        h.color = not h.color
        h.left.color = not h.left.color
        h.right.color = not h.right.color
    
    def insert(self, val: int) -> None:
        """
        Insert value into LLRB tree.
        
        Time: O(log n)
        
        >>> rbt = LeftLeaningRedBlackTree()
        >>> for v in [10, 20, 30, 15, 25, 5]: rbt.insert(v)
        >>> rbt.inorder()
        [5, 10, 15, 20, 25, 30]
        """
        self.root = self._insert(self.root, val)
        self.root.color = Color.BLACK  # Root is always black
    
    def _insert(self, h: Optional[RBNode], val: int) -> RBNode:
        """Recursive insert with rebalancing."""
        # Standard BST insert
        if not h:
            return RBNode(val, Color.RED)
        
        if val < h.val:
            h.left = self._insert(h.left, val)
        elif val > h.val:
            h.right = self._insert(h.right, val)
        else:
            h.val = val  # Update value if exists
        
        # Fix-up: maintain LLRB invariants
        
        # Right red link? Rotate left
        if self._is_red(h.right) and not self._is_red(h.left):
            h = self._rotate_left(h)
        
        # Two consecutive left red links? Rotate right
        if self._is_red(h.left) and self._is_red(h.left.left if h.left else None):
            h = self._rotate_right(h)
        
        # Both children red? Flip colors
        if self._is_red(h.left) and self._is_red(h.right):
            self._flip_colors(h)
        
        return h
    
    def search(self, val: int) -> bool:
        """Search for value."""
        node = self.root
        while node:
            if val < node.val:
                node = node.left
            elif val > node.val:
                node = node.right
            else:
                return True
        return False
    
    def inorder(self) -> List[int]:
        """Return inorder traversal."""
        result = []
        self._inorder(self.root, result)
        return result
    
    def _inorder(self, node: Optional[RBNode], result: List[int]) -> None:
        if node:
            self._inorder(node.left, result)
            result.append(node.val)
            self._inorder(node.right, result)
    
    def get_height(self) -> int:
        """Get height of tree."""
        return self._height(self.root)
    
    def _height(self, node: Optional[RBNode]) -> int:
        if not node:
            return 0
        return 1 + max(self._height(node.left), self._height(node.right))
```

### Check If Tree is Balanced (LeetCode 110)

```python
def is_balanced(root: Optional[AVLNode]) -> bool:
    """
    Check if binary tree is height-balanced.
    A tree is balanced if heights of subtrees differ by at most 1.
    
    Time: O(n), Space: O(h)
    
    >>> # Create balanced tree
    >>> root = AVLNode(1)
    >>> root.left, root.right = AVLNode(2), AVLNode(3)
    >>> is_balanced(root)
    True
    """
    def check_height(node: Optional[AVLNode]) -> int:
        """Return height if balanced, -1 if not."""
        if not node:
            return 0
        
        left_height = check_height(node.left)
        if left_height == -1:
            return -1
        
        right_height = check_height(node.right)
        if right_height == -1:
            return -1
        
        if abs(left_height - right_height) > 1:
            return -1
        
        return 1 + max(left_height, right_height)
    
    return check_height(root) != -1
```

### Balance a BST (LeetCode 1382)

```python
def balance_bst(root: Optional[AVLNode]) -> Optional[AVLNode]:
    """
    Convert BST to balanced BST.
    
    Approach: 
    1. Inorder traversal to get sorted array
    2. Build balanced BST from sorted array
    
    Time: O(n), Space: O(n)
    
    >>> # Create unbalanced BST: 1 -> 2 -> 3 -> 4
    >>> root = AVLNode(1)
    >>> root.right = AVLNode(2)
    >>> root.right.right = AVLNode(3)
    >>> root.right.right.right = AVLNode(4)
    >>> balanced = balance_bst(root)
    >>> balanced.val  # Middle element
    2
    """
    # Step 1: Get sorted values
    values = []
    
    def inorder(node: Optional[AVLNode]) -> None:
        if node:
            inorder(node.left)
            values.append(node.val)
            inorder(node.right)
    
    inorder(root)
    
    # Step 2: Build balanced BST
    def build(left: int, right: int) -> Optional[AVLNode]:
        if left > right:
            return None
        
        mid = (left + right) // 2
        node = AVLNode(values[mid])
        node.left = build(left, mid - 1)
        node.right = build(mid + 1, right)
        return node
    
    return build(0, len(values) - 1)
```

### Using Python's sortedcontainers Library

```python
# For interviews/competitive programming, use existing balanced BST implementations
from sortedcontainers import SortedList, SortedDict, SortedSet

def demo_sorted_containers():
    """
    SortedList maintains sorted order with O(log n) operations.
    Internally uses a modified B-tree.
    """
    sl = SortedList([3, 1, 4, 1, 5, 9, 2, 6])
    
    # Add: O(log n)
    sl.add(7)
    print(sl)  # [1, 1, 2, 3, 4, 5, 6, 7, 9]
    
    # Remove: O(log n)
    sl.remove(1)
    print(sl)  # [1, 2, 3, 4, 5, 6, 7, 9]
    
    # Index access: O(log n)
    print(sl[0])   # 1 (minimum)
    print(sl[-1])  # 9 (maximum)
    print(sl[3])   # 4 (4th smallest)
    
    # Binary search: O(log n)
    idx = sl.bisect_left(5)  # Index where 5 would be inserted
    print(idx)  # 4
    
    # Range: O(k + log n) for k elements in range
    in_range = list(sl.irange(3, 7))  # [3, 4, 5, 6, 7]
    print(in_range)


# Install: pip install sortedcontainers
```

---

## 7. Step-by-Step Worked Example

### AVL Insert: Building Tree from [30, 20, 40, 10, 25, 35, 50, 5]

```
Step 1: Insert 30
    30 (h=1, bf=0)

Step 2: Insert 20
      30 (h=2, bf=1)
     /
    20 (h=1, bf=0)

Step 3: Insert 40
      30 (h=2, bf=0)
     /  \
    20   40
Balanced ✓

Step 4: Insert 10
        30 (bf=1)
       /  \
      20   40
     /
    10
Balanced ✓

Step 5: Insert 25
        30 (bf=1)
       /  \
      20   40
     /  \
    10   25
Balanced ✓

Step 6: Insert 35
        30 (bf=0)
       /  \
      20   40
     /  \  /
    10  25 35
Balanced ✓

Step 7: Insert 50
        30 (bf=0)
       /  \
      20   40
     /  \  / \
    10  25 35 50
Balanced ✓

Step 8: Insert 5
          30 (bf=2!) ← Violation!
         /  \
        20   40
       /  \  / \
      10  25 35 50
     /
    5

Node 30 has bf=2 (left heavy)
Node 20 has bf=1 (left heavy)
This is Left-Left case → Right rotate at 30

After Right Rotation:
          20
         /  \
        10   30
       /    /  \
      5    25   40
              /  \
             35   50

All balanced ✓
```

---

## 8. Common Mistakes

1. **Forgetting to update heights after rotation**
   ```python
   # Wrong: heights not updated
   def rotate_right(y):
       x = y.left
       y.left = x.right
       x.right = y
       return x
   
   # Correct: update heights in order
   def rotate_right(y):
       x = y.left
       y.left = x.right
       x.right = y
       y.height = 1 + max(height(y.left), height(y.right))  # y first!
       x.height = 1 + max(height(x.left), height(x.right))  # then x
       return x
   ```

2. **Wrong rotation case selection**
   ```python
   # Must check which case applies based on balance factors
   # Left-Left: bf > 1 and left_bf >= 0
   # Left-Right: bf > 1 and left_bf < 0
   # Right-Right: bf < -1 and right_bf <= 0
   # Right-Left: bf < -1 and right_bf > 0
   ```

3. **Not handling single-child deletion in AVL**
   ```python
   # When deleting node with one child, must still rebalance ancestors
   ```

4. **Confusing height with depth**
   ```python
   # Height: counted from node down to leaf (leaves have height 1)
   # Depth: counted from root down to node (root has depth 0)
   # Balance factor uses HEIGHT of subtrees
   ```

5. **Red-Black: Forgetting to maintain root as black**
   ```python
   # After every insert, ensure:
   self.root.color = Color.BLACK
   ```

---

## 9. Variations & Optimizations

### Treap (Tree + Heap)

```python
import random

class TreapNode:
    """Treap node with BST key and heap priority."""
    def __init__(self, key: int):
        self.key = key
        self.priority = random.random()  # Random priority for balance
        self.left = None
        self.right = None

class Treap:
    """
    Treap: BST ordered by key, heap ordered by random priority.
    Expected O(log n) height due to random priorities.
    """
    
    def __init__(self):
        self.root = None
    
    def _rotate_right(self, node):
        left = node.left
        node.left = left.right
        left.right = node
        return left
    
    def _rotate_left(self, node):
        right = node.right
        node.right = right.left
        right.left = node
        return right
    
    def insert(self, key: int) -> None:
        self.root = self._insert(self.root, key)
    
    def _insert(self, node, key):
        if not node:
            return TreapNode(key)
        
        if key < node.key:
            node.left = self._insert(node.left, key)
            if node.left.priority > node.priority:
                node = self._rotate_right(node)
        else:
            node.right = self._insert(node.right, key)
            if node.right.priority > node.priority:
                node = self._rotate_left(node)
        
        return node
```

### Scapegoat Tree

```python
class ScapegoatTree:
    """
    Scapegoat Tree: Rebalances lazily by finding "scapegoat" node.
    
    When tree becomes too unbalanced (height > log base α of n),
    find and rebuild subtree at scapegoat node.
    
    α typically 2/3 or 3/4.
    Amortized O(log n) operations.
    """
    
    def __init__(self, alpha: float = 0.7):
        self.root = None
        self.alpha = alpha
        self.size = 0
    
    def _is_scapegoat(self, node, size):
        """Check if node is unbalanced enough to be scapegoat."""
        return (self._size(node.left) > self.alpha * size or
                self._size(node.right) > self.alpha * size)
    
    def _rebuild(self, node):
        """Rebuild subtree rooted at node into balanced tree."""
        nodes = []
        self._flatten(node, nodes)
        return self._build_balanced(nodes, 0, len(nodes) - 1)
    
    def _flatten(self, node, nodes):
        """Inorder flatten to list."""
        if node:
            self._flatten(node.left, nodes)
            nodes.append(node)
            self._flatten(node.right, nodes)
    
    def _build_balanced(self, nodes, left, right):
        """Build balanced tree from sorted list."""
        if left > right:
            return None
        mid = (left + right) // 2
        node = nodes[mid]
        node.left = self._build_balanced(nodes, left, mid - 1)
        node.right = self._build_balanced(nodes, mid + 1, right)
        return node
```

### Order Statistics Tree

```python
class OrderStatNode:
    """BST node with subtree size for order statistics."""
    def __init__(self, val: int):
        self.val = val
        self.left = None
        self.right = None
        self.size = 1  # Size of subtree rooted at this node

class OrderStatisticsTree:
    """
    BST augmented with subtree sizes.
    Supports O(log n) kth smallest and rank queries.
    """
    
    def __init__(self):
        self.root = None
    
    def _size(self, node):
        return node.size if node else 0
    
    def _update_size(self, node):
        if node:
            node.size = 1 + self._size(node.left) + self._size(node.right)
    
    def kth_smallest(self, k: int):
        """Find kth smallest element (1-indexed)."""
        return self._kth_smallest(self.root, k)
    
    def _kth_smallest(self, node, k):
        if not node:
            return None
        
        left_size = self._size(node.left)
        
        if k <= left_size:
            return self._kth_smallest(node.left, k)
        elif k == left_size + 1:
            return node.val
        else:
            return self._kth_smallest(node.right, k - left_size - 1)
    
    def rank(self, val: int) -> int:
        """Find rank (number of elements less than val)."""
        return self._rank(self.root, val)
    
    def _rank(self, node, val):
        if not node:
            return 0
        
        if val <= node.val:
            return self._rank(node.left, val)
        else:
            return 1 + self._size(node.left) + self._rank(node.right, val)
```

---

## 10. Interview Tips

### What Interviewers Look For

1. **Conceptual understanding**: Why do we need balanced trees?
2. **Rotation mechanics**: Can you draw/explain rotations?
3. **Trade-offs**: AVL vs Red-Black, when to use each
4. **Application**: Know that TreeMap/TreeSet use Red-Black

### Common Interview Questions

```
Q: Why use balanced BST instead of hash table?
A: Ordered operations (min, max, range queries, kth element) are 
   O(log n) in BST but O(n) in hash table. BST also has consistent
   performance without hash collisions.

Q: When would you choose AVL over Red-Black?
A: AVL for read-heavy workloads (more strictly balanced = faster lookups).
   Red-Black for write-heavy workloads (fewer rotations on insert/delete).

Q: What's the maximum height of Red-Black tree with n nodes?
A: 2 * log₂(n+1). Property 5 (equal black-height) ensures this.

Q: Can you implement a balanced BST?
A: Understand rotations conceptually. Full implementation rarely required,
   but be ready to write rotation code.
```

### Quick Pattern Recognition

```
See "maintain sorted order with O(log n) ops"? → Balanced BST
See "frequently accessed items"? → Consider Splay Tree
See "range queries needed"? → BST (balanced if performance matters)
See "database indexing"? → B-tree family
```

---

## 11. Related Patterns

| Pattern | Description | Example |
|---------|-------------|---------|
| Self-Balancing | Automatic height maintenance | AVL, Red-Black |
| Augmented BST | Extra data in nodes | Order statistics, interval trees |
| Lazy Rebuilding | Defer rebalancing | Scapegoat tree |
| Randomization | Use random priorities | Treap |

---

## 12. References

- **CLRS**: Chapter 13 (Red-Black Trees)
- **Sedgewick**: Left-Leaning Red-Black Trees (2008)
- **AVL Paper**: Adelson-Velsky & Landis (1962)
- **Visualization**: [VisuAlgo AVL/RBT](https://visualgo.net/en/bst)

---

## 13. Practice Problems

### Easy
| # | Problem | Key Concepts | LeetCode |
|---|---------|--------------|----------|
| 1 | Balanced Binary Tree | Check balance | [110](https://leetcode.com/problems/balanced-binary-tree/) |
| 2 | Convert Sorted Array to BST | Build balanced | [108](https://leetcode.com/problems/convert-sorted-array-to-binary-search-tree/) |
| 3 | Convert Sorted List to BST | Build from linked list | [109](https://leetcode.com/problems/convert-sorted-list-to-binary-search-tree/) |
| 4 | Minimum Absolute Difference in BST | Inorder property | [530](https://leetcode.com/problems/minimum-absolute-difference-in-bst/) |

### Medium
| # | Problem | Key Concepts | LeetCode |
|---|---------|--------------|----------|
| 5 | Balance a Binary Search Tree | Rebuild balanced | [1382](https://leetcode.com/problems/balance-a-binary-search-tree/) |
| 6 | Count of Smaller Numbers After Self | Augmented BST | [315](https://leetcode.com/problems/count-of-smaller-numbers-after-self/) |
| 7 | Kth Smallest Element in BST | Order statistics | [230](https://leetcode.com/problems/kth-smallest-element-in-a-bst/) |
| 8 | Count Complete Tree Nodes | Binary search on complete tree | [222](https://leetcode.com/problems/count-complete-tree-nodes/) |
| 9 | Lowest Common Ancestor | BST property | [235](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-search-tree/) |
| 10 | Validate BST | Range checking | [98](https://leetcode.com/problems/validate-binary-search-tree/) |

### Hard
| # | Problem | Key Concepts | LeetCode |
|---|---------|--------------|----------|
| 11 | Count of Range Sum | Merge sort / BST | [327](https://leetcode.com/problems/count-of-range-sum/) |
| 12 | Reverse Pairs | Modified merge sort | [493](https://leetcode.com/problems/reverse-pairs/) |
| 13 | Create Sorted Array Through Instructions | BST with counts | [1649](https://leetcode.com/problems/create-sorted-array-through-instructions/) |
| 14 | Count Good Triplets in Array | Order statistics | [2179](https://leetcode.com/problems/count-good-triplets-in-an-array/) |

---

## 14. Key Takeaways

1. **Balanced = O(log n) guaranteed**: Prevents worst-case O(n) of regular BST

2. **AVL is stricter, Red-Black is practical**:
   - AVL: |balance factor| ≤ 1, height ≤ 1.44 log n
   - Red-Black: height ≤ 2 log n, fewer rotations

3. **Rotations are the key operation**: Left, right, left-right, right-left

4. **In practice, use libraries**:
   - Python: `sortedcontainers.SortedList`
   - Java: `TreeMap`, `TreeSet`
   - C++: `std::map`, `std::set`

5. **Know when to use over hash table**:
   - Need ordered iteration
   - Need range queries
   - Need kth smallest
   - Memory predictability

---

## 15. Spaced Repetition Prompts

1. **Q**: What is the AVL balance condition?
   **A**: For every node, the heights of left and right subtrees differ by at most 1. Balance factor ∈ {-1, 0, +1}.

2. **Q**: What are the 5 Red-Black tree properties?
   **A**: 1) Every node is red or black. 2) Root is black. 3) Leaves (NIL) are black. 4) Red nodes have black children. 5) All paths have same black-height.

3. **Q**: When do you need a double rotation in AVL?
   **A**: Left-Right case (bf > 1, left child bf < 0): rotate left at child, then right at node. Right-Left case (bf < -1, right child bf > 0): rotate right at child, then left at node.

4. **Q**: What's the maximum height of AVL tree with n nodes?
   **A**: Approximately 1.44 * log₂(n). More precisely: floor(1.44 * log₂(n + 2) - 0.328).

5. **Q**: AVL vs Red-Black: which is better for what?
   **A**: AVL: Read-heavy (stricter balance = faster lookup). Red-Black: Write-heavy (fewer rotations, 2-3 per operation vs O(log n) for AVL delete).
