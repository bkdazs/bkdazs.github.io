---
title: Tries / Prefix Trees
topic: Trie
difficulty: Medium
tags: [trie, prefix-tree, autocomplete, string-search, dictionary]
status: complete
weight: 13
---

# Tries / Prefix Trees

## 1. Summary / TL;DR

- **Trie** (pronounced "try") is a tree for storing strings, where each node represents a character
- Enables **O(m)** operations where m = word length (independent of dictionary size!)
- **Key applications**: autocomplete, spell check, IP routing, word games
- Each path from root to node represents a prefix; leaf-marked nodes represent complete words
- Space-intensive but extremely fast for prefix-based queries
- Variants: Compressed trie (radix tree), Suffix trie, Ternary search tree

---

## 2. When & Where to Use

| Scenario | Why Trie? |
|----------|-----------|
| Autocomplete/Type-ahead | All words with prefix in O(p + k) |
| Spell checker | Fast lookup + suggest corrections |
| IP routing (longest prefix match) | Router forwarding tables |
| Word games (Scrabble, Boggle) | Check valid words efficiently |
| Dictionary implementation | Fast search with prefix queries |
| Text prediction | Phone keyboards, search suggestions |
| DNA sequence matching | Alphabet size = 4 (A, C, G, T) |

### Trie vs Hash Table

| Aspect | Trie | Hash Table |
|--------|------|------------|
| Search | O(m) | O(m) average |
| Prefix search | O(p) | O(n * m) - must check all |
| Space | O(Σ * m * n) | O(m * n) |
| Ordered iteration | Natural | Need sorting |
| Longest prefix | O(m) | Difficult |

---

## 3. Time & Space Complexity

| Operation | Time | Notes |
|-----------|------|-------|
| Insert word | O(m) | m = word length |
| Search word | O(m) | |
| Search prefix | O(p) | p = prefix length |
| Delete word | O(m) | May need cleanup |
| Find all with prefix | O(p + k) | k = number of matches |
| Longest common prefix | O(m) | Traverse while single path |

**Space Complexity**: 
- Worst case: O(Σ * m * n) where Σ = alphabet size, m = max word length, n = number of words
- In practice, much less due to shared prefixes

---

## 4. Core Concepts & Theory

### Trie Structure

```
Words: ["apple", "app", "apt", "bat", "bad"]

          (root)
         /      \
        a        b
       /          \
      p            a
     / \          / \
    p   t        t   d
   /    *        *   *
  l
 /
e
*

* = end of word marker
```

### Key Properties

1. **Root is empty**: Represents the empty string
2. **Each edge represents a character**: Path from root spells a string
3. **End marker distinguishes words**: "app" vs just prefix of "apple"
4. **Shared prefixes share structure**: "apple" and "app" share 3 nodes
5. **Children typically stored as**: Array (fast, more space) or HashMap (flexible, less space)

### Node Structure Options

```python
# Option 1: Array-based (fixed alphabet like lowercase letters)
children = [None] * 26  # Index 0 = 'a', 1 = 'b', etc.

# Option 2: HashMap-based (variable/large alphabet)
children = {}  # Key = character, value = child node

# Option 3: Both (hybrid for common case optimization)
```

---

## 5. Diagrams / Visualizations

### Insert Process: "apple"

```
Step 1: Insert 'a'        Step 2: Insert 'p'       Step 3: Insert 'p'
    (root)                    (root)                   (root)
       |                         |                        |
       a                         a                        a
                                 |                        |
                                 p                        p
                                                          |
                                                          p

Step 4: Insert 'l'        Step 5: Insert 'e' + mark end
    (root)                    (root)
       |                         |
       a                         a
       |                         |
       p                         p
       |                         |
       p                         p
       |                         |
       l                         l
                                 |
                                 e*
```

### Search Process: "app"

```
Trie with ["apple", "app", "apt"]:

    (root)
       |
       a ← Check 'a': exists ✓
       |
       p ← Check 'p': exists ✓
      / \
     p*  t*  ← Check 'p': exists ✓, is_end = True ✓
    /
   l
   |
   e*

"app" found! (exists as word, not just prefix)
```

### Prefix Search: "ap"

```
Starting from "ap", find all words:

    (root)
       |
       a
       |
       p ← Start here after matching "ap"
      / \
     p   t* → "apt"
    /
   l
   |
   e* → "apple"
   
Also: "app" (p node is marked)

Results: ["app", "apple", "apt"]
```

