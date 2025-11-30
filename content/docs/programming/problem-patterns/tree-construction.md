---
title: "Tree Construction & Serialization Patterns"
topic: "Problem Patterns"
difficulty: "Medium"
tags: ["tree", "construction", "serialization", "traversal"]
status: "complete"
weight: 23
---

# Tree Construction & Serialization Patterns

## 📚 Overview

Tree construction from traversals and serialization/deserialization are common interview patterns.

---

## 1️⃣ Build Tree from Preorder & Inorder (LC 105)

```python
def build_tree(preorder: list[int], inorder: list[int]) -> 'TreeNode':
    """Construct binary tree from preorder and inorder traversal"""
    if not preorder or not inorder:
        return None
    
    # Create index map for O(1) lookup in inorder
    inorder_map = {val: idx for idx, val in enumerate(inorder)}
    pre_idx = [0]  # Mutable container for preorder index
    
    def build(in_left: int, in_right: int) -> 'TreeNode':
        if in_left > in_right:
            return None
        
        # First element in preorder is root
        root_val = preorder[pre_idx[0]]
        pre_idx[0] += 1
        
        root = TreeNode(root_val)
        
        # Find root in inorder
        in_idx = inorder_map[root_val]
        
        # Build left subtree first (preorder: root, left, right)
        root.left = build(in_left, in_idx - 1)
        root.right = build(in_idx + 1, in_right)
        
        return root
    
    return build(0, len(inorder) - 1)
```

---

## 2️⃣ Build Tree from Inorder & Postorder (LC 106)

```python
def build_tree(inorder: list[int], postorder: list[int]) -> 'TreeNode':
    """Construct from inorder and postorder"""
    if not inorder or not postorder:
        return None
    
    inorder_map = {val: idx for idx, val in enumerate(inorder)}
    post_idx = [len(postorder) - 1]
    
    def build(in_left: int, in_right: int) -> 'TreeNode':
        if in_left > in_right:
            return None
        
        root_val = postorder[post_idx[0]]
        post_idx[0] -= 1
        
        root = TreeNode(root_val)
        in_idx = inorder_map[root_val]
        
        # Build right subtree first (postorder: left, right, root)
        root.right = build(in_idx + 1, in_right)
        root.left = build(in_left, in_idx - 1)
        
        return root
    
    return build(0, len(inorder) - 1)
```

---

## 3️⃣ Build Tree from Preorder & Postorder (LC 889)

```python
def construct_from_pre_post(preorder: list[int], postorder: list[int]) -> 'TreeNode':
    """Construct from preorder and postorder (not unique)"""
    if not preorder:
        return None
    
    post_map = {val: idx for idx, val in enumerate(postorder)}
    pre_idx = [0]
    
    def build(post_left: int, post_right: int) -> 'TreeNode':
        if post_left > post_right:
            return None
        
        root = TreeNode(preorder[pre_idx[0]])
        pre_idx[0] += 1
        
        if post_left == post_right:
            return root
        
        # Left child is next in preorder
        left_root_val = preorder[pre_idx[0]]
        left_root_idx = post_map[left_root_val]
        
        # Elements from post_left to left_root_idx are left subtree
        root.left = build(post_left, left_root_idx)
        root.right = build(left_root_idx + 1, post_right - 1)
        
        return root
    
    return build(0, len(postorder) - 1)
```

---

## 4️⃣ Build BST from Preorder (LC 1008)

```python
def bst_from_preorder(preorder: list[int]) -> 'TreeNode':
    """Construct BST from preorder traversal - O(n)"""
    idx = [0]
    
    def build(min_val: float, max_val: float) -> 'TreeNode':
        if idx[0] >= len(preorder):
            return None
        
        val = preorder[idx[0]]
        if val < min_val or val > max_val:
            return None
        
        idx[0] += 1
        root = TreeNode(val)
        root.left = build(min_val, val)
        root.right = build(val, max_val)
        
        return root
    
    return build(float('-inf'), float('inf'))
```

---

## 5️⃣ Serialize and Deserialize Binary Tree (LC 297)

