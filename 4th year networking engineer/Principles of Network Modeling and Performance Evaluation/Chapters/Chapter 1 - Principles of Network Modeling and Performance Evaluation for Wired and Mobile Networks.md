# Chapter 1 — Principles of Network Modeling and Performance Evaluation for Wired and Mobile Networks

## 1. Introduction

Modern communication networks are complex systems composed of numerous interconnected elements: links, routers, switches, base stations, mobile devices, servers, and protocols.

As the size and complexity of a network increase, it becomes difficult to determine its behavior solely through experimentation on the real system. Network modeling and performance evaluation provide a systematic approach to understanding, predicting, and optimizing network behavior.

The central question is:

> **How can we predict whether a network will satisfy its performance requirements under a given workload and operating environment?**

Network performance evaluation attempts to answer questions such as:

- How much traffic can the network support?
    
- What is the expected packet delay?
    
- What is the probability of packet loss?
    
- How does congestion develop?
    
- How many users can a wireless cell support?
    
- What happens when traffic increases?
    
- Which network configuration provides the best performance?
    
- How does mobility affect network performance?
    
- What is the impact of a failure or a change in topology?
    

This chapter introduces the fundamental principles required to answer these questions.

---

# 2. Network Modeling

## 2.1 Definition

A **network model** is a simplified mathematical or computational representation of a real communication network.

A model does not reproduce every physical detail of the real system. Instead, it retains the characteristics that are relevant to the problem being studied.

We can represent the modeling process as:

$$
\text{Real Network}  
\rightarrow  
\text{Abstraction}  
\rightarrow  
\text{Mathematical/Computational Model}  
\rightarrow  
\text{Performance Analysis}  
$$

The purpose of abstraction is to reduce complexity while preserving the properties that significantly influence performance.

---

## 2.2 Why do we model networks?

Network modeling is useful for several reasons.

### 1. Prediction

A model can predict how a network behaves under different traffic conditions.

For example:

[  
\lambda \uparrow  
\quad\Rightarrow\quad  
\text{queue length} \uparrow  
\quad\Rightarrow\quad  
\text{delay} \uparrow  
]

where (\lambda) represents the packet arrival rate.

### 2. Design

Models allow engineers to compare different architectures before implementing them.

For example, we may compare:

- a 1 Gbit/s link with a 10 Gbit/s link;
    
- different routing algorithms;
    
- different buffer sizes;
    
- different wireless channel configurations.
    

### 3. Optimization

A model can identify configurations that minimize:

- delay,
    
- packet loss,
    
- energy consumption,
    
- congestion,
    

while maximizing:

- throughput,
    
- reliability,
    
- capacity,
    
- resource utilization.
    

### 4. Cost reduction

Experimenting on a real network can be expensive or impossible.

A simulation can test thousands of scenarios without deploying physical infrastructure.

### 5. Understanding

A simplified model makes it possible to isolate the effect of individual variables.

---

# 3. Components of a Network Model

A network model generally contains four fundamental elements:

1. **Entities**
    
2. **Resources**
    
3. **Workload**
    
4. **Performance measures**
    

## 3.1 Entities

Entities are the objects participating in network communication.

Examples include:

- packets,
    
- flows,
    
- users,
    
- mobile terminals,
    
- routers,
    
- switches,
    
- base stations,
    
- servers.
    

---

## 3.2 Resources

Resources are the network elements used to provide communication services.

Examples:

- transmission links,
    
- buffers,
    
- radio channels,
    
- processing capacity,
    
- spectrum,
    
- CPU resources.
    

A resource can become a **bottleneck** when demand exceeds its capacity.

---

## 3.3 Workload

The workload represents the traffic imposed on the network.

It may be characterized by:

- packet arrival rate,
    
- packet size,
    
- number of users,
    
- flow duration,
    
- session arrival rate,
    
- traffic burstiness,
    
- mobility patterns.
    

For packet traffic, the arrival rate is commonly represented by:

[  
\lambda = \frac{\text{number of arriving packets}}{\text{unit of time}}  
]

and is generally expressed in packets/s.

---

## 3.4 Performance measures

Performance measures quantify the behavior of the network.

Typical metrics include:

- throughput,
    
- delay,
    
