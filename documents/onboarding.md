# Biller Onboarding

A biller should be able to easily configure her software and specify all parameters
required to communicate with the API.

## JSON format

File `onboarding-sample.json` provides an example JSON payload used to configure
the client software with its initial parameters.

Here is a subset of the JSON file:

```json
{
  "version": "1.0",
  "is_test": false,

  "audience": "biller",
  "expiration_date": "2020-02-20T23:59:59+01:00",
  
  "party": {
    "id": "41090012345678938",
    ...
  },
  "nwp": {
    "id": "4109",
    "api_endpoint": {
      "url": "https://api.mynwp.ch/biller/v1",
      ...
    }
    ...
  },

  "auth": {
    "issuer": "https://auth.mynwp.ch",
    "authorization_endpoint": {
      "url": "https://auth.mynwp.ch/oauth/v1/initial",
      "headers": [],
      "params": {
        "code": "oaerhgergha0ghaergj",
        "grant_type": "authorization_code",
        "client_id": "https://ebill-swp.org",
        "redirect_uri": "tag:ebill-swp.org,2020:biller-onboarding"
      }
    },

    "token_endpoint": {
      "url": "https://auth.mynwp.ch/oauth/v1/token",
      "headers": ["Authorization: Bearer czZCaGRSa3F0MzpnWDFmQmF0M2JW"]
    }
  }
}
```

### `party` section

The `party` section defines the target audience of this JSON file. In the sample,
the audience is the `biller`, which is defined by its `id` (PID), `name` and
additional settings.

### `nwp` section

The `nwp` section defines the identity of the NWP and the API entry points used
by the SWP to communicate with the NWP.

- `nwp.id` &rArr; identifier of the NWP (please see `nwp-directory`).
- `nwp.name` &rArr; name of the NWP (e.g. `SIX Paynet`).
- `nwp.logo_url` &rArr; URL to PNG image used to represent the NWP in the SWP's UI.
- `nwp.info_url` &rArr; URL to NWP landing page (for the end user).
- `nwp.api_endpoint` &rArr; definition of the API endpoint (`url` and `headers` which
  should be provided with every call to the API, in addition to the `Authorization`
  header, which will be filled with a valid _access token_).

### `auth` section

The `auth` section provides the information needed to complete the onboarding
process.

The SWP sends the initial authorization code to the _authorization endpoint_
provided by the NWP. The NWP replies with an initial _access token_ and
_refresh token_. The refresh token can then be sent to the _token endpoint_
in order to request new tokens.

- `auth.authorization_endoint` &rArr; defines how to talk to the authorization endpoint:
  - `url` &rArr; URL of the endpoint (always `https`, TLS 1.2 or higher).
  - `headers` &rArr; additional headers which should be included in the request.
  - `params` &rArr; set of key/value pairs which need to be sent in the body of the `POST` request,
    using the standard `application/x-www-form-urlencoded` encoding. This includes the `code` which
    is unique and can be used exactly once (*) until the expiration date has been reached.
- `auth.token_endpoint` &rArr; defins how to talk to the token endpoint:
  - `url` &rArr; URL of the endpoint (always `https`, TLS 1.2 or higher).
  - `headers` &rArr; additional headers which should be included in the requests.

> (*) Should we define restrictions to the use of the _authorization code_ (e.g. single use)?

## Example OAuth 2.0 flow

### Initial request

Use the provided _authorization endpoint_ information to retrieve the initial
access and refresh tokens:

```txt
     POST /auth/v1/initial HTTP/1.1
     Host: auth.mynwp.ch
     Content-Type: application/x-www-form-urlencoded

     code=oaerhgergha0ghaergj&grant_type=authorization_code&client_id=...&redirect_uri=...
```

### Subsequent requests

Use the _authorization_ and _refresh token_ retrieved by the initial request to
fetch updated access and refresh tokens:

```txt
     POST /oauth/v1/token HTTP/1.1
     Host: auth.mynwp.ch
     Authorization: Bearer czZCaGRSa3F0MzpnWDFmQmF0M2JW
     Content-Type: application/x-www-form-urlencoded

     grant_type=refresh_token&refresh_token=eyJhbGciO.eyJqdggSrg53.3rgSJC34ef
```

### Responses (200 OK)

The token requests return an opaque _access token_ which must be handed over to
every API call. The _refresh token_ must be stored and/or updated for future calls.

```json
{
  "access_token": "eyJhbGIJ9.eyJqdGkMTI1NH0.LcSaQy",
  "expires_in": 600,
  "refresh_token": "eyJhbGciO.eyJqdggSrg53.3rgSJC34ef"
}
```

The access token returned by the NWP should be treated as an opaque string; it might
or might not be a JWT.


### API call

Use the _access token_ in the header _Authorization: Bearer_ of API calls:

```txt
     GET /biller/v1/healthcheck HTTP/1.1
     Host: api.mynwp.ch
     Authorization: Bearer eyJhbGIJ9.eyJqdGkMTI1NH0.LcSaQy
     Accept: application/json
     X-NWP-Foo: bar
```
or
```txt
curl -X GET https://api.mynwp.ch/biller/v1/healthcheck -H 'Accept: application/json' \
     -H 'Authorization: Bearer eyJhbGIJ9.eyJqdGkMTI1NH0.LcSaQy' -H 'X-NWP-Foo: bar' 
```

### API Responses (200 OK)

The API call returns a json, e.g.:

```json
{
  "message": "The healthcheck GET request was successfully received and processed.",
  "requestDateTime": "2020-05-04T13:02:25.889126+02:00",
  "receivedHeaders": [
    {
      "headerName": "X-NWP-Foo",
      "headerValue": "bar"
    }
  ],
  "environmentStage": "X",
  "applicationVersion": "1.2.3",
  "apiVersion": "1.5.2"
}
```

## External Resources

- OAuth 2.0 [Token Endpoint](https://tools.ietf.org/html/rfc6749#section-3.2)
- OAuth 2.0 [Access Token Request](https://tools.ietf.org/html/rfc6749#section-4.1.3) and
  [Access Token Response](https://tools.ietf.org/html/rfc6749#section-4.1.4)
- OAuth 2.0 [Refreshing an Access Token](https://tools.ietf.org/html/rfc6749#section-6)
