---
title: Binary Search Trees
topic: Binary Search Trees
difficulty: Medium
tags: [bst, binary-tree, search, ordered-data, inorder]
status: complete
weight: 10
---

# Binary Search Trees (BST)

## 1. Summary / TL;DR

- **BST Property**: For every node, all left descendants < node < all right descendants
- **Inorder traversal** of BST yields sorted sequence
- **Average case**: O(log n) for search, insert, delete; **Worst case**: O(n) for skewed trees
- Common operations: search, insert, delete, find min/max, successor/predecessor
- **Balanced BSTs** (AVL, Red-Black) guarantee O(log n) worst case
- Key patterns: validate BST, kth smallest, LCA, range queries

---

## 2. When & Where to Use

| Scenario | Why BST? |
|----------|----------|
| Ordered data with frequent search/insert/delete | O(log n) average for all ops |
| Finding min/max elements | O(h) traversal to leftmost/rightmost |
| Range queries | Efficient in-range element retrieval |
| Successor/predecessor queries | O(h) navigation |
| Dynamic sorted data | Insert maintains sorted order |
| Implementing TreeSet/TreeMap | Underlying data structure |

### BST vs Hash Table

| Aspect | BST | Hash Table |
|--------|-----|------------|
| Search | O(log n) avg | O(1) avg |
| Insert/Delete | O(log n) avg | O(1) avg |
| Ordered iteration | O(n) | Not available |
| Range queries | Efficient | Must check all |
| Space | O(n) | O(n) + overhead |
| Min/Max | O(log n) | O(n) |

---

## 3. Time & Space Complexity

### BST Operations

| Operation | Average | Worst (Skewed) | Balanced BST |
|-----------|---------|----------------|--------------|
| Search | O(log n) | O(n) | O(log n) |
| Insert | O(log n) | O(n) | O(log n) |
| Delete | O(log n) | O(n) | O(log n) |
| Find Min/Max | O(log n) | O(n) | O(log n) |
| Successor/Predecessor | O(log n) | O(n) | O(log n) |
| Inorder Traversal | O(n) | O(n) | O(n) |

**Space**: O(n) for storing n nodes, O(h) for recursion stack

---

## 4. Core Concepts & Theory

### BST Property

```
           8           For every node N:
         /   \         - All nodes in left subtree < N
        3     10       - All nodes in right subtree > N
       / \      \
      1   6      14
         / \    /
        4   7  13

Inorder: 1, 3, 4, 6, 7, 8, 10, 13, 14 (sorted!)
```

### BST Properties

1. **No duplicates** (typically) - duplicates can go left, right, or use count
2. **Inorder = Sorted** - key insight for many problems
3. **Search = Binary Search on Tree** - eliminate half at each step
4. **Height matters** - determines efficiency; ranges from O(log n) to O(n)

### Successor and Predecessor

**Inorder Successor** (next larger element):
- If node has right child: leftmost node in right subtree
- Else: lowest ancestor for which node is in left subtree

**Inorder Predecessor** (previous smaller element):
- If node has left child: rightmost node in left subtree
- Else: lowest ancestor for which node is in right subtree

```
        20
       /  \
      10   30
     /  \
    5   15

Successor of 15: 20 (go up to first ancestor where 15 is in left subtree)
Successor of 10: 15 (rightmost in left subtree of 10's right child)
Predecessor of 20: 15 (rightmost in left subtree)
```

---

## 5. Diagrams / Visualizations

### BST Search Process

```
Search for 7 in BST:

           8            Step 1: 7 < 8, go left
         /   \
       [3]    10        Step 2: 7 > 3, go right
       / \      \
      1  [6]    14      Step 3: 7 > 6, go right
         / \    /
        4  [7] 13       Step 4: Found 7!

Path: 8 → 3 → 6 → 7
Comparisons: 4 (height of tree + 1)
```

### BST Deletion Cases

