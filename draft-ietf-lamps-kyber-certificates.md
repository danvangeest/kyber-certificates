---
title: >
  Internet X.509 Public Key Infrastructure - Algorithm Identifiers
  for the Module-Lattice-Based Key-Encapsulation Mechanism (ML-KEM)
abbrev: ML-KEM in Certificates
category: std

docname: draft-ietf-lamps-kyber-certificates-latest
submissiontype: IETF
number:
date:
consensus: true
v: 3
area: SEC
workgroup: LAMPS
keyword:
  ML-KEM
  Kyber
  KEM
  Certificate
  X.509
  PKIX
venue:
  group: "Limited Additional Mechanisms for PKIX and SMIME (lamps)"
  type: "Working Group"
  mail: "spasm@ietf.org"
  arch: "https://mailarchive.ietf.org/arch/browse/spasm/"
  github: "lamps-wg/kyber-certificates"
  latest: "https://lamps-wg.github.io/kyber-certificates/#go.draft-ietf-lamps-kyber-certificates.html"

author:
 -
    name: Sean Turner
    organization: sn3rd
    email: sean@sn3rd.com
 -
    ins: P. Kampanakis
    name: Panos Kampanakis
    org: AWS
    email: kpanos@amazon.com
 -
    ins: J. Massimo
    name: Jake Massimo
    organization: AWS
    email: jakemas@amazon.com
 -
    ins: B. Westerbaan
    name: Bas Westerbaan
    organization: Cloudflare
    email: bas@westerbaan.name

normative:
  X680:
    target: https://www.itu.int/rec/T-REC-X.680
    title: >
      Information technology - Abstract Syntax Notation One (ASN.1):
      Specification of basic notation
    date: 2021-02
    author:
    -  org: ITU-T
    seriesinfo:
      ITU-T Recommendation: X.680
      ISO/IEC: 8824-1:2021
  X690:
    target: https://www.itu.int/rec/T-REC-X.690
    title: >
      Information technology - Abstract Syntax Notation One (ASN.1):
      ASN.1 encoding rules: Specification of Basic Encoding Rules (BER),
      Canonical Encoding Rules (CER) and Distinguished Encoding Rules (DER)
    date: 2021-02
    author:
    -  org: ITU-T
    seriesinfo:
      ITU-T Recommendation: X.690
      ISO/IEC: 8825-1:2021

informative:
  CDM23:
    title: "Keeping Up with the KEMs: Stronger Security Notions for KEMs and automated analysis of KEM-based protocols"
    target: https://eprint.iacr.org/2023/1933.pdf
    date: 2023
    author:
      -
        ins: C. Cremers
        name: Cas Cremers
        org: CISPA Helmholtz Center for Information Security
      -
        ins: A. Dax
        name: Alexander Dax
        org: CISPA Helmholtz Center for Information Security
      -
        ins: N. Medinger
        name: Niklas Medinger
        org: CISPA Helmholtz Center for Information Security
  KEMMY24:
    title: "Unbindable Kemmy Schmidt: ML-KEM is neither MAL-BIND-K-CT nor MAL-BIND-K-PK"
    target: https://eprint.iacr.org/2024/523.pdf
    date: 2024
    author:
      -
        ins: S. Schmieg
        name: Sophie Schmieg
  NIST-PQC:
    target: https://csrc.nist.gov/projects/post-quantum-cryptography
    title: >
      Post-Quantum Cryptography Project
    author:
    - org: National Institute of Standards and Technology (NIST)
    date: 2016-12-20

--- abstract

The Module-Lattice-Based Key-Encapsulation Mechanism (ML-KEM) is a
quantum-resistant key-encapsulation mechanism (KEM). This document
describes the conventions for using the ML-KEM in X.509 Public Key
Infrastructure. The conventions for the subject public keys and
private keys are also described.

--- middle

# Introduction

The Module-Lattice-Based Key-Encapsulation Mechanism (ML-KEM) standardized in
{{!FIPS203=DOI.10.6028/NIST.FIPS.203}} is a quantum-resistant
key-encapsulation mechanism (KEM) standardized by the US National Institute
of Standards and Technology (NIST) PQC Project {{NIST-PQC}}. Prior to
standardization, the earlier versions of the mechanism were known as
Kyber. ML-KEM and Kyber are not compatible. This document specifies the use
of ML-KEM in Public Key Infrastructure X.509 (PKIX) certificates {{!RFC5280}}
at three security levels: ML-KEM-512, ML-KEM-768, and ML-KEM-1024, using
object identifiers assigned by NIST. The private key format is also
specified.

## Applicability Statement

ML-KEM certificates are used in protocols where the public key is used to
generate and encapsulate a shared secret used to derive a symmetric key used
to encrypt a payload; see {{?I-D.ietf-lamps-cms-kyber}}. To be used in TLS,
ML-KEM certificates could only be used as end-entity identity certificates
and would require significant updates to the protocol; see
{{?I-D.celi-wiggers-tls-authkem}}.

# Conventions and Definitions

{::boilerplate bcp14-tagged}

# Algorithm Identifiers

The AlgorithmIdentifier type is defined in {{!RFC5912}} as follows:

~~~
  AlgorithmIdentifier{ALGORITHM-TYPE, ALGORITHM-TYPE:AlgorithmSet} ::=
    SEQUENCE {
      algorithm   ALGORITHM-TYPE.&id({AlgorithmSet}),
      parameters  ALGORITHM-TYPE.
                    &Params({AlgorithmSet}{@algorithm}) OPTIONAL
    }
