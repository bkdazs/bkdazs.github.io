---
title: "Two Pointers Technique"
weight: 3
---

# Two Pointers Technique

---
title: "Two Pointers Technique"
topic: "Two Pointer Patterns and Applications"
difficulty: "Easy to Medium"
tags: ["two-pointers", "array", "string", "sorted", "sliding-window"]
status: "complete"
---

## Summary / TL;DR

Two pointers is a technique using two indices that move through data, often reducing O(n²) to O(n). Common patterns include:

1. **Opposite ends:** Start from both ends, move inward
2. **Same direction:** Fast and slow pointers
3. **Two arrays:** Process two sequences together
4. **Sliding window:** Variant with expandable window

---

## When to Use

- **Sorted array operations:** Two Sum, 3Sum, container problems
- **Palindrome checking:** Compare from both ends
- **Partitioning:** Separate elements by condition
- **Cycle detection:** Floyd's algorithm
- **Merge operations:** Combine sorted arrays

### Pattern Recognition

| Clue | Pattern |
|------|---------|
| "Sorted array" + "find pair" | Opposite ends |
| "Remove duplicates" | Same direction |
| "Cycle in linked list" | Fast/slow |
| "Merge sorted" | Two arrays |
| "Container with water" | Opposite ends |

---

## Big-O Complexity

| Pattern | Time | Space |
|---------|------|-------|
| Opposite ends | O(n) | O(1) |
| Same direction | O(n) | O(1) |
| Fast/slow | O(n) | O(1) |
| Two arrays | O(n + m) | O(1) or O(n) |

---

## Core Patterns

### Pattern 1: Opposite Ends (Inward Movement)

```python
from typing import List

def two_sum_sorted(numbers: List[int], target: int) -> List[int]:
    """
    Find two numbers in sorted array that sum to target.
    
    Time: O(n), Space: O(1)
    """
    left, right = 0, len(numbers) - 1
    
    while left < right:
        curr_sum = numbers[left] + numbers[right]
        
        if curr_sum == target:
            return [left + 1, right + 1]  # 1-indexed
        elif curr_sum < target:
            left += 1
        else:
            right -= 1
    
    return []


def three_sum(nums: List[int]) -> List[List[int]]:
    """
    Find all unique triplets that sum to zero.
    
    Time: O(n²), Space: O(1) excluding output
    """
    nums.sort()
    result = []
    
    for i in range(len(nums) - 2):
        # Skip duplicates for first element
        if i > 0 and nums[i] == nums[i - 1]:
            continue
        
        left, right = i + 1, len(nums) - 1
        target = -nums[i]
        
        while left < right:
            curr_sum = nums[left] + nums[right]
            
            if curr_sum == target:
                result.append([nums[i], nums[left], nums[right]])
                
                # Skip duplicates
                while left < right and nums[left] == nums[left + 1]:
                    left += 1
                while left < right and nums[right] == nums[right - 1]:
                    right -= 1
                
                left += 1
                right -= 1
            elif curr_sum < target:
                left += 1
            else:
                right -= 1
    
    return result


def three_sum_closest(nums: List[int], target: int) -> int:
    """
    Find triplet sum closest to target.
    
    Time: O(n²), Space: O(1)
    """
    nums.sort()
    closest = float('inf')
    
    for i in range(len(nums) - 2):
        left, right = i + 1, len(nums) - 1
        
        while left < right:
            curr_sum = nums[i] + nums[left] + nums[right]
            
            if abs(curr_sum - target) < abs(closest - target):
                closest = curr_sum
            
            if curr_sum < target:
                left += 1
            elif curr_sum > target:
                right -= 1
            else:
                return target
    
    return closest


def container_with_most_water(height: List[int]) -> int:
    """
    Find two lines that form container with most water.
    
    Key insight: Move the shorter line inward (can't get more water otherwise).
    
    Time: O(n), Space: O(1)
    """
    left, right = 0, len(height) - 1
    max_area = 0
    
    while left < right:
        width = right - left
        h = min(height[left], height[right])
        max_area = max(max_area, width * h)
        
        # Move shorter line
        if height[left] < height[right]:
            left += 1
        else:
            right -= 1
    
    return max_area


def valid_palindrome(s: str) -> bool:
    """
    Check if string is palindrome (alphanumeric only).
    
    Time: O(n), Space: O(1)
    """
    left, right = 0, len(s) - 1
    
    while left < right:
        # Skip non-alphanumeric
        while left < right and not s[left].isalnum():
            left += 1
        while left < right and not s[right].isalnum():
            right -= 1
        
        if s[left].lower() != s[right].lower():
            return False
        
        left += 1
        right -= 1
    
    return True


def valid_palindrome_ii(s: str) -> bool:
    """
    Check if string can be palindrome by removing at most one character.
    
    Time: O(n), Space: O(1)
    """
    def is_palindrome(left: int, right: int) -> bool:
        while left < right:
            if s[left] != s[right]:
                return False
            left += 1
            right -= 1
        return True
    
    left, right = 0, len(s) - 1
    
    while left < right:
        if s[left] != s[right]:
            # Try removing either character
            return is_palindrome(left + 1, right) or is_palindrome(left, right - 1)
        left += 1
        right -= 1
    
    return True
```