```
Case 1: Leaf node (no children)
Delete 4:
    6           6
   / \    →    / \
  4   7           7

Case 2: One child
Delete 3:
    8           8
   / \         / \
  3   10  →   6   10
   \
    6

Case 3: Two children (replace with inorder successor)
Delete 8:
    8                10  (successor)
   / \              /  \
  3   10    →      3    14
       \              /
        14          13
       /
      13
```

---

## 6. Implementation (Python)

### BST Node Definition

```python
from typing import Optional, List, Iterator

class BSTNode:
    """Binary Search Tree node."""
    def __init__(self, val: int):
        self.val = val
        self.left: Optional['BSTNode'] = None
        self.right: Optional['BSTNode'] = None
    
    def __repr__(self) -> str:
        return f"BSTNode({self.val})"
```

### Complete BST Class

```python
class BinarySearchTree:
    """
    Binary Search Tree implementation with standard operations.
    Assumes no duplicate values.
    """
    
    def __init__(self):
        self.root: Optional[BSTNode] = None
        self.size: int = 0
    
    def search(self, val: int) -> Optional[BSTNode]:
        """
        Search for value in BST.
        Time: O(h) where h is height
        
        >>> bst = BinarySearchTree()
        >>> bst.insert(5); bst.insert(3); bst.insert(7)
        >>> bst.search(3).val
        3
        >>> bst.search(10) is None
        True
        """
        return self._search_recursive(self.root, val)
    
    def _search_recursive(self, node: Optional[BSTNode], val: int) -> Optional[BSTNode]:
        if not node or node.val == val:
            return node
        
        if val < node.val:
            return self._search_recursive(node.left, val)
        else:
            return self._search_recursive(node.right, val)
    
    def search_iterative(self, val: int) -> Optional[BSTNode]:
        """Iterative search - more space efficient."""
        current = self.root
        
        while current:
            if val == current.val:
                return current
            elif val < current.val:
                current = current.left
            else:
                current = current.right
        
        return None
    
    def insert(self, val: int) -> None:
        """
        Insert value into BST.
        Time: O(h)
        
        >>> bst = BinarySearchTree()
        >>> bst.insert(5); bst.insert(3); bst.insert(7)
        >>> bst.inorder()
        [3, 5, 7]
        """
        self.root = self._insert_recursive(self.root, val)
        self.size += 1
    
    def _insert_recursive(self, node: Optional[BSTNode], val: int) -> BSTNode:
        if not node:
            return BSTNode(val)
        
        if val < node.val:
            node.left = self._insert_recursive(node.left, val)
        else:  # val > node.val (assuming no duplicates)
            node.right = self._insert_recursive(node.right, val)
        
        return node
    
    def insert_iterative(self, val: int) -> None:
        """Iterative insert."""
        new_node = BSTNode(val)
        
        if not self.root:
            self.root = new_node
            self.size += 1
            return
        
        current = self.root
        while True:
            if val < current.val:
                if not current.left:
                    current.left = new_node
                    break
                current = current.left
            else:
                if not current.right:
                    current.right = new_node
                    break
                current = current.right
        
        self.size += 1
    
    def delete(self, val: int) -> None:
        """
        Delete value from BST.
        Time: O(h)
        
        >>> bst = BinarySearchTree()
        >>> for v in [5, 3, 7, 2, 4]: bst.insert(v)
        >>> bst.delete(3)
        >>> bst.inorder()
        [2, 4, 5, 7]
        """
        self.root = self._delete_recursive(self.root, val)
        self.size -= 1
    
    def _delete_recursive(self, node: Optional[BSTNode], val: int) -> Optional[BSTNode]:
        if not node:
            return None
        
        if val < node.val:
            node.left = self._delete_recursive(node.left, val)
        elif val > node.val:
            node.right = self._delete_recursive(node.right, val)
        else:
            # Found node to delete
            
            # Case 1: Leaf node
            if not node.left and not node.right:
                return None
            
            # Case 2: One child
            if not node.left:
                return node.right
            if not node.right:
                return node.left
            
            # Case 3: Two children
            # Replace with inorder successor (minimum in right subtree)
            successor = self._find_min_node(node.right)
            node.val = successor.val
            node.right = self._delete_recursive(node.right, successor.val)
        
        return node
    
    def find_min(self) -> Optional[int]:
        """Find minimum value in BST."""
        if not self.root:
            return None
        return self._find_min_node(self.root).val
    
    def _find_min_node(self, node: BSTNode) -> BSTNode:
        """Find node with minimum value."""
        current = node
        while current.left:
            current = current.left
        return current
    
    def find_max(self) -> Optional[int]:
        """Find maximum value in BST."""
        if not self.root:
            return None
        
        current = self.root
        while current.right:
            current = current.right
        return current.val
    
    def inorder(self) -> List[int]:
        """Return inorder traversal (sorted order)."""
        result = []
        self._inorder_recursive(self.root, result)
        return result
    
    def _inorder_recursive(self, node: Optional[BSTNode], result: List[int]) -> None:
        if node:
            self._inorder_recursive(node.left, result)
            result.append(node.val)
            self._inorder_recursive(node.right, result)
    
    def inorder_successor(self, val: int) -> Optional[int]:
        """
        Find inorder successor (next larger element).
        
        >>> bst = BinarySearchTree()
        >>> for v in [20, 10, 30, 5, 15, 25, 35]: bst.insert(v)
        >>> bst.inorder_successor(15)
        20
        >>> bst.inorder_successor(10)
        15
        """
        successor = None
        current = self.root
        
        while current:
            if val < current.val:
                successor = current.val  # This could be successor
                current = current.left
            else:
                current = current.right
        
        return successor
    
    def inorder_predecessor(self, val: int) -> Optional[int]:
        """Find inorder predecessor (previous smaller element)."""
        predecessor = None
        current = self.root
        
        while current:
            if val > current.val:
                predecessor = current.val  # This could be predecessor
                current = current.right
            else:
                current = current.left
        
        return predecessor
    
    def __len__(self) -> int:
        return self.size
    
    def __contains__(self, val: int) -> bool:
        return self.search(val) is not None
```

