---
title: "Heap & Priority Queue Patterns"
topic: "Problem Patterns"
difficulty: "Medium"
tags: ["heap", "priority-queue", "top-k", "streaming"]
status: "complete"
weight: 20
---

# Heap & Priority Queue Patterns

## 📚 Overview

Heaps are essential for problems involving streaming data, top-k elements, and priority-based processing.

---

## 1️⃣ Top K Elements

### Kth Largest Element (LC 215)

```python
import heapq

def find_kth_largest(nums: list[int], k: int) -> int:
    """Find kth largest element - O(n log k)"""
    # Min heap of size k
    heap = nums[:k]
    heapq.heapify(heap)
    
    for num in nums[k:]:
        if num > heap[0]:
            heapq.heapreplace(heap, num)
    
    return heap[0]

def find_kth_largest_quickselect(nums: list[int], k: int) -> int:
    """Quickselect approach - O(n) average"""
    import random
    
    def partition(left: int, right: int, pivot_idx: int) -> int:
        pivot = nums[pivot_idx]
        nums[pivot_idx], nums[right] = nums[right], nums[pivot_idx]
        store_idx = left
        
        for i in range(left, right):
            if nums[i] > pivot:
                nums[i], nums[store_idx] = nums[store_idx], nums[i]
                store_idx += 1
        
        nums[store_idx], nums[right] = nums[right], nums[store_idx]
        return store_idx
    
    left, right = 0, len(nums) - 1
    target = k - 1
    
    while True:
        pivot_idx = random.randint(left, right)
        pivot_idx = partition(left, right, pivot_idx)
        
        if pivot_idx == target:
            return nums[pivot_idx]
        elif pivot_idx < target:
            left = pivot_idx + 1
        else:
            right = pivot_idx - 1
```

### Top K Frequent Elements (LC 347)

```python
def top_k_frequent(nums: list[int], k: int) -> list[int]:
    """Top k most frequent elements"""
    from collections import Counter
    
    freq = Counter(nums)
    
    # Method 1: Heap
    return heapq.nlargest(k, freq.keys(), key=freq.get)
    
def top_k_frequent_bucket(nums: list[int], k: int) -> list[int]:
    """Bucket sort approach - O(n)"""
    from collections import Counter
    
    freq = Counter(nums)
    
    # Bucket by frequency
    buckets = [[] for _ in range(len(nums) + 1)]
    for num, count in freq.items():
        buckets[count].append(num)
    
    result = []
    for i in range(len(buckets) - 1, -1, -1):
        result.extend(buckets[i])
        if len(result) >= k:
            return result[:k]
    
    return result
```

### K Closest Points to Origin (LC 973)

```python
def k_closest(points: list[list[int]], k: int) -> list[list[int]]:
    """K closest points to origin"""
    # Max heap to keep k smallest distances
    heap = []
    
    for x, y in points:
        dist = x*x + y*y
        
        if len(heap) < k:
            heapq.heappush(heap, (-dist, x, y))
        elif dist < -heap[0][0]:
            heapq.heapreplace(heap, (-dist, x, y))
    
    return [[x, y] for _, x, y in heap]
```

---

## 2️⃣ Merge K Sorted

### Merge K Sorted Lists (LC 23)

```python
def merge_k_lists(lists: list) -> 'ListNode':
    """Merge k sorted linked lists"""
    heap = []
    
    for i, lst in enumerate(lists):
        if lst:
            heapq.heappush(heap, (lst.val, i, lst))
    
    dummy = ListNode(0)
    current = dummy
    
    while heap:
        val, i, node = heapq.heappop(heap)
        current.next = node
        current = current.next
        
        if node.next:
            heapq.heappush(heap, (node.next.val, i, node.next))
    
    return dummy.next
```

### Smallest Range Covering Elements from K Lists (LC 632)

```python
def smallest_range(nums: list[list[int]]) -> list[int]:
    """Smallest range containing at least one element from each list"""
    heap = []
    max_val = float('-inf')
    
    # Initialize with first element from each list
    for i, lst in enumerate(nums):
        heapq.heappush(heap, (lst[0], i, 0))
        max_val = max(max_val, lst[0])
    
    result = [float('-inf'), float('inf')]
    
    while True:
        min_val, i, j = heapq.heappop(heap)
        
        if max_val - min_val < result[1] - result[0]:
            result = [min_val, max_val]
        
        if j + 1 == len(nums[i]):
            break
        
        next_val = nums[i][j + 1]
        heapq.heappush(heap, (next_val, i, j + 1))
        max_val = max(max_val, next_val)
    
    return result
```

