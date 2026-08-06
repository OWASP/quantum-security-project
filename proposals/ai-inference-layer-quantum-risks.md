# QS-NEW: AI Inference Layer Quantum Attack Surface

**Proposed addition to OWASP Quantum Security Top 10**  
**Contributed by:** Richard Barron, Red Specter Security Research Ltd  
**Date:** 29 July 2026 (revised 6 August 2026)  
**Evidence classification:** Emerging (see evidence note below)

---

## The Gap

Current PQC frameworks — NIST FIPS 203/204/205, OWASP QS draft v0.1 — address the cryptographic migration problem: replacing RSA/ECDSA/DH with lattice-based, hash-based, and code-based alternatives.

What is missing is the AI-specific quantum attack surface. AI systems introduce cryptographic dependencies that classical PQC frameworks do not address, because classical PQC frameworks were designed before AI agents became the primary execution environment for sensitive operations.

Five distinct AI-layer quantum risks are not covered in any existing framework.

---

## Evidence Classification Note

QS-NEW-01, 02, and 03 are revised from Demonstrated to Emerging. The offensive tooling is gate-controlled and requires signed ROE, which means external reviewers cannot independently verify the Demonstrated claim against the OWASP evidence standard. A public artifact demonstrating forgery against a test key is in preparation and will be linked when available.

QS-NEW-04 and 05 remain Emerging, which is an accurate fit.

---

## Taxonomy Note: TNFL vs HNDL

Following the taxonomy discussion in #11:

- **QS-NEW-01, 02, 03** are **trust-now-forge-later (TNFL)** risks. An adversary does not need to harvest anything today. When quantum capability arrives, they can forge signatures against any public key they choose — retroactively invalidating provenance built today. Nothing useful is gained by collecting signed artefacts now.
- **QS-NEW-04** is a split: the ECDH session establishment half is **HNDL** (intercept encrypted sessions today, decrypt when quantum capability arrives); the message-signature half is **TNFL**.
- **QS-NEW-05** is genuine **HNDL**: capturing encrypted RAG sessions today yields plaintext when session key exchange (ECDH) breaks.

---

## QS-NEW-01: Model Signature Integrity Under Quantum Threat

**Classification:** Emerging  
**Attack surface:** AI model supply chain  
**Quantum mechanism:** TNFL

**Description:**  
Model weights, checkpoints, and adapters are distributed with cryptographic signatures (Ed25519, ECDSA) that prove provenance and integrity. These signatures are vulnerable to Shor's algorithm on a sufficiently capable quantum computer.

The risk is retroactive forgeability. When quantum capability arrives, every model distribution signed with a classical key becomes forgeable — regardless of when it was signed or distributed. An adversary can:

- Forge valid model signatures for any existing public key
- Distribute backdoored weights indistinguishable from legitimate releases
- Retroactively invalidate provenance chains for models deployed in regulated environments

Provenance built today cannot be relied on tomorrow.

**Existing framework coverage:** None. NIST PQC migration guidance does not address model distribution signing. OWASP LLM Top 10 does not address post-quantum model supply chain integrity.

**Mitigation:**

- ML-DSA-65 (FIPS 204) model signing at publish time
- Dual signing: Ed25519 (current) + ML-DSA-65 (post-quantum) during transition
- Merkle tree provenance chains over model version history
- Registry-level PQC verification enforcement

*Note on algorithm selection:* ML-DSA-65 is used throughout this proposal as the default. Deployments within scope of CNSA 2.0 (US national security systems) are required to use ML-DSA-87, which is not permitted to be downgraded to ML-DSA-65.

---

## QS-NEW-02: Agent Identity Binding Degradation

**Classification:** Emerging  
**Attack surface:** AI agent authentication  
**Quantum mechanism:** TNFL

**Description:**  
AI agents operating in multi-agent environments authenticate to each other and to orchestration infrastructure using asymmetric key pairs (typically Ed25519 or ECDSA). Agent identity tokens, capability certificates, and delegation chains rely on these signatures.

Under quantum threat, the attacker does not steal credentials — they forge them. The critical consequence is the detection gap:

> There is no authentication log anomaly. The forged agent identity is cryptographically indistinguishable from legitimate.

This is categorically worse than classical credential theft. Classical theft leaves traces: unusual access patterns, token reuse, session anomalies. Quantum forgery does not. The usual detection surface — authentication logs, anomaly detection, behavioural monitoring — goes silent at the same time the cryptographic protection fails.

Delegation chains — "Agent A is authorised to invoke Agent B on behalf of User C" — become manipulable in the same way.

**Existing framework coverage:** None. FIDO2/WebAuthn PQC transition guidance does not address AI agent-to-agent identity. NIST AI RMF does not address post-quantum agent authentication.

**Mitigation:**

- ML-DSA-65 agent identity certificates (ML-DSA-87 for CNSA 2.0 scope)
- Short-lived agent tokens (TTL ≤ 1 hour) to minimise the window of forged token utility
- ML-KEM (FIPS 203) key encapsulation for agent session establishment
- Quantum-resistant delegation chain verification

---

## QS-NEW-03: Evidence Chain Cryptographic Dependency

**Classification:** Emerging  
**Attack surface:** AI forensics and compliance  
**Quantum mechanism:** TNFL

**Description:**  
AI agent audit trails, forensic evidence packages, and compliance records depend on cryptographic integrity for legal admissibility. The quantum-fragile components are:

- **RFC 3161 timestamps:** The timestamp protocol uses RSA signatures, which Shor's algorithm breaks. No post-quantum RFC 3161 profile exists (IETF draft in progress).
- **Ed25519-signed audit logs:** Become retroactively forgeable under quantum capability.

