---
title: "Suffix Array & Suffix Tree"
weight: 17
---

# Suffix Array & Suffix Tree

---
title: Suffix Array and Suffix Tree
topic: Data Structures
difficulty: Hard
tags: [suffix-array, suffix-tree, string-algorithms, pattern-matching, interview-advanced]
status: complete
---

## 1. Summary / Quick Reference

### Suffix Array
| Property | Value |
|----------|-------|
| Build (naive) | O(n² log n) |
| Build (optimized) | O(n log n) or O(n) |
| Space | O(n) |
| Pattern Search | O(m log n + occ) |
| Use Case | String searching, LCP queries |

### Suffix Tree
| Property | Value |
|----------|-------|
| Build (Ukkonen) | O(n) |
| Space | O(n) - but large constant |
| Pattern Search | O(m + occ) |
| Use Case | Complex string operations |

---

## 2. Conceptual Foundation

### What is a Suffix?
A suffix of string S is any substring that ends at the last character.
```
S = "banana"
Suffixes: "banana", "anana", "nana", "ana", "na", "a"
```

### Suffix Array
A sorted array of all suffixes (represented by starting indices).

```
S = "banana$"  ($ is sentinel, lexicographically smallest)

Index  Suffix     Sorted
0      banana$    6: $
1      anana$     5: a$
2      nana$      3: ana$
3      ana$       1: anana$
4      na$        0: banana$
5      a$         4: na$
6      $          2: nana$

Suffix Array: [6, 5, 3, 1, 0, 4, 2]
```

### Suffix Tree
A compressed trie of all suffixes with edge labels.

```
S = "banana$"

                root
               / | \
              /  |  \
            a$  b   n
            |   |   |
           na  an  a$
           |   |   |
          na  an  (leaf)
          |   |
         $   a$
         |   |
       (leaf)(leaf)
```

---

## 3. Suffix Array Implementation

### Naive O(n² log n) Construction

```python
from typing import List, Tuple

def suffix_array_naive(s: str) -> List[int]:
    """
    Build suffix array using naive sorting.
    
    Time: O(n² log n) - n log n comparisons, each O(n)
    Space: O(n)
    """
    n = len(s)
    # Create list of (suffix, starting_index)
    suffixes = [(s[i:], i) for i in range(n)]
    # Sort by suffix
    suffixes.sort()
    # Return indices only
    return [idx for _, idx in suffixes]


# Example
s = "banana"
sa = suffix_array_naive(s)
print(f"Suffix Array: {sa}")  # [5, 3, 1, 0, 4, 2]
# Suffixes in order: a, ana, anana, banana, na, nana
```

### O(n log n) Construction with Prefix Doubling

```python
def suffix_array_log(s: str) -> List[int]:
    """
    Build suffix array using prefix doubling (Manber-Myers).
    
    Idea: Sort by first 2^k characters, doubling k each iteration.
    
    Time: O(n log n)
    Space: O(n)
    """
    n = len(s)
    
    # Initial ranking based on first character
    rank = [ord(c) for c in s]
    sa = list(range(n))
    
    k = 1
    while k < n:
        # Sort by (rank[i], rank[i + k])
        # Use (-1) for positions beyond string
        def key(i):
            return (rank[i], rank[i + k] if i + k < n else -1)
        
        sa.sort(key=key)
        
        # Update ranks
        new_rank = [0] * n
        new_rank[sa[0]] = 0
        
        for i in range(1, n):
            prev_key = (rank[sa[i - 1]], rank[sa[i - 1] + k] if sa[i - 1] + k < n else -1)
            curr_key = (rank[sa[i]], rank[sa[i] + k] if sa[i] + k < n else -1)
            
            new_rank[sa[i]] = new_rank[sa[i - 1]] + (1 if curr_key != prev_key else 0)
        
        rank = new_rank
        
        # Early termination if all ranks distinct
        if rank[sa[n - 1]] == n - 1:
            break
        
        k *= 2
    
    return sa


# Example
s = "banana"
sa = suffix_array_log(s)
print(f"Suffix Array: {sa}")  # [5, 3, 1, 0, 4, 2]
```