---

## 6. Implementation (Python)

### Basic Trie Node

```python
from typing import Optional, List, Dict
from collections import defaultdict

class TrieNode:
    """Basic Trie node using dictionary for children."""
    
    def __init__(self):
        self.children: Dict[str, 'TrieNode'] = {}
        self.is_end: bool = False
    
    def __repr__(self) -> str:
        return f"TrieNode(children={list(self.children.keys())}, is_end={self.is_end})"
```

### Complete Trie Implementation

```python
class Trie:
    """
    Trie (Prefix Tree) implementation.
    
    Supports:
    - Insert word: O(m)
    - Search word: O(m)
    - Search prefix: O(p)
    - Find all words with prefix: O(p + k)
    - Delete word: O(m)
    
    >>> trie = Trie()
    >>> trie.insert("apple")
    >>> trie.insert("app")
    >>> trie.search("app")
    True
    >>> trie.starts_with("ap")
    True
    >>> trie.search("ap")
    False
    """
    
    def __init__(self):
        self.root = TrieNode()
    
    def insert(self, word: str) -> None:
        """
        Insert word into trie.
        
        Time: O(m) where m = len(word)
        Space: O(m) in worst case (no shared prefix)
        """
        node = self.root
        for char in word:
            if char not in node.children:
                node.children[char] = TrieNode()
            node = node.children[char]
        node.is_end = True
    
    def search(self, word: str) -> bool:
        """
        Search for exact word in trie.
        
        Time: O(m)
        """
        node = self._find_node(word)
        return node is not None and node.is_end
    
    def starts_with(self, prefix: str) -> bool:
        """
        Check if any word starts with prefix.
        
        Time: O(p) where p = len(prefix)
        """
        return self._find_node(prefix) is not None
    
    def _find_node(self, prefix: str) -> Optional[TrieNode]:
        """Navigate to node representing prefix."""
        node = self.root
        for char in prefix:
            if char not in node.children:
                return None
            node = node.children[char]
        return node
    
    def delete(self, word: str) -> bool:
        """
        Delete word from trie.
        Returns True if word existed and was deleted.
        
        Time: O(m)
        """
        def _delete(node: TrieNode, word: str, depth: int) -> bool:
            """
            Returns True if parent should delete this node.
            """
            if depth == len(word):
                if not node.is_end:
                    return False  # Word doesn't exist
                node.is_end = False
                return len(node.children) == 0  # Delete if no children
            
            char = word[depth]
            if char not in node.children:
                return False  # Word doesn't exist
            
            child = node.children[char]
            should_delete_child = _delete(child, word, depth + 1)
            
            if should_delete_child:
                del node.children[char]
                return len(node.children) == 0 and not node.is_end
            
            return False
        
        return _delete(self.root, word, 0)
    
    def find_all_words_with_prefix(self, prefix: str) -> List[str]:
        """
        Find all words starting with prefix.
        
        Time: O(p + k) where k = total chars in all matching words
        
        >>> trie = Trie()
        >>> for word in ["apple", "app", "application", "apt"]: trie.insert(word)
        >>> sorted(trie.find_all_words_with_prefix("app"))
        ['app', 'apple', 'application']
        """
        result = []
        node = self._find_node(prefix)
        
        if node is None:
            return result
        
        # DFS to find all words
        def dfs(node: TrieNode, path: str) -> None:
            if node.is_end:
                result.append(path)
            for char, child in node.children.items():
                dfs(child, path + char)
        
        dfs(node, prefix)
        return result
    
    def count_words_with_prefix(self, prefix: str) -> int:
        """Count number of words starting with prefix."""
        words = self.find_all_words_with_prefix(prefix)
        return len(words)
    
    def get_all_words(self) -> List[str]:
        """Return all words in trie (alphabetically ordered due to DFS)."""
        return self.find_all_words_with_prefix("")
    
    def longest_common_prefix(self) -> str:
        """
        Find longest common prefix among all words.
        
        >>> trie = Trie()
        >>> for word in ["flower", "flow", "flight"]: trie.insert(word)
        >>> trie.longest_common_prefix()
        'fl'
        """
        result = []
        node = self.root
        
        while node and len(node.children) == 1 and not node.is_end:
            char = list(node.children.keys())[0]
            result.append(char)
            node = node.children[char]
        
        return ''.join(result)
```

### Trie with Array-based Children (Optimized)

