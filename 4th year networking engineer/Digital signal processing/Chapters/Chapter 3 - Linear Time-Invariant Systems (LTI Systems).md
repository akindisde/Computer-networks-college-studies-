# Chapter 3 — Linear Time-Invariant Systems (LTI Systems)

## 1. Generalities on Systems

### 1.1 Introduction

In signal processing, a **system** is a mathematical model that transforms an input signal into an output signal.

We represent a system as:

[  
x(t)\xrightarrow{\boxed{\text{System}}}y(t)  
]

where:

- (x(t)) is the **input**,
    
- (y(t)) is the **output**.
    

Mathematically:

[  
\boxed{y(t)=\mathcal{T}{x(t)}}  
]

where (\mathcal{T}) denotes the system operator.

The system may represent:

- an electrical circuit,
    
- an amplifier,
    
- a filter,
    
- a mechanical structure,
    
- a communication channel,
    
- a sensor,
    
- a digital algorithm,
    
- a mathematical transformation.
    

The central problem of system analysis is:

> Given an input signal and a system, determine the output signal.

For LTI systems, this problem becomes particularly elegant because the output can be calculated using **convolution**.

---

## 1.2 Continuous-time and discrete-time systems

A continuous-time system maps continuous-time signals:

[  
x(t)\rightarrow y(t)  
]

A discrete-time system maps sequences:

[  
x[n]\rightarrow y[n]  
]

For example:

[  
y(t)=2x(t)  
]

is a continuous-time system, while:

[  
y[n]=2x[n]  
]

is a discrete-time system.

The mathematical principles are largely analogous, but integrals are replaced by sums in discrete time.

---

# 2. Basic System Properties

A system can be classified according to several properties.

These properties are important because they determine what mathematical tools can be used and whether a system is physically realizable or stable.

---

## 2.1 Linearity

A system (\mathcal{T}) is **linear** if it satisfies both:

### Homogeneity

If:

[  
x(t)\rightarrow y(t)  
]

then:

[  
Ax(t)\rightarrow Ay(t)  
]

for any constant (A).

### Additivity

If:

[  
x_1(t)\rightarrow y_1(t)  
]

and:

[  
x_2(t)\rightarrow y_2(t)  
]

then:

[  
x_1(t)+x_2(t)  
\rightarrow  
y_1(t)+y_2(t)  
]

Together, these properties give the **superposition principle**:

a\mathcal{T}{x_1(t)}  
+  
b\mathcal{T}{x_2(t)}  
}  
]

for arbitrary constants (a) and (b).

This property is the foundation of LTI-system analysis.

---

## 2.2 Example of a linear system

Consider:

[  
y(t)=3x(t)  
]

Then:

3[a x_1(t)+b x_2(t)]  
]

which gives:

[  
=3a x_1(t)+3b x_2(t)  
]

and therefore:

[  
=a\mathcal{T}{x_1(t)}  
+b\mathcal{T}{x_2(t)}  
]

The system is linear.

---

## 2.3 Example of a nonlinear system

Consider:

[  
y(t)=x^2(t)  
]

For an input (ax(t)):

[  
y(t)=[ax(t)]^2=a^2x^2(t)  
]

But linearity would require:

[  
ay(t)=ax^2(t)  
]

which is not generally equal to:

[  
a^2x^2(t)  
]

Therefore:

[  
\boxed{y(t)=x^2(t)\text{ is nonlinear}}  
]

---

# 3. Time Invariance

A system is **time-invariant** if shifting the input in time causes exactly the same shift in the output.

Suppose:

$$
x(t)\rightarrow y(t)  
$$

If the input is delayed:

[  
x(t-t_0)  
]

then a time-invariant system must produce:

[  
y(t-t_0)  
]

Therefore:

y(t-t_0)  
}  
]

for every (t_0).

---

## 3.1 Example of a time-invariant system

Consider:

[  
y(t)=x(t-2)  
]

For an input:

[  
x(t)\rightarrow y(t)=x(t-2)  
]

Now shift the input:

[  
x_s(t)=x(t-t_0)  
]

The output becomes:

[  
y_s(t)=x_s(t-2)  
]

so:

[  
y_s(t)=x(t-t_0-2)  
]

while:

[  
y(t-t_0)=x(t-t_0-2)  
]

Therefore:

[  
y_s(t)=y(t-t_0)  
]

and the system is time-invariant.

---

## 3.2 Example of a time-varying system

Consider:

[  
y(t)=t,x(t)  
]

For the shifted input:

[  
x_s(t)=x(t-t_0)  
]

the output is:

[  
y_s(t)=t,x(t-t_0)  
]

But the shifted original output is:

(t-t_0)x(t-t_0)  
]

These are not generally equal.

Therefore:

[  
\boxed{y(t)=t,x(t)\text{ is time-varying}}  
]

---

# 4. Causality

A system is **causal** if its output at time (t_0) depends only on input values at times:

[  
t\leq t_0  
]

In other words, the system cannot depend on future input values.

For a causal system:

[  
\boxed{  
y(t_0)\text{ depends only on }x(t),\quad t\leq t_0  
}  
]

This is essential for real-time physical systems.

---

## 4.1 Example of a causal system

[  
y(t)=x(t)+x(t-1)  
]

At time (t_0), the output depends on:

[  
x(t_0)  
]

and:

[  
x(t_0-1)  
]

Both are present or past values.

Therefore the system is causal.

---

## 4.2 Example of a non-causal system

[  
y(t)=x(t+1)  
]

At time (t_0):

[  
y(t_0)=x(t_0+1)  
]

which requires a future input value.

Therefore:

[  
\boxed{\text{The system is non-causal}}  
]

Non-causal systems can still be mathematically useful and can sometimes be implemented offline, but they cannot operate as ordinary real-time causal systems.

---

# 5. Stability

For signal-processing systems, one important notion of stability is **bounded-input bounded-output (BIBO) stability**.

A system is BIBO stable if:

> Every bounded input produces a bounded output.

If:

[  
|x(t)|\leq M_x<\infty  
]

then there must exist some finite constant (M_y) such that:

[  
|y(t)|\leq M_y<\infty  
]

---

## 5.1 Example

Consider:

[  
y(t)=2x(t)  
]

If:

[  
|x(t)|\leq M  
]

then:

[  
|y(t)|=2|x(t)|\leq2M  
]

so the system is BIBO stable.

---

# 6. Memoryless and Dynamic Systems

A system is **memoryless** if the output at time (t_0) depends only on the input at that same time:

[  
y(t_0)=F[x(t_0)]  
]

Example:

[  
y(t)=3x(t)  
]

is memoryless.

But:

[  
y(t)=x(t)+x(t-1)  
]

has memory because it depends on a past input.

Similarly:

[  
y(t)=\int_{-\infty}^{t}x(\tau),d\tau  
]

has memory because it depends on an interval of previous input values.

---

# 7. Invertibility

A system is **invertible** if distinct inputs produce distinct outputs, so that the original input can be uniquely recovered from the output.

If:

[  
y(t)=\mathcal{T}{x(t)}  
]

then an inverse system (\mathcal{T}^{-1}) satisfies:

[  
\boxed{  
\mathcal{T}^{-1}{\mathcal{T}{x(t)}}=x(t)  
}  
]

Example:

[  
y(t)=2x(t)  
]

is invertible, with:

[  
x(t)=\frac{y(t)}{2}  
]

But:

[  
y(t)=x^2(t)  
]

is not generally invertible over real-valued signals because (x(t)) and (-x(t)) produce the same output.

---

# 8. Linear Time-Invariant Systems

A system that is both:

1. **Linear**, and
    
2. **Time-invariant**
    

is called an **LTI system**.

LTI systems are central to signal processing because they possess a remarkable property:

> An LTI system is completely characterized by its response to an impulse.

This response is called the **impulse response**.

---

# 9. Impulse Response

Let the input be the Dirac impulse:

[  
x(t)=\delta(t)  
]

The output of an LTI system is called:

[  
\boxed{h(t)}  
]

Therefore:

[  
\boxed{  
\delta(t)  
\rightarrow  
h(t)  
}  
]

where (h(t)) is the system's **impulse response**.

The impulse response completely characterizes the LTI system under the usual assumptions.

---

## 9.1 Why the impulse is so important

A signal can be represented using shifted impulses.

For continuous time:

[  
\boxed{  
x(t)=  
\int_{-\infty}^{+\infty}  
x(\tau)\delta(t-\tau),d\tau  
}  
]

This identity can be interpreted as decomposing the signal into infinitely many weighted and shifted impulses.

Because an LTI system knows how to respond to an impulse, linearity and time invariance allow us to determine its response to any input.

This leads directly to convolution.

---

# 10. Convolution Product

## 10.1 Continuous-time convolution

For an LTI system with impulse response (h(t)), the output is:

[  
\boxed{  
y(t)=x(t)*h(t)  
}  
]

where (*) denotes convolution.

The convolution integral is:

[  
\boxed{  
y(t)=  
\int_{-\infty}^{+\infty}  
x(\tau)h(t-\tau),d\tau  
}  
]

An equivalent expression is:

[  
\boxed{  
y(t)=  
\int_{-\infty}^{+\infty}  
h(\tau)x(t-\tau),d\tau  
}  
]

Both expressions are equivalent.

---

## 10.2 Derivation of convolution

Start with the impulse decomposition:

[  
x(t)=  
\int_{-\infty}^{+\infty}  
x(\tau)\delta(t-\tau),d\tau  
]

The system is linear, so its response to this sum/integral is the corresponding sum/integral of the responses.

The system response to:

[  
\delta(t-\tau)  
]

is, by time invariance:

[  
h(t-\tau)  
]

Therefore:

[  
y(t)=  
\int_{-\infty}^{+\infty}  
x(\tau)h(t-\tau),d\tau  
]

Thus:

[  
\boxed{  
y(t)=x(t)*h(t)  
}  
]

