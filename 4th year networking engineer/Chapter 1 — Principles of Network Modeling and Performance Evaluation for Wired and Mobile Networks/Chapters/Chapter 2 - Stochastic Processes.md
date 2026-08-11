# Chapter 2 — Stochastic Processes

## 1. Introduction

Network performance is inherently uncertain.

Packets do not necessarily arrive at perfectly regular intervals. Users generate traffic at different times. Packet sizes vary. Wireless channels fluctuate. Transmission times may change. Users move unpredictably.

To model these phenomena mathematically, we use **stochastic processes**.

A stochastic process provides a mathematical framework for describing the evolution of a random phenomenon over time.

This chapter introduces the fundamental concepts required to model network traffic and network states as stochastic processes.

The main topics are:

- random variables and probability distributions;
    
- random processes;
    
- discrete-time and continuous-time processes;
    
- stationary processes;
    
- independent and identically distributed processes;
    
- counting processes;
    
- Poisson processes;
    
- Markov processes;
    
- birth-death processes;
    
- autocorrelation and dependence;
    
- stochastic-process applications to computer networks.
    

---

# 2. Random Variables

Before studying stochastic processes, we need to understand random variables.

## 2.1 Definition

A **random variable** is a numerical quantity whose value depends on the outcome of a random experiment.

It is commonly denoted by:

[  
X  
]

For example, consider the number of packets arriving during a one-second interval.

We could define:

[  
X=\text{number of packet arrivals during 1 second}  
]

The possible values might be:

[  
X\in{0,1,2,3,\ldots}  
]

Another example is packet transmission time:

[  
X=\text{packet transmission time}  
]

which may take continuous values:

[  
X\in[0,\infty)  
]

---

# 3. Discrete and Continuous Random Variables

## 3.1 Discrete random variable

A discrete random variable takes values from a finite or countably infinite set.

Examples:

- number of packets arriving;
    
- number of active users;
    
- number of packets in a queue;
    
- number of retransmissions.
    

For a discrete random variable, we use the **probability mass function (PMF)**:

[  
p_X(x)=P(X=x)  
]

and:

[  
\sum_x p_X(x)=1  
]

---

## 3.2 Continuous random variable

A continuous random variable can take values in an interval.

Examples:

- packet delay;
    
- transmission time;
    
- inter-arrival time;
    
- signal power.
    

For continuous random variables, we use a **probability density function (PDF)**:

[  
f_X(x)  
]

The probability that (X) belongs to an interval ([a,b]) is:

# [  
P(a\le X\le b)

\int_a^b f_X(x),dx  
]

and:

[  
\int_{-\infty}^{+\infty}f_X(x),dx=1  
]

---

# 4. Cumulative Distribution Function

The **Cumulative Distribution Function (CDF)** of (X) is:

[  
F_X(x)=P(X\le x)  
]

For a continuous random variable:

[  
F_X(x)=  
\int_{-\infty}^{x}f_X(u),du  
]

The CDF is useful for performance analysis because it provides more information than the average alone.

For example:

[  
P(D\le 50\text{ ms})  
]

answers the question:

> What proportion of packets experience a delay of at most 50 ms?

This can be more informative than simply knowing:

[  
E[D]=30\text{ ms}  
]

---

# 5. Expected Value

The expected value represents the long-term average value of a random variable.

For a discrete random variable:

# [  
E[X]

\sum_x xP(X=x)  
]

For a continuous random variable:

# [  
E[X]

\int_{-\infty}^{+\infty}  
x f_X(x),dx  
]

In network performance analysis, expected values are frequently used for:

- average delay;
    
- average queue length;
    
- average packet size;
    
- average service time;
    
- average number of active users.
    

---

# 6. Variance

The variance measures the dispersion of a random variable around its mean.

It is defined as:

# [  
\operatorname{Var}(X)

E[(X-E[X])^2]  
]

An equivalent expression is:

# [  
\operatorname{Var}(X)

E[X^2]-(E[X])^2  
]

The standard deviation is:

[  
\sigma_X=\sqrt{\operatorname{Var}(X)}  
]

Two network systems can have the same average delay but very different variances.

For example:

System A:

[  
D=20,20,20,20\text{ ms}  
]

System B:

[  
D=5,5,5,65\text{ ms}  
]

Both can have the same average delay, but System B has significantly greater variability.