### O(n log n) with Counting Sort (Faster)

```python
def suffix_array_fast(s: str) -> List[int]:
    """
    Build suffix array using radix sort for O(n log n).
    
    Uses counting sort instead of comparison sort.
    """
    n = len(s)
    
    # Count sort helper
    def counting_sort(sa: List[int], rank: List[int], k: int) -> List[int]:
        count = {}
        for i in range(n):
            key = rank[sa[i] + k] if sa[i] + k < n else -1
            count[key] = count.get(key, 0) + 1
        
        # Cumulative count
        prev = 0
        sorted_keys = sorted(count.keys())
        for key in sorted_keys:
            count[key], prev = prev, prev + count[key]
        
        # Build result
        result = [0] * n
        for i in range(n):
            key = rank[sa[i] + k] if sa[i] + k < n else -1
            result[count[key]] = sa[i]
            count[key] += 1
        
        return result
    
    # Initialize
    rank = [ord(c) for c in s]
    sa = sorted(range(n), key=lambda i: s[i])
    
    k = 1
    while k < n:
        # Radix sort by second key, then first key
        sa = counting_sort(sa, rank, k)
        sa = counting_sort(sa, rank, 0)
        
        # Update ranks
        new_rank = [0] * n
        new_rank[sa[0]] = 0
        
        for i in range(1, n):
            prev_key = (rank[sa[i - 1]], rank[sa[i - 1] + k] if sa[i - 1] + k < n else -1)
            curr_key = (rank[sa[i]], rank[sa[i] + k] if sa[i] + k < n else -1)
            new_rank[sa[i]] = new_rank[sa[i - 1]] + (1 if curr_key != prev_key else 0)
        
        rank = new_rank
        
        if rank[sa[n - 1]] == n - 1:
            break
        
        k *= 2
    
    return sa
```

---

## 4. LCP Array

### What is LCP Array?
Longest Common Prefix array stores the length of LCP between consecutive suffixes in the suffix array.

```
S = "banana"
SA = [5, 3, 1, 0, 4, 2]

Suffixes in SA order:
a       (SA[0] = 5)
ana     (SA[1] = 3)
anana   (SA[2] = 1)
banana  (SA[3] = 0)
na      (SA[4] = 4)
nana    (SA[5] = 2)

LCP between consecutive:
LCP[0] = 0 (undefined, or LCP of first suffix)
LCP[1] = 1 (LCP of "a" and "ana" = "a")
LCP[2] = 3 (LCP of "ana" and "anana" = "ana")
LCP[3] = 0 (LCP of "anana" and "banana" = "")
LCP[4] = 0 (LCP of "banana" and "na" = "")
LCP[5] = 2 (LCP of "na" and "nana" = "na")
```

### Kasai's Algorithm for LCP

```python
def build_lcp_array(s: str, sa: List[int]) -> List[int]:
    """
    Build LCP array using Kasai's algorithm.
    
    Time: O(n)
    Space: O(n)
    
    Key insight: LCP[rank[i]] >= LCP[rank[i-1]] - 1
    """
    n = len(s)
    
    # Build rank array (inverse of SA)
    rank = [0] * n
    for i in range(n):
        rank[sa[i]] = i
    
    lcp = [0] * n
    k = 0  # Current LCP length
    
    for i in range(n):
        if rank[i] == 0:
            k = 0
            continue
        
        # Previous suffix in SA order
        j = sa[rank[i] - 1]
        
        # Extend LCP
        while i + k < n and j + k < n and s[i + k] == s[j + k]:
            k += 1
        
        lcp[rank[i]] = k
        
        # Key: next LCP is at least k - 1
        if k > 0:
            k -= 1
    
    return lcp


# Example
s = "banana"
sa = suffix_array_fast(s)
lcp = build_lcp_array(s, sa)
print(f"SA: {sa}")    # [5, 3, 1, 0, 4, 2]
print(f"LCP: {lcp}")  # [0, 1, 3, 0, 0, 2]
```

