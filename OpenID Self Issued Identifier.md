# OpenID Self Issued Identifiers

## Warning

This document is not an OIDF International Standard. It is distributed for review and comment. It is subject to change without notice and may not be referred to as an International Standard.

Recipients of this draft are invited to submit, with their comments, notification of any relevant patent rights of which they are aware and to provide supporting documentation.

## Copyright notice & license
The OpenID Foundation (OIDF) grants to any Contributor, developer, implementer, or other interested party a non-exclusive, royalty free, worldwide copyright license to reproduce, prepare derivative works from, distribute, perform and display, this Implementers Draft or Final Specification solely for the purposes of (i) developing specifications, and (ii) implementing Implementers Drafts and Final Specifications based on such documents, provided that attribution be made to the OIDF as the source of the material, but that such attribution does not indicate an endorsement by the OIDF.

The technology described in this document was made available from contributions from various sources, including members of the OpenID Foundation and others. Although the OpenID Foundation has taken steps to help ensure that the technology is available for distribution, it takes no position regarding the validity or scope of any intellectual property or other rights that might be claimed to pertain to the implementation or use of the technology described in this specification or the extent to which any license under such rights might or might not be available; neither does it represent that it has made any independent effort to identify any such rights. The OpenID Foundation and the contributors to this specification make no (and hereby expressly disclaim any) warranties (express, implied, or otherwise), including implied warranties of merchantability, non-infringement, fitness for a particular purpose, or title, related to this specification, and the entire risk as to implementing this specification is assumed by the implementer. The OpenID Intellectual Property Rights policy requires contributors to offer a patent promise not to assert certain patent claims against other contributors and against implementers. The OpenID Foundation invites any interested party to bring to its attention any copyrights, patents, patent applications, or other proprietary rights that may cover technology that may be required to practice this specification.



## Foreword

The OpenID Foundation (OIDF) promotes, protects and nurtures the OpenID community and technologies. As a non-profit international standardizing body, it is comprised by over 160 participating entities (workgroup participants). The work of preparing implementer drafts and final international standards is carried out through OIDF workgroups in accordance with the OpenID Process. Participants interested in a subject for which a workgroup has been established has the right to be represented in that workgroup. International organizations, governmental and non-governmental, in liaison with OIDF, also take part in the work. OIDF collaborates closely with other standardizing bodies in the related fields.


## Introduction



### Notational Conventions

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT",
"SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and
"OPTIONAL" in this document are to be interpreted as described in BCP
14 [RFC2119] [RFC8174] when, and only when, they appear in all
capitals, as shown here.

# **OpenID Self Issued Identifiers **

[TOC]

## 1. Scope

This document specifies the methods for an application to:
* support self-issued identifiers in an OpenID Connect environment 
* enable a strong identifier that can survive changes to current authentication methods.
* format tokens that are bound to the issuer and recipient. (It does not support bearer tokens)

## 2. Normative references
The following referenced documents are indispensable for the application of this document. For dated references, only the edition cited applied. For undated references, the latest edition of the referenced document (including any amendments) applies.

[BCP14] - Key words for use in RFCs to Indicate Requirement Levels
[BCP14]: https://tools.ietf.org/html/bcp14

[RFC6749] - The OAuth 2.0 Authorization Framework
[RFC6749]: https://tools.ietf.org/html/rfc6749

[BCP212] - OAuth 2.0 for Native Apps
[BCP212]: https://tools.ietf.org/html/bcp212

[RFC6819] - OAuth 2.0 Threat Model and Security Considerations
[RFC6819]: https://tools.ietf.org/html/rfc6819

[RFC8259] - The JavaScript Object Notation (JSON) Data Interchange Format
[RFC8259]: https://datatracker.ietf.org/doc/rfc8259/

[BCP195] - Recommendations for Secure Use of Transport Layer Security (TLS) and Datagram Transport Layer Security (DTLS)
[BCP195]: https://tools.ietf.org/html/bcp195

[OIDC] - OpenID Connect Core 1.0 incorporating errata set 1
[OIDC]: https://openid.net/specs/openid-connect-core-1_0.html

[OpenID.Discovery] - OpenID Connect Discovery 1.0
[OpenID.Discovery]: https://openid.net/specs/openid-connect-discovery-1_0.html

[OpenID.Registration] - OpenID Connect Registration 1.0
[OpenID.Registration]: http://openid.net/specs/openid-connect-registration-1_0.html

