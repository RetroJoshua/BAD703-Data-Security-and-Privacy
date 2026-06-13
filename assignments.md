[🏠 Home](README.md) | [📅 Schedule](schedule.md) | [📖 M1](module1.md) | [📖 M2](module2.md) | [📖 M3](module3.md) | [📖 M4](module4.md) | [📖 M5](module5.md) | [📝 Assignments](assignments.md) | [📚 Resources](resources.md)

---

# Cryptography & Information Security — Group Assignments

**Course:** Cryptography & Information Security
**Format:** Group work (4–5 students per group)
**Submission:** GitHub repository link + PDF report + recorded demo (5–10 min)
**Languages allowed:** Python 3.10+ (preferred), Java, or C++
**Academic integrity:** All code must be written by the group. External libraries are allowed **only** for I/O, testing, and visualization — *never* for the core cryptographic primitives unless explicitly stated.

---

## 📌 General Submission Requirements (Applies to Both Assignments)

Each group must submit:

1. **Source code** in a public/private GitHub repository with:
   - A clear `README.md` (build & run instructions, group member names + roles).
   - Modular code (separate files for algorithm, CLI, tests).
   - At least **5 unit tests** per implemented algorithm (use `pytest` / `JUnit`).
2. **Report (PDF, 8–12 pages)** containing:
   - Mathematical background of each algorithm.
   - Implementation design (flowcharts / pseudocode).
   - Sample runs (screenshots) with at least 3 test vectors.
   - Security analysis, known attacks, and limitations.
   - Individual contribution table (who did what, in %).
3. **Demo video (5–10 min)** uploaded to YouTube (unlisted) or Google Drive showing each member explaining a part.

**Group composition rules:**
- 4–5 students per group; mixed skill levels recommended.
- Assign roles: *Team Lead*, *Algorithm Developer(s)*, *Tester / QA*, *Documentation Lead*, *Presenter*.
- Use Git branches and pull requests — commit history will be reviewed.

---

# Assignment 1 — Symmetric Cryptography: Classical Cipher + DES Demonstration

**Weight:** 25% of final grade
**Deadline:** 3 weeks from release date
**Group size:** 4–5 students

## 🎯 Learning Objectives

By the end of this assignment, students will be able to:
- Implement and break a classical symmetric cipher.
- Understand the Feistel structure and the round-based design of DES.
- Differentiate between confusion and diffusion in practice.
- Identify weaknesses of legacy symmetric algorithms.

## 📚 Background

Symmetric-key cryptography uses the **same key** for both encryption and decryption. Classical ciphers (Playfair, Vigenère) illustrate substitution and polyalphabetic principles, while DES introduced the modern block-cipher paradigm based on the Feistel network.

## Part A — Classical Symmetric Cipher (50%)

Choose **one** of the following ciphers (one per group; coordinate with instructor to avoid duplication where possible):

### Option 1: Playfair Cipher
- Build the 5×5 key matrix from a user-supplied keyword (handle `I/J` merging).
- Implement encryption and decryption with correct digraph rules (same row, same column, rectangle).
- Properly handle padding (e.g., insert `X` between repeated letters and at the end if odd).

### Option 2: Vigenère Cipher
- Implement encryption/decryption with a repeating keyword.
- Implement the **Kasiski examination** *or* **Index of Coincidence** attack to recover the key length from ciphertext.
- Then perform frequency analysis on each Caesar-shifted subgroup to recover the key.

### Required Deliverables (Part A)
- CLI tool: `python cipher.py encrypt|decrypt|attack --key KEY --input file.txt`.
- Demonstrate on a ciphertext of **at least 500 characters**.
- Cryptanalysis report: how many ciphertext characters were needed to recover the key reliably?

## Part B — Simple DES Demonstration (50%)

Implement a **simplified DES (S-DES)** *or* a **didactic full-DES** in your chosen language.

> ✅ Recommended: implement **S-DES** (8-bit block, 10-bit key, 2 rounds) as described in Stallings, Appendix G — it is tractable and pedagogically rich. Groups of 5 may instead implement full **16-round DES** for bonus 10%.

### Required Features
- Key schedule generation (P10, shift, P8 for S-DES; or PC-1/PC-2 for full DES).
- Initial and Final Permutations.
- Feistel function `F` with S-boxes (S0/S1 for S-DES; S1–S8 for full DES).
- ECB and **CBC** mode of operation (you implement the chaining, not a library).
- Encrypt/decrypt a short text file and verify round-trip equality.

