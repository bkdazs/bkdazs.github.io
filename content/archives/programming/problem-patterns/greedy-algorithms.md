---
title: "Greedy Algorithm Patterns"
topic: "Problem Patterns"
difficulty: "Medium"
tags: ["greedy", "activity-selection", "scheduling", "optimization"]
status: "complete"
weight: 19
---

# Greedy Algorithm Patterns

## 📚 Overview

Greedy algorithms make locally optimal choices at each step. Key insight: prove the greedy choice is safe (leads to global optimal).

---

## 1️⃣ Activity Selection / Meeting Rooms

### Non-overlapping Intervals (LC 435)

```python
def erase_overlap_intervals(intervals: list[list[int]]) -> int:
    """Minimum intervals to remove for non-overlap"""
    if not intervals:
        return 0
    
    # Sort by end time
    intervals.sort(key=lambda x: x[1])
    
    count = 0
    prev_end = float('-inf')
    
    for start, end in intervals:
        if start >= prev_end:
            prev_end = end  # Keep this interval
        else:
            count += 1  # Remove overlapping
    
    return count
```

### Meeting Rooms II (LC 253)

```python
def min_meeting_rooms(intervals: list[list[int]]) -> int:
    """Minimum meeting rooms required"""
    import heapq
    
    if not intervals:
        return 0
    
    intervals.sort(key=lambda x: x[0])
    
    # Min heap of end times
    heap = []
    heapq.heappush(heap, intervals[0][1])
    
    for i in range(1, len(intervals)):
        # If room is free, reuse it
        if intervals[i][0] >= heap[0]:
            heapq.heappop(heap)
        
        heapq.heappush(heap, intervals[i][1])
    
    return len(heap)
```

### Maximum Number of Events (LC 1353)

```python
def max_events(events: list[list[int]]) -> int:
    """Maximum events you can attend (one per day)"""
    import heapq
    
    events.sort()
    max_day = max(e[1] for e in events)
    
    i = 0
    count = 0
    heap = []  # End days of available events
    
    for day in range(1, max_day + 1):
        # Add events starting today
        while i < len(events) and events[i][0] == day:
            heapq.heappush(heap, events[i][1])
            i += 1
        
        # Remove expired events
        while heap and heap[0] < day:
            heapq.heappop(heap)
        
        # Attend event ending soonest
        if heap:
            heapq.heappop(heap)
            count += 1
    
    return count
```

---

## 2️⃣ Jump Game Patterns

### Jump Game (LC 55)

```python
def can_jump(nums: list[int]) -> bool:
    """Can reach last index"""
    max_reach = 0
    
    for i, jump in enumerate(nums):
        if i > max_reach:
            return False
        max_reach = max(max_reach, i + jump)
        if max_reach >= len(nums) - 1:
            return True
    
    return True
```

### Jump Game II (LC 45)

```python
def jump(nums: list[int]) -> int:
    """Minimum jumps to reach end"""
    n = len(nums)
    if n <= 1:
        return 0
    
    jumps = 0
    current_end = 0
    farthest = 0
    
    for i in range(n - 1):
        farthest = max(farthest, i + nums[i])
        
        if i == current_end:
            jumps += 1
            current_end = farthest
            
            if current_end >= n - 1:
                break
    
    return jumps
```

### Video Stitching (LC 1024)

```python
def video_stitching(clips: list[list[int]], time: int) -> int:
    """Minimum clips to cover [0, time]"""
    clips.sort()
    
    count = 0
    current_end = 0
    i = 0
    n = len(clips)
    
    while current_end < time:
        farthest = current_end
        
        # Find clip that extends farthest
        while i < n and clips[i][0] <= current_end:
            farthest = max(farthest, clips[i][1])
            i += 1
        
        if farthest == current_end:
            return -1  # Can't extend
        
        current_end = farthest
        count += 1
    
    return count
```

---

## 3️⃣ Gas Station & Circular Problems

### Gas Station (LC 134)

```python
def can_complete_circuit(gas: list[int], cost: list[int]) -> int:
    """Find starting station for circular trip"""
    n = len(gas)
    total_tank = 0
    current_tank = 0
    start = 0
    
    for i in range(n):
        gain = gas[i] - cost[i]
        total_tank += gain
        current_tank += gain
        
        if current_tank < 0:
            # Can't start from any station before i+1
            start = i + 1
            current_tank = 0
    
    return start if total_tank >= 0 else -1
```

### Candy Distribution (LC 135)

```python
def candy(ratings: list[int]) -> int:
    """Minimum candies so higher rating gets more"""
    n = len(ratings)
    candies = [1] * n
    
    # Left to right pass
    for i in range(1, n):
        if ratings[i] > ratings[i - 1]:
            candies[i] = candies[i - 1] + 1
    
    # Right to left pass
    for i in range(n - 2, -1, -1):
        if ratings[i] > ratings[i + 1]:
            candies[i] = max(candies[i], candies[i + 1] + 1)
    
    return sum(candies)
```

