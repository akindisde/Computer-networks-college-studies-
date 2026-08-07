# Chapter 2: Symmetric Encryption — Block and Stream Ciphers

## 1. The Principle of Symmetric Encryption and Usage Scenarios

Symmetric encryption is one of the central foundations of modern information security. It protects data in networks, files, databases, backups, disks, and secure communication systems. The basic idea is that communicating parties share secret key material and use that secret to transform plaintext into ciphertext.

A symmetric encryption scheme can be described by three algorithms: key generation, encryption, and decryption. We write

$$  
K \leftarrow \operatorname{Gen}(1^\lambda),  
$$

$$  
C \leftarrow \operatorname{Enc}_K(M),  
$$

and

$$  
M \leftarrow \operatorname{Dec}_K(C).  
$$

Correctness requires

$$  
\operatorname{Dec}_K(\operatorname{Enc}_K(M))=M.  
$$

This equation only establishes correctness, not security. Security requires a precise definition of what an adversary can observe and what the adversary should be unable to accomplish.

The central practical difficulty is the shared-key problem. Alice and Bob must possess the same secret key, but an attacker may control or observe their communication channel. Sending the key over that channel would defeat the purpose of encryption. This is one reason modern systems often combine public-key cryptography for authentication and key establishment with symmetric cryptography for efficient bulk encryption.

Symmetric encryption is used both for data in transit and data at rest. However, confidentiality alone is not sufficient for most real systems. An attacker may be unable to read a ciphertext while still being able to modify it. Modern protocols therefore commonly use authenticated encryption, which provides confidentiality and integrity together.

It is also important to distinguish a cryptographic primitive from a complete encryption scheme. AES, for example, is a block cipher operating on fixed-size blocks. It must be combined with a suitable mode of operation to encrypt arbitrary-length data safely.

## 2. The One-Time Pad and Algorithmic Security

The One-Time Pad (OTP) is the classical example of perfect secrecy. Let

$$  
M=m_1m_2\ldots m_n  
$$

be an $n$-bit plaintext and let

$$  
K=k_1k_2\ldots k_n  
$$

be a truly random key of the same length. Encryption is

$$  
C=M\oplus K.  
$$

Because XOR is its own inverse, decryption is

$$  
M=C\oplus K.  
$$

The key must be genuinely random, secret, at least as long as the message, and never reused. When these conditions hold, the OTP provides perfect secrecy:

$$  
\Pr[M=m\mid C=c]=\Pr[M=m].  
$$

An unlimited computationally powerful adversary gains no information about the plaintext from the ciphertext alone.

The price is practicality. Protecting a one-gigabyte message requires one gigabyte of secret random key material. The key must also be securely distributed beforehand.

Key reuse destroys the guarantee. If

$$  
C_1=M_1\oplus K  
$$

and

$$  
C_2=M_2\oplus K,  
$$

then

$$  
C_1\oplus C_2=M_1\oplus M_2.  
$$

The key cancels completely. This is the two-time-pad problem.

The OTP therefore gives us a fundamental distinction between **information-theoretic security** and **computational security**. Modern encryption generally uses the latter: attacks may exist theoretically, but a realistic efficient adversary should not be able to perform them.

## 3. Semantic Security and Fundamental Security Definitions

Saying that an encryption scheme should “hide the message” is not precise enough. Modern cryptography defines security through experiments that specify the adversary's capabilities and objective.

Semantic security informally means that seeing a ciphertext should not allow an efficient adversary to obtain useful information about the plaintext that it could not obtain otherwise. A closely related and operationally convenient formulation is indistinguishability.

An adversary chooses two messages $M_0$ and $M_1$. A challenger selects

$$  
b\leftarrow{0,1}  
$$

and returns

$$  
C\leftarrow\operatorname{Enc}_K(M_b).  
$$

The adversary outputs a guess $b'$. Its advantage is