### Validate BST (LeetCode 98)

```python
def is_valid_bst(root: Optional[BSTNode]) -> bool:
    """
    Validate if tree is a valid BST.
    
    Approach: Each node must be within a valid range.
    Left subtree has upper bound, right subtree has lower bound.
    
    Time: O(n), Space: O(h)
    
    >>> root = BSTNode(5)
    >>> root.left, root.right = BSTNode(3), BSTNode(7)
    >>> is_valid_bst(root)
    True
    """
    def validate(node: Optional[BSTNode], 
                 min_val: float, 
                 max_val: float) -> bool:
        if not node:
            return True
        
        if not (min_val < node.val < max_val):
            return False
        
        return (validate(node.left, min_val, node.val) and
                validate(node.right, node.val, max_val))
    
    return validate(root, float('-inf'), float('inf'))


def is_valid_bst_inorder(root: Optional[BSTNode]) -> bool:
    """
    Validate BST using inorder traversal.
    Key insight: Inorder of valid BST is strictly increasing.
    
    >>> root = BSTNode(5)
    >>> root.left, root.right = BSTNode(3), BSTNode(7)
    >>> is_valid_bst_inorder(root)
    True
    """
    prev = float('-inf')
    
    def inorder(node: Optional[BSTNode]) -> bool:
        nonlocal prev
        
        if not node:
            return True
        
        # Check left subtree
        if not inorder(node.left):
            return False
        
        # Check current (must be greater than previous)
        if node.val <= prev:
            return False
        prev = node.val
        
        # Check right subtree
        return inorder(node.right)
    
    return inorder(root)
```

### Kth Smallest Element (LeetCode 230)

