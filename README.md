#1a Depth First Search (DFS)


import collections

def dfs(g, n, seen, d):
    if n not in seen:
        seen.append(n)

        for i in g[n]:
            if seen[-1] == d:
                break
            dfs(g, i, seen, d)

    return seen


graph = {
    'M': ['R', 'Q', 'N'],
    'N': ['M', 'Q', 'O'],
    'O': ['N', 'P'],
    'R': ['M'],
    'Q': ['M', 'N', 'P'],
    'P': ['O', 'Q']
}

print(dfs(graph, 'M', [], 'P'))
--------------------------------------------------------
#1b BFS - Breadth First Search

import collections


def bfs(graph, root):
    seen, queue = set([root]), collections.deque([root])

    while queue:
        vertex = queue.popleft()
        visit(vertex)

        for node in graph[vertex]:
            if node not in seen:
                seen.add(node)
                queue.append(node)


def all_path(st, end, gr):
    todo = [(st, [st])]

    while len(todo):
        node, path = todo.pop(0)

        for next_node in gr[node]:
            if next_node in path:
                continue

            if next_node == end:
                yield path + [next_node]
            else:
                todo.append((next_node, path + [next_node]))


def visit(n):
    print(n)


def bfs_shortest_path(graph, source, destination):
    checked = []
    queue = [[source]]

    if source == destination:
        return "Source is Destination"

    while queue:
        path = queue.pop(0)
        node = path[-1]

        if node not in checked:
            neighbours = graph[node]

            for neighbour in neighbours:
                new_path = list(path)
                new_path.append(neighbour)
                queue.append(new_path)

                if neighbour == destination:
                    return new_path

            checked.append(node)

    return "Path does not exist"


graph = {
    'A': ['B', 'D'],
    'B': ['C', 'F'],
    'C': ['E', 'G', 'H'],
    'G': ['E', 'H'],
    'E': ['B', 'F'],
    'F': ['A'],
    'D': ['F'],
    'H': ['A']
}


print("Graph Traversal")
bfs(graph, 'A')

print("\n\nAll paths are:")

for x in all_path('A', 'E', graph):
    print(x)

print("\nShortest path of Graph is:",
      bfs_shortest_path(graph, 'A', 'E'))
------------------------------------------------------------
#2a N-Queens Problem


def print_board(board):
    for row in board:
        print(" ".join(row))
    print()


def check_q(board, row, col, n):
    for i in range(row):
        if board[i][col] == 'Q':
            return False

    i, j = row, col

    while i >= 0 and j >= 0:
        if board[i][j] == 'Q':
            return False
        i -= 1
        j -= 1

    i, j = row, col

    while i >= 0 and j < n:
        if board[i][j] == 'Q':
            return False
        i -= 1
        j += 1

    return True


def solve_queens(board, row, n):
    if row == n:
        print_board(board)
        return True

    for col in range(n):

        if check_q(board, row, col, n):
            board[row][col] = 'Q'

            if solve_queens(board, row + 1, n):
                return True

            board[row][col] = '.'

    return False


def queens():
    n = int(input("Enter value of N: "))

    board = []

    for i in range(n):
        row = []

        for j in range(n):
            row.append('.')

        board.append(row)

    if not solve_queens(board, 0, n):
        print("No solution found")


queens()
-------------------------------------------
#2b tower of hanoi

A = [3, 2, 1]
B = []
C = []

def display():
    print("\nA:", A)
    print("B:", B)
    print("C:", C)


rods = {'A': A, 'B': B, 'C': C}
moves = 0

while C != [3, 2, 1]:
    display()

    source = input("\nMove from (A/B/C): ").upper()
    destination = input("Move to (A/B/C): ").upper()

    if source not in rods or destination not in rods:
        print("Invalid rod! Choose A, B, or C")
        continue

    if not rods[source]:
        print("Source rod is empty!")
        continue

    if rods[destination] and rods[destination][-1] < rods[source][-1]:
        print("Invalid Move! Larger disk cannot be placed on a smaller disk.")
        continue

    disk = rods[source].pop()
    rods[destination].append(disk)
    moves += 1

