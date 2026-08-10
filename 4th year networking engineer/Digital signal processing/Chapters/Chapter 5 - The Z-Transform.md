# Chapter 5 — The Z-Transform

## 1. Introduction

The **Z-transform** is one of the fundamental mathematical tools of discrete-time signal processing.

It plays a role for discrete-time signals similar to the **Laplace transform** for continuous-time signals.

It allows us to:

- represent discrete-time signals in a transform domain,
    
- solve linear difference equations,
    
- analyze discrete-time LTI systems,
    
- study causality and stability,
    
- determine poles and zeros,
    
- analyze frequency response,
    
- design and analyze digital filters.
    

For a sequence (x[n]), the bilateral Z-transform is

[  
\boxed{X(z)=\sum_{n=-\infty}^{+\infty}x[n]z^{-n}}  
]

where (z) is complex:

[  
z=re^{j\omega}.  
]

The key idea is:

[  
\boxed{\text{Discrete-time sequence}\rightarrow\text{complex-domain representation}}  
]

---

## 2. Why the Z-Transform?

Consider the difference equation

[  
y[n]-ay[n-1]=x[n].  
]

Under zero initial conditions, the Z-transform gives

[  
Y(z)-az^{-1}Y(z)=X(z),  
]

hence

[  
Y(z)(1-az^{-1})=X(z)  
]

and

[  
\boxed{H(z)=\frac{Y(z)}{X(z)}=\frac{1}{1-az^{-1}}}.  
]

A difference equation has become an algebraic equation. This is the central practical advantage of the Z-transform.

---

## 3. Bilateral and Unilateral Z-Transforms

### 3.1 Bilateral Z-transform

[  
\boxed{  
X(z)=\sum_{n=-\infty}^{+\infty}x[n]z^{-n}  
}  
]

It is mainly used for general signal and system analysis.

### 3.2 Unilateral Z-transform

[  
\boxed{  
X^+(z)=\sum_{n=0}^{+\infty}x[n]z^{-n}  
}  
]

It is especially useful for causal systems and difference equations with nonzero initial conditions.

Thus:

[  
\boxed{  
\text{Bilateral}\rightarrow\text{general analysis}  
}  
]

[  
\boxed{  
\text{Unilateral}\rightarrow\text{initial conditions}  
}  
]

---

## 4. Region of Convergence (ROC)

The algebraic expression (X(z)) is not enough. We must also specify where the defining infinite series converges.

The **Region of Convergence** is

[  
\boxed{  
\mathrm{ROC}={z\in\mathbb C(z)\text{ converges}}.  
}  
]

Therefore, a Z-transform is properly characterized by

[  
\boxed{X(z)+\text{ROC}}.  
]

For example,

[  
X(z)=\frac{1}{1-az^{-1}}  
]

can represent either a right-sided or left-sided sequence depending on the ROC.

### Right-sided

[  
|z|>|a|  
]

corresponds to

[  
x[n]=a^nu[n].  
]

### Left-sided

[  
|z|<|a|  
]

corresponds to

[  
x[n]=-a^nu[-n-1].  
]

The algebraic expression is identical; the ROC distinguishes the sequences.

---

## 5. The Z-Plane

Write

[  
z=re^{j\omega}.  
]

Then:

- (r=|z|) is the magnitude,
    
- (\omega=\arg(z)) is the angle.
    

The z-plane is the complex plane used to visualize poles, zeros, and the ROC.

A constant magnitude

[  
|z|=r  
]

is a circle centered at the origin.

Typical ROCs therefore have forms such as

[  
|z|>r_0,  
\qquad  
|z|<r_0,  
\qquad  
r_1<|z|<r_2.  
]

The last case is an annular region.

---

## 6. Relationship with the DTFT

The DTFT is

\sum_{n=-\infty}^{+\infty}x[n]e^{-j\omega n}.  
]

Since

[  
z=e^{j\omega}  
]

on the unit circle,

