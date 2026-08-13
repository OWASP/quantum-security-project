# Top 10 Entry Template

## QSXX:2026 - Risk Name

**Description**

Describe the risk, the affected assets, the relevant threat or failure condition, and the potential impact.

**Scope**

Clearly define what this entry covers and, where useful, what it explicitly does **not** cover. Use this section to distinguish the risk from adjacent Top 10 entries.

**Evidence**

**Classification:** Demonstrated | Emerging | Theoretical

Provide a short justification and reference the supporting evidence.

See `EVIDENCE_CONVENTION.md` for the evidence convention.

**Common Examples**

Representative examples of the conditions, weaknesses, exposures, or implementation patterns associated with this risk.

1. Example
2. Example
3. Example

**Detection**

Describe practical ways an organisation can determine whether it is exposed to this risk.

Examples include:

- architecture reviews
- cryptographic inventories / CBOMs
- configuration analysis
- software composition analysis
- runtime monitoring
- infrastructure assessments

**How to Prevent**

Describe practical mitigations.

Where practical, recommendations should be:

- **Actionable** – clearly state what should be implemented.
- **Measurable** – allow objective assessment of implementation.
- **Verifiable** – capable of being independently assessed through testing, automation, review or audit.

Detailed verification procedures belong in supporting guidance rather than in the Top 10 itself.

**Example Attack Scenarios**

Scenario #1: A detailed scenario illustrating how an attacker could potentially exploit this vulnerability, including the attacker's actions and the potential outcomes.

Scenario #2: Another example of an attack scenario showing a different way the vulnerability could be exploited.

**Standards & Regulatory Mapping**

Identify the authoritative documents that define, recommend, or influence security practice for this risk.

Include relevant:

- standards;
- regulations;
- government guidance;
- recognised industry guidance.

Briefly describe how each document relates to the risk.

If no authoritative mapping currently exists, state this explicitly.

**Related Risks**

Reference other Quantum Security Top 10 entries where relevant and briefly explain the relationship.

- QSXX – Relationship
- QSYY – Relationship

Examples for QS01:
- **QS04 – Absent Cryptographic Inventory:** A cryptographic inventory is required to identify quantum-vulnerable encryption and prioritise HNDL exposure.
- **QS05 – Crypto-Agility Failures:** Poor crypto agility increases migration time, extending HNDL exposure.
- **QS06 – Insecure Migration and Hybrid Misuse:** Migration failures may leave HNDL exposure unresolved even after migration has begun.

**Reference Links**

Provide the primary references supporting the technical content of this entry.

Examples include:

- peer-reviewed research;
- technical reports;
- RFCs;
- vendor documentation;
- security advisories;
- CVEs;
- publicly available proof-of-concepts.

These references should support the statements, attack scenarios, mitigations, or evidence classification described in the entry.
