## QS09:2026 - Quantum Platform Isolation and Control Plane Exposure

**NOTE TO ENTRY LEAD**
This entry's placeholder combines TWO original entries:

* QPU Tenant Isolation Failures
* Side-Channel and Control-Plane Exposure



## QPU Tenant Isolation Failures

**Description:**

Cloud-based quantum platforms increasingly host workloads from multiple tenants on shared QPUs, allocating qubits from a shared processor to different tenants concurrently or in rapid succession, and claiming isolation between them. Recent peer-reviewed research shows that isolation is weaker than it appears. Two classes of attack are documented: crosstalk attacks, where quantum crosstalk lets a malicious circuit on one tenant's allocation degrade the fidelity of a victim's computation on the same processor (even mere QPU-topology adjacency suffices); and state-leakage attacks, where standard reset gates fail to fully clear qubit state between consecutively executed circuits, leaking information across the boundary the platform claims to enforce. Organisations running sensitive computations - proprietary algorithms, trade-secret optimisation problems, classified workloads - must treat shared-QPU isolation as a security claim requiring evidence, not a default property.

**Common Examples of Vulnerability:**

1. Sensitive workloads run on shared (multi-tenant) QPUs rather than dedicated allocations.
2. Platforms whose documented isolation guarantees are unsupported by evidence such as post-execution qubit-reset verification or neighbour-isolation policies.
3. Circuits whose inputs (problem instance, parameters, hardcoded constants) or outputs constitute confidential or proprietary information, executed adjacent to untrusted tenants.
4. Reliance on standard reset gates that leave residual state observable by the next tenant on the same physical qubits.

**How to Prevent:**

1. For sensitive workloads, use dedicated QPU allocations or reservation modes where available, even at higher cost.
2. Where shared QPUs are unavoidable, design circuits to minimise the information value of crosstalk or state-leakage observation: split sensitive computations across runs, randomise parameter mappings, avoid embedding hardcoded sensitive constants directly in circuits.
3. Require documented isolation evidence from providers: post-execution qubit reset verification, neighbour-isolation policies, scheduling constraints.
4. Treat shared-QPU output as observable by other tenants until proven otherwise; do not pass it through trust boundaries that depend on confidentiality.
5. Track ongoing QPU-isolation research (NDSS, CCS, Usenix Security, ISLPED).

**Example Attack Scenarios:**

Scenario #1: A financial firm runs a proprietary optimisation circuit on a shared QPU. A malicious co-tenant schedules a circuit adjacent on the QPU topology and uses quantum crosstalk to degrade the victim's fidelity (Choudhury et al., NDSS 2025; Ash-Saki et al., ISLPED 2020), corrupting results the firm relies on - without ever needing co-execution.

Scenario #2: A tenant's circuit is scheduled on physical qubits immediately after a competitor's workload. Because standard reset gates do not fully clear state (Xu et al., CCS 2023), the tenant observes residual state leaking information about the previous, confidential computation.

**Reference Links:**

<!-- References verified 2026-07-27 against authoritative primary sources. Reference #1 is attributed to "Choudhury et al.": the paper's first author is Navnil Choudhury, confirmed against the arXiv metadata API for 2412.10507 and the NDSS proceedings PDF (13C-f2185-choudhury.pdf). Recorded here so the attribution is not changed again. -->

