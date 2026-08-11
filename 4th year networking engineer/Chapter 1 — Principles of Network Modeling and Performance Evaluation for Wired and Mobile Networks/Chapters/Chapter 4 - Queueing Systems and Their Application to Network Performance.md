# Chapter 4 — Queueing Systems and Their Application to Network Performance

## 1. Introduction

Queueing theory is one of the fundamental mathematical tools used to model and evaluate the performance of communication networks.

Whenever demand for a network resource temporarily exceeds its processing capacity, a queue may form.

Examples include:

- packets waiting in a router buffer;
    
- requests waiting for a server;
    
- users waiting for access to a wireless channel;
    
- calls waiting for communication resources;
    
- jobs waiting for CPU processing;
    
- packets waiting for transmission;
    
- flows waiting for bandwidth.
    

A queueing system can be represented conceptually as:

[  
\boxed{  
\text{Arrivals}  
\rightarrow  
\text{Queue}  
\rightarrow  
\text{Service}  
\rightarrow  
\text{Departures}  
}  
]

The objective is not merely to determine whether a queue exists. We want to quantify its performance.

Typical performance measures include:

- average number of customers;
    
- average queue length;
    
- average waiting time;
    
- average response time;
    
- server utilization;
    
- throughput;
    
- probability of blocking;
    
- probability of packet loss;
    
- probability of congestion;
    
- required buffer size;
    
- required number of servers.
    

In network engineering, a **customer** may represent a packet, flow, request, call, or user, while a **server** may represent a transmission channel, processor, communication link, or service resource.

---

# 2. Basic Structure of a Queueing System

A queueing system consists of several fundamental components.

[  
\boxed{  
\text{Arrival process}  
+  
\text{Waiting discipline}  
+  
\text{Service mechanism}  
+  
\text{System capacity}  
}  
]

We need to characterize each component.

## 2.1 Arrival Process

The arrival process describes how customers enter the system.

The most common model is the **Poisson arrival process**.

If arrivals occur according to a Poisson process with rate:

[  
\lambda  
]

then the number of arrivals during an interval of length (t) follows:

[  
N(t)\sim\operatorname{Poisson}(\lambda t)  
]

Therefore:

# [  
P(N(t)=k)

\frac{(\lambda t)^k e^{-\lambda t}}{k!}  
]

where:

- (\lambda) = average arrival rate;
    
- (k) = number of arrivals;
    
- (t) = observation interval.
    

The interarrival time is exponentially distributed:

[  
T_A\sim\operatorname{Exp}(\lambda)  
]

with:

[  
E[T_A]=\frac{1}{\lambda}  
]

---

# 3. Service Process

The service process describes how long a customer requires the resource.

For an exponential service time:

[  
T_S\sim\operatorname{Exp}(\mu)  
]

where:

[  
\mu  
]

is the service rate.

The average service time is:

[  
\boxed{  
E[T_S]=\frac{1}{\mu}  
}  
]

For example, if:

[  
\mu=100\text{ packets/s}  
]

then the average service time is:

[  
E[T_S]=\frac{1}{100}=0.01\text{ s}  
]

or:

[  
10\text{ ms}  
]

---

# 4. Queueing Discipline

The queueing discipline determines which customer receives service next.

The most common disciplines are:

### FIFO / FCFS

**First In, First Out** or **First Come, First Served**.

The first customer to arrive is served first.

This is common in packet queues.

### LIFO

**Last In, First Out**.

The most recent arrival is served first.

### Priority Queue

Customers are divided into priority classes.

For example:

[  
\text{Emergency traffic}

\text{Voice}

\text{Video}

\text{Best effort}  
]

This is highly relevant to QoS systems.

### Processor Sharing

The available service capacity is shared among active customers.

This is useful for modeling some computing and data-network systems.

---

# 5. Queue Capacity

The capacity specifies the maximum number of customers that can be present.

There are two major cases.

### Infinite capacity

Theoretically:

[  
K=\infty  
]

The queue can continue growing.

### Finite capacity

[  
K<\infty  
]

When the system is full, new arrivals are blocked or discarded.

For a router:

[  
\boxed{  
\text{Full buffer}  
\rightarrow  
\text{incoming packet dropped}  
}  
]

This is particularly important in packet-switched networks.

---

# 6. Kendall's Notation

Queueing systems are commonly described using **Kendall's notation**:

[  
\boxed{  
A/S/c/K/N/D  
}  
]

where:

- (A) = interarrival-time distribution;
    
- (S) = service-time distribution;
    
- (c) = number of servers;
    
- (K) = system capacity;
    
- (N) = population size;
    
- (D) = queueing discipline.
    

The simplified notation:

[  
A/S/c  
]

is often used when the remaining parameters are assumed to be infinite or standard.

Common symbols include:

|Symbol|Meaning|
|---|---|
|(M)|Markovian / exponential|
|(D)|Deterministic|
|(G)|General distribution|
|(c)|Number of servers|

Examples:

[  
M/M/1  
]

means:

- Poisson arrivals;
    
- exponential service;
    
- one server.
    

[  
M/M/c  
]

means:

- Poisson arrivals;
    
- exponential service;
    
- (c) parallel servers.
    

[  
M/M/1/K  
]

means:

- Poisson arrivals;
    
- exponential service;
    
- one server;
    
- finite system capacity (K).
    

---

# 7. Fundamental Performance Variables

Let:

[  
N(t)  
]

be the number of customers in the system at time (t).

We distinguish between:

### Number in the system

[  
L  
]

This includes both:

- customers waiting;
    
- customers currently being served.
    

### Number in the queue

[  
L_q  
]

This excludes customers currently receiving service.

Therefore:

[  
\boxed{  
L=L_q+\text{number currently in service}  
}  
]

For a single-server system:

[  
L=L_q+\text{server utilization}  
]

in the long-term average sense.

---

# 8. Waiting Time

Let:

[  
W  
]

be the total time a customer spends in the system.

It includes:

[  
\boxed{  
W=W_q+S  
}  
]

where:

- (W_q) = waiting time before service;
    
- (S) = service time.
    

Thus:

[  
E[W]=E[W_q]+E[S]  
]

The distinction is important in network performance analysis.

For a packet:

# [  
\boxed{  
\text{End-to-end queueing delay}  
+  
\text{transmission/service time}

\text{total system time}  
}  
]

---

# 9. Little's Law

One of the most important results in queueing theory is **Little's Law**.

Under standard steady-state conditions:

[  
\boxed{  
L=\lambda_{\text{eff}}W  
}  
]

Similarly:

[  
\boxed{  
L_q=\lambda_{\text{eff}}W_q  
}  
]

where:

- (L) = average number of customers in the system;
    
- (L_q) = average number waiting;
    
- (W) = average time in system;
    
- (W_q) = average waiting time;
    
- (\lambda_{\text{eff}}) = effective throughput.
    

For a stable infinite-capacity system:

