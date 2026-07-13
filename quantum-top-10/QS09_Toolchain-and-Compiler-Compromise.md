## QS09:2026 - Toolchain and Compiler Compromise

**Description:**

A submitted quantum job is not just a circuit - it is hardcoded constants and computational data embedded in a program that flows through a compiler, transpiler, and pulse-level scheduler before reaching hardware, and each layer is a potential point of compromise. Quantum software stacks compose high-level frameworks (Qiskit, Cirq, PennyLane), transpilers, optimising compilers, pulse-level schedulers, and hardware configuration files. Two classes of attack are documented: circuit theft via compromised compilers (Suresh et al., HASP 2021), where - because circuits encode their data hardcoded as parameters - theft of the circuit is theft of both the algorithm and its inputs; and QTrojan (Chu et al., 2023), where adversaries stealthily disable data encoding inside a circuit by manipulating hardware configuration files disguised as routine pulse calibrations, so the circuit appears to execute normally but produces incorrect results invisibly. This is a supply-chain risk specific to quantum software stacks: circuit integrity, IP confidentiality, and pipeline trustworthiness must be evaluated, not assumed.

**Common Examples of Vulnerability:**

1. Quantum toolchains composed of multiple open-source and vendor components with limited integrity verification between stages.
2. Circuits that encode sensitive data as hardcoded parameters (most, given current platform constraints), exposed to theft anywhere along the pipeline.
3. Calibration and pulse-configuration update pipelines with weak authentication or no change auditing.
4. Production toolchains that auto-update compilers and transpilers without integrity verification.
5. Pulse and hardware configuration files treated as routine rather than a sensitive operations surface.

**How to Prevent:**

1. Apply software supply-chain hygiene to quantum toolchains: signed releases, verified dependencies, reproducible builds where possible.
2. Where IP is embedded in circuits, evaluate whether the provider's audit and integrity controls suffice for the threat model.
3. Pin specific versions of compilers and transpilers; do not auto-update production toolchains without integrity verification.
4. Treat hardware configuration and pulse calibration as a sensitive surface: restrict modification access and audit changes.
5. Engage platform providers on transparency around toolchain integrity controls.

**Example Attack Scenarios:**

Scenario #1: A compromised transpiler in the pipeline exfiltrates submitted circuits (Suresh et al., HASP 2021). Because the circuit carries its proprietary optimisation problem hardcoded as parameters, the attacker steals both the algorithm and its confidential inputs in a single theft.

Scenario #2: An adversary alters a hardware configuration file to disable a circuit's data encoding, disguising the change as a routine pulse calibration (QTrojan, Chu et al., 2023). The victim's circuit appears to run normally but silently returns incorrect results, and the manipulation is invisible to the submitting user.

**Reference Links:**

<!-- REVIEW: reference URLs are best-effort canonical pages and require human verification against the source doc. -->

1. [Suresh et al. - Circuit theft via compromised quantum compilers (HASP 2021)](https://dl.acm.org/): Compiler-based circuit-theft attack.
2. [Chu et al. - QTrojan: stealthy data-encoding disablement via hardware configuration manipulation (2023)](https://arxiv.org/): QTrojan attack.
3. [SLSA - Supply-chain Levels for Software Artifacts](https://slsa.dev/): Supply-chain integrity framework applicable in spirit.
4. [EU DORA - Regulation (EU) 2022/2554, Articles 28-44](https://eur-lex.europa.eu/eli/reg/2022/2554/oj): Third-party ICT risk obligations extending to provider toolchain integrity.
5. [EU Cyber Resilience Act, Annex I](https://eur-lex.europa.eu/eli/reg/2024/2847/oj): State-of-the-art integrity and authenticity for quantum software products.

<!-- TODO: The source doc includes a "Standards and regulatory mapping" section not represented in _template.md. Preserved below — decide whether to extend the template to carry it.

Standards and regulatory mapping: No formal standard yet covers quantum toolchain integrity directly. The relevant published research includes Suresh et al. on circuit-theft attacks (HASP 2021) and Chu et al. on QTrojan (2023). General software supply chain frameworks - SLSA, Sigstore, CISA Secure Software Development Framework - apply in spirit but have not been adapted to quantum stacks. Where regulated entities consume quantum platform services, DORA Articles 28-44 third-party ICT risk obligations extend to the toolchain integrity of those providers. EU CRA Annex I state-of-the-art integrity and authenticity requirements apply to quantum software products placed on the EU market.
-->

