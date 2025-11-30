---
title: "Interval & Scheduling Patterns"
topic: "Problem Patterns"
difficulty: "Medium"
tags: ["intervals", "scheduling", "sweep-line", "merge", "greedy"]
status: "complete"
weight: 16
---

# Interval & Scheduling Patterns

## 📚 Overview

Interval problems are extremely common. Master sorting + greedy approaches.

---

## 1️⃣ Merge Intervals (LC 56)

```python
def merge(intervals: list[list[int]]) -> list[list[int]]:
    """Merge overlapping intervals"""
    if not intervals:
        return []
    
    intervals.sort(key=lambda x: x[0])
    merged = [intervals[0]]
    
    for start, end in intervals[1:]:
        if start <= merged[-1][1]:
            merged[-1][1] = max(merged[-1][1], end)
        else:
            merged.append([start, end])
    
    return merged
```

---

## 2️⃣ Insert Interval (LC 57)

```python
def insert(intervals: list[list[int]], newInterval: list[int]) -> list[list[int]]:
    """Insert interval and merge if necessary"""
    result = []
    i = 0
    n = len(intervals)
    
    # Add intervals before new interval
    while i < n and intervals[i][1] < newInterval[0]:
        result.append(intervals[i])
        i += 1
    
    # Merge overlapping intervals
    while i < n and intervals[i][0] <= newInterval[1]:
        newInterval[0] = min(newInterval[0], intervals[i][0])
        newInterval[1] = max(newInterval[1], intervals[i][1])
        i += 1
    result.append(newInterval)
    
    # Add remaining intervals
    result.extend(intervals[i:])
    
    return result
```

---

## 3️⃣ Non-Overlapping Intervals (LC 435)

```python
def erase_overlap_intervals(intervals: list[list[int]]) -> int:
    """Minimum removals to make non-overlapping"""
    if not intervals:
        return 0
    
    # Sort by end time (greedy: keep earliest ending)
    intervals.sort(key=lambda x: x[1])
    
    removals = 0
    prev_end = intervals[0][1]
    
    for i in range(1, len(intervals)):
        if intervals[i][0] < prev_end:
            removals += 1  # Remove this interval
        else:
            prev_end = intervals[i][1]
    
    return removals
```

---

## 4️⃣ Meeting Rooms

### Meeting Rooms I (LC 252)

```python
def can_attend_meetings(intervals: list[list[int]]) -> bool:
    """Check if person can attend all meetings"""
    intervals.sort()
    
    for i in range(1, len(intervals)):
        if intervals[i][0] < intervals[i-1][1]:
            return False
    
    return True
```

### Meeting Rooms II - Min Rooms (LC 253)

```python
def min_meeting_rooms(intervals: list[list[int]]) -> int:
    """Minimum conference rooms needed"""
    if not intervals:
        return 0
    
    # Sweep line algorithm
    events = []
    for start, end in intervals:
        events.append((start, 1))   # Meeting starts
        events.append((end, -1))    # Meeting ends
    
    events.sort()
    
    max_rooms = current = 0
    for _, delta in events:
        current += delta
        max_rooms = max(max_rooms, current)
    
    return max_rooms

# Alternative: Min-heap
import heapq

def min_meeting_rooms_heap(intervals: list[list[int]]) -> int:
    intervals.sort()
    heap = []  # End times
    
    for start, end in intervals:
        if heap and heap[0] <= start:
            heapq.heappop(heap)  # Reuse room
        heapq.heappush(heap, end)
    
    return len(heap)
```

---

## 5️⃣ Interval Intersection (LC 986)

```python
def interval_intersection(
    firstList: list[list[int]], 
    secondList: list[list[int]]
) -> list[list[int]]:
    """Find intersection of two interval lists"""
    result = []
    i = j = 0
    
    while i < len(firstList) and j < len(secondList):
        a_start, a_end = firstList[i]
        b_start, b_end = secondList[j]
        
        # Find intersection
        start = max(a_start, b_start)
        end = min(a_end, b_end)
        
        if start <= end:
            result.append([start, end])
        
        # Move pointer with smaller end
        if a_end < b_end:
            i += 1
        else:
            j += 1
    
    return result
```

---

## 6️⃣ Minimum Arrows to Burst Balloons (LC 452)

```python
def find_min_arrow_shots(points: list[list[int]]) -> int:
    """Minimum arrows to burst all balloons"""
    if not points:
        return 0
    
    points.sort(key=lambda x: x[1])  # Sort by end
    
    arrows = 1
    arrow_pos = points[0][1]
    
    for start, end in points[1:]:
        if start > arrow_pos:
            arrows += 1
            arrow_pos = end
    
    return arrows
```

---

## 7️⃣ Sweep Line Algorithm

### Count Points in Intervals

