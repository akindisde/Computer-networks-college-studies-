# Chapter 1: Introduction and History of Cryptography

Cryptography is one of the fundamental disciplines underlying modern information security. It is involved in Internet communications, banking systems, payment cards, mobile phones, private networks, authentication systems, digital signatures, secure messaging, and the protection of stored data. Yet cryptography did not begin with computers. Long before computers, networks, and the Internet existed, people already needed to protect information from individuals who were not supposed to understand or obtain it.

Understanding cryptography therefore requires understanding how it evolved. Early techniques for concealing messages often relied on simple transformations of text. Modern cryptographic systems, by contrast, rely on sophisticated mathematical constructions and precise assumptions about what an adversary can and cannot do. This evolution is not simply a matter of improving old encryption techniques. It represents a fundamental change in how the security problem itself is defined. Modern cryptography does not merely attempt to hide a message. It provides a collection of security services designed to protect information and communication systems against different classes of attacks.

## 1. Elementary Definitions

Before studying cryptographic algorithms, it is essential to establish precise terminology. Many beginner-level misunderstandings arise because different concepts are casually grouped under the general word "cryptography." Encrypting data, computing a cryptographic hash, authenticating a user, and digitally signing a document are different operations that address different security problems.

The word **cryptography** comes from the Ancient Greek _kryptós_, meaning "hidden," and _gráphein_, meaning "to write." Its literal historical meaning is therefore close to "hidden writing." Historically, cryptography primarily referred to techniques for transforming information so that it would not be intelligible to someone who did not possess the information necessary to recover or interpret it.

In its modern sense, cryptography has become much broader. It can be understood as the scientific and technical discipline concerned with designing, analyzing, and using mathematical mechanisms to protect information and communications against adversaries. It relies heavily on mathematics, including algebra, modular arithmetic, number theory, probability, information theory, and computational complexity.

It is important to distinguish **cryptography** from **cryptanalysis**. Cryptography focuses on constructing mechanisms that provide security properties. Cryptanalysis studies methods for analyzing those mechanisms and, when possible, bypassing them, extracting secret information, or demonstrating that they fail to provide their intended security properties. The two disciplines are complementary. A cryptographic construction should not be considered secure merely because it appears complicated; it must withstand appropriate forms of cryptanalytic analysis.

The broader field containing both cryptography and cryptanalysis is generally called **cryptology**. We can therefore think of cryptology as encompassing two complementary activities: cryptography, which designs mechanisms for protection, and cryptanalysis, which studies their weaknesses and the ways in which an adversary might attack them.

To understand the general operation of cryptography, consider Alice, who wants to send information to Bob without an adversary, traditionally called Eve, being able to understand its contents. Alice starts with a message in **plaintext**, which we can denote by $M$. She applies an encryption algorithm using a key $K$. The result is the **ciphertext**, denoted by $C$.

Abstractly, encryption can be represented as:

$$  
C = E_K(M)  
$$

where $E$ represents the encryption algorithm, $K$ is the key, and $M$ is the plaintext. Bob, who possesses the information necessary to recover the message, applies a decryption function:

$$  
M = D_K(C)  
$$

A correctly functioning encryption scheme must allow the original message to be recovered from the ciphertext under the appropriate key. Thus:

$$  
D_K(E_K(M)) = M  
$$

This representation is deliberately abstract. It allows us to understand the basic principle without depending on a particular algorithm. In some systems, Alice and Bob use the same secret key for encryption and decryption. This is called **symmetric cryptography**. In other systems, encryption and decryption involve different but mathematically related keys. This is called **asymmetric cryptography**, or **public-key cryptography**.

The concept of the **key** is central to cryptography. A common beginner misconception is that a cryptographic algorithm itself must remain secret in order for the system to be secure. Modern cryptography takes the opposite approach. The algorithm can, and generally should, be public. Security should primarily depend on the secrecy of the appropriate key. This principle is traditionally associated with **Kerckhoffs's principle**, formulated in the nineteenth century: a cryptographic system should remain secure even if everything about the system, except the secret key, is known to the adversary.

This principle is important because it separates two very different notions: secrecy of the algorithm and secrecy of the key. If a system depends on keeping its algorithm secret, discovering or leaking the algorithm can compromise the entire system. If the algorithm is public and only the key must remain secret, researchers can publish, analyze, test, and audit the algorithm without revealing the secret that actually protects the data.

