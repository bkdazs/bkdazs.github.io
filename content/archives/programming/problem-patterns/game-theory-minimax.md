---
title: "Game Theory and Minimax"
topic: "Problem Patterns"
difficulty: "Hard"
tags: ["game-theory", "minimax", "alpha-beta", "nim", "sprague-grundy"]
status: "complete"
weight: 10
---

# Game Theory and Minimax

## 📚 Summary

Game theory problems involve two players making optimal moves. Key patterns: Minimax with alpha-beta pruning, Nim game theory, Sprague-Grundy theorem, and game state DP.

---

## 1️⃣ Minimax Algorithm

```python
"""
Minimax: Two players - Maximizer and Minimizer
Maximizer wants to maximize score
Minimizer wants to minimize score

Each player assumes opponent plays optimally
"""

def minimax(position, depth: int, is_maximizing: bool, 
            evaluate, get_moves, is_terminal) -> int:
    """
    Basic Minimax implementation
    """
    if depth == 0 or is_terminal(position):
        return evaluate(position)
    
    if is_maximizing:
        max_eval = float('-inf')
        for move in get_moves(position):
            new_position = make_move(position, move)
            eval_score = minimax(new_position, depth - 1, False,
                                evaluate, get_moves, is_terminal)
            max_eval = max(max_eval, eval_score)
        return max_eval
    else:
        min_eval = float('inf')
        for move in get_moves(position):
            new_position = make_move(position, move)
            eval_score = minimax(new_position, depth - 1, True,
                                evaluate, get_moves, is_terminal)
            min_eval = min(min_eval, eval_score)
        return min_eval
```

---

## 2️⃣ Alpha-Beta Pruning

```python
def minimax_alpha_beta(position, depth: int, alpha: float, beta: float,
                       is_maximizing: bool, evaluate, get_moves, is_terminal) -> int:
    """
    Minimax with Alpha-Beta Pruning
    Alpha: Best value maximizer can guarantee
    Beta: Best value minimizer can guarantee
    
    Prune when alpha >= beta (no need to explore further)
    """
    if depth == 0 or is_terminal(position):
        return evaluate(position)
    
    if is_maximizing:
        max_eval = float('-inf')
        for move in get_moves(position):
            new_position = make_move(position, move)
            eval_score = minimax_alpha_beta(new_position, depth - 1, 
                                           alpha, beta, False,
                                           evaluate, get_moves, is_terminal)
            max_eval = max(max_eval, eval_score)
            alpha = max(alpha, eval_score)
            if beta <= alpha:
                break  # Beta cutoff
        return max_eval
    else:
        min_eval = float('inf')
        for move in get_moves(position):
            new_position = make_move(position, move)
            eval_score = minimax_alpha_beta(new_position, depth - 1,
                                           alpha, beta, True,
                                           evaluate, get_moves, is_terminal)
            min_eval = min(min_eval, eval_score)
            beta = min(beta, eval_score)
            if beta <= alpha:
                break  # Alpha cutoff
        return min_eval
```

---

## 3️⃣ Stone Game Problems

### Stone Game (LC 877)

```python
def stone_game(piles: list[int]) -> bool:
    """
    Two players pick from ends, first player wins if score > opponent
    Alex always wins with even piles (mathematical proof)
    """
    # Mathematical: With even piles, first player can always choose
    # to take all odd-indexed or all even-indexed piles
    return True


def stone_game_dp(piles: list[int]) -> bool:
    """
    DP approach - works for any case
    dp[i][j] = max score difference (current player - opponent) for piles[i:j+1]
    """
    n = len(piles)
    dp = [[0] * n for _ in range(n)]
    
    # Single pile
    for i in range(n):
        dp[i][i] = piles[i]
    
    # Increasing length
    for length in range(2, n + 1):
        for i in range(n - length + 1):
            j = i + length - 1
            dp[i][j] = max(
                piles[i] - dp[i + 1][j],  # Take left
                piles[j] - dp[i][j - 1]   # Take right
            )
    
    return dp[0][n - 1] > 0
```

### Stone Game II (LC 1140)