1. [Choudhury et al. - Crosstalk-induced Side Channel Threats in Multi-Tenant NISQ Computers (NDSS 2025)](https://www.ndss-symposium.org/ndss-paper/crosstalk-induced-side-channel-threats-in-multi-tenant-nisq-computers/): Demonstrated crosstalk-based side-channel/fidelity attack on shared QPUs ([arXiv:2412.10507](https://arxiv.org/abs/2412.10507)).
2. [Ash-Saki et al. - Analysis of Crosstalk in NISQ Devices and Security Implications in Multi-Programming Regime (ISLPED 2020)](https://doi.org/10.1145/3370748.3406570): Crosstalk-based fault injection.
3. [Xu et al. - Securing NISQ Quantum Computer Reset Operations Against Higher Energy State Attacks (CCS 2023)](https://doi.org/10.1145/3576915.3623104): Documents reset-operation state leakage across the tenant boundary.
4. [EU DORA - Regulation (EU) 2022/2554, Article 28](https://eur-lex.europa.eu/eli/reg/2022/2554/oj/eng): Third-party ICT risk expectation that platform claims are evidenced.

**Standards and Regulatory Mapping:**

> **TODO:** This section is carried over from the source document and is not part of `_template.md`. Confirm whether to keep it in the final entry format, and verify each standard/citation.

No formal standard yet covers QPU tenant isolation. NIST and NCSC have not published guidance on quantum platform security. The relevant published research includes Choudhury et al. on crosstalk-induced side-channel threats (NDSS 2025), Ash-Saki et al. on crosstalk-based fault injection (ISLPED 2020), and Xu et al. on reset-operation state leakage (CCS 2023). Where DORA Article 28 third-party risk applies to financial entities using quantum platform services, the supervisory expectation is that platform security claims are evidenced rather than assumed.



## Side-Channel and Control-Plane Exposure

**Description:**

Quantum computers depend on extensive classical control infrastructure - including signal generators, arbitrary waveform generators, mixers, FPGAs, controller electronics, and control software - to translate circuits into the pulses that operate a QPU. Although unknown quantum states cannot be copied, these classical control signals can leak information about the workload. Xu et al. (CCS 2023) demonstrated timing, energy, and power-trace attacks that identify circuits and circuit properties; under their strongest per-channel trace model, an attacker can reconstruct the sequence of non-virtual gates. The demonstrated threat model assumes physical access to the controller, such as access by a malicious data-centre insider. The authors state that controller power data is not currently exposed by cloud providers and describe a remote extension as future risk. This is therefore evidence of a controller-physical-access and insider threat, not evidence that an ordinary remote tenant can perform the attack today. Exposure can reveal proprietary algorithms and, when inputs or parameters are encoded in circuit elements such as oracles or ansatzes, sensitive data.

**Common Examples of Vulnerability:**

1. Controller electronics or their power, electromagnetic, or timing signals are accessible to unauthorised personnel.
2. Controller telemetry and diagnostic traces are collected or retained without access controls equivalent to the sensitivity of the workloads they describe.
3. Job-management, calibration, and controller-administration roles are not separated or audited.
4. Providers do not document the physical-access assumptions, telemetry exposure, or side-channel resistance of their control infrastructure.
5. Proprietary circuit structure, or sensitive inputs and parameters encoded in circuit elements, is submitted without accounting for controller-side information leakage.

**How to Prevent:**

1. Restrict, monitor, and audit physical access to controller electronics; use tamper-evident controls appropriate to the sensitivity of hosted workloads.
2. Minimise collection of controller power and timing telemetry, isolate monitoring interfaces, and protect any retained traces as sensitive workload-derived data.
3. Enforce least privilege and role separation across job management, calibration, controller administration, and telemetry access.
4. Require providers to state their side-channel threat model and disclose which physical and logical controls protect the controller plane.
5. Evaluate workload-level mitigations described by Xu et al. where appropriate, including duration or energy equalisation and logically equivalent circuit transformations. Test the security, fidelity, and performance trade-offs: the paper notes that a defence against one metric may not resist combined side channels.
6. Avoid encoding sensitive values in circuit structure when the use case permits, and assess the impact if circuit identity, gates, qubit mapping, or parameters are exposed.

**Example Attack Scenarios:**

Scenario #1: A malicious data-centre insider instruments the per-channel power consumption of QPU controller electronics. With knowledge of the controller's basis pulses, the attacker reconstructs the victim circuit's non-virtual gate sequence and infers proprietary algorithm structure (Xu et al., CCS 2023).

Scenario #2: An unauthorised controller operator captures timing or aggregate energy measurements for a victim's repeated circuit executions. Comparing the measurements with known candidate circuits lets the attacker identify which workload ran and infer circuit properties. This scenario requires controller-level or physical measurement access under the demonstrated threat model; remote tenant access is not assumed.

**Reference Links:**

<!-- References re-verified against the papers on 2026-07-29. Mi et al. (CCS 2022) was removed because it demonstrates residual qubit-state leakage across reset operations, not reset-timing or classical-controller leakage; it is relevant to QS08 instead. -->

1. [Xu et al. - Exploration of Power Side-Channel Vulnerabilities in Quantum Computer Controllers (CCS 2023)](https://doi.org/10.1145/3576915.3623118): Demonstrates timing, energy, and power-trace attacks against controller signals, states the physical-access threat model, and evaluates workload-level defences.
2. [NIST FIPS 140-3 - Security Requirements for Cryptographic Modules](https://csrc.nist.gov/pubs/fips/140-3/final): Classical physical-security framework offering partial conceptual coverage.
3. [Common Criteria (ISO/IEC 15408)](https://www.commoncriteriaportal.org/): General security-evaluation framework; quantum-controller-specific protection profiles are not established here.

**Standards and Regulatory Mapping:**

> **TODO:** This section is carried over from the source document and is not part of `_template.md`. Confirm whether to keep it in the final entry format, and verify each standard/citation.

No formal standard cited here specifically covers quantum-platform controller side channels. The relevant published research is Xu et al. on timing and power side-channel attacks against quantum-computer controllers (CCS 2023). General classical frameworks (FIPS 140-3 physical-security requirements and Common Criteria) provide partial conceptual coverage, but citing them does not establish quantum-controller-specific compliance or resistance.
