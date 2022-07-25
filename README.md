# How to secure your app with Azure AD
This repo explain how to secure your azure application (internal or external) leveragin Azure AD

 - Scenario 1: User authentication to a backend

```mermaid
    sequenceDiagram
        actor U as User
        participant SPA as SPA application (browser)
        participant W as Web Backend
        U->>SPA: navigate to web page url
        SPA->>SPA: check if the user have a token in cache (cookie/localstorage)
        opt no token or token invalid
            SPA->>U: redirect to aad login
            Note right of SPA: scope: webapplication/.webcustomer
            U->>LoginAADPage: enter login
            LoginAADPage-->>U: IdToken and AccessToken
        end
        SPA->>APIM: execute REST call
        Note right of SPA: apikey + access token
        APIM->>W: same request with access token
        opt aad key not in cache
            W->>AAD: get public key en save it localy
        end
        W->>W: check signature of access token and validate metadata
        W->>W: decode token and read approle from the claims
        W->>SPA: send response        

```
