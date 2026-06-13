[🏠 Home](README.md) | [📅 Schedule](schedule.md) | [📖 M1](module1.md) | [📖 M2](module2.md) | [📖 M3](module3.md) | [📖 M4](module4.md) | [📖 M5](module5.md) | [📝 Assignments](assignments.md) | [📚 Resources](resources.md)

---

# Module 3: Key Management Fundamentals and Public-Key Management

> **Recommended Reading:** Keith M. Martin, *Everyday Cryptography: Fundamental Principles and Applications* (2nd ed., Oxford University Press) — Chapters 10 and 11.

---

## 1. Introduction

Cryptographic algorithms are only as strong as the keys they use. As Keith Martin observes, "the security of any cryptosystem ultimately reduces to the security of the keys it relies on." Key management is the discipline that governs **how cryptographic keys are generated, distributed, stored, used, archived, and destroyed**, across their entire lifecycle.

This module covers:

1. Fundamentals of key management
2. Public-key management challenges
3. Digital certificates and Certification Authorities (CAs)
4. The cryptographic key lifecycle
5. Practical considerations and worked examples

---

## 2. Key Management Fundamentals

### 2.1 Why Key Management Matters

A perfectly designed cipher (e.g., AES-256) provides **no real security** if:

- Keys are predictable or weakly generated
- Keys are transmitted over insecure channels
- Keys are stored in plaintext on disk
- Compromised keys are not promptly revoked

Martin emphasizes the **"key management problem"** as the central practical challenge of applied cryptography.

### 2.2 Core Requirements

| Requirement | Description |
|-------------|-------------|
| **Confidentiality** | Secret keys must be known only to authorized parties |
| **Integrity** | Keys must not be modified by unauthorized parties |
| **Authenticity** | Keys must be bound reliably to their owners |
| **Availability** | Keys must be accessible when needed |
| **Accountability** | Use of keys must be auditable |

### 2.3 Symmetric vs. Asymmetric Key Management

- **Symmetric keys**: Both parties share the same secret. The challenge is *secure distribution* — how do you share the secret without an attacker intercepting it?
- **Asymmetric keys**: Each party has a public/private key pair. The challenge is *binding* — how do you trust that a public key really belongs to the claimed owner?

---

## 3. Public-Key Management

### 3.1 The Public Key Authenticity Problem

Public keys are, by definition, *public*. The fundamental concern is not secrecy but **authenticity**:

> *Is the public key I just received really Alice's public key, or did Eve substitute her own?*

If Eve can substitute her key for Alice's, she can mount a **man-in-the-middle attack**, decrypting and re-encrypting messages without detection.

### 3.2 Trust Models

Three dominant trust models address public-key authenticity:

1. **Direct Trust** — out-of-band key verification (e.g., comparing key fingerprints in person).
2. **Web of Trust** (PGP/GPG) — peers cross-sign one another's keys.
3. **Hierarchical Trust (PKI)** — trusted third parties (Certification Authorities) vouch for keys via digital certificates.

---

## 4. Digital Certificates and Certification Authorities

### 4.1 What Is a Digital Certificate?

A **digital certificate** is a signed data structure that binds an identity to a public key. The signature is produced by a **Certification Authority (CA)** trusted by the relying party.

The dominant standard is **X.509 v3**, used in TLS, S/MIME, code signing, and more.

### 4.2 X.509 Certificate Structure

```
Certificate
├── Version (v3)
├── Serial Number
├── Signature Algorithm (e.g., sha256WithRSAEncryption)
├── Issuer (CA's Distinguished Name)
├── Validity
│     ├── Not Before
│     └── Not After
├── Subject (Owner's Distinguished Name)
├── Subject Public Key Info
│     ├── Algorithm (e.g., RSA, ECDSA)
│     └── Public Key
├── Extensions (Key Usage, SAN, CRL Distribution Point, ...)
└── CA Signature
```

### 4.3 The Certification Authority (CA)

A CA performs three core functions:

1. **Registration**: Verifying the identity of the applicant (often through a Registration Authority, RA).
2. **Issuance**: Constructing and signing the certificate.
3. **Revocation**: Publishing notice when a certificate must no longer be trusted.

### 4.4 Certificate Chains and Trust Anchors

Browsers and operating systems ship with a **root trust store** of self-signed root CA certificates. Trust flows hierarchically:

```
Root CA  ──signs──►  Intermediate CA  ──signs──►  End-Entity (e.g., www.example.com)
```

When validating, the client constructs a **chain of trust** from the end-entity certificate up to a trusted root.

