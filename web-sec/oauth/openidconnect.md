# OpenID Connect

## What is OpenID Connect?
- The OAuth was first not built for authentication, it was just built for authorization.
- While using OpenID, scope also includes <code>openid</code> along with other requested resources.
- Infact the response type is <code>id_token</code> along with <code>code</code> or <code>token</code> according to the flow.

## Flow of OpenID Connect
- User clicks on sign in with google. Google verifies user's identity.
- Google sends PhotoShare an ID token containing signed information about user's authentication.
- Photoshare validates the token and uses it to establish user's session.
- The key thing is that photoshare needs to know who logged in not just what resource access is requested.
<pre>
{
  "iss": "https://accounts.google.example",
  "sub": "user-8472",
  "aud": "photoshare123",
  "exp": 1924992000,
  "iat": 1924988400,
  "email": "rahul@example.com"
}
</pre>
- This is what the ID Token looks like. iss is who issued the token, sub is the unique identifier at the provider, aud is which application is the token intended for, exp is when the token expires, iat is when the token was issued, email is the resource which was requested.
- Each key is called claims.
- Note that this JSON is signed, so that it is ensured that the claims have not been altered and the token was issued by the trusted issuer. The client application should not also blindly trust the issued token, it should validate it properly.
- Say, the sub was changed by the attacker but failed to generate a valid signature, then the validation fails and client application knows that the claims was modified.
- Note that provider's public signing keys, often published through a JWKS endpoint. The client uses the appropriate trusted public key to verify the token's signature. (the cryptographic keys for signature verification are transmitted over the same network channel (normally exposed on /.well-known/jwks.json), some attacks are still possible.)
- You can access the configuration file from the standard endpoint /.well-known/openid-configuration. Like for the basic OAuth, it is also a good idea to check for the OAuth provider's documentation to see if there's any useful information about their OpenID Connect support.