---

## 5. Applications of Suffix Array

### 5.1 Pattern Searching

```python
def search_pattern(text: str, pattern: str, sa: List[int]) -> List[int]:
    """
    Search for pattern in text using suffix array.
    
    Time: O(m log n + occ) where m = pattern length, occ = occurrences
    
    Use binary search to find range of suffixes starting with pattern.
    """
    n = len(text)
    m = len(pattern)
    
    # Binary search for left bound
    def lower_bound() -> int:
        lo, hi = 0, n
        while lo < hi:
            mid = (lo + hi) // 2
            suffix = text[sa[mid]:sa[mid] + m]
            if suffix < pattern:
                lo = mid + 1
            else:
                hi = mid
        return lo
    
    # Binary search for right bound
    def upper_bound() -> int:
        lo, hi = 0, n
        while lo < hi:
            mid = (lo + hi) // 2
            suffix = text[sa[mid]:sa[mid] + m]
            if suffix <= pattern:
                lo = mid + 1
            else:
                hi = mid
        return lo
    
    left = lower_bound()
    right = upper_bound()
    
    # Return all occurrences
    return sorted(sa[left:right])


# Example
text = "banana"
sa = suffix_array_fast(text)
occurrences = search_pattern(text, "ana", sa)
print(f"'ana' found at: {occurrences}")  # [1, 3]
```

### 5.2 Longest Repeated Substring

```python
def longest_repeated_substring(s: str) -> str:
    """
    Find longest substring that appears at least twice.
    
    Time: O(n log n) for SA + O(n) for LCP = O(n log n)
    
    Answer is the suffix pair with maximum LCP.
    """
    if not s:
        return ""
    
    sa = suffix_array_fast(s)
    lcp = build_lcp_array(s, sa)
    
    max_lcp = max(lcp)
    if max_lcp == 0:
        return ""
    
    # Find index with max LCP
    max_idx = lcp.index(max_lcp)
    return s[sa[max_idx]:sa[max_idx] + max_lcp]


# Example
print(longest_repeated_substring("banana"))  # "ana"
print(longest_repeated_substring("abcabc"))  # "abc"
```

### 5.3 Number of Distinct Substrings

```python
def count_distinct_substrings(s: str) -> int:
    """
    Count number of distinct substrings.
    
    Total substrings = n(n+1)/2
    Duplicate substrings = sum of LCP values
    
    Time: O(n log n)
    """
    n = len(s)
    if n == 0:
        return 0
    
    sa = suffix_array_fast(s)
    lcp = build_lcp_array(s, sa)
    
    # Total possible substrings
    total = n * (n + 1) // 2
    
    # Subtract duplicates (sum of LCP)
    duplicates = sum(lcp)
    
    return total - duplicates


# Example
print(count_distinct_substrings("banana"))  # 15
# All distinct: b, a, n, ba, an, na, ban, ana, nan, bana, anan, nana, banan, anana, banana
```

### 5.4 Longest Common Substring of Two Strings

```python
def longest_common_substring(s1: str, s2: str) -> str:
    """
    Find longest common substring of two strings.
    
    Approach: Concatenate s1 + '#' + s2, find max LCP between
    suffixes from different strings.
    
    Time: O((n + m) log (n + m))
    """
    n1, n2 = len(s1), len(s2)
    
    # Concatenate with separator
    combined = s1 + '#' + s2
    n = len(combined)
    
    sa = suffix_array_fast(combined)
    lcp = build_lcp_array(combined, sa)
    
    max_len = 0
    result_idx = -1
    
    for i in range(1, n):
        # Check if consecutive suffixes are from different strings
        pos1, pos2 = sa[i - 1], sa[i]
        
        # One from s1, one from s2
        from_s1_prev = pos1 < n1
        from_s1_curr = pos2 < n1
        
        if from_s1_prev != from_s1_curr:
            if lcp[i] > max_len:
                max_len = lcp[i]
                result_idx = sa[i]
    
    if result_idx == -1:
        return ""
    
    return combined[result_idx:result_idx + max_len]


# Example
print(longest_common_substring("abcdef", "zbcdf"))  # "bcd"
```