---

# 7. From Random Variables to Stochastic Processes

A random variable describes uncertainty at one point.

A **stochastic process** describes the evolution of a random phenomenon over time.

We can represent a stochastic process as:

[  
{X(t),t\in T}  
]

where:

- (X(t)) is the random variable representing the state at time (t);
    
- (T) is the time index set.
    

For example:

[  
X(t)=\text{number of packets in a router queue at time }t  
]

Then:

[  
X(0),X(1),X(2),\ldots  
]

describes how the queue evolves over time.

---

# 8. Sample Path

A **sample path** is one particular realization of a stochastic process.

Suppose:

[  
X(t)=\text{number of packets in a queue}  
]

One possible realization could be:

[  
0\rightarrow1\rightarrow2\rightarrow1\rightarrow3\rightarrow2\rightarrow0  
]

Another realization might be:

[  
0\rightarrow0\rightarrow1\rightarrow1\rightarrow2\rightarrow4\rightarrow3  
]

The stochastic process represents all possible realizations and their probabilities.

This distinction is important:

# [  
\boxed{  
\text{Stochastic process}

\text{collection of possible sample paths}  
}  
]

---

# 9. Discrete-Time Stochastic Processes

A stochastic process is **discrete-time** when its index is discrete.

For example:

[  
{X_n:n=0,1,2,\ldots}  
]

An example is the number of packets observed every second:

[  
X_n=  
\text{number of packets during second }n  
]

The process could be:

[  
4,7,5,9,3,6,\ldots  
]

Discrete-time processes are frequently used when observations are made at regular intervals.

---

# 10. Continuous-Time Stochastic Processes

A process is **continuous-time** when its index can take any value in an interval:

[  
{X(t):t\ge0}  
]

For example:

[  
X(t)=\text{number of packets currently in a queue}  
]

The queue can change at arbitrary times when packets arrive or depart.

This makes continuous-time stochastic processes particularly useful for network queueing models.

---

# 11. State Space

The **state space** is the set of possible values that a stochastic process can take.

For example, if:

[  
X(t)=\text{number of packets in a queue}  
]

and the queue is unlimited:

[  
S={0,1,2,3,\ldots}  
]

If the queue capacity is 10 packets:

[  
S={0,1,2,\ldots,10}  
]

A process can therefore be classified according to:

1. its time domain;
    
2. its state space.
    

For example:

|Time|State|Example|
|---|---|---|
|Discrete|Discrete|Number of packets per second|
|Discrete|Continuous|Measured signal value per sample|
|Continuous|Discrete|Number of packets in queue|
|Continuous|Continuous|Received signal power|

---

# 12. Independence

Two random variables (X) and (Y) are independent if:

# [  
P(X\in A,Y\in B)

P(X\in A)P(Y\in B)  
]

for appropriate sets (A) and (B).

Informally:

> Knowing the value of one variable gives no information about the other.

In network modeling, independence is often used as a simplifying assumption.

However, real network traffic may exhibit strong dependence.

---

# 13. Independent and Identically Distributed Variables

A sequence:

[  
X_1,X_2,\ldots,X_n  
]

is called **independent and identically distributed (i.i.d.)** if:

1. all variables have the same probability distribution;
    
2. all variables are mutually independent.
    

We write:

[  
X_i\overset{\text{i.i.d.}}{\sim}F  
]

The i.i.d. assumption is important in many analytical models.

However, it should not be used blindly for network traffic because traffic can exhibit temporal correlations, burstiness, and long-range dependence.

---

# 14. Stationarity

Stationarity describes whether the statistical behavior of a stochastic process changes over time.

## 14.1 Strict-sense stationarity

A stochastic process (X(t)) is strictly stationary if its joint probability distributions are invariant under time shifts.

Conceptually:

[  
(X(t_1),X(t_2),\ldots,X(t_n))  
]

has the same distribution as:

[  
(X(t_1+\tau),X(t_2+\tau),\ldots,X(t_n+\tau))  
]

for any time shift (\tau).

In simple terms:

> The probabilistic behavior of the process does not depend on when we observe it.

---

# 15. Wide-Sense Stationarity

In many engineering applications, a weaker concept called **Wide-Sense Stationarity (WSS)** is sufficient.

A process is WSS if:

### Constant mean

[  
E[X(t)]=\mu  
]