```python
class Codec:
    """BFS serialization"""
    def serialize(self, root: 'TreeNode') -> str:
        """Encode tree to string"""
        if not root:
            return ""
        
        from collections import deque
        
        result = []
        queue = deque([root])
        
        while queue:
            node = queue.popleft()
            
            if node:
                result.append(str(node.val))
                queue.append(node.left)
                queue.append(node.right)
            else:
                result.append('null')
        
        # Remove trailing nulls
        while result and result[-1] == 'null':
            result.pop()
        
        return ','.join(result)
    
    def deserialize(self, data: str) -> 'TreeNode':
        """Decode string to tree"""
        if not data:
            return None
        
        from collections import deque
        
        values = data.split(',')
        root = TreeNode(int(values[0]))
        queue = deque([root])
        idx = 1
        
        while queue and idx < len(values):
            node = queue.popleft()
            
            # Left child
            if idx < len(values) and values[idx] != 'null':
                node.left = TreeNode(int(values[idx]))
                queue.append(node.left)
            idx += 1
            
            # Right child
            if idx < len(values) and values[idx] != 'null':
                node.right = TreeNode(int(values[idx]))
                queue.append(node.right)
            idx += 1
        
        return root

class CodecPreorder:
    """Preorder serialization with markers"""
    def serialize(self, root: 'TreeNode') -> str:
        result = []
        
        def preorder(node):
            if not node:
                result.append('#')
                return
            
            result.append(str(node.val))
            preorder(node.left)
            preorder(node.right)
        
        preorder(root)
        return ','.join(result)
    
    def deserialize(self, data: str) -> 'TreeNode':
        if not data:
            return None
        
        values = iter(data.split(','))
        
        def build() -> 'TreeNode':
            val = next(values)
            if val == '#':
                return None
            
            node = TreeNode(int(val))
            node.left = build()
            node.right = build()
            return node
        
        return build()
```

---

## 6️⃣ Serialize and Deserialize BST (LC 449)

```python
class CodecBST:
    """Optimized for BST - no null markers needed"""
    def serialize(self, root: 'TreeNode') -> str:
        result = []
        
        def preorder(node):
            if node:
                result.append(str(node.val))
                preorder(node.left)
                preorder(node.right)
        
        preorder(root)
        return ','.join(result)
    
    def deserialize(self, data: str) -> 'TreeNode':
        if not data:
            return None
        
        values = iter(int(x) for x in data.split(','))
        
        def build(min_val: float, max_val: float) -> 'TreeNode':
            val = next(values, None)
            if val is None or val < min_val or val > max_val:
                # Put it back (using peek pattern)
                return None
            
            node = TreeNode(val)
            node.left = build(min_val, val)
            node.right = build(val, max_val)
            return node
        
        # Alternative implementation with queue
        queue = [int(x) for x in data.split(',')]
        idx = [0]
        
        def build2(min_val: float, max_val: float) -> 'TreeNode':
            if idx[0] >= len(queue):
                return None
            
            val = queue[idx[0]]
            if val < min_val or val > max_val:
                return None
            
            idx[0] += 1
            node = TreeNode(val)
            node.left = build2(min_val, val)
            node.right = build2(val, max_val)
            return node
        
        return build2(float('-inf'), float('inf'))
```

---

## 7️⃣ Serialize N-ary Tree (LC 428)

```python
class CodecNary:
    """Serialize N-ary tree"""
    def serialize(self, root: 'Node') -> str:
        if not root:
            return ""
        
        result = []
        
        def encode(node):
            result.append(str(node.val))
            result.append(str(len(node.children)))
            for child in node.children:
                encode(child)
        
        encode(root)
        return ','.join(result)
    
    def deserialize(self, data: str) -> 'Node':
        if not data:
            return None
        
        values = iter(data.split(','))
        
        def decode() -> 'Node':
            val = int(next(values))
            num_children = int(next(values))
            node = Node(val, [])
            for _ in range(num_children):
                node.children.append(decode())
            return node
        
        return decode()
```

---

