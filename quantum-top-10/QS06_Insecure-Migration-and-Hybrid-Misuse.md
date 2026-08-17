## QS06:2026 - Insecure Migration and Hybrid Misuse

**Description:**

Hybrid cryptography is the practical migration pattern: a construction that combines a classical algorithm with a PQC algorithm so that breaking the combination requires breaking both. For key encapsulation, the session key is derived from both an ECDHE share and an ML-KEM share; for signatures, two signatures are produced over the same artefact. Done correctly, hybrid provides defence in depth across the transition. The risks in getting it wrong are real, but they concentrate in three distinct layers that are often conflated, and the mitigations differ for each.

*Construction risk* applies to custom combiners, not to standardised ones. RFC 9954 fixes the hybrid key exchange construction for TLS 1.3, and the ECDHE-MLKEM groups - X25519MLKEM768, SecP256r1MLKEM768, SecP384r1MLKEM1024 - are implemented inside mainstream libraries, with OpenSSL 3.5 offering X25519MLKEM768 as a default keyshare. An organisation that selects a standard named group inherits a reviewed construction. An organisation that assembles its own combiner, deriving the session key by selecting one shared secret or XORing the two rather than running a KDF over both, discards the property that makes hybrid worth deploying: security is then no better than the weaker component.

*Negotiation and deployment risk* is where the transition window actually bites, and it is largely a configuration and operations problem rather than a cryptographic one. TLS 1.3 itself resists in-handshake tampering: the Finished MAC authenticates the full transcript, so an on-path attacker who edits the offered groups causes the handshake to abort, not to complete classically (RFC 8446 Section 4.1.3 covers version downgrade; the transcript hash covers parameter tampering). The exposure is instead the endpoint's own behaviour. Client fallback logic that answers a failed hybrid handshake with a fresh classical-only attempt hands an active attacker a downgrade without any tampered handshake completing - the attacker only has to make the first attempt fail. This is not conformant TLS 1.3 behaviour: a standard client does not retract its offered groups or retry classical-only on failure. The exposure comes specifically from non-standard or legacy fallback logic in a client or application, or from operators disabling hybrid under middlebox and fragmentation pressure to restore service. Preference lists that leave classical groups ahead of hybrid ones, and hybrid that is configured but never verified as negotiated, produce the same outcome with no attacker at all: traffic an organisation believes is quantum-safe is not. A session downgraded today is a session harvested today, which places this failure directly upstream of QS01. Larger PQC handshakes add an operational dimension - where a ClientHello is fragmented or dropped by middleboxes, the common resolution is to disable hybrid to restore service.

*Implementation risk* is demonstrated rather than hypothetical. KyberSlash recovered ML-KEM secret keys by exploiting secret-dependent division timings present in several implementations, including reference code. Clangover (CVE-2024-37880) showed a compiler optimising constant-time source into a secret-dependent branch, a leak invisible to source-level review because it exists only in the compiled binary.

Hybrid is also an interim state rather than a destination. The EU roadmap prohibits standalone quantum-vulnerable public-key cryptography for high-risk use cases after 2030, and deployments still running pre-standard Kyber or Dilithium drafts are running algorithms that ML-KEM and ML-DSA superseded.

**Common Examples of Vulnerability:**

1. Deployments that silently fall back to classical-only key exchange when PQC negotiation fails, with no alerting to distinguish a hybrid handshake from a classical one.
2. Client fallback logic that answers a failed hybrid handshake by retrying classical-only - a downgrade an active attacker can induce simply by making the first attempt fail, and which completes as an internally valid handshake with no failed negotiation left on the wire.
3. Hybrid enabled in configuration but never verified in production - group preference lists that place classical ahead of hybrid, or clients and middleboxes that quietly negotiate around it.
4. Larger PQC handshakes fragmented or dropped by middleboxes, load balancers, or firewalls, leading operators to disable hybrid to restore service.
5. Custom hybrid combiners that derive the session key by selection or XOR rather than a KDF over both the classical and PQC inputs.
6. PQC implementations vulnerable to timing side channels, including reference code affected by KyberSlash and binaries affected by compiler-introduced leaks such as Clangover (CVE-2024-37880).
7. Pre-standard Kyber or Dilithium draft implementations still in production after ML-KEM and ML-DSA superseded them, and weak parameter selection such as ML-KEM-512 where ML-KEM-768 is required.

**How to Prevent:**

1. Adopt standard named hybrid groups through a maintained library rather than assembling a combiner: the RFC 9954 construction with the ECDHE-MLKEM groups from `draft-ietf-tls-ecdhe-mlkem`. Where a custom combination is genuinely unavoidable, derive the key with a KDF over both inputs - never by selection or XOR.
2. Monitor the negotiated group in production and alert on classical-only handshakes. Hybrid that is configured but unobserved is indistinguishable from hybrid that is not working.
3. Test fallback behaviour explicitly: induce hybrid handshake failure in a test harness and confirm the client behaves as policy requires - failing closed where classical-only is not acceptable - rather than silently retrying classical.
4. Pilot for handshake size before rollout, measuring ClientHello fragmentation, middlebox behaviour, latency, and throughput for each architecture pattern, so capacity problems surface in test rather than as a production incident whose fastest fix is turning hybrid off.
5. Use validated constant-time implementations rather than reference code, prefer libraries hardened against compiler-introduced timing leaks, and track PQC implementation advisories as they are published.
6. Verify that deployments use ML-KEM and ML-DSA at the required parameter sets, not superseded pre-standard Kyber or Dilithium drafts.
7. Record which systems run hybrid and treat that record as a transitional inventory, planning pure-PQC replacement ahead of the regulatory deadlines that apply to high-risk use cases.

