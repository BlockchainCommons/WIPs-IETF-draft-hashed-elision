---
v: 4

title: "Deterministic Hashed Data Elision: Problem Statement and Areas of Work"
abbrev: "HashedElision"
docname: draft-appelcline-hashed-elision-latest
category: info
stream: IETF

ipr: trust200902
area: Applications and Real-Time
workgroup: Network Working Group
keyword: Internet-Draft

pi: [toc, sortrefs, symrefs]

venue:
  github: BlockchainCommons/WIPs-IETF-draft-hashed-elision

author:
 -
    name: Shannon Appelcline
    organization: Blockchain Commons
    email: shannon.appelcline@gmail.com
 -
    name: Wolf McNally
    organization: Blockchain Commons
    email: wolf@wolfmcnally.com
 -
    name: Christopher Allen
    organization: Blockchain Commons
    email: christophera@lifewithalacrity.com

normative:
    ada:
        title: "Americans with Disabilities Act of 1990, As Amended"
        target: "https://www.ada.gov/law-and-regs/ada/"
    battery:
        title: "REGULATION (EU) 2023/1542 OF THE EUROPEAN PARLIAMENT AND OF THE COUNCIL of 12 July 2023 concerning batteries and waste batteries ..."
        target: "https://eur-lex.europa.eu/legal-content/EN/TXT/HTML/?uri=CELEX:32023R1542"
    c2pa:
        title: "C2PA Technical Specification"
        target: "https://spec.c2pa.org/specifications/specifications/1.2/specs/C2PA_Specification.html"
    c2pa-harms:
        title: "PHASE III - Existing and potential mitigations (Version 1.0 - December 2021)"
        target: "https://spec.c2pa.org/specifications/specifications/1.0/security/_attachments/Due_Diligence_Actions.pdf"
    ccpa:
        title: "California Consumer Privacy Act of 2018 (CCPA)"
        target: "https://leginfo.legislature.ca.gov/faces/codes_displayText.xhtml?division=3.&part=4.&lawCode=CIV&title=1.81.5"
    cybersecurity:
        title: "REGULATION (EU) 2024/2847 OF THE EUROPEAN PARLIAMENT AND OF THE COUNCIL of 23 October 2024 on horizontal cybersecurity requirements for products with digital elements ..."
        target: "https://eur-lex.europa.eu/legal-content/EN/TXT/HTML/?uri=OJ:L_202402847"
    fatf-rec:
        title: "The FATF Recommendations"
        target: "https://www.fatf-gafi.org/content/dam/fatf-gafi/recommendations/fatf-recommendations-2012.pdf"
    gdpr-17:
        title: "Art. 17 GDPR: Right to erasure (‘right to be forgotten’)"
        target: "https://gdpr-info.eu/art-17-gdpr/"
    gdpr-blockchain:
        title: "Guidelines 02/2025 on processing of personal data through blockchain technologies"
        target: "https://www.edpb.europa.eu/documents/guideline/guidelines-022025-on-processing-of-personal-data-through-blockchain_en"
    gdpr-masking:
        title: "Coordinated Enforcement Action, implementation of the right to erasure by controllers"
        target: "https://www.edpb.europa.eu/documents/coordinated-enforcement-framework/coordinated-enforcement-action-implementation-of-the-0_en"
    gordian-envelope: I-D.mcnally-envelope
    hipaa:
        title: "Health Insurance Portability and Accountability Act of 1996 (HIPAA)"
        target: "https://www.cdc.gov/phlp/php/resources/health-insurance-portability-and-accountability-act-of-1996-hipaa.html"
    nophonehome:
        title: "No Phone Home Statement"
        target: "https://nophonehome.com/"
    sb923:
        title: "Consumer privacy requests: deletion request records and request submission methods."
        target: "https://legiscan.com/CA/text/SB923/id/3448002"
    RFC9901: Selective Disclosure for JSON Web Tokens
    RFC9995: CBOR Object Signing and Encryption (COSE) Hash Envelope
    trips:
        title: "AGREEMENT ON TRADE-RELATED ASPECTS OF INTELLECTUAL PROPERTY RIGHTS AS AMENDED BY THE 2005 PROTOCOL AMENDING THE TRIPS AGREEMENT"
        target: "https://www.wto.org/english/docs_e/legal_e/downloads_e/TRIPS05_en.pdf"
    utsa: 
        title: "Uniform Trade Secrets Act, model law, as amended in 1985 (© The National Conference of Commissioners on Uniform State Laws 1985), United States of America"
        target: "https://www.wipo.int/wipolex/en/legislation/details/3934"
    