### Analysis Tasks
1. Show the **avalanche effect**: flip 1 bit of the plaintext and 1 bit of the key — count bit differences in the ciphertext across rounds. Present as a table or chart.
2. Briefly discuss why DES is considered insecure today (key size, known attacks: brute-force, differential, linear cryptanalysis).
3. Compare ECB vs. CBC by encrypting a simple bitmap image (provided in resources) and showing the visual difference.

## 📋 Grading Rubric — Assignment 1

| Criterion | Weight |
|---|---|
| Correctness of classical cipher (encrypt/decrypt) | 15% |
| Successful cryptanalysis / attack | 15% |
| Correctness of S-DES / DES implementation | 25% |
| Avalanche & mode-of-operation analysis | 15% |
| Code quality, tests, Git hygiene | 10% |
| Report clarity & depth | 15% |
| Demo video & individual contributions | 5% |

---

# Assignment 2 — Public-Key Cryptography + Steganography

**Weight:** 30% of final grade
**Deadline:** 4 weeks from release date
**Group size:** 4–5 students

## 🎯 Learning Objectives

- Understand and implement asymmetric (public-key) cryptography from scratch.
- Demonstrate a secure key-exchange protocol over an insecure channel.
- Combine cryptography with steganography to achieve confidentiality + covert communication.
- Reason about real-world attacks (Man-in-the-Middle, statistical steganalysis).

## Part A — Asymmetric Crypto (50%)

Choose **one** of the following (groups must coordinate so both options are represented in the class):

### Option 1: RSA from Scratch
- Implement **Miller–Rabin primality testing** to generate two primes of at least **512 bits** each (1024-bit modulus).
- Compute `n`, `φ(n)`, choose `e`, and derive `d` via the **Extended Euclidean Algorithm**.
- Implement encryption, decryption, and a basic **PKCS#1 v1.5-style padding** (do not use a library).
- Demonstrate **signing & verification** of a short message using SHA-256 (you may use `hashlib` for the hash only).
- Bonus (+5%): Mount a textbook-RSA attack (e.g., low-exponent attack with `e=3` and no padding).

### Option 2: Diffie–Hellman ("Alice–Bob") Key Exchange
- Implement DH over a safe prime `p` of at least **1024 bits** (you may use a standard MODP group from RFC 3526, cite it).
- Two scripts: `alice.py` and `bob.py` communicating over **sockets** (TCP) on `localhost`.
- Derive a shared symmetric key, then use it with **AES-128 in CBC** (you may use `pycryptodome` for AES here) to exchange an encrypted message.
- Simulate a **Man-in-the-Middle (Mallory)** that intercepts and substitutes public values — show how the attack succeeds without authentication, and discuss mitigations (signed DH, certificates).

### Required Deliverables (Part A)
- Working source + sample session logs.
- Performance numbers: time to generate keys / compute shared secret for 1024, 2048 bits.
- Threat-model section in the report.

## Part B — LSB Image Steganography (50%)

Implement a **Least-Significant-Bit (LSB)** steganography tool for **PNG / BMP** images (lossless formats only).

### Required Features
1. **Embed** an arbitrary message (text *or* small file) into the LSBs of the RGB channels of a cover image.
2. **Extract** the hidden payload, given only the stego-image and a password / seed.
3. Use a **PRNG seeded by a password** to pseudo-randomly select pixel positions (not sequential) — increases steganalysis resistance.
4. **Encrypt the payload first** using the symmetric key derived in Part A (RSA-wrapped AES key, or DH-derived AES key). The final pipeline must be:
   `plaintext → AES-encrypt → LSB-embed → stego.png`
5. Provide statistics: maximum payload capacity (bytes) for a given image, PSNR between cover and stego image.

### Analysis Tasks
- Show side-by-side cover vs. stego image — they should be visually indistinguishable.
- Run a simple **chi-square** or **histogram** steganalysis on both images and discuss results.
- Discuss limitations: what happens if the stego image is re-saved as JPEG?

## 📋 Grading Rubric — Assignment 2

| Criterion | Weight |
|---|---|
| Correctness of RSA / DH implementation | 25% |
| MITM demo *or* RSA attack demo | 10% |
| LSB embedding & extraction correctness | 20% |
| Integration of crypto + stego pipeline | 15% |
| Steganalysis & security discussion | 10% |
| Code quality, tests, Git hygiene | 10% |
| Report & demo video | 10% |

---

## 🚫 Academic Integrity

- Plagiarism (including copy-pasting from GitHub / ChatGPT without understanding) results in **zero** for the assignment and referral to the academic committee.
- Cite every external resource you consult in `references.md` inside your repo.
- Each member must be able to **explain any line of code** in the demo viva.

## 🆘 Support

- Weekly 1-hour lab sessions for Q&A.
- Office hours: see syllabus.
- See `resources.md` for textbooks, papers, videos, and starter datasets.
