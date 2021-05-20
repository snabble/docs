# General API access

This documentation describes the general access topics for the snabble REST API.

## Environments

The snabble platform has three public environments: one for production usage and
two for different kinds of testing. The environments do not share any data.

### Production environment `snabble.io`

This environment is for serious production usage.

### Staging environment `snabble-staging.io`

This environment is for integration testing from the retailer side or from app development agencies.
It can be used as a sandbox for integration tests.

### Staging environment `snabble-testing.io`

This environment is used internally by the snabble team for software testing, but it can also be used together
with retailers for testing of features in a very early stage.

## API Endpoint Overview

All environments share the same API structure and provide the following endpoints as subdomains. For simplicity we use the production environment in all examples.

| Subdomain                      | Usage                                       |
|--------------------------------|---------------------------------------------|
| api.snabble.io                 | The general REST api                        |
| sftp.snabble.io                | The sftp exchange host                      |
| retailer.snabble.io            | The retailer portal                         |
| docs.snabble.io                | The documentation                           |

## IP Address

The api.* and sftp.* endpoints have fixed IP addresses. These are guaranteed to stay the same.

| Subdomain               | Address        |
|-------------------------|----------------|
| api.snabble-testing.io  | 146.148.30.201 |
| sftp.snabble-testing.io | 146.148.30.201 |
| api.snabble-staging.io  | 35.195.192.232 |
| sftp.snabble-staging.io | 35.195.192.232 |
| api.snabble.io          | 35.189.214.135 |
| sftp.snabble.io         | 35.189.214.135 |

## Access and Security

### HTTPS

All Endpoints are only available via https.

### Project identifier

The contents of all products and stores is scoped in a project. The project id is in the form `<brand>-<randomId>`. So for example, the customer `demo` may have access to the project `demo-ab42xy`. This project id is the first path component in all API requests.

### API Tokens

Most API endpoints are not available for public usage, but only for a limited audience.
They are protected by a JSON Web Token (JWT), which has to be sent as Bearer Authorization in the `Authorization` header.

```
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiYWRtaW4iOnRydWV9.TJVA95OrM7E2cBab30RMHrHDcEfxjoYZgeFONFh7HgQ
```

It is also possible but deprecated to send the JWT in the `Client-Token` header instead.

```
Client-Token: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiYWRtaW4iOnRydWV9.TJVA95OrM7E2cBab30RMHrHDcEfxjoYZgeFONFh7HgQ
```

## Curl example
With `curl`, a command request to the resource /{project}/foo might look as follows:

```
$ export CLIENT_TOKEN=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiYWRtaW4iOnRydWV9.TJVA95OrM7E2cBab30RMHrHDcEfxjoYZgeFONFh7HgQ
$ export PROJECT=demo-ab42xy
$ curl -H "Authorization: Bearer $CLIENT_TOKEN" https://api.snabble.io/$PROJECT/foo
```

## Common Responses

The REST API uses the standard HTTP verbs and HTTP status codes as defined by [RFC 7231](https://tools.ietf.org/html/rfc7231#section-6).

### Structured errors

The api has a structured errors object to response with further details to known request errors. The structured error object looks like this:

| Property  | Type       | Description                              |
|-----------|------------|------------------------------------------|
| type      | `string`   | error type                               |
| message   | `string`   | error message                            |
| details   | `Error[]`  | error details                            |

Example:
```
{
   "type": "shop_not_found",
   "message": "shop with id 'some-shop' not found"
}
```

### Successful 2xx
The request was successfully received, understood, and accepted

**200 OK**:

   The 200 (OK) status code indicates that the request has succeeded.

**201 Created**:

   The 201 (Created) status code indicates that the request has been
   fulfilled and has resulted in one or more new resources being
   created.  The primary resource created by the request is identified
   by either a Location header field in the response or, if no Location
   field is received, by the effective request URI.

   The 201 response payload typically describes and links to the
   resource(s) created.  See Section 7.2 for a discussion of the meaning
   and purpose of validator header fields, such as ETag and
   Last-Modified, in a 201 response.

**204 No Content**:

   The 204 (No Content) status code indicates that the server has
   successfully fulfilled the request and that there is no additional
   content to send in the response payload body.

### Redirection 3xx

**301 Moved Permanently**:

   The 301 (Moved Permanently) status code indicates that the target
   resource has been assigned a new permanent URI and any future
   references to this resource ought to use one of the enclosed URIs.

   A 301 response is cacheable by default; i.e., unless otherwise
   indicated by the method definition or explicit cache controls (see
   Section 4.2.2 of [RFC7234]).

**302 Found**:

   The 302 (Found) status code indicates that the target resource
   resides temporarily under a different URI.  Since the redirection
   might be altered on occasion, the client ought to continue to use the
   effective request URI for future requests.

### Client Error 4xx

The request contains bad syntax or cannot be fulfilled

**400 Bad Request**:

   The 400 (Bad Request) status code indicates that the server cannot or
   will not process the request due to something that is perceived to be
   a client error, e.g. an invalid Content-Type or missing parameters.

**403 Forbidden**:

   The 403 (Forbidden) status code indicates that the server understood
   the request but refuses to authorize it. E.g. the provided JWT
   is missing or does not have sufficient permissions.

**404 Not Found**:

   The 404 (Not Found) status code indicates that the origin server
   did not find a current representation for the target resource

**405 Method Not Allowed**:

   The 405 (Method Not Allowed) status code indicates that the method
   received in the request-line is known by the origin server but not
   supported by the target resource.


### Server Error 5xx

The server failed to fulfill an apparently valid request

**500 Internal Server Error**:

   The 500 (Internal Server Error) status code indicates that the server
   encountered an unexpected condition that prevented it from fulfilling
   the request.