This is one of the most important derivations in signal processing.

---

# 11. Graphical Interpretation of Convolution

The convolution:

[  
y(t)=  
\int_{-\infty}^{+\infty}  
x(\tau)h(t-\tau),d\tau  
]

can be evaluated graphically.

The standard procedure is:

### Step 1 — Time reversal

Take:

[  
h(\tau)  
]

and reverse it:

[  
h(-\tau)  
]

### Step 2 — Shift

Replace (\tau) by (t-\tau):

[  
h(t-\tau)  
]

### Step 3 — Multiply

Calculate:

[  
x(\tau)h(t-\tau)  
]

### Step 4 — Integrate

Compute the area:

[  
y(t)=  
\int_{-\infty}^{+\infty}  
x(\tau)h(t-\tau),d\tau  
]

The value of the convolution at each (t) is therefore an **overlap integral**.

---

# 12. Properties of Convolution

Convolution has several important properties.

## 12.1 Commutativity

[  
\boxed{  
x(t)*h(t)=h(t)*x(t)  
}  
]

---

## 12.2 Associativity

x(t)*[h_1(t)*h_2(t)]  
}  
]

---

## 12.3 Distributivity

x(t)*h_1(t)+x(t)*h_2(t)  
}  
]

---

## 12.4 Identity element

The Dirac impulse acts as the identity:

[  
\boxed{  
x(t)*\delta(t)=x(t)  
}  
]

This follows directly from the sifting property.

---

# 13. Discrete-Time Convolution

For discrete-time LTI systems:

[  
\boxed{  
y[n]=x[n]*h[n]  
}  
]

where:

[  
\boxed{  
y[n]=  
\sum_{k=-\infty}^{+\infty}  
x[k]h[n-k]  
}  
]

Equivalently:

[  
\boxed{  
y[n]=  
\sum_{k=-\infty}^{+\infty}  
h[k]x[n-k]  
}  
]

The concepts are the same as continuous-time convolution, except that the integral becomes a summation.

---

## 13.1 Example

Suppose:

[  
x[n]=  
\begin{cases}  
1,&0\leq n\leq2\  
0,&\text{otherwise}  
\end{cases}  
]

and:

[  
h[n]=  
\begin{cases}  
1,&0\leq n\leq1\  
0,&\text{otherwise}  
\end{cases}  
]

Then:

[  
y[n]=x[n]*h[n]  
]

The result is:

[  
y[n]=  
\begin{cases}  
1,&n=0\  
2,&n=1\  
2,&n=2\  
1,&n=3\  
0,&\text{otherwise}  
\end{cases}  
]

The output shape results from the overlap between the two sequences.

---

# 14. Convolution and the Fourier Transform

The convolution theorem states:

[  
\boxed{  
x(t)*h(t)  
\overset{\mathcal F}{\longleftrightarrow}  
X(f)H(f)  
}  
]

Therefore, if:

[  
y(t)=x(t)*h(t)  
]

then:

[  
\boxed{  
Y(f)=X(f)H(f)  
}  
]

This is fundamental to filtering.

In the time domain:

[  
\boxed{  
y(t)=x(t)*h(t)  
}  
]

In the frequency domain:

[  
\boxed{  
Y(f)=X(f)H(f)  
}  
]

So convolution in time becomes multiplication in frequency.

---

# 15. Transfer Function / Frequency Response

The Fourier transform of the impulse response is:

[  
\boxed{  
H(f)=\mathcal{F}{h(t)}  
}  
]

This is called the **frequency response** of the LTI system.

Since:

[  
Y(f)=X(f)H(f)  
]

we can interpret (H(f)) as the factor by which the system modifies each frequency component.

If:

[  
H(f)=|H(f)|e^{j\phi_H(f)}  
]

then:

- (|H(f)|) controls amplitude,
    
- (\phi_H(f)) controls phase.
    

Therefore:

[  
|Y(f)|=|X(f)|,|H(f)|  
]

and:

\angle X(f)+\angle H(f)  
]

This gives a very powerful interpretation of filtering.

---

# 16. LTI Systems and Complex Exponentials

Complex exponentials are **eigenfunctions** of LTI systems.

Suppose:

[  
x(t)=e^{j\omega t}  
]

and the system has impulse response (h(t)).

Then:

[  
y(t)=x(t)*h(t)  
]

so:

\int_{-\infty}^{+\infty}  
h(\tau)e^{j\omega(t-\tau)},d\tau  
]

Factor out:

[  
e^{j\omega t}  
]

to obtain:

e^{j\omega t}  
\int_{-\infty}^{+\infty}  
h(\tau)e^{-j\omega\tau},d\tau  
]

Therefore:

[  
\boxed{  
y(t)=H(\omega)e^{j\omega t}  
}  
]

The input keeps exactly the same frequency; only its amplitude and phase are changed.

This is one of the deepest and most useful properties of LTI systems.

---

# 17. Differential-Equation Representation of LTI Systems