display()

print("\nCongratulations! You solved the Tower of Hanoi!!!")
print("Total moves:", moves)

##3a Alpha-Beta Pruning


tree = {
    'A': ['B', 'C'],
    'B': ['D', 'E'],
    'C': ['F', 'G'],
    'D': [4, 3],
    'E': [6, 2],
    'F': [2, 1],
    'G': [9, 5]
}


def minimax_alpha_beta(node, depth, alpha, beta, max_player):

    if depth == 0:
        if node in tree:
            return tree[node][0] if max_player else tree[node][0]
        else:
            return node

    if max_player:
        value = float('-inf')

        for child in tree[node]:
            value = max(
                value,
                minimax_alpha_beta(
                    child, depth - 1, alpha, beta, False
                )
            )

            alpha = max(alpha, value)

            if beta <= alpha:
                print(f"Pruning branch at node {node}")
                break

        return value

    else:
        value = float('inf')

        for child in tree[node]:
            value = min(
                value,
                minimax_alpha_beta(
                    child, depth - 1, alpha, beta, True
                )
            )

            beta = min(beta, value)

            if beta <= alpha:
                print(f"Pruning branch at node {node}")
                break

        return value


best_score = minimax_alpha_beta(
    'A',
    3,
    float('-inf'),
    float('inf'),
    True
)

print(f"The best score is: {best_score}")

----------------------------------------------------
#3b Hill Climbing


import random

distance = [
    [0, 2, 9, 10],
    [2, 0, 6, 4],
    [9, 6, 0, 3],
    [10, 4, 3, 0]
]


def get_cost(tour):
    cost = 0

    for i in range(len(tour)):
        cost += distance[tour[i - 1]][tour[i]]
        print("Cost of tour", cost)

    return cost


def get_neighbor(tour):
    a, b = random.sample(range(len(tour)), 2)

    tour[a], tour[b] = tour[b], tour[a]

    return tour


def hill_climb():

    current = [0, 1, 2, 3]
    random.shuffle(current)

    current_cost = get_cost(current)

    print("Starting tour:", current, "Cost:", current_cost)

    for i in range(10):

        neighbor = current[:]

        print("Current neighbor:", neighbor)

        neighbor = get_neighbor(neighbor)

        print("Connected neighbor:", neighbor)

        neighbor_cost = get_cost(neighbor)

        if neighbor_cost < current_cost:

            current = neighbor

            print("Current neighbor:", current)

            current_cost = neighbor_cost

            print("Current Cost:", current_cost)

            print(
                "Better tour found:",
                current,
                "Cost:",
                current_cost
            )

    return current, current_cost


best_tour, best_cost = hill_climb()

print("\nBest tour:", best_tour)
print("\nBest Cost:", best_cost)
---------------------------------------------------------------------
#4a.A* Algorithm

graph = {
    'A': ({'B': 5, 'C': 1}, 6),
    'B': ({'C': 1}, 2),
    'C': ({'E': 2}, 5),
    'D': ({'E': 1}, 3),
    'E': ({'F': 2}, 2),
    'F': ({}, 0)
}


def get_min(q):
    mn = None
    min_value = float('inf')

    for i in q:
        value = q[i][0] + q[i][1]

        if value < min_value:
            min_value = value
            mn = i

    return mn


def a_star(graph, prev, dst, path, pcost, q):
    mn = get_min(q)

    if mn is None:
        return []

    q.pop(mn)

    print("Connected nodes of current node", prev, "with h(n) value")

    for n in graph[prev][0]:

        if n not in path:

            h = graph[n][1]
            edge_cost = graph[prev][0][n]

            q[n] = (h, edge_cost)

            print(n, "-->", q[n])

            add1 = h + edge_cost
            path_cost = pcost + add1

            print("A* value for", n, "is:", path_cost)

    while q:

        mn = get_min(q)

        print("Selecting minimum vertex:", mn)
        print("------------------------------")

        if dst == mn:
            return path + [dst]

        pc = pcost + q[mn][1]

        print("Previous path cost:", pc)

        new_path = a_star(
            graph,
            mn,
            dst,
            path + [mn],
            pc,
            q
        )

        if new_path:
            return new_path

    return []


