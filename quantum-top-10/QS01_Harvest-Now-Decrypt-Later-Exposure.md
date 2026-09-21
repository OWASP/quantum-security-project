## QS01:2026 - Harvest-Now-Decrypt-Later Exposure

**Description:**

Adversaries are already capturing encrypted traffic and stored ciphertext today, retaining it for future decryption once a cryptographically relevant quantum computer (CRQC) exists. The relevant operation is typically a key-establishment step protected by RSA, finite-field Diffie-Hellman, or elliptic-curve Diffie-Hellman - all broken in polynomial time by Shor's algorithm. Once the session key is recovered, the symmetric ciphertext follows. Any organisation whose data has meaningful confidentiality lifetime - financial records, health data, source code, intelligence material, contractual or commercial secrets - must treat current TLS, VPN, and at-rest encryption based on these primitives as future-readable. The risk is concrete now, not contingent on quantum hardware availability.

**Scope:**

This entry covers exposure created by recording classically-encrypted data or key-establishment traffic today for decryption once a CRQC exists. It does not cover the downstream consequences of that data remaining sensitive for a long time (see QS02), the organisational inventory work needed to find exposed systems (see QS04), the ability of an organisation to migrate quickly (see QS05), or defects introduced during the migration itself (see QS06). QS01 is the exposure created by capture-now; QS02 is the reason the captured data still matters years later.

**Evidence:**

**Classification:** Demonstrated

**Justification:** Passive recording of RSA/ECDH/DH-protected traffic and ciphertext is routine, publicly documented adversary tradecraft, and Shor's algorithm's ability to break these primitives is a proven mathematical result - not a speculative one. The only undemonstrated component is the future availability of a CRQC capable of running Shor's algorithm at cryptographically relevant key sizes.

**Primary Evidence:** NSM-10 and OMB M-23-02 (references below) cite HNDL as the primary driver of US federal migration policy, and NCSC/EU migration guidance treats bulk-recorded ciphertext as an active exposure today.

**Common Examples of Vulnerability:**

1. Data sets whose confidentiality requirement extends beyond 2030 - health records, intellectual property, regulated personal data with long retention, government and defence data - protected only by classical public-key cryptography.
2. Transport and channel protection using vulnerable key establishment: TLS endpoints, VPN tunnels, encrypted backup channels, archival storage encryption, and satellite or microwave links relying on RSA, ECDH, or finite-field DH.
3. Encrypted traffic transiting an untrusted boundary where it can be passively recorded and retained for later decryption.
4. Session encryption migrated to PQC while long-validity certificates and key-wrapping keys are left on classical algorithms.

**Detection:**

1. Cryptographic inventory / CBOM sweep to identify TLS endpoints, VPN tunnels, backup channels, and archival storage still negotiating RSA, finite-field DH, or ECDH key establishment (see QS04).
2. Configuration analysis of TLS/VPN gateways to confirm whether hybrid PQC key establishment (e.g. ML-KEM) is offered and preferred over classical-only cipher suites.
3. Data classification review to identify datasets whose confidentiality requirement extends past the point a CRQC is plausible, so migration effort can be prioritised by exposure window rather than by system age.
4. Network boundary review to identify points where traffic can be passively recorded (peering points, cloud egress, satellite/microwave links) and cross-reference against the key-establishment methods in use there.

**How to Prevent:**

1. Migrate vulnerable channels to hybrid post-quantum TLS using ML-KEM (FIPS 203) where the platform supports it.
2. For data at rest, layer a PQC-protected encryption envelope over existing classical encryption for the highest-sensitivity datasets.
3. Rotate symmetric data keys protected by quantum-vulnerable wrapping more frequently to reduce the volume exposed by any single recovered key.
4. Reduce data retention where the business case allows - data not retained cannot be decrypted later.

**Example Attack Scenarios:**

Scenario #1: An adversary passively records TLS-protected traffic as it crosses an untrusted network boundary today. The handshake used RSA or ECDH key establishment. The captured ciphertext is archived. Once a CRQC becomes available, the adversary recovers the session key via Shor's algorithm and decrypts years of previously confidential traffic retroactively.

Scenario #2: An organisation encrypts long-retention backups at rest with AES-256, but the AES data key is wrapped with RSA. An attacker exfiltrates the encrypted backups and the wrapped keys. Because the quantum-vulnerable layer is the RSA key-wrapping - not the symmetric cipher - the attacker recovers the wrapping key with a future CRQC and unwraps the AES keys, exposing the entire archive.

**Standards & Regulatory Mapping:**

- **NIST FIPS 203 (ML-KEM):** Key-establishment standard organisations migrate to in order to close HNDL exposure.
- **UK NCSC - Timelines for migration to post-quantum cryptography (March 2025):** Migration timelines calling out long-lived sensitive data as a priority class.
- **EU Coordinated Implementation Roadmap for PQC (June 2025):** End-2030 deadline prohibiting standalone quantum-vulnerable PKC for high-risk use cases.
- **NSA CNSA 2.0:** Prioritises network encryption and long-lived secrets for early migration.
- **NIS2 Article 21(2)(h) and DORA Article 9:** Cryptographic policy and confidentiality/integrity obligations that HNDL exposure puts at risk.
- **NSM-10 and OMB M-23-02:** Cite HNDL explicitly as the driver of US federal migration urgency.

**Related Risks:**

- **QS02 - Long-Lived Sensitive Data:** QS01 is the capture event; QS02 is why the captured data remains a liability for years afterward.
- **QS04 - Absent Cryptographic Inventory and CBOM:** A cryptographic inventory is required to identify quantum-vulnerable key establishment and prioritise HNDL exposure.
- **QS05 - Crypto-Agility Failures:** Poor crypto-agility increases migration time, extending the HNDL exposure window.
- **QS06 - Insecure Migration and Hybrid Misuse:** Migration failures may leave HNDL exposure unresolved even after migration has begun.

**Reference Links:**

<!-- References verified 2026-07-13 against authoritative canonical sources. -->

1. [NIST FIPS 203 (ML-KEM)](https://csrc.nist.gov/pubs/fips/203/final): Key-establishment standard for post-quantum migration.
2. [UK NCSC - Timelines for migration to post-quantum cryptography (March 2025)](https://www.ncsc.gov.uk/guidance/pqc-migration-timelines): Migration timelines calling out long-lived sensitive data as a priority class.
3. [EU Coordinated Implementation Roadmap for PQC (June 2025)](https://digital-strategy.ec.europa.eu/en/library/coordinated-implementation-roadmap-transition-post-quantum-cryptography): End-2030 deadline prohibiting standalone quantum-vulnerable PKC for high-risk use cases.
4. [White House National Security Memorandum 10 (NSM-10)](https://bidenwhitehouse.archives.gov/briefing-room/statements-releases/2022/05/04/national-security-memorandum-on-promoting-united-states-leadership-in-quantum-computing-while-mitigating-risks-to-vulnerable-cryptographic-systems/) and [OMB M-23-02](https://www.whitehouse.gov/wp-content/uploads/2022/11/M-23-02-M-Memo-on-Migrating-to-Post-Quantum-Cryptography.pdf): Cite HNDL as the primary driver of US migration urgency.

