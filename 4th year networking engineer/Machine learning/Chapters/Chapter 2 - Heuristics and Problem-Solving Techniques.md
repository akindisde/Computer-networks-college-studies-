# Chapter 2 --- Heuristics and Problem-Solving Techniques

## Learning Objectives

By the end of this chapter, you should be able to:

- Explain what a heuristic is and why heuristics are useful in AI.
    
- Distinguish informed from uninformed search.
    
- Understand A* and the evaluation function: [ f(n)=g(n)+h(n) ]
    
- Explain admissible and consistent heuristics.
    
- Understand Branch and Bound and pruning.
    
- Explain game theory, minimax, and alpha-beta pruning.
    
- Describe evolutionary algorithms, including selection, crossover,  
    mutation, and fitness.
    
- Identify applications in networking and cybersecurity.
    
- Choose an appropriate technique based on the problem structure and  
    required guarantees.
    

---

# 1. Introduction to Heuristics and Problem Solving

Many AI tasks can be formulated as **search problems**.

A search problem generally contains:

- an initial state,
    
- possible actions,
    
- a transition model,
    
- a goal condition,
    
- and possibly a cost associated with actions.
    

A problem can be represented as:

[ P=(S,A,T,s_0,G,C) ]

where:

- $S$ = set of possible states,
    
- $A$ = set of actions,
    
- $T$ = transition function,
    
- $s_0$ = initial state,
    
- $G$ = goal states,
    
- $C$ = cost function.
    

The objective may be to find:

[ s_0 `\rightarrow` {=tex}s_1  
`\rightarrow` {=tex}`\cdots` {=tex}`\rightarrow` {=tex}s_g ]

such that:

[ s_g `\in` {=tex}G ]

and, when optimization is required, the total cost is minimized.

## 1.1 Combinatorial Explosion

If a problem has approximately $b$ possible actions at each state and a  
solution depth of $d$, the number of nodes can be approximately:

[ 1+b+b^2+`\cdots`{=tex}+b^d ]

For $b>1$:

[ `\sum`{=tex}_{i=0}^{d}b^i=`\frac{b^{d+1}-1}{b-1}`{=tex} ]

This grows exponentially with depth.

This is called **combinatorial explosion** and is one reason intelligent  
search techniques are necessary.

---

# 2. Heuristics

## 2.1 Definition

A **heuristic** is a rule, estimate, or strategy that uses  
problem-specific information to guide a search toward promising  
solutions.

A heuristic function is usually written:

[ h(n) ]

where $h(n)$ estimates the cost of reaching a goal from state $n$.

A heuristic does not necessarily calculate the exact answer. It provides  
useful guidance.

For example, in geographical navigation, straight-line distance can be  
used as an estimate of remaining travel distance.

## 2.2 Why Use Heuristics?

Without domain knowledge, a search algorithm may explore many irrelevant  
states.

A heuristic allows the algorithm to prioritize states that appear more  
promising.

The basic idea is:

```
Uninformed search:
Explore according to a generic strategy.

Informed search:
Explore using a generic strategy + problem knowledge.
```

## 2.3 Uninformed vs. Informed Search

### Uninformed Search

Examples:

- Breadth-First Search (BFS)
    
- Depth-First Search (DFS)
    
- Uniform-Cost Search
    

These methods do not use a problem-specific estimate of distance to the  
goal.

### Informed Search

Examples:

- Greedy Best-First Search
    
- A*
    

These methods use additional information, typically a heuristic.

---

# 3. A* Search

## 3.1 Definition

**A*** is a fundamental informed search algorithm.

It evaluates a state $n$ using:

[ `\boxed{f(n)=g(n)+h(n)}`{=tex} ]

where:

- $g(n)$ = exact cost from the initial state to $n$,
    
- $h(n)$ = estimated cost from $n$ to a goal,
    
- $f(n)$ = estimated total cost of a solution passing through $n$.
    

A* generally expands the node with the smallest $f(n)$.

## 3.2 Understanding $g(n)$

Suppose:

```
Start → A → B → C
```

with costs:

```
Start → A = 2
A → B     = 3
B → C     = 4
```

Then:

[ g(A)=2 ]

[ g(B)=2+3=5 ]

[ g(C)=2+3+4=9 ]

Thus, $g(n)$ represents the cost already incurred.

## 3.3 Understanding $h(n)$

Suppose the estimated remaining cost from a node is 7:

[ h(n)=7 ]

The quality of the heuristic strongly affects the efficiency of A*.

## 3.4 Understanding $f(n)$

If:

[ g(n)=10 ]

and:

[ h(n)=6 ]

then:

[ f(n)=10+6=16 ]

A* interprets 16 as the estimated total solution cost through $n$.

---

# 4. A* Algorithm

A simplified version is:

```
A*(start, goal):

    OPEN = {start}
    CLOSED = {}

    while OPEN is not empty:

        n = node in OPEN with minimum f(n)

        if n is the goal:
            return solution path

        remove n from OPEN
        add n to CLOSED

        for each successor m of n:

            calculate tentative g(m)

            if m is new
               or tentative g(m) is better:

                update parent of m
                update g(m)
                calculate f(m)

                if m is not in OPEN:
                    add m to OPEN

    return failure
```

In practical implementations, `OPEN` is commonly implemented using a  
**priority queue**.

---

# 5. A* Worked Example

Consider:

```
             2
        A -------- B
       /                  1              4
     /                  Start                Goal
     \                /
      5              1
       \            /
        C -------- D
             2
```

Suppose:

```
h(Start) = 5
h(A)     = 5
h(B)     = 4
h(C)     = 3
h(D)     = 1
h(Goal)  = 0
```

Initially:

[ g(Start)=0 ]

and:

[ f(Start)=0+5=5 ]

After expanding `Start`:

For A:

[ g(A)=1 ]

[ f(A)=1+5=6 ]

For C:

[ g(C)=5 ]

[ f(C)=5+3=8 ]

A* prefers A because:

[ 6<8 ]

The search continues by selecting the node with the lowest $f$ value.

The key principle is:

> A* balances the cost already paid with an estimate of the remaining  
> cost.

---

# 6. Admissible Heuristics

A heuristic $h(n)$ is **admissible** if it never overestimates the true  
minimum remaining cost.

Let $h^*(n)$ be the true optimal remaining cost.

An admissible heuristic satisfies:

[ `\boxed{h(n)\leq h^*(n)}`{=tex} ]

for every relevant state.

In other words:

```
Estimated remaining cost
        ≤
True optimal remaining cost
```

Under standard assumptions, admissibility is a key condition for A*  
optimality.

## Example

In many road-navigation problems, straight-line distance to the  
destination is a lower bound on road distance.

Therefore, it can serve as an admissible heuristic.

The important idea is:

> An admissible heuristic provides a lower bound on the remaining cost.

---

# 7. Consistent Heuristics

A heuristic is **consistent** (or monotone) if for every transition from  
$n$ to $n'$:

[ h(n)`\leq` {=tex}c(n,n')+h(n') ]

