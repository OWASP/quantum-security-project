## QS03:2026 - Vulnerable Signatures and Code-Signing

**Description:**

Public-key signatures underpin code signing, supply-chain integrity, document validity, identity certificates, transactions, and long-term non-repudiation. RSA, DSA, ECDSA, and EdDSA are all broken by Shor's algorithm, so any system that verifies these signatures to establish trust - software updates, container images, firmware, package managers, TLS certificate hierarchies, SBOM attestations, signed documents, blockchain transactions - is at risk once a CRQC exists. Unlike confidentiality breaches, signature forgery enables active attacks: malicious updates, fake identities, fraudulent transactions, supply-chain compromise. The post-quantum signature standards (ML-DSA / FIPS 204 and SLH-DSA / FIPS 205) have larger keys and signatures and different operational profiles, with non-trivial impact on hardware roots of trust, constrained devices, and certificate ecosystems.

The same Mosca's-inequality logic that governs confidentiality exposure (QS01) applies to signature trust, with the inequality read against integrity rather than secrecy: if the time to migrate (X) plus the period a signature or credential must remain trustworthy (Y) exceeds the time until a CRQC exists (Z), that signature is already exposed. Contracts, regulatory filings, evidentiary records, signed software releases, and long-lived identity or credential issuance - government identity, professional credentials, root certificates - routinely carry validity periods long enough to fail this test today, independent of when a CRQC actually arrives.

This scope also includes re-establishment failure: a migration can complete correctly on every cryptographic measure - the new signature verifies, the new certificate chains, the algorithm is compliant - while the process that issued the new credential accepted the wrong evidence to do so. An enrolment flow that takes proof-of-possession from a key this entry already classifies as forgeable, and issues a strong new credential on the strength of it, produces two certificates that both verify and a record indistinguishable from a correct migration. The risk is not that the signature fails; it is that the re-issuance accepted a compromised anchor as sufficient authority to mint its replacement. Prevention item 7 below is the mitigation for this failure mode specifically.

**Common Examples of Vulnerability:**

1. Certification authority hierarchies (root, intermediate, issuing CA) signing with RSA-2048 or ECDSA P-256 over multi-year validity periods.
2. Code-signing keys: OS update signing, firmware signing, container image signing (Sigstore, cosign, Notary), package signing, mobile app signing, CI/CD signing.
3. Other long-lived signature trust anchors: UEFI Secure Boot keys, TPM endorsement keys, JWT/SAML issuer keys, document-signing certificates, blockchain wallet keys.
4. Large verifier populations - devices, services, or artefacts - that trust a classical key and cannot easily be upgraded.
5. Long-lived signed artefacts whose validity must persist for years - contracts, regulatory filings, evidentiary records, signed software releases, blockchain transactions - where confidentiality-style Mosca's-inequality reasoning applied to integrity shows the artefact is already exposed.
6. Identity and credential records with multi-year lifetimes - government identity issuance, professional credentials, root certificates - signed with a classical algorithm and expected to remain trustworthy well past any reasonable migration window.

**How to Prevent:**

1. Migrate code-signing and CA hierarchies before broader estate migration - they have the longest blast radius and lead time.
2. Adopt hybrid signing during transition: produce both a classical and a PQC signature on the same artefact so non-PQC verifiers keep working while PQC-aware verifiers gain forward security.
3. Use ML-DSA (FIPS 204) for general digital signatures; use SLH-DSA (FIPS 205) for very long-lived, high-assurance signatures where stateless hash-based security is preferred.
4. Plan for shorter certificate lifetimes during transition (e.g. the CA/Browser Forum 47-day TLS maximum effective 2029) to reduce the exposure window.
5. Engage PKI, code-signing, and certificate-authority vendors on PQC roadmaps - failure here is a supply-chain blocker.
6. For firmware and software signing specifically, CNSA 2.0 lists LMS and XMSS (NIST SP 800-208) as the approved stateful hash-based schemes, and does not approve SLH-DSA for NSS use (SLH-DSA remains valid per FIPS 205 outside that scope - item 3 above still applies generally). This approval covers single-tree LMS and XMSS only - HSS and XMSS^MT, the multi-tree variants, are not approved for NSS use, even though they are the more common library default for high signature counts. ML-DSA is also CNSA 2.0-approved for this use case and may suit setups needing more signatures than a single LMS/XMSS key can produce, or distributed signing. LMS and XMSS are stateful: each key can produce only a fixed number of signatures, and reusing a state index breaks the security guarantee. State management should be handled in hardware such as an HSM, and any flow that transfers or duplicates key material - backup, restore, failover - must guarantee state is never reused.
7. For long-lived signed artefacts already failing the Mosca's-inequality test - contracts, filings, releases, and other fixed-content documents - plan re-signing or counter-signing with PQC schemes (ML-DSA, SLH-DSA) before the classical signature scheme is deprecated, rather than treating the artefact as settled once issued. Re-signing is sufficient here because the artefact's content is unchanged; the new signature only re-attests something that was already true.
8. For long-lived credentials failing the same test, do not treat re-signing as equivalent to remediation. A credential asserts that a named subject controls a key, and a PQC signature over the same claim only re-asserts it with stronger cryptography - it does not re-establish the claim itself. Require re-issuance to rest on evidence independent of the credential being replaced (fresh identity proofing, a still-trusted anchor, or hardware attestation), not on the outgoing key vouching for its own successor.