[  
\lambda_{\text{eff}}=\lambda  
]

Therefore:

[  
\boxed{  
L=\lambda W  
}  
]

and:

[  
\boxed{  
L_q=\lambda W_q  
}  
]

---

# 10. Why Little's Law Matters in Networks

Suppose a router processes:

[  
\lambda=1000\text{ packets/s}  
]

and the average number of packets in the system is:

[  
L=20  
]

Then:

[  
W=\frac{L}{\lambda}  
]

giving:

# [  
W=\frac{20}{1000}

0.02\text{ s}  
]

Therefore:

[  
\boxed{  
W=20\text{ ms}  
}  
]

This means that the average packet spends 20 ms in the system.

Little's Law is powerful because it allows us to derive one performance metric from two others without explicitly solving the entire stochastic model.

---

# 11. Stability

A queue must have sufficient service capacity to handle its long-term workload.

For an (M/M/1) queue:

[  
\boxed{  
\rho=\frac{\lambda}{\mu}  
}  
]

where (\rho) is the **traffic intensity** or **server utilization**.

For an infinite-capacity queue to have a stable steady state:

[  
\boxed{  
\rho<1  
}  
]

or equivalently:

[  
\boxed{  
\lambda<\mu  
}  
]

If:

[  
\lambda\ge\mu  
]

the average queue size grows without bound in the standard infinite-buffer model.

This is one of the most important principles in network dimensioning:

[  
\boxed{  
\text{Offered load} < \text{Service capacity}  
}  
]

---

# 12. 4.1 Single-Server Queues

A single-server queue contains:

- one arrival process;
    
- one waiting line;
    
- one server.
    

The classical example is:

[  
\boxed{  
M/M/1  
}  
]

The system can be represented by the number of customers:

[  
0,1,2,3,\ldots  
]

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

This is a **birth-death process**, which connects directly to the Markov-chain theory developed in Chapter 3.

---

# 13. (M/M/1) Queue

Assume:

- Poisson arrivals at rate (\lambda);
    
- exponential service at rate (\mu);
    
- one server;
    
- infinite capacity;
    
- FIFO discipline.
    

The traffic intensity is:

[  
\rho=\frac{\lambda}{\mu}  
]

with:

[  
\rho<1  
]

for stability.

---

# 14. State Probabilities of (M/M/1)

Let:

[  
\pi_n=P(N=n)  
]

be the stationary probability of having (n) customers.

For the (M/M/1) queue:

[  
\boxed{  
\pi_n=(1-\rho)\rho^n  
}  
]

for:

[  
n=0,1,2,\ldots  
]

In particular:

[  
\boxed{  
\pi_0=1-\rho  
}  
]

This means the probability that the server is idle is:

[  
1-\rho  
]

and the server utilization is:

[  
\boxed{  
\rho  
}  
]

---

# 15. Mean Number in the System

For an (M/M/1) queue:

[  
\boxed{  
L=\frac{\rho}{1-\rho}  
}  
]

Since:

[  
\rho=\frac{\lambda}{\mu}  
]

we can also write:

[  
\boxed{  
L=\frac{\lambda}{\mu-\lambda}  
}  
]

---

# 16. Mean Number in the Queue

The average number waiting, excluding the customer in service, is:

[  
\boxed{  
L_q=\frac{\rho^2}{1-\rho}  
}  
]

or:

[  
\boxed{  
L_q=  
\frac{\lambda^2}  
{\mu(\mu-\lambda)}  
}  
]

The relationship:

[  
L=L_q+\rho  
]

can be verified directly:

# [  
\frac{\rho^2}{1-\rho}+\rho

\frac{\rho}{1-\rho}  
]

---

# 17. Mean Response Time

Using Little's Law:

[  
L=\lambda W  
]

therefore:

[  
W=\frac{L}{\lambda}  
]

For (M/M/1):

[  
\boxed{  
W=\frac{1}{\mu-\lambda}  
}  
]

This includes service time.

---

# 18. Mean Waiting Time

Similarly:

[  
L_q=\lambda W_q  
]

so:

[  
\boxed{  
W_q=  
\frac{\rho}{\mu-\lambda}  
}  
]

Equivalently:

[  
\boxed{  
W_q=  
\frac{\lambda}  
{\mu(\mu-\lambda)}  
}  
]

The relationship:

[  
W=W_q+\frac{1}{\mu}  
]

must hold.

---

# 19. (M/M/1) Summary

For:

[  
\rho=\frac{\lambda}{\mu}<1  
]

we have:

[  
\boxed{  
P_n=(1-\rho)\rho^n  
}  
]

[  
\boxed{  
L=\frac{\rho}{1-\rho}  
}  
]

[  
\boxed{  
L_q=\frac{\rho^2}{1-\rho}  
}  
]

[  
\boxed{  
W=\frac{1}{\mu-\lambda}  
}  
]

[  
\boxed{  
W_q=\frac{\rho}{\mu-\lambda}  
}  
]

These formulas should be part of your core queueing-theory toolkit.

---

# 20. Example — Router Queue

A router receives:

[  
\lambda=800\text{ packets/s}  
]

and its transmission mechanism can process:

[  
\mu=1000\text{ packets/s}  
]

Therefore:

[  
\rho=\frac{800}{1000}=0.8  
]

The system is stable because:

[  
0.8<1  
]

The average number of packets is:

# [  
L=  
\frac{0.8}{1-0.8}

4  
]

Therefore:

[  
\boxed{  
L=4\text{ packets}  
}  
]

The average time in the system is:

[  
W=  
\frac{1}{1000-800}  
]

[  
W=0.005\text{ s}  
]

Thus:

[  
\boxed{  
W=5\text{ ms}  
}  
]

Using Little's Law:

[  
L=\lambda W  
]

we verify:

[  
800(0.005)=4  
]

---

# 21. The Nonlinear Effect of Utilization

One of the most important lessons from queueing theory is that delay does not increase linearly with utilization.

For an (M/M/1) queue:

[  
W=\frac{1}{\mu-\lambda}  
]

or:

[  
W=\frac{1}{\mu(1-\rho)}  
]

As:

[  
\rho\rightarrow1  
]

we obtain:

[  
W\rightarrow\infty  
]

This means that operating a network resource close to 100% utilization can cause severe delays.

For example, if:

[  
\rho=0.5  
]

then:

[  
\frac{1}{1-\rho}=2  
]

but if:

[  
\rho=0.9  
]

then:

[  
\frac{1}{1-\rho}=10  
]

and if:

[  
\rho=0.99  
]

then:

[  
\frac{1}{1-\rho}=100  
]

Thus:

[  
\boxed{  
\text{High utilization can produce disproportionately high delay.}  
}  
]

This principle is fundamental in network engineering.

---

# 22. (M/G/1) Queue

The (M/M/1) model assumes exponential service times.

Real network systems do not always satisfy this assumption.

