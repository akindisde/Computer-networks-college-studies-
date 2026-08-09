# Chapter 2 — Fourier Analysis

## 1. General Concepts

### 1.1 Why Fourier analysis?

In Chapter 1, we studied signals in the **time domain**. A signal such as

$$  
x(t)=A\cos(\omega_0t+\phi)  
$$
is described by how its amplitude changes with time.

However, many signal-processing problems are easier to understand in the **frequency domain**.

A signal may look complicated in time while being composed of only a few simple frequency components.

Fourier analysis provides a mathematical framework for answering questions such as:

- Which frequencies are present in a signal?
    
- What is the amplitude of each frequency component?
    
- What is the phase of each component?
    
- What bandwidth does a signal occupy?
    
- How does a system affect different frequencies?
    
- Why does filtering work?
    
- How are periodic and aperiodic signals represented in frequency?
    

The central idea is:

> **A sufficiently well-behaved signal can be represented as a combination of sinusoids or complex exponentials.**

This is the fundamental idea behind Fourier analysis.

---

## 1.2 Time domain vs. frequency domain

A signal can be represented in two complementary ways.

### Time-domain representation

We write:

[  
x(t)  
]

and study how the signal varies with time.

For example:

[  
x(t)=\cos(2\pi 100t)+0.5\cos(2\pi 300t)  
]

In the time domain, this looks like a composite waveform.

### Frequency-domain representation

Instead of describing the signal by its value at every time instant, we describe it in terms of its frequency components.

For the signal above, the important frequencies are:

[  
100\text{ Hz}  
]

and:

[  
300\text{ Hz}  
]

with different amplitudes and phases.

This representation is called the **frequency spectrum**.

---

## 1.3 Frequency as a signal coordinate

The time variable (t) has units of seconds.

The frequency variable (f) has units of hertz:

[  
1\text{ Hz}=1\text{ cycle/second}  
]

Angular frequency is:

[  
\omega=2\pi f  
]

with units of radians per second.

Therefore:

[  
\boxed{\omega=2\pi f}  
]

and:

[  
\boxed{f=\frac{\omega}{2\pi}}  
]

Both (f) and (\omega) are used extensively in Fourier analysis.

---

## 1.4 Complex exponentials

Fourier analysis relies heavily on complex exponentials.

Euler's formula states:

$$
\boxed{  
e^{j\theta}=\cos\theta+j\sin\theta  
}  
$$

Therefore:

\cos(\omega t)+j\sin(\omega t)  
]

A sinusoid can consequently be represented using complex exponentials.

For example:

\frac{1}{2}  
\left(  
e^{j\omega_0t}  
+  
e^{-j\omega_0t}  
\right)  
]

and:

e^{-j\omega_0t}  
\right)  
]

This is not merely mathematical notation. Complex exponentials are the natural basis functions of Fourier analysis.

---

## 1.5 Spectral representation

Suppose a signal can be represented as:

[  
x(t)=\sum_k A_k\cos(\omega_kt+\phi_k)  
]

Then the signal consists of multiple frequency components.

For each component, we can describe:

- its frequency,
    
- its amplitude,
    
- its phase.
    

The collection of these components forms the signal's **spectrum**.

This gives us a powerful interpretation:

[  
\boxed{  
\text{Time-domain signal}  
\longleftrightarrow  
\text{Frequency-domain representation}  
}  
]

Fourier series and the Fourier transform provide the mathematical tools for making this correspondence precise.

---

# 2. Fourier Series

## 2.1 Why Fourier series?

Fourier series are used primarily for **periodic signals**.

The fundamental statement is:

> A periodic signal can be represented as a sum of harmonically related sinusoids.

Consider a periodic signal (x(t)) with fundamental period (T_0).

Its fundamental angular frequency is:

[  
\boxed{  
\omega_0=\frac{2\pi}{T_0}  
}  
]

The Fourier series expresses (x(t)) using frequencies:

[  
0,\quad \omega_0,\quad 2\omega_0,\quad 3\omega_0,\ldots  
]

These are called **harmonics**.

---

## 2.2 Trigonometric Fourier series

For a periodic continuous-time signal (x(t)), the trigonometric Fourier series is:

\frac{a_0}{2}  
+  
\sum_{k=1}^{\infty}  
\left[  
a_k\cos(k\omega_0t)  
+  
b_k\sin(k\omega_0t)  
\right]  
}  
]

where:

[  
\omega_0=\frac{2\pi}{T_0}  
]

The coefficients are:

[  
\boxed{  
a_0=  
\frac{2}{T_0}  
\int_{t_0}^{t_0+T_0}  
x(t),dt  
}  
]

