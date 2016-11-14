The CodaLab REST API is still under heavy development, and interfaces may change at any time.

# Authentication

We use [OAuth 2.0](https://oauth.net/2/) for authentication and authorization.

All API requests to protected resources must include a valid OAuth bearer token as a HTTP header:
```
GET /rest/bundles/0x491808200c7e4e2798530d5cf9bdfdd HTTP/1.1
Content-Type: application/json
Authorization: Bearer WM0vteAcGpPhXEGtKmhCGjgVIOvYbB
```

Unauthorized requests will receive a `403 Forbidden` HTTP response.


## Resource Owner Password Credentials Grant

The easiest way to get an OAuth token is by requesting a token directly using a username and password.

### `POST /rest/oauth2/token`
```
Content-Type: application/x-www-form-urlencoded
Authorization: Basic Y29kYWxhYl9jbGlfY2xpZW50Og==
```

| Form Parameter    | Description |
| :---         |      :---      |
| `grant_type` | REQUIRED. Value MUST be set to `password`.                      | 
| `username`   | REQUIRED. The resource owner username or email address.         | 
| `password`   | REQUIRED. The resource owner password.                          | 
| `scope`      | OPTIONAL. Defaults to `default`, which provides full access to the user's resources. No other scopes are currently supported, though we may introduce them in the future.   | 

Example response:
```
{
    "access_token": "NT4xa9noJkBQMxwoL8AikZ6wuGDlUQ",
    "token_type": "Bearer",
    "expires_in": 3600,
    "refresh_token": "HWTsWyqTXmVJuran04OhyXua3k8VlL",
    "scope": "default"
}
```

# Resources

We follow the [JSON API](jsonapi.org) v1.0 specification for the most part, with additional support for bulk operations based on an [unofficial Bulk extension](https://github.com/json-api/json-api/blob/9c7a03dbc37f80f6ca81b16d444c960e96dd7a57/extensions/bulk/index.md).

Some complete examples will be provided to illustrate how this translated into full JSON objects.

## Bundles

### Search bundles
`GET /rest/bundles/`

| Query Parameter    | Description |
| :---         |      :---      |
| `keywords`   | MULTIPLE. Keywords for a search query, in the same form as in `cl search`.                 | 
| `specs  `    | MULTIPLE. Bundle specs to search for.         | 
| `worksheet`  | OPTIONAL. ID of the parent worksheet for resolving bundle specs.                    | 
| `depth`      | OPTIONAL. Include all descendants of the found bundles down by this depth. | 

`/rest/bundles/<uuid>`

`/rest/bundles/<uuid>/contents/info/`

`/rest/bundles/<uuid>/contents/blob/`


## Worksheets

`/rest/worksheets/<uuid>`

`/rest/worksheets/`

## Users



## Groups

`GET /groups`

Fetch all groups accessible by the authenticated user.