```python
class TrieNodeArray:
    """Trie node optimized for lowercase letters only."""
    
    def __init__(self):
        self.children: List[Optional['TrieNodeArray']] = [None] * 26
        self.is_end: bool = False
        self.word: Optional[str] = None  # Store complete word (optional)
    
    def _char_to_index(self, char: str) -> int:
        return ord(char) - ord('a')


class TrieOptimized:
    """
    Optimized Trie for lowercase English letters.
    Uses array instead of dict for O(1) child access.
    """
    
    def __init__(self):
        self.root = TrieNodeArray()
    
    def insert(self, word: str) -> None:
        node = self.root
        for char in word:
            idx = node._char_to_index(char)
            if node.children[idx] is None:
                node.children[idx] = TrieNodeArray()
            node = node.children[idx]
        node.is_end = True
        node.word = word
    
    def search(self, word: str) -> bool:
        node = self._find_node(word)
        return node is not None and node.is_end
    
    def starts_with(self, prefix: str) -> bool:
        return self._find_node(prefix) is not None
    
    def _find_node(self, s: str) -> Optional[TrieNodeArray]:
        node = self.root
        for char in s:
            idx = node._char_to_index(char)
            if node.children[idx] is None:
                return None
            node = node.children[idx]
        return node
```

### Trie with Count (Word Frequency)

```python
class TrieNodeCount:
    """Trie node with word count for frequency tracking."""
    
    def __init__(self):
        self.children: Dict[str, 'TrieNodeCount'] = {}
        self.count: int = 0  # How many times this word was inserted
        self.prefix_count: int = 0  # How many words have this prefix


class TrieWithCount:
    """
    Trie that tracks word frequency and prefix counts.
    
    >>> trie = TrieWithCount()
    >>> trie.insert("apple")
    >>> trie.insert("apple")
    >>> trie.insert("app")
    >>> trie.get_count("apple")
    2
    >>> trie.get_prefix_count("app")
    3
    """
    
    def __init__(self):
        self.root = TrieNodeCount()
    
    def insert(self, word: str) -> None:
        node = self.root
        for char in word:
            if char not in node.children:
                node.children[char] = TrieNodeCount()
            node = node.children[char]
            node.prefix_count += 1
        node.count += 1
    
    def get_count(self, word: str) -> int:
        """Get number of times word was inserted."""
        node = self._find_node(word)
        return node.count if node else 0
    
    def get_prefix_count(self, prefix: str) -> int:
        """Get number of words with this prefix."""
        node = self._find_node(prefix)
        return node.prefix_count if node else 0
    
    def _find_node(self, s: str) -> Optional[TrieNodeCount]:
        node = self.root
        for char in s:
            if char not in node.children:
                return None
            node = node.children[char]
        return node
```

### Autocomplete with Top-K Results

```python
import heapq
from typing import Tuple

class TrieNodeAuto:
    def __init__(self):
        self.children: Dict[str, 'TrieNodeAuto'] = {}
        self.is_end: bool = False
        self.frequency: int = 0  # For ranking results


class AutocompleteTrie:
    """
    Trie optimized for autocomplete with frequency-based ranking.
    
    >>> auto = AutocompleteTrie()
    >>> auto.insert("amazon", 100)
    >>> auto.insert("amazing", 50)
    >>> auto.insert("amaze", 30)
    >>> auto.autocomplete("ama", 2)
    ['amazon', 'amazing']
    """
    
    def __init__(self):
        self.root = TrieNodeAuto()
    
    def insert(self, word: str, frequency: int = 1) -> None:
        """Insert word with frequency/popularity score."""
        node = self.root
        for char in word:
            if char not in node.children:
                node.children[char] = TrieNodeAuto()
            node = node.children[char]
        node.is_end = True
        node.frequency = frequency
    
    def autocomplete(self, prefix: str, k: int = 5) -> List[str]:
        """
        Return top-k words matching prefix, sorted by frequency.
        
        Time: O(p + n) where n = nodes in subtree
        """
        node = self._find_node(prefix)
        if not node:
            return []
        
        # Collect all words with their frequencies
        candidates: List[Tuple[int, str]] = []
        
        def dfs(node: TrieNodeAuto, path: str) -> None:
            if node.is_end:
                heapq.heappush(candidates, (-node.frequency, path))
            for char, child in node.children.items():
                dfs(child, path + char)
        
        dfs(node, prefix)
        
        # Return top-k
        result = []
        for _ in range(min(k, len(candidates))):
            _, word = heapq.heappop(candidates)
            result.append(word)
        
        return result
    
    def _find_node(self, prefix: str) -> Optional[TrieNodeAuto]:
        node = self.root
        for char in prefix:
            if char not in node.children:
                return None
            node = node.children[char]
        return node
```