for all (t).

### Autocorrelation depends only on time difference

# [  
R_X(t_1,t_2)

R_X(t_2-t_1)  
]

or:

[  
R_X(\tau)  
]

where:

[  
\tau=t_2-t_1  
]

WSS is widely used in signal processing and communication-system analysis.

---

# 16. Autocorrelation

Autocorrelation measures the statistical relationship between values of a process at different times.

For a process (X(t)):

# [  
R_X(t_1,t_2)

E[X(t_1)X(t_2)]  
]

For a stationary process:

# [  
R_X(\tau)

E[X(t)X(t+\tau)]  
]

where (\tau) is the time lag.

If the process has strong temporal dependence, values close together in time tend to be similar.

---

# 17. Why Autocorrelation Matters in Networks

Consider packet arrivals.

Suppose the traffic rate at time (t) is high.

If the traffic rate is also likely to remain high shortly afterward, then the traffic is correlated.

This produces **bursty traffic**.

For example:

[  
\text{low}\rightarrow  
\text{high}\rightarrow  
\text{high}\rightarrow  
\text{high}\rightarrow  
\text{low}  
]

Such traffic can produce:

- queue buildup;
    
- increased delay;
    
- buffer overflow;
    
- packet loss.
    

Therefore, two traffic models with the same average arrival rate can produce very different network performance.

---

# 18. Counting Processes

A **counting process** (N(t)) counts the number of events that have occurred up to time (t).

For example:

# [  
N(t)

\text{number of packets that arrived by time }t  
]

A counting process must satisfy:

[  
N(t)\ge0  
]

and generally:

[  
N(t_2)\ge N(t_1)  
]

whenever:

[  
t_2>t_1  
]

because events accumulate.

---

# 19. Inter-Arrival Times

Let:

[  
T_1,T_2,T_3,\ldots  
]

represent the arrival times of packets.

The **inter-arrival time** is:

[  
A_n=T_n-T_{n-1}  
]

For example:

[  
T_1=2\text{ ms}  
]

[  
T_2=7\text{ ms}  
]

then:

[  
A_2=7-2=5\text{ ms}  
]

Inter-arrival times are fundamental variables in traffic modeling.

---

# 20. Poisson Process

The **Poisson process** is one of the most important stochastic processes in queueing theory and network performance analysis.

It is commonly used to model event arrivals.

Examples:

- packet arrivals;
    
- call arrivals;
    
- session arrivals;
    
- requests to a server.
    

Let:

[  
N(t)  
]

be a Poisson process with rate:

[  
\lambda>0  
]

Then the number of arrivals during an interval of length (t) follows a Poisson distribution:

# [  
P(N(t)=n)

\frac{(\lambda t)^n}{n!}  
e^{-\lambda t}  
]

for:

[  
n=0,1,2,\ldots  
]

---

# 21. Properties of the Poisson Process

The standard Poisson process has several important properties.

## Property 1 — Initial condition

[  
N(0)=0  
]

---

## Property 2 — Independent increments

The number of events occurring during non-overlapping time intervals is independent.

For example:

[  
N(2)-N(1)  
]

is independent of:

[  
N(4)-N(3)  
]

---

## Property 3 — Stationary increments

The distribution of the number of arrivals depends only on the length of the interval, not its position in time.

Thus:

[  
N(t+\tau)-N(t)  
]

depends on (\tau), not (t).

---

## Property 4 — No simultaneous arrivals

In the standard Poisson model, the probability of two or more arrivals occurring in an infinitesimally small interval is negligible.

---

# 22. Mean and Variance of a Poisson Process

For:

[  
N(t)\sim\operatorname{Poisson}(\lambda t)  
]

we have:

[  
E[N(t)]=\lambda t  
]

and:

[  
\operatorname{Var}[N(t)]=\lambda t  
]

Therefore:

[  
\boxed{  
E[N(t)]=\operatorname{Var}[N(t)]  
}  
]

This equality is an important property of the Poisson distribution.

---

# 23. Example — Poisson Packet Arrivals

Suppose packets arrive according to a Poisson process with:

[  
\lambda=100\text{ packets/s}  
]

What is the probability that exactly 2 packets arrive during:

[  
t=0.01\text{ s}?  
]

First:

# [  
\lambda t

# 100(0.01)

1  
]

Therefore:

# [  
P(N(0.01)=2)

\frac{1^2}{2!}e^{-1}  
]

# [

\frac{e^{-1}}{2}  
]

approximately:

[  
\boxed{P(N(0.01)=2)\approx0.184}  
]

So there is approximately an 18.4% probability of exactly two arrivals during that 10 ms interval.

---

# 24. Exponential Distribution

A key property of the Poisson process is that its inter-arrival times are exponentially distributed.

Let:

[  
A\sim\operatorname{Exponential}(\lambda)  
]

Then the PDF is:

# [  
f_A(a)

\lambda e^{-\lambda a},  
\qquad a\ge0  
]

The CDF is:

# [  
F_A(a)

1-e^{-\lambda a}  
]

The mean is:

[  
E[A]=\frac{1}{\lambda}  
]

and the variance is:

# [  
\operatorname{Var}(A)

\frac{1}{\lambda^2}  
]

---

# 25. Memoryless Property

The exponential distribution has a unique property called **memorylessness**.

For (s,t\ge0):

# [  
P(A>s+t\mid A>s)

P(A>t)  
]

In words:

> The probability that we must wait an additional (t) units of time does not depend on how long we have already waited.

This property is extremely important in Markovian queueing models.

---

# 26. Poisson Process and Exponential Inter-Arrival Times

The relationship can be summarized as:

[  
\boxed{  
\text{Poisson arrivals}  
\Longleftrightarrow  
\text{Exponential inter-arrival times}  
}  
]

More precisely, a standard Poisson process has i.i.d. exponentially distributed inter-arrival times.

This relationship forms one of the foundations of classical queueing theory.

---

# 27. Markov Processes

A **Markov process** is a stochastic process whose future depends on the present state but not on the complete history.

This is known as the **Markov property**.

Mathematically:

# [  
P(X_{t+\Delta}=x  
\mid  
X_t,\text{past history})

P(X_{t+\Delta}=x\mid X_t)  
]

Conceptually:

[  
\boxed{  
\text{Future}  
\leftarrow  
\text{Present State}  
}  
]

rather than:

[  
\text{Future}  
\leftarrow  
\text{Entire History}  
]

---

# 28. Example of the Markov Property

Consider a queue where:

[  
X(t)=\text{number of packets currently in the system}  
]

If the future arrival and departure behavior depends only on the current number of packets, then (X(t)) may be modeled as a Markov process.

For example, if:

[  
X(t)=5  
]

the probability of the next transition depends on the current state (5), rather than on whether the queue previously contained 2, 3, or 10 packets.

---

# 29. Markov Chains

A **Markov chain** is a Markov process with a discrete state space.

For example:

[  
S={0,1,2,3}  
]

could represent:

- 0 = idle;
    
- 1 = lightly loaded;
    
- 2 = moderately loaded;
    
- 3 = heavily loaded.
    

The system transitions from one state to another according to transition probabilities.

---

# 30. Transition Probability

For a discrete-time Markov chain:

# [  
P_{ij}

P(X_{n+1}=j\mid X_n=i)  
]

This represents the probability of moving from state (i) to state (j).

The transition matrix is:

[  
P=  
\begin{bmatrix}  
P_{00}&P_{01}&\cdots\  
P_{10}&P_{11}&\cdots\  
\vdots&\vdots&\ddots  
\end{bmatrix}  
]

Every row must satisfy:

[  
\sum_j P_{ij}=1  
]

and:

[  
P_{ij}\ge0  
]

---

# 31. Continuous-Time Markov Chains

A **Continuous-Time Markov Chain (CTMC)** has:

- continuous time;
    
- discrete states.
    

This combination is particularly important in queueing theory.

For example:

[  
X(t)=\text{number of packets in a queue}  
]

where:

[  
X(t)\in{0,1,2,\ldots}  
]

The queue can change at arbitrary times because arrivals and departures occur continuously.

---

# 32. Birth-Death Process

A **birth-death process** is a special type of continuous-time Markov chain in which transitions occur only between neighboring states.

From state (n), the system can move to:

[  
n-1  
]

or:

[  
n+1  
]

For example, in a queue:

- arrival = birth;
    
- departure = death.
    

The transitions can be represented as:

[  
\cdots  
\leftrightarrow  
n-1  
\leftrightarrow  
n  
\leftrightarrow  
n+1  
\leftrightarrow  
\cdots  
]

