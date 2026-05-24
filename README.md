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

