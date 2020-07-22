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

**Auenticator** - A collection of software and hardware that can be used to authenticate the user

**UA** - User Agent, which is the holder of the credential used by the user to authenticate themself and issue authorization tokens to the Resource Servers authorizing access to user Personal Information.

## 4. Symbols and abbreviated terms
ticate a user.

**HTTP** - Hyper Text Transfer Protocol

**TLS** - Transport Layer Security

**URL** - Uniform Resource Locator

## 5. OpenID Self Issued Identifiers

### 5.1 Introduction

Personal information can be stored in various places : locally or at various online resource servers. The document targets the use of the [OIDC] (section 7) Self Issued Identifier to control access to that personal information by providing the means to those resource servers to recognize the user's intent to conset to enable access to that Personal information. Note that Claims providers can be used by the User Agent to aquire claims to be included in the Az Token. This document assumes that the "sub" element continues to be a key id as specified in the OpenID Connect specification.  (nb -- that last statemen is subject to review)

OpenID Self Issued Identifiers provides a way for a user to exercise fine grained control over who can see their identifier as well as have access to their personal resources even as their current authenticators become inadequate to the task for any reason.


### 5.2 Recovery

This specification assumes that the user has continuing needs to use their identifiers to access their personal resources, even as the authntiction factors become obsolete or are lost. The following are some of the known problems with loss of use of an authentication factor include:
#### 1 The device holding the authentication factor is lost.
#### 2 The algorithm or key strength is declared obsolete or unable to continue to protect the users factors.
#### 3 A key has become compromised or is otherwise no longer usable.
#### 4 The user wishes to enable more than one authenticator to access their resources.

For these or anyother reason the user needs to enable a different key and bind that key to their resources. This document is desinged to provide secure means for that recovery.

(The point here was to include some of the recovery methods, which i know markus was looking at. My question is whether any recovery mechanism should be normative.  My guess is that they are all non-normative and belong in a secion below.)

### 5.3 Persistant User ID

The [OIDC] specification defines the format of the subject ID [sub] to be a type of key id. That can be used for the life of the key, but when any of he above recovery condistions are triggered the user that wants to have continued access to the resources that were bound to that key must have some means to seamlessly maintain that access.

Wherever a self-issued ID meet the requiremet for recovery, it will be carried in the transmission packets as a [puid]. The data in the field must be structured as a URL or URI. The intention is that the [puid] would servive any of the revcovery conditions listed in section 5.2. It is possible that the nature of the PUID binds it to authentiction methods that become compromised or obsoleted. In that case it may not be possible for the PUID to be reused.

Resolution of the PUID into a set of current keys and authenticators ---  is a nice to have, but is it required for this purpose??

# 6. Security Considerations

The user interchanges that carry user private information must be encrypted. This may be by TLS or by encrypted jose packets.

# 7. Privacy Considerations

The user interchanges that carry user private information must be encrypted. This may be by TLS or by encrypted jose packets.

# 8. IANA Considerations
puid -  as an element
guid - as a URI

# Authors

* Tom Jones
