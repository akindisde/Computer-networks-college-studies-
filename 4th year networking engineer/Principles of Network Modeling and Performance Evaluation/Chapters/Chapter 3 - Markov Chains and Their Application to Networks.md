# Chapter 3 — Markov Chains and Their Application to Networks

## 1. Introduction

Markov chains are among the most important stochastic models used in network performance analysis.

They provide a mathematical framework for representing systems whose behavior evolves randomly from one state to another.

In communication networks, Markov models can represent:

- queue occupancy;
    
- server states;
    
- channel conditions;
    
- wireless link quality;
    
- user activity;
    
- network congestion;
    
- routing states;
    
- retransmission states;
    
- connection states;
    
- failures and repairs;
    
- numbers of active users.
    

The central idea is the **Markov property**:

> The future evolution of the system depends on its current state, rather than on the complete history of how it reached that state.

We can express this conceptually as:

[  
\boxed{  
\text{Past}  
\rightarrow  
\text{Current State}  
\rightarrow  
\text{Future}  
}  
]

The past influences the future only through the information contained in the current state.

This chapter studies two major classes:

1. **Discrete-Time Markov Chains (DTMCs)**;
    
2. **Continuous-Time Markov Chains (CTMCs)**.
    

The distinction is fundamental:

[  
\boxed{  
\text{DTMC: state changes are observed at discrete time instants}  
}  
]

[  
\boxed{  
\text{CTMC: state changes can occur at arbitrary times}  
}  
]

---

# 2. Markov Property

Let:

[  
X_n  
]

represent the state of a system at discrete time (n).

The process is Markovian if:

# [  
P(X_{n+1}=j  
\mid  
X_n=i,X_{n-1},\ldots,X_0)

P(X_{n+1}=j\mid X_n=i)  
]

In words:

> Given the current state, the previous states do not provide additional information about the next state.

For a continuous-time process (X(t)), the equivalent idea is:

# [  
P(X(t+\tau)=j  
\mid  
X(t),\text{past})

P(X(t+\tau)=j\mid X(t))  
]

The Markov property is therefore a **conditional independence property**.

---

# 3. Modeling a Network as a Markov Chain

To model a network component using a Markov chain, we first define its states.

Consider a wireless channel with three quality levels:

[  
S=  
{G,M,B}  
]

where:

- (G) = Good;
    
- (M) = Medium;
    
- (B) = Bad.
    

At each observation instant, the channel occupies one of these states.

The system may evolve as:

[  
G\rightarrow G  
]

[  
G\rightarrow M  
]

[  
M\rightarrow B  
]

and so on.

The transition probabilities describe how likely each change is.

---

# 4. 3.1 Discrete-Time Markov Chains

## 4.1 Definition

A **Discrete-Time Markov Chain (DTMC)** is a stochastic process:

[  
{X_n:n=0,1,2,\ldots}  
]

with:

- a discrete time index;
    
- a discrete or countable state space;
    
- the Markov property.
    

The state at time (n+1) depends probabilistically on the state at time (n).

---

# 5. State Space

Let the state space be:

[  
S={1,2,\ldots,M}  
]

where each state represents a meaningful condition of the network.

For example:

[  
S={0,1,2,3}  
]

could represent the number of packets in a buffer with capacity 3.

The interpretation would be:

|State|Meaning|
|--:|---|
|0|Empty|
|1|One packet|
|2|Two packets|
|3|Full|

The state definition is one of the most important modeling decisions.

A poorly selected state may fail to contain enough information to satisfy the Markov property.

---

# 6. Transition Probabilities

The one-step transition probability from state (i) to state (j) is:

# [  
P_{ij}

P(X_{n+1}=j\mid X_n=i)  
]

These probabilities form the **transition matrix**:

[  
P=  
\begin{bmatrix}  
P_{00}&P_{01}&\cdots&P_{0M}\  
P_{10}&P_{11}&\cdots&P_{1M}\  
\vdots&\vdots&\ddots&\vdots\  
P_{M0}&P_{M1}&\cdots&P_{MM}  
\end{bmatrix}  
]

Every element satisfies:

[  
0\le P_{ij}\le1  
]

and each row must sum to one:

[  
\boxed{  
\sum_jP_{ij}=1  
}  
]

because, given state (i), the system must transition to one of the possible states.

---

# 7. Example — Wireless Channel

Suppose a wireless channel has three states:

[  
S={G,M,B}  
]

with transition matrix:

[  
P=  
\begin{bmatrix}  
0.7&0.2&0.1\  
0.3&0.5&0.2\  
0.1&0.3&0.6  
\end{bmatrix}  
]

