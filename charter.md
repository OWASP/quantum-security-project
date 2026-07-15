OWASP Quantum Security Project Working Group Charter

I. Purpose
The OWASP Quantum Security Project Working Group is dedicated to developing structured, vendor-neutral guidance for the security risks introduced by advancing quantum computing capability. Our initial focus is an OWASP Top 10 for Quantum Security Risks, supported by threat modeling and practical mitigation guidance for quantum computing platforms and hybrid quantum-classical architectures. This initiative aligns with the broader goals of the OWASP Foundation to foster a more secure cyberspace and is in line with the overarching intention behind all OWASP Top 10 lists.
II. Target Audience
The primary audience of our work is security architects, application and platform developers, cryptography engineers, and organisational risk owners who must prepare for quantum-capable adversaries today and for the deployment of quantum computing platforms tomorrow. While the issues we address may be of interest to other stakeholders in the quantum ecosystem — such as researchers, regulators, compliance officers, procurement teams, and end users — our core focus is to provide actionable, practical, and concise security guidance to these technical and risk-management teams.

In acknowledging the inclusive and ever-evolving landscape of practitioners, we extend our focus to Citizen Developers and platform operators as well. These individuals may be new to cryptographic transition planning or to quantum platform design, but they are handling critical data, services, and infrastructure for their organisations and need to be included in our efforts to make systems exposed to quantum risk secure and safe.
III. Goals
The goal of this Working Group is to provide a foundation for organisations to assess and remediate quantum security risk, and for builders to design and operate quantum platforms that can be used securely and safely by a wide range of entities, from individuals and companies to governments and other organisations.

To meet this goal, the project will deliver work across two complementary tracks:

Quantum Security Risks and Readiness — Primary Year 1 focus. Delivers the OWASP Top 10 for Quantum Security Risks, mitigation guidance aligned with NIST, UK NCSC, and EU recommendations, and an open-source Quantum Readiness Assessment Assistant so the Top 10 becomes operational rather than purely descriptive.
Quantum Platform Threat Modeling — Parallel but secondary in Year 1. Defines threat models, reference architectures, attack surface maps, and secure design guidance for quantum computing platforms and hybrid quantum-classical architectures.
IV. Scope of Risks and Vulnerabilities
Given the nature of quantum-era exposure, the risks we document may be broader than those seen in previous Top 10 lists. They span both present-day data and cryptographic exposure (for example harvest-now-decrypt-later, long-lived sensitive data, crypto-agility failures, algorithm and key lifecycle dependency, digital signature and symmetric cryptography considerations) and emerging platform-level concerns (trust boundaries and control planes in quantum and hybrid architectures, attack surfaces of quantum platforms, and the architectural impact of quantum capability on AI pipelines and autonomous systems).

Our focus is on providing an actionable framework to assess issues that have a clear bearing on security and safety. While it is important to acknowledge and understand the inherent uncertainties of an emerging field, our purpose is to equip practitioners with the knowledge and tools to understand the security considerations around quantum readiness and quantum platform design, and thus ensure the secure and robust evolution of systems exposed to quantum risk.
V. Relation to Other OWASP Top 10 Lists, Standards, and Vulnerabilities
In documenting risks, we will explore quantum-era exposures that resemble vulnerability types on other OWASP Top 10 lists — for example, cryptographic failures, insecure design, and supply-chain risk. When we do, we will expand on the specific implications of these issues for organisations preparing for quantum-capable adversaries and for systems built on quantum platforms — rather than rehash the generic.

We aim to bridge the gap between general application and infrastructure security principles and the specific challenges posed by quantum computing. This means exploring how conventional vulnerabilities may pose different risks under a quantum threat model, how they might be exploited in novel ways, and how traditional remediation strategies — including key management, cryptographic agility, and secure SDLC — need to be adapted for the post-quantum era and for quantum platform deployments.

This project complements all other OWASP projects and anchors quantum readiness in practical risk prioritisation, and introduces structured threat modeling before insecure patterns become widespread. It remains documentation-focused and vendor-neutral, with emphasis on clarity, practicality, and alignment with recognised guidance from NIST, UK NCSC, and EU institutions rather than speculative research.

In doing so, we aim to provide clear, actionable, and comprehensive guidance to organisations and platform builders facing quantum-era risk. This will not only help them understand and mitigate these vulnerabilities but also aid them in proactively preventing them.

The nature of quantum-era exposure demands a specific, comprehensive exploration of both organisational readiness and quantum platform security. The OWASP Quantum Security Project Working Group is committed to illuminating these issues and providing practical solutions for the community.

Through this charter, we aim to clarify our intentions and methods and invite those who share our goals to join us in making the use of cryptography, applications, and platforms in the quantum era safer and more secure for all.



Project Category: Documentation Project Project Classification: Builder · Defender · Breaker License: Creative Commons Attribution-ShareAlike 4.0
Project Leads: John Sotiropoulos,  Roy Barkay

