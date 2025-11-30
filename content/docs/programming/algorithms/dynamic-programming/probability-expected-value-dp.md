---
title: Probability & Expected Value DP
topic: Algorithms - Dynamic Programming
difficulty: Hard
tags: [dp, probability, expected-value, stochastic, markov]
status: complete
weight: 17
---

# Probability & Expected Value DP

## 1. Overview

### Core Concept
Probability DP deals with problems involving random events, computing probabilities of outcomes or expected values. The key is modeling state transitions with their probabilities.

### Key Formulas
```
Expected Value: E[X] = Σ (value × probability)

Linearity of Expectation: E[X + Y] = E[X] + E[Y]

Probability of OR (mutually exclusive): P(A ∪ B) = P(A) + P(B)

Probability of AND (independent): P(A ∩ B) = P(A) × P(B)
```

### Common Patterns
| Pattern | Description | Example |
|---------|-------------|---------|
| Forward probability | P(reaching state) | Random walk |
| Backward expectation | E[cost from state] | Dice games |
| Markov chain | State transitions | Page rank |
| Absorption probability | P(ending in state) | Gambler's ruin |

---

## 2. Basic Probability DP

### Dice Roll Probability

```python
from typing import List
from functools import lru_cache

def probability_of_sum(n: int, faces: int, target: int) -> float:
    """
    Probability of getting exactly target sum with n dice of k faces.
    
    Time: O(n × target × faces)
    """
    # dp[i][s] = probability of getting sum s with i dice
    dp = [[0.0] * (target + 1) for _ in range(n + 1)]
    dp[0][0] = 1.0
    
    prob_per_face = 1.0 / faces
    
    for i in range(1, n + 1):
        for s in range(i, min(i * faces, target) + 1):
            for face in range(1, min(faces, s) + 1):
                dp[i][s] += dp[i - 1][s - face] * prob_per_face
    
    return dp[n][target]


def probability_at_least(n: int, faces: int, target: int) -> float:
    """
    Probability of getting at least target sum.
    """
    max_sum = n * faces
    if target > max_sum:
        return 0.0
    
    dp = [[0.0] * (max_sum + 1) for _ in range(n + 1)]
    dp[0][0] = 1.0
    
    prob_per_face = 1.0 / faces
    
    for i in range(1, n + 1):
        for s in range(i, i * faces + 1):
            for face in range(1, faces + 1):
                if s - face >= 0:
                    dp[i][s] += dp[i - 1][s - face] * prob_per_face
    
    return sum(dp[n][target:])


# Test
print(f"P(sum=7 with 2d6): {probability_of_sum(2, 6, 7):.4f}")  # 0.1667
```

---

## 3. Expected Value Problems

### Soup Servings

```python
def soup_servings(n: int) -> float:
    """
    LeetCode 808 - Soup Servings
    
    Two soups A, B start with n ml each. Each operation:
    - (100,0), (75,25), (50,50), (25,75) with equal probability
    
    Return P(A empties first) + 0.5 × P(both empty simultaneously)
    
    Time: O(n²) but converges for large n
    """
    # For large n, probability approaches 1
    if n > 4800:
        return 1.0
    
    # Convert to units of 25ml
    n = (n + 24) // 25
    
    @lru_cache(maxsize=None)
    def dp(a: int, b: int) -> float:
        if a <= 0 and b <= 0:
            return 0.5  # Both empty
        if a <= 0:
            return 1.0  # A empty first
        if b <= 0:
            return 0.0  # B empty first
        
        return 0.25 * (
            dp(a - 4, b) +      # (100, 0)
            dp(a - 3, b - 1) +  # (75, 25)
            dp(a - 2, b - 2) +  # (50, 50)
            dp(a - 1, b - 3)    # (25, 75)
        )
    
    return dp(n, n)


# Test
print(f"soup_servings(50): {soup_servings(50):.5f}")
```

### Knight Probability on Chessboard

