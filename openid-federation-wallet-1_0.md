%%%
title = "OpenID Federation Wallet Architectures 1.0 - draft 02"
abbrev = "openid-federation-wallet"
ipr = "none"
workgroup = "OpenID Connect A/B"
keyword = ["security", "openid", "ssi"]

[seriesInfo]
name = "Internet-Draft"
value = "openid-federation-wallet"
status = "standard"

[[author]]
initials="G."
surname="De Marco"
fullname="Giuseppe De Marco"
organization="Dipartimento per la trasformazione digitale"
    [author.address]
    email = "gi.demarco@innovazione.gov.it"

[[author]]
initials="R."
surname="Hedberg"
fullname="Roland Hedberg"
organization="Catalogix"
    [author.address]
    email = "roland@catalogix.se"

[[author]]
initials="M.B."
surname="Jones"
fullname="Michael B. Jones"
organization="Self-Issued Consulting"
    [author.address]
    email = "michael_b_jones@hotmail.com"

[[author]]
initials="J."
surname="Bradley"
fullname="John Bradley"
organization="Yubico"
    [author.address]
    email = "ve7jtb@ve7jtb.com"

%%%

.# Abstract

This specification defines the OpenID Federation entity types for
digital wallet architectures.

{mainmatter}

# Introduction

As digital wallets become increasingly deployed for managing identity credentials,
establishing an architecture for trusted communication is required to allow each
participant in the ecosystem to evaluate other participants' compliance with mutual trust frameworks
and accomplish secure and trusted transactions.

This specification defines how to use OpenID Federation 1.0 [@!OpenID.Federation] to enhance the
security and interoperability of wallet ecosystems, facilitating trust establishment
among the parties and enabling secure metadata exchange and policy
application across large scale deployments.
It outlines the general architecture of a federated trust
infrastructure for wallet ecosystems, identifying participant roles and describing
the use of those roles.

Additionally, this specification provides practical examples of how to apply
policies for typical use cases within wallet ecosystems.
Finally, it offers guidance on defining trust marks for use within
wallet ecosystems.

## Requirements Notation and Conventions

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD",
"SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in
this document are to be interpreted as described in BCP 14 [@!RFC2119]
[@!RFC8174] when, and only when, they appear in all capitals, as shown here.

## Warning

This document is not an OpenID Foundation International Standard.
It is distributed for review and comment.
It is subject to change without notice and may not be referred to as an International Standard.

# Scope

This specification is a profile of [@!OpenID.Federation] for wallet ecosystems.
It defines entity types for entities participating in those ecosystems.
It describes trust evaluation mechanisms for those entities.
It uses applicable metadata parameters defined by other specifications
for wallet entities.

Collaboration Note: When a metadata parameter is needed for an Entity Type
defined by this specification that does not currently exist and
that would be usable by wallet ecosystems both using and not using OpenID Federation,
it is the editors' intent to work with the working groups creating
general-purpose wallet specifications to define those new parameters there.

# Terminology

This specification uses the terms
"End-User" and "Entity" as defined by OpenID Connect Core [@!OpenID.Core],
"JSON Web Token (JWT)" defined by JSON Web Token (JWT) [@!RFC7519],
"CBOR Web Token (CWT)" defined by CBOR Web Token (CWT) [@!RFC8392],
"Client" as defined by [@!RFC6749],
"Verifiable Presentation" and "Wallet Attestation" defined in [@!OpenID4VP],
"Holder" and "Credential Issuer" defined in [@!OpenID4VCI],
and "Trust Mark", "Federation Entity", "Trust Anchor",
"Intermediate", and "Subordinate Statement" defined in [@!OpenID.Federation].

This specification also defines the following terms:

Organizational Entity:
: A Federation Entity represented by a legal entity, specifically referring to public or private organizations (excluding natural persons) recognized through a unique identifier. For the purposes of this specification, an Organizational Entity is also referred to as an Organization.

Personal Device:
: Any electronic device that is primarily used by an individual. This includes smartphones, tablets, laptops, personal computers, smart watches, and other wearable technologies. Personal Devices are owned and managed by End-Users as individuals, rather than by Organizations.

Wallet Provider:
: An Organizational Entity responsible for the development, publication, and management of a Wallet Solution.

Wallet Instance:
: Instance of a Wallet Solution belonging to and controlled by a person, be this natural or legal. It enables the request, storage, presentation, and management of Digital Credentials. It can be installed (instantiated) in a Personal Device or in a Remote Service.

Wallet Solution:
: The Wallet Solution is a product offered by a Wallet Provider to enable End-Users to securely manage and use their Digital Credentials. It is delivered by the Wallet Provider in the form of mobile app or cloud service or another form of software application. It may also utilize services and web services for the exchange of data between its Wallet Provider and the Wallet Instances.

Authentic Source:
: A protected Resource Server, not necessarily an OAuth 2.0 Resource Server, utilized by the Credential Issuer to retrieve the data necessary for issuing a Credential related to a subject.

Credential Verifier:
: Entity that requests and verifies Digital Credentials presented by a Holder. 

Credential Verifier Instance:
: A software application that allows an individual to request to an Holder and receive from that Holder a Digital Credential, sometimes in a proximity flow, and then verify the received Digital Credential.

## Trust Models and Trust Frameworks

The terms "trust model" and "trust framework" are often used in the context of security, identity management, and federation systems.

The Trust Model defines the relationships and mechanisms through which trust is established and maintained between entities in a system. It outlines how entities interact, the basis on which they can trust each other, and the roles they play within the system. Trust Models can be simple or complex, depending on the number of parties involved and the nature of their interactions. Common examples include:

- **Direct Trust**: Trust is established directly between two parties without intermediaries.
- **Third-Party Trust**: Trust is facilitated by a trusted third party.
- **Web of Trust**: Each participant makes individual decisions about whom to trust, using Direct Trust or potentially multiple Third-Parties.

**Third-Party Trust** is the focus of this specification, although the **Web of Trust** model is not excluded if multiple trusted third parties (Trust Anchors) are supported by the participants.

A Trust Framework is a comprehensive structure that includes policies, standards, and guidelines that govern the implementation of a Trust Model. It provides detailed rules for how trust should be managed, including the legal, technical, and procedural aspects. To allow for a scalable approach, as many aspects of the framework as possible should be presented in a machine discoverable and machine-readable way.

In the scope of this specification, only the technical and procedural aspects are considered and fully covered.

OpenID Federation [@!OpenID.Federation] is a building block for assembling and using trust frameworks. It can help ensure that all participants in a system understand and adhere to the same principles and practices, making interactions predictable and secure.

# The Four-Party Model

The Four-Party Model is a framework involving four key participants:
the Holder, the Credential Issuer, the Credential Verifier,
and an Entity trusted by the other Entities called the Trust Anchor.
This is an extension of the three-party Issuer-Holder-Verifier Model described in
[@!OpenID4VCI] and [@!OpenID4VP] that adds a fourth party: the Trust Anchor.
The four Entities interact with each other as described below:

1. **Holder**: The Holder requests, stores, presents, and manages Digital Credentials and other forms of digital attestations. It discovers trustworthy Credential Issuers through the Trust Anchor and its Intermediates. Additionally, the Holder evaluates trust with Credential Verifiers recognized by the Trust Anchor and its Intermediates and checks for the non-revocation of the other Entities in use.
2. **Credential Issuer**: This Entity issues Digital Credentials to the Holder, after having evaluated the trust in the Wallet Solution and the security of the Holder.
3. **Credential Verifier**: This is any Entity that requires proof of the End-User's identity, through the presentation of Credentials, to provide services or carry out transactions. Credential Verifiers rely on the validity of the Digital Credentials presented via the End-User's Wallet. They MUST have the means to verify these Credentials against the Credential Issuer's cryptographic public keys or other verification methods to ensure they are authentic and have not been tampered with. The Credential Verifier uses the Trust Anchor and its Intermediates to establish the trust with the Credential Issuers, obtains their metadata and cryptographic material, and check the validity of the presented Digital Credentials. It also establishes trust with the Holder and the Wallet Solution used by it.
4. **Trust Anchor**: This Entity and its Intermediates, issue Subordinate Statements and any required information about the status of the Federation and its participants (Organizational Entities), to demonstrate their non-revocations, distribute the policies and prevents the repudiation of the past transaction about any trust evaluation, if signed. Historical proofs allow for the evaluation of an Organizational Entity's status within a federation and their past signatures, which can be verified using a historical Trust Chain.


~~~ ascii-art
+-------------------+    +---------------+    +---------------------+
| Credential Issuer |<-->|    Holder     |<-->| Credential Verifier |
|                   |    |               |    |                     |
+-------------------+    +---------------+    +---------------------+
         |                       |                     |
         |                       |                     |
         V                       V                     V
+--------------------------------------------------------------+
|                          Trust Anchor                        |
+--------------------------------------------------------------+
~~~
**Figure 1**: The relationships and interactions within a Wallet ecosystem using the Four-Party Model, where Authentic Sources and Wallet Providers figure such as extensions to be not considered in the core structure of the four parties.

The Figure above illustrates at the center the Holder, who interacts directly with both the Credential Issuer and the Credential Verifier. The Credential Issuer provides Digital Credentials to the Holder, while the Credential Verifier relies on these Credentials to verify the Holder's claims. Above the Holder is the Wallet Provider, which facilitates the registration and the attestation of the security and integrity of the Holder. All entities, including the Credential Issuer, Credential Verifier, Wallet Provider and therefore Holders, and are underpinned by a Trust Anchor, which provides a foundational layer of trust and security for the entire system. This setup ensures that all interactions and transactions are anchored in a trusted framework.

In the Wallet Ecosystem, the primary interaction resolves around asset management. Unlike an Identity Provider in OpenID Connect or SAML2, which authenticates the End-User's identity for third parties, the Credential Issuer in the Wallet ecosystem focuses on managing the issuance of Digital Credentials to the Holder, therefore to the End-User in control of the Wallet.

The transactions primarily involve the transfer or management of Digital Credentials rather than granting access to services based on identity verification.

Consequently, the End-User obtains and holds the Digital Credentials without disclosing their intended use to the Credential Issuers. At any subsequent time, the End-User can present these Digital Credentials to a Credential Verifier to authenticate themselves.


~~~ ascii-art
+------------------+     +-----------------+
| Authentic Source |     | Wallet Provider |
|                  |     |                 |
+------------------+     +-----------------+
         |                       |
         |                       |
         V                       V
+-------------------+    +---------------+    +---------------------+
| Credential Issuer |<-->|    Holder     |<-->| Credential Verifier |
|                   |    |               |    |                     |
+-------------------+    +---------------+    +---------------------+
         |                       |                     |
         |                       |                     |
         V                       V                     V
+-------------------------------------------------------------------+
|                          Trust Anchor                             |
+-------------------------------------------------------------------+
````
**Figure 2**: Representation acknowledging the roles of Authentic Sources and Wallet Providers in the ecosystem while maintaining the core structure of the Four-Party Model.


# Wallet Instance Types

There are many ways to technically implement Wallet Instances to manage Digital Credentials. There are typically two types of Wallet End-Users: one is a natural person and another is an Organizational Entity, such as a legal person. These two types of End-Users may have different usage and functional requirements.

Below a non-exhaustive list of the different Wallet Instance types.