[  
\boxed{  
a_k=  
\frac{2}{T_0}  
\int_{t_0}^{t_0+T_0}  
x(t)\cos(k\omega_0t),dt  
}  
]

and:

[  
\boxed{  
b_k=  
\frac{2}{T_0}  
\int_{t_0}^{t_0+T_0}  
x(t)\sin(k\omega_0t),dt  
}  
]

The integration can be performed over **any complete period**.

---

## 2.3 Meaning of the Fourier coefficients

The coefficient (a_0/2) represents the **DC component**, or average value, of the signal.

The coefficients (a_k) and (b_k) determine the cosine and sine contributions at the (k)-th harmonic.

The (k)-th harmonic has frequency:

[  
f_k=kf_0  
]

or angular frequency:

[  
\omega_k=k\omega_0  
]

Thus:

[  
\boxed{  
\text{harmonic frequency}=k\times\text{fundamental frequency}  
}  
]

---

## 2.4 DC component

The average value of a periodic signal is:

\frac{1}{T_0}  
\int_{t_0}^{t_0+T_0}x(t),dt  
}  
]

Comparing this with (a_0):

[  
\boxed{  
x_{\text{avg}}=\frac{a_0}{2}  
}  
]

Therefore, the DC component is the zero-frequency component of the Fourier series.

---

## 2.5 Complex exponential Fourier series

The trigonometric form is intuitive, but the **complex exponential form** is usually more convenient for theoretical analysis.

The complex Fourier series is:

[  
\boxed{  
x(t)=  
\sum_{k=-\infty}^{+\infty}  
C_k e^{jk\omega_0t}  
}  
]

where:

[  
\boxed{  
C_k=  
\frac{1}{T_0}  
\int_{t_0}^{t_0+T_0}  
x(t)e^{-jk\omega_0t},dt  
}  
]

The coefficient (C_k) is generally complex:

[  
C_k=|C_k|e^{j\phi_k}  
]

Therefore:

- (|C_k|) describes spectral magnitude,
    
- (\phi_k) describes spectral phase.
    

---

## 2.6 Relationship between trigonometric and complex coefficients

The trigonometric coefficients and complex coefficients are related.

For (k>0):

[  
\boxed{  
C_k=\frac{a_k-jb_k}{2}  
}  
]

and:

[  
\boxed{  
C_{-k}=\frac{a_k+jb_k}{2}  
}  
]

For (k=0):

[  
\boxed{  
C_0=\frac{a_0}{2}  
}  
]

For a real-valued signal:

[  
\boxed{  
C_{-k}=C_k^*  
}  
]

where (^*) denotes complex conjugation.

This property is called **conjugate symmetry**.

---

## 2.7 Fourier series and signal symmetry

Signal symmetry can dramatically simplify Fourier-series calculations.

### Even signal

If:

[  
x(-t)=x(t)  
]

then:

[  
b_k=0  
]

Therefore:

[  
\boxed{  
x(t)=  
\frac{a_0}{2}  
+  
\sum_{k=1}^{\infty}  
a_k\cos(k\omega_0t)  
}  
]

Only cosine terms remain.

---

### Odd signal

If:

[  
x(-t)=-x(t)  
]

then:

[  
a_0=0  
]

and:

[  
a_k=0  
]

Therefore:

[  
\boxed{  
x(t)=  
\sum_{k=1}^{\infty}  
b_k\sin(k\omega_0t)  
}  
]

Only sine terms remain.

---

### Half-wave symmetry

If:

[  
x\left(t+\frac{T_0}{2}\right)=-x(t)  
]

then all even harmonics vanish.

This is an important shortcut when analyzing periodic waveforms.

---

## 2.8 Example — Fourier series of a square wave

Consider the symmetric square wave:

[  
x(t)=  
\begin{cases}  
A,&0<t<\frac{T_0}{2}\  
-A,&-\frac{T_0}{2}<t<0  
\end{cases}  
]

with periodic extension.

This signal is odd:

[  
x(-t)=-x(t)  
]

Therefore:

[  
a_0=0  
]

and:

[  
a_k=0  
]

Only sine terms are required.

The coefficient is:

[  
b_k=  
\frac{2}{T_0}  
\int_{-T_0/2}^{T_0/2}  
x(t)\sin(k\omega_0t),dt  
]

Using odd symmetry, this becomes:

[  
b_k=  
\frac{4A}{T_0}  
\int_0^{T_0/2}  
\sin(k\omega_0t),dt  
]

Since:

[  
\omega_0=\frac{2\pi}{T_0}  
]

we obtain:

[  
b_k=  
\frac{2A}{k\pi}  
\left[1-(-1)^k\right]  
]

Therefore:

[  
b_k=  
\begin{cases}  
\frac{4A}{k\pi},&k\text{ odd}\  
0,&k\text{ even}  
\end{cases}  
]

