# Proposal: OWASP-maintained inventory management tooling

**Status:** Discussion draft, not a commitment to build. Raised following Roy's
suggestion on PR #33 that the project should build and maintain real tools -
inventory management, PQ-readiness software - with a proper contributor model,
rather than the tooling directory mostly hosting individually-contributed
proofs of concept. This sketches what that could concretely mean, so there's
something specific to react to rather than an open-ended idea. Every design
decision below is a strawman offered for discussion, not a decision.

<!-- Prior-art references verified 2026-08-17 against the projects' own repositories and announcements. -->

---

## Why this, and why OWASP

Two regulatory clocks make an inventory the first artefact any migration
programme must produce:

- The **EU Coordinated Implementation Roadmap** requires a cryptographic
  inventory and dependency map by **end-2026** - months away.
- The **UK NCSC timeline** requires discovery complete, with defined migration
  goals and an initial plan, by **2028**.

[QS04](../quantum-top-10/QS04_Absent-Cryptographic-Inventory-and-CBOM.md) calls
the absence of this inventory "the single most common blocker to PQC migration
in 2026". Organisations facing these deadlines currently choose between
commercial platforms and spreadsheets. A vendor-neutral, open,
standards-aligned tool that produces exactly the artefact the deadlines demand
is squarely the kind of thing an OWASP project exists to provide - and it is
the natural executable counterpart to what this project's documents already
say: QS04 names the risk, the
[Readiness Assessment](../tooling/assessment/README.md)'s Domain C asks whether
you are exposed to it, and nothing yet helps you close it.

## Prior art - what exists, and where it stops

Surveyed so this proposal extends the ecosystem rather than duplicating it:

- **[pq-audit](https://github.com/mk-scorpiosec/pq-audit)** (community,
  referenced in the awesomelist thread) - **discovery**: scans code, cloud/IaC,
  certificates, network, containers, and web3 against FIPS 203/204/205, JSON
  output for pipelines.