---

## 4️⃣ Task Scheduling

### Task Scheduler (LC 621)

```python
def least_interval(tasks: list[str], n: int) -> int:
    """Minimum intervals to complete all tasks with cooldown"""
    from collections import Counter
    
    freq = Counter(tasks)
    max_freq = max(freq.values())
    max_count = sum(1 for f in freq.values() if f == max_freq)
    
    # Formula: (max_freq - 1) * (n + 1) + max_count
    # But at least len(tasks) if no idle needed
    return max(len(tasks), (max_freq - 1) * (n + 1) + max_count)
```

### Reorganize String (LC 767)

```python
def reorganize_string(s: str) -> str:
    """Rearrange so no adjacent chars are same"""
    from collections import Counter
    import heapq
    
    freq = Counter(s)
    n = len(s)
    
    # Check if possible
    max_freq = max(freq.values())
    if max_freq > (n + 1) // 2:
        return ""
    
    # Max heap of (-count, char)
    heap = [(-count, char) for char, count in freq.items()]
    heapq.heapify(heap)
    
    result = []
    prev_count, prev_char = 0, ''
    
    while heap:
        count, char = heapq.heappop(heap)
        result.append(char)
        
        # Add previous char back if it has remaining count
        if prev_count < 0:
            heapq.heappush(heap, (prev_count, prev_char))
        
        prev_count = count + 1  # Used one
        prev_char = char
    
    return ''.join(result)
```

---

## 5️⃣ Partition Problems

### Partition Labels (LC 763)

```python
def partition_labels(s: str) -> list[int]:
    """Partition into max parts where each letter in at most one part"""
    # Find last occurrence of each character
    last = {c: i for i, c in enumerate(s)}
    
    result = []
    start = 0
    end = 0
    
    for i, c in enumerate(s):
        end = max(end, last[c])
        
        if i == end:
            result.append(end - start + 1)
            start = i + 1
    
    return result
```

### Split Array into Consecutive Subsequences (LC 659)

```python
def is_possible(nums: list[int]) -> bool:
    """Split into subsequences of length >= 3"""
    from collections import Counter
    
    freq = Counter(nums)
    need = Counter()  # Subsequences waiting for this number
    
    for num in nums:
        if freq[num] == 0:
            continue
        
        if need[num] > 0:
            # Extend existing subsequence
            need[num] -= 1
            need[num + 1] += 1
        elif freq[num + 1] > 0 and freq[num + 2] > 0:
            # Start new subsequence
            freq[num + 1] -= 1
            freq[num + 2] -= 1
            need[num + 3] += 1
        else:
            return False
        
        freq[num] -= 1
    
    return True
```

---

## 6️⃣ Greedy Stock Problems

### Best Time to Buy and Sell Stock II (LC 122)

```python
def max_profit(prices: list[int]) -> int:
    """Max profit with multiple transactions"""
    profit = 0
    
    for i in range(1, len(prices)):
        if prices[i] > prices[i - 1]:
            profit += prices[i] - prices[i - 1]
    
    return profit
```

### Best Time to Buy and Sell with Transaction Fee (LC 714)

```python
def max_profit(prices: list[int], fee: int) -> int:
    """Max profit with transaction fee"""
    cash = 0           # Max profit if not holding
    hold = -prices[0]  # Max profit if holding
    
    for price in prices[1:]:
        cash = max(cash, hold + price - fee)
        hold = max(hold, cash - price)
    
    return cash
```

---

## 7️⃣ String Greedy

### Remove Duplicate Letters (LC 316)

```python
def remove_duplicate_letters(s: str) -> str:
    """Remove duplicates for smallest lexicographic result"""
    from collections import Counter
    
    last_occurrence = {c: i for i, c in enumerate(s)}
    stack = []
    seen = set()
    
    for i, c in enumerate(s):
        if c in seen:
            continue
        
        # Remove larger chars if they appear later
        while stack and c < stack[-1] and last_occurrence[stack[-1]] > i:
            seen.remove(stack.pop())
        
        stack.append(c)
        seen.add(c)
    
    return ''.join(stack)
```

### Remove K Digits (LC 402)

```python
def remove_kdigits(num: str, k: int) -> str:
    """Remove k digits for smallest number"""
    stack = []
    
    for digit in num:
        while k > 0 and stack and stack[-1] > digit:
            stack.pop()
            k -= 1
        stack.append(digit)
    
    # Remove remaining k digits from end
    stack = stack[:-k] if k else stack
    
    # Remove leading zeros and handle empty
    return ''.join(stack).lstrip('0') or '0'
```