The Fourier series is therefore:

[  
\boxed{  
x(t)=  
\frac{4A}{\pi}  
\left[  
\sin(\omega_0t)  
+  
\frac{1}{3}\sin(3\omega_0t)  
+  
\frac{1}{5}\sin(5\omega_0t)  
+\cdots  
\right]  
}  
]

This result is fundamental.

A square wave is therefore not a single-frequency signal. It is constructed from:

[  
f_0,\quad3f_0,\quad5f_0,\quad7f_0,\ldots  
]

with decreasing harmonic amplitudes.

---

## 2.9 Example — Fourier series of a cosine

Consider:

[  
x(t)=A\cos(\omega_0t+\phi)  
]

Using:

## \cos\phi\cos(\omega_0t)

\sin\phi\sin(\omega_0t)  
]

we obtain:

[  
a_1=A\cos\phi  
]

and:

[  
b_1=-A\sin\phi  
]

All other harmonics are zero.

Thus a pure sinusoid contains only one positive-frequency harmonic pair in the complex representation.

---

## 2.10 Convergence and Gibbs phenomenon

Fourier series do not necessarily reproduce a discontinuous signal point-by-point everywhere.

At a discontinuity, the Fourier series converges under standard conditions to the midpoint of the left and right limits:

\frac{x(t_0^-)+x(t_0^+)}{2}  
}  
]

For a square wave, the reconstructed signal exhibits oscillatory overshoot near discontinuities.

This is called the **Gibbs phenomenon**.

The overshoot becomes narrower as more harmonics are added, but its peak magnitude does not disappear completely.

This is important when interpreting truncated Fourier-series approximations.

---

## 2.11 Parseval's theorem for Fourier series

Fourier coefficients also provide a way to calculate signal power.

For a periodic signal:

\frac{a_0^2}{4}  
+  
\frac{1}{2}  
\sum_{k=1}^{\infty}  
(a_k^2+b_k^2)  
}  
]

In complex form:

[  
\boxed{  
P_x=  
\sum_{k=-\infty}^{+\infty}|C_k|^2  
}  
]

This is Parseval's theorem.

It states that the total average power in the time domain equals the total power distributed among the Fourier components.

---

# 3. Fourier Transform

## 3.1 From periodic to aperiodic signals

Fourier series are designed for periodic signals.

But many practical signals are **aperiodic** or finite-duration.

Examples include:

- a single pulse,
    
- a transient,
    
- a recorded sound segment,
    
- a sensor event,
    
- a decaying waveform.
    

For these signals, the appropriate tool is the **Fourier transform**.

The Fourier transform can be understood conceptually as the limiting form of Fourier-series analysis when the period becomes infinitely large.

Thus:

[  
\boxed{  
\text{Fourier Series}  
\rightarrow  
\text{Periodic signals}  
}  
]

while:

[  
\boxed{  
\text{Fourier Transform}  
\rightarrow  
\text{Aperiodic signals}  
}  
]

---

## 3.2 Definition of the continuous-time Fourier transform

The Fourier transform of (x(t)) is:

[  
\boxed{  
X(f)=  
\int_{-\infty}^{+\infty}  
x(t)e^{-j2\pi ft},dt  
}  
]

The inverse Fourier transform is:

[  
\boxed{  
x(t)=  
\int_{-\infty}^{+\infty}  
X(f)e^{j2\pi ft},df  
}  
]

The pair is written:

[  
\boxed{  
x(t)  
\overset{\mathcal{F}}{\longleftrightarrow}  
X(f)  
}  
]

An equivalent angular-frequency convention is:

[  
\boxed{  
X(\omega)=  
\int_{-\infty}^{+\infty}  
x(t)e^{-j\omega t},dt  
}  
]

and:

[  
\boxed{  
x(t)=  
\frac{1}{2\pi}  
\int_{-\infty}^{+\infty}  
X(\omega)e^{j\omega t},d\omega  
}  
]

### Important convention

The two definitions are equivalent, but the (2\pi) factors appear in different locations.

Always identify which convention is being used before manipulating a Fourier transform.

---

## 3.3 Interpretation of the Fourier transform

The Fourier transform:

[  
X(f)  
]

describes how the signal's content is distributed over frequency.

It is generally complex:

[  
X(f)=|X(f)|e^{j\angle X(f)}  
]

Therefore, two fundamental spectral representations are:

### Magnitude spectrum

[  
\boxed{  
|X(f)|  
}  
]

which describes the strength of the frequency components.

### Phase spectrum

[  
\boxed{  
\angle X(f)  
}  
]

which describes their phase relationships.

Together:

[  
\boxed{  
X(f)=|X(f)|e^{j\angle X(f)}  
}  
]

