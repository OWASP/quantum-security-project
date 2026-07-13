## QS03:2026 - Vulnerable Signatures and Code-Signing

**Description:**

Public-key signatures underpin code signing, supply-chain integrity, document validity, identity certificates, transactions, and long-term non-repudiation. RSA, DSA, ECDSA, and EdDSA are all broken by Shor's algorithm, so any system that verifies these signatures to establish trust - software updates, container images, firmware, package managers, TLS certificate hierarchies, SBOM attestations, signed documents, blockchain transactions - is at risk once a CRQC exists. Unlike confidentiality breaches, signature forgery enables active attacks: malicious updates, fake identities, fraudulent transactions, supply-chain compromise. The post-quantum signature standards (ML-DSA / FIPS 204 and SLH-DSA / FIPS 205) have larger keys and signatures and different operational profiles, with non-trivial impact on hardware roots of trust, constrained devices, and certificate ecosystems.

**Common Examples of Vulnerability:**

1. Certification authority hierarchies (root, intermediate, issuing CA) signing with RSA-2048 or ECDSA P-256 over multi-year validity periods.
2. Code-signing keys: OS update signing, firmware signing, container image signing (Sigstore, cosign, Notary), package signing, mobile app signing, CI/CD signing.
3. Other long-lived signature trust anchors: UEFI Secure Boot keys, TPM endorsement keys, JWT/SAML issuer keys, document-signing certificates, blockchain wallet keys.
4. Large verifier populations - devices, services, or artefacts - that trust a classical key and cannot easily be upgraded.

**How to Prevent:**

1. Migrate code-signing and CA hierarchies before broader estate migration - they have the longest blast radius and lead time.
2. Adopt hybrid signing during transition: produce both a classical and a PQC signature on the same artefact so non-PQC verifiers keep working while PQC-aware verifiers gain forward security.
3. Use ML-DSA (FIPS 204) for general digital signatures; use SLH-DSA (FIPS 205) for very long-lived, high-assurance signatures where stateless hash-based security is preferred.
4. Plan for shorter certificate lifetimes during transition (e.g. the CA/Browser Forum 47-day TLS maximum effective 2029) to reduce the exposure window.
5. Engage PKI, code-signing, and certificate-authority vendors on PQC roadmaps - failure here is a supply-chain blocker.

**Example Attack Scenarios:**

Scenario #1: An attacker with a future CRQC recovers the private key of a code-signing certificate still on ECDSA P-256. They sign malware that passes verification on every device trusting that anchor, distributing a malicious "update" through the legitimate update channel.

Scenario #2: An organisation migrates its leaf TLS certificates to PQC but leaves the root and intermediate CAs on RSA. An attacker forges an intermediate CA signature with a CRQC and issues trusted certificates for arbitrary domains - the chain is only as strong as its weakest classical link.

**Reference Links:**

<!-- REVIEW: reference URLs are best-effort canonical pages and require human verification against the source doc. -->

1. [NIST FIPS 204 (ML-DSA)](https://csrc.nist.gov/pubs/fips/204/final): Module-lattice digital signature standard.
2. [NIST FIPS 205 (SLH-DSA)](https://csrc.nist.gov/pubs/fips/205/final): Stateless hash-based signature standard for high-assurance, long-lived use.
3. [NSA Commercial National Security Algorithm Suite 2.0 (CNSA 2.0)](https://www.nsa.gov/): Software and firmware signing exclusively CNSA 2.0 by 2030.
4. [IETF LAMPS Working Group](https://datatracker.ietf.org/wg/lamps/about/): PQC X.509 and CMS extensions.
5. [EU Cyber Resilience Act, Annex I](https://eur-lex.europa.eu/eli/reg/2024/2847/oj): State-of-the-art integrity and authenticity requirements.

<!-- TODO: The source doc includes a "Standards and regulatory mapping" section not represented in _template.md. Preserved below — decide whether to extend the template to carry it.

Standards and regulatory mapping: NIST FIPS 204 (ML-DSA), FIPS 205 (SLH-DSA). NSA CNSA 2.0: software and firmware signing exclusively CNSA 2.0 by 2030. NCSC recommends ML-DSA-65 for most use cases. EU CRA Annex I requires state-of-the-art mechanisms for integrity and authenticity. IETF LAMPS working group on PQC X.509 and CMS extensions. DORA Articles 28-44 on third-party risk management apply to PKI and signing service vendors.
-->

