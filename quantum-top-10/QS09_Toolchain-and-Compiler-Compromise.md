## QS09:2026 - Toolchain and Compiler Compromise

**Description:**

A submitted quantum job is not just a circuit - it is hardcoded constants and computational data embedded in a program that flows through a compiler, transpiler, and pulse-level scheduler before reaching hardware, and each layer is a potential point of compromise. Quantum software stacks compose high-level frameworks (Qiskit, Cirq, PennyLane), transpilers, optimising compilers, pulse-level schedulers, and hardware configuration files. Two classes of attack are documented: circuit theft via compromised compilers (Suresh et al., HASP 2021), where - because circuits encode their data hardcoded as parameters - theft of the circuit is theft of both the algorithm and its inputs; and QTrojan (Chu et al., 2023), where adversaries stealthily disable data encoding inside a circuit by manipulating hardware configuration files disguised as routine pulse calibrations, so the circuit appears to execute normally but produces incorrect results invisibly. This is a supply-chain risk specific to quantum software stacks: circuit integrity, IP confidentiality, and pipeline trustworthiness must be evaluated, not assumed.

Toolchain integrity also requires verifiable continuity across transformation and dispatch. A stable user-facing job identifier alone does not establish that the submitted circuit and parameters remain integrity-linked to the security-relevant transformed artifact dispatched for execution, the platform-reported backend and execution context, and the returned result record.

**Common Examples of Vulnerability:**

1. Quantum toolchains composed of multiple open-source and vendor components with limited integrity verification between stages.
2. Circuits that encode sensitive data as hardcoded parameters (most, given current platform constraints), exposed to theft anywhere along the pipeline.
3. Calibration and pulse-configuration update pipelines with weak authentication or no change auditing.
4. Production toolchains that auto-update compilers and transpilers without integrity verification.
5. Pulse and hardware configuration files treated as routine rather than a sensitive operations surface.
6. Quantum job pipelines that preserve a stable user-facing job identifier without integrity-protected linkage among the submitted circuit and parameters, the security-relevant transformed artifacts and toolchain configurations that produced them, the exact artifact and platform context used at dispatch, and the returned result record.

**How to Prevent:**

1. Apply software supply-chain hygiene to quantum toolchains: signed releases, verified dependencies, reproducible builds where possible.
2. Where IP is embedded in circuits, evaluate whether the provider's audit and integrity controls suffice for the threat model.
3. Pin specific versions of compilers and transpilers; do not auto-update production toolchains without integrity verification.
4. Treat hardware configuration and pulse calibration as a sensitive surface: restrict modification access and audit changes.
5. Engage platform providers on transparency around toolchain integrity controls.
6. Maintain integrity-protected provenance for each security-relevant quantum-job transformation. Bind the submitted circuit and parameters to the resulting transformed artifacts; the identities, versions, configurations, and resolved dependencies of the toolchain components that produced them; and the target backend, topology, and applicable calibration-state or execution-context identifiers. Use content digests where appropriate for the artifact type and threat model, and use authenticated references or verifiable commitments where raw artifacts cannot be disclosed, so that substitution, omission, or mismatch can be detected.
7. At the dispatch boundary, verify that the exact artifact being dispatched is integrity-linked to the submitted job and its recorded transformation lineage. Also verify that the selected backend, topology, calibration state, and other security-relevant execution context satisfy the job's declared or policy-defined constraints. Block dispatch or require re-evaluation when any required binding or evidence item is absent, invalid, mismatched, stale, unauthorized, or outside the permitted context.
8. Return an authenticated record that binds the returned result artifact, or an integrity-protected identifier for it, to the submitted job, the security-relevant transformation lineage, the exact artifact identified at dispatch, and the platform-reported backend and execution context. Identify the record producer and the trust boundary within which its claims are evaluated. Treat a provider-generated record as a provider assertion; do not represent it as independent proof of physical QPU execution, computational correctness, or result fidelity.

**Example Attack Scenarios:**

Scenario #1: A compromised transpiler in the pipeline exfiltrates submitted circuits (Suresh et al., HASP 2021). Because the circuit carries its proprietary optimisation problem hardcoded as parameters, the attacker steals both the algorithm and its confidential inputs in a single theft.

