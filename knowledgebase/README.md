# Quantum security knowledge base

A running log of disclosed vulnerabilities and cryptanalysis findings relevant
to quantum-era security, mapped against the OWASP Top 10 for Quantum Security
Risks (QS01-QS10).

Maintainer: Vishnu Ajith (@Vishnu2707)

## How entries work

- Title
- Date
- Maps to: QS0x
- Summary: plain language, 2-3 lines
- Source
- Why it matters

Anyone can add entries. Open a PR against this file with the same format.

---

## Entries

### HAWK signature scheme, improved key recovery attack

- Date: 28 Jul 2026
- Maps to: QS03, Vulnerable Signatures and Code-Signing
- Summary: Anthropic researchers found a faster attack against HAWK, a NIST round-3 post-quantum signature candidate, using an AI model. It doesn't break HAWK outright, but it halves the effective key strength, so HAWK would need double the key size to hold its original security level. That wipes out most of the efficiency case for HAWK as a candidate.
- Source: https://www.anthropic.com/research/discovering-cryptographic-weaknesses
- Why it matters: HAWK isn't deployed anywhere, so no production impact today. But it's a live example of a PQC candidate getting weakened after two years of expert review, which is exactly the kind of algorithm-level risk QS03 needs to keep tracking as candidates move toward standardisation.

### CVE-2026-46344, heap overflow in liboqs OQS_MEM_aligned_alloc

- Date: 2026
- Maps to: QS06, Insecure Migration and Hybrid Misuse
- Summary: A heap buffer overflow in liboqs's memory allocation path, in the function multiple PQC algorithm implementations use for aligned memory allocation. Fixes are merged in liboqs, oqs-provider, and cloudflare/circl.
- Source: CVE-2026-46344
- Why it matters: this is an implementation bug, not an algorithm weakness, sitting in one of the most widely used open source PQC library stacks. Organisations migrating to PQC often pull these libraries in directly. Worth flagging that the current Top 10 draft covers migration strategy and algorithm risk well, but implementation bugs in the actual PQC libraries used for migration look like a gap.