```python
def kth_smallest(root: Optional[BSTNode], k: int) -> int:
    """
    Find kth smallest element in BST.
    
    Approach: Inorder traversal visits elements in sorted order.
    Stop when we've visited k elements.
    
    Time: O(H + k) where H is height
    Space: O(H)
    
    >>> root = BSTNode(5)
    >>> root.left, root.right = BSTNode(3), BSTNode(7)
    >>> root.left.left, root.left.right = BSTNode(2), BSTNode(4)
    >>> kth_smallest(root, 3)  # sorted: 2,3,4,5,7
    4
    """
    count = 0
    result = None
    
    def inorder(node: Optional[BSTNode]) -> None:
        nonlocal count, result
        
        if not node or result is not None:
            return
        
        inorder(node.left)
        
        count += 1
        if count == k:
            result = node.val
            return
        
        inorder(node.right)
    
    inorder(root)
    return result


def kth_smallest_iterative(root: Optional[BSTNode], k: int) -> int:
    """Iterative approach using stack."""
    stack = []
    current = root
    count = 0
    
    while current or stack:
        # Go left as far as possible
        while current:
            stack.append(current)
            current = current.left
        
        current = stack.pop()
        count += 1
        
        if count == k:
            return current.val
        
        current = current.right
    
    return -1  # k > number of nodes
```

### Lowest Common Ancestor in BST (LeetCode 235)

```python
def lca_bst(root: Optional[BSTNode], p: int, q: int) -> Optional[BSTNode]:
    """
    Find LCA of two nodes in BST.
    
    Key insight: LCA is the first node where p and q diverge
    (one goes left, one goes right), or one of p/q itself.
    
    Time: O(h), Space: O(1) iterative
    
    >>> root = BSTNode(6)
    >>> root.left, root.right = BSTNode(2), BSTNode(8)
    >>> lca_bst(root, 2, 8).val
    6
    """
    current = root
    
    while current:
        if p < current.val and q < current.val:
            # Both in left subtree
            current = current.left
        elif p > current.val and q > current.val:
            # Both in right subtree
            current = current.right
        else:
            # Diverge here (or one of them is current)
            return current
    
    return None


def lca_bst_recursive(root: Optional[BSTNode], p: int, q: int) -> Optional[BSTNode]:
    """Recursive approach."""
    if not root:
        return None
    
    if p < root.val and q < root.val:
        return lca_bst_recursive(root.left, p, q)
    elif p > root.val and q > root.val:
        return lca_bst_recursive(root.right, p, q)
    else:
        return root
```

### Convert Sorted Array to BST (LeetCode 108)

```python
def sorted_array_to_bst(nums: List[int]) -> Optional[BSTNode]:
    """
    Convert sorted array to height-balanced BST.
    
    Approach: Middle element becomes root, recurse on left and right halves.
    
    Time: O(n), Space: O(log n) recursion
    
    >>> nums = [1, 2, 3, 4, 5, 6, 7]
    >>> root = sorted_array_to_bst(nums)
    >>> root.val  # Middle element
    4
    """
    def build(left: int, right: int) -> Optional[BSTNode]:
        if left > right:
            return None
        
        mid = (left + right) // 2
        node = BSTNode(nums[mid])
        
        node.left = build(left, mid - 1)
        node.right = build(mid + 1, right)
        
        return node
    
    return build(0, len(nums) - 1)
```

### Range Sum BST (LeetCode 938)

```python
def range_sum_bst(root: Optional[BSTNode], low: int, high: int) -> int:
    """
    Sum of all nodes with values in range [low, high].
    
    Optimization: Use BST property to prune branches.
    
    Time: O(n) worst case, but typically better due to pruning
    
    >>> root = BSTNode(10)
    >>> root.left, root.right = BSTNode(5), BSTNode(15)
    >>> root.left.left, root.left.right = BSTNode(3), BSTNode(7)
    >>> range_sum_bst(root, 7, 15)  # 7 + 10 + 15 = 32
    32
    """
    if not root:
        return 0
    
    total = 0
    
    # Include current node if in range
    if low <= root.val <= high:
        total += root.val
    
    # Only go left if there might be values in range
    if root.val > low:
        total += range_sum_bst(root.left, low, high)
    
    # Only go right if there might be values in range
    if root.val < high:
        total += range_sum_bst(root.right, low, high)
    
    return total
```

### BST Iterator (LeetCode 173)