[MTLS] - OAuth 2.0 Mutual TLS Client Authentication and Certificate Bound Access Tokens
[MTLS]: https://tools.ietf.org/html/draft-ietf-oauth-mtls

[JWT] - JSON Web Token
[JWT]: http://tools.ietf.org/html/draft-ietf-oauth-json-web-token

[JWS] - JSON Web Signature
[JWS]: http://tools.ietf.org/html/draft-ietf-jose-json-web-signature

[JWE] - JSON Web Encryption
[JWE]: http://tools.ietf.org/html/draft-ietf-jose-json-web-encryption

[JWA] - JSON Web Algorithms
[JWA]: http://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms


## 3. Terms and definitions
For the purpose of this document, the terms defined in [RFC6749], [OpenID Connect Core][OIDC] apply.

**Authenticator** - A collection of software and hardware that can be used to authenticate the user

**UA** - User Agent, which is the holder of the credential used by the user to authenticate themselves and issue authorization tokens to the Resource Servers authorizing access to user Personal Information.

## 4. Symbols and abbreviated terms

**CP** - any of Claim Provider, Certificate Provider, Credential Service Provider

**HTTP** - Hyper Text Transfer Protocol

**TLS** - Transport Layer Security

**URL** - Uniform Resource Locator

## 5. OpenID Self Issued Identifiers

### 5.1 Introduction

Personal information can be stored in various places : locally or at various online resource servers. The document targets the use of the [OIDC] (section 7) Self Issued Identifier to control access to that personal information by providing the means to those resource servers to recognize the user's intent to consent to enable access to that Personal information. Note that Claims providers can be used by the User Agent to acquire claims to be included in the Az Token. This document assumes that the "sub" element continues to be a key id as specified in the OpenID Connect specification.  (nb -- that last statement is subject to review)

OpenID Self Issued Identifiers provides a way for a user to exercise fine grained control over who can see their identifier as well as have access to their personal resources even as their current authenticators become inadequate to the task for any reason.

### 5.2 Subject Identifier

The existing [OIDC] core spec makes the SUB in the self-issued section 7 a key ID which makes it different than any other part of the spec, and actually in violation of some of the other assertions about the SUB in that doc. This document will consider that a key id can still be used in this sense, but adds a minor breaking change to say that the key ID MUST NOT include a colon (":"). Then any URL can be used to allow the SUB to serve as a real subject ID. In particular this allows a `did:` URL prefix to refer to the [DID-CORE]. This URL must provide a method to securely recover the key(s) that are used to sign this [OIDC] JWT.

The other potential breaking change would be the use of a different redirection method in place of, or as well as, the self-issued.me in the core spec.

### 5.3 Recovery

This specification assumes that the user has continuing needs to use their identifiers to access their personal resources, even as the authentication factors become obsolete or are lost. The following are some of the known problems with loss of use of an authentication factor include:
* 1 The device holding the authentication factor is lost.
* 2 The algorithm or key strength is declared obsolete or unable to continue to protect the user's authentication factors.
* 3 A key has become compromised or is otherwise no longer usable.
* 4 The user wishes to enable more than one authenticator to access their resources.

For these or any other reason the user needs to enable a different key and bind that key to their resources. This document is designed to provide secure means for that recovery.

Note that recovery of the user access, which is the topic of this specification, may be a part of a larger recovery of user data.

