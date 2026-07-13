## QS02:2026 - Long-Lived Sensitive Data

**Description:**

Data with a confidentiality requirement that extends past the projected arrival of a CRQC cannot be protected by today's public-key primitives alone. Mosca's inequality is the operative planning frame: if the time to migrate to quantum-safe cryptography (X) plus the required confidentiality lifetime of the data (Y) exceeds the time until a CRQC exists (Z), the organisation is already exposed. For data with multi-decade confidentiality requirements - archives, backups, regulated personal data, identity records, intellectual property, signed material whose integrity must hold for years - current public-key protection is insufficient regardless of exactly when a CRQC arrives. This is distinct from QS01: it focuses on stored data and signed artefacts whose protection requirements outlast any reasonable migration window, rather than in-flight traffic.

**Common Examples of Vulnerability:**

1. Major data categories where confidentiality lifetime plus migration lead time exceeds published government CRQC planning horizons (2030-2035 for most regulators).
2. Archives and backups that pre-date current crypto policy and may contain quantum-vulnerable encryption under long retention requirements.
3. Signed artefacts whose validity must persist for years: contracts, regulatory filings, evidentiary records, signed software releases, blockchain transactions.
4. Identity and credential records with multi-year lifetimes: government identity issuance, professional credentials, root certificates.
5. Data-at-rest encrypted with AES-256 but wrapped with an RSA or ECC key - the quantum-vulnerable layer sits above the symmetric key.

**How to Prevent:**

1. Establish data classification by confidentiality lifetime, not just sensitivity - a medium-sensitivity record with a 30-year retention requirement may outrank a high-sensitivity record retained for two years.
2. Plan re-encryption programmes for high-priority archives, sequenced against migration capacity.
3. For long-lived signed artefacts, plan re-signing or counter-signing with PQC schemes (ML-DSA, SLH-DSA) before classical signature schemes are deprecated.
4. Where re-encryption is not feasible, reduce retention to the minimum legally and operationally required.

**Example Attack Scenarios:**

Scenario #1: A regulated entity retains personal records for a statutory 30-year period, encrypted with an RSA-wrapped AES key. An adversary harvests the encrypted store today. Applying Mosca's inequality, the confidentiality lifetime far exceeds the CRQC horizon, so the records are effectively already compromised: the attacker recovers the wrapping key once a CRQC exists and decrypts the full archive, well within its required protection window.

Scenario #2: A vendor issues software releases signed with ECDSA, with signatures expected to remain valid for the product's decade-long support lifetime. An attacker records the signed artefacts and, after a CRQC becomes available, forges signatures on malicious updates that still validate against the long-lived, un-rotated trust anchor.

**Reference Links:**

<!-- REVIEW: reference URLs are best-effort canonical pages and require human verification against the source doc. -->

1. [NIST IR 8547 (Draft) - Transition to Post-Quantum Cryptography Standards](https://csrc.nist.gov/pubs/ir/8547/ipd): Transition planning guidance referencing Mosca's inequality.
2. [UK NCSC migration guidance](https://www.ncsc.gov.uk/): Long-lived data prioritisation for PQC migration.
3. [EU Coordinated Implementation Roadmap for PQC (June 2025)](https://digital-strategy.ec.europa.eu/): End-2030 high-risk deadline and standalone-PKC prohibition.
4. [EU Cyber Resilience Act - Regulation (EU) 2024/2847, Annex I](https://eur-lex.europa.eu/eli/reg/2024/2847/oj): State-of-the-art protection required through the product support period.

<!-- TODO: The source doc includes a "Standards and regulatory mapping" section not represented in _template.md. Preserved below — decide whether to extend the template to carry it.

Standards and regulatory mapping: NIST IR 8547 (Draft) on transition planning. NCSC migration guidance on long-lived data prioritisation. EU Coordinated Implementation Roadmap end-2030 high-risk deadline; explicit prohibition on standalone quantum-vulnerable PKC for high-risk cases after 2030. CRA Annex I requires state-of-the-art protection through the support period, which for many products extends past 2030.
-->

