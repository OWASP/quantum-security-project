# Awesome Post-Quantum Cryptography [![Awesome](https://awesome.re/badge.svg)](https://awesome.re)

> Useful resources for Post-Quantum Cryptography 

Post-Quantum Cryptography is the field of cyber security concerned with updating cryptographic systems to achieve quantum resistance, remaining secure once powerful quantum computers have beeen created.


## Contents

- [Cryptographic Inventory](#cryptographic-inventory)
- [PQC Implementations](#pqc-implementations)
- [Standards](#standards)
- [Test & Validation](#test--validation)
- [Training & Education](#training--education)

<br>

## Cryptographic Inventory

Tools and resources that assist with the creation of cryptographic inventories, used to help guide PQC migrations.

- [open-crypto-rules](https://github.com/scanoss/open-crypto-rules) - Open source Semgrep/OpenGrep rules for detecting cryptographic usage in source code (cuurently C, Go & Rust).
- [pq-audit](http://github.com/mk-scorpiosec/pq-audit) - A tool that evaluates cryptographic posture, infrastructure configuration, and code against NIST PQC standards.


## PQC Implementations
 
- [boringssl](https://boringssl.googlesource.com/boringssl) - Google-maintained fork of OpenSSL for internal and specific project needs.
- [openssl](https://github.com/openssl/openssl) - Production-grade implementations of NIST-standardised algorithms, from version 3.5 onwards.
- [liboqs](https://github.com/open-quantum-safe/liboqs) - Implements a broad range of standardised and candidate PQC schemes, great for experimentation but not recommended for production.


## Standards

- [NIST PQC Standardisation Homepage](https://csrc.nist.gov/projects/post-quantum-cryptography) - Home page for NIST's PQC standardisation project.
- [NIST Standard: ML-KEM](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.203.pdf) - FIPS 203, Module-Lattice-Based Key-Encapsulation Mechanism Standard (ML-KEM).
- [NIST Standard: ML-DSA](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.204.pdf) - FIPS 204, Module-Lattice-Based Digital Signature Standard (ML-DSA).
- [NIST Standard: SLH-DSA](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.205.pdf) - FIPS 205, Stateless Hash-Based Digital Signature Standard (SLH-DSA).


## Test & Validation

- [OQS Test Servers](https://test.openquantumsafe.org/) - Individual TLS test services for specific PQC algorithms.


## Training & Education
 
- [PQC at The Cloudflare Blog](https://blog.cloudflare.com/tag/post-quantum/) - High-quality detailed articles on practical implementation of PQC.


