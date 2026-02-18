---
title: "Stacks"
topic: "Data Structures"
difficulty: "Fundamental"
tags: ["stack", "LIFO", "recursion", "expression-evaluation"]
status: "complete"
weight: 5
---

# Stacks

## Short Summary

A **stack** is a Last-In-First-Out (LIFO) data structure supporting push (add to top), pop (remove from top), and peek (view top) operations in O(1) time. Stacks are essential for managing function calls (call stack), expression evaluation, backtracking algorithms, and undo mechanisms. In interviews, stack problems often involve parsing, monotonic patterns, or simulating recursive behavior iteratively.

---

## Why It Matters in Interviews

- **Ubiquitous Pattern:** Bracket matching, expression evaluation, DFS traversal
- **Monotonic Stack:** Powerful technique for next greater/smaller element problems
- **Space-Time Tradeoff:** Often converts O(n²) brute force to O(n) solutions

---

## Formal Definition / Properties / Invariants

### LIFO Principle
```
Push/Pop only at TOP
┌─────┐
│  3  │ ← TOP (most recently added)
├─────┤
│  2  │
├─────┤
│  1  │ ← BOTTOM (first added)
└─────┘

Push(4): Add 4 to top
Pop():   Remove 3 (returns 3)
Peek():  View 3 (no removal)
```

### Stack ADT Operations

| Operation | Description | Time |
|-----------|-------------|------|
| `push(x)` | Add element x to top | O(1) |
| `pop()` | Remove and return top element | O(1) |
| `peek()` / `top()` | Return top without removing | O(1) |
| `isEmpty()` | Check if stack is empty | O(1) |
| `size()` | Return number of elements | O(1) |

### Invariants
- Only top element is accessible
- Elements are removed in reverse order of insertion
- Underflow: pop/peek on empty stack is undefined

---

## Time & Space Complexity

| Implementation | Push | Pop | Peek | Space |
|----------------|------|-----|------|-------|
| Array-based | O(1)* | O(1) | O(1) | O(n) |
| Linked List | O(1) | O(1) | O(1) | O(n) + pointers |

*Amortized O(1) with dynamic array resizing

**When to use which:**
- **Array-based:** Better cache locality, less memory overhead
- **Linked List:** No resize cost, constant worst-case (not amortized)

---

## Typical Operations & Pseudo-Implementation

### Array-Based Stack

```python
from typing import TypeVar, Generic, Optional, List

T = TypeVar('T')

class ArrayStack(Generic[T]):
    """
    Stack implementation using dynamic array.
    
    >>> stack = ArrayStack[int]()
    >>> stack.push(1)
    >>> stack.push(2)
    >>> stack.push(3)
    >>> stack.pop()
    3
    >>> stack.peek()
    2
    >>> len(stack)
    2
    >>> stack.is_empty()
    False
    """
    
    def __init__(self):
        self._data: List[T] = []
    
    def __len__(self) -> int:
        return len(self._data)
    
    def is_empty(self) -> bool:
        return len(self._data) == 0
    
    def push(self, item: T) -> None:
        """Add item to top. O(1) amortized."""
        self._data.append(item)
    
    def pop(self) -> T:
        """Remove and return top item. O(1)"""
        if self.is_empty():
            raise IndexError("Pop from empty stack")
        return self._data.pop()
    
    def peek(self) -> T:
        """Return top item without removing. O(1)"""
        if self.is_empty():
            raise IndexError("Peek from empty stack")
        return self._data[-1]
    
    def __repr__(self) -> str:
        return f"Stack({self._data})"


if __name__ == "__main__":
    import doctest
    doctest.testmod()
```

### Linked List-Based Stack