*Note:* HMAC-SHA256 evidence chains are Grover-resistant with full-length keys. The HMAC is not the quantum risk in this entry. The risk is the asymmetric signature layer above it.

The consequence is specific to regulated industries: an AI system's audit trail — the primary mechanism for proving what an agent did and when — becomes retroactively untrustworthy. Court-admissible AI evidence packages built today may not be admissible in post-quantum litigation environments, because the timestamps and log signatures cannot be verified as unforgeable.

**Existing framework coverage:** None. RFC 3161 has no post-quantum profile. eIDAS 2.0 PQC provisions do not address AI agent audit trails. ISO 42001 does not specify cryptographic requirements for AI audit logs.

**Mitigation:**

- ML-DSA-65 dual signing on all audit records alongside current Ed25519
- Post-quantum RFC 3161 timestamp profile when available (track IETF progress)
- Merkle tree audit chains with PQC root signatures
- Hybrid signature schemes during transition period

---

## QS-NEW-04: Inter-Agent Trust Protocol Exposure

**Classification:** Emerging  
**Attack surface:** Multi-agent orchestration  
**Quantum mechanism:** HNDL (session encryption) + TNFL (message authentication)

**Description:**  
Agent-to-agent communication protocols — A2A, MCP, custom orchestration — establish trust via key exchange (ECDH) and authenticate messages via digital signatures.

These are two distinct quantum risks with different mechanisms:

**HNDL component (ECDH session establishment):** An adversary recording encrypted agent communication today can decrypt it when quantum capability becomes available. Multi-agent AI systems processing sensitive data in healthcare, finance, or defence are the highest-risk deployments — the confidentiality of recorded sessions is at risk.

**TNFL component (message signatures):** Message authentication signatures become forgeable retroactively. An adversary can forge agent messages against any recorded public key.

**Existing framework coverage:** Partial. NIST PQC migration guidance covers TLS 1.3 but does not specifically address AI agent communication protocols. MCP specification has no cryptographic requirements. A2A has no PQC provisions.

**Mitigation:**

- ML-KEM (FIPS 203) key encapsulation for agent session establishment
- ML-DSA-65 message authentication in agent communication (ML-DSA-87 for CNSA 2.0 scope)
- Protocol-level PQC requirements in MCP and A2A specifications
- Forward-secret session keys with quantum-resistant KEM

---

## QS-NEW-05: RAG and Vector Database Confidentiality Under Quantum Threat

**Classification:** Emerging  
**Attack surface:** AI retrieval infrastructure  
**Quantum mechanism:** HNDL

**Description:**  
RAG systems and vector databases store encrypted knowledge bases, semantic embeddings, and retrieved context.

AES-256 (symmetric) is Grover-resistant with existing key lengths — this is not the vulnerability. The risk is in key management: ECDH key exchange for session keys and RSA-encrypted key storage become insecure under quantum capability.

An HNDL adversary capturing encrypted RAG retrieval sessions today can obtain session keys — and therefore the retrieved context — when quantum capability breaks ECDH. The AES-256 encryption of the knowledge base itself is not the target; the asymmetric key exchange protecting the session key is.

For RAG systems used in intelligence, legal, or medical contexts, this represents a long-term confidentiality risk against sensitive knowledge base content.

**Existing framework coverage:** None. No existing framework addresses PQC requirements for AI retrieval infrastructure specifically.

**Mitigation:**

- ML-KEM (FIPS 203) for RAG session key establishment
- Post-quantum key encapsulation for vector database encryption key management
- Audit trail for all RAG retrievals to enable breach detection
- Data minimisation: limit what is stored in quantum-exposed retrieval systems

---

## Summary Table

| Risk | Mechanism | Attack Surface | Existing Coverage | Quantum Risk Level |
|------|-----------|---------------|-------------------|--------------------|
| QS-NEW-01: Model Signature Integrity | TNFL | AI supply chain | None | HIGH |
| QS-NEW-02: Agent Identity Binding | TNFL | Multi-agent orchestration | None | HIGH |
| QS-NEW-03: Evidence Chain Integrity | TNFL | AI forensics/compliance | None | HIGH |
| QS-NEW-04: Inter-Agent Trust Protocol | HNDL + TNFL | MCP/A2A protocols | Partial | HIGH |
| QS-NEW-05: RAG Confidentiality | HNDL | Retrieval infrastructure | None | MEDIUM |

---

## Placement Note

Five entries is a significant ask. QS-NEW-02 and QS-NEW-03 are the strongest and most distinct. If the project leads prefer consolidation, this proposal is absorption-ready:

- QS-NEW-01 and QS-NEW-02 could consolidate into a single AI agent layer cryptographic dependency entry
- QS-NEW-05 could absorb into the HNDL entry proposed in #11
- QS-NEW-04 could sit adjacent to existing inter-system trust entries

Happy to revise in whatever direction is most useful for the final list.

---

## References

1. NIST FIPS 203 — ML-KEM (CRYSTALS-Kyber)
2. NIST FIPS 204 — ML-DSA (CRYSTALS-Dilithium)
3. NIST FIPS 205 — SLH-DSA (SPHINCS+)
4. RFC 3161 — Internet X.509 PKI Time-Stamp Protocol
5. OWASP LLM Top 10 2025
6. NIST AI RMF 1.0
7. Red Specter RS-2026-003 — NIGHTFALL Attack Surface Taxonomy (DOI: 10.5281/zenodo.21462689)
8. ISO/IEC 42001:2023 — AI Management Systems

*These risks are operationalised in commercial tooling (details available on request). QS-NEW-02 and QS-NEW-03 public validation artefacts are in preparation.*

---

*Red Specter Security Research Ltd | Company No. 17106988 | red-specter.co.uk*