```python
class BSTIterator:
    """
    Implement an iterator over a BST.
    next() returns next smallest, hasNext() checks if more elements exist.
    
    Approach: Controlled inorder traversal using stack.
    
    Time: O(1) amortized for next(), O(h) for hasNext()
    Space: O(h) for stack
    """
    
    def __init__(self, root: Optional[BSTNode]):
        self.stack = []
        self._push_left(root)
    
    def _push_left(self, node: Optional[BSTNode]) -> None:
        """Push all left children onto stack."""
        while node:
            self.stack.append(node)
            node = node.left
    
    def next(self) -> int:
        """Return next smallest element."""
        node = self.stack.pop()
        self._push_left(node.right)
        return node.val
    
    def hasNext(self) -> bool:
        """Return True if more elements exist."""
        return len(self.stack) > 0


# Usage example
# root = sorted_array_to_bst([1, 2, 3, 4, 5])
# iterator = BSTIterator(root)
# while iterator.hasNext():
#     print(iterator.next())  # Prints 1, 2, 3, 4, 5
```

### Delete Node in BST (LeetCode 450)

```python
def delete_node(root: Optional[BSTNode], key: int) -> Optional[BSTNode]:
    """
    Delete a node with given key from BST.
    
    Cases:
    1. Leaf: Just remove
    2. One child: Replace with child
    3. Two children: Replace with inorder successor
    
    Time: O(h)
    
    >>> root = sorted_array_to_bst([1, 2, 3, 4, 5])
    >>> root = delete_node(root, 3)
    >>> # Tree structure changes
    """
    if not root:
        return None
    
    if key < root.val:
        root.left = delete_node(root.left, key)
    elif key > root.val:
        root.right = delete_node(root.right, key)
    else:
        # Found node to delete
        
        # Case 1 & 2: No children or one child
        if not root.left:
            return root.right
        if not root.right:
            return root.left
        
        # Case 3: Two children
        # Find inorder successor (min in right subtree)
        successor = root.right
        while successor.left:
            successor = successor.left
        
        # Copy successor value to current node
        root.val = successor.val
        
        # Delete successor from right subtree
        root.right = delete_node(root.right, successor.val)
    
    return root
```

---

## 7. Step-by-Step Worked Example

### Problem: Recover Binary Search Tree (LeetCode 99)

**Problem**: Two nodes in a BST are swapped by mistake. Recover the BST without changing its structure.

**Example**:
```
Swapped BST:        Correct BST:
      1                  3
     /                  /
    3        →         1
     \                  \
      2                  2

Nodes 1 and 3 are swapped
```

**Approach**: In inorder traversal of valid BST, values are strictly increasing. In swapped BST, we'll find violations where current < previous.

```python
def recover_tree(root: Optional[BSTNode]) -> None:
    """
    Recover BST where two nodes are swapped.
    
    Key insight: Inorder should be increasing.
    - Adjacent swap: One violation (prev > curr)
    - Non-adjacent swap: Two violations
    
    Time: O(n), Space: O(h)
    """
    first = second = prev = None
    
    def inorder(node: Optional[BSTNode]) -> None:
        nonlocal first, second, prev
        
        if not node:
            return
        
        inorder(node.left)
        
        # Check for violation
        if prev and prev.val > node.val:
            if not first:
                # First violation: prev is the first wrong node
                first = prev
            # Always update second (handles both adjacent and non-adjacent)
            second = node
        
        prev = node
        inorder(node.right)
    
    inorder(root)
    
    # Swap values of the two wrong nodes
    if first and second:
        first.val, second.val = second.val, first.val
```

**Trace through example [3, 1, 2]**:
```
Inorder traversal encounters: 3 → 1 → 2

Step 1: Visit 3
  prev = None, no comparison
  prev = node(3)

Step 2: Visit 1  
  prev.val(3) > node.val(1) → Violation!
  first = prev = node(3)
  second = node(1)
  prev = node(1)

Step 3: Visit 2
  prev.val(1) < node.val(2) → No violation
  prev = node(2)

Result: first = node(3), second = node(1)
Swap: first.val ↔ second.val → Tree becomes [1, 3, 2] 

Wait, that's wrong! Let me retrace...
Actually the tree is:
      1
     /
    3
     \
      2

Inorder: 3, 2, 1 (following left-root-right from root 1)
Actually let me reconsider the tree structure...

If tree is:    1        
              /
             3
              \
               2

Inorder visits: 3, 2, 1

prev=None, visit 3: no check, prev=3
prev=3, visit 2: 3>2 violation! first=3, second=2, prev=2
prev=2, visit 1: 2>1 violation! second=1
final: first=3, second=1

Swap values: 3↔1

Result tree:   3
              /
             1
              \
               2

Inorder: 1, 2, 3 ✓ Valid BST!
```

