---
layout: default
title: Security
nav_order: 10
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

> Finastra Open APIs MUST secure endpoints with oAuth2 


## API Channel Types

This secion provides extra details on Finastra channel types:

- **B2B** - Business-to-Business
  - A B2B API is used to build apps whose target is another business
  - This channel supports server-to-server data interchange with good security level (token rotation)
  - Authentication is geared toward a technical account; user level authentication is not available
  - Support the Client Credentials OAuth2 authentication flow

- **DIGITAL** - Business-to-Customer	
  - The Business-to-Customer channel (B2C) groups APIs that are used to build apps targeting customers of a financial institution
  - The APIs on this channel support mobile banking applications on browsers, smartphones and tablets
  - Support the Authorization Code grant flow
  - Authentication is geared toward the institution's customers Identity provider

- **B2E** - Business-to-Employee
  - A B2E API is used to build apps whose target is the employee of a Financial Institution
  - Authentication is geared toward the institution's employees

- **SERVICE** 
  - The APIs available for the Service channel are useful to consume  financial services offered by Finastra or third-parties
  - They support authenticated server-to-server data interchange
  - Support the Client Credentials OAuth2 authentication flow
  - Work with Finastra maintained tenants - finastra-dev, finastra-uat, and finastra-prod, corresponding to the promotion stage of your application


> APIs **MUST** specify the channel use define in the OAS  `info` section `x-finastra-channel-type` with the various channel defined


## API Security Definitions

Authentication for a specific channel types is described by using combinations of the Open API specification (OAS2 or OAS3) security keywords.

At OAS2 the `securityDefinitions` keyword is used to define the authentication types supported by the API, and the `security` keyword then applies specific authentication types to the whole API (global setting) or to individual operations.

At OAS3 the `securitySchemes` keyword is used to define the authentication types supported by the API, and the `security` keyword then applies specific authentication types to the whole API (global setting) or to individual operations.

> For Finastra APIs the following security definitions are mandatory:


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

