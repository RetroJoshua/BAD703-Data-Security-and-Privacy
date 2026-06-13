[🏠 Home](README.md) | [📅 Schedule](schedule.md) | [📖 M1](module1.md) | [📖 M2](module2.md) | [📖 M3](module3.md) | [📖 M4](module4.md) | [📖 M5](module5.md) | [📝 Assignments](assignments.md) | [📚 Resources](resources.md)

---

# Module 1: Classical Encryption and Block Ciphers/DES

**Course:** Cryptography and Network Security  
**Primary Reference:** William Stallings, *Cryptography and Network Security*, 7th Edition  
**Textbook Focus:** Chapters 1, 3, 4, 8, 9, 10  
**Audience:** Final-year Engineering Students  
**Purpose:** Exam-oriented lecture notes with conceptual clarity, worked examples, and algorithmic detail

---

## Learning Objectives

By the end of this module, students should be able to:

1. Distinguish between classical and modern encryption ideas.
2. Explain and apply Caesar, Playfair, Hill, and polyalphabetic ciphers.
3. Compare cryptography with steganography.
4. Describe the architecture and security goals of block ciphers.
5. Explain the Feistel structure and why it enables decryption using the same framework as encryption.
6. Describe the DES algorithm, including its initial permutation, round function, S-boxes, key schedule, and final permutation.
7. Solve basic exam-style problems on classical ciphers and DES.

---

## 1. Introduction

Cryptography is the science of protecting information by transforming it into a form that is unintelligible to unauthorized users. In Stallings’ treatment, the subject develops from **classical ciphers** to **modern symmetric ciphers** built from carefully designed mathematical structures.

This module focuses on two broad themes:

- **Classical encryption techniques**: Caesar, Playfair, Hill, and polyalphabetic ciphers.
- **Symmetric block cipher design**: general block cipher principles and the Data Encryption Standard (DES).

Although classical ciphers are historically important and commonly asked in examinations, they are insecure by modern standards. DES, on the other hand, is historically significant because it introduced many design ideas that influenced later ciphers such as 3DES and AES.

---

## 2. Core Concepts in Cryptography

### 2.1 Confidentiality
Confidentiality ensures that only authorized parties can read the message.

### 2.2 Substitution and Transposition
Cryptographic techniques traditionally fall into two categories:

- **Substitution**: Replace each element of plaintext with another element.
- **Transposition**: Rearrange the positions of the plaintext symbols without changing the symbols themselves.

Many practical systems combine both concepts to achieve **confusion** and **diffusion**.

### 2.3 Confusion and Diffusion
Stallings emphasizes two important design goals in symmetric ciphers:

- **Confusion**: Make the relationship between key and ciphertext complex.
- **Diffusion**: Spread plaintext statistics across the ciphertext so patterns are hidden.

These ideas are central to block cipher design.

---

## 3. Caesar Cipher

The Caesar cipher is the simplest substitution cipher. Each letter is shifted by a fixed amount.

### 3.1 Encryption and Decryption
Let the letters be numbered as:

\[
a=0,\; b=1,\; \dots,\; z=25
\]

For a shift key \(k\):

\[
C = (P + k) \bmod 26
\]
\[
P = (C - k) \bmod 26
\]

where:
- \(P\) is plaintext symbol value,
- \(C\) is ciphertext symbol value.

### 3.2 Example
Encrypt **HELLO** with key \(k=3\):

- H → K
- E → H
- L → O
- L → O
- O → R

Ciphertext: **KHOOR**

### 3.3 Security Analysis
The Caesar cipher has only 26 possible shifts, and one of them is trivial. Hence the effective key space is tiny.

**Weaknesses:**
- Easy brute force attack.
- Frequency analysis works quickly.
- Preserves language statistics.

### 3.4 Exam Point
A Caesar cipher is a **monoalphabetic substitution cipher** with a shift key. Its security is practically nonexistent.

---

## 4. Playfair Cipher

The Playfair cipher encrypts plaintext two letters at a time, making it a **digraph substitution cipher**.

### 4.1 Why Playfair Was Better Than Simple Substitution
In monoalphabetic ciphers, single-letter frequency is exposed. Playfair hides single-letter frequency better because letters are encrypted in pairs.

### 4.2 Construction of the 5 × 5 Matrix
Steps:

1. Choose a keyword.
2. Remove repeated letters.
3. Fill the remaining cells with the rest of the alphabet.
4. Combine I/J in one cell (standard convention).

#### Example Keyword: `MONARCHY`

| M | O | N | A | R |
|---|---|---|---|---|
| C | H | Y | B | D |
| E | F | G | I/J | K |
| L | P | Q | S | T |
| U | V | W | X | Z |