```python
def stone_game_ii(piles: list[int]) -> int:
    """
    Can take 1 to 2M piles, M updates to max(M, taken)
    Return max stones Alice can get
    """
    n = len(piles)
    
    # Suffix sum
    suffix = [0] * (n + 1)
    for i in range(n - 1, -1, -1):
        suffix[i] = suffix[i + 1] + piles[i]
    
    memo = {}
    
    def dp(i: int, m: int) -> int:
        """Max stones current player can get from position i with M=m"""
        if i >= n:
            return 0
        
        if (i, m) in memo:
            return memo[(i, m)]
        
        # If can take all remaining
        if i + 2 * m >= n:
            return suffix[i]
        
        # Try taking x piles (1 to 2*m)
        min_opponent = float('inf')
        for x in range(1, 2 * m + 1):
            min_opponent = min(min_opponent, dp(i + x, max(m, x)))
        
        # Current player gets: total remaining - opponent's best
        result = suffix[i] - min_opponent
        memo[(i, m)] = result
        return result
    
    return dp(0, 1)
```

### Stone Game III (LC 1406)

```python
def stone_game_iii(values: list[int]) -> str:
    """
    Take 1, 2, or 3 stones from front
    Return "Alice", "Bob", or "Tie"
    """
    n = len(values)
    
    # dp[i] = max score difference for player starting at i
    dp = [0] * (n + 1)
    
    for i in range(n - 1, -1, -1):
        dp[i] = float('-inf')
        take = 0
        for k in range(1, 4):
            if i + k - 1 < n:
                take += values[i + k - 1]
                dp[i] = max(dp[i], take - dp[i + k])
    
    if dp[0] > 0:
        return "Alice"
    elif dp[0] < 0:
        return "Bob"
    else:
        return "Tie"
```

### Stone Game IV (LC 1510)

```python
def winner_square_game(n: int) -> bool:
    """
    Remove perfect square stones, player with no move loses
    Return True if Alice wins
    """
    dp = [False] * (n + 1)
    
    for i in range(1, n + 1):
        # Check all perfect square moves
        j = 1
        while j * j <= i:
            if not dp[i - j * j]:  # Opponent loses from that state
                dp[i] = True
                break
            j += 1
    
    return dp[n]
```

---

## 4️⃣ Nim Game Theory

```python
"""
Nim Game: Multiple piles, take any number from one pile
Player who can't move loses

Nim-Sum (XOR of all pile sizes):
- If XOR = 0: Current player loses with optimal play
- If XOR ≠ 0: Current player wins with optimal play

Winning move: Leave opponent with XOR = 0
"""

def can_win_nim(piles: list[int]) -> bool:
    """
    Check if current player can win Nim game
    """
    xor_sum = 0
    for pile in piles:
        xor_sum ^= pile
    return xor_sum != 0


def find_winning_move_nim(piles: list[int]) -> tuple[int, int]:
    """
    Find winning move: (pile_index, stones_to_remove)
    Returns (-1, -1) if no winning move
    """
    xor_sum = 0
    for pile in piles:
        xor_sum ^= pile
    
    if xor_sum == 0:
        return (-1, -1)  # No winning move
    
    for i, pile in enumerate(piles):
        # After move, pile becomes: pile - removed
        # We want: (xor_sum ^ pile ^ (pile - removed)) = 0
        # So: pile - removed = xor_sum ^ pile
        target = xor_sum ^ pile
        if target < pile:
            return (i, pile - target)
    
    return (-1, -1)


# LC 292: Nim Game - Single pile, take 1-3
def can_win_nim_single(n: int) -> bool:
    """
    Take 1, 2, or 3 stones. Return True if first player wins.
    Pattern: Lose if n % 4 == 0
    """
    return n % 4 != 0
```

---

## 5️⃣ Sprague-Grundy Theorem

