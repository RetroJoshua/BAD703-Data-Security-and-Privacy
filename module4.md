# Module 4: Web Security (TLS) and IP Security (IPsec)

> **Recommended Reading:** Keith M. Martin, *Everyday Cryptography: Fundamental Principles and Applications* (2nd ed., Oxford University Press) — Chapter 12 (Cryptographic Applications).

---

## 1. Introduction

Modern internet communications rely on two cornerstone security protocol families:

- **TLS (Transport Layer Security)** — protects data above the transport layer (typically over TCP), securing web traffic (HTTPS), email (SMTPS), and APIs.
- **IPsec (Internet Protocol Security)** — protects data at the network layer, securing entire IP packets and underpinning most enterprise VPNs.

Together they illustrate two complementary strategies for applying cryptography to network communication: **end-to-end at the application/transport layer** versus **host-to-host (or gateway-to-gateway) at the network layer**.

---

## 2. Web Security with TLS

### 2.1 Background and Evolution

| Version | Year | Status |
|--------:|------|--------|
| SSL 2.0 | 1995 | Deprecated (insecure) |
| SSL 3.0 | 1996 | Deprecated (POODLE) |
| TLS 1.0 | 1999 | Deprecated |
| TLS 1.1 | 2006 | Deprecated |
| TLS 1.2 | 2008 | Widely deployed |
| **TLS 1.3** | **2018 (RFC 8446)** | **Current standard** |

TLS sits **between the application layer and TCP**, offering applications a secure, reliable byte stream.

### 2.2 Security Services Provided

- **Confidentiality** — symmetric encryption (AES-GCM, ChaCha20-Poly1305)
- **Integrity** — AEAD modes or HMAC
- **Authentication** — X.509 certificates (server, optionally client)
- **Replay protection** — sequence numbers in the record layer
- **Forward secrecy** — ephemeral Diffie-Hellman (TLS 1.3 mandates (EC)DHE)

### 2.3 TLS Architecture

```
┌──────────────────────────────────────────┐
│            Application (HTTP, SMTP)      │
├──────────────────────────────────────────┤
│  TLS Handshake │ Alert │ Change Cipher   │  ← Control protocols
├──────────────────────────────────────────┤
│           TLS Record Protocol            │  ← Encrypts & authenticates data
├──────────────────────────────────────────┤
│                  TCP / IP                │
└──────────────────────────────────────────┘
```

### 2.4 The TLS 1.3 Handshake (Simplified)

```
Client                                              Server
  |---- ClientHello (supported ciphers, key share) ---->|
  |                                                     |
  |<--- ServerHello (chosen cipher, key share)          |
  |     {EncryptedExtensions, Certificate,              |
  |      CertificateVerify, Finished} -------------------|
  |                                                     |
  |---- {Finished} ------------------------------------>|
  |                                                     |
  |======= Application Data (AEAD-encrypted) ==========>|
```

**Key changes from TLS 1.2:**

- Only 1 round-trip for a full handshake (0-RTT possible for resumed sessions).
- All handshake messages after ServerHello are **encrypted**.
- Static RSA key exchange removed → **forward secrecy is mandatory**.
- Cipher suite list shrunk dramatically; only AEAD ciphers permitted.

### 2.5 Practical Example — Inspecting a TLS Handshake

```bash
# Connect to example.com and show the negotiated parameters
openssl s_client -connect example.com:443 -tls1_3 -servername example.com

# Capture handshake with tcpdump and inspect in Wireshark
sudo tcpdump -i any -s 0 -w tls.pcap host example.com and port 443
```

### 2.6 Practical Example — Minimal Python TLS Client

```python
import socket, ssl

ctx = ssl.create_default_context()
with socket.create_connection(("example.com", 443)) as sock:
    with ctx.wrap_socket(sock, server_hostname="example.com") as tls:
        print("Protocol:", tls.version())
        print("Cipher  :", tls.cipher())
        tls.sendall(b"GET / HTTP/1.1\r\nHost: example.com\r\n\r\n")
        print(tls.recv(512).decode(errors="ignore"))
```

### 2.7 Common TLS Attacks (and Mitigations)

| Attack | Description | Mitigation |
|--------|-------------|------------|
| **POODLE** | Padding oracle in SSL 3.0 | Disable SSL 3.0 |
| **BEAST** | CBC IV reuse in TLS 1.0 | Use TLS ≥ 1.2 with AEAD |
| **Heartbleed** | OpenSSL memory leak (CVE-2014-0160) | Patch OpenSSL |
| **Downgrade** | Force weaker version | TLS_FALLBACK_SCSV, ALPN |
| **MITM with rogue CA** | Untrusted CA issues fake cert | HSTS, HPKP (deprecated), Certificate Transparency |

---

## 3. IP Security (IPsec)

### 3.1 What Is IPsec?

IPsec is a suite of protocols (RFCs 4301–4309) operating at the **network layer (Layer 3)** that secures IP packets between two endpoints — hosts or gateways. Because it works below the transport layer, it is **transparent to applications**.

### 3.2 IPsec Architecture Components