informative:
    RFC6973: Privacy
    RFC9620: Human Rights

--- abstract

This document discusses the need for deterministic hashed data elision as a baseline for secure data storage. It focuses on four different categories of requirements: corporate needs, privacy compliance requirements, variable access compliance requirements, and past IETF RFCs. Additional sections discuss the functional requirements that match these needs and how existing technologies meet them.

--- middle

# Introduction

Data is the lifeblood of the digital world, but it's also increasingly recognized as a danger. This Problem Statement lists four situations where better control of data is needed:

1. For companies protecting secrets.
2. For anyone bound by strict compliance needs such as GDPR.
3. For anyone bound by compliance requirements that call for different actors to have different levels of access to data.
4. For anyone interested in protecting privacy and human rights.

In recent years, data minimization has been recognized as a solution for many of these problems: decrease the data being shown to what is needed for a task. But it's just a first step that doesn't meet all of these requirements. To offer complete solutions to these problems requires creating data structures where data is dynamically removable without it impacting digital verification or validation. This can be done with "deterministic hashed data elision", which is offered as a best practice for these problems. Functional requirements are laid out for deterministic hashed data elision and current technologies are examined for how well they support it.

Even this remains a baseline: a minimum level for secure data storage. More powerful techniques such as anti-correlation signatures and zero-knowledge proofs can expand on this baseline, particularly if a deterministic hashed data elision system is sufficiently open that it can be used with a variety of cryptographic techniques.

# Problem Statement

A variety of requirements are converging on the need for deterministic hashed data elision.

## Companies Generally Need to Protect Secrets

Most generally, companies (and other entities) need to protect digital secrets. 

The Uniform Trade Secrets Act (1985) [utsa] of the United States requires "reasonable" efforts to protect trade secrets, which is reflected by similar language in the WTO's TRIPs Agrement (1994) [trips]. Specific laws such as the US ADA (1990) [ada] also require the protection of specific data (in this case, related to an employee's disabilities). Fidicuiary duties of confidentiality offer yet another vector where certain peoples or companies might have higher secrecy requirements.

Encryption have usually been considered sufficient for these needs, but it is insufficient for newer regulations, calling for a new general solution.

## Data Protection Laws Also Require Deletion

It has now been almost a decade since the  implementation date of the GDPR (2016), whose Article 17 [gdpr-17] requires that data holders "erase personal data without undue delay" when it is no longer needed, when consent is withdrawn, or under certain other conditions. However, the repercussions of that continue to be felt, such as the EDPB's 2025 "Coordinated Enforcement Action" [gdpr-masking], which noted that "basic pseudonymisation or partial masking" was insufficient to meet erasure needs, or their 2025 "Guidelines" [gdpr-blockchain], which said the right to erasure "must be complied with by design" of blockchain systems.

The GDPR was just the newest of a long series of government regulations that call for data protection in a variety of situations. Title II of the United States' HIPAA (1996) [hipaa], requiring that "protected health information" be kept confidential by "covered entities", is the most famous.

Meanwhile, deletion and protection laws continue to expand. California's CCPA (2018) [ccpa] took the model of the GDPR to to the United States, including a right to deletion. As with the GDPR, there has been continued clarification of this right, such as SB 923 (2026) [sb923], which allows a business to retain "the minimum data necessary to ensure the consumer’s personal information remains deleted from its record".

