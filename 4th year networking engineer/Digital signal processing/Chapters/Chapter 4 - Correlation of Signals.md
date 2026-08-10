# Chapter 4 — Correlation of Signals

## 1. Introduction

Correlation is one of the fundamental tools of signal processing.

While convolution describes how an LTI system produces an output from an input, **correlation measures similarity between signals as a function of relative displacement or delay**.

Correlation is used to answer questions such as:

- How similar are two signals?
    
- At what delay are two signals most similar?
    
- Does a signal contain a delayed copy of another signal?
    
- Is a periodic signal present?
    
- What information about a signal can be extracted from its statistical or deterministic structure?
    

Correlation is central to:

- signal detection,
    
- synchronization,
    
- radar and sonar,
    
- communications,
    
- pattern recognition,
    
- time-delay estimation,
    
- system identification,
    
- spectral analysis,
    
- noise analysis.
    

The key idea is:

[  
\boxed{\text{Correlation measures similarity versus time shift.}}  
]

---

# 2. Correlation Concepts

## 2.1 Similarity between signals

Suppose we have two signals:

[  
x(t)  
]

and:

[  
y(t)  
]

We may want to determine whether (y(t)) resembles a delayed version of (x(t)).

For example:

[  
y(t)\approx x(t-\tau_0)  
]

If this is true, a correlation measure should become large around:

[  
\tau=\tau_0  
]

Therefore, correlation can be interpreted as a **delay-dependent similarity measure**.

---

# 3. Cross-Correlation

The **cross-correlation function** between two continuous-time signals (x(t)) and (y(t)) is commonly defined as:
$$
{
\int_{-\infty}^{+\infty}  
x(t)y^*(t-\tau)dt  
}  
$$

where:

- ($\tau$) is the relative delay,
    
- ($^*$) denotes complex conjugation.
    

For real-valued signals:

$$
y^*(t-\tau)=y(t-\tau)  
$$

and therefore:

\int_{-\infty}^{+\infty}  
x(t)y(t-\tau),dt  
}  
]

The precise sign convention for the delay varies between textbooks. What matters is using one convention consistently.

---

## 3.1 Interpretation

At a given value of (\tau):

1. Shift one signal relative to the other.
    
2. Multiply the overlapping samples.
    
3. Integrate the product.
    

A large positive value means that the signals tend to have similar signed variations at that relative shift.

A large negative value indicates opposite behavior.

A value near zero indicates weak similarity under that shift.

Thus:

[  
\boxed{  
R_{xy}(\tau)  
\text{ measures similarity as a function of delay.}  
}  
]

---

# 4. Autocorrelation

When the two signals are the same:

[  
y(t)=x(t)  
]

the cross-correlation becomes the **autocorrelation function**.

For a complex signal:

\int_{-\infty}^{+\infty}  
x(t)x^*(t-\tau),dt  
}  
]

For a real signal:

\int_{-\infty}^{+\infty}  
x(t)x(t-\tau),dt  
}  
]

Autocorrelation measures how similar a signal is to a delayed version of itself.

---

## 4.1 Physical interpretation

If a signal is strongly correlated with a delayed copy of itself, then:

[  
R_{xx}(\tau)  
]

will have a large value at that delay.

This makes autocorrelation useful for identifying:

- periodicity,
    
- repeated patterns,
    
- characteristic time scales,
    
- delays,
    
- signal structure hidden by noise.
    

---

# 5. Correlation as an Inner Product

Correlation can be understood using the concept of an inner product.

At a fixed delay (\tau):

\int x(t)y^*(t-\tau),dt  
]

This is an inner product between:

[  
x(t)  
]

and the shifted version:

[  
y(t-\tau)  
]

Therefore, correlation can be viewed as:

\text{inner product after relative shifting}  
}  
]

This interpretation is particularly useful for understanding matched filtering and detection.

---

# 6. Normalized Correlation