The rows represent the current state and the columns represent the next state.

Therefore:

[  
P_{GM}=0.2  
]

means:

[  
P(X_{n+1}=M\mid X_n=G)=0.2  
]

Similarly:

[  
P_{BB}=0.6  
]

means that if the channel is currently bad, there is a 60% probability that it remains bad at the next observation.

---

# 8. State Probability Vector

Let:

# [  
\pi^{(n)}

\begin{bmatrix}  
P(X_n=0)&  
P(X_n=1)&  
\cdots&  
P(X_n=M)  
\end{bmatrix}  
]

be the probability distribution of the state at time (n).

The state distribution at the next time step is:

[  
\boxed{  
\pi^{(n+1)}=\pi^{(n)}P  
}  
]

Consequently:

[  
\boxed{  
\pi^{(n)}=\pi^{(0)}P^n  
}  
]

This equation is fundamental.

It allows us to calculate the probability distribution after (n) transitions.

---

# 9. Example — State Evolution

Suppose:

# [  
\pi^{(0)}

\begin{bmatrix}  
1&0&0  
\end{bmatrix}  
]

This means that initially the channel is certainly in state (G).

Using:

[  
P=  
\begin{bmatrix}  
0.7&0.2&0.1\  
0.3&0.5&0.2\  
0.1&0.3&0.6  
\end{bmatrix}  
]

we obtain:

# [  
\pi^{(1)}

\pi^{(0)}P  
]

Therefore:

# [  
\pi^{(1)}

\begin{bmatrix}  
0.7&0.2&0.1  
\end{bmatrix}  
]

After one transition:

- probability of Good = (0.7);
    
- probability of Medium = (0.2);
    
- probability of Bad = (0.1).
    

After two transitions:

# [  
\pi^{(2)}

\pi^{(1)}P  
]

or:

# [  
\pi^{(2)}

\pi^{(0)}P^2  
]

---

# 10. Multi-Step Transition Probabilities

The (n)-step transition probability is:

# [  
P_{ij}^{(n)}

P(X_{k+n}=j\mid X_k=i)  
]

For a homogeneous DTMC:

[  
\boxed{  
P^{(n)}=P^n  
}  
]

Therefore:

# [  
P_{ij}^{(n)}

[P^n]_{ij}  
]

This means that matrix multiplication provides a direct way to calculate multi-step state transition probabilities.

---

# 11. Chapman–Kolmogorov Equation

The Chapman–Kolmogorov equation describes the composition of transitions.

For a DTMC:

# [  
P_{ij}^{(n+m)}

\sum_k  
P_{ik}^{(n)}  
P_{kj}^{(m)}  
]

In matrix form:

# [  
\boxed{  
P^{(n+m)}

P^nP^m  
}  
]

This is fundamental because it allows long-term transitions to be constructed from shorter transitions.

---

# 12. Homogeneous Markov Chains

A Markov chain is **time-homogeneous** if the transition probabilities do not depend on the time index.

Therefore:

# [  
P(X_{n+1}=j\mid X_n=i)

P_{ij}  
]

for all (n).

The transition matrix is therefore constant:

[  
P_n=P  
]

This assumption greatly simplifies analysis.

In real networks, however, transition probabilities may change with:

- time of day;
    
- traffic load;
    
- mobility;
    
- channel conditions;
    
- network configuration.
    

In such cases, a time-varying Markov model may be more appropriate.

---

# 13. Classification of States

Markov-chain theory provides several ways of classifying states.

## 13.1 Accessible states

State (j) is accessible from state (i) if:

[  
P_{ij}^{(n)}>0  
]

for some:

[  
n\ge0  
]

We write:

[  
i\rightarrow j  
]

---

## 13.2 Communicating states

States (i) and (j) communicate if:

[  
i\rightarrow j  
]

and:

[  
j\rightarrow i  
]

We write:

[  
i\leftrightarrow j  
]

Communication partitions the state space into communicating classes.

---

# 14. Irreducible Markov Chain

A Markov chain is **irreducible** if every state can be reached from every other state.

For all (i,j):

[  
i\rightarrow j  
]

This is an important condition for long-term network analysis because it often guarantees that the system does not become permanently trapped in a subset of states.

---

# 15. Absorbing State

A state (i) is absorbing if:

[  
P_{ii}=1  
]

Once the system enters this state, it never leaves.

Example:

[  
P=  
\begin{bmatrix}  
0.8&0.2&0\  
0.1&0.7&0.2\  
0&0&1  
\end{bmatrix}  
]

State 3 is absorbing.