Cryptography must also be distinguished from **encoding**. Encoding transforms data from one representation into another according to a known rule, usually to facilitate storage, transmission, or interpretation. For example, Base64 represents binary data using a particular set of characters. Base64 is not a confidentiality mechanism: anyone who knows the encoding can decode it. Encryption, in contrast, is intended to prevent an unauthorized party from obtaining the protected information in a meaningful form without the appropriate key.

**Compression** is also fundamentally different from cryptography. Compression attempts to represent information using fewer bits, while cryptography attempts to provide a defined security property. A piece of data can therefore be compressed and then encrypted, but the two operations serve completely different purposes.

We must also distinguish encryption from **cryptographic hash functions**. A hash function takes an input of arbitrary length and produces an output of generally fixed length called a **hash**, **digest**, or **hash value**. We can represent this operation as:

$$  
h = H(M)  
$$

Unlike encryption, a cryptographic hash function is not normally designed to allow the original message $M$ to be recovered from $h$. Hash functions are instead used to provide several security properties, particularly in the areas of integrity, authentication, digital signatures, and password protection. This distinction will become especially important when we study hash functions in detail.

## 2. Objectives and Security Services of Cryptography

Historically, the primary concern of cryptography was often **confidentiality**: Alice wanted to prevent a third party from understanding the contents of her message. In modern information systems, confidentiality remains fundamental, but it is far from sufficient. A secure communication system must generally answer several different security questions.

The first question is: "Who is allowed to read the information?" This concerns **confidentiality**. Confidentiality is the property of preventing unauthorized parties from obtaining meaningful information about protected data. Encryption is one of the primary mechanisms used to provide confidentiality. If Alice sends an encrypted message to Bob and Eve intercepts the ciphertext, Eve should not be able to determine the protected content within the limits of the defined security model.

Confidentiality does not necessarily mean that the existence of the communication itself is secret. A system may protect the contents of a message while still exposing metadata such as the existence of a connection, network addresses, timing information, or approximate message sizes. Protecting content and protecting metadata are therefore separate security problems.

The second question is: "Is the information I received the same information that was sent?" This concerns **integrity**. Integrity is the property of detecting or preventing unauthorized modification of data. Suppose Alice sends Bob an instruction saying "transfer $100." If Eve can modify the message so that Bob receives "transfer $10,000," confidentiality alone clearly does not provide adequate protection.

Integrity is therefore concerned with detecting unauthorized changes to data. Modern cryptographic systems can provide integrity through mechanisms such as **message authentication codes**, or **MACs**, and **authenticated encryption** schemes. Digital signatures can also provide protection against modification while additionally providing a way to associate data with a particular signing key.

A third major property is **authentication**. The term authentication can refer to several related but distinct problems, so it should be used precisely. In communication security, authentication may answer the question: "Did this message actually originate from the entity that claims to have sent it?" In an information system, authentication can instead refer to verifying the identity of a user or device.

This distinction leads to concepts such as **message authentication**, **source authentication**, and **entity authentication**, depending on the context. A cryptographic mechanism must be evaluated according to the exact property it is intended to provide.

Another important property is **non-repudiation**, traditionally associated with digital signatures. In appropriate settings, non-repudiation aims to provide evidence that can associate an operation or statement with a particular entity and make subsequent denial more difficult. However, it is important to be precise: non-repudiation is not simply a mathematical property that automatically appears whenever a digital signature is used. Its meaning and effectiveness depend on the protocol, key management, identity management, trust model, and sometimes legal and organizational procedures.

Cryptographic services can therefore address different needs: protecting confidentiality, preserving integrity, authenticating data or entities, establishing shared secrets, and producing digital signatures. It is essential to distinguish these properties because a particular cryptographic algorithm does not automatically provide all of them.

For example, an encryption mechanism designed only to protect confidentiality does not automatically guarantee integrity. An attacker may sometimes modify ciphertext without knowing the plaintext or the secret key. This is one reason modern cryptography places considerable emphasis on **authenticated encryption**, whose goal is to protect confidentiality and integrity together under a precise security definition.

Another fundamental concept is **secret-key management**. The security of a cryptographic system depends not only on the algorithm being used, but also on how keys are generated, stored, distributed, rotated, backed up, revoked, and destroyed. This collection of problems is generally called **key management**. In practice, an excellent cryptographic primitive combined with poor key management can produce a completely insecure system.

This leads to a crucial idea: cryptography is not merely a collection of algorithms. It is a set of mechanisms integrated into systems that have security goals, users, adversaries, keys, protocols, and assumptions. A cryptographic primitive can be mathematically robust while being used incorrectly inside a real-world protocol.

