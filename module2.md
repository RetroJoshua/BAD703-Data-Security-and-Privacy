# Module 2: PRNGs and Public Key Cryptography

**Course:** Cryptography and Network Security  
**Primary Reference:** William Stallings, *Cryptography and Network Security*, 7th Edition  
**Textbook Focus:** Chapters 1, 3, 4, 8, 9, 10  
**Audience:** Final-year Engineering Students  
**Purpose:** Exam-oriented lecture notes with mathematical grounding, worked examples, and comparison of major public-key systems

---

## Learning Objectives

By the end of this module, students should be able to:

1. Explain why randomness is essential in cryptography.
2. Distinguish between true random number generators and pseudo-random number generators.
3. State the security requirements of a cryptographically strong PRNG.
4. Explain the public-key cryptography paradigm and trapdoor one-way functions.
5. Perform RSA key generation, encryption, and decryption computations.
6. Describe the Diffie-Hellman key exchange and its security basis.
7. Explain the mathematical idea behind elliptic curve cryptography and its security advantage.
8. Compare RSA, Diffie-Hellman, and ECC in terms of security, efficiency, and key sizes.

---

## 1. Introduction

Modern cryptography depends on both **randomness** and **asymmetric key techniques**.

- Random bits are required for session keys, nonces, initialization vectors, salts, and prime generation.
- Public-key cryptography solves the key distribution problem by separating encryption and decryption keys.

This module combines the foundational ideas behind random bit generation with the main public-key systems used in practice.

---

## 2. Randomness in Cryptography

### 2.1 Why Randomness Matters
Randomness is essential for:

- generating session keys,
- choosing primes in RSA,
- producing nonces and challenges,
- preventing replay and prediction attacks.

Poor randomness can destroy the security of an otherwise strong algorithm.

### 2.2 TRNG, PRNG, and CSPRNG

| Term | Meaning | Example Source | Security Note |
|---|---|---|---|
| TRNG | True random number generator | Thermal noise, quantum effects | Hard to predict, but may be slow |
| PRNG | Pseudo-random number generator | Deterministic algorithm from a seed | Not secure unless designed carefully |
| CSPRNG | Cryptographically secure PRNG | Uses a secure construction and seed | Suitable for cryptography |

### 2.3 Security Requirements of a PRNG
A cryptographic PRNG must satisfy:

- **Statistical randomness**: output should not show simple patterns.
- **Forward unpredictability**: knowing past output should not reveal future bits.
- **Backward unpredictability**: knowing output should not reveal the seed or internal state.
- **Seed sensitivity**: small changes in the seed should produce very different sequences.

### 2.4 Linear Congruential Generator (LCG)
A simple PRNG is the linear congruential generator:

\[
X_{n+1} = (aX_n + c) \bmod m
\]

where \(a\), \(c\), and \(m\) are constants.

#### Properties
- Very fast and easy to implement.
- Useful in simulation.
- **Not secure for cryptographic use** because future outputs can often be predicted once some values are known.

### 2.5 Blum Blum Shub (BBS)
BBS is a cryptographically strong generator based on the hardness of factoring.

#### Algorithm
1. Choose two large primes \(p\) and \(q\) such that \(p \equiv q \equiv 3 \pmod 4\).
2. Compute \(n = pq\).
3. Choose a seed \(x_0\) relatively prime to \(n\).
4. Iterate:

\[
x_{i+1} = x_i^2 \bmod n
\]

5. Output one or more low-order bits of each state.

#### Why It Is Secure
Recovering earlier states or predicting future states is related to factoring \(n\), which is computationally difficult for sufficiently large values.

### 2.6 Exam Point
A common exam distinction is:

- **PRNG**: deterministic and repeatable from a seed.
- **Cryptographically secure PRNG**: deterministic internally, but computationally unpredictable to an attacker.

---

## 3. Public Key Cryptography Fundamentals

### 3.1 Why Public Key Cryptography Was Needed
In symmetric cryptography, both parties must already share a secret key. That creates a distribution problem.

Public-key cryptography solves this by using:

- a **public key** that can be widely distributed,
- a **private key** that remains secret.

### 3.2 Main Uses
Public-key systems are used for:

- key establishment,
- encryption,
- digital signatures,
- authentication.

In practice, public-key methods are often used to establish a symmetric session key, because symmetric encryption is faster for large data.

### 3.3 Trapdoor One-Way Function
A function \(f(x)\) is:

- easy to compute,
- hard to invert,
- easy to invert only if a secret trapdoor is known.

This is the mathematical basis of public-key cryptography.

### 3.4 Important Misconceptions

1. **Public-key is not automatically more secure than symmetric cryptography.**  
   Security depends on the mathematical problem and key size.

2. **Public-key does not replace symmetric cryptography.**  
   Public-key methods are slower and typically used for key exchange or signatures.