source = input("Enter source vertex: ")
dest = input("Enter destination vertex: ")
heuristic = int(input("Enter given heuristic value for source: "))

if source not in graph or dest not in graph:
    print("Invalid source or destination vertex")
else:
    path = a_star(
        graph,
        source,
        dest,
        [],
        0,
        {source: (heuristic, 0)}
    )

    if path:
        print("Path:", path)
    else:
        print("Path not found")
--------------------------------------------------------
#4b Greedy Best First Search

graph = {
    'S': ({'A': 2, 'E': 3}, 6),
    'A': ({'S': 2, 'D': 1}, 3),
    'B': ({'C': 3, 'D': 3}, 2),
    'C': ({'B': 3, 'G': 2}, 2),
    'D': ({'A': 1, 'B': 3, 'G': 2}, 4),
    'E': ({'S': 3, 'G': 2}, 5),
    'G': ({}, 0)
}


def greedy_search_rec(graph, prev, dest, path, q):

    neighbours = graph[prev][0].keys()

    for n in neighbours:
        if n not in path:
            q[n] = graph[n][1]
            print(n, "->", q[n])

    while q:

        mn = min(q, key=q.get)

        print("Taking minimum h(n) vertex:", mn)

        if dest == mn:
            return path + [dest]

        del q[mn]

        new_path = greedy_search_rec(
            graph,
            mn,
            dest,
            path + [mn],
            q
        )

        if new_path:
            return new_path

    return []


source = input("Enter source vertex: ")

if source not in graph:
    print("Invalid source vertex")
else:
    result = greedy_search_rec(
        graph,
        source,
        'G',
        [source],
        {}
    )

    if result:
        print("Resulting path:", result)
    else:
        print("Path not found")
-------------------------------------------------
#5a. Water Jug Prblm using bfs

from collections import deque

def is_visited(state, visited):
    return state in visited

def water_jug_bfs():
    max_a, max_b = 6, 5
    visited = set()
    queue = deque()

    queue.append((0, 0))

    while queue:
        a, b = queue.popleft()

        if (a, b) in visited:
            continue

        visited.add((a, b))
        print(f"Jug A: {a}L, Jug B: {b}L")

        if a == 3 or b == 3:
            print("Found a solution!")
            return

        # All possible actions from current state
        possible_states = [
            (max_a, b),
            (a, max_b),
            (0, b),
            (a, 0),
            (min(a + b, max_a), b - (min(a + b, max_a) - a)),
            (a - (min(a + b, max_b) - b), min(a + b, max_b))
        ]

        for state in possible_states:
            if state not in visited:
                queue.append(state)

    print("No solution found!")

water_jug_bfs()
-----------------------------------------------------------
##5b. Travelling Salesman Problem

from itertools import permutations

dist = [
    [0, 10, 15, 20],
    [10, 0, 35, 25],
    [15, 35, 0, 30],
    [20, 25, 30, 0]
]

n = len(dist)
cities = range(1, n)

min_distance = float('inf')
best_path = None

for path in permutations(cities):
    current_path = (0,) + path + (0,)
    distance = 0

    for i in range(len(current_path) - 1):
        distance += dist[current_path[i]][current_path[i + 1]]

    if distance < min_distance:
        min_distance = distance
        best_path = current_path

print("Shortest path distance:", min_distance)
print("Best path:", best_path)
-----------------------------------------------------------
# 6a. Missionaries and Cannibals


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

----------------------------------------------------------
## 6b 8-Puzzle using BFS


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

----------------------------------------------------------
## 7A Tic-Tac-Toe