For an (M/G/1) queue:

- arrivals are Poisson;
    
- service times have a general distribution;
    
- one server.
    

Let:

[  
E[S]  
]

be the mean service time and:

[  
E[S^2]  
]

the second moment.

The utilization is:

[  
\rho=\lambda E[S]  
]

The **Pollaczek–Khinchine formula** gives:

[  
\boxed{  
W_q=  
\frac{\lambda E[S^2]}  
{2(1-\rho)}  
}  
]

This is important because it shows that queueing delay depends not only on the mean service time, but also on its variability.

---

# 23. Why Variability Matters

Consider two systems with identical:

[  
E[S]  
]

but different service-time variability.

The system with greater:

[  
E[S^2]  
]

will have greater:

[  
W_q  
]

Thus:

[  
\boxed{  
\text{More service-time variability}  
\Rightarrow  
\text{more queueing delay}  
}  
]

This is highly relevant to networks carrying heterogeneous traffic.

For example, a network may process:

- small packets;
    
- large packets;
    
- short flows;
    
- long flows.
    

Such heterogeneity creates service-time variability.

---

# 24. 4.2 Multi-Server Queues

A multi-server queue contains:

[  
c>1  
]

parallel servers.

A classical model is:

[  
\boxed{  
M/M/c  
}  
]

Examples include:

- multiple transmission channels;
    
- parallel processors;
    
- multiple wireless channels;
    
- server farms;
    
- parallel network links;
    
- multiple service stations.
    

---

# 25. (M/M/c) Model

Assume:

- Poisson arrivals at rate (\lambda);
    
- (c) identical servers;
    
- each server has service rate (\mu);
    
- infinite queue capacity;
    
- exponential service times.
    

The total service capacity depends on the number of busy servers.

If (n<c):

[  
\text{service rate}=n\mu  
]

If:

[  
n\ge c  
]

all servers are busy and:

[  
\text{service rate}=c\mu  
]

Thus the birth-death transitions are:

[  
n  
\xrightarrow{\lambda}  
n+1  
]

and:

[  
n  
\xrightarrow{\min(n,c)\mu}  
n-1  
]

---

# 26. Stability of (M/M/c)

The total service capacity is:

[  
c\mu  
]

Therefore stability requires:

[  
\boxed{  
\lambda<c\mu  
}  
]

Define:

[  
a=\frac{\lambda}{\mu}  
]

and:

[  
\rho=\frac{\lambda}{c\mu}  
]

Then stability requires:

[  
\boxed{  
\rho<1  
}  
]

Note carefully that:

[  
\frac{\lambda}{\mu}  
]

is the offered load in Erlangs, while:

[  
\frac{\lambda}{c\mu}  
]

is the average utilization per server.

---

# 27. Erlang-C Formula

For an (M/M/c) queue, the probability that an arriving customer has to wait is given by the **Erlang-C formula**.

Define:

[  
a=\frac{\lambda}{\mu}  
]

and:

# [  
\rho=\frac{a}{c}

\frac{\lambda}{c\mu}  
]

The probability that all servers are busy is:

# [  
\boxed{  
P_{\text{wait}}

\frac{  
\frac{a^c}{c!}\frac{1}{1-\rho}  
}{  
\sum_{n=0}^{c-1}\frac{a^n}{n!}  
+  
\frac{a^c}{c!}\frac{1}{1-\rho}  
}  
}  
]

This is the Erlang-C waiting probability.

---

# 28. Mean Waiting Time for (M/M/c)

Once:

[  
P_{\text{wait}}  
]

is known, the average waiting time in the queue is:

[  
\boxed{  
W_q=  
\frac{P_{\text{wait}}}  
{c\mu-\lambda}  
}  
]

The mean time in the system is:

[  
\boxed{  
W=W_q+\frac{1}{\mu}  
}  
]

and Little's Law gives:

[  
L_q=\lambda W_q  
]

and:

[  
L=\lambda W  
]

---

# 29. Example — Multi-Server Network Service

Suppose a network service has:

[  
\lambda=8\text{ requests/s}  
]

and:

[  
\mu=3\text{ requests/s/server}  
]

with:

[  
c=3  
]

The total service capacity is:

[  
c\mu=3(3)=9  
]

Since:

[  
8<9  
]

the system is stable.

The server utilization is:

[  
\rho=  
\frac{8}{9}  
\approx0.889  
]

The system is therefore heavily loaded.

This illustrates an important engineering point:

[  
\boxed{  
\text{Adding servers increases capacity and can dramatically reduce waiting.}  
}  
]

However, if:

[  
\lambda\approx c\mu  
]

the queue can still become very sensitive to load.

---

# 30. (M/M/c/c): No Waiting Room

A particularly important multi-server model is:

[  
\boxed{  
M/M/c/c  
}  
]

There are:

- (c) servers;
    
- no waiting room;
    
- arrivals finding all servers occupied are blocked.
    

This model is known as the **Erlang loss system**.

Applications include:

- telephone systems;
    
- wireless channels;
    
- circuit-switched networks;
    
- finite communication resources.
    

---

# 31. Erlang-B Formula

Let:

[  
A=\frac{\lambda}{\mu}  
]

be the offered traffic in Erlangs.

The probability that an arriving customer is blocked is:

# [  
\boxed{  
B(c,A)

\frac{\frac{A^c}{c!}}  
{\sum_{k=0}^{c}\frac{A^k}{k!}}  
}  
]

This is the **Erlang-B formula**.

The blocking probability is therefore:

[  
\boxed{  
P_{\text{block}}=B(c,A)  
}  
]

---

# 32. Erlang and Network Traffic

An **Erlang** is a dimensionless unit of offered traffic.

If:

- 10 calls are simultaneously active on average,
    

then the offered traffic is:

[  
A=10\text{ Erlangs}  
]

For a communication system, traffic intensity can be approximated as:

[  
\boxed{  
A=\lambda E[S]  
}  
]

where:

- (\lambda) = call arrival rate;
    
- (E[S]) = average call duration.
    

For example:

[  
\lambda=2\text{ calls/min}  
]

and:

[  
E[S]=5\text{ min}  
]

gives:

[  
A=2(5)=10  
]

Therefore:

[  
\boxed{  
A=10\text{ Erlangs}  
}  
]

---

# 33. Erlang-B vs. Erlang-C

These two formulas must not be confused.

### Erlang-B

[  
M/M/c/c  
]

No waiting room.

If all servers are busy:

[  
\boxed{  
\text{Customer is blocked/lost}  
}  
]

### Erlang-C

[  
M/M/c  
]

Waiting room exists.

If all servers are busy:

[  
\boxed{  
\text{Customer waits}  
}  
]

Therefore:

[  
\boxed{  
\text{Erlang-B}\rightarrow\text{blocking}  
}  
]

[  
\boxed{  
\text{Erlang-C}\rightarrow\text{waiting}  
}  
]

---

