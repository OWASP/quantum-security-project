# Contributing

Feedback and contributions are welcome. The OWASP Top 10 for Quantum Security
Risks is at draft v0.1 and is explicitly opened for discussion - the v0.1 entries
hold no incumbency and may be revised, merged, or dropped.

The route from v0.1 to a community-validated v1 is set out in the
[sprint plan](plans/sprint-plan-quantum-top10-2026.md). Contributors do not need
to be entry leads or working group members to submit.

## Where your contribution goes

| You want to | Do this |
|---|---|
| Propose a new risk entry | Pull request adding a file to [`proposals/`](proposals/) |
| Give structured feedback on an existing entry | Pull request against that entry in [`quantum-top-10/`](quantum-top-10/) |
| Raise a question, flag an error, or discuss scope and ordering | Open an [issue](https://github.com/OWASP/quantum-security-project/issues) |
| Contribute without using GitHub | Use the [candidate risks and feedback form](https://forms.gle/8NbEEX6mmiKdUXxXA) or the [work areas and proposals form](https://forms.gle/n7BicJJpQJFQ8eRz7) |

Issues are the right place for feedback that is not yet a concrete edit. A pull
request is the right place for feedback that is.

## Making a change

```bash
git clone https://github.com/OWASP/quantum-security-project.git
cd quantum-security-project
git checkout -b my-contribution
# edit or add entries; start from quantum-top-10/_template.md for new risks
git commit -am "Describe your change"
git push -u origin my-contribution
```

Then open a pull request against `main`. If you do not have write access, fork
the repository first and open the pull request from your fork.

Please keep a pull request to one entry or one concern where you can. It makes
review tractable and keeps discussion of a disputed scope boundary separate from
discussion of a citation fix.

## What contributions need to satisfy

- **Grounded in evidence.** Ground new or revised content in published
  standards, regulation, or peer-reviewed research. See the
  [evidence and anchor convention](quantum-top-10/_evidence-convention.md) for
  the tags and the anchor hierarchy, and cite primary sources rather than
  secondary summaries.
- **Vendor-neutral.** Naming a product is fine where it is the evidence - a
  library's default configuration, a documented vulnerability - but the project
  does not endorse or recommend vendors.
- **Practical.** The success criteria for v1 are that it is useful to defenders
  today, actionable, and linked to existing guidance. Mitigations should say
  what to do, not that a problem should be addressed.

Contributions on the **platform surface** (QS08-QS10: QPU tenant isolation,
toolchain and compiler security, side-channel and control-plane exposure) are
particularly sought, since the practitioner community there is smaller.

## Community

- **OWASP Slack:** `#project-quantum-security` - [join the OWASP Slack](https://owasp.org/slack/invite)
- **Biweekly community call:** Mondays 17:30-18:30 London, every two weeks from
  3 August 2026. Zoom link, meeting ID, and an ICS invite are in the
  [README](README.md#community-and-contact); decks are published in [`calls/`](calls/).
- **Email:** contribute@quantum-owasp.org

The project follows the OWASP Code of Conduct and OWASP's vendor-neutrality
requirements.

## License

Contributions are made under the
[Creative Commons Attribution-ShareAlike 4.0](LICENSE) license (CC BY-SA 4.0).
By opening a pull request you agree that your contribution is licensed on those
terms.
