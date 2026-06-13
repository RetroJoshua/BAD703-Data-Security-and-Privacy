[🏠 Home](README.md) | [📅 Schedule](schedule.md) | [📖 M1](module1.md) | [📖 M2](module2.md) | [📖 M3](module3.md) | [📖 M4](module4.md) | [📖 M5](module5.md) | [📝 Assignments](assignments.md) | [📚 Resources](resources.md)

---

# Module 5: Data Hiding in Text and Images — Watermarking, LSB and BPCS Steganography

> **Recommended Reading:** David Salomon, *Data Privacy and Security: Encryption and Information Hiding* (Springer) — Part on Information Hiding (Steganography and Watermarking).

---

## 1. Introduction

Whereas cryptography makes a message **unreadable**, **information hiding** makes its very existence **undetectable**. The two principal disciplines of information hiding are:

- **Steganography** — concealing a *secret payload* inside an innocuous *cover* (text, image, audio, video) so that an observer is unaware that any hidden message is present.
- **Digital Watermarking** — embedding an *identifying mark* (often imperceptibly) into a cover for copyright protection, ownership proof, tamper detection, or tracing.

Salomon emphasizes a useful slogan: *"Cryptography hides the meaning; steganography hides the existence."*

This module covers:

1. Watermarking fundamentals
2. LSB (Least Significant Bit) steganography
3. BPCS (Bit-Plane Complexity Segmentation) steganography
4. Data hiding in text
5. Worked examples and visual explanations

---

## 2. Foundational Concepts

### 2.1 The Information-Hiding Model

```
                ┌────────────┐
   Secret  ────►│            │
                │   Embed    │────►  Stego-object
   Cover   ────►│            │       (looks like cover)
                └─────┬──────┘
                      │ (Key, optional)
                      ▼
                 transmit / store
                      │
                      ▼
                ┌────────────┐
   Stego-obj ──►│  Extract   │────►  Secret
                └────────────┘
                      ▲
                      │ (Key, optional)
```

### 2.2 The Information-Hiding Triangle

Every scheme must trade off three competing goals:

```
              Capacity
                /\
               /  \
              /    \
   Robustness ──── Imperceptibility
```

- **Capacity** — how many bits we can hide
- **Imperceptibility (fidelity)** — how undetectable the change is
- **Robustness** — how well the payload survives modifications (compression, cropping)

Steganography typically prioritizes **imperceptibility**; watermarking typically prioritizes **robustness**.

---

## 3. Digital Watermarking

### 3.1 Classification

| Dimension | Categories |
|-----------|------------|
| Perceptibility | **Visible** vs **Invisible** |
| Robustness | **Fragile**, **Semi-fragile**, **Robust** |
| Domain | **Spatial** (pixel) vs **Transform** (DCT, DWT, DFT) |
| Detection | **Blind** (no original needed), **Semi-blind**, **Non-blind** |

### 3.2 Common Applications

- Copyright assertion (e.g., stock-photo overlays)
- Broadcast monitoring
- Fingerprinting / traitor tracing
- Tamper detection in forensic images
- Authentication of medical / legal documents

### 3.3 Spatial-Domain Visible Watermark — Example

```python
from PIL import Image

cover = Image.open("photo.jpg").convert("RGBA")
mark  = Image.open("logo.png").convert("RGBA")
mark.putalpha(96)                                  # 38% opacity
cover.alpha_composite(mark, (20, 20))
cover.convert("RGB").save("watermarked.jpg")
```

### 3.4 Transform-Domain (DCT) Invisible Watermark — Sketch

1. Apply 8×8 block DCT to the cover image.
2. Embed watermark bits by perturbing mid-frequency coefficients:
   `C'[u,v] = C[u,v] · (1 + α · w_i)` where `α` controls strength.
3. Inverse DCT → watermarked image.

Mid-frequency coefficients are chosen because:

- **Low frequencies** carry image energy — perturbation is visible.
- **High frequencies** are removed by JPEG compression — not robust.

---

## 4. LSB (Least Significant Bit) Steganography

### 4.1 Principle

A grayscale pixel value (0–255) is an 8-bit byte. The **least significant bit** contributes a brightness change of only 1/256 — well below human visual sensitivity. Replacing it with payload bits is essentially invisible.

```
Original byte : 1 0 1 1 0 0 1 0   (value = 178)
                              ↑
                       this bit replaced
Secret bit    : 1
Stego byte    : 1 0 1 1 0 0 1 1   (value = 179)   ← change of ±1
```

### 4.2 Capacity

A 24-bit RGB image has 3 LSBs per pixel:

- 1024 × 1024 image → 3 × 1024 × 1024 = **3,145,728 bits ≈ 384 KB** of hidden payload.

### 4.3 Embedding Pseudocode