Many physical LTI systems are described by linear constant-coefficient differential equations:

b_M\frac{d^Mx(t)}{dt^M}  
+\cdots  
+b_1\frac{dx(t)}{dt}  
+b_0x(t)  
}  
]

Examples include:

- electrical RLC circuits,
    
- mechanical mass-spring-damper systems,
    
- control systems,
    
- analog filters.
    

The Fourier transform and Laplace transform provide systematic ways to analyze such equations.

---

# 18. Laplace Transform

## 18.1 Why introduce the Laplace transform?

The Fourier transform is powerful, but it does not exist in the ordinary sense for every signal.

For example, growing exponentials may not have a conventional Fourier transform.

The **Laplace transform** generalizes Fourier analysis by introducing a complex frequency variable.

The Laplace transform is particularly useful for:

- differential equations,
    
- initial conditions,
    
- system stability,
    
- transient analysis,
    
- transfer functions,
    
- pole-zero analysis,
    
- control systems.
    

---

# 19. Definition of the Bilateral Laplace Transform

The bilateral Laplace transform of (x(t)) is:

[  
\boxed{  
X(s)=  
\int_{-\infty}^{+\infty}  
x(t)e^{-st},dt  
}  
]

where:

[  
\boxed{  
s=\sigma+j\omega  
}  
]

is the complex frequency variable.

Substituting:

[  
s=\sigma+j\omega  
]

gives:

e^{-\sigma t}e^{-j\omega t}  
]

Therefore:

\int_{-\infty}^{+\infty}  
x(t)e^{-\sigma t}e^{-j\omega t},dt  
]

The factor:

[  
e^{-\sigma t}  
]

can force a signal to decay sufficiently fast for the integral to converge.

This is the key difference between Laplace and Fourier analysis.

---

# 20. Region of Convergence

The **region of convergence (ROC)** is the set of complex values (s) for which the Laplace integral converges.

The ROC is an essential part of a Laplace-transform representation.

We therefore should not think of the transform as merely:

[  
X(s)  
]

but more precisely as:

[  
\boxed{  
X(s)\quad+\quad\text{ROC}  
}  
]

Two signals can have the same algebraic expression for (X(s)) but different regions of convergence and therefore represent different time-domain signals.

---

# 21. One-Sided Laplace Transform

For causal-system analysis, the **unilateral (one-sided) Laplace transform** is often used:

[  
\boxed{  
X(s)=  
\int_{0^-}^{+\infty}  
x(t)e^{-st},dt  
}  
]

The one-sided transform is especially useful for:

- initial-value problems,
    
- differential equations,
    
- causal systems,
    
- systems with initial energy or stored conditions.
    

---

# 22. Basic Laplace Transform Pairs

## 22.1 Unit impulse

[  
\boxed{  
\delta(t)  
\longleftrightarrow  
1  
}  
]

---

## 22.2 Unit step

For:

[  
u(t)  
]

the unilateral or causal Laplace transform is:

[  
\boxed{  
u(t)  
\longleftrightarrow  
\frac{1}{s}  
}  
]

with ROC:

[  
\operatorname{Re}(s)>0  
]

for the bilateral transform of (u(t)).

---

## 22.3 Exponential

For:

[  
x(t)=e^{-at}u(t)  
]

we have:

[  
X(s)=  
\int_0^\infty  
e^{-at}e^{-st},dt  
]

so:

[  
\boxed{  
X(s)=\frac{1}{s+a}  
}  
]

with:

[  
\boxed{  
\operatorname{Re}(s)>-a  
}  
]

---

## 22.4 General causal exponential

For:

[  
x(t)=e^{at}u(t)  
]

we obtain:

[  
\boxed{  
X(s)=\frac{1}{s-a}  
}  
]

with ROC:

[  
\boxed{  
\operatorname{Re}(s)>a  
}  
]

---

# 23. Relationship Between Laplace and Fourier Transforms

Recall:

[  
s=\sigma+j\omega  
]

If we set:

[  
\sigma=0  
]

then:

[  
s=j\omega  
]

and the Laplace transform becomes:

\int_{-\infty}^{+\infty}  
x(t)e^{-j\omega t},dt  
]

which is precisely the Fourier transform, provided the (j\omega) axis lies inside the ROC.

Therefore:

\text{Laplace transform evaluated on }s=j\omega  
}  
]

when the Fourier transform exists.

This is one of the most important connections in the course.

---

# 24. Laplace Transform Properties

## 24.1 Linearity

If:

[  
x_1(t)\longleftrightarrow X_1(s)  
]

and:

[  
x_2(t)\longleftrightarrow X_2(s)  
]

then:

[  
\boxed{  
a x_1(t)+b x_2(t)  
\longleftrightarrow  
aX_1(s)+bX_2(s)  
}  
]

---

## 24.2 Time differentiation

For the bilateral transform, under suitable conditions:

[  
\boxed{  
\frac{dx(t)}{dt}  
\longleftrightarrow  
sX(s)  
}  
]

For the unilateral transform, initial conditions appear:

sX(s)-x(0^-)  
}  
]

This is one reason the unilateral Laplace transform is so useful for solving differential equations.

For the second derivative:

s^2X(s)-sx(0^-)-x'(0^-)  
}  
]

---

## 24.3 Time integration

For appropriate conditions:

\frac{X(s)}{s}  
}  
]

---

## 24.4 Time shift

A time delay:

[  
x(t-t_0)u(t-t_0)  
]

has transform:

[  
\boxed{  
e^{-st_0}X(s)  
}  
]

This property is particularly useful when analyzing delayed systems.

---

# 25. Laplace Transform of an LTI System

Suppose an LTI system has:

[  
y(t)=x(t)*h(t)  
]

Taking the Laplace transform:

[  
Y(s)=X(s)H(s)  
]

Therefore:

[  
\boxed{  
H(s)=\frac{Y(s)}{X(s)}  
}  
]

when the ratio is meaningful.

(H(s)) is the **system function** or **transfer function**.

It provides a compact description of the system in the Laplace domain.

---

# 26. Transfer Function from a Differential Equation

Consider:

[  
\frac{dy(t)}{dt}+ay(t)=bx(t)  
]

Assume zero initial conditions.

Taking the Laplace transform:

[  
sY(s)+aY(s)=bX(s)  
]

Factor:

[  
(s+a)Y(s)=bX(s)  
]

Therefore:

\frac{b}{s+a}  
}  
]

This immediately gives:

- the pole at (s=-a),
    
- the system dynamics,
    
- the frequency response by evaluating (H(j\omega)).
    

---

# 27. Poles and Zeros

A transfer function is commonly written as:

[  
\boxed{  
H(s)=  
K  
\frac{(s-z_1)(s-z_2)\cdots(s-z_M)}  
{(s-p_1)(s-p_2)\cdots(s-p_N)}  
}  
]

The values (z_i) are called **zeros**.

The values (p_i) are called **poles**.

### Zeros

A zero is a value of (s) for which:

[  
H(s)=0  
]

### Poles

A pole is a value of (s) where (H(s)) becomes unbounded, assuming no cancellation.

Poles are particularly important because they determine the natural modes of the system and strongly influence stability and transient behavior.

---

# 28. Stability and Pole Location

For a standard causal rational LTI system, BIBO stability requires all poles to lie strictly in the left half-plane:

[  
\boxed{  
\operatorname{Re}(p_i)<0  
}  
]

That is:

[  
\boxed{  
\text{stable causal rational LTI system}  
\Rightarrow  
\text{all poles in the open left half-plane}  
}  
]

Poles on the imaginary axis generally indicate marginal or non-BIBO-stable behavior for common rational-system cases.

Poles in the right half-plane indicate instability for a causal rational system.

---

# 29. Example — First-Order LTI System

Consider:

[  
\frac{dy(t)}{dt}+2y(t)=x(t)  
]

with zero initial conditions.

Taking the Laplace transform:

[  
sY(s)+2Y(s)=X(s)  
]

Therefore:

[  
(s+2)Y(s)=X(s)  
]

and:

[  
\boxed{  
H(s)=\frac{1}{s+2}  
}  
]

The pole is:

[  
\boxed{s=-2}  
]

Since:

[  
\operatorname{Re}(-2)<0  
]

the causal system is BIBO stable.

The impulse response is:

[  
\boxed{  
h(t)=e^{-2t}u(t)  
}  
]

The frequency response is obtained by setting:

[  
s=j\omega  
]

giving:

[  
\boxed{  
H(j\omega)=\frac{1}{2+j\omega}  
}  
]

Its magnitude is:

\frac{1}{\sqrt{4+\omega^2}}  
}  
]

Thus high frequencies are attenuated more strongly than low frequencies.

This system behaves as a basic **low-pass filter**.

---

# 30. Convolution vs. Laplace Transform

These two tools solve related but different problems.

### Convolution

Directly gives the output:

[  
\boxed{  
y(t)=x(t)*h(t)  
}  
]

It is particularly useful when:

- (x(t)) and (h(t)) are known,
    
- signals have simple shapes,
    
- graphical interpretation is useful.
    

### Laplace transform

Transforms differential or convolution equations into algebraic equations:

[  
\boxed{  
Y(s)=X(s)H(s)  
}  
]

It is particularly useful for:

- differential equations,
    
- initial conditions,
    
- transfer functions,
    
- poles and zeros,
    
- stability,
    
- transient analysis.
    

A powerful workflow is:

[  
\boxed{  
\text{Time-domain system}  
\rightarrow  
\text{Laplace domain}  
\rightarrow  
\text{algebra}  
\rightarrow  
\text{inverse Laplace}  
}  
]

---

# 31. Relationship Between the Main Domains

At this stage, three complementary representations are important.

## Time domain

[  
x(t)  
]

Best for understanding:

- waveform shape,
    
- delays,
    
- transients,
    
- direct physical behavior.
    

## Fourier domain

[  
X(j\omega)\quad\text{or}\quad X(f)  
]