### Pattern 2: Same Direction (Fast/Slow Pointers)

```python
def remove_duplicates(nums: List[int]) -> int:
    """
    Remove duplicates in-place from sorted array.
    Return new length.
    
    slow: position to write next unique element
    fast: scans through array
    
    Time: O(n), Space: O(1)
    """
    if not nums:
        return 0
    
    slow = 1
    
    for fast in range(1, len(nums)):
        if nums[fast] != nums[fast - 1]:
            nums[slow] = nums[fast]
            slow += 1
    
    return slow


def remove_duplicates_allow_two(nums: List[int]) -> int:
    """
    Remove duplicates allowing at most 2 occurrences.
    
    Time: O(n), Space: O(1)
    """
    if len(nums) <= 2:
        return len(nums)
    
    slow = 2
    
    for fast in range(2, len(nums)):
        if nums[fast] != nums[slow - 2]:
            nums[slow] = nums[fast]
            slow += 1
    
    return slow


def move_zeroes(nums: List[int]) -> None:
    """
    Move all zeroes to end, maintaining relative order of non-zero elements.
    
    Time: O(n), Space: O(1)
    """
    slow = 0
    
    for fast in range(len(nums)):
        if nums[fast] != 0:
            nums[slow], nums[fast] = nums[fast], nums[slow]
            slow += 1


def remove_element(nums: List[int], val: int) -> int:
    """
    Remove all instances of val in-place.
    
    Time: O(n), Space: O(1)
    """
    slow = 0
    
    for fast in range(len(nums)):
        if nums[fast] != val:
            nums[slow] = nums[fast]
            slow += 1
    
    return slow


def sort_array_by_parity(nums: List[int]) -> List[int]:
    """
    Rearrange so even numbers come before odd.
    
    Time: O(n), Space: O(1)
    """
    slow = 0
    
    for fast in range(len(nums)):
        if nums[fast] % 2 == 0:
            nums[slow], nums[fast] = nums[fast], nums[slow]
            slow += 1
    
    return nums
```

### Pattern 3: Linked List Fast/Slow

```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next


def has_cycle(head: ListNode) -> bool:
    """
    Detect cycle in linked list using Floyd's algorithm.
    
    Time: O(n), Space: O(1)
    """
    if not head or not head.next:
        return False
    
    slow = fast = head
    
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        
        if slow == fast:
            return True
    
    return False


def find_cycle_start(head: ListNode) -> ListNode:
    """
    Find the node where cycle begins.
    
    After detection: distance from head to cycle start = 
                     distance from meeting point to cycle start
    
    Time: O(n), Space: O(1)
    """
    if not head or not head.next:
        return None
    
    slow = fast = head
    
    # Detect cycle
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        
        if slow == fast:
            break
    else:
        return None  # No cycle
    
    # Find cycle start
    slow = head
    while slow != fast:
        slow = slow.next
        fast = fast.next
    
    return slow


def find_middle(head: ListNode) -> ListNode:
    """
    Find middle node (second middle if even length).
    
    Time: O(n), Space: O(1)
    """
    slow = fast = head
    
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    
    return slow


def get_nth_from_end(head: ListNode, n: int) -> ListNode:
    """
    Get nth node from end (1-indexed).
    
    Time: O(length), Space: O(1)
    """
    fast = head
    
    # Move fast n steps ahead
    for _ in range(n):
        fast = fast.next
    
    slow = head
    while fast:
        slow = slow.next
        fast = fast.next
    
    return slow


def is_palindrome_list(head: ListNode) -> bool:
    """
    Check if linked list is palindrome.
    
    1. Find middle
    2. Reverse second half
    3. Compare both halves
    
    Time: O(n), Space: O(1)
    """
    if not head or not head.next:
        return True
    
    # Find middle
    slow = fast = head
    while fast.next and fast.next.next:
        slow = slow.next
        fast = fast.next.next
    
    # Reverse second half
    second = slow.next
    slow.next = None
    prev = None
    while second:
        next_node = second.next
        second.next = prev
        prev = second
        second = next_node
    
    # Compare
    first, second = head, prev
    while second:
        if first.val != second.val:
            return False
        first = first.next
        second = second.next
    
    return True
```