For businesses to ensure compliance with these and other laws requiring data protection and deletion demands a robust program of not just encryption, but data elision (removal), and is therefore the first requirement leading to a deterministic hashed data elision system.

## Other Laws Require Variable Data Access Levels

Some laws reveal even more complex compliance needs, where different parties must be given different levels of access to data.

The 2024 EU Cyber Resilience Act [cybersecurity] requires a very common two-level model, where specific information (software bills of material) must be offered to authorities but not the public.

The 2023 EU Battery Regulation [battery] goes further, offering an example of how wide-ranging legislature (this one focused on battery recycling) can now include compliance requirements for variable data access. It describes a battery passport where different information should be offered to the public, recyclers, and other actors. The passport must further be stored with a "decentralised data system" and must carry "data in a secure way which respects privacy rules".

The 2026 FATF recommendation #17 [fatf-rec] offers a more intriguing variation. It allows banks to rely on other banks for Know-Your-Customer (KYC) information but requires "Financial institutions should take adequate steps to satisfy themselves that copies of identification data and other relevant documentation relating to the CDD requirements will be made available from the third party upon request without delay." In other words, it recognizes variable data storage among parties and requires that variability be provable (and ultimately accessible).

These compliance needs suggest that a more nuanced solution is needed than even simple protection or removal of data, which means that they are the second requirement leading to a deterministic hashed data elision system.

## Human Rights Call for Privacy as Well

The IETF has previously recognized the need for data privacy in two informational RFCs, {{RFC 6973}} and {{RFC 9620}}. Between them, the two offer a large menu of architectural designs to protect data communication protocols and ensure digital human rights.

Though these RFCs are valuable resources, they unfortunately have not been widely considered in modern standards. Newer regulations such as the GDPR are making them more of a necessity.

## Data Minimization is Broadly the Answer

Most broadly, the solution to requirements for data protection and erasure is the removal of unnecessary content that is not required for a specific task ("data minimization"), with the data holder deciding exactly what goes to each party ("selective disclosure"). 

However, simplistic data minimization is not the full answer. It can impact the integrity and authenticity of the original data set, which are both flagged as things needed to ensure Human Rights in RFC 9620. It also doesn't offer good solutions for the variable access needs of more recent regulations such as the EU Battery Regulation, nor does it address issues like SB 923, which seeks a way to identify a customer without holding personally identifiable information.

The deterministic hashed data elision described here is meant to offer a solution to all of these issues.

# Potential Use Cases

To date, selective disclosure has largely been used in credential systems, yet modern-day compliance needs demonstate a much larger set of use cases.

Some possibilities are included here:

1. Employee Records. Within the US, there has long been a need to protect certain elements of employee records under the ADA, but records for European employees are now subject to the GDPR as well, requiring deletion when there is not a legal obligation to retain them.
2. User Account Systems. Any online account system is subject to the GDPR and/or CCPA if persons from those jurisdictions have created accounts. Therefore, user accounts must be able to elide or delete data.
3. Medical Records. Not only does HIPAA restrict medical data sharing in the US, but more generally health data is sensitive and must be well protected. Fitness trackers offer a strong use case: they have large packets of medical (and locational) information that is sensitive enough to require protection.
4. Shipping Records. Shipping records are often very sensitive because they reveal not just cargoes, but also who is buying, selling, or shipping what. They also offer a strong use case for variable access as different members of the supply chain will need different information.
5. Logs. Audit logs of various types tend to mix personally identifiable data with sensitive and non-sensitive data. Each of these three types of data must be protected in different ways.

## Use Cases Within the IETF

The IETF is already working on projects related to these topics:

* CBOR: CBOR can ensure the determinism of its content through CDE and dCBOR. Determinism of this sort is a central building block for deterministic hashed data elision.
* COSE: COSE has already defined signatures across hash envelopes {{RFC9995}}. Deterministic hashed data elision provides the opportunity to put them into pragmatic use.
* SCITT: The supply chain is generally a strong use case for deterministic hashed data elision, particularly given the non-redactability of some current solutions.
* SPICE: Though SPICE is currently focused on issuer-determined selective disclosure,  deterministic hashed data elision offers new options.
* TRANS/PLANTS: Issues with redaction and entry suppression for the TRANS group date back 10 years and have been carried forward to PLANTS. Deterministic hashed data elision could offer solutions within a Merkle Tree.
 
# Functional Requirements

The following lays out the functional elements that make up the deterministic hashed data elision system being offered as a solution to these largely compliance-related requirements and these use cases.

## Data Must Be Structured

The must fundamental requirement is that the data storage must be structured so that it's not just a loose bag of information.

Three more specific requirements for the structure follow.

### Structure Must be Radically Recursive

Requirement: The structure must allow data to underlie data to an effectively infinite depth. 

Purpose: This allows for the creation of complex and meaningful data representations and should permit modeling of a number of different types of graphs, lists, arrays, or other data organizations, creating strong interoperability.

### Structure Must Be Deterministic

Requirement: The structure must be deterministic, so that it is always stored in the same way no matter what platform it is created or reviewed on.

Purpose: This is required to ensure data hashes are always the same, with data hashes being the main tool we suggest for permitting elision of data.

### Structure Must Be Tamper-Evident

Requirement: It must be obvious when data is removed from the structure.

Purpose: Tamper-evidence and provenance are needed by many modern data systems.

## Data Must Be Elidable

Requirement: It must be possible to remove data from the structure.

Purpose: This supprots the data minimization and selective disclosure required by many modern regulations.

## Different Views Must Be Possible

Requirement: It must be possible to make different views (versions) of a document that are potentially elided in different ways.

Purpose: This is just an expansion of the elidability requirement. It basically says that data should be elidable in a variety of ways.

## Elided Data Must Be Provable

Requirement: It must be possible to later prove that elided data was part of a structure.

Purpose: You don't want to be able to just remove data, but also to show that elided data to specific parties: this supports that need.

## Data Must Be Deletable

Requirement: It must be possible to remove data from the structure in a way that it is no longer possible to prove or even blindly correlate that the data was once part of the structure. 

Purpose: This supports rights of erasure and deletion and other situations where elision alone isn't enough.

## Decisions Must Be Made Dynamically

Requirement: The decisions to elide and make different views must be controlled entirely by the holder of the data, allowing them to do so in a dynamic fashion.

Purpose: This avoids centralization, which could be a time delay, a censorship point, or an information leak. Conflicts over removing phone-home behavior from mDL [nophonehome] have already shown the importance of this requirement. 

## Data Must Remain Signed

Requirement: Any signatures made across the data must remain valid even through elisions and removals.

Purpose: This ensures that the system doesn't break any signed docs, including not just credentials but also contracts and assertions. If signatures were broken, then the system would return to phone-home behavior, as holders would have to request new signatures from an issuer after elision.

# The Deterministic Hashed Data Elision Model

The Deterministic Hashed Data Elision Model offers a specific model for meeting the requirements above. 

It is assumed to generally meet the structural designs of radical recursiveness and tamper evidence, as they could be implemented in a number of different ways, and doing so is considered trivial; they nonetheless remain vitally important to the overall design.

The ability to elide, maintain signatures, maintain provability, and ensure true deletion is the heart of the model and what is supported by the specific design principles below.

## Deterministic Data Hashing

Data hashing generates a fingerprint for each datum through a hash function such as SHA-256, BLAKE3, or any other preferred hashing algorithm. The Deterministic Hashed Data Elision model calls for these hashes to be arranged into a hash tree, so that a single "root hash" can represent (and validate) the content of the entire tree of data.

To support this, a specification must:

1. Create a unidirection fingerprint hash of any data, so that the fingerprint can prove the existence of the data, but the data cannot be derived from the fingerprint.
2. Allow elided data to be verified with a fingerprint.
3. Support a hash tree where each leaf's data is hashed and then each node hash is made up of the sum of the leaf hashes or node hashes below it, up to the root hash, which is made up of the sum of the top-level node hashes and leaves.

The determinism adds one more requirement. A specification must:

4. Ensure that the same data hashes to the same fingerprint no matter when or where it is hashed.

## Data Signing

For data signatures to remain valid, data signing must be done across objects that do not change with elision or deletion. To ensure this, a specification must:

5. Require signing be done against either node hashes or root hashes. 

A root hash signature will always remain valid, even if all leaves and nodes are removed. A node hash signature will remain valid up to the point where that node hash is itself deleted.

## Data Elision

Data elision means that any individual datum (down to the level of granularity of the data structure) can be removed.

To support this, a specification must:

6. Allow for the elision of any datum or collection of data from a larger package of data, leaving behind only a fingerprint of the datum or collection of data.
7. Allow the holder of that data to do that elision, rather than restricting it to only issuers.

This means that an issuer cannot be required to do the elision for the holder, but also cannot preselect what can be elided by a holder.

## Data Deletion

Data deletion requires something more: the ability to permanently delete data in a nonrecoverable way.

To support this, a specification must:

8. Support salting, where a unguessably large random value can be incorporated into a datum or collection of data, so that it becomes part of the hash for that datum or collection of data.
9. Allow for the permanent, irrecoverable removal of that salt as part of the elision process.

The incorporation of a salt ensures there is no possibility of accidental or rainbow-table correlation of elided data, so it has wider use than just ensuring true deletion.

Once all parties have expunged a salt, there will be no way to recover the data through correlation or even to prove its existence because the salt would have to be guessed as well, and by design it is unguessable.

## Data Proving

After data is removed, it can still be proved by revealing the content and comparing this to the remaining hash.

To support this, a specification must:

10. Allow for the revelation of specific fingerprints.
11. Support the easy creation of an inclusion proof that demonstrates how specific data can be hashed to create that specific fingerprint.
12. Enable any holder to create that inclusion proof, not just an issuer.

If a datum has been salted, then the salt must be included as part of an inclusion proof. If that salt is no longer available, then this requirement cannot be met, by design.

# Current Approaches & Their Limitations

A number of methods have appeared in the last several years that support some form of data minimization. Each has their own limitations. The following descriptions and listings lay out the advantages and disadvantages of approaches to date.

## Overview of Methods & Limitations

One of the most general limitations of data-minimization designs to date is that each has been designed to fill a specific niche. C2PA is theoretically about any type of content, but most of its deployment to date focuses on images. SD-JWT and mdoc both focus on identity credentials.

Following is more in-depth information on each method:

C2PA (photo and other content credentials) [c2pa]: A technical specification from the Coalition for Content Provenance and Authenticity (C2PA) for Content Credentials. It focuses on the provenance of content, including audios and videos but especially photographs. It stores data in manifests that consist of claims that contain assertions in JSON-LD, CBOR, and other formats that are then signed with COSE. It allows verification of the validity and provenance of content as well as the attachment of other metadata. Redaction is possible, but limited, as the specification  forbids the redaction of "action-oriented assertions" (e.g., that an image was cropped) and requires that records of redactions remain. Deployment decisions might also change how individual companies manage redaction. As described in C2PA's own harms report [c2pa-harms], redaction depends "on a given C2PA-enabled system's functionality for redaction of soft or hard binding provenance datastores, and/or what usage of irrevocable ledgers."

Gordian Envelope (data) [gordian-envelope]: A data-minimization format that was created to support any type of data, not just the credentials common in other data-minimization systems. It was especially created for data at rest, though its GSTP system also allows for secure data transmission. However, it's ultimately not specialized for work requiring fast response or for microservices. Elision, encryption, and compression are all built directly into the specification. The user can redact (or encrypt or compress) any datum at any time, with signatures staying intact. True deletion is allowed through the use of salt. Multiple semantic contexts can be supported simply by eliding ones that are not required by a particular party.

