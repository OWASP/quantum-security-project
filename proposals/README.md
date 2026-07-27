# Proposals

This directory holds proposed new entries for the OWASP Top 10 for Quantum
Security Risks while they are under consideration. It exists because the
[sprint plan](../plans/sprint-plan-quantum-top10-2026.md) routes new entries
here: *"Open submission of new entries via pull request to the proposals
directory."*

Proposals live here rather than in [`quantum-top-10/`](../quantum-top-10/) so
that the draft list and the candidate pool stay visibly separate. Nothing in
this directory is part of the Top 10.

## Submitting a proposal

1. Copy [`../quantum-top-10/_template.md`](../quantum-top-10/_template.md) to
   `proposals/PROPOSAL_Short-Name.md`.
2. Fill in the template sections. Ground the content in published standards,
   regulation, or peer-reviewed research, and keep it vendor-neutral.
3. Tag the evidence for each attack class you describe, following
   [the evidence and anchor convention](../quantum-top-10/_evidence-convention.md).
4. Open a pull request against `main`.

Proposals do not carry a QS number. Numbering is assigned if and when an entry
is selected for the list.

## What happens to a proposal

Per the sprint plan, the generative sprint (to 3 August 2026) collects
proposals; Sprint 1 (3-17 August) ranks them by community vote alongside
internal review of evidence quality, scope overlap, and coverage across the
migration and platform surfaces. The v0.1 entries hold no incumbency and
compete on the same terms, so a proposal may displace an existing entry rather
than only be added alongside one.

Selected proposals move into `quantum-top-10/` with a QS number. Proposals that
are not selected stay here as a record of what was considered.

## Other kinds of contribution

- **Structured feedback on an existing entry:** open a pull request against that
  entry in `quantum-top-10/`, not a proposal here.
- **Less formed feedback, questions, or a discussion about scope:** open an
  [issue](https://github.com/OWASP/quantum-security-project/issues).

See [CONTRIBUTING.md](../CONTRIBUTING.md) for the full picture.