### 4.5 Revocation Mechanisms

| Mechanism | Description |
|-----------|-------------|
| **CRL** (Certificate Revocation List) | Periodically published list of revoked serial numbers |
| **OCSP** (Online Certificate Status Protocol) | Real-time per-certificate status query |
| **OCSP Stapling** | Server attaches a fresh OCSP response to its TLS handshake |
| **CT** (Certificate Transparency) | Public, append-only logs of issued certificates |

---

## 5. The Key Lifecycle

Martin (Chapter 10) presents key management as a lifecycle of distinct phases:

```
┌──────────┐   ┌──────────┐   ┌──────────┐   ┌─────────┐   ┌──────────┐   ┌────────────┐
│ Generate │──►│ Distribute│──►│  Store   │──►│   Use   │──►│ Archive  │──►│  Destroy   │
└──────────┘   └──────────┘   └──────────┘   └─────────┘   └──────────┘   └────────────┘
                                                    │
                                                    ▼
                                              ┌──────────┐
                                              │  Revoke  │ (when compromised)
                                              └──────────┘
```

### 5.1 Generation

- Use **cryptographically secure RNGs** (e.g., `/dev/urandom`, hardware RNGs).
- Respect minimum key lengths (NIST SP 800-57): AES ≥ 128 bits, RSA ≥ 2048 bits, ECC ≥ 256 bits.

### 5.2 Distribution

- Symmetric keys: use key transport (wrap under a public key) or key agreement (Diffie-Hellman).
- Public keys: distribute via certificates.

### 5.3 Storage

- Software keystores (PKCS#12, JKS) — passphrase-protected.
- Hardware Security Modules (HSMs) and TPMs — keys never leave the device.

### 5.4 Use

- Enforce **key separation** — never reuse a key for multiple purposes (e.g., do not use a signing key for encryption).

### 5.5 Archival

- Decryption/verification keys may need long-term retention even after they retire.

### 5.6 Destruction

- Securely wipe keys when retired or compromised (e.g., zeroize HSM partitions).

### 5.7 Revocation

- Immediately publish revocation notices via CRL/OCSP when keys are compromised.

---

## 6. Practical Examples

### Example 1 — Generating an RSA Key Pair and Self-Signed Certificate (OpenSSL)

```bash
# 1. Generate a 2048-bit RSA private key
openssl genpkey -algorithm RSA -out alice.key -pkeyopt rsa_keygen_bits:2048

# 2. Create a Certificate Signing Request (CSR)
openssl req -new -key alice.key -out alice.csr \
    -subj "/C=IN/ST=KA/O=Example/CN=alice@example.com"

# 3. Self-sign the CSR (in production, a CA would sign instead)
openssl x509 -req -in alice.csr -signkey alice.key -out alice.crt -days 365
```

### Example 2 — Inspecting an X.509 Certificate

```bash
openssl x509 -in alice.crt -noout -text
```

This reveals the Issuer, Subject, validity dates, public key, and extensions.

### Example 3 — Verifying a Certificate Chain

```bash
openssl verify -CAfile root-ca.crt -untrusted intermediate.crt server.crt
```

### Example 4 — Pseudocode: Validating a Certificate Path

```
function validate(cert, trust_store):
    chain = build_chain(cert)
    for c in chain:
        if c.not_before > now() or c.not_after < now():
            return INVALID  # expired or not yet valid
        if is_revoked(c):
            return INVALID
        if not verify_signature(c, c.issuer.public_key):
            return INVALID
    if chain.root not in trust_store:
        return UNTRUSTED
    return VALID
```

---

## 7. Key Takeaways

- **Key management — not algorithm design — is the most common point of failure** in real systems.
- Public-key cryptography solves the secrecy of distribution but **introduces an authenticity problem**, addressed by certificates.
- **PKI** provides scalable trust through hierarchical CAs and X.509 certificates.
- A robust deployment must handle the **entire key lifecycle**: generation, distribution, storage, use, archival, destruction, and revocation.
- Tools like **OpenSSL**, **HSMs**, and **OCSP/CRL/CT** are the practical workhorses of public-key management.

---

## 8. Further Reading

- Martin, K. M. *Everyday Cryptography*, 2nd ed., Chapters 10–11.
- NIST SP 800-57 Part 1 Rev. 5 — *Recommendation for Key Management*.
- RFC 5280 — *Internet X.509 Public Key Infrastructure Certificate and CRL Profile*.
- RFC 6960 — *X.509 Internet PKI Online Certificate Status Protocol (OCSP)*.
