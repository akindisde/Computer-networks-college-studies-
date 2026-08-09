# Chapter 1 — Generalities on Signals

## 1. Introduction

### 1.1 What is signal processing?

**Signal processing** is the study of mathematical methods used to represent, analyze, transform, transmit, store, and extract information from signals.

A signal is a physical or mathematical quantity that varies with one or more independent variables and carries information about a phenomenon or system.

Examples include:

- An electrical voltage measured by a sensor.
    
- An audio waveform recorded by a microphone.
    
- An image represented as a function of two spatial coordinates.
    
- Temperature measured over time.
    
- The output of a radar or communication system.
    
- Biomedical measurements such as ECG signals.
    

Signal processing provides the mathematical tools required to answer questions such as:

- What information does a signal contain?
    
- Is the signal periodic or non-periodic?
    
- What frequencies are present?
    
- How can noise be removed?
    
- How can a signal be sampled and reconstructed?
    
- How can a system modify a signal in a controlled way?
    

The fundamental object of study is therefore the **signal**, usually represented mathematically as a function.

---

### 1.2 Signals and systems

Signal processing is closely related to the study of **systems**.

A system is an operation or physical device that transforms an input signal into an output signal:

[  
x(t) \longrightarrow \boxed{\text{System}} \longrightarrow y(t)  
]

where:

- (x(t)) is the **input signal**,
    
- (y(t)) is the **output signal**.
    

A simple example is an amplifier:

[  
y(t)=A x(t)  
]

where (A) is the amplification factor.

Another example is a moving-average filter:

[  
y(t)=\frac{1}{3}\left[x(t)+x(t-T)+x(t-2T)\right]  
]

The purpose of signal processing is often to design or analyze such transformations.

---

### 1.3 Continuous-time and discrete-time viewpoints

Signals can be classified according to their independent variable.

A **continuous-time signal** is defined for every value of time:

[  
x(t), \qquad t\in\mathbb{R}  
]

A **discrete-time signal** is defined only at discrete instants:

[  
x[n], \qquad n\in\mathbb{Z}  
]

The distinction is fundamental in digital signal processing.

For example, a continuously measured temperature can be modeled as:

[  
x(t)  
]

while measurements taken every second can be represented by:

[  
x[n]=x(nT_s)  
]

where (T_s) is the sampling period.

---

# 2. Definitions

## 2.1 Mathematical definition of a signal

A signal can be modeled as a function:

[  
x:\mathcal{D}\rightarrow\mathcal{A}  
]

where:

- (\mathcal{D}) is the **domain**, representing the independent variable(s),
    
- (\mathcal{A}) is the **amplitude set** or range.
    

For a time signal:

[  
x(t):\mathbb{R}\rightarrow\mathbb{R}  
]

or, for a complex-valued signal:

[  
x(t):\mathbb{R}\rightarrow\mathbb{C}  
]

The independent variable does not have to be time. For example:

- (x(t)): temporal signal,
    
- (I(x,y)): image,
    
- (p(x,y,z)): spatial distribution,
    
- (x[n]): discrete-time sequence.
    

Thus, a signal is not necessarily an electrical waveform.

---

## 2.2 Independent variable

The **independent variable** describes where or when the signal is evaluated.

For temporal signals, it is usually time:

[  
t \quad \text{or} \quad n  
]

For spatial signals, it can be a spatial coordinate:

[  
x,; y,; z  
]

For a discrete-time signal, the independent variable is conventionally an integer index:

[  
n\in\mathbb{Z}  
]

This distinction is important:

[  
x(t) \neq x[n]  
]

The first is a continuous-time signal, while the second is a discrete-time sequence.

---

## 2.3 Amplitude

The **amplitude** is the value taken by the signal at a particular point in its domain.

For a signal (x(t)), the value:

[  
x(t_0)  
]

is its amplitude at time (t_0).

The amplitude may be:

- positive,
    
- negative,
    
- zero,
    
- real-valued,
    
- complex-valued.
    

For example:

[  
x(t)=A\cos(\omega_0t+\phi)  
]

has a maximum magnitude determined by (A).

---

## 2.4 Signal energy

For a continuous-time signal, the **energy** is defined as:

[  
E_x=\int_{-\infty}^{+\infty}|x(t)|^2,dt  
]