---

## 8. Common Mistakes

1. **Not handling duplicates**
   ```python
   # If duplicates allowed, specify where they go
   # Convention 1: Duplicates go right
   if val < node.val:
       node.left = insert(node.left, val)
   else:  # val >= node.val
       node.right = insert(node.right, val)
   
   # Convention 2: Don't allow duplicates
   # Convention 3: Use count in node
   ```

2. **Incorrect BST validation**
   ```python
   # Wrong: Only checking immediate children
   def is_valid_wrong(node):
       if not node:
           return True
       if node.left and node.left.val >= node.val:
           return False
       if node.right and node.right.val <= node.val:
           return False
       return is_valid_wrong(node.left) and is_valid_wrong(node.right)
   
   # This fails for:
   #     5
   #    / \
   #   1   6
   #      / \
   #     3   7   <- 3 < 5 but is in right subtree!
   
   # Correct: Pass bounds
   def is_valid_correct(node, min_val, max_val):
       if not node:
           return True
       if not (min_val < node.val < max_val):
           return False
       return (is_valid_correct(node.left, min_val, node.val) and
               is_valid_correct(node.right, node.val, max_val))
   ```

3. **Deletion edge cases**
   ```python
   # Remember to handle:
   # - Deleting root node
   # - Deleting node with two children
   # - Node not found (no-op)
   ```

4. **Confusing BST with Binary Tree**
   ```python
   # BST LCA uses values: O(h)
   # Binary Tree LCA needs full traversal: O(n)
   
   # BST: Can compare values and go left/right
   # Binary Tree: Must check both subtrees
   ```

5. **Iterator invalidation**
   ```python
   # Modifying BST while iterating causes issues
   # Either: Iterate first, then modify
   # Or: Create copy of elements to iterate
   ```

---

## 9. Variations & Optimizations

### BST with Parent Pointers

```python
class BSTNodeWithParent:
    """BST node with parent pointer for O(1) parent access."""
    def __init__(self, val: int):
        self.val = val
        self.left = None
        self.right = None
        self.parent = None  # Useful for successor without recursion
    
def find_successor_with_parent(node: 'BSTNodeWithParent') -> Optional['BSTNodeWithParent']:
    """Find successor using parent pointers."""
    if node.right:
        # Go to rightmost of right subtree
        current = node.right
        while current.left:
            current = current.left
        return current
    
    # Go up until we come from a left child
    parent = node.parent
    while parent and node == parent.right:
        node = parent
        parent = parent.parent
    return parent
```

### Threaded BST

```python
class ThreadedBSTNode:
    """
    Threaded BST: null pointers replaced with inorder successor/predecessor.
    Enables O(1) successor finding without stack/recursion.
    """
    def __init__(self, val: int):
        self.val = val
        self.left = None
        self.right = None
        self.is_threaded = False  # True if right points to successor
```

### Floor and Ceiling

```python
def floor_bst(root: Optional[BSTNode], key: int) -> Optional[int]:
    """
    Find largest value <= key.
    
    >>> root = sorted_array_to_bst([1, 3, 5, 7, 9])
    >>> floor_bst(root, 6)  # Largest <= 6 is 5
    5
    """
    floor = None
    current = root
    
    while current:
        if current.val == key:
            return current.val
        elif current.val < key:
            floor = current.val  # Potential floor
            current = current.right
        else:
            current = current.left
    
    return floor


def ceiling_bst(root: Optional[BSTNode], key: int) -> Optional[int]:
    """
    Find smallest value >= key.
    
    >>> root = sorted_array_to_bst([1, 3, 5, 7, 9])
    >>> ceiling_bst(root, 6)  # Smallest >= 6 is 7
    7
    """
    ceiling = None
    current = root
    
    while current:
        if current.val == key:
            return current.val
        elif current.val > key:
            ceiling = current.val  # Potential ceiling
            current = current.left
        else:
            current = current.right
    
    return ceiling
```