## 8️⃣ Verify Preorder Sequence (LC 255)

```python
def verify_preorder(preorder: list[int]) -> bool:
    """Verify if preorder sequence is valid BST"""
    # Use monotonic stack
    min_val = float('-inf')
    stack = []
    
    for val in preorder:
        # All right subtree values must be > root
        if val < min_val:
            return False
        
        # Pop smaller elements (going to right subtree)
        while stack and val > stack[-1]:
            min_val = stack.pop()
        
        stack.append(val)
    
    return True

def verify_preorder_inplace(preorder: list[int]) -> bool:
    """O(1) space using array as stack"""
    min_val = float('-inf')
    stack_idx = -1
    
    for val in preorder:
        if val < min_val:
            return False
        
        while stack_idx >= 0 and val > preorder[stack_idx]:
            min_val = preorder[stack_idx]
            stack_idx -= 1
        
        stack_idx += 1
        preorder[stack_idx] = val
    
    return True
```

---

## 9️⃣ Recover BST from Preorder String (LC 1028)

```python
def recover_from_preorder(traversal: str) -> 'TreeNode':
    """Reconstruct tree from "1-2--3--4-5--6--7" format"""
    stack = []
    i = 0
    
    while i < len(traversal):
        # Count depth (number of dashes)
        depth = 0
        while i < len(traversal) and traversal[i] == '-':
            depth += 1
            i += 1
        
        # Parse number
        j = i
        while j < len(traversal) and traversal[j].isdigit():
            j += 1
        val = int(traversal[i:j])
        i = j
        
        node = TreeNode(val)
        
        # Pop stack until we find parent
        while len(stack) > depth:
            stack.pop()
        
        # Attach to parent
        if stack:
            if not stack[-1].left:
                stack[-1].left = node
            else:
                stack[-1].right = node
        
        stack.append(node)
    
    return stack[0] if stack else None
```

---

## 🔟 Complete Binary Tree Insertion (LC 919)

```python
class CBTInserter:
    """Insert into complete binary tree"""
    def __init__(self, root: 'TreeNode'):
        from collections import deque
        
        self.root = root
        self.queue = deque()
        
        # BFS to find incomplete nodes
        q = deque([root])
        while q:
            node = q.popleft()
            if not node.left or not node.right:
                self.queue.append(node)
            if node.left:
                q.append(node.left)
            if node.right:
                q.append(node.right)
    
    def insert(self, val: int) -> int:
        parent = self.queue[0]
        new_node = TreeNode(val)
        
        if not parent.left:
            parent.left = new_node
        else:
            parent.right = new_node
            self.queue.popleft()  # Parent now complete
        
        self.queue.append(new_node)
        return parent.val
    
    def get_root(self) -> 'TreeNode':
        return self.root
```

---

## 📊 Build Tree Summary

| Input | Output | Key Insight |
|-------|--------|-------------|
| Preorder + Inorder | Binary Tree | First preorder = root, split inorder |
| Inorder + Postorder | Binary Tree | Last postorder = root, split inorder |
| Preorder + Postorder | Binary Tree | Not unique, use left child info |
| Preorder only | BST | Use bounds to determine structure |
| Level order | Complete Tree | BFS construction |

---

## 📝 Related Problems

| Problem | LC # | Pattern |
|---------|------|---------|
| Build Tree from Pre+In | 105 | Divide & Conquer |
| Build Tree from In+Post | 106 | Divide & Conquer |
| Build Tree from Pre+Post | 889 | Divide & Conquer |
| BST from Preorder | 1008 | Bounds |
| Serialize Binary Tree | 297 | BFS/DFS |
| Serialize BST | 449 | Optimized |
| Serialize N-ary Tree | 428 | Child Count |
| Verify BST Preorder | 255 | Monotonic Stack |
| Recover from Preorder | 1028 | Stack |
| CBT Inserter | 919 | Queue |
| Clone Tree | - | DFS |
| Flatten to Linked List | 114 | Preorder |
| Convert to Greater Tree | 538 | Reverse Inorder |

---

*Last Updated: 2024*
