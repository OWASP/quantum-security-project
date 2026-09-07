## QS01:2026 - Harvest-Now-Decrypt-Later Exposure

**Description:**

Adversaries are already capturing encrypted traffic and stored ciphertext today, retaining it for future decryption once a cryptographically relevant quantum computer (CRQC) exists. The relevant operation is typically a key-establishment step protected by RSA, finite-field Diffie-Hellman, or elliptic-curve Diffie-Hellman - all broken in polynomial time by Shor's algorithm. Once the session key or wrapping key is recovered, the symmetric ciphertext it protects follows. This entry covers the full harvest surface: data captured **in transit**, where an adversary records ciphertext as it crosses an untrusted network boundary, and data already sitting **at rest**, where an adversary can exfiltrate an existing encrypted store at any point before a CRQC exists and simply wait. Both are the same exposure - ciphertext an adversary can obtain now, protected by an algorithm that fails later - differing only in where the ciphertext currently resides.

Mosca's inequality is the operative planning frame for both surfaces: if the time to migrate to quantum-safe cryptography (X) plus the required confidentiality lifetime of the data (Y) exceeds the time until a CRQC exists (Z), the data is already exposed regardless of exactly when a CRQC arrives. For in-transit data this determines how urgently a channel needs migrating; for at-rest data - archives, backups, regulated personal data, identity records, intellectual property - it determines how urgently existing stores need re-encryption, since a record with a multi-decade retention requirement can fail this inequality today even if migration starts immediately. Any organisation whose data has meaningful confidentiality lifetime - financial records, health data, source code, intelligence material, contractual or commercial secrets - must treat current TLS, VPN, and at-rest encryption based on these primitives as future-readable. The risk is concrete now, not contingent on quantum hardware availability.

**Common Examples of Vulnerability:**

1. Data sets whose confidentiality requirement extends beyond the CRQC planning horizon most regulators use (2030-2035) - health records, intellectual property, regulated personal data with long retention, government and defence data - protected only by classical public-key cryptography, whether in transit or already stored.
2. Transport and channel protection using vulnerable key establishment: TLS endpoints, VPN tunnels, encrypted backup channels, and satellite or microwave links relying on RSA, ECDH, or finite-field DH, traversing an untrusted boundary where they can be passively recorded and retained.
3. Archives and backups that pre-date current crypto policy, held under long retention requirements, where confidentiality lifetime plus migration lead time already exceeds the CRQC horizon by Mosca's inequality.
4. Data at rest encrypted with AES-256 but wrapped with an RSA or ECC key - the quantum-vulnerable layer sits above the symmetric key, so the attacker's target is the wrapping key, not the cipher.
5. Session encryption migrated to PQC while long-validity certificates and key-wrapping keys protecting archived data are left on classical algorithms.

**How to Prevent:**

1. Migrate vulnerable channels to hybrid post-quantum TLS using ML-KEM (FIPS 203) where the platform supports it.
2. Classify data by confidentiality lifetime, not just sensitivity - a medium-sensitivity record with a 30-year retention requirement may outrank a high-sensitivity record retained for two years, and Mosca's inequality is the tool for making that ranking explicit.
3. For the highest-priority archives identified by that classification, layer a PQC-protected encryption envelope over the existing classical encryption, sequenced against migration capacity.
4. Rotate symmetric data keys protected by quantum-vulnerable wrapping more frequently, to reduce the volume of data exposed by the recovery of any single wrapping key.
5. Reduce data retention where the business case allows - data not retained cannot be harvested or decrypted later.

**Example Attack Scenarios:**

Scenario #1: An adversary passively records TLS-protected traffic as it crosses an untrusted network boundary today. The handshake used RSA or ECDH key establishment. The captured ciphertext is archived. Once a CRQC becomes available, the adversary recovers the session key via Shor's algorithm and decrypts years of previously confidential traffic retroactively.

Scenario #2: A regulated entity retains personal records for a statutory 30-year period, encrypted with an RSA-wrapped AES key, held at rest rather than transmitted. An adversary exfiltrates the encrypted store today - no interception is needed, since the ciphertext already sits somewhere reachable. Applying Mosca's inequality, the confidentiality lifetime alone exceeds the CRQC horizon, so the records are effectively already compromised: the attacker recovers the wrapping key once a CRQC exists and decrypts the full archive, well within its required protection window.

**Reference Links:**

<!-- This entry merges the former QS01 (Harvest-Now-Decrypt-Later Exposure) and QS02 (Long-Lived Sensitive Data), consolidating the in-transit and at-rest confidentiality surfaces under Mosca's inequality as a single prioritisation frame, per the discussion in #11. QS02's integrity content (long-lived signed artefacts, identity and credential records) moves to QS03, which already covers signature trust. References verified 2026-08-05 against authoritative canonical sources. -->

1. [NIST FIPS 203 (ML-KEM)](https://csrc.nist.gov/pubs/fips/203/final): Key-establishment standard for post-quantum migration.
2. [NIST IR 8547 (Draft) - Transition to Post-Quantum Cryptography Standards](https://csrc.nist.gov/pubs/ir/8547/ipd): Transition planning guidance referencing Mosca's inequality.
3. [UK NCSC - Timelines for migration to post-quantum cryptography (March 2025)](https://www.ncsc.gov.uk/guidance/pqc-migration-timelines): Migration timelines calling out long-lived sensitive data as a priority class.
4. [EU Coordinated Implementation Roadmap for PQC (June 2025)](https://digital-strategy.ec.europa.eu/en/library/coordinated-implementation-roadmap-transition-post-quantum-cryptography): End-2030 deadline prohibiting standalone quantum-vulnerable PKC for high-risk use cases.
5. [White House National Security Memorandum 10 (NSM-10)](https://bidenwhitehouse.archives.gov/briefing-room/statements-releases/2022/05/04/national-security-memorandum-on-promoting-united-states-leadership-in-quantum-computing-while-mitigating-risks-to-vulnerable-cryptographic-systems/) and [OMB M-23-02](https://www.whitehouse.gov/wp-content/uploads/2022/11/M-23-02-M-Memo-on-Migrating-to-Post-Quantum-Cryptography.pdf): Cite HNDL as the primary driver of US migration urgency.
6. [EU Cyber Resilience Act - Regulation (EU) 2024/2847, Annex I](https://eur-lex.europa.eu/eli/reg/2024/2847/oj/eng): State-of-the-art protection required through the product support period, which for many products extends past 2030.

**Standards and Regulatory Mapping:**

> **TODO:** This section is carried over from the source document and is not part of `_template.md`. Confirm whether to keep it in the final entry format, and verify each standard/citation.

NIST FIPS 203 (ML-KEM) for key establishment. NIST IR 8547 (Draft) on transition planning and Mosca's inequality. NCSC Timelines for migration to post-quantum cryptography (March 2025), long-lived data prioritisation. EU Coordinated Implementation Roadmap (June 2025), end-2030 high-risk deadline and standalone-PKC prohibition. NSA CNSA 2.0 prioritises network encryption and long-lived secrets. NIS2 Article 21(2)(h) cryptographic policy obligation; DORA Article 9 confidentiality and integrity at rest, in use and in transit. NSM-10 and OMB M-23-02 cite HNDL as the migration driver. CRA Annex I requires state-of-the-art protection through the product support period.
