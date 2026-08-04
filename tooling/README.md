# Tooling

The in-repo home for tools, proofs of concept, and utilities built and
maintained by the OWASP Quantum Security Project community.

The boundary with the [awesomelist](../awesomelist/) is build versus link: the
awesomelist curates external resources; this directory holds work the project
community contributes and maintains here. A tool that graduates to its own
repository keeps an index entry below.

## Index

| Tool | Status | Maps to | Maintainer |
|---|---|---|---|
| Quantum Readiness Assessment | proposed - markdown-first self-assessment operationalising the migration-surface entries | QS01-QS07 | *open* |
| QIR/LLVM circuit mapper | incoming - analyses LLVM bitcode / `.ll` files and maps quantum gates graphically, built on the QIR Alliance work | QS09 | Gabriel Ambroise (community) |

Reference tooling developed by community members in their own repositories:

- [pq-audit](https://github.com/mk-scorpiosec/pq-audit) - cryptographic
  inventory across code, cloud/IaC, certificates, network, containers, and web3,
  checked against FIPS 203/204/205, with JSON output for pipelines. Relevant to
  QS04 (inventory and CBOM).

## Contributing a tool

1. Open a thread in `#project-quantum-security` describing the tool, or just
   raise the pull request if it is already working.
2. Add a subdirectory under `tooling/` containing the tool and a README that
   states: what it does, which QS entries it operationalises, how to run it, and
   its maturity (proof of concept, usable, maintained).
3. Add a row to the index above, and state a named maintainer.

Every tool should declare which Top 10 entries it maps to - the point of this
directory is that the Top 10 stays operational rather than purely descriptive,
which is the charter's Track 1 commitment.

## Open question: code licensing

The repository is licensed CC BY-SA 4.0, which suits documentation but is not
designed for source code. Before substantial code lands here, the project leads
may want to designate a standard code license for `tooling/` contents (Apache-2.0
and MIT are the common OWASP choices), with CC BY-SA continuing to cover
documentation. Flagged for decision rather than assumed.