completely specifies the signal, subject to the usual mathematical conditions.

---

## 3.4 Fourier transform of the unit impulse

One of the most important Fourier-transform pairs is:

[  
\boxed{  
\delta(t)  
\overset{\mathcal{F}}{\longleftrightarrow}  
1  
}  
]

Indeed:

[  
X(f)=  
\int_{-\infty}^{+\infty}  
\delta(t)e^{-j2\pi ft},dt  
=1  
]

This means the impulse contains all frequencies equally in the idealized Fourier sense.

---

## 3.5 Fourier transform of a constant

The transform of:

[  
x(t)=1  
]

is:

[  
\boxed{  
1  
\overset{\mathcal{F}}{\longleftrightarrow}  
\delta(f)  
}  
]

This illustrates a fundamental duality:

- an impulse in time has a constant spectrum,
    
- a constant signal in time has an impulse spectrum.
    

---

## 3.6 Fourier transform of a complex exponential

Consider:

[  
x(t)=e^{j2\pi f_0t}  
]

Its Fourier transform is:

[  
\boxed{  
e^{j2\pi f_0t}  
\overset{\mathcal{F}}{\longleftrightarrow}  
\delta(f-f_0)  
}  
]

A single complex exponential therefore corresponds to a single frequency.

For:

[  
e^{-j2\pi f_0t}  
]

we obtain:

[  
\boxed{  
e^{-j2\pi f_0t}  
\overset{\mathcal{F}}{\longleftrightarrow}  
\delta(f+f_0)  
}  
]

---

## 3.7 Fourier transform of a cosine

Using:

\frac12  
\left(  
e^{j2\pi f_0t}  
+  
e^{-j2\pi f_0t}  
\right)  
]

we obtain:

[  
\boxed{  
\cos(2\pi f_0t)  
\overset{\mathcal{F}}{\longleftrightarrow}  
\frac12  
\left[  
\delta(f-f_0)  
+  
\delta(f+f_0)  
\right]  
}  
]

Thus a real cosine has spectral components at both:

[  
+f_0  
]

and:

[  
-f_0  
]

This is why the Fourier spectrum is generally defined over both positive and negative frequencies.

---

## 3.8 Fourier transform of a rectangular pulse

Consider:

[  
x(t)=  
\begin{cases}  
A,&|t|\leq\frac{T}{2}\  
0,&\text{otherwise}  
\end{cases}  
]

Then:

A\int_{-T/2}^{T/2}  
e^{-j2\pi ft},dt  
]

which gives:

AT  
\frac{\sin(\pi fT)}{\pi fT}  
]

Therefore:

[  
\boxed{  
X(f)=AT,\operatorname{sinc}(fT)  
}  
]

under the normalized definition:

[  
\operatorname{sinc}(u)=\frac{\sin(\pi u)}{\pi u}  
]

This is one of the most important Fourier-transform pairs.

It reveals a fundamental time-frequency relationship:

> A shorter pulse in time produces a broader spectrum in frequency.

Conversely:

> A longer pulse produces a narrower spectrum.

---

## 3.9 Fourier transform properties

The power of Fourier analysis comes not only from the definition, but also from its properties.

---

### Linearity

If:

[  
x_1(t)\overset{\mathcal F}{\longleftrightarrow}X_1(f)  
]

and:

[  
x_2(t)\overset{\mathcal F}{\longleftrightarrow}X_2(f)  
]

then:

[  
\boxed{  
a x_1(t)+b x_2(t)  
\overset{\mathcal F}{\longleftrightarrow}  
aX_1(f)+bX_2(f)  
}  
]

---

### Time shifting

If:

[  
x(t)\overset{\mathcal F}{\longleftrightarrow}X(f)  
]

then:

[  
\boxed{  
x(t-t_0)  
\overset{\mathcal F}{\longleftrightarrow}  
X(f)e^{-j2\pi ft_0}  
}  
]

A time shift changes the **phase** of the spectrum but not its magnitude:

[  
|X_{\text{shifted}}(f)|=|X(f)|  
]

This is an extremely important result.

---

### Frequency shifting

If:

[  
x(t)\overset{\mathcal F}{\longleftrightarrow}X(f)  
]

then:

[  
\boxed{  
x(t)e^{j2\pi f_0t}  
\overset{\mathcal F}{\longleftrightarrow}  
X(f-f_0)  
}  
]

Multiplication by a complex exponential shifts the spectrum in frequency.

---

### Time scaling

If:

[  
x(t)\overset{\mathcal F}{\longleftrightarrow}X(f)  
]

then:

[  
\boxed{  
x(at)  
\overset{\mathcal F}{\longleftrightarrow}  
\frac{1}{|a|}  
X\left(\frac{f}{a}\right)  
}  
]