The magnitude of a correlation depends not only on similarity but also on signal energy.

To compare similarity independently of overall signal amplitude, a normalized correlation coefficient can be used.

For deterministic finite-energy signals, one common normalized form is:

\frac{  
R_{xy}(\tau)  
}{  
\sqrt{R_{xx}(0)R_{yy}(0)}  
}  
}  
]

For real signals under appropriate conditions:

[  
\boxed{  
|\rho_{xy}(\tau)|\leq1  
}  
]

A value near:

[  
+1  
]

indicates strong similarity.

A value near:

[  
-1  
]

indicates strong opposition.

A value near:

[  
0  
]

indicates weak linear similarity at that delay.

---

# 7. Energy and the Value of Autocorrelation at Zero

Set:

[  
\tau=0  
]

in the autocorrelation:

\int_{-\infty}^{+\infty}  
x(t)x^*(t),dt  
]

Since:

[  
x(t)x^*(t)=|x(t)|^2  
]

we obtain:

\int_{-\infty}^{+\infty}|x(t)|^2dt  
}  
]

This is the signal energy:

[  
\boxed{  
E_x=R_{xx}(0)  
}  
]

Therefore, the maximum self-similarity of a finite-energy signal occurs at zero delay, and its autocorrelation at zero gives the total energy.

---

# 8. Discrete-Time Correlation

For discrete-time signals, the cross-correlation is:

\sum_{n=-\infty}^{+\infty}  
x[n]y^*[n-k]  
}  
]

For real sequences:

\sum_{n=-\infty}^{+\infty}  
x[n]y[n-k]  
}  
]

The autocorrelation is:

\sum_{n=-\infty}^{+\infty}  
x[n]x^*[n-k]  
}  
]

At zero lag:

\sum_{n=-\infty}^{+\infty}|x[n]|^2  
}  
]

which is the signal energy for a finite-energy sequence.

---

# 9. Correlation vs. Convolution

Correlation and convolution look very similar mathematically, but they have different meanings.

### Convolution

[  
\boxed{  
y(t)=  
\int x(\tau)h(t-\tau),d\tau  
}  
]

### Cross-correlation

\int x(t)y^*(t-\tau),dt  
}  
]

The crucial distinction is that correlation is fundamentally a **similarity operation**, while convolution is fundamentally a **system-response operation**.

For real-valued signals, correlation can be related to convolution with a time-reversed signal.

If:

[  
\tilde y(t)=y(-t)  
]

then, under the stated convention:

x(\tau)_y^_(-\tau)  
}  
]

Conceptually:

\text{convolution with a reversed/conjugated signal}  
}  
]

This is why the two operations can look almost identical in calculations.

---

# 10. Graphical Interpretation

To understand cross-correlation graphically:

### Step 1 — Choose one signal

Take:

[  
y(t)  
]

### Step 2 — Conjugate if necessary

For complex signals:

[  
y^*(t)  
]

### Step 3 — Reverse it

[  
y^*(-t)  
]

### Step 4 — Shift it

Produce:

[  
y^*(t-\tau)  
]

### Step 5 — Multiply

Calculate:

[  
x(t)y^*(t-\tau)  
]

### Step 6 — Integrate

Calculate:

\int x(t)y^*(t-\tau),dt  
]

The resulting curve shows similarity versus delay.

---

# 11. Detecting a Delayed Signal

Suppose:

[  
y(t)=x(t-\tau_0)  
]

Then the cross-correlation should have a strong peak near the delay associated with the chosen convention.

Under the convention:

\int x(t)y^*(t-\tau),dt  
]

the peak occurs at:

[  
\boxed{  
\tau=-\tau_0  
}  
]

for the exact noiseless delayed-copy case.

This sign is important.

Other textbooks may define cross-correlation with the opposite shift convention, in which case the peak appears at (+\tau_0).

Therefore, in engineering practice:

> Always inspect the exact definition of the correlation function before interpreting the sign of the estimated delay.

---

# 12. Periodic Signals and Autocorrelation

If a signal is periodic with period (T_0), then:

[  
x(t+T_0)=x(t)  
]

and its autocorrelation reflects this periodic structure.

For a periodic signal, an appropriate time-averaged autocorrelation is:

\frac{1}{T_0}  
\int_{t_0}^{t_0+T_0}  
x(t)x^*(t-\tau),dt  
}  
]

The result is periodic in (\tau):

[  
\boxed{  
R_{xx}(\tau+T_0)=R_{xx}(\tau)  
}  
]

This makes autocorrelation useful for estimating unknown periodicities.

---

# 13. Correlation Properties

The correlation functions have several fundamental properties.

---

## 13.1 Conjugate symmetry of autocorrelation

For a complex signal:

[  
\boxed{  
R_{xx}(\tau)=R_{xx}^*(-\tau)  
}  
]

For real signals:

[  
\boxed{  
R_{xx}(\tau)=R_{xx}(-\tau)  
}  
]

Therefore, the autocorrelation of a real signal is an **even function**.

---

## 13.2 Maximum magnitude at zero lag

For a finite-energy signal:

[  
\boxed{  
|R_{xx}(\tau)|\leq R_{xx}(0)  
}  
]

This follows from the Cauchy–Schwarz inequality.

Thus:

[  
R_{xx}(0)  
]

is the maximum possible magnitude of the autocorrelation.

For many signals, the largest peak occurs at:

[  
\tau=0  
]

---

## 13.3 Autocorrelation at zero equals energy

As established earlier:

[  
\boxed{  
R_{xx}(0)=E_x  
}  
]

for a finite-energy signal.

---

## 13.4 Cross-correlation conjugate symmetry

With the stated convention:

R_{yx}^*(-\tau)  
}  
]

For real signals:

R_{yx}(-\tau)  
}  
]

This means the two cross-correlation functions contain essentially the same information with a reversal/conjugation relationship.

---

# 14. Correlation and Energy Bounds

For finite-energy signals:

[  
E_x=  
\int |x(t)|^2dt  
]

and:

[  
E_y=  
\int |y(t)|^2dt  
]

The Cauchy–Schwarz inequality gives:

[  
\boxed{  
|R_{xy}(\tau)|  
\leq  
\sqrt{E_xE_y}  
}  
]

Since:

[  
R_{xx}(0)=E_x  
]

we also have:

[  
\boxed{  
|R_{xx}(\tau)|  
\leq  
R_{xx}(0)  
}  
]

This is an important theoretical constraint.

---

# 15. Correlation of a Shifted Signal

Suppose:

[  
y(t)=x(t-\tau_0)  
]

Then the cross-correlation is a shifted version of the autocorrelation.

Under the convention used in this chapter:

[  
\boxed{  
R_{xy}(\tau)=R_{xx}(\tau-\tau_0)  
}  
]

depending on the exact ordering and convention.

This relationship explains why correlation can estimate time delays.

The key principle is:

[  
\boxed{  
\text{A delayed copy produces a shifted correlation peak.}  
}  
]

---

# 16. Correlation of Orthogonal Signals

Two signals can be orthogonal if their inner product is zero.

For a particular relative delay:

[  
\boxed{  
R_{xy}(\tau)=0  
}  
]

means the two signals are orthogonal at that delay under the correlation inner-product interpretation.

Orthogonality is fundamental in:

- digital communications,
    
- modulation,
    
- signal decomposition,
    
- radar waveforms,
    
- code-division techniques.
    

---

# 17. Correlation of Sinusoids

Consider:

[  
x(t)=A\cos(\omega_0t)  
]

For a periodic autocorrelation defined as a time average:

\frac{1}{T}  
\int_{t_0}^{t_0+T}  
x(t)x(t-\tau),dt  
]

