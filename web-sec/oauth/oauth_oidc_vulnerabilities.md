# OAuth / OpenID Connect Vulnerabilities

## 1. Unprotected Dynamic Client Registration

### What is Dynamic Client Registration?

Dynamic Client Registration is an OpenID Connect feature that allows client applications to register themselves with an OpenID Provider (OP) by sending an HTTP request to a registration endpoint, usually `/register` or `/openid/register`.

During registration, the application can submit information such as:

- `client_name` — application name
- `redirect_uris` — allowed OAuth callback URLs
- `logo_uri` — URL of the application's logo
- `jwks_uri` — URL containing the client's public signing keys
- `token_endpoint_auth_method` — how the client authenticates at the token endpoint

If registration succeeds, the provider typically returns a `client_id` identifying the registered application.

### Why can it be vulnerable?

The vulnerability occurs when an OpenID Provider allows unauthorized or unrestricted client registration without the authentication, authorization, or safeguards required by its security model.

An attacker may be able to register a malicious application and potentially abuse how the provider trusts or processes the supplied configuration.

> **Important:** Dynamic client registration is not inherently vulnerable. Public registration can be intentional.

### Example scenario

Imagine an OpenID Provider called `AuthProvider`.

1. The attacker discovers its dynamic registration endpoint.
2. The attacker submits a registration request containing their own application details.
3. The provider accepts the request without the required restrictions.
4. The attacker receives a `client_id` for their malicious application.
5. The attacker investigates whether the provider's handling of the registered client creates another security weakness.

Example registration request:

```http
POST /openid/register HTTP/1.1
Host: oauth.example
Content-Type: application/json

{
  "application_type": "web",
  "redirect_uris": [
    "https://evil.example/callback"
  ],
  "client_name": "Fake Application",
  "logo_uri": "https://evil.example/logo.png"
}
```

If registration is intentionally public, this request alone does not prove a vulnerability. The security impact depends on what the attacker can do with the registered client.

### Potential impacts

- Registering malicious applications that impersonate legitimate services.
- Abuse of unsafe redirect URI or client configuration handling.
- Second-order SSRF if the provider later fetches attacker-controlled URLs.
- Other OAuth/OIDC attacks if additional weaknesses are present.

### Second-order SSRF

Second-order SSRF happens when an attacker supplies a URL at one stage and the server uses that URL later to make an HTTP request.

Example:

1. The attacker registers a client with a URL in `logo_uri` or `jwks_uri`.
2. The provider stores the URL.
3. Later, a provider component fetches the URL.
4. If destination validation is inadequate, the attacker may cause requests to internal or otherwise restricted services.

A stored URL alone does not prove SSRF. Confirm that the server actually fetches it and that the fetch can reach an unintended destination.

### How to test in an authorized lab

1. Identify the registration endpoint through documentation or observed traffic.
2. Determine whether registration requires authentication or a registration access token.
3. Inspect which client metadata fields are accepted.
4. Determine whether attacker-controlled URLs are fetched by the provider.
5. Verify whether client configuration can be abused to cause a concrete security impact.

### Prevention

- Require appropriate authentication or authorization for client registration, where required by the deployment model.
- Validate client metadata and restrict redirect URIs.
- Apply SSRF defenses to all server-side URL fetching.
- Restrict outbound requests to approved destinations.
- Validate the use of registered client metadata throughout the OAuth/OIDC lifecycle.

---

## 2. Allowing Authorization Requests by Reference (`request_uri`)

### What is an authorization request by reference?

Normally, an OAuth/OIDC authorization request sends parameters such as `client_id`, `scope`, `response_type`, and `redirect_uri` directly in the URL query string.

OpenID Connect also supports a Request Object: a JWT containing authorization request parameters.

When supported, the client can use `request_uri` to refer to a location from which the provider retrieves the Request Object.

Normal authorization request:

```http
GET /authorize?response_type=code&client_id=client123&scope=openid%20email&redirect_uri=https%3A%2F%2Fclient.example%2Fcallback
```