```python
def knight_probability(n: int, k: int, row: int, column: int) -> float:
    """
    LeetCode 688 - Knight Probability in Chessboard
    
    Probability that knight stays on n×n board after k moves.
    
    Time: O(k × n²)
    """
    moves = [(-2, -1), (-2, 1), (-1, -2), (-1, 2),
             (1, -2), (1, 2), (2, -1), (2, 1)]
    
    # dp[i][j] = probability of being at (i, j)
    dp = [[0.0] * n for _ in range(n)]
    dp[row][column] = 1.0
    
    for _ in range(k):
        new_dp = [[0.0] * n for _ in range(n)]
        for i in range(n):
            for j in range(n):
                if dp[i][j] > 0:
                    for di, dj in moves:
                        ni, nj = i + di, j + dj
                        if 0 <= ni < n and 0 <= nj < n:
                            new_dp[ni][nj] += dp[i][j] / 8
        dp = new_dp
    
    return sum(sum(row) for row in dp)


# Test
print(f"knight_probability(3, 2, 0, 0): {knight_probability(3, 2, 0, 0):.5f}")
```

---

## 4. Expected Number of Trials

### Expected Dice Rolls to Get All Faces

```python
def expected_rolls_all_faces(n: int) -> float:
    """
    Expected number of rolls to see all n faces of a fair die.
    
    This is the Coupon Collector Problem.
    
    E = n × H(n) = n × (1 + 1/2 + 1/3 + ... + 1/n)
    
    Or by DP:
    E[k faces seen] = expected rolls to see all n starting with k seen
    """
    # Analytical: Coupon Collector
    expected = 0.0
    for i in range(1, n + 1):
        expected += n / i
    return expected


def expected_rolls_dp(n: int) -> float:
    """
    DP approach for generalized problems.
    
    dp[k] = expected rolls to complete starting with k unique faces seen
    """
    # dp[n] = 0 (done)
    # dp[k] = 1 + (k/n) × dp[k] + ((n-k)/n) × dp[k+1]
    # Solving: dp[k] = n/(n-k) + dp[k+1]
    
    dp = [0.0] * (n + 1)
    
    for k in range(n - 1, -1, -1):
        dp[k] = n / (n - k) + dp[k + 1]
    
    return dp[0]


# Test
print(f"Expected rolls for 6-sided die: {expected_rolls_all_faces(6):.2f}")  # ~14.7
```

### Expected Steps in Random Walk

```python
def expected_steps_to_boundary(start: int, left: int, right: int, p: float = 0.5) -> float:
    """
    Expected steps for random walk starting at 'start' to reach
    left boundary (≤left) or right boundary (≥right).
    
    At each step: move right with probability p, left with probability 1-p.
    
    Time: O(right - left)
    """
    if start <= left or start >= right:
        return 0.0
    
    n = right - left
    pos = start - left  # Normalize to [0, n]
    
    # E[i] = expected steps from position i
    # E[i] = 1 + p × E[i+1] + (1-p) × E[i-1]
    # Boundary: E[0] = E[n] = 0
    
    if abs(p - 0.5) < 1e-9:
        # Symmetric case: E[i] = i × (n - i)
        return pos * (n - pos)
    
    # General case: solve system of equations
    q = 1 - p
    r = q / p
    
    # E[i] = (n - i × (1 - r^n) / (1 - r^i)) / (p - q) for p ≠ 0.5
    # Simplified formula for absorbing boundaries
    if abs(r - 1) < 1e-9:
        return pos * (n - pos)
    
    # Using generating function solution
    expected = (pos - n * (1 - r**pos) / (1 - r**n)) / (q - p)
    return expected


def expected_steps_absorbing(states: int, start: int, absorbing: set, 
                            transitions: List[List[float]]) -> float:
    """
    Expected steps to reach any absorbing state from start.
    
    General Markov chain approach.
    
    Time: O(n³) for matrix operations
    """
    import numpy as np
    
    n = states
    transient = [i for i in range(n) if i not in absorbing]
    t = len(transient)
    
    # Q = transition matrix among transient states
    Q = np.zeros((t, t))
    for i, si in enumerate(transient):
        for j, sj in enumerate(transient):
            Q[i][j] = transitions[si][sj]
    
    # Fundamental matrix N = (I - Q)^(-1)
    I = np.eye(t)
    N = np.linalg.inv(I - Q)
    
    # Expected steps from each transient state
    ones = np.ones(t)
    expected = N @ ones
    
    start_idx = transient.index(start) if start in transient else -1
    return expected[start_idx] if start_idx >= 0 else 0.0
```