- jitter,
    
- packet loss,
    
- blocking probability,
    
- utilization,
    
- availability,
    
- energy consumption.
    

These metrics will be studied in detail throughout the course.

---

# 4. Wired and Mobile Networks

Network modeling depends strongly on the type of network.

## 4.1 Wired networks

A wired network uses physical transmission media such as:

- optical fiber,
    
- copper cables,
    
- coaxial cables.
    

Examples include:

- Ethernet networks,
    
- data-center networks,
    
- IP backbone networks,
    
- access networks.
    

A simplified wired network can be represented as a graph:

[  
G=(V,E)  
]

where:

- (V) is the set of network nodes;
    
- (E) is the set of communication links.
    

Each link may be characterized by:

- bandwidth (C),
    
- propagation delay (d_p),
    
- transmission delay (d_t),
    
- queue size (B),
    
- packet loss probability.
    

---

## 4.2 Mobile networks

Mobile networks introduce additional sources of variability.

Examples include:

- cellular networks,
    
- wireless LANs,
    
- mobile ad hoc networks,
    
- vehicular networks,
    
- wireless sensor networks.
    

Unlike wired links, wireless links are affected by:

- channel fading,
    
- interference,
    
- path loss,
    
- noise,
    
- user mobility,
    
- radio resource allocation.
    

Therefore, the network state may vary with time and location.

A mobile network model may need to represent:

$$
\text{Network State}(t)
$$
$$
f(  
\text{topology},  
\text{traffic},  
\text{mobility},  
\text{channel},  
\text{resources}  
)  
$$

---

# 5. Abstraction Levels

One of the most important principles of modeling is choosing the appropriate level of abstraction.

A network can be modeled at several levels.

## 5.1 Physical level

The model describes physical phenomena such as:

- signal power,
    
- noise,
    
- interference,
    
- modulation,
    
- coding,
    
- propagation.
    

For example, the received power can be represented conceptually as:

[  
P_r = P_t G_t G_r L  
]

where:

- (P_t) = transmitted power;
    
- (G_t) = transmitter antenna gain;
    
- (G_r) = receiver antenna gain;
    
- (L) = propagation-related loss factor.
    

---

## 5.2 Link level

The model focuses on communication links.

Important parameters include:

- bandwidth,
    
- data rate,
    
- bit error rate,
    
- packet error rate,
    
- propagation delay.
    

---

## 5.3 Network level

The model focuses on:

- routing,
    
- topology,
    
- congestion,
    
- queues,
    
- packet forwarding.
    

At this level, physical details may be abstracted into parameters such as link capacity and error probability.

---

## 5.4 Application level

The model focuses on user and application behavior.

Examples:

- web traffic,
    
- video streaming,
    
- VoIP,
    
- file transfers,
    
- IoT traffic.
    

The correct abstraction depends on the objective of the study.

> **A good model is not necessarily the most detailed model. It is the model that contains sufficient detail to answer the question being studied.**

---

# 6. Deterministic and Stochastic Models

Network behavior may be modeled using either deterministic or stochastic approaches.

## 6.1 Deterministic model

A deterministic model assumes that all relevant variables are known exactly.

For example, suppose packets arrive exactly every (10) ms.

Then:

[  
T_a = 10 \text{ ms}  
]

for every packet arrival.

The behavior is therefore predictable.

---

## 6.2 Stochastic model

In real networks, many variables are uncertain.

Packet arrivals, packet sizes, service times, channel conditions, and user behavior can vary randomly.

A stochastic model therefore represents these variables using probability distributions.

For example:

[  
A(t) \sim \text{Poisson process}  
]

may be used to model packet arrivals.

Similarly, a service time (S) could be modeled using a random variable:

$$
S \sim \text{Exponential}(\mu)
$$

where (\mu) is the service rate.

Stochastic models are particularly important in performance evaluation because real network traffic is rarely perfectly deterministic.

---

# 7. Traffic Modeling

Traffic modeling is fundamental to performance analysis.

A network may perform very differently under different workloads even when the network architecture is unchanged.

## 7.1 Traffic intensity

For a simple system, traffic intensity can be represented by:

[  
\rho = \frac{\lambda}{\mu}  
]

where:

- (\lambda) = average arrival rate;
    
- (\mu) = average service rate.
    

For a stable single-server system, we generally require:

[  
\rho < 1  
]

If:

[  
\rho \geq 1  
]

the average offered workload is at least as large as the service capacity, and an unlimited queue will not have a stable long-term average size.

This concept is central to queueing theory.

---

## 7.2 Packet arrival rate

The packet arrival rate is:

[  
\lambda = \frac{N}{T}  
]

where:

- (N) = number of packets arriving during interval (T).
    

Example:

If 12,000 packets arrive during 10 seconds:

[  
\lambda = \frac{12000}{10}  
=1200\text{ packets/s}  
]

---

## 7.3 Bit rate

If the average packet size is (L) bits, the offered bit rate is approximately:

[  
R = \lambda L  
]

For example:

[  
\lambda = 1000\text{ packets/s}  
]

and

[  
L = 12000\text{ bits}  
]

give:

[  
R = 1000 \times 12000  
=12,000,000\text{ bit/s}  
]

or:

[  
R=12\text{ Mbit/s}  
]

---

# 8. Performance Evaluation

## 8.1 Definition

**Performance evaluation** is the process of measuring or estimating how well a system performs according to defined criteria.

The basic process is:

[  
\boxed{  
\text{Model}  
\rightarrow  
\text{Workload}  
\rightarrow  
\text{Experiment/Analysis}  
\rightarrow  
\text{Metrics}  
\rightarrow  
\text{Interpretation}  
}  
]

The evaluation should be based on clearly defined objectives.

---

# 9. Main Network Performance Metrics

## 9.1 Throughput

Throughput is the amount of useful data successfully delivered per unit of time.

[  
X = \frac{\text{successfully transmitted data}}{\text{time}}  
]

It is commonly expressed in:

- bit/s,
    
- Mbit/s,
    
- Gbit/s,
    
- packets/s.
    

Throughput should not be confused with nominal link capacity.

If a link has capacity:

[  
C=100\text{ Mbit/s}  
]

the actual throughput may be only:

[  
X=70\text{ Mbit/s}  
]

because of congestion, protocol overhead, errors, or insufficient traffic.

---

## 9.2 Delay

Network delay is the time required for a packet to travel from source to destination.

A useful decomposition is:

[  
D =  
D_{\text{processing}}  
+  
D_{\text{queue}}  
+  
D_{\text{transmission}}  
+  
D_{\text{propagation}}  
]

### Processing delay

Time required to process a packet.

### Queueing delay

Time spent waiting in buffers.

### Transmission delay

Time required to place all packet bits onto the transmission medium.

For a packet of (L) bits transmitted at rate (R):

# [  
D_{\text{transmission}}

\frac{L}{R}  
]

### Propagation delay

Time required for the signal to physically propagate through the medium.

# [  
D_{\text{propagation}}

\frac{d}{v}  
]

where:

- (d) = distance;
    
- (v) = propagation speed.
    

Therefore:

# [  
D_{\text{total}}

D_p+D_q+D_t+D_{\text{prop}}  
]

---

# 10. Jitter

For real-time applications, average delay is not sufficient.

**Jitter** describes the variation in packet delay.

Suppose four packets experience delays:

[  
10,;12,;10,;25\text{ ms}  
]

The average delay alone does not fully describe the behavior.

The sudden increase to (25) ms can negatively affect:

- VoIP,
    
- video conferencing,
    
- interactive gaming,
    
- industrial control.
    

Thus, performance evaluation should sometimes consider both:

[  
\text{Mean Delay}  
]

and

[  
\text{Delay Variation}  
]

---

# 11. Packet Loss

Packet loss occurs when packets fail to reach their destination.

The packet loss ratio can be defined as:

# [  
P_{\text{loss}}

\frac{N_{\text{lost}}}  
{N_{\text{sent}}}  
]

or as a percentage:

# [  
P_{\text{loss}}(%)

100  
\frac{N_{\text{lost}}}  
{N_{\text{sent}}}  
]

Example:

If 50 packets are lost out of 10,000:

# [  
P_{\text{loss}}

\frac{50}{10000}  
=0.005  
]

Therefore:

[  
P_{\text{loss}}=0.5%  
]

