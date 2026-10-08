## QS05:2026 - Crypto-Agility Failures

**Description:**

Cryptographic agility is the engineering property that allows an algorithm, key size, or parameter set to be replaced without rebuilding the system around it. Most production systems hard-code cryptographic algorithms, key sizes, parameter sets, and key formats deep in code, configuration, hardware, and protocols. Systems that lack agility - algorithms baked into source, protocol logic, custom data formats, or non-updatable firmware - require replacement, not migration. As ML-KEM, ML-DSA, and SLH-DSA move into production and parameter sets continue to evolve through subsequent NIST rounds (Falcon, HQC, others), organisations without negotiable algorithms, abstracted crypto APIs, version-aware protocols, and dynamic policy will be unable to respond to standards updates, broken parameter sets, or future migrations. Crypto-agility is itself a security control. Like any other control, it must have a named owner, a governing policy, and a measurable definition of agility: a system can be technically agile but operationally unable to exercise that agility if no one is held accountable for authorising, driving, verifying and validating cryptographic changes.

Symmetric primitives are more quantum-resilient, but "quantum is only a public-key problem" must not be read as "symmetric needs no work": AES key sizes, hash strengths, MAC lengths, and KDFs all need review end-to-end.

**Common Examples of Vulnerability:**

1. Hard-coded algorithm identifiers in source: `RSA`, `sha256WithRSAEncryption`, explicit OIDs embedded in protocol logic.
2. Proprietary protocols that fix algorithms rather than negotiate them (unlike TLS 1.3).
3. Embedded devices with no tested path to receive firmware updates that change cryptographic primitives.
4. Systems that cannot select among ML-KEM-512/768/1024 parameter sets without code changes.
5. Symmetric weaknesses left unaddressed: AES-128 on long-lived links, truncated MACs, weak KDFs, unsafe key derivation in hybrid KEMs.
6. Data formats and fields sized for classical keys and signatures, which an abstraction layer cannot resize: signed tokens in cookies and headers, database columns, signed QR codes.

**How to Prevent:**

1. Refactor algorithm selection out of business logic into a cryptographic abstraction layer.
2. Standardise on libraries that ship PQC: OpenSSL 3.x with PQC providers, BoringSSL, SymCrypt, JDK 24+, .NET 10+.
3. Avoid custom protocol implementations where standard alternatives exist - prefer TLS 1.3 with hybrid PQC cipher suites over a bespoke handshake.
4. Test the rotation path end-to-end in non-production, including embedded and mobile clients, to surface agility blockers before migration.
5. Include agility requirements in procurement: new contracts should require PQC support and demonstrable algorithm replacement on a defined timescale.
6. Track IETF PQUIP and TLS working-group output for documented migration patterns and failure modes.
7. Assign accountable owner and develop governing policy for cryptographic change, identify who will authorise it and maintain a tested runbook for time-pressure events.
8. Make agility measurable and inventory-bounded: track cryptographic time-to-rotate (cMTTR) as the primary metric, capped by the share of the estate visible in the CBOM (QS04), with abstraction-layer coverage and negotiation posture reported wherever useful.
9. Record size-constrained fields and formats in the cryptographic inventory (QS04) and test them against the encoded sizes of every parameter set the system may adopt (FIPS 203, FIPS 204, FIPS 205). Prefer designs whose footprint does not depend on the algorithm, such as an opaque session identifier instead of a self-contained signed token.

**Example Attack Scenarios:**

Scenario #1: A NIST parameter set for a deployed PQC algorithm is later found weak. An organisation that hard-coded the algorithm cannot swap it without a full rebuild-and-reship cycle across firmware it cannot update in the field, leaving vulnerable systems exposed for the length of a replacement project.

Scenario #2: A team adds a crypto abstraction layer but never tests rotation. When migration day arrives, an embedded client that pins a classical algorithm identifier silently fails PQC negotiation and continues on classical crypto, and the untested "agility" turns out to be theoretical - the attacker targets the client that never actually migrated.

Scenario #3: An organisation has an abstraction layer, negotiable protocols, and a documented rotation runbook. Therefore, technically it is agile. No accountable owner is assigned to watch the triggers, so a broken parameter set, a CNSA 2.0 milestone, and a binding regulatory deadline all pass unobserved, and the runbook is not opened until an audit forces the question. The exposure is undetected triggers rather than slow execution. By then, the organisation is in breach with a rotation it has never timed against the deadline.