---

## 5. Game Theory with Probability

### New 21 Game

```python
def new21_game(n: int, k: int, max_pts: int) -> float:
    """
    LeetCode 837 - New 21 Game
    
    Draw cards 1 to maxPts with equal probability until points ≥ k.
    Return probability that points ≤ n.
    
    Time: O(n + maxPts)
    """
    if k == 0 or n >= k + max_pts:
        return 1.0
    
    # dp[i] = probability of reaching exactly i points
    dp = [0.0] * (n + 1)
    dp[0] = 1.0
    
    window_sum = 1.0  # Sum of dp[i-maxPts:i]
    
    for i in range(1, n + 1):
        dp[i] = window_sum / max_pts
        
        if i < k:
            window_sum += dp[i]
        if i >= max_pts:
            window_sum -= dp[i - max_pts]
    
    return sum(dp[k:n + 1])


# Test
print(f"new21_game(21, 17, 10): {new21_game(21, 17, 10):.5f}")
```

### Probability of Winning

```python
def probability_of_winning(my_cards: List[int], opponent_cards: List[int]) -> float:
    """
    Probability of winning in a card comparison game.
    
    Each player draws one card, higher wins.
    Ties resolved by redraw.
    """
    wins = 0
    ties = 0
    total = len(my_cards) * len(opponent_cards)
    
    for my_card in my_cards:
        for opp_card in opponent_cards:
            if my_card > opp_card:
                wins += 1
            elif my_card == opp_card:
                ties += 1
    
    # P(win) = wins/total + ties/total × P(win)
    # P(win) × (1 - ties/total) = wins/total
    # P(win) = wins / (total - ties)
    
    if total == ties:
        return 0.5  # All ties
    return wins / (total - ties)
```

---

## 6. Markov Chain Expected Value

### Expected Visits Before Absorption

```python
def expected_visits(n: int, transitions: List[List[float]], 
                   absorbing: set, start: int, target: int) -> float:
    """
    Expected number of visits to 'target' state before absorption.
    
    Time: O(n³)
    """
    import numpy as np
    
    transient = [i for i in range(n) if i not in absorbing]
    t = len(transient)
    
    if target in absorbing:
        return 0.0  # Can't visit absorbing state multiple times
    
    # Q matrix (transient to transient transitions)
    Q = np.zeros((t, t))
    for i, si in enumerate(transient):
        for j, sj in enumerate(transient):
            Q[i][j] = transitions[si][sj]
    
    # Fundamental matrix
    N = np.linalg.inv(np.eye(t) - Q)
    
    start_idx = transient.index(start)
    target_idx = transient.index(target)
    
    return N[start_idx][target_idx]


def absorption_probabilities(n: int, transitions: List[List[float]], 
                            absorbing: set, start: int) -> dict:
    """
    Probability of ending in each absorbing state.
    
    Returns: dict mapping absorbing state to probability
    """
    import numpy as np
    
    transient = [i for i in range(n) if i not in absorbing]
    absorbing_list = list(absorbing)
    t = len(transient)
    a = len(absorbing_list)
    
    # Q matrix and R matrix
    Q = np.zeros((t, t))
    R = np.zeros((t, a))
    
    for i, si in enumerate(transient):
        for j, sj in enumerate(transient):
            Q[i][j] = transitions[si][sj]
        for j, sj in enumerate(absorbing_list):
            R[i][j] = transitions[si][sj]
    
    # Absorption probability matrix B = N × R
    N = np.linalg.inv(np.eye(t) - Q)
    B = N @ R
    
    start_idx = transient.index(start) if start in transient else -1
    
    if start_idx < 0:
        # Start is absorbing
        return {s: (1.0 if s == start else 0.0) for s in absorbing_list}
    
    return {absorbing_list[j]: B[start_idx][j] for j in range(a)}
```

---

## 7. Probability in Grid/Path Problems

### Paths with Random Turns