---

# 12. Utilization

Utilization measures how intensively a resource is being used.

For a link:

[  
U=\frac{\text{average carried traffic}}{\text{link capacity}}  
]

For example, if a 100 Mbit/s link carries an average of 80 Mbit/s:

[  
U=\frac{80}{100}=0.8  
]

Therefore:

[  
U=80%  
]

High utilization can indicate efficient resource usage, but excessive utilization can also lead to rapidly increasing queueing delay.

---

# 13. Capacity

Network capacity refers to the maximum amount of traffic or number of users that can be supported while satisfying specified performance requirements.

Capacity depends on the criterion being used.

For example, a network may be considered capable of supporting users if:

[  
D \leq D_{\max}  
]

and:

[  
P_{\text{loss}}\leq P_{\max}  
]

Therefore, capacity is not necessarily equivalent to the maximum theoretical bit rate.

---

# 14. Quality of Service

Performance evaluation is often performed in terms of **Quality of Service (QoS)** requirements.

Different applications have different requirements.

|Application|Important metrics|
|---|---|
|File transfer|Throughput, completion time|
|Web browsing|Response time, delay|
|VoIP|Delay, jitter, packet loss|
|Video streaming|Throughput, delay, packet loss|
|Industrial control|Delay, reliability|
|IoT|Energy, reliability, latency|

This leads to an important principle:

> **There is no single universal performance metric.**

The appropriate metrics depend on the application and evaluation objective.

---

# 15. Methods of Performance Evaluation

There are three major approaches.

## 15.1 Analytical modeling

Analytical methods use mathematical equations to derive performance measures.

Examples include:

- queueing theory,
    
- Markov chains,
    
- probability theory,
    
- stochastic processes,
    
- optimization theory.
    

### Advantages

- Fast evaluation once the model is established.
    
- Mathematical insight.
    
- Useful for identifying relationships between parameters.
    
- Can provide exact or approximate solutions.
    

### Limitations

- Requires simplifying assumptions.
    
- Complex networks can lead to mathematically intractable models.
    

---

# 16. Simulation

Simulation reproduces the behavior of a network model over time.

A simulation can represent:

- packet arrivals,
    
- queueing,
    
- routing,
    
- wireless channel behavior,
    
- mobility,
    
- packet transmission,
    
- failures.
    

A typical simulation workflow is:

[  
\text{Model}  
\rightarrow  
\text{Input Parameters}  
\rightarrow  
\text{Simulation Runs}  
\rightarrow  
\text{Measurements}  
\rightarrow  
\text{Statistical Analysis}  
]

Simulation is especially useful when analytical modeling becomes too difficult.

---

## 16.1 Discrete-event simulation

Network simulation is often based on **Discrete-Event Simulation (DES)**.

The system state changes only when an event occurs.

Examples:

- packet arrival,
    
- packet departure,
    
- link failure,
    
- handover,
    
- route change.
    

Suppose the event sequence is:

[  
E_1,E_2,E_3,\ldots,E_n  
]

where each event occurs at time:

[  
t_1<t_2<t_3<\cdots<t_n  
]

The simulator processes events in chronological order.

---

# 17. Experimental Measurement

The third major approach is measurement on a real system.

Examples include measuring:

- real packet delays,
    
- throughput,
    
- packet loss,
    
- radio signal quality,
    
- network utilization.
    

### Advantages

- Represents real-world behavior.
    
- Captures effects that may be absent from simplified models.
    

### Limitations

- Can be expensive.
    
- Difficult to control all variables.
    
- Reproducibility may be limited.
    
- Some experiments may be impossible or unethical to perform on operational networks.
    

---

# 18. Comparison of Evaluation Methods

|Method|Main advantage|Main limitation|
|---|---|---|
|Analytical|Mathematical insight and speed|Requires assumptions|
|Simulation|Flexible and detailed|Computational cost|
|Measurement|Real-world accuracy|Cost and limited control|

In practice, the strongest studies often combine multiple approaches.

For example:

[  
\boxed{  
\text{Analytical Model}  
+  
\text{Simulation}  
+  
\text{Real Measurements}  
}  
]

can provide stronger validation than any single method.

---

