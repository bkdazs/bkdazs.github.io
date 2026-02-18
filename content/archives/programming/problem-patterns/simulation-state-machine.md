---
title: "Simulation & State Machine Patterns"
topic: "Problem Patterns"
difficulty: "Medium"
tags: ["simulation", "state-machine", "implementation"]
status: "complete"
weight: 25
---

# Simulation & State Machine Patterns

## 📚 Overview

Simulation problems require careful implementation of rules. State machines model systems with distinct states and transitions.

---

## 1️⃣ Game Simulation

### Game of Life (LC 289)

```python
def game_of_life(board: list[list[int]]) -> None:
    """In-place simulation using bit encoding"""
    rows, cols = len(board), len(board[0])
    
    # Encode: bit 0 = current state, bit 1 = next state
    for r in range(rows):
        for c in range(cols):
            # Count live neighbors
            live = 0
            for dr in [-1, 0, 1]:
                for dc in [-1, 0, 1]:
                    if dr == dc == 0:
                        continue
                    nr, nc = r + dr, c + dc
                    if 0 <= nr < rows and 0 <= nc < cols:
                        live += board[nr][nc] & 1
            
            # Apply rules
            if board[r][c] & 1:  # Currently alive
                if 2 <= live <= 3:
                    board[r][c] |= 2  # Stays alive
            else:  # Currently dead
                if live == 3:
                    board[r][c] |= 2  # Becomes alive
    
    # Extract next state
    for r in range(rows):
        for c in range(cols):
            board[r][c] >>= 1
```

### Robot Room Cleaner (LC 489)

```python
def clean_room(robot) -> None:
    """DFS cleaning with backtracking"""
    visited = set()
    directions = [(-1, 0), (0, 1), (1, 0), (0, -1)]  # up, right, down, left
    
    def go_back():
        robot.turnRight()
        robot.turnRight()
        robot.move()
        robot.turnRight()
        robot.turnRight()
    
    def dfs(r: int, c: int, d: int):
        visited.add((r, c))
        robot.clean()
        
        for i in range(4):
            new_d = (d + i) % 4
            dr, dc = directions[new_d]
            nr, nc = r + dr, c + dc
            
            if (nr, nc) not in visited and robot.move():
                dfs(nr, nc, new_d)
                go_back()
            
            robot.turnRight()
    
    dfs(0, 0, 0)
```

---

## 2️⃣ Robot Path Simulation

### Walking Robot Simulation (LC 874)

```python
def robot_sim(commands: list[int], obstacles: list[list[int]]) -> int:
    """Simulate robot movement, return max squared distance"""
    obstacle_set = set(map(tuple, obstacles))
    
    # Directions: North, East, South, West
    dx = [0, 1, 0, -1]
    dy = [1, 0, -1, 0]
    
    x = y = 0
    direction = 0  # North
    max_dist = 0
    
    for cmd in commands:
        if cmd == -2:  # Turn left
            direction = (direction - 1) % 4
        elif cmd == -1:  # Turn right
            direction = (direction + 1) % 4
        else:  # Move forward
            for _ in range(cmd):
                nx, ny = x + dx[direction], y + dy[direction]
                if (nx, ny) not in obstacle_set:
                    x, y = nx, ny
                    max_dist = max(max_dist, x*x + y*y)
                else:
                    break
    
    return max_dist
```

### Robot Bounded in Circle (LC 1041)

```python
def is_robot_bounded(instructions: str) -> bool:
    """Check if robot returns to origin or changes direction"""
    x = y = 0
    direction = 0  # 0=N, 1=E, 2=S, 3=W
    dx = [0, 1, 0, -1]
    dy = [1, 0, -1, 0]
    
    for cmd in instructions:
        if cmd == 'G':
            x += dx[direction]
            y += dy[direction]
        elif cmd == 'L':
            direction = (direction - 1) % 4
        else:  # R
            direction = (direction + 1) % 4
    
    # Bounded if returns to origin OR direction changed
    return (x == 0 and y == 0) or direction != 0
```

---

## 3️⃣ Spiral Traversal

### Spiral Matrix (LC 54)