---

## 7. Step-by-Step Worked Example

### Problem: Word Search II (LeetCode 212)

**Problem**: Given a 2D board of characters and a list of words, find all words that can be constructed by sequentially adjacent cells (horizontally or vertically).

**Example**:
```
board = [
  ['o','a','a','n'],
  ['e','t','a','e'],
  ['i','h','k','r'],
  ['i','f','l','v']
]
words = ["oath", "pea", "eat", "rain"]

Output: ["eat", "oath"]
```

**Approach**: Build trie from words, then DFS on board using trie to prune search.

```python
def find_words(board: List[List[str]], words: List[str]) -> List[str]:
    """
    Find all words from list that exist in board.
    
    Approach:
    1. Build trie from word list
    2. DFS from each cell, using trie to prune
    3. Mark found words to avoid duplicates
    
    Time: O(M * N * 4^L) where L = max word length
    Space: O(total chars in words)
    """
    # Build trie
    root = TrieNode()
    for word in words:
        node = root
        for char in word:
            if char not in node.children:
                node.children[char] = TrieNode()
            node = node.children[char]
        node.is_end = True
        node.word = word  # Store word at end node
    
    rows, cols = len(board), len(board[0])
    result = []
    
    def dfs(r: int, c: int, node: TrieNode) -> None:
        char = board[r][c]
        
        # Not in trie path - prune
        if char not in node.children:
            return
        
        next_node = node.children[char]
        
        # Found a word
        if next_node.is_end:
            result.append(next_node.word)
            next_node.is_end = False  # Avoid duplicates
        
        # Mark visited
        board[r][c] = '#'
        
        # Explore neighbors
        for dr, dc in [(0, 1), (0, -1), (1, 0), (-1, 0)]:
            nr, nc = r + dr, c + dc
            if 0 <= nr < rows and 0 <= nc < cols and board[nr][nc] != '#':
                dfs(nr, nc, next_node)
        
        # Restore
        board[r][c] = char
        
        # Optimization: prune empty branches
        if not next_node.children:
            del node.children[char]
    
    # Start DFS from each cell
    for r in range(rows):
        for c in range(cols):
            dfs(r, c, root)
    
    return result
```

**Trace through finding "eat"**:
```
Trie for ["oath", "pea", "eat", "rain"]:
       root
      /  |  \  \
     o   p   e   r
     |   |   |   |
     a   e   a   a
     |   |   |   |
     t   a   t   i
     |   *   *   |
     h           n
     *           *

Starting at board[1][1] = 'e':
  dfs(1,1,root): 'e' in children ✓
    dfs(1,2,e_node): 'a' in children ✓
      dfs(0,2,a_node): 'a' - not in children ✗
      dfs(2,2,a_node): 'k' - not in children ✗
      dfs(1,1,a_node): '#' (visited) ✗
      dfs(1,3,a_node): 'e' - not in children ✗
    dfs(0,1,e_node): 'a' in children ✓
      dfs(0,0,a_node): 'o' - not in children ✗
      dfs(0,2,a_node): 'a' - not in children ✗
      dfs(1,1,a_node): '#' (visited) ✗
    dfs(2,1,e_node): 'h' - not in children ✗

Hmm, let me retrace with correct board positions...
Actually at board[1][1]='t', board[0][1]='a', board[1][0]='e'

Starting at (1,0) = 'e':
  dfs(1,0,root): 'e' in children ✓
    (1,1) = 't': not in e's children
    (2,0) = 'i': not in e's children  
    (0,0) = 'o': not in e's children

Starting at (2,1) = 'h':
  ...not in root's children

Let me check board again: board[0] = ['o','a','a','n']
So (0,1) = 'a', but 'e' is at...

For "eat": need e-a-t path
board[1][0]='e', then need 'a' adjacent
board[0][0]='o', board[2][0]='i', board[1][1]='t' - no 'a'!

Looking more carefully at the board:
Row 0: o a a n
Row 1: e t a e
Row 2: i h k r
Row 3: i f l v

For "eat" starting at (1,0)='e':
  (1,0)->'e', (1,1)->'t'? No, need 'a' first
  
Oh I see - "eat" can be found via (1,3)->(0,3)? No...
Let me trace: e(1,0) -> a? Need adjacent 'a'
Adjacent to (1,0): (0,0)='o', (1,1)='t', (2,0)='i'
No 'a' adjacent to 'e' at (1,0)

Check e at (1,3): adjacent cells (0,3)='n', (1,2)='a'! ✓
So: e(1,3) -> a(1,2) -> t(1,1)? Is t(1,1) adjacent to a(1,2)? Yes! ✓

DFS trace for "eat":
  Start at (1,3)='e', node=root
  'e' in root.children ✓, move to e_node
  Mark (1,3)='#'
  
  Check neighbor (1,2)='a', node=e_node  
  'a' in e_node.children ✓, move to a_node
  Mark (1,2)='#'
  
  Check neighbor (1,1)='t', node=a_node
  't' in a_node.children ✓, move to t_node
  t_node.is_end = True → Found "eat"! ✓
```

