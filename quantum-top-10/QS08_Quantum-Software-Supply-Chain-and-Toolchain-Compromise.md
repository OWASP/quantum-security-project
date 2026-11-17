## QS08:2026 - Quantum Software Supply Chain and Toolchain Compromise

Description:

Quantum applications depend on a software supply chain that extends beyond the quantum compiler and execution toolchain. Source code, third-party dependencies, SDKs, build systems, CI/CD pipelines, quantum circuit artifacts, package repositories, container images, deployment workflows, and provenance metadata can all influence the workload ultimately submitted for quantum execution.

A compromise at any of these stages can introduce malicious or unauthorized changes before the workload reaches a trusted quantum compiler or platform. The resulting application or circuit may remain syntactically valid and operational while producing manipulated results, leaking intellectual property, or embedding malicious behavior.

This candidate risk is intentionally distinct from QS09:2026 - Toolchain and Compiler Compromise. QS09 addresses whether the quantum transformation and execution toolchain can be trusted. This risk addresses whether the software and artifacts entering that toolchain can be trusted in the first place. A trusted compiler cannot compensate for a compromised dependency, build process, CI/CD pipeline, or artifact delivered to it.

Common Examples of Vulnerability:

  1. Quantum SDKs or third-party dependencies are consumed without version pinning, integrity verification, or dependency inventory.
  2. Quantum artifacts such as OpenQASM, QIR, or other intermediate representations move between development, build, testing, and deployment environments without cryptographic integrity verification.
  3. CI/CD pipelines allow unauthorized modification of source code, dependencies, build instructions, or generated quantum artifacts.
  4. Quantum software artifacts are distributed without verifiable provenance linking them to source, dependencies, build systems, and build parameters.
  5. Package repositories or artifact registries permit artifact substitution, dependency confusion, rollback, or publication through compromised identities.
  6. Deployment workflows do not verify that the artifact submitted for quantum execution corresponds to the reviewed and approved source version.
  7. SBOMs or equivalent dependency inventories are absent, incomplete, or not linked to the deployed artifact.

How to Prevent:

  1. Maintain inventories of software components and dependencies used in quantum applications, using SBOMs or equivalent manifests where appropriate.
  2. Pin and verify dependencies and SDK versions, and restrict dependency acquisition to trusted sources.
  3. Generate verifiable build provenance and retain attestations linking artifacts to source, dependencies, build systems, and build parameters.
  4. Cryptographically sign software and quantum artifacts, and verify signatures and provenance before deployment or execution.
  5. Harden CI/CD and build environments using isolated build infrastructure, least-privilege access, protected branches, controlled build identities, and auditable workflows.
  6. Apply software supply-chain frameworks such as SLSA and in-toto to quantum software development pipelines where applicable.
  7. Use reproducible or independently verifiable builds where technically feasible.
  8. Enforce policy gates that reject unsigned, unverified, or provenance-deficient artifacts before they reach production quantum environments.
  9. Protect package repositories and artifact registries against unauthorized publication, replacement, and rollback.

Example Attack Scenarios:

Scenario #1: An attacker compromises a third-party dependency used by a quantum optimization application. The dependency subtly modifies circuit parameters before the application invokes the quantum SDK. The resulting circuit remains valid and passes through a trusted transpiler and compiler, but the computation has been intentionally altered. Controls focused only on compiler or transpiler integrity do not detect the compromise.

Scenario #2: An attacker gains access to a CI/CD pipeline responsible for building and packaging a quantum application. After source review, the attacker modifies a generated OpenQASM or QIR artifact during the build process. The artifact is deployed without signature or provenance verification. The quantum platform executes it normally while developers incorrectly assume that it corresponds to the reviewed source.

Scenario #3: An organization retrieves a quantum SDK dependency from a package repository without pinning its source or verifying integrity. An attacker exploits dependency confusion or a compromised maintainer identity to publish a malicious package. The dependency alters quantum circuit generation while preserving expected application behavior, allowing the compromise to persist across builds.