where (T) is an integer number of periods.

Using:

\frac{1}{2}  
[\cos(\alpha-\beta)+\cos(\alpha+\beta)]  
]

the second term averages to zero over an integer number of periods.

Thus:

\frac{A^2}{2}\cos(\omega_0\tau)  
}  
]

The autocorrelation is itself a cosine with the same frequency.

This illustrates an important principle:

> The autocorrelation of a pure sinusoid preserves its periodicity.

---

# 18. Correlation and Noise

Correlation is extremely useful when a desired signal is embedded in noise.

Suppose:

[  
r(t)=s(t)+n(t)  
]

where:

- (s(t)) is the desired signal,
    
- (n(t)) is noise.
    

If the noise is approximately uncorrelated with a known reference signal (s(t)), correlation with (s(t)) can reveal the desired component.

For example:

R_{ss}(\tau)+R_{ns}(\tau)  
]

If:

[  
R_{ns}(\tau)\approx0  
]

then:

[  
\boxed{  
R_{rs}(\tau)\approx R_{ss}(\tau)  
}  
]

This principle is fundamental to detection and synchronization.

---

# 19. Cross-Correlation as a Detector

Suppose a known waveform (s(t)) is expected to appear in an observed signal:

[  
r(t)=s(t-\tau_0)+n(t)  
]

A detector can calculate:

[  
R_{rs}(\tau)  
]

and search for the strongest peak.

The estimated delay can be obtained as:

\operatorname*{arg,max}_{\tau}  
|R_{rs}(\tau)|  
}  
]

The exact sign of (\hat\tau) depends on the adopted correlation convention.

This idea leads directly to matched-filter detection.

---

# 20. Correlation and the Fourier Transform

A fundamental result connects correlation and spectral analysis.

For suitable signals:

[  
\boxed{  
R_{xy}(\tau)  
\overset{\mathcal F}{\longleftrightarrow}  
X(f)Y^*(f)  
}  
]

Thus:

X(f)Y^*(f)  
}  
]

For autocorrelation:

[  
\boxed{  
R_{xx}(\tau)  
\overset{\mathcal F}{\longleftrightarrow}  
|X(f)|^2  
}  
]

This is one form of the **Wiener–Khinchin theorem** for energy signals.

Therefore, autocorrelation and spectral energy density are Fourier-transform pairs.

---

# 21. Wiener–Khinchin Theorem

For an appropriate signal or stationary random process, the autocorrelation and power spectral density are Fourier-transform pairs.

In frequency notation:

\mathcal F{R_{xx}(\tau)}  
}  
]

and conversely:

\mathcal F^{-1}{S_{xx}(f)}  
}  
]

This is extremely important because it allows us to move between:

- time-domain correlation,
    
- frequency-domain power spectral density.
    

Conceptually:

[  
\boxed{  
\text{Correlation structure in time}  
\longleftrightarrow  
\text{spectral structure in frequency}  
}  
]

---

# 22. Correlation and Spectral Energy Density

For a finite-energy deterministic signal:

[  
R_{xx}(\tau)  
\overset{\mathcal F}{\longleftrightarrow}  
|X(f)|^2  
]

The quantity:

[  
\boxed{  
|X(f)|^2  
}  
]

describes how the signal's energy is distributed over frequency.

Therefore, autocorrelation contains the same information as the energy spectrum, expressed in a different domain.

This is closely related to Parseval's theorem:

# \int_{-\infty}^{+\infty}|x(t)|^2dt

\int_{-\infty}^{+\infty}|X(f)|^2df  
}  
]

and because:

[  
R_{xx}(0)=E_x  
]

we obtain:

\int_{-\infty}^{+\infty}|X(f)|^2df  
}  
]

under the Fourier-transform convention used here.

---

# 23. Example — Correlation of a Rectangular Pulse

Consider:

