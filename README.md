# Practical 6

## 1. Missionaries and Cannibals

```python
from collections import deque


def is_valid(state):
    missionaries_left, cannibals_left, boat = state
    missionaries_right = 3 - missionaries_left
    cannibals_right = 3 - cannibals_left

    if not (0 <= missionaries_left <= 3 and
            0 <= cannibals_left <= 3):
        return False

    if missionaries_left > 0 and missionaries_left < cannibals_left:
        return False

    if missionaries_right > 0 and missionaries_right < cannibals_right:
        return False

    return True


def get_next_states(state):
    missionaries_left, cannibals_left, boat = state

    possible_moves = [
        (1, 0),
        (2, 0),
        (0, 1),
        (0, 2),
        (1, 1)
    ]

    next_states = []

    for missionaries, cannibals in possible_moves:

        if boat == 0:
            new_state = (
                missionaries_left - missionaries,
                cannibals_left - cannibals,
                1
            )
        else:
            new_state = (
                missionaries_left + missionaries,
                cannibals_left + cannibals,
                0
            )

        if is_valid(new_state):
            next_states.append(new_state)

    return next_states


def bfs():
    initial_state = (3, 3, 0)
    goal_state = (0, 0, 1)

    queue = deque([(initial_state, [initial_state])])
    visited = {initial_state}

    while queue:

        state, path = queue.popleft()

        if state == goal_state:
            return path

        for next_state in get_next_states(state):

            if next_state not in visited:
                visited.add(next_state)
                queue.append((next_state, path + [next_state]))

    return None


solution = bfs()

print("Solution Path:")

for state in solution:
    print(state)
```

## 2. 8-Puzzle using BFS

```python
from collections import deque


def get_neighbors(state):
    neighbors = []

    zero_index = state.index(0)

    row = zero_index // 3
    col = zero_index % 3

    moves = [
        (-1, 0),
        (1, 0),
        (0, -1),
        (0, 1)
    ]

    for dr, dc in moves:

        new_row = row + dr
        new_col = col + dc

        if 0 <= new_row < 3 and 0 <= new_col < 3:

            new_index = new_row * 3 + new_col

            new_state = list(state)

            new_state[zero_index], new_state[new_index] = (
                new_state[new_index],
                new_state[zero_index]
            )

            neighbors.append(tuple(new_state))

    return neighbors


def bfs(start, goal):

    queue = deque([(start, [start])])
    visited = {start}

    while queue:

        current, path = queue.popleft()

        if current == goal:
            return path

        for neighbor in get_neighbors(current):

            if neighbor not in visited:
                visited.add(neighbor)

                queue.append(
                    (neighbor, path + [neighbor])
                )

    return None


start = (
    1, 2, 3,
    4, 0, 6,
    7, 5, 8
)

goal = (
    1, 2, 3,
    4, 5, 6,
    7, 8, 0
)

solution = bfs(start, goal)

print("Solution:")

for state in solution:

    for i in range(0, 9, 3):
        print(state[i:i + 3])

    print()
```

# 7A Tic-Tac-Toe

```markdown
# Practical 7A

```python
board = ['' for _ in range(9)]
player = 'X'


def show_board():
    print()
    print(f" {board[0]} | {board[1]} | {board[2]} ")
    print("---+---+---")
    print(f" {board[3]} | {board[4]} | {board[5]} ")
    print("---+---+---")
    print(f" {board[6]} | {board[7]} | {board[8]} ")
    print()


def is_winner(p):
    return (
        (board[0] == p and board[1] == p and board[2] == p) or
        (board[3] == p and board[4] == p and board[5] == p) or
        (board[6] == p and board[7] == p and board[8] == p) or
        (board[0] == p and board[3] == p and board[6] == p) or
        (board[1] == p and board[4] == p and board[7] == p) or
        (board[2] == p and board[5] == p and board[8] == p) or
        (board[0] == p and board[4] == p and board[8] == p) or
        (board[2] == p and board[4] == p and board[6] == p)
    )


def is_tie():
    return '' not in board


def game():
    global player

    while True:
        show_board()

        try:
            move = int(input(
                f"Player {player}, enter a position (0-8): "
            ))

            if 0 <= move <= 8 and board[move] == '':
                board[move] = player

                if is_winner(player):
                    show_board()
                    print(f"Player {player} wins!")
                    break

                elif is_tie():
                    show_board()
                    print("It's a tie!")
                    break

                if player == 'X':
                    player = 'O'
                else:
                    player = 'X'

            else:
                print("Invalid move. Try again.")

        except ValueError:
            print("Invalid input. Please enter a number between 0 and 8.")


game()

# 7B  Shuffle Cards

```python
import random

# Step 1: Create the deck
suits = ['Hearts', 'Diamonds', 'Clubs', 'Spades']
ranks = ['2', '3', '4', '5', '6', '7', '8', '9', '10', 'Jack', 'Queen', 'King', 'Ace']

# Combine suits and ranks
deck = [rank + " of " + suit for suit in suits for rank in ranks]

# Step 2: Shuffle the deck
random.shuffle(deck)

# Step 3: Display the shuffled deck
print("Shuffled Deck of Cards:")

for card in deck:
    print(card)
```

# 8 Constraint Satisfaction Problem (Map Coloring).

```python
import itertools

variables = ['A', 'B', 'C']
colors = ['Red', 'Blue', 'Yellow']

all_assignments = itertools.product(
    colors, repeat=len(variables)
)

def valid(c):
    A = c['A']
    B = c['B']
    C = c['C']

    return (A != B) and (B != C) and (A != C)

solutions = []

for p in all_assignments:
    sol = dict(zip(variables, p))

    if valid(sol):
        solutions.append(sol)

print("Valid Colorings of the map:")

for sol in solutions:
    print(sol)
```