---

## 6. Suffix Tree

### Ukkonen's Algorithm (Simplified Concept)

```python
class SuffixTreeNode:
    """Node in suffix tree."""
    
    def __init__(self):
        self.children = {}  # char -> node
        self.suffix_link = None
        self.start = -1     # Start index of edge label
        self.end = None     # End index (or pointer for leaf)
        self.suffix_index = -1  # For leaves, the suffix starting index


class SuffixTree:
    """
    Suffix Tree implementation (simplified for understanding).
    
    For production, use Ukkonen's O(n) algorithm.
    """
    
    def __init__(self, text: str):
        self.text = text + '$'  # Add terminal
        self.n = len(self.text)
        self.root = SuffixTreeNode()
        self._build_naive()
    
    def _build_naive(self) -> None:
        """Build suffix tree naively O(n²)."""
        for i in range(self.n):
            self._insert_suffix(i)
    
    def _insert_suffix(self, start: int) -> None:
        """Insert suffix starting at 'start'."""
        node = self.root
        
        for i in range(start, self.n):
            char = self.text[i]
            
            if char not in node.children:
                # Create new leaf
                leaf = SuffixTreeNode()
                leaf.start = i
                leaf.end = self.n - 1
                leaf.suffix_index = start
                node.children[char] = leaf
                return
            
            child = node.children[char]
            
            # Walk down the edge
            j = child.start
            while j <= child.end and i < self.n and self.text[j] == self.text[i]:
                j += 1
                i += 1
            
            if j > child.end:
                # Entire edge matched, continue from child
                node = child
                i -= 1  # Adjust for loop increment
            else:
                # Split edge
                split = SuffixTreeNode()
                split.start = child.start
                split.end = j - 1
                
                child.start = j
                split.children[self.text[j]] = child
                
                # New leaf
                leaf = SuffixTreeNode()
                leaf.start = i
                leaf.end = self.n - 1
                leaf.suffix_index = start
                split.children[self.text[i]] = leaf
                
                node.children[char] = split
                return
    
    def search(self, pattern: str) -> List[int]:
        """
        Search for pattern in text.
        
        Time: O(m + occ) where m = pattern length
        """
        node = self.root
        i = 0
        
        while i < len(pattern):
            char = pattern[i]
            
            if char not in node.children:
                return []
            
            child = node.children[char]
            
            # Match characters on edge
            j = child.start
            while j <= child.end and i < len(pattern):
                if self.text[j] != pattern[i]:
                    return []
                j += 1
                i += 1
            
            if i == len(pattern):
                # Pattern found, collect all leaf indices
                return self._collect_indices(child)
            
            # Continue from child
            node = child
        
        return []
    
    def _collect_indices(self, node: SuffixTreeNode) -> List[int]:
        """Collect all suffix indices under this node."""
        result = []
        
        if node.suffix_index != -1:
            result.append(node.suffix_index)
        
        for child in node.children.values():
            result.extend(self._collect_indices(child))
        
        return sorted(result)


# Example
st = SuffixTree("banana")
print(f"Pattern 'ana' found at: {st.search('ana')}")  # [1, 3]
print(f"Pattern 'nan' found at: {st.search('nan')}")  # [2]
print(f"Pattern 'xyz' found at: {st.search('xyz')}")  # []
```

---

## 7. Suffix Automaton