---

## 8. Common Mistakes

1. **Not marking end of word**
   ```python
   # Wrong: only checks if path exists
   def search(self, word):
       node = self._find_node(word)
       return node is not None  # "app" would match for "apple"
   
   # Correct: check is_end flag
   def search(self, word):
       node = self._find_node(word)
       return node is not None and node.is_end
   ```

2. **Memory leaks on delete**
   ```python
   # Wrong: just unmark is_end
   def delete(self, word):
       node = self._find_node(word)
       if node:
           node.is_end = False  # Nodes still exist!
   
   # Better: prune empty branches (see implementation above)
   ```

3. **Incorrect character indexing**
   ```python
   # Wrong for uppercase or non-letters
   idx = ord(char) - ord('a')  # Fails for 'A' or '1'
   
   # Safe: validate or use dictionary
   if 'a' <= char <= 'z':
       idx = ord(char) - ord('a')
   else:
       # Handle other characters
   ```

4. **Not handling empty strings**
   ```python
   # Consider: should empty string be searchable?
   trie.insert("")  # Valid? Check requirements
   ```

5. **Forgetting to restore state in DFS**
   ```python
   # In Word Search II, must restore board cell
   board[r][c] = '#'  # Mark visited
   # ... dfs ...
   board[r][c] = char  # MUST restore!
   ```

---

## 9. Variations & Optimizations

### Compressed Trie (Radix Tree)

```python
class RadixNode:
    """
    Radix tree node - stores string segment instead of single char.
    Reduces space by combining chains of single-child nodes.
    """
    def __init__(self, key: str = ""):
        self.key = key  # Edge label (can be multiple chars)
        self.children: Dict[str, 'RadixNode'] = {}
        self.is_end: bool = False


class RadixTree:
    """
    Compressed trie where single-child chains are merged.
    
    Regular trie:        Radix tree:
        r                    root
        |                      |
        o                   "romane"
        |                    / \
        m              "us"   "ulus"
       /|\                     |
      a n u                  "us"
      | | |
      n e s
      ...
    
    Much more space-efficient for long common prefixes.
    """
    
    def __init__(self):
        self.root = RadixNode()
    
    def insert(self, word: str) -> None:
        """Insert word into radix tree."""
        node = self.root
        i = 0
        
        while i < len(word):
            # Find child starting with word[i]
            found = False
            for key, child in node.children.items():
                if key[0] == word[i]:
                    # Find common prefix length
                    j = 0
                    while j < len(key) and i + j < len(word) and key[j] == word[i + j]:
                        j += 1
                    
                    if j == len(key):
                        # Fully matched, continue down
                        node = child
                        i += j
                    else:
                        # Partial match - split node
                        common = key[:j]
                        remaining_key = key[j:]
                        remaining_word = word[i + j:]
                        
                        # Create new intermediate node
                        new_node = RadixNode(common)
                        node.children[common] = new_node
                        del node.children[key]
                        
                        # Move old child under new node
                        child.key = remaining_key
                        new_node.children[remaining_key] = child
                        
                        # Add new word suffix if any
                        if remaining_word:
                            new_child = RadixNode(remaining_word)
                            new_child.is_end = True
                            new_node.children[remaining_word] = new_child
                        else:
                            new_node.is_end = True
                        
                        return
                    found = True
                    break
            
            if not found:
                # No matching child, create new
                new_node = RadixNode(word[i:])
                new_node.is_end = True
                node.children[word[i:]] = new_node
                return
        
        node.is_end = True
```

