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

### Get bundle info
`GET /rest/bundles/<uuid>`

Example response:
```
{
    "included": [
        {
            "attributes": {
                "first_name": "Percy",
                "last_name": "Liang",
                "url": null,
                "affiliation": "Stanford",
                "user_name": "codalab",
                "date_joined": null
            },
            "type": "users",
            "id": "0"
        },
        {
            "relationships": {
                "group": {
                    "data": {
                        "type": "groups",
                        "id": "0x20b3fdd2415d4200a0817471aa38fc58"
                    }
                }
            },
            "attributes": {
                "permission": 1,
                "permission_spec": "read",
                "group_name": "public"
            },
            "type": "bundle-permissions",
            "id": "2137"
        }
    ],
    "meta": {
        "version": "0.2.0"
    },
    "data": {
        "relationships": {
            "owner": {
                "data": {
                    "type": "users",
                    "id": "0"
                }
            },
            "group_permissions": {
                "data": [
                    {
                        "type": "bundle-permissions",
                        "id": "2137"
                    }
                ]
            },
            "children": {
                "data": []
            }
        },
        "attributes": {
            "host_worksheets": [
                {
                    "uuid": "0x72a6926d82dd4b9d98f05d8c566ebdaf",
                    "name": "home-codalab"
                }
            ],
            "data_hash": "0x4bc7f3f5f4e1c02cf54c5df3ec1adb5cbda43a13",
            "uuid": "0x491808200c7e4e2798530d5cf9bdfdd1",
            "permission": 1,
            "args": "run date",
            "state": "ready",
            "dependencies": [],
            "command": "date",
            "bundle_type": "run",
            "permission_spec": "read",
            "metadata": {
                "allow_failed_dependencies": false,
                "last_updated": 1479156285,
                "actions": [],
                "request_cpus": 0,
                "run_status": "Finished",
                "memory_max": 0,
                "request_time": "",
                "request_queue": "",
                "request_priority": 0,
                "data_size": 165,
                "description": "",
                "tags": [],
                "started": 1479156284,
                "request_gpus": 0,
                "remote": "hermes-2.local",
                "name": "run-date",
                "request_network": false,
                "request_memory": "",
                "created": 1479156284,
                "request_docker_image": "",
                "time": 1.28475403786,
                "request_disk": "",
                "exitcode": 0
            }
        },
        "meta": {
            "editable_metadata_keys": [
                "name",
                "description",
                "tags",
                "allow_failed_dependencies",
                "request_docker_image",
                "request_time",
                "request_memory",
                "request_disk",
                "request_cpus",
                "request_gpus",
                "request_queue",
                "request_priority",
                "request_network"
            ]
        },
        "type": "bundles",
        "id": "0x491808200c7e4e2798530d5cf9bdfdd1"
    }
}
```

### Search bundles
`GET /rest/bundles/`

| Query Parameter    | Description |
| :---         |      :---      |
| `keywords`   | MULTIPLE. Keywords for a search query, in the same form as in `cl search`.                 | 
| `specs  `    | MULTIPLE. Bundle specs to search for.         | 
| `worksheet`  | OPTIONAL. ID of the parent worksheet for resolving bundle specs.                    | 
| `depth`      | OPTIONAL. Include all descendants of the found bundles down by this depth. | 


`/rest/bundles/<uuid>/contents/info/`

`/rest/bundles/<uuid>/contents/blob/`


## Worksheets

`/rest/worksheets/<uuid>`

`/rest/worksheets/`

## Users



## Groups

`GET /groups`

Fetch all groups accessible by the authenticated user.