[  
\boxed{  
X(e^{j\omega})=X(z)\big|_{z=e^{j\omega}}  
}  
]

provided that the unit circle belongs to the ROC.

Thus:

[  
\boxed{\text{DTFT = Z-transform evaluated on the unit circle}}  
]

when the unit circle is in the ROC.

---

## 7. The Unit Circle

The unit circle is

[  
\boxed{|z|=1}  
]

or

[  
z=e^{j\omega}.  
]

It is fundamental because it provides the frequency response:

[  
\boxed{  
H(e^{j\omega})=H(z)\big|_{z=e^{j\omega}}  
}  
]

provided the unit circle is in the ROC.

For a stable discrete-time LTI system, the unit circle must be contained in the ROC.

---

## 8. Fundamental Z-Transform Pairs

### 8.1 Unit impulse

[  
x[n]=\delta[n]  
]

gives

[  
\boxed{\delta[n]\longleftrightarrow1}  
]

with ROC (0<|z|<\infty).

### 8.2 Delayed impulse

[  
\boxed{  
\delta[n-n_0]\longleftrightarrow z^{-n_0}  
}  
]

### 8.3 Right-sided exponential

For

[  
x[n]=a^nu[n],  
]

[  
X(z)=\sum_{n=0}^{\infty}(az^{-1})^n  
]

so

[  
\boxed{  
a^nu[n]  
\longleftrightarrow  
\frac{1}{1-az^{-1}},  
\qquad |z|>|a|  
}  
]

### 8.4 Left-sided exponential

[  
\boxed{  
-a^nu[-n-1]  
\longleftrightarrow  
\frac{1}{1-az^{-1}},  
\qquad |z|<|a|  
}  
]

### 8.5 Unit step

For (u[n]),

[  
\boxed{  
u[n]\longleftrightarrow\frac{1}{1-z^{-1}},  
\qquad |z|>1  
}  
]

---

## 9. Properties of the Z-Transform

### 9.1 Linearity

If

[  
x_1[n]\longleftrightarrow X_1(z)  
]

and

[  
x_2[n]\longleftrightarrow X_2(z),  
]

then

[  
\boxed{  
ax_1[n]+bx_2[n]  
\longleftrightarrow  
aX_1(z)+bX_2(z)  
}  
]

under the usual ROC conditions.

### 9.2 Time shifting

[  
\boxed{  
x[n-n_0]  
\longleftrightarrow  
z^{-n_0}X(z)  
}  
]

Therefore:

[  
x[n-1]\longleftrightarrow z^{-1}X(z).  
]

A delay in time becomes multiplication by a power of (z^{-1}).

### 9.3 Time reversal

[  
\boxed{  
x[-n]\longleftrightarrow X(z^{-1})  
}  
]

### 9.4 Multiplication by (a^n)

[  
\boxed{  
a^nx[n]\longleftrightarrow X\left(\frac{z}{a}\right)  
}  
]

for appropriate (a\neq0).

### 9.5 Convolution

If

[  
y[n]=x[n]*h[n],  
]

then

[  
\boxed{  
Y(z)=X(z)H(z)  
}  
]

with the appropriate ROC.

Thus:

[  
\boxed{  
\text{Convolution in time}\leftrightarrow\text{multiplication in the z-domain}  
}  
]

---

## 10. Difference Equations

Consider

b_0x[n]+b_1x[n-1]+\cdots+b_Mx[n-M].  
]

Under zero initial conditions:

X(z)  
(b_0+b_1z^{-1}+\cdots+b_Mz^{-M}).  
]

Therefore the transfer function is

\frac{  
b_0+b_1z^{-1}+\cdots+b_Mz^{-M}  
}{  
a_0+a_1z^{-1}+\cdots+a_Nz^{-N}  
}  
}  
]

This is one of the most important applications of the Z-transform.

---

## 11. Transfer Function and Impulse Response

For an LTI system,

[  
y[n]=x[n]*h[n].  
]

Therefore,

[  
Y(z)=X(z)H(z)  
]

