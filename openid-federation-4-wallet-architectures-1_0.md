%%%
title = "OpenID Federation for Wallet Architectures"
abbrev = "openid-federation-4-wallet-architectures"
ipr = "none"
workgroup = "OpenID Connect A/B"
keyword = ["security", "openid", "ssi"]

[seriesInfo]
name = "Internet-Draft"
value = "openid-federation-4-wallet-architectures-1_0-00"
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
initials="M."
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

This specification defines the openid federation entity types for the
digital wallet architectures.

{mainmatter}

# Introduction

As digital wallets become increasingly deployed for managing identity credentials,
establishing an  architecture  for trusted communication is required to allow each
of the participant in the ecosystem to evaluate their compliance with trust frameworks
and accomplish secure and trusted transactions.

This specification defines how to use OpenID Federation 1.0 to enhance the
security and interoperability of wallet ecosystems, facilitating trust establishment
among the parties and enabling secure metadata exchange and policy
application across large scale deployments.

This specification also outlines the general architecture of a federated trust
infrastructure for wallet ecosystems, identifying participant roles and defining
metadata used for those roles. In particular, it defines the metadata for
the roles of Wallet Provider and Wallet Relying Party.

Additionally, this specification provides practical examples of how to apply
policies for typical use cases within wallet ecosystems.

Finally, it offers guidance on defining trust marks for use within
wallet ecosystems.

## Requirements Notation and Conventions

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD",
"SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in
this document are to be interpreted as described in BCP 14 [@!RFC2119]
[@!RFC8174] when, and only when, they appear in all capitals, as shown here.

# Terminology

This specification uses the terms "End-User" and "Entity" as defined by
OpenID Connect Core [OpenID.Core], the term "JSON Web Token (JWT)" defined
by JSON Web Token (JWT) {{RFC7519}}, the term "CBOR Web Token (CWT)" defined
in {{RFC8392}}, "Client" as defined {{RFC6749}}, the terms
"Verifiable Presentation" and "Wallet Attestation" defined in [@OpenID4VP],
the terms "Holder" and "Credential Issuer" defined in [OpenID4VCI],
and the terms "Trust Mark", "Federation Entity", "Trust Anchor",
"Intermediate", and "Subordinate Statement" defined in [OpenIDFed].

This specification also defines the following terms:

Organizational Entity:
: A Federation Entity represented by a legal entity, specifically referring to public or private organizations (excluding natural persons) recognized through a unique identifier. For the purposes of this specification, an Organizational Entity is also referred to as an Organization.

Personal Device:
: Any electronic device that is primarily used by an individual. This includes smartphones, tablets, laptops, personal computers, smart watches, and other wearable technologies. Personal Devices are owned and managed by End-Users as individuals, rather than by Organizations.

Wallet Provider:
: An Organizational Entity responsible for the develoment, publication, and management of a Wallet Solution. 

Wallet Instance:
: Instance of a Wallet Solution belonging to and controlled by a person, be this natural or legal. It enables the request, storage, presentation, and management of Digital Credentials. It can be installed (instantiated) in a Personal Device or in a Remote Service.

Wallet Solution:
: The Wallet Solution is a product offered by a Wallet Provider to enable Users to securely manage and use their Digital Credentials. It is delivered by the Wallet Provider in the form of mobile app or cloud service. It may also utilize services and web services for the exchange of data between the Wallet Provider and its Wallet Instances.

Authentic Source:
: A protected Resource Server, not necessarly an OAuth 2.0 Resource Server, utilized by the Credential Issuer to retrieve the data necessary for issuing a Credential related to a subject.

## Trust Models and Trust Frameworks

The terms "trust model" and "trust framework" are often used in the context of security, identity management, and federation systems. 

The Trust Model defines the relationships and mechanisms through which trust is established and maintained between entities in a system. It outlines how entities interact, the basis on which they can trust each other, and the roles they play within the system. Trust Models can be simple or complex, depending on the number of parties involved and the nature of their interactions. Common examples include:

- **Direct Trust**: Trust is established directly between two parties without intermediaries.
- **Third-Party Trust**: Trust is facilitated by a trusted third party.
- **Web of Trust**: Trust is decentralized, with each participant making individual decisions about whom to trust, using Direct Trust or potentially multiple Third-Parties.

**Third-Party Trust** is the focus of this specification, although the **Web of Trust** model is not excluded if multiple trusted third parties (trust anchors) are supported by the participants.

A Trust Framework is a comprehensive structure that includes policies, standards, and guidelines that govern the implementation of a trust model. It provides detailed rules for how trust should be managed, including the legal, technical, and procedural aspects.

In the scopes of this specification, only the technical and procedural aspects are considered and fully covered.

OpenID Federation is a building block for assembling and using trust frameworks. It can help ensure that all participants in a system understand and adhere to the same principles and practices, making interactions predictable and secure.

# The Four Party Model

The Four Party Model is a framework involving four key participants: the Holder, the Credential Issuer, the Relying Party and an Entity trusted by the other Entities called the Trust Anchor. These Entities interact with each other as described below:

1. **Holder**: The Holder requests, stores, presents, and manages Digital Credentials and other forms of digital attestations. It discovers trustworthy Credential Issuers through the Trust Anchor and its Intermediates. Additionally, the Holder evaluates trust with Relying Parties recognized by the Trust Anchor and its Intermediates and checks for the non-revocation of the other Entities in use.
2. **Credential Issuer**: This Entity issues Digital Credentials to the Holder, after having evaluated the trust in the Wallet Solution and the security of the Holder. 
3. **Relying Party**: This is any Entity that requires proof of the End-User's identity, through the presentation of Credentials, to provide services or carry out transactions. Relying Parties rely on the validity of the Digital Credentials presented via the End-User's Wallet. They MUST have the means to verify these Credentials against the Credential Issuer's cryptographic public keys or other verification methods to ensure they are authentic and have not been tampered with. The Relying Party uses the Trust Anchor and its Intermediates to establish the trust with the Credential Issuers, obtains their metadata and cryptographic material, and check the validity of the presented Digital Credentials. It also establishes trust with the Holder and the Wallet Solution used by it.
4. **Trust Anchor**: and its Intermediates, issues Subordinate Statements and any required information about the status of the Federation and its participants (Organizational Entities), to demonstrate their non-revocations, distribute the policies and prevents the repudiation of the past transaction about any trust evaluation, if signed. Historical proofs allow for the evaluation of an Organizational Entity's status within a federation and their past signatures, which can be verified using a historical Trust Chain.

````
+------------------+     +-----------------+
| Authentic Source |     | Wallet Provider |
|                  |     |                 |
+------------------+     +-----------------+
         |                       |    
         |                       |       
         V                       V       
+-------------------+    +---------------+    +----------------+
| Credential Issuer |    |    Holder     |    |  Relying Party |
|                   |<-->|               |<-->|                |
+-------------------+    +---------------+    +----------------+
         |                       |                     |
         |                       |                     |
         V                       V                     V
+--------------------------------------------------------------+
|                          Trust Anchor                        |
+--------------------------------------------------------------+
````
**Figure 1**: The relationships and interactions within a Wallet ecosystem.

The Figure above illustrates at the center the Holder, who interacts directly with both the Credential Issuer and the Relying Party. The Credential Issuer provides Digital Credentials to the Holder, while the Relying Party relies on these Credentials to verify the Holder's claims. Above the Holder is the Wallet Provider, which facilitates the registration and the attestation of the security and integrity of the Holder. All entities, including the Credential Issuer, Holder, and Relying Party, are underpinned by the Trust Anchor, which provides a foundational layer of trust and security for the entire system. This setup ensures that all interactions and transactions are anchored in a trusted framework.

In the Wallet Ecosystem, the primary interaction resolves around asset management. Unlike an Identity Provider in OpenID Connect or SAML2, which authenticates the End-User's identity for third parties, the Credential Issuer in the Wallet ecosystem focuses on managing the issuance of Digital Credentials to the Holder, therefore to the End-User in control of the Wallet.

The transactions primarily involve the transfer or management of Digital Credentials rather than granting access to services based on identity verification.

Consequently, the End-User obtains and holds the Digital Credentials without disclosing their intended use to the Credential Issuers. At any subsequent time, the End-User can present these Digital Credentials to a Relying Party to authenticate themselves.

# Wallet Instance Types

There are many ways to technically implement Wallet Instances to manage Digital Credentials. There are typically two types of Wallet End-Users: one is a natural person and another is an Organisational Entity such as a legal person. These two types of users may have different usage and functional requirements.

Below a non-exhaustive list of the different Wallet Instance types.

; Mobile Wallet Native Application
  : Also known as Mobile Wallet only, is an application that runs natively on a Personal Device under the sole control of an End-User and provided through a platform vendor specific app-store, on behalf of the Wallet Solution. In some cases the End-User as natural person uses the Mobile Wallet representing a legal person.

; Web Wallet Native Application
  : Also known as Cloud Wallet or Web Wallet only, is a Wallet that uses native web technologies for its components, such as UI components. Cloud Wallets are typically suited for Organisational Entities that requires automated Digital Credential operations (request, issuance, store, presentation, revocations) in unsupervised flows, therefore without any human control. Web Wallets are divided into two additional subtypes:
    * **Custodial Web Wallet**: Cloud Wallets that have dependency on a cloud infrastructure, not necessarily hosted by the Wallet Provider, are typically classified as Custodial Web Wallets; in this case, the cryptographic keys used and the Digital Credentials are stored in the cloud infrastructure.
    * **Non-Custodial Web Wallet**: A Web Wallet where the cryptographic keys are stored and managed on a media in possession by the End-User and the Digital Credentials can only be used by the End-User, e.g. using a FIDO enabled security hardware token, no matter whether the Credentials are stored locally in a Personal Device or in cloud storage.

; Progressive Web Application Wallet (PWAW)
  : PWAW is a web application that looks like a native app. It can be installed on a Personal Device and not necessarily using the operative system specific app-store. The advantage with a PWAW is that it gives the End-User the same experience as a Mobile Native Wallet Application while also offering the benefits of a web application. PWAW can be Custodial or Non-Custodial.

## Establishing Trust With The Holder

Since the Holder may not be an Organizational Entity and cannot be registered as an Organization through registration services, it is not represented within a Trust Chain and does not qualify as a Federation Entity. This context sets the stage for understanding the unique position of the Holder in relation to the Trust Chain and Federation Entities.

```
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
```

Outside the Trust Chain, we have the Wallet Attestation, where the Wallet Provider that issued it is attestable through the Trust Chain, while the Wallet, such as the End-User's Native Mobile Application installed on the Personal Device, is attested through the responsibility of the Wallet Provider.

# Establishing Trust With The Verifier

TBD: concept of RP instance in the form of verifier app in control of a natural person.

# Metadata

