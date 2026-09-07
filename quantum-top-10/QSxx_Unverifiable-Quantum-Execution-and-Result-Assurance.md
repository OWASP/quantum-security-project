## QSxx:2026 - Unverifiable Quantum Execution and Result Assurance

> **Proposal status:** New entry submitted during Pre-Sprint 0. Number deliberately left as QSxx - placement and numbering are for Sprint 1 voting to decide. Surface: Platform (quantum platforms and hybrid quantum-classical systems). Maturity: Demonstrated under the project's proof-of-concept convention - adversarial tampering by less-trusted providers is modelled and experimentally evaluated in peer-reviewed work, and a runtime detection heuristic is proposed. No public vendor incident is claimed.

**Description:**

A quantum job submitted to a cloud QPU returns a distribution of measurement outcomes. The tenant generally receives no evidence, independent of the provider, about what physically executed or under what conditions. Quantum results are probabilistic, so a tampered, degraded, or misrouted execution can return a plausible-looking distribution that is difficult to distinguish from ordinary NISQ noise. Peer-reviewed work models and experimentally evaluates adversarial tampering by less-trusted providers, and proposes splitting shots across providers as a runtime means of identifying untrustworthy hardware (Upadhyay and Ghosh, Frontiers in Computer Science 2024; HASP 2022). As QPU access is increasingly resold and brokered, the tenant may sit several administrative boundaries away from the operator, and a provider-generated record remains a provider assertion rather than independent evidence of execution.

The difficulty is sharpest for the workloads with the strongest commercial case for quantum - optimisation, simulation, materials, finance. Some of these admit a cheap classical check of a returned solution; others may not admit economically practical independent validation at the relevant scale. Where no practical check exists, the tenant may lack evidence sufficient to assess what executed or whether the returned result was manipulated.

**Scope.** None of the current platform entries centres assurance of the underlying execution and returned result when the tenant-side toolchain and co-tenant isolation boundaries are otherwise intact. QS08 addresses harm arising from co-tenants, including fidelity degradation; QS09 addresses compromise of toolchain components; QS10 addresses confidentiality leakage through the control plane. Controls for integrity-verifiable linkage across the submission-to-dispatch-to-result lineage establish traceability within an applicable trust boundary; this entry addresses what evidence permits independent appraisal of the underlying execution and the returned result.

**Common Examples of Vulnerability:**

1. Business, engineering, or safety decisions taken on quantum results that were never validated against a classical reference, a known-answer test, or a second independent provider.
2. Providers that supply no attestation of which physical device, calibration state, or queue path executed a submitted job.
3. Brokered or resold QPU access, where the tenant contracts with an intermediary and has no direct relationship with the operator of the hardware.
4. Workloads for which independent validation is not economically practical at the relevant scale, leaving the returned result without a feasible independent check.
5. Absence of any provider-side job-integrity log that the tenant can obtain and retain, which regulated entities may need in order to evidence a computation after the fact.
6. Service claims accepted without verification - device grade, shot count, queue priority - where a cheaper or degraded device may be substituted without the tenant being able to detect it.
7. Results retained without recording the provider, device, calibration snapshot, and toolchain version, so that a later vulnerability disclosure cannot be scoped to the affected jobs.
8. Provider-generated execution records treated as independent proof of physical execution rather than as provider assertions appraised within a stated trust boundary.

**How to Prevent:**

1. Where the workload permits, embed known-answer or trap circuits alongside the real job and verify their outcomes before relying on the result.
2. For high-value workloads, split shots across two or more independent providers and compare the returned distributions; divergence beyond expected noise is a tampering or degradation signal (Upadhyay and Ghosh, 2024).
3. Validate against classical simulation, a reduced-size instance, or a cheap solution-quality check wherever the problem admits one, and treat results with no practical independent check as advisory rather than authoritative.
4. Require providers to publish and contractually commit to execution evidence: device identity, calibration state at execution time, and an auditable job log available to the tenant.
5. Record the provider, device, calibration snapshot, and toolchain version alongside every retained result.
6. Identify the producer of any execution record and the trust boundary within which its claims are appraised. Treat a provider-generated record as a provider assertion, not as independent proof of physical execution or result fidelity.
7. Include result-assurance and attestation requirements in procurement and third-party risk assessment for quantum services, as would be expected of any other outsourced computation.
8. Where quantum output feeds a regulated process, define in advance what evidence of correct execution will be required, and do not adopt providers that cannot supply it.