---

## 3️⃣ Sliding Window with Heap

### Sliding Window Maximum (LC 239)

```python
def max_sliding_window(nums: list[int], k: int) -> list[int]:
    """Maximum in each sliding window of size k"""
    from collections import deque
    
    # Monotonic decreasing deque
    dq = deque()
    result = []
    
    for i, num in enumerate(nums):
        # Remove elements outside window
        while dq and dq[0] <= i - k:
            dq.popleft()
        
        # Maintain decreasing order
        while dq and nums[dq[-1]] < num:
            dq.pop()
        
        dq.append(i)
        
        if i >= k - 1:
            result.append(nums[dq[0]])
    
    return result

def max_sliding_window_heap(nums: list[int], k: int) -> list[int]:
    """Heap-based solution"""
    heap = []  # Max heap: (-value, index)
    result = []
    
    for i in range(len(nums)):
        heapq.heappush(heap, (-nums[i], i))
        
        if i >= k - 1:
            # Remove elements outside window
            while heap[0][1] <= i - k:
                heapq.heappop(heap)
            
            result.append(-heap[0][0])
    
    return result
```

### Sliding Window Median (LC 480)

```python
def median_sliding_window(nums: list[int], k: int) -> list[float]:
    """Median of each sliding window"""
    from sortedcontainers import SortedList
    
    window = SortedList(nums[:k])
    result = []
    
    def get_median():
        if k % 2 == 1:
            return float(window[k // 2])
        return (window[k // 2 - 1] + window[k // 2]) / 2
    
    result.append(get_median())
    
    for i in range(k, len(nums)):
        window.add(nums[i])
        window.remove(nums[i - k])
        result.append(get_median())
    
    return result
```

---

## 4️⃣ Two Heaps Pattern

### Find Median from Data Stream (LC 295)

```python
class MedianFinder:
    """Running median using two heaps"""
    def __init__(self):
        self.small = []  # Max heap (negated)
        self.large = []  # Min heap
    
    def addNum(self, num: int) -> None:
        # Add to max heap first
        heapq.heappush(self.small, -num)
        
        # Balance: largest of small should <= smallest of large
        if self.small and self.large and (-self.small[0] > self.large[0]):
            heapq.heappush(self.large, -heapq.heappop(self.small))
        
        # Balance sizes
        if len(self.small) > len(self.large) + 1:
            heapq.heappush(self.large, -heapq.heappop(self.small))
        elif len(self.large) > len(self.small):
            heapq.heappush(self.small, -heapq.heappop(self.large))
    
    def findMedian(self) -> float:
        if len(self.small) > len(self.large):
            return -self.small[0]
        return (-self.small[0] + self.large[0]) / 2
```

### IPO Problem (LC 502)

```python
def find_maximized_capital(k: int, w: int, profits: list[int], capital: list[int]) -> int:
    """Maximize capital after k projects"""
    n = len(profits)
    projects = sorted(zip(capital, profits))
    
    max_heap = []  # Available project profits (negated)
    i = 0
    
    for _ in range(k):
        # Add affordable projects
        while i < n and projects[i][0] <= w:
            heapq.heappush(max_heap, -projects[i][1])
            i += 1
        
        if not max_heap:
            break
        
        w += -heapq.heappop(max_heap)
    
    return w
```

---

## 5️⃣ Schedule/Meeting Problems

### Meeting Rooms II (LC 253)

```python
def min_meeting_rooms(intervals: list[list[int]]) -> int:
    """Minimum meeting rooms required"""
    intervals.sort()
    
    rooms = []  # End times
    
    for start, end in intervals:
        if rooms and rooms[0] <= start:
            heapq.heapreplace(rooms, end)
        else:
            heapq.heappush(rooms, end)
    
    return len(rooms)
```

### Task Scheduler (LC 621)

```python
def least_interval(tasks: list[str], n: int) -> int:
    """Minimum time to complete all tasks with cooldown"""
    from collections import Counter
    
    freq = Counter(tasks)
    max_heap = [-f for f in freq.values()]
    heapq.heapify(max_heap)
    
    time = 0
    cooldown = []  # (available_time, count)
    
    while max_heap or cooldown:
        time += 1
        
        if max_heap:
            count = heapq.heappop(max_heap) + 1  # Decrement (negated)
            if count < 0:
                cooldown.append((time + n, count))
        
        if cooldown and cooldown[0][0] == time:
            heapq.heappush(max_heap, cooldown.pop(0)[1])
    
    return time
```