```
┌────────────────────────────────────────────┐
│                  IKE (IKEv2)               │  ← Key management
├────────────────────────────────────────────┤
│           AH               │     ESP       │  ← Data protection protocols
├────────────────────────────────────────────┤
│  Security Policy DB (SPD)  │ Security      │
│                            │ Association DB│  ← Policy & state
│                            │ (SAD)         │
└────────────────────────────────────────────┘
```

### 3.3 Two Protection Protocols

| Protocol | Provides | IP Protocol # |
|----------|----------|---------------|
| **AH** (Authentication Header) | Integrity + authentication, **no encryption** | 51 |
| **ESP** (Encapsulating Security Payload) | Encryption + (optionally) integrity & authentication | 50 |

ESP is by far the more commonly deployed.

### 3.4 Two Modes of Operation

#### Transport Mode (host-to-host)

```
Original:  [IP hdr | TCP | Payload]
ESP/TM  :  [IP hdr | ESP hdr | TCP | Payload | ESP trailer | ESP auth]
                              \__________ encrypted __________/
```

#### Tunnel Mode (gateway-to-gateway VPN)

```
Original:  [IP hdr | TCP | Payload]
ESP/Tun :  [New IP | ESP hdr | IP hdr | TCP | Payload | ESP trailer | ESP auth]
                              \_____________ encrypted _____________/
```

Tunnel mode hides the *original* source/destination IP addresses — essential for site-to-site VPNs.

### 3.5 Security Associations (SAs)

An **SA** is a one-way logical connection that defines:

- Cryptographic algorithms (e.g., AES-256-GCM)
- Keys
- Sequence numbers
- Lifetime

Each SA is identified by a triple: `(SPI, Destination IP, Protocol)`.

A bidirectional secure channel therefore requires **two SAs** (one per direction).

### 3.6 IKEv2 — Key Management

IKE (Internet Key Exchange, RFC 7296) establishes SAs using a two-phase exchange:

```
Initiator                                    Responder
  |--- IKE_SA_INIT (DH, nonces, ciphers) ---->|
  |<-- IKE_SA_INIT (DH, nonce, cert req) -----|
  |    (now have shared secret; further       |
  |     messages are encrypted)               |
  |--- IKE_AUTH (identity, cert, child SA) -->|
  |<-- IKE_AUTH (identity, cert, child SA) ---|
```

After IKE_AUTH, a pair of **Child SAs** is in place and ESP-protected traffic can flow.

### 3.7 Practical Example — Site-to-Site VPN with strongSwan (Linux)

`/etc/swanctl/conf.d/site-to-site.conf`

```conf
connections {
    branch-hq {
        version = 2
        local_addrs  = 203.0.113.5
        remote_addrs = 198.51.100.10

        local  { auth = psk; id = branch.example.com }
        remote { auth = psk; id = hq.example.com }

        children {
            net-net {
                local_ts  = 10.10.0.0/16
                remote_ts = 10.20.0.0/16
                esp_proposals = aes256gcm16-prfsha384-ecp384
                mode = tunnel
            }
        }
        proposals = aes256-sha384-ecp384
    }
}
secrets {
    ike-hq { secret = "shared-pre-shared-key" }
}
```

Bring it up:

```bash
sudo swanctl --load-all
sudo swanctl --initiate --child net-net
sudo swanctl --list-sas        # verify SAs are established
```

### 3.8 Practical Example — Reading a Packet with `tcpdump`

```bash
sudo tcpdump -ni eth0 esp        # see ESP-protected packets between gateways
```

You will see only `ESP(spi=0x..., seq=...)` — the original IP header and TCP payload are encrypted inside.

---

## 4. TLS vs. IPsec — When to Use Which?

| Aspect | TLS | IPsec |
|--------|-----|-------|
| Layer | Transport / Application | Network |
| Scope | Per-application, per-connection | Per-host or per-network |
| Application changes | Required (use TLS API) | None (transparent) |
| Typical use | HTTPS, email, APIs | VPNs, secure inter-datacenter links |
| NAT traversal | Native (TCP) | Requires NAT-T (UDP 4500) |
| Granularity | Per-session | Per-packet |

Martin observes that the two are **complementary**: TLS protects specific application sessions end-to-end, while IPsec protects all traffic across an untrusted network segment.

---

## 5. Key Takeaways

- **TLS 1.3** is the modern standard for securing application traffic — fewer round-trips, mandatory forward secrecy, AEAD-only ciphers.
- TLS handshake provides **authentication via X.509**, **negotiated symmetric session keys**, and **integrity-protected record traffic**.
- **IPsec** secures arbitrary IP traffic at the network layer using **AH** (integrity) or **ESP** (encryption + integrity).
- IPsec uses **Security Associations** managed by **IKEv2**; tunnel mode is the basis of site-to-site VPNs.
- Choice between TLS and IPsec depends on **scope, granularity, application awareness, and operational model**.

---

## 6. Further Reading

- Martin, K. M. *Everyday Cryptography*, 2nd ed., Chapter 12.
- RFC 8446 — *The Transport Layer Security (TLS) Protocol Version 1.3*.
- RFC 4301 — *Security Architecture for the Internet Protocol*.
- RFC 7296 — *Internet Key Exchange Protocol Version 2 (IKEv2)*.
- Rescorla, E. *SSL and TLS: Designing and Building Secure Systems*.