For a discrete-time signal:

[  
E_x=\sum_{n=-\infty}^{+\infty}|x[n]|^2  
]

A signal is called an **energy signal** if:

[  
0<E_x<\infty  
]

The use of (|x(t)|^2), rather than (x^2(t)), allows the definition to apply to complex-valued signals as well.

---

## 2.5 Signal power

The average power of a continuous-time signal is:

[  
P_x=  
\lim_{T\rightarrow\infty}  
\frac{1}{2T}  
\int_{-T}^{T}|x(t)|^2,dt  
]

For a discrete-time signal:

[  
P_x=  
\lim_{N\rightarrow\infty}  
\frac{1}{2N+1}  
\sum_{n=-N}^{N}|x[n]|^2  
]

A signal is called a **power signal** if:

[  
0<P_x<\infty  
]

Energy and power classifications are developed in detail later, but they are useful from the beginning because they quantify the magnitude of signals.

---

## 2.6 Period

A signal (x(t)) is **periodic** if there exists a positive number (T_0) such that:

[  
x(t+T_0)=x(t)  
]

for every (t).

The smallest positive value satisfying this condition is called the **fundamental period**.

For a discrete-time signal:

[  
x[n+N_0]=x[n]  
]

where (N_0) is a positive integer.

### Example

Consider:

[  
x(t)=\cos(2\pi f_0t)  
]

Its fundamental period is:

[  
T_0=\frac{1}{f_0}  
]

The corresponding angular frequency is:

[  
\omega_0=2\pi f_0  
]

and therefore:

[  
T_0=\frac{2\pi}{\omega_0}  
]

---

## 2.7 Frequency

The frequency of a periodic signal represents the number of cycles occurring per unit time.

It is measured in **hertz (Hz)**:

[  
f_0=\frac{1}{T_0}  
]

where:

- (f_0) is frequency in Hz,
    
- (T_0) is period in seconds.
    

The corresponding angular frequency is:

[  
\omega_0=2\pi f_0  
]

and is measured in radians per second.

Therefore:

[  
\boxed{\omega_0=2\pi f_0}  
]

---

## 2.8 Phase

A sinusoidal signal can be written as:

[  
x(t)=A\cos(\omega_0t+\phi)  
]

where (\phi) is the **phase**.

The phase determines the horizontal displacement of the sinusoid.

Using:

\cos\left[\omega_0\left(t+\frac{\phi}{\omega_0}\right)\right]  
]

we see that phase is related to a time shift.

A positive phase (\phi) corresponds to a shift toward the left when the signal is expressed in this form.

---

# 3. Classification of Signals

Signals can be classified according to several different criteria. These classifications are not mutually exclusive.

A signal can, for example, be:

> continuous-time, deterministic, periodic, even, real-valued, and an energy signal.

Each classification describes a different property.

---

## 3.1 Continuous-time and discrete-time signals

### Continuous-time signals

A continuous-time signal is defined for every real value of time:

[  
x(t),\qquad t\in\mathbb{R}  
]

Example:

[  
x(t)=\sin(2\pi t)  
]

The notation (t) emphasizes the continuous independent variable.

---

### Discrete-time signals

A discrete-time signal is defined only at discrete values of its independent variable:

[  
x[n],\qquad n\in\mathbb{Z}  
]

Example:

[  
x[n]=\sin\left(\frac{\pi}{4}n\right)  
]

A discrete-time signal is often represented graphically using individual points or stems.

---

### Important distinction: discrete-time vs digital

These concepts must not be confused.

A **discrete-time signal** has discrete time but can still have continuous amplitude values.

A **digital signal** is discrete in both:

1. time,
    
2. amplitude.
    

For example, an ADC converts an analog signal into samples and then quantizes their amplitudes.

A simplified chain is:

[  
\text{Analog signal}  
\rightarrow  
\text{Sampling}  
\rightarrow  
\text{Quantization}  
\rightarrow  
\text{Digital signal}  
]

---

## 3.2 Analog and digital signals

An **analog signal** generally has a continuous independent variable and continuous amplitude.

A **digital signal** has discrete values of both the independent variable and amplitude.

For example, an ADC may transform:

[  
x(t)  
]

into a sequence:

[  
x_q[n]  
]