```python
from typing import TypeVar, Generic, Optional

T = TypeVar('T')

class StackNode(Generic[T]):
    def __init__(self, val: T, next: Optional['StackNode[T]'] = None):
        self.val = val
        self.next = next


class LinkedStack(Generic[T]):
    """
    Stack implementation using singly linked list.
    O(1) worst-case for all operations.
    
    >>> stack = LinkedStack[int]()
    >>> stack.push(1)
    >>> stack.push(2)
    >>> stack.pop()
    2
    >>> stack.peek()
    1
    """
    
    def __init__(self):
        self._top: Optional[StackNode[T]] = None
        self._size: int = 0
    
    def __len__(self) -> int:
        return self._size
    
    def is_empty(self) -> bool:
        return self._top is None
    
    def push(self, item: T) -> None:
        """Add item to top. O(1)"""
        self._top = StackNode(item, self._top)
        self._size += 1
    
    def pop(self) -> T:
        """Remove and return top item. O(1)"""
        if self.is_empty():
            raise IndexError("Pop from empty stack")
        val = self._top.val
        self._top = self._top.next
        self._size -= 1
        return val
    
    def peek(self) -> T:
        """Return top item without removing. O(1)"""
        if self.is_empty():
            raise IndexError("Peek from empty stack")
        return self._top.val


if __name__ == "__main__":
    import doctest
    doctest.testmod()
```

### Python Built-in Stack (using list)

```python
def python_stack_demo():
    """
    Using Python list as stack.
    
    >>> stack = []
    >>> stack.append(1)  # push
    >>> stack.append(2)
    >>> stack.append(3)
    >>> stack.pop()  # pop
    3
    >>> stack[-1]  # peek
    2
    >>> len(stack)  # size
    2
    >>> bool(stack)  # not empty
    True
    """
    pass
```

---

## Key Insights for Correctness

### Stack Call Relationship with Recursion

```python
def factorial_recursive(n: int) -> int:
    """Each call adds frame to call stack."""
    if n <= 1:
        return 1
    return n * factorial_recursive(n - 1)


def factorial_iterative_stack(n: int) -> int:
    """
    Convert recursion to iteration using explicit stack.
    
    >>> factorial_iterative_stack(5)
    120
    """
    stack = []
    result = 1
    
    # Simulate recursive calls
    while n > 1:
        stack.append(n)
        n -= 1
    
    # Simulate returns
    while stack:
        result *= stack.pop()
    
    return result
```

### Stack State Visualization

```
Expression evaluation: 3 + 4 * 2

Using operator precedence with two stacks:

Step 1: Push 3
        nums: [3]
        ops: []

Step 2: Push +
        nums: [3]
        ops: [+]

Step 3: Push 4
        nums: [3, 4]
        ops: [+]

Step 4: Push * (higher precedence, don't evaluate yet)
        nums: [3, 4]
        ops: [+, *]

Step 5: Push 2
        nums: [3, 4, 2]
        ops: [+, *]

Step 6: End of expression, evaluate right to left
        Pop 2, 4, * → 8
        nums: [3, 8]
        ops: [+]
        
        Pop 8, 3, + → 11
        Result: 11
```

---

## Common Implementations

### Valid Parentheses

```python
def is_valid_parentheses(s: str) -> bool:
    """
    Check if parentheses are balanced.
    
    Time: O(n), Space: O(n)
    
    >>> is_valid_parentheses("()")
    True
    >>> is_valid_parentheses("()[]{}")
    True
    >>> is_valid_parentheses("(]")
    False
    >>> is_valid_parentheses("([)]")
    False
    >>> is_valid_parentheses("{[]}")
    True
    """
    stack = []
    mapping = {')': '(', ']': '[', '}': '{'}
    
    for char in s:
        if char in mapping:  # Closing bracket
            if not stack or stack[-1] != mapping[char]:
                return False
            stack.pop()
        else:  # Opening bracket
            stack.append(char)
    
    return len(stack) == 0


if __name__ == "__main__":
    import doctest
    doctest.testmod()
```

### Basic Calculator (with +, -, parentheses)

