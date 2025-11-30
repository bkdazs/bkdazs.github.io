---
title: "Trie (Prefix Tree) Patterns"
topic: "Problem Patterns"
difficulty: "Medium"
tags: ["trie", "prefix-tree", "string", "autocomplete"]
status: "complete"
weight: 22
---

# Trie (Prefix Tree) Patterns

## 📚 Overview

Tries are efficient for string prefix operations, autocomplete, and word search problems.

---

## 1️⃣ Basic Trie Implementation

```python
class TrieNode:
    def __init__(self):
        self.children = {}
        self.is_end = False
        self.word = None  # Optional: store word at end

class Trie:
    """Implement Trie (LC 208)"""
    def __init__(self):
        self.root = TrieNode()
    
    def insert(self, word: str) -> None:
        """Insert word - O(m) where m = len(word)"""
        node = self.root
        for char in word:
            if char not in node.children:
                node.children[char] = TrieNode()
            node = node.children[char]
        node.is_end = True
        node.word = word
    
    def search(self, word: str) -> bool:
        """Search for exact word - O(m)"""
        node = self._find_node(word)
        return node is not None and node.is_end
    
    def startsWith(self, prefix: str) -> bool:
        """Check if any word starts with prefix - O(m)"""
        return self._find_node(prefix) is not None
    
    def _find_node(self, prefix: str) -> TrieNode:
        """Helper to find node for prefix"""
        node = self.root
        for char in prefix:
            if char not in node.children:
                return None
            node = node.children[char]
        return node
```

---

## 2️⃣ Trie with Array Children

```python
class TrieArray:
    """Array-based Trie (faster for lowercase letters only)"""
    def __init__(self):
        self.root = [None] * 27  # 26 letters + is_end flag
    
    def insert(self, word: str) -> None:
        node = self.root
        for char in word:
            idx = ord(char) - ord('a')
            if node[idx] is None:
                node[idx] = [None] * 27
            node = node[idx]
        node[26] = True  # Mark end
    
    def search(self, word: str) -> bool:
        node = self.root
        for char in word:
            idx = ord(char) - ord('a')
            if node[idx] is None:
                return False
            node = node[idx]
        return node[26] is True
```

---

## 3️⃣ Word Search II (LC 212)

```python
def find_words(board: list[list[str]], words: list[str]) -> list[str]:
    """Find all words from dictionary in grid"""
    # Build Trie
    trie = {}
    for word in words:
        node = trie
        for char in word:
            node = node.setdefault(char, {})
        node['$'] = word  # End marker with word
    
    rows, cols = len(board), len(board[0])
    result = []
    
    def dfs(r: int, c: int, parent: dict):
        char = board[r][c]
        node = parent.get(char)
        
        if not node:
            return
        
        # Found word
        if '$' in node:
            result.append(node['$'])
            del node['$']  # Avoid duplicates
        
        # Mark visited
        board[r][c] = '#'
        
        # Explore neighbors
        for dr, dc in [(0, 1), (0, -1), (1, 0), (-1, 0)]:
            nr, nc = r + dr, c + dc
            if 0 <= nr < rows and 0 <= nc < cols and board[nr][nc] != '#':
                dfs(nr, nc, node)
        
        # Restore
        board[r][c] = char
        
        # Optimization: prune empty branches
        if not node:
            del parent[char]
    
    for r in range(rows):
        for c in range(cols):
            dfs(r, c, trie)
    
    return result
```

---

## 4️⃣ Add and Search Word (LC 211)

```python
class WordDictionary:
    """Supports . wildcard in search"""
    def __init__(self):
        self.root = {}
    
    def addWord(self, word: str) -> None:
        node = self.root
        for char in word:
            node = node.setdefault(char, {})
        node['$'] = True
    
    def search(self, word: str) -> bool:
        def dfs(idx: int, node: dict) -> bool:
            if idx == len(word):
                return '$' in node
            
            char = word[idx]
            
            if char == '.':
                # Try all children
                for child in node:
                    if child != '$' and dfs(idx + 1, node[child]):
                        return True
                return False
            else:
                if char not in node:
                    return False
                return dfs(idx + 1, node[char])
        
        return dfs(0, self.root)
```

---

## 5️⃣ Autocomplete System (LC 642)

```python
class AutocompleteSystem:
    """Design autocomplete with history"""
    def __init__(self, sentences: list[str], times: list[int]):
        self.trie = {}
        self.counts = {}
        self.current_input = ""
        
        for sentence, count in zip(sentences, times):
            self.counts[sentence] = count
            self._insert(sentence)
    
    def _insert(self, sentence: str) -> None:
        node = self.trie
        for char in sentence:
            node = node.setdefault(char, {})
            node.setdefault('sentences', set()).add(sentence)
    
    def input(self, c: str) -> list[str]:
        if c == '#':
            # End of input - save sentence
            self.counts[self.current_input] = self.counts.get(self.current_input, 0) + 1
            self._insert(self.current_input)
            self.current_input = ""
            return []
        
        self.current_input += c
        
        # Find node for current prefix
        node = self.trie
        for char in self.current_input:
            if char not in node:
                return []
            node = node[char]
        
        # Get all sentences with this prefix
        sentences = node.get('sentences', set())
        
        # Sort by -count, then alphabetically
        sorted_sentences = sorted(sentences, key=lambda s: (-self.counts.get(s, 0), s))
        
        return sorted_sentences[:3]
```

---

## 6️⃣ Replace Words (LC 648)