---

# 33. Birth and Death Rates

Let:

[  
\lambda_n  
]

be the birth rate in state (n), and:

[  
\mu_n  
]

be the death rate in state (n).

Then:

[  
n  
\xrightarrow{\lambda_n}  
n+1  
]

and:

[  
n  
\xrightarrow{\mu_n}  
n-1  
]

In a simple (M/M/1) queue:

[  
\lambda_n=\lambda  
]

and:

[  
\mu_n=\mu  
]

for the appropriate states.

---

# 34. Network Interpretation of Stochastic Processes

Stochastic processes can represent almost every major dynamic component of a network.

|Network phenomenon|Possible stochastic model|
|---|---|
|Packet arrivals|Poisson process|
|Inter-arrival times|Exponential distribution|
|Queue length|CTMC|
|User mobility|Random process / mobility model|
|Wireless fading|Stochastic process|
|Channel state|Markov chain|
|Call arrivals|Poisson process|
|Session duration|Random variable|
|Server state|Markov process|
|Network failures|Renewal process / Markov model|

This illustrates why stochastic processes are fundamental to network performance evaluation.

---

# 35. Traffic Models

One of the most important applications is modeling network traffic.

A simple traffic model might assume:

[  
N(t)\sim\operatorname{Poisson}(\lambda t)  
]

This means that traffic arrivals are modeled as a Poisson process.

However, real network traffic may exhibit:

- burstiness;
    
- correlation;
    
- heavy-tailed behavior;
    
- self-similarity;
    
- long-range dependence.
    

Therefore, the Poisson model is useful but not universally accurate.

---

# 36. Burstiness

A traffic process is considered bursty when packets arrive in clusters separated by periods of lower activity.

For example:

[  
\underbrace{11111111}_{\text{burst}}  
\rightarrow  
000  
\rightarrow  
\underbrace{111111}_{\text{burst}}  
]

A simple Poisson process may not adequately reproduce this behavior.

Burstiness can cause:

[  
\text{Burst}  
\rightarrow  
\text{Queue buildup}  
\rightarrow  
\text{Buffer overflow}  
\rightarrow  
\text{Packet loss}  
]

even when the average traffic rate is below the link capacity.

---

# 37. Correlation and Long-Range Dependence

In some traffic models, observations separated by large time intervals can remain statistically dependent.

This phenomenon is called **long-range dependence**.

It is particularly relevant in the modeling of certain aggregate data-network traffic.

The important lesson is:

> The average arrival rate alone may be insufficient to characterize network traffic.

Two traffic sources can have identical:

[  
E[\lambda]  
]

but produce very different:

[  
D,\quad  
P_{\text{loss}},\quad  
Q  
]

where (Q) represents queue occupancy.

---

# 38. Renewal Processes

A **renewal process** is a counting process in which the inter-arrival times are i.i.d.

Let:

[  
A_1,A_2,\ldots  
]

be i.i.d. inter-arrival times.

Define:

[  
T_n=A_1+A_2+\cdots+A_n  
]

as the time of the (n)-th event.

Then:

[  
N(t)=\max{n:T_n\le t}  
]

is a renewal counting process.

The Poisson process is a special case of a renewal process in which:

[  
A_i\sim\operatorname{Exponential}(\lambda)  
]

---

# 39. Why the Choice of Process Matters

Suppose two networks have:

[  
\lambda=1000\text{ packets/s}  
]

Both therefore have the same average arrival rate.

However:

### Network A

Packets arrive approximately uniformly.

### Network B

Packets arrive in bursts.

Although:

[  
E[\lambda_A]=E[\lambda_B]  
]

their queue behavior may differ significantly.

Network B can produce:

- larger queue lengths;
    
- larger delay;
    
- greater packet loss.
    

Thus:

[  
\boxed{  
\text{Same mean traffic}  
\not\Rightarrow  
\text{same network performance}  
}  
]

---

# 40. Important Stochastic Processes for Network Modeling

The most important processes to recognize are:

## 40.1 Poisson process

Used for:

- packet arrivals;
    
- calls;
    
- requests;
    
- sessions.
    

Key parameter:

[  
\lambda  
]

---

## 40.2 Renewal process

Used when inter-arrival times are i.i.d. but not necessarily exponential.

---