Request by reference:

```http
GET /authorize?client_id=client123&request_uri=https%3A%2F%2Fclient.example%2Frequest.jwt
```

The provider retrieves the referenced request object and processes its parameters according to the applicable OIDC rules and configuration.

### Why can it be vulnerable?

There are two main risks:

1. **Inadequate validation of the Request Object:** The provider validates ordinary query-string parameters correctly but fails to enforce the same security rules on parameters inside the Request Object.
2. **SSRF through `request_uri`:** The provider retrieves a Request Object from an attacker-influenced URL without appropriate destination restrictions.

### Attack scenario A: Bypassing redirect URI validation

Imagine a provider checks the `redirect_uri` in a normal authorization request against the client's registered callback URLs.

However, it fails to apply equivalent validation to a `redirect_uri` contained inside a Request Object.

Possible attack flow:

1. The attacker identifies a provider that supports `request_uri`.
2. The attacker supplies a Request Object containing an unauthorized redirect URI.
3. The provider retrieves and processes the Request Object.
4. Due to flawed validation, the provider accepts the unauthorized redirect URI.
5. If the provider subsequently sends an authorization code to that URI, the attacker may capture the code.

Whether this leads to account compromise depends on additional conditions, including Request Object integrity, code redemption, and client/token protections.

> **Important:** A Request Object may need to be signed, and the provider must validate its signature, issuer, audience, and other required claims. An attacker cannot necessarily modify a valid signed request object belonging to another client.

### Attack scenario B: SSRF through `request_uri`

1. The attacker provides a `request_uri` pointing to an attacker-controlled endpoint.
2. The provider's server attempts to retrieve the referenced Request Object.
3. If the provider permits requests to unsafe destinations, the attacker may influence server-side network requests.
4. If internal destinations are reachable and security controls are inadequate, this may expose internal services or information.

The impact depends on the provider's network access, response handling, and destination restrictions.

### How to test in an authorized lab

1. Capture the normal authorization request in Burp Suite.
2. Determine whether the provider supports `request_uri`.
3. Observe how the referenced Request Object is retrieved and validated.
4. Check whether `redirect_uri` and other sensitive parameters are validated inside the object.
5. If testing SSRF, confirm whether the server fetches attacker-controlled references and whether destination restrictions can be bypassed.
6. Demonstrate concrete impact rather than relying only on the presence of `request_uri`.

### Prevention

- Validate Request Objects according to the applicable OIDC requirements.
- Verify signatures and relevant claims, including issuer and audience where required.
- Apply the same redirect URI restrictions to parameters inside Request Objects as to ordinary authorization requests.
- Restrict where the provider can retrieve Request Objects from.
- Prevent server-side requests to internal, loopback, link-local, and other restricted destinations.
- Avoid accepting arbitrary request references when the deployment does not require them.

---

## 3. Quick Comparison

| Feature | Dynamic Client Registration | Authorization Request by Reference |
|---|---|---|
| Main feature | Registers client applications | References an authorization Request Object |
| Common parameter/endpoint | `/register`, `redirect_uris`, `logo_uri`, `jwks_uri` | `request_uri` |
| Main validation concern | Who can register clients and how their metadata is used | Whether referenced requests and their parameters are trusted correctly |
| Possible SSRF source | Provider fetches registered metadata URLs | Provider fetches a referenced Request Object |
| Possible OAuth impact | Malicious client registration or abuse of client configuration | Unauthorized redirect URI acceptance or other request-validation flaws |

## 4. Key Takeaways

- Dynamic client registration is a legitimate feature; unrestricted registration is not automatically a vulnerability.
- Request Objects package authorization parameters into a JWT.
- `request_uri` refers to the location of a Request Object; it does not automatically make the request trustworthy.
- Both features can create SSRF risks if the provider makes unsafe server-side requests.
- Always distinguish a suspicious configuration from an exploitable vulnerability.
- For bug bounty reports, demonstrate the actual impact and explain the missing security validation.