Best for understanding:

- frequency content,
    
- bandwidth,
    
- filtering,
    
- sinusoidal steady-state behavior.
    

## Laplace domain

[  
X(s)  
]

Best for understanding:

- differential equations,
    
- transients,
    
- poles and zeros,
    
- stability,
    
- system dynamics.
    

The relationships can be summarized as:

[  
\boxed{  
x(t)  
\xrightarrow{\mathcal F}  
X(j\omega)  
}  
]

and:

[  
\boxed{  
x(t)  
\xrightarrow{\mathcal L}  
X(s)  
}  
]

with:

[  
\boxed{  
X(j\omega)=X(s)\big|_{s=j\omega}  
}  
]

when the Fourier transform exists.

---

# 32. Worked Problems

## Example 1 — Determine whether a system is LTI

Consider:

[  
y(t)=x(t-3)+2x(t)  
]

### Linearity

For:

[  
x(t)=a x_1(t)+b x_2(t)  
]

the output is:

a[x_1(t-3)+2x_1(t)]  
+  
b[x_2(t-3)+2x_2(t)]  
]

Therefore the system is linear.

### Time invariance

Shift the input:

[  
x_s(t)=x(t-t_0)  
]

Then:

x(t-t_0-3)+2x(t-t_0)  
]

But:

x(t-t_0-3)+2x(t-t_0)  
]

Therefore:

[  
y_s(t)=y(t-t_0)  
]

The system is time-invariant.

Thus:

[  
\boxed{\text{The system is LTI}}  
]

---

## Example 2 — Convolution with an impulse

Suppose:

[  
h(t)=e^{-t}u(t)  
]

and:

[  
x(t)=\delta(t-2)  
]

Then:

[  
y(t)=x(t)*h(t)  
]

Using the shifted-impulse property:

[  
\delta(t-t_0)*h(t)=h(t-t_0)  
]

we obtain:

[  
\boxed{  
y(t)=e^{-(t-2)}u(t-2)  
}  
]

This demonstrates why impulse responses are so useful.

---

## Example 3 — Convolution of two causal exponentials

Let:

[  
x(t)=e^{-at}u(t)  
]

and:

[  
h(t)=e^{-bt}u(t)  
]

with (a\neq b) and (a,b>0).

Then:

[  
y(t)=  
\int_{-\infty}^{+\infty}  
x(\tau)h(t-\tau),d\tau  
]

Because both signals are causal:

[  
0\leq\tau\leq t  
]

for (t\geq0).

Therefore:

[  
y(t)=  
\int_0^t  
e^{-a\tau}  
e^{-b(t-\tau)}  
,d\tau  
]

Factor:

e^{-bt}  
\int_0^t  
e^{-(a-b)\tau},d\tau  
]

This gives:

[  
\boxed{  
y(t)=  
\frac{e^{-at}-e^{-bt}}{b-a}u(t)  
}  
]

This result is also easily obtained using Laplace transforms.

---

## Example 4 — Solve a first-order system using Laplace transforms

Consider:

[  
\frac{dy(t)}{dt}+3y(t)=2x(t)  
]

with zero initial conditions.

Taking the Laplace transform:

[  
sY(s)+3Y(s)=2X(s)  
]

Therefore:

[  
(s+3)Y(s)=2X(s)  
]

so:

[  
\boxed{  
H(s)=\frac{2}{s+3}  
}  
]

The impulse response is:

[  
\boxed{  
h(t)=2e^{-3t}u(t)  
}  
]

The pole is:

[  
s=-3  
]

and the causal system is stable.

---

# 33. Problem-Solving Strategy

When facing an LTI-system problem, ask the following questions.

### Step 1 — Is the system linear?

Test:

[  
\mathcal T{ax_1+bx_2}  
]

### Step 2 — Is it time-invariant?

Compare:

[  
\mathcal T{x(t-t_0)}  
]

with:

[  
y(t-t_0)  
]

### Step 3 — Is the system causal?

Determine whether (y(t_0)) depends on future values:

[  
x(t),\quad t>t_0  
]

### Step 4 — Is it stable?

Check whether bounded input implies bounded output.

For an LTI system, a particularly important condition is:

[  
\boxed{  
\int_{-\infty}^{+\infty}|h(t)|dt<\infty  
}  
]

for continuous time.

For discrete time:

[  
\boxed{  
\sum_{n=-\infty}^{+\infty}|h[n]|<\infty  
}  
]

These are the standard BIBO stability conditions for LTI systems.

### Step 5 — Find the impulse response

If possible, determine:

[  
h(t)  
]

### Step 6 — Choose the most efficient domain

Use convolution if:

[  
x(t)*h(t)  
]

is simple.

Use Fourier analysis if the problem focuses on:

- steady-state frequency behavior,
    
- bandwidth,
    
- filtering.
    

Use Laplace transforms if the problem involves:

- differential equations,
    
- initial conditions,
    
- poles and zeros,
    
- transient behavior.
    

---

# 34. Common Mistakes