## 40.3 Markov chain

Used for systems with discrete states and the Markov property.

---

## 40.4 Continuous-Time Markov Chain

Used when state transitions occur at arbitrary times.

---

## 40.5 Birth-death process

Used for systems in which transitions occur between neighboring states.

This is particularly important for:

- queues;
    
- population models;
    
- resource occupancy;
    
- number of active users.
    

---

# 41. Worked Example — Queue as a Stochastic Process

Consider a router with a buffer.

Define:

[  
X(t)=\text{number of packets in the buffer at time }t  
]

The state space is:

[  
S={0,1,2,\ldots,B}  
]

where (B) is the buffer capacity.

Suppose packets arrive according to a Poisson process with rate:

[  
\lambda  
]

and packets are served according to an exponential service process with rate:

[  
\mu  
]

Then the queue can be modeled as a CTMC.

The state transitions are:

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

for appropriate states.

At:

[  
n=0  
]

there can be no departure.

At:

[  
n=B  
]

there can be no transition to (B+1); an arriving packet may therefore be dropped.

This simple model is the foundation of finite-buffer queueing models.

---

# 42. Performance Measures Derived from Stochastic Processes

Once a stochastic process is established, many network performance metrics can be derived.

For example, if:

[  
X(t)=\text{number of packets in the system}  
]

then:

[  
E[X]  
]

represents the average number of packets in the system.

Similarly, if (D) is packet delay:

[  
E[D]  
]

represents average delay.

If:

[  
N_{\text{lost}}  
]

is a random variable representing the number of lost packets, then:

# [  
P_{\text{loss}}

\frac{E[N_{\text{lost}}]}  
{E[N_{\text{sent}}]}  
]

may be used as an average loss ratio under suitable assumptions.

Thus, stochastic modeling provides the bridge between:

[  
\boxed{  
\text{Random network behavior}  
\rightarrow  
\text{Performance metrics}  
}  
]

---

# 43. Analytical vs. Simulation View

A stochastic model can be analyzed mathematically or simulated computationally.

## Analytical approach

We derive quantities such as:

[  
E[X],\quad  
P(X=n),\quad  
E[D],\quad  
P_{\text{loss}}  
]

using mathematical methods.

## Simulation approach

We generate random events according to the stochastic model and estimate the same quantities empirically.

For example:

# [  
\hat D

\frac{1}{N}  
\sum_{i=1}^{N}D_i  
]

The analytical and simulation results can then be compared.

---

# 44. Common Modeling Errors

## Error 1 — Confusing a random variable with a stochastic process

A random variable describes a random quantity.

A stochastic process describes how a random quantity evolves over an index such as time.

---

## Error 2 — Assuming Poisson traffic automatically

The Poisson process is mathematically convenient, but it is an assumption.

It should be justified according to the traffic being modeled.

---

## Error 3 — Ignoring dependence

Real traffic may be correlated.

Assuming independence can underestimate burstiness and therefore underestimate queueing effects.

---

## Error 4 — Focusing only on the mean

Two processes may have the same mean but different:

- variance;
    
- autocorrelation;
    
- burstiness;
    
- tail behavior.
    

Their network performance can consequently be very different.

---

## Error 5 — Confusing stationarity with independence

A process can be stationary without being independent.

Likewise, independent variables do not automatically imply that the entire process has every form of stationarity.

These are distinct concepts.

---

# 45. Summary of Essential Formulas

### Expected value

[  
\boxed{  
E[X]=\sum_x xp_X(x)  
}  
]

for discrete (X), or:

[  
\boxed{  
E[X]=\int x f_X(x),dx  
}  
]

for continuous (X).

### Variance

# [  
\boxed{  
\operatorname{Var}(X)

E[X^2]-(E[X])^2  
}  
]

### Poisson distribution

# [  
\boxed{  
P(N=n)

\frac{(\lambda t)^n}{n!}e^{-\lambda t}  
}  
]

### Poisson mean

[  
\boxed{  
E[N(t)]=\lambda t  
}  
]

### Poisson variance

[  
\boxed{  
\operatorname{Var}[N(t)]=\lambda t  
}  
]

### Exponential PDF

[  
\boxed{  
f_A(a)=\lambda e^{-\lambda a}  
}  
]

### Exponential mean

[  
\boxed{  
E[A]=\frac{1}{\lambda}  
}  
]