# 19. Modeling Mobile Networks

Mobile networks require additional modeling dimensions.

## 19.1 Mobility

Users can change position over time:

[  
(x(t),y(t))  
]

This can modify:

- received signal strength,
    
- path loss,
    
- interference,
    
- available data rate,
    
- serving base station.
    

---

## 19.2 Channel variability

A wireless channel may vary because of:

- distance,
    
- shadowing,
    
- multipath propagation,
    
- fading,
    
- interference.
    

Consequently, the instantaneous transmission rate may be represented as:

[  
R(t)  
]

rather than a constant (R).

---

## 19.3 Handover

When a mobile terminal moves between coverage areas, it may change its serving base station.

This process is called **handover**.

Handover performance can be evaluated using:

- handover probability,
    
- handover failure probability,
    
- interruption time,
    
- signaling overhead,
    
- packet loss,
    
- latency.
    

---

## 19.4 Mobile network capacity

In a cellular system, capacity depends on multiple factors:

[  
C =  
f(  
\text{spectrum},  
\text{bandwidth},  
\text{SINR},  
\text{reuse},  
\text{interference},  
\text{scheduler},  
\text{traffic}  
)  
]

This is one reason why mobile-network performance evaluation is more complicated than simply dividing bandwidth by the number of users.

---

# 20. Model Assumptions

Every model contains assumptions.

For example, we might assume:

- packet arrivals follow a Poisson process;
    
- packet sizes are constant;
    
- service time is exponentially distributed;
    
- links have constant capacity;
    
- packets are independent;
    
- users do not move.
    

These assumptions simplify the analysis.

However, assumptions can also introduce errors.

Therefore, a performance study must explicitly state:

1. **What is modeled?**
    
2. **What is ignored?**
    
3. **Which assumptions are made?**
    
4. **Why are those assumptions acceptable?**
    

---

# 21. Model Validation

A model should not automatically be considered correct simply because its mathematical formulation is elegant.

**Model validation** determines whether the model adequately represents the real system for the intended purpose.

A simplified validation process is:

[  
\text{Real System}  
\rightarrow  
\text{Measurements}  
]

and:

[  
\text{Model}  
\rightarrow  
\text{Predictions}  
]

Then compare:

[  
\text{Predictions}  
\quad\text{vs.}\quad  
\text{Measurements}  
]

If the difference is sufficiently small for the intended application, the model may be considered valid within that domain.

---

# 22. Verification vs. Validation

These concepts must be distinguished.

### Verification

> **Did we implement the model correctly?**

Verification concerns the correctness of the implementation.

### Validation

> **Is the model an adequate representation of the real system?**

Validation concerns the adequacy of the model itself.

A simulation can be perfectly implemented and still be based on a poor model.

Thus:

[  
\boxed{  
\text{Verification} \neq \text{Validation}  
}  
]

---

# 23. Statistical Nature of Performance Evaluation

When random variables are involved, one simulation result is generally insufficient.

Suppose a simulation produces:

[  
D_1,D_2,\ldots,D_n  
]

for (n) packet delays.

The sample mean is:

[  
\bar D =  
\frac{1}{n}  
\sum_{i=1}^{n}D_i  
]

The sample variance is:

[  
S^2=  
\frac{1}{n-1}  
\sum_{i=1}^{n}  
(D_i-\bar D)^2  
]

The results therefore contain statistical uncertainty.

A rigorous performance study should consider:

- number of observations,
    
- number of independent runs,
    
- random seeds,
    
- confidence intervals,
    
- warm-up period,
    
- transient behavior,
    
- steady-state behavior.
    

These topics become particularly important in simulation-based evaluation.

---

# 24. A General Performance Evaluation Methodology

A robust study can be organized into the following steps.

## Step 1 — Define the objective

Example:

> Determine the maximum number of users that can be supported while maintaining an average delay below 50 ms.

---

## Step 2 — Define the system

Specify:

- topology,
    
- links,
    
- nodes,
    
- protocols,
    
- wireless characteristics,
    
- resources.
    

---

## Step 3 — Define the workload

Specify:

- number of users,
    
- arrival rate,
    
- packet sizes,
    
- traffic patterns,
    
- mobility.
    

---

## Step 4 — Select the performance metrics