```python
"""
Sprague-Grundy (Nimber) Theory:
- Every impartial game position has a Grundy number (nimber)
- G(position) = mex({G(reachable positions)})
- mex = minimum excludant = smallest non-negative integer not in set
- Position is losing if G = 0, winning if G ≠ 0
- For combined games: XOR of Grundy numbers
"""

def compute_grundy(n: int, moves: list[int]) -> list[int]:
    """
    Compute Grundy numbers for positions 0 to n
    moves: list of possible stone removals
    """
    grundy = [0] * (n + 1)
    
    for i in range(1, n + 1):
        reachable = set()
        for move in moves:
            if i >= move:
                reachable.add(grundy[i - move])
        
        # Compute mex
        mex = 0
        while mex in reachable:
            mex += 1
        grundy[i] = mex
    
    return grundy


def can_win_multi_pile(piles: list[int], moves: list[int]) -> bool:
    """
    Multi-pile game with restricted moves
    Uses Sprague-Grundy theorem
    """
    max_pile = max(piles)
    grundy = compute_grundy(max_pile, moves)
    
    xor_sum = 0
    for pile in piles:
        xor_sum ^= grundy[pile]
    
    return xor_sum != 0
```

---

## 6️⃣ Predict the Winner (LC 486)

```python
def predict_winner(nums: list[int]) -> bool:
    """
    Two players pick from ends
    Return True if player 1 can win or tie
    """
    n = len(nums)
    memo = {}
    
    def dp(i: int, j: int) -> int:
        """Score difference (current - opponent) for nums[i:j+1]"""
        if i > j:
            return 0
        
        if (i, j) in memo:
            return memo[(i, j)]
        
        # Take left or right
        take_left = nums[i] - dp(i + 1, j)
        take_right = nums[j] - dp(i, j - 1)
        
        memo[(i, j)] = max(take_left, take_right)
        return memo[(i, j)]
    
    return dp(0, n - 1) >= 0
```

---

## 7️⃣ Can I Win (LC 464)

```python
def can_i_win(max_choosable: int, desired_total: int) -> bool:
    """
    Choose numbers 1 to max_choosable without replacement
    First to reach desired_total wins
    """
    # Quick checks
    if desired_total <= 0:
        return True
    
    total_sum = max_choosable * (max_choosable + 1) // 2
    if total_sum < desired_total:
        return False
    
    memo = {}
    
    def can_win(used: int, remaining: int) -> bool:
        """
        used: bitmask of used numbers
        remaining: target sum to reach
        """
        if (used, remaining) in memo:
            return memo[(used, remaining)]
        
        for i in range(1, max_choosable + 1):
            if used & (1 << i):
                continue  # Already used
            
            # Win immediately or opponent loses
            if i >= remaining or not can_win(used | (1 << i), remaining - i):
                memo[(used, remaining)] = True
                return True
        
        memo[(used, remaining)] = False
        return False
    
    return can_win(0, desired_total)
```

---

## 8️⃣ Tic-Tac-Toe Optimal Play

```python
def tic_tac_toe_minimax(board: list[list[str]]) -> tuple[int, int]:
    """
    Find optimal move for current player (X)
    Returns (row, col) of best move
    """
    def check_winner(b: list[list[str]]) -> str:
        """Return 'X', 'O', or '' """
        # Check rows and columns
        for i in range(3):
            if b[i][0] == b[i][1] == b[i][2] != '':
                return b[i][0]
            if b[0][i] == b[1][i] == b[2][i] != '':
                return b[0][i]
        
        # Check diagonals
        if b[0][0] == b[1][1] == b[2][2] != '':
            return b[0][0]
        if b[0][2] == b[1][1] == b[2][0] != '':
            return b[0][2]
        
        return ''
    
    def is_full(b: list[list[str]]) -> bool:
        return all(b[i][j] != '' for i in range(3) for j in range(3))
    
    def minimax(b: list[list[str]], is_x: bool) -> int:
        winner = check_winner(b)
        if winner == 'X':
            return 1
        if winner == 'O':
            return -1
        if is_full(b):
            return 0
        
        if is_x:
            best = float('-inf')
            for i in range(3):
                for j in range(3):
                    if b[i][j] == '':
                        b[i][j] = 'X'
                        best = max(best, minimax(b, False))
                        b[i][j] = ''
            return best
        else:
            best = float('inf')
            for i in range(3):
                for j in range(3):
                    if b[i][j] == '':
                        b[i][j] = 'O'
                        best = min(best, minimax(b, True))
                        b[i][j] = ''
            return best
    
    # Find best move for X
    best_score = float('-inf')
    best_move = (-1, -1)
    
    for i in range(3):
        for j in range(3):
            if board[i][j] == '':
                board[i][j] = 'X'
                score = minimax(board, False)
                board[i][j] = ''
                
                if score > best_score:
                    best_score = score
                    best_move = (i, j)
    
    return best_move
```