$$
\left|\Pr[b'=b]-\frac12\right|.  
$$

A secure scheme requires this advantage to be negligible in the security parameter.

A function $\mu(\lambda)$ is negligible if, for every positive polynomial $p(\lambda)$, there is a sufficiently large $\lambda_0$ such that

$$  
\mu(\lambda)<\frac{1}{p(\lambda)}  
$$

for all $\lambda>\lambda_0$.

**IND-CPA**, or indistinguishability under chosen-plaintext attack, gives the adversary access to an encryption oracle. This models situations in which an attacker can influence plaintexts submitted to the encryption system. A secure construction should remain indistinguishable even under this capability.

The security definition must therefore specify the adversary model, the information available to the adversary, the allowed queries, the winning condition, and the acceptable advantage. This formal approach replaces vague claims that a cryptographic algorithm merely “looks secure.”

## 4. PRNGs and Stream Ciphers

The OTP requires a random key as long as the message. A **pseudorandom generator (PRG)** solves the key-expansion problem computationally by expanding a short random seed into a longer sequence that is computationally indistinguishable from random:

$$  
G:{0,1}^{\lambda}\rightarrow{0,1}^{\ell(\lambda)},  
$$

where

$$  
\ell(\lambda)>\lambda.  
$$

A stream cipher uses such a pseudorandom keystream. If

$$  
Z=G(K),  
$$

then

$$  
C=M\oplus Z  
$$

and

$$  
M=C\oplus Z.  
$$

In practical constructions, a nonce is often included:

$$  
Z=G(K,N),  
$$

so that different encryptions under the same key produce different keystreams.

The nonce does not usually need to be secret, but its required property depends on the construction. For many stream ciphers, uniqueness is essential. If the same keystream is reused,

$$  
C_1\oplus C_2=M_1\oplus M_2.  
$$

### LFSR

A Linear Feedback Shift Register generates bits through a recurrence over $\mathrm{GF}(2)$:

$$
c_0s_t\oplus c_1s_{t+1}\oplus\cdots\oplus c_{n-1}s_{t+n-1}.  
$$

LFSRs can produce long-period sequences efficiently, but their linear structure makes them unsuitable as secure stream ciphers by themselves. With enough output, an attacker can reconstruct the recurrence. The Berlekamp–Massey algorithm is a classical example of such analysis.

### RC4

RC4 is a historically important stream cipher based on a key-dependent permutation. It became widespread because of its simplicity and speed, but cryptanalysis revealed significant statistical weaknesses, including biases in its keystream. RC4 is now considered obsolete for modern secure protocols.

### Salsa20 and ChaCha

Salsa20 and its descendant ChaCha use combinations of addition modulo $2^{32}$, XOR, and rotations. ChaCha20 is widely used with Poly1305 in authenticated encryption. These designs demonstrate that simple operations can create strong cryptographic behavior when combined in a carefully designed structure.

## 5. Block Ciphers: Definitions, Principles, and History

A block cipher transforms fixed-size plaintext blocks into fixed-size ciphertext blocks under a secret key. It can be modeled as

$$  
E_K:{0,1}^n\rightarrow{0,1}^n,  
$$

where $E_K$ is a permutation for every key $K$. Decryption is its inverse:

$$  
D_K=E_K^{-1}.  
$$

Thus

$$  
D_K(E_K(M))=M.  
$$

A block cipher does not automatically provide safe encryption of arbitrary-length messages. If identical plaintext blocks are encrypted independently under the same key, identical ciphertext blocks result. This leaks structural information. Modes of operation solve this problem by defining how blocks are combined with previous ciphertext, counters, nonces, or other values.

### DES

DES uses 64-bit blocks and an effective 56-bit key. It employs a 16-round Feistel structure. Its key space contains

$$  
2^{56}  
$$

possibilities, which eventually became small enough for practical exhaustive search as computing technology improved.

### 3DES

Triple DES applies DES three times, commonly in Encrypt-Decrypt-Encrypt form:

$$  
C=E_{K_3}(D_{K_2}(E_{K_1}(M))).  
$$

3DES extended the lifetime of DES but remained slow and retained a 64-bit block size. It is no longer appropriate for modern general-purpose encryption.

### AES

AES was selected by NIST after an international competition and is based on Rijndael. AES has a 128-bit block size and supports 128-, 192-, and 256-bit keys. It uses a substitution-permutation architecture with operations including SubBytes, ShiftRows, MixColumns, and AddRoundKey.

AES uses arithmetic over

$$  
\mathrm{GF}(2^8).  
$$

It is one of the most extensively analyzed symmetric primitives in modern cryptography.

## 6. Building Blocks of Block Ciphers: PRFs, PRPs, Semantic Security, and Feistel Networks

A **pseudorandom function (PRF)** is a keyed function designed to be computationally indistinguishable from a truly random function. An adversary can query the function on chosen inputs and should not be able to determine whether it is interacting with the real keyed function or a random function.

A **pseudorandom permutation (PRP)** is a keyed permutation designed to be indistinguishable from a uniformly random permutation. A block cipher is commonly modeled as a PRP.

A random function may produce collisions, while a permutation cannot:

$$  
x\neq y\Rightarrow P_K(x)\neq P_K(y).  
$$

This distinction is useful in cryptographic proofs.

A Feistel network divides a block into two halves, $(L_i,R_i)$, and performs rounds of the form

$$  
L_{i+1}=R_i  
$$

and

$$  
R_{i+1}=L_i\oplus F_i(R_i).  
$$

The important property is that $F_i$ itself need not be invertible. The overall transformation remains invertible because

$$  
R_i=L_{i+1}  
$$

and

$$  
L_i=R_{i+1}\oplus F_i(L_{i+1}).  
$$

DES is a classic Feistel cipher.

Feistel structures and other block-cipher architectures aim to produce **confusion** and **diffusion**. Confusion complicates the relationship between key and ciphertext, while diffusion spreads the influence of individual plaintext bits. These ideas contribute to the avalanche effect, although avalanche behavior alone is not a formal proof of security.

## 7. AES, DES, and 3DES

DES illustrates the importance of choosing security parameters appropriate to the expected computational environment. Its 56-bit key was eventually insufficient even though the construction had been heavily analyzed.

3DES increased resistance to brute force through repeated DES operations:

$$  
C=E_{K_3}(D_{K_2}(E_{K_1}(M))).  
$$

However, meet-in-the-middle considerations and the 64-bit block size limited its long-term usefulness.

AES provides a substantially stronger modern foundation. AES-128, AES-192, and AES-256 use 128-, 192-, and 256-bit keys respectively, while all operate on 128-bit blocks.

The AES state is a $4\times4$ array of bytes. SubBytes introduces nonlinearity, ShiftRows rearranges byte positions, MixColumns provides diffusion, and AddRoundKey introduces key dependence. The construction is public and has been subjected to extensive cryptanalysis, reflecting the principle that cryptographic security should not depend on algorithm secrecy.

## 8. Key Reuse, Multi-Time Keys, Randomized Encryption, and Nonces

Modern encryption must permit one secret key to protect many messages. This is fundamentally different from the OTP, where the key is consumed after one use.

One approach to preventing deterministic encryption is **randomized encryption**:

$$  
C\leftarrow\operatorname{Enc}_K(M;r),  
$$

where $r$ is fresh randomness. Encrypting the same plaintext twice can then produce different ciphertexts:

$$  
\operatorname{Enc}_K(M;r_1)  
\neq  
\operatorname{Enc}_K(M;r_2).  
$$

Without such variation, an attacker may be able to encrypt guesses independently and compare the results with a target ciphertext.

Modern symmetric schemes frequently use a **nonce**, a value intended to be used once under a particular key. A typical construction is

$$  
C\leftarrow\operatorname{Enc}_K(N,M).  
$$

The nonce normally does not need to be secret and may be transmitted with the ciphertext.

For a stream cipher,

$$  
C=M\oplus G(K,N).  
$$

If a nonce is accidentally reused, the same keystream may be generated and

$$  
C_1\oplus C_2=M_1\oplus M_2.  
$$

Nonce management is therefore a cryptographic security requirement, not a minor implementation detail. Different constructions have different requirements: some require uniqueness, some require unpredictability, and some use deterministic counters.

The security of a multi-use key also depends on the total number of encryptions and the construction's formal security bounds. A key can remain secret while the security of the overall scheme nevertheless degrades as more encryption operations are performed.

## 9. CPA Security and Modes of Operation

In an IND-CPA experiment, an adversary chooses two messages

$$  
M_0,M_1.  
$$

A challenger selects

$$  
b\leftarrow{0,1}  
$$

and returns

$$  
C\leftarrow\operatorname{Enc}_K(M_b).  
$$

The adversary guesses $b'$, and its advantage is

\left|  
\Pr[b'=b]-\frac12  
\right|.  
$$

The advantage should be negligible for every efficient adversary.

### ECB

Electronic Codebook mode encrypts each block independently:

$$  
C_i=E_K(M_i).  
$$

Therefore

$$  
M_i=M_j\Rightarrow C_i=C_j.  
$$

ECB leaks repeated plaintext structure and is unsuitable for general-purpose confidentiality.

### CBC

Cipher Block Chaining uses an initialization vector:

$$  
C_1=E_K(M_1\oplus IV)  
$$

and

$$  
C_i=E_K(M_i\oplus C_{i-1}).  
$$

CBC provides confidentiality under appropriate assumptions but does not provide authentication by itself. Its IV requirements must also be respected.

### CTR

Counter mode generates a keystream from encrypted counter values:

$$  
S_i=E_K(\operatorname{ctr}_i)  
$$

and

$$  
C_i=M_i\oplus S_i.  
$$

The same counter input must never be reused under the same key. Otherwise,

$$  
C_1\oplus C_2=M_1\oplus M_2.  
$$

### Authenticated Encryption and GCM

Modern systems generally require both confidentiality and integrity. Authenticated encryption can be represented as

$$  
(C,T)\leftarrow\operatorname{Enc}_K(N,M,A),  
$$

where $N$ is a nonce and $A$ is associated data that is authenticated but not encrypted.

Decryption verifies the tag before accepting the plaintext:

$$  
M\leftarrow\operatorname{Dec}_K(N,C,A,T).  
$$

GCM combines counter-mode encryption with an authentication mechanism based on finite-field arithmetic. ChaCha20-Poly1305 is another major authenticated-encryption construction.

Authenticated encryption represents an important evolution in symmetric cryptography: the objective is not merely to hide plaintext, but to provide a well-defined confidentiality and integrity guarantee against an explicitly modeled adversary.

## Conclusion

Symmetric cryptography begins with a shared secret and develops into a sophisticated collection of mathematical constructions. The One-Time Pad demonstrates perfect secrecy, while also revealing why perfect secrecy is impractical for most large-scale systems. Pseudorandom generators provide a computational alternative by expanding short keys into long pseudorandom sequences, giving rise to stream ciphers.

Block ciphers provide fixed-size keyed permutations. PRFs and PRPs give us useful abstractions for reasoning about these primitives, while Feistel networks show how invertible structures can be constructed from round functions. DES, 3DES, and AES illustrate the historical evolution of practical block-cipher design and the importance of adapting security parameters to changing computational capabilities.

The key-reuse problem remains central. A secure primitive can become insecure if a keystream, nonce, counter input, or other state is reused incorrectly. This is why nonce and randomness management are part of the cryptographic design itself.

Finally, IND-CPA security gives a rigorous confidentiality goal: an efficient adversary should not be able to distinguish the encryption of chosen messages. This requirement explains the importance of randomized encryption, nonces, and secure modes of operation.

The conceptual progression is

$$  
\text{One-Time Pad}  
\rightarrow  
\text{Computational Security}  
\rightarrow  
\text{PRGs}  
\rightarrow  
\text{Stream Ciphers}  
\rightarrow  
\text{Block Ciphers}  
\rightarrow  
\text{PRFs/PRPs}  
\rightarrow  
\text{Modes of Operation}  
\rightarrow  
\text{IND-CPA Security}.  
$$

The central lesson is that secure symmetric encryption is not merely an algorithm that transforms plaintext into ciphertext. It is a complete construction whose security depends on the primitive, key management, randomness, nonce management, mode of operation, adversary model, and exact security property being claimed.