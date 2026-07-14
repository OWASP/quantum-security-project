## QS01:2026 - Harvest-Now-Decrypt-Later Exposure

**Description:**

Adversaries are already capturing encrypted traffic and stored ciphertext today, retaining it for future decryption once a cryptographically relevant quantum computer (CRQC) exists. The relevant operation is typically a key-establishment step protected by RSA, finite-field Diffie-Hellman, or elliptic-curve Diffie-Hellman - all broken in polynomial time by Shor's algorithm. Once the session key is recovered, the symmetric ciphertext follows. Any organisation whose data has meaningful confidentiality lifetime - financial records, health data, source code, intelligence material, contractual or commercial secrets - must treat current TLS, VPN, and at-rest encryption based on these primitives as future-readable. The risk is concrete now, not contingent on quantum hardware availability.

**Common Examples of Vulnerability:**

1. Data sets whose confidentiality requirement extends beyond 2030 - health records, intellectual property, regulated personal data with long retention, government and defence data - protected only by classical public-key cryptography.
2. Transport and channel protection using vulnerable key establishment: TLS endpoints, VPN tunnels, encrypted backup channels, archival storage encryption, and satellite or microwave links relying on RSA, ECDH, or finite-field DH.
3. Encrypted traffic transiting an untrusted boundary where it can be passively recorded and retained for later decryption.
4. Session encryption migrated to PQC while long-validity certificates and key-wrapping keys are left on classical algorithms.

**How to Prevent:**

1. Migrate vulnerable channels to hybrid post-quantum TLS using ML-KEM (FIPS 203) where the platform supports it.
2. For data at rest, layer a PQC-protected encryption envelope over existing classical encryption for the highest-sensitivity datasets.
3. Rotate symmetric data keys protected by quantum-vulnerable wrapping more frequently to reduce the volume exposed by any single recovered key.
4. Reduce data retention where the business case allows - data not retained cannot be decrypted later.

**Example Attack Scenarios:**

Scenario #1: An adversary passively records TLS-protected traffic as it crosses an untrusted network boundary today. The handshake used RSA or ECDH key establishment. The captured ciphertext is archived. Once a CRQC becomes available, the adversary recovers the session key via Shor's algorithm and decrypts years of previously confidential traffic retroactively.

Scenario #2: An organisation encrypts long-retention backups at rest with AES-256, but the AES data key is wrapped with RSA. An attacker exfiltrates the encrypted backups and the wrapped keys. Because the quantum-vulnerable layer is the RSA key-wrapping - not the symmetric cipher - the attacker recovers the wrapping key with a future CRQC and unwraps the AES keys, exposing the entire archive.

**Reference Links:**

<!-- References verified 2026-07-13 against authoritative canonical sources. -->

1. [NIST FIPS 203 (ML-KEM)](https://csrc.nist.gov/pubs/fips/203/final): Key-establishment standard for post-quantum migration.
2. [UK NCSC - Timelines for migration to post-quantum cryptography (March 2025)](https://www.ncsc.gov.uk/guidance/pqc-migration-timelines): Migration timelines calling out long-lived sensitive data as a priority class.
3. [EU Coordinated Implementation Roadmap for PQC (June 2025)](https://digital-strategy.ec.europa.eu/en/library/coordinated-implementation-roadmap-transition-post-quantum-cryptography): End-2030 deadline prohibiting standalone quantum-vulnerable PKC for high-risk use cases.
4. [White House National Security Memorandum 10 (NSM-10)](https://bidenwhitehouse.archives.gov/briefing-room/statements-releases/2022/05/04/national-security-memorandum-on-promoting-united-states-leadership-in-quantum-computing-while-mitigating-risks-to-vulnerable-cryptographic-systems/) and [OMB M-23-02](https://www.whitehouse.gov/wp-content/uploads/2022/11/M-23-02-M-Memo-on-Migrating-to-Post-Quantum-Cryptography.pdf): Cite HNDL as the primary driver of US migration urgency.

**Standards and Regulatory Mapping:**

> **TODO:** This section is carried over from the source document and is not part of `_template.md`. Confirm whether to keep it in the final entry format, and verify each standard/citation.

NIST FIPS 203 (ML-KEM) for key establishment. NCSC Timelines for migration to post-quantum cryptography (March 2025). EU Coordinated Implementation Roadmap (June 2025), end-2030 high-risk deadline. NSA CNSA 2.0 prioritises network encryption and long-lived secrets. NIS2 Article 21(2)(h) cryptographic policy obligation; DORA Article 9 confidentiality and integrity at rest, in use and in transit. NSM-10 and OMB M-23-02 cite HNDL as the migration driver.