SD-JWT (credentials) {{RFC9901}}: A system allowing data minimization for specific elements of a JSON structure, focused on JSON Web Token (JWT) claims. The data minimization of SD-JWT depends entirely on the issuer, who in advance creates salted hashes for any data to be selectively disclosed (or hidden) in the future. There is no opportunity for new decisions to be made about hiding data without returning to the issuer for a new version of the credential. Selective Disclosure CBOR Web Tokens (SD-CWT) follows the same model.

mdoc (credentials): mdoc is a "mobile document", as defined by ISO/IEC 18013-5. At this point, it's best known as the format for an mDL (mobile driver's license). mdoc is specifically built for high assurance identity, such as passports or state identification cards. mdoc uses salted hashes for selective disclosure, much like SD-JWT, with the difference being that everything is protected by default with mdoc, with all hashes precomputed and signatures made in advance. This allows for much more holder control than SD-JWT, but ultimately means that selective disclosure is limited by how the issuer defines its data. The balance of power for disclosure also lies mainly with the verifier asking for data rather than the holder offering it (e.g., a verifier might ask for a date-of-birth field rather than a less intrusive age-over field). mDL is currently used by US states and EUDI.

## Summary of Methods & Limitations

The following summarizes some of the content-focused and privacy-focused choices made by these four selective-disclosure formats.

1. General Usage
    - C2PA: content (images)
    - Gordian Envelope: any data
    - SD-JWT: credentials (JWT)
    - mdoc: credentials (identity)
2. General Data Structure
    - C2PA: Flat
    - Gordian Envelope: Hash Tree
    - SD-JWT: Flat in Practice
    - mdoc: Flat
3. Radical Recursion / Infinite Depth
    - C2PA: NO
    - Gordian Envelope: YES
    - SD-JWT: NO in Practice
    - mdoc: NO