## 3. Classical Cryptography: History, Evolution, and General Attacks

The history of cryptography is closely connected to the history of secret communication. For centuries, cryptographic techniques were primarily used in military, diplomatic, political, and governmental contexts. The basic problem was often straightforward: an individual or organization needed to send a message to an intended recipient while preventing a third party who could intercept the communication from understanding it.

The earliest known cryptographic techniques generally relied on transformations of text. Some methods changed the positions of symbols, while others replaced symbols with different symbols. These two broad families are traditionally called **transposition** and **substitution**.

In a transposition cipher, the symbols of the message are rearranged rather than necessarily replaced. If the original message contains the sequence

$$  
M = m_1m_2m_3\ldots m_n,  
$$

a transposition produces another sequence containing the same symbols but in a different order:

$$  
C = m_{\pi(1)}m_{\pi(2)}m_{\pi(3)}\ldots m_{\pi(n)},  
$$

where $\pi$ represents a permutation of the positions.

In a substitution cipher, the positions may remain unchanged while individual symbols are replaced according to a rule. Abstractly, this can be represented as:

$$  
C_i = f(M_i).  
$$

One of the best-known historical examples is the **Caesar cipher**, in which each letter of the alphabet is replaced by a letter a fixed number of positions away. If letters are represented by the integers $0$ through $25$, the encryption operation can be written as:

$$  
E_k(x) = (x+k) \bmod 26.  
$$

The decryption operation is:

$$  
D_k(y) = (y-k) \bmod 26.  
$$

The modulo $26$ operation makes the alphabet wrap around when the end is reached. This simple example is already useful mathematically because it demonstrates how an encryption rule can be expressed precisely as a function.

However, the Caesar cipher is extremely weak. Its key space is very small, so an attacker can simply try every possible value of $k$. This is a basic example of a **brute-force attack**, in which the adversary systematically tests possible keys until a plausible result is found.

The weakness of classical systems does not come only from their small key spaces. Natural languages have statistical structure that can also be exploited. In a given language, some letters occur more frequently than others, and certain sequences of letters occur more frequently than others. A monoalphabetic substitution preserves much of this statistical structure. An attacker can therefore analyze the frequencies of symbols in the ciphertext and attempt to infer the substitution mapping.

This technique is known as **frequency analysis**. It was historically important because it demonstrated that a cipher can fail even when the attacker does not simply test every possible key. Instead, the attacker can exploit statistical properties of the underlying message.

Cryptographers subsequently developed more sophisticated systems designed to reduce or obscure these patterns. **Polyalphabetic ciphers** represent an important step in this evolution. Instead of using one substitution throughout the entire message, they use several transformations according to the position of the character or according to a sequence controlled by a key.

The **Vigenère cipher** is a famous historical example. In a numerical representation of the alphabet, a simplified version of its encryption rule can be written as:

$$  
C_i = (M_i + K_i) \bmod 26.  
$$

The value $K_i$ depends on the position $i$ and on a key that is repeated or extended according to a defined rule. This makes straightforward frequency analysis less effective, but it does not provide modern security. Statistical properties and repetitions in the key can still be exploited.

The evolution of classical cryptography therefore demonstrates a general phenomenon: **cryptographic improvements often lead to new forms of cryptanalysis**. Cryptography and cryptanalysis evolve together. A system that appears difficult to attack using the methods available at one point in history may become vulnerable after a new mathematical, statistical, or computational technique is developed.

This dynamic became particularly visible during the twentieth century with the development of electromechanical cipher machines. Rotor-based systems, of which **Enigma** is the most famous historical example, could produce transformations considerably more complex than simple manual substitutions. The cryptanalysis of these systems played a major role in the history of World War II and contributed to the development of systematic approaches to cryptanalysis.

The analysis of historical cryptographic systems also contributed to the development of algorithmic thinking. When a system becomes complex, it is no longer enough to intuitively guess letter substitutions. The system must be modeled formally, its observable properties must be identified, and systematic procedures must be developed to search for weaknesses.

To understand attacks in general, it is useful to reason in terms of an **adversary model**. An adversary does not always have the same capabilities. In some situations, the attacker can only observe ciphertexts. In other situations, the attacker knows some plaintexts corresponding to observed ciphertexts. In stronger scenarios, the attacker may be able to choose messages and request that the system encrypt them, or may be able to submit selected ciphertexts to a decryption mechanism and observe the results.

