## QS08:2026 - QPU Tenant Isolation Failures

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

<!-- REVIEW: reference URLs are best-effort canonical pages and require human verification against the source doc. -->

1. [Choudhury et al. - Quantum crosstalk attacks on multi-tenant quantum systems (NDSS 2025)](https://www.ndss-symposium.org/): Demonstrated crosstalk fidelity-degradation attack.
2. [Ash-Saki et al. - Crosstalk-based fault injection in NISQ-era quantum systems (ISLPED 2020)](https://dl.acm.org/): Crosstalk fault injection.
3. [Xu et al. - Reset-gate state leakage and power side-channel attacks on quantum controllers (CCS 2023)](https://dl.acm.org/doi/proceedings/10.1145/3576915): Reset-gate state leakage across tenant boundary.
4. [EU DORA - Regulation (EU) 2022/2554, Article 28](https://eur-lex.europa.eu/eli/reg/2022/2554/oj): Third-party ICT risk expectation that platform claims are evidenced.

<!-- TODO: The source doc includes a "Standards and regulatory mapping" section not represented in _template.md. Preserved below — decide whether to extend the template to carry it.

Standards and regulatory mapping: No formal standard yet covers QPU tenant isolation. NIST and NCSC have not published guidance on quantum platform security. The relevant published research includes Choudhury et al. on quantum crosstalk attacks (NDSS 2025), Ash-Saki et al. on crosstalk-based fault injection (ISLPED 2020), and Xu et al. on reset-gate state leakage (CCS 2023). Where DORA Article 28 third-party risk applies to financial entities using quantum platform services, the supervisory expectation is that platform security claims are evidenced rather than assumed.
-->