# 34. 4.3 Queueing Systems with Limited Capacity

Real network buffers are finite.

A router cannot store an infinite number of packets.

Suppose the maximum system capacity is:

[  
K  
]

Then:

[  
N(t)\in{0,1,\ldots,K}  
]

An arrival finding:

[  
N(t)=K  
]

is blocked or dropped.

A classical model is:

[  
\boxed{  
M/M/1/K  
}  
]

---

# 35. (M/M/1/K) Queue

The system contains:

- Poisson arrivals at rate (\lambda);
    
- exponential service at rate (\mu);
    
- one server;
    
- maximum capacity (K).
    

The transition structure is:

[  
0  
\rightleftarrows  
1  
\rightleftarrows  
2  
\rightleftarrows  
\cdots  
\rightleftarrows  
K  
]

with:

[  
n\rightarrow n+1  
]

at rate:

[  
\lambda  
]

for:

[  
n<K  
]

and:

[  
n\rightarrow n-1  
]

at rate:

[  
\mu  
]

for:

[  
n>0  
]

At state (K), arrivals cannot enter.

---

# 36. Stationary Distribution of (M/M/1/K)

Let:

[  
\rho=\frac{\lambda}{\mu}  
]

For:

[  
\rho\neq1  
]

the stationary probabilities are:

[  
\boxed{  
\pi_n=  
\frac{(1-\rho)\rho^n}  
{1-\rho^{K+1}}  
}  
]

for:

[  
n=0,\ldots,K  
]

The full-system probability is:

[  
\boxed{  
\pi_K=  
\frac{(1-\rho)\rho^K}  
{1-\rho^{K+1}}  
}  
]

This is the probability of buffer occupancy at maximum capacity.

---

# 37. Special Case (\rho=1)

When:

[  
\lambda=\mu  
]

the previous expression becomes an indeterminate form.

The stationary distribution becomes uniform:

[  
\boxed{  
\pi_n=\frac{1}{K+1}  
}  
]

for:

[  
n=0,\ldots,K  
]

Therefore:

[  
\boxed{  
P_{\text{full}}=\frac{1}{K+1}  
}  
]

---

# 38. Packet Loss Probability

If every arrival finding the system full is dropped, then:

[  
\boxed{  
P_{\text{loss}}=\pi_K  
}  
]

This is a fundamental metric in packet-switched networks.

The effective throughput is:

# [  
\boxed{  
\lambda_{\text{eff}}

\lambda(1-P_{\text{loss}})  
}  
]

The distinction between offered arrival rate and accepted throughput is crucial.

---

# 39. Example — Finite Router Buffer

Suppose:

[  
\lambda=90\text{ packets/s}  
]

[  
\mu=100\text{ packets/s}  
]

and:

[  
K=5  
]

Then:

[  
\rho=0.9  
]

The probability of the buffer being full is:

# [  
\pi_5

\frac{(1-0.9)(0.9)^5}  
{1-(0.9)^6}  
]

This gives the probability that an arriving packet finds the system at capacity.

The effective throughput is then:

# [  
\lambda_{\text{eff}}

90(1-\pi_5)  
]

The finite-capacity model therefore allows us to quantify packet loss rather than incorrectly assuming that the queue can grow indefinitely.

---

# 40. Finite Capacity and Network Dimensioning

Suppose a router has a target packet-loss probability:

[  
P_{\text{loss}}\le10^{-3}  
]

The buffer capacity (K) can be selected such that:

[  
\boxed{  
\pi_K\le10^{-3}  
}  
]

This is a practical example of **performance-based dimensioning**.

The engineer can ask:

> How large should the buffer be to meet the required loss probability?

This is a much more useful question than simply asking whether the queue exists.

---

# 41. Finite Population Queues

Another type of limited system occurs when the number of potential customers is itself finite.

For example:

- a network with a fixed number of terminals;
    
- a finite set of machines generating requests;
    
- a fixed number of users sharing a wireless system.
    

The arrival rate can then depend on the number of active users.

If there are (N) possible sources and (n) are currently active, the effective arrival rate might be:

[  
\lambda_n=(N-n)\lambda  
]

because only inactive sources can generate new requests.

This creates a **finite-source queueing model**.

Such systems are common in computer and telecommunications networks.

---

# 42. Blocking vs. Packet Loss

The words "blocking" and "loss" often describe related phenomena but should be interpreted according to the system.

### Blocking

An arriving customer cannot obtain service immediately or cannot enter the system.

### Packet loss

A packet is discarded because the system cannot accommodate it.

For example:

[  
\text{Full buffer}  
\rightarrow  
\text{packet discarded}  
]

This is a form of blocking at the buffer level.

The exact terminology depends on the modeled system.

---

# 43. 4.4 Petri Nets

Queueing models are excellent for systems where the principal concern is:

[  
\text{number of customers}  
]

However, network systems often involve:

- concurrency;
    
- synchronization;
    
- resource sharing;
    
- conflicts;
    
- parallel activities;
    
- logical conditions.
    

**Petri nets** provide a graphical and mathematical framework for modeling such systems.

A Petri net is composed of:

[  
\boxed{  
\text{Places}  
+  
\text{Transitions}  
+  
\text{Arcs}  
+  
\text{Tokens}  
}  
]

---

# 44. Places

A **place** represents a condition, resource, or storage location.

Graphically, places are conventionally represented by circles.

Examples:

- packets waiting;
    
- server available;
    
- channel occupied;
    
- user connected;
    
- buffer space available.
    

A place can contain zero or more tokens.

---

# 45. Tokens

A token represents the presence of some quantity or condition.

For example, a place:

[  
P_{\text{queue}}  
]

containing 5 tokens may represent:

[  
5\text{ packets waiting}  
]

A token does not necessarily represent a literal packet. Its interpretation depends on the model.

---

# 46. Transitions

A **transition** represents an event or activity.

Examples:

- packet arrival;
    
- packet transmission;
    
- service completion;
    
- connection establishment;
    
- failure;
    
- recovery.
    

Transitions are conventionally represented by rectangles or bars.

---

# 47. Arcs

Arcs connect:

- places to transitions;
    
- transitions to places.
    

They define the relationship between conditions and events.

An arc from:

[  
P_1\rightarrow T  
]

means that tokens in (P_1) are required for transition (T) to fire.

An arc:

[  
T\rightarrow P_2  
]

means that firing (T) produces tokens in (P_2).

---

# 48. Marking

The state of a Petri net is called its **marking**.

If there are (m) places, the marking can be represented as:

[  
M=  
[m_1,m_2,\ldots,m_m]  
]

where:

[  
m_i  
]

is the number of tokens in place (i).

For example:

[  
M=[2,0,1]  
]

means:

- place 1 contains 2 tokens;
    
- place 2 contains 0 tokens;
    
- place 3 contains 1 token.
    

Thus:

[  
\boxed{  
\text{Marking}=\text{state of the Petri net}  
}  
]