**Example Attack Scenarios:**

Scenario #1: A client configured with non-standard classical-only retry logic falls back to a classical-only handshake when its hybrid handshake fails. An active attacker interferes with the initial hybrid attempt - tampering with the offered groups aborts the handshake under TLS 1.3 transcript authentication, which is all the attacker needs - and the client falls back, completing a fresh, internally valid classical handshake. The attacker records that session for decryption once a CRQC exists. Because the fallback is the client's own behaviour, no tampered handshake ever completes on the wire: there is only a clean classical session that nothing distinguishes from normal unless the negotiated group is monitored.

Scenario #2: A team builds a custom hybrid KEM that derives the session key from the ML-KEM share alone and attaches the ECDHE share for compatibility. Because breaking the combination requires breaking only the PQC component, an implementation flaw in the PQC library compromises the whole session - the classical share contributes nothing, and the construction provides none of the defence in depth it was adopted for.

Scenario #3: Following a hybrid TLS rollout, an organisation sees intermittent handshake failures where a legacy middlebox drops the larger ClientHello. Under incident pressure the fastest remedy is to remove the hybrid groups from the affected path. The change is never revisited, the estate is recorded as migrated, and an attacker harvests traffic from a segment that reverted to classical key exchange months earlier.

**Reference Links:**

<!-- References verified 2026-07-27 against authoritative primary sources. RFC 9954 supersedes draft-ietf-tls-hybrid-design (published July 2026); draft-ietf-tls-ecdhe-mlkem replaces draft-kwiatkowski-tls-ecdhe-mlkem and is approved, awaiting RFC publication. -->

1. [RFC 9954 - Hybrid Key Exchange in TLS 1.3](https://www.rfc-editor.org/info/rfc9954): The standardised hybrid construction for TLS 1.3 (Informational, July 2026). Supersedes `draft-ietf-tls-hybrid-design`.
2. [draft-ietf-tls-ecdhe-mlkem - Post-quantum hybrid ECDHE-MLKEM key agreement for TLS 1.3](https://datatracker.ietf.org/doc/draft-ietf-tls-ecdhe-mlkem/): Defines the X25519MLKEM768, SecP256r1MLKEM768, and SecP384r1MLKEM1024 groups. IESG-approved and awaiting RFC publication; replaces `draft-kwiatkowski-tls-ecdhe-mlkem`.
3. [RFC 8446 - TLS 1.3](https://www.rfc-editor.org/rfc/rfc8446): Section 4.1.3 is the version-downgrade sentinel (`ServerHello.random`); handshake integrity against group/parameter tampering comes from the Finished MAC over the transcript hash (Section 4.4.4 and the key schedule) - a separate mechanism. Together they confine the practical downgrade path to endpoint fallback behaviour rather than in-handshake tampering.
4. [NIST FIPS 203 (ML-KEM)](https://csrc.nist.gov/pubs/fips/203/final) and [FIPS 204 (ML-DSA)](https://csrc.nist.gov/pubs/fips/204/final): Standardised PQC primitives for hybrid use.
5. [Bernstein et al. - KyberSlash: Exploiting secret-dependent division timings in Kyber implementations](https://eprint.iacr.org/2024/1049): Secret-key recovery from timing leaks in ML-KEM implementations, including reference code (IACR TCHES).
6. [CVE-2024-37880 (Clangover)](https://www.cve.org/CVERecord?id=CVE-2024-37880): Compiler optimisation of constant-time source into a secret-dependent branch in the Kyber reference implementation.
7. [OpenSSL 3.5 release notes](https://openssl-library.org/news/openssl-3.5-notes/): Default TLS groups changed to include and prefer hybrid PQC KEM groups, with X25519MLKEM768 offered as a default keyshare.
8. [IETF PQUIP Working Group](https://datatracker.ietf.org/wg/pquip/about/): Hybrid construction, downgrade resistance, and migration patterns.
9. [EU Coordinated Implementation Roadmap for PQC](https://digital-strategy.ec.europa.eu/en/library/coordinated-implementation-roadmap-transition-post-quantum-cryptography): End-2030 standalone-classical prohibition for high-risk cases, with hybrid as an interim state.

**Standards and Regulatory Mapping:**

> **TODO:** This section is carried over from the source document and is not part of `_template.md`. Confirm whether to keep it in the final entry format, and verify each standard/citation.

NIST FIPS 203, FIPS 204. RFC 9954 standardises the TLS 1.3 hybrid key exchange construction; `draft-ietf-tls-ecdhe-mlkem` defines the ECDHE-MLKEM groups and is awaiting RFC publication. NCSC guidance treats hybrid as interim. EU Coordinated Implementation Roadmap end-2030 standalone-classical prohibition for high-risk cases. NIS2 Article 21(2)(h) state-of-the-art cryptography obligation, which supervisors increasingly read as requiring correctly-constructed hybrid during transition. DORA Article 9 confidentiality and integrity obligations apply to financial entities deploying hybrid TLS in production. IETF PQUIP working group migration documents; IETF LAMPS work on Composite ML-DSA for hybrid signatures.