[  
x(t)=  
\begin{cases}  
1,& |t|\leq T/2\  
0,&\text{otherwise}  
\end{cases}  
]

This is a rectangular pulse of width (T).

The autocorrelation:

\int x(t)x(t-\tau),dt  
]

is determined by the overlap of the pulse with its shifted version.

The overlap length is:

[  
T-|\tau|  
]

when:

[  
|\tau|\leq T  
]

Therefore:

\begin{cases}  
T-|\tau|,&|\tau|\leq T\  
0,&|\tau|>T  
\end{cases}  
}  
]

The autocorrelation is triangular.

Its maximum occurs at:

[  
\boxed{  
R_{xx}(0)=T  
}  
]

which is exactly the pulse energy because the pulse amplitude is 1.

This example is particularly important because it demonstrates how correlation can be understood geometrically as overlap.

---

# 24. Example — Delayed Rectangular Pulse

Let:

[  
y(t)=x(t-\tau_0)  
]

where (x(t)) is the rectangular pulse above.

The cross-correlation becomes a shifted triangular function.

The peak identifies the relative delay.

This is a simple model of a practical problem such as:

- locating a reflected radar pulse,
    
- estimating propagation delay,
    
- synchronization,
    
- measuring time-of-flight.
    

---

# 25. Example — Discrete-Time Autocorrelation

Consider the finite sequence:

[  
x[n]=  
\begin{cases}  
1,&n=0,1,2\  
0,&\text{otherwise}  
\end{cases}  
]

Then:

\sum_nx[n]x[n-k]  
]

At:

[  
k=0  
]

there are three overlapping samples:

[  
R_{xx}[0]=3  
]

At:

[  
k=1  
]

there are two overlapping samples:

[  
R_{xx}[1]=2  
]

At:

[  
k=2  
]

there is one overlapping sample:

[  
R_{xx}[2]=1  
]

By symmetry:

[  
R_{xx}[-1]=2  
]

and:

[  
R_{xx}[-2]=1  
]

Therefore:

\begin{cases}  
3,&k=0\  
2,&|k|=1\  
1,&|k|=2\  
0,&|k|>2  
\end{cases}  
}  
]

This is the discrete analogue of the triangular autocorrelation of a rectangular pulse.

---

# 26. Practical Interpretation of Correlation Peaks

When interpreting a correlation graph, focus on:

### Peak position

Indicates the relative delay.

### Peak amplitude

Indicates the degree of similarity, but it is also affected by signal energy/amplitude.

### Peak width

Can provide information about the temporal resolution of the matching process.

### Secondary peaks

May indicate:

- periodicity,
    
- repeated structures,
    
- multiple echoes,
    
- multipath propagation,
    
- ambiguity in the reference waveform.
    

Correlation therefore contains more information than simply "similar" or "not similar."

---

# 27. Correlation in Signal Detection

A common detection problem is:

[  
r(t)=  
\begin{cases}  
n(t),&H_0\  
s(t)+n(t),&H_1  
\end{cases}  
]

where:

- (H_0): signal absent,
    
- (H_1): signal present.
    

A correlation detector computes:

[  
z(\tau)=R_{rs}(\tau)  
]

and compares the result with a threshold.

If:

[  
|z(\tau)|>\gamma  
]

for some threshold (\gamma), the receiver may declare that the signal is present.

This is a simplified model of a broad class of detection systems.

---

# 28. Correlation and Matched Filtering

For a known finite-energy signal (s(t)), the matched-filter concept is closely related to correlation.

A matched filter is designed so that its output reaches a maximum when the received waveform aligns with the known reference.

Conceptually:

[  
\boxed{  
\text{Matched filtering}  
\approx  
\text{correlation with a known waveform}  
}  
]

This explains why correlation is widely used in:

- digital communications,
    
- radar,
    
- sonar,
    
- spread-spectrum systems,
    
- synchronization.
    

---

