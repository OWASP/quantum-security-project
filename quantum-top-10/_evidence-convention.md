# Evidence and anchor convention

The [sprint plan](../plans/sprint-plan-quantum-top10-2026.md) sets out the
working principle this document implements:

> **Evidence over speculation.** Proposals are tagged as demonstrated, emerging
> or theoretical, so that the evidence base of the final list is visible rather
> than implied.

Tagging makes the strength of each claim explicit in the artefact itself. A
theoretical risk is not disqualified by being theoretical - a list that only
admitted demonstrated attacks would miss most of the migration surface, where
the defining risk is that the attack is not yet possible. What matters is that
the reader can see which is which without reconstructing it from the references.

## Evidence tags

Tags apply per attack class, not per entry. An entry may carry more than one
tag where it describes several failure modes.

| Tag | Meaning |
|---|---|
| **demonstrated** | Shown against real systems: a published exploit or proof-of-concept on production or production-representative systems, or an assigned CVE. |
| **emerging** | Shown under laboratory conditions, typically in peer-reviewed research, or documented as a near-miss - but not yet observed against systems in production use. |
| **theoretical** | A sound analysis of a plausible failure mode, following from published cryptographic or architectural results, with no demonstration yet. |

A useful boundary case: an attack that depends on a cryptographically relevant
quantum computer (CRQC) is tagged by the state of everything *except* the CRQC.
Harvest-now-decrypt-later collection is demonstrated as collection; the
decryption step is not. Where an entry turns on this distinction, say so in the
text rather than resolving it in the tag alone.

## Anchor hierarchy

Every claim should rest on the strongest anchor available to it. Where a
stronger anchor exists, prefer it:

1. **Standards and regulation** - NIST FIPS and IR publications, IETF RFCs, UK
   NCSC guidance, EU regulation and roadmaps, NSA CNSA.
2. **Peer-reviewed research** - papers at recognised venues; cite the venue and
   a DOI or the publisher's page.
3. **CVEs and demonstrated proofs-of-concept** - cite the CVE record.
4. **Preprints and working drafts** - acceptable where nothing stronger exists,
   but mark the status in the citation.

Two practices keep the anchors trustworthy over the life of the list:

- **Cite the primary source.** Link the standard, the RFC, the CVE record, or
  the paper itself, not a secondary summary of it. Secondary sources have
  already introduced errors into this repository's citations.
- **Record verification.** Entries carry an HTML comment above the reference
  list noting the date the references were verified and any corrections made.
  Keep it current when you touch the references, and note when an anchor has
  been superseded - drafts become RFCs, and draft names change on working-group
  adoption.

## Applying a tag

State the tag inline where the attack class is described, or in the pull request
where an entry's overall evidence base is under discussion. If the template
gains a dedicated field for this - as proposed in
[issue #15](https://github.com/OWASP/quantum-security-project/issues/15) - that
field takes precedence over any convention here.
