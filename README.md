<div align="center">

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="assets/logo-white.png">
  <img src="assets/logo.png" alt="OWASP Quantum Security Project logo" width="160">
</picture>

# OWASP Quantum Security Project

**Prepare Today. Protect tomorrow.**

</div>

An official OWASP community project mapping the security risks of the quantum
era in a practical, vendor-neutral, and risk-based way - the antidote to hype
and fear.

> **Status:** Entry development. The OWASP Top 10 for Quantum Security Risks is in draft and open for community input, with leads assigned for each entry.

---

## About the project

NIST, the UK NCSC, and EU institutions have all issued concrete guidance on
post-quantum cryptography, crypto-agility, and harvest-now-decrypt-later
mitigation - while quantum systems themselves move from research into cloud and
enterprise environments. Yet security teams still face three gaps:

1. **No concise, prioritised view** of quantum security risks.
2. **No widely adopted threat model** for quantum platforms.
3. **Organisational readiness and platform security are treated separately.**

This project closes those gaps with work that is:

- **Practical and immediate** - helping defenders act on quantum risk today, not in a decade.
- **Systematic and risk-based** - mapping the landscape in a structured, prioritised way.
- **Actionable and grounded** - tied to recognised guidance from NIST, NCSC, and the EU.
- **One community** - connecting research, industry, and practitioners in a shared effort.

### Two tracks

| Track | Focus | Key deliverables |
|-------|-------|------------------|
| **Track 1** (primary, year one) | Quantum security risks & readiness | OWASP Top 10 for Quantum Security Risks, mitigation guidance, and a Quantum Readiness Assessment Assistant |
| **Track 2** (parallel) | Quantum platform threat modeling | Threat models, reference architectures, attack-surface mapping, and secure-design guidance for quantum platforms |

---

## Project leads

- **John Sotiropoulos**
- **Roy Barkay**

The project runs expert-backed and community-driven, with open and transparent
peer review and project-lead sign-off. Additional working-group and entry leads
are being onboarded as the project matures.

See the [project charter](charter.md) for the mission, scope, governance, and
ways of working.

---

## The OWASP Top 10 for Quantum Security Risks

Draft v0.3 - use branch `2026-draft03`.

Each entry lives under [`quantum-top-10/`](quantum-top-10/).

The route from the draft to a community-validated v1 is laid out in the [sprint plan and project timeline](plans/sprint-plan-quantum-top10-2026.md).

QS01-QS07 address the **migration surface** - risks to organisations whose existing classical cryptography must be replaced with post-quantum equivalents. QS08-QS10 address the **platform surface** - risks to organisations running workloads on quantum computing platforms.

