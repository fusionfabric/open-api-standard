---
layout: default
title: Security
nav_order: 12
---

# Security
{: .no_toc}

This section provides an overview of Security considerations for Finastra APIs:
- TOC
{:toc}

## Introduction

Finastra APIs must define a **channel type** using the `x-finastra-channel-type` attribute - the channel type can be one of the following:
- **B2B** - Business-to-Business
- **SERVICE** - Business-to-Business for specific Finastra services 
- **B2C** - Business-to-Customer
- **B2E** - Business-to-Employee

A single API cannot define more than one channel type and client application must be aware of the API type 
so that the correct token is obtained.

The channel type determines the **OAuth2 flow** that will be used by the API: 
- **Client Credentials** - for B2B and SERVICE APIs for server to server communication
- **Authorization Code** - for B2C and B2E APIs for customer and employee communication

These flows are detailed in [RFC 6749](https://datatracker.ietf.org/doc/html/rfc6749), section 4 .

| Rule Identifier  | Description  |
|:-------:|:------------ |
| SEC-001 | Finastra Open APIs **MUST** secure endpoints with oAuth2 |


## API Channel Types

This section provides extra details on Finastra channel types:

- **B2B** - Business-to-Business
  - B2B APIs are used to build apps whose target is another business system
  - B2B APIs support server-to-server data interchange using tokens and associated token refresh policies
  - B2B APIs support the OAuth2 Client Credentials authentication flow
  - With B2B APIs, authentication is targeted at a technical account; user level authentication is not available

- **SERVICE** 
  - SERVICE APIs identify APIs that are implemented by Finastra rather than a financial institution e.g. IBAN validation 
  - SERVICE APIs have the same attributes as B2B APIs

- **B2C** - Business-to-Customer	
  - B2C APIs are used to build apps targeting customers of a Financial Institution
  - B2C APIs support mobile banking applications on browsers, smartphones and tablets
  - B2C APIs support the OAuth2 Authorization Code grant flow
  - With B2C APIs, authentication is targeted at a customer held in a financial institution's identity provider

- **B2E** - Business-to-Employee
  - B2E APIs ares used to build apps whose target is the employee of a financial institution
  - B2E APIs support the OAuth2 Authorization Code grant flow
  - With B2E APIs, authentication is targeted at an employee held in a financial institution's identity provide


## API Security Definitions

Authentication for a specific channel types is described by using combinations of the Open API specification (OAS2 or OAS3) security keywords.

At OAS2 the `securityDefinitions` keyword is used to define the authentication types supported by the API, and the `security` keyword then applies specific authentication types to the whole API (global setting) or to individual operations.

At OAS3 the `securitySchemes` keyword is used to define the authentication types supported by the API, and the `security` keyword then applies specific authentication types to the whole API (global setting) or to individual operations.

| Rule Identifier  | Description  |
|:-------:|:------------ |
| SEC-002 | For Finastra APIs the following security definitions are mandatory |

- OAS2 security definition used for Finastra B2C and B2E APIs

```yaml
security:
  - Oauth2: []
securityDefinitions:
  Oauth2:
    type: oauth2
    description: Oauth 2.0 authentication
    tokenUrl: 'https://any.local.keycloak:8443/login/v1/sandbox/auth'
    flow: application
```

- OAS2 security definition used for Finastra B2B APIs

```yaml
security:
  - Oauth2: []
securityDefinitions:
  Oauth2:
    type: oauth2
    description: Oauth 2.0 authentication
    authorizationUrl: 'https://any.local.keycloak:8443//login/v1/sandbox/oidc/authorize'
    tokenUrl: 'https://any.local.keycloak:8443/login/v1/sandbox/oidc/token'
    flow: accessCode
```

- OAS3 security definition used for Finastra B2C and B2E APIs

```yaml
security:
  - Oauth2: []
components:
  securitySchemes:
    Oauth2:
      type: oauth2
      description: Oauth 2.0 authentication
      flows:
        authorizationCode:
          authorizationUrl: 'https://any.local.keycloak:8443/login/v1/sandbox/oidc/auth'
          tokenUrl: 'https://any.local.keycloak:8443/login/v1/sandbox/oidc/token'
          scopes: {}
```

- OAS3 security definition used for Finastra B2B APIs

```yaml
security:
  - Oauth2: []
components:
  securitySchemes:
    Oauth2:
      type: oauth2
      description: Oauth 2.0 authentication
      flows:
        clientCredentials:
          tokenUrl: 'https://any.local.keycloak:8443/login/v1/sandbox/oidc/token'
```

## Data classification 

Finastra has a wide range of requirements to protect the confidentiality, integrity and availability of data under its custody. Finastra protects data by applying safeguards and controls which are appropriate given the sensitivity and criticality of the data.

Data within APIs is classified as one of the following:

1. Public data - for example Market data.
2. Restricted data - this is information that is protected under General Data Protection Regulation (GDPR) compliance or California Consumer Privacy ACT (CCPA).

For more information about GDPR/CCPA compliance please check [General Data Protection Regulation guide](https://gdpr-info.eu/)  and [California Consumer Privacy Act](https://leginfo.legislature.ca.gov/faces/codes_displayText.xhtml?lawCode=CIV&division=3.&title=1.81.5.&part=4.&chapter=&article=)

Where an API contains Restricted data the end user of the the API must apply appropriate privacy policies to protect the data, compliant with GDPR or CCPA. For example, data provided to Finastra from Banks for business intelligence purposes that is classified as `Restricted` must comply with regulations such as GDPR or CCPA when using the restricted data in apps.

Fields that are `Restricted` are shown in APIs with a custom Finastra tag named `x-finastra-data-classification`. 

The `Restricted` data classification tag is applied to the API, its endpoints and at parameter level.

If an endpoint uses a definition that is defined as `Restricted`, the endpoint is declared as `Restricted`, and similarly the API is declared as `Restricted`.

For example, in the following excerpt from the Account Information API, the `x-finastra-data-classification` field is added to the `GET /accounts` because data in the payload is restricted. 

```
"/accounts": {
    "get": {
      "tags": ["Accounts"],
      "summary": "Retrieve a Summary of Accounts for an Account Holder",
      "operationId": "accounts",
      "description": "This call returns a summary of all accounts owned by an account holder.",
      "parameters": [...],
      "responses": {...},
      "x-finastra-data-classification": ["Restricted"]
    }
  }
```

| Rule Identifier  | Description  |
|:-------:|:------------ |
| DCC-001 | Finastra APIs **MUST** undergo data classification before they can be published |