---

## 8️⃣ Greedy with Sorting

### Boats to Save People (LC 881)

```python
def num_rescue_boats(people: list[int], limit: int) -> int:
    """Minimum boats (max 2 people, max weight limit)"""
    people.sort()
    boats = 0
    left, right = 0, len(people) - 1
    
    while left <= right:
        if people[left] + people[right] <= limit:
            left += 1
        right -= 1
        boats += 1
    
    return boats
```

### Assign Cookies (LC 455)

```python
def find_content_children(greed: list[int], cookies: list[int]) -> int:
    """Maximize content children"""
    greed.sort()
    cookies.sort()
    
    child = 0
    cookie = 0
    
    while child < len(greed) and cookie < len(cookies):
        if cookies[cookie] >= greed[child]:
            child += 1
        cookie += 1
    
    return child
```

### Two City Scheduling (LC 1029)

```python
def two_city_sched_cost(costs: list[list[int]]) -> int:
    """Send n people to each city with minimum cost"""
    # Sort by cost difference (city A - city B)
    costs.sort(key=lambda x: x[0] - x[1])
    
    total = 0
    n = len(costs) // 2
    
    # First half goes to city A, second half to city B
    for i in range(n):
        total += costs[i][0] + costs[i + n][1]
    
    return total
```

---

## 9️⃣ Fractional Greedy

### Minimum Number of Arrows to Burst Balloons (LC 452)

```python
def find_min_arrow_shots(points: list[list[int]]) -> int:
    """Minimum arrows to burst all balloons"""
    if not points:
        return 0
    
    # Sort by end coordinate
    points.sort(key=lambda x: x[1])
    
    arrows = 1
    arrow_pos = points[0][1]
    
    for start, end in points[1:]:
        if start > arrow_pos:
            arrows += 1
            arrow_pos = end
    
    return arrows
```

### Queue Reconstruction by Height (LC 406)

```python
def reconstruct_queue(people: list[list[int]]) -> list[list[int]]:
    """Reconstruct queue by (height, count_taller_ahead)"""
    # Sort by height descending, then by k ascending
    people.sort(key=lambda x: (-x[0], x[1]))
    
    result = []
    for person in people:
        result.insert(person[1], person)
    
    return result
```

---

## 🔟 Advanced Greedy

### IPO (LC 502)

```python
def find_maximized_capital(k: int, w: int, profits: list[int], capital: list[int]) -> int:
    """Select at most k projects to maximize capital"""
    import heapq
    
    n = len(profits)
    projects = sorted(zip(capital, profits))
    
    max_heap = []  # Max heap of profits for affordable projects
    i = 0
    
    for _ in range(k):
        # Add all affordable projects
        while i < n and projects[i][0] <= w:
            heapq.heappush(max_heap, -projects[i][1])
            i += 1
        
        if not max_heap:
            break
        
        w += -heapq.heappop(max_heap)
    
    return w
```

### Minimum Cost to Hire K Workers (LC 857)

```python
def min_cost_to_hire_workers(quality: list[int], wage: list[int], k: int) -> float:
    """Minimum cost to hire k workers fairly"""
    import heapq
    
    n = len(quality)
    
    # Sort by wage/quality ratio
    workers = sorted([(w / q, q) for w, q in zip(wage, quality)])
    
    result = float('inf')
    quality_sum = 0
    max_heap = []  # Max heap of qualities
    
    for ratio, q in workers:
        heapq.heappush(max_heap, -q)
        quality_sum += q
        
        if len(max_heap) > k:
            quality_sum += heapq.heappop(max_heap)  # Remove max quality
        
        if len(max_heap) == k:
            result = min(result, ratio * quality_sum)
    
    return result
```

---

## 📊 When to Use Greedy

| Criteria | Description |
|----------|-------------|
| Optimal Substructure | Optimal solution contains optimal solutions to subproblems |
| Greedy Choice Property | Local optimal choice leads to global optimal |
| No Backtracking | Decisions are final |

---

## 📝 Related Problems

| Problem | LC # | Pattern |
|---------|------|---------|
| Non-overlapping Intervals | 435 | Sort by End |
| Meeting Rooms II | 253 | Heap + Sorting |
| Jump Game | 55 | Max Reach |
| Jump Game II | 45 | BFS-like |
| Gas Station | 134 | Circular |
| Candy | 135 | Two Pass |
| Task Scheduler | 621 | Frequency |
| Partition Labels | 763 | Last Occurrence |
| Remove K Digits | 402 | Monotonic Stack |
| Two City Scheduling | 1029 | Sort by Difference |
| Queue Reconstruction | 406 | Sort + Insert |
| IPO | 502 | Heap |

---

*Last Updated: 2024*