### Suffix Trie

```python
class SuffixTrie:
    """
    Trie containing all suffixes of a string.
    Useful for substring search.
    
    For "banana":
    Suffixes: banana, anana, nana, ana, na, a
    
    Can answer "is X a substring?" in O(|X|)
    """
    
    def __init__(self, text: str):
        self.root = TrieNode()
        # Insert all suffixes
        for i in range(len(text)):
            self._insert(text[i:])
    
    def _insert(self, suffix: str) -> None:
        node = self.root
        for char in suffix:
            if char not in node.children:
                node.children[char] = TrieNode()
            node = node.children[char]
        node.is_end = True
    
    def contains_substring(self, pattern: str) -> bool:
        """Check if pattern is a substring of original text."""
        node = self.root
        for char in pattern:
            if char not in node.children:
                return False
            node = node.children[char]
        return True
```

### Ternary Search Tree

```python
class TSTNode:
    """Ternary Search Tree node - more space-efficient than standard trie."""
    def __init__(self, char: str):
        self.char = char
        self.left: Optional['TSTNode'] = None   # < char
        self.mid: Optional['TSTNode'] = None    # = char (child)
        self.right: Optional['TSTNode'] = None  # > char
        self.is_end: bool = False


class TernarySearchTree:
    """
    Ternary Search Tree: combines trie efficiency with BST space efficiency.
    
    Each node has 3 children:
    - left: chars less than current
    - mid: next char in word
    - right: chars greater than current
    
    Space: O(3n) vs O(26n) for array trie
    Time: O(m + log n) vs O(m) for trie
    """
    
    def __init__(self):
        self.root: Optional[TSTNode] = None
    
    def insert(self, word: str) -> None:
        self.root = self._insert(self.root, word, 0)
    
    def _insert(self, node: Optional[TSTNode], word: str, idx: int) -> TSTNode:
        char = word[idx]
        
        if node is None:
            node = TSTNode(char)
        
        if char < node.char:
            node.left = self._insert(node.left, word, idx)
        elif char > node.char:
            node.right = self._insert(node.right, word, idx)
        elif idx < len(word) - 1:
            node.mid = self._insert(node.mid, word, idx + 1)
        else:
            node.is_end = True
        
        return node
    
    def search(self, word: str) -> bool:
        node = self._find(self.root, word, 0)
        return node is not None and node.is_end
    
    def _find(self, node: Optional[TSTNode], word: str, idx: int) -> Optional[TSTNode]:
        if node is None:
            return None
        
        char = word[idx]
        
        if char < node.char:
            return self._find(node.left, word, idx)
        elif char > node.char:
            return self._find(node.right, word, idx)
        elif idx < len(word) - 1:
            return self._find(node.mid, word, idx + 1)
        else:
            return node
```

---

## 10. Interview Tips

### What Interviewers Look For

1. **Understanding of structure**: Can you explain how trie stores strings?
2. **Time complexity analysis**: Why O(m) regardless of dictionary size?
3. **Trade-off awareness**: When trie vs hash table?
4. **Optimization thinking**: Can you optimize space? Prune searches?

### Common Follow-up Questions

```
Q: What's the space complexity of a trie?
A: O(Σ * M * N) worst case where Σ=alphabet size, M=max length, N=words.
   In practice, much less due to shared prefixes.

Q: How would you implement autocomplete?
A: Navigate to prefix node, then DFS to collect all words. 
   Sort by frequency/recency for relevance.

Q: How to handle very large dictionaries?
A: Use compressed trie (radix tree), or store on disk with 
   memory-mapped files. Consider bloom filter for negative lookups.

Q: What if alphabet is huge (Unicode)?
A: Use HashMap for children instead of array.
   Consider ternary search tree for better space.
```

### Quick Pattern Recognition

```
See "prefix matching"? → Trie
See "autocomplete"? → Trie with DFS
See "word search on grid"? → Trie + DFS/backtracking
See "spell checker"? → Trie + edit distance
See "longest common prefix"? → Trie (single-path check)
```

---

## 11. Related Patterns

| Pattern | Description | Example Problems |
|---------|-------------|------------------|
| Trie + DFS | Explore all words matching pattern | Word Search II |
| Trie + Backtracking | Build words character by character | Add and Search Word |
| Trie + DP | Combine with memoization | Word Break |
| Trie for Bits | XOR queries, binary representation | Maximum XOR |