In network models, an absorbing state might represent:

- permanent system failure;
    
- completed session;
    
- terminated process.
    

---

# 16. Transient and Recurrent Behavior

A state may be:

- transient;
    
- recurrent;
    
- positive recurrent;
    
- null recurrent.
    

For network performance evaluation, **positive recurrence** is particularly important because it is associated with the existence of a stationary probability distribution in many finite or well-behaved models.

---

# 17. Stationary Distribution

A stationary distribution is a probability vector:

[  
\pi=  
\begin{bmatrix}  
\pi_0&\pi_1&\cdots&\pi_M  
\end{bmatrix}  
]

such that:

[  
\boxed{  
\pi=\pi P  
}  
]

with:

[  
\sum_i\pi_i=1  
]

and:

[  
\pi_i\ge0  
]

The stationary distribution describes the long-term fraction of time the process spends in each state under appropriate conditions.

---

# 18. Solving for the Stationary Distribution

To find (\pi), solve:

[  
\pi=\pi P  
]

together with:

[  
\sum_i\pi_i=1  
]

For a two-state system:

[  
P=  
\begin{bmatrix}  
p_{00}&p_{01}\  
p_{10}&p_{11}  
\end{bmatrix}  
]

we have:

# [  
\pi_0

\pi_0p_{00}  
+  
\pi_1p_{10}  
]

# [  
\pi_1

\pi_0p_{01}  
+  
\pi_1p_{11}  
]

and:

[  
\pi_0+\pi_1=1  
]

---

# 19. Example — Two-State Network Channel

Consider:

[  
P=  
\begin{bmatrix}  
0.9&0.1\  
0.4&0.6  
\end{bmatrix}  
]

The states are:

- 0 = Good;
    
- 1 = Bad.
    

Let:

[  
\pi=  
\begin{bmatrix}  
\pi_0&\pi_1  
\end{bmatrix}  
]

The stationary equation gives:

# [  
\pi_0

0.9\pi_0+0.4\pi_1  
]

with:

[  
\pi_0+\pi_1=1  
]

Rearranging:

[  
0.1\pi_0=0.4\pi_1  
]

so:

[  
\pi_0=4\pi_1  
]

Therefore:

[  
4\pi_1+\pi_1=1  
]

[  
5\pi_1=1  
]

and:

[  
\boxed{  
\pi_1=0.2  
}  
]

Thus:

[  
\boxed{  
\pi_0=0.8  
}  
]

In the long run, the channel is expected to be:

- Good 80% of the time;
    
- Bad 20% of the time.
    

---

# 20. Performance Measures from a DTMC

Once the stationary distribution is known, many performance metrics can be calculated.

Suppose:

[  
g(i)  
]

is a performance cost associated with state (i).

Then the long-term average performance is:

# [  
\boxed{  
E[g(X)]

\sum_i\pi_i g(i)  
}  
]

For example, if (X) represents queue occupancy:

[  
g(i)=i  
]

then:

[  
\boxed{  
E[X]=\sum_i i\pi_i  
}  
]

This gives the average number of packets in the system.

---

# 21. DTMC Application — Wireless Channel

Suppose a wireless channel has states:

[  
S={G,M,B}  
]

and each state corresponds to a transmission rate:

[  
R_G=20\text{ Mbit/s}  
]

[  
R_M=10\text{ Mbit/s}  
]

[  
R_B=2\text{ Mbit/s}  
]

If the stationary distribution is:

[  
\pi=  
[0.6,0.3,0.1]  
]

then the long-term average data rate is:

# [  
E[R]

0.6(20)+0.3(10)+0.1(2)  
]

# [  
E[R]

12+3+0.2  
]

Therefore:

[  
\boxed{  
E[R]=15.2\text{ Mbit/s}  
}  
]

This illustrates how a Markov model can transform channel-state statistics into a network performance metric.

---

# 22. 3.2 Continuous-Time Markov Chains

## 22.1 Motivation

In many communication systems, state transitions do not occur at regular discrete time intervals.

For example:

- packets arrive at random times;
    
- packets depart at random times;
    
- calls begin and end at arbitrary times;
    
- failures occur at random times;
    
- users enter and leave a system asynchronously.
    

A DTMC can approximate such systems by observing them periodically, but a **Continuous-Time Markov Chain (CTMC)** provides a more natural model.

---

# 23. Definition of a CTMC

A CTMC is a stochastic process:

[  
{X(t),t\ge0}  
]

with:

- continuous time;
    
- discrete state space;
    
- Markov property.
    

The process remains in a state for some random amount of time and then transitions to another state.

For example:

[  
0  
\xrightarrow{}  
1  
\xrightarrow{}  
2  
\xrightarrow{}  
1  
\xrightarrow{}  
0  
]

The transitions can occur at arbitrary times:

[  
t_1,t_2,t_3,\ldots  
]

---

# 24. Holding Time

The **holding time** is the amount of time the process remains in a state before transitioning.

In a standard CTMC, the holding time in state (i) is exponentially distributed.

If the total transition rate out of state (i) is:

[  
q_i  
]

then:

[  
T_i\sim\operatorname{Exponential}(q_i)  
]

and:

[  
E[T_i]=\frac{1}{q_i}  
]

This property is what makes exponential distributions central to CTMCs.

---

# 25. Transition Rates

For a CTMC, we use **transition rates** instead of discrete-time transition probabilities.

For (i\neq j):

# [  
q_{ij}

\lim_{\Delta t\rightarrow0}  
\frac{  
P(X(t+\Delta t)=j\mid X(t)=i)  
}{  
\Delta t  
}  
]

The quantity (q_{ij}) represents the instantaneous transition rate from (i) to (j).

It has units such as:

[  
\text{s}^{-1}  
]

---

# 26. Generator Matrix

All transition rates are collected in the **generator matrix**, also called the **infinitesimal generator**:

[  
Q=  
\begin{bmatrix}  
q_{00}&q_{01}&\cdots\  
q_{10}&q_{11}&\cdots\  
\vdots&\vdots&\ddots  
\end{bmatrix}  
]

For (i\neq j):

[  
q_{ij}\ge0  
]

The diagonal elements are:

# [  
\boxed{  
q_{ii}

-\sum_{j\neq i}q_{ij}  
}  
]

Therefore:

[  
\boxed{  
\sum_jq_{ij}=0  
}  
]

for every row.

---

# 27. Example — Two-State CTMC

Consider a network link with two states:

- (U) = Up;
    
- (D) = Down.
    

Suppose:

[  
q_{UD}=\alpha  
]

and:

[  
q_{DU}=\beta  
]

Then:

[  
Q=  
\begin{bmatrix}  
-\alpha&\alpha\  
\beta&-\beta  
\end{bmatrix}  
]

The system remains Up for an exponentially distributed duration with mean:

[  
\frac{1}{\alpha}  
]

and remains Down for an exponentially distributed duration with mean:

[  
\frac{1}{\beta}  
]

This provides a simple model of network reliability.

---

# 28. CTMC Transition Probabilities

Define:

# [  
P_{ij}(t)

P(X(t)=j\mid X(0)=i)  
]

The transition probability matrix is:

# [  
P(t)

\left[P_{ij}(t)\right]  
]

For a time-homogeneous CTMC:

[  
\boxed{  
P(t)=e^{Qt}  
}  
]

where (e^{Qt}) is the matrix exponential.

This is the continuous-time counterpart of:

[  
P^n  
]

for a DTMC.

---

# 29. Kolmogorov Equations

The transition probabilities of a CTMC satisfy the **Kolmogorov forward and backward equations**.

### Forward equation

# [  
\boxed{  
\frac{dP(t)}{dt}

P(t)Q  
}  
]

### Backward equation

# [  
\boxed{  
\frac{dP(t)}{dt}

QP(t)  
}  
]

For time-homogeneous CTMCs, these relationships lead to:

[  
P(t)=e^{Qt}  
]

These equations are fundamental for transient analysis.

---

# 30. State Probability Evolution

Let:

[  
\pi(t)  
]

be the row vector of state probabilities.

Then:

# [  
\boxed{  
\frac{d\pi(t)}{dt}

\pi(t)Q  
}  
]

and:

[  
\boxed{  
\pi(t)=\pi(0)e^{Qt}  
}  
]

This is the continuous-time equivalent of the DTMC relation:

# [  
\pi^{(n)}

\pi^{(0)}P^n  
]

---

# 31. Stationary Distribution of a CTMC

A stationary distribution satisfies:

[  
\boxed{  
\pi Q=0  
}  
]

with:

[  
\sum_i\pi_i=1  
]

and:

[  
\pi_i\ge0  
]

Compare the two fundamental equations:

### DTMC

[  
\boxed{  
\pi P=\pi  
}  
]

### CTMC

[  
\boxed{  
\pi Q=0  
}  
]

This distinction must be memorized and, more importantly, understood.

---

# 32. Example — Link Availability

Consider the two-state CTMC:

[  
Q=  
\begin{bmatrix}  
-\alpha&\alpha\  
\beta&-\beta  
\end{bmatrix}  
]

