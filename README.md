
# JWT verifier for Google tokens

![](https://api.travis-ci.org/amezcua/jwt-google-tokens.svg?branch=master)

Elixir library that verifies Google generated JWT tokens (such as those returned by Firebase authentication) and returns the claims data.

The intended use case is to validate signed tokens retrieved by a mobile app using [Firebase Authentication](https://firebase.google.com/docs/auth/), where the app talks directly with the Google Authentication service and retrieves an authentication token (a Json Web Token) that can be later sent to a server for verification.  

JWT tokens are also returned by other Google authentication services and this library can be used to verify them too. 

## Usage

```elixir
    iex > {:ok, {claims}} = Jwt.verify token
```

## Installation

The package can be installed as (will try to make it available in Hex in a future version):

  1. Add `jwt` to your list of dependencies in `mix.exs`:

    ```elixir
    def deps do
      [{:jwt, "~> 0.1.0"}]
    end
    ```

  2. Ensure `jwt` is started before your application:

    ```elixir
    def application do
      [applications: [:jwt]]
    end
    ```

## Plug

A plug 

```elixir
Jwt.Plug
```

is included with the library to allow for integration in web frameworks. The plug looks at the authorization HTTP header to see if it includes a value with the format

```elixir
Authorization: Bearer [JWT]
```

where *[JWT]* is a JWT token. If it is there the library will attempt to validate it and attach the claims to the *Plug.Conn* object. The claims can then be accessed with the *:jwtclaims* atom:

```elixir
claims = conn.assigns[:jwtclaims]
name = claims["name"]
```

If the token is invalid, the plug with directly return a 401 response to the client.

## Limitations

* At this point the library only can verify RSA SHA256 signed tokens. It uses the [public discovery document](https://developers.google.com/identity/protocols/OpenIDConnect#discovery) provided by Google to retrieve the public key used to verify the RSA signatures but if the signing method is changed by Google the library will fail to verify the tokens.

* For every verification request the library does two HTTP calls to retrieve the discovery document and the keys document. Those documents should be cached but they are not being cached at this moment so be aware of it if you use it.

## License

[Apache v2.0](https://opensource.org/licenses/Apache-2.0)