Mobile Wallet Native Application
: Also known as Mobile Wallet only, is an application that runs natively on a Personal Device under the sole control of an End-User and provided through a platform vendor specific app-store, on behalf of the Wallet Solution. In some cases the End-User as natural person uses the Mobile Wallet representing a legal person.

Web Wallet Native Application
: Also known as Cloud Wallet or Web Wallet only, is a Wallet that uses native web technologies for its components, such as UI components. Cloud Wallets are typically suited for Organizational Entities that requires automated Digital Credential operations (request, issuance, store, presentation, revocations) in unsupervised flows, therefore without any human control. Web Wallets are divided into two additional subtypes:
    - **Custodial Web Wallet**: Cloud Wallets that have dependency on a cloud infrastructure, not necessarily hosted by the Wallet Provider, are typically classified as Custodial Web Wallets; in this case, the cryptographic keys used and the Digital Credentials are stored in the cloud infrastructure.
    - **Non-Custodial Web Wallet**: A Web Wallet where the cryptographic keys are stored and managed on a media in possession by the End-User and the Digital Credentials can only be used by the End-User, e.g. using a FIDO enabled security hardware token, no matter whether the Credentials are stored locally in a Personal Device or in cloud storage.

Progressive Web Application Wallet (PWAW)
: PWAW is a web application that looks like a native app. It can be installed on a Personal Device and not necessarily using the operative system specific app-store. The advantage with a PWAW is that it gives the End-User the same experience as a Mobile Native Wallet Application while also offering the benefits of a web application. PWAW can be Custodial or Non-Custodial.

## Establishing Trust with the Holder

Since the Holder may not be an Organizational Entity and cannot be registered as an Organization through registration services, it is not represented within a Trust Chain and does not qualify as a Federation Entity. This context sets the stage for understanding the unique position of the Holder in relation to the Trust Chain and Federation Entities.

~~~ ascii-art
+----------------------------+
| Trust Chain                |
| +------------------------+ |
| | Trust Anchor           | |
| | (Entity Configuration) | |
| +------------------------+ |
|                     |      |
|                     v      |
| +------------------------+ |
| | Trust Anchor           | |
| | (Subordinate Statement | |
| |  about the             | |
| |  Wallet Provider)      | |
| +------------------------+ |
|                     |      |
|                     v      |
| +------------------------+ |
| | Wallet Provider        | |    +-------------------------------+
| | (Entity Configuration) |----->|      Wallet Attestation       |
| +------------------------+ |    | (Not part of the Trust Chain) |
+----------------------------+    +-------------------------------+
~~~
**Figure 3**: Federation Trust Chain and Wallet Attestation are separate things, where the Wallet Attestation is linked to its Issuer attested within the Trust Chain.

Outside the Trust Chain, it is the Wallet Attestation, where the Wallet Provider that issued it is attestable through the Trust Chain, while the Wallet, such as the End-User's Native Mobile Application installed on the Personal Device, is attested through the Wallet Attestation and under the responsibility of its issuer, the Wallet Provider.

# Establishing Trust with a Credential Verifier Instance

A Credential Verifier Instance is typically installed on a mobile device, personal computer, or embedded system. It enables an individual to perform Digital Credential verification tasks locally, often in proximity to the Holder, and without necessarily requiring a broadband connection. This instance engages in peer-to-peer exchanges with Holders, facilitating Credential verifications directly on the device. This approach represents a shift from traditional server-based verification to a more user-centric model within the Wallet ecosystem.

To establish trust between a Holder's Wallet Instance and a Credential Verifier Instance, a mechanism using a verifiable attestation, such as the Wallet Instance Attestations, SHOULD be employed. This process ensures that the Credential Verifier Instance is legitimate and trustworthy.

# Wallet Architecture Entity Types

This section defines the Entity Types used by Organizational Entities in their Entity Configurations according to their roles in the Wallet ecosystem.

| Entity         | Entity Type Identifiers                                              | References                          |
|-----------------------|------------------------------------------------------------|-------------------------------------|
| Trust Anchor          | `federation_entity`                                        | [@!OpenID.Federation]                       |
| Intermediate          | `federation_entity`                                        | [@!OpenID.Federation]                       |
| Wallet Provider       | `federation_entity`, `openid_wallet_provider`              | this specification                                  |
| Authorization Server  | `federation_entity`, `oauth_authorization_server`          | [@!OpenID4VCI], [@!RFC8414]                    |
| Credential Issuer     | `federation_entity`, `openid_credential_issuer`, `oauth_authorization_server` | [@!OpenID4VCI], this specification |
| Credential Verifier   | `federation_entity`, `openid_credential_verifier`          | [@!OpenID.Federation], [@!OpenID4VP], this specification       |
**Table 1**: Map of the Federation Entity Types and corresponding metadata types for the Wallet architectures.


The Credential Issuer is an OAuth 2.0 Protected Resource Server and it MAY also implement, within the same Entity, an OAuth 2.0 Authorization Server. According to [@!OpenID4VCI], the Authorization Server can be external to the Entity that implements the Credential Endpoint, therefore the use of `oauth_authorization_server` is OPTIONAL.

## OpenID Wallet Provider Entity Type

The OpenID Federation Entity Type Identifier for the Wallet Provider is `openid_wallet_provider`.

For information on metadata parameters specific to OpenID Wallets,
refer to Section *8. Wallet Metadata (Authorization Server Metadata)* of
the OpenID for Verifiable Presentations [@!OpenID4VP] specification.

## OpenID Credential Issuer Entity Type

The OpenID Federation Entity Type Identifier for the Credential Issuer is `openid_credential_issuer`.

For information on metadata parameters specific to OpenID Credential Issuers,
refer to Section *10.2. Credential Issuer Metadata* of
the OpenID for Verifiable Credential Issuance [@!OpenID4VCI] specification.