where each (x_q[n]) belongs to a finite set of quantization levels.

Digital signal processing works primarily with discrete-time signals, usually obtained by sampling analog signals.

---

## 3.3 Deterministic and random signals

### Deterministic signals

A deterministic signal is completely specified mathematically.

For example:

[  
x(t)=3\cos(100\pi t)  
]

At any time (t), the value of the signal can be determined exactly from its equation.

---

### Random signals

A random or stochastic signal cannot be predicted exactly at every instant.

Instead, it is described statistically using quantities such as:

- mean,
    
- variance,
    
- probability density,
    
- autocorrelation,
    
- power spectral density.
    

Examples include:

- thermal noise,
    
- communication channel noise,
    
- measurement noise.
    

Random signals are fundamental in practical signal processing because real measurements almost always contain noise.

---

## 3.4 Periodic and aperiodic signals

### Periodic continuous-time signals

A continuous-time signal is periodic if:

[  
x(t+T_0)=x(t)  
]

for some (T_0>0).

Example:

[  
x(t)=\cos(2\pi f_0t)  
]

---

### Periodic discrete-time signals

A discrete-time signal is periodic if:

[  
x[n+N_0]=x[n]  
]

for some positive integer (N_0).

For a discrete-time sinusoid:

[  
x[n]=\cos(\omega_0n+\phi)  
]

the signal is periodic if there exists a positive integer (N_0) such that:

[  
\omega_0N_0=2\pi k  
]

for some integer (k).

Therefore:

[  
\boxed{\frac{\omega_0}{2\pi}\in\mathbb{Q}}  
]

must be rational.

### Important consequence

A discrete-time sinusoid is **not necessarily periodic**.

For example:

[  
x[n]=\cos(\sqrt{2}\pi n)  
]

is not periodic because:

[  
\frac{\sqrt{2}\pi}{2\pi}=\frac{\sqrt{2}}{2}  
]

is irrational.

This is a major difference between continuous-time and discrete-time sinusoids.

---

## 3.5 Even and odd signals

This classification is based on symmetry.

### Even signal

A signal is **even** if:

[  
x(-t)=x(t)  
]

for continuous time, or:

[  
x[-n]=x[n]  
]

for discrete time.

An even signal is symmetric around the vertical axis.

Example:

[  
x(t)=\cos(t)  
]

because:

[  
\cos(-t)=\cos(t)  
]

---

### Odd signal

A signal is **odd** if:

[  
x(-t)=-x(t)  
]

or, for discrete time:

[  
x[-n]=-x[n]  
]

Example:

[  
x(t)=\sin(t)  
]

because:

[  
\sin(-t)=-\sin(t)  
]

An odd signal necessarily satisfies:

[  
x(0)=0  
]

when (t=0) belongs to its domain.

---

### Decomposition into even and odd parts

Any signal (x(t)) can be decomposed into an even part and an odd part:

[  
x(t)=x_e(t)+x_o(t)  
]

where:

[  
\boxed{  
x_e(t)=\frac{x(t)+x(-t)}{2}  
}  
]

and:

[  
\boxed{  
x_o(t)=\frac{x(t)-x(-t)}{2}  
}  
]

The same formulas apply to discrete-time signals by replacing (t) with (n).

This decomposition is extremely useful in Fourier analysis.

---

## 3.6 Energy and power signals

Signals can also be classified according to their energy and average power.

### Energy signal

[  
0<E_x<\infty  
]

Typically, finite-duration signals are energy signals.

Example:

[  
x(t)=e^{-at}u(t),\qquad a>0  
]

Its energy is finite:

[  
E_x=\int_0^\infty e^{-2at},dt  
=\frac{1}{2a}  
]

---

### Power signal

A power signal satisfies:

[  
0<P_x<\infty  
]

Periodic signals with finite nonzero amplitude are generally power signals.

For example:

[  
x(t)=A\cos(\omega_0t)  
]

has average power:

[  
\boxed{P_x=\frac{A^2}{2}}  
]

---

### Can a signal be both?

For a nonzero signal, it cannot have both finite nonzero energy and finite nonzero average power.

In standard signal classification:

- energy signal: finite energy and zero average power,
    
- power signal: infinite energy and finite nonzero average power.
    

The zero signal is a special case.

---

## 3.7 Causal and non-causal signals

