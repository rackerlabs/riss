# RAML Standards and Practices

## Internal Authentication/Authorization Security Scheme

```
#%RAML 0.8
---
description: Rackspace Internal Identity authentication
type: x-auth-token
describedBy:
  headers:
    X-Auth-Token:
      description: Used to send a valid Identity access token.
      type: string
  responses:
    401:
      description: |
        Bad or expired token. To fix, you should re-
        authenticate the user.
    403:
      description: |
        Bad request
settings:
  identityServiceUri: https://identity-internal.api.rackspacecloud.com/v2.0/
```
