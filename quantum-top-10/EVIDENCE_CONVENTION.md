# Evidence Convention

## Purpose

The Evidence classification communicates the maturity of the **attack class** described by a Top 10 entry.

It does **not** describe:

- likelihood
- impact
- overall risk
- priority

Those are assessed independently.

---

## Evidence Classifications

### Demonstrated

The attack class has been demonstrated against real systems or realistic prototypes and is supported by publicly available evidence.

Acceptable evidence includes one or more of:

- peer-reviewed experimental research
- public proof-of-concept
- CVE or security advisory
- vendor acknowledgement
- publicly documented real-world incident

### Emerging

The attack class is technically credible and supported by research or early demonstrations, but has not yet been broadly demonstrated in operational environments.

Typical evidence includes:

- laboratory demonstrations
- prototype implementations
- peer-reviewed research
- credible engineering analyses

### Theoretical

The attack class is supported by accepted theory or security analysis but has not yet been experimentally demonstrated.

Evidence typically consists of:

- mathematical analysis
- security proofs
- theoretical models

---

## Classification Guidance

The classification applies to the **attack class**, not to every step required for successful exploitation.

Example:

Harvest Now, Decrypt Later (HNDL)

- Harvesting encrypted data: Demonstrated
- Future decryption using a CRQC: Future dependency

Overall classification:

**Demonstrated**, with the note that successful decryption depends on the availability of a Cryptographically Relevant Quantum Computer (CRQC).

---

## Author Guidance

Every Top 10 entry should include:

**Evidence**

- **Classification:** Demonstrated | Emerging | Theoretical
- **Justification:** One or two sentences explaining the classification.
- **Primary Evidence:** The strongest supporting references should also appear in the Reference section.

The objective is transparency and consistency rather than precise scientific scoring.
