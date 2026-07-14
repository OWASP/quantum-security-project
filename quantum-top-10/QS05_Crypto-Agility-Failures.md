## QS05:2026 - Crypto-Agility Failures

**Description:**

Cryptographic agility is the engineering property that allows an algorithm, key size, or parameter set to be replaced without rebuilding the system around it. Most production systems hard-code cryptographic algorithms, key sizes, parameter sets, and key formats deep in code, configuration, hardware, and protocols. Systems that lack agility - algorithms baked into source, protocol logic, custom data formats, or non-updatable firmware - require replacement, not migration. As ML-KEM, ML-DSA, and SLH-DSA move into production and parameter sets continue to evolve through subsequent NIST rounds (Falcon, HQC, others), organisations without negotiable algorithms, abstracted crypto APIs, version-aware protocols, and dynamic policy will be unable to respond to standards updates, broken parameter sets, or future migrations. Crypto-agility is itself a security control. Symmetric primitives are more quantum-resilient, but "quantum is only a public-key problem" must not be read as "symmetric needs no work": AES key sizes, hash strengths, MAC lengths, and KDFs all need review end-to-end.

**Common Examples of Vulnerability:**

1. Hard-coded algorithm identifiers in source: `RSA`, `sha256WithRSAEncryption`, explicit OIDs embedded in protocol logic.
2. Proprietary protocols that fix algorithms rather than negotiate them (unlike TLS 1.3).
3. Embedded devices with no tested path to receive firmware updates that change cryptographic primitives.
4. Systems that cannot select among ML-KEM-512/768/1024 parameter sets without code changes.
5. Symmetric weaknesses left unaddressed: AES-128 on long-lived links, truncated MACs, weak KDFs, unsafe key derivation in hybrid KEMs.

**How to Prevent:**

1. Refactor algorithm selection out of business logic into a cryptographic abstraction layer.
2. Standardise on libraries that ship PQC: OpenSSL 3.x with PQC providers, BoringSSL, SymCrypt, JDK 24+, .NET 10+.
3. Avoid custom protocol implementations where standard alternatives exist - prefer TLS 1.3 with hybrid PQC cipher suites over a bespoke handshake.
4. Test the rotation path end-to-end in non-production, including embedded and mobile clients, to surface agility blockers before migration.
5. Include agility requirements in procurement: new contracts should require PQC support and demonstrable algorithm replacement on a defined timescale.
6. Track IETF PQUIP and TLS working-group output for documented migration patterns and failure modes.

**Example Attack Scenarios:**

Scenario #1: A NIST parameter set for a deployed PQC algorithm is later found weak. An organisation that hard-coded the algorithm cannot swap it without a full rebuild-and-reship cycle across firmware it cannot update in the field, leaving vulnerable systems exposed for the length of a replacement project.

Scenario #2: A team adds a crypto abstraction layer but never tests rotation. When migration day arrives, an embedded client that pins a classical algorithm identifier silently fails PQC negotiation and continues on classical crypto, and the untested "agility" turns out to be theoretical - the attacker targets the client that never actually migrated.

**Reference Links:**

<!-- References verified 2026-07-13 against authoritative canonical sources. NCSC has no standalone crypto-agility page; the PQC migration timelines page is the appropriate anchor. -->

1. [CISA, NSA, NIST - Quantum-Readiness fact sheet](https://www.cisa.gov/resources-tools/resources/quantum-readiness-migration-post-quantum-cryptography): Cryptographic agility recommendation.
2. [UK NCSC - Timelines for migration to post-quantum cryptography](https://www.ncsc.gov.uk/guidance/pqc-migration-timelines): Agility expectations within PQC migration guidance.
3. [EU Coordinated Implementation Roadmap for PQC](https://digital-strategy.ec.europa.eu/en/library/coordinated-implementation-roadmap-transition-post-quantum-cryptography): Explicit agility expectation.
4. [IETF PQUIP Working Group](https://datatracker.ietf.org/wg/pquip/about/): Agility documents and migration patterns.
5. [IETF - Hybrid key exchange in TLS 1.3 (draft-ietf-tls-hybrid-design)](https://datatracker.ietf.org/doc/draft-ietf-tls-hybrid-design/): Hybrid KEM negotiation.

**Standards and Regulatory Mapping:**

> **TODO:** This section is carried over from the source document and is not part of `_template.md`. Confirm whether to keep it in the final entry format, and verify each standard/citation.

CISA, NSA, NIST Quantum-Readiness fact sheet (cryptographic agility recommendation). NCSC guidance on crypto-agility. EU Coordinated Implementation Roadmap explicit agility expectation. NIS2 Article 21(2)(h) implies agility through state-of-the-art and risk-based requirements. IETF PQUIP working group agility documents; IETF TLS working group on hybrid KEM and signature negotiation.

