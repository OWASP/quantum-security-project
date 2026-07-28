## QSxx:2026 - Unverifiable Computation and Job Provenance

> **Proposal status:** New entry submitted during Pre-Sprint 0. Number deliberately left as QSxx - placement and numbering are for Sprint 1 voting to decide. Surface: Platform (quantum platforms and hybrid quantum-classical systems). Maturity: Demonstrated - tampering by untrusted quantum hardware providers and a runtime detection heuristic are shown in peer-reviewed work.

**Description:**

A quantum job submitted to a cloud QPU returns a distribution of measurement outcomes with no accompanying evidence that the job ran as specified, on the hardware advertised, or without interference. Quantum results are probabilistic by nature, so a tampered, degraded, or misrouted execution returns a plausible-looking distribution that the tenant cannot distinguish from ordinary NISQ noise. The problem is structural rather than incidental: the workloads with the strongest commercial case for quantum - optimisation, simulation, materials, finance - are precisely those where the correct answer is not known in advance and therefore cannot be checked. Peer-reviewed work demonstrates that less-trusted vendors can tamper with executions to return suboptimal results, and that detecting this requires the tenant to deliberately split shots across providers rather than trust any single one (Upadhyay and Ghosh, Frontiers in Computer Science, 2024). As QPU access is increasingly resold and brokered, the tenant is often several steps removed from the hardware and has no means of establishing what actually executed.

This entry is distinct from the existing platform entries. QS08 concerns confidentiality and fidelity harm caused by co-tenants; QS09 concerns the compromise of a toolchain component; QS10 concerns confidentiality leakage through the control plane. This entry concerns integrity and assurance in the absence of any compromise: even with a trusted toolchain, a dedicated allocation, and no observable side channel, the tenant still has no means to verify what was executed, where, or under what calibration state.

**Common Examples of Vulnerability:**

1. Business, engineering, or safety decisions taken on quantum results that were never validated against a classical reference, a known-answer test, or a second independent provider.
2. Providers that supply no attestation of which physical device, calibration state, or queue path executed a submitted job.
3. Brokered or resold QPU access, where the tenant contracts with an intermediary and cannot establish which underlying hardware ran the workload.
4. Workloads selected precisely because they are not classically computable, leaving no independent means of checking the returned result.
5. Absence of any provider-side job-integrity log that the tenant can obtain and retain, which regulated entities need in order to evidence a computation after the fact.
6. Service claims accepted without verification - device grade, shot count, queue priority - where a cheaper or degraded device may be substituted without detection.
7. Results retained without recording the provider, device, calibration snapshot, and toolchain version, so that a later vulnerability disclosure cannot be scoped to the affected jobs.

**How to Prevent:**

1. Where the workload permits, embed known-answer or trap circuits alongside the real job and verify their outcomes before trusting the result.
2. For high-value workloads, split shots across two or more independent providers and compare the returned distributions; divergence beyond expected noise is a tampering or degradation signal (Upadhyay and Ghosh, 2024).
3. Validate against classical simulation or a reduced-size instance of the same problem wherever it admits one, and treat results that cannot be validated by any means as advisory rather than authoritative.
4. Require providers to publish and contractually commit to job provenance: device identity, calibration state at execution time, and an auditable job log available to the tenant.
5. Record the provider, device, calibration snapshot, and toolchain version alongside every retained result.
6. Include result-integrity and attestation requirements in procurement and third-party risk assessment for quantum services, as would be expected of any other outsourced computation.
7. Where quantum output feeds a regulated process, define in advance what evidence of correct execution will be required, and do not adopt providers that cannot supply it.

**Example Attack Scenarios:**

Scenario #1: An organisation buys QPU time from a low-cost vendor. The vendor silently routes jobs to an older, lower-fidelity device than the one advertised, or truncates the requested shot count. The returned distribution is noisier but entirely plausible. Running an optimisation problem with no known correct answer, the tenant accepts a suboptimal solution as genuine and acts on it. Upadhyay and Ghosh demonstrate both this class of tampering and a shot-splitting heuristic that detects it at runtime.

Scenario #2: A tenant runs a variational workload through a broker offering pooled access across several hardware vendors. One vendor in the pool biases the distributions it returns. Because the broker provides no per-job device attestation, the tenant cannot determine which jobs were affected, cannot scope the incident, and cannot demonstrate which results remain sound.

Scenario #3: A regulated entity uses quantum-derived output as an input to a risk model. During supervisory review it is asked to evidence that the computation was performed as documented. No provenance record exists, no attestation was offered by the provider, and the result cannot be reproduced because the calibration state at execution time was never captured.

**Reference Links:**

1. [Upadhyay and Ghosh - Trustworthy and reliable computing using untrusted and unreliable quantum hardware (Frontiers in Computer Science, 2024)](https://doi.org/10.3389/fcomp.2024.1431788): Demonstrates tampering by less-trusted quantum hardware vendors and proposes a run-adaptive shot-splitting heuristic that identifies untrustworthy hardware at runtime ([arXiv:2305.01826](https://arxiv.org/abs/2305.01826)).
2. [Upadhyay and Ghosh - Robust and Secure Hybrid Quantum-Classical Computation on Untrusted Cloud-Based Quantum Hardware (HASP 2022)](https://doi.org/10.1145/3569562.3569569): Models and simulates adversarial tampering of input parameters and measurement outcomes on QAOA workloads running on untrusted cloud hardware ([arXiv:2209.11872](https://arxiv.org/abs/2209.11872)).
3. [Xu, Erata and Szefer - Quantum Computer Fault Injection Attacks (IEEE QCE 2024)](https://ieeexplore.ieee.org/document/10821412/): Classifies fault injection against quantum computers, including insider attacks in data centres that compromise the integrity of computations and resulting data ([arXiv:2309.05478](https://arxiv.org/abs/2309.05478)).
4. [EU DORA - Regulation (EU) 2022/2554, Articles 28-30](https://eur-lex.europa.eu/eli/reg/2022/2554/oj/eng): Third-party ICT risk obligations, including contractual requirements for audit and evidence of service performance.

**Standards and Regulatory Mapping:**

> Included for consistency with the existing v0.1 entries; carries the same open question about whether this section is retained in the final entry format.

No formal standard covers quantum computation attestation or job provenance. Classical assurance concepts have direct analogues that have not yet been adapted: confidential-computing attestation models, SLSA provenance for build artefacts, and audit-evidence expectations for outsourced processing. Where regulated entities consume quantum platform services, DORA Articles 28-30 third-party ICT risk obligations extend to the auditability of provider performance, and the inability of a provider to evidence what executed is a contractual gap under those obligations rather than a purely technical one.