Scenario #2: An adversary alters a hardware configuration file to disable a circuit's data encoding, disguising the change as a routine pulse calibration (QTrojan, Chu et al., 2023). The victim's circuit appears to run normally but silently returns incorrect results, and the manipulation is invisible to the submitting user.

Scenario #3: After a tenant submits a quantum job, an attacker with write access to a transformation cache or artifact store substitutes a different transpiled artifact while preserving the original user-facing job identifier. The platform later reports the job as completed, but the returned result record is associated only with that identifier and not with the submitted circuit and parameters, the security-relevant transformation lineage, or the artifact and platform context identified at dispatch. The tenant cannot determine from the returned record whether the intended or substituted artifact was dispatched.

**Reference Links:**

<!-- References checked 2026-07-27. Quantum-specific research is listed before generic frameworks and regulatory sources. -->

1. [Suresh et al. - Short Paper: A Quantum Circuit Obfuscation Methodology for Security and Privacy (HASP 2021)](https://doi.org/10.1145/3505253.3505260): Establishes the untrusted-compiler circuit-IP-theft threat and proposes an obfuscation countermeasure.
2. [Chu et al. - QTrojan: A Circuit Backdoor Against Quantum Neural Networks (IEEE ICASSP 2023)](https://doi.org/10.1109/ICASSP49357.2023.10096293): Demonstrates a backdoor triggered through quantum-compiler configuration files ([arXiv:2302.08090](https://arxiv.org/abs/2302.08090)).
3. [Weder et al. - QProv: A provenance system for quantum computing](https://doi.org/10.1049/qtc2.12012): Identifies quantum-specific provenance attributes and presents a system for collecting them across quantum circuits, quantum computers, compilation, and execution.
4. [Hrdá et al. - CHEQ: Towards Enabling Circuit Integrity Checking in Quantum Controllers](https://doi.org/10.1145/3716368.3735296): Outlines integrity requirements and a Circuit Hashing Engine design for controller-side quantum-circuit integrity checking.
5. [SLSA - Provenance](https://slsa.dev/spec/v1.2/provenance): Defines a provenance model for describing where, when, and how an artifact was produced through a software supply chain.
6. [IETF RFC 9334 - Remote ATtestation procedureS (RATS) Architecture](https://www.rfc-editor.org/rfc/rfc9334.html): Defines Attester, Verifier, and Relying Party roles, Evidence and Attestation Results, and the trust relationships used to appraise evidentiary claims.
7. [EU DORA - Regulation (EU) 2022/2554, Articles 28-44](https://eur-lex.europa.eu/eli/reg/2022/2554/oj/eng): Establishes ICT third-party risk-management obligations relevant when regulated financial entities rely on quantum platform services.
8. [EU Cyber Resilience Act - Regulation (EU) 2024/2847, Annex I](https://eur-lex.europa.eu/eli/reg/2024/2847/oj/eng): Establishes essential cybersecurity requirements for products with digital elements; potential applicability to commercial quantum software depends on the product and applicable scope.

**Standards and Regulatory Mapping:**

> **TODO:** This section is carried over from the source document and is not part of `_template.md`. Confirm whether to keep it in the final entry format, and verify each standard/citation.

The cited sources do not establish a formal standard that directly covers end-to-end quantum toolchain integrity. Published quantum-specific work includes Suresh et al. on circuit-theft threats (HASP 2021), Chu et al. on QTrojan (2023), QProv on quantum-specific provenance collection (2021), and CHEQ on controller-side circuit-integrity checking (2025). SLSA and RFC 9334 provide generic provenance and attestation models that can inform quantum-platform controls but are not quantum-specific. No cited source establishes the combined submitted-job-to-dispatch-to-result control as a deployed quantum-platform standard.

For regulated use, DORA may be relevant to financial entities' management of ICT third-party risk when quantum platform services support regulated operations. The Cyber Resilience Act may apply to quantum software that qualifies as a product with digital elements. Applicability depends on the product, deployment, parties, and governing legal scope.