---

# 49. Enabling and Firing

A transition is **enabled** when all of its input conditions are satisfied.

Suppose:

[  
P_1\rightarrow T  
]

with an arc weight of 1.

If:

[  
M(P_1)\ge1  
]

then (T) is enabled.

When (T) fires:

- the required tokens are removed from input places;
    
- output tokens are produced in output places.
    

Therefore firing changes the marking.

---

# 50. Example — Simple Queue as a Petri Net

Consider a simple queue:

[  
\text{Arrival}  
\rightarrow  
\text{Queue}  
\rightarrow  
\text{Service}  
\rightarrow  
\text{Departure}  
]

We can use:

### Places

[  
P_Q=\text{packets waiting}  
]

[  
P_S=\text{server available}  
]

### Transitions

[  
T_A=\text{packet arrival}  
]

[  
T_S=\text{packet service}  
]

The marking represents:

- how many packets are waiting;
    
- whether the server is available.
    

This illustrates why Petri nets can represent both **data** and **resource availability**.

---

# 51. Petri Nets and Concurrency

One major advantage of Petri nets is that they naturally represent concurrent activities.

Suppose a network has two independent links.

A Petri net can represent:

[  
T_1=\text{transmit packet on Link 1}  
]

and:

[  
T_2=\text{transmit packet on Link 2}  
]

If both are enabled, they may fire independently.

This is more expressive than a simple linear queue representation.

---

# 52. Petri Nets and Synchronization

Suppose an operation requires two resources simultaneously:

[  
\text{Resource A}  
+  
\text{Resource B}  
\rightarrow  
\text{Operation}  
]

A transition can have two input places.

The transition is enabled only if both places contain the required tokens.

This naturally models synchronization.

Network examples include:

- packet requiring two resources;
    
- communication requiring channel and buffer availability;
    
- distributed operations requiring multiple acknowledgments.
    

---

# 53. Petri Nets and Conflicts

Suppose two transitions require the same resource.

Then the same token may enable competing transitions.

This represents a **conflict**.

For example:

[  
P_{\text{channel}}  
\rightarrow  
T_{\text{voice}}  
]

and:

[  
P_{\text{channel}}  
\rightarrow  
T_{\text{data}}  
]

If there is only one channel token, both transitions cannot necessarily use it simultaneously.

This provides a natural model of resource contention.

---

# 54. Petri Net Structural Representation

A Petri net can be represented mathematically as:

[  
\boxed{  
PN=(P,T,A,W,M_0)  
}  
]

where:

- (P) = set of places;
    
- (T) = set of transitions;
    
- (A) = set of arcs;
    
- (W) = arc-weight function;
    
- (M_0) = initial marking.
    

The places and transitions form a bipartite graph:

[  
P\cap T=\varnothing  
]

and arcs connect only:

[  
P\rightarrow T  
]

or:

[  
T\rightarrow P  
]

---

# 55. Incidence Matrix

A Petri net can also be represented algebraically using an incidence matrix.

Let:

[  
C=C^+-C^-  
]

where:

- (C^-) represents tokens consumed by transitions;
    
- (C^+) represents tokens produced.
    

If transition (T_j) fires, the marking changes according to:

[  
\boxed{  
M'=M+C_{\cdot j}  
}  
]

For a firing sequence represented by vector (\sigma):

[  
\boxed{  
M=M_0+C\sigma  
}  
]

This algebraic representation is useful for analyzing reachability and invariants.

---

# 56. Reachability

A marking (M) is **reachable** from (M_0) if there exists a valid sequence of transition firings transforming:

[  
M_0  
\rightarrow  
M  
]

The set of all reachable markings is called the **reachability set**.

This allows us to ask questions such as:

- Can the network reach a deadlock?
    
- Can the buffer become full?
    
- Can two resources become occupied simultaneously?
    
- Can a packet become permanently blocked?
    

---

# 57. Deadlock

A **deadlock** occurs when the system reaches a marking where no required transition can fire.

In network systems, deadlocks can represent:

- resource contention;
    
- circular waiting;
    
- protocol failures;
    
- blocked distributed processes.
    

Petri nets are particularly useful for detecting and analyzing such situations.

---

# 58. Boundedness

A place is bounded if the number of tokens it can contain is finite.

A Petri net is bounded if every place has a finite upper bound on its number of tokens.

This concept is useful for modeling finite network resources.

For example:

[  
P_{\text{buffer}}  
]

may be bounded by:

[  
K=100  
]

meaning that at most 100 packets can be represented.

---

# 59. 4.5 Stochastic Petri Nets

Ordinary Petri nets describe possible behavior but do not necessarily specify how long transitions take or how frequently they occur.

To evaluate **performance**, we need timing and stochastic information.

This leads to **Stochastic Petri Nets (SPNs)**.

A stochastic Petri net associates random firing times with transitions.

The most classical SPN uses exponentially distributed firing times.

---

# 60. Exponential Firing Times

Suppose transition (T_i) has firing rate:

[  
\lambda_i  
]

Then its firing time is:

[  
T_i\sim\operatorname{Exp}(\lambda_i)  
]

and:

[  
E[T_i]=\frac{1}{\lambda_i}  
]

Thus, SPNs provide a graphical representation of a stochastic system while retaining the mathematical structure of CTMCs.

---

# 61. SPN and CTMC Connection

For a stochastic Petri net with exponentially distributed transition firing times:

[  
\boxed{  
\text{SPN}  
\longrightarrow  
\text{Reachability Graph}  
\longrightarrow  
\text{CTMC}  
}  
]

Each reachable marking becomes a CTMC state.

Each enabled transition produces a possible state transition.

The transition rate is determined by the firing rate of the corresponding Petri-net transition.

This is one of the most important connections in this chapter.

---

# 62. Example — Stochastic Queue

Consider a queue with:

- packet arrivals at rate (\lambda);
    
- packet departures at rate (\mu).
    

An SPN can have:

### Place

[  
P_Q=\text{number of packets}  
]

### Transition

[  
T_A=\text{arrival}  
]

with rate:

[  
\lambda  
]

### Transition

[  
T_D=\text{departure}  
]

with rate:

[  
\mu  
]

The reachable markings are:

[  
0,1,2,3,\ldots  
]

which correspond directly to the states of an (M/M/1) CTMC.

Therefore:

[  
\boxed{  
\text{SPN representation}  
\equiv  
\text{graphical representation of the Markov model}  
}  
]

under the appropriate assumptions.

---

# 63. Immediate and Timed Transitions

More sophisticated stochastic Petri nets may contain:

- timed transitions;
    
- immediate transitions.
    

A **timed transition** takes a random amount of time before firing.

An **immediate transition** fires without an associated delay once enabled.

Immediate transitions are useful for modeling logical behavior that occurs effectively instantaneously compared with the time scale of interest.

For example:

[  
\text{Packet arrival}  
\rightarrow  
\text{Routing decision}  
\rightarrow  
\text{Queue selection}  
]

The routing decision might be represented by an immediate transition.

---

# 64. Competing Transitions

Suppose two transitions are simultaneously enabled:

[  
T_1  
]

with rate:

[  
\lambda_1  
]

and:

[  
T_2  
]

with rate:

[  
\lambda_2  
]

The time until the next firing is exponentially distributed with total rate:

[  
\boxed{  
\lambda_1+\lambda_2  
}  
]

The probability that (T_1) fires first is:

# [  
\boxed{  
P(T_1\text{ first})

\frac{\lambda_1}  
{\lambda_1+\lambda_2}  
}  
]

Similarly:

# [  
P(T_2\text{ first})

\frac{\lambda_2}  
{\lambda_1+\lambda_2}  
]

This property is fundamental in stochastic Petri nets.

---

# 65. Example — Competing Network Events

Suppose a packet currently has two possible outcomes:

- successful transmission at rate:
    

[  
\lambda_s=8\text{ s}^{-1}  
]

- transmission failure at rate:
    

[  
\lambda_f=2\text{ s}^{-1}  
]

The total rate is:

[  
\lambda=8+2=10\text{ s}^{-1}  
]

The probability that successful transmission occurs first is:

# [  
P_s=  
\frac{8}{10}

0.8  
]

and:

# [  
P_f=  
\frac{2}{10}

0.2  
]

Thus:

[  
\boxed{  
P_s=80%  
}  
]

This provides a direct probabilistic interpretation of competing transitions.

---

# 66. SPNs for Network Performance

Stochastic Petri nets can represent:

- packet arrivals;
    
- packet processing;
    
- wireless transmission;
    
- retransmission;
    
- channel occupation;
    
- resource contention;
    
- failures;
    
- recovery;
    
- parallel processing;
    
- synchronization.
    

Performance metrics can include:

[  
\boxed{  
\begin{array}{c}  
\text{Throughput}\  
\text{Delay}\  
\text{Buffer occupancy}\  
\text{Utilization}\  
\text{Loss probability}\  
\text{Availability}\  
\text{Reliability}  
\end{array}  
}  
]

---

# 67. Example — Wireless Transmission with Retransmission

Consider a wireless packet transmission.

A simplified SPN could contain:

### Places

[  
P_Q=\text{packet waiting}  
]

[  
P_T=\text{packet being transmitted}  
]

[  
P_S=\text{successful transmission}  
]

[  
P_F=\text{transmission failure}  
]

### Transitions

[  
T_A=\text{packet arrival}  
]

[  
T_S=\text{successful transmission}  
]

[  
T_F=\text{transmission failure}  
]

The failure transition can return the packet to:

[  
P_Q  
]

representing retransmission.

This structure can model:

[  
\text{Arrival}  
\rightarrow  
\text{Transmission}  
\rightarrow  
\begin{cases}  
\text{Success}\  
\text{Failure}\rightarrow\text{Retransmission}  
\end{cases}  
]

This is difficult to express naturally using a simple (M/M/1) queue but is straightforward with a Petri net.

---

# 68. Why Use Petri Nets Instead of Only Markov Chains?

Markov chains are mathematically elegant but can become difficult to construct when systems contain many interacting components.

Petri nets offer graphical modeling of:

- concurrency;
    
- synchronization;
    
- resource sharing;
    
- conflicts;
    
- dependencies.
    

The Petri net can then be transformed into a stochastic state-space model when quantitative performance analysis is needed.

Therefore:

[  
\boxed{  
\text{Petri Nets}  
\rightarrow  
\text{system structure}  
}  
]

while:

[  
\boxed{  
\text{Markov Chains}  
\rightarrow  
\text{probabilistic state evolution}  
}  
]

and:

[  
\boxed{  
\text{Stochastic Petri Nets}  
\rightarrow  
\text{both structure and stochastic timing}  
}  
]

---

# 69. Queueing Models vs. Petri Nets

|Feature|Queueing Model|Petri Net|Stochastic Petri Net|
|---|---|---|---|
|Queue length|Excellent|Possible|Excellent|
|Multiple resources|Moderate|Excellent|Excellent|
|Concurrency|Limited|Excellent|Excellent|
|Synchronization|Limited|Excellent|Excellent|
|Random timing|Yes|Not necessarily|Yes|
|Performance analysis|Excellent|Mainly structural|Excellent|
|Graphical representation|Limited|Yes|Yes|
|CTMC representation|Often direct|Possible|Direct under exponential assumptions|

---

# 70. From Queueing Model to Petri Net

Consider an (M/M/1) queue.

The queueing formulation says:

[  
\lambda=\text{arrival rate}  
]

[  
\mu=\text{service rate}  
]

The Petri-net formulation can represent:

- a place containing packets;
    
- a transition representing arrival;
    
- a transition representing service;
    
- a place representing the server.
    

The resulting stochastic Petri net provides a graphical representation of the same stochastic behavior.

This is an important modeling principle:

[  
\boxed{  
\text{Different mathematical formalisms can describe the same physical system.}  
}  
]

---

# 71. Network Performance Modeling Workflow

For a network system, a practical modeling workflow is:

### Step 1 — Identify entities

What are the customers?

Examples:

[  
\text{packets, calls, users, requests}  
]

### Step 2 — Identify resources

What provides service?

Examples:

[  
\text{links, channels, CPUs, servers}  
]

### Step 3 — Define states

Examples:

[  
\text{queue occupancy}  
]

or:

[  
\text{resource availability}  
]

### Step 4 — Define arrivals

Determine:

[  
\lambda  
]

or a more general arrival distribution.

### Step 5 — Define service

Determine:

[  
\mu  
]

or the service-time distribution.

### Step 6 — Determine capacity

Is:

[  
K=\infty  
]

or:

[  
K<\infty?  
]

### Step 7 — Choose a model

For example:

[  
M/M/1  
]

[  
M/M/c  
]

[  
M/M/1/K  
]

or:

[  
SPN  
]

### Step 8 — Determine performance metrics

For example:

[  
L,\quad L_q,\quad W,\quad W_q,\quad \rho,\quad P_{\text{loss}}  
]

### Step 9 — Validate assumptions

Compare model predictions with real measurements or simulation.

---

# 72. Queueing Network Interpretation

A communication network often contains several interconnected queues:

[  
Q_1\rightarrow Q_2\rightarrow Q_3  
]

For example:

[  
\text{Access Router}  
\rightarrow  
\text{Core Router}  
\rightarrow  
\text{Server}  
]

Each node may have:

- its own arrival rate;
    
- service rate;
    
- queue length;
    
- delay;
    
- loss probability.
    

Therefore, a network can be viewed as a **network of queues**.

This topic naturally extends queueing theory from one resource to an entire network.

---

# 73. Throughput

Throughput is the rate at which customers successfully complete service.