### 4.3 Preparing Plaintext
Before encryption:

- Split the message into digraphs.
- If a pair has the same letter, insert a filler letter, usually **X**.
- If the last pair is incomplete, append a filler letter.

Example:

`BALLOON` → `BA LX LO ON`  
because the repeated `LL` is separated by `X`.

### 4.4 Encryption Rules
For each pair:

1. **Same row**: replace each letter with the one to its right.
2. **Same column**: replace each letter with the one below it.
3. **Rectangle rule**: replace each letter with the letter in the same row but the other letter’s column.

### 4.5 Worked Example
Encrypt the pair **HI** using the matrix above.

- H is at row 2, col 2
- I/J is at row 3, col 4

They form a rectangle.

So:
- H → letter in H’s row and I’s column = **I/J**
- I/J → letter in I’s row and H’s column = **F**

Hence **HI → IF**

### 4.6 Strengths and Weaknesses

**Strengths:**
- Much better than Caesar.
- Hides single-letter frequencies.
- Increases the number of possible digraph mappings.

**Weaknesses:**
- Still vulnerable to digraph frequency analysis.
- Language structure remains partly visible.
- Not secure by modern standards.

### 4.7 Exam Point
The Playfair cipher is a classical **polygraphic substitution cipher** operating on digraphs.

---

## 5. Hill Cipher

The Hill cipher is a matrix-based polygraphic substitution cipher that uses linear algebra over modular arithmetic.

### 5.1 Idea
A block of plaintext letters is represented as a vector, and encryption is performed using matrix multiplication modulo 26.

### 5.2 Encryption Formula
If \(P\) is an \(m\)-component plaintext vector and \(K\) is an \(m \times m\) key matrix:

\[
C = K P \pmod{26}
\]

Decryption uses the inverse matrix:

\[
P = K^{-1} C \pmod{26}
\]

The key matrix must be invertible modulo 26.

### 5.3 Condition for Invertibility
A matrix \(K\) is valid only if:

\[
\gcd(\det(K), 26) = 1
\]

Otherwise the inverse does not exist modulo 26.

### 5.4 Example with a 2 × 2 Key
Let

\[
K = \begin{bmatrix}
3 & 3 \\
2 & 5
\end{bmatrix}
\]

and plaintext **HI**.

Using \(a=0, b=1, \dots, z=25\):

\[
H=7,\quad I=8
\]

So

\[
P = \begin{bmatrix}7\\8\end{bmatrix}
\]

Encrypt:

\[
C = \begin{bmatrix}
3 & 3 \\
2 & 5
\end{bmatrix}
\begin{bmatrix}7\\8\end{bmatrix}
=
\begin{bmatrix}45\\54\end{bmatrix}
\equiv
\begin{bmatrix}19\\2\end{bmatrix} \pmod{26}
\]

This maps to **T C**.

### 5.5 Security Considerations

**Advantages:**
- Better diffusion than simple substitution.
- Encrypts multiple letters together.

**Weaknesses:**
- Linear structure makes it vulnerable to known-plaintext attacks.
- If enough plaintext-ciphertext pairs are known, the key matrix can be recovered.

### 5.6 Exam Point
The Hill cipher is elegant mathematically, but its linear algebraic structure makes it weak against attack.

---

## 6. Polyalphabetic Ciphers

Polyalphabetic ciphers use multiple substitution alphabets to reduce the usefulness of frequency analysis.

### 6.1 Vigenère Cipher
The Vigenère cipher uses a repeating keyword to determine a sequence of Caesar shifts.

If plaintext symbols are \(P_i\) and key symbols are \(K_i\):

\[
C_i = (P_i + K_i) \bmod 26
\]

#### Example
Plaintext: `ATTACKATDAWN`  
Keyword: `LEMONLEMONLE`

Each letter is shifted by the corresponding keyword letter.

### 6.2 Kasiski Examination and Frequency Attack
The Vigenère cipher is not immune to attack:

- Repeated keyword length creates periodic patterns.
- Kasiski examination and Friedman test can help estimate the key length.
- Once the key length is known, the cipher can be broken by solving multiple Caesar ciphers.

### 6.3 Vernam Cipher
The Vernam cipher combines plaintext and key using XOR operations on bits.

\[
C = P \oplus K
\]

### 6.4 One-Time Pad (OTP)
The One-Time Pad is a special Vernam cipher with strict conditions:

1. Key is truly random.
2. Key length equals message length.
3. Key is used only once.
4. Key remains completely secret.

### 6.5 Why OTP Is Perfectly Secure
For a given ciphertext, every plaintext of the same length is equally probable if the key is uniformly random and used only once. This gives **information-theoretic security**.

