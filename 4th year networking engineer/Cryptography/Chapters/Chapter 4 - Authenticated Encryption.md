# Chapter 4: Authenticated Encryption

Authenticated encryption is the natural combination of the two major goals developed in the preceding chapters: confidentiality and integrity. Encryption attempts to prevent an adversary from learning information about a plaintext, while message authentication attempts to prevent an adversary from modifying or fabricating messages without detection. In a modern cryptographic protocol, these properties are usually required simultaneously.

This chapter introduces the stronger adversarial model of **chosen-ciphertext attacks (CCA)** and explains why protecting against passive observation or chosen-plaintext attacks is not sufficient for many real systems. We review MACs and cryptographic hash functions, then formalize CCA security and authenticated encryption. We study the principal ways encryption and authentication can be composed, including **MAC-then-encrypt**, **MAC-and-encrypt**, and **Encrypt-then-MAC**. We then examine practical authenticated-encryption modes such as **GCM, CCM, and EAX**, before studying historical and practical attack examples involving **TLS, CBC padding, and SSH**.

The central theme is that secure cryptography depends not only on the security of individual primitives but also on the exact way those primitives are composed and exposed to an adversary.

---

## 1. The Chosen-Ciphertext Attack Problem: Passive and Active Attacks

The simplest cryptographic adversary is passive. A passive adversary observes ciphertexts transmitted between legitimate parties but does not alter them. The primary objective of the attacker is to extract information about the underlying plaintext.

For an encryption scheme

$$  
C\leftarrow\operatorname{Enc}_K(M),  
$$

a passive adversary may obtain one or more ciphertexts $C$ and attempt to determine information about $M$.

The security requirement traditionally associated with this setting is confidentiality. Under a modern indistinguishability formulation, the adversary should not be able to distinguish the encryption of two chosen messages with more than negligible advantage.

A stronger attacker is active. An active attacker can manipulate the communication channel. It may intercept ciphertexts, replace them, inject new ciphertexts, replay old ciphertexts, or deliberately construct malformed ciphertexts.

This creates a fundamentally different security problem.

Suppose a receiver possesses a decryption mechanism

$$  
M=\operatorname{Dec}_K(C).  
$$

If the attacker submits a modified ciphertext

$$  
C'  
$$

and the system returns some information about the result of decryption, the attacker has gained access to a potentially powerful cryptographic interface.

The attacker may not know the secret key, but the receiver is effectively performing computations under that key on the attacker's behalf.

This is the essence of the **chosen-ciphertext attack (CCA)** model.

A chosen-ciphertext adversary is allowed to submit ciphertexts of its choice to a decryption oracle and observe the resulting information. The goal is to determine whether the challenge ciphertext encrypts one message or another.

This model is stronger than the chosen-plaintext model because the adversary can interact with the decryption side of the system.

The security consequences are significant. An encryption scheme can be secure against passive observation and even secure against chosen-plaintext attacks while failing under chosen-ciphertext attacks.

This is not merely a theoretical concern. Real network protocols frequently expose decryption behavior indirectly. A server might respond differently when a ciphertext has invalid padding, an invalid authentication tag, an invalid sequence number, or an invalid internal structure. These differences can become an oracle.

### Passive Attacks

A passive attacker observes communication without changing it.

The attacker may see

$$  
C_1,C_2,\ldots,C_q  
$$

and attempt to infer information about

$$  
M_1,M_2,\ldots,M_q.  
$$

Encryption security is primarily concerned with preventing this type of information leakage.

### Active Attacks

An active attacker can modify or inject data.

The attacker may transform

$$  
C\rightarrow C'  
$$

and send $C'$ to the receiver.

The most dangerous situation occurs when the receiver reveals whether the modified ciphertext was accepted or rejected, or when different types of rejection can be distinguished.

A single-bit distinction can sometimes be enough to construct a powerful oracle.

### Chosen-Ciphertext Attacks

In a formal CCA experiment, the adversary receives access to a decryption oracle

$$  
\mathcal{D}_K(C)=\operatorname{Dec}_K(C).  
$$

The adversary chooses two messages

$$  
M_0,M_1  
$$

and the challenger chooses a secret bit

$$  
b\leftarrow{0,1}.  
$$

The challenger returns

$$  
C^*\leftarrow\operatorname{Enc}_K(M_b).  
$$

The adversary can continue querying the decryption oracle on ciphertexts of its choice, except for the challenge ciphertext $C^*$ itself.

