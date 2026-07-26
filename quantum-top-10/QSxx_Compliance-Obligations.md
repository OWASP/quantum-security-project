## Compliance Obligations

**Description:**

Regulatory, legislative and standards requirements mandate the migration to quantum-resistant cryptography. Organisations must meet such requirements regardless of the creation of a cryptographically-relevant quantum computer (CRQC) that can actually break classical crypto schemes.

**Common Examples of Vulnerability:**

1. NSM-10 requires US federal entities to migrate to quantum-resistant cryptography, with a target of "achieving as much migration as possible by 2035".
2. NIST has established timelines for depreciating and disallowing RSA, ECC and Diffie-Hellman. NIST standards are considered de-facto industry standards; in parallel, contractual and insurance terms often include a provision to adopt industry standards. Ergo failing to follow NIST standards could leave an organisation in breach of contractual or insurance terms. 
3. Several EU laws reference the need to consider "state-of-the-art" security controls; quantum-resistant crypto meets that definition.

**How to Prevent:**

1. Understand the consequences of not migrating, or delaying migrating, by identifying any relevant legislation, regulatory requirements and contractual terms relating to cryptography.
2. Develop and execute a cryptographic migration that fulfils the requirements identified in #1.

**Example Attack Scenarios:**

Scenario #1: An organisation defers PQC-related activities in the belief that a CRQC is still many decades away, whilst failing to recognise the evolving compliance landscape. They discover during an audit several years later that their failure to migrate leaves them out-of-step with industry standards, and therefore in breach of contract with their clients. Clients are forced to move their business elsewhere due to their own 33rd-party commitments to their clients. 

**Reference Links:**

1. [National Security Memorandum 10](https://www.presidency.ucsb.edu/documents/memorandum-promoting-united-states-leadership-quantum-computing-while-mitigating-risks): NSM-10 - Federal requirements for migrating to quantum-resistant cryptography
2. [NIST IR 8547](https://nvlpubs.nist.gov/nistpubs/ir/2024/NIST.IR.8547.ipd.pdf): NIST proposals to depreciate & disallow RSA/ECC/DH