```python
def replace_words(dictionary: list[str], sentence: str) -> str:
    """Replace words with their shortest root in dictionary"""
    # Build Trie
    trie = {}
    for root in dictionary:
        node = trie
        for char in root:
            node = node.setdefault(char, {})
        node['$'] = root  # Store root at end
    
    def find_root(word: str) -> str:
        node = trie
        for char in word:
            if '$' in node:
                return node['$']  # Found shortest root
            if char not in node:
                return word  # No root found
            node = node[char]
        return node.get('$', word)
    
    words = sentence.split()
    return ' '.join(find_root(word) for word in words)
```

---

## 7️⃣ Map Sum Pairs (LC 677)

```python
class MapSum:
    """Sum of values with given prefix"""
    def __init__(self):
        self.trie = {}
        self.map = {}  # Store previous values for updates
    
    def insert(self, key: str, val: int) -> None:
        delta = val - self.map.get(key, 0)
        self.map[key] = val
        
        node = self.trie
        for char in key:
            node = node.setdefault(char, {'sum': 0})
            node['sum'] += delta
    
    def sum(self, prefix: str) -> int:
        node = self.trie
        for char in prefix:
            if char not in node:
                return 0
            node = node[char]
        return node['sum']
```

---

## 8️⃣ Longest Word in Dictionary (LC 720)

```python
def longest_word(words: list[str]) -> str:
    """Longest word built one character at a time"""
    # Build Trie
    trie = {}
    for word in words:
        node = trie
        for char in word:
            node = node.setdefault(char, {})
        node['$'] = True
    
    result = ""
    
    def dfs(node: dict, path: str):
        nonlocal result
        
        if len(path) > len(result) or (len(path) == len(result) and path < result):
            result = path
        
        for char in sorted(node.keys()):  # Alphabetical order
            if char != '$' and '$' in node[char]:
                dfs(node[char], path + char)
    
    dfs(trie, "")
    return result
```

---

## 9️⃣ Word Squares (LC 425)

```python
def word_squares(words: list[str]) -> list[list[str]]:
    """Build word squares from word list"""
    n = len(words[0])
    
    # Build prefix map
    prefix_map = {}
    for word in words:
        for i in range(n):
            prefix = word[:i]
            prefix_map.setdefault(prefix, []).append(word)
    
    result = []
    
    def backtrack(square: list[str]):
        if len(square) == n:
            result.append(square[:])
            return
        
        idx = len(square)
        # Build prefix from column at position idx
        prefix = ''.join(word[idx] for word in square)
        
        for word in prefix_map.get(prefix, []):
            square.append(word)
            backtrack(square)
            square.pop()
    
    for word in words:
        backtrack([word])
    
    return result
```

---

## 🔟 XOR Trie (Binary Trie)

### Maximum XOR of Two Numbers (LC 421)

```python
def find_maximum_xor(nums: list[int]) -> int:
    """Find maximum XOR of two numbers"""
    # Build binary Trie
    trie = {}
    max_bits = max(nums).bit_length()
    
    for num in nums:
        node = trie
        for i in range(max_bits - 1, -1, -1):
            bit = (num >> i) & 1
            node = node.setdefault(bit, {})
    
    max_xor = 0
    
    for num in nums:
        node = trie
        curr_xor = 0
        
        for i in range(max_bits - 1, -1, -1):
            bit = (num >> i) & 1
            opposite = 1 - bit
            
            if opposite in node:
                curr_xor |= (1 << i)
                node = node[opposite]
            else:
                node = node[bit]
        
        max_xor = max(max_xor, curr_xor)
    
    return max_xor
```

### Maximum XOR With Element (LC 1707)

```python
def maximize_xor(nums: list[int], queries: list[list[int]]) -> list[int]:
    """Max XOR with element <= mi"""
    nums.sort()
    indexed_queries = sorted(enumerate(queries), key=lambda x: x[1][1])
    
    trie = {}
    result = [-1] * len(queries)
    idx = 0
    
    for query_idx, (xi, mi) in indexed_queries:
        # Add all nums <= mi to trie
        while idx < len(nums) and nums[idx] <= mi:
            node = trie
            for i in range(29, -1, -1):
                bit = (nums[idx] >> i) & 1
                node = node.setdefault(bit, {})
            idx += 1
        
        if not trie:
            continue
        
        # Find max XOR
        node = trie
        max_xor = 0
        
        for i in range(29, -1, -1):
            bit = (xi >> i) & 1
            opposite = 1 - bit
            
            if opposite in node:
                max_xor |= (1 << i)
                node = node[opposite]
            else:
                node = node[bit]
        
        result[query_idx] = max_xor
    
    return result
```

---

## 📊 Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| Insert | O(m) | O(m) |
| Search | O(m) | O(1) |
| StartsWith | O(m) | O(1) |
| Delete | O(m) | O(1) |

m = length of word/prefix

---

## 📝 Related Problems

| Problem | LC # | Pattern |
|---------|------|---------|
| Implement Trie | 208 | Basic Trie |
| Add and Search Word | 211 | Wildcard Search |
| Word Search II | 212 | Trie + DFS |
| Replace Words | 648 | Prefix Match |
| Map Sum Pairs | 677 | Prefix Sum |
| Design Autocomplete | 642 | Ranking + Trie |
| Longest Word in Dictionary | 720 | Build Word |
| Word Squares | 425 | Prefix + Backtrack |
| Maximum XOR | 421 | Binary Trie |
| Stream of Characters | 1032 | Suffix Trie |
| Palindrome Pairs | 336 | Trie |

---

*Last Updated: 2024*
