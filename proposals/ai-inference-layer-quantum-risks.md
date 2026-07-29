# QS-NEW: AI Inference Layer Quantum Attack Surface

**Proposed addition to OWASP Quantum Security Top 10**  
**Contributed by:** Richard Barron, Red Specter Security Research Ltd  
**Date:** 29 July 2026  
**Evidence classification:** Demonstrated (operationalised in NIGHTFALL L46 + AI Shield M103)

---

## The Gap

Current PQC frameworks — NIST FIPS 203/204/205, OWASP QS draft v0.1 — address the cryptographic migration problem: replacing RSA/ECDSA/DH with lattice-based, hash-based, and code-based alternatives.

What is missing is the AI-specific quantum attack surface. AI systems introduce cryptographic dependencies that classical PQC frameworks do not address, because classical PQC frameworks were designed before AI agents became the primary execution environment for sensitive operations.

Five distinct AI-layer quantum risks are not covered in any existing framework:

---

## QS-NEW-01: Model Signature Integrity Under Quantum Threat

**Classification:** Demonstrated  
**Attack surface:** AI model supply chain  

**Description:**  
Model weights, checkpoints, and adapters are distributed with cryptographic signatures (Ed25519, ECDSA) that prove provenance and integrity. These signatures are vulnerable to Shor's algorithm on a sufficiently capable quantum computer.

A harvest-now-decrypt-later (HNDL) adversary collecting signed model distributions today can, with future quantum capability:
- Forge valid model signatures
- Distribute backdoored weights indistinguishable from legitimate releases
- Retroactively invalidate provenance chains for models deployed in regulated environments

**Existing framework coverage:** None. NIST PQC migration guidance does not address model distribution signing. OWASP LLM Top 10 does not address post-quantum model supply chain integrity.

**Mitigation:**  
- ML-DSA-65 (FIPS 204) model signing at publish time  
- Dual signing: Ed25519 (current) + ML-DSA-65 (post-quantum) during transition  
- Merkle tree provenance chains over model version history  
- Registry-level PQC verification enforcement

**Red Specter implementation:** AI Shield M103 enforces ML-DSA-65 model signing across AI agent deployments. NIGHTFALL L46 operationalises quantum-era model signature forgery for offensive validation.

---

## QS-NEW-02: Agent Identity Binding Degradation

**Classification:** Demonstrated  
**Attack surface:** AI agent authentication  

**Description:**  
AI agents operating in multi-agent environments authenticate to each other and to orchestration infrastructure using asymmetric key pairs (typically Ed25519 or ECDSA). Agent identity tokens, capability certificates, and delegation chains rely on these signatures.

Under quantum threat:
- Agent identity tokens become forgeable
- Delegation chains — "Agent A is authorised to invoke Agent B on behalf of User C" — become manipulable
- The trust fabric of multi-agent orchestration collapses

This is distinct from classical identity theft: the attacker does not steal credentials, they forge them with quantum capability. There is no authentication log anomaly. The forged agent identity is cryptographically indistinguishable from legitimate.

**Existing framework coverage:** None. FIDO2/WebAuthn PQC transition guidance does not address AI agent-to-agent identity. NIST AI RMF does not address post-quantum agent authentication.

**Mitigation:**  
- ML-DSA-65 agent identity certificates  
- Short-lived agent tokens (TTL ≤ 1 hour) to limit HNDL window  
- CRYSTALS-Kyber key encapsulation for agent session establishment  
- Quantum-resistant delegation chain verification

**Red Specter implementation:** AI Shield M103 covers PQC agent identity enforcement. AI Shield M20 (Agent Identity Verification) implements short-lived token rotation.

---

## QS-NEW-03: Evidence Chain Cryptographic Dependency

**Classification:** Demonstrated  
**Attack surface:** AI forensics and compliance  

**Description:**  
AI agent audit trails, forensic evidence packages, and compliance records depend on cryptographic integrity for legal admissibility. RFC 3161 timestamps use RSA. HMAC-SHA256 evidence chains are not quantum-resistant. Ed25519-signed audit logs become forgeable.

The consequence is specific to regulated industries: an AI system's audit trail — the primary mechanism for proving what an agent did and when — becomes retroactively untrustworthy under quantum capability. Court-admissible AI evidence packages built today may not be admissible in post-quantum litigation environments.

**Existing framework coverage:** None. RFC 3161 has no post-quantum profile. eIDAS 2.0 PQC provisions do not address AI agent audit trails. ISO 42001 does not specify cryptographic requirements for AI audit logs.

**Mitigation:**  
- ML-DSA-65 dual signing on all audit records (alongside current Ed25519)  
- Post-quantum RFC 3161 timestamp profile (IETF draft in progress)  
- Merkle tree audit chains with PQC root signatures  
- Hybrid signature schemes during transition period

**Red Specter implementation:** AI Shield BLACK BOX produces Ed25519 + ML-DSA-65 dual-signed evidence chains with RFC 3161 timestamps and Merkle root verification. Hybrid signing ensures forward compatibility.