**Example Attack Scenarios:**

Scenario #1: A tenant runs an optimisation workload on a less-trusted provider. The provider tampers with the execution so that returned results are systematically suboptimal while remaining statistically plausible. Upadhyay and Ghosh model and experimentally evaluate this class of adversarial tampering, and propose a run-adaptive shot-splitting heuristic that identifies untrustworthy hardware at runtime. A tenant relying on a single provider has no comparison point and accepts the degraded output as genuine.

Scenario #2: A tenant runs a variational workload through a broker offering pooled access across several hardware vendors. One vendor in the pool biases the distributions it returns. Because the broker provides no per-job device attestation, the tenant cannot determine which jobs were affected, cannot scope the incident, and cannot demonstrate which results remain sound. This scenario is illustrative of the brokered-access condition; no public incident of this kind is claimed.

Scenario #3: A regulated entity uses quantum-derived output as an input to a risk model. During supervisory review it is asked to evidence that the computation was performed as documented. No execution record exists beyond the provider's own completion status, and the result cannot be reproduced because the calibration state at execution time was never captured.

**Reference Links:**

1. [Upadhyay and Ghosh - Trustworthy and reliable computing using untrusted and unreliable quantum hardware (Frontiers in Computer Science, 2024)](https://doi.org/10.3389/fcomp.2024.1431788): Models and experimentally evaluates adversarial tampering by less-trusted quantum hardware vendors, and proposes a run-adaptive shot-splitting heuristic that identifies untrustworthy hardware at runtime ([arXiv:2305.01826](https://arxiv.org/abs/2305.01826)).
2. [Upadhyay and Ghosh - Robust and Secure Hybrid Quantum-Classical Computation on Untrusted Cloud-Based Quantum Hardware (HASP 2022)](https://doi.org/10.1145/3569562.3569569): Models and simulates adversarial tampering of input parameters and measurement outcomes on QAOA workloads running on untrusted cloud hardware ([arXiv:2209.11872](https://arxiv.org/abs/2209.11872)).
3. [Xu, Erata and Szefer - Quantum Computer Fault Injection Attacks (IEEE QCE 2024)](https://ieeexplore.ieee.org/document/10821412/): Classifies fault injection against quantum computers, including insider attacks in data centres that could compromise the integrity of computations and resulting data ([arXiv:2309.05478](https://arxiv.org/abs/2309.05478)).
4. [IETF RFC 9334 - Remote ATtestation procedureS (RATS) Architecture](https://www.rfc-editor.org/rfc/rfc9334.html): Defines Attester, Verifier, and Relying Party roles, Evidence and Attestation Results, and the trust relationships under which evidentiary claims are appraised. A generic model for distinguishing an assertion from independently appraised evidence; not quantum-specific.
5. [EU DORA - Regulation (EU) 2022/2554, Articles 28-30](https://eur-lex.europa.eu/eli/reg/2022/2554/oj/eng): Establishes risk-based contracting, subcontracting oversight, performance monitoring, and audit rights for ICT third-party services. Does not prescribe per-job QPU execution attestation.

**Standards and Regulatory Mapping:**

> Included for consistency with the existing v0.1 entries; carries the same open question about whether this section is retained in the final entry format.

No cited source establishes a quantum-specific standard for attestation of physical QPU execution or for independent appraisal of a returned result. RFC 9334 provides a generic architecture separating an assertion from independently appraised evidence, and confidential-computing attestation models and SLSA provenance offer related classical analogues, but none is quantum-specific. Where regulated entities consume quantum platform services, DORA Articles 28-30 support risk-based contracting, subcontracting oversight, performance monitoring, and audit rights; they do not prescribe per-job execution attestation. Missing execution evidence is therefore better described as something that may create a contractual or oversight gap in the applicable circumstances than as a gap directly mandated by DORA.