The stationary equations are:

[  
-\alpha\pi_U+\beta\pi_D=0  
]

and:

[  
\pi_U+\pi_D=1  
]

Therefore:

[  
\alpha\pi_U=\beta\pi_D  
]

and:

[  
\boxed{  
\pi_U=  
\frac{\beta}{\alpha+\beta}  
}  
]

[  
\boxed{  
\pi_D=  
\frac{\alpha}{\alpha+\beta}  
}  
]

The long-term availability of the link is therefore:

[  
\boxed{  
A=  
\frac{\beta}{\alpha+\beta}  
}  
]

This is an important direct application of CTMCs to network reliability.

---

# 33. Birth-Death Processes

A particularly important class of CTMCs is the **birth-death process**.

The state is generally:

[  
n=0,1,2,\ldots  
]

The only possible transitions are:

[  
n\rightarrow n+1  
]

and:

[  
n\rightarrow n-1  
]

The rates are:

[  
\lambda_n  
]

for upward transitions, and:

[  
\mu_n  
]

for downward transitions.

---

# 34. Birth-Death Model of a Queue

Consider a queue where:

- packets arrive at rate (\lambda);
    
- packets are served at rate (\mu).
    

Let:

[  
X(t)=\text{number of packets in the system}  
]

Then:

[  
n  
\xrightarrow{\lambda}  
n+1  
]

and:

[  
n  
\xrightarrow{\mu}  
n-1  
]

for:

[  
n\ge1  
]

This is the classical **(M/M/1) queue**.

The notation will be studied in depth in the queueing-theory chapter.

---

# 35. Stationary Distribution of an (M/M/1) Queue

Define:

[  
\rho=\frac{\lambda}{\mu}  
]

For stability:

[  
\boxed{  
\rho<1  
}  
]

The stationary probability that the system contains (n) packets is:

[  
\boxed{  
\pi_n=(1-\rho)\rho^n  
}  
]

for:

[  
n=0,1,2,\ldots  
]

This is a geometric distribution.

---

# 36. Example — Queue Occupancy

Suppose:

[  
\lambda=80\text{ packets/s}  
]

and:

[  
\mu=100\text{ packets/s}  
]

Then:

[  
\rho=\frac{80}{100}=0.8  
]

Therefore:

[  
\pi_0=1-\rho=0.2  
]

The probability that there are exactly 3 packets in the system is:

# [  
\pi_3

(1-0.8)(0.8)^3  
]

[  
=0.2(0.512)  
]

[  
\boxed{  
\pi_3=0.1024  
}  
]

Thus, under this model, the probability of exactly three packets in the system is 10.24%.

---

# 37. Probability of Queue Overflow

Suppose the queue has finite capacity:

[  
B  
]

Then states are:

[  
S={0,1,\ldots,B}  
]

An arrival when the system is already at state (B) cannot be accepted.

Therefore:

[  
P_{\text{loss}}  
]

is related to the probability that the system is full.

For a finite-capacity birth-death model:

[  
\boxed{  
P_{\text{loss}}=\pi_B  
}  
]

under the basic assumption that every arrival finding a full system is lost.

This provides a direct connection between a Markov chain and packet-loss performance.

---

# 38. Application to Wireless Networks

Markov chains are especially useful for modeling wireless channel states.

Suppose:

[  
S={G,M,B}  
]

where:

- (G) = high-quality channel;
    
- (M) = medium-quality channel;
    
- (B) = poor-quality channel.
    

Transitions can represent changes due to:

- fading;
    
- interference;
    
- mobility;
    
- shadowing.
    

The stationary probabilities:

[  
\pi_G,\pi_M,\pi_B  
]

represent the long-term proportion of time spent in each channel condition.

If each state has a corresponding rate:

[  
R_G,R_M,R_B  
]

then:

# [  
E[R]

\pi_GR_G  
+  
\pi_MR_M  
+  
\pi_BR_B  
]

This can be used as an approximation of long-term average throughput.

---

# 39. Application to Handover

A mobile user can be represented using states such as:

[  
S=  
{  
\text{Connected},  
\text{Handover},  
\text{Disconnected}  
}  
]

Transitions may represent:

[  
\text{Connected}  
\rightarrow  
\text{Handover}  
]

[  
\text{Handover}  
\rightarrow  
\text{Connected}  
]

or:

[  
\text{Handover}  
\rightarrow  
\text{Disconnected}  
]

A Markov model can estimate:

- probability of handover failure;
    
- probability of disconnection;
    
- average time spent in each state;
    
- long-term connection availability.
    

---

# 40. Application to Network Reliability