# Practical 7A


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
------------------------------------------
## 7B  Shuffle Cards


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

-----------------------------------------------------------
## 8 Constraint Satisfaction Problem (Map Coloring).


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

##9a Associative law

def associative():
    # Associative Law for Addition
    print("Associative law for Addition")

    a = int(input("Enter value of a: "))
    b = int(input("Enter value of b: "))
    c = int(input("Enter value of c: "))

    lhs = a + (b + c)
    rhs = (a + b) + c

    if lhs == rhs:
        print("LHS =", lhs)
        print("RHS =", rhs)
        print("Associative law satisfied")
    else:
        print("Associative law not satisfied")


    # Associative Law for Multiplication
    print("Associative law for Multiplication")

    a = int(input("Enter value of a: "))
    b = int(input("Enter value of b: "))
    c = int(input("Enter value of c: "))

    lhs = a * (b * c)
    rhs = (a * b) * c

    if lhs == rhs:
        print("LHS =", lhs)
        print("RHS =", rhs)
        print("Associative law satisfied")
    else:
        print("Associative law not satisfied")


    # Associative Law for Boolean AND
    print("Associative law for Boolean AND")

    a = int(input("Enter value of a: "))
    b = int(input("Enter value of b: "))
    c = int(input("Enter value of c: "))

    lhs = a and (b and c)
    rhs = (a and b) and c

    if lhs == rhs:
        print("Associative law satisfied")
    else:
        print("Associative law not satisfied")


    # Associative Law for Boolean OR
    print("Associative law for Boolean OR")

    a = int(input("Enter value of a: "))
    b = int(input("Enter value of b: "))
    c = int(input("Enter value of c: "))

    lhs = a or (b or c)
    rhs = (a or b) or c

    if lhs == rhs:
        print("Associative law satisfied")
    else:
        print("Associative law not satisfied")


associative()
-----------------------------------------
##9b Distributive Law

def distributive():
    a = int(input("Enter value of a: "))
    b = int(input("Enter value of b: "))
    c = int(input("Enter value of c: "))

    print("Distributive law of multiplication over Addition")
    lhs = a * (b + c)
    rhs = (a * b) + (a * c)

    print("LHS =", lhs)
    print("RHS =", rhs)
    print("Distributive law satisfied" if lhs == rhs
          else "Distributive law not satisfied")


    print("Distributive law of Addition over multiplication")
    lhs = a + (b * c)
    rhs = (a + b) * (a + c)

    print("LHS =", lhs)
    print("RHS =", rhs)
    print("Distributive law satisfied" if lhs == rhs
          else "Distributive law not satisfied")


    print("Distributive law of Boolean AND over OR")
    lhs = a and (b or c)
    rhs = (a and b) or (a and c)

    print("LHS =", lhs)
    print("RHS =", rhs)
    print("Distributive law satisfied" if lhs == rhs
          else "Distributive law not satisfied")


    print("Distributive law of Boolean OR over AND")
    lhs = a or (b and c)
    rhs = (a or b) and (a or c)

    print("LHS =", lhs)
    print("RHS =", rhs)
    print("Distributive law satisfied" if lhs == rhs
          else "Distributive law not satisfied")


distributive()
-----------------------------------------------------------------------
# PRACTICAL 10 - PREDICATES PROLOG 

## Q1. Batsman → Cricketer → Sportsman → Famous Person

Write a Prolog program with the following:

### Facts:
Define at least four batsmen (e.g. Sachin, Virat, Rohit, Dhoni).

### Rules:
1. If X is a batsman, then X is a cricketer.
2. If X is a cricketer, then X is a sportsman.
3. If X is a sportsman, then X is a famous person.

### Queries:
1. Check if Sachin is a cricketer.
2. Check if Virat is a sportsman.
3. List all famous persons.

### Code:

```prolog
batsman(sachin).
batsman(virat).
batsman(dhoni).
batsman(rohit).

cricketer(X) :- batsman(X).
sportsman(X) :- cricketer(X).
famous(X) :- sportsman(X).
```

