# AY2510-cs3243-midterm
## Week 1
AI are intelligent mechanisms that solve problems to help humans, expected to be at least as fast as humans.
Intelligence assessed based on the mechanism’s `generality` and `performance`.
### Strong AI
General problem solvers &rarr; solve many different problems
### Weak / Narrow AI
Less dynamic &rarr; solve fewer problems, most AI's are like this, `easier` to formalise.
### Agent Framework
1. `Sensors` &rarr; Perceive and capture environment
2. `Agent function`
3. `Actuators` &rarr; How the agent affects the environment
### Environment Properties
1. `Fully` vs `partially` observable
2. `Deterministic` vs `stochastic`
3. `Episodic` vs `sequential`
   1. `Episodic` → actions only impact the current state (not those beyond)
   2. `Sequential` → an action may impact all future decisions
4. `Discrete` vs `Continuous`
5. `Single` vs `multi`-agent
6. `Known` vs `unknown`
7. `Static` vs `dynamic`
### Types of Agents
1. `Reflex` agents &rarr; rules for limited cases (`if-else` statements)
2. `Model-based reflex` agents
3. `Goal-based` and `utility-based` agents &rarr; (un)informed search, local search
   1. Also called `problem-solving` agents
4. `Learning` agents

## Week 2 - PSA & Path Planning
### PSA/Goal-based agents
- Uses graph search algorithm
- Able to find solutions to **ANY** problem case as it assumes `generalisable` problem
### Assumed environment
`Fully observable`, `Deterministic`, `Discrete`, `Episodic`
### Defining search space (elaborate?)
`State representation`, `Actions`, `Transition model`, `Goal test`, `Action costs`, `Initial state`
### General Search Algorithm (Tree Search)
```python
Function TreeSearch(initial_state, actions, T, isGoal, cost):
    frontier = {Node(initial_state, NULL)} # ADT: Node(current_state, parent_node)
    while frontier not empty: # add path cost, depth, action as required
        current = frontier.pop()
        if isGoal(current.state): return current.getPath()
        for a in actions(current.state):
            successor = Node(T(current.state, a), current)
            frontier.push(successor)
    return failure # i.e., no path from the initial state to any goal state
```
### `States` vs `Nodes`
State &rarr; A representation of the environment at some timestamp\
Node &rarr; Element in frontier containing `state`, `parent node`, `action`, `path cost`, `depth`
### Uninformed &rarr; no domain knowledge beyond search problem formulation
| Algorithm                        | Frontier                        |
|:---------------------------------|:--------------------------------|
| BFS                              | queue                           |
| UCS                              | priority queue                  |
| DFS                              | stack                           |
| DLS (Depth-Limited Search)       | variation of DFS with max depth |
| IDS (Iterative Deepening Search) | iterative version of DLS        |

Correctness &rarr; `complete`, `optimal`
### BFS, tie-breaking: alphabet
Assume that BFS does `early goal test`, can save one more branch factor.
### UCS, tie-breaking: alphabet
### DFS, tie-breaking: reverse alphabet
### Summary
|   Criterion   |       BFS        |           UCS           |       DFS        |       DLS        |       IDS        |
|:-------------:|:----------------:|:-----------------------:|:----------------:|:----------------:|:----------------:|
|   Complete?   | Yes<sup>1</sup>  |    Yes<sup>1,2</sup>    | No<sup>3</sup3>  |        No        | Yes<sup>1</sup>  |
| Optimal Cost? |  No<sup>4</sup>  |           Yes           |        No        |        No        |  No<sup>4</sup>  |
|     Time      | O(b<sup>d</sup>) | O(b<sup>1+⌊C*/ε⌋</sup>) | O(b<sup>m</sup>) | O(b<sup>ℓ</sup>) | O(b<sup>d</sup>) |
|     Space     | O(b<sup>d</sup>) | O(b<sup>1+⌊C*/ε⌋</sup>) |      O(bm)       |      O(bℓ)       |      O(bd)       |
1. Complete if `b` finite and either has a solution or `m` finite
2. Complete if all actions costs are > ε > 0
3. DFS is `incomplete` unless the search space is finite – i.e., when `b` finite and `m` finite
4. Cost optimal if action costs are all identical (and several other cases)