**Example Attack Scenarios:**

Scenario #1: An attacker with a future CRQC recovers the private key of a code-signing certificate still on ECDSA P-256. They sign malware that passes verification on every device trusting that anchor, distributing a malicious "update" through the legitimate update channel.

Scenario #2: An organisation migrates its leaf TLS certificates to PQC but leaves the root and intermediate CAs on RSA. An attacker forges an intermediate CA signature with a CRQC and issues trusted certificates for arbitrary domains - the chain is only as strong as its weakest classical link.

Scenario #3: A vendor issues software releases signed with ECDSA, with signatures expected to remain valid for the product's decade-long support lifetime. Once a CRQC exists, an attacker recovers the signing key and forges signatures on malicious updates that still validate against the long-lived, un-rotated trust anchor - the artefact was exposed from the day it was signed, under the same Mosca's-inequality logic that governs confidentiality.

**Reference Links:**

<!-- References verified 2026-08-05 against authoritative canonical sources. This entry absorbs the integrity content (long-lived signed artefacts, identity and credential records, Scenario #3) formerly in QS02, per the QS01/QS02 merge discussed in #11; QS02's confidentiality content merges into QS01. -->

1. [NIST FIPS 204 (ML-DSA)](https://csrc.nist.gov/pubs/fips/204/final): Module-lattice digital signature standard.
2. [NIST FIPS 205 (SLH-DSA)](https://csrc.nist.gov/pubs/fips/205/final): Stateless hash-based signature standard for high-assurance, long-lived use.
3. [NSA Commercial National Security Algorithm Suite 2.0 (CNSA 2.0)](https://www.nsa.gov/Press-Room/News-Highlights/Article/Article/3148990/nsa-releases-future-quantum-resistant-qr-algorithm-requirements-for-national-se/): Software and firmware signing exclusively CNSA 2.0 by 2030.
4. [IETF LAMPS Working Group](https://datatracker.ietf.org/wg/lamps/about/): PQC X.509 and CMS extensions.
5. [EU Cyber Resilience Act, Annex I](https://eur-lex.europa.eu/eli/reg/2024/2847/oj/eng): State-of-the-art integrity and authenticity requirements.
6. [NIST SP 800-208](https://csrc.nist.gov/pubs/sp/800/208/final): Recommendation for Stateful Hash-Based Signature Schemes (LMS, XMSS).
7. [NSA - The Commercial National Security Algorithm Suite 2.0 and Quantum Computing FAQ, v2.1](https://media.defense.gov/2022/Sep/07/2003071836/-1/-1/0/CSI_CNSA_2.0_FAQ_.PDF): PP-24-4014, December 2024. Algorithm-allowance table: LMS/XMSS approved for firmware and software signing; SLH-DSA not approved for NSS use.
8. [NIST IR 8547 (Draft) - Transition to Post-Quantum Cryptography Standards](https://csrc.nist.gov/pubs/ir/8547/ipd): Transition planning guidance referencing Mosca's inequality, applied here to signature and credential lifetime.

**Standards and Regulatory Mapping:**

> **TODO:** This section is carried over from the source document and is not part of `_template.md`. Confirm whether to keep it in the final entry format, and verify each standard/citation.

NIST FIPS 204 (ML-DSA), FIPS 205 (SLH-DSA). NIST IR 8547 (Draft) on transition planning and Mosca's inequality. NSA CNSA 2.0: software and firmware signing exclusively CNSA 2.0 by 2030. NCSC recommends ML-DSA-65 for most use cases. EU CRA Annex I requires state-of-the-art mechanisms for integrity and authenticity, applicable through the product support period for long-lived signed artefacts. IETF LAMPS working group on PQC X.509 and CMS extensions. DORA Articles 28-44 on third-party risk management apply to PKI and signing service vendors.

