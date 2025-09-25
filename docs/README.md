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
    node <NODE(STATE=problem.INITIAL)
    frontier <a priority queue ordered by f, with node as an element
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
    cost < node.PATH-COST + problem.ACTION-COST(s, action, s')
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