### Pattern 4: Two Arrays/Strings

```python
def merge_sorted_arrays(nums1: List[int], m: int, 
                        nums2: List[int], n: int) -> None:
    """
    Merge nums2 into nums1 (nums1 has extra space).
    
    Key: Start from the end to avoid overwriting.
    
    Time: O(m + n), Space: O(1)
    """
    p1, p2, p = m - 1, n - 1, m + n - 1
    
    while p1 >= 0 and p2 >= 0:
        if nums1[p1] > nums2[p2]:
            nums1[p] = nums1[p1]
            p1 -= 1
        else:
            nums1[p] = nums2[p2]
            p2 -= 1
        p -= 1
    
    # Copy remaining from nums2 (if any)
    nums1[:p2 + 1] = nums2[:p2 + 1]


def intersection_of_two_arrays(nums1: List[int], nums2: List[int]) -> List[int]:
    """
    Find intersection (each element appears once in result).
    
    Time: O(n log n + m log m), Space: O(1) excluding output
    """
    nums1.sort()
    nums2.sort()
    
    result = []
    p1, p2 = 0, 0
    
    while p1 < len(nums1) and p2 < len(nums2):
        if nums1[p1] < nums2[p2]:
            p1 += 1
        elif nums1[p1] > nums2[p2]:
            p2 += 1
        else:
            # Found match
            if not result or result[-1] != nums1[p1]:
                result.append(nums1[p1])
            p1 += 1
            p2 += 1
    
    return result


def is_subsequence(s: str, t: str) -> bool:
    """
    Check if s is subsequence of t.
    
    Time: O(len(t)), Space: O(1)
    """
    if not s:
        return True
    
    sp = 0
    
    for char in t:
        if char == s[sp]:
            sp += 1
            if sp == len(s):
                return True
    
    return False


def backspace_string_compare(s: str, t: str) -> bool:
    """
    Compare strings where '#' means backspace.
    
    Process from right to left.
    
    Time: O(n + m), Space: O(1)
    """
    def get_next_valid(string: str, idx: int) -> int:
        skip = 0
        while idx >= 0:
            if string[idx] == '#':
                skip += 1
                idx -= 1
            elif skip > 0:
                skip -= 1
                idx -= 1
            else:
                break
        return idx
    
    i, j = len(s) - 1, len(t) - 1
    
    while i >= 0 or j >= 0:
        i = get_next_valid(s, i)
        j = get_next_valid(t, j)
        
        if i >= 0 and j >= 0:
            if s[i] != t[j]:
                return False
            i -= 1
            j -= 1
        elif i >= 0 or j >= 0:
            return False
    
    return True
```

### Pattern 5: Partition/Dutch National Flag

```python
def sort_colors(nums: List[int]) -> None:
    """
    Sort array containing 0, 1, 2 (Dutch National Flag).
    
    Three pointers: low (next 0), mid (current), high (next 2)
    
    Time: O(n), Space: O(1)
    """
    low, mid, high = 0, 0, len(nums) - 1
    
    while mid <= high:
        if nums[mid] == 0:
            nums[low], nums[mid] = nums[mid], nums[low]
            low += 1
            mid += 1
        elif nums[mid] == 1:
            mid += 1
        else:  # nums[mid] == 2
            nums[mid], nums[high] = nums[high], nums[mid]
            high -= 1


def partition_labels(s: str) -> List[int]:
    """
    Partition string into max parts where each letter appears in at most one part.
    
    Time: O(n), Space: O(1)
    """
    # Last occurrence of each character
    last = {c: i for i, c in enumerate(s)}
    
    result = []
    start = end = 0
    
    for i, c in enumerate(s):
        end = max(end, last[c])
        
        if i == end:
            result.append(end - start + 1)
            start = i + 1
    
    return result
```

---

## Worked Examples

### Example 1: Two Sum II (Sorted Array)

```
numbers = [2, 7, 11, 15], target = 9

left=0, right=3: 2 + 15 = 17 > 9, move right
left=0, right=2: 2 + 11 = 13 > 9, move right
left=0, right=1: 2 + 7 = 9 = target ✓

Return [1, 2] (1-indexed)
```

### Example 2: Container With Most Water

```
height = [1, 8, 6, 2, 5, 4, 8, 3, 7]

left=0, right=8: area = min(1,7) * 8 = 8, move left (shorter)
left=1, right=8: area = min(8,7) * 7 = 49 ✓ best so far
left=1, right=7: area = min(8,3) * 6 = 18
...continue...

Maximum area: 49
```

### Example 3: Cycle Detection

```
List: 1 -> 2 -> 3 -> 4 -> 5
              ^         |
              |_________|

slow: 1, fast: 1
slow: 2, fast: 3
slow: 3, fast: 5
slow: 4, fast: 4 (meet!)

Cycle detected. To find start:
slow from head, fast from meeting point, same speed
slow: 1, fast: 4
slow: 2, fast: 5
slow: 3, fast: 3 (meet at cycle start!)
```

---

## Edge Cases & Gotchas

```python
# 1. Empty array
if not nums:
    return

# 2. Single element
if len(nums) == 1:
    return nums[0]

# 3. All same elements
# May need special handling in partition

# 4. Duplicates in two-pointer search
# Skip duplicates to avoid redundant work

# 5. Off-by-one in opposite ends
while left < right:  # Not <=

# 6. Linked list edge cases
if not head or not head.next:
    return head
```

---

## Interview Tips

### Framework

1. **Identify pattern:** Opposite ends? Same direction? Two arrays?
2. **Initialize pointers:** Where do they start?
3. **Define movement:** When does each pointer move?
4. **Termination:** When do we stop?

### Key Phrases

- "Since the array is sorted, I can use two pointers from opposite ends."
- "I'll use fast/slow pointers to detect the cycle."
- "The slow pointer marks where to write; fast pointer scans."
- "When pointers meet/cross, we've covered all elements."

---

## Practice Problems

### Opposite Ends

| # | Problem | Key Concept |
|---|---------|-------------|
| 1 | [Two Sum II](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/) | Sorted two sum |
| 2 | [3Sum](https://leetcode.com/problems/3sum/) | Three pointers |
| 3 | [3Sum Closest](https://leetcode.com/problems/3sum-closest/) | Minimize difference |
| 4 | [Container With Most Water](https://leetcode.com/problems/container-with-most-water/) | Area optimization |
| 5 | [Trapping Rain Water](https://leetcode.com/problems/trapping-rain-water/) | Height tracking |
| 6 | [Valid Palindrome](https://leetcode.com/problems/valid-palindrome/) | Character compare |
| 7 | [Valid Palindrome II](https://leetcode.com/problems/valid-palindrome-ii/) | Remove one char |

### Same Direction

| # | Problem | Key Concept |
|---|---------|-------------|
| 8 | [Remove Duplicates](https://leetcode.com/problems/remove-duplicates-from-sorted-array/) | Fast/slow |
| 9 | [Remove Duplicates II](https://leetcode.com/problems/remove-duplicates-from-sorted-array-ii/) | Allow k copies |
| 10 | [Move Zeroes](https://leetcode.com/problems/move-zeroes/) | Partition |
| 11 | [Remove Element](https://leetcode.com/problems/remove-element/) | Filter |
| 12 | [Sort Array By Parity](https://leetcode.com/problems/sort-array-by-parity/) | Even/odd |

### Linked List

| # | Problem | Key Concept |
|---|---------|-------------|
| 13 | [Linked List Cycle](https://leetcode.com/problems/linked-list-cycle/) | Floyd's |
| 14 | [Linked List Cycle II](https://leetcode.com/problems/linked-list-cycle-ii/) | Find start |
| 15 | [Middle of Linked List](https://leetcode.com/problems/middle-of-the-linked-list/) | Fast/slow |
| 16 | [Remove Nth from End](https://leetcode.com/problems/remove-nth-node-from-end-of-list/) | Gap pointer |
| 17 | [Palindrome Linked List](https://leetcode.com/problems/palindrome-linked-list/) | Reverse half |
| 18 | [Intersection of Two Lists](https://leetcode.com/problems/intersection-of-two-linked-lists/) | Length adjust |

### Two Arrays/Strings

| # | Problem | Key Concept |
|---|---------|-------------|
| 19 | [Merge Sorted Array](https://leetcode.com/problems/merge-sorted-array/) | Reverse merge |
| 20 | [Is Subsequence](https://leetcode.com/problems/is-subsequence/) | Match chars |
| 21 | [Backspace String Compare](https://leetcode.com/problems/backspace-string-compare/) | Reverse process |
| 22 | [Interval List Intersections](https://leetcode.com/problems/interval-list-intersections/) | Interval overlap |

### Dutch Flag / Partition

| # | Problem | Key Concept |
|---|---------|-------------|
| 23 | [Sort Colors](https://leetcode.com/problems/sort-colors/) | Three-way partition |
| 24 | [Partition Labels](https://leetcode.com/problems/partition-labels/) | Greedy partition |

---

## References

- [Two Pointers Pattern](https://leetcode.com/explore/learn/card/two-pointers/)
- [Grokking: Two Pointers](https://www.educative.io/courses/grokking-the-coding-interview)
- [Floyd's Cycle Detection](https://en.wikipedia.org/wiki/Cycle_detection)