```python
def calculate(s: str) -> int:
    """
    Evaluate expression with +, -, (, ).
    
    Time: O(n), Space: O(n)
    
    >>> calculate("1 + 1")
    2
    >>> calculate(" 2-1 + 2 ")
    3
    >>> calculate("(1+(4+5+2)-3)+(6+8)")
    23
    """
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
            # Push current result and sign
            stack.append(result)
            stack.append(sign)
            result = 0
            sign = 1
        elif char == ')':
            result += sign * num
            num = 0
            # Pop sign and previous result
            result *= stack.pop()  # sign
            result += stack.pop()  # previous result
    
    return result + sign * num


if __name__ == "__main__":
    import doctest
    doctest.testmod()
```

### Min Stack

```python
class MinStack:
    """
    Stack with O(1) getMin operation.
    
    >>> ms = MinStack()
    >>> ms.push(-2)
    >>> ms.push(0)
    >>> ms.push(-3)
    >>> ms.get_min()
    -3
    >>> ms.pop()
    >>> ms.top()
    0
    >>> ms.get_min()
    -2
    """
    
    def __init__(self):
        self.stack = []
        self.min_stack = []
    
    def push(self, val: int) -> None:
        self.stack.append(val)
        if not self.min_stack or val <= self.min_stack[-1]:
            self.min_stack.append(val)
    
    def pop(self) -> None:
        if self.stack:
            if self.stack[-1] == self.min_stack[-1]:
                self.min_stack.pop()
            self.stack.pop()
    
    def top(self) -> int:
        return self.stack[-1]
    
    def get_min(self) -> int:
        return self.min_stack[-1]


if __name__ == "__main__":
    import doctest
    doctest.testmod()
```

---

## Step-by-Step Worked Example

### Problem: Evaluate Reverse Polish Notation

**Input:** `tokens = ["2", "1", "+", "3", "*"]`
**Output:** `9` (Explanation: ((2 + 1) * 3) = 9)

```
RPN uses postfix notation: operands before operators.
Process left to right, push numbers, apply operators to top 2 elements.

Step-by-step:
tokens = ["2", "1", "+", "3", "*"]

Token "2": Push 2
Stack: [2]

Token "1": Push 1
Stack: [2, 1]

Token "+": Pop 1, Pop 2, Compute 2+1=3, Push 3
Stack: [3]

Token "3": Push 3
Stack: [3, 3]

Token "*": Pop 3, Pop 3, Compute 3*3=9, Push 9
Stack: [9]

Final Result: 9
```

---

## Variants & Extensions

### Two-Stack Queue

```python
class QueueUsingStacks:
    """
    Implement queue using two stacks.
    Amortized O(1) for all operations.
    
    >>> q = QueueUsingStacks()
    >>> q.push(1)
    >>> q.push(2)
    >>> q.peek()
    1
    >>> q.pop()
    1
    >>> q.empty()
    False
    """
    
    def __init__(self):
        self.in_stack = []   # For push
        self.out_stack = []  # For pop/peek
    
    def push(self, x: int) -> None:
        """Push to back of queue. O(1)"""
        self.in_stack.append(x)
    
    def pop(self) -> int:
        """Remove from front. Amortized O(1)"""
        self._transfer()
        return self.out_stack.pop()
    
    def peek(self) -> int:
        """View front. Amortized O(1)"""
        self._transfer()
        return self.out_stack[-1]
    
    def empty(self) -> bool:
        return not self.in_stack and not self.out_stack
    
    def _transfer(self) -> None:
        """Transfer from in_stack to out_stack if out is empty."""
        if not self.out_stack:
            while self.in_stack:
                self.out_stack.append(self.in_stack.pop())


if __name__ == "__main__":
    import doctest
    doctest.testmod()
```

### Stack with Max Operation