3. **Public-key methods still need randomness.**  
   Weak randomness can compromise key generation and padding.

### 3.5 Exam Point
A strong answer should mention that public-key cryptography is based on **hard mathematical problems** such as factoring and discrete logarithms.

---

## 4. RSA Algorithm

RSA is the most famous public-key algorithm and the standard textbook example of trapdoor one-way cryptography.

### 4.1 Security Basis
RSA security is based on the difficulty of factoring the product of two large primes.

If an attacker can factor \(n = pq\), the private key can be derived.

### 4.2 Mathematical Foundations
For RSA:

- \(n = pq\), where \(p\) and \(q\) are large primes.
- \(\phi(n) = (p-1)(q-1)\)
- Choose \(e\) such that \(\gcd(e, \phi(n)) = 1\)
- Find \(d\) such that:

\[
ed \equiv 1 \pmod{\phi(n)}
\]

The public key is \((e, n)\), and the private key is \((d, n)\).

### 4.3 RSA Key Generation Algorithm

```text
Input: Security parameters / required key size
1. Select two large random primes p and q.
2. Compute n = p × q.
3. Compute φ(n) = (p − 1)(q − 1).
4. Choose e such that 1 < e < φ(n) and gcd(e, φ(n)) = 1.
5. Compute d such that e·d ≡ 1 (mod φ(n)).
6. Public key = (e, n); Private key = (d, n).
```

### 4.4 RSA Encryption and Decryption

Given plaintext message \(M\) with \(0 \le M < n\):

\[
C = M^e \bmod n
\]

To decrypt:

\[
M = C^d \bmod n
\]

### 4.5 Worked Example
Let:

- \(p = 3\), \(q = 11\)
- \(n = 33\)
- \(\phi(n) = 20\)
- choose \(e = 3\)

Now find \(d\) such that:

\[
3d \equiv 1 \pmod{20}
\]

The solution is \(d = 7\), because \(3 \times 7 = 21 \equiv 1 \pmod{20}\).

If \(M = 2\):

\[
C = 2^3 \bmod 33 = 8
\]

Decrypt:

\[
M = 8^7 \bmod 33 = 2
\]

### 4.6 Why RSA Works
Since \(ed \equiv 1 \pmod{\phi(n)}\), there exists an integer \(k\) such that:

\[
ed = 1 + k\phi(n)
\]

Using Euler’s theorem, for messages relatively prime to \(n\), encryption followed by decryption returns the original message.

### 4.7 Practical Issues with RSA

- Large key sizes are needed for good security.
- RSA is slow compared with symmetric algorithms.
- Padding is essential in real systems.
- Raw RSA should never be used directly on arbitrary long messages.

### 4.8 Exam Point
If asked what protects RSA, answer: **the hardness of factoring large composite numbers**.

---

## 5. Diffie-Hellman Key Exchange

Diffie-Hellman allows two parties to establish a shared secret over an insecure channel without prior shared secret material.

### 5.1 Security Basis
It is based on the **discrete logarithm problem**:

Given \(\alpha^x \bmod q\), finding \(x\) is difficult when parameters are large.

### 5.2 Protocol Steps
Let \(q\) be a large prime and \(\alpha\) a primitive root modulo \(q\).

1. Alice chooses a private value \(X_A\), computes:

\[
Y_A = \alpha^{X_A} \bmod q
\]

2. Bob chooses a private value \(X_B\), computes:

\[
Y_B = \alpha^{X_B} \bmod q
\]

3. Alice and Bob exchange \(Y_A\) and \(Y_B\).
4. Alice computes:

\[
K = (Y_B)^{X_A} \bmod q
\]

5. Bob computes:

\[
K = (Y_A)^{X_B} \bmod q
\]

Both values are equal to:

\[
\alpha^{X_A X_B} \bmod q
\]

### 5.3 Why the Keys Match
Because exponentiation is associative in this modular form:

\[
(Y_B)^{X_A} = (\alpha^{X_B})^{X_A} = \alpha^{X_A X_B}
\]

and similarly for Bob.

### 5.4 Small Numerical Example
Take:

- \(q = 23\)
- \(\alpha = 5\)
- Alice chooses \(X_A = 6\)
- Bob chooses \(X_B = 15\)

Then:

\[
Y_A = 5^6 \bmod 23 = 8
\]

\[
Y_B = 5^{15} \bmod 23 = 19
\]

Shared key:

\[
K = 19^6 \bmod 23 = 2
\]

and Bob obtains the same result.

### 5.5 Man-in-the-Middle Vulnerability
Basic Diffie-Hellman does **not** authenticate the parties. An attacker can intercept and replace public values, creating separate keys with Alice and Bob. This is why authenticated key exchange is necessary in real systems.

### 5.6 Exam Point
The standard answer is: **Diffie-Hellman establishes a shared secret, but by itself it does not provide authentication**.