This formula shows the time-frequency scaling relationship.

If the signal is compressed in time, its spectrum expands in frequency.

---

### Differentiation in time

If:

[  
x(t)\overset{\mathcal F}{\longleftrightarrow}X(f)  
]

then:

[  
\boxed{  
\frac{dx(t)}{dt}  
\overset{\mathcal F}{\longleftrightarrow}  
j2\pi fX(f)  
}  
]

Differentiation therefore emphasizes high-frequency components because of the multiplication by (f).

---

### Integration in time

Under appropriate conditions:

[  
\boxed{  
\int_{-\infty}^{t}x(\tau),d\tau  
\overset{\mathcal F}{\longleftrightarrow}  
\frac{X(f)}{j2\pi f}  
}  
]

with the usual distributional/DC considerations.

---

### Convolution theorem

If:

[  
y(t)=x(t)*h(t)  
]

where:

[  
(x*h)(t)=  
\int_{-\infty}^{+\infty}  
x(\tau)h(t-\tau),d\tau  
]

then:

[  
\boxed{  
Y(f)=X(f)H(f)  
}  
]

This is one of the most important results in signal processing.

Convolution in time becomes multiplication in frequency.

Conversely:

[  
\boxed{  
x(t)h(t)  
\overset{\mathcal F}{\longleftrightarrow}  
X(f)*H(f)  
}  
]

up to the convention-dependent scaling factor.

---

# 4. Energy Spectral Density and Power Spectral Density

## 4.1 Energy spectral density

For an energy signal with Fourier transform (X(f)), the **energy spectral density** is:

[  
\boxed{  
S_E(f)=|X(f)|^2  
}  
]

The total energy is:

[  
\boxed{  
E_x=  
\int_{-\infty}^{+\infty}  
|X(f)|^2,df  
}  
]

under the (f)-based Fourier-transform convention.

This is another form of Parseval's theorem.

---

## 4.2 Power spectral density

For power signals, the corresponding concept is the **power spectral density (PSD)**.

The PSD describes how average power is distributed over frequency.

It is fundamental in:

- noise analysis,
    
- communications,
    
- electronic systems,
    
- control systems,
    
- instrumentation.
    

For random processes, PSD is closely related to the Fourier transform of the autocorrelation function.

That subject will be developed later when stochastic signals and noise are studied.

---

# 5. Important Fourier Transform Pairs

The following pairs should gradually become familiar.

|Time-domain signal|Fourier transform|
|---|---|
|(\delta(t))|(1)|
|(1)|(\delta(f))|
|(e^{j2\pi f_0t})|(\delta(f-f_0))|
|(e^{-j2\pi f_0t})|(\delta(f+f_0))|
|(\cos(2\pi f_0t))|(\frac12[\delta(f-f_0)+\delta(f+f_0)])|
|(\sin(2\pi f_0t))|(\frac{1}{2j}[\delta(f-f_0)-\delta(f+f_0)])|
|Rectangular pulse|(AT,\operatorname{sinc}(fT))|

These pairs are the Fourier-analysis equivalent of a vocabulary list.

Do not try to memorize them without understanding their structure. Derive them from the definition and properties when necessary.

---

# 6. Fourier Series vs. Fourier Transform

The distinction is fundamental.

|   |   |   |
|---|---|---|
|Feature|Fourier Series|Fourier Transform|
|Main application|Periodic signals|Aperiodic signals|
|Frequency representation|Discrete harmonics|Continuous frequency variable|
|Spectrum|Line spectrum|Continuous spectrum|
|Mathematical representation|Sum|Integral|
|Fundamental frequency|Exists|Not necessarily|
|Typical output|(C_k)|(X(f))|

The relationship can be summarized as:

[  
\boxed{  
\text{Periodic signal}  
\rightarrow  
\text{Fourier series}  
}  
]

[  
\boxed{  
\text{Aperiodic signal}  
\rightarrow  
\text{Fourier transform}  
}  
]

A periodic signal has spectral lines located at integer multiples of its fundamental frequency.

An aperiodic signal generally has a continuous spectrum.

---

# 7. Worked Examples

## Example 1 — Fourier coefficients of an even signal

Consider:

[  
x(t)=  
\begin{cases}  
1,&|t|<\frac{T_0}{4}\  
0,&\frac{T_0}{4}<|t|<\frac{T_0}{2}  
\end{cases}  
]

with periodic extension.

The signal is even.

Therefore:

[  
b_k=0  
]

The DC coefficient is:

[  
a_0=  
\frac{2}{T_0}  
\int_{-T_0/4}^{T_0/4}1,dt  
]

giving:

[  
a_0=1  
]

