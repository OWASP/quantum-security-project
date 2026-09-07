# Tooling

The home for tools that are **OWASP Quantum Security Project deliverables** -
code authored as project output and living in this repository, not a directory
of links to independently maintained external projects. That distinction is
deliberate, per Roy's comment on the PR that opened this directory: this
project does not audit or maintain external repositories, and listing one here
would read as an endorsement or verification the project isn't in a position to
give. Tools built and maintained by the community in their own repositories -
however useful - belong on the [awesomelist](../awesomelist/), which exists
precisely to curate external resources without implying OWASP maintains them.
If you're looking for a tool someone else built, start there.

**Sequencing:** general tooling work here follows the readiness guides, not the
other way round - the Quantum Readiness Assessment is what makes the Top 10
operational, and it comes first. This directory exists right now to hold that
assessment and directly-contributed proofs of concept, not as a general tool
marketplace.

Presence in the index below states a tool's own declared maturity (proof of
concept, usable, maintained) - it is not an OWASP quality certification, and
readers should treat it as such regardless of maturity label.

## Index

| Tool | Status | Maps to | Maintainer |
|---|---|---|---|
| Quantum Readiness Assessment | proposed - markdown-first self-assessment operationalising the migration-surface entries | QS01-QS07 | *open* |
| QIR/LLVM circuit mapper | incoming - proof of concept to be contributed directly into this repository, analysing LLVM bitcode / `.ll` files and mapping quantum gates graphically, built on the QIR Alliance work | QS09 | Gabriel Ambroise (community) |

## Contributing a tool

Contributing here means contributing the tool's code into this repository (or
formally adopting an existing one into it) - not adding a link to somewhere
else it lives. For a link, use the awesomelist instead.

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