```
function embed_lsb(image, payload_bits):
    i = 0
    for each pixel (r, g, b) in image:
        for channel in (r, g, b):
            if i < len(payload_bits):
                channel = (channel & 0b11111110) | payload_bits[i]
                i += 1
    return image
```

### 4.4 Working Python Example

```python
from PIL import Image

def bits(data):
    for byte in data:
        for k in range(7, -1, -1):
            yield (byte >> k) & 1

def embed_lsb(cover_path, message: bytes, out_path):
    img = Image.open(cover_path).convert("RGB")
    pixels = list(img.getdata())
    # Prefix payload with 32-bit length so we know where it ends
    header = len(message).to_bytes(4, "big")
    stream = list(bits(header + message))

    new_pixels, idx = [], 0
    for r, g, b in pixels:
        if idx < len(stream): r = (r & ~1) | stream[idx]; idx += 1
        if idx < len(stream): g = (g & ~1) | stream[idx]; idx += 1
        if idx < len(stream): b = (b & ~1) | stream[idx]; idx += 1
        new_pixels.append((r, g, b))

    img.putdata(new_pixels)
    img.save(out_path, "PNG")            # PNG is lossless — important!

embed_lsb("cover.png", b"Meet at midnight.", "stego.png")
```

> ⚠ **Use a lossless format (PNG/BMP).** JPEG re-encoding will destroy LSBs.

### 4.5 Strengths and Weaknesses

| Pros | Cons |
|------|------|
| Simple, fast, high capacity | **Not robust** — any re-compression destroys it |
| Visually imperceptible | Detectable by statistical attacks (chi-square, RS-analysis) |
| Easy to implement | Vulnerable to filtering, resizing, cropping |

### 4.6 LSB Detection — Chi-Square Attack (intuition)

In a natural image, adjacent pixel values are unlikely to occur in perfectly equal proportions. Random LSB embedding tends to *equalize* counts of pair-of-values (2k, 2k+1). A chi-square test on those histogram pairs reveals the anomaly.

---

## 5. BPCS (Bit-Plane Complexity Segmentation) Steganography

Proposed by **Eiji Kawaguchi (1998)**, BPCS is described by Salomon as a "high-capacity" steganographic method that can hide as much as **50% of the cover image size** while remaining visually undetectable.

### 5.1 Key Insight

Decompose an 8-bit image into **8 bit-planes** (one per bit position). A bit-plane block is either:

- **Informative (simple)** — visible image structure (edges, smooth regions)
- **Noise-like (complex)** — random-looking, contributes nothing perceptual

Human vision is **insensitive to changes in complex (noise-like) regions**. BPCS replaces those blocks wholesale with payload data, then converts them back.

### 5.2 Bit-Plane Decomposition (PBC vs. CGC)

Plain binary code (PBC) suffers from the "hamming-cliff" problem (127 → 128 flips 8 bits). BPCS uses **Canonical Gray Code (CGC)**, which ensures adjacent intensities differ by exactly 1 bit:

```
PBC :  0111 1111  →  1000 0000    (8 changes)
CGC :  0100 0000  →  1100 0000    (1 change)
```

Conversion:

- PBC → CGC: `g[i] = b[i] XOR b[i-1]`
- CGC → PBC: `b[i] = g[i] XOR b[i-1]`

### 5.3 Complexity Measure

For an 8×8 binary block, define the **complexity α** as:

```
α = (number of black-white border transitions, horizontal + vertical)
    --------------------------------------------------------------
                  maximum possible transitions (= 112)
```

A block is considered **noise-like** if `α ≥ α₀`, where the threshold `α₀ ≈ 0.3` is typical.

### 5.4 BPCS Embedding Algorithm

```
1.  Convert cover image from PBC to CGC.
2.  Slice into 8 bit-planes; split each plane into 8×8 blocks.
3.  Slice the secret payload into 8×8 blocks (8 bytes = 64 bits).
4.  For each cover block B with complexity α(B) ≥ α₀ :
        a. Take next secret block S.
        b. If α(S) < α₀ :
              S ← conjugate(S)       # S XOR checkerboard
              mark S as conjugated   # store a 1 in a map block
        c. Replace B with S.
5.  Convert image back from CGC to PBC.
```

Extraction reverses these steps using the conjugation map.

### 5.5 The Conjugation Operation

```
S* = S XOR W      where W is the chessboard pattern
                  (alternating 1s and 0s)

Property:   α(S*) = 1 - α(S)
```

Thus every "simple" payload block can be turned into a "complex" one before embedding — preserving capacity while maintaining the invariant that all replaced blocks are noise-like.

### 5.6 Visual Explanation of Bit-Planes