| ID | Name | Entry Leads | Key Risk Area |
|----|------|-------------| ------------- | 
| [QS01](quantum-top-10/QS01_Harvest-Now-Decrypt-Later-Exposure.md) | Harvest-Now-Decrypt-Later Exposure | [Scott Kennedy](https://github.com/usinfosec), [Andrea Fukushima](https://github.com/38rea)  | A CRQC could decrypt sensitive data. Data is at risk of being stolen now in advance of a CRQC being created. |
| [QS02](quantum-top-10/QS02_Vulnerable-Signatures-and-Code-Signing.md) | Vulnerable Signatures and Code-Signing | [Aysha Nahan](https://github.com/ZCodeBrew) | A CRQC could fabricate signatures for transactions/code. |
| [QS03](quantum-top-10/QS03_Insecure-Migration-and-Hybrid-Misuse.md) |Insecure Migration and Hybrid Misuse | [Michael Howard](https://github.com/x509cert), [Aysha Nahan](https://github.com/ZCodeBrew), [Andy Smith](https://github.com/rot169) | Bad PQC designs & implementations could create new attack vectors, undermining the security supposedly provided by cryptography. |
| [QS04](quantum-top-10/QS04_Incomplete-Cryptographic-Discovery-and-Inventory.md) | Incomplete Cryptographic Discovery and Inventory | [Ronald Robertson](https://github.com/Treyrob3), [Anjali Gugle](https://github.com/aige-git), [Michael Howard](https://github.com/x509cert) | The absence of a crypto inventory makes migration difficult, and some cryptoassets may be forgotten about. |
| [QS05](quantum-top-10/QS05_Software-Crypto-Agility-Failures.md) | Software Crypto-Agility Failures | [Michael Howard](https://github.com/x509cert), [Anjali Gugle](https://github.com/aige-git), [Goutama Bachtiar](https://github.com/goubach) | Some aspects of crypto software are difficult to change, making them more difficult to migtrate. |
| [QS06](quantum-top-10/QS06_Hardware-Crypto-Agility-Failures.md) | Hardware Crypto-Agility Failures | [Mayur Agnihotri](https://github.com/Mayur021) | Some hardware assets are difficult to change, making them more difficult to migtrate. |
| [QS07](quantum-top-10/QS07_Misdirected-Quantum-Countermeasures.md) | Misdirected Quantum Countermeasures | [Rhea Anthony](https://github.com/RayAnn1606) | Some vendors sell products claiming to help with PQC, but may not be approprite for all situations, giving a false sense of security or wasting efforts. |
| [QS08](quantum-top-10/QS08_Quantum-Software-Supply-Chain-and-Toolchain-Compromise.md) | Quantum Software Supply Chain and Toolchain Compromise | [Rhea Anthony](https://github.com/RayAnn1606), [Aamiruddin Syed](https://github.com/syedDS) | Tools  & services used to create quantum programs could be compromised/backdoored. |
| [QS09](quantum-top-10/QS09_Quantum-Platform-Isolation-and-Control-Plane-Exposure.md) | Quantum Platform Isolation and Control Plane Exposure | [Mayur Agnihotri](https://github.com/Mayur021) | Shared quantum computers may not provide sufficient segregation between customers code/executions. |
| [QS10](quantum-top-10\QS10_Unverifiable-Quantum-Execution-and-Result-Assurance.md) | Unverifiable Quantum Execution and Result Assurance | [Mayur Agnihotri](https://github.com/Mayur021), [Anjali Gugle](https://github.com/aige-git) | Results provided by a QC might be tampered with. |


Each entry follows a common [template](quantum-top-10/_template.md): description, common examples, prevention, example attack scenarios, references, and a standards-and-regulatory mapping.

---

## Community calls

The project runs a biweekly community call (Mondays 17:30-18:30 London, starting
3 August 2026 - see [Community and contact](#community-and-contact) for the Zoom
and calendar details), with additional calls on demand as the work picks up. Decks
for every call are published in [`calls/`](calls/), starting with the project
kick-off deck; decks for subsequent calls are added there as they happen.

---

## How to contribute

Feedback and contributions are welcome - this is a community project and the
Top 10 is explicitly a draft opened for discussion.

**1. Clone the repository**

```bash
git clone https://github.com/OWASP/quantum-security-project.git
cd quantum-security-project
```

**2. Propose a change with a pull request**

```bash
git checkout -b my-contribution
# edit or add entries under quantum-top-10/ (start from _template.md for new risks)
git commit -am "Describe your change"
git push -u origin my-contribution
```

Then open a pull request against `main`. Please ground new or revised content in
published standards, regulation, or peer-reviewed research, and keep the project
vendor-neutral.

**3. Or share feedback via GitHub Issues**

Prefer to discuss before writing a change? [Open an issue](https://github.com/OWASP/quantum-security-project/issues)
to suggest a candidate risk, flag an error, question the selection or ordering,
or start a conversation. Issues are the best place for feedback that isn't yet a
concrete edit.

**Quick submission forms**

During the bootstrap phase you can also contribute via these forms without using GitHub:

- [Top 10 for Quantum Security - candidate risks & feedback](https://forms.gle/8NbEEX6mmiKdUXxXA)
- [Work Areas & Proposals - propose a topic or working area](https://forms.gle/n7BicJJpQJFQ8eRz7)

---

## Community and contact

- **Homepage:** [quantum-owasp.org](https://quantum-owasp.org)
- **GitHub:** [github.com/OWASP/quantum-security-project](https://github.com/OWASP/quantum-security-project)
- **OWASP Slack:** `#project-quantum-security` - [join the OWASP Slack](https://owasp.org/slack/invite)
- **Biweekly community call:** Mondays 17:30-18:30 (London / Europe/London), every two weeks starting **3 August 2026**, on Zoom - [join](https://deepcyberx.zoom.us/j/87321791672?pwd=pDq33o7cwIca0h32amjpOb4vQkSQFk.1) (Meeting ID `873 2179 1672`, passcode `701551`). Add to your calendar: [ICS invite](calls/OWASPQuantumBiweeklyCall.ics).
- **Email:** contribute@quantum-owasp.org

The project follows OWASP's standard documentation and contribution practices,
including the OWASP Code of Conduct and vendor neutrality.

---

## License

Content is released under the **Creative Commons Attribution-ShareAlike 4.0**
(CC BY-SA 4.0) license.
