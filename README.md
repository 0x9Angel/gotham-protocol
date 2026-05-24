
<div align="center">

# Gotham Protocol

### A real-time, post-quantum mixnet for sovereign communications

*Sphinx-format · Loopix-style mixing · X25519 + ML-KEM-768 hybrid · Rust*

![Status](https://img.shields.io/badge/status-pre--alpha-orange?style=for-the-badge)
![Spec](https://img.shields.io/badge/spec-v0.1%20draft-blue?style=for-the-badge)
![Implementation](https://img.shields.io/badge/impl-Rust-000?style=for-the-badge&logo=rust)

</div>

---

## What is Gotham?

Gotham is a **network-layer anonymity protocol** purpose-built for low-latency messaging. It is what carries packets between two users of [Crypto](https://github.com/0x9Angel/Crypto), and it is designed so that **no single party — not the relays, not the network operator, not even a colluding subset — can reconstruct who talks to whom**.

It belongs to the same family as **Tor**, **Mixminion**, **Loopix** and **Nym**, but solves a specific gap none of them cover:

> **Real-time + post-quantum + production-grade enterprise integration.**

---

## Why a new protocol

| Existing system | What it does well | Why we still needed Gotham |
|---|---|---|
| **Tor** | 20 years of production hardening, large anonymity set | Too slow (800–2000 ms) for real-time messaging; pre-quantum |
| **Mixminion** | Strong anonymity guarantees | Multi-hour delays — designed for email |
| **Loopix** (academic) | Proper Poisson mixing + cover | Research-only; no production codebase |
| **Nym** | Production mixnet | Token-blockchain bound; not designed for sovereign enterprise deployment |
| **Signal sealed-sender** | Hides sender identity | Server still sees the receiver's identity and message timing |

Gotham keeps the best ideas (Sphinx packet format, Loopix mixing, sealed-sender envelopes) and adds what was missing:

- **Median latency 50–300 ms** — enables real-time presence, typing indicators, read receipts.
- **Post-quantum hybrid** key encapsulation (X25519 + ML-KEM-768) from version 0.1.
- **Stateless relays** — nothing on disk to subpoena, nothing to seize.
- **Fully open standards inside** — no proprietary primitives. Just engineering them in the right order.

---

## How it works (high level)


┌─ Entry ─┐    ┌─ Mix ──┐    ┌─ Exit ─┐
Sender ──▶│     unwrap  │──▶ │ unwrap │──▶ │ unwrap │──▶ Recipient
│ Poisson │    │ Poisson│    │ deliver│
└─────────┘    └────────┘    └────────┘
▲                                      ▼
│      cover traffic (continuous)      │
└──────────────────────────────────────┘



Each packet is:
- **Exactly 2048 bytes** — same size regardless of payload length. Traffic analysis cannot infer message size.
- **Onion-encrypted** — each relay can only decrypt its own layer; sees only previous hop and next hop.
- **Authenticated** with a per-hop MAC chain — tampering by any relay or external party is detected.
- **Mixed** with Poisson-distributed delays at each hop — breaks timing correlation.
- **Indistinguishable** from cover-traffic packets at every hop.

The sender, the receiver, and each relay all see different views of the same packet. No single observer ever sees the whole picture.

---

## Cryptographic primitives

Standard primitives, composed carefully. No new ciphers, no rolled-our-own crypto.

| Primitive | Purpose | Standard |
|---|---|---|
| **X25519** | Classical key agreement (Diffie-Hellman) | RFC 7748 |
| **ML-KEM-768** | Post-quantum key encapsulation | NIST FIPS 203 |
| **HKDF-SHA256** | Sub-key derivation | RFC 5869 |
| **ChaCha20-Poly1305** | Authenticated encryption | RFC 8439 |
| **Poly1305** | Per-hop MAC chain | RFC 8439 |
| **Ed25519** | Directory authority signatures | RFC 8032 |
| **Noise XK** | Per-link transport (over QUIC) | Noise Protocol Framework |

The cryptographic design follows **Sphinx** (Danezis & Goldberg, 2009) for the packet format and **Loopix** (Piotrowska et al., 2017) for the mixing discipline.

---

## What Gotham resists

| Adversary | Outcome |
|---|---|
| Passive network observer (ISP, café WiFi) | Sees fixed-size encrypted blobs. Nothing else. |
| Compromise of any single relay | Sees only the previous hop and the next hop. Cannot reconstruct the path. |
| Active tampering (replay, byte-flipping) | Detected by per-hop MAC chain → packet dropped silently. |
| Future quantum adversary | Hybrid X25519 + ML-KEM-768 — both must break for the session to be compromised. |
| Deep packet inspection / UDP/443 blocking | Pluggable transports — falls back to TLS 1.3 over TCP/443 with vanilla HTTPS fingerprint. |

What Gotham does **not** resist (explicitly):

- A global passive adversary with 60%+ network visibility.
- Compromise of a majority of relays in the path pool.
- Endpoint compromise (malware on the user's device).
- Forced disclosure of non-ephemeral key material.

Honesty about limitations is itself part of the threat model.

---

## Status

| Track | Status |
|---|---|
| Packet format v0.1 (slot-based) |  Implemented + tested |
| Packet format v0.2 (folded, position-hiding) |  Implemented (branch) |
| Per-hop payload AEAD |  Implemented (branch) |
| Stateless relay daemon |  Implemented |
| Cover traffic scheduler |  Implemented |
| Directory authority + signed directory |  Implemented |
| Anonymous directory refresh through Gotham |  Protocol implemented |
| Pluggable transports (QUIC + TLS-TCP) |  Implemented |
| obfs4 / meek-CDN transports |  Scaffolded, in progress |
| Push notification relay for mobile |  Scaffolded, in progress |
| External cryptographic audit |  Planned |
| Public relay network |  Planned |
| RFC submission (informational) |  Long-term |

---

## Design documents

The cryptographic specification, threat model, and conformance levels live in the private spec repository. They are released to:

- **Cryptographic auditors** under standard NDA.
- **Pilot customers** evaluating deployment.
- **Academic reviewers** invited to comment on the design.

If you fit one of these categories, please reach out.

---


<div align="center">

*Gotham powers [Crypto](https://github.com/0x9Angel/Crypto). The implementation is held under dual licence (AGPLv3 + commercial) in a private repository pending audit.*

</div>
