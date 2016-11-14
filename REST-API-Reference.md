The CodaLab REST API is still under heavy development, and interfaces may change at any time.

We follow the [JSON API](jsonapi.org) v1.0 specification for the most part, with additional support for bulk operations based on an [unofficial Bulk extension](https://github.com/json-api/json-api/blob/9c7a03dbc37f80f6ca81b16d444c960e96dd7a57/extensions/bulk/index.md).

Some complete examples will be provided to illustrate how this translated into full JSON objects.

# Authentication

We use [OAuth 2.0](https://oauth.net/2/) for authentication and authorization.

All API requests to protected resources must include a valid OAuth bearer token as a HTTP header:
```
GET /rest/bundles/0x491808200c7e4e2798530d5cf9bdfdd HTTP/1.1
Content-Type: application/json
Authorization: Bearer WM0vteAcGpPhXEGtKmhCGjgVIOvYbB
```

Unauthorized requests will receive a `403 Forbidden` HTTP response.

# Bundles

`/rest/bundles/`

`/rest/bundles/<uuid>`

`/rest/bundles/<uuid>/contents/info/`

`/rest/bundles/<uuid>/contents/blob/`


# Worksheets

`/rest/worksheets/<uuid>`

`/rest/worksheets/`

# Users


# Groups

`GET /groups`

Fetch all groups accessible by the authenticated user.