```python
class MaxStack:
    """
    Stack supporting O(1) max operation.
    Similar to MinStack but tracks maximum.
    
    >>> ms = MaxStack()
    >>> ms.push(5)
    >>> ms.push(1)
    >>> ms.push(5)
    >>> ms.top()
    5
    >>> ms.pop_max()
    5
    >>> ms.top()
    1
    >>> ms.peek_max()
    5
    >>> ms.pop()
    1
    >>> ms.top()
    5
    """
    
    def __init__(self):
        self.stack = []
        self.max_stack = []
    
    def push(self, x: int) -> None:
        self.stack.append(x)
        if not self.max_stack or x >= self.max_stack[-1]:
            self.max_stack.append(x)
        else:
            self.max_stack.append(self.max_stack[-1])
    
    def pop(self) -> int:
        self.max_stack.pop()
        return self.stack.pop()
    
    def top(self) -> int:
        return self.stack[-1]
    
    def peek_max(self) -> int:
        return self.max_stack[-1]
    
    def pop_max(self) -> int:
        """Pop maximum element. O(n) in worst case."""
        max_val = self.max_stack[-1]
        buffer = []
        while self.stack[-1] != max_val:
            buffer.append(self.pop())
        self.pop()  # Remove max
        while buffer:
            self.push(buffer.pop())
        return max_val


if __name__ == "__main__":
    import doctest
    doctest.testmod()
```

---

## Common Interview Problems