(The point here was to include some of the recovery methods, which i know Markus was looking at. My question is whether any recovery mechanism should be normative.  (My guess is that they are all non-normative and belong in a section below.)
Some thoughts:
* Key is stored in a pfx file that is encrypted to a user secret.
* Key is contained in a remove data store and can be restored if the user is authenticated.
* Key could be shared between multiple user devices and any one could back up the other. (Android already enables this.)
* A new key could be generated and bound the the user's PUID.
* WebAuthn dual PRF function that takes two salts are returns two keys. That with 2FA to get decrytption key for user secret.
* OpenID Moderna Account forward spec OpenID Connect Account Porting https://openid.net/specs/openid-connect-account-porting-1_0.html
* John Bradely: You could also include the WebAuthn credentialID in the did document as part of bootstraping info. Only the person with the authenticator would be able to decrypt the user secrets into the wallet.
The following two helper functions can be used to enable recovery.
* A FIDO / Web AuthN key is created on a key fob that can be used to initialize the user's identity on any device.
* A QR could could be used to provide the secret information to unlock the user's secrets.

### 5.x Persistent User ID

(The need for this section is up for reconsideration)

The [OIDC] specification defines the format of the subject ID [sub] to be a type of key id. That can be used for the life of the key, but when any of the above recovery conditions are triggered the user that wants to have continued access to the resources that were bound to that key must have some means to seamlessly maintain that access.

Wherever a self-issued ID meets the requirement for recovery, it will be carried in the transmission packets as a [puid]. The data in the field must be structured as a URL or URI. The intention is that the [puid] would survive any of the recovery conditions listed in section 5.2. It is possible that the nature of the PUID binds it to authentication methods that become compromised or obsoleted. In that case it may not be possible for the PUID to be reused.

Resolution of the PUID into a set of current keys and authenticators -- is a nice to have, but is it required for this purpose??

### 5.4 Token Niblet (searching for an acceptable alternative name)

A collection of attributes or other data elements and are collected together into a signed jose structure. Encryption may also be added but should probably be discouraged. The header of the token niblet must identify the signer and cryptography used. This has he same structure as an encoded and signed jwt, but is intended to be embedded inside a jwt. A token niblet is always treated like a string in the enclosing jwt.

### 5.5 IdToken

This is the only token created by a User Agent to carry identifier information for a user. It is based on the [OIDC] IdToken and includes all of the mandatory fields from that token. In this specification it is always signed by the key identified in the sub element and always sent as a jose token. It may optionally be encrypted.

The concept of ephemeral id endpoint for temporary storage of iID Tokens as well as the use of references to ID Tokens needs to be discussed.

### 5.6 Flows

The only flow considered in the core spec is implicit, where the user device sends a single ID Token that is the sum of all the claims or grants that a user is providing the RP.

An alternate flow is the device flow where the user is operating on a large form device (like a laptop) using their phone as an authenticator.

### 5.7 Formats
The current jwt format would remain as the required format, but others could be standardized including:
* json-ld
* cbor
* cbor-ld. https://docs.google.com/presentation/d/1ksh-gUdjJJwDpdleasvs9aRXEmeRvqhkVWqeitx5ZAE/edit#slide=id.gc6f9e470d_0_0

# 6. Security Considerations

TK

# 7. Privacy Considerations

The user interchanges that carry user private information must be encrypted. This may be by TLS or by encrypted jose packets. (question, should pls or alternate be MUST?)

# 8. IANA Considerations
* puid -  as an element
* guid - as a URI

# 9. References
## 9.1 Normative References

[DID-CORE] - Decentralized Identifiers (DIDs) v1.0. Drummond Reed; Manu Sporny; Markus Sabadello; Dave Longley; Christopher Allen; Jonathan Holt. W3C. 31 July 2020. W3C Working Draft.
[DID-CORE]: https://www.w3.org/TR/did-core/

## 9.2 Non-normative References

[Did Recovery] - DIF I&D WG: Starting work on cryptographic secret recovery
[Did Recovery]: https://medium.com/decentralized-identity/dif-id-wg-starting-work-on-cryptographic-secret-recovery-204117b6a2ab

[Fuzzy Vault] - Fuzzy Vault Encryption (contributed by Microsoft):
[Fuzzy Vault]: https://github.com/decentralized-identity/fuzzy-encryption/blob/master/fuzzy-encryption-construction.pdf

[Horcrux] - Horcrux Protocol (contributed by VeridiumID):
[Horcrux]: https://github.com/decentralized-identity/horcrux/

[SeedQuest] - SeedQuest has been discussed quite a bit on the various DIF calls:
[SeedQuest]: https://github.com/reputage/seedQuest

[DCM] - There is also some work on "Decentralized Key Management Systems" (DKMS) in Hyperleder Indy:
[DCM]:https://github.com/hyperledger/aries-rfcs/blob/master/concepts/0051-dkms/dkms-v4.md

[Claims Aggregation] - Claims Aggregation
[Claims Aggregation]: https://bitbucket.org/edmund_jay/oidc-claims-aggregation/src/master/OpenID%20Connect%20Claims%20Aggregation.md

[Client Bound Assertions] - Client Bound Assertions
[Client Bound Assertions]: https://mattrglobal.github.io/oidc-client-bound-assertions-spec/

# Authors

* Tom Jones
* Tobias Looker