### Queries:


?- cricketer(sachin).
true.

?- sportsman(virat).
true.

?- famous(X).
X = sachin ;
X = virat ;
X = dhoni ;
X = rohit.
```

---

## Q2. Teacher → Employee → Human → Living Being

Write a Prolog program with the following:

### Facts:
Define at least four teachers (e.g. Anita, Raj, Meena, Rahul).

### Rules:
1. A teacher is an employee.
2. An employee is a human.
3. A human is a living being.

### Queries:
1. Prove that Anita is a human.
2. Prove that Rahul is a living being.
3. List all humans.

### Code:

```prolog
teacher(anita).
teacher(raj).
teacher(meena).
teacher(rahul).

employee(X) :- teacher(X).
human(X) :- employee(X).
livingbeing(X) :- human(X).


### Queries:


?- human(anita).
true.

?- livingbeing(rahul).
true.

?- human(X).
X = anita ;
X = raj ;
X = meena ;
X = rahul.
```

---

## Q3. Student → Learner → Knowledge Seeker → Future Professional

Write a Prolog program using the following relationship:

**Student → Learner → Knowledge Seeker → Future Professional**

### Define four students:

Riya, Amit, Sam and Neha.

### Code:


student(riya).
student(amit).
student(sam).
student(neha).

learner(X) :- student(X).
knowledgeseeker(X) :- learner(X).
futureprofessional(X) :- knowledgeseeker(X).


### Queries:


?- learner(riya).
true.

?- futureprofessional(amit).
true.

?- knowledgeseeker(X).
X = riya ;
X = amit ;
X = sam ;
X = neha.
```

---

## Q4. Dog → Animal → Pet → Living Being

Write a Prolog program using the following relationship:

**Dog → Animal → Pet → Living Being**

### Define four dogs:

Tommy, Bruno, Lucy and Rocky.

### Code:


dog(tommy).
dog(bruno).
dog(lucy).
dog(rocky).

animal(X) :- dog(X).
pet(X) :- animal(X).
livingbeing(X) :- pet(X).


### Queries:


?- pet(tommy).
true.

?- livingbeing(bruno).
true.

?- livingbeing(X).
X = tommy ;
X = bruno ;
X = lucy ;
X = rocky.
```

---

## Q5. Book → Knowledge Source → Educational Material → Valuable Resource

Write a Prolog program using the following relationship:

**Book → Knowledge Source → Educational Material → Valuable Resource**

### Define four books:

Physics, Math, History and Computer.

### Code:


book(physics).
book(math).
book(history).
book(computer).

knowledgesource(X) :- book(X).
educationalmaterial(X) :- knowledgesource(X).
valuableresource(X) :- educationalmaterial(X).


### Queries:


?- educationalmaterial(math).
true.

?- valuableresource(physics).
true.

?- valuableresource(X).
X = physics ;
X = math ;
X = history ;
X = computer.
```

---

## Q6. Family Tree

Write a Prolog program to represent family relationships using `male`, `female` and `parent` facts.

Define rules for:

- Father
- Mother
- Grandfather
- Grandmother
- Sibling
- Ancestor

### Code:


male(john).
male(mike).
male(david).

female(lisa).
female(susan).
female(anna).

parent(john, mike).
parent(john, lisa).
parent(susan, mike).
parent(susan, lisa).
parent(mike, david).
parent(anna, david).

father(F, C) :-
    male(F),
    parent(F, C).

mother(M, C) :-
    female(M),
    parent(M, C).

grandfather(GF, C) :-
    male(GF),
    parent(GF, P),
    parent(P, C).

grandmother(GM, C) :-
    female(GM),
    parent(GM, P),
    parent(P, C).

sibling(X, Y) :-
    parent(P, X),
    parent(P, Y),
    X \= Y.

ancestor(A, C) :-
    parent(A, C).

ancestor(A, C) :-
    parent(A, P),
    ancestor(P, C).

