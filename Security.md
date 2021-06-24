# Security

**Introduction**

Finastra APIs use one of the following OAuth2 flows: 
- Client Credentials - B2B for APIs targeted server to server communication
- Authorization Code - B2C and B2E for APIs targeted at customer and employee communication

These flows are detailed in [RFC 6749](https://datatracker.ietf.org/doc/html/rfc6749), section 4 .

> Finastra Open APIs MUST secure endpoints with oAuth2 

**Authentication**

Authentication is described by using combinations of the Open API specification (OAS2 or OAS3) security keywords.

At OAS2 the `securityDefinitions` keyword is used to define the authentication types supported by the API, and the `security` keyword then applies specific authentication types to the whole API (global setting) or to individual operations.

At OAS3 the `securitySchemes` keyword is used to define the authentication types supported by the API, and the `security` keyword then applies specific authentication types to the whole API (global setting) or to individual operations.

> For Finastra APIs the following security definitions are mandatory:


- OAS2 security definition used for Finastra B2C and B2E APIs

``` notoggle
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

``` notoggle
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

``` notoggle
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

``` notoggle
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


**Channel** 

Finastra API are split by 'Channel'. Split correspond the securities flow to a dedicated domain of usage. For instance to avoid avoid having an application mixing B2C, and B2B call.
Splitting by channel enforce that token are dedicated to a single channel. It means that given a client application you need to leverage on different set of credential. 

Finastra distinguishes 4 kind of channel. 


- B2B (Business-to-Business) : 
  - A B2B API is used to build apps whose target is another business. 
  - This channel supports server-to-server data interchange with good security level (token rotation).
  - Authentication is geared toward a technical account; user level authentication is not available.
  - Support the Client Credentials OAuth2 authentication flow

-	Digital Channel (B2C): 	
  - The Business-to-Customer channel (B2C) groups APIs that are used to build apps targeting customers of a financial institution. 
  - The APIs on this channel support mobile banking applications on browsers, smartphones and tablets. 
  - Support the Authorization Code grant flow.
  - Authentication is geared toward the institution's customers Identity provider.

- B2E (Business-to-Employee) Channel: 
  - A B2E API is used to build apps whose target is the employee of a Financial Institution. This category of users have
  - Authentication is geared toward the institution's employees.


- SERVICE Channel: 
  - The APIs available for the Service channel are useful to consume  financial services offered by Finastra or third-parties. 
  - They support authenticated server-to-server data interchange. 
  - Support the Client Credentials OAuth2 authentication flow.
  - Work with Finastra maintained tenants - finastra-dev, finastra-uat, and finastra-prod, corresponding to the promotion stage of your application.

- Platform : 
  - The APIs available for the Platform channel are used to interact with FusionFabric.cloud platform itself. They don’t provide financial or business data or services, but rather act as meta-APIs, to enable you manage your applications. 
  - Support Client Credentials OAuth2 authentication flow. 
  - Work with Finastra maintained tenants - platform-dev, platform-uat, and platform-prod, corresponding to the promotion stage of your application.

> API **MUST** specifies the channel use define in the OAS  `info` section `x-finastra-channel-type` with the various channel defined