Default assumptions: `b` finite, `d` finite (contains solution), `m` infinite, all costs > ε > 0
### Graph Search Algorithm (Version 1)
```python
Function GraphSearchV1(initial_state, actions, T, isGoal, cost):
    initial_node = Node(initial_state, NULL)
    frontier = {initial_node}
    reached = {initial_state: initial_node}
    while frontier not empty:
        current = frontier.pop()
        if isGoal(current.state): return current.getPath()
        for a in actions(current.state):
            successor = Node(T(current.state, a), current)
            if successor.state not in reached:
                frontier.push(successor)
                reached.insert(successor.state: successor)
    return failure
```
Nodes are `never revisited`, may omit `optimal path`.\
Since `BFS` / `DFS` / `DLS` / `IDS` all cannot guarantee optimality, 
using `Graph Search Version 1` decreases complexity 
(since the resultant search tree excludes ALL
redundant paths) without loss of expected performance!

### Graph Search Algorithm (Version 2)
```python
Function GraphSearchV1(initial_state, actions, T, isGoal, cost):
    initial_node = Node(initial_state, NULL)
    frontier = {initial_node}
    reached = {initial_state: initial_node}
    while frontier not empty:
        current = frontier.pop()
        if isGoal(current.state): return current.getPath()
        for a in actions(current.state):
            successor = Node(T(current.state, a), current)
            if successor.state not in reached or
                successor.getCost() < reached[successor.state].getCost():
                    frontier.push(successor)
                    reached.insert(successor.state: successor)
    return failure
```
More relaxed constraint on paths that are considered, also considers paths with lower path cost.
### Summary
Time, space for everyone is `O(|V|+|E|)`
### Notes
For CS3243, unless otherwise mentioned, `assume tree search`.\
For graph search, assume `V2` for UCS and `V1` for other uninformed search algorithm.
## Week 3 - Informed Search: Incorporating Domain Knowledge
Systematic search affords `completeness`, assuming either: search space finite, or solution exists.\
Evaluation functions: Greedy Best-First Search priority: `f(n) = h(n)`\
A* Search priority: `f(n) = g(n) + h(n)`
### Best-First Search Algorithm
```python
function BEST-FIRST-SEARCH(problem, f) returns a solution node or failure
    node <- NODE(STATE=problem.INITIAL)
    frontier <- a priority queue ordered by f, with node as an element
    reached <- a lookup table, with one entry with key problem.INITIAL and value node
    while not IS-EMPTY(frontier) do
        node < POP(frontier)
        if problem.IS-GOAL(node.STATE) then return node
        for each child in EXPAND(problem, node) do
            s<child.STATE
            if s is not in reached or child.PATH-COST < reached[s].PATH-COST then
                reached[s] < child
                add child to frontier
    return failure

function EXPAND(problem, node) yields nodes
    s <- node.STATE
    for each action in problem.ACTIONS(s) do
    s` <- problem.RESULT(s, action)
    cost <- node.PATH-COST + problem.ACTION-COST(s, action, s`)
    yield NODE(STATE=s`, PARENT=node, ACTION=action, PATH-COST=cost)
```
`Tree-search` implementation is `incomplete`!\
`Graph-search` implementation is `complete` if search space is `finite`.\
`Not optimal` under either tree search or graph search.
## A* search
`Completeness` requires same criteria as UCS.\
`Optimality` depends on `h`.\
`GSV1` may skip optimal path.\
`GSV2` and `Tree search` returns optimal path.
## Graph Search Algorithm (Version 3)
```python
Function GraphSearchV3(initial_state, actions, T, isGoal, cost):
    frontier = {Node(initial_state, NULL)}
    reached = {}
    while frontier not empty:
        current = frontier.pop()
        reached.insert(current.state: current)
        if isGoal(current.state): return current.getPath()
        for a in actions(current.state):
            successor = Node(T(current.state, a), current)
            if successor.state not in reached:
                frontier.push(successor)
    return failure