```python
def spiral_order(matrix: list[list[int]]) -> list[int]:
    """Spiral traversal of matrix"""
    if not matrix:
        return []
    
    result = []
    top, bottom = 0, len(matrix) - 1
    left, right = 0, len(matrix[0]) - 1
    
    while top <= bottom and left <= right:
        # Right
        for c in range(left, right + 1):
            result.append(matrix[top][c])
        top += 1
        
        # Down
        for r in range(top, bottom + 1):
            result.append(matrix[r][right])
        right -= 1
        
        if top <= bottom:
            # Left
            for c in range(right, left - 1, -1):
                result.append(matrix[bottom][c])
            bottom -= 1
        
        if left <= right:
            # Up
            for r in range(bottom, top - 1, -1):
                result.append(matrix[r][left])
            left += 1
    
    return result
```

### Spiral Matrix II (LC 59)

```python
def generate_matrix(n: int) -> list[list[int]]:
    """Generate n x n matrix with spiral values"""
    matrix = [[0] * n for _ in range(n)]
    
    top, bottom = 0, n - 1
    left, right = 0, n - 1
    num = 1
    
    while top <= bottom and left <= right:
        for c in range(left, right + 1):
            matrix[top][c] = num
            num += 1
        top += 1
        
        for r in range(top, bottom + 1):
            matrix[r][right] = num
            num += 1
        right -= 1
        
        for c in range(right, left - 1, -1):
            matrix[bottom][c] = num
            num += 1
        bottom -= 1
        
        for r in range(bottom, top - 1, -1):
            matrix[r][left] = num
            num += 1
        left += 1
    
    return matrix
```

---

## 4️⃣ State Machine Pattern

### Valid Number (LC 65)

```python
def is_number(s: str) -> bool:
    """State machine for validating number"""
    # States: start, sign, integer, dot, decimal, exp, exp_sign, exp_integer, end
    
    state = 'start'
    transitions = {
        'start': {'sign': 'sign', 'digit': 'integer', 'dot': 'dot'},
        'sign': {'digit': 'integer', 'dot': 'dot'},
        'integer': {'digit': 'integer', 'dot': 'decimal', 'e': 'exp'},
        'dot': {'digit': 'decimal'},
        'decimal': {'digit': 'decimal', 'e': 'exp'},
        'exp': {'sign': 'exp_sign', 'digit': 'exp_integer'},
        'exp_sign': {'digit': 'exp_integer'},
        'exp_integer': {'digit': 'exp_integer'}
    }
    
    final_states = {'integer', 'decimal', 'exp_integer'}
    
    def get_type(c: str) -> str:
        if c in '+-':
            return 'sign'
        if c.isdigit():
            return 'digit'
        if c == '.':
            return 'dot'
        if c in 'eE':
            return 'e'
        return 'unknown'
    
    for c in s:
        char_type = get_type(c)
        if char_type == 'unknown':
            return False
        if char_type not in transitions.get(state, {}):
            return False
        state = transitions[state][char_type]
    
    return state in final_states
```

### UTF-8 Validation (LC 393)

```python
def valid_utf8(data: list[int]) -> bool:
    """Validate UTF-8 encoding"""
    n_bytes = 0  # Number of bytes remaining
    
    for num in data:
        num = num & 0xFF  # Get last 8 bits
        
        if n_bytes == 0:
            # Determine number of bytes
            if (num >> 7) == 0:      # 0xxxxxxx
                n_bytes = 0
            elif (num >> 5) == 0b110:  # 110xxxxx
                n_bytes = 1
            elif (num >> 4) == 0b1110:  # 1110xxxx
                n_bytes = 2
            elif (num >> 3) == 0b11110:  # 11110xxx
                n_bytes = 3
            else:
                return False
        else:
            # Continuation byte must be 10xxxxxx
            if (num >> 6) != 0b10:
                return False
            n_bytes -= 1
    
    return n_bytes == 0
```

---

## 5️⃣ Text Justification (LC 68)

