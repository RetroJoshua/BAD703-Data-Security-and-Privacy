[🏠 Home](README.md) | [📅 Schedule](schedule.md) | [📖 M1](module1.md) | [📖 M2](module2.md) | [📖 M3](module3.md) | [📖 M4](module4.md) | [📖 M5](module5.md) | [📝 Assignments](assignments.md) | [📚 Resources](resources.md)

---

# 📚 Resources — Cryptography & Information Security Assignments

A curated list of textbooks, papers, online courses, tools, and datasets to support **Assignment 1 (Symmetric + DES)** and **Assignment 2 (RSA / Diffie–Hellman + LSB Steganography)**.

---

## 1. 📖 Core Textbooks

| # | Title | Authors | Edition | Link |
|---|---|---|---|---|
| 1 | **Cryptography and Network Security: Principles and Practice** | William Stallings | 8th | [Pearson](https://www.pearson.com/en-us/subject-catalog/p/cryptography-and-network-security-principles-and-practice/P200000003477) · [Author site](https://www.williamstallings.com/Cryptography/) |
| 2 | **Understanding Cryptography** *(beginner-friendly, with companion videos)* | Christof Paar, Jan Pelzl | 2nd | [Springer](https://link.springer.com/book/10.1007/978-3-031-50816-7) · [Free lecture videos](https://www.youtube.com/playlist?list=PL6N5qY2nvvJE8X75pJbtjAjm9I8mEqyMt) |
| 3 | **Introduction to Modern Cryptography** | Jonathan Katz, Yehuda Lindell | 3rd | [CRC Press](https://www.routledge.com/Introduction-to-Modern-Cryptography/Katz-Lindell/p/book/9780815354369) |
| 4 | **Handbook of Applied Cryptography** *(free, classic reference)* | Menezes, van Oorschot, Vanstone | 1st | [Free PDF](https://cacr.uwaterloo.ca/hac/) |
| 5 | **Serious Cryptography** | Jean-Philippe Aumasson | 2nd | [No Starch Press](https://nostarch.com/serious-cryptography-2nd-edition) |
| 6 | **Cryptography: Theory and Practice** | Douglas R. Stinson, Maura B. Paterson | 4th | [CRC Press](https://www.routledge.com/Cryptography-Theory-and-Practice/Stinson-Paterson/p/book/9781138197015) |
| 7 | **The Codebreakers** *(historical context)* | David Kahn | Rev. | [Scribner](https://www.simonandschuster.com/books/The-Codebreakers/David-Kahn/9780684831305) |
| 8 | **Disappearing Cryptography: Information Hiding — Steganography & Watermarking** | Peter Wayner | 3rd | [Elsevier](https://www.sciencedirect.com/book/9780123744791/disappearing-cryptography) |

---

## 2. 📄 Recommended Papers & Standards

### Symmetric & DES
- NIST FIPS 46-3, *Data Encryption Standard (DES)* — [PDF](https://csrc.nist.gov/files/pubs/fips/46-3/final/docs/fips46-3.pdf)
- Biham & Shamir, *Differential Cryptanalysis of DES-like Cryptosystems*, 1991 — [Springer](https://link.springer.com/article/10.1007/BF00630563)
- Matsui, *Linear Cryptanalysis Method for DES Cipher*, EUROCRYPT 1993 — [PDF](https://link.springer.com/chapter/10.1007/3-540-48285-7_33)

### Public-Key Cryptography
- Rivest, Shamir, Adleman, *A Method for Obtaining Digital Signatures and Public-Key Cryptosystems*, 1978 — [ACM](https://dl.acm.org/doi/10.1145/359340.359342)
- Diffie & Hellman, *New Directions in Cryptography*, 1976 — [IEEE PDF](https://ee.stanford.edu/~hellman/publications/24.pdf)
- RFC 3526 — *More Modular Exponential (MODP) Diffie–Hellman groups for IKE* — [IETF](https://datatracker.ietf.org/doc/html/rfc3526)
- RFC 8017 — *PKCS #1 v2.2: RSA Cryptography Specifications* — [IETF](https://datatracker.ietf.org/doc/html/rfc8017)
- Boneh, *Twenty Years of Attacks on the RSA Cryptosystem*, 1999 — [PDF](https://crypto.stanford.edu/~dabo/pubs/papers/RSA-survey.pdf)

### Steganography
- Fridrich, Goljan, Du, *Detecting LSB Steganography in Color and Grayscale Images*, 2001 — [PDF](http://www.ws.binghamton.edu/fridrich/Research/mmsec2001ChiSquareAttack.pdf)
- Provos & Honeyman, *Hide and Seek: An Introduction to Steganography*, IEEE S&P 2003 — [PDF](https://niels.xtdnet.nl/papers/practical.pdf)
- Westfeld & Pfitzmann, *Attacks on Steganographic Systems* (Chi-square attack) — [PDF](https://link.springer.com/chapter/10.1007/10719724_5)

---

## 3. 🎓 Free Online Courses & Lectures

- **Cryptography I** — Dan Boneh, Stanford (Coursera): https://www.coursera.org/learn/crypto
- **Cryptography II** — Dan Boneh (Coursera): https://www.coursera.org/learn/crypto2
- **Understanding Cryptography** video lectures (Paar): https://www.youtube.com/playlist?list=PL6N5qY2nvvJE8X75pJbtjAjm9I8mEqyMt
- **Computerphile — Cryptography playlist**: https://www.youtube.com/playlist?list=PLzH6n4zXuckoAod3z31QEST1ZaizBuNHh
- **MIT 6.875 — Foundations of Cryptography** (OCW): https://ocw.mit.edu/courses/6-875-cryptography-and-cryptanalysis-spring-2005/
- **Khan Academy — Journey into Cryptography**: https://www.khanacademy.org/computing/computer-science/cryptography

---

## 4. 🌐 Interactive Sites & Tutorials

- **CrypTool-Online** — visual experiments with Playfair, Vigenère, DES, RSA: https://www.cryptool.org/en/cto/
- **CrypTool 2** (desktop, Windows): https://www.cryptool.org/en/ct2/
- **Practical Cryptography** — classical cipher tutorials & attacks: http://practicalcryptography.com/ciphers/
- **dCode** — online cipher solvers (for *checking* your attack output, not for solving the assignment for you): https://www.dcode.fr/
- **CryptoHack** — gamified cryptography challenges: https://cryptohack.org/
- **The Cryptopals Crypto Challenges**: https://cryptopals.com/

---

## 5. 🛠️ Libraries & Tools (allowed for I/O, hashing, testing — not for primitives)

| Purpose | Library | Link |
|---|---|---|
| Big integer math (Python) | built-in `int` | — |
| Hashing (SHA-256) | `hashlib` (Python) | https://docs.python.org/3/library/hashlib.html |
| AES (allowed in Assignment 2 Part B pipeline only) | `pycryptodome` | https://pycryptodome.readthedocs.io/ |
| Image manipulation (LSB) | `Pillow` | https://pillow.readthedocs.io/ |
| Numerical / image arrays | `numpy` | https://numpy.org/ |
| Image quality metric (PSNR / SSIM) | `scikit-image` | https://scikit-image.org/ |
| Testing | `pytest` | https://docs.pytest.org/ |
| Plots for avalanche / histograms | `matplotlib` | https://matplotlib.org/ |

### Steganalysis & Forensics Tools (for your security analysis section)
- **StegExpose** (Java): https://github.com/b3dk7/StegExpose
- **zsteg** (Ruby, for PNG/BMP): https://github.com/zed-0xff/zsteg
- **StegOnline** (browser-based): https://stegonline.georgeom.net/

---

## 6. 🧪 Sample Datasets & Test Vectors

- **Project Gutenberg** — long English plaintexts for Vigenère / DES tests: https://www.gutenberg.org/
- **NIST CAVS DES Test Vectors**: https://csrc.nist.gov/projects/cryptographic-algorithm-validation-program/block-ciphers
- **USC-SIPI Image Database** — classic cover images (Lena, Baboon, Peppers) for steganography: https://sipi.usc.edu/database/
- **BOSSbase 1.01** — standard steganography benchmark dataset: https://media.springernature.com/full/springer-static/image/art%3A10.1038%2Fs41598-026-35556-9/MediaObjects/41598_2026_35556_Fig1_HTML.png
- **RFC 3526 MODP groups** (for DH primes): https://datatracker.ietf.org/doc/html/rfc3526

---

## 7. 📰 Blogs, Wikis & Cheat Sheets

- **A Graduate Course in Applied Cryptography** — Boneh & Shoup (free draft): https://toc.cryptobook.us/
- **Bruce Schneier's blog**: https://www.schneier.com/
- **Trail of Bits — Cryptographic Right Answers**: https://blog.cryptographyengineering.com/
- **Matthew Green's *Cryptography Engineering* blog**: https://blog.cryptographyengineering.com/
- **OWASP — Cryptographic Storage Cheat Sheet**: https://cheatsheetseries.owasp.org/cheatsheets/Cryptographic_Storage_Cheat_Sheet.html

---

## 8. 🎥 Selected YouTube Videos

- *Diffie–Hellman Key Exchange — Computerphile*: https://www.youtube.com/watch?v=NmM9HA2MQGI
- *RSA Encryption — Computerphile*: https://www.youtube.com/watch?v=4zahvcJ9glg
- *DES Explained — Neso Academy*: https://www.youtube.com/watch?v=jJ5o0SfI_T8
- *LSB Steganography in Python — NeuralNine*: https://www.youtube.com/watch?v=q3eOOMx5qoo

---

## 9. ✅ Tips for Group Work

- Use **GitHub Projects / Issues** to track tasks; assign each issue to a member.
- Adopt a branching model: `main` (protected) ← PRs from `feature/<name>` branches; require at least 1 review.
- Keep commits small and meaningful. Avoid `final_final_v3.py` 🙃.
- Use a shared **Overleaf** project for the report (LaTeX templates: IEEE / ACM).
- Schedule a weekly 30-minute stand-up; rotate the note-taker.

---

> 📩 *If you find additional high-quality resources, open a PR on the course repository so future cohorts benefit.*