```
*Note: with typical interpretations, dominance requires admissibility.*
## Week 4 - Heuristics
Theorem: If `h(n)` is admissible, then A* using `tree search` is `optimal`.\
Theorem: If `h(n)` is consistent, then A* using `graph search V2/V3` is `optimal`.
### Remember:
1. `admissible`: never overestimates
2. `consistent`: never overestimates, monotonically increasing
### Summary
1. UCS
   1. `Optimal` under `Tree Search`, `GSV2`, `GSV3`
2. Greedy Best-First Search
   1. `Not optimal`
   2. `Incomplete` under tree search
3. A* Search
    1. Assuming admissible `h`: `Optimal` under `Tree Search`, `GSV2`
    2. Assuming consistent `h`: `Optimal` under `Tree Search`, `GSV2`, `GSV3`

*Note: Assume graph search version 2 and late goal testing for UCS and A\* (unless otherwise stated)*
### Building h
Ideally, `h` is O(1) or something reasonably cheap.
We build `h` by relaxing the problem!\
Main point: relax a little bit of constraints (more liberties means worse approximation)
## Week 5 - Local Search: Goal Versus Path Search
We only want legal goal state values.\
Local search – goal determination
- Abandon systematic search – ignore path and path cost
- Only maintain “best” successor state – greedy approach

Advantage: Only store current and immediate successor states\
Space complexity: `O(b)`, may be reduced to `O(1)` if successors may be processed one at a time
### Local Search via Hill-Climbing
```python
Function HillClimbing(initial_state, eval_fn, highest_valued_successor):
    current = initial_state
    while True:
        neighbour = highest_valued_successor(current)
        if eval_fn(neighbour) <= eval_fn(current): return current
        current = neighbour
```
How it works:
1. Initial state is typically random
2. Frontier only stores current (best) state
3. For each iteration, find a `successor` that improves on the current state
4. If none exists, `return` `current` as the best state

We do not need nodes, only concerned with `state` information.\
Hill-climbing **may not** return a solution, it may get stuck at
- Local Maxima
- Shoulder or Plateau
- Ridge (sequence of local maxima)

![img.png](img.png)
### Hill-climbing variants:
#### Stochastic hill climbing
- Chooses randomly among states with `f`-values better than current
#### First-choice hill climbing
- Handles high `b` by randomly generating successors until one with better f-value than current is
  found (instead of generating all possible successors)
#### Sideways move
- Replaces ≤ with <
- Allows continuation when eval_fn(neighbour) == eval_fn(current)
- Can traverse shoulders / plateaus
#### Random-restart hill climbing
- Different algorithm 
- Adds an outer loop which randomly picks a new starting state
- Keeps attempting random restarts until a solution is found
```python
Function RandomRestarts(isGoal, random_initial_state, eval_fn, highest_valued_successor):
    current = NULL
    while current is NULL or not isGoal(current):
        current = random_initial_state()
        current = HillClimbing(current, eval_fn, highest_valued_successor)
    return current
```
*Note: Requires function to generate random initial state `random_initial_state()`*
### Local Beam Search
Beam (window) stores `k` best successor (actually `k` starts)
- Better than `k` parallel random restarts
```python
Function BeamSearch(random_initial_state, isGoal, actions, T, choose_best_k):
    frontier = {}
    for i in 1 to k:
        s = random_initial_state()
        if isGoal(s): return s
            frontier.push(s)
    while True:
        new_frontier = {}
        while frontier not empty:
            s = frontier.pop()
            for a in actions(s):
                new_s = T(s, a)
                if isGoal(new_s): return new_s
                new_frontier.push(new_s)
        frontier = choose_best_k(new_frontier)
```
Notice that we may allow states with lower values than their parents to be added since
`choose_best_k` will simply choose the best `k` successor states generated.