```python
class SuffixAutomaton:
    """
    Suffix Automaton - accepts all suffixes of a string.
    
    Time: O(n) construction
    Space: O(n) states
    
    Powerful for:
        - Pattern matching
        - Counting distinct substrings
        - Finding shortest string not in text
    """
    
    class State:
        def __init__(self):
            self.length = 0
            self.link = -1
            self.next = {}
    
    def __init__(self, s: str = ""):
        self.states = [self.State()]
        self.last = 0
        
        for char in s:
            self._extend(char)
    
    def _extend(self, char: str) -> None:
        """Extend automaton with new character."""
        cur = len(self.states)
        self.states.append(self.State())
        self.states[cur].length = self.states[self.last].length + 1
        
        p = self.last
        
        while p != -1 and char not in self.states[p].next:
            self.states[p].next[char] = cur
            p = self.states[p].link
        
        if p == -1:
            self.states[cur].link = 0
        else:
            q = self.states[p].next[char]
            
            if self.states[p].length + 1 == self.states[q].length:
                self.states[cur].link = q
            else:
                # Clone state
                clone = len(self.states)
                self.states.append(self.State())
                self.states[clone].length = self.states[p].length + 1
                self.states[clone].link = self.states[q].link
                self.states[clone].next = self.states[q].next.copy()
                
                while p != -1 and self.states[p].next.get(char) == q:
                    self.states[p].next[char] = clone
                    p = self.states[p].link
                
                self.states[q].link = clone
                self.states[cur].link = clone
        
        self.last = cur
    
    def count_distinct_substrings(self) -> int:
        """Count all distinct substrings (including empty)."""
        total = 0
        for state in self.states:
            if state.link != -1:
                total += state.length - self.states[state.link].length
        return total
    
    def contains(self, pattern: str) -> bool:
        """Check if pattern is a substring."""
        cur = 0
        for char in pattern:
            if char not in self.states[cur].next:
                return False
            cur = self.states[cur].next[char]
        return True


# Example
sa = SuffixAutomaton("banana")
print(f"Contains 'ana': {sa.contains('ana')}")  # True
print(f"Contains 'xyz': {sa.contains('xyz')}")  # False
print(f"Distinct substrings: {sa.count_distinct_substrings()}")  # 15
```

---

## 8. LeetCode Problems

| # | Problem | Difficulty | Technique |
|---|---------|------------|-----------|
| 5 | Longest Palindromic Substring | Medium | Suffix Array/Manacher |
| 28 | Implement strStr() | Easy | Pattern matching |
| 214 | Shortest Palindrome | Hard | KMP/Suffix Array |
| 459 | Repeated Substring Pattern | Easy | Suffix Array |
| 718 | Maximum Length of Repeated Subarray | Medium | SA + LCP |
| 1044 | Longest Duplicate Substring | Hard | SA + Binary Search |
| 1062 | Longest Repeating Substring | Medium | SA + LCP |
| 1316 | Distinct Echo Substrings | Hard | SA + LCP |
| 1408 | String Matching in an Array | Easy | SA/Trie |

---

## 9. Interview Tips

### When to Use Suffix Array vs Suffix Tree

| Scenario | Use |
|----------|-----|
| Space is critical | Suffix Array |
| Need O(m) search | Suffix Tree |
| Multiple patterns | Suffix Tree |
| LCP queries | Suffix Array + LCP |
| Simpler code | Suffix Array |

### Key Points to Remember
1. Suffix Array can be built in O(n log n) or O(n)
2. LCP array enables many powerful operations
3. Suffix Tree has better theoretical bounds but larger constants
4. Suffix Automaton is powerful for counting problems

### Common Mistakes
1. Forgetting sentinel character for uniqueness
2. Off-by-one errors in LCP computation
3. Not handling empty strings
4. Confusing 0-indexed vs 1-indexed

---

## 10. References

1. Manber & Myers - "Suffix Arrays: A New Method" (1990)
2. Kasai et al. - "Linear-Time LCP Array Construction"
3. Ukkonen - "On-line construction of suffix trees"
4. CP-Algorithms: https://cp-algorithms.com/string/suffix-array.html