Consider a communication link that can be:

[  
S={\text{Up},\text{Down}}  
]

A CTMC can model:

[  
\text{Up}  
\xrightarrow{\lambda_f}  
\text{Down}  
]

and:

[  
\text{Down}  
\xrightarrow{\lambda_r}  
\text{Up}  
]

where:

- (\lambda_f) = failure rate;
    
- (\lambda_r) = repair/recovery rate.
    

The stationary availability is:

[  
A=  
\frac{\lambda_r}  
{\lambda_f+\lambda_r}  
]

and the unavailability is:

[  
U=  
\frac{\lambda_f}  
{\lambda_f+\lambda_r}  
]

Therefore:

[  
\boxed{  
A+U=1  
}  
]

This is a simple but powerful example of performance and reliability evaluation using CTMCs.

---

# 41. DTMC vs. CTMC

The distinction should be completely clear.

|Characteristic|DTMC|CTMC|
|---|---|---|
|Time|Discrete|Continuous|
|State changes|At discrete steps|At arbitrary times|
|Main parameter|Transition probability (P_{ij})|Transition rate (q_{ij})|
|Matrix|(P)|(Q)|
|State evolution|(\pi^{(n+1)}=\pi^{(n)}P)|(\frac{d\pi(t)}{dt}=\pi(t)Q)|
|Stationarity|(\pi P=\pi)|(\pi Q=0)|
|Multi-step/temporal evolution|(P^n)|(e^{Qt})|
|Holding time|Not central|Exponential|

A useful memory aid is:

[  
\boxed{  
\text{DTMC}\rightarrow P  
}  
]

[  
\boxed{  
\text{CTMC}\rightarrow Q  
}  
]

---

# 42. Modeling Decision: DTMC or CTMC?

The choice depends primarily on how time and events occur.

### DTMC is appropriate when:

- the system is observed at regular time steps;
    
- decisions occur in discrete slots;
    
- time is naturally divided into frames;
    
- state transitions are naturally associated with discrete events or rounds.
    

Examples:

- wireless channel sampled every 1 ms;
    
- discrete routing decisions;
    
- slotted communication systems.
    

### CTMC is appropriate when:

- events occur at arbitrary times;
    
- arrivals and departures are asynchronous;
    
- event durations matter;
    
- exponential holding times provide a reasonable approximation.
    

Examples:

- packet queues;
    
- call arrivals and departures;
    
- link failures and repairs;
    
- numbers of active connections.
    

---

# 43. Markov Reward Models

A Markov chain can be extended by assigning a reward or cost to each state.

Let:

[  
r_i  
]

be the reward associated with state (i).

For a stationary distribution (\pi), the long-term average reward is:

[  
\boxed{  
R=  
\sum_i\pi_i r_i  
}  
]

In network performance evaluation, the reward could represent:

- throughput;
    
- energy consumption;
    
- delay cost;
    
- channel capacity;
    
- number of active users;
    
- packet loss cost.
    

This provides a general framework for turning Markov states into performance metrics.

---

# 44. Example — Average Throughput Using a CTMC

Suppose a wireless system has three states:

[  
S={G,M,B}  
]

with rates:

[  
R_G=30\text{ Mbit/s}  
]

[  
R_M=15\text{ Mbit/s}  
]

[  
R_B=3\text{ Mbit/s}  
]

Suppose the stationary distribution is:

[  
\pi=  
[0.5,0.3,0.2]  
]

Then:

# [  
E[R]

0.5(30)+0.3(15)+0.2(3)  
]

# [  
E[R]

15+4.5+0.6  
]

Therefore:

[  
\boxed{  
E[R]=20.1\text{ Mbit/s}  
}  
]

This is a Markov reward calculation.

---

# 45. Markov Models and Network Performance Evaluation

The general process is:

[  
\boxed{  
\text{Network}  
\rightarrow  
\text{States}  
\rightarrow  
\text{Transitions}  
\rightarrow  
\text{Markov Model}  
\rightarrow  
\text{Stationary Distribution}  
\rightarrow  
\text{Performance Metrics}  
}  
]

For example:

### Queue

[  
\text{State}= \text{number of packets}  
]

### Wireless channel

[  
\text{State}= \text{channel quality}  
]

### Reliability

[  
\text{State}= \text{Up/Down}  
]

### User activity

[  
\text{State}= \text{Idle/Active}  
]

The model is useful only if the state definition captures the information necessary to predict future behavior.

---

# 46. Important Modeling Principle: State Design

One of the most difficult parts of Markov modeling is not solving the equations.

It is choosing the correct state.