~~~

<aside markdown="block">
  NOTE: The above syntax is from {{!RFC5912}} and is compatible with the
  2021 ASN.1 syntax {{X680}}. See {{RFC5280}} for the 1988 ASN.1 syntax.
</aside>

The fields in AlgorithmIdentifier have the following meanings:

* algorithm identifies the cryptographic algorithm with an object
  identifier.

* parameters, which are optional, are the associated parameters for
  the algorithm identifier in the algorithm field.

The AlgorithmIdentifier for a ML-KEM public key MUST use one of the
id-alg-ml-kem object identifiers listed below, based on the security
level. The parameters field of the AlgorithmIdentifier for the ML-KEM
public key MUST be absent.

When any of the ML-KEM AlgorithmIdentifier appears in the
SubjectPublicKeyInfo field of an X.509 certificate, the key usage
certificate extension MUST only contain keyEncipherment
{{Section 4.2.1.3 of RFC5280}}.

~~~
  nistAlgorithms OBJECT IDENTIFIER ::= { joint-iso-ccitt(2)
    country(16) us(840) organization(1) gov(101) csor(3)
    nistAlgorithm(4) }

  kems OBJECT IDENTIFIER ::= { nistAlgorithms 4 }

  id-alg-ml-kem-512 OBJECT IDENTIFIER ::= { kems 1 }

  id-alg-ml-kem-768 OBJECT IDENTIFIER ::= { kems 2 }

  id-alg-ml-kem-1024 OBJECT IDENTIFIER ::= { kems 3 }

  pk-ml-kem-512 PUBLIC-KEY ::= {
    IDENTIFIER id-alg-ml-kem-512
    -- KEY no ASN.1 wrapping --
    PARAMS ARE absent
    CERT-KEY-USAGE { keyEncipherment }
    --- PRIVATE-KEY no ASN.1 wrapping --
    }

  pk-ml-kem-768 PUBLIC-KEY ::= {
    IDENTIFIER id-alg-ml-kem-768
    -- KEY no ASN.1 wrapping --
    PARAMS ARE absent
    CERT-KEY-USAGE { keyEncipherment }
    --- PRIVATE-KEY no ASN.1 wrapping --
    }

  pk-ml-kem-1024 PUBLIC-KEY ::= {
    IDENTIFIER id-alg-ml-kem-1024
    -- KEY no ASN.1 wrapping --
    PARAMS ARE absent
    CERT-KEY-USAGE { keyEncipherment }
    --- PRIVATE-KEY no ASN.1 wrapping --
    }

    ML-KEM-PublicKey ::= OCTET STRING

    ML-KEM-PrivateKey ::= OCTET STRING
~~~

No additional encoding of the ML-KEM public key value is applied in
the SubjectPublicKeyInfo field of an X.509 certificate {{RFC5280}}.
However, whenever the ML-KEM public key value appears outside of a
certificate, it MAY be encoded as an OCTET STRING.

No additional encoding of the ML-KEM private key value is applied in
the PrivateKeyInfo field of an Asymmetric Key Package {{RFC5958}}.
However, whenever the ML-KEM private key value appears outside of a
Asymmetric Key Package, it MAY be encoded as an OCTET STRING.

# Subject Public Key Fields

In the X.509 certificate, the subjectPublicKeyInfo field has the
SubjectPublicKeyInfo type, which has the following ASN.1 syntax:

~~~
  SubjectPublicKeyInfo {PUBLIC-KEY: IOSet} ::= SEQUENCE {
      algorithm        AlgorithmIdentifier {PUBLIC-KEY, {IOSet}},
      subjectPublicKey BIT STRING
  }
~~~

<aside markdown="block">
  NOTE: The above syntax is from {{RFC5912}} and is compatible with the
  2021 ASN.1 syntax {{X680}}. See {{RFC5280}} for the 1988 ASN.1 syntax.
</aside>

The fields in SubjectPublicKeyInfo have the following meaning:

* algorithm is the algorithm identifier and parameters for the
  public key (see above).

* subjectPublicKey contains the byte stream of the public key.

{{example-public}} contains examples for ML-KEM public keys
encoded using the textual encoding defined in {{?RFC7468}}.

# Private Key Format

In short, an ML-KEM private key is encoded by storing its 64-octet seed in
the privateKey field as follows.

{{FIPS203}} specifies two formats for an ML-KEM private key: a 64-octet
seed and an (expanded) private key, which is referred to as the
decapsulation key. The expanded private key (and public key)
is computed from the seed using `ML-KEM.KeyGen_internal(d,z)` (algorithm 16)
using the first 32 octets as *d* and the remaining 32 octets as *z*.

A keypair is generated by sampling 64 octets uniformly at random
for the seed (private key)  from a cryptographically secure
pseudorandom number generator (CSPRNGs). The public key can then
be computed using `ML-KEM.KeyGen_internal(d,z)` as described earlier.

"Asymmetric Key Packages" {{!RFC5958}} describes how to encode a private
key in a structure that both identifies what algorithm the private key
is for and allows for the public key and additional attributes about the
key to be included as well. For illustration, the ASN.1 structure
OneAsymmetricKey is replicated below.