and

[  
\boxed{  
H(z)=\frac{Y(z)}{X(z)}  
}  
]

under zero initial conditions.

Also,

[  
\boxed{  
H(z)=\mathcal Z{h[n]}  
}  
]

with its associated ROC.

---

## 12. Poles and Zeros

Suppose

[  
H(z)=\frac{N(z)}{D(z)}.  
]

### Zeros

Values satisfying

[  
N(z)=0  
]

are zeros.

### Poles

Values satisfying

[  
D(z)=0  
]

are poles, provided they are not canceled.

Thus:

[  
\boxed{\text{Zeros}\rightarrow H(z)=0}  
]

[  
\boxed{\text{Poles}\rightarrow H(z)\text{ becomes unbounded}}  
]

in the ideal algebraic sense.

A pole-zero representation is often written as

[  
H(z)=K  
\frac{\prod_k(z-z_k)}  
{\prod_m(z-p_m)}.  
]

---

## 13. ROC and Causality

For a rational Z-transform:

[  
\boxed{  
\text{Right-sided sequence}\rightarrow  
\text{ROC outside the outermost pole}  
}  
]

Thus a causal rational LTI system has an ROC of the form

[  
\boxed{  
|z|>\max_k|p_k|.  
}  
]

For example,

[  
H(z)=\frac{1}{1-az^{-1}}  
]

with

[  
|z|>|a|  
]

corresponds to

[  
h[n]=a^nu[n].  
]

---

## 14. ROC and Stability

A discrete-time LTI system is BIBO stable if

[  
\sum_{n=-\infty}^{+\infty}|h[n]|<\infty.  
]

In terms of the Z-transform:

[  
\boxed{  
\text{The unit circle must belong to the ROC.}  
}  
]

For a causal rational system, this means

[  
\boxed{  
|p_k|<1  
\quad\text{for every pole}.  
}  
]

Hence:

[  
\boxed{  
\text{Causal + stable rational system}  
\Rightarrow  
\text{all poles strictly inside the unit circle}.  
}  
]

---

## 15. Pole Location and Time-Domain Behavior

For

[  
H(z)=\frac{1}{1-az^{-1}}  
]

with a causal ROC,

[  
h[n]=a^nu[n].  
]

### If (|a|<1)

The response decays and the system is stable.

### If (|a|=1)

The response does not decay; the corresponding causal system is not BIBO stable.

### If (|a|>1)

The response grows and the causal system is unstable.

Therefore:

[  
\boxed{  
|p|<1\rightarrow\text{decay}  
}  
]

[  
\boxed{  
|p|=1\rightarrow\text{non-decaying behavior}  
}  
]

[  
\boxed{  
|p|>1\rightarrow\text{growth}  
}  
]

---

## 16. Frequency Response

If the unit circle belongs to the ROC,

[  
\boxed{  
H(e^{j\omega})=H(z)|_{z=e^{j\omega}}.  
}  
]

The frequency response can be written as

|H(e^{j\omega})|e^{j\angle H(e^{j\omega})}.  
]

Thus:

- (|H(e^{j\omega})|): magnitude response,
    
- (\angle H(e^{j\omega})): phase response.
    

This is the foundation of digital filter analysis.

---

## 17. Inverse Z-Transform

Given (X(z)), we may need to recover (x[n]).

### Method 1 — Transform pairs

Use known pairs directly.

### Method 2 — Power-series expansion

For

[  
X(z)=\frac{1}{1-az^{-1}},  
\qquad |z|>|a|,  
]

expand:

[  
X(z)=1+az^{-1}+a^2z^{-2}+\cdots  
]

so

[  
\boxed{x[n]=a^nu[n]}.  
]

### Method 3 — Partial fractions

For rational functions, decompose into standard terms such as

[  
\frac{A}{1-pz^{-1}}.  
]

### Method 4 — Contour integral

The formal inverse is

[  
\boxed{  
x[n]=\frac{1}{2\pi j}  
\oint_CX(z)z^{n-1}dz  
}  
]