---

## QS-NEW-04: Inter-Agent Trust Protocol Exposure

**Classification:** Emerging  
**Attack surface:** Multi-agent orchestration  

**Description:**  
Agent-to-agent communication protocols — A2A, MCP, custom orchestration — establish trust via key exchange (ECDH) and authenticate messages via digital signatures. Under quantum threat:

- ECDH key establishment becomes insecure (Shor's algorithm)  
- Recorded agent communication sessions become retroactively decryptable  
- Man-in-the-middle attacks on agent communication become feasible with quantum capability

Multi-agent AI systems processing sensitive data (healthcare, finance, defence) are particularly exposed: HNDL adversaries recording agent communication today can decrypt it when quantum capability becomes available.

**Existing framework coverage:** Partial. NIST PQC migration guidance covers TLS 1.3 but does not specifically address AI agent communication protocols. MCP specification has no cryptographic requirements. A2A (Google) has no PQC provisions.

**Mitigation:**  
- CRYSTALS-Kyber key encapsulation for agent session establishment  
- ML-DSA-65 message authentication in agent communication  
- Protocol-level PQC requirements in MCP and A2A specifications  
- Forward-secret session keys with quantum-resistant KEM

**Red Specter implementation:** AI Shield M21 (Inter-Agent Communication Security) implements hybrid classical/PQC key establishment. NIGHTFALL L46 operationalises quantum-era inter-agent protocol attacks.

---

## QS-NEW-05: RAG and Vector Database Confidentiality Under Quantum Threat

**Classification:** Emerging  
**Attack surface:** AI retrieval infrastructure  

**Description:**  
RAG systems and vector databases store encrypted knowledge bases, semantic embeddings, and retrieved context. Encryption uses AES-256 (symmetric) and RSA/ECDH for key management (asymmetric).

Symmetric encryption (AES-256) is quantum-resistant to Grover's algorithm with existing key lengths. The vulnerability is in key management: ECDH key exchange for session keys and RSA-encrypted key storage become insecure under quantum capability. An HNDL adversary capturing encrypted RAG retrieval sessions today can obtain session keys — and therefore the retrieved context — when quantum capability becomes available.

For RAG systems used in intelligence, legal, or medical contexts, this represents a long-term confidentiality risk against sensitive knowledge base content.

**Existing framework coverage:** None. No existing framework addresses PQC requirements for AI retrieval infrastructure specifically.

**Mitigation:**  
- CRYSTALS-Kyber for RAG session key establishment  
- Post-quantum key encapsulation for vector database encryption key management  
- Audit trail for all RAG retrievals to enable breach detection  
- Data minimisation: limit what is stored in quantum-exposed retrieval systems

**Red Specter implementation:** AI Shield M42 (RAG Security Monitor) and M43 (Knowledge Base Integrity) cover RAG attack surface. M103 (Quantum AI Security Engine) addresses PQC enforcement across retrieval infrastructure.

---

## Summary Table

| Risk | Attack Surface | Existing Coverage | Quantum Risk Level |
|------|---------------|-------------------|-------------------|
| QS-NEW-01: Model Signature Integrity | AI supply chain | None | HIGH — Shor's on ECDSA |
| QS-NEW-02: Agent Identity Binding | Multi-agent orchestration | None | HIGH — Shor's on Ed25519 |
| QS-NEW-03: Evidence Chain Integrity | AI forensics/compliance | None | HIGH — RFC 3161/HMAC |
| QS-NEW-04: Inter-Agent Trust Protocol | MCP/A2A protocols | Partial | HIGH — HNDL on ECDH |
| QS-NEW-05: RAG Confidentiality | Retrieval infrastructure | None | MEDIUM — key management |

---

## Offensive Validation

These risks are not theoretical. Red Specter Security Research operationalises all five attack surfaces:

- **NIGHTFALL L46** — Post-Quantum AI Cryptography exploitation layer (T148 SPECTER QUANTA)
- **AI Shield M103** — Quantum AI Security Engine (defensive enforcement)

Findings are available for independent validation. All offensive tooling is gate-controlled and requires signed ROE.

---

## References

1. NIST FIPS 203 — ML-KEM (CRYSTALS-Kyber)
2. NIST FIPS 204 — ML-DSA (CRYSTALS-Dilithium / ML-DSA-65)
3. NIST FIPS 205 — SLH-DSA (SPHINCS+)
4. RFC 3161 — Internet X.509 PKI Time-Stamp Protocol
5. OWASP LLM Top 10 2025
6. NIST AI RMF 1.0
7. Red Specter RS-2026-003 — NIGHTFALL Attack Surface Taxonomy (DOI: 10.5281/zenodo.21462689)
8. ISO/IEC 42001:2023 — AI Management Systems

---

*Red Specter Security Research Ltd | Company No. 17106988 | red-specter.co.uk*  
*"While others announce. We ship."*