Suppose the future behavior of a network depends on:

- current queue length;
    
- current channel condition;
    
- current user class.
    

If the model stores only queue length, the Markov property may fail.

A better state could be:

[  
X(t)=  
(\text{queue length},  
\text{channel state},  
\text{user class})  
]

However, increasing the number of state variables causes **state-space explosion**.

If variable (X_1) has (n_1) states and (X_2) has (n_2) states, their combined state space may contain:

[  
n_1n_2  
]

states.

With several variables:

# [  
|S|

\prod_{k=1}^{K}n_k  
]

This can rapidly make exact analysis computationally difficult.

---

# 47. State-Space Explosion

Suppose a network model includes:

- 20 queue states;
    
- 5 channel states;
    
- 10 user states.
    

Then:

# [  
|S|

# 20\times5\times10

1000  
]

states.

If another variable with 20 states is added:

# [  
|S|

# 1000\times20

20,000  
]

states.

This phenomenon is known as **state-space explosion**.

It is one of the major limitations of Markov modeling for complex networks.

Possible solutions include:

- state aggregation;
    
- approximation;
    
- decomposition;
    
- simulation;
    
- reduced-order models.
    

---

# 48. Common Mistakes

## Mistake 1 — Confusing probabilities and rates

DTMC:

[  
P_{ij}  
]

is a probability.

CTMC:

[  
q_{ij}  
]

is a rate.

They are not interchangeable.

---

## Mistake 2 — Forgetting normalization

A stationary probability vector must satisfy:

[  
\sum_i\pi_i=1  
]

---

## Mistake 3 — Using the wrong stationary equation

For DTMC:

[  
\boxed{\pi P=\pi}  
]

For CTMC:

[  
\boxed{\pi Q=0}  
]

---

## Mistake 4 — Ignoring the state definition

The Markov property depends on the state containing sufficient information about the system.

---

## Mistake 5 — Treating Markov models as universally realistic

A Markov model is an abstraction.

If real network traffic exhibits strong memory, long-range dependence, or non-exponential durations, a simple Markov model may be inadequate.

---

# 49. Essential Equations

## DTMC

Transition probability:

[  
\boxed{  
P_{ij}=P(X_{n+1}=j\mid X_n=i)  
}  
]

State evolution:

[  
\boxed{  
\pi^{(n+1)}=\pi^{(n)}P  
}  
]

After (n) steps:

[  
\boxed{  
\pi^{(n)}=\pi^{(0)}P^n  
}  
]

Stationary distribution:

[  
\boxed{  
\pi=\pi P  
}  
]

---

## CTMC

Transition rate:

# [  
\boxed{  
q_{ij}

\lim_{\Delta t\to0}  
\frac{P(X(t+\Delta t)=j\mid X(t)=i)}  
{\Delta t}  
}  
]

Generator diagonal:

# [  
\boxed{  
q_{ii}

-\sum_{j\ne i}q_{ij}  
}  
]

State evolution:

# [  
\boxed{  
\frac{d\pi(t)}{dt}

\pi(t)Q  
}  
]

Solution:

[  
\boxed{  
\pi(t)=\pi(0)e^{Qt}  
}  
]

Stationary distribution:

[  
\boxed{  
\pi Q=0  
}  
]

---

# 50. Chapter Summary

Markov chains provide a powerful framework for modeling network systems with state-dependent stochastic behavior.

The central principle is:

[  
\boxed{  
\text{The current state contains the relevant information needed to model the future.}  
}  
]

Two major models are used.

## Discrete-Time Markov Chains

A DTMC evolves at discrete time instants:

[  
X_0,X_1,X_2,\ldots  
]

and is characterized by a transition probability matrix:

[  
P  
]

with:

[  
\pi^{(n+1)}=\pi^{(n)}P  
]

and stationary distribution:

[  
\pi P=\pi  
]

---

## Continuous-Time Markov Chains

A CTMC evolves continuously in time:

[  
X(t),\quad t\ge0  
]

and is characterized by a generator matrix:

[  
Q  
]

with:

# [  
\frac{d\pi(t)}{dt}

\pi(t)Q  
]

and stationary distribution:

[  
\pi Q=0  
]

---

# 51. Network Applications

Markov chains can model:

[  
\boxed{  
\begin{array}{c}  
\text{Queues}\  
\text{Wireless channels}\  
\text{Handover}\  
\text{Link reliability}\  
\text{User activity}\  
\text{Congestion}\  
\text{Network failures}\  
\text{Resource occupancy}  
\end{array}  
}  
]

The general methodology is:

[  
\boxed{  
\text{Define states}  
\rightarrow  
\text{Define transitions}  
\rightarrow  
\text{Construct }P\text{ or }Q  
\rightarrow  
\text{Solve state probabilities}  
\rightarrow  
\text{Calculate performance metrics}  
}  
]

The most important conceptual distinction to retain is:

[  
\boxed{  
\textbf{DTMC: transition probabilities}  
}  
]

versus:

[  
\boxed{  
\textbf{CTMC: transition rates}  
}  
]

---

# 52. Exercises

## Exercise 1 — Transition Matrix

Consider a network device with three states:

[  
S={I,B,O}  
]

where:

- (I) = Idle;
    
- (B) = Busy;
    
- (O) = Overloaded.
    

The transition matrix is:

[  
P=  
\begin{bmatrix}  
0.6&0.3&0.1\  
0.2&0.6&0.2\  
0.1&0.2&0.7  
\end{bmatrix}  
]

Answer the following:

1. What is the probability of going from Busy to Overloaded in one step?
    
2. What is the probability of remaining Overloaded?
    
3. Verify that (P) is a valid transition matrix.
    
4. If the device is initially Idle, determine the state probability vector after one transition.
    

---

## Exercise 2 — Two-Step Transition

Using the matrix from Exercise 1, calculate:

[  
P^2  
]

Interpret the element:

[  
[P^2]_{IO}  
]

in terms of the network.

---

## Exercise 3 — Stationary Distribution

For:

[  
P=  
\begin{bmatrix}  
0.8&0.2\  
0.3&0.7  
\end{bmatrix}  
]

calculate the stationary distribution:

[  
\pi=[\pi_0,\pi_1]  
]

using:

[  
\pi P=\pi  
]

and:

[  
\pi_0+\pi_1=1  
]

---

## Exercise 4 — Markov Channel

A wireless channel has two states:

- Good;
    
- Bad.
    

Its transition matrix is:

[  
P=  
\begin{bmatrix}  
0.95&0.05\  
0.20&0.80  
\end{bmatrix}  
]

Calculate:

1. The stationary probability of the Good state.
    
2. The stationary probability of the Bad state.
    
3. If the Good state provides 20 Mbit/s and the Bad state provides 2 Mbit/s, calculate the long-term average rate.
    

---

## Exercise 5 — CTMC Generator

A network link can be:

- Up;
    
- Down.
    

The failure rate is:

[  
\lambda_f=0.02\text{ s}^{-1}  
]

and the repair rate is:

[  
\lambda_r=0.5\text{ s}^{-1}  
]

1. Construct the generator matrix (Q).
    
2. Calculate the stationary availability.
    
3. Calculate the stationary unavailability.
    

---

## Exercise 6 — Queue as a Birth-Death Process

A router receives packets at:

[  
\lambda=50\text{ packets/s}  
]

and processes packets at:

[  
\mu=100\text{ packets/s}  
]

Assume an (M/M/1) model.

Calculate:

1. (\rho).
    
2. (P_0).
    
3. (P_1).
    
4. (P_2).
    
5. The probability that at least 3 packets are in the system.
    

---

## Exercise 7 — Markov Model Design

Design a Markov model for a mobile user with the following conditions:

- the user is disconnected;
    
- the user is connected;
    
- the user is undergoing handover;
    
- the handover can succeed or fail.
    

Define:

1. The state space.
    
2. Possible transitions.
    
3. The type of Markov chain you would use.
    
4. At least three performance metrics that could be derived from the model.
    

Explain your choices.

---

# 53. Mentor Checkpoint

Before proceeding to queueing theory, make sure you can answer these questions.

### 1. What makes a process Markovian?

### 2. What is the difference between a transition probability and a transition rate?

### 3. Why does a DTMC use (P), while a CTMC uses (Q)?

### 4. What equations define the stationary distribution?

[  
\pi P=\pi  
]

versus:

[  
\pi Q=0  
]

### 5. Why are birth-death processes particularly useful for network queues?

### 6. Why does the definition of the state matter?

### 7. What is state-space explosion, and why does it become a problem in complex networks?

### 8. How can a Markov chain be transformed into a network performance metric?

The key mental model to retain is:

$$
\boxed{  
\text{Network behavior}  
\rightarrow  
\text{States}  
\rightarrow  
\text{Transitions}  
\rightarrow  
\text{Probability distribution}  
\rightarrow  
\text{Performance}  
}  
$$

This is the foundation for the next major subject: **queueing systems and queueing theory**, where Markov chains become a direct tool for deriving delay, throughput, queue-length, utilization, and packet-loss results.