where (C) lies within the ROC.

---

## 18. Partial-Fraction Example

Consider

[  
X(z)=  
\frac{1}{  
(1-0.5z^{-1})(1-0.2z^{-1})  
}  
]

with

[  
|z|>0.5.  
]

Write

[  
X(z)=  
\frac{A}{1-0.5z^{-1}}  
+  
\frac{B}{1-0.2z^{-1}}.  
]

Solving gives

[  
A=\frac53,  
\qquad  
B=-\frac23.  
]

Therefore

\frac{2/3}{1-0.2z^{-1}}.  
]

Hence

## \frac53(0.5)^nu[n]

\frac23(0.2)^nu[n].  
}  
]

---

## 19. Possible ROCs for Rational Transforms

Suppose

[  
X(z)=  
\frac{1}{  
(1-p_1z^{-1})(1-p_2z^{-1})  
}  
]

with

[  
|p_1|<|p_2|.  
]

Possible ROCs include:

### Right-sided

[  
\boxed{|z|>|p_2|}  
]

### Two-sided

[  
\boxed{|p_1|<|z|<|p_2|}  
]

### Left-sided

[  
\boxed{|z|<|p_1|}  
]

The ROC contains no poles.

---

## 20. Stability Example

Consider

[  
X(z)=  
\frac{1}{  
(1-0.5z^{-1})(1-2z^{-1})  
}.  
]

The poles are:

[  
p_1=0.5,\qquad p_2=2.  
]

Possible ROCs are:

[  
|z|>2,  
]

[  
0.5<|z|<2,  
]

and

[  
|z|<0.5.  
]

The unit circle (|z|=1) belongs only to

[  
\boxed{0.5<|z|<2}.  
]

Therefore, only the corresponding two-sided sequence is stable.

This demonstrates that stability depends on the ROC, not merely on the algebraic expression.

---

## 21. Initial and Final Value Theorems

### Initial value

For a causal sequence under the appropriate conditions:

[  
\boxed{  
x[0]=\lim_{z\to\infty}X(z)  
}  
]

### Final value

Under the required convergence and pole conditions:

\lim_{z\to1}(z-1)X(z)  
}  
]

The final-value theorem must not be used without checking its pole conditions.

---

## 22. FIR and IIR Systems

### FIR — Finite Impulse Response

An FIR filter has a finite-duration impulse response:

[  
h[n]=0  
]

outside a finite range.

Its transfer function is typically

[  
\boxed{  
H(z)=b_0+b_1z^{-1}+\cdots+b_Mz^{-M}.  
}  
]

### IIR — Infinite Impulse Response

An IIR filter generally has feedback:

[  
\boxed{  
H(z)=  
\frac{  
b_0+b_1z^{-1}+\cdots+b_Mz^{-M}  
}{  
1+a_1z^{-1}+\cdots+a_Nz^{-N}  
}.  
}  
]

The impulse response can continue indefinitely.

Thus:

[  
\boxed{  
\text{FIR}\rightarrow\text{finite impulse response}  
}  
]

[  
\boxed{  
\text{IIR}\rightarrow\text{potentially infinite impulse response}  
}  
]

---

## 23. Pole-Zero Geometry and Frequency Response

For

[  
H(z)=K  
\frac{\prod_k(z-z_k)}  
{\prod_m(z-p_m)},  
]

on the unit circle:

K  
\frac{  
\prod_k(e^{j\omega}-z_k)  
}{  
\prod_m(e^{j\omega}-p_m)  
}.  
]

Therefore,

|K|  
\frac{  
\prod_k|e^{j\omega}-z_k|  
}{  
\prod_m|e^{j\omega}-p_m|  
}.  
}  
]

Geometrically:

- a zero close to the unit circle tends to attenuate frequencies near its angle,
    
- a pole close to the unit circle tends to emphasize frequencies near its angle.
    

A zero exactly on the unit circle produces zero response at its corresponding frequency.