# 29. Correlation vs. Covariance

Do not confuse deterministic signal correlation with statistical covariance.

For random variables:

E[(X-\mu_X)(Y-\mu_Y)]  
]

Correlation in the statistical sense often normalizes covariance:

\frac{\operatorname{Cov}(X,Y)}  
{\sigma_X\sigma_Y}  
]

Signal correlation functions, however, are typically functions of a time shift:

[  
R_{xy}(\tau)  
]

The two concepts are related but not identical.

In signal processing, always inspect the definition being used.

---

# 30. Common Mistakes

## Mistake 1 — Confusing correlation with convolution

Convolution and correlation have similar mathematical forms, but their interpretations differ.

Remember:

[  
\boxed{  
\text{Convolution}\rightarrow\text{system response}  
}  
]

[  
\boxed{  
\text{Correlation}\rightarrow\text{similarity}  
}  
]

---

## Mistake 2 — Forgetting complex conjugation

For complex signals:

\int x(t)y^*(t-\tau),dt  
]

The conjugate is essential.

---

## Mistake 3 — Assuming the delay sign without checking the convention

Different textbooks use different definitions.

Always start from the exact formula.

---

## Mistake 4 — Assuming the correlation peak is always exactly at zero

For autocorrelation of a finite-energy signal:

[  
|R_{xx}(\tau)|\leq R_{xx}(0)  
]

but cross-correlation can peak at a nonzero delay because the signals may be shifted relative to one another.

---

## Mistake 5 — Interpreting large correlation as causation

Correlation measures similarity or statistical association.

It does not, by itself, establish a causal relationship.

---

## Mistake 6 — Ignoring signal energy

A large raw correlation value does not necessarily mean stronger similarity if one signal simply has a much larger amplitude.

For comparisons, normalization may be appropriate.

---

# 31. Problem-Solving Strategy

When solving a correlation problem:

### Step 1 — Identify the type

Is it:

- cross-correlation?
    
- autocorrelation?
    
- deterministic signal correlation?
    
- random-process correlation?
    

### Step 2 — Write the exact definition

For continuous-time signals:

\int x(t)y^*(t-\tau),dt  
]

For discrete-time signals:

\sum_nx[n]y^*[n-k]  
]

### Step 3 — Check whether the signals are real

If they are real, remove the conjugation.

### Step 4 — Determine the overlap

For finite-duration signals, determine the values of the delay for which the signals overlap.

### Step 5 — Compute the product

Calculate:

[  
x(t)y^*(t-\tau)  
]

### Step 6 — Integrate or sum

This produces the correlation value at each delay.

### Step 7 — Check properties

For autocorrelation:

[  
R_{xx}(0)=E_x  
]

and:

[  
|R_{xx}(\tau)|\leq R_{xx}(0)  
]

For real signals:

[  
R_{xx}(\tau)=R_{xx}(-\tau)  
]

### Step 8 — Interpret the result

Ask:

- Where is the maximum?
    
- What does its delay mean?
    
- Are there secondary peaks?
    
- Does the result reveal periodicity?
    
- Does normalization matter?
    

---

# 32. Exercises

## Exercise 1 — Basic autocorrelation

Let:

[  
x(t)=  
\begin{cases}  
1,&0\leq t\leq T\  
0,&\text{otherwise}  
\end{cases}  
]

Derive:

[  
R_{xx}(\tau)  
]

and sketch the result.

---

## Exercise 2 — Energy

For:

[  
x(t)=2e^{-t}u(t)  
]

calculate:

[  
R_{xx}(0)  
]

using:

[  
R_{xx}(0)=\int|x(t)|^2dt  
]

---

## Exercise 3 — Cross-correlation

Let:

[  
x(t)=u(t)-u(t-1)  
]

and:

[  
y(t)=x(t-2)  
]

Determine the qualitative shape of:

[  
R_{xy}(\tau)  
]