## Mistake 1 — Assuming linearity from appearance

A system can look simple and still be nonlinear.

For example:

[  
y(t)=x(t)+5  
]

is **not linear** because the zero input produces:

[  
y(t)=5\neq0  
]

---

## Mistake 2 — Confusing delay direction

For:

[  
x(t-t_0)  
]

the signal is delayed/right-shifted by (t_0).

---

## Mistake 3 — Forgetting time reversal in graphical convolution

The convolution:

[  
x(t)*h(t)  
]

requires:

[  
h(-\tau)  
]

before shifting to:

[  
h(t-\tau)  
]

---

## Mistake 4 — Treating (H(s)) and (H(j\omega)) as identical

They are related, but they are not the same object.

[  
H(s)  
]

is the system function in the complex (s)-plane.

[  
H(j\omega)  
]

is the frequency response obtained along the imaginary axis, when valid.

---

## Mistake 5 — Ignoring the ROC

For bilateral Laplace transforms, the algebraic expression alone is incomplete.

The ROC is part of the transform description.

---

## Mistake 6 — Using pole-location stability rules without assumptions

The simple statement:

[  
\text{all poles in the left half-plane}  
]

is the standard criterion for a **causal rational LTI system** with no problematic pole-zero cancellations.

Always keep the assumptions in mind.

---

# 35. Exercises

## Exercise 1 — System classification

Determine whether each system is:

- linear,
    
- time-invariant,
    
- causal,
    
- memoryless.
    

### (a)

[  
y(t)=4x(t)  
]

### (b)

[  
y(t)=x(t)+x(t-2)  
]

### (c)

[  
y(t)=t,x(t)  
]

### (d)

[  
y(t)=x(t+1)  
]

### (e)

[  
y(t)=x^2(t)  
]

---

## Exercise 2 — Convolution

Compute:

[  
y(t)=x(t)*h(t)  
]

for:

[  
x(t)=u(t)-u(t-2)  
]

and:

[  
h(t)=u(t)-u(t-1)  
]

Sketch the resulting output.

---

## Exercise 3 — Impulse response

An LTI system has:

[  
h(t)=e^{-2t}u(t)  
]

Find the output for:

[  
x(t)=\delta(t-3)  
]

---

## Exercise 4 — Differential equation

Consider:

[  
\frac{dy(t)}{dt}+4y(t)=x(t)  
]

with zero initial conditions.

Determine:

1. The transfer function (H(s)).
    
2. The pole location.
    
3. The impulse response.
    
4. Whether the causal system is BIBO stable.
    
5. The frequency response (H(j\omega)).
    

---

## Exercise 5 — Laplace transform

Calculate the Laplace transform and ROC of:

[  
x(t)=e^{-3t}u(t)  
]

Then calculate:

[  
\mathcal{L}\left{\frac{dx(t)}{dt}\right}  
]

using the unilateral-transform formula.

---

## Exercise 6 — Transfer function and poles

Given:

[  
H(s)=  
\frac{(s+2)}  
{(s+1)(s+4)}  
]

determine:

1. The zeros.
    
2. The poles.
    
3. Whether the causal system is stable.
    
4. The DC gain (H(0)).
    

---

# 36. Conceptual Questions

Before proceeding, you should be able to explain:

1. What is a system?
    
2. What does linearity mean mathematically?
    
3. What does time invariance mean?
    
4. What is an LTI system?
    
5. Why is the impulse response sufficient to characterize an LTI system?
    
6. Why does convolution appear naturally in LTI systems?
    
7. What does the graphical convolution procedure actually compute?
    
8. What is the relationship between (h(t)) and (H(f))?
    
9. Why are complex exponentials eigenfunctions of LTI systems?
    
10. What is the difference between (H(s)) and (H(j\omega))?
    
11. What is the region of convergence?
    
12. Why is the Laplace transform useful for differential equations?
    
13. What is a pole?
    
14. What is a zero?
    
15. How are pole locations related to stability?
    
16. Under what assumptions does the left-half-plane pole criterion apply?
    
17. When should you choose convolution, Fourier analysis, or Laplace analysis?
    

---

# 37. Essential Formula Sheet

## LTI input-output relation

[  
\boxed{  
y(t)=x(t)*h(t)  
}  
]

## Continuous-time convolution

[  
\boxed{  
y(t)=  
\int_{-\infty}^{+\infty}  
x(\tau)h(t-\tau),d\tau  
}  
]

## Discrete-time convolution

[  
\boxed{  
y[n]=  
\sum_{k=-\infty}^{+\infty}  
x[k]h[n-k]  
}  
]

## Convolution with impulse

[  
\boxed{  
x(t)*\delta(t)=x(t)  
}  
]

## Fourier-domain LTI relation

[  
\boxed{  
Y(f)=X(f)H(f)  
}  
]

## Frequency response

[  
\boxed{  
H(f)=\mathcal F{h(t)}  
}  
]

## Laplace transform

