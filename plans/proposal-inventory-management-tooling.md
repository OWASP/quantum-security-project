# Proposal: OWASP-maintained inventory management tooling

**Status:** Discussion draft, not a commitment to build. Raised following Roy's
suggestion on PR #33 that the project should build and maintain real tools -
inventory management, PQ-readiness software - with a proper contributor model,
rather than the tooling directory mostly hosting individually-contributed
proofs of concept. This sketches what that could concretely mean, so there's
something specific to react to rather than an open-ended idea.

---

## The gap this fills

[QS04](../quantum-top-10/QS04_Absent-Cryptographic-Inventory-and-CBOM.md) names
the risk - no organisation can migrate cryptography it hasn't catalogued - and
defines what a good inventory contains: algorithm, key length, custody,
rotation, and a named owner, ideally in CBOM format. The
[Readiness Assessment](../tooling/assessment/README.md)'s Domain C turns that
into self-assessment questions, and treats an "Unknown" answer as a QS04
finding in its own right.

Neither produces or maintains an actual inventory. They describe what one
should look like and ask whether one exists - they don't help build or operate
one. That's the gap: nothing in the project's own output closes the loop from
*assess* to *operate*.

## What already exists, and where it stops

[pq-audit](https://github.com/mk-scorpiosec/pq-audit) (community, referenced in
the original awesomelist thread) already does **discovery** - scanning code,
cloud/IaC, certificates, network, containers, and web3 against FIPS
203/204/205, with JSON output. That's real, useful, and not something this
proposal suggests duplicating.

What discovery tools don't do is **management**: once something is found, who
owns it, what's its rotation cadence, has it been migrated, and how does that
picture stay current as the estate changes. That's the layer QS04 asks for
beyond algorithm and key length, and it's the layer nothing currently covers.

## Proposed scope (strawman)

- **Not another scanner.** Ingest CBOM/JSON output from existing discovery
  tools (pq-audit and others) rather than re-implementing discovery.
- **Track, per asset:** algorithm, custody, rotation cadence, named owner,
  migration status, and a link to the QS finding it relates to.
- **Import/export CycloneDX CBOM**, per QS04's own recommended format.
- **Flag stale or missing fields** the same way the Readiness Assessment flags
  Unknown answers - an inventory entry missing an owner or rotation date is
  itself a finding, not a formatting gap.

## Open question: is this the same thing as "PQ-ready software"?

Roy's message named two things - inventory management and PQ-ready software.
This proposal assumes they're related but distinct: this document is about the
inventory/management layer. "PQ-ready software" might mean something adjacent -
a build-time or CI-time check that flags quantum-vulnerable crypto usage in a
codebase, closer to what pq-audit's code-scanning already does. Worth
Roy or John clarifying before scope gets set on either.

## MVP, if this goes ahead

Deliberately small: a CLI or lightweight web tool that ingests one or more
CBOMs, merges and deduplicates them, and produces a tracked register with
owner/status/rotation fields - not a scanner, not a dashboard, not integrations
with every CMDB on day one.

## Maintenance model

Named maintainer(s) plus an open contributor path, not a single-person side
project - the same distinction [`tooling/README.md`](../tooling/README.md)
already draws between OWASP-maintained deliverables and individually
contributed proofs of concept, applied here from the start rather than
retrofitted later.

## License

Same open question already flagged in `tooling/README.md`: the repository's
CC BY-SA 4.0 suits documentation, not code. Any code here needs a proper
license (Apache-2.0 or MIT are the common OWASP choices) designated before
substantial code lands.

## Ask

Does this match what "inventory management tooling" meant, or is the intent
different? Who else should be pulled into scoping this - pq-audit's maintainer,
or anyone else already working in this space? Happy to adjust or drop any part
of this in favour of what the leads actually have in mind.