### Markov property

# [  
\boxed{  
P(X_{t+\Delta}\mid X_t,\text{history})

P(X_{t+\Delta}\mid X_t)  
}  
]

### Autocorrelation

# [  
\boxed{  
R_X(\tau)

E[X(t)X(t+\tau)]  
}  
]

for a stationary process.

---

# 46. Chapter Summary

A stochastic process is a mathematical model describing the evolution of a random phenomenon over time.

The key concepts are:

[  
\boxed{  
\text{Random Variable}  
\rightarrow  
\text{Stochastic Process}  
\rightarrow  
\text{Counting Process}  
\rightarrow  
\text{Poisson Process}  
\rightarrow  
\text{Markov Process}  
\rightarrow  
\text{Queueing Model}  
}  
]

The most important ideas to retain are:

1. A random variable models uncertainty at a particular observation.
    
2. A stochastic process models the evolution of a random phenomenon.
    
3. Processes can have discrete or continuous time and discrete or continuous state spaces.
    
4. Stationarity describes invariance of statistical behavior over time.
    
5. Autocorrelation describes temporal dependence.
    
6. A Poisson process is a fundamental model for random arrivals.
    
7. Poisson arrivals have exponential inter-arrival times.
    
8. Markov processes have the memoryless-state property.
    
9. Birth-death processes are especially useful for queueing systems.
    
10. Network traffic cannot always be characterized adequately by its average arrival rate alone.
    

The fundamental conceptual relationship for network performance analysis is:

[  
\boxed{  
\text{Traffic Process}  
\rightarrow  
\text{Queue Evolution}  
\rightarrow  
\text{Delay / Loss / Throughput}  
}  
]

This relationship will become central when we study **queueing theory**.

---

# 47. Exercises

## Exercise 1 — Random Variables

A router observes the number of packets arriving during five consecutive one-second intervals:

[  
4,;7,;5,;9,;5  
]

Calculate:

1. The sample mean.
    
2. The sample variance.
    
3. The sample standard deviation.
    

---

## Exercise 2 — Poisson Arrivals

Packets arrive according to a Poisson process with:

[  
\lambda=200\text{ packets/s}  
]

Calculate the probability that exactly 3 packets arrive during:

[  
t=10\text{ ms}  
]

Use:

# [  
P(N(t)=n)

\frac{(\lambda t)^n}{n!}e^{-\lambda t}  
]

---

## Exercise 3 — Zero Arrivals

A server receives requests according to a Poisson process with:

[  
\lambda=50\text{ requests/s}  
]

What is the probability of receiving **zero requests** during 20 ms?

---

## Exercise 4 — Inter-Arrival Time

Packets follow a Poisson process with:

[  
\lambda=1000\text{ packets/s}  
]

The inter-arrival time (A) is exponentially distributed.

Calculate:

1. (E[A]).
    
2. (\operatorname{Var}(A)).
    
3. (P(A>2\text{ ms})).
    

---

## Exercise 5 — Markov Chain

A network device can be in three states:

[  
S={\text{Idle},\text{Busy},\text{Overloaded}}  
]

Explain why a Markov chain could be used to model the evolution of this device.

What information would be required to construct its transition matrix?

---

## Exercise 6 — Traffic Modeling

Two packet streams both have an average rate of:

[  
\lambda=500\text{ packets/s}  
]

Stream A follows a Poisson process.

Stream B is highly bursty.

Explain why a router could experience different:

- average queue lengths;
    
- delays;
    
- packet-loss rates;
    

for the two streams even though their average arrival rates are identical.

---

# 48. Mentor Checkpoint

Before moving to queueing theory, you should be able to explain the following without relying solely on formulas:

### Question 1

What is the difference between:

[  
X  
]

and:

[  
X(t)?  
]

### Question 2

Why is:

[  
N(t)\sim\operatorname{Poisson}(\lambda t)  
]

useful for modeling packet arrivals?

### Question 3

Why do exponential inter-arrival times appear naturally in Poisson-based queueing models?

### Question 4

What does the Markov property mean physically?

### Question 5

Why can two traffic sources with the same average rate produce different queueing performance?

If you can answer these five questions clearly, you have the conceptual foundation needed for the next major topic:

[  
\boxed{  
\textbf{Queueing Theory and Queueing Models}  
}  
]