```python
def full_justify(words: list[str], max_width: int) -> list[str]:
    """Full text justification"""
    result = []
    line = []
    line_len = 0
    
    for word in words:
        # Check if word fits in current line
        if line_len + len(word) + len(line) > max_width:
            # Justify current line
            spaces = max_width - line_len
            
            if len(line) == 1:
                result.append(line[0] + ' ' * spaces)
            else:
                gaps = len(line) - 1
                space_per_gap = spaces // gaps
                extra = spaces % gaps
                
                justified = ""
                for i, w in enumerate(line[:-1]):
                    justified += w + ' ' * (space_per_gap + (1 if i < extra else 0))
                justified += line[-1]
                result.append(justified)
            
            line = []
            line_len = 0
        
        line.append(word)
        line_len += len(word)
    
    # Last line - left justified
    last_line = ' '.join(line)
    last_line += ' ' * (max_width - len(last_line))
    result.append(last_line)
    
    return result
```

---

## 6️⃣ Iterator Design

### Flatten 2D Vector (LC 251)

```python
class Vector2D:
    def __init__(self, vec: list[list[int]]):
        self.vec = vec
        self.outer = 0
        self.inner = 0
        self._advance()
    
    def _advance(self):
        """Move to next valid position"""
        while self.outer < len(self.vec) and self.inner >= len(self.vec[self.outer]):
            self.outer += 1
            self.inner = 0
    
    def next(self) -> int:
        result = self.vec[self.outer][self.inner]
        self.inner += 1
        self._advance()
        return result
    
    def hasNext(self) -> bool:
        return self.outer < len(self.vec)
```

### Peeking Iterator (LC 284)

```python
class PeekingIterator:
    def __init__(self, iterator):
        self.iterator = iterator
        self.peeked = None
        self.has_peeked = False
    
    def peek(self) -> int:
        if not self.has_peeked:
            self.peeked = self.iterator.next()
            self.has_peeked = True
        return self.peeked
    
    def next(self) -> int:
        if self.has_peeked:
            self.has_peeked = False
            return self.peeked
        return self.iterator.next()
    
    def hasNext(self) -> bool:
        return self.has_peeked or self.iterator.hasNext()
```

### Zigzag Iterator (LC 281)

```python
class ZigzagIterator:
    def __init__(self, v1: list[int], v2: list[int]):
        from collections import deque
        
        self.queue = deque()
        if v1:
            self.queue.append(iter(v1))
        if v2:
            self.queue.append(iter(v2))
    
    def next(self) -> int:
        iterator = self.queue.popleft()
        value = next(iterator)
        
        if self._has_more(iterator):
            self.queue.append(iterator)
        
        return value
    
    def _has_more(self, iterator):
        try:
            self.queue.append(iter([next(iterator)]))
            return True
        except StopIteration:
            return False
    
    def hasNext(self) -> bool:
        return len(self.queue) > 0
```

---

## 7️⃣ Calculator Problems

### Basic Calculator (LC 224)

```python
def calculate(s: str) -> int:
    """Evaluate expression with +, -, (, )"""
    stack = []
    num = 0
    sign = 1
    result = 0
    
    for char in s:
        if char.isdigit():
            num = num * 10 + int(char)
        elif char == '+':
            result += sign * num
            num = 0
            sign = 1
        elif char == '-':
            result += sign * num
            num = 0
            sign = -1
        elif char == '(':
            stack.append(result)
            stack.append(sign)
            result = 0
            sign = 1
        elif char == ')':
            result += sign * num
            num = 0
            result *= stack.pop()  # sign before (
            result += stack.pop()  # result before (
    
    return result + sign * num
```

### Basic Calculator II (LC 227)

```python
def calculate(s: str) -> int:
    """Evaluate with +, -, *, /"""
    stack = []
    num = 0
    sign = '+'
    
    for i, char in enumerate(s):
        if char.isdigit():
            num = num * 10 + int(char)
        
        if char in '+-*/' or i == len(s) - 1:
            if sign == '+':
                stack.append(num)
            elif sign == '-':
                stack.append(-num)
            elif sign == '*':
                stack.append(stack.pop() * num)
            elif sign == '/':
                stack.append(int(stack.pop() / num))  # Truncate toward zero
            
            sign = char
            num = 0
    
    return sum(stack)
```

---

