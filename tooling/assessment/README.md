# Quantum Readiness Assessment

**Status: v0.1 draft for community review.** Structure, questions, and scoring
are all open to challenge - this is a starting point, in the same spirit as the
Top 10 v0.1 itself.

A structured self-assessment that operationalises the migration surface of the
OWASP Top 10 for Quantum Security Risks (QS01-QS07). The charter's Track 1
commits to an assessment capability "so the Top 10 becomes operational rather
than purely descriptive"; this is its markdown-first form. It is usable today
with a spreadsheet and a working session, and is designed to grow into the
open-source assistant tool after the Top 10 v1 ships.

**Audience:** security architects, cryptography engineers, and risk owners
running or planning a PQC migration programme. A useful first pass takes one
workshop with the people who own applications, PKI, infrastructure, and
procurement; the answers you cannot produce in that session are themselves
findings.

---

## How to use it

1. Answer each question in the seven domains below: **Yes**, **Partial**,
   **No**, or **Unknown**.
2. Score: Yes = 2, Partial = 1, No = 0, **Unknown = 0 and flag it** - an
   Unknown is an inventory gap (QS04) regardless of which domain it appears in.
3. For each No/Unknown, record a finding using the table template at the end,
   linking the QS entry it comes from.
4. Prioritise findings with the deadline matrix and the Mosca worksheet: a
   finding is urgent in proportion to how close its governing deadline is and
   how long the data or trust anchor behind it must stay sound.

Do not average scores across domains. A strong inventory does not compensate
for unmonitored hybrid fallback; the domains fail independently.

## Deadline matrix

The dates findings are prioritised against. All are published, current
guidance; none are speculative.