### 6.6 Practical Limitation
The OTP is almost impossible to use at scale because of key generation, storage, and secure distribution requirements.

### 6.7 Exam Point
Remember: **Vigenère improves security, but OTP provides perfect secrecy** under ideal conditions.

---

## 7. Steganography

Steganography is the practice of hiding the existence of a message rather than hiding its meaning.

### 7.1 Cryptography vs Steganography

| Feature | Cryptography | Steganography |
|---|---|---|
| Goal | Hide meaning | Hide existence |
| Output | Ciphertext | Apparently normal media |
| Detection | Obvious encrypted form | Harder to notice |

### 7.2 Common Techniques

- **Text-based methods**: invisible ink, spacing, formatting, character marking.
- **Image-based methods**: least significant bit (LSB) insertion.
- **Audio/video methods**: small changes in samples or frames.

### 7.3 Least Significant Bit Method
In digital images, the last bit of pixel values can be modified with minimal visual change.

Example:
- Pixel value 100 = `01100100`
- Change LSB to encode a bit → `01100101` = 101

The visual effect is usually negligible.

### 7.4 Advantages and Limitations

**Advantages:**
- Conceals the presence of the message.
- Useful when secrecy of communication itself is important.

**Limitations:**
- Limited payload capacity.
- Vulnerable if the embedding method is discovered.
- Compression, resizing, or filtering may destroy the hidden message.

### 7.5 Exam Point
Steganography complements cryptography; it does not replace it.

---

## 8. Block Ciphers

A block cipher is a symmetric cipher that transforms a fixed-size block of plaintext into a ciphertext block of the same size.

### 8.1 Block Cipher vs Stream Cipher

| Feature | Block Cipher | Stream Cipher |
|---|---|---|
| Unit of processing | Fixed-size blocks | Bit/byte stream |
| Error propagation | Often higher | Usually lower |
| Examples | DES, AES | RC4, stream constructions |

### 8.2 Requirements of a Good Block Cipher
A strong block cipher should provide:

- Large key space.
- High avalanche effect.
- Resistance to brute force and differential/linear attacks.
- Efficient implementation in hardware/software.

### 8.3 Feistel Structure
Many block ciphers use a Feistel network.

#### Round Equations
Split the input block into left and right halves:

\[
L_i = R_{i-1}
\]
\[
R_i = L_{i-1} \oplus F(R_{i-1}, K_i)
\]

where:
- \(F\) is the round function,
- \(K_i\) is the subkey for round \(i\).

### 8.4 Why Feistel Is Important
The same structure can be used for encryption and decryption, with subkeys applied in reverse order. The round function \(F\) does not need to be invertible.

### 8.5 Confusion and Diffusion in Block Ciphers
- **Confusion** is provided by substitution and nonlinear components.
- **Diffusion** is provided by permutations and mixing across rounds.

### 8.6 Exam Point
A Feistel cipher remains invertible even if \(F\) is not invertible. This is a very common exam question.

---

## 9. DES: Data Encryption Standard

DES is the landmark 64-bit block cipher standardized in the 1970s and widely studied for its architecture.

### 9.1 Basic Parameters
- Block size: **64 bits**
- Effective key size: **56 bits**
- Number of rounds: **16**
- Structure: **Feistel network**

### 9.2 DES Overview
DES applies:

1. Initial permutation
2. 16 Feistel rounds
3. Final permutation (inverse initial permutation)

### 9.3 DES Encryption Algorithm

```text
Input: 64-bit plaintext block, 56-bit effective key
1. Apply Initial Permutation (IP) to the plaintext block.
2. Split the block into L0 and R0, each 32 bits.
3. For i = 1 to 16 do:
      Li = Ri-1
      Ri = Li-1 XOR F(Ri-1, Ki)
4. Swap the halves after round 16.
5. Apply IP^-1 to produce the ciphertext.
```

### 9.4 The DES Round Function F
For a 32-bit right half and a 48-bit subkey:

1. **Expansion permutation (E)**: expand 32 bits to 48 bits.
2. XOR with the round key \(K_i\).
3. Split into eight 6-bit blocks.
4. Each 6-bit block enters an S-box, producing 4 bits.
5. Apply a permutation \(P\) to the resulting 32-bit output.

### 9.5 Role of the S-Boxes
The S-boxes are the nonlinear heart of DES.

For each 6-bit input:
- Outer bits determine the row.
- Inner four bits determine the column.

The S-box outputs 4 bits. This nonlinear substitution provides resistance to cryptanalysis.

### 9.6 Key Schedule
DES does not use the 56-bit key directly in each round. Instead:

- The 64-bit key includes 8 parity bits.
- A permutation choice table removes parity bits.
- The 56 bits are split into two 28-bit halves.
- Left shifts are applied across rounds.
- Another permutation choice compresses to 48-bit round keys.

### 9.7 Why DES Was Important
DES showed how carefully designed substitution, permutation, and iterative round processing could produce strong practical encryption.

### 9.8 Why DES Is No Longer Secure
DES is vulnerable mainly because of its short effective key length.

- A 56-bit key is too small for modern brute-force search.
- The algorithmic design is strong, but the key size is inadequate.

### 9.9 Avalanche Effect
A small change in the plaintext or key should produce a large, unpredictable change in the ciphertext. DES was designed to exhibit this property.

### 9.10 Exam Point
If asked why DES is insecure today, the key answer is: **56-bit key length, not weak round structure**.

---

## 10. Worked Examples

### 10.1 Caesar Cipher
Plaintext: `HELLO`  
Key: 3  
Ciphertext: `KHOOR`

### 10.2 Hill Cipher Matrix Calculation
Let

\[
K = \begin{bmatrix}3 & 3\\2 & 5\end{bmatrix}
\]

Plaintext vector for `HI`:

\[
P = \begin{bmatrix}7\\8\end{bmatrix}
\]

Then

\[
C = KP \bmod 26 = \begin{bmatrix}19\\2\end{bmatrix}
\]

which corresponds to **TC**.

### 10.3 DES Round Intuition
If the right half of a block changes by even one bit, the expansion, XOR, S-box substitution, and permutation can cause many output bits to change. This is the avalanche effect in action.

---

## 11. Summary Table

| Topic | Main Idea | Typical Weakness |
|---|---|---|
| Caesar Cipher | Shift alphabet by fixed amount | Tiny key space |
| Playfair Cipher | Digraph substitution using 5 × 5 matrix | Digraph frequency leakage |
| Hill Cipher | Matrix encryption over mod 26 | Linear, known-plaintext attack |
| Polyalphabetic Ciphers | Multiple substitution alphabets | Key repetition patterns |
| Steganography | Hide the existence of a message | Limited capacity, fragile |
| Block Ciphers | Fixed-size block transformation | Depends on design and key size |
| DES | 16-round Feistel block cipher | 56-bit key too short |

---

## 12. Common Exam Questions and Key Answers

1. **Why is Caesar cipher insecure?**  
   Because the key space is very small and the cipher preserves frequency patterns.

2. **How does Playfair handle repeated letters in a digraph?**  
   Insert a filler such as X between the repeated letters.

3. **When does a Hill cipher matrix have an inverse?**  
   When the determinant is relatively prime to 26.

4. **Why is OTP perfectly secure?**  
   Because each ciphertext corresponds to all plaintexts with equal probability, assuming a random one-time key.

5. **What is the role of S-boxes in DES?**  
   They provide the nonlinearity required for confusion.

6. **Why is Feistel structure useful?**  
   It allows decryption with the same basic algorithm as encryption.

7. **Why is DES obsolete today?**  
   Its 56-bit key is vulnerable to brute-force attack.

---

## 13. Revision Questions

1. Explain the difference between substitution and transposition ciphers.
2. Encrypt a short phrase using the Caesar cipher with a chosen key.
3. Describe the three Playfair cipher rules for same row, same column, and rectangle cases.
4. Why must a Hill cipher key matrix be invertible modulo 26?
5. Compare Vigenère cipher and One-Time Pad.
6. Define steganography and give one digital method of embedding hidden data.
7. Explain the Feistel round equations in a block cipher.
8. Describe the main steps of DES encryption.
9. Why are DES S-boxes considered the most critical component?
10. Distinguish between confusion and diffusion in modern cryptographic design.

---

## 14. Final Exam-Oriented Takeaways

- Classical ciphers are historically important but cryptographically weak.
- The Hill cipher is mathematically elegant but linearly vulnerable.
- Polyalphabetic ciphers improve resistance to frequency analysis, but repeating keys still leak structure.
- Steganography hides the **existence** of data, not just its meaning.
- DES is the classic example of a well-designed Feistel block cipher, but its short key makes it insecure today.
- In exam answers, always connect **security strength** to **key size**, **nonlinearity**, and **statistical resistance**.

---

## Suggested Short Answer Points for Viva/Exam

- Caesar cipher: shift substitution.
- Playfair cipher: digraph substitution with 5 × 5 matrix.
- Hill cipher: matrix multiplication mod 26.
- OTP: perfect secrecy with random one-time key.
- Block cipher: fixed-size block, same-size output.
- Feistel network: reversible by reversing subkeys.
- DES: 64-bit block, 56-bit key, 16 rounds, S-box based nonlinear design.
