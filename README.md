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

> **Status:** Bootstrap phase. The OWASP Top 10 for Quantum Security Risks is at
> draft **v0.1** and open for community input. It is our starting point, not the
> finished list.

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

Draft v0.1. Each entry lives under [`quantum-top-10/`](quantum-top-10/).

The route from the v0.1 draft to a community-validated v1 is laid out in the
[sprint plan and project timeline](plans/sprint-plan-quantum-top10-2026.md).

QS01-QS07 address the **migration surface** - risks to organisations whose
existing classical cryptography must be replaced with post-quantum equivalents.
QS08-QS10 address the **platform surface** - risks to organisations running
workloads on quantum computing platforms.

| ID | Risk | Primary anchor |
|----|------|----------------|
| [QS01](quantum-top-10/QS01_Harvest-Now-Decrypt-Later-Exposure.md) | Harvest-Now-Decrypt-Later Exposure | EU Roadmap end-2030; NCSC 2031; NSM-10 |
| [QS02](quantum-top-10/QS02_Long-Lived-Sensitive-Data.md) | Long-Lived Sensitive Data | Mosca's inequality; EU Roadmap end-2030 |
| [QS03](quantum-top-10/QS03_Vulnerable-Signatures-and-Code-Signing.md) | Vulnerable Signatures and Code-Signing | NSA CNSA 2.0 by 2030; CRA Annex I |
| [QS04](quantum-top-10/QS04_Absent-Cryptographic-Inventory-and-CBOM.md) | Absent Cryptographic Inventory and CBOM | NCSC 2028; EU Roadmap end-2026 |
| [QS05](quantum-top-10/QS05_Crypto-Agility-Failures.md) | Crypto-Agility Failures | NIS2 Article 21(2)(h); IETF PQUIP/TLS WG |
| [QS06](quantum-top-10/QS06_Insecure-Migration-and-Hybrid-Misuse.md) | Insecure Migration and Hybrid Misuse | EU Roadmap end-2030 standalone-classical prohibition |
| [QS07](quantum-top-10/QS07_Hardware-Roots-of-Trust.md) | Hardware Roots of Trust | NCSC 2028 milestone; CRA Annex IV |
| [QS08](quantum-top-10/QS08_QPU-Tenant-Isolation-Failures.md) | QPU Tenant Isolation Failures | Li et al. NDSS 2025; Xu et al. CCS 2023 |
| [QS09](quantum-top-10/QS09_Toolchain-and-Compiler-Compromise.md) | Toolchain and Compiler Compromise | Suresh et al. HASP 2021; Chu et al. ICASSP 2023 |
| [QS10](quantum-top-10/QS10_Side-Channel-and-Control-Plane-Exposure.md) | Side-Channel and Control-Plane Exposure | Mi et al. CCS 2022; Xu et al. CCS 2023 |

Each entry follows a common [template](quantum-top-10/_template.md): description,
common examples, prevention, example attack scenarios, references, and a
standards-and-regulatory mapping.

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
