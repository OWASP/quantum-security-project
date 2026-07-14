## QS04:2026 - Absent Cryptographic Inventory and CBOM

**Description:**

Organisations cannot migrate cryptography they have not catalogued. Cryptographic inventory means a documented record of every place cryptography is used - applications, services, certificates, signing keys, protocols, libraries, hardware roots of trust, third-party components, and embedded keys in firmware or silicon - with algorithm, key length, key custody, lifecycle, and system owner recorded for each. Without it, scope is unknowable, prioritisation is impossible, and a controlled migration cannot be executed. Long-lived signing keys, embedded device keys, root and intermediate CA keys, code-signing keys, hardware-bound keys in HSMs and TPMs, and keys baked into firmware or silicon are all on the critical path and are most often where inventories fail. The absence of a structured cryptographic bill of materials (CBOM) is the single most common blocker to PQC migration in 2026.

**Common Examples of Vulnerability:**

1. No structured, current record of cryptographic usage across the estate - or one that exists only as unstructured prose rather than a CBOM-compatible format.
2. Inventories that omit whole classes: HSM and TPM contents, embedded devices, third-party components, and SaaS dependencies.
3. One-off inventories with no automated or scheduled process to keep them current, so they age out within months.
4. Records that capture algorithm and key length but not key custody, generation, or rotation procedures.
5. Keys baked into firmware, silicon, or sealed devices that cannot be enumerated through software discovery.

**How to Prevent:**

1. Adopt CBOM (Cryptography Bill of Materials) as the inventory format, aligning with CycloneDX or SPDX cryptographic extensions.
2. Use cryptographic discovery tooling to bootstrap the inventory: TLS scanners, certificate transparency logs, code-scanning for crypto-library usage, dependency scanners for SBOMs.
3. Assign named owners for each asset class: applications, certificates, signing infrastructure, embedded systems, third-party.
4. Make the inventory a living document - integrate updates into change management, procurement, and CI/CD pipelines.
5. Extend inventory to cover key generation, custody, rotation, and revocation, not just static algorithm and key-length data.

**Example Attack Scenarios:**

Scenario #1: An organisation begins PQC migration but has no CBOM. A forgotten intermediate CA and a set of firmware-embedded signing keys are never catalogued, so they are never migrated. After a CRQC exists, an attacker targets exactly these un-inventoried classical anchors, which remain trusted across the estate.

Scenario #2: A SaaS dependency terminates TLS with a quantum-vulnerable configuration the organisation never recorded because inventory covered only owned-and-operated systems. The unmanaged dependency becomes the harvest point for an HNDL adversary, invisible to the migration programme.

**Reference Links:**

<!-- References verified 2026-07-13 against authoritative canonical sources. -->

1. [UK NCSC - Timelines for migration to post-quantum cryptography](https://www.ncsc.gov.uk/guidance/pqc-migration-timelines): 2028 discovery-and-assessment milestone.
2. [EU Coordinated Implementation Roadmap for PQC](https://digital-strategy.ec.europa.eu/en/library/coordinated-implementation-roadmap-transition-post-quantum-cryptography): End-2026 inventory and dependency-map requirement.
3. [CISA, NSA, NIST - Quantum-Readiness: Migration to PQC fact sheet (August 2023)](https://www.cisa.gov/resources-tools/resources/quantum-readiness-migration-post-quantum-cryptography): Cryptographic inventory recommendation.
4. [CycloneDX CBOM specification](https://cyclonedx.org/capabilities/cbom/): Cryptography Bill of Materials schema.
5. [NIST IR 8547 (Draft)](https://csrc.nist.gov/pubs/ir/8547/ipd): Lifecycle management for transition planning.

**Standards and Regulatory Mapping:**

> **TODO:** This section is carried over from the source document and is not part of `_template.md`. Confirm whether to keep it in the final entry format, and verify each standard/citation.

NCSC Timelines for migration to post-quantum cryptography (2028 discovery milestone). EU Coordinated Implementation Roadmap end-2026 inventory and dependency-map requirement. CISA, NSA, NIST Quantum-Readiness fact sheet (August 2023) cryptographic inventory recommendation. NIST IR 8547 on lifecycle management. NIS2 Article 21(2)(h). DORA Article 9 ICT risk management obligation. CycloneDX and SPDX CBOM specifications.

