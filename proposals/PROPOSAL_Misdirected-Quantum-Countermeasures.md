## Proposal: Misdirected Quantum Countermeasures

**Evidence:** Demonstrated for the underlying guidance - the UK NCSC and the US NSA
have both published explicit positions against substituting QKD for post-quantum
cryptography. Emerging for the procurement failure mode itself: national technical
authorities considered the substitution likely enough to warrant formal positions,
but this proposal makes no claim about how often it occurs in practice.

**Description:**

Every other entry in this list describes an organisation that has not yet migrated.
This one describes an organisation that believes it has. The quantum transition has
produced a market of products marketed as "quantum-safe", "quantum-proof", or
"quantum-resistant", and the terms carry no defined meaning. An organisation can
spend budget and calendar on a quantum-branded purchase that replaces none of its
quantum-vulnerable algorithms, record the programme as progressing, and arrive at a
regulatory deadline with the same RSA and elliptic-curve estate it started with.
The countermeasure was real; it addressed a different problem.

Four substitutions account for most of this. **Quantum key distribution deployed in
place of PQC** is the one national authorities have addressed directly: the NCSC
states it "will not support the use of QKD for government or military applications",
and the NSA does not support QKD for National Security Systems and does not
anticipate certifying QKD products. QKD also does not provide authentication, so a
QKD link still depends on classical signatures that a CRQC breaks - the quantum
channel protects key agreement while the trust anchor beneath it remains vulnerable.
**Quantum random number generators** address entropy quality, which was never the
quantum threat; Shor's algorithm recovers a key from a public key regardless of how
well the private key was generated. **Proprietary or non-standardised "post-quantum"
algorithms** carry none of the multi-year public cryptanalysis that produced FIPS
203, 204 and 205, and the NIST process itself demonstrated why that matters: several
submissions were broken during evaluation, some by classical attacks on ordinary
hardware. **Unvalidated vendor claims** are the general case - "quantum-safe" on a
datasheet is not evidence of a standardised algorithm correctly implemented, and
the distinction is only visible in validation records.

The consequence is a reporting failure as much as a technical one. Misdirected spend
is recoverable; a migration programme that reports completion against work that
changed no algorithm is not, because the error surfaces at the deadline.

**Common Examples of Vulnerability:**

1. QKD links procured or deployed as a substitute for PQC migration, rather than as
   a complement to it, particularly where the deploying organisation falls within
   the scope of published NCSC or NSA positions.
2. QKD deployments whose endpoint authentication still rests on classical signatures,
   leaving a quantum-vulnerable trust anchor beneath a quantum-protected channel.
3. Quantum random number generation treated as a mitigation for the quantum threat
   to public-key cryptography, rather than as an entropy-quality measure.
4. Products described as "quantum-safe", "quantum-proof" or "quantum-resistant"
   accepted without identifying which standardised algorithm is implemented.
5. Proprietary or non-standardised algorithms marketed as post-quantum, where no
   public cryptanalytic record exists.
6. Migration programmes that report progress in spend or projects delivered rather
   than in quantum-vulnerable algorithms retired.

**How to Prevent:**

1. Require vendors to name the specific standardised algorithm and parameter set
   implemented - ML-KEM (FIPS 203), ML-DSA (FIPS 204), SLH-DSA (FIPS 205) - and
   treat any answer that is an adjective rather than a standard as unevidenced.
2. Require validation evidence rather than marketing claims: a CMVP or CAVP
   certificate number that can be checked independently against the NIST validation
   lists.
3. Assess any QKD proposal against the published NCSC and NSA positions before
   procurement, and require a specific answer on how endpoint authentication is
   protected, since QKD does not provide it.
4. Separate entropy from algorithm substitution in planning. A QRNG may be a
   reasonable purchase on its own merits; record it against entropy quality, never
   against PQC migration progress.
5. Measure migration in quantum-vulnerable algorithms retired and systems
   re-anchored, not in budget spent or projects delivered, so that a purchase which
   retires nothing cannot register as progress.
6. Route quantum-branded procurement through the cryptographic inventory (QS04): a
   product that does not appear against a catalogued quantum-vulnerable asset is not
   advancing the migration of that asset.

**Example Attack Scenarios:**

Scenario #1: A financial institution deploys QKD between two data centres and reports
a quantum-safe inter-site link to its board. The QKD channel protects key agreement,
but the endpoints authenticate to each other with ECDSA certificates, and the
remaining estate - customer-facing TLS, backups, code signing - is untouched. An
adversary ignores the quantum channel entirely, harvests the classical traffic
either side of it, and after a CRQC exists also forges the certificates
authenticating the QKD endpoints, defeating the link the institution paid most for.

Scenario #2: A vendor sells a "quantum-resistant" VPN appliance built on a
proprietary lattice construction that has never been published or independently
analysed. The buyer records the estate as migrated and stands down the programme.
The construction is later found weak against a classical attack, as several NIST
submissions were during evaluation - so the organisation is left with neither
post-quantum nor classical assurance, and no remaining migration budget with the
deadline approaching.

**Reference Links:**

<!-- References verified 2026-07-27 against authoritative primary sources. nsa.gov returns 403 to automated requests but resolves normally in a browser; this affects existing NSA citations in QS03 and QS07 equally. -->

1. [UK NCSC - Quantum security technologies (white paper)](https://www.ncsc.gov.uk/whitepaper/quantum-security-technologies): States that NCSC will not support QKD for government or military applications, that QKD does not provide authentication, and that PQC is the best mitigation.
2. [NSA - Quantum Key Distribution (QKD) and Quantum Cryptography (QC)](https://www.nsa.gov/Cybersecurity/Quantum-Key-Distribution-QKD-and-Quantum-Cryptography-QC/): States that NSA does not support QKD or QC for National Security Systems and does not anticipate certifying such products, with the technical limitations enumerated.
3. [NIST FIPS 203 (ML-KEM)](https://csrc.nist.gov/pubs/fips/203/final), [FIPS 204 (ML-DSA)](https://csrc.nist.gov/pubs/fips/204/final), [FIPS 205 (SLH-DSA)](https://csrc.nist.gov/pubs/fips/205/final): The standardised algorithms a "quantum-safe" claim should resolve to.
4. [NIST Cryptographic Module Validation Program (CMVP)](https://csrc.nist.gov/projects/cryptographic-module-validation-program): Independent validation records against which a vendor claim can be checked.
5. [NIST Post-Quantum Cryptography Standardization project](https://csrc.nist.gov/projects/post-quantum-cryptography): The public cryptanalytic process, including submissions broken during evaluation, that non-standardised algorithms have not undergone.

**Standards and Regulatory Mapping:**

NCSC Quantum security technologies white paper (position against QKD for government
and military use; PQC as the primary mitigation). NSA position on QKD and QC for
National Security Systems. NIST FIPS 203, 204 and 205 as the algorithms against
which a quantum-safe claim should be assessed, and CMVP/CAVP as the validation
evidence. EU Coordinated Implementation Roadmap end-2030 high-risk deadline, which a
misdirected programme reaches without having migrated. NIS2 Article 21(2)(h)
state-of-the-art cryptography obligation, which a non-standardised proprietary
algorithm is unlikely to satisfy. Relates to QS04 (inventory as the control that
detects misdirected spend) and QS05 (procurement requirements for agility).