Hence the average value is:

[  
\frac{a_0}{2}=\frac12  
]

For (k\geq1):

[  
a_k=  
\frac{2}{T_0}  
\int_{-T_0/4}^{T_0/4}  
\cos(k\omega_0t),dt  
]

Using even symmetry:

[  
a_k=  
\frac{4}{T_0}  
\int_0^{T_0/4}  
\cos(k\omega_0t),dt  
]

Therefore:

[  
a_k=  
\frac{2}{k\pi}  
\sin\left(\frac{k\pi}{2}\right)  
]

The result is:

[  
\boxed{  
x(t)=  
\frac12  
+  
\sum_{k=1}^{\infty}  
\frac{2}{k\pi}  
\sin\left(\frac{k\pi}{2}\right)  
\cos(k\omega_0t)  
}  
]

Because:

[  
\sin\left(\frac{k\pi}{2}\right)  
]

vanishes for even (k), only odd harmonics occur.

---

## Example 2 — Fourier transform of a shifted rectangular pulse

Suppose:

[  
x(t)=A,\operatorname{rect}\left(\frac{t-t_0}{T}\right)  
]

The unshifted rectangular pulse has transform:

[  
AT,\operatorname{sinc}(fT)  
]

The time-shift property gives:

AT,\operatorname{sinc}(fT)  
e^{-j2\pi ft_0}  
}  
]

Therefore:

AT|\operatorname{sinc}(fT)|  
]

The shift changes phase but does not change the magnitude spectrum.

This is a fundamental result that appears frequently in practical signal analysis.

---

## Example 3 — Why a shorter pulse has a broader spectrum

For:

[  
x(t)=\operatorname{rect}\left(\frac{t}{T}\right)  
]

we have:

[  
X(f)=T\operatorname{sinc}(fT)  
]

The first zeros occur when:

[  
fT=\pm1  
]

so:

[  
f=\pm\frac{1}{T}  
]

The main-lobe width between the first zeros is therefore:

[  
\boxed{  
\Delta f=\frac{2}{T}  
}  
]

If (T) decreases, (\Delta f) increases.

Hence:

[  
\boxed{  
\text{shorter in time}  
\Longleftrightarrow  
\text{broader in frequency}  
}  
]

and:

[  
\boxed{  
\text{longer in time}  
\Longleftrightarrow  
\text{narrower in frequency}  
}  
]

This is one of the most important time-frequency trade-offs in signal processing.

---

# 8. A Practical Method for Fourier-Series Problems

When asked to determine the Fourier series of a periodic signal, use the following procedure.

### Step 1 — Determine the period

Find:

[  
T_0  
]

and then:

[  
\omega_0=\frac{2\pi}{T_0}  
]

### Step 2 — Inspect symmetry

Check whether the signal is:

- even,
    
- odd,
    
- half-wave symmetric.
    

This can eliminate many coefficients immediately.

### Step 3 — Calculate the DC component

Compute:

\frac{1}{T_0}  
\int_{T_0}x(t),dt  
]

### Step 4 — Calculate the remaining coefficients

Use:

[  
a_k=  
\frac{2}{T_0}  
\int_{T_0}x(t)\cos(k\omega_0t),dt  
]

and:

[  
b_k=  
\frac{2}{T_0}  
\int_{T_0}x(t)\sin(k\omega_0t),dt  
]

### Step 5 — Simplify

Look for:

- even/odd harmonic cancellation,
    
- zero coefficients,
    
- standard trigonometric identities.
    

### Step 6 — Interpret the result

Do not stop at the coefficient calculation.

Ask:

- Which harmonics are present?
    
- Which harmonic is dominant?
    
- How quickly do amplitudes decrease?
    
- What does the spectrum tell us about the waveform?
    

---

# 9. A Practical Method for Fourier-Transform Problems

When calculating a Fourier transform:

### Step 1 — Identify the signal

Is it:

- an impulse?
    
- a rectangular pulse?
    
- an exponential?
    
- a sinusoid?
    
- a shifted/scaled standard signal?
    

### Step 2 — Look for a known transform pair

Use known pairs whenever possible.

### Step 3 — Apply properties

Check for:

- time shift,
    
- time scaling,
    
- frequency shift,
    
- differentiation,
    
- linearity,
    
- convolution.
    

### Step 4 — Check units and conventions

Determine whether the problem uses:

[  
f  
]

or:

[  
\omega  
]

as the frequency variable.

### Step 5 — Interpret the spectrum

Determine:

- magnitude,
    
- phase,
    
- bandwidth,
    
- symmetry,
    
- dominant frequency components.
    

---

# 10. Common Mistakes

## Mistake 1 — Confusing (f) and (\omega)

Remember:

[  
\boxed{\omega=2\pi f}  
]

Do not use:

[  
e^{-jft}  
]

when the convention requires:

[  
e^{-j2\pi ft}  
]

---

## Mistake 2 — Forgetting the fundamental frequency

For Fourier series:

[  
\omega_0=\frac{2\pi}{T_0}  
]

and the (k)-th harmonic is:

[  
k\omega_0  
]

---

## Mistake 3 — Ignoring symmetry

Before integrating, always ask:

[  
x(-t)=x(t)?  
]

or:

[  
x(-t)=-x(t)?  
]

Symmetry can eliminate half or all of the coefficient calculations.

---

## Mistake 4 — Thinking a square wave contains only one frequency

A square wave is composed of multiple harmonics.

For the symmetric square wave:

[  
f_0,;3f_0,;5f_0,\ldots  
]

are present.

---

## Mistake 5 — Confusing Fourier series with Fourier transform

A Fourier series produces a **discrete set of spectral coefficients** for a periodic signal.

A Fourier transform produces a **continuous frequency-domain function** for an aperiodic signal, under the standard formulation.

---

## Mistake 6 — Forgetting negative frequencies

For real signals, the Fourier spectrum generally exists at both positive and negative frequencies.

For a cosine:

[  
\cos(2\pi f_0t)  
]

the spectrum contains:

[  
+f_0  
]

and:

[  
-f_0  
]

Negative frequencies are not "extra physical frequencies"; they arise naturally from the complex-exponential representation.

---

# 11. Exercises

## Exercise 1 — Basic Fourier-series analysis

A periodic signal has period:

[  
T_0=4\text{ ms}  
]

Determine:

1. Fundamental frequency (f_0).
    
2. Fundamental angular frequency (\omega_0).
    
3. Frequencies of the first five harmonics.
    

---

## Exercise 2 — Symmetry

For each signal, determine whether it is even, odd, or neither:

### (a)

[  
x(t)=t^2  
]

### (b)

[  
x(t)=t^3  
]

### (c)

[  
x(t)=e^t  
]

Then state which Fourier-series coefficients must vanish.

---

## Exercise 3 — Square wave

For the symmetric square wave of amplitude (A):

1. Determine whether it is even or odd.
    
2. Determine (a_0).
    
3. Determine (a_k).
    
4. Determine (b_k).
    
5. Identify which harmonics are present.
    
6. Explain what happens as more harmonics are included.
    

---

## Exercise 4 — Fourier transform

Using the Fourier-transform definition, derive:

[  
\delta(t)  
\longleftrightarrow  
1  
]

and:

[  
e^{j2\pi f_0t}  
\longleftrightarrow  
\delta(f-f_0)  
]

---

## Exercise 5 — Time shift

Suppose:

[  
x(t)  
\longleftrightarrow  
X(f)  
]

Determine the Fourier transform of:

[  
y(t)=x(t-3)  
]

---

## Exercise 6 — Time scaling

Suppose:

[  
x(t)  
\longleftrightarrow  
X(f)  
]

Determine the Fourier transform of:

[  
y(t)=x(4t)  
]

Then explain qualitatively what happens to the bandwidth.

---

## Exercise 7 — Rectangular pulse

Consider:

[  
x(t)=  
\begin{cases}  
1,&|t|\leq2\  
0,&\text{otherwise}  
\end{cases}  
]

1. Calculate (X(f)).
    
2. Express the answer using sinc.
    
3. Find the first spectral zeros.
    
4. Explain how changing the pulse width affects the spectrum.
    

---

# 12. Conceptual Questions

You should be able to explain these without simply quoting formulas:

1. Why is Fourier analysis useful?
    
2. What is the difference between the time domain and frequency domain?
    
3. Why are complex exponentials used in Fourier analysis?
    
4. What is a harmonic?
    
5. What is the difference between the fundamental frequency and a harmonic?
    
6. Why does an even signal contain only cosine terms in its Fourier series?
    
7. Why does an odd signal contain only sine terms?
    
8. Why does a square wave contain odd harmonics?
    
9. What is the difference between Fourier series and Fourier transform?
    
10. What do magnitude and phase spectra represent?
    
11. Why does a time shift change spectral phase but not spectral magnitude?
    
12. Why does compressing a signal in time broaden its spectrum?
    
13. What is the Gibbs phenomenon?
    
14. What is the physical/mathematical meaning of negative frequencies?
    
15. Why is convolution important in Fourier analysis?
    

---

# 13. Essential Formula Sheet

## Fundamental frequency

[  
\boxed{  
f_0=\frac{1}{T_0}  
}  
]

## Fundamental angular frequency

[  
\boxed{  
\omega_0=\frac{2\pi}{T_0}=2\pi f_0  
}  
]