and identify the location of its maximum using the correlation convention defined in this chapter.

---

## Exercise 4 — Discrete correlation

Given:

[  
x[n]=[1,2,1]  
]

with indices:

[  
n=0,1,2  
]

calculate the autocorrelation sequence:

[  
R_{xx}[k]  
]

for all nonzero values.

---

## Exercise 5 — Sinusoidal autocorrelation

For:

[  
x(t)=A\cos(\omega_0t)  
]

derive its periodic autocorrelation and show that:

\frac{A^2}{2}\cos(\omega_0\tau)  
]

---

## Exercise 6 — Fourier relationship

If:

[  
X(f)  
]

is known, explain how to obtain the autocorrelation using:

[  
R_{xx}(\tau)  
\overset{\mathcal F}{\longleftrightarrow}  
|X(f)|^2  
]

What information about the signal is lost when using only (|X(f)|^2) instead of (X(f))?

---

# 33. Conceptual Questions

You should be able to explain:

1. What does correlation measure?
    
2. What is the difference between cross-correlation and autocorrelation?
    
3. Why does correlation depend on a time shift?
    
4. Why is the complex conjugate required for complex signals?
    
5. Why is (R_{xx}(0)) equal to signal energy?
    
6. Why is the autocorrelation of a real signal even?
    
7. Why can correlation be used for time-delay estimation?
    
8. What does a correlation peak represent?
    
9. What can secondary peaks indicate?
    
10. What is the difference between correlation and convolution?
    
11. Why can correlation be implemented using convolution?
    
12. What is normalized correlation?
    
13. What does the Cauchy–Schwarz inequality tell us about correlation?
    
14. How is autocorrelation related to the energy spectrum?
    
15. What is the Wiener–Khinchin theorem?
    
16. Why is correlation useful in noisy environments?
    
17. How is correlation related to matched filtering?
    
18. Why should the sign convention be checked before interpreting delay?
    

---

# 34. Essential Formula Sheet

## Continuous-time cross-correlation

\int_{-\infty}^{+\infty}  
x(t)y^*(t-\tau),dt  
}  
]

## Continuous-time autocorrelation

\int_{-\infty}^{+\infty}  
x(t)x^*(t-\tau),dt  
}  
]

## Discrete-time cross-correlation

\sum_{n=-\infty}^{+\infty}  
x[n]y^*[n-k]  
}  
]

## Discrete-time autocorrelation

\sum_{n=-\infty}^{+\infty}  
x[n]x^*[n-k]  
}  
]

## Autocorrelation at zero

[  
\boxed{  
R_{xx}(0)=E_x  
}  
]

for finite-energy continuous-time signals.

## Discrete-time energy

\sum_n|x[n]|^2  
}  
]

## Autocorrelation symmetry

[  
\boxed{  
R_{xx}(\tau)=R_{xx}^*(-\tau)  
}  
]

For real signals:

[  
\boxed{  
R_{xx}(\tau)=R_{xx}(-\tau)  
}  
]

## Cross-correlation symmetry

[  
\boxed{  
R_{xy}(\tau)=R_{yx}^*(-\tau)  
}  
]

## Maximum autocorrelation magnitude

[  
\boxed{  
|R_{xx}(\tau)|\leq R_{xx}(0)  
}  
]

## Energy bound

[  
\boxed{  
|R_{xy}(\tau)|  
\leq  
\sqrt{E_xE_y}  
}  
]

## Normalized correlation

\frac{R_{xy}(\tau)}  
{\sqrt{R_{xx}(0)R_{yy}(0)}}  
}  
]

## Correlation theorem

[  
\boxed{  
R_{xy}(\tau)  
\overset{\mathcal F}{\longleftrightarrow}  
X(f)Y^*(f)  
}  
]

## Autocorrelation theorem

[  
\boxed{  
R_{xx}(\tau)  
\overset{\mathcal F}{\longleftrightarrow}  
|X(f)|^2  
}  
]