For example:

[  
D_{\text{avg}},  
\quad  
P_{\text{loss}},  
\quad  
X,  
\quad  
U  
]

---

## Step 5 — Choose the evaluation method

Select:

- analytical model,
    
- simulation,
    
- measurement,
    
- or a combination.
    

---

## Step 6 — Run experiments

Vary important parameters such as:

[  
\lambda,\quad  
N,\quad  
C,\quad  
B  
]

where:

- (\lambda) = traffic arrival rate;
    
- (N) = number of users;
    
- (C) = capacity;
    
- (B) = buffer size.
    

---

## Step 7 — Analyze the results

Look for:

- trends,
    
- bottlenecks,
    
- saturation,
    
- instability,
    
- trade-offs.
    

---

## Step 8 — Validate conclusions

Check whether the conclusions are consistent with:

- theoretical expectations,
    
- alternative models,
    
- simulations,
    
- real measurements.
    

---

# 25. Example: Simple Network Performance Analysis

Consider a router with a single outgoing link.

The link capacity is:

[  
C=10\text{ Mbit/s}  
]

Packets arrive at:

[  
\lambda=500\text{ packets/s}  
]

The average packet size is:

[  
L=10,000\text{ bits}  
]

The offered traffic is:

[  
R=\lambda L  
]

Therefore:

[  
R=500\times10,000  
]

[  
R=5,000,000\text{ bit/s}  
]

Thus:

[  
R=5\text{ Mbit/s}  
]

The utilization is approximately:

[  
U=\frac{R}{C}  
]

[  
U=\frac{5}{10}=0.5  
]

Therefore:

[  
\boxed{U=50%}  
]

The network is not operating at saturation under this simplified model.

Now suppose the arrival rate increases to:

[  
\lambda=1200\text{ packets/s}  
]

Then:

[  
R=1200\times10,000  
=12\text{ Mbit/s}  
]

But:

[  
C=10\text{ Mbit/s}  
]

Therefore:

[  
R>C  
]

The offered load exceeds the link capacity.

This indicates that, under sustained conditions, the queue will tend to grow unless traffic is controlled or packets are dropped.

This simple example illustrates a fundamental performance principle:

> **When offered load approaches or exceeds resource capacity, congestion becomes increasingly important.**

---

# 26. Important Performance Trade-offs

Network engineering is fundamentally about trade-offs.

## Throughput vs. delay

Increasing utilization may increase throughput but can also increase queueing delay.

Conceptually:

[  
U\uparrow  
\quad\Rightarrow\quad  
D_q\uparrow  
]

especially as the system approaches saturation.

---

## Buffer size vs. packet loss

A larger buffer can reduce packet drops during temporary bursts:

[  
B\uparrow  
\quad\Rightarrow\quad  
P_{\text{loss}}\downarrow  
]

in some conditions.

However, excessively large buffers can increase queueing delay, producing the phenomenon commonly associated with **bufferbloat**.

---

## Wireless capacity vs. interference

Increasing simultaneous transmissions may improve resource utilization, but interference can reduce the quality of the wireless channel.

---

## Energy vs. performance

In mobile and wireless systems, improving performance may require additional:

- transmission power,
    
- processing,
    
- signaling,
    
- radio resources.
    

Thus:

[  
\text{Performance}  
\leftrightarrow  
\text{Energy Consumption}  
]

is often an important optimization problem.

---

# 27. Key Concepts to Remember

The most important concepts from this chapter are:

### 1. Modeling

A network model is an abstraction of a real system designed for a specific analytical purpose.

### 2. Workload

Network behavior depends strongly on the traffic imposed on the system.

### 3. Performance metrics

Important metrics include:

[  
\boxed{  
\text{Throughput, Delay, Jitter, Loss, Utilization, Capacity}  
}  
]

### 4. Evaluation methods

The three fundamental approaches are:

[  
\boxed{  
\text{Analytical}  
\quad  
\text{Simulation}  
\quad  
\text{Measurement}  
}  
]

### 5. Randomness

Real networks are inherently stochastic, making probability and statistics essential tools.

### 6. Stability

A fundamental concept is the relationship between offered load and service capacity.

For a simple system:

[  
\rho=\frac{\lambda}{\mu}  
]

and stable operation generally requires:

[  
\rho<1  
]

### 7. Mobile networks

Mobility, interference, fading, and time-varying channel conditions make mobile-network modeling more complex.

### 8. Validation

A model must be validated against the system it represents or against sufficiently credible reference behavior.

---

# 28. Chapter Summary

Network modeling and performance evaluation provide the theoretical and practical foundations required to analyze communication systems.

The essential methodology is:

[  
\boxed{  
\text{Real Network}  
\rightarrow  
\text{Abstraction}  
\rightarrow  
\text{Model}  
\rightarrow  
\text{Workload}  
\rightarrow  
\text{Evaluation}  
\rightarrow  
\text{Metrics}  
\rightarrow  
\text{Validation}  
}  
]

A good performance analysis begins with a clearly defined objective and selects an appropriate level of abstraction.

For wired networks, important factors include:

- topology,
    
- link capacity,
    
- queueing,
    
- routing,
    
- buffers,
    
- traffic load.
    

For mobile networks, additional factors include:

- mobility,
    
- radio propagation,
    
- interference,
    
- fading,
    
- spectrum,
    
- handover,
    
- dynamic resource allocation.
    

The next chapters can build on this foundation by introducing the mathematical tools used to model these systems, particularly **probability theory, stochastic processes, queueing theory, traffic models, and discrete-event simulation**.

---

# 29. Self-Assessment Questions

## Conceptual Questions

1. What is a network model?
    
2. Why is abstraction necessary in network modeling?
    
3. What is the difference between a deterministic and stochastic model?
    
4. What is the difference between throughput and link capacity?
    
5. What are the four main components of network delay?
    
6. What is jitter and why is it important for real-time applications?
    
7. Define packet loss ratio.
    
8. What does network utilization represent?
    
9. What is the difference between analytical modeling and simulation?
    
10. What is the difference between verification and validation?
    
11. Why is mobile-network modeling generally more complex than wired-network modeling?
    
12. What happens when offered traffic exceeds the capacity of a bottleneck resource?
    

---

# 30. Exercises

## Exercise 1 — Link Utilization

A link has a capacity of:

[  
C=100\text{ Mbit/s}  
]

The network generates 2,000 packets/s, each with an average size of 4,000 bytes.

Calculate:

1. The offered traffic in Mbit/s.
    
2. The link utilization.
    
3. Whether the link is saturated.
    

---

## Exercise 2 — Delay

A packet has a size of 12,000 bits and is transmitted over a 20 Mbit/s link.

Calculate the transmission delay:

[  
D_t=\frac{L}{R}  
]

---

## Exercise 3 — Packet Loss

A router receives 50,000 packets during an experiment and drops 250 packets.

Calculate:

[  
P_{\text{loss}}  
]

as a percentage.

---

## Exercise 4 — Traffic Intensity

A queue receives packets at an average rate:

[  
\lambda=800\text{ packets/s}  
]

The server processes:

[  
\mu=1000\text{ packets/s}  
]

Calculate:

[  
\rho=\frac{\lambda}{\mu}  
]

Is the system stable under the basic single-server queueing assumption?

---

## Exercise 5 — Mobile Network

A mobile user moves from one base station's coverage area to another.

Identify at least four performance metrics that could be used to evaluate the resulting handover.

Explain why each metric is relevant.

---

# 31. Mentor's Perspective

When studying network performance, avoid memorizing formulas without understanding what they represent.

For every performance equation, ask four questions:

1. **What does each variable represent?**
    
2. **What assumptions does the equation make?**
    
3. **What physical/network phenomenon does it describe?**
    
4. **When does the equation stop being a good approximation?**
    

For example:

[  
\rho=\frac{\lambda}{\mu}  
]

is not merely a formula to calculate a number.

It expresses a fundamental relationship:

[  
\boxed{  
\text{offered workload}  
\quad\text{vs.}\quad  
\text{processing capacity}  
}  
]

Understanding this relationship will make later concepts in **queueing theory and network performance analysis** considerably easier.

The goal of this course is therefore not simply to calculate performance metrics, but to develop the ability to answer:

> **Why does network performance behave this way, and how can we predict or improve it?**