---

## 9️⃣ Flip Game II (LC 294)

```python
def can_win_flip(current_state: str) -> bool:
    """
    Flip "++" to "--", player who can't move loses
    """
    memo = {}
    
    def can_win(state: str) -> bool:
        if state in memo:
            return memo[state]
        
        for i in range(len(state) - 1):
            if state[i:i + 2] == "++":
                # Make move
                new_state = state[:i] + "--" + state[i + 2:]
                if not can_win(new_state):
                    memo[state] = True
                    return True
        
        memo[state] = False
        return False
    
    return can_win(current_state)
```

---

## 🔟 Game State Pattern Recognition

```python
"""
Key patterns for game theory problems:

1. Turn-based optimal play:
   - dp[state] = best outcome for current player
   - Current wins if ANY move leads to opponent losing
   - Current loses if ALL moves lead to opponent winning

2. Score difference games:
   - dp[i][j] = score_current - score_opponent
   - Positive = current player ahead
   
3. Win/Lose state:
   - Losing state: All reachable states are winning (for opponent)
   - Winning state: At least one reachable state is losing

4. Nim-like games:
   - XOR of Grundy numbers
   - XOR = 0 means current player loses

5. Interval games (pick from ends):
   - dp[i][j] for subarray/interval
   - Try left and right choices
"""
```

---

## ⏱️ Complexity

| Algorithm | Time | Space |
|-----------|------|-------|
| Minimax | O(b^d) | O(d) |
| Alpha-Beta | O(b^(d/2)) best | O(d) |
| Game DP | O(states × moves) | O(states) |
| Grundy Numbers | O(n × moves) | O(n) |

Where b = branching factor, d = depth

---

## 📚 Practice Problems

| # | Problem | Key Concept |
|---|---------|-------------|
| 1 | [LC 292: Nim Game](https://leetcode.com/problems/nim-game/) | Math pattern |
| 2 | [LC 877: Stone Game](https://leetcode.com/problems/stone-game/) | Interval DP |
| 3 | [LC 1140: Stone Game II](https://leetcode.com/problems/stone-game-ii/) | Variable moves |
| 4 | [LC 1406: Stone Game III](https://leetcode.com/problems/stone-game-iii/) | Take 1-3 |
| 5 | [LC 1510: Stone Game IV](https://leetcode.com/problems/stone-game-iv/) | Perfect squares |
| 6 | [LC 486: Predict Winner](https://leetcode.com/problems/predict-the-winner/) | Score diff |
| 7 | [LC 464: Can I Win](https://leetcode.com/problems/can-i-win/) | Bitmask |
| 8 | [LC 294: Flip Game II](https://leetcode.com/problems/flip-game-ii/) | String game |
| 9 | [LC 375: Guess Number II](https://leetcode.com/problems/guess-number-higher-or-lower-ii/) | Minimax |
| 10 | [LC 843: Guess the Word](https://leetcode.com/problems/guess-the-word/) | Interactive |
| 11 | [LC 913: Cat and Mouse](https://leetcode.com/problems/cat-and-mouse/) | Graph game |
| 12 | [LC 1025: Divisor Game](https://leetcode.com/problems/divisor-game/) | Math |
| 13 | [LC 1690: Stone Game VII](https://leetcode.com/problems/stone-game-vii/) | Variant |
| 14 | [LC 1563: Stone Game V](https://leetcode.com/problems/stone-game-v/) | Split |
| 15 | [LC 1686: Stone Game VI](https://leetcode.com/problems/stone-game-vi/) | Greedy |

---

*Last Updated: 2024*