Finally, the adversary outputs a guess $b'$.

The adversary's advantage can be written as

$$
\left|  
\Pr[b'=b]-\frac12  
\right|.  
$$

A CCA-secure encryption scheme requires this advantage to be negligible for every efficient adversary.

The exclusion of the exact challenge ciphertext is necessary. If the adversary were allowed to submit $C^*$ directly to the decryption oracle, it would simply receive the plaintext and immediately learn $b$.

The restriction therefore models a realistic attacker who can manipulate and submit related ciphertexts but cannot simply ask the system to decrypt the exact challenge.

### Why This Model Matters

CCA security captures the fact that decryption itself can become an attack surface.

A cryptographic implementation must therefore not merely decrypt valid ciphertexts correctly. It must also ensure that malformed or manipulated ciphertexts do not reveal information that helps an attacker distinguish, recover, or modify plaintexts.

This is one of the principal reasons authenticated encryption became the dominant design paradigm for modern secure communication.

---

## 2. Review of MACs and Cryptographic Hash Functions

Before defining authenticated encryption, it is useful to review the two primitives that form much of its conceptual foundation: MACs and cryptographic hash functions.

A **MAC** is a keyed authentication mechanism. Given a secret key $K$ and message $M$, the sender computes

$$  
T=\operatorname{MAC}_K(M).  
$$

The receiver computes the same value and accepts the message only when verification succeeds.

The fundamental security goal is resistance to forgery. An attacker who can obtain valid tags for chosen messages should still be unable to produce a valid tag for a new message.

This property is generally formalized as **existential unforgeability under chosen-message attack**, or EUF-CMA security.

The attacker can query

$$  
M_1,M_2,\ldots,M_q  
$$

and receive

$$  
T_i=\operatorname{MAC}_K(M_i).  
$$

The attack succeeds if the adversary produces

$$  
(M^_,T^_)  
$$

such that

$$  
\operatorname{Verify}_K(M^_,T^_)=1  
$$

for a message $M^*$ that was not previously submitted.

A MAC provides integrity and authentication, but not confidentiality.

A cryptographic hash function is different. It is an unkeyed function

$$  
H:{0,1}^*\rightarrow{0,1}^n.  
$$

It maps arbitrary-length messages to fixed-length outputs.

Important security properties include preimage resistance, second-preimage resistance, and collision resistance.

Preimage resistance requires that given

$$  
y=H(M),  
$$

it be computationally infeasible to find a message $M'$ such that

$$  
H(M')=y.  
$$

Second-preimage resistance requires that, given $M$, it be difficult to find $M'\neq M$ satisfying

$$  
H(M')=H(M).  
$$

Collision resistance requires that it be difficult to find any pair

$$  
M\neq M'  
$$

such that

$$  
H(M)=H(M').  
$$

For an ideal $n$-bit hash function, generic collision attacks require approximately

$$  
2^{n/2}  
$$

work because of the birthday paradox, while generic preimage attacks require approximately

$$  
2^n.  
$$

A hash function alone does not authenticate a message because there is no secret. Anyone can compute

$$  
H(M).  
$$

A MAC adds a secret key:

$$  
T=\operatorname{MAC}_K(M).  
$$

This distinction becomes crucial when constructing authenticated encryption.

Authenticated encryption effectively combines the confidentiality provided by encryption with an integrity mechanism so that an active attacker cannot meaningfully manipulate ciphertexts without detection.

---

## 3. CCA Security and the Definition of Authenticated Encryption

### CCA Security

The chosen-ciphertext security experiment is a stronger version of the indistinguishability experiment.

The challenger generates a key

$$  
K\leftarrow\operatorname{Gen}(1^\lambda).  
$$

The adversary receives access to a decryption oracle

$$  
\mathcal{D}_K(C).  
$$

The adversary chooses two equal-length messages $M_0$ and $M_1$. A random bit is selected:

$$  
b\leftarrow{0,1}.  
$$

The challenger returns

$$  
C^*=\operatorname{Enc}_K(M_b).  
$$

The adversary may continue querying the decryption oracle on ciphertexts other than $C^*$.

If the adversary cannot determine $b$ with non-negligible advantage, the encryption scheme is considered **IND-CCA secure**.

The key insight is that the adversary can interact with decryption. Therefore, the security definition captures attacks involving malformed ciphertexts and related ciphertexts rather than only attacks based on passive observation.

### Authenticated Encryption

Authenticated encryption, commonly abbreviated **AE**, is an encryption mechanism that simultaneously provides confidentiality and integrity.

A basic authenticated-encryption scheme can be modeled as

$$  
(C,T)\leftarrow\operatorname{Enc}_K(N,M,A),  
$$

where:

- $K$ is the secret key;
    
- $N$ is a nonce or initialization value;
    
- $M$ is the plaintext;
    
- $A$ is associated data;
    
- $C$ is the ciphertext;
    
- $T$ is the authentication tag.
    

The receiver performs

$$  
M\leftarrow\operatorname{Dec}_K(N,C,A,T).  
$$

If verification fails, the result is a rejection symbol, commonly written

$$  
\perp.  
$$

Thus,

$$  
\operatorname{Dec}_K(N,C,A,T)\in{M,\perp}.  
$$

This is an important conceptual improvement over encryption systems that expose partially decrypted plaintext or distinguish multiple failure conditions.

### Associated Data

Authenticated encryption often authenticates data that should remain visible but must not be modified.

For example, a network packet may contain:

$$  
A=\text{header}  
$$

and

$$  
M=\text{payload}.  
$$

The header may need to remain unencrypted because routers or protocol components need to read it. However, changing it must be detected.

Authenticated encryption therefore protects

$$  
(C,T)  
$$

while authenticating both $M$ and $A$.

This model is commonly called **AEAD**, meaning **Authenticated Encryption with Associated Data**.

### Semantic Security of Authenticated Encryption

Authenticated encryption must provide a confidentiality property against active attackers.

Informally, the attacker should not be able to distinguish encryptions of chosen messages even while interacting with the decryption interface, except for the fact that invalid ciphertexts are rejected.

The integrity component is equally important. If an attacker modifies

$$  
(C,T)  
\rightarrow  
(C',T'),  
$$

the receiver should reject the modified ciphertext unless it was legitimately generated under the secret key.

The combination creates a stronger system-level security goal:

$$  
\text{Confidentiality}  
+  
\text{Integrity}  
\rightarrow  
\text{Authenticated Encryption}.  
$$

However, the security of authenticated encryption is not simply the sum of the security of encryption and the security of a MAC. The composition must be designed carefully.

---

## 4. Composition Methods: MAC-Then-Encrypt, MAC-and-Encrypt, and Encrypt-Then-MAC

Before dedicated AEAD modes became common, designers frequently combined separate encryption and authentication primitives.

There are several possible orders, and they are **not equivalent**.

### MAC-Then-Encrypt

In **MAC-then-encrypt**, the sender first computes a tag over the plaintext:

$$  
T=\operatorname{MAC}_{K_M}(M).  
$$

The sender then encrypts both:

$$  
C=\operatorname{Enc}_{K_E}(M\parallel T).  
$$

The receiver decrypts:

$$  
M\parallel T=\operatorname{Dec}_{K_E}(C)  
$$

and then checks

$$  
T\stackrel{?}{=}\operatorname{MAC}_{K_M}(M).  
$$

This construction appears superficially reasonable because the MAC is protected by encryption.

However, the receiver must decrypt before it can determine whether the authentication tag is valid. If decryption or padding validation leaks information before the MAC is checked, the attacker may obtain a useful oracle.

This was historically important in protocols using CBC encryption.

The major lesson is that **authentication occurring after decryption does not automatically protect the decryption process itself**.

### MAC-and-Encrypt

In **MAC-and-encrypt**, the plaintext is encrypted and the MAC is computed independently, for example:

$$  
C=\operatorname{Enc}_{K_E}(M)  
$$

and

$$  
T=\operatorname{MAC}_{K_M}(M).  
$$

The transmitted object is

$$  
(C,T).  
$$

This construction exposes the tag directly. The ciphertext provides confidentiality while the MAC provides integrity of the plaintext.

However, security depends on exactly what is authenticated, what information is revealed, whether encryption and authentication use independent keys, and how verification and decryption are ordered.

The construction should therefore not be assumed secure simply because both primitives are individually secure.

### Encrypt-Then-MAC

In **Encrypt-then-MAC (EtM)**, the plaintext is first encrypted:

$$  
C=\operatorname{Enc}_{K_E}(M),  
$$

and then the ciphertext is authenticated:

$$  
T=\operatorname{MAC}_{K_M}(C).  
$$

The receiver first verifies

$$  
\operatorname{Verify}_{K_M}(C,T)  
$$

and only if verification succeeds does it decrypt:

$$  
M=\operatorname{Dec}_{K_E}(C).  
$$

This ordering is particularly powerful because an invalid ciphertext is rejected before decryption takes place.

Thus, the decryption operation is not exposed to arbitrary attacker-controlled ciphertexts unless those ciphertexts possess a valid authentication tag.

Under appropriate assumptions, Encrypt-then-MAC provides a clean route to strong confidentiality and integrity guarantees.

The general principle is:

$$  
\boxed{  
\text{Authenticate the ciphertext before decrypting it.}  
}  
$$

This does not mean that every implementation of Encrypt-then-MAC is automatically secure. The precise construction, key separation, nonce management, tag verification, and error handling still matter.

### Key Separation

When encryption and authentication are separate primitives, it is generally preferable to use independent keys:

$$  
K_E\neq K_M.  
$$

These keys can be derived from a master secret using a secure key-derivation mechanism.

Using independent keys simplifies security analysis and prevents unexpected interactions between the encryption and authentication primitives.

---

## 5. Authenticated-Encryption Modes: GCM, CCM, and EAX

Dedicated authenticated-encryption modes integrate encryption and authentication into a single standardized construction.

Three historically important examples are **GCM, CCM, and EAX**.

### GCM

**Galois/Counter Mode (GCM)** combines counter-mode encryption with polynomial authentication over a binary finite field.

For encryption, GCM uses a block cipher such as AES in counter mode:

$$  
C_i=M_i\oplus E_K(J_i),  
$$

where $J_i$ represents a sequence of counter-derived inputs.

The authentication component is based on a hash-like function called **GHASH**, operating over

$$  
\mathrm{GF}(2^{128}).  
$$

At a high level, GHASH processes the ciphertext and associated data using multiplication in the finite field.

The authentication tag can be represented abstractly as

$$  
T=\operatorname{GHASH}_H(A,C)\oplus E_K(J_0),  
$$

where $H$ is derived from the AES key and $J_0$ is a nonce-dependent initial value.

The exact GCM specification contains additional formatting and length-processing details, but the important conceptual structure is that confidentiality comes from counter-mode encryption while integrity comes from a polynomial authenticator.

GCM is widely used because it supports high performance and parallel processing.

However, **nonce reuse is catastrophic**.

If the same nonce is used with the same key, counter-mode encryption can reuse the same keystream:

$$  
C_1=M_1\oplus S  
$$

and

$$  
C_2=M_2\oplus S.  
$$

Therefore,

$$  
C_1\oplus C_2=M_1\oplus M_2.  
$$

Nonce reuse also compromises the authentication security of GCM because the attacker can obtain algebraic information about the authentication key used by GHASH.

Consequently, GCM implementations must guarantee the required nonce uniqueness conditions.

### CCM

**Counter with CBC-MAC (CCM)** combines counter-mode encryption with CBC-MAC authentication.

Conceptually,

\text{CTR encryption}  
+  
\text{CBC-MAC authentication}.  
$$

The CBC-MAC component authenticates the plaintext and associated data, while counter mode encrypts the resulting plaintext.

CCM therefore combines two classical constructions into a unified authenticated-encryption mode.

CCM has been used extensively in constrained and embedded environments, including wireless and low-power systems.

Like other nonce-based authenticated-encryption schemes, CCM requires careful nonce management. The nonce contributes to the uniqueness of the encryption process and authentication computation.

### EAX

**EAX** is an authenticated-encryption construction based on CTR-mode encryption and a MAC derived from OMAC/CMAC-like processing.

At a high level, EAX computes a ciphertext

$$  
C=\operatorname{CTR}_K(M)  
$$

and combines authentication components for the nonce, associated data, and ciphertext.

The final tag can be viewed abstractly as

$$  
T=T_N\oplus T_A\oplus T_C.  
$$

The construction is historically important because it provides a clean example of how encryption and authentication can be composed while explicitly authenticating associated data.

### Comparison

GCM is based on a counter-mode encryption layer and GHASH authentication. CCM combines CTR mode with CBC-MAC. EAX combines CTR encryption with a MAC construction derived from OMAC.

All three illustrate the same high-level design goal:

$$  
\text{confidentiality}  
+  
\text{ciphertext integrity}  
+  
\text{associated-data integrity}.  
$$

The exact security properties and implementation requirements differ, but all require careful treatment of nonces, keys, tags, and verification failures.

---

## 6. Security of Authenticated Encryption and the Importance of Verification

An authenticated-encryption system should not merely output plaintext whenever decryption mathematically produces a value. It should first establish that the ciphertext is authentic.

Conceptually, decryption is

$$  
M=\operatorname{Dec}_K(N,C,A,T)  
$$

only if the tag is valid.

Otherwise,

$$  
\operatorname{Dec}_K(N,C,A,T)=\perp.  
$$

The symbol $\perp$ represents rejection.

This seemingly simple rule has deep security consequences.

Suppose a receiver decrypts an invalid ciphertext and then checks the tag. If the receiver's behavior leaks information about the intermediate plaintext, an attacker may be able to use the receiver as a decryption oracle.

The ideal behavior is therefore conceptually:

$$  
\boxed{  
\text{verify}  
\rightarrow  
\text{accept or reject}  
\rightarrow  
\text{release plaintext only after acceptance}  
}  
$$

The implementation should also avoid distinguishable error messages that reveal whether failure resulted from padding, authentication, formatting, sequence numbers, or another internal condition.

This is particularly important in network protocols where the attacker can submit a large number of carefully constructed ciphertexts and observe server responses.

---

## 7. Examples of Attacks: TLS, CBC Padding, and SSH

### TLS and the Evolution of Secure Record Protection

**Transport Layer Security (TLS)** provides confidentiality and integrity for network communication. Its history contains several important lessons about authenticated encryption and cryptographic composition.

Older versions of TLS commonly used constructions based on MACs combined with symmetric encryption. Some cipher suites used CBC-mode encryption with MAC-then-encrypt processing.

This architecture created a difficult interaction between encryption, padding, authentication, and error handling.

A TLS record could conceptually contain

$$  
M\parallel \operatorname{MAC}(M)\parallel\operatorname{padding}.  
$$

The complete structure was then encrypted using CBC.

The receiver had to decrypt the record, process the padding, recover the MAC, and verify it.

If an attacker could distinguish different failure conditions, the server could become an oracle.

This class of problems contributed to several practical attacks and ultimately reinforced the migration toward authenticated-encryption cipher suites.

Modern TLS deployments strongly favor AEAD constructions such as AES-GCM and ChaCha20-Poly1305.

The lesson is not simply that CBC is "bad." CBC can be used securely under appropriate conditions. The deeper problem is the interaction among CBC encryption, padding validation, MAC verification, error handling, and attacker-controlled ciphertexts.

### CBC Padding Attacks

CBC encryption operates on blocks:

$$  
C_i=E_K(P_i\oplus C_{i-1}),  
$$

with the corresponding decryption relation

$$  
P_i=D_K(C_i)\oplus C_{i-1}.  
$$

Because the previous ciphertext block is XORed into the decrypted block, an attacker can deliberately modify $C_{i-1}$ and influence the resulting plaintext block $P_i$.

Suppose

$$  
P_i=D_K(C_i)\oplus C_{i-1}.  
$$

If the attacker replaces $C_{i-1}$ with

$$  
C'_{i-1}=C_{i-1}\oplus\Delta,  
$$

then the new plaintext becomes

D_K(C_i)\oplus C'_{i-1}.  
$$

Therefore,

$$  
P'_i=P_i\oplus\Delta.  
$$

The attacker does not know $P_i$, but can control its difference.

By itself, this does not necessarily reveal the plaintext. The danger arises when the receiver reveals whether the resulting plaintext has valid padding.

### The Padding Oracle

Suppose a receiver returns one observable result when padding is valid and another when padding is invalid.

The attacker can manipulate ciphertext blocks and repeatedly submit them.

If the receiver's response reveals whether the padding was valid, the attacker obtains a one-bit oracle about the decrypted plaintext.

Repeated queries can recover plaintext bytes.

The classic **Vaudenay padding-oracle attack** demonstrated how CBC padding validation could be exploited when the receiver exposed a distinguishable padding-validity signal.

The key point is that the attacker does not need the encryption key.

The attacker exploits the system's behavior as a decryption oracle.

This is a direct illustration of the CCA model introduced at the beginning of the chapter.

### Why Authentication Prevents This

If the receiver authenticates the ciphertext before decrypting it, the attacker cannot freely modify ciphertexts and obtain useful decryption behavior.

For Encrypt-then-MAC,

$$  
T=\operatorname{MAC}_{K_M}(C),  
$$

the receiver first verifies $T$.

If the attacker modifies

$$  
C\rightarrow C',  
$$

the original tag no longer verifies:

$$  
\operatorname{MAC}_{K_M}(C')  
\neq T  
$$

except with negligible probability.

The receiver rejects the ciphertext before decryption.

This eliminates the attack surface that allowed arbitrary ciphertext modifications to reach the CBC decryption and padding-validation stages.

### SSH and Active Attacks

**Secure Shell (SSH)** provides secure remote login and related secure communication capabilities. Its history also provides valuable examples of the challenges involved in designing secure packet-processing protocols.

Early SSH constructions used encryption and integrity mechanisms whose exact ordering and packet-format behavior created opportunities for active attacks.

A particularly important family of SSH attacks concerns the relationship between packet lengths, encrypted packet structure, sequence numbers, and integrity verification.

A secure protocol must ensure that attackers cannot use malformed packets to obtain useful information about internal processing.

Modern SSH protocol designs incorporate stronger integrity mechanisms and encryption modes, but the general lesson remains the same: the cryptographic primitive cannot be analyzed independently from the packet format and protocol state machine.

The attacker interacts with the complete system, not with an isolated AES invocation.

### The General Lesson from TLS, CBC, and SSH

These examples demonstrate the same fundamental principle:

$$  
\boxed{  
\text{A cryptographic primitive can be secure while its protocol composition is insecure.}  
}  
$$

A block cipher can be secure while CBC padding handling creates an oracle. A MAC can be secure while it is applied in an unsafe order. An encryption scheme can satisfy a chosen-plaintext security definition while failing when the receiver exposes a decryption interface.

Security therefore has to be analyzed at the level of the complete construction.

---

# Conclusion

Authenticated encryption emerged from the realization that confidentiality and integrity cannot safely be treated as unrelated afterthoughts.

A passive attacker may only observe ciphertext, but an active attacker can manipulate and inject ciphertexts. Once the attacker can submit chosen ciphertexts to a decryption system and observe its responses, the system must resist the much stronger **chosen-ciphertext attack** model.

CCA security formalizes this threat. The adversary receives a decryption oracle and attempts to distinguish encryptions of chosen messages. A secure construction must prevent the decryption interface from becoming a source of useful information.

MACs provide integrity and authentication, while hash functions provide fixed-length digests and properties such as collision resistance. Combining encryption with authentication requires careful composition.

The three classical composition strategies are:

$$  
\text{MAC-Then-Encrypt},  
$$

$$  
\text{MAC-and-Encrypt},  
$$

and

$$  
\text{Encrypt-Then-MAC}.  
$$

They are not equivalent. In particular, Encrypt-then-MAC has an important structural advantage because the receiver can reject an unauthenticated ciphertext before decrypting it.

Dedicated authenticated-encryption modes implement this general objective more systematically. GCM combines counter-mode encryption with GHASH authentication, CCM combines CTR mode with CBC-MAC, and EAX combines CTR encryption with a MAC-based authentication mechanism.

Nonce management is critical in all nonce-based authenticated-encryption schemes. Reusing a nonce under the same key can destroy confidentiality and, depending on the construction, authentication security as well.

The historical examples of TLS, CBC padding oracles, and SSH demonstrate why these principles matter in practice. Attackers do not interact with abstract security definitions; they interact with implementations, network protocols, error messages, packet formats, and state machines.

The conceptual progression of the chapter is

$$  
\text{Passive Attacks}  
\rightarrow  
\text{Active Attacks}  
\rightarrow  
\text{Chosen-Ciphertext Attacks}  
\rightarrow  
\text{MACs and Hash Functions}  
\rightarrow  
\text{CCA Security}  
\rightarrow  
\text{Authenticated Encryption}  
\rightarrow  
\text{Composition Methods}  
\rightarrow  
\text{AEAD Modes}  
\rightarrow  
\text{Real-World Attacks}.  
$$

The central lesson is that **confidentiality without integrity is insufficient against active attackers**. Modern cryptographic systems therefore aim to ensure that attacker-controlled ciphertexts cannot reach sensitive decryption logic without first passing authentication. Authenticated encryption is not simply "encryption plus a MAC"; it is a carefully designed security abstraction whose correctness depends on the primitive, composition method, nonce discipline, key separation, verification order, error handling, and adversarial model.