## OpenID Credential Verifier Entity Type

The OpenID Federation Entity Type Identifier for the Credential Verifier is `openid_credential_verifier`.

This specification introduces a distinct Entity Type Identifier for the OpenID Credential Verifier to clearly differentiate it from a traditional OpenID Connect Relying Party (`openid_relying_party`). This distinction highlights the unique characteristics and functionalities of the Wallet ecosystem and its Credential Verifier.

For information on metadata parameters specific to OpenID Credential Verifiers,
refer to Section *9. Verifier Metadata (Client Metadata)* of
the OpenID for Verifiable Presentations [@!OpenID4VP] specification.

# Federation Policies

Policies refer to a set of rules that govern the operations, security, and interactions within a federation.

Technical implementation of federation policies over participants metadata is managed with the use of `metadata` and `metadata_policy` parameters in Subordinate Statements. These parameters allow for the configuration enforcement of application-specific metadata changes for each subject (Leaf).

Qualitative aspects of federation entities, including administrative protocols, security measures, and behavioral profiling, are regulated by Trust Marks. These marks provide verifiable assertions of compliance with specific profiles beyond the scope of the application-specific metadata.

## Using Metadata

Metadata refers to application-specific properties about a subject and for the purpose of the interoperability. This includes details such as service endpoints, cryptographic keys, and other supported configurations.

Metadata within a Subordinate Statement allows for modifications to the metadata published in a Leaf's Entity Configuration.
These modifications allow a federation authority, such as a Trust Anchor, to apply policies coercively to its subordinates. This can include actions such as removing weak signature algorithms from their metadata, enforcing the use of specific endpoints configured at the time of the entity's registration within the ecosystem, or restricting the personal data that a Credential Verifier is allowed to request.