A signal is **causal** if it is zero before the reference origin:

[  
x(t)=0,\qquad t<0  
]

For discrete time:

[  
x[n]=0,\qquad n<0  
]

Example:

[  
x(t)=e^{-t}u(t)  
]

is causal.

A signal that has nonzero values for (t<0) is non-causal.

### Important note

Causality is especially important for **systems**, but it can also be used to describe signals.

In physical real-time systems, causal behavior is essential because a system cannot respond to an input that has not occurred yet.

---

## 3.8 Real and complex signals

A signal is **real-valued** if:

[  
x(t)\in\mathbb{R}  
]

A signal is **complex-valued** if:

[  
x(t)\in\mathbb{C}  
]

A complex signal can be expressed as:

[  
x(t)=x_R(t)+jx_I(t)  
]

where:

- (x_R(t)) is the real part,
    
- (x_I(t)) is the imaginary part,
    
- (j=\sqrt{-1}).
    

Complex signals are especially important in:

- Fourier analysis,
    
- communications,
    
- modulation,
    
- control,
    
- radar,
    
- digital signal processing.
    

---

## 3.9 Finite-duration and infinite-duration signals

A signal is **finite-duration** if it is nonzero only over a finite interval.

For example:

[  
x(t)=  
\begin{cases}  
1,&0\leq t\leq T\  
0,&\text{otherwise}  
\end{cases}  
]

This is a finite-duration rectangular pulse.

A signal such as:

[  
x(t)=e^{-t}u(t)  
]

has infinite duration because it remains nonzero for all (t\geq0), even though its amplitude decreases toward zero.

---

## 3.10 Summary of signal classifications

A signal can be classified along multiple dimensions:

|Property|Possible classifications|
|---|---|
|Independent variable|Continuous-time / Discrete-time|
|Amplitude|Continuous / Quantized|
|Predictability|Deterministic / Random|
|Repetition|Periodic / Aperiodic|
|Symmetry|Even / Odd / Neither|
|Energy behavior|Energy / Power|
|Time support|Finite-duration / Infinite-duration|
|Time direction|Causal / Non-causal|
|Values|Real / Complex|

These classifications are independent in the sense that one does not generally determine another.

---

# 4. Common Signals

A small number of standard signals appear repeatedly throughout signal processing. Learning their mathematical forms and properties is essential.

---

## 4.1 Unit impulse

The **Dirac delta** is a continuous-time generalized signal denoted by:

[  
\delta(t)  
]

It is characterized by:

[  
\delta(t)=0,\qquad t\neq0  
]

and:

[  
\int_{-\infty}^{+\infty}\delta(t),dt=1  
]

Its most important property is the **sifting property**:

[  
\boxed{  
\int_{-\infty}^{+\infty}x(t)\delta(t-t_0),dt=x(t_0)  
}  
]

The delta is not an ordinary function in the classical sense; it is a distribution/generalized function.

It is fundamental in:

- convolution,
    
- system characterization,
    
- sampling theory,
    
- Fourier analysis.
    

---

### Discrete-time unit impulse

The discrete-time counterpart is:

[  
\delta[n]=  
\begin{cases}  
1,&n=0\  
0,&n\neq0  
\end{cases}  
]

It satisfies:

[  
\sum_{n=-\infty}^{+\infty}x[n]\delta[n-n_0]  
=x[n_0]  
]

---

## 4.2 Unit step

The continuous-time unit step is defined as:

[  
u(t)=  
\begin{cases}  
1,&t>0\  
0,&t<0  
\end{cases}  
]

The value at (t=0) depends on the convention being used. A common convention is:

[  
u(0)=\frac12  
]

but this value usually does not affect ordinary integration results.

The discrete-time unit step is:

[  
u[n]=  
\begin{cases}  
1,&n\geq0\  
0,&n<0  
\end{cases}  
]

---

### Relationship between impulse and step

In the generalized-function sense:

[  
\boxed{  
\delta(t)=\frac{d}{dt}u(t)  
}  
]

and:

[  
\boxed{  
u(t)=\int_{-\infty}^{t}\delta(\tau),d\tau  
}  
]

For discrete time:

[  
\boxed{  
\delta[n]=u[n]-u[n-1]  
}  
]

---

## 4.3 Ramp signal