For a stable queue without losses:

[  
\boxed{  
X=\lambda  
}  
]

where:

[  
X=\text{throughput}  
]

For a finite-capacity system:

[  
\boxed{  
X=\lambda(1-P_{\text{loss}})  
}  
]

This distinction is essential.

An offered load of:

[  
1000\text{ packets/s}  
]

does not necessarily mean:

[  
1000\text{ packets/s}  
]

are successfully processed.

---

# 74. Utilization

For a single server:

[  
\boxed{  
\rho=\frac{\lambda}{\mu}  
}  
]

For (c) identical servers:

[  
\boxed{  
\rho=\frac{\lambda}{c\mu}  
}  
]

Utilization represents the fraction of available service capacity being consumed.

High utilization generally improves resource efficiency but increases queueing delay.

This creates a fundamental engineering trade-off:

[  
\boxed{  
\text{Efficiency}  
\leftrightarrow  
\text{Delay}  
}  
]

---

# 75. Buffer Size vs. Packet Loss

Increasing buffer capacity generally reduces overflow probability.

However, a larger buffer can also increase queueing delay.

Therefore:

[  
\boxed{  
\text{Large buffer}  
\rightarrow  
\text{lower loss but potentially higher delay}  
}  
]

This is one of the reasons why network performance cannot be optimized using only one metric.

A system might have:

- low packet loss;
    
- but excessive delay.
    

Or:

- low delay;
    
- but excessive packet loss.
    

Performance engineering requires balancing multiple QoS objectives.

---

# 76. Bufferbloat

A particularly important networking phenomenon is **bufferbloat**.

If a network buffer is excessively large, packets can remain queued for long periods even when packet loss is low.

The system may therefore exhibit:

[  
\boxed{  
\text{Low loss}  
+  
\text{High delay}  
}  
]

This illustrates why buffer capacity alone is not an adequate performance metric.

Queueing theory provides the tools for quantifying this trade-off.

---

# 77. A Unified View

The major models in this chapter can be connected as follows:

[  
\boxed{  
\text{Arrival Process}  
\rightarrow  
\text{Queue}  
\rightarrow  
\text{Service Process}  
}  
]

For simple systems:

[  
\boxed{  
M/M/1  
}  
]

For multiple servers:

[  
\boxed{  
M/M/c  
}  
]

For finite capacity:

[  
\boxed{  
M/M/1/K  
}  
]

For systems involving concurrency and synchronization:

[  
\boxed{  
\text{Petri Net}  
}  
]

For systems requiring both structure and stochastic timing:

[  
\boxed{  
\text{Stochastic Petri Net}  
}  
]

---

# 78. Essential Formulas

## Little's Law

[  
\boxed{  
L=\lambda W  
}  
]

[  
\boxed{  
L_q=\lambda W_q  
}  
]

---

## (M/M/1)

[  
\boxed{  
\rho=\frac{\lambda}{\mu}<1  
}  
]

[  
\boxed{  
L=\frac{\rho}{1-\rho}  
}  
]

[  
\boxed{  
L_q=\frac{\rho^2}{1-\rho}  
}  
]

[  
\boxed{  
W=\frac{1}{\mu-\lambda}  
}  
]

[  
\boxed{  
W_q=\frac{\rho}{\mu-\lambda}  
}  
]

[  
\boxed{  
P_n=(1-\rho)\rho^n  
}  
]

---

## (M/M/1/K)

For:

[  
\rho\neq1  
]

[  
\boxed{  
\pi_n=  
\frac{(1-\rho)\rho^n}  
{1-\rho^{K+1}}  
}  
]

and:

[  
\boxed{  
P_{\text{loss}}=\pi_K  
}  
]

---

## (M/M/c)

[  
\boxed{  
\rho=\frac{\lambda}{c\mu}<1  
}  
]

Erlang-C:

# [  
\boxed{  
P_{\text{wait}}

\frac{  
\frac{a^c}{c!}\frac{1}{1-\rho}  
}{  
\sum_{n=0}^{c-1}\frac{a^n}{n!}  
+  
\frac{a^c}{c!}\frac{1}{1-\rho}  
}  
}  
]

where:

[  
a=\frac{\lambda}{\mu}  
]

and:

[  
\boxed{  
W_q=  
\frac{P_{\text{wait}}}  
{c\mu-\lambda}  
}  
]

---

## (M/M/c/c)

Erlang-B:

# [  
\boxed{  
P_{\text{block}}

\frac{\frac{A^c}{c!}}  
{\sum_{k=0}^{c}\frac{A^k}{k!}}  
}  
]

where:

[  
\boxed{  
A=\frac{\lambda}{\mu}  
}  
]

---

## (M/G/1)

Pollaczek-Khinchine:

[  
\boxed{  
W_q=  
\frac{\lambda E[S^2]}  
{2(1-\rho)}  
}  
]

where:

[  
\boxed{  
\rho=\lambda E[S]  
}  
]

---

# 79. Key Concepts to Memorize

You should be able to explain these without consulting notes:

### Queueing system

[  
\boxed{  
\text{Arrivals + Waiting + Service + Capacity}  
}  
]

### Little's Law

[  
\boxed{  
L=\lambda W  
}  
]

### Stability

For (M/M/1):

[  
\boxed{  
\lambda<\mu  
}  
]

For (M/M/c):

[  
\boxed{  
\lambda<c\mu  
}  
]

### (M/M/1)

[  
\boxed{  
\rho=\frac{\lambda}{\mu}  
}  
]

### Finite buffer

[  
\boxed{  
P_{\text{loss}}=P(N=K)  
}  
]

under the standard loss assumption.

### Erlang-B

[  
\boxed{  
\text{Blocking when there is no waiting room}  
}  
]

### Erlang-C

[  
\boxed{  
\text{Waiting when all servers are occupied}  
}  
]

### Petri net

[  
\boxed{  
\text{Places + Transitions + Tokens + Arcs}  
}  
]

### Stochastic Petri net

[  
\boxed{  
\text{Petri net + random transition timing}  
}  
]

---

# 80. Worked Integrated Example

Consider a router with:

[  
\lambda=900\text{ packets/s}  
]

and:

[  
\mu=1000\text{ packets/s}  
]

First calculate:

[  
\rho=\frac{900}{1000}=0.9  
]

The queue is stable because:

[  
0.9<1  
]

The average number of packets is:

[  
L=\frac{0.9}{1-0.9}  
=9  
]

The average system time is:

[  
W=\frac{1}{1000-900}  
]

[  
W=0.01\text{ s}  
]

Thus:

[  
\boxed{  
W=10\text{ ms}  
}  
]

Now suppose the buffer has capacity:

[  
K=10  
]

The model becomes:

[  
M/M/1/10  
]

The probability that the system is full is:

# [  
P_{\text{loss}}

\frac{(1-\rho)\rho^{10}}  
{1-\rho^{11}}  
]