### Two Sum in BST

```python
def find_target_bst(root: Optional[BSTNode], k: int) -> bool:
    """
    Find if two nodes sum to k (LeetCode 653).
    
    Approach: Use two iterators - one forward (smallest up), one backward (largest down).
    
    Time: O(n), Space: O(h)
    """
    # Forward iterator (ascending)
    stack_asc = []
    node = root
    while node:
        stack_asc.append(node)
        node = node.left
    
    # Backward iterator (descending)
    stack_desc = []
    node = root
    while node:
        stack_desc.append(node)
        node = node.right
    
    # Two pointers
    while stack_asc and stack_desc:
        low = stack_asc[-1]
        high = stack_desc[-1]
        
        if low == high:  # Same node
            break
        
        total = low.val + high.val
        
        if total == k:
            return True
        elif total < k:
            # Need larger low, advance forward iterator
            node = stack_asc.pop()
            node = node.right
            while node:
                stack_asc.append(node)
                node = node.left
        else:
            # Need smaller high, advance backward iterator
            node = stack_desc.pop()
            node = node.left
            while node:
                stack_desc.append(node)
                node = node.right
    
    return False
```

---

## 10. Interview Tips

### What Interviewers Look For

1. **Understanding BST property**: Can you explain why inorder gives sorted order?
2. **Handling edge cases**: Empty tree, single node, skewed trees
3. **Choosing approach**: Recursive vs iterative based on constraints
4. **Optimization**: Can you use BST property to prune search space?

### Common Follow-up Questions

```
Q: What if tree is very deep (10^6 nodes in line)?
A: Use iterative approach to avoid stack overflow.

Q: How would you handle duplicates?
A: Options: 1) Count in node, 2) Duplicates go left/right, 3) No duplicates allowed

Q: How to make operations O(log n) guaranteed?
A: Use balanced BST (AVL, Red-Black) - covered in next file.
```

### Quick Pattern Recognition

```
See "sorted" + tree? → BST
See "kth smallest/largest"? → Inorder traversal
See "validate BST"? → Range validation or inorder increasing check
See "LCA in BST"? → Use BST property (compare values)
See "construct BST"? → Sorted array → balanced, preorder → reconstruct
```

---

## 11. Related Patterns

| Pattern | Description | Example Problems |
|---------|-------------|------------------|
| Inorder = Sorted | Use for kth element, range queries | Kth Smallest, Range Sum |
| Range Validation | Pass min/max bounds down | Validate BST |
| BST Navigation | Use value comparison to go left/right | LCA, Search, Successor |
| Two Iterators | Traverse from both ends | Two Sum BST, Merge Two BSTs |

---

## 12. References

