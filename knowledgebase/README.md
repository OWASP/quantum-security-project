# Quantum security knowledge base

A running log of disclosed vulnerabilities and cryptanalysis findings relevant
to quantum-era security, mapped against the OWASP Top 10 for Quantum Security
Risks (QS01-QS10).

Maintainer: Vishnu Ajith (@Vishnu2707)

## How entries work

- Title
- Date
- Maps to: QS0x
- Summary: plain language, 2-3 lines
- Source
- Why it matters

Anyone can add entries. Open a PR against this file with the same format.

---

## Entries

### HAWK signature scheme, improved key recovery attack, since withdrawn

- Date: 28 Jul 2026
- Maps to: QS03, Vulnerable Signatures and Code-Signing
- Summary: Anthropic researchers found a faster key recovery attack against HAWK, a NIST round-3 post-quantum signature candidate, using an AI model. It reduces key recovery to a lattice problem in half the dimension. By their gate-count estimates HAWK-512 drops from 2^150 to 2^108 and HAWK-1024 from 2^288 to 2^182, and they recovered a HAWK-256 secret key in a few hours on one server. The attack was not retracted or corrected. The HAWK team has since withdrawn HAWK from NIST's additional signatures process, and NIST's round-3 page now says so. It doesn't carry over to Falcon.
- Source: https://www.anthropic.com/research/discovering-cryptographic-weaknesses https://anthropic.com/document/hawk_key_recovery.pdf https://csrc.nist.gov/projects/pqc-dig-sig/round-3-additional-signatures
- Why it matters: HAWK isn't deployed anywhere, so no production impact today. But it's a live example of a PQC candidate getting weakened after two years of expert review and then dropped before standardisation, which is exactly the kind of algorithm-level risk QS03 needs to keep tracking. Systems that hard-code one signature scheme have no way out when this happens.

### CVE-2026-46344, XMSS verification OID mismatch, out-of-bounds read in liboqs

- Date: 15 May 2026
- Maps to: QS03, Vulnerable Signatures and Code-Signing
- Summary: In liboqs's XMSS and XMSS^MT stateful signature verification, the wrapper layer checks the caller-supplied signature length against the declared algorithm's expected size, but the underlying code re-parses the OID from the public key itself and derives a different, larger signature length from it. When a public key's OID references a different XMSS parameter set than the one declared, the code reads past the end of the actual signature buffer. Distinct from a related issue (CVE-2026-44518), which the initial fix for that CVE did not close. Fixed in liboqs 0.16.0.
- Source: https://github.com/open-quantum-safe/liboqs/security/advisories/GHSA-2wxh-55qf-c7wg
- Why it matters: no known way to leak data or forge a valid signature through this (I reported it), worst case is a crash, but the reachability matters more than the severity. This is exposed to any caller that verifies a public key from an untrusted source, firmware update metadata, TUF metadata, certificate stores. Those are exactly the trust boundaries QS03 is meant to cover, and it shows how a signature scheme can have a sound design but still fail on an implementation detail nobody tested for, mismatched parameter sets encoded in the key itself.

### CVE-2024-37880, Clangover, timing leak in the Kyber reference code

- Date: 10 Jun 2024
- Maps to: QS06, Insecure Migration and Hybrid Misuse
- Summary: The Kyber reference implementation's poly_frommsg is written in constant-time style, but Clang up to 18.x with some optimisation flags compiles it into a secret-dependent branch. That lets an attacker recover ML-KEM-512 secret keys from timing in minutes. Fixed in pq-crystals/kyber.
- Source: https://www.cve.org/CVERecord?id=CVE-2024-37880
- Why it matters: the source looks fine and the leak only exists in the compiled binary, so reviewing the code or picking a good algorithm doesn't catch it. Migration plans need to test the compiled build, not just the source.

### CVE-2025-48946, theoretical design flaw in HQC (liboqs)

- Date: 29 May 2025
- Maps to: QS06, Insecure Migration and Hybrid Misuse
- Summary: Reported by Markku-Juhani Saarinen. Large numbers of malformed ciphertexts can share the same implicit rejection value in HQC. No known exploit, CVSS 3.7. Affects liboqs up to 0.13.0 with no patch, so HQC has been disabled by default in liboqs since 0.13.0 until the HQC team updates the design.
- Source: https://github.com/open-quantum-safe/liboqs/security/advisories/GHSA-3rxw-4v8q-9gq5
- Why it matters: HQC is the backup KEM NIST picked in case lattices fall. It doesn't give the same guarantees as ML-KEM in protocols that derive keys from the KEM output, so anyone treating it as a drop-in fallback needs to know that.