~~~
  OneAsymmetricKey ::= SEQUENCE {
    version                  Version,
    privateKeyAlgorithm      SEQUENCE {
    algorithm                PUBLIC-KEY.&id({PublicKeySet}),
    parameters               PUBLIC-KEY.&Params({PublicKeySet}
                               {@privateKeyAlgorithm.algorithm})
                                  OPTIONAL}
    privateKey               OCTET STRING (CONTAINING
                               PUBLIC-KEY.&PrivateKey({PublicKeySet}
                                 {@privateKeyAlgorithm.algorithm})),
    attributes           [0] Attributes OPTIONAL,
    ...,
    [[2: publicKey       [1] BIT STRING (CONTAINING
                               PUBLIC-KEY.&Params({PublicKeySet}
                                 {@privateKeyAlgorithm.algorithm})
                                 OPTIONAL,
    ...
  }
~~~

<aside markdown="block">
  NOTE: The above syntax is from {{RFC5958}} and is compatible with the
  2021 ASN.1 syntax {{X680}}.
</aside>

When used in a OneAsymmetricKey type, the privateKey OCTET STRING contains
the raw octet string encoding of the 64-octet seed. The publicKey field
SHOULD be omitted because the public key can be computed as noted earlier
in this section.

{{example-private}} contains examples for ML-KEM private keys
encoded using the textual encoding defined in {{?RFC7468}}.

# Security Considerations

The Security Considerations section of {{RFC5280}} applies to this
specification as well.

Protection of the private-key information, i.e., the seed, is vital to
public-key cryptography.  Disclosure of the private-key material to another
entity can lead to masquerades.

For ML-KEM specific security considerations refer to
{{?I-D.sfluhrer-cfrg-ml-kem-security-considerations}}.

The generation of private keys relies on random numbers. The use of
inadequate pseudo-random number generators (PRNGs) to generate these
values can result in little or no security.  An attacker may find it
much easier to reproduce the PRNG environment that produced the keys,
searching the resulting small set of possibilities, rather than brute
force searching the whole key space.  The generation of quality
random numbers is difficult, and {{?RFC4086}} offers important guidance
in this area.

ML-KEM key generation as standardized in {{FIPS203}} has specific
requirements around randomness generation, described in section 3.3,
'Randomness generation'.

Key formats have implications on KEM binding properties, initially formalized
in {{CDM23}}. Per the analysis of the final {{FIPS203}} in {{KEMMY24}}, a
compliant instantiation of ML-KEM is LEAK-BIND-K-PK-secure and
LEAK-BIND-K-CT-secure when using the expanded key format, but not
MAL-BIND-K-PK-secure nor MAL-BIND-K-CT-secure. This means that the computed
shared secret binds to the encapsulation key used to compute it against a
malicious adversary that has access to leaked, honestly-generated key
material but is not capable of manufacturing maliciously generated
keypairs. This binding to the encapsulation key broadly protects against
re-encapsulation attacks but not completely.

Using the 64-byte seed format provides a step up in binding security by
mitigating an attack enabled by the hash of the public encapsulation key
stored in the expanded private decapsulation key format, providing
MAL-BIND-K-CT security and LEAK-BIND-K-PK security.

# IANA Considerations

For the ASN.1 Module in {{asn1}}, IANA is requested to assign an
object identifier (OID) for the module identifier (TBD) with a
Description of "id-mod-x509-ml-kem-2024".  The OID for the module
should be allocated in the "SMI Security for PKIX Module Identifier"
registry (1.3.6.1.5.5.7.0).


--- back


# ASN.1 Module {#asn1}

This appendix includes the ASN.1 module {{X680}} for the ML-KEM.  Note that
as per {{RFC5280}}, certificates use the Distinguished Encoding Rules; see
{{X690}}. This module imports objects from {{RFC5912}} and {{!RFC9629}}.

~~~
<CODE BEGINS>
{::include X509-ML-KEM-2024.asn}
<CODE ENDS>
~~~

# Security Strengths {#arnold}

Instead of defining the strength of a quantum algorithm in a traditional
manner using the imprecise notion of bits of security, NIST has
defined security levels by picking a reference scheme, which
NIST expects to offer notable levels of resistance to both quantum and
classical attack.  To wit, a KEM algorithm that achieves NIST PQC
security must require computational resources to break IND-CCA2
security comparable or greater than that required for key search
on AES-128, AES-192, and AES-256 for Levels 1, 3, and 5, respectively.
Levels 2 and 4 use collision search for SHA-256 and SHA-384 as reference.

<aside markdown="block">
  TODO: what should go in this table?
</aside>

| Level | Parameter Set | Encap. Key | Decap. Key | Ciphertext | SS |
|-      |-              |-           |-           |-           |-   |
| 1     | ML-KEM-512    | 800        | 1632       | 768        | 32 |
| 3     | ML-KEM-768    | 1184       | 2400       | 1952       | 32 |
| 5     | ML-KEM-1024   | 1568       | 3168       | 2592       | 32 |
{: #tab-strengths title="ML-KEM security strengths"}

# Examples {#examples}

This appendix contains examples of ML-KEM public keys, private keys and
certificates.


## Example Private Key {#example-private}

The following is an example of a ML-KEM-512 private key with hex seed `0001…3f`:

~~~
{::include ./example/ML-KEM-512.priv}
~~~

~~~
0  82: SEQUENCE
2   2:  INTEGER 0
5  11:  SEQUENCE {
7   9:   OBJECT IDENTIFIER '2.16.840.1.101.3.4.4.1'
     :   }
18 64:  OCTET STRING
     :    00 01 02 03 04 05 06 07 08 09 0a 0b 0c 0d 0e 0f
     :    10 11 12 13 14 15 16 17 18 19 1a 1b 1c 1d 1e 1f
     :    20 21 22 23 24 25 26 27 28 29 2a 2b 2c 2d 2e 2f
     :    30 31 32 33 34 35 36 37 38 39 3a 3b 3c 3d 3e 3f
     :  }
~~~

The following is an example of a ML-KEM-768 private key from the same seed.

~~~
{::include ./example/ML-KEM-768.priv}
~~~

~~~
0  82: SEQUENCE
2   2:  INTEGER 0
5  11:  SEQUENCE {
7   9:   OBJECT IDENTIFIER '2.16.840.1.101.3.4.4.2'
     :   }
18 64:  OCTET STRING
     :    00 01 02 03 04 05 06 07 08 09 0a 0b 0c 0d 0e 0f
     :    10 11 12 13 14 15 16 17 18 19 1a 1b 1c 1d 1e 1f
     :    20 21 22 23 24 25 26 27 28 29 2a 2b 2c 2d 2e 2f
     :    30 31 32 33 34 35 36 37 38 39 3a 3b 3c 3d 3e 3f
     :  }
~~~

The following is an example of a ML-KEM-1024 private key from the same seed.

~~~
{::include ./example/ML-KEM-1024.priv}
~~~

~~~
0  82: SEQUENCE
2   2:  INTEGER 0
5  11:  SEQUENCE {
7   9:   OBJECT IDENTIFIER '2.16.840.1.101.3.4.4.3'
     :   }
18 64:  OCTET STRING
     :    00 01 02 03 04 05 06 07 08 09 0a 0b 0c 0d 0e 0f
     :    10 11 12 13 14 15 16 17 18 19 1a 1b 1c 1d 1e 1f
     :    20 21 22 23 24 25 26 27 28 29 2a 2b 2c 2d 2e 2f
     :    30 31 32 33 34 35 36 37 38 39 3a 3b 3c 3d 3e 3f
     :  }
~~~

<aside markdown="block">
  NOTE: The private key is the seed and all three examples keys
  use the same seed; therefore, the private above are the same
  except for the OID used to represent the ML-KEM algorithm's
  security strength.
</aside>

## Example Public Key {#example-public}

The following is the ML-KEM-512 public key corresponding to the private
key in the previous section.

~~~
{::include ./example/ML-KEM-512.pub}
~~~

~~~
  0 818: SEQUENCE {
  4  11:   SEQUENCE {
  6   9:     OBJECT IDENTIFIER '2 16 840 1 101 3 4 4 1'
       :     }
 17 801:   BIT STRING
       :     39 95 81 5E 59 7D 10 43 55 CF 29 AA 53 33 C9 32
       :     51 86 9D 5B CD BE 48 71 24 F6 02 B8 B6 A6 6C 16
       :     C4 76 16 48 AD 76 5C F5 D8 00 6B 51 5E 90 5A 7F
       :     0A C0 76 B0 C6 2E FA 32 81 53 E7 CA 57 01 69 9F
       :     13 05 F1 E6 BC 6F 90 B0 E4 9B 69 35 12 B6 CE 99
       :     2A 8B 80 16 DD FC 1A 66 2C 7E 3F 96 19 CB D8 69
       :     DD 77 1A F3 08 96 CC D5 91 8A C6 CB 77 46 6C 5E
       :     77 99 96 D6 7F F9 AA BC 97 50 3F 2C 7B 7E 2D 00
       :     0D 86 45 0F B1 80 7C A4 CA BD A4 65 82 5A 31 C7
       :     89 A1 B7 A4 91 AB 38 72 76 5D 32 0D 0B 71 92 0F
       :     A2 13 C9 40 93 41 6B 83 B8 12 4E 69 F6 5E 62 CB
       :     50 00 DC C3 7A A9 A0 FF F7 39 70 C4 77 2F 35 7D
       :     24 18 9C A6 F5 30 55 68 C0 E2 37 6A 37 62 A6 8C
       :     60 5E 56 3C 5D 20 95 72 E0 FC 75 32 CA 29 47 29
       :     53 55 67 B5 FC 41 3C 5E 87 92 D2 46 45 36 CC 80
       :     8F 98 AD D7 46 64 F1 41 56 6F 90 16 A9 0A 54 18
       :     29 A9 8A 04 64 CE 41 A8 BB 44 C2 D4 FA 3C 2C 20
       :     94 60 72 8E F1 4A 1A 7C 4C 9B 98 D1 22 03 B4 CC
       :     35 29 16 0A 9A B2 D7 83 8F 7F F6 B5 3A E0 5A A3
       :     1A 7D 64 6B 7A FA 6C 45 93 25 26 A3 C3 75 56 19
       :     BE 99 4C 21 1C 2A 31 C0 5B 34 47 83 6C B2 15 0B
       :     E1 82 9D AE 6B 04 C5 53 5C FF 54 6E 39 2B A7 97
       :     41 17 20 F9 24 F4 90 A5 AC 54 95 F2 13 56 D5 50
       :     B7 82 A6 4C 16 88 B6 B6 55 BC C7 84 21 97 A4 34
       :     C2 F6 56 3B 5B 7F 09 A7 8B CC 48 82 32 78 35 61
       :     D1 6F 4C BA B6 75 54 00 05 07 81 57 0C 66 60 4B
       :     81 7A D1 25 22 94 73 6E 8B 01 86 1A 4B 5A 74 51
       :     9B 8B 6F E5 14 89 A5 07 23 92 E5 87 62 6C 71 37
       :     76 57 5D 33 80 6A 1C 8E 27 32 AF 97 C2 68 0F 51
       :     66 63 31 C4 EB 8B BC 04 31 C4 F9 68 32 DA F1 B3
       :     C4 55 28 FB A1 53 F6 C7 8B 1C 19 87 02 94 7C CD
       :     33 77 27 A4 6F B5 3B A1 1D E5 CB 41 91 34 68 59
       :     51 6C B6 AD 72 40 0F 3C F2 09 B2 36 AE F3 5A 58
       :     0A C8 7E B3 E3 0F AF D6 69 73 CA 8A 7D D2 67 5A
       :     F4 1F 7A 17 B6 14 33 CD 1A F8 0F 77 08 86 9F 66
       :     54 88 49 79 80 B1 AC 10 A0 CD CB 63 6A 00 ED 86
       :     81 B3 5E 42 91 24 CA 80 35 07 25 B8 5F 83 A5 EA
       :     C3 A4 A3 CC 16 00 90 3E 65 29 35 60 B9 B3 36 E5
       :     AF 0D 52 9D AC 1A 04 81 19 30 2C B7 A9 BC C1 10
       :     B9 48 51 BF 02 11 7F 19 9D C4 85 A8 52 B7 47 3F
       :     09 B8 31 A6 83 1D 5B 54 C0 B7 90 D2 25 CF 6B B9
       :     2D 94 62 A2 6C DB 33 DD A5 12 3C 7A AF 0E 26 A0
       :     B8 36 55 EE A2 8B F3 A8 07 47 25 01 8F D6 BA E4
       :     B6 01 CF 61 BA AB 71 A7 A3 D3 51 97 A3 43 E7 4B
       :     4A 27 2C 12 5D 54 08 96 42 6D 85 B7 95 8D 3B 38
       :     A6 BA 98 7E C3 72 25 C7 B4 4C DB 12 DD E4 53 9B
       :     4A B0 82 36 36 83 F0 4B F7 A0 9C C5 C4 1D FE 83
       :     0A 1B 16 2E 0B 32 43 34 36 2F 08 4A 14 46 77 23
       :     34 4B AD D0 00 F8 D8 C5 37 C4 8F 99 8F 05 30 7C
       :     EB D1 ED E0 B8 1C 3B C5 9A 06 5A 1B 6D 63 B2 6C
       :   }
~~~
<aside markdown="block">
NOTE: The padding byte of the DER-encoded BIT STRING is not displayed in the pretty print above.
</aside>

The following is the ML-KEM-768 public key corresponding to the private
key in the previous section.

~~~
{::include ./example/ML-KEM-768.pub}
~~~

~~~
   0 1202: SEQUENCE {
   4   11:   SEQUENCE {
   6    9:     OBJECT IDENTIFIER '2 16 840 1 101 3 4 4 2'
         :     }
  17 1185:   BIT STRING
         :     29 8A A1 0D 42 3C 8D DA 06 9D 02 BC 59 E6 CD F0
         :     3A 09 6B 8B 3D A4 CA B9 B8 0C A4 A1 49 07 67 2C
         :     CE F1 EC 4F AF 23 4A 0B C5 B7 E9 D4 73 F2 B3 13
         :     3B 3B 26 A1 D1 75 CB 67 A7 80 59 19 69 9C 02 F7
         :     65 31 B9 9C 5F 89 18 07 04 BB 4C A4 53 5C 5B 89
         :     72 67 9C 66 0A 07 C5 E5 14 B8 70 09 C8 62 EB 8F
         :     51 57 69 5E FB 3F C4 0A 9D EF 6B 81 C1 CC 02 A2
         :     49 AE 4F 09 4A D0 D9 BD 34 85 C1 C1 C6 80 80 52
         :     0A 7C 8C 63 20 32 CE E7 38 15 4E 5C 51 76 C0 7D
         :     A5 60 24 77 6A 43 0F E7 6E AC F6 65 A3 F7 B8 32
         :     10 22 15 BC 82 F1 09 39 C8 35 57 04 33 6A 8F AC
         :     1D 81 E4 BB 04 85 AA 5D 7C 74 D6 B5 9B BE 5C 5E
         :     97 2A 0D 8B AC 41 1B 55 B5 D5 55 7C D6 80 A1 A8
         :     F7 1B 4E B8 6B C4 8C 9A 05 09 73 1A 54 BD 9D 72
         :     90 B2 79 63 E4 37 2D C9 B1 99 CF DC AC 0B 01 AC
         :     D2 8A 62 39 51 12 E4 C4 36 48 D6 22 C4 8C 82 34
         :     D0 14 40 E8 CC 37 6C 92 7F 23 A5 AF C9 AC 04 74
         :     C6 62 27 4E 42 45 25 C8 55 2E CE 3B 3F E2 65 16
         :     DE 90 1B C7 D5 15 BD E8 95 58 E6 26 C9 5C 80 B9
         :     33 42 F8 01 00 04 F3 9E 6C 6C 94 87 1C 5E 34 4C
         :     AB 39 66 C8 35 F9 A9 6A 59 AF D3 1C 40 28 6B 38
         :     B1 C1 A7 84 70 BA B9 47 51 89 34 45 3C E8 67 36
         :     A9 19 F1 F5 A6 D5 10 A8 6F 54 54 FC 39 80 CB 5C
         :     76 5B D2 BD 5F 7B 36 B1 41 0D 66 35 C8 CE B4 7C
         :     4D DA 0D 76 A2 8E AC 93 9C 71 C3 02 48 04 86 6C
         :     71 62 66 58 44 21 63 C2 C2 21 17 E5 0A CE FC E6
         :     37 8A 98 56 52 30 2A 4E F0 C2 CE 0C C7 16 B7 79
         :     6E 2B 6B 2E 37 77 DF A1 AC 3D A2 59 A3 1B 5A 9B
         :     53 0F 8C B6 38 A8 1A 62 AC 30 18 49 AB AF 95 A7
         :     30 1B DA 30 06 89 09 BF DB 7E 67 DB CC BB 38 A5
         :     55 1A 25 B1 A3 A0 F6 85 74 8A D5 75 3D 88 80 F0
         :     01 6C 62 74 86 16 63 84 C5 57 1F E2 36 59 00 36
         :     4D 03 83 11 E2 D8 75 DB 36 66 86 93 2B 5E C6 02
         :     43 0A 36 9E 87 A6 EF 5C 33 87 86 65 78 25 BD 4C
         :     05 7A CE B9 23 EB 09 35 E6 90 5E 63 B4 CE D7 F8
         :     08 57 A7 73 DD 64 B1 50 D2 66 12 EA 9A C1 20 52
         :     DB 20 17 BF 18 43 CC B4 B3 28 1B 69 0D C7 28 AD
         :     FA 85 C0 02 81 B8 E3 C0 92 87 33 5F 85 6B 4F C2
         :     89 2F 69 A2 F5 79 21 AD A0 19 14 C4 09 88 66 2D
         :     57 76 96 62 A7 86 35 1B 9B 66 49 3D AB 79 59 4D
         :     98 6D E2 10 0D 65 BA 0F F4 EA 58 B8 15 38 D2 4A
         :     44 35 A2 58 FA C2 54 04 AA 7F 41 F6 58 B1 38 50
         :     65 E1 58 DC B6 01 15 73 27 20 F4 04 59 AA AC 15
         :     E4 06 95 3A 90 AC 52 99 7D 1C CD 07 00 60 EF C6
         :     5D B9 E6 53 35 44 67 FA D5 6E C7 13 C8 6E 75 40
         :     C4 23 AC F2 66 9F 52 FA 6F 4A C6 88 8D 87 1E F3
         :     E8 47 C0 29 A8 AA FB B9 2E 17 B2 4A A0 79 B1 F4
         :     19 BA 61 75 B4 42 AF B1 19 09 D4 A5 6B 70 A0 33
         :     5B 28 73 92 18 AA 7C 93 48 E2 C3 C2 F3 EB 3D 15
         :     A4 1E 64 17 C0 DD 94 BF EB 21 41 9B 31 1A 7B B1
         :     3A 18 0B BE 83 32 18 A9 A6 B1 74 47 CC 85 F2 25
         :     85 95 87 A7 30 77 04 9A CB CF D4 4D 0F 02 54 38
         :     E1 5D 15 38 27 0D 58 6E 1B F8 31 92 A9 45 9C F6
         :     3C 0E 97 2F 85 29 76 79 83 1E CF 12 15 09 85 1C
         :     B8 34 0F 6F 10 7B 0F A1 A0 EF D1 B3 6A 81 89 BC
         :     08 5C 4F 5C B7 84 E5 53 F4 1B 91 8F 80 39 7C E1
         :     95 6F 78 5B EE 37 7C A9 AA 8B E6 99 8A DA 30 C2
         :     6B 7C 3D 8C 6B 55 25 4C C9 62 03 B2 0C 42 AE E0
         :     AC 4E 1E BB 40 8E 49 A9 E3 F8 79 D0 AB 07 85 EB
         :     70 25 42 5D 13 05 A2 29 9C 01 5E 12 0D 16 3B 0E
         :     19 49 4C E5 72 53 D0 24 6D 18 27 45 CB 81 97 AB
         :     74 38 B3 C1 BB 79 72 BE C5 A3 06 EB A3 56 78 55
         :     C0 14 69 9F EF 65 AE 54 C7 70 A0 D8 5C 18 40 0C
         :     F6 42 AE DC 66 07 77 BA 4B 13 85 02 BD 5A 78 12
         :     F6 21 F8 4A 48 29 6B 98 DD 43 22 B6 F1 58 28 B8
         :     A8 F0 E0 0A 8B A4 4A 53 C3 A8 B1 43 57 1B 07 40
         :     AB D5 67 DA F1 CD E9 C7 9C 20 4B 6D 5E 25 9D 17
         :     66 A3 1B BB CB 4E 6A 05 CF 45 02 17 6B 30 1C 1C
         :     2F 41 24 77 50 15 7B CE C8 5E 80 9B 30 A4 D6 0D
         :     77 47 CD D0 F5 B9 9A A8 C8 26 98 75 17 79 3A AA
         :     80 80 A0 B1 24 A8 55 8D F7 2B BE 37 B7 5F 4E DB
         :     B6 BE 82 16 D6 C6 33 FB 2B 22 80 E2 51 13 D8 69
         :     5E 43 48 1C 3E EB 39 7E B1 92 50 52 29 B6 7A 20
         :     1E A8 93 C3 E2 CB 32 DA 8B C3 42 FA 4D EA 05 78
         :   }
~~~
<aside markdown="block">
NOTE: The padding byte of the DER-encoded BIT STRING is not displayed in the pretty print above.
</aside>

The following is the ML-KEM-1024 public key corresponding to the private
key in the previous section.

~~~
{::include ./example/ML-KEM-1024.pub}
~~~

~~~
   0 1586: SEQUENCE {
   4   11:   SEQUENCE {
   6    9:     OBJECT IDENTIFIER '2 16 840 1 101 3 4 4 3'
         :     }
  17 1569:   BIT STRING
         :     4B 94 C2 94 50 11 11 91 82 3B 35 14 C9 AC 1E A3
         :     D9 82 5C CB 86 39 3A 2D FB 04 65 4F A2 19 2D 37
         :     BF AD 1C 49 7C 65 02 EE E5 CA 80 A7 3B FC E0 BA
         :     F5 A5 4A 88 58 5A 40 13 97 A3 D2 32 F4 26 A7 AF
         :     B0 82 BC 21 A4 43 17 09 0E AA C7 59 2C 2E A8 8A
         :     65 3C 44 91 EA 19 39 31 33 5F 52 E9 89 A3 C4 CC
         :     56 D9 C5 53 73 2D 57 C4 70 FB 41 AB 75 9B 65 D2
         :     D0 44 45 38 2F CD 9C 4E 34 4A 11 28 FA 9E 11 E0
         :     43 58 E1 92 ED 01 4B 23 23 2A 7E E2 B2 2E 23 71
         :     7F 44 11 1E E3 35 75 39 9C 37 64 6D A9 81 3E C9
         :     B2 12 AF E9 4E 5D C5 C2 33 0A 72 94 CC 1F 42 34
         :     A6 D3 FB B4 F1 68 5A B8 89 2C 04 AC B1 7C D1 C1
         :     70 D7 B0 61 1B 6A 71 76 C7 94 CC 8C 67 F5 5F C9
         :     23 C2 AD 20 31 00 F3 65 99 18 82 C3 02 43 D7 78
         :     13 84 3B 5E C7 C9 64 03 22 63 70 60 92 EC F0 0C
         :     75 16 BE 64 E4 59 8C A4 22 6C 06 9B B5 E6 7E 41
         :     75 CF 22 86 C8 DD 5C 48 8A 6C 58 61 F3 1B AA 0B
         :     D0 26 94 70 E8 B5 51 DD 3B CD 38 C8 6C 12 F9 CD
         :     B1 76 C7 7D C8 B6 C0 2A 70 1F 47 89 02 C8 55 3F
         :     69 4C 0D 82 72 7B 4C 4A 5C 2C 10 41 21 2A A1 27
         :     48 08 B8 21 11 B3 77 EC 75 21 4E 9B 19 78 F7 60
         :     04 D4 13 9D 98 61 3F 4B 8E 98 D2 0A F7 B5 34 07
         :     3A 50 9A 95 9B 7A 75 64 F9 B4 0C A2 18 BF 61 82
         :     93 20 A8 50 20 17 95 4D 32 8D 7A C6 C7 69 EC 29
         :     70 07 56 E7 B0 68 5B 34 0D 5E 11 80 59 50 4A 49
         :     A9 A5 0A 10 19 8E B1 0A 57 84 67 8E B4 27 D7 B4
         :     BA BB 95 52 93 3B 06 28 97 97 3E 13 18 EA F0 A0
         :     EA C3 75 84 A6 54 01 B1 70 3E 04 2A CC D8 37 53
         :     14 83 F2 41 CA DC D1 C1 D3 78 11 9E 69 44 29 DB
         :     19 9A C8 91 E4 C5 34 37 57 08 5B B3 AE 78 36 67
         :     35 0C 44 58 D9 76 72 E8 61 E8 0B 1D 26 79 51 0E
         :     A3 A6 F2 36 0C 77 A4 69 42 C7 A0 6A 55 4D 22 80
         :     80 C8 4B 47 AE F1 4D B1 76 20 CB 16 C0 6A B3 0A
         :     1B E4 CD A7 08 2B E9 F8 7E 9C 21 1C 46 91 63 49
         :     A5 BA 8E AA 52 01 C7 29 4A 3C 08 85 B5 3B 65 74
         :     52 10 88 25 EC 64 6C 90 A0 46 12 32 4E E7 D0 31
         :     AF E5 34 31 32 CB EF 67 B6 EF B1 A5 EC 28 09 B7
         :     73 53 8C E7 7B 3D 8B 04 EB 0B 3C 22 56 01 1E 4C
         :     71 6C 19 A8 BA 07 52 BF 71 49 21 17 64 9F 06 15
         :     C3 29 0F C2 9A 46 FD E4 BD 52 DB 92 86 D6 03 38
         :     82 44 25 9C 15 A7 AC 2B 64 0A 60 CC 03 37 6A 58
         :     41 A3 FB 8A 47 35 68 FA 9B 1A 26 72 15 F3 4C 01
         :     69 7B 0F 0E 62 71 75 D7 21 05 B7 70 7C 29 B9 E6
         :     14 BD C3 3A 6F 6C 81 8A 95 37 0B 42 78 82 D7 B4
         :     76 79 6A 9E C6 EB 99 32 74 CD 9B 23 91 A8 2B A4
         :     5E 33 93 D2 E9 AE 97 21 CA 9D 6C 1B 98 8B 58 27
         :     71 3F 90 A6 58 5D E9 43 35 28 C0 2B 03 CE 10 BB
         :     5F 72 01 38 D0 FB B4 C3 0C 12 66 B9 18 E5 29 25
         :     DF E1 7B 37 F9 5D 22 BC A5 4F 47 59 19 AC 85 90
         :     98 C0 F0 D0 8A C5 87 5E F2 9B 56 FD 14 1E 6E F1
         :     5F 70 0A 0B 66 F3 95 95 C5 88 17 73 73 C4 66 9B
         :     21 BC 07 1E 4C 3A A5 F0 B4 A3 1B 62 58 F3 5D A2
         :     4A C3 CD 29 C7 F2 09 24 10 C5 07 83 55 B1 38 FB
         :     53 A6 B9 AE 6E 0B 9C 08 24 3E 7B AA 45 C4 73 76
         :     EB 8C 7F 13 D4 CF 51 AA 73 6F A3 15 40 C9 24 1F
         :     37 0D A5 44 BF 9F 9C 28 D9 A5 7E 2F 2A 7C A9 5A
         :     4E 4B 46 6E 64 1A B3 BC C7 6A DF 11 39 D5 67 A6
         :     F1 2B 52 F3 A6 5E 7E C0 AA E2 6B CA A8 C5 58 33
         :     B0 4E 59 99 8E BC 9A 19 30 FB B6 D2 23 3C 53 D2
         :     C1 F8 B9 51 8E 3C 2D E7 3A 19 DE E6 B3 80 A5 B3
         :     29 71 CF 64 E1 29 FD 6C 1F A6 E7 5D 4A 23 45 01
         :     E9 66 DD 3A 54 0A F5 C8 F4 F3 4A 6B 4A 25 3E E2
         :     84 92 56 6D 5E 67 C6 F5 58 55 FC B0 50 6F B0 6C
         :     15 67 44 D9 A0 3A 31 A2 6F A9 4C AD 14 F1 57 B7
         :     F3 03 D0 7A 69 C7 73 76 8F CB 4D 07 9C 09 05 97
         :     03 A0 C3 A9 4D E4 B9 9E A3 A2 F1 65 83 D0 F9 17
         :     0A 39 50 DB 07 B4 F0 BC 30 80 29 27 F9 F7 96 1B
         :     62 59 89 26 36 A9 50 2A 27 05 30 36 37 79 9D D3
         :     44 DA 45 1C 1C F7 BF 67 84 0C EB 30 79 AB 8C 6B
         :     8C 19 27 F6 40 53 C6 12 45 0C 45 C9 E6 03 BC 16
         :     66 6E 59 6B 34 71 E1 03 B6 F1 54 47 42 4D 17 02
         :     20 48 11 1F FB D3 7E 1C 67 0F 64 F1 4B 8A 7B 32
         :     B9 4C 1A 49 B4 5D D2 FC 38 CD 52 89 D9 10 AD 63
         :     60 2C F5 E1 30 42 C6 4A C6 79 7B 89 FB 55 1A D0
         :     8E 05 A9 2D 20 0C CC B7 E7 12 EF 23 C9 31 2C B3
         :     50 F0 29 AB 53 7E 28 73 47 FD 30 75 AC 10 90 6A
         :     78 3F 1C 6C 07 CC B8 8F 41 22 8C 4B E1 C6 40 F7
         :     90 B5 C3 A5 D5 D3 CA 79 24 95 D7 4B C4 61 56 26
         :     58 C0 7A C6 00 27 6B 92 4A B5 BC 9B E1 F0 49 4C
         :     B7 6F 82 F4 60 A7 48 09 72 66 33 81 E1 69 99 60
         :     61 D7 99 85 9E C5 4D 4F 5C A5 C4 11 C0 1D B1 59
         :     7B 16 59 77 66 9D E1 3A 92 8A 34 AF BA C2 58 FE
         :     A8 C4 76 42 39 C9 42 1D C3 11 9B F5 B4 76 99 20
         :     69 78 32 7B 1C 53 45 EF 74 6A 79 83 84 1F 05 6E
         :     25 34 10 0A B2 4D 4E 9A BB D0 B1 7C 6A 95 BD 4C
         :     3C 0E 40 F6 9E 16 12 AC EE B2 8B 99 08 6C 95 11
         :     6E 72 04 27 38 93 39 0B F4 6B 89 9B 36 28 6B 0E
         :     BF 19 47 BB 98 84 F7 32 CA 27 DA 82 B1 9B 5D C0
         :     CC 7F 88 85 71 49 10 88 8B 23 10 C4 F9 31 9D 41
         :     0B 34 E6 43 3B 90 03 E2 17 6B B9 95 25 74 56 10
         :     6E 89 52 16 3B 8B A5 92 53 0C C5 AA 0A EB 43 AD
         :     39 8F E9 E9 7B AA 52 3D 7A 44 31 67 7C 3D 3A F0
         :     71 9E 47 5D B8 5C A9 5A F5 08 9B EA BE B0 5B 2F
         :     AA B4 89 6B A6 0F 81 C8 84 72 A5 7B 46 A8 28 82
         :     6A 0C DF B4 46 F8 18 91 82 D2 BF 5E AC 4E C1 CC
         :     5D EA F5 99 C8 A1 3E 48 23 54 06 D1 7F FD DC 83
         :     44 B6 C6 69 84 A8 68 AA 92 FA 02 22 7A 08 69 50
         :     EB 0C 87 01 ED 58 DC 62 87 76 B9 83 88 2E 11 75
         :   }
~~~
<aside markdown="block">
NOTE: The padding byte of the DER-encoded BIT STRING is not displayed in the pretty print above.
</aside>

The following example, in addition to encoding the ML-KEM-768 private key,
has an attribute included as well as the public key:

~~~
  -----BEGIN PRIVATE KEY-----
  TODO insert example private key with attribute
  -----END PRIVATE KEY-------
~~~

## Example Certificate {#example-certificate}

~~~
  TODO insert ASN.1 Pretty Print
~~~

~~~
  -----BEGIN CERTIFICATE-----
  TODO Certificate
  -----END CERTIFICATE-------
~~~

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