---

## 6️⃣ Graph Shortest Path

### Network Delay Time (LC 743)

```python
def network_delay_time(times: list[list[int]], n: int, k: int) -> int:
    """Dijkstra's shortest path"""
    from collections import defaultdict
    
    graph = defaultdict(list)
    for u, v, w in times:
        graph[u].append((v, w))
    
    dist = {k: 0}
    heap = [(0, k)]
    
    while heap:
        d, node = heapq.heappop(heap)
        
        if d > dist.get(node, float('inf')):
            continue
        
        for neighbor, weight in graph[node]:
            new_dist = d + weight
            if new_dist < dist.get(neighbor, float('inf')):
                dist[neighbor] = new_dist
                heapq.heappush(heap, (new_dist, neighbor))
    
    return max(dist.values()) if len(dist) == n else -1
```

### Cheapest Flights Within K Stops (LC 787)

```python
def find_cheapest_price(n: int, flights: list[list[int]], src: int, dst: int, k: int) -> int:
    """Shortest path with at most k stops"""
    from collections import defaultdict
    
    graph = defaultdict(list)
    for u, v, w in flights:
        graph[u].append((v, w))
    
    # (cost, stops, node)
    heap = [(0, 0, src)]
    stops = [float('inf')] * n
    
    while heap:
        cost, num_stops, node = heapq.heappop(heap)
        
        if node == dst:
            return cost
        
        if num_stops > k or num_stops >= stops[node]:
            continue
        
        stops[node] = num_stops
        
        for neighbor, weight in graph[node]:
            heapq.heappush(heap, (cost + weight, num_stops + 1, neighbor))
    
    return -1
```

---

## 7️⃣ Greedy with Heap

### Minimum Cost to Connect Sticks (LC 1167)

```python
def connect_sticks(sticks: list[int]) -> int:
    """Minimum cost to connect all sticks"""
    heapq.heapify(sticks)
    total_cost = 0
    
    while len(sticks) > 1:
        first = heapq.heappop(sticks)
        second = heapq.heappop(sticks)
        cost = first + second
        total_cost += cost
        heapq.heappush(sticks, cost)
    
    return total_cost
```

### Maximum Performance of a Team (LC 1383)

```python
def max_performance(n: int, speed: list[int], efficiency: list[int], k: int) -> int:
    """Max sum(speed) * min(efficiency) with at most k engineers"""
    MOD = 10**9 + 7
    
    # Sort by efficiency descending
    engineers = sorted(zip(efficiency, speed), reverse=True)
    
    speed_heap = []  # Min heap of speeds
    speed_sum = 0
    max_perf = 0
    
    for eff, spd in engineers:
        heapq.heappush(speed_heap, spd)
        speed_sum += spd
        
        if len(speed_heap) > k:
            speed_sum -= heapq.heappop(speed_heap)
        
        max_perf = max(max_perf, speed_sum * eff)
    
    return max_perf % MOD
```

---

## 8️⃣ K-way Operations

### Find K Pairs with Smallest Sums (LC 373)

```python
def k_smallest_pairs(nums1: list[int], nums2: list[int], k: int) -> list[list[int]]:
    """K pairs with smallest sums from two sorted arrays"""
    if not nums1 or not nums2:
        return []
    
    heap = [(nums1[0] + nums2[0], 0, 0)]
    seen = {(0, 0)}
    result = []
    
    while heap and len(result) < k:
        _, i, j = heapq.heappop(heap)
        result.append([nums1[i], nums2[j]])
        
        if i + 1 < len(nums1) and (i + 1, j) not in seen:
            heapq.heappush(heap, (nums1[i + 1] + nums2[j], i + 1, j))
            seen.add((i + 1, j))
        
        if j + 1 < len(nums2) and (i, j + 1) not in seen:
            heapq.heappush(heap, (nums1[i] + nums2[j + 1], i, j + 1))
            seen.add((i, j + 1))
    
    return result
```

### Kth Smallest Element in a Sorted Matrix (LC 378)