```python
def probability_path(grid: List[List[int]], k: int) -> float:
    """
    Probability of reaching bottom-right in exactly k steps.
    At each step, randomly choose right or down (if valid).
    
    Time: O(mnk)
    """
    m, n = len(grid), len(grid[0])
    
    # dp[i][j][s] = probability of being at (i,j) after s steps
    dp = [[[0.0] * (k + 1) for _ in range(n)] for _ in range(m)]
    dp[0][0][0] = 1.0
    
    for s in range(k):
        for i in range(m):
            for j in range(n):
                if dp[i][j][s] == 0:
                    continue
                
                # Count valid moves
                valid_moves = 0
                if i + 1 < m:
                    valid_moves += 1
                if j + 1 < n:
                    valid_moves += 1
                
                if valid_moves == 0:
                    continue
                
                prob = dp[i][j][s] / valid_moves
                
                if i + 1 < m:
                    dp[i + 1][j][s + 1] += prob
                if j + 1 < n:
                    dp[i][j + 1][s + 1] += prob
    
    return dp[m - 1][n - 1][k]


def expected_path_length(m: int, n: int) -> float:
    """
    Expected path length from (0,0) to (m-1, n-1).
    At each cell, randomly go right or down.
    
    Answer: E = (m-1) + (n-1) = m + n - 2 (deterministic!)
    
    The path length is always the same regardless of choices.
    """
    return m + n - 2
```

---

## 8. Expected Value with Memoization

### Toss Strange Coins

```python
def probability_of_heads(prob: List[float], target: int) -> float:
    """
    LeetCode 1230 - Toss Strange Coins
    
    prob[i] = probability that coin i shows heads.
    Return probability of exactly target heads.
    
    Time: O(n × target)
    """
    n = len(prob)
    
    # dp[i][j] = probability of j heads with first i coins
    dp = [[0.0] * (target + 1) for _ in range(n + 1)]
    dp[0][0] = 1.0
    
    for i in range(1, n + 1):
        p = prob[i - 1]
        for j in range(min(i, target) + 1):
            # Don't get head on coin i
            dp[i][j] = dp[i - 1][j] * (1 - p)
            # Get head on coin i
            if j > 0:
                dp[i][j] += dp[i - 1][j - 1] * p
    
    return dp[n][target]


# Test
print(f"probability_of_heads([0.4], 1): {probability_of_heads([0.4], 1):.1f}")  # 0.4
```

### Expected Flip Count

```python
def expected_flips_to_pattern(pattern: str) -> float:
    """
    Expected coin flips to see pattern (e.g., "HHH").
    
    Uses Markov chain / automaton approach.
    """
    n = len(pattern)
    
    # Build failure function (KMP-like)
    fail = [0] * n
    j = 0
    for i in range(1, n):
        while j > 0 and pattern[i] != pattern[j]:
            j = fail[j - 1]
        if pattern[i] == pattern[j]:
            j += 1
        fail[i] = j
    
    # E[i] = expected flips from state i (i characters matched)
    # E[n] = 0 (done)
    # E[i] = 1 + 0.5 × E[next_H] + 0.5 × E[next_T]
    
    @lru_cache(maxsize=None)
    def expected(matched: int) -> float:
        if matched == n:
            return 0.0
        
        # Flip H
        if pattern[matched] == 'H':
            next_h = matched + 1
        else:
            j = matched
            while j > 0 and pattern[j] != 'H':
                j = fail[j - 1]
            next_h = j + 1 if pattern[j] == 'H' else 0
        
        # Flip T
        if pattern[matched] == 'T':
            next_t = matched + 1
        else:
            j = matched
            while j > 0 and pattern[j] != 'T':
                j = fail[j - 1]
            next_t = j + 1 if pattern[j] == 'T' else 0
        
        return 1 + 0.5 * expected(next_h) + 0.5 * expected(next_t)
    
    return expected(0)


# Test
print(f"Expected flips for HH: {expected_flips_to_pattern('HH'):.1f}")  # 6.0
print(f"Expected flips for HT: {expected_flips_to_pattern('HT'):.1f}")  # 4.0
```

---

## 9. Random Process Simulation Validation