Reference Links:

  1. [OWASP Quantum Security Project - QS08:2026 Quantum Software Supply Chain and Toolchain Compromise](https://github.com/OWASP/quantum-security-project/blob/main/quantum-top-10/QS08_Quantum-Software-Supply-Chain-and-Toolchain-Compromise.md) : Existing risk focused on compromise within the quantum transformation and execution toolchain.
  2. [SLSA - Supply-chain Levels for Software Artifacts](https://slsa.dev/) : Framework for software supply-chain integrity, provenance, and hardened build processes.
  3. [in-toto](https://in-toto.io/) : Framework for recording and verifying the steps, actors, and artifacts involved in a software supply chain.
  4. [Sigstore](https://www.sigstore.dev/) : Software artifact signing, verification, and transparency infrastructure.
  5. [NIST Secure Software Development Framework - SP 800-218](https://csrc.nist.gov/pubs/sp/800/218/final) : Secure software development practices applicable to software integrity and third-party components.
  6. [Piattini et al. - Let's do it right the first time: Survey on security concerns in the way to quantum software engineering](https://doi.org/10.1016/j.neucom.2023.03.060) : Research on security concerns across quantum software engineering.
  7. [Security discussions in quantum software projects on GitHub](https://doi.org/10.1016/j.jss.2025.112585) : Empirical research on security issues discussed in quantum software projects.
  8. [Rahman, Haghparast and Mikkonen - Classification of security challenges and mitigation approaches in quantum software engineering](https://doi.org/10.1016/j.jss.2026.112884) : Classification of quantum software security challenges and mitigations.
  9. [Silva - Toward Quantum-Resilient Software Supply Chains: A DevSecOps Case Study with Hybrid Post-Quantum Artifact Signing](https://doi.org/10.1109/QCNC69040.2026.00064) : Applied research on artifact integrity, provenance, CI/CD, and hybrid post-quantum signing in software supply chains.


<!-- MERGE SEAM. Everything above is the former QSxx Insecure Quantum Software Supply Chain candidate; everything below is the former QS09:2026 Toolchain and Compiler Compromise. The Sprint 1 vote merged both into this entry. Both are carried verbatim for the entry lead to reconcile. NOTE: the text above argues this risk is "intentionally distinct from QS09:2026 - Toolchain and Compiler Compromise" and its first reference link points at the file now merged here. Those two passages need the entry lead. -->

### Toolchain and Compiler Compromise (carried from the former QS09:2026)

**Description:**

A submitted quantum job is not just a circuit - it is hardcoded constants and computational data embedded in a program that flows through a compiler, transpiler, and pulse-level scheduler before reaching hardware, and each layer is a potential point of compromise. Quantum software stacks compose high-level frameworks (Qiskit, Cirq, PennyLane), transpilers, optimising compilers, pulse-level schedulers, and hardware configuration files. Two classes of attack are documented: circuit theft via compromised compilers (Suresh et al., HASP 2021), where - because circuits encode their data hardcoded as parameters - theft of the circuit is theft of both the algorithm and its inputs; and QTrojan (Chu et al., 2023), where adversaries stealthily disable data encoding inside a circuit by manipulating hardware configuration files disguised as routine pulse calibrations, so the circuit appears to execute normally but produces incorrect results invisibly. This is a supply-chain risk specific to quantum software stacks: circuit integrity, IP confidentiality, and pipeline trustworthiness must be evaluated, not assumed. A third class requires no compromised component at all. The interface between a circuit's gate-level description and its pulse-level implementation is not validated, so a custom gate can be defined whose actual pulse behaviour differs from what its gate-level specification implies. Xu and Szefer (IEEE S&P 2025) demonstrate qubit plunder, qubit block, qubit reorder, and timing, frequency, phase and waveform mismatch attacks on real quantum hardware, and find most current SDKs vulnerable. Here the malicious input is the gate definition itself, so an imported or shared custom gate is a supply-chain vector even when every component of the toolchain is authentic. 

Toolchain integrity also requires verifiable continuity across transformation and dispatch. A stable user-facing job identifier alone does not establish that the submitted circuit and parameters remain integrity-linked to the security-relevant transformed artifact dispatched for execution, the platform-reported backend and execution context, and the returned result record.

**Common Examples of Vulnerability:**

1. Quantum toolchains composed of multiple open-source and vendor components with limited integrity verification between stages.
2. Circuits that encode sensitive data as hardcoded parameters (most, given current platform constraints), exposed to theft anywhere along the pipeline.
3. Calibration and pulse-configuration update pipelines with weak authentication or no change auditing.
4. Production toolchains that auto-update compilers and transpilers without integrity verification.
5. Pulse and hardware configuration files treated as routine rather than a sensitive operations surface.
6. Quantum job pipelines that preserve a stable user-facing job identifier without integrity-protected linkage among the submitted circuit and parameters, the security-relevant transformed artifacts and toolchain configurations that produced them, the exact artifact and platform context used at dispatch, and the returned result record.
7. Custom gate definitions imported from shared libraries, collaborators, or third parties, where the pulse-level implementation is never checked against the gate-level specification it declares.
8. SDKs and submission pipelines that accept pulse-level circuit definitions without validating them against declared gate-level semantics, including the qubits, timing window, and frequencies a gate is scoped to touch.

**How to Prevent:**

1. Apply software supply-chain hygiene to quantum toolchains: signed releases, verified dependencies, reproducible builds where possible.
2. Where IP is embedded in circuits, evaluate whether the provider's audit and integrity controls suffice for the threat model.
3. Pin specific versions of compilers and transpilers; do not auto-update production toolchains without integrity verification.
4. Treat hardware configuration and pulse calibration as a sensitive surface: restrict modification access and audit changes.
5. Engage platform providers on transparency around toolchain integrity controls.
6. Maintain integrity-protected provenance for each security-relevant quantum-job transformation. Bind the submitted circuit and parameters to the resulting transformed artifacts; the identities, versions, configurations, and resolved dependencies of the toolchain components that produced them; and the target backend, topology, and applicable calibration-state or execution-context identifiers. Use content digests where appropriate for the artifact type and threat model, and use authenticated references or verifiable commitments where raw artifacts cannot be disclosed, so that substitution, omission, or mismatch can be detected.
7. At the dispatch boundary, verify that the exact artifact being dispatched is integrity-linked to the submitted job and its recorded transformation lineage. Also verify that the selected backend, topology, calibration state, and other security-relevant execution context satisfy the job's declared or policy-defined constraints. Block dispatch or require re-evaluation when any required binding or evidence item is absent, invalid, mismatched, stale, unauthorized, or outside the permitted context.
8. Return an authenticated record that binds the returned result artifact, or an integrity-protected identifier for it, to the submitted job, the security-relevant transformation lineage, the exact artifact identified at dispatch, and the platform-reported backend and execution context. Identify the record producer and the trust boundary within which its claims are evaluated. Treat a provider-generated record as a provider assertion; do not represent it as independent proof of physical QPU execution, computational correctness, or result fidelity.
9. Validate pulse-level implementations against their gate-level specification before submission, and reject custom gates whose pulse behaviour exceeds the qubit, timing, or frequency scope they declare.
10. Treat externally authored custom gate definitions as untrusted input and subject them to the same review as any third-party code dependency.

**Example Attack Scenarios:**

Scenario #1: A compromised transpiler in the pipeline exfiltrates submitted circuits (Suresh et al., HASP 2021). Because the circuit carries its proprietary optimisation problem hardcoded as parameters, the attacker steals both the algorithm and its confidential inputs in a single theft.

Scenario #2: An adversary alters a hardware configuration file to disable a circuit's data encoding, disguising the change as a routine pulse calibration (QTrojan, Chu et al., 2023). The victim's circuit appears to run normally but silently returns incorrect results, and the manipulation is invisible to the submitting user.

Scenario #3: After a tenant submits a quantum job, an attacker with write access to a transformation cache or artifact store substitutes a different transpiled artifact while preserving the original user-facing job identifier. The platform later reports the job as completed, but the returned result record is associated only with that identifier and not with the submitted circuit and parameters, the security-relevant transformation lineage, or the artifact and platform context identified at dispatch. The tenant cannot determine from the returned record whether the intended or substituted artifact was dispatched.

Scenario #4: A researcher reuses a custom gate definition from a shared circuit library. The gate's pulse-level implementation applies operations to qubits beyond those it declares (qubit plunder) and shifts the timing of neighbouring operations, corrupting the surrounding computation. The gate-level circuit the researcher inspects looks entirely correct, and no component of the toolchain has been compromised (Xu and Szefer, IEEE S&P 2025).

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
9. [Xu and Szefer - Security Attacks Abusing Pulse-level Quantum Circuits (IEEE S&P 2025)](https://ieeexplore.ieee.org/document/11023334/): First systematic exploration of attacks on the gate-level/pulse-level interface, demonstrated on real quantum hardware and simulators; most current SDKs found vulnerable. ([arXiv:2406.05941](https://arxiv.org/abs/2406.05941))
10. [SLSA - Supply-chain Levels for Software Artifacts](https://slsa.dev/): Supply-chain integrity framework applicable in spirit.


**Standards and Regulatory Mapping:**

> **TODO:** This section is carried over from the source document and is not part of `_template.md`. Confirm whether to keep it in the final entry format, and verify each standard/citation.

The cited sources do not establish a formal standard that directly covers end-to-end quantum toolchain integrity. Published quantum-specific work includes Suresh et al. on circuit-theft threats (HASP 2021), Chu et al. on QTrojan (2023), QProv on quantum-specific provenance collection (2021), and CHEQ on controller-side circuit-integrity checking (2025). SLSA and RFC 9334 provide generic provenance and attestation models that can inform quantum-platform controls but are not quantum-specific. No cited source establishes the combined submitted-job-to-dispatch-to-result control as a deployed quantum-platform standard.

No formal standard yet covers quantum toolchain integrity directly. The relevant published research includes Suresh et al. on circuit-theft attacks (HASP 2021), Chu et al. on QTrojan (2023), and Xu and Szefer on pulse-level circuit attacks (IEEE S&P 2025). General software supply chain frameworks - SLSA, Sigstore, CISA Secure Software Development Framework - apply in spirit but have not been adapted to quantum stacks. Where regulated entities consume quantum platform services, DORA Articles 28-44 third-party ICT risk obligations extend to the toolchain integrity of those providers. EU CRA Annex I state-of-the-art integrity and authenticity requirements apply to quantum software products placed on the EU market.

For regulated use, DORA may be relevant to financial entities' management of ICT third-party risk when quantum platform services support regulated operations. The Cyber Resilience Act may apply to quantum software that qualifies as a product with digital elements. Applicability depends on the product, deployment, parties, and governing legal scope.