| # | Problem | Difficulty | Approach | Time | LeetCode |
|---|---------|------------|----------|------|----------|
| 1 | Valid Parentheses | Easy | Stack matching | O(n) | [#20](https://leetcode.com/problems/valid-parentheses/) |
| 2 | Min Stack | Medium | Auxiliary stack | O(1) | [#155](https://leetcode.com/problems/min-stack/) |
| 3 | Implement Queue using Stacks | Easy | Two stacks | O(1)* | [#232](https://leetcode.com/problems/implement-queue-using-stacks/) |
| 4 | Evaluate RPN | Medium | Operator stack | O(n) | [#150](https://leetcode.com/problems/evaluate-reverse-polish-notation/) |
| 5 | Daily Temperatures | Medium | Monotonic stack | O(n) | [#739](https://leetcode.com/problems/daily-temperatures/) |
| 6 | Next Greater Element I | Easy | Monotonic stack + Hash | O(n) | [#496](https://leetcode.com/problems/next-greater-element-i/) |
| 7 | Decode String | Medium | Nested stack | O(n) | [#394](https://leetcode.com/problems/decode-string/) |
| 8 | Basic Calculator | Hard | Stack + sign | O(n) | [#224](https://leetcode.com/problems/basic-calculator/) |
| 9 | Largest Rectangle in Histogram | Hard | Monotonic stack | O(n) | [#84](https://leetcode.com/problems/largest-rectangle-in-histogram/) |
| 10 | Trapping Rain Water | Hard | Monotonic stack | O(n) | [#42](https://leetcode.com/problems/trapping-rain-water/) |

---

## Fully Worked Sample Problem

### Problem: Daily Temperatures (Monotonic Stack)

**Problem Statement:** Given an array of integers `temperatures`, return an array `answer` such that `answer[i]` is the number of days you have to wait after the ith day to get a warmer temperature.

**Difficulty:** Medium | [LeetCode #739](https://leetcode.com/problems/daily-temperatures/)

```python
from typing import List

def daily_temperatures(temperatures: List[int]) -> List[int]:
    """
    Find days until warmer temperature using monotonic decreasing stack.
    
    Key insight: Stack stores indices of temperatures waiting for warmer day.
    When we find a warmer temperature, pop all smaller temperatures.
    
    Time Complexity: O(n) - each element pushed and popped at most once
    Space Complexity: O(n) - stack can hold all elements
    
    >>> daily_temperatures([73, 74, 75, 71, 69, 72, 76, 73])
    [1, 1, 4, 2, 1, 1, 0, 0]
    >>> daily_temperatures([30, 40, 50, 60])
    [1, 1, 1, 0]
    >>> daily_temperatures([30, 60, 90])
    [1, 1, 0]
    """
    n = len(temperatures)
    result = [0] * n
    stack = []  # Store indices
    
    for i in range(n):
        # Pop all temperatures smaller than current
        while stack and temperatures[stack[-1]] < temperatures[i]:
            prev_index = stack.pop()
            result[prev_index] = i - prev_index
        stack.append(i)
    
    # Remaining in stack have no warmer day (already 0)
    return result


def daily_temperatures_visualized(temperatures: List[int]) -> List[int]:
    """
    Same algorithm with step-by-step visualization.
    
    >>> daily_temperatures_visualized([73, 74, 75, 71, 69, 72, 76, 73])
    Processing index 0, temp 73: Stack=[], Push 0, Stack=[0]
    Processing index 1, temp 74: Stack=[0], 73<74, pop 0, result[0]=1, Push 1, Stack=[1]
    Processing index 2, temp 75: Stack=[1], 74<75, pop 1, result[1]=1, Push 2, Stack=[2]
    Processing index 3, temp 71: Stack=[2], Push 3, Stack=[2, 3]
    Processing index 4, temp 69: Stack=[2, 3], Push 4, Stack=[2, 3, 4]
    Processing index 5, temp 72: Stack=[2, 3, 4], 69<72, pop 4, result[4]=1, 71<72, pop 3, result[3]=2, Push 5, Stack=[2, 5]
    Processing index 6, temp 76: Stack=[2, 5], 72<76, pop 5, result[5]=1, 75<76, pop 2, result[2]=4, Push 6, Stack=[6]
    Processing index 7, temp 73: Stack=[6], Push 7, Stack=[6, 7]
    Final result: [1, 1, 4, 2, 1, 1, 0, 0]
    [1, 1, 4, 2, 1, 1, 0, 0]
    """
    n = len(temperatures)
    result = [0] * n
    stack = []
    
    for i in range(n):
        output = f"Processing index {i}, temp {temperatures[i]}: Stack={stack}, "
        
        while stack and temperatures[stack[-1]] < temperatures[i]:
            prev_index = stack.pop()
            result[prev_index] = i - prev_index
            output += f"{temperatures[prev_index]}<{temperatures[i]}, pop {prev_index}, result[{prev_index}]={result[prev_index]}, "
        
        stack.append(i)
        output += f"Push {i}, Stack={stack}"
        print(output)
    
    print(f"Final result: {result}")
    return result


# Unit tests
def test_daily_temperatures():
    assert daily_temperatures([73, 74, 75, 71, 69, 72, 76, 73]) == [1, 1, 4, 2, 1, 1, 0, 0]
    assert daily_temperatures([30, 40, 50, 60]) == [1, 1, 1, 0]
    assert daily_temperatures([30, 60, 90]) == [1, 1, 0]
    assert daily_temperatures([90, 80, 70]) == [0, 0, 0]  # Decreasing
    assert daily_temperatures([70, 70, 70]) == [0, 0, 0]  # All same
    assert daily_temperatures([70]) == [0]  # Single element
    print("All tests passed!")


if __name__ == "__main__":
    import doctest
    doctest.testmod()
    test_daily_temperatures()
```

**Pattern(s) Used:** Monotonic Stack (Decreasing)

---

## Edge Cases & Pitfalls

- **Empty Stack:** Check before pop/peek operations
- **Stack Underflow:** Handle gracefully or raise meaningful exception
- **Mismatched Brackets:** Different types `(`, `[`, `{` require matching
- **Operator Precedence:** In expression evaluation, handle `*`, `/` before `+`, `-`
- **Negative Numbers:** In calculators, distinguish unary minus from subtraction
- **Stack Overflow:** Deep recursion can exhaust call stack (use iteration instead)

---

## Follow-ups and Optimization Ideas

1. **Multi-threaded Stack:** Thread-safe stack using locks or lock-free algorithms
2. **Persistent Stack:** Immutable stack with structural sharing
3. **Undo/Redo:** Two stacks - one for history, one for redo
4. **Expression Tree:** Build tree from postfix expression using stack
5. **Monotonic Stack Variants:** Next smaller, previous greater, etc.

---

## Practice Checklist

| # | Problem | Difficulty | Link | Pattern |
|---|---------|------------|------|---------|
| 1 | Valid Parentheses | Easy | [LC #20](https://leetcode.com/problems/valid-parentheses/) | Matching |
| 2 | Min Stack | Medium | [LC #155](https://leetcode.com/problems/min-stack/) | Aux Stack |
| 3 | Implement Queue using Stacks | Easy | [LC #232](https://leetcode.com/problems/implement-queue-using-stacks/) | Two Stacks |
| 4 | Implement Stack using Queues | Easy | [LC #225](https://leetcode.com/problems/implement-stack-using-queues/) | Two Queues |
| 5 | Evaluate RPN | Medium | [LC #150](https://leetcode.com/problems/evaluate-reverse-polish-notation/) | Postfix |
| 6 | Daily Temperatures | Medium | [LC #739](https://leetcode.com/problems/daily-temperatures/) | Monotonic |
| 7 | Next Greater Element I | Easy | [LC #496](https://leetcode.com/problems/next-greater-element-i/) | Monotonic |
| 8 | Next Greater Element II | Medium | [LC #503](https://leetcode.com/problems/next-greater-element-ii/) | Circular |
| 9 | Decode String | Medium | [LC #394](https://leetcode.com/problems/decode-string/) | Nested |
| 10 | Remove K Digits | Medium | [LC #402](https://leetcode.com/problems/remove-k-digits/) | Monotonic |
| 11 | Asteroid Collision | Medium | [LC #735](https://leetcode.com/problems/asteroid-collision/) | Simulation |
| 12 | Basic Calculator | Hard | [LC #224](https://leetcode.com/problems/basic-calculator/) | Expression |
| 13 | Basic Calculator II | Medium | [LC #227](https://leetcode.com/problems/basic-calculator-ii/) | Precedence |
| 14 | Largest Rectangle in Histogram | Hard | [LC #84](https://leetcode.com/problems/largest-rectangle-in-histogram/) | Monotonic |
| 15 | Maximal Rectangle | Hard | [LC #85](https://leetcode.com/problems/maximal-rectangle/) | Histogram |
| 16 | Trapping Rain Water | Hard | [LC #42](https://leetcode.com/problems/trapping-rain-water/) | Monotonic |
| 17 | Remove Duplicate Letters | Medium | [LC #316](https://leetcode.com/problems/remove-duplicate-letters/) | Monotonic |
| 18 | 132 Pattern | Medium | [LC #456](https://leetcode.com/problems/132-pattern/) | Monotonic |
| 19 | Online Stock Span | Medium | [LC #901](https://leetcode.com/problems/online-stock-span/) | Monotonic |
| 20 | Sum of Subarray Minimums | Medium | [LC #907](https://leetcode.com/problems/sum-of-subarray-minimums/) | Contribution |

---

## Further Reading & References

- [CLRS Chapter 10.1 - Stacks and Queues](https://mitpress.mit.edu/books/introduction-algorithms-third-edition)
- [VisuAlgo - Stack](https://visualgo.net/en/list)
- [GeeksforGeeks - Stack](https://www.geeksforgeeks.org/stack-data-structure/)
- [LeetCode Stack Problems](https://leetcode.com/tag/stack/)
- [Monotonic Stack Pattern](https://leetcode.com/tag/monotonic-stack/)

---

## Flashcard Summary

**Q: What is LIFO?**
A: Last-In-First-Out: the most recently added element is removed first.

**Q: How does monotonic stack work for "next greater element"?**
A: Maintain decreasing stack. For each element, pop all smaller ones (they found their next greater). Push current.

**Q: How to implement queue using two stacks?**
A: Use in-stack for push, out-stack for pop. Transfer in→out when out is empty. Amortized O(1).

**Q: Time complexity of MinStack.getMin()?**
A: O(1) by maintaining auxiliary stack tracking minimum at each state.

**Q: When is monotonic decreasing vs increasing stack used?**
A: Decreasing for next greater element, increasing for next smaller element.

**Q: How does stack help with balanced parentheses?**
A: Push opening brackets, pop and match on closing brackets. Valid if stack empty at end.