---

## 24. Example — First-Order System

Given

[  
y[n]-ay[n-1]=x[n],  
]

we obtain

[  
\boxed{  
H(z)=\frac{1}{1-az^{-1}}.  
}  
]

The pole is

[  
p=a.  
]

For a causal system:

[  
\boxed{\mathrm{ROC}:|z|>|a|}  
]

and

[  
h[n]=a^nu[n].  
]

The system is stable if

[  
\boxed{|a|<1}.  
]

Its frequency response is

\frac{1}{1-ae^{-j\omega}}.  
}  
]

---

## 25. Example — Second-Order System

Consider

[  
y[n]-1.2y[n-1]+0.32y[n-2]=x[n].  
]

Then

[  
H(z)=  
\frac{1}{  
1-1.2z^{-1}+0.32z^{-2}  
}.  
]

Factor the denominator:

(1-0.8z^{-1})(1-0.4z^{-1}).  
]

Thus

[  
\boxed{  
H(z)=  
\frac{1}{  
(1-0.8z^{-1})(1-0.4z^{-1})  
}.  
}  
]

The poles are:

[  
\boxed{p_1=0.8,\qquad p_2=0.4.}  
]

For a causal system:

[  
\boxed{\mathrm{ROC}:|z|>0.8}  
]

and the system is stable because both poles are inside the unit circle.

---

## 26. Example — Difference Filter

Consider

[  
y[n]=x[n]-x[n-1].  
]

Then

[  
H(z)=1-z^{-1}.  
]

Equivalently,

[  
H(z)=\frac{z-1}{z}.  
]

There is a zero at

[  
\boxed{z=1}.  
]

The frequency response is

[  
H(e^{j\omega})=1-e^{-j\omega}.  
]

At DC,

[  
H(e^{j0})=0.  
]

Therefore the filter suppresses the DC component.

---

## 27. Unilateral Z-Transform and Initial Conditions

The unilateral transform is especially useful when initial conditions are nonzero.

For example,

z^{-1}X^+(z)+x[-1].  
}  
]

This additional initial-value term is exactly why the unilateral transform is useful for solving initial-value problems.

---

## 28. Z-Transform and the Laplace Transform

The Z-transform is closely related to the Laplace transform through

[  
\boxed{  
z=e^{sT}  
}  
]

where (T) is the sampling period.

If

[  
s=\sigma+j\Omega,  
]

then

[  
z=e^{(\sigma+j\Omega)T}.  
]

Therefore,

[  
\boxed{|z|=e^{\sigma T}}  
]

and

[  
\boxed{\arg(z)=\Omega T}.  
]

Consequently,

[  
\sigma<0  
\quad\Longleftrightarrow\quad  
|z|<1.  
]

Thus the left half of the (s)-plane maps to the interior of the unit circle.

This explains the analogy:

[  
\boxed{  
\text{Continuous-time stability: }\Re(s)<0  
}  
]

[  
\boxed{  
\text{Discrete-time causal stability: }|z|<1  
}  
]

for poles.

---

## 29. Common Mistakes

### Mistake 1 — Ignoring the ROC

The expression

[  
\frac{1}{1-az^{-1}}  
]

does not uniquely determine the sequence without its ROC.

### Mistake 2 — Assuming stability always means poles inside the unit circle

The general statement is:

[  
\boxed{\text{The unit circle must belong to the ROC.}}  
]

The pole-inside-unit-circle rule additionally assumes a causal rational system.

### Mistake 3 — Confusing (z) and (z^{-1})

Digital filter expressions are naturally written in powers of (z^{-1}).

### Mistake 4 — Forgetting initial conditions

Nonzero initial conditions may require the unilateral Z-transform.

### Mistake 5 — Using the final-value theorem blindly

Always verify the pole/convergence conditions.

### Mistake 6 — Forgetting that the ROC cannot contain poles

For standard rational transforms:

[  
\boxed{\text{Poles are never inside the ROC.}}  
]

