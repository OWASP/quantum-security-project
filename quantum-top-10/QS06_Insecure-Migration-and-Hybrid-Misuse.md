## QS06:2026 - Insecure Migration and Hybrid Misuse

**Description:**

Hybrid cryptography is the practical migration pattern, but it is widely misimplemented. A hybrid construction combines a classical algorithm and a PQC algorithm so that breaking the combination requires breaking both - for key encapsulation, deriving the session key from both an ECDH share and an ML-KEM share; for signatures, producing two signatures over the same artefact. Done correctly, hybrid provides defence in depth during transition. Done incorrectly, it weakens security, hides algorithm failures, becomes an attractive permanent state that delays full migration, or introduces new attack surface: silent fallback to classical when PQC negotiation fails, broken hybrid KEM constructions where key derivation does not require both inputs, weak parameter selection, non-constant-time PQ implementations vulnerable to timing side channels, and downgrade attacks during the transition window.

**Common Examples of Vulnerability:**

1. Drop-in replacement that silently falls back to classical when PQC negotiation fails.
2. Broken hybrid KEM constructions where the key derivation does not require both the classical and PQC inputs (selection or XOR rather than a KDF over both).
3. Weak parameter selection - ML-KEM-512 where ML-KEM-768 is required.
4. Non-constant-time PQ implementations vulnerable to timing side channels.
5. Downgrade attacks where an attacker strips PQC options during transition to force classical-only negotiation.
6. Pre-2024 hybrid Kyber/Dilithium implementations still in use after ML-KEM and ML-DSA superseded them.

**How to Prevent:**

1. Use standard hybrid constructions from IETF drafts and vendor implementations rather than custom combinations.
2. Combine hybrid outputs through a KDF over both inputs, not by selection or XOR.
3. Test for downgrade resistance - ensure PQC failure does not silently fall back to classical-only, and monitor for handshakes that revert.
4. Track hybrid deployments as transitional and plan their replacement with pure PQC ahead of regulatory deadlines for high-risk use cases.
5. Verify hybrid implementations use ML-KEM and ML-DSA, not predecessor draft algorithms, and rely on validated constant-time libraries (SymCrypt, BoringSSL, AWS-LC) rather than reference code.
6. Pilot hybrid PQC TLS in non-production for each architecture pattern, measuring handshake size, latency, throughput, and middlebox behaviour - larger PQC handshakes may be dropped or fragmented by firewalls and load balancers.

**Example Attack Scenarios:**

Scenario #1: A deployment negotiates hybrid PQC TLS but falls back to classical-only if the PQC option is absent. An active attacker strips the PQC key-share from the handshake, forcing a downgrade to ECDH, then records the session for later CRQC decryption - the hybrid protection is nullified by the fallback.

Scenario #2: A team builds a custom hybrid KEM that derives the session key from the ML-KEM share alone and merely attaches the ECDH share for "compatibility." Because breaking the combination requires breaking only the PQC part, an implementation flaw in the PQC library compromises the whole session, defeating the point of hybrid.

**Reference Links:**

<!-- References verified 2026-07-13 against authoritative canonical sources. -->

1. [NIST FIPS 203 (ML-KEM)](https://csrc.nist.gov/pubs/fips/203/final) and [FIPS 204 (ML-DSA)](https://csrc.nist.gov/pubs/fips/204/final): Standardised PQC primitives for hybrid use.
2. [IETF - Hybrid key exchange in TLS 1.3 (draft-ietf-tls-hybrid-design)](https://datatracker.ietf.org/doc/draft-ietf-tls-hybrid-design/): Standard hybrid KEM construction.
3. [IETF PQUIP Working Group](https://datatracker.ietf.org/wg/pquip/about/): Hybrid construction, downgrade resistance, and migration patterns.
4. [EU Coordinated Implementation Roadmap for PQC](https://digital-strategy.ec.europa.eu/en/library/coordinated-implementation-roadmap-transition-post-quantum-cryptography): End-2030 standalone-classical prohibition for high-risk cases (hybrid as interim).

**Standards and Regulatory Mapping:**

> **TODO:** This section is carried over from the source document and is not part of `_template.md`. Confirm whether to keep it in the final entry format, and verify each standard/citation.

NIST FIPS 203, FIPS 204. NCSC guidance treats hybrid as interim. EU Coordinated Implementation Roadmap end-2030 standalone-classical prohibition for high-risk cases. NIS2 Article 21(2)(h) state-of-the-art cryptography obligation, which supervisors increasingly read as requiring correctly-constructed hybrid during transition. DORA Article 9 confidentiality and integrity obligations apply to financial entities deploying hybrid TLS in production. IETF drafts on Hybrid PQ Key Exchange in TLS 1.3 and on Composite ML-DSA. IETF PQUIP working group migration documents.