```python
def count_points(intervals: list[list[int]], queries: list[int]) -> list[int]:
    """For each query point, count intervals containing it"""
    events = []
    
    for i, (start, end) in enumerate(intervals):
        events.append((start, 0, i))    # Start
        events.append((end, 2, i))      # End
    
    for i, q in enumerate(queries):
        events.append((q, 1, i))        # Query
    
    events.sort()
    
    active = set()
    result = [0] * len(queries)
    
    for pos, event_type, idx in events:
        if event_type == 0:
            active.add(idx)
        elif event_type == 2:
            active.discard(idx)
        else:
            result[idx] = len(active)
    
    return result
```

### Rectangle Area (LC 850)

```python
def rectangle_area(rectangles: list[list[int]]) -> int:
    """Total area covered by rectangles"""
    MOD = 10**9 + 7
    
    # Compress y-coordinates
    ys = sorted(set([y for x1, y1, x2, y2 in rectangles for y in [y1, y2]]))
    y_idx = {y: i for i, y in enumerate(ys)}
    
    # Sweep line from left to right
    events = []
    for x1, y1, x2, y2 in rectangles:
        events.append((x1, 1, y1, y2))   # Start
        events.append((x2, -1, y1, y2))  # End
    
    events.sort()
    
    def calc_y_coverage(count):
        """Calculate total y coverage from count array"""
        total = 0
        for i in range(len(count)):
            if count[i] > 0:
                total += ys[i + 1] - ys[i]
        return total
    
    count = [0] * len(ys)
    prev_x = events[0][0]
    area = 0
    
    for x, delta, y1, y2 in events:
        y_coverage = calc_y_coverage(count)
        area = (area + y_coverage * (x - prev_x)) % MOD
        
        for i in range(y_idx[y1], y_idx[y2]):
            count[i] += delta
        
        prev_x = x
    
    return area
```

---

## 8️⃣ Activity Selection (Maximum Non-Overlapping)

```python
def max_non_overlapping(intervals: list[list[int]]) -> int:
    """Maximum number of non-overlapping intervals"""
    if not intervals:
        return 0
    
    # Greedy: always pick earliest ending
    intervals.sort(key=lambda x: x[1])
    
    count = 1
    end = intervals[0][1]
    
    for i in range(1, len(intervals)):
        if intervals[i][0] >= end:
            count += 1
            end = intervals[i][1]
    
    return count
```

---

## 9️⃣ Interval Coverage

### Minimum Intervals to Cover Range

```python
def min_intervals_to_cover(intervals: list[list[int]], target: list[int]) -> int:
    """Minimum intervals to cover [target[0], target[1]]"""
    intervals.sort()
    
    target_start, target_end = target
    count = 0
    i = 0
    current_end = target_start
    
    while current_end < target_end:
        max_reach = current_end
        
        # Find interval with best reach starting at or before current_end
        while i < len(intervals) and intervals[i][0] <= current_end:
            max_reach = max(max_reach, intervals[i][1])
            i += 1
        
        if max_reach == current_end:
            return -1  # Can't extend
        
        current_end = max_reach
        count += 1
    
    return count
```

### Video Stitching (LC 1024)

```python
def video_stitching(clips: list[list[int]], time: int) -> int:
    """Minimum clips to cover [0, time]"""
    clips.sort()
    
    count = 0
    current_end = 0
    i = 0
    
    while current_end < time:
        max_reach = current_end
        
        while i < len(clips) and clips[i][0] <= current_end:
            max_reach = max(max_reach, clips[i][1])
            i += 1
        
        if max_reach == current_end:
            return -1
        
        current_end = max_reach
        count += 1
    
    return count
```

---

## 🔟 Employee Free Time (LC 759)

```python
def employee_free_time(schedule: list[list[list[int]]]) -> list[list[int]]:
    """Find common free time for all employees"""
    # Flatten and sort all intervals
    intervals = []
    for emp in schedule:
        intervals.extend(emp)
    
    intervals.sort()
    
    # Merge intervals
    merged = [intervals[0]]
    for start, end in intervals[1:]:
        if start <= merged[-1][1]:
            merged[-1][1] = max(merged[-1][1], end)
        else:
            merged.append([start, end])
    
    # Find gaps
    free_time = []
    for i in range(1, len(merged)):
        free_time.append([merged[i-1][1], merged[i][0]])
    
    return free_time
```

---

## 📝 Related Problems

| Problem | LC # | Pattern |
|---------|------|---------|
| Merge Intervals | 56 | Sort + Merge |
| Insert Interval | 57 | Binary Search + Merge |
| Non-overlapping Intervals | 435 | Greedy |
| Meeting Rooms | 252 | Overlap Check |
| Meeting Rooms II | 253 | Sweep Line / Heap |
| Interval List Intersections | 986 | Two Pointers |
| Min Arrows for Balloons | 452 | Greedy |
| Minimum Interval to Include | 1851 | Sort + Heap |
| Video Stitching | 1024 | Greedy Coverage |
| Employee Free Time | 759 | Merge + Gaps |
| My Calendar I/II/III | 729/731/732 | Sweep Line |
| Remove Covered Intervals | 1288 | Sort + Greedy |

---

*Last Updated: 2024*
