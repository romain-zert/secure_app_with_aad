# How to secure your app with Azure AD
This repo explain how to secure your azure application (internal or external) leveragin Azure AD

 - **Scenario 1: User authentication to a backend

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
            U->>AAD: enter login
            AAD-->>U: IdToken and AccessToken
        end
        SPA->>APIM: execute REST call
        Note right of SPA: apikey + access token
        APIM->>AAD: checktoken
        APIM->>W: same request with access token
        opt aad key not in cache
            W->>AAD: get public key en save it localy
        end
        W->>W: check signature of access token and validate metadata
        W->>W: decode token and read approle from the claims
        W->>SPA: send response        

```

 - Scenario 2: External service authentication to backend 

```mermaid
    sequenceDiagram
        actor U as External service
        participant W as Web Backend
        U->>AAD: navigate to web page url
        U->>U: check if the user have a token in cache or not expired
        opt no token or token invalid
            U->>+AAD: request token
            Note right of U: scope: webapplication/.webcustomer clientId and secret/ certificate
            AAD-->>-U: IdToken and AccessToken
        end
        U->>APIM: execute REST call
        Note right of U: apikey + access token        
        APIM->>AAD: checktoken
        APIM->>W: same request with access token
        opt aad key not in cache
            W->>AAD: get public key en save it localy
        end
        W->>W: check signature of access token and validate metadata
        W->>W: decode token and read approle from the claims
        W->>U: send response        

```

 - **Scenario 6: On behalf scenario

```mermaid
    sequenceDiagram
        actor U as User
        participant SPA as SPA application (browser)
        participant W as Web Backend
        participant W2 as User info api
        U->>SPA: navigate to web page url
        SPA->>SPA: check if the user have a token in cache (cookie/localstorage)
        opt no token or token invalid
            SPA->>U: redirect to aad login
            Note right of SPA: scope: webapplication/.webcustomer
            U->>AAD: enter login
            AAD-->>U: IdToken and AccessToken
        end
        SPA->>APIM: execute REST call
        Note right of SPA: apikey + access token
        APIM->>AAD: checktoken
        APIM->>W: same request with access token
        opt aad key not in cache
            W->>AAD: get public key en save it localy
        end
        W->>W: check signature of access token and validate metadata
        W->>+AAD: request token for user info API
        Note right of W: access token + client and secret ID of backend
        AAD-->>-W: access token for user info api
        W->>+W2: request user info as user
        opt aad key not in cache
            W2->>AAD: get public key en save it localy
        end
        W2->>W2: check signature of access token and validate metadata
        W2-->>-W: send user info        
        W->>SPA: send user info

```