```
MSB                                                                    LSB
 ┌─────┐ ┌─────┐ ┌─────┐ ┌─────┐ ┌─────┐ ┌─────┐ ┌─────┐ ┌─────┐
 │ Bit │ │ Bit │ │ Bit │ │ Bit │ │ Bit │ │ Bit │ │ Bit │ │ Bit │
 │  7  │ │  6  │ │  5  │ │  4  │ │  3  │ │  2  │ │  1  │ │  0  │
 │recog│ │recog│ │recog│ │mixed│ │mixed│ │noisy│ │noisy│ │noisy│
 └─────┘ └─────┘ └─────┘ └─────┘ └─────┘ └─────┘ └─────┘ └─────┘
                              ▲                       ▲
                LSB-style hiding affects only this    │
                BPCS uses noisy regions in ALL planes ─┘
```

### 5.7 Capacity Comparison

| Method | Typical capacity for 512×512 grayscale |
|--------|----------------------------------------|
| LSB (1 bit/pixel)              | ~32 KB |
| LSB (3 bits/pixel in RGB)      | ~96 KB |
| **BPCS (α₀ = 0.3)**            | **~100–130 KB (~50% of image)** |

### 5.8 BPCS — Strengths and Weaknesses

| Pros | Cons |
|------|------|
| Very high capacity | More complex implementation |
| Imperceptible — touches only noisy regions | Still **not robust** to lossy compression |
| Resistant to simple LSB-detection attacks | Specialized statistical attacks exist |

---

## 6. Data Hiding in Text

Text has very low redundancy compared to images, making steganography harder but not impossible.

### 6.1 Format-Based Methods

- **White-space encoding** — extra spaces at end-of-line or between words encode bits (e.g., `snow` tool).
- **Line/word shifting** — minute vertical/horizontal shifts in printed documents.
- **Font / kerning manipulation** — invisible style changes in word processors.
- **Unicode tricks** — interleaving zero-width characters (`U+200B`, `U+200C`).

### 6.2 Linguistic Methods

- **Synonym substitution** — choose between synonyms ("big" vs "large") to encode bits.
- **Syntactic transformations** — active/passive voice toggling.
- **Generation-based** — produce plausible cover text controlled by the payload (e.g., NICETEXT, Markov chain "spam mimicry").

### 6.3 Example — Hiding 1 Bit per Word with a Synonym List

```
Synonym pair: ("happy"=0, "joyful"=1)

Secret bits : 1 0 1
Cover text  : She is ___ today, very ___ but a little ___.
Result      : She is joyful today, very happy but a little joyful.
```

### 6.4 Example — Zero-Width Character Encoding (Python)

```python
ZW0, ZW1 = "\u200B", "\u200C"        # zero-width space, ZWNJ

def hide_text(cover: str, secret: str) -> str:
    bits = "".join(f"{ord(c):08b}" for c in secret)
    payload = "".join(ZW0 if b == "0" else ZW1 for b in bits)
    return cover[:1] + payload + cover[1:]   # inject after first character

stego = hide_text("Public announcement.", "HI")
print(repr(stego))
```

The visible text appears unchanged, yet two characters ("HI") are encoded in the zero-width characters between the first two letters.

---

## 7. Practical Comparison

| Technique | Capacity | Imperceptibility | Robustness | Typical Use |
|-----------|---------:|-----------------:|-----------:|-------------|
| Visible watermark   | n/a     | Low (intentional) | High      | Copyright branding |
| DCT-domain watermark | Low    | High             | High       | Forensic / DRM |
| LSB steganography   | Medium  | High             | Very low   | Covert messaging in lossless images |
| BPCS steganography  | Very high | High           | Low        | Large-payload covert transfer |
| Text white-space    | Very low | Medium          | Low        | Plain-text channels |

---

## 8. Key Takeaways

- **Information hiding** complements cryptography by concealing the existence of communication or embedding ownership data.
- **Watermarking** is optimized for robustness; **steganography** is optimized for imperceptibility.
- **LSB** is the canonical entry-point technique — simple and high-capacity but fragile and statistically detectable.
- **BPCS** exploits the human eye's insensitivity to *noise-like* image regions, achieving up to ~50% embedding capacity by replacing complex bit-plane blocks.
- **Text data hiding** has low capacity but exploits formatting, Unicode peculiarities, or linguistic choice to encode information.
- Choice of method depends on the **capacity / imperceptibility / robustness** trade-off appropriate to the application.

---

## 9. Further Reading

- Salomon, D. *Data Privacy and Security: Encryption and Information Hiding*, Springer — chapters on Steganography, Watermarking, and Image Hiding.
- Kawaguchi, E. & Eason, R. (1998). *Principles and applications of BPCS-Steganography*, SPIE Proc. 3528.
- Cox, I. et al. *Digital Watermarking and Steganography*, 2nd ed., Morgan Kaufmann.
- Fridrich, J. *Steganography in Digital Media: Principles, Algorithms, and Applications*, Cambridge University Press.