- **CLRS**: Chapter 12 (Binary Search Trees)
- **LeetCode**: BST tag (50+ problems)
- **Grokking**: Tree DFS, BST patterns
- **Visualization**: [VisuAlgo BST](https://visualgo.net/en/bst)

---

## 13. Practice Problems

### Easy
| # | Problem | Key Concepts | LeetCode |
|---|---------|--------------|----------|
| 1 | Search in BST | Basic BST search | [700](https://leetcode.com/problems/search-in-a-binary-search-tree/) |
| 2 | Range Sum of BST | Pruning with BST property | [938](https://leetcode.com/problems/range-sum-of-bst/) |
| 3 | Minimum Absolute Difference in BST | Inorder gives sorted | [530](https://leetcode.com/problems/minimum-absolute-difference-in-bst/) |
| 4 | Two Sum IV - BST | Two pointers on BST | [653](https://leetcode.com/problems/two-sum-iv-input-is-a-bst/) |
| 5 | Convert Sorted Array to BST | Divide and conquer | [108](https://leetcode.com/problems/convert-sorted-array-to-binary-search-tree/) |
| 6 | Lowest Common Ancestor of BST | Use BST property | [235](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-search-tree/) |

### Medium
| # | Problem | Key Concepts | LeetCode |
|---|---------|--------------|----------|
| 7 | Validate BST | Range validation | [98](https://leetcode.com/problems/validate-binary-search-tree/) |
| 8 | Kth Smallest Element in BST | Inorder traversal | [230](https://leetcode.com/problems/kth-smallest-element-in-a-bst/) |
| 9 | Delete Node in BST | Three cases | [450](https://leetcode.com/problems/delete-node-in-a-bst/) |
| 10 | Insert into BST | Basic BST insert | [701](https://leetcode.com/problems/insert-into-a-binary-search-tree/) |
| 11 | BST Iterator | Controlled inorder | [173](https://leetcode.com/problems/binary-search-tree-iterator/) |
| 12 | Construct BST from Preorder | Stack-based construction | [1008](https://leetcode.com/problems/construct-binary-search-tree-from-preorder-traversal/) |
| 13 | Convert BST to Greater Tree | Reverse inorder | [538](https://leetcode.com/problems/convert-bst-to-greater-tree/) |
| 14 | Inorder Successor in BST | Successor finding | [285](https://leetcode.com/problems/inorder-successor-in-bst/) |
| 15 | Trim a BST | Range-based pruning | [669](https://leetcode.com/problems/trim-a-binary-search-tree/) |
| 16 | Balance a BST | Inorder + rebuild | [1382](https://leetcode.com/problems/balance-a-binary-search-tree/) |
| 17 | All Elements in Two BSTs | Merge two sorted | [1305](https://leetcode.com/problems/all-elements-in-two-binary-search-trees/) |
| 18 | Unique BSTs | Catalan numbers | [96](https://leetcode.com/problems/unique-binary-search-trees/) |

### Hard
| # | Problem | Key Concepts | LeetCode |
|---|---------|--------------|----------|
| 19 | Recover BST | Find two swapped nodes | [99](https://leetcode.com/problems/recover-binary-search-tree/) |
| 20 | Count of Smaller Numbers After Self | BST with count | [315](https://leetcode.com/problems/count-of-smaller-numbers-after-self/) |
| 21 | Serialize and Deserialize BST | Preorder encoding | [449](https://leetcode.com/problems/serialize-and-deserialize-bst/) |
| 22 | Maximum Sum BST in Binary Tree | Validate + sum | [1373](https://leetcode.com/problems/maximum-sum-bst-in-binary-tree/) |

---

## 14. Key Takeaways

1. **BST = Sorted Data Structure**: Inorder traversal is always sorted

2. **Use BST property to prune**: Don't explore subtrees that can't contain answer

3. **Average O(log n), Worst O(n)**: Skewed trees degrade to linked list

4. **Three deletion cases**: Leaf, one child, two children (replace with successor)

5. **Successor/Predecessor**: Key operations, can be done in O(h)

6. **When to use BST over hash table**:
   - Need ordered iteration
   - Need range queries
   - Need min/max efficiently
   - Memory is constrained (no load factor overhead)

---

## 15. Spaced Repetition Prompts

1. **Q**: What is the BST property?
   **A**: For every node, all values in left subtree are less than node value, all values in right subtree are greater than node value.

2. **Q**: How do you find kth smallest in BST?
   **A**: Inorder traversal visits nodes in sorted order. Stop after visiting k nodes. Time: O(h + k).

3. **Q**: How do you validate a BST?
   **A**: Pass min/max bounds recursively. Left child gets (min, node.val), right child gets (node.val, max). Check node.val is within bounds.

4. **Q**: How do you find LCA in BST?
   **A**: Start from root. If both values < current, go left. If both > current, go right. Otherwise, current is LCA.

5. **Q**: What are the three cases for BST deletion?
   **A**: 1) Leaf: just remove. 2) One child: replace with child. 3) Two children: replace with inorder successor (min in right subtree), then delete successor.