This leads to several classical attack models. In a **known-ciphertext scenario**, the attacker has ciphertexts and attempts to extract information from them. In a **known-plaintext attack**, the attacker possesses plaintext-ciphertext pairs $(M,C)$. In a **chosen-plaintext attack**, the attacker can choose messages and observe their encryptions. In a **chosen-ciphertext attack**, the attacker can select ciphertexts and observe the corresponding decryptions, subject to whatever restrictions the system imposes.

These models are important because an algorithm should not be considered secure merely because it survives a weak adversary. A cryptographic system operating over a network must generally assume that an attacker can observe, capture, replay, modify, reorder, and sometimes actively interact with messages. Modern cryptography therefore explicitly models the adversary's capabilities instead of assuming that the attacker can only passively read traffic.

Attacks can also be classified according to their goals. An adversary may attempt to recover a secret key, recover a particular plaintext, distinguish encrypted data from random data, forge a valid message, modify a communication without detection, or obtain some other unauthorized advantage. Cryptanalysis should therefore not be reduced to "breaking the key."

This point is particularly important. A system can be vulnerable even if an attacker never manages to recover the secret key. Suppose an attacker can modify a ciphertext in a predictable way and cause a corresponding change in the decrypted message. If the system does not detect this modification, a serious security failure exists even though the key remains secret.

Historical attacks therefore taught cryptographers that security cannot be evaluated merely by asking whether a message appears sufficiently "scrambled." A ciphertext may look meaningless to a human while still being vulnerable to an efficient algorithm. Modern cryptography attempts to replace this intuition with precise definitions, explicit adversary models, and rigorous security analysis.

One of the most important lessons of classical cryptography is therefore that **apparent obscurity is not a measure of security**. A cryptographic system should be evaluated according to the information available to an adversary, the computational resources required for attacks, the mathematical structure of the construction, and the security properties it is actually supposed to provide.

## 4. Modern Cryptography: New Aspects and Objectives

Modern cryptography emerged as the problem of information protection began to be formulated in mathematical and scientific terms. The objective was no longer simply to construct a mechanism complicated enough to prevent a human reader from understanding a message. Instead, cryptographers began to define precisely what an adversary could do, what the adversary should not be able to accomplish, and under what assumptions the system could be considered secure.

The arrival of computers produced a fundamental change. Computers can perform enormous numbers of operations quickly. A transformation that appears complicated to a human can be evaluated millions or billions of times by a machine. Conversely, a modern cryptographic system can use key spaces so large that exhaustive search becomes computationally infeasible with realistic resources.

The notion of **cryptographic strength** is therefore closely connected to the resources required to perform an attack. It is not enough to ask whether an attack exists theoretically. We must also consider its computational complexity and its requirements in terms of time, memory, data, hardware, and access to the target system.

Modern cryptography also introduced a much clearer distinction between **cryptographic primitives** and **cryptographic protocols**. A primitive is a mathematical mechanism designed to provide a particular security property. Examples include block ciphers, stream ciphers, cryptographic hash functions, MACs, digital signatures, and cryptographically secure pseudorandom generators. A protocol combines primitives with communication rules and state-management procedures to achieve a larger security objective.

This distinction is crucial in practice. A protocol can be vulnerable even when every cryptographic primitive it uses is individually secure. The problem may lie in how those primitives are combined, how keys are negotiated, how messages are ordered, how identities are verified, or how failures are handled. Security therefore has to be analyzed at the protocol level as well as at the primitive level.

Modern cryptography also greatly expanded the set of objectives being pursued. Confidentiality remains important, but it is now accompanied by integrity, authentication, secure key establishment, digital signatures, proof of possession, access-control mechanisms, secure randomness, and many other security goals.

**Symmetric cryptography** is one of the major modern families. In this model, communicating parties share a secret key, or use secret keys derived from a shared secret. A symmetric encryption scheme can be represented abstractly as:

$$  
C = E_K(M),  
$$

followed by:

$$  
M = D_K(C).  
$$

Block ciphers such as **AES** are major examples of this family. Symmetric cryptography is highly efficient and is therefore essential for protecting large quantities of data in real systems.

The second major family is **asymmetric cryptography**, also called **public-key cryptography**. It uses a pair of keys: a **public key**, which can be distributed openly, and a **private key**, which must remain secret. This separation enables solutions to problems that are difficult to address directly with symmetric cryptography, particularly key establishment and digital signatures.

Public-key cryptography fundamentally changed the field because two parties can perform certain cryptographic operations without first sharing a secret key over a secure channel. **Diffie–Hellman key exchange**, for example, allows two parties to establish a shared secret based on public parameters and private values.