---

## 30. Problem-Solving Strategy

When solving a Z-transform problem:

1. Identify whether you have a sequence, system, impulse response, or difference equation.
    
2. Write the appropriate Z-transform or known transform pair.
    
3. Determine the ROC.
    
4. Factor the rational expression when useful.
    
5. Identify poles and zeros.
    
6. Determine causality from the ROC.
    
7. Determine stability by checking whether the unit circle is in the ROC.
    
8. If appropriate, evaluate (z=e^{j\omega}) to obtain the frequency response.
    
9. For inverse transforms, use transform pairs, series expansion, or partial fractions.
    
10. Check the result against known properties.
    

---

## 31. Exercises

### Exercise 1 — Direct transform

Find the Z-transform and ROC of

[  
x[n]=(0.8)^nu[n].  
]

### Exercise 2 — Left-sided sequence

Find the Z-transform and ROC of

[  
x[n]=-(0.8)^nu[-n-1].  
]

Explain why its algebraic expression matches Exercise 1.

### Exercise 3 — Delayed impulse

For

[  
x[n]=\delta[n-3],  
]

calculate (X(z)).

### Exercise 4 — Difference equation

Given

[  
y[n]-0.7y[n-1]=x[n],  
]

find:

1. (H(z)),
    
2. the pole,
    
3. the causal ROC,
    
4. the stability condition,
    
5. (h[n]).
    

### Exercise 5 — Pole-zero analysis

For

[  
H(z)=\frac{1-2z^{-1}}{1-0.5z^{-1}},  
]

find:

1. the zero,
    
2. the pole,
    
3. the causal ROC,
    
4. whether the causal system is stable.
    

### Exercise 6 — ROC possibilities

Given

[  
X(z)=  
\frac{1}{  
(1-0.25z^{-1})(1-4z^{-1})  
},  
]

find all possible ROCs and identify the right-sided, left-sided, two-sided, and stable cases.

### Exercise 7 — Frequency response

For

[  
H(z)=1-z^{-1},  
]

calculate (H(e^{j\omega})) and determine the frequency where the response is zero.

### Exercise 8 — Inverse Z-transform

Find the causal inverse Z-transform of

[  
X(z)=  
\frac{1}{  
(1-0.3z^{-1})(1-0.6z^{-1})  
}  
]

with

[  
|z|>0.6.  
]

Use partial fractions.

---

## 32. Mini Project — Digital Filter Analysis

Consider

[  
y[n]=1.2y[n-1]-0.36y[n-2]+x[n].  
]

Perform:

1. Find (H(z)).
    
2. Determine the poles.
    
3. Determine the causal ROC.
    
4. Determine whether the causal filter is stable.
    
5. Calculate (H(e^{j\omega})).
    
6. Explain the effect of pole locations on frequency response.
    
7. Determine (h[n]).
    

This exercise combines the principal concepts of the chapter.

---

## 33. Essential Formula Sheet

### Bilateral Z-transform

[  
\boxed{  
X(z)=\sum_{n=-\infty}^{+\infty}x[n]z^{-n}  
}  
]

### Unilateral Z-transform

[  
\boxed{  
X^+(z)=\sum_{n=0}^{+\infty}x[n]z^{-n}  
}  
]

### Complex variable

[  
\boxed{z=re^{j\omega}}  
]

### Unit circle

[  
\boxed{|z|=1}  
]

### DTFT relationship

[  
\boxed{  
X(e^{j\omega})=X(z)|_{z=e^{j\omega}}  
}  
]

when the unit circle is in the ROC.

### Linearity

[  
\boxed{  
ax_1[n]+bx_2[n]  
\longleftrightarrow  
aX_1(z)+bX_2(z)  
}  
]

### Time shift

[  
\boxed{  
x[n-n_0]  
\longleftrightarrow  
z^{-n_0}X(z)  
}  
]

### Time reversal

[  
\boxed{  
x[-n]\longleftrightarrow X(z^{-1})  
}  
]

