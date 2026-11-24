# Getting Started with Quantum Security

New to quantum security? This page is a guided path through the concepts you need in order to read the [OWASP Top 10 for Quantum Security Risks](README.md#the-owasp-top-10-for-quantum-security-risks) critically and contribute to the project. It is ordered deliberately — each section builds on the one before it. For a curated link collection, see the [awesome list](https://github.com/OWASP/quantum-security-project/tree/main/awesomelist)..

No physics background is required. You will not need to know what a Hamiltonian is. You will need to be comfortable with ordinary security concepts: key exchange, signatures, side channels, supply chains.

Sections 1–5 are concepts; **section 6 is hands-on** — free courses and live endpoints you can work through. If you learn better by doing, skip ahead and come back.

---

## 1. Why quantum computers break cryptography

Two quantum algorithms matter for security, and it is worth being precise about which does what.

**Shor's algorithm** (1994) solves integer factoring and discrete logarithms in polynomial time on a sufficiently large quantum computer. Those two problems are the entire security basis of RSA, Diffie-Hellman, and elliptic-curve cryptography — including the signature schemes built on them (ECDSA, EdDSA, DSA). When a large enough machine exists, all mainstream public-key cryptography deployed today fails at once.

**Grover's algorithm** provides a quadratic speedup for brute-force search. Against symmetric cryptography it effectively halves the security level: AES-128 behaves like roughly 64-bit security. The remedy is larger parameters (AES-256, SHA-384), not new algorithm families — which is why quantum is primarily a public-key problem, though symmetric parameters still need review ([QS05](quantum-top-10/QS05_Crypto-Agility-Failures.md)).

Two acronyms you will see constantly:

- **CRQC** — Cryptographically Relevant Quantum Computer: a machine large and reliable enough to actually run Shor's algorithm against real key sizes. It does not exist. Government planning horizons cluster around 2030–2035, but nobody knows.
- **NISQ** — Noisy Intermediate-Scale Quantum: today's machines. Small, error-prone, and nowhere near cryptographically relevant — but commercially available via the cloud, which is why the platform surface (section 5) matters already.

**Read:** the opening sections of [NIST IR 8547](https://csrc.nist.gov/pubs/ir/8547/ipd) for the official framing of the transition.

## 2. Why this matters now, not in 2035

If the dangerous machine does not exist, why is this a current risk category? Two reasons, and they anchor the first two entries of the Top 10.

**Harvest-Now-Decrypt-Later (HNDL).** An adversary records encrypted traffic or exfiltrates encrypted archives today, stores them, and decrypts them once a CRQC exists. The capture half of the attack requires no quantum computer — only storage and patience. Every TLS session whose key exchange used RSA or ECDH is future-readable to whoever captured it. This is [QS01](quantum-top-10/QS01_Harvest-Now-Decrypt-Later-Exposure.md).

**Mosca's inequality.** The planning rule: if **X** (how long your data must stay confidential) plus **Y** (how long your migration takes) exceeds **Z** (time until a CRQC), you are exposed *today* — data encrypted now will still need protection after the machine arrives. For a hospital with 30-year records or a state with indefinite secrets, the inequality already fails on any plausible Z. This is the engine behind [QS02](quantum-top-10/QS02_Long-Lived-Sensitive-Data.md).

**Read:** the joint CISA/NSA/NIST [Quantum-Readiness fact sheet](https://www.cisa.gov/resources-tools/resources/quantum-readiness-migration-post-quantum-cryptography) — short, official, and the source of the migration playbook the Top 10's migration entries assume.

## 3. The fix: post-quantum cryptography

**Post-quantum cryptography (PQC)** is classical cryptography built on math problems with no known quantum speedup — mostly lattice problems and hash functions. It runs on ordinary computers; no quantum hardware is involved in the defence.

The NIST standards, finalized August 2024:

| Standard | Algorithm | Replaces | Notes |
|---|---|---|---|
| [FIPS 203](https://csrc.nist.gov/pubs/fips/203/final) | ML-KEM (Kyber) | RSA/ECDH key exchange | Parameter sets 512/768/1024 |
| [FIPS 204](https://csrc.nist.gov/pubs/fips/204/final) | ML-DSA (Dilithium) | RSA/ECDSA signatures | Keys 2–4 KB, signatures 2.4–4.5 KB |
| [FIPS 205](https://csrc.nist.gov/pubs/fips/205/final) | SLH-DSA (SPHINCS+) | Long-lived signatures | Conservative hash-based assumptions |

Note the sizes. PQC signatures are roughly ten times larger than classical ones, which is why constrained hardware — smart cards, TPMs, HSMs — gets its own Top 10 entry ([QS07](quantum-top-10/QS07_Hardware-Roots-of-Trust.md)).

**Hybrid cryptography** is the transition pattern: combine a classical and a PQC algorithm so that an attacker must break both. Done correctly, the session key is derived through a KDF over both inputs. Done incorrectly — key derived from one input, silent fallback to classical, downgrade-able negotiation — hybrid gives false comfort, which is [QS06](quantum-top-10/QS06_Insecure-Migration-and-Hybrid-Misuse.md).

The deadlines are regulatory, not just technical: NIST deprecates RSA/ECC by 2030 and disallows them by 2035; NSA's CNSA 2.0 requires quantum-resistant software signing by 2030; the EU roadmap prohibits standalone classical PKC for high-risk uses after 2030.

**Read:** the [Cloudflare post-quantum blog series](https://blog.cloudflare.com/tag/post-quantum/) for what deployment actually looks like at scale.

## 4. The migration surface (QS01–QS07)

The first seven Top 10 entries cover the cryptographic transition. The consistent lesson: migration fails on engineering realities, not algorithm choice.

A useful mental pipeline, mapped to the entries it defends against:

1. **Inventory** — you cannot migrate what you cannot see. Discovering where your estate uses which algorithms (code, protocols, certificates, hardware, vendor products) is step one in every government playbook. Its absence is [QS04](quantum-top-10/QS04_Absent-Cryptographic-Inventory-and-CBOM.md).
2. **Prioritise by data lifetime** — Mosca's inequality, applied per dataset ([QS02](quantum-top-10/QS02_Long-Lived-Sensitive-Data.md)). A medium-sensitivity record kept 30 years outranks a high-sensitivity record kept two.
3. **Build agility** — hard-coded algorithms mean replacement instead of migration ([QS05](quantum-top-10/QS05_Crypto-Agility-Failures.md)). Agility outlives this transition: PQC parameter sets will themselves change.
4. **Migrate, anchors first** — signature and trust-anchor migration has the longest lead time and the widest blast radius, because forgery is an *active* attack: fake updates, forged certificates ([QS03](quantum-top-10/QS03_Vulnerable-Signatures-and-Code-Signing.md)). Hardware roots of trust may be physically unreplaceable on relevant timescales ([QS07](quantum-top-10/QS07_Hardware-Roots-of-Trust.md)). And do hybrid correctly ([QS06](quantum-top-10/QS06_Insecure-Migration-and-Hybrid-Misuse.md)).

**Tooling:** see the [awesome list's cryptographic inventory section](awesomelist/README.md#cryptographic-inventory) for discovery tools, and its [PQC implementations section](awesomelist/README.md#pqc-implementations) for libraries that ship the standards.

## 5. The platform surface (QS08–QS10)

The last three entries concern something different: the security of quantum computers themselves, as cloud services people already pay to use. This is the side of the project most in need of contributors, and the least familiar to most security practitioners — which makes it the more interesting place to start contributing.

The architecture you need in your head: you never touch a QPU directly. A job flows from your **gate-level circuit** (the abstract program you write) through frameworks, transpilers, and compilers, down to **pulse-level** control signals (calibrated microwaves), executed by racks of **classical control electronics** driving a possibly **shared, multi-tenant** QPU. Results come back as probability distributions.

One architectural fact drives most of the platform risks: **current quantum computers have no quantum memory**. Your input data enters hardcoded as constants inside the circuit itself. Stealing the circuit therefore steals the algorithm *and* the data; observing the control plane observes the workload.

The three entries, and the peer-reviewed attacks behind them:

- [QS08 — QPU Tenant Isolation Failures](quantum-top-10/QS08_QPU-Tenant-Isolation-Failures.md): crosstalk from an adjacent tenant's circuit degrades your computation (NDSS 2025); standard qubit reset leaves residual state readable by the next tenant (CCS 2023).
- [QS09 — Toolchain and Compiler Compromise](quantum-top-10/QS09_Toolchain-and-Compiler-Compromise.md): compromised compilers steal circuits (HASP 2021); config-file backdoors corrupt results invisibly (QTrojan, ICASSP 2023); and the gate-level/pulse-level gap is itself attackable with no compromised component at all (IEEE S&P 2025).
- [QS10 — Side-Channel and Control-Plane Exposure](quantum-top-10/QS10_Side-Channel-and-Control-Plane-Exposure.md): reset timing reveals program structure (CCS 2022); power traces from the classical controllers reconstruct entire circuits (CCS 2023).

A further theme under active discussion in the project's proposals: whether you can trust results at all — a tampered or degraded execution looks like ordinary noise, and provider-issued records are assertions, not proof. See the open proposals for where this debate stands.

**Read:** ["A Primer on Security of Quantum Computing Hardware"](https://arxiv.org/abs/2305.02505) for a survey of the hardware attack surface.

## 6. Hands-on: learn by doing

Reading only gets you so far. Everything below is free, and most of it runs in a browser with no setup. Suggested order for someone coming from security rather than physics.

### Start here if you have one afternoon

- **[Practical Introduction to Quantum Safe Cryptography](https://quantum.cloud.ibm.com/learning/en/courses/quantum-safe-cryptography)** (IBM Quantum Learning, ~10 hours) — the single most on-topic course for this project: the risk quantum poses to cryptography and what "quantum-safe" actually means. Start here if you only do one thing.
- **[Test your own browser against post-quantum TLS](https://pq.cloudflareresearch.com/)** (Cloudflare Research, ~5 minutes) — an endpoint that tells you whether your connection negotiated a post-quantum key exchange. The fastest way to make sections 2 and 3 concrete: this is the migration, live, on your machine right now.

### Understanding the machines (context for QS08–QS10)

- **[Use a quantum computer today](https://quantum.cloud.ibm.com/learning/en/courses/use-a-qc-today)** (IBM, ~3 hours) — the shortest path from zero to having run something on real hardware.
- **[IBM Quantum Platform](https://quantum.cloud.ibm.com/)** — free-tier accounts can submit jobs to real QPUs (usage limits apply). Worth doing once: seeing your circuit queue, transpile, and return a probability distribution makes the platform-surface entries far less abstract. You will also notice, first-hand, that you receive no evidence of what physically executed.
- **[PennyLane Codebook](https://codebook.xanadu.ai/)** (Xanadu) — exercise-based, runs entirely in the browser, no account or install. Good if you prefer learning by writing code.
- **[Basics of Quantum Information](https://quantum.cloud.ibm.com/learning/en/courses/basics-of-quantum-information)** (IBM / John Watrous, ~15 hours) — university-level and genuinely rigorous. Optional for security work, excellent if you want real depth.

### Practical migration work

- **[NCCoE Migration to Post-Quantum Cryptography](https://www.nccoe.nist.gov/applied-cryptography/migration-to-pqc)** (NIST) — a live project with 50+ industry collaborators, organised around cryptographic discovery and interoperability testing. Its [FAQ and documentation site](https://pages.nist.gov/nccoe-migration-post-quantum-cryptography/) is the most practical migration reading available, and it maps directly onto [QS04](quantum-top-10/QS04_Absent-Cryptographic-Inventory-and-CBOM.md) and [QS05](quantum-top-10/QS05_Crypto-Agility-Failures.md).
- **[Open Quantum Safe test servers](https://test.openquantumsafe.org/)** — per-algorithm TLS endpoints for testing client support.
- **[Cloudflare's post-quantum blog series](https://blog.cloudflare.com/tag/post-quantum/)** — what deploying PQC at internet scale actually involved, including the failure modes.

> **A note on vendor material.** IBM, Cloudflare, Xanadu and others produce genuinely good free education, and it is listed here on merit. Read it aware that it sits alongside commercial offerings. The standards bodies (NIST, NCCoE, IETF) and the peer-reviewed papers cited in each Top 10 entry remain the neutral references.

## 7. Where to go from here

- **The Top 10 itself** — read the [draft entries](quantum-top-10/) with a critical eye. Every entry is open to challenge during the current sprint cycle, and several are under active revision through open community PRs.
- **The [awesome list](https://github.com/OWASP/quantum-security-project/tree/main/awesomelist)** — curated tools, implementations, and standards. Contributions welcome, especially platform-security resources, which it does not yet cover.
- **Key primary sources** — [NIST IR 8547](https://csrc.nist.gov/pubs/ir/8547/ipd) (transition roadmap), the [CISA/NSA/NIST fact sheet](https://www.cisa.gov/resources-tools/resources/quantum-readiness-migration-post-quantum-cryptography) (migration playbook), and the papers cited in each entry's Reference Links.
- **Contribute** — see [How to Contribute](README.md#how-to-contribute). You do not need to be a quantum expert: close reading, evidence verification, and boundary-testing of entries are exactly the contributions the bootstrap phase needs, and the platform surface actively seeks newcomers willing to learn in public.
- **Community** — fortnightly community calls (see the README for the calendar invite) and `#project-quantum-security` on the [OWASP Slack](https://owasp.org/slack/invite).

---

*Part of the [OWASP Quantum Security Project](https://github.com/OWASP/quantum-security-project). Licensed CC BY-SA 4.0. Corrections and additions welcome by pull request.*