## Wiener–Khinchin relationship

[  
\boxed{  
S_{xx}(f)=\mathcal F{R_{xx}(\tau)}  
}  
]

---

# 35. Chapter Summary

Correlation provides a mathematical way to measure **similarity between signals as a function of relative displacement**.

For two continuous-time signals:

\int x(t)y^*(t-\tau),dt  
}  
]

This is the cross-correlation.

When the two signals are identical:

\int x(t)x^*(t-\tau),dt  
}  
]

we obtain the autocorrelation.

The most important properties are:

[  
\boxed{  
R_{xx}(0)=E_x  
}  
]

[  
\boxed{  
|R_{xx}(\tau)|\leq R_{xx}(0)  
}  
]

and:

[  
\boxed{  
R_{xx}(\tau)=R_{xx}^*(-\tau)  
}  
]

For real signals:

[  
\boxed{  
R_{xx}(\tau)=R_{xx}(-\tau)  
}  
]

Correlation is closely related to convolution:

[  
\boxed{  
\text{correlation}  
\approx  
\text{convolution with time reversal and conjugation}  
}  
]

Its importance becomes even greater in the frequency domain:

[  
\boxed{  
R_{xy}(\tau)  
\overset{\mathcal F}{\longleftrightarrow}  
X(f)Y^*(f)  
}  
]

and:

[  
\boxed{  
R_{xx}(\tau)  
\overset{\mathcal F}{\longleftrightarrow}  
|X(f)|^2  
}  
]

Therefore:

[  
\boxed{  
\text{Autocorrelation}  
\longleftrightarrow  
\text{Energy/power spectral information}  
}  
]

The practical applications are extensive:

[  
\boxed{  
\text{Correlation}  
\rightarrow  
\begin{cases}  
\text{delay estimation}\  
\text{signal detection}\  
\text{synchronization}\  
\text{pattern matching}\  
\text{periodicity detection}\  
\text{radar/sonar}\  
\text{communications}\  
\text{noise analysis}  
\end{cases}  
}  
]

The central idea to remember is:

[  
\boxed{  
\text{Shift}  
\rightarrow  
\text{multiply}  
\rightarrow  
\text{integrate}  
\rightarrow  
\text{measure similarity}  
}  
]

---

# 36. Mastery Checklist

Before considering Chapter 4 mastered, you should be able to:

- Define cross-correlation.
    
- Define autocorrelation.
    
- Explain correlation physically.
    
- Write continuous-time correlation formulas.
    
- Write discrete-time correlation formulas.
    
- Handle complex conjugation correctly.
    
- Interpret the delay variable.
    
- Explain the relationship between correlation and inner products.
    
- Calculate (R_{xx}(0)).
    
- Relate (R_{xx}(0)) to signal energy.
    
- Prove or use autocorrelation symmetry.
    
- Use the Cauchy–Schwarz bound.
    
- Distinguish normalized and unnormalized correlation.
    
- Compute simple correlation functions.
    
- Interpret correlation peaks.
    
- Estimate relative delay from a correlation function.
    
- Distinguish correlation from convolution.
    
- Explain correlation using graphical overlap.
    
- Relate correlation to Fourier transforms.
    
- State the Wiener–Khinchin theorem.
    
- Explain how correlation assists detection in noise.
    
- Explain the connection between correlation and matched filtering.
    
- Recognize periodicity from autocorrelation.
    
- Interpret secondary correlation peaks.
    
- Check the correlation convention before interpreting delay signs.
    

The conceptual chain to remember is:

[  
\boxed{  
\text{Two signals}  
\rightarrow  
\text{relative shift}  
\rightarrow  
\text{overlap/product}  
\rightarrow  
\text{integration or summation}  
\rightarrow  
\text{correlation function}  
\rightarrow  
\text{similarity / delay / structure}  
}  
]