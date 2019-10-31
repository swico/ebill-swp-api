# Biller Onboarding (proposal)

A biller should be able to easily configure her software and specify all parameters
required to communicate with the API.

## JSON format

File `onboarding-sample.json` provides an example JSON payload used to configure
the client software with its initial parameters:

```json
{
  "auth": {
    "authorization": "czZCaGRSa3F0MzpnWDFmQmF0M2JW",
    "authorizationCode": "SplxlOBeZQQYbYS6WxSbIA",
    "tokenUrl": "https://api.mynwp.ch/auth/ebill/oauth/token",
    "tokenUrlParams": { "xyz": "abc" }
  },
  "apiUrl": "https://api.mynwp.ch/api/ebill",
  "billerPid": "41..."
}
```

- `auth.authorization` &rArr; map to `Authorization: Basic ...` header
- `auth.authorizationCode` &rArr; map to `code` parameter for `token` endpoint.
- `auth.tokenUrl` &rArr; URL of OAuth 2.0 token API.
- `auth.tokenUrlParams` &rArr; additional parameters for HTTP POST on `token` endpoint.
- `apiUrl` &rArr; URL of API.
- `billerPid` &rArr; biller PID.

The client software uses the _authorization code_ to retrieve a _refresh token_
over the _token URL_.

> Should we define restrictions to the use of the _authorization code_ (e.g. expiration,
> single use, etc.)?

## Example OAuth 2.0 flow

### Initial request

Use the provided _authorization_ and _authorization code_ to retrieve the initial
access and refresh tokens:

```txt
     POST /auth/ebill/oauth/token HTTP/1.1
     Host: api.mynwp.ch
     Authorization: Basic czZCaGRSa3F0MzpnWDFmQmF0M2JW
     Content-Type: application/x-www-form-urlencoded

     grant_type=authorization_code&code=SplxlOBeZQQYbYS6WxSbIA&xyz=abc
```

### Subsequent requests

Use the _authorization_ and _refresh token_ retrieved by the initial request to
fetch updated access and refresh tokens:

```txt
     POST /auth/ebill/oauth/token HTTP/1.1
     Host: api.mynwp.ch
     Authorization: Basic czZCaGRSa3F0MzpnWDFmQmF0M2JW
     Content-Type: application/x-www-form-urlencoded

     grant_type=refresh_token&refresh_token=tGzv3JOkF0XG5Qx2TlKWIA&xyz=abc
```

### Responses (200 OK)

The token requests return an opaque _access token_ which must be handed over to
every API call. The _refresh token_ must be stored and/or updated for future calls.

```json
{
  "access_token": "...",
  "expires_in": 3600,
  "refresh_token": "tGzv3JOkF0XG5Qx2TlKWIA"
}
```

The access token returned by the NWP should be a JWT rather than an opaque token.

> Should this be specified?

## External Resources

- OAuth 2.0 [Token Endpoint](https://tools.ietf.org/html/rfc6749#section-3.2)
- OAuth 2.0 [Access Token Request](https://tools.ietf.org/html/rfc6749#section-4.1.3) and
  [Access Token Response](https://tools.ietf.org/html/rfc6749#section-4.1.4)
- OAuth 2.0 [Refreshing an Access Token](https://tools.ietf.org/html/rfc6749#section-6)