## 8️⃣ Design Tic-Tac-Toe (LC 348)

```python
class TicTacToe:
    def __init__(self, n: int):
        self.n = n
        self.rows = [0] * n
        self.cols = [0] * n
        self.diag = 0
        self.anti_diag = 0
    
    def move(self, row: int, col: int, player: int) -> int:
        """O(1) per move"""
        add = 1 if player == 1 else -1
        n = self.n
        
        self.rows[row] += add
        self.cols[col] += add
        
        if row == col:
            self.diag += add
        
        if row + col == n - 1:
            self.anti_diag += add
        
        if (abs(self.rows[row]) == n or
            abs(self.cols[col]) == n or
            abs(self.diag) == n or
            abs(self.anti_diag) == n):
            return player
        
        return 0
```

---

## 9️⃣ Time-Based Operations

### Time Based Key-Value Store (LC 981)

```python
class TimeMap:
    def __init__(self):
        from collections import defaultdict
        self.store = defaultdict(list)  # key -> [(timestamp, value)]
    
    def set(self, key: str, value: str, timestamp: int) -> None:
        self.store[key].append((timestamp, value))
    
    def get(self, key: str, timestamp: int) -> str:
        if key not in self.store:
            return ""
        
        values = self.store[key]
        
        # Binary search for largest timestamp <= given timestamp
        left, right = 0, len(values) - 1
        result = ""
        
        while left <= right:
            mid = (left + right) // 2
            if values[mid][0] <= timestamp:
                result = values[mid][1]
                left = mid + 1
            else:
                right = mid - 1
        
        return result
```

---

## 🔟 LRU Cache (LC 146)

```python
class LRUCache:
    def __init__(self, capacity: int):
        from collections import OrderedDict
        self.cache = OrderedDict()
        self.capacity = capacity
    
    def get(self, key: int) -> int:
        if key not in self.cache:
            return -1
        
        self.cache.move_to_end(key)
        return self.cache[key]
    
    def put(self, key: int, value: int) -> None:
        if key in self.cache:
            self.cache.move_to_end(key)
        
        self.cache[key] = value
        
        if len(self.cache) > self.capacity:
            self.cache.popitem(last=False)

# Manual implementation with doubly linked list
class Node:
    def __init__(self, key=0, val=0):
        self.key = key
        self.val = val
        self.prev = None
        self.next = None

class LRUCacheManual:
    def __init__(self, capacity: int):
        self.capacity = capacity
        self.cache = {}
        self.head = Node()
        self.tail = Node()
        self.head.next = self.tail
        self.tail.prev = self.head
    
    def _add(self, node):
        node.prev = self.tail.prev
        node.next = self.tail
        self.tail.prev.next = node
        self.tail.prev = node
    
    def _remove(self, node):
        node.prev.next = node.next
        node.next.prev = node.prev
    
    def get(self, key: int) -> int:
        if key not in self.cache:
            return -1
        
        node = self.cache[key]
        self._remove(node)
        self._add(node)
        return node.val
    
    def put(self, key: int, value: int) -> None:
        if key in self.cache:
            self._remove(self.cache[key])
        
        node = Node(key, value)
        self.cache[key] = node
        self._add(node)
        
        if len(self.cache) > self.capacity:
            lru = self.head.next
            self._remove(lru)
            del self.cache[lru.key]
```

---

## 📝 Related Problems

| Problem | LC # | Pattern |
|---------|------|---------|
| Game of Life | 289 | Bit Encoding |
| Spiral Matrix | 54 | Boundary Simulation |
| Spiral Matrix II | 59 | Boundary Fill |
| Robot Bounded | 1041 | Direction State |
| Valid Number | 65 | State Machine |
| UTF-8 Validation | 393 | Byte State |
| Text Justification | 68 | Line Building |
| Basic Calculator | 224 | Stack + State |
| Design Tic-Tac-Toe | 348 | Count State |
| Time Map | 981 | Binary Search |
| LRU Cache | 146 | Doubly Linked List |
| LFU Cache | 460 | Two Maps + DLL |
| Snake Game | 353 | Queue Simulation |

---

*Last Updated: 2024*