4. Selection Controller (for What's Redacted)
    - C2PA: Holder (Restricted)
    - Gordian Envelope: Holder (Structure Limited)
    - SD-JWT: Issuer
    - mdoc: Holder (Structure Limited)
5. Disclosure Controller (for What's Shared)
    - C2PA: Holder
    - Gordian Envelope: Holder
    - SD-JWT: Holder
    - mdoc: Verifier
6. Dynamic Control of Disclosure
    - C2PA: YES (Limited)
    - Gordian Envelope: YES
    - SD-JWT: NO
    - mdoc: YES
7. Salted Hashes
    - C2PA: YES (Possible)
    - Gordian Envelope: YES
    - SD-JWT YES (Limited)
    - mdoc: YES
8. Full Deletion
    - C2PA: YES (Limited, with Records)
    - Gordian Envelope: YES
    - SD-JWT: NO
    - mdoc: NO

# Areas of Work

## Core Areas of Work

The Core Areas of work for deterministic hashed data elision have been laid out in the Functional Requirements and Data Model sections of this document.

Most generally, they center on the following areas:

* Prioritize Data Minimization.
* Base Data Models on Radically Recursive Structures.
* Support Data Elision and Deletion Through Provable Hashing and Salting.
* Put the Holder in Dynamic Control of Elision and Deletion Decisions.
* Put the Holder in Control of Data Sharing Decisions.
* Expand Beyond the Limited Use Cases of C2PA, SD-JWT, and mdoc.

## Optional Areas of Work

Using hashed data elision as a foundation would improve the privacy of almost any IETF protocol.

The Gordian Envelope Internet-Draft is one example of a specification that supports hashed data elision. It could be used to enable all of the Core Areas of Work. It also goes further, incorporating additional functionality that can provide better support for RFC 6973, RFC 9620, and corporate and compliance needs, as described below.

### Extend Support to Encryption & Compression

A hashed data elision system can be expanded to support both encryption and compression functions, as encrypted and compressed data can also be represented by their hashes without revealing any information about the original data.

Incorporating encryption into a data specification offers the highest level of privacy and of data minimization possible, as data can only be viewed by select individuals with the decryption key.

### Address Additional Human Rights Threats

As currently detailed, the Gordian Envelope Internet-Draft supports several other Guidelines for Human Rights Considerations that are listed in §4 of RFC 9620:

- Reliability (§4.3), Integrity (§4.10). Gordian Envelope is founded on CBOR, which means that data is self-describing. This improves its reliability and integrity and also creates interoperability and avoids centralization.
- Localization (§4.6), Accessibility (§4.18). Gordian Envelope supports integrated metadata, which can be used to ensure accessibility and internationalization of data through inclusions of references with a variety of localizations.
- Open Standards (§4.7). As an Internet Draft, Gordian Envelope represents an open standard. It can support interoperable exchange of data, which is vital for human rights.
- Heterogeneity Support (§4.8), Adaptability (§4.9). Gordian Envelope builds its data format on triples: assertions of subjects, predicates, and objects. This format can easily be adapted to a wide variety of data formatting styles.
- Privacy (§4.14). Besides the obvious privacy benefits of data minimization, Gordian Envelope also can improve privacy through its integrated metadata, which can be used to document the sensitivity of contents, retention limits, etc.
- Pseudonymity (§4.15), Censorship Resistance (§4.16). Gordian Envelope is built to support XIDs, or extensible identifiers, which can provide censorship-resistant pseudonymity.
- Decentralization (§4.19). Gordian Envelope is built as an autonomous cryptographic object, self-contained, self-verifying, and incorporating local signatures and cryptography.

### Expand Cryptographic Techniques

This Problem Statement has generally been crypto-agnostic. It has not specified precise hashing or encryption methods. A model that can embrace a variety of cryptographic options will be more powerful, as it will be able to adapt to flaws in cryptography systems and ultimately to post-quantum cryptography.

This Problem Statement has also not addressed more advanced cryptographic methods. As such, it should be considered a baseline: the minimum necessary to meet human-rights needs and the requirements of the modern-day compliance landscape.

More powerful techniques could improve on this. These include, but are not limited to:

- Anti-Correlation Signatures. The possibility of untintended correlation is an important issue to consider with hashed elision, so anything that can work against it is a boon.
- Zero-Knowledge Proofs. ZKPs offer another way to minimize data, by not sharing the foundational information that a fact is built upon.

# Privacy Considerations

As outlined, the general concept of hashed data elision provides a wide variety of privacy advancements. 

The biggest remaining privacy concern is  accidental correlation. Salting is included as a requirement so that elided data can't be uncovered with rainbow tables (or just knowledge of what might be contained). This protects against the correlation of individual data fields. However, a different form of correlation can arise if different parties have different versions of the same data, which has been elided in different ways: they can together piece together a new view that has more content that was originally intended. 

This is currently seen as an acceptable side-effect of an elision system that offers many other benefits and can be offset by careful creation of structures, such as gathering small groups of data into distinct, elided branches.

However, the question also remains open as to whether there might be more expansive and more automated solutions.

# Security Considerations

Hashed data elision is intended to strengthen communication security, primarily by enhancing confidentiality (through elision) while also maintaining data integrity (through hashing). Supporting this with a signature system that signs hashes rather than original data also allows for peer entity authentication, creating a strong foundation for overall communication security.

However, that security depends on the strength of hashing algorithms and encryption/signature algorithms. Strong, unbroken hashes and encryption schemes are required. Potential threats to hashes and encryption such as quantum computing would result in threats to any hashed data elision system.

# IANA Considerations

This document has no IANA actions. Gordian Envelope has already been assigned CBOR tag #200 by IANA.

--- back

# Acknowledgments

The authors are grateful for the support of the CBOR working group in discussions of Gordian Envelope and general guidance within the IETF.