---

## 6. Elliptic Curve Cryptography (ECC)

ECC is a public-key approach based on algebraic structures of elliptic curves over finite fields.

### 6.1 Why ECC Is Important
ECC provides equivalent security with much smaller key sizes than RSA.

Typical comparisons:

- 160-bit ECC ≈ 1024-bit RSA
- 256-bit ECC ≈ 3072-bit RSA

Smaller keys mean:

- faster computation,
- less memory use,
- less bandwidth,
- better suitability for mobile and embedded systems.

### 6.2 Curve Equation
A common elliptic curve over a prime field is:

\[
y^2 = x^3 + ax + b \pmod p
\]

with conditions ensuring the curve is non-singular.

### 6.3 Point Addition and Doubling
The group operation is defined geometrically.

- **Point addition**: the line through two points intersects the curve at a third point; reflection gives the sum.
- **Point doubling**: when both points are the same, use the tangent line at that point.

### 6.4 Scalar Multiplication
The operation used in ECC is:

\[
Q = kP
\]

meaning point \(P\) is added to itself \(k\) times.

This operation is easy to perform, but reversing it is hard.

### 6.5 Security Basis
ECC relies on the **Elliptic Curve Discrete Logarithm Problem (ECDLP)**:

Given \(P\) and \(Q = kP\), finding \(k\) is computationally infeasible for large parameters.

### 6.6 Why ECC Is Efficient
Because the best known attacks on ECDLP are less efficient than the best known attacks on factoring for similar security levels, ECC can use smaller keys for the same security margin.

### 6.7 Exam Point
The major advantage of ECC is **smaller key size with comparable security**, not that it is “more secure” in an absolute sense.

---

## 7. Comparison of Public-Key Systems

| Feature | RSA | Diffie-Hellman | ECC |
|---|---|---|---|
| Main purpose | Encryption / signatures | Key exchange | Encryption / signatures / key exchange |
| Hard problem | Integer factoring | Discrete logarithm | Elliptic-curve discrete logarithm |
| Key size | Large | Large | Small |
| Speed | Slower | Moderate | Fast for same security level |
| Authentication | Supported with signatures | Not inherent | Supported in ECC-based schemes |

---

## 8. PRNG and Public-Key Interdependence

Randomness and public-key cryptography are tightly connected.

- RSA key generation requires random primes.
- Diffie-Hellman requires random private exponents.
- ECC key generation requires random private scalars.
- Padding schemes and nonce generation require unpredictable randomness.

A weak PRNG can compromise even mathematically strong public-key algorithms.

---

## 9. Common Exam Questions and Key Answers

1. **Why is randomness important in RSA?**  
   It is required for prime generation and secure padding.

2. **What is a trapdoor one-way function?**  
   A function easy to compute but hard to invert unless a secret trapdoor is known.

3. **What problem underlies RSA security?**  
   Factoring large integers.

4. **What problem underlies Diffie-Hellman security?**  
   The discrete logarithm problem.

5. **What problem underlies ECC security?**  
   The elliptic curve discrete logarithm problem.

6. **Why is Diffie-Hellman vulnerable to MITM attacks?**  
   Because it lacks authentication.

7. **Why is ECC preferred in constrained devices?**  
   Because it achieves comparable security with much smaller keys.

---

## 10. Summary

- Cryptography requires both secure algorithms and secure randomness.
- PRNGs are deterministic but can still be cryptographically secure if designed well.
- Public-key cryptography solves the key distribution problem using trapdoor one-way functions.
- RSA is based on factoring; Diffie-Hellman is based on discrete logarithms; ECC is based on elliptic-curve discrete logarithms.
- In modern systems, public-key cryptography is usually used to establish symmetric keys rather than encrypt large data directly.

---

## 11. Revision Questions

1. Differentiate between TRNG, PRNG, and CSPRNG.
2. Explain the security weakness of a linear congruential generator.
3. Derive the RSA decryption exponent for a small example.
4. Explain why RSA encryption and decryption are inverse operations.
5. Show how two users derive the same Diffie-Hellman shared key.
6. What is the role of a primitive root in Diffie-Hellman?
7. Compare RSA and ECC in terms of key size and computational cost.
8. Why is authentication needed in practical Diffie-Hellman systems?

---

## 12. Final Exam-Oriented Takeaways

- Do not confuse **randomness** with **unpredictability**; cryptography requires both.
- RSA, Diffie-Hellman, and ECC depend on different mathematical hardness assumptions.
- Public-key cryptography is a tool for solving the key distribution problem, not a replacement for all symmetric cryptography.
- ECC is attractive because of its compact keys and efficiency, especially for mobile and embedded applications.
- In exam answers, always mention the underlying hard problem and the practical advantage or limitation of each algorithm.