```python
import random

def validate_expected_value(func, args, simulations: int = 100000) -> tuple:
    """
    Validate expected value calculation via Monte Carlo simulation.
    
    Returns: (calculated, simulated, relative_error)
    """
    calculated = func(*args)
    
    # Simulation logic depends on the problem
    # This is a template
    simulated_sum = 0
    
    for _ in range(simulations):
        # Simulate one trial and add result
        # This needs to be customized per problem
        pass
    
    simulated = simulated_sum / simulations
    error = abs(calculated - simulated) / max(calculated, 1e-9)
    
    return calculated, simulated, error


def simulate_dice_rolls_for_sum(n: int, faces: int, target: int, trials: int = 100000) -> float:
    """
    Monte Carlo simulation for dice sum probability.
    """
    count = 0
    for _ in range(trials):
        total = sum(random.randint(1, faces) for _ in range(n))
        if total == target:
            count += 1
    return count / trials
```

---

## 10. Practice Problems

### LeetCode Problems

| # | Problem | Pattern | Difficulty |
|---|---------|---------|------------|
| 576 | [Out of Boundary Paths](https://leetcode.com/problems/out-of-boundary-paths/) | Grid probability | Medium |
| 688 | [Knight Probability in Chessboard](https://leetcode.com/problems/knight-probability-in-chessboard/) | Grid probability | Medium |
| 808 | [Soup Servings](https://leetcode.com/problems/soup-servings/) | Expected value | Medium |
| 837 | [New 21 Game](https://leetcode.com/problems/new-21-game/) | Game probability | Medium |
| 1025 | [Divisor Game](https://leetcode.com/problems/divisor-game/) | Game theory | Easy |
| 1139 | [Largest 1-Bordered Square](https://leetcode.com/problems/largest-1-bordered-square/) | Related DP | Medium |
| 1227 | [Airplane Seat Assignment](https://leetcode.com/problems/airplane-seat-assignment-probability/) | Probability | Medium |
| 1230 | [Toss Strange Coins](https://leetcode.com/problems/toss-strange-coins/) | Coin probability | Medium |
| 1467 | [Probability of Two Boxes](https://leetcode.com/problems/probability-of-a-two-boxes-having-the-same-number-of-distinct-balls/) | Counting + prob | Hard |
| 1540 | [Can Convert String in K Moves](https://leetcode.com/problems/can-convert-string-in-k-moves/) | Related | Medium |
| 1641 | [Count Sorted Vowel Strings](https://leetcode.com/problems/count-sorted-vowel-strings/) | Counting | Medium |
| 1751 | [Maximum Number of Events II](https://leetcode.com/problems/maximum-number-of-events-that-can-be-attended-ii/) | Scheduling | Hard |

### Classic Problems

| Problem | Pattern | Notes |
|---------|---------|-------|
| Gambler's Ruin | Absorption | P(win/lose all) |
| Coupon Collector | Expected trials | E[collect all] |
| Random Walk | Hitting time | E[reach boundary] |
| Ehrenfest Urn | Markov chain | E[equilibrium] |
| Secretary Problem | Optimal stopping | Best candidate |

---

## 11. Key Patterns Summary

```
Probability/Expected Value DP Framework:

1. Forward Probability:
   - P[state] = Σ P[prev_state] × P(transition)
   - Start from initial state, propagate forward
   
2. Backward Expectation:
   - E[state] = cost + Σ P(action) × E[next_state]
   - Start from terminal states (E=0), work backward
   
3. Linearity of Expectation:
   - E[X + Y] = E[X] + E[Y] (even if dependent!)
   - Often simplifies complex problems
   
4. Absorbing Markov Chains:
   - N = (I - Q)^(-1) is fundamental matrix
   - N[i][j] = expected visits to j starting from i
   
5. Convergence:
   - For large state spaces, probability often converges
   - Can return early (e.g., soup_servings for n > 4800)

Common Pitfalls:
- Floating point precision
- Not handling edge cases (p=0, p=1)
- Forgetting to normalize probabilities
- Off-by-one in state transitions
```

---

## 12. References

1. "Introduction to Probability" - Bertsekas & Tsitsiklis
2. "Markov Chains and Stochastic Stability" - Meyn & Tweedie
3. CP-Algorithms: Expected Value
4. Codeforces Blog: Probability Problems