```python
def kth_smallest(matrix: list[list[int]], k: int) -> int:
    """Kth smallest in row and column sorted matrix"""
    n = len(matrix)
    heap = [(matrix[0][0], 0, 0)]
    seen = {(0, 0)}
    
    for _ in range(k):
        val, r, c = heapq.heappop(heap)
        
        if r + 1 < n and (r + 1, c) not in seen:
            heapq.heappush(heap, (matrix[r + 1][c], r + 1, c))
            seen.add((r + 1, c))
        
        if c + 1 < n and (r, c + 1) not in seen:
            heapq.heappush(heap, (matrix[r][c + 1], r, c + 1))
            seen.add((r, c + 1))
    
    return val
```

---

## 9️⃣ Custom Comparator

### Sort Characters By Frequency (LC 451)

```python
def frequency_sort(s: str) -> str:
    """Sort characters by frequency descending"""
    from collections import Counter
    
    freq = Counter(s)
    heap = [(-count, char) for char, count in freq.items()]
    heapq.heapify(heap)
    
    result = []
    while heap:
        count, char = heapq.heappop(heap)
        result.append(char * (-count))
    
    return ''.join(result)
```

### Reorganize String (LC 767)

```python
def reorganize_string(s: str) -> str:
    """Rearrange so no adjacent chars same"""
    from collections import Counter
    
    freq = Counter(s)
    max_freq = max(freq.values())
    
    if max_freq > (len(s) + 1) // 2:
        return ""
    
    heap = [(-count, char) for char, count in freq.items()]
    heapq.heapify(heap)
    
    result = []
    prev_count, prev_char = 0, ''
    
    while heap:
        count, char = heapq.heappop(heap)
        result.append(char)
        
        if prev_count < 0:
            heapq.heappush(heap, (prev_count, prev_char))
        
        prev_count = count + 1
        prev_char = char
    
    return ''.join(result)
```

---

## 🔟 Lazy Deletion Pattern

### Design Twitter (LC 355)

```python
class Twitter:
    def __init__(self):
        self.time = 0
        self.tweets = {}      # user -> [(time, tweet_id)]
        self.following = {}   # user -> set of followees
    
    def postTweet(self, userId: int, tweetId: int) -> None:
        if userId not in self.tweets:
            self.tweets[userId] = []
        self.tweets[userId].append((self.time, tweetId))
        self.time += 1
    
    def getNewsFeed(self, userId: int) -> list[int]:
        # Get all relevant users
        users = {userId}
        if userId in self.following:
            users.update(self.following[userId])
        
        # Merge tweets using heap
        heap = []
        for user in users:
            if user in self.tweets:
                tweets = self.tweets[user]
                if tweets:
                    idx = len(tweets) - 1
                    heapq.heappush(heap, (-tweets[idx][0], tweets[idx][1], user, idx))
        
        result = []
        while heap and len(result) < 10:
            _, tweet_id, user, idx = heapq.heappop(heap)
            result.append(tweet_id)
            
            if idx > 0:
                idx -= 1
                t = self.tweets[user][idx]
                heapq.heappush(heap, (-t[0], t[1], user, idx))
        
        return result
    
    def follow(self, followerId: int, followeeId: int) -> None:
        if followerId not in self.following:
            self.following[followerId] = set()
        self.following[followerId].add(followeeId)
    
    def unfollow(self, followerId: int, followeeId: int) -> None:
        if followerId in self.following:
            self.following[followerId].discard(followeeId)
```

---

## 📊 Complexity Summary

| Operation | Min Heap | Max Heap |
|-----------|----------|----------|
| Insert | O(log n) | O(log n) |
| Extract | O(log n) | O(log n) |
| Peek | O(1) | O(1) |
| Heapify | O(n) | O(n) |

---

## 📝 Related Problems

| Problem | LC # | Pattern |
|---------|------|---------|
| Kth Largest Element | 215 | Top K |
| Top K Frequent Elements | 347 | Top K |
| Merge K Sorted Lists | 23 | K-way Merge |
| Sliding Window Maximum | 239 | Deque/Heap |
| Find Median from Data Stream | 295 | Two Heaps |
| Meeting Rooms II | 253 | Schedule |
| Task Scheduler | 621 | Greedy + Heap |
| Network Delay Time | 743 | Dijkstra |
| K Closest Points | 973 | Top K |
| Reorganize String | 767 | Greedy |

---

*Last Updated: 2024*
