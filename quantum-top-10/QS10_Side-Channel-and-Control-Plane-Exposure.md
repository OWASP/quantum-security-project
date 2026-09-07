## QS10:2026 - Side-Channel and Control-Plane Exposure

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