- **[CBOMkit](https://github.com/cbomkit/cbomkit)** (originated at IBM
  Research, [donated to the Linux Foundation](https://research.ibm.com/blog/cryptographic-cbom-linux-foundation)) -
  **generation and analysis**: scans git repositories to generate CBOMs
  (Hyperion, Theia), visualises them (Coeus), checks them against compliance
  policies, and stores them behind a REST API.
- **[CycloneDX CBOM](https://cyclonedx.org/capabilities/cbom/)** - the
  **format**: the object model for cryptographic assets and their dependencies,
  already QS04's recommended representation.

What none of these provide is the **organisational register layer** - the
fields QS04 explicitly requires beyond algorithm and key length: named owner,
key custody, rotation cadence, lifecycle, and migration status, tracked over
time as the estate changes and reportable against the regulatory deadlines.
Discovery tools find cryptography; CBOMkit turns findings into standard
documents; nothing then answers *who owns this, when does it rotate, has it
been migrated, and what remains before end-2026*. That operational layer is
the proposed scope.

## Proposed scope

**Not another scanner, and not another CBOM generator.** Ingest what discovery
and generation tools already produce; add the management layer on top.

### The register: what gets tracked per asset

| Field | Example | Why |
|---|---|---|
| Asset and class | `payments-gateway TLS endpoint`, `firmware signing key`, `RSA-wrapped backup KEK` | QS04's asset classes |
| Location / system | cluster, KMS path, device fleet | Findability |
| Algorithm and parameters | `RSA-2048`, `ECDSA P-256`, `X25519MLKEM768` | The core inventory datum |
| Usage context | key establishment, code signing, key wrapping | Different QS entries, different deadlines |
| Custody | HSM, cloud KMS, software keystore, baked into firmware | QS04 requires custody, not just algorithm |
| Named owner | team or individual | QS04's most-cited gap; also Goutama's governance point on QS05 (#36) |
| Rotation cadence and last-rotated date | 90 days / 2026-06-01 | Stale rotation is a finding |
| Required confidentiality/integrity lifetime | 30 years (statutory retention) | The Mosca Y input, per QS01/QS03 |
| Migration status | not started / planned / hybrid interim / migrated / **no migration path** | "No path" is QS07's hardware case and must be representable, not hidden |
| Governing deadline | EU end-2026, CNSA 2.0 2030, NCSC 2031 | From the assessment's deadline matrix |
| Maps to QS entries | QS01, QS03... | Keeps register and Top 10 coupled |
| Evidence source and last-verified date | pq-audit scan 2026-08-01, manual attestation | An unverified row ages into an Unknown |

### Workflow

1. **Ingest** - CycloneDX CBOMs, discovery-tool JSON, certificate exports.
2. **Normalise and deduplicate** - the same key found by two scanners is one
   asset.
3. **Enrich** - the human layer no scanner can produce: owner, custody,
   required lifetime, governing deadline.
4. **Track** - status transitions over time; flag staleness (evidence older
   than a threshold reverts toward Unknown).
5. **Report** - see below.

### Reporting: the part that makes it worth building

- **Deadline gap report** - assets whose governing deadline arrives before
  their migration status will: the direct evidence artefact for the EU
  end-2026 and NCSC 2028 milestones.
- **Unknowns as findings** - any register row missing owner, custody, or
  lifetime is reported as a QS04 finding, exactly as the Readiness
  Assessment's scoring rule treats an Unknown answer.
- **Assessment integration** - Domain C of the assessment (C1-C5) becomes
  answerable from the register directly; several Domain A/B questions (data
  lifetimes, signing-key inventory) draw from the same fields.
- **Mosca view** - per asset class, X + Y against Z, giving the
  prioritisation order QS01 describes.

## Non-goals

- **Not a scanner** - pq-audit, CBOMkit-Hyperion and commercial tools do
  discovery; this consumes their output.
- **Not a CMDB or asset-management platform** - it tracks cryptographic
  assets for migration purposes, nothing broader.
- **Not a key manager** - it records custody and rotation facts; it never
  holds or touches key material.
- **Not a compliance certification** - reports are evidence an organisation
  assembles, not an OWASP attestation (the same non-endorsement line
  `tooling/README.md` already draws).

## Build options - a genuine fork in the road

**A. Standalone OWASP tool.** CLI-first, file-based register (JSON/YAML,
git-friendly, diffable), CycloneDX import/export. Full project control;
duplicates nothing today, but must track the CycloneDX spec as it evolves.

**B. Contribute the register layer upstream to CBOMkit.** It is now a Linux
Foundation project with storage and compliance-check components; ownership,
rotation, and migration-status tracking could be proposed as extensions there,
with OWASP contributing the requirements (QS04, the assessment mapping, the
deadline matrix) rather than a codebase. Least duplication; less project
identity and control.

**C. Hybrid.** OWASP defines and maintains the *register profile* - the field
schema above, published as a CycloneDX property-set convention plus the
deadline mapping - with a thin reference CLI; heavier lifting stays in the
existing ecosystem. Cheapest to maintain; standards-shaped, which is what a
documentation project is best at.

No recommendation is baked in here. Option B or C being right would be a fine
outcome for the project even though it means writing less code - the decision
belongs to scoping, and the pq-audit and CBOMkit maintainers should be in that
conversation.

## MVP and phasing (assuming option A or C)

- **Phase 0 - scoping.** This document, leads' direction on the open
  questions, contact with pq-audit and CBOMkit maintainers.
- **Phase 1 - register MVP.** CLI: ingest one or more CBOMs, merge and
  deduplicate, initialise a register with the enrichment fields, validate
  completeness (report Unknowns).
- **Phase 2 - reporting.** Deadline gap report, Mosca view, assessment
  Domain C answers.
- **Phase 3 - ecosystem.** Certificate-transparency and KMS-inventory
  importers, CI integration to diff the register against fresh scans.

Each phase is independently useful; the project can stop after any of them and
still have shipped something real.

## Maintenance model and licensing

Named maintainers (at least two, so no single-person side project), an open
contributor path with a roadmap in issues, and the same OWASP-deliverable bar
`tooling/README.md` draws: this is project output, maintained here - the
distinction applied from day one rather than retrofitted.

Code needs a real code license before any lands - Apache-2.0 or MIT per common
OWASP practice - since the repository's CC BY-SA 4.0 suits documentation only.
Same open question already flagged on #33; a decision on it gates Phase 1
regardless of which build option is chosen.

## Risks, named rather than discovered later

- **Duplication risk** - mitigated by the prior-art survey above and by
  keeping option B genuinely open.
- **Spec drift** - CycloneDX's crypto model is evolving; the register profile
  must version against it.
- **Volunteer bandwidth** - mitigated by the small Phase 1 and by each phase
  standing alone.
- **Vendor neutrality** - importers must not privilege any commercial
  scanner's format; CycloneDX is the canonical interchange.

## Open questions for the leads

1. Does this match what "inventory management tooling" meant - and is
   "PQ-ready software" (also named in Roy's message) the same tool, or a
   sibling (e.g. a CI-time check flagging quantum-vulnerable crypto in a
   build, closer to existing code-scanning)?
2. Standalone, upstream, or hybrid (A/B/C above)?
3. Who else joins scoping - pq-audit's maintainer, anyone with CBOMkit
   contacts?
4. Code license for `tooling/` (gates any phase beyond this document).

Happy to adjust or drop any part of this in favour of what the leads actually
have in mind.
