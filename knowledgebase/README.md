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

Where they materially clarify a finding, entries should also record its current
status, evidence level, production impact, Top 10 implication, last verification
date, and links to primary sources.

Anyone can add entries. Open a PR against this file with the same format.

---

## Entries

### HAWK signature scheme, improved key recovery attack and withdrawal

- Published: 28 Jul 2026
- Last verified: 3 Aug 2026
- Status: Withdrawn from NIST's additional digital signature standardisation
  process on 29 Jul 2026
- Evidence: Demonstrated - paper, end-to-end implementation, coordinated
  disclosure, and confirmation by the HAWK team
- Maps to: Primary - QS03, Vulnerable Signatures and Code-Signing; secondary -
  QS05, Crypto-Agility Failures
- Summary: Anthropic researchers developed an improved key recovery attack
  against HAWK, then a NIST round-3 post-quantum signature candidate. In the
  paper's gate-count model, it lowers the estimated attack cost for HAWK-512 from
  2^150 to 2^108 and for HAWK-1024 from 2^288 to 2^182. The researchers also
  recovered a HAWK-256 secret key end to end in a few hours on one server. The
  HAWK team confirmed that the attack approximately halves the lattice-reduction
  block size required for key recovery and withdrew the candidate the following
  day.
- Production impact: None identified. HAWK was a candidate rather than a
  deployed standard, and the attack does not transfer to Falcon, ML-DSA, or
  lattice-based cryptography generally.
- Top 10 implication: Supports QS03 by showing that post-quantum signature
  candidates require continuing cryptanalysis, and QS05 by demonstrating why
  systems must be able to change algorithms and parameters. It does not require
  a new Top 10 category.
- Sources:
  - [Anthropic overview](https://www.anthropic.com/research/discovering-cryptographic-weaknesses)
  - [HAWK-n Key Recovery Reduces to SVP in Dimension n/2 + 1](https://anthropic.com/document/hawk_key_recovery.pdf)
  - [Demonstration implementation](https://github.com/anthropics/cryptography-research-demo)
  - [Coordinated disclosure and HAWK team withdrawal](https://groups.google.com/a/list.nist.gov/g/pqc-forum/c/2r2u6SbHun4/m/0_I2KOZ_CQAJ)
  - [NIST Round 3 additional signature candidates](https://csrc.nist.gov/projects/pqc-dig-sig/round-3-additional-signatures)
- Why it matters: This is a complete example of the PQC review lifecycle:
  expert-reviewed candidate, improved cryptanalysis, reproducible validation,
  coordinated disclosure, and withdrawal before standardisation or deployment.

### CVE-2026-46344, heap overflow in liboqs OQS_MEM_aligned_alloc

- Date: 2026
- Maps to: QS06, Insecure Migration and Hybrid Misuse
- Summary: A heap buffer overflow in liboqs's memory allocation path, in the function multiple PQC algorithm implementations use for aligned memory allocation. Fixes are merged in liboqs, oqs-provider, and cloudflare/circl.
- Source: CVE-2026-46344
- Why it matters: this is an implementation bug, not an algorithm weakness, sitting in one of the most widely used open source PQC library stacks. Organisations migrating to PQC often pull these libraries in directly. Worth flagging that the current Top 10 draft covers migration strategy and algorithm risk well, but implementation bugs in the actual PQC libraries used for migration look like a gap.
