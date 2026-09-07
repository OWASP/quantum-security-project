## Candidate Risk - Insecure Quantum Software Supply Chain

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

  1. [OWASP Quantum Security Project - QS09:2026 Toolchain and Compiler Compromise](https://github.com/OWASP/quantum-security-project/blob/main/quantum-top-10/QS09_Toolchain-and-Compiler-Compromise.md) : Existing risk focused on compromise within the quantum transformation and execution toolchain.
  2. [SLSA - Supply-chain Levels for Software Artifacts](https://slsa.dev/) : Framework for software supply-chain integrity, provenance, and hardened build processes.
  3. [in-toto](https://in-toto.io/) : Framework for recording and verifying the steps, actors, and artifacts involved in a software supply chain.
  4. [Sigstore](https://www.sigstore.dev/) : Software artifact signing, verification, and transparency infrastructure.
  5. [NIST Secure Software Development Framework - SP 800-218](https://csrc.nist.gov/pubs/sp/800/218/final) : Secure software development practices applicable to software integrity and third-party components.
  6. [Piattini et al. - Let's do it right the first time: Survey on security concerns in the way to quantum software engineering](https://doi.org/10.1016/j.neucom.2023.03.060) : Research on security concerns across quantum software engineering.
  7. [Security discussions in quantum software projects on GitHub](https://doi.org/10.1016/j.jss.2025.112585) : Empirical research on security issues discussed in quantum software projects.
  8. [Rahman, Haghparast and Mikkonen - Classification of security challenges and mitigation approaches in quantum software engineering](https://doi.org/10.1016/j.jss.2026.112884) : Classification of quantum software security challenges and mitigations.
  9. [Silva - Toward Quantum-Resilient Software Supply Chains: A DevSecOps Case Study with Hybrid Post-Quantum Artifact Signing](https://doi.org/10.1109/QCNC69040.2026.00064) : Applied research on artifact integrity, provenance, CI/CD, and hybrid post-quantum signing in software supply chains.
