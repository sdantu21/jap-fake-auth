# mock-auth-backend

A mock backend to test authentication and authorization with [JSON Web Tokens](https://jwt.io/) (JWT). By default, this backend exposes an `/auth` route, which lets you perform a login with a user user and password and returns a JWT, and a `/protected` route, which requires a valid JWT in the request header. Additional routes can be configured (see below), to authorize routes depending on roles in the JWT. By default, an `/admin` route is exposed, which requires the `admin` role.

## Table of Contents

- [Install](#install)
- [Configuration](#configuration)
  - [Server and Routes](#server-and-routes)
  - [Users](#users)
- [Run](#run)
- [Query](#query)

## Install

Requires `node` and `npm`. Install with `npm install`.

## Configuration

### Server and Routes

Edit `config/default.json` to configure the backend and routes being exposed:

```javascript
{
    "port": 8085, //Port to serve on
    "cors-origin": "http://localhost:8080", //Origin of CORS requests to allow. Set to "*" to allow all origins.
    "secret": "supda-dupa-secret", //Secret used to sign the JWT
    "debugOutput": true, //Whether to print debug output to the console

    //Modify those to mimick the backend API you are mocking
    "authenticationRoute": "/auth",
    "authorizationRoute": "/protected",

    //Define all roles known to the backend
    "roles": ["admin"]

    //Additional routes that need a specific role for authorization
    "routes": [
        {
            "path": "/admin",
            "role": "admin"
        }
    ]
}
```

### Users

Edit the `accounts.json` to set up a few users. Default is:

```json
[
  {
    "user": "a@b.com",
    "password": "test",
    "roles": ["admin"]
  },
  {
    "user": "c@d.com",
    "password": "test",
    "roles": []
  }
]
```

## Run

Run the server with `node index.js`.

## Query

The following routes are available by default (paths can be changed in the config, see above):

| Method | Path       | Body                                   | Positive response                                | Negative response       |
| ------ | ---------- | -------------------------------------- | ------------------------------------------------ | ----------------------- |
| POST   | /auth      | `{user: "...", password: "..."}` | `{"message": "Login successful!", token: "..."}` | Status 401 Unauthorized |
| GET    | /protected | -                                      | `{"message": "Successfully authorized!"}`        | Status 401 Unauthorized |

Additional `GET` authorization routes are available, if set through the `routes` array in the config. Those are meant to authorize only if the token contains a certain role (see section about configuration above). By default, the `/admin` route is exposed:

| Method | Path   | Body | Positive response                                           | Negative response       |
| ------ | ------ | ---- | ----------------------------------------------------------- | ----------------------- |
| GET    | /admin | -    | `{"message": "Successfully authorized with role 'admin'!"}` | Status 401 Unauthorized |

The `test` directory of this repo contains an export of an [Insomnia](https://insomnia.rest/) workspace. You can import it into Insomnia in order to get ready-to-send queries for the default routes set up!