```
{
  "iss": "https://trust-anchor.example.com",
  "sub": "https://credential-verifier.example.it",
  "iat": 1616239022,
  "exp": 1616239322,
  "metadata": {
    "federation_entity": {
      "organization_name": "Example Credential Verifier",
    },
    "openid_credential_verifier": {
      "application_type": "web",
      "client_name": "Example Credential Verifier",
      "request_uris": [
          "https://verifier.example.org/request_uri"
      ],
      "response_uris_supported": [
          "https://verifier.example.org/response_uri"
      ],
      "presentation_definitions_supported": [
          {
              "id": "d76c51b7-ea90-49bb-8368-6b3d194fc131",
              "input_descriptors": [
                  {
                      "id": "PersonIdentificationData",
                      "name": "Person Identification Data",
                      "purpose": "User Authentication",
                      "format": {
                          "vc+sd-jwt": {
                              "alg": [
                                  "ES256",
                                  "ES384",
                                  "ES512"
                              ]
                          }
                      },
                      "constraints": {
                          "limit_disclosure": "required",
                          "fields": [
                              {
                                  "filter": {
                                      "const": "PersonIdentificationData",
                                      "type": "string"
                                  },
                                  "path": [
                                      "$.vct"
                                  ]
                              },
                              {
                                  "filter": {
                                      "type": "object"
                                  },
                                  "path": [
                                      "$.cnf.jwk"
                                  ]
                              },
                              {
                                  "path": [
                                      "$.first_name"
                                  ]
                              },
                              {
                                  "path": [
                                      "$.family_name"
                                  ]
                              }
                          ]
                      }

                  }
              ]
          }
      ],
    }
  },
  "jwks": {
    "keys": [
      {
        "kty": "RSA",
        "use": "sig",
        "kid": "1",
        "n": "0vx7agoebGcQSuuPiLJXZptN9nndrQmbXEPCRfceaC7mkxr8v...",
        "e": "AQAB"
      }
    ]
  }
}
**Example 1**: Example demonstrating how a Federation Authority can issue a Subordinate Statement about a Credential Verifier, specifying certain metadata parameters such as the endpoints to use and the allowed Digital Credentials to be requested.

## Differences Between `metadata` and `metadata_policy`

The key difference between `metadata` and `metadata_policy` is that metadata directly affects only the Immediate Subordinate Entity, while `metadata_policy` impacts the configuration of all Subordinate Entities along a Trust Chain, as defined in Sections 5 and 6.1 of [@!OpenID.Federation].

This distinction positions the `metadata` parameter as an ideal tool for federation authorities managing entity registrations and needing to sanitize Leaves configurations in an arbitrary way. The Trust Anchor (TA) and Intermediate (INT) sanitize an Entity Configuration during technical tests and finalize it by setting specific metadata parameters.

## Using Metadata Policies

Differently from `metadata`, `metadata_policy` ensures that specific settings can be propagated to all the Entities Statements contained within a Trust Chain.

## Using Trust Marks

Trust Marks are issued by authorized entities (Trust Mark Issuers) within the federation, typically after an entity has demonstrated compliance with certain standards, this might happen through auditing or certification processes.

Trust Marks are typically implemented as signed assertions that can be verified by other entities.

This verification process involves checking the digital signature against the public key of the Trust Mark Issuer to ensure the Trust Mark has not been forged, and its check to the Trust Mark Status endpoint to check it against any revocation.

Trust Marks SHOULD be defined within the trust framework. Trust Marks are asserted about a subject through a registration service or compliance evaluation mechanism and therefore included in subject's Entity Configurations. This allows other entities to quickly assess the compliance status of a subject by examining the Entity Configuration of a subject.


```json=
{
  "id":"https://diligent.federation.example.com/openid_credential_verifier/private/under-age",
  "iss": "https://trustissuer.pinarolo.example.it",
  "sub": "https://vavuso.example.com/rp",
  "iat": 1579621160,
  "policy_uri": "https://vavuso.example.com/policy",
  "tos_uri": "https://vavuso.example.com/tos"
}
```
**Example 2**: Trust Mark to be included in a Leaf Entity Configuration, which payload states Leaf's compliance in interacting with under-age End-User.

# Federation Trust Discovery Use Cases

The process of trust establishment in federated environments is illustrated in this section through specific use cases involving Wallet Instances, Credential Issuers (CIs), and Credential Verifiers (CVs).

## Wallet Checking the Non-Revocation of its Wallet Provider

Wallets SHOULD periodically check their Wallet Providers' compliance through the federation's trust infrastructure. This involves retrieving the Wallet Provider's Entity Configuration and verifying its Trust Chain up to a recognized Trust Anchor, ensuring that the Wallet Provider has not been revoked within the federation. Wallets SHOULD remain neutral in attesting to the reliability of their Wallet Providers for the End-User, thereby protecting the End-User against any malevolent behavior by the Wallet Provider.

The Wallet Provider’s Entity Configuration provides essential information, including its roles within the federation, policies it adheres to, and cryptographic keys for secure communication. The Wallet Instance SHOULD use the Federation API to periodically reestablish trust with its Wallet Provider.

The process to discover the trust with a Wallet Provider is equivalent to the one used for discovering the trust with a Credential Issuer, as described in the dedicated section below.

## Wallet Discovering Credentials Issuers

Wallets begin by discovering the identity of Credential Issuers through the federation's trust infrastructure. This involves retrieving the Credential Issuer's Entity Configuration and verifying its Trust Chain up to a recognized Trust Anchor. The Credential Issuer’s Entity Configuration provides essential information, including its roles within the federation, policies it adheres to, and cryptographic keys for secure communication.

In the process represented in the sequence diagram below, the Wallet Instance uses the Federation API to discover and collect all the Credential Issuers enabled within the federation.

~~~ ascii-art

        +------+                                           +------------+ +------------+ +-----------------+          
        |Wallet|                                           |Trust Anchor| |Intermediate| |Credential Issuer|          
        +---+--+                                           +------+-----+ +------+-----+ +--------+--------+          
            |  Fetch the list of all Intermediates and CIs        |              |                |                   
            |---------------------------------------------------->|              |                |                   
            |                                                     |              |                |                   
+-----------+-----------------------------------------------------+--------------+------+         |                   
| LOOP  |for each Intermediate                                    |              |      |         |                   
|-------+   |                                                     |              |      |         |                   
|           |                 Fetch listing of Subordinates/CIs   |              |      |         |                   
|           |------------------------------------------------------------------->|      |         |                   
+-----------+-----------------------------------------------------+--------------+------+         |                   
            |                                                     |              |                |                   
+-----------+-----------------------------------------------------+--------------+----------------+--------+
| LOOP  |for each CI                                              |              |                |        |
|-------+   |                                                     |              |                |        |
|           |                          Fetch CI's Entity Configuration           |                |        |
|           |------------------------------------------------------------------------------------>|        |
|           |                                                     |              |                |        |
|           |         Fetch Subordinate Statement(s) for CI       |              |                |        |
|           |------------------------------------------------------------------->|                |        |
|           |                                                     |              |                |        |
|           |  Fetch Subordinate Statement for Intermediate(s)    |              |                |        |
|           |---------------------------------------------------->|              |                |        |
|           |----+                                                |              |                |        |
|           |    | Validate Trust Chain for CI                    |              |                |        |
|           |<---+                                                |              |                |        |
|           |                                                     |              |                |        |
|           |----+                                                |              |                |        |
|           |    | Include CI in Discovery Page                   |              |                |        |
|           |    | with validated information and logo            |              |                |        |
|           |<---+                                                |              |                |        |
+-----------+-----------------------------------------------------+--------------+----------------+--------+
        +---+--+                                           +------+-----+ +------+-----+ +--------+--------+          
        |Wallet|                                           |Trust Anchor| |Intermediate| |Credential Issuer|          
        +------+                                           +------------+ +------------+ +-----------------+    
~~~
**Figure 4**: Federation Credential Issuer listing, the Wallet Instance browse the entire federation collecting all the Credential Issuers.  


The diagram above shows how a Wallet navigates the federation, collecting and validating the Trust Chain for each Credential Issuer (CI), and creating a discovery page including each Credential Issuer using the information, such as the credential types and logo obtained through their Trust Chain.

The diagram below illustrates how a Wallet establishes trust with a Credential Issuer by verifying its link (even if indirect) to a Trust Anchor and validating which Credentials it is authorized to issue. This may happen in a credential offer flow, for instance, where the Wallet is used by an End-User starting from the Credential Issuer website and without any discovery phases started before within the Wallet.

~~~ ascii-art
        +------+                                 +-----------------+ +-------------------------+          
        |Wallet|                                 |Credential Issuer| |Intermediate/Trust Anchor|          
        +---+--+                                 +--------+--------+ +------------+------------+          
            |    Fetch CI's Entity Configuration          |                       |                       
            |-------------------------------------------->|                       |                       
            |                                             |                       |                       
            |----+                                        |                       |                       
            |    | Extract Authority Hints                |                       |
            |    | from CI's Configuration                |                       |                       
            |<---+                                        |                       |                       
            |                                             |                       |                       
            |                                             |                       |                       
+-------+---+---------------------------------------------+-----------------------+------------+
| LOOP  |for each Authority Hint                          |                       |            |
+-------+   |                                             |                       |            |
|           |                     Fetch Entity Configuration                      |            |
|           |-------------------------------------------------------------------->|            |
|           |                                             |                       |            |
|           |                    Fetch Subordinate Statement                      |            |
|           |-------------------------------------------------------------------->|            |
|           |                                             |                       |            |
|           |----+                                        |                       |            |
|           |    | Validate the previous statement        |                       |            |
|           |<---+ using the Federation Entity Keys       |                       |            |
|           |      provided in the Subordinate Statement  |                       |            |
|           |                                             |                       |            |
+-----------+---------------------------------------------+-----------------------+------------+
            |                                             |                       |                       
            |----+                                        |                       |                       
            |    | Validate Trust Chain                   |                       |                       
            |<---+                                        |                       |                       
            |                                             |                       |                       
            |                                             |                       |                       
+------+----+----------------------------------------+    |                       |                       
| ALT  |If Trust Chain is Valid and Unexpired        |    |                       |                       
+------+    |                                        |    |                       |                       
|           |----+                                   |    |                       |                       
|           |    | Proceed with Federation Process   |    |                       |                       
|           |<---+                                   |    |                       |                       
+-----------+----------------------------------------+    |                       |                       
|           |                                        |    |                       |                       
|           |----+                                   |    |                       |                       
|           |    | Abort Process with Error          |    |                       |                       
|           |<---+                                   |    |                       |                       
+-----------+----------------------------------------+    |                       |                       
            |                                             |                       |                       
            |----+                                        |                       |                       
            |    | Applies Policies                       |                       |                       
            |<---+                                        |                       |                       
            |                                             |                       |                       
            |----+                                        |                       |                       
            |    | Derive CI's final metadata             |                       |                       
            |<---+                                        |                       |                       
            |                                             |                       |                       
            |----+                                        |                       |                       
            |    | Get available Credentials              |                       |
            |    | allowed for issuance                   |                       |                       
            |<---+                                        |                       |                       
        +---+--+                                 +--------+--------+ +------------+------------+          
        |Wallet|                                 |Credential Issuer| |Intermediate/Trust Anchor|          
        +------+                                 +-----------------+ +-------------------------+     
~~~
**Figure 5**: Federation Entity Discovery, the Wallet Instance evaluates the trust with a Credential Issuer.


## Credential Issuers Establishing Trust in the Wallet Provider

...

## Credential Issuers Establishing Trust in the Wallet

...

## Wallet Establishing Trust in the Credential Verifier

The Federation Entity Discovery starts with the Wallet Instance fetching the Credential Verifier's Entity Configuration to identify authority hints, pointing to Federation Entities that can issue Subordinate Statements about the Credential Verifier. The Wallet Instance then follows these hints and collects the Subordinate Statements and validating each one. The process continues until the Wallet Instance reaches the Trust Anchor. Finally, the Wallet Instance compiles the validated Trust Chain. If the Trust Chain is valid, the Wallet Instance processes the Credential Verifier final metadata.

Note: While this section exemplifies the journey of discovery from the perspective of an OpenID Wallet Instance, it is important to understand that this approach can be applied to every kind of entity type within the federation.


~~~ ascii-art
        +------+                               +-------------------+ +-------------------------+          
        |Wallet|                               |Credential Verifier| |Intermediate/Trust Anchor|          
        +---┬--+                               +--------+----------+ +--------+----------------+          
            |       Fetch Entity Configuration          |                     |                       
            |------------------------------------------>|                     |                       
            |                                           |                     |                       
            |----+                                      |                     |                       
            |    | Extract Authority Hints              |                     |
            |    | from Entity Configuration            |                     |                       
            |<---+                                      |                     |                       
            |                                           |                     |                       
            |                                           |                     |                       
+-------+---+-------------------------------------------+---------------------+----------+
| LOOP  |for each Authority Hint                        |                     |          |
+-------+   |                                           |                     |          |
|           |                   Fetch Entity Configuration                    |          |
|           |---------------------------------------------------------------->|          |
|           |                                           |                     |          |
|           |                    Fetch Subordinate Statement                  |          |
|           |---------------------------------------------------------------->|          |
|           |                                           |                     |          |
|           |----+                                      |                     |          |
|           |    | Validate the previous statement      |                     |          |
|           |<---+ using the Federation Entity Keys     |                     |          |
|           |      provided in the Subordinate Statement|                     |          |
|           |                                           |                     |          |
+-----------+-------------------------------------------+---------------------+----------+
            |                                           |                     |                       
            |----+                                      |                     |                       
            |    | Validate Trust Chain                 |                     |                       
            |<---+                                      |                     |                       
            |                                           |                     |                       
            |                                           |                     |                       
+------+----+----------------------------------------+  |                     |                       
| ALT  |If Trust Chain is Valid and Unexpired        |  |                     |                       
+------+    |                                        |  |                     |                       
|           |----+                                   |  |                     |                       
|           |    | Proceed with Federation Process   |  |                     |                       
|           |<---+                                   |  |                     |                       
+-----------+----------------------------------------+  |                     |                       
|           |                                        |  |                     |                       
|           |----+                                   |  |                     |                       
|           |    | Abort Process with Error          |  |                     |                       
|           |<---+                                   |  |                     |                       
+-----------+----------------------------------------+  |                     |                       
            |                                           |                     |                       
            |----+                                      |                     |                       
            |    | Applies Policies                     |                     |                       
            |<---+                                      |                     |                       
            |                                           |                     |                       
            |----+                                      |                     |                       
            |    | Derive final metadata                |                     |                       
            |<---+                                      |                     |                       
            |                                           |                     |                       
            |----+                                      |                     |                       
            |    | Get Credentials                      |                     |
            |    | allowed for presentation             |                     |                       
            |<---+                                      |                     |                       
        +---+--+                               +--------+--------+ +----------+--------------+          
        |Wallet|                               |Credential Issuer| |Intermediate/Trust Anchor|          
        +------+                               +-----------------+ +-------------------------+     
~~~
**Figure 6**: Federation Entity Discovery, the Wallet Instance evaluates the trust with a Credential Verifier.

# Implementation Considerations for Offline Flows

The static Trust Chain parameter within the JWT headers, as defined in Section 4.3 of [@!OpenID.Federation], is used as a hint to the Entity involved in a transaction with a common Trust Anchor. This facilitates trust evaluation without the need for real-time Federation Entity Discovery using Federation API endpoints.

The Entity that issues a signed data object, including the `trust_chain` parameter, might be:

- Wallet Providers in signed Wallet Attestations. The Wallet Instance obtains one or more Wallet Attestations from its Wallet Provider, each of them including a Trust Chain related to each Trust Anchor the Wallet Provider trusts;
- Credential Verifiers in signed request objects. The Wallet Instance obtains a presentation request that includes a Trust Chain using a Trust Anchor that the Credential Verifier has in common with the Wallet Provider, according to the `wallet_metadata` parameter provided by the Wallet in the Request URI POST;
- A Credential Issuer in a signed Digital Credential. The Wallet Instance obtains a Digital Credential from its Credential Issuer, which includes the Trust Chain using a Trust Anchor that the Credential Verifier has in common with the Wallet Provider, according to the Wallet Attestation used during the Issuance.

The Entity that receives the data object including the JWT `trust_chain`, such as the Wallet Instance obtaining a signed request object, verifies the Trust Chain using the Trust Anchor's public keys and applies any metadata policies, without needing to have a working network connection for reaching the Federation API.

Using short-lived Trust Chains ensures compatibility with required revocation administrative protocols, such as those defined in a legal framework. For example, if a revocation must be propagated in less than 24 hours, the Trust Chain should not be valid for more than that period.


# Acknowledgments

We would like to thank the following individuals for their comments, ideas, and contributions to this implementation profile and to the initial set of implementations.

- Leif Johansson
- Stefan Liström
- Francesco Antonio Marino
- Giada Sciarretta
- Niels van Dijk

{backmatter}

<reference anchor="OpenID.Core" target="http://openid.net/specs/openid-connect-core-1_0.html">
  <front>
    <title>OpenID Connect Core 1.0 incorporating errata set 2</title>
    <author initials="N." surname="Sakimura" fullname="Nat Sakimura">
      <organization>NRI</organization>
    </author>
    <author initials="J." surname="Bradley" fullname="John Bradley">
      <organization>Ping Identity</organization>
    </author>
    <author initials="M." surname="Jones" fullname="Michael B. Jones">
      <organization>Microsoft</organization>
    </author>
    <author initials="B." surname="de Medeiros" fullname="Breno de Medeiros">
      <organization>Google</organization>
    </author>
    <author initials="C." surname="Mortimore" fullname="Chuck Mortimore">
      <organization>Salesforce</organization>
    </author>
   <date day="15" month="December" year="2023"/>
  </front>
</reference>

<reference anchor="OpenID.Discovery" target="https://openid.net/specs/openid-connect-discovery-1_0.html">
  <front>
    <title>OpenID Connect Discovery 1.0</title>

    <author fullname="Nat Sakimura" initials="N." surname="Sakimura">
      <organization abbrev="NAT.Consulting (was at NRI)">NAT.Consulting</organization>
    </author>

    <author fullname="John Bradley" initials="J." surname="Bradley">
      <organization abbrev="Yubico (was at Ping Identity)">Yubico</organization>
    </author>

    <author fullname="Michael B. Jones" initials="M.B." surname="Jones">
      <organization abbrev="Self-Issued Consulting (was at Microsoft)">Self-Issued Consulting</organization>
    </author>

    <author fullname="Edmund Jay" initials="E." surname="Jay">
      <organization abbrev="Illumila">Illumila</organization>
    </author>

    <date day="15" month="December" year="2023"/>
  </front>
</reference>

<reference anchor="OpenID.Registration" target="https://openid.net/specs/openid-connect-registration-1_0.html">
  <front>
    <title>OpenID Connect Dynamic Client Registration 1.0</title>

    <author fullname="Nat Sakimura" initials="N." surname="Sakimura">
      <organization abbrev="NAT.Consulting (was at NRI)">NAT.Consulting</organization>
    </author>

    <author fullname="John Bradley" initials="J." surname="Bradley">
      <organization abbrev="Yubico (was at Ping Identity)">Yubico</organization>
    </author>

    <author fullname="Michael B. Jones" initials="M.B." surname="Jones">
      <organization abbrev="Self-Issued Consulting (was at Microsoft)">Self-Issued Consulting</organization>
    </author>

    <date day="15" month="December" year="2023"/>
  </front>
</reference>

<reference anchor="OpenID4VP" target="https://openid.net/specs/openid-4-verifiable-presentations-1_0.html">
      <front>
        <title>OpenID for Verifiable Presentations</title>
        <author initials="O." surname="Terbu" fullname="Oliver Terbu">
         <organization>Mattr</organization>
        </author>
        <author initials="T." surname="Lodderstedt" fullname="Torsten Lodderstedt">
          <organization>SPRIND</organization>
        </author>
        <author initials="K." surname="Yasuda" fullname="Kristina Yasuda">
          <organization>SPRIND</organization>
        </author>
        <author initials="T." surname="Looker" fullname="Tobias Looker">
          <organization>Mattr</organization>
        </author>
       <date day="9" month="August" year="2024"/>
      </front>
</reference>

<reference anchor="OpenID4VCI" target="https://openid.net/specs/openid-4-verifiable-credential-issuance-1_0.html">
        <front>
          <title>OpenID for Verifiable Credential Issuance</title>
          <author initials="T." surname="Lodderstedt" fullname="Torsten Lodderstedt">
            <organization>SPRIND</organization>
          </author>
          <author initials="K." surname="Yasuda" fullname="Kristina Yasuda">
            <organization>SPRIND</organization>
          </author>
          <author initials="T." surname="Looker" fullname="Tobias Looker">
            <organization>Mattr</organization>
          </author>
          <date day="9" month="August" year="2024"/>
        </front>
</reference>

<reference anchor="OpenID4VC-HAIP" target="https://openid.net/specs/openid4vc-high-assurance-interoperability-profile-sd-jwt-vc-1_0.html">
        <front>
          <title>OpenID4VC High Assurance Interoperability Profile with SD-JWT VC</title>
          <author initials="K." surname="Yasuda" fullname="Kristina Yasuda">
            <organization>Microsoft</organization>
          </author>
          <author initials="T." surname="Lodderstedt" fullname="Torsten Lodderstedt">
            <organization>sprind.org</organization>
          </author>
          <date day="9" month="January" year="2024"/>
        </front>
</reference>

<reference anchor="DIF.PresentationExchange" target="https://identity.foundation/presentation-exchange/spec/v2.0.0/">
        <front>
          <title>Presentation Exchange 2.0.0</title>
		  <author fullname="Daniel Buchner">
            <organization>Microsoft</organization>
          </author>
          <author fullname="Brent Zundel">
            <organization>Evernym</organization>
          </author>
          <author fullname="Martin Riedel">
            <organization>Consensys Mesh</organization>
          </author>
          <author fullname="Kim Hamilton Duffy">
            <organization>Centre Consortium</organization>
          </author>
        </front>
</reference>

<reference anchor="OAuth.JARM" target="https://openid.net/specs/oauth-v2-jarm.html">
  <front>
    <title>JWT Secured Authorization Response Mode for OAuth 2.0 (JARM)</title>
    <author initials="T." surname="Lodderstedt" fullname="Torsten Lodderstedt">
      <organization>yes.com</organization>
    </author>
    <author initials="B." surname="Brian" fullname="Brian Campbell">
      <organization>Ping Identity</organization>
    </author>
   <date day="9" month="November" year="2022"/>
  </front>
</reference>

<reference anchor="OpenID.Federation" target="https://openid.net/specs/openid-federation-1_0.html">
        <front>
          <title>OpenID Federation 1.0</title>
		  <author fullname="R. Hedberg, Ed.">
            <organization>independent</organization>
          </author>
          <author fullname="Michael B. Jones">
            <organization>Self-Issued Consulting</organization>
          </author>
          <author fullname="A. Solberg">
            <organization>Sikt</organization>
          </author>
          <author fullname="John Bradley">
            <organization>Yubico</organization>
          </author>
          <author fullname="Giuseppe De Marco">
            <organization>independent</organization>
          </author>
          <author fullname="Vladimir Dzhuvinov">
            <organization>Connect2id</organization>
          </author>
          <date day="31" month="May" year="2024"/>
        </front>
</reference>

<reference anchor="eIDAS" target="https://eur-lex.europa.eu/legal-content/EN/TXT/HTML/?uri=CELEX:32014R0910">
  <front>
    <title>REGULATION (EU) No 910/2014 OF THE EUROPEAN PARLIAMENT AND OF THE COUNCIL on electronic identification and trust services for electronic transactions in the internal market and repealing Directive 1999/93/EC</title>
    <author surname="European Parliament">
      <organization>European Parliament</organization>
    </author>
    <date year="2014" month="July" day="23"></date>
  </front>
</reference>

# Notices

Copyright (c) 2024 The OpenID Foundation.

The OpenID Foundation (OIDF) grants to any Contributor, developer, implementer, or other interested party a non-exclusive, royalty free, worldwide copyright license to reproduce, prepare derivative works from, distribute, perform and display, this Implementers Draft or Final Specification solely for the purposes of (i) developing specifications, and (ii) implementing Implementers Drafts and Final Specifications based on such documents, provided that attribution be made to the OIDF as the source of the material, but that such attribution does not indicate an endorsement by the OIDF.

The technology described in this specification was made available from contributions from various sources, including members of the OpenID Foundation and others. Although the OpenID Foundation has taken steps to help ensure that the technology is available for distribution, it takes no position regarding the validity or scope of any intellectual property or other rights that might be claimed to pertain to the implementation or use of the technology described in this specification or the extent to which any license under such rights might or might not be available; neither does it represent that it has made any independent effort to identify any such rights. The OpenID Foundation and the contributors to this specification make no (and hereby expressly disclaim any) warranties (express, implied, or otherwise), including implied warranties of merchantability, non-infringement, fitness for a particular purpose, or title, related to this specification, and the entire risk as to implementing this specification is assumed by the implementer. The OpenID Intellectual Property Rights policy requires contributors to offer a patent promise not to assert certain patent claims against other contributors and against implementers. The OpenID Foundation invites any interested party to bring to its attention any copyrights, patents, patent applications, or other proprietary rights that MAY cover technology that MAY be required to practice this specification.

# Document History

   [[ To be removed from the final specification ]]

   -02

   * Added non-normative example about using policies with metadata and trust marks
   * Added Credential Verifier and Credential Verifier Instance
   * Added section about Credential Verifier Instance
   * Illustrative rationale about Authentic Sources and Wallet Provider within the Four-Party Model sections
   * Moved text on Possible Use of Metadata Parameters by Wallet Ecosystems to issue #22.
   * Added warning about the specification not being final.

   -01

   * Created Scope section describing the purpose of the document and collaboration with other working groups.
   * Moved metadata tables and examples to an informative appendix on possible usage.
   * Fixed #10: Renamed `openid_wallet_relying_party` to `openid_credential_verifier`.

   -00 

   *  Initial version