[  
\boxed{  
X(s)=  
\int_{-\infty}^{+\infty}  
x(t)e^{-st},dt  
}  
]

## Complex frequency

[  
\boxed{  
s=\sigma+j\omega  
}  
]

## Fourier/Laplace relationship

[  
\boxed{  
X(j\omega)=X(s)|_{s=j\omega}  
}  
]

when the Fourier transform exists.

## Transfer function

[  
\boxed{  
H(s)=\frac{Y(s)}{X(s)}  
}  
]

under the standard zero-initial-condition transfer-function definition.

## Differentiation — unilateral Laplace transform

sX(s)-x(0^-)  
}  
]

## Causal exponential

[  
\boxed{  
e^{-at}u(t)  
\longleftrightarrow  
\frac{1}{s+a}  
}  
]

with:

[  
\operatorname{Re}(s)>-a  
]

## BIBO stability of an LTI system

Continuous time:

[  
\boxed{  
\int_{-\infty}^{+\infty}|h(t)|dt<\infty  
}  
]

Discrete time:

[  
\boxed{  
\sum_{n=-\infty}^{+\infty}|h[n]|<\infty  
}  
]

## Standard causal rational stability criterion

[  
\boxed{  
\operatorname{Re}(p_i)<0  
\quad\forall i  
}  
]

for a causal rational LTI system under the standard assumptions.

---

# 38. Chapter Summary

The central object in this chapter is the **linear time-invariant system**.

An LTI system is simultaneously:

[  
\boxed{  
\text{Linear}  
+  
\text{Time-Invariant}  
}  
]

Its behavior is completely characterized by its impulse response:

[  
\boxed{  
h(t)  
}  
]

Once (h(t)) is known, the response to any input is:

[  
\boxed{  
y(t)=x(t)*h(t)  
}  
]

This gives the first major representation of an LTI system.

Fourier analysis provides a second:

[  
\boxed{  
Y(f)=X(f)H(f)  
}  
]

where:

[  
H(f)=\mathcal F{h(t)}  
]

The system therefore acts on each frequency component by changing its magnitude and phase.

The Laplace transform provides a third and more general representation:

[  
\boxed{  
Y(s)=X(s)H(s)  
}  
]

with:

[  
\boxed{  
H(s)=\frac{Y(s)}{X(s)}  
}  
]

and:

[  
s=\sigma+j\omega  
]

The relationship between the three viewpoints can be summarized as:

[  
\boxed{  
\begin{array}{ccc}  
\text{Time domain} & \xrightarrow{\text{Fourier}} & \text{Frequency domain}\  
x(t),h(t),y(t) & & X(j\omega),H(j\omega),Y(j\omega)  
\end{array}  
}  
]

and:

[  
\boxed{  
\begin{array}{ccc}  
\text{Time domain} & \xrightarrow{\text{Laplace}} & \text{Complex-frequency domain}\  
x(t),h(t),y(t) & & X(s),H(s),Y(s)  
\end{array}  
}  
]

The key engineering insight is:

[  
\boxed{  
\text{LTI system}  
\rightarrow  
\text{Impulse response}  
\rightarrow  
\text{Convolution}  
\rightarrow  
\text{Fourier/Laplace representation}  
\rightarrow  
\text{System analysis}  
}  
]

Once this chain is understood, filtering, transfer functions, poles and zeros, stability, analog filters, and eventually digital filters become much easier to understand.

---

# 39. Mastery Checklist

Before considering Chapter 3 mastered, you should be able to:

- Define a system mathematically.
    
- Test a system for linearity.
    
- Test a system for time invariance.
    
- Determine whether a system is causal.
    
- Explain memoryless vs. dynamic behavior.
    
- Explain BIBO stability.
    
- Define an LTI system.
    
- Explain why the impulse response characterizes an LTI system.
    
- Derive the convolution integral.
    
- Compute simple continuous-time convolutions.
    
- Compute simple discrete-time convolutions.
    
- Explain convolution graphically.
    
- State the main properties of convolution.
    
- Relate (h(t)) to (H(f)).
    
- Explain why complex exponentials are eigenfunctions of LTI systems.
    
- Define the Laplace transform.
    
- Explain the meaning of (s=\sigma+j\omega).
    
- Explain the region of convergence.
    
- Distinguish bilateral and unilateral Laplace transforms.
    
- Derive a transfer function from a differential equation.
    
- Identify poles and zeros.
    
- Explain the relationship between pole locations and stability.
    
- Obtain the frequency response from (H(s)) using (s=j\omega).
    
- Know when convolution, Fourier analysis, or Laplace analysis is the most efficient tool.
    

The core conceptual chain to remember is:

[  
\boxed{  
x(t)  
\xrightarrow{\text{LTI system}}  
y(t)  
}  
]

[  
\boxed{  
y(t)=x(t)*h(t)  
}  
]

[  
\boxed{  
Y(f)=X(f)H(f)  
}  
]

[  
\boxed{  
Y(s)=X(s)H(s)  
}  
]

These four equations form a major foundation of continuous-time signal and system analysis.