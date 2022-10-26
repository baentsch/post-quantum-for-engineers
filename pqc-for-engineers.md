# Post-Quantum Cryptography for Engineers

This work is part of public domain.
[See this page for more information.](https://creativecommons.org/publicdomain/zero/1.0/)

## Introduction

This document is meant to give general guidance on the structure and use of post-quantum cryptographic (PQC) algorithms for engineers who are using PQC algorithms in their software.
Topics include which PQC algorithms to use, how PQC key exchange mechanisms (KEMs) differ from classical KEMs, expected size and processing time differences between PQC algorithms and classical algorithms, and so on.

The reader of this document is expected to understand coding and data structures using established cryptographic libraries. They are also expected to understand classical cryptographic basics.
It does not cover such topics as when classical algorithms might become vulnerable (for that, see documents such as [Quantum Computing and the DNS](https://www.icann.org/octo-031-en.pdf) and others.)

### Contributing to This Document

The guide was inspired by a thread in September 2022 on the <mailto:pqc@ietf.org> mailing list.
The document is being collaborated on through a [GitHub repository] (https://github.com/paulehoffman/post-quantum-for-engineers).

The editors actively encourage contributions to this document.
Please consider writing a section on a topic that you think is missing.
Short of that, writing a paragraph or two on an issue you found when writing code that uses PQC would make this document more useful to other coders.
Opening issues that suggest new material is fine too, but relying on others to write the first draft of such material is much less likely to happen than if you take a stab at it yourself.

## Classical Cryptographic Primitives that Could Be Replaced by PQC

(KEMs)

(Signatures)

## Popular PQC Algorithms

The National Institute of Standards and Technology (NIST) started a process to solicit, evaluate, and standardize one or more quantum-resistant public-key cryptographic algorithms, as seen [here](https://csrc.nist.gov/projects/post-quantum-cryptography).
Said process has reached its [first announcement](https://csrc.nist.gov/publications/detail/nistir/8413/final) in July 5, 2022, which stated which candidates to be standardized for two types of algorithms:

* Key Encapsulation Mechanisms (KEMs)
* Digital Signatures

NIST announced as well that they will be [opening a fourth round](https://csrc.nist.gov/csrc/media/Projects/post-quantum-cryptography/documents/round-4/guidelines-for-submitting-tweaks-fourth-round.pdf) to standardize an alternative KEM, and a [call](https://csrc.nist.gov/csrc/media/Projects/pqc-dig-sig/documents/call-for-proposals-dig-sig-sept-2022.pdf) for new candidates for a post-quantum signature algorithm.

## Classic vs. Post-Quantum

Post-quantum cryptography or quantum-safe cryptography refers to cryptographic algorithms that are secure against cryptographic attacks from both a quantum and a classic computers.

When considering the security risks associated with the ability of a quantum computer to attack classic cryptography it is important to distinguish between the impact on symmetric algorithms and public-key ones. 

For unstructured data such as symmetric encrypted data or cryptographic hashes, although quantum computers can search for specific solutions across all possible input combinations (e.g., Grover's Algorithm), no quantum algorithm is known to completely break the security properties of these classes of algorithms: doubling the size of symmetric secrets and hash outputs provide an easy solution against such attacks.

For structured data such as public-key and signatures, instead, quantum computers can fully solve the underlying hard problems used in classic cryptography (see Shor's Algorithm). Because an increase of the size of the keypair would not provide a secure solution in this case, a complete replacement of the algorithm is needed. Therefore, post-quantum public-key cryptography must rely on problems that are different from the ones used in classic public-key cryptography (i.e., the integer factorization problem, the discrete logarithm problem, and the elliptic-curve discrete logarithm problem). The current set of problems used in post-quantum cryptography can be currently grouped into five different categories: multivariate, lattice-based, code-based, hash-based, and the isogeny-based.

### Lattice-Based Public-Key Cryptography

Lattice-based public-key cryptography leverages the simple construction of lattices (i.e., a regular collection of points in an Euclidean space that are regularly spaced) to build problems that are hard to solve such as the Shortest Vector or Closes Vector Problem, Learning with Errors, and Learning with Rounding. All these problems have good proof for worst-to-average case reduction, thus equating the hardness of the average case to the worst-case.

The possibility to implement public-key schemes on lattices is tied to the characteristics of the basis used for the lattice. In particular, solving any of the mentioned problems can be easy when using reduced or "good" basis (i.e., as short as possible and as orthogonal as possible), while it becomes computationally infeasible when using "bad" basis (i.e., long vectors not orthogonal). Although the problem might seem trivial, it is computationally hard when considering many dimensions. Therefore a typical approach is to use use "bad" basis for public keys and "good" basis for private keys. The public keys ("bad" basis) let you easily verify signatures by checking, for example, that a vector is the closest or smallest, but do not let you solve the problem (i.e., finding the vector). Conversely, private keys ( i.e., the "good" basis) can be used for generating the signatures (e.g., finding the specific vector). Signing is equivalent of solving the lattice problem.

Lattice-based schemes usually have good performances and average size public keys and signatures making them good candidates for general-purpose use such as replacing the use of RSA in PKIX certificates.

Examples of such class of algorithms are Falcon and Dilithium.

### Multivariate-Based Public-Key Cryptography

The Multivariate Quadratic problem is an NP-hard problem that can be expressed as finding the common "zero" vector that solves a set of polynomials in finite fields. In other words, the underlying problem can be expressed as finding the vector (z1, ..., zn) in Fn2 that solves a set of given equations:

    f1(x1, ..., xn) = 0, ...., fm(x1, ..., xn) = 0

Signatures use easily invertible non-linear polynomials (P) that need to be masked by using a combination of affine linear transformations (S and T). Indeed, given P:Fn -> Fm, S: Fn -> Fn, T: Fm -> Fm, the affine transformations are build in such a way to make the public key G = S * P * T hard to invert. Knowing its individual components (i.e., the private key) allows to easily compute the inverse G^(-1) which is used to produce signatures, i.e. G^(-1) = T^(-1) * P^(-1) * S^(-1). To verify signatures, use the public key over the signature vector, i.e. G(s) = m.

### Code-Based Public-Key Cryptography

### Hash-Based Public-Key Cryptography

### Isogeny-Based Public-Key Cryptography

### Announced to be standardized NIST algorithms

#### PQC KEMs

* [CRYSTALS-Kyber](https://pq-crystals.org/kyber/): Kyber is a module learning with errors (MLWE)-based key encapsulation mechanism.

#### PQC Signatures

* [CRYSTALS-Dilithium](https://pq-crystals.org/dilithium/)
* [Falcon](https://falcon-sign.info/)
* [SPHINCS+](https://sphincs.org/)

### Candidates advancing to the fourth-round for standardization at NIST

The fourth-round of the NIST process only concerns with KEMs.
The candidates still advancing for standardization are:

* [Classic McEliece](https://classic.mceliece.org/)
* [BIKE](https://bikesuite.org/)
* [HQC](http://pqc-hqc.org/)
* [SIKE](https://sike.org/): Supersingular Isogeny Key Encapsulation (SIKE) is a specific realization of the SIDH (Supersingular Isogeny Diffie-Hellman) protocol. Recently, a [mathematical attack](https://eprint.iacr.org/2022/975.pdf) based on the "glue-and-split" theorem from 1997 from Ernst Kani was found against the underlying chosen starting curve and torsion information. In practical terms, this attack allows for the efficient recovery of the private key. NIST has to yet comment if the scheme will be still considered and there is still debate around if the scheme can be changed so that the attack can be prevented.

### Algorithms not-to-be standardized by NIST that have some support

#### KEMs

* [NTRU](https://ntru.org/)
* [NTRU-Prime](https://ntruprime.cr.yp.to/)

## KEMs

### What is a KEM

### What security properties do they provide

* IND-CPA
* IND-CCA

### Where can a KEM be used

To note:
* KEMs vs Diffie-Hellman (DH): they can be a replacement but they are not a one-to-one replacement. KEMs do not provide non-interactivity, for example, that DH does provide.
* Replacing DH algorithms with PQ KEMs.
* Which are used where.
* “Key Transport API (aka RSA)”, the “Key Agreement API (aka (EC)DH)”, and how the “KEM API” is neither of those.

## PQC Signatures

### What is a Post-quantum Signature

### What security properties do they provide

### Where can different types of PQC signatures be used

(HBS vs Lattice signatures: when each is appropriate.)

(Guidance for managing state of XMSS / LMS. Tree-splitting at keygen time, synchronous state management, any other tricks that are worth documenting. Including when it’s just too complicated to even attempt, like a 30 year signing key where you don’t know at keygen time how many backup copies you’ll need over its lifetime and flips to backup may happen without warning.)

## Recommendations for Security / Performance Tradeoffs

(For example if full-strength Kyber1024 just won’t fit. Under what circumstances can you go down to level1 lattice strength (or less)?)

## Details of FALCON and Dilithium Relevant to Coding

(A layman’s explanation of the gaussian-vs-uniform sampling difference between FALCON and Dilithium. Why access to a robust floating point stack matters. What type of devices you should / should not implement FALCON on.)

## Hash-then-Sign Versus Sign-then-Hash

(...and the security weaknesses of each)

## Further Reading & Resources

### Reading List
(A reading list. [Serious Cryptography](https://nostarch.com/seriouscrypto). Pointers to PQC sites with good explanations. List of reasonable Wikipedia pages.)

### Developer Resources

- [Open Quantum Safe](https://openquantumsafe.org/) and corresponding [github](https://github.com/open-quantum-safe)