## Trigonometric Fourier series

[  
\boxed{  
x(t)=  
\frac{a_0}{2}  
+  
\sum_{k=1}^{\infty}  
[  
a_k\cos(k\omega_0t)  
+  
b_k\sin(k\omega_0t)  
]  
}  
]

## Fourier coefficients

[  
\boxed{  
a_k=  
\frac{2}{T_0}  
\int_{T_0}x(t)\cos(k\omega_0t),dt  
}  
]

[  
\boxed{  
b_k=  
\frac{2}{T_0}  
\int_{T_0}x(t)\sin(k\omega_0t),dt  
}  
]

[  
\boxed{  
a_0=  
\frac{2}{T_0}  
\int_{T_0}x(t),dt  
}  
]

## Complex Fourier series

[  
\boxed{  
x(t)=  
\sum_{k=-\infty}^{+\infty}  
C_ke^{jk\omega_0t}  
}  
]

[  
\boxed{  
C_k=  
\frac{1}{T_0}  
\int_{T_0}  
x(t)e^{-jk\omega_0t},dt  
}  
]

## Fourier transform

[  
\boxed{  
X(f)=  
\int_{-\infty}^{+\infty}  
x(t)e^{-j2\pi ft},dt  
}  
]

## Inverse Fourier transform

[  
\boxed{  
x(t)=  
\int_{-\infty}^{+\infty}  
X(f)e^{j2\pi ft},df  
}  
]

## Time shift

[  
\boxed{  
x(t-t_0)  
\longleftrightarrow  
X(f)e^{-j2\pi ft_0}  
}  
]

## Time scaling

[  
\boxed{  
x(at)  
\longleftrightarrow  
\frac{1}{|a|}  
X\left(\frac{f}{a}\right)  
}  
]

## Differentiation

[  
\boxed{  
\frac{dx}{dt}  
\longleftrightarrow  
j2\pi fX(f)  
}  
]

## Convolution

[  
\boxed{  
x(t)*h(t)  
\longleftrightarrow  
X(f)H(f)  
}  
]

## Parseval

\int_{-\infty}^{+\infty}|X(f)|^2df  
}  
]

under the (f)-based Fourier-transform convention.

---

# 14. Chapter Summary

Fourier analysis provides a bridge between the **time domain** and the **frequency domain**.

For periodic signals, the appropriate tool is the **Fourier series**:

[  
\boxed{  
x(t)=  
\sum_{k=-\infty}^{+\infty}  
C_ke^{jk\omega_0t}  
}  
]

The signal is represented by a discrete set of harmonics.

For aperiodic signals, the appropriate tool is the **Fourier transform**:

[  
\boxed{  
X(f)=  
\int_{-\infty}^{+\infty}  
x(t)e^{-j2\pi ft},dt  
}  
]

The signal is represented by a continuous frequency spectrum.

The most important conceptual relationships are:

[  
\boxed{  
\text{Periodic}  
\rightarrow  
\text{Fourier Series}  
\rightarrow  
\text{Discrete Spectrum}  
}  
]

and:

[  
\boxed{  
\text{Aperiodic}  
\rightarrow  
\text{Fourier Transform}  
\rightarrow  
\text{Continuous Spectrum}  
}  
]

The central engineering insight is that a signal can often be understood more easily by decomposing it into frequency components.

This idea becomes especially powerful when studying systems:

[  
\boxed{  
Y(f)=X(f)H(f)  
}  
]

where (H(f)) describes how a system modifies each frequency component.

This is the foundation of **frequency-domain filtering**, and it will become central when we study linear time-invariant systems, convolution, filtering, sampling, and digital signal processing.

---

# 15. What You Should Master Before Chapter 3

Before moving forward, make sure you can:

- Compute the fundamental frequency and angular frequency.
    
- Write a trigonometric Fourier series.
    
- Calculate (a_0), (a_k), and (b_k).
    
- Exploit even and odd symmetry.
    
- Explain the meaning of harmonics.
    
- Write the complex Fourier series.
    
- Explain magnitude and phase spectra.
    
- Compute basic Fourier transforms.
    
- Apply time-shift and time-scaling properties.
    
- Explain the convolution theorem.
    
- Explain why shorter signals have broader spectra.
    
- Distinguish Fourier series from Fourier transform.
    
- Interpret a spectrum rather than merely calculate it.
    

The objective is not to memorize a table of formulas. The objective is to understand the following chain:

[  
\boxed{  
\text{Signal}  
\rightarrow  
\text{Decomposition}  
\rightarrow  
\text{Frequency Components}  
\rightarrow  
\text{Spectrum}  
\rightarrow  
\text{System Response}  
}  
]

That chain is one of the central ideas of signal processing.