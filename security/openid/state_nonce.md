# OpenID Connect state and nonce

## state

Defined in OAuth 2.0

### Generation

Generated when preparing authentication request to Authorization Endpoint.

```
https://server.example.com/authorize?
    response_type=code
    &scope=openid%20profile%20email
    &client_id=s6BhdRkqt3
    &state=af0ifjsldkj
    &redirect_uri=https%3A%2F%2Fclient.example.org%2Fcb
```

From the specification:

The value that binds the request to the user-agent's authenticated state (e.g., a hash of the session cookie used to authenticate the user-agent).

### Validation

Authorization server returns a 302 response to user agent for Authorization Endpoint.

```
HTTP/1.1 302 Found
Location: https://client.example.org/cb?
  code=SplxlOBeZQQYbYS6WxSbIA
  &state=af0ifjsldkj
```

User agent follows the location to client. Client gets the ```state``` from query parameter and validates against the one generated for authentication request.

### Possible attack when without this measure

This measure prevents CSRF attack.

1. Attacker gets response from Authorization Endpoint with his account.
2. Attacker puts the link from the response to his web site as an image.

Example:

```
<img src="https://client.example.org/cb?code=SplxlOBeZQQYbYS6WxSbIA" width="0" height="0" border="0">
```

3. Victim goes to Attacker's web site and silently sends the request to Client.
4. Client stores ```id_token``` associated with Attacker's account in Victim's session.
5. Victim goes to Client's web site and saves sensitive information. However, Victim saves to Attacker's account.

## nonce

Defined in OpenID Connect Core 1.0

Prevent replay attack

### Generation

Generated when preparing authentication request to Authorization Endpoint.

```
https://server.example.com/authorize?
    response_type=code
    &scope=openid%20profile%20email
    &client_id=s6BhdRkqt3
    &state=af0ifjsldkj
    &redirect_uri=https%3A%2F%2Fclient.example.org%2Fcb
```

#### Validation

Authorization server returns ID token for Token Endpoint.

```
HTTP/1.1 200 OK
Content-Type: application/json
Cache-Control: no-store
Pragma: no-cache

{
 "access_token": "SlAV32hkKG",
 "token_type": "Bearer",
 "refresh_token": "8xLOxBtZp8",
 "expires_in": 3600,
 "id_token": "eyJhbGciOiJSUzI1NiIsImtpZCI6IjFlOWdkazcifQ.ewogImlzc
   yI6ICJodHRwOi8vc2VydmVyLmV4YW1wbGUuY29tIiwKICJzdWIiOiAiMjQ4Mjg5
   NzYxMDAxIiwKICJhdWQiOiAiczZCaGRSa3F0MyIsCiAibm9uY2UiOiAibi0wUzZ
   fV3pBMk1qIiwKICJleHAiOiAxMzExMjgxOTcwLAogImlhdCI6IDEzMTEyODA5Nz
   AKfQ.ggW8hZ1EuVLuxNuuIJKX_V8a_OMXzR0EHR9R6jgdqrOOF4daGU96Sr_P6q
   Jp6IcmD3HP99Obi1PRs-cwh3LO-p146waJ8IhehcwL7F09JdijmBqkvPeB2T9CJ
   NqeGpe-gccMg4vfKjkM8FcGvnzZUN4_KSP0aAp1tOJ1zZwgjxqGByKHiOtX7Tpd
   QyHE5lcMiKPXfEIQILVq0pc_E2DzL7emopWoaoZTF_m0_N0YzFC6g6EJbOEoRoS
   K5hoDalrcvRYLSrQAZZKflyuVCyixEoV9GfNQC3_osjzw2PAithfubEEBLuVVk4
   XUVrWOLrLl0nx7RkKU8NXNHq-rvKMzqg"
}
```

Client gets the ```nonce``` from ```id_token``` and validates against the one generated for authentication request.

## References

* [OpenID Connect Core 1.0 incorporating errata set 1](https://openid.net/specs/openid-connect-core-1_0.html)
* [The OAuth 2.0 Authorization Framework](https://tools.ietf.org/html/rfc6749)