| Anchor | Milestone | Date |
|---|---|---|
| UK NCSC [migration timelines](https://www.ncsc.gov.uk/guidance/pqc-migration-timelines) | Discovery complete, migration goals and initial plan defined | 2028 |
| UK NCSC | Highest-priority migration executed, full roadmap refined | 2031 |
| UK NCSC | Migration complete across systems, services, products | 2035 |
| EU [Coordinated Implementation Roadmap](https://digital-strategy.ec.europa.eu/en/library/coordinated-implementation-roadmap-transition-post-quantum-cryptography) | Cryptographic inventory and dependency map | end-2026 |
| EU Roadmap | Standalone quantum-vulnerable public-key crypto prohibited for high-risk use | end-2030 |
| NSA [CNSA 2.0](https://www.nsa.gov/Press-Room/News-Highlights/Article/Article/3148990/nsa-releases-future-quantum-resistant-qr-algorithm-requirements-for-national-se/) | Software and firmware signing exclusively CNSA 2.0 | 2030 |
| NSA CNSA 2.0 | Niche equipment and legacy systems fully transitioned | 2033 |
| CA/Browser Forum | Maximum TLS certificate lifetime 47 days | 2029 |
| NIST [IR 8547 (draft)](https://csrc.nist.gov/pubs/ir/8547/ipd) | Proposed deprecation of RSA-2048 / ECC-P256; full disallowance | 2030 / 2035 |

## Mosca worksheet

For each major data class, estimate three numbers, in years:

- **X** - time your organisation needs to complete migration for the systems
  protecting this data class
- **Y** - how long this data must remain confidential (or this signature must
  remain trustworthy)
- **Z** - time until a cryptographically relevant quantum computer, per your
  chosen planning anchor (the deadline matrix implies regulators are planning
  around 2030-2035)

**If X + Y > Z, that data class is exposed now**, and harvest-now-decrypt-later
collection (QS01) makes the exposure current rather than future. Sort data
classes by X + Y - Z, descending: that is your migration priority order,
independent of sensitivity labels.

---

## Domain A - Data exposure and lifetimes (QS01, QS02)

| # | Question | Answer |
|---|---|---|
| A1 | Is data classified by required confidentiality **lifetime**, not only by sensitivity level? | |
| A2 | Have you identified every channel where your ciphertext crosses an untrusted boundary and could be passively recorded (internet transit, third-party networks, satellite/microwave links)? | |
| A3 | For encrypted data at rest, do you know the full key hierarchy - specifically whether symmetric data keys are wrapped by RSA or ECC keys? | |
| A4 | Has the Mosca worksheet been completed for your longest-lived data classes? | |
| A5 | Where X + Y > Z, is there an active re-encryption or PQC-envelope plan for that data class, sequenced against migration capacity? | |
| A6 | Is retention actively minimised for long-lived sensitive data - is anything being kept encrypted that does not need to be kept at all? | |

## Domain B - Signatures and trust anchors (QS03)

| # | Question | Answer |
|---|---|---|
| B1 | Is there an inventory of all signing keys: code signing, firmware signing, CA hierarchy (root/intermediate/issuing), document signing, JWT/SAML issuers? | |
| B2 | Do you know which verifier populations (devices, services, customers) trust each anchor, and which of those populations cannot be upgraded easily? | |
| B3 | Is there a migration plan for the CA hierarchy and code-signing infrastructure that runs **ahead of** the general estate, reflecting its longer blast radius and lead time? | |
| B4 | Where signatures must stay valid for years (contracts, evidence, releases), is re-signing or counter-signing with ML-DSA / SLH-DSA planned before classical schemes are deprecated? | |
| B5 | Can your certificate operations sustain 47-day TLS certificate lifetimes (automation, not manual issuance) ahead of 2029? | |
| B6 | If you sign software or firmware under CNSA 2.0 scope, will signing be exclusively CNSA 2.0 by 2030? | |

## Domain C - Inventory and CBOM (QS04)

| # | Question | Answer |
|---|---|---|
| C1 | Does a structured cryptographic inventory exist in a CBOM-compatible format (CycloneDX / SPDX), rather than prose or spreadsheets alone? | |
| C2 | Does it cover the classes inventories usually miss: HSM and TPM contents, embedded/firmware keys, third-party components, SaaS dependencies? | |
| C3 | Does each record capture algorithm, key length, **custody, rotation, and a named owner** - not just algorithm and length? | |
| C4 | Is the inventory maintained by process (change management, procurement, CI/CD integration), not by one-off audit? | |
| C5 | Would your inventory satisfy the EU end-2026 inventory-and-dependency-map requirement if it applied to you today? | |

## Domain D - Crypto-agility (QS05)

| # | Question | Answer |
|---|---|---|
| D1 | Is algorithm selection abstracted from business logic, such that an algorithm or parameter-set change does not require application rewrites? | |
| D2 | Are you on library versions that ship standardised PQC (for example OpenSSL 3.5+), rather than forks or bespoke implementations? | |
| D3 | Has an algorithm **rotation actually been executed end-to-end in a test environment** - including embedded and mobile clients - rather than assumed possible? | |
| D4 | Do procurement contracts require PQC support and demonstrable algorithm replacement on a defined timescale? | |
| D5 | Are symmetric-layer decisions (AES key sizes, hash strengths, MAC lengths, KDFs) reviewed as part of the programme, not exempted as "not a quantum problem"? | |

## Domain E - Migration and hybrid deployment (QS06)

| # | Question | Answer |
|---|---|---|
| E1 | Where hybrid key exchange is deployed, is it via standard named groups (X25519MLKEM768 family) in maintained libraries - no custom combiners? | |
| E2 | Is the **negotiated** group monitored in production, with alerting on classical-only handshakes - as opposed to trusting configuration? | |
| E3 | Has client fallback behaviour been tested by inducing hybrid handshake failure, and does it fail closed where classical-only is unacceptable? | |
| E4 | Have handshake-size effects (ClientHello fragmentation, middlebox behaviour) been piloted per architecture pattern before production rollout? | |
| E5 | Are you certain no pre-standard Kyber/Dilithium draft implementations remain in production since ML-KEM / ML-DSA superseded them? | |
| E6 | Is every hybrid deployment tracked as transitional, with a pure-PQC replacement date ahead of the end-2030 high-risk prohibition where it applies? | |

## Domain F - Hardware roots of trust (QS07)

| # | Question | Answer |
|---|---|---|
| F1 | Is there an inventory of hardware-anchored cryptography: TPMs, UEFI Secure Boot keys, smart cards, HSMs, embedded device certificates, signed firmware in OT/vehicles? | |
| F2 | For each device class, do you know whether the trust anchor can be updated in the field, and has that update path been tested? | |
| F3 | Do you know which device fleets have service lives crossing 2030/2035 but no re-anchoring path - and is retirement or compensating control planned for them? | |
| F4 | Does hardware procurement now require PQC capability or demonstrable crypto-agility (including capacity for ML-DSA key and signature sizes)? | |
| F5 | Are your TPM, HSM, smart-card, and embedded suppliers engaged on PQC roadmaps, with their answers recorded in the inventory? | |

## Domain G - Programme integrity (QS04, QS05)

The domain that checks the programme itself, not the estate.

| # | Question | Answer |
|---|---|---|
| G1 | Is migration progress measured in **quantum-vulnerable algorithms retired and anchors re-signed**, not in budget spent or projects delivered? | |
| G2 | For every product claiming to be "quantum-safe", can you name the standardised algorithm and parameter set it implements, backed by a checkable CMVP/CAVP validation record? | |
| G3 | Has any QKD procurement been assessed against the published NCSC and NSA positions, including the question of how endpoint authentication is protected? | |
| G4 | Does every quantum-branded purchase map to a catalogued quantum-vulnerable asset in the inventory that it actually migrates? | |

---

## Findings template

| Finding | Domain / QS entry | Evidence | Governing deadline | Mosca margin (X+Y-Z) | Priority | Owner |
|---|---|---|---|---|---|---|
| e.g. Backup archives AES-encrypted, data keys RSA-wrapped | A3 / QS01, QS02 | KMS config export | EU end-2030 | +6 years | 1 | |

Priority rule of thumb: anything with a positive Mosca margin or a governing
deadline before 2031 is priority 1; deadline 2031-2033 is priority 2; the
remainder are priority 3, revisited annually as Z estimates move.

## Contributing

This draft deliberately covers only the migration surface (QS01-QS07). A
platform-surface extension (QS08-QS10) should exist once those entries
stabilise through Sprint 1 voting - contributions welcome, especially from
practitioners running workloads on quantum platforms today.

Question wording, scoring, and the deadline matrix are all open to challenge
via PR or in `#project-quantum-security`. Where a question's premise conflicts
with an entry as it evolves through the sprints, the entry wins and the
question should be updated.