The continuous-time unit ramp is:

[  
r(t)=tu(t)  
]

or explicitly:

[  
r(t)=  
\begin{cases}  
t,&t\geq0\  
0,&t<0  
\end{cases}  
]

Its derivative is:

[  
\frac{d}{dt}r(t)=u(t)  
]

The ramp is useful for modeling linearly increasing quantities and for analyzing systems.

---

## 4.4 Rectangular pulse

A rectangular pulse can be represented as:

[  
x(t)=  
\begin{cases}  
A,&|t|<\frac{T}{2}\  
0,&|t|>\frac{T}{2}  
\end{cases}  
]

where:

- (A) is the amplitude,
    
- (T) is the pulse width.
    

It can also be written using unit steps:

u\left(t-\frac{T}{2}\right)  
\right]  
}  
]

Rectangular pulses are extremely important in:

- pulse modulation,
    
- digital communications,
    
- sampling,
    
- Fourier analysis.
    

---

## 4.5 Exponential signal

A continuous-time exponential signal has the form:

[  
x(t)=Ae^{at}  
]

where (A) and (a) may be real or complex.

For (a<0):

[  
x(t)=Ae^{at}  
]

decays as (t\rightarrow+\infty).

For (a>0), it grows exponentially.

A causal decaying exponential is often written:

[  
x(t)=Ae^{-at}u(t),\qquad a>0  
]

---

### Complex exponential

A complex exponential is:

[  
x(t)=Ae^{(\sigma+j\omega)t}  
]

which can be separated as:

[  
x(t)=Ae^{\sigma t}e^{j\omega t}  
]

Using Euler's formula:

\cos(\omega t)+j\sin(\omega t)  
]

Therefore:

e^{\sigma t}  
\left[  
\cos(\omega t)+j\sin(\omega t)  
\right]  
]

This relationship is central to Fourier and Laplace analysis.

---

## 4.6 Sinusoidal signals

The general continuous-time sinusoid is:

[  
\boxed{  
x(t)=A\cos(\omega_0t+\phi)  
}  
]

where:

- (A): amplitude,
    
- (\omega_0): angular frequency,
    
- (\phi): phase.
    

The frequency in hertz is:

[  
f_0=\frac{\omega_0}{2\pi}  
]

and the period is:

[  
T_0=\frac{2\pi}{\omega_0}  
]

---

### Sine and cosine

The sine signal is:

[  
x(t)=A\sin(\omega_0t+\phi)  
]

The cosine and sine are related by:

[  
\sin(\theta)=\cos\left(\theta-\frac{\pi}{2}\right)  
]

so either can be used as the fundamental sinusoidal model.

---

## 4.7 Signum signal

The signum function is:

[  
\operatorname{sgn}(t)=  
\begin{cases}  
1,&t>0\  
0,&t=0\  
-1,&t<0  
\end{cases}  
]

It is related to the unit step by:

[  
\boxed{  
\operatorname{sgn}(t)=2u(t)-1  
}  
]

under the usual convention away from (t=0).

The signum signal is an odd signal:

[  
\operatorname{sgn}(-t)=-\operatorname{sgn}(t)  
]

---

## 4.8 sinc signal

The normalized sinc function is commonly defined as:

[  
\boxed{  
\operatorname{sinc}(t)=  
\frac{\sin(\pi t)}{\pi t}  
}  
]

with:

[  
\operatorname{sinc}(0)=1  
]

by continuity.

Another convention is:

[  
\operatorname{sinc}(t)=\frac{\sin t}{t}  
]

Therefore, always check which convention is being used.

The sinc function is fundamental in:

- sampling theory,
    
- interpolation,
    
- Fourier transforms,
    
- ideal low-pass filtering.
    

---

# 5. Signal Transformations

Before moving to systems and Fourier analysis, it is essential to understand how signals behave under basic transformations.

---

## 5.1 Time shifting

Given:

[  
y(t)=x(t-t_0)  
]

the signal is shifted **to the right** by (t_0) if (t_0>0).

Similarly:

[  
y(t)=x(t+t_0)  
]

shifts the signal **to the left** by (t_0).

### Rule

[  
\boxed{x(t-t_0)\Rightarrow \text{delay by }t_0}  
]