However, public-key cryptography does not eliminate key-management problems. Instead, it changes their nature. The private key must be protected, while a public key must be reliably associated with the expected identity or role. This problem is central to **public-key infrastructures**, or **PKIs**, and to digital certificates.

Modern cryptography is also deeply concerned with formal security definitions. A cryptographic construction is typically studied within a model in which the adversary's capabilities and objective are explicitly defined. For example, for an encryption scheme, one might require that an attacker cannot distinguish the encryption of one chosen message from the encryption of another chosen message, even after obtaining certain auxiliary information.

This leads to concepts such as **indistinguishability under chosen-plaintext attack**, commonly abbreviated **IND-CPA**. A stronger notion, **IND-CCA**, considers an adversary with certain decryption capabilities. These definitions illustrate the fundamental difference between classical and modern cryptography. Rather than asking only whether "the message is hidden," modern cryptography asks whether an adversary can obtain a precisely defined advantage despite precisely defined capabilities.

Another essential aspect is **cryptographic randomness**. Many modern systems depend on random or pseudorandom values that are unpredictable to an attacker. Keys, protocol nonces, initialization values, salts, and other security-critical values may depend on randomness. A weakness in random-number generation can therefore compromise an otherwise mathematically sound cryptographic system.

Modern cryptography must also account for implementation. An algorithm can be secure within its mathematical model while its implementation remains vulnerable. **Side-channel attacks** are an important example. An attacker may exploit execution time, power consumption, electromagnetic emissions, cache behavior, or other physical characteristics to obtain information about secret keys.

This leads to an important distinction between **algorithmic security** and **system security**. Algorithmic security concerns the mathematical properties of the cryptographic construction. System security also includes the implementation, key management, protocol design, interfaces, operating system, hardware, user behavior, and physical environment.

Modern cryptography must also account for changing computational capabilities. Key sizes and cryptographic parameters must be selected to provide sufficient security for the expected lifetime of the system. A key size that provides an acceptable security margin today may eventually become inadequate as computational technology advances.

This issue has become especially important with the development of **quantum computing**. Certain quantum algorithms, most notably **Shor's algorithm**, threaten important families of currently deployed public-key cryptography, particularly systems based on integer factorization and discrete logarithms. **Grover's algorithm**, meanwhile, affects the complexity of certain exhaustive searches and must be considered when evaluating the security of symmetric primitives.

These developments have led to **post-quantum cryptography**, whose goal is to construct cryptographic mechanisms that remain secure against attackers equipped with sufficiently powerful quantum computers. This development illustrates an important characteristic of cryptography: cryptographic security is not static. It must evolve as the capabilities of adversaries evolve.

Modern cryptography therefore extends far beyond the simple idea of "hiding a message." It is a discipline concerned with constructing mechanisms that protect confidentiality, detect unauthorized modification, authenticate entities and data, establish shared secrets, produce digital signatures, and secure protocols against adversaries whose capabilities are explicitly modeled.

The historical evolution of cryptography reveals a fundamental transformation. Classical cryptography often focused on making a message sufficiently difficult to understand. Modern cryptography takes a much more rigorous approach: **define the desired security property, model the adversary, construct an appropriate primitive or protocol, analyze its resistance to relevant attacks, and account for the conditions under which it will actually be deployed.**

## 5. Chapter Summary

Cryptography evolved from a collection of historical techniques for concealing messages into a mathematical and computer-science discipline concerned with precise security properties. This evolution was accompanied by a corresponding evolution in cryptanalysis. Attacks are no longer limited to guessing a message or testing a small set of possible keys. They can exploit statistical properties, mathematical structure, protocol interactions, implementation behavior, and physical characteristics of systems.

The basic concepts of plaintext, ciphertext, key, encryption, decryption, cryptography, cryptanalysis, and cryptology form the vocabulary needed for further study. Encryption must be distinguished from encoding, compression, and hashing because these mechanisms serve fundamentally different purposes.

Modern cryptography addresses several security objectives, including confidentiality, integrity, and authentication. It uses multiple families of primitives, including symmetric cryptography, public-key cryptography, cryptographic hash functions, MACs, and digital signatures. These primitives are integrated into protocols that must be analyzed under explicit adversary models.

The most important principle to carry forward is that **modern cryptography does not depend on an algorithm merely looking complicated or remaining secret. It depends on public and well-analyzed constructions, properly managed keys, explicit assumptions, precise security definitions, and rigorous analysis of the adversary's capabilities.** This way of thinking will form the foundation for the chapters that follow.