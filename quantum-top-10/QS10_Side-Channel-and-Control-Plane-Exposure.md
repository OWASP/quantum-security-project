## QS10:2026 - Side-Channel and Control-Plane Exposure

**Description:**

Unlike classical processors, quantum computers depend on extensive classical control infrastructure to execute any quantum operation - signal generators, mixers, FPGAs, room-scale racks of conventional electronics, and classical control software. This control plane mediates everything that reaches the QPU and everything observed from it, has not been studied comprehensively from a security perspective, and is more physically accessible than the microchip-scale architecture of classical CPUs. Side-channel attacks against this layer are demonstrated: timing analysis of reset operations reveals program execution patterns (Mi et al., CCS 2022), and power-consumption traces from quantum controllers enable reverse-engineering of gate-level circuits (Xu et al., CCS 2023). Combined with the fact that current platforms have no quantum memory or quantum networking - so every job carries its data hardcoded into the program itself - interception or side-channel observation of the control plane is a direct path to leaking the workload it runs.

**Common Examples of Vulnerability:**

1. Classical control infrastructure (signal generators, mixers, FPGAs, control software) accessible to other tenants or the platform operator.
2. Timing observability of a tenant's circuit execution, leaking program structure via reset-operation timing.
3. Power-trace observability where control electronics are co-located with other workloads in a shared physical environment.
4. Platforms that do not document side-channel resistance properties.
5. Sensitive data hardcoded into circuits (no quantum memory), so any control-plane compromise is data compromise.

**How to Prevent:**

1. For sensitive workloads, prefer providers that document side-channel resistance and physical isolation properties.
2. For on-premises quantum hardware, apply physical security controls comparable to cryptographic key infrastructure: restricted access, monitoring, tamper-evidence.
3. Where workloads can be split, randomise execution timing and parameter ordering to reduce information leak.
4. Engage providers on disclosure of side-channel research and applied mitigations.
5. Treat control-plane compromise as equivalent to workload compromise - the data is hardcoded in the circuit.
6. Track ongoing research; new results appear at major security venues each year.

**Example Attack Scenarios:**

Scenario #1: An attacker with access to the classical control plane performs timing analysis of reset operations (Mi et al., CCS 2022), recovering the structure of a victim tenant's quantum program from the timing of classical reset commands - without touching the qubits themselves.

Scenario #2: Power-consumption traces are captured from the controllers driving the QPU (Xu et al., CCS 2023). Power analysis of the classical electronics recovers the gate sequence of a confidential circuit; because the circuit's data is hardcoded, reconstructing the gates reconstructs the workload and its inputs.

**Reference Links:**

<!-- References verified 2026-07-13. Academic titles/DOIs corrected; #1 is a defense paper documenting reset-operation state leakage, #2 is a distinct CCS 2023 paper from the reset-operations one cited in QS08. -->

1. [Mi et al. - Securing Reset Operations in NISQ Quantum Computers (CCS 2022)](https://doi.org/10.1145/3548606.3559380): Documents reset-operation state leakage / timing exposure across tenants.
2. [Xu et al. - Exploration of Power Side-Channel Vulnerabilities in Quantum Computer Controllers (CCS 2023)](https://doi.org/10.1145/3576915.3623118): Gate-level circuit reverse-engineering via power traces.
3. [NIST FIPS 140-3 - Security Requirements for Cryptographic Modules](https://csrc.nist.gov/pubs/fips/140-3/final): Classical physical-security framework offering partial conceptual coverage.
4. [Common Criteria (ISO/IEC 15408)](https://www.commoncriteriaportal.org/): Side-channel evaluation concepts not yet adapted to quantum control infrastructure.

**Standards and Regulatory Mapping:**

> **TODO:** This section is carried over from the source document and is not part of `_template.md`. Confirm whether to keep it in the final entry format, and verify each standard/citation.

No formal standard yet covers quantum platform side channels. The relevant published research includes Mi et al. on timing side channels (CCS 2022) and Xu et al. on power side-channel attacks (CCS 2023). General classical side-channel frameworks (FIPS 140-3 physical security requirements, Common Criteria) provide partial conceptual coverage but have not been adapted to quantum control infrastructure.