[  
\boxed{x(t+t_0)\Rightarrow \text{advance by }t_0}  
]

This sign convention is one of the most common sources of errors for beginners.

---

## 5.2 Time reversal

The transformation:

[  
y(t)=x(-t)  
]

reflects the signal around the vertical axis.

It is called **time reversal**.

For discrete time:

[  
y[n]=x[-n]  
]

---

## 5.3 Time scaling

Consider:

[  
y(t)=x(at)  
]

### If (a>1)

The signal is compressed in time.

### If (0<a<1)

The signal is expanded in time.

For example:

[  
y(t)=x(2t)  
]

compresses the signal by a factor of 2.

Whereas:

[  
y(t)=x\left(\frac{t}{2}\right)  
]

expands it by a factor of 2.

---

## 5.4 Amplitude scaling

For:

[  
y(t)=Ax(t)  
]

the signal amplitude is multiplied by (A).

If (A>1), the amplitude is increased.

If:

[  
0<A<1  
]

the amplitude is reduced.

If (A<0), the signal is also inverted.

---

# 6. Worked Examples

## Example 1 — Classifying a sinusoid

Consider:

[  
x(t)=3\cos(20\pi t+\frac{\pi}{4})  
]

### Step 1 — Amplitude

[  
A=3  
]

### Step 2 — Angular frequency

[  
\omega_0=20\pi\ \text{rad/s}  
]

### Step 3 — Frequency

[  
f_0=\frac{\omega_0}{2\pi}  
=\frac{20\pi}{2\pi}  
=10\text{ Hz}  
]

### Step 4 — Period

[  
T_0=\frac{1}{f_0}=0.1\text{ s}  
]

### Step 5 — Classification

The signal is:

- continuous-time,
    
- deterministic,
    
- periodic,
    
- real-valued,
    
- even neither generally nor odd,
    
- a power signal.
    

---

## Example 2 — Even and odd decomposition

Consider:

[  
x(t)=e^t  
]

We calculate:

[  
x(-t)=e^{-t}  
]

Therefore:

[  
x_e(t)=\frac{e^t+e^{-t}}{2}  
]

Using the definition of hyperbolic cosine:

[  
\boxed{x_e(t)=\cosh(t)}  
]

Similarly:

[  
x_o(t)=\frac{e^t-e^{-t}}{2}  
]

so:

[  
\boxed{x_o(t)=\sinh(t)}  
]

Thus:

[  
e^t=\cosh(t)+\sinh(t)  
]

This illustrates that every signal can be decomposed into an even and an odd component.

---

## Example 3 — Discrete-time periodicity

Consider:

[  
x[n]=\cos\left(\frac{\pi}{4}n\right)  
]

We need the smallest positive integer (N_0) satisfying:

[  
\frac{\pi}{4}N_0=2\pi k  
]

Dividing by (\pi):

[  
\frac{N_0}{4}=2k  
]

so:

[  
N_0=8k  
]

The smallest positive value occurs for (k=1):

[  
\boxed{N_0=8}  
]

Therefore, the fundamental period is 8 samples.

---

## Example 4 — Time transformation

Suppose:

[  
x(t)=  
\begin{cases}  
1,&0\leq t\leq2\  
0,&\text{otherwise}  
\end{cases}  
]

Consider:

[  
y(t)=x(2t-2)  
]

We determine when the argument of (x) is between 0 and 2:

[  
0\leq2t-2\leq2  
]

Adding 2:

[  
2\leq2t\leq4  
]

Dividing by 2:

[  
1\leq t\leq2  
]

Therefore:

[  
y(t)=  
\begin{cases}  
1,&1\leq t\leq2\  
0,&\text{otherwise}  
\end{cases}  
]

The original pulse has been compressed by a factor of 2 and shifted to the right.

---

# 7. Key Concepts to Remember

The following concepts form the foundation of signal processing:

### 1. A signal carries information

Mathematically, it is modeled as a function of one or more independent variables.

### 2. Continuous-time and discrete-time are different

[  
x(t)\quad\text{vs.}\quad x[n]  
]

A discrete-time signal is not necessarily digital.

### 3. Periodicity depends on the domain

Continuous-time:

[  
x(t+T_0)=x(t)  
]

Discrete-time:

[  
x[n+N_0]=x[n]  
]