---

## 12. References

- **CLRS**: Not covered (see specialized texts)
- **Sedgewick**: Algorithms 4th Ed., Chapter 5.2
- **cp-algorithms**: [Trie](https://cp-algorithms.com/string/trie.html)
- **LeetCode**: Trie tag (30+ problems)

---

## 13. Practice Problems

### Easy
| # | Problem | Key Concepts | LeetCode |
|---|---------|--------------|----------|
| 1 | Implement Trie | Basic trie ops | [208](https://leetcode.com/problems/implement-trie-prefix-tree/) |
| 2 | Longest Common Prefix | Trie structure | [14](https://leetcode.com/problems/longest-common-prefix/) |
| 3 | Index Pairs of String | Word matching | [1065](https://leetcode.com/problems/index-pairs-of-a-string/) |

### Medium
| # | Problem | Key Concepts | LeetCode |
|---|---------|--------------|----------|
| 4 | Design Add and Search Words | Trie + wildcard DFS | [211](https://leetcode.com/problems/design-add-and-search-words-data-structure/) |
| 5 | Replace Words | Prefix replacement | [648](https://leetcode.com/problems/replace-words/) |
| 6 | Map Sum Pairs | Trie with values | [677](https://leetcode.com/problems/map-sum-pairs/) |
| 7 | Top K Frequent Words | Trie + frequency | [692](https://leetcode.com/problems/top-k-frequent-words/) |
| 8 | Camelcase Matching | Pattern matching | [1023](https://leetcode.com/problems/camelcase-matching/) |
| 9 | Implement Magic Dictionary | Trie + edit distance | [676](https://leetcode.com/problems/implement-magic-dictionary/) |
| 10 | Search Suggestions System | Autocomplete | [1268](https://leetcode.com/problems/search-suggestions-system/) |
| 11 | Stream of Characters | Suffix matching | [1032](https://leetcode.com/problems/stream-of-characters/) |
| 12 | Maximum XOR of Two Numbers | Bit trie | [421](https://leetcode.com/problems/maximum-xor-of-two-numbers-in-an-array/) |

### Hard
| # | Problem | Key Concepts | LeetCode |
|---|---------|--------------|----------|
| 13 | Word Search II | Trie + board DFS | [212](https://leetcode.com/problems/word-search-ii/) |
| 14 | Palindrome Pairs | Trie + palindrome | [336](https://leetcode.com/problems/palindrome-pairs/) |
| 15 | Word Squares | Backtracking + trie | [425](https://leetcode.com/problems/word-squares/) |
| 16 | Concatenated Words | Trie + DP | [472](https://leetcode.com/problems/concatenated-words/) |
| 17 | Prefix and Suffix Search | Double trie | [745](https://leetcode.com/problems/prefix-and-suffix-search/) |

---

## 14. Key Takeaways

1. **O(m) operations independent of dictionary size**: This is trie's superpower

2. **Perfect for prefix-based queries**: Autocomplete, spell check, IP routing

3. **Space vs time trade-off**: More space than hash table, but supports prefix ops

4. **Two main implementations**:
   - Array-based: O(1) child access, more space, fixed alphabet
   - HashMap-based: Flexible alphabet, slightly slower

5. **Common optimizations**:
   - Compressed trie (radix tree) for space
   - Store word at end node for quick retrieval
   - Count at nodes for frequency queries

---

## 15. Spaced Repetition Prompts

1. **Q**: What is the time complexity of trie operations and why?
   **A**: O(m) where m = word length. We traverse exactly one node per character, regardless of how many words are in the trie.

2. **Q**: When would you use a trie instead of a hash set?
   **A**: When you need prefix operations (autocomplete, starts_with), longest common prefix, or want sorted iteration of strings.

3. **Q**: How do you implement "find all words with prefix"?
   **A**: Navigate to node representing prefix, then DFS from there collecting all words (nodes with is_end=True).

4. **Q**: What are the two main ways to store trie children?
   **A**: Array (size = alphabet, O(1) access, more space) or HashMap (flexible alphabet, O(1) average access, less space).

5. **Q**: In Word Search II, why use trie instead of checking each word separately?
   **A**: Trie allows pruning - we abandon a path as soon as it doesn't match any word prefix. Without trie, we'd do O(words * cells * 4^L) vs O(cells * 4^L) with trie.