This means that the finite-buffer model predicts a nonzero packet-loss probability even though the corresponding infinite-buffer system is stable.

The effective throughput is:

[  
X=  
900(1-P_{\text{loss}})  
]

This example illustrates a crucial principle:

[  
\boxed{  
\text{Stability does not imply zero packet loss.}  
}  
]

A finite-capacity system can be stable while still dropping packets.

---

# 81. Exercises

## Exercise 1 — (M/M/1)

A router receives:

[  
\lambda=600\text{ packets/s}  
]

and can transmit:

[  
\mu=800\text{ packets/s}  
]

Calculate:

1. Utilization (\rho).
    
2. Probability that the system is empty.
    
3. Average number of packets in the system (L).
    
4. Average number waiting (L_q).
    
5. Average time in the system (W).
    
6. Average waiting time (W_q).
    

Verify your results using Little's Law.

---

## Exercise 2 — Utilization and Delay

Consider two routers with identical service rate:

[  
\mu=1000\text{ packets/s}  
]

Router A has:

[  
\lambda_A=500\text{ packets/s}  
]

Router B has:

[  
\lambda_B=900\text{ packets/s}  
]

Calculate the average system time for each router.

Explain why the difference is much larger than the difference in arrival rates might initially suggest.

---

## Exercise 3 — Little's Law

A network node has an average of:

[  
L=50  
]

packets in the system.

Its throughput is:

[  
\lambda=2000\text{ packets/s}  
]

Calculate:

[  
W  
]

in milliseconds.

---

## Exercise 4 — Finite Buffer

A router is modeled as:

[  
M/M/1/5  
]

with:

[  
\lambda=80\text{ packets/s}  
]

and:

[  
\mu=100\text{ packets/s}  
]

Calculate:

1. (\rho).
    
2. (\pi_0).
    
3. (\pi_5).
    
4. Packet-loss probability.
    
5. Effective throughput.
    

---

## Exercise 5 — Multi-Server Queue

A service system has:

[  
c=4  
]

servers.

Each server processes:

[  
\mu=5\text{ requests/s}  
]

The arrival rate is:

[  
\lambda=15\text{ requests/s}  
]

Calculate:

1. Total service capacity.
    
2. Server utilization.
    
3. Whether the system is stable.
    
4. The offered traffic (a=\lambda/\mu).
    

Then calculate the Erlang-C waiting probability.

---

## Exercise 6 — Erlang-B

A wireless system has:

[  
c=5  
]

communication channels.

The offered traffic is:

[  
A=3\text{ Erlangs}  
]

Calculate:

[  
P_{\text{block}}  
]

using Erlang-B.

Interpret the result as a probability of call blocking.

---

## Exercise 7 — Queueing Model Selection

For each system, choose an appropriate model and justify your choice.

### A

A router has one transmission interface, Poisson packet arrivals, exponential transmission times, and an effectively infinite buffer.

### B

A wireless system has 10 communication channels and no waiting room.

### C

A router has one transmission interface and a buffer that can store at most 50 packets.

### D

A distributed system has several parallel resources, synchronization conditions, resource conflicts, and stochastic service times.

Possible models:

[  
M/M/1  
]

[  
M/M/c/c  
]

[  
M/M/1/K  
]

[  
\text{Stochastic Petri Net}  
]

---

## Exercise 8 — Petri Net Modeling

Design a Petri-net model for a router with:

- a finite packet buffer;
    
- one transmission resource;
    
- packet arrivals;
    
- packet departures.
    

Identify:

1. Places.
    
2. Transitions.
    
3. Tokens.
    
4. Initial marking.
    
5. Conditions required for transmission.
    
6. What happens when the buffer is full.
    

---

## Exercise 9 — Stochastic Petri Net

A wireless packet can either:

- successfully transmit at rate:
    

[  
\lambda_s=9\text{ s}^{-1}  
]

- fail at rate:
    

[  
\lambda_f=1\text{ s}^{-1}  
]

Calculate:

1. The total firing rate.
    
2. The expected time until one of the events occurs.
    
3. The probability of successful transmission occurring first.
    
4. The probability of failure occurring first.
    

---

# 82. Advanced Questions for Deeper Understanding

A student who wants to master network performance modeling should be able to reason about the following questions.

### Question 1

Why does increasing utilization from 50% to 90% have such a dramatic effect on delay?

### Question 2

Why can increasing buffer size reduce packet loss while increasing delay?

### Question 3

Why does service-time variability influence queueing delay?

### Question 4

When is (M/M/1) an inappropriate model for a real network?

### Question 5

Why is (M/M/c/c) appropriate for a system with no waiting room?

### Question 6

What advantage does a Petri net provide when multiple resources operate concurrently?

### Question 7

How can a stochastic Petri net be transformed into a CTMC?

### Question 8

Why is state-space explosion a problem when converting complex Petri nets into Markov models?

---

# 83. Chapter Summary

Queueing theory provides a mathematical framework for studying systems in which customers compete for limited service resources.

The basic structure is:

[  
\boxed{  
\text{Arrivals}  
\rightarrow  
\text{Queue}  
\rightarrow  
\text{Service}  
\rightarrow  
\text{Departures}  
}  
]

The principal performance measures are:

[  
\boxed{  
L,\quad L_q,\quad W,\quad W_q,\quad \rho,\quad X,\quad P_{\text{loss}}  
}  
]

where:

- (L) = average number in system;
    
- (L_q) = average queue length;
    
- (W) = average time in system;
    
- (W_q) = average waiting time;
    
- (\rho) = utilization;
    
- (X) = throughput;
    
- (P_{\text{loss}}) = loss probability.
    

The most important analytical models are:

[  
\boxed{  
M/M/1  
}  
]

for single-server systems,

[  
\boxed{  
M/M/c  
}  
]

for multiple servers with waiting,

[  
\boxed{  
M/M/c/c  
}  
]

for multiple servers without waiting,

and:

[  
\boxed{  
M/M/1/K  
}  
]

for a single server with finite capacity.

For more complex systems involving concurrency, synchronization, and resource sharing:

[  
\boxed{  
\text{Petri Nets}  
}  
]

provide a powerful modeling formalism.

When stochastic timing is added:

[  
\boxed{  
\text{Stochastic Petri Nets}  
}  
]

allow both structural and performance analysis.

The complete conceptual chain is:

[  
\boxed{  
\text{Network}  
\rightarrow  
\text{Arrivals}  
\rightarrow  
\text{Queue}  
\rightarrow  
\text{Service}  
\rightarrow  
\text{Markov/Petri Model}  
\rightarrow  
\text{Performance Metrics}  
}  
]

The most important engineering lesson is:

[  
\boxed{  
\textbf{High utilization, finite buffers, and variability interact to determine network performance.}  
}  
]

A network should therefore not be evaluated using throughput alone. **Delay, utilization, loss, queue occupancy, blocking, and resource capacity must be considered together.**