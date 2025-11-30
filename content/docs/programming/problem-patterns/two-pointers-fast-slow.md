---
title: "Two Pointers and Fast-Slow Pointers"
topic: "Problem Patterns"
difficulty: "Easy-Medium"
tags: ["two-pointers", "fast-slow", "cycle-detection", "array", "linked-list"]
status: "complete"
weight: 2
---

# Two Pointers and Fast-Slow Pointers

## 📚 Summary

Two pointers is a versatile technique for solving array, string, and linked list problems. Fast-slow (Floyd's) pointers are essential for cycle detection.

---

## 1️⃣ Two Pointers from Both Ends

### Basic Pattern

```python
def two_sum_sorted(nums: list[int], target: int) -> list[int]:
    """
    Two Sum II - Input Array Is Sorted (LC 167)
    """
    left, right = 0, len(nums) - 1
    
    while left < right:
        current_sum = nums[left] + nums[right]
        if current_sum == target:
            return [left + 1, right + 1]  # 1-indexed
        elif current_sum < target:
            left += 1
        else:
            right -= 1
    
    return []


def three_sum(nums: list[int]) -> list[list[int]]:
    """
    3Sum (LC 15)
    Find all unique triplets that sum to zero
    """
    nums.sort()
    n = len(nums)
    result = []
    
    for i in range(n - 2):
        # Skip duplicates
        if i > 0 and nums[i] == nums[i - 1]:
            continue
        
        left, right = i + 1, n - 1
        target = -nums[i]
        
        while left < right:
            current_sum = nums[left] + nums[right]
            if current_sum == target:
                result.append([nums[i], nums[left], nums[right]])
                # Skip duplicates
                while left < right and nums[left] == nums[left + 1]:
                    left += 1
                while left < right and nums[right] == nums[right - 1]:
                    right -= 1
                left += 1
                right -= 1
            elif current_sum < target:
                left += 1
            else:
                right -= 1
    
    return result


def container_with_most_water(height: list[int]) -> int:
    """
    Container With Most Water (LC 11)
    """
    left, right = 0, len(height) - 1
    max_area = 0
    
    while left < right:
        width = right - left
        h = min(height[left], height[right])
        max_area = max(max_area, width * h)
        
        if height[left] < height[right]:
            left += 1
        else:
            right -= 1
    
    return max_area


def trapping_rain_water(height: list[int]) -> int:
    """
    Trapping Rain Water (LC 42)
    """
    if not height:
        return 0
    
    left, right = 0, len(height) - 1
    left_max = right_max = 0
    water = 0
    
    while left < right:
        if height[left] < height[right]:
            if height[left] >= left_max:
                left_max = height[left]
            else:
                water += left_max - height[left]
            left += 1
        else:
            if height[right] >= right_max:
                right_max = height[right]
            else:
                water += right_max - height[right]
            right -= 1
    
    return water
```

### Palindrome Problems

```python
def valid_palindrome(s: str) -> bool:
    """
    Valid Palindrome (LC 125)
    """
    left, right = 0, len(s) - 1
    
    while left < right:
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
    Valid Palindrome II (LC 680)
    Can delete at most one character
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
            # Try deleting either character
            return is_palindrome(left + 1, right) or is_palindrome(left, right - 1)
        left += 1
        right -= 1
    
    return True
```

---

## 2️⃣ Two Pointers Same Direction

### Remove Duplicates

```python
def remove_duplicates(nums: list[int]) -> int:
    """
    Remove Duplicates from Sorted Array (LC 26)
    """
    if not nums:
        return 0
    
    write = 1
    
    for read in range(1, len(nums)):
        if nums[read] != nums[read - 1]:
            nums[write] = nums[read]
            write += 1
    
    return write


def remove_duplicates_ii(nums: list[int]) -> int:
    """
    Remove Duplicates from Sorted Array II (LC 80)
    Allow at most 2 duplicates
    """
    if len(nums) <= 2:
        return len(nums)
    
    write = 2
    
    for read in range(2, len(nums)):
        if nums[read] != nums[write - 2]:
            nums[write] = nums[read]
            write += 1
    
    return write


def remove_element(nums: list[int], val: int) -> int:
    """
    Remove Element (LC 27)
    """
    write = 0
    
    for read in range(len(nums)):
        if nums[read] != val:
            nums[write] = nums[read]
            write += 1
    
    return write


def move_zeroes(nums: list[int]) -> None:
    """
    Move Zeroes (LC 283)
    """
    write = 0
    
    for read in range(len(nums)):
        if nums[read] != 0:
            nums[write], nums[read] = nums[read], nums[write]
            write += 1
```

### Merge Operations

```python
def merge_sorted_arrays(nums1: list[int], m: int, nums2: list[int], n: int) -> None:
    """
    Merge Sorted Array (LC 88)
    Merge in-place from the end
    """
    p1, p2 = m - 1, n - 1
    write = m + n - 1
    
    while p1 >= 0 and p2 >= 0:
        if nums1[p1] > nums2[p2]:
            nums1[write] = nums1[p1]
            p1 -= 1
        else:
            nums1[write] = nums2[p2]
            p2 -= 1
        write -= 1
    
    # Copy remaining from nums2
    while p2 >= 0:
        nums1[write] = nums2[p2]
        p2 -= 1
        write -= 1


def sort_colors(nums: list[int]) -> None:
    """
    Sort Colors / Dutch National Flag (LC 75)
    Three-way partition
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
```

---

## 3️⃣ Fast-Slow Pointers (Floyd's Cycle Detection)

### Linked List Cycle

```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next


def has_cycle(head: ListNode) -> bool:
    """
    Linked List Cycle (LC 141)
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


def detect_cycle(head: ListNode) -> ListNode | None:
    """
    Linked List Cycle II (LC 142)
    Return the node where cycle begins
    
    Mathematical proof:
    - Let distance from head to cycle start = a
    - Let cycle length = c
    - When fast and slow meet, slow has traveled a + b (b = distance into cycle)
    - Fast has traveled 2(a + b)
    - Difference is one cycle: 2(a+b) - (a+b) = c => a + b = c
    - So a = c - b, meaning starting from head and meeting point, 
      they'll meet at cycle start
    """
    if not head or not head.next:
        return None
    
    slow = fast = head
    
    # Find meeting point
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


def find_duplicate(nums: list[int]) -> int:
    """
    Find the Duplicate Number (LC 287)
    Array as linked list: nums[i] points to index nums[i]
    """
    # Phase 1: Find meeting point
    slow = fast = nums[0]
    while True:
        slow = nums[slow]
        fast = nums[nums[fast]]
        if slow == fast:
            break
    
    # Phase 2: Find cycle start
    slow = nums[0]
    while slow != fast:
        slow = nums[slow]
        fast = nums[fast]
    
    return slow
```

### Middle of List

```python
def middle_node(head: ListNode) -> ListNode:
    """
    Middle of the Linked List (LC 876)
    When fast reaches end, slow is at middle
    """
    slow = fast = head
    
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    
    return slow


def reorder_list(head: ListNode) -> None:
    """
    Reorder List (LC 143)
    L0→L1→...→Ln → L0→Ln→L1→Ln-1→...
    """
    if not head or not head.next:
        return
    
    # Find middle
    slow = fast = head
    while fast.next and fast.next.next:
        slow = slow.next
        fast = fast.next.next
    
    # Reverse second half
    prev, curr = None, slow.next
    slow.next = None
    
    while curr:
        next_node = curr.next
        curr.next = prev
        prev = curr
        curr = next_node
    
    # Merge two halves
    first, second = head, prev
    while second:
        next1, next2 = first.next, second.next
        first.next = second
        second.next = next1
        first = next1
        second = next2


def is_palindrome_list(head: ListNode) -> bool:
    """
    Palindrome Linked List (LC 234)
    """
    if not head or not head.next:
        return True
    
    # Find middle
    slow = fast = head
    while fast.next and fast.next.next:
        slow = slow.next
        fast = fast.next.next
    
    # Reverse second half
    prev, curr = None, slow.next
    while curr:
        next_node = curr.next
        curr.next = prev
        prev = curr
        curr = next_node
    
    # Compare
    first, second = head, prev
    while second:
        if first.val != second.val:
            return False
        first = first.next
        second = second.next
    
    return True
```

### Happy Number

```python
def is_happy(n: int) -> bool:
    """
    Happy Number (LC 202)
    Either reaches 1 or enters cycle
    """
    def get_next(num: int) -> int:
        total = 0
        while num > 0:
            digit = num % 10
            total += digit * digit
            num //= 10
        return total
    
    slow = fast = n
    
    while True:
        slow = get_next(slow)
        fast = get_next(get_next(fast))
        
        if slow == fast:
            break
    
    return slow == 1
```

---

## 4️⃣ Partition and Rearrangement

```python
def partition_array(nums: list[int], pivot: int) -> list[int]:
    """
    Partition array around pivot
    Elements < pivot | Elements = pivot | Elements > pivot
    """
    n = len(nums)
    result = [0] * n
    
    low, high = 0, n - 1
    
    for num in nums:
        if num < pivot:
            result[low] = num
            low += 1
        elif num > pivot:
            result[high] = num
            high -= 1
    
    # Fill middle with pivot
    while low <= high:
        result[low] = pivot
        low += 1
    
    return result


def rearrange_array(nums: list[int]) -> list[int]:
    """
    Rearrange Array Elements by Sign (LC 2149)
    Alternate positive and negative
    """
    n = len(nums)
    result = [0] * n
    pos_idx, neg_idx = 0, 1
    
    for num in nums:
        if num > 0:
            result[pos_idx] = num
            pos_idx += 2
        else:
            result[neg_idx] = num
            neg_idx += 2
    
    return result
```

---

## ⏱️ Complexity Analysis

| Pattern | Time | Space |
|---------|------|-------|
| Two ends | O(n) | O(1) |
| Same direction | O(n) | O(1) |
| Fast-slow | O(n) | O(1) |
| Partition | O(n) | O(1) or O(n) |

---

## 📚 Practice Problems

### Two Pointers from Both Ends
| # | Problem | Key Concept |
|---|---------|-------------|
| 1 | [LC 11: Container With Most Water](https://leetcode.com/problems/container-with-most-water/) | Max area |
| 2 | [LC 15: 3Sum](https://leetcode.com/problems/3sum/) | Sort + 2P |
| 3 | [LC 16: 3Sum Closest](https://leetcode.com/problems/3sum-closest/) | Sort + 2P |
| 4 | [LC 42: Trapping Rain Water](https://leetcode.com/problems/trapping-rain-water/) | Two pointers |
| 5 | [LC 167: Two Sum II](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/) | Sorted array |

### Same Direction
| # | Problem | Key Concept |
|---|---------|-------------|
| 6 | [LC 26: Remove Duplicates](https://leetcode.com/problems/remove-duplicates-from-sorted-array/) | In-place |
| 7 | [LC 75: Sort Colors](https://leetcode.com/problems/sort-colors/) | Dutch flag |
| 8 | [LC 88: Merge Sorted Array](https://leetcode.com/problems/merge-sorted-array/) | Merge from end |
| 9 | [LC 283: Move Zeroes](https://leetcode.com/problems/move-zeroes/) | Swap |

### Fast-Slow Pointers
| # | Problem | Key Concept |
|---|---------|-------------|
| 10 | [LC 141: Linked List Cycle](https://leetcode.com/problems/linked-list-cycle/) | Cycle detection |
| 11 | [LC 142: Linked List Cycle II](https://leetcode.com/problems/linked-list-cycle-ii/) | Find cycle start |
| 12 | [LC 202: Happy Number](https://leetcode.com/problems/happy-number/) | Number as list |
| 13 | [LC 234: Palindrome Linked List](https://leetcode.com/problems/palindrome-linked-list/) | Middle + reverse |
| 14 | [LC 287: Find the Duplicate](https://leetcode.com/problems/find-the-duplicate-number/) | Array as list |
| 15 | [LC 876: Middle of Linked List](https://leetcode.com/problems/middle-of-the-linked-list/) | Fast-slow |

---

*Last Updated: 2024*