This section defines the Entity Types used by Organizational Entities in their Entity Configurations according to their roles in the Wallet ecosystem.

| Entity         | Entity Type Identifiers                                              | References                          |
|-----------------------|------------------------------------------------------------|-------------------------------------|
| Trust Anchor          | `federation_entity`                                        | [OpenIDFed](#)                       |
| Intermediate          | `federation_entity`                                        | [OpenIDFed](#)                       |
| Wallet Provider       | `federation_entity`, `openid_wallet_provider`              | this specification                                  |
| Authorization Server  | `federation_entity`, `oauth_authorization_server`          | [OpenID4VCI](#)                     |
| Credential Issuer     | `federation_entity`, `openid_credential_issuer`, [`oauth_authorization_server`] | [OpenID4VCI](#) |
| Relying Party         | `federation_entity`, `openid_wallet_relying_party`         | [OpenIDFed](#), [OpenID4VP](#)       |

The Credential Issuer is an OAuth 2.0 protected Resource Server and it not necessarly implements, within the same Entity, also an OAuth 2.0 Authorization Server. According to [OpenID4VCI], the Authorization Server can be external to the Entity that implements the Credential endpoint, therefore the use of [`oauth_authorization_server`] is OPTIONAL.

## Metadata for OpenID Wallet Provider

The OpenID Federation Entity Type Identifier for the Wallet Provider is `openid_wallet_provider`.
The metadata for an OpenID Wallet Provider are listed in the table below.

| **Metadata Parameter** | **Availability** | **Value** |
|----------|----------- | ------|
| jwks | REQUIRED | A JSON Web Key Set (JWKS) that represents the  Wallet Provider's public keys. |
| token_endpoint | REQUIRED | Endpoint for obtaining the Wallet Instance Attestation. |
| aal_values_supported | OPTIONAL | List of supported values for the Authenticator Assurance Level, as defined in [ NIST Special Publication 800-63B](https://pages.nist.gov/800-63-3/sp800-63b.html#sec4). These values specify the security level of the app. Values are trust framework specific. |
| grant_types_supported | REQUIRED | The grant types supported by the token endpoint.|
| token_endpoint_auth_methods_supported | REQUIRED | Supported authentication methods for the token endpoint.|
| token_endpoint_auth_signing_alg_values_supported          | REQUIRED | Supported signature algorithms for the token endpoint.|

A non-normative example of `openid_wallet_provider`  metadata in an Entity Statement is:

````
{
  "token_endpoint": "https://wallet.provider.example/token",
  "jwks": {
      "keys": [
        {
          "crv": "P-256",
          "kty": "EC",
          "x": "qrJrj3Af_B57sbOIRrcBM7br7wOc8ynj7lHFPTeffUk",
          "y": "1H0cWDyGgvU8w-kPKU_xycOCUNT2o0bwslIQtnPU6iM",
          "kid": "5t5YYpBhN-EgIEEI5iUzr6r0MR02LnVQ0OmekmNKcjY"
        }
      ]
  },
  "aal_values_supported": [
      "https://trust-framework.example.org/LoA/basic",
      "https://trust-framework.example.org/LoA/medium",
      "https://trust-framework.example.org/LoA/high"
  ],
  "grant_types_supported": [
      "urn:ietf:params:oauth:client-assertion-type:jwt-client-attestation"
  ],
  "token_endpoint_auth_signing_alg_values_supported": ["RS256", "ES256"],
  "token_endpoint_auth_methods_supported": [
      "private_key_jwt"
  ]
}
````

## Metadata for the OpenID Credential Issuer

For detailed information on the metadata parameters specific to OpenID Credential Issuers, refer to the section *11.2. Credential Issuer Metadata* in the OpenID for Verifiable Credential Issuance [OpenID4VCI] specification.

Since one of the key scopes of OpenID Federation 1.0 is to provide consistency in the metadata of the participants described in the Entity Statements, be this Entity Configurations or Subordinate Statements, this specification requires the cryptographic material used for the Credential issuance operation be consistent and verifiable using the Trust Chain. For this reason the following metadata parameters are added:

| Metadata Parameter | Status | Description |
|------|----------|---------------------------------------------------------------------------------------------------------------------|
| jwks | REQUIRED | JSON Web Key Set directly embeds the public keys used by the Credential Issuer for its signature operations, such as signing Digital Credentials. |

Below is a non-normative example of a payload of an Credential Issuer Entity Configuration:

````
{
    "iat": 1718207217,
    "exp": 1749743216,
    "iss": "https://eaa-provider.example.org",
    "sub": "https://eaa-provider.example.org",
    "authority_hints": [
        "https://trust-anchor.example.org"
    ],
    "jwks": {
        "keys": [
            {
                "kid": "FANFS3YnC9tjiCaivhWLVUJ3AxwGGz_98uRFaqMEEs",
                "kty": "EC",
                "crv": "P-256",
                "x": "jE2RpcQbFQxKpMqehahgZv6smmXD0i/LTP2QRzMADk4",
                "y": "qkMx5iqt5PhPu5tfctS6HsP+FmLgrxfrzUV2GwMQuh8"
            }
        ]
    },
    "metadata": {
        "federation_entity": {
            "homepage_uri": "https://eaa-provider.example.org/",
            "organization_name": "Organization Name",
            "contacts": [
                "informazioni@example.it",
                "protocollo@pec.example.it"
            ],
            "tos_uri": "https://eaa-provider.example.org/public/info_policy.html",
            "policy_uri": "https://eaa-provider.example.org/public/privacy_policy.html",
            "logo_uri": "https://eaa-provider.example.org/public/logo.svg"
        },
        "oauth_authorization_server": {
            "issuer": "https://eaa-provider.example.org",
            "pushed_authorization_request_endpoint": "https://eaa-provider.example.org/as/par",
            "authorization_endpoint": "https://eaa-provider.example.org/authorize",
            "token_endpoint": "https://eaa-provider.example.org/token",
            "client_registration_types_supported": [
                "automatic"
            ],
            "code_challenge_methods_supported": [
                "S256"
            ],
            "scopes_supported": [
                "EuropeanDisabilityCard",
                "EuropeanHealthInsuranceCard",
                "MDL"
            ],
            "response_modes_supported": [
                "form_post.jwt",
                "query"
            ],
            "authorization_signing_alg_values_supported": [
                "ES256",
                "ES384",
                "ES512"
            ],
            "grant_types_supported": [
                "authorization_code"
            ],
            "token_endpoint_auth_methods_supported": [
                "attest_jwt_client_auth"
            ],
            "token_endpoint_auth_signing_alg_values_supported": [
                "ES256",
                "ES384",
                "ES512"
            ],
            "request_object_signing_alg_values_supported": [
                "ES256",
                "ES384",
                "ES512"
            ],
            "jwks": {
                "keys": [
                    {
                        "kid": "f10aca0992694b3581f6f699bfc8a2c6cc687725",
                        "kty": "EC",
                        "crv": "P-256",
                        "x": "jE2RpcQbFQxKpMqehahgZv6smmXD0i/LTP2QRzMADk4",
                        "y": "qkMx5iqt5PhPu5tfctS6HsP+FmLgrxfrzUV2GwMQuh8"
                    }
                ]
            }
        },
        "openid_credential_issuer": {
            // ... parameters defined in OpenID4VCI ...
            "jwks": {
                "keys": [
                    {
                        "kid": "f10aca0992694b3581f6f699bfc8a2c6cc687725",
                        "kty": "EC",
                        "crv": "P-256",
                        "x": "jE2RpcQbFQxKpMqehahgZv6smmXD0i/LTP2QRzMADk4",
                        "y": "qkMx5iqt5PhPu5tfctS6HsP+FmLgrxfrzUV2GwMQuh8"
                    }
                ]
            }
        }
}
````


## Metadata for OpenID Wallet Relying Party

The OpenID Federation Entity Type Identifier for the Wallet Relying Party is `openid_wallet_relying_party`.

This specification introduces a distinct Entity Type Identifier for the OpenID Wallet Relying Party to clearly differentiate it from a traditional OpenID Connect Relying Party (`openid_relying_party`). This distinction highlights the unique characteristics and functionalities of the Wallet ecosystem and its Wallet Relying Party.

The metadata parameters for an OpenID Wallet Relying Party are defined below:

| Metadata Parameter | Description | Reference |
|----------------------------------------|---------------------------------------------------------------------------------------------------------------------------|-----------|
| client_id | It MUST contain an HTTPS URL that uniquely identifies the RP. | RFC 7591 Section 3.2.1 and OpenID Connect Dynamic Client Registration 1.0 Section 3.2 |
| client_name | Human-readable string name of the RP. | RFC 7591 Section 2 |
| request_uris | JSON Array of request_uri values that are pre-registered by the RP. These URLs MUST use the https scheme. | OpenID Connect Dynamic Client Registration 1.0 Section 2 |
| response_uris_supported | JSON Array of response URI strings to which the Wallet Instance MUST send the Authorization Response using an HTTP POST request. | this specification |
| authorization_signed_response_alg | String identifying the JWS algorithm that MUST be used for signing authorization responses. The algorithm `none` MUST NOT be used. | RFC 7515 and oauth-v2-jarm Section 3 |
| vp_formats | JSON object defining the formats and proof types of Verifiable Presentations and Verifiable Credentials the RP supports. | OpenID4VC-HAIP Draft 00 Section 7.2.7 and OpenID4VP Draft 20 Section 9.1 |
| presentation_definitions_supported | JSON Array of supported presentation_definition objects that MUST be compliant to the syntax defined. | this specification, DIF.PresentationExchange Section 5 and OpenID4VC-HAIP Draft 00 Section 7.2.8 |
| jwks | JSON Web Key Set document, passed by value, containing the protocol specific keys for the Relying Party. | oauth-v2-jarm Section 3, OpenIDFed Draft 36 Section 5.2.1, and JWK |

Below a non normative example of the payload of a Wallet Relying Party Entity Configuration:

````
{
    "iat": 1718207217,
    "exp": 1749743216,
    "iss": "https://relying-party.example.org",
    "sub": "https://relying-party.example.org",
    "authority_hints": [
        "https://trust-anchor.example.org"
    ],
    "jwks": {
        "keys": [
            {
                "kid": "FANFS3YnC9tjiCaivhWLVUJ3AxwGGz_98uRFaqMEEs",
                "kty": "EC",
                "crv": "P-256",
                "x": "jE2RpcQbFQxKpMqehahgZv6smmXD0i/LTP2QRzMADk4",
                "y": "qkMx5iqt5PhPu5tfctS6HsP+FmLgrxfrzUV2GwMQuh8"
            }
        ]
    },
    "metadata": {
        "federation_entity": {
            "homepage_uri": "https://relying-party.example.org",
            "organization_name": "Organization Name",
            "contacts": [
                "informazioni@example.it",
                "protocollo@pec.example.it"
            ],
            "tos_uri": "https://relying-party.example.org/public/info_policy.html",
            "policy_uri": "https://relying-party.example.org/public/privacy_policy.html",
            "logo_uri": "https://relying-party.example.org/public/logo.svg"
        },
        "openid_wallet_relying_party": {
            "application_type": "web",
            "client_id": "https://relying-party.example.org",
            "client_name": "Organization Name",
            "contacts": [
                "informazioni@example.it"
            ],
            "request_uris": [
                "https://relying-party.example.org/request_uri"
            ],
            "response_uris_supported": [
                "https://relying-party.example.org/response_uri"
            ],
            "authorization_signed_response_alg": "ES256",
            "vp_formats": {
                "vc+sd-jwt": {
                    "sd-jwt_alg_values": [
                        "ES256",
                        "ES384",
                        "ES512"
                    ]
                }
            },
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
            "jwks": {
                "keys": [
                    {
                        "kid": "f10aca0992694b3581f6f699bfc8a2c6cc687725",
                        "kty": "EC",
                        "crv": "P-256",
                        "x": "jE2RpcQbFQxKpMqehahgZv6smmXD0i/LTP2QRzMADk4",
                        "y": "qkMx5iqt5PhPu5tfctS6HsP+FmLgrxfrzUV2GwMQuh8"
                    }
                ]
            }
        }
    }
}
````

### Security Considerations About The Parameters request_uris And response_uris_supported

There are scenarios where the RP's endpoints are attested by a trusted third party, such as a registration service owned by a federation Intermediate. This Entity not only attests to the RP's metadata but also ensures its integrity and authenticity by utilizing `metadata` and the `metadata_policy` in the Subordinate Statement about that RP, as defined in the OpenID Federation specification.

In these cases, the RP is restricted from altering its URIs due to the registration requirements, unless it utilizes URI fragments as permitted under the OpenID Connect Core 1.0 specification.

To enhance the security of implementations, it is generally recommended that Relying Parties (RPs) randomize their endpoints. This strategy involves appending random fragments to the URIs, such as https://rp.example.org/request-uri#random-value. Such randomization can help mitigate certain types of attacks by making it more difficult for attackers to predict or reuse fixed endpoint URLs, that could be victim of abuse, such as the one caused by the issuance of many signed responses that may facilitate resource consuptions attacks.

For this reason the parameters `metadata` or `metadata_policy` SHOULD fix the supported URIs to prevent wallet hijacks to fraudolent endpoints and at the same time allow URI randomization using fragments.

### Security Considerations About The User's Data Protection Using presentation_definitions_supported

The `presentation_definitions_supported` enhance the End-User data protection within OpenID trust framework. By defining the specific presentation definitions that a Relying Party (RP) is authorized to use, this parameter limits the amount of personal data that can be requested. This constraint prevents the over-asking of personal data, aligning with the principles of data minimization and purpose limitation under privacy regulations.

The `metadata` and `metadata_policy` parameters can be used in the Federation Subordinate Statements, issued by the Trust Anchor and its Intermediates, to configure these limitations. They ensure that only the necessary data as defined by the federation's policy is requested and processed by the RP. This approach not only protects End-User privacy but also builds trust in the federation system by ensuring that all data collection practices are transparent and compliant with established policies.


### Metadata for the Authentic Sources

The Authentic Source is a designated authority or system responsible for providing verified and reliable data. It is up to the implementers to decide if the Authentic Source should be implemented using the OAuth 2.0 framework, which offers robust security protocols and extensive support for diverse authentication scenarios. In these cases, the Authentice Sources represent an OAuth 2.0 Resource Server and therefore the metadata type used is `oauth_protected_resource`, as defined in [OAuth 2.0 Protected Resource Metadata](https://datatracker.ietf.org/doc/draft-ietf-oauth-resource-metadata/).

The Authentic Source provides reliable and accurate data about resources or information about a subject. It's up to the implementers to decide if the Authentic Source is implemented using the OAuth 2.0 framework. In these cases, the Authentic Sources represent an OAuth 2.0 Resource Server and therefore the metadata type used is `oauth_protected_resource`, as defined in OAuth 2.0 Protected Resource Metadata. This approach ensures that the Authentic Source can securely manage access to resources, leveraging the robust security mechanisms provided by OAuth 2.0.

This section provides some common configurations for ...

```
non norm example 1
```

```
non norm example 2
```

# Federation Policies

Policies refer to a set of rules that govern the operations, security, and interactions within a federation.

Technical implementation of federation policies over participants metadata is managed with the use of `metadata` and `metadata_policy` parameters in Subordinate Statements. These parameters allow for the configuration enforcement of application-specific metadata changes for each subject (Leaf).

Qualitative aspects of federation entities, including administrative protocols, security measures, and behavioral profiling, are regulated by Trust Marks. These marks provide verifiable assertions of compliance with specific profiles beyond the scope of the application specific metadata.


## Using Metadata

Metadata refers to application specific properties about a subject and for the purpose of the interoperability. This includes details such as service endpoints, cryptographic keys, and other supported configurations.

OpenID Federation allows the definition of any custom metadata schema, since it doesn't limit the implementation to OAuth 2.0 and OpenID Connect only.

Metadata within a Subordinate Statement allows for arbitrary modifications to the metadata published in a Leaf's Entity Configuration.

TBD: example 1 about problem solved with a Subordinate Statement metadata parameter [sanitize endpoints]

TBD: example 2 about problem solved with a Subordinate Statement metadata parameter [name of the organization]

TBD: example 3 about problem solved with a Subordinate Statement metadata parameter [data that can be requested by an RP]

TBD: example 4 about problem solved with a Subordinate Statement metadata parameter [digital credential configuration supported by a VCI]

## Differences Between metadata and metadata_policies

The key difference between `metadata` and `metadata_policy` is that metadata directly affects only the Immediate Subordinate Entity, while `metadata_policy` impacts the configuration of all Subordinate Entities along a Trust Chain.

This distinction positions the `metadata` parameter as an ideal tool for federation authorities managing entity registrations and needing to sanitize Leaves configurations in an arbitrary way. The Trust Anchor (TA) and Intermediate (INT) sanitize an Entity Configuration during technical tests and finalize it by setting specific metadata parameters.

## Using Metadata Policies

Differently from `metadata`, `metadata_policy` ensures that specific settings can be propagated to all the entities statements contained within a Trust Chain.

TBD: example 1 about problem solved with a Subordinate Statement metadata_policy parameter [weak sign alg disabled for everybody along the chain]

TBD: example 2 about problem solved with a Subordinate Statement metadata_policy parameter [resolve endpoint overwritted to the one provided by the TA]


## Using Trust Marks

Trust Marks are issued by authorized entities (Trust Mark Issuers) within the federation, typically after an entity has demonstrated compliance with certain standards, this might happend through auditing or certification processes.

Trust Marks are typically implemented as signed assertions that can be verified by other entities. 

This verification process involves checking the digital signature against the public key of the Trust Mark Issuer to ensure the Trust Mark has not been forged, and its check to the Trust Mark Status endpoint to check it against any revocation.

Trust Marks SHOULD be defined within the trust framework. Trust Marks are asserted about a subject through a registration service or compliance evaluation mechanism and therefore included in subject's Entity Configurations. This allows other entities to quickly assess the compliance status of a subject by examining the Entity Configuration of a subject.

TBD: example 1 about problem solved with a Subordinate Statement trust_marks parameter [under age user]

TBD: example 2 about problem solved with a Subordinate Statement trust_marks parameter [eidas trust list registration]

TBD: example 3 about problem solved with a Subordinate Statement trust_marks parameter [disabled user accessibility compliance]

# Federation Trust Discovery Use Cases

The process of trust establishment in federated environments is  illustrated in this section through specific use cases involving Wallet Instances, Credential Issuers (CIs), and Relying Parties (RPs).

## Wallet Checking The Non-Revocation Of Its Wallet Provider

...

## Wallet Discovering The Credentials Issuers

Wallets begin by discovering the identity of Credential Issuers through the federation's trust infrastructure. This involves retrieving the Credential Issuer's Entity Configuration and verifying its Trust Chain up to a recognized Trust Anchor. The Credential Issuer’s Entity Configuration provides essential information, including its roles within the federation, policies it adheres to, and cryptographic keys for secure communication.

In the example represented in the sequence diagram below, the Wallet Instance uses the Federation API to discover and collect all the Credential Issuers enabled within the federation.


````mermaid
sequenceDiagram
    participant Wallet
    participant TA as Trust Anchor
    participant IM as Intermediate
    participant CI as Credential Issuer (CI)

    Wallet->>TA: Fetch the list of all Intermediates and CIs
    loop for each Intermediate
        Wallet->>IM: Fetch listing of Subordinates/CIs
    end
    loop for each CI
        Wallet->>CI: Fetch CI's Entity Configuration
        Wallet->>IM: Fetch Subordinate Statement(s) for CI
        Wallet->>TA: Fetch Subordinate Statement for Intermediate(s)
        Wallet->>Wallet: Validate Trust Chain for CI
        Wallet->>Wallet: Include CI in Discovery Page<br/>with validated information and logo
    end
````
The diagram above shows how a Wallet navigates the federation, collecting and validating the Trust Chain for each Credential Issuer (CI), and creating a discovery page including each Credential Issuer using the information, such as the credential types and logo obtained through their Trust Chain.

The diagram below illustrates how a Wallet establishes trust with a CI by verifying its link (even if indirect) to a Trust Anchor and validating which credentials it's authorized to issue. This may happen in a credential offer flow, for instance, where the Wallet is used by an End-User starting from the Credential Issuer website and without any discovery phases started before within the Wallet.

````mermaid
sequenceDiagram
    participant Wallet
    participant CI as Credential Issuer
    participant IE as Intermediate Entities
    participant TA as Trust Anchor

    Wallet->>CI: Fetch CI's Entity Configuration
    CI->>Wallet: Return Entity Configuration

    Wallet->>Wallet: Extract Authority Hints from CI's Configuration

    loop for each Authority Hint
        Wallet->>IE: Fetch Entity Configuration
        Wallet->>IE: Fetch Subordinate Statement
        Wallet->>Wallet: Validate the previous statement<br/>using the Federation Entity Keys<br/>provided in the Subordinate Statement
    end

    Wallet->>Wallet: Validate Trust Chain

    alt If Trust Chain is Valid and Unexpired
        Wallet->>Wallet: Proceed with Federation Process
    else
        Wallet->>Wallet: Abort Process with Error
    end

    Wallet->>Wallet: Applies Policies
    Wallet->>Wallet: Derive CI's final metadata
    Wallet->>Wallet: Get available Credentials allowed for issuance
````

## Credential Issuers Establishing Trust With The Wallet Provider

...

## Credential Issuers Establishing Trust With The Wallet

...

## Wallet Establish Trust With The Relying Party

The Federation Entity Discovery starts with the Wallet Instance fetching the Relying Party's Entity Configuration to identify authority hints, pointing to Federation Entities that can issue Subordinate Statements about the Relying Party. The Wallet Instance then follows these hints and collects the Subordinate Statements and validating each one. The process continues until the Wallet Instance reaches the Trust Anchor. Finally, the Wallet Instance compiles the validated Trust Chain. If the Trust Chain is valid, the Wallet Instance processes the Relying Party final metadata.

Note: While this section exemplifies the journey of discovery from the perspective of an OpenID Wallet Instance, it's important to understand that this approach can be applied to every kind of entity type within the federation.

````mermaid
sequenceDiagram
    participant WalletInstance as Wallet Instance
    participant RP as Relying Party
    participant IE as Intermediate
    participant TA as Trust Anchor

    WalletInstance->>RP: Fetch RP's Entity Configuration
    RP->>WalletInstance: Return Entity Configuration

    WalletInstance->>RP: Extract Authority Hints from RP's Configuration
    RP->>WalletInstance: Provide Authority Hints

    loop for each Authority Hint
        WalletInstance->>IE: Fetch Entity Configuration -> get federation_fetch_api URL
        IE->>WalletInstance: Fetch Subordinate Statement
        WalletInstance->>WalletInstance: Validate the previous statement<br/>using the Federation Entity Keys<br/>provided in the Subordinate Statement
    end

    WalletInstance->>WalletInstance: Validate Trust Chain

    alt If Trust Chain is valid and unexpired
        WalletInstance->>WalletInstance: Proceed with Federation Process
    else
        WalletInstance->>WalletInstance: Abort Process with Error
    end

    WalletInstance->>WalletInstance: Applies Policies
    WalletInstance->>WalletInstance: Derive RP's final metadata
````


# Implementation Considerations for the Offline Flows

TBD: usage of static trust chain or x.509 ceritifcate chain

# Acknowledgments

We would like to thank the following individuals for their comments, ideas, and contributions to this implementation profile and to the initial set of implementations.

- Leif Johansson
- Stefan Liström
- Francesco Antonio Marino
- Giada Sciarretta

{backmatter}


<reference anchor="VC_DATA" target="https://www.w3.org/TR/vc-data-model">
  <front>
    <title>Verifiable Credentials Data Model 1.1</title>
    <author fullname="Manu Sporny">
      <organization>Digital Bazaar</organization>
    </author>
    <author fullname="Grant Noble">
      <organization>ConsenSys</organization>
    </author>
    <author fullname="Dave Longley">
      <organization>Digital Bazaar</organization>
    </author>
    <author fullname="Daniel C. Burnett">
      <organization>ConsenSys</organization>
    </author>
    <author fullname="Brent Zundel">
      <organization>Evernym</organization>
    </author>
    <author fullname="Kyle Den Hartog">
      <organization>Mattr</organization>
    </author>
   <date day="3" month="March" year="2022"/>
  </front>
</reference>

<reference anchor="VC_DATA_2.0" target="https://www.w3.org/TR/vc-data-model-2.0">
  <front>
    <title>Verifiable Credentials Data Model 2.0</title>
    <author fullname="Manu Sporny">
      <organization>Digital Bazaar</organization>
    </author>
    <author fullname="Ted Thibodeau Jr">
      <organization>OpenLink Software</organization>
    </author>
    <author fullname="Ivan Herman">
      <organization>W3C</organization>
    </author>
    <author fullname="Michael B. Jones">
      <organization>Invited Expert</organization>
    </author>
    <author fullname="Gabe Cohen">
      <organization>Block</organization>
    </author>
   <date day="27" month="December" year="2023"/>
  </front>
</reference>

<reference anchor="VC_Data_Integrity" target="https://w3c.github.io/vc-data-integrity/">
  <front>
    <title>Verifiable Credential Data Integrity 1.0</title>
    <author fullname="Manu Sporny">
      <organization>Digital Bazaar</organization>
    </author>
    <author fullname="Dave Longley">
      <organization>Digital Bazaar</organization>
    </author>
    <author fullname="Greg Bernstein">
      <organization>Invited Expert</organization>
    </author>
    <author fullname="Dmitri Zagidulin">
      <organization>Invited Expert</organization>
    </author>
    <author fullname="Sebastian Crane">
      <organization>Invited Expert</organization>
    </author>
   <date day="14" month="November" year="2023"/>
  </front>
</reference>

<reference anchor="USASCII">
        <front>
          <title>Coded Character Set -- 7-bit American Standard Code for Information Interchange</title>
          <author>
            <organization>American National Standards Institute</organization>
          </author>
          <date year="1986"/>
        </front>
</reference>

<reference anchor="SIOPv2" target="https://openid.net/specs/openid-connect-self-issued-v2-1_0.html">
  <front>
    <title>Self-Issued OpenID Provider V2</title>
    <author fullname="Kristina Yasuda">
      <organization>Microsoft</organization>
    </author>
    <author fullname="Michael B. Jones">
      <organization>Self-Issued Consulting</organization>
    </author>
    <author initials="T." surname="Lodderstedt" fullname="Torsten Lodderstedt">
      <organization>sprind.org</organization>
    </author>
   <date day="28" month="November" year="2023"/>
  </front>
</reference>

<reference anchor="BCP195" target="https://www.rfc-editor.org/info/bcp195">
        <front>
          <title>BCP195</title>
          <author>
            <organization>IETF</organization>
          </author>
          <date month="November" year="2022"/>
        </front>
</reference>

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

<reference anchor="DIF.Well-Known_DID" target="https://identity.foundation/specs/did-configuration/">
        <front>
          <title>Well Known DID Configuration</title>
      <author fullname="Daniel Buchner">
            <organization>Microsoft</organization>
          </author>
          <author fullname="Orie Steele">
            <organization>Transmute</organization>
          </author>
          <author fullname="Tobias Looker">
            <organization>Mattr</organization>
          </author>
        </front>
</reference>

<reference anchor="CSS-Color" target="https://www.w3.org/TR/css-color-3">
      <front>
        <title>CSS Color Module Level 3</title>
        <author initials="T." surname="Çelik" fullname="Tantek Çelik">
         <organization>Mozilla Corporation</organization>
        </author>
        <author initials="C." surname="Lilley" fullname="Chris Lilley">
          <organization>W3C</organization>
        </author>
        <author initials="D." surname="Baron" fullname="L. David Baron">
          <organization>W3C Invited Expert</organization>
       </author>
       <date day="18" month="January" year="2022"/>
      </front>
</reference>

<reference anchor="JSON-LD" target="https://www.w3.org/TR/json-ld11/">
      <front>
        <title>JSON-LD 1.1: A JSON-based Serialization for Linked Data.</title>
        <author fullname="Gregg Kellogg">
        </author>
        <author fullname="Manu Sporny">
        </author>
        <author fullname="Dave Longley">
       </author>
       <author fullname="Markus Lanthaler">
       </author>
       <author fullname="Pierre-Antoine Champin">
       </author>
       <author fullname="Niklas Lindström">
       </author>
       <date day="16" month="July" year="2020"/>
      </front>
</reference>

<reference anchor="ISO.18013-5" target="https://www.iso.org/standard/69084.html">
        <front>
          <title>ISO/IEC 18013-5:2021 Personal identification — ISO-compliant driving licence — Part 5: Mobile driving licence (mDL)  application</title>
          <author>
            <organization> ISO/IEC JTC 1/SC 17 Cards and security devices for personal identification</organization>
          </author>
          <date Month="September" year="2021"/>
        </front>
</reference>

<reference anchor="IANA.JOSE.ALGS" target="https://www.iana.org/assignments/jose/jose.xhtml#web-signature-encryption-algorithms">
        <front>
          <title>JSON Web Signature and Encryption Algorithms</title>
          <author>
            <organization>IANA</organization>
          </author>
        </front>
</reference>

<reference anchor="IANA.COSE.ALGS" target="https://www.iana.org/assignments/cose/cose.xhtml#algorithms">
        <front>
          <title>COSE Algorithms</title>
          <author>
            <organization>IANA</organization>
          </author>
        </front>
</reference>

<reference anchor="OpenID4VP" target="https://openid.net/specs/openid-4-verifiable-presentations-1_0.html">
      <front>
        <title>OpenID for Verifiable Presentations</title>
        <author initials="O." surname="Terbu" fullname="Oliver Terbu">
         <organization>Mattr</organization>
        </author>
        <author initials="T." surname="Lodderstedt" fullname="Torsten Lodderstedt">
          <organization>sprind.org</organization>
        </author>
        <author initials="K." surname="Yasuda" fullname="Kristina Yasuda">
          <organization>Microsoft</organization>
        </author>
        <author initials="T." surname="Looker" fullname="Tobias Looker">
          <organization>Mattr</organization>
        </author>
       <date day="29" month="November" year="2022"/>
      </front>
</reference>

<reference anchor="DID_Specification_Registries" target="https://www.w3.org/TR/did-spec-registries/">
        <front>
          <title>DID Specification Registries</title>
      <author fullname="Orie Steele">
            <organization>Transmute</organization>
          </author>
          <author fullname="Manu Sporny">
            <organization>Digital Bazaar</organization>
          </author>
         <date day="11" month="September" year="2023"/>
        </front>
</reference>

<reference anchor="LD_Suite_Registry" target="https://w3c-ccg.github.io/ld-cryptosuite-registry/">
        <front>
          <title>Linked Data Cryptographic Suite Registry</title>
          <author fullname="Manu Sporny">
            <organization>Digital Bazaar</organization>
          </author>
          <author fullname="Drummond Reed">
            <organization>Evernym</organization>
          </author>
          <author fullname="Orie Steele">
            <organization>Transmute</organization>
          </author>
         <date day="29" month="December" year="2020"/>
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
          <date day="4" month="December" year="2023"/>
        </front>
</reference>

<reference anchor="IANA.OAuth.Parameters" target="https://www.iana.org/assignments/oauth-parameters">
  <front>
    <title>OAuth Parameters</title>
    <author>
      <organization>IANA</organization>
    </author>
    <date/>
  </front>
</reference>

<reference anchor="IANA.MediaTypes" target="https://www.iana.org/assignments/media-types">
  <front>
    <title>Media Types</title>
    <author>
      <organization>IANA</organization>
    </author>
    <date/>
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

<reference anchor="ISO.29100" target="https://standards.iso.org/ittf/PubliclyAvailableStandards/index.html">
  <front>
    <author fullname="ISO"></author>
    <title>ISO/IEC 29100:2011 Information technology — Security techniques — Privacy framework</title>
  </front>
</reference>

# Document History

   [[ To be removed from the final specification ]]

   -00 

   *  initial revision