A discrete-time sinusoid is periodic only when its normalized frequency is rational.

### 4. Energy and power are different measures

[  
E_x=\int_{-\infty}^{\infty}|x(t)|^2dt  
]

[  
P_x=\lim_{T\to\infty}\frac{1}{2T}  
\int_{-T}^{T}|x(t)|^2dt  
]

### 5. Symmetry is powerful

Any signal can be decomposed as:

[  
x(t)=x_e(t)+x_o(t)  
]

with:

[  
x_e(t)=\frac{x(t)+x(-t)}{2}  
]

and:

[  
x_o(t)=\frac{x(t)-x(-t)}{2}  
]

### 6. Standard signals are the vocabulary of signal processing

You should be comfortable with:

[  
\delta(t),\quad u(t),\quad r(t),\quad  
e^{at},\quad \cos(\omega t+\phi),\quad  
\operatorname{sgn}(t),\quad \operatorname{sinc}(t)  
]

These signals will repeatedly appear in convolution, Fourier transforms, Laplace transforms, sampling, filtering, and system analysis.

---

# 8. Self-Assessment Exercises

## Exercise 1 — Classification

Classify the following signal according to as many properties as possible:

[  
x(t)=5e^{-2t}u(t)  
]

Determine whether it is:

1. Continuous-time or discrete-time.
    
2. Deterministic or random.
    
3. Periodic or aperiodic.
    
4. Causal or non-causal.
    
5. Real or complex.
    
6. An energy signal or power signal.
    

---

## Exercise 2 — Sinusoid parameters

For:

[  
x(t)=7\sin(100\pi t-\frac{\pi}{3})  
]

find:

1. Amplitude.
    
2. Angular frequency.
    
3. Frequency.
    
4. Period.
    
5. Phase.
    

---

## Exercise 3 — Discrete-time periodicity

Determine whether:

[  
x[n]=\cos\left(\frac{3\pi}{5}n\right)  
]

is periodic. If it is, determine its fundamental period.

---

## Exercise 4 — Even/odd decomposition

Find the even and odd components of:

[  
x(t)=t+t^2  
]

---

## Exercise 5 — Signal transformation

Let:

[  
x(t)=  
\begin{cases}  
1,&-1\leq t\leq1\  
0,&\text{otherwise}  
\end{cases}  
]

Sketch or describe:

[  
y(t)=x(2t-4)  
]

Determine the interval over which (y(t)=1).

---

# 9. Conceptual Questions

Before continuing to the next chapter, you should be able to answer these questions without relying on memorized formulas:

1. What makes a signal continuous-time?
    
2. What is the difference between a discrete-time signal and a digital signal?
    
3. What is the difference between a deterministic signal and a random signal?
    
4. How do you test whether a signal is periodic?
    
5. Why is a discrete-time sinusoid not always periodic?
    
6. What is the difference between signal energy and signal power?
    
7. How do you determine whether a signal is even or odd?
    
8. How can any signal be decomposed into even and odd components?
    
9. What is the role of the unit impulse?
    
10. What is the relationship between the unit impulse and unit step?
    
11. What happens to (x(t)) when replacing (t) by (t-t_0)?
    
12. What is the difference between time scaling and amplitude scaling?
    

---

# 10. Chapter Summary

Signal processing begins with the mathematical description of signals.

A signal may be continuous-time or discrete-time, deterministic or random, periodic or aperiodic, even or odd, causal or non-causal, real or complex, and an energy or power signal.

The most important standard signals include:

[  
\boxed{  
\delta(t),;  
u(t),;  
r(t),;  
e^{at},;  
\cos(\omega t+\phi),;  
\operatorname{sgn}(t),;  
\operatorname{sinc}(t)  
}  
]

Understanding these signals and their transformations is not merely introductory material. They constitute the basic vocabulary used throughout signal processing.

The next stages of the subject build directly on these concepts:

[  
\boxed{  
\text{Signals}  
\rightarrow  
\text{Systems}  
\rightarrow  
\text{Convolution}  
\rightarrow  
\text{Fourier Analysis}  
\rightarrow  
\text{Sampling}  
\rightarrow  
\text{Digital Signal Processing}  
}  
]

A strong command of the definitions and classifications introduced in this chapter will make the mathematical development of the following chapters substantially easier.