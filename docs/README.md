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

| e  | e  | e  | hissis |
|:---|:---|:---|:-------|
| hi | wq | ww | ww     |