where $c(n,n')$ is the transition cost.

With:

[ h(goal)=0 ]

consistency gives useful properties for graph-search implementations of  
A*.

Under standard assumptions:

```
Consistency ⇒ Admissibility
```

The reverse does not necessarily hold in general.

---

# 8. Greedy Best-First Search vs. A*

Greedy Best-First Search uses:

[ f(n)=h(n) ]

It focuses only on estimated remaining cost.

A* uses:

[ f(n)=g(n)+h(n) ]

It considers both the cost already incurred and the estimated cost  
remaining.

Therefore:

```
Greedy:
"What looks closest to the goal?"

A*:
"What appears to lead to the cheapest complete solution?"
```

Greedy search can be fast but does not generally guarantee an optimal  
solution.

---

# 9. Branch and Bound

## 9.1 Definition

**Branch and Bound (B&B)** is a general optimization technique.

The procedure is:

1. **Branch** the problem into subproblems.
    
2. Compute a **bound** on the best solution obtainable from each  
    subproblem.
    
3. **Prune** branches that cannot improve the current best solution.
    

## 9.2 Basic Example

Suppose the best complete solution currently has cost:

[ C^*=100 ]

An unexplored branch has a lower bound:

[ LB(branch)=130 ]

Since:

[ 130>100 ]

that branch cannot produce a better solution and can be pruned.

```
Current best = 100
Branch lower bound = 130
        ↓
      Prune
```

---

# 10. Branch and Bound Example

Imagine a delivery problem in which we need to visit several locations.

A naive approach could enumerate many routes:

```
A → B → C → D
A → B → D → C
A → C → B → D
A → C → D → B
...
```

Branch and Bound constructs routes incrementally.

Suppose:

```
Best complete route = 250 km
```

A partial route has:

```
Cost already traveled = 180 km
Lower bound for remaining = 100 km
```

Therefore:

[ LB=180+100=280 ]

Since:

[ 280>250 ]

the partial route is pruned.

---

# 11. A* vs. Branch and Bound

---

Property A* Branch and Bound

---

Main use Goal/path search Optimization

Evaluation $g+h$ Bound vs. incumbent

Heuristic Usually central Optional but useful

Current best solution Not necessarily an Explicitly maintained  
incumbent in the same  
sense

Pruning Based on search/cost Based explicitly on  
structure bounds

The techniques are related but should not be treated as identical.

---

# 12. Applications of A* and Branch and Bound

## Networking

- shortest-path routing,
    
- topology navigation,
    
- route planning,
    
- configuration search.
    

## Robotics

- path planning,
    
- obstacle avoidance,
    
- map navigation.
    

## Cybersecurity

- attack-path analysis,
    
- security configuration optimization,
    
- network segmentation planning,
    
- critical-path analysis.
    

## Operations Research

- scheduling,
    
- assignment,
    
- routing,
    
- resource allocation,
    
- combinatorial optimization.
    

---

# 13. Game Theory

## 13.1 Definition

**Game theory** studies strategic interactions between decision-makers.

A player can be:

- a human,
    
- a company,
    
- a software agent,
    
- an attacker,
    
- a defender,
    
- a network controller.
    

The crucial idea is:

> The best action may depend on what another agent does.

This makes game theory especially relevant to cybersecurity.

---

# 14. Components of a Game

A strategic game can be described using:

1. **Players**
    
2. **Actions or strategies**
    
3. **Payoffs**
    
4. **Information**
    
5. **Rules and constraints**
    

Example:

```
Player 1: Attacker
Player 2: Defender
```

Attacker strategies:

```
A1 = Attack Server A
A2 = Attack Server B
A3 = Do nothing
```

Defender strategies:

```
D1 = Protect Server A
D2 = Protect Server B
D3 = Monitor
```

Each combination produces an outcome.

---

# 15. Payoff Matrix

Consider:

```
           Defend A   Defend B
```

---

Attack A -5 10  
Attack B 8 -4

The values represent the attacker's payoff.

For example:

- Attack A + Defend A → attacker gets -5.
    
- Attack A + Defend B → attacker gets 10.
    

The defender attempts to reduce the attacker's payoff.

This is a simple model of adversarial decision-making.

---

# 16. Zero-Sum Games

In a **zero-sum game**, one player's gain corresponds directly to the  
other player's loss.

[ U_A+U_D=0 ]

For example:

```
Attacker payoff = +10
Defender payoff = -10
```

Thus:

[ 10+(-10)=0 ]

Many real security interactions are not perfectly zero-sum, but the  
model can still be useful.

---

# 17. Minimax

**Minimax** is a classic decision algorithm for two-player adversarial  
games.

It is associated with games such as:

- chess,
    
- checkers,
    
- tic-tac-toe.
    

The maximizing player chooses:

[ `\max`{=tex} ]

while the minimizing player chooses:

[ `\min`{=tex} ]

The basic assumption is:

> The opponent will choose the action that is worst for you.

## Example

Suppose Action A can lead to:

```
+3
+5
```

The opponent chooses:

[ `\min`{=tex}(3,5)=3 ]

Action B can lead to:

```
+2
+8
```

The opponent chooses:

[ `\min`{=tex}(2,8)=2 ]

The current player chooses:

[ `\max`{=tex}(3,2)=3 ]

Therefore Action A is selected.

---

# 18. Game Trees

A game can be represented as:

```
                    Root
                  /                      A          B
              /  \       /  \
             3    5     2    8
```

For A:

[ `\min`{=tex}(3,5)=3 ]

For B:

[ `\min`{=tex}(2,8)=2 ]

Then:

[ `\max`{=tex}(3,2)=3 ]

Therefore the maximizing player chooses A.

---

# 19. Alpha-Beta Pruning

Game trees can become enormous.

**Alpha-beta pruning** improves minimax by eliminating branches that  
cannot affect the final decision.

It maintains:

- $\alpha$ = best value already guaranteed for MAX.
    
- $\beta$ = best value already guaranteed for MIN.
    

When:

[ `\alpha`{=tex}`\geq`{=tex}`\beta`{=tex} ]

a branch can be pruned under the standard minimax framework.

The principle is:

> Do not evaluate a branch when you already know it cannot change the  
> final decision.

## Complexity

Naive minimax requires approximately:

[ O(b^d) ]

nodes for branching factor $b$ and depth $d$.

With effective move ordering, alpha-beta pruning can approach:

[ O(b^{d/2}) ]

in favorable theoretical conditions.

This is a major reduction.

---

# 20. Game Theory in Cybersecurity

Game theory provides a natural framework for attacker-defender  
scenarios.

Attacker:

```
Scan
Exploit
Exfiltrate
```

Defender:

```
Monitor
Patch
Block
Isolate
```

A game-theoretic model can help answer:

- Where should security resources be allocated?
    
- Which assets should be defended first?
    
- What happens if an attacker changes tactics?
    
- How should a defender respond under uncertainty?
    
- Which defensive strategy is robust against an adaptive adversary?
    

---

# 21. Evolutionary Algorithms

## 21.1 Motivation

Some optimization problems are extremely difficult to solve exactly.

Examples:

- scheduling,
    
- routing,
    
- network design,
    
- feature selection,
    
- parameter optimization.
    

Evolutionary algorithms are inspired by biological evolution.

The general process is:

```
Population
    ↓
Evaluate Fitness
    ↓
Selection
    ↓
Crossover / Recombination
    ↓
Mutation
    ↓
New Population
    ↓
Repeat
```

---

# 22. Genetic Algorithms

A **Genetic Algorithm (GA)** is a widely used evolutionary algorithm.

A population contains candidate solutions.

Each candidate may be called:

- an individual,
    
- a chromosome,
    
- a candidate solution.
    

Each candidate receives a **fitness score**.

A higher fitness usually means a better solution when the problem is  
formulated as maximization.

---

# 23. Representation

We must decide how to encode a candidate solution.

For example, selecting security controls can use a binary  
representation:

```
10110
```

where:

```
1 = control selected
0 = control not selected
```

A continuous optimization problem may instead use:

[ x=[0.12,0.84,0.37,0.91] ]

The representation must match the problem.

---

# 24. Fitness Function

The **fitness function** measures solution quality.

Suppose we want to maximize network throughput while minimizing cost:

[ Fitness(x)=Throughput(x)-`\lambda` {=tex}Cost(x) ]

where:

[ `\lambda`{=tex}>0 ]

controls the cost penalty.

Fitness design is critical.

A poorly designed fitness function may cause the algorithm to optimize  
the wrong thing.

This is a general AI principle:

> An optimization algorithm can only optimize what the objective  
> function measures.

---

# 25. Selection

Selection chooses individuals to reproduce.

Common methods include:

- roulette-wheel selection,
    
- tournament selection,
    
- rank selection.
    

The principle is:

> Better candidates should generally have a greater probability of  
> contributing to the next generation.

However, excessive selection pressure can eliminate diversity too  
quickly.

---

# 26. Crossover

**Crossover** combines information from two parents.

Suppose:

```
Parent 1 = 101|101
Parent 2 = 011|010
```

A one-point crossover produces:

```
Child 1 = 101|010
Child 2 = 011|101
```

Crossover allows useful properties from different solutions to be  
combined.

---

# 27. Mutation

Mutation introduces random variation.

For example:

```
Before:
101101

After:
101001
```

One bit changed.

Mutation helps:

- maintain diversity,
    
- explore new regions,
    
- avoid premature convergence.
    

---

# 28. Evolutionary Algorithm Workflow

A simplified genetic algorithm is:

```
Initialize population

while stopping condition is not met:

    Evaluate fitness

    Select parents

    Apply crossover

    Apply mutation

    Evaluate offspring

    Create next generation

return best solution
```

Many implementations also use **elitism**, where some of the best  
individuals are copied directly into the next generation.

---

# 29. Example: Network Topology Optimization

Suppose a network engineer wants to select links for a network.

A candidate solution might be:

```
101011001
```

Each bit indicates whether a particular link is selected.

The fitness function could combine:

- reliability,
    
- bandwidth,
    
- latency,
    
- deployment cost.
    

For example:

[ Fitness = w_1Reliability +w_2Bandwidth -w_3Latency -w_4Cost ]

The evolutionary algorithm searches through candidate network designs.

---

# 30. Evolutionary Algorithms in Cybersecurity

Applications include:

### Feature selection

Selecting a useful subset of features for intrusion detection.

```
100 features
    ↓
Evolutionary search
    ↓
15 useful features
```

### Security rule optimization

Optimizing combinations of:

- firewall rules,
    
- detection thresholds,
    
- security policies.
    

### Network defense optimization

Searching for configurations balancing:

- security,
    
- performance,
    
- cost.
    

### Adversarial research

Evolutionary search can also explore how inputs might be modified to  
challenge ML models.

---

# 31. Strengths of Evolutionary Algorithms

They can:

- search large and complex spaces,
    
- handle nonlinear objective functions,
    
- handle discrete and continuous variables,
    
- operate without gradient information,
    
- optimize competing objectives,
    
- find good approximate solutions when exact optimization is  
    expensive.
    

---

# 32. Limitations of Evolutionary Algorithms

### 1. No general guarantee of global optimality

A genetic algorithm may find a very good solution without finding the  
true global optimum.

### 2. Computational cost

Population-based optimization can require many fitness evaluations.

### 3. Hyperparameter sensitivity

Performance depends on:

- population size,
    
- mutation rate,
    
- crossover rate,
    
- selection strategy,
    
- number of generations.
    

### 4. Fitness-function design

A poor objective can produce a technically optimal but practically  
useless solution.

---

# 33. Multi-Objective Evolutionary Optimization

Real engineering problems often contain conflicting objectives.

For example:

```
Maximize:
    Security

Minimize:
    Cost
    Latency
    Energy
```

One solution may be highly secure but expensive.

Another may be cheap but less secure.

Rather than finding one optimum, evolutionary methods can identify a set  
of **Pareto-optimal** solutions.

## 33.1 Pareto Dominance

Solution A dominates B if:

1. A is at least as good as B in every objective.
    
2. A is strictly better in at least one objective.
    

The set of non-dominated solutions forms the **Pareto front**.

Conceptually:

```
Security ↑

        •
      •
    •
  •
 •________________→ Cost
```

The final decision can then be made using operational constraints.

---

# 34. Comparing the Main Techniques

---

Technique Main Idea Strength Limitation

---

A* $g+h$ guided Efficient path Depends on  
search finding with heuristic quality  
suitable  
heuristic

Branch and Bound Branch + bound + Exact Can still be  
prune optimization exponential  
under appropriate  
conditions

Minimax Assume adversarial Strategic Large game trees  
opponent decision-making

Alpha-Beta Prune minimax tree Faster minimax Still depends on  
search depth and move  
ordering

Genetic Algorithm Evolve candidate Flexible No general  
solutions optimization optimality  
guarantee

---

# 35. Choosing the Right Technique

### Pathfinding problem

Consider:

[ `\boxed{A^*}`{=tex} ]

especially when a useful heuristic is available.

### Optimization with useful bounds

Consider:

[ `\boxed{Branch\ and\ Bound}`{=tex} ]

especially when an exact solution is required.

### Adversarial opponent

Consider:

[ `\boxed{Game\ Theory / Minimax}`{=tex} ]

For large game trees, alpha-beta pruning can improve search.

### Huge or difficult optimization space

Consider:

[ `\boxed{Evolutionary\ Algorithms}`{=tex} ]

especially when a good fitness function can be designed.

---

# 36. A Unified View

Although these techniques are different, they share a common principle:

> **Do not blindly examine every possible solution. Use structure,  
> knowledge, or feedback to focus computational effort on promising  
> possibilities.**

### A*

Uses:

```
Cost so far + heuristic estimate
```

### Branch and Bound

Uses:

```
Current best + mathematical bounds
```

### Game Theory

Uses:

```
Opponent models + strategic reasoning
```

### Evolutionary Algorithms

Uses:

```
Population + selection + variation + fitness
```

---

# 37. Applications in Networks and Cybersecurity

## 37.1 Network Routing

A network can be represented as:

[ G=(V,E) ]

A* can search for a path from source $s$ to destination $t$.

The heuristic may estimate the remaining distance.

## 37.2 Attack-Path Analysis

An enterprise network can be represented as:

```
Internet
   ↓
Web Server
   ↓
Application Server
   ↓
Database
```

Search can help identify:

- possible attack paths,
    
- shortest attack paths,
    
- high-risk paths,
    
- critical assets.
    

## 37.3 Security Resource Allocation

Possible decisions include:

```
Deploy IDS
Patch Server A
Patch Server B
Add MFA
Segment Network
Increase Monitoring
```

Branch and Bound or evolutionary optimization can search for high-value  
combinations.

## 37.4 Adaptive Defense

A defender may react to an attacker:

```
Attacker Action
       ↓
Defender Response
       ↓
Attacker Adaptation
       ↓
Defender Adaptation
```

This is naturally modeled using game-theoretic reasoning.

## 37.5 ML Feature Selection

If an intrusion dataset contains 500 features, evolutionary optimization  
can search for a smaller subset.

For example:

[ 500 features `\rightarrow 40`{=tex} useful features ]

The selected features can then be evaluated with a machine learning  
model.

---

# 38. Exact vs. Approximate Solutions

An important distinction is whether an algorithm guarantees an optimal  
solution.

### A*

Under appropriate conditions, including an admissible heuristic and  
suitable implementation, can provide an optimal path.

### Branch and Bound

Can provide an exact optimum when exhaustive search with valid bounds is  
completed.

### Evolutionary Algorithms

Usually provide approximate solutions and do not generally guarantee the  
global optimum.

This produces an engineering trade-off:

[ `\text{Solution Quality}`{=tex}  
`\leftrightarrow` {=tex}`\text{Computation Time}`{=tex} ]

Sometimes a very good solution in seconds is more useful than an exact  
solution requiring days.

---

# 39. Practical Example: Security Configuration Optimization

Suppose a company has 20 possible security controls.

Each control has:

- deployment cost,
    
- security benefit,
    
- performance impact.
    

A candidate configuration can be:

```
x = [1,0,1,1,0,0,...]
```

where:

[ x_i=

```
\begin{cases}
1 & \text{control }i\text{ is enabled}\\
0 & \text{otherwise}
\end{cases}
```

]

Total cost:

[ Cost(x)=`\sum`{=tex}_i c_i x_i ]

Security benefit:

[ Security(x)=`\sum`{=tex}_i s_i x_i ]

Subject to:

[ Cost(x)`\leq` {=tex}Budget ]

Possible approaches include:

- Branch and Bound,
    
- Genetic Algorithms,
    
- Integer Programming,
    
- heuristic search.
    

The best choice depends on problem size, constraints, and the required  
guarantees.

---

# 40. Mentor Notes

When solving a problem, do not start by asking:

> "Which algorithm should I use?"

Start with:

### Step 1 --- Define the state

What exactly represents one possible situation?

### Step 2 --- Define actions

What transitions are possible?

### Step 3 --- Define the objective

Are you trying to:

- find any solution?
    
- find the shortest solution?
    
- minimize cost?
    
- maximize reward?
    
- defeat an opponent?
    
- balance several objectives?
    

### Step 4 --- Determine available knowledge

Do you have:

- a heuristic?
    
- a mathematical bound?
    
- an opponent model?
    
- a fitness function?
    
- labeled data?
    

### Step 5 --- Determine the required guarantee

Do you need:

```
Exact optimum?
```

or is:

```
Good approximate solution
```

sufficient?

### Step 6 --- Estimate computational complexity

Ask:

```
How large is the search space?
How expensive is evaluating a candidate?
How quickly does the problem grow?
```

These questions often determine the appropriate technique.

---

# 41. Review Questions

## Heuristics and A*

1. What is a heuristic?
    
2. What is the difference between informed and uninformed search?
    
3. What are $g(n)$, $h(n)$, and $f(n)$ in A*?
    
4. Why does A* use $f(n)=g(n)+h(n)$?
    
5. What is an admissible heuristic?
    
6. What does it mean for a heuristic to be consistent?
    
7. How does A* differ from Greedy Best-First Search?
    
8. Why can a good heuristic reduce search effort?
    

## Branch and Bound

9. What is the purpose of branching?
    
10. What is a bound?
    
11. Why can a branch be pruned?
    
12. What is an incumbent solution?
    
13. How does Branch and Bound differ from A*?
    
14. Give an example where Branch and Bound could be useful.
    

## Game Theory

15. What are the main components of a strategic game?
    
16. What is a payoff matrix?
    
17. What is a zero-sum game?
    
18. Explain the minimax principle.
    
19. Why is alpha-beta pruning useful?
    
20. What does $\alpha\geq\beta$ indicate?
    
21. Give an attacker-defender example.
    

## Evolutionary Algorithms

22. What is a population?
    
23. What is a fitness function?
    
24. What is selection?
    
25. What is crossover?
    
26. What is mutation?
    
27. Why is mutation important?
    
28. What is elitism?
    
29. What is Pareto optimality?
    
30. Why do evolutionary algorithms not generally guarantee a global  
    optimum?
    

---

# 42. Exercises

## Exercise 1 --- A* Calculation

Suppose:

Node $g(n)$ $h(n)$

---

A 4 8  
B 7 3  
C 2 10

Calculate:

[ f(n)=g(n)+h(n) ]

### Answer

For A:

[ f(A)=4+8=12 ]

For B:

[ f(B)=7+3=10 ]

For C:

[ f(C)=2+10=12 ]

Therefore:

[ `\boxed{B}`{=tex} ]

is selected.

---

## Exercise 2 --- Heuristic Quality

Suppose:

Node True cost $h^*(n)$ Heuristic $h(n)$

---

A 10 8  
B 7 7  
C 12 15

Determine which heuristics are admissible.

### Answer

A:

[ 8`\leq10`{=tex} ]

Admissible.

B:

[ 7`\leq7`{=tex} ]

Admissible.

C:

[ 15>12 ]

Not admissible.

Therefore:

[  
`\boxed{A\text{ and }B\text{ are admissible; }C\text{ is not.}}`{=tex}  
]

---

## Exercise 3 --- Branch and Bound

Suppose the current best solution has cost:

[ C^*=150 ]

and three branches have lower bounds:

```
Branch A = 120
Branch B = 175
Branch C = 140
```

Which can immediately be pruned?

### Answer

A branch can be pruned when its lower bound cannot beat the incumbent:

[ LB`\geq` {=tex}C^* ]

Therefore:

```
A: 120 < 150 → keep
B: 175 > 150 → prune
C: 140 < 150 → keep
```

Only Branch B can immediately be pruned.

---

## Exercise 4 --- Minimax

Consider:

```
                 MAX
               /     \
              A       B
             / \     / \
            4   7   2   9
```

Assume the opponent is MIN.

For A:

[ `\min`{=tex}(4,7)=4 ]

For B:

[ `\min`{=tex}(2,9)=2 ]

MAX chooses:

[ `\max`{=tex}(4,2)=4 ]

Therefore the minimax value is:

[ `\boxed{4}`{=tex} ]

and MAX chooses A.

---

## Exercise 5 --- Genetic Algorithm

Suppose:

```
P1 = 110|010
P2 = 001|111
```

Perform one-point crossover at `|`.

### Answer

```
Child 1 = 110|111
Child 2 = 001|010
```

If mutation changes the fifth bit of Child 1:

```
Child 1 = 110|101
```

Crossover combines information; mutation introduces variation.

---

# 43. Mini Project

## Security Monitoring Optimization

Design a strategy for selecting three monitoring sensors in a small  
enterprise network.

The network has:

```
5 servers
3 routers
2 firewalls
```

The security team wants to deploy only three additional sensors.

Constraints:

- some servers are more critical than others,
    
- monitoring has a performance cost,
    
- some network paths are more exposed to attacks.
    

### Tasks

1. Define the state representation.
    
2. Define possible actions.
    
3. Define a fitness function.
    
4. Explain how a genetic algorithm could search for a good  
    configuration.
    
5. Explain how Branch and Bound could solve the same problem.
    
6. Explain how an attacker-defender game could model the problem.
    
7. Compare the approaches.
    

A possible fitness formulation is:

[ Fitness = w_1AttackCoverage +w_2AssetCriticality -w_3PerformanceCost  
]

The weights should be justified rather than selected arbitrarily.

---

# 44. Chapter Summary

Heuristics and problem-solving techniques are fundamental to AI because  
many real-world problems contain enormous search spaces.

The major techniques covered are:

[ `\boxed{ A^* \quad Branch\ and\ Bound \quad Game\ Theory \quad Evolutionary\ Algorithms }`{=tex} ]

### A*

A* uses:

[ `\boxed{f(n)=g(n)+h(n)}`{=tex} ]

to combine the cost already incurred with an estimate of remaining cost.

### Branch and Bound

Branch and Bound searches an optimization space while using bounds to  
eliminate solutions that cannot improve the current best result.

### Game Theory

Game theory models situations in which outcomes depend on multiple  
decision-makers.

Minimax and alpha-beta pruning are fundamental tools for adversarial  
search.

### Evolutionary Algorithms

Evolutionary algorithms search for high-quality solutions through:

```
Population
→ Selection
→ Crossover
→ Mutation
→ Evaluation
→ New Generation
```

---

# Final Perspective

The central lesson is not to memorize four algorithms.

It is to recognize four different approaches to making difficult search  
problems computationally manageable:

[ `\boxed{ \begin{array}{ll} A^* & \rightarrow \text{Use estimates to guide search}\\ Branch\ and\ Bound & \rightarrow \text{Use bounds to eliminate impossible improvements}\\ Game\ Theory & \rightarrow \text{Reason about strategic opponents}\\ Evolutionary\ Algorithms & \rightarrow \text{Search through populations of candidate solutions} \end{array} }`{=tex} ]

These ideas will become increasingly important when we study machine  
learning and intelligent systems.

In networking and cybersecurity, a real system may combine several  
techniques:

```
Network Graph
     ↓
A* / Graph Search
     ↓
Attack-Path Analysis
     ↓
Game-Theoretic Modeling
     ↓
Evolutionary Optimization
     ↓
Machine Learning
     ↓
Automated Security Decision
```

The ability to select and combine problem-solving techniques is a core  
skill in AI engineering.