## QS07:2026 - Hardware Roots of Trust

**Description:**

Hardware roots of trust are cryptographic anchors burned into silicon, firmware, or sealed devices: TPM endorsement keys, UEFI Secure Boot platform keys, smart-card root certificates, HSM device identities, embedded SSL certificates in IoT devices, signed firmware in vehicles and industrial controllers. They are designed to be tamper-resistant and difficult to update - the opposite of what PQC migration requires - and they have the longest replacement cycle of any cryptographic asset class. The ML-DSA and SLH-DSA schemes have substantially larger keys and signatures than RSA or ECDSA, with material implications for HSMs, smart cards, and constrained devices that may lack capacity for the new primitives. Devices in service today will likely outlive any practical PQC deadline: operational technology and industrial control systems often run for 15-20 years, so hardware shipped in 2026 will still be in service in 2041.

**Common Examples of Vulnerability:**

1. Hardware roots of trust with no in-place upgrade path: TPMs, UEFI keys, smart cards, HSMs, embedded device certificates, signed firmware, automotive and industrial control system certificates.
2. Devices whose expected replacement cycle crosses the 2030 or 2035 PQC deadline applicable to their sector but which cannot be re-anchored.
3. HSMs and smart cards without capacity to hold ML-DSA keys (typically 2-4 KB) or produce ML-DSA signatures (2.4-4.5 KB).
4. IoT and OT devices - industrial controllers, smart meters, connected vehicles - treated as out of scope despite hardware-anchored cryptography.

**How to Prevent:**

1. Begin procurement transition now: new hardware should support PQC algorithms or be crypto-agile by design.
2. For existing devices with upgrade paths, plan and test firmware updates that introduce PQC trust anchors before existing trust is compromised.
3. For devices without upgrade paths, plan retirement or compensating-control wraps (network segmentation, restricted-lifetime certificates above the hardware anchor, additional cryptographic layers).
4. Engage TPM, HSM, smart-card, and embedded-device suppliers on PQC roadmaps during procurement.
5. Track Trusted Computing Group, UEFI Forum, and 3GPP work on PQC adaptations of hardware-anchored standards.

**Example Attack Scenarios:**

Scenario #1: A fleet of industrial controllers with a 20-year service life ships in 2026 with ECDSA-based signed-firmware verification and no field-update path for the trust anchor. After a CRQC exists, an attacker forges firmware signatures that the controllers accept, and there is no mechanism to rotate the anchor short of physical replacement.

Scenario #2: An organisation attempts to migrate smart cards to ML-DSA but discovers the deployed cards lack the memory and compute to hold the larger keys and produce the larger signatures. The cards cannot be upgraded in place, so the classical root remains trusted until the entire card base is physically reissued.

**Reference Links:**

<!-- References verified 2026-07-13 against authoritative canonical sources. -->

1. [UK NCSC - Timelines for migration to post-quantum cryptography](https://www.ncsc.gov.uk/guidance/pqc-migration-timelines): Identifies TPM, X.509 PKI, UEFI Secure Boot, and 6G as needing new PQC standards by 2028.
2. [NSA Commercial National Security Algorithm Suite 2.0 (CNSA 2.0)](https://www.nsa.gov/Press-Room/News-Highlights/Article/Article/3148990/nsa-releases-future-quantum-resistant-qr-algorithm-requirements-for-national-se/): Niche equipment and constrained devices CNSA 2.0 by 2033.
3. [Trusted Computing Group](https://trustedcomputinggroup.org/): Work on PQC TPM specifications.
4. [EU Cyber Resilience Act, Annex IV](https://eur-lex.europa.eu/eli/reg/2024/2847/oj/eng): Critical product categories including smart-card and smart-meter gateways.
5. [IETF LAMPS Working Group](https://datatracker.ietf.org/wg/lamps/about/): PQC X.509 profiles.

**Standards and Regulatory Mapping:**

> **TODO:** This section is carried over from the source document and is not part of `_template.md`. Confirm whether to keep it in the final entry format, and verify each standard/citation.

NCSC Timelines for migration explicitly identifies hardware roots of trust. NSA CNSA 2.0: niche equipment and constrained devices CNSA 2.0 by 2033. IETF LAMPS for X.509 PQC. Trusted Computing Group work on PQC TPM specifications. UEFI Forum Secure Boot evolution. EU CRA Annex IV critical product categories (smart meter gateways, smart cards). 3GPP work on PQC for 6G and post-5G cellular.