### Convolution

[  
\boxed{  
x[n]*h[n]\longleftrightarrow X(z)H(z)  
}  
]

### Transfer function

[  
\boxed{  
H(z)=\frac{Y(z)}{X(z)}  
}  
]

### Frequency response

[  
\boxed{  
H(e^{j\omega})=H(z)|_{z=e^{j\omega}}  
}  
]

### Stability

[  
\boxed{  
|z|=1\text{ must belong to the ROC}  
}  
]

### Causal rational system

[  
\boxed{  
\mathrm{ROC}:\ |z|>\text{magnitude of outermost pole}  
}  
]

### Causal + stable rational system

[  
\boxed{  
|p_k|<1\quad\forall k  
}  
]

### Initial value theorem

[  
\boxed{  
x[0]=\lim_{z\to\infty}X(z)  
}  
]

under appropriate conditions.

### Final value theorem

\lim_{z\to1}(z-1)X(z)  
}  
]

under the appropriate conditions.

### Laplace/Z mapping

[  
\boxed{z=e^{sT}}  
]

---

## 34. Mastery Checklist

Before considering this chapter mastered, you should be able to:

- Define the bilateral Z-transform.
    
- Define the unilateral Z-transform.
    
- Explain why the Z-transform is useful.
    
- Explain (z=re^{j\omega}).
    
- Draw and interpret the z-plane.
    
- Define the ROC.
    
- Explain why the ROC is necessary.
    
- Determine ROCs for basic sequences.
    
- Distinguish right-sided, left-sided, and two-sided sequences.
    
- Use common Z-transform pairs.
    
- Apply linearity.
    
- Apply time shifting.
    
- Apply time reversal.
    
- Apply the convolution property.
    
- Solve linear constant-coefficient difference equations.
    
- Derive transfer functions.
    
- Identify poles and zeros.
    
- Explain pole-zero geometry.
    
- Determine causality from the ROC.
    
- Determine stability from the ROC.
    
- Explain why causal stable rational systems have poles inside the unit circle.
    
- Obtain the DTFT from the Z-transform.
    
- Calculate frequency response.
    
- Compute inverse Z-transforms using partial fractions.
    
- Understand the unilateral transform's role in initial conditions.
    
- Use initial- and final-value theorems correctly.
    
- Distinguish FIR and IIR filters.
    
- Explain the relationship between the z-plane and the Laplace (s)-plane.
    
- Explain how pole-zero locations affect frequency response.
    

---

## 35. Final Perspective

The Z-transform is not merely another formula for representing a discrete-time sequence. It is a complete framework for discrete-time signal and system analysis.

The central relationships are:

[  
\boxed{  
x[n]\overset{\mathcal Z}{\longrightarrow}X(z)  
}  
]

[  
\boxed{  
x[n]*h[n]  
\overset{\mathcal Z}{\longrightarrow}  
X(z)H(z)  
}  
]

[  
\boxed{  
\text{Time delay}  
\longleftrightarrow  
z^{-n_0}  
}  
]

[  
\boxed{  
\text{Difference equation}  
\longrightarrow  
\text{algebraic equation}  
}  
]

[  
\boxed{  
\text{Poles + zeros + ROC}  
\longrightarrow  
\text{system characterization}  
}  
]

[  
\boxed{  
|z|=1  
\longrightarrow  
\text{frequency response}  
}  
]

[  
\boxed{  
\text{Unit circle in ROC}  
\longrightarrow  
\text{BIBO stability}  
}  
]

and, for rational causal systems,

[  
\boxed{  
\text{All poles inside the unit circle}  
\longrightarrow  
\text{causal stability}  
}  
]

The mental model to retain is:

[  
\boxed{  
\text{Sequence}  
\rightarrow  
\text{Z-transform}  
\rightarrow  
\text{ROC}  
\rightarrow  
\text{poles/zeros}  
\rightarrow  
\text{causality + stability}  
\rightarrow  
\text{frequency response}  
}  
]