Scenario #4: A team migrates JWT session tokens from ES256 to ML-DSA-65 through its crypto abstraction layer. The larger signature exceeds cookie size limits, so browsers silently discard the cookie and users authenticate successfully but no session is established. Under incident pressure the change is rolled back, leaving the system on a quantum-vulnerable signature scheme until the session design is replaced with one that does not depend on signature size.

**Reference Links:**

<!-- References verified 2026-07-27 against authoritative primary sources. NCSC has no standalone crypto-agility page; the PQC migration timelines page is the appropriate anchor, and its 2028/2031/2035 milestones were confirmed against the live page. Reference #5 updated: draft-ietf-tls-hybrid-design was published as RFC 9954 in July 2026. -->

1. [CISA, NSA, NIST - Quantum-Readiness fact sheet](https://www.cisa.gov/resources-tools/resources/quantum-readiness-migration-post-quantum-cryptography): Cryptographic agility recommendation.
2. [UK NCSC - Timelines for migration to post-quantum cryptography](https://www.ncsc.gov.uk/guidance/pqc-migration-timelines): Agility expectations within PQC migration guidance.
3. [EU Coordinated Implementation Roadmap for PQC](https://digital-strategy.ec.europa.eu/en/library/coordinated-implementation-roadmap-transition-post-quantum-cryptography): Explicit agility expectation.
4. [IETF PQUIP Working Group](https://datatracker.ietf.org/wg/pquip/about/): Agility documents and migration patterns.
5. [IETF - Hybrid key exchange in TLS 1.3 (draft-ietf-tls-hybrid-design)](https://datatracker.ietf.org/doc/draft-ietf-tls-hybrid-design/): Hybrid KEM negotiation.
6. [NIST CSWP 39upd1 - Considerations for Achieving Crypto Agility](https://csrc.nist.gov/pubs/cswp/39/upd1/considerations-for-achieving-crypto-agility/final): Ownership, measurable agility, resource considerations, and agility-aware design (Sections 6.1 and 6.2).
7. [RFC 9954 - Hybrid Key Exchange in TLS 1.3](https://www.rfc-editor.org/info/rfc9954): Hybrid KEM negotiation (Informational, July 2026). Supersedes `draft-ietf-tls-hybrid-design`.
8. [OWASP ASVS 5.0.0 - V3.3 Cookie Setup](https://github.com/OWASP/ASVS/blob/v5.0.0_release/5.0/en/0x12-V3-Web-Frontend-Security.md#v33-cookie-setup): Requirement 3.3.5 - browsers do not store cookies whose name and value together exceed 4,096 bytes.
9. [NIST FIPS 204 (ML-DSA)](https://csrc.nist.gov/pubs/fips/204/final): Key and signature sizes per parameter set (Table 2); an ML-DSA-65 signature is 3,309 bytes (4,412 characters once base64url-encoded).
10. [RFC 9964 - ML-DSA for JOSE and COSE](https://www.rfc-editor.org/info/rfc9964): Registers ML-DSA-44, ML-DSA-65 and ML-DSA-87 as JWS algorithms (Proposed Standard, May 2026).

**Standards and Regulatory Mapping:**

> **TODO:** This section is carried over from the source document and is not part of `_template.md`. Confirm whether to keep it in the final entry format, and verify each standard/citation.

NIST CSWP 39upd1, Considerations for Achieving Crypto Agility: Strategies and Practices (Final, 29 June 2026, superseding CSWP 39 of 19 December 2025), is the authoritative crypto-agility anchor and directly addresses ownership, measurable agility, resource considerations, and agility-aware design. Companion anchors: NIST IR 8547 for migration-timeline framing, NSA CNSA 2.0 for dated deadlines, and NIST SP 1800-38 for practice detail. Offered subject to the leads' URL verification process.

CISA, NSA, NIST Quantum-Readiness fact sheet (cryptographic agility recommendation). NCSC guidance on crypto-agility. EU Coordinated Implementation Roadmap explicit agility expectation. NIS2 Article 21(2)(h) implies agility through state-of-the-art and risk-based requirements. IETF PQUIP working group agility documents; IETF TLS working group on hybrid KEM and signature negotiation.

