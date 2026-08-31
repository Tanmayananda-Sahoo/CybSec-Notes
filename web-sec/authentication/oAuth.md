# OAuth Vulnerabilities

## 1. What is it?

OAuth is basically skips the idea of entering username and password manually and it logs you in with the help of an already existing social media account (like facebook, google, apple).

It works like this:
1. Client first request for authorization from the authorization server which gives a redirect URL for returning an access token.
2. Authorization server authorizes the client.
3. Then the resource owner interacts with the authorization server for granting access.
4. Authorization server redirects back to the client with an access token or authorization code and a refresh token as well.
5. Now client requests for data required using the access token given.

## 2. Why does it happen?

One of the other key issues with OAuth is the general lack of built-in security features. The security relies almost entirely on developers using the right combination of configuration options and implementing their own additional security measures on top, such as robust input validation.

## 3. Basic example

[Small request/query example]

## 4. How do I identify it?

Recognizing when an application is using OAuth authentication is relatively straightforward. If you see an option to log in using your account from a different website, this is a strong indication that OAuth is being used.
Intercept your message while logging in and regradless of the grant type being used, the first request is made to the /authorization endpoint using the client id, callback url and all. So if this type of request is made, then it is a OAuth request.

## 5. How do I exploit it?

- One method to exploit is through implicit flow in OAuth. SO what happens in this is that the authorization server authorizes the client and then the access token is returned back to the client through fragments https://vuln-web.com#access=123 So what happens is that the server sometimes only checks whether the token is valid or not. It does not check whether the token belongs to the specific user that is requesting it. 
1. Attacker logs in through OAuth using their own account and obtains a valid access token.

2. The browser sends the token and user ID to the application's login endpoint:

POST /login
user_id=attacker
access_token=ATTACKER_TOKEN

3. Attacker intercepts this request using Burp Suite.

4. They change the user_id to the victim's ID:

POST /login
user_id=victim
access_token=ATTACKER_TOKEN

5. If the server only checks that the token is valid but doesn't verify that the token belongs to the specified user, it creates a session for the victim.
6. The attacker is therefore logged in as the victim.

Root cause: Failure to properly bind the OAuth access token to the user's identity.

- Again there is one more called login CSRF. The normal flow is that 
## 9. Prevention

- ...
- ...

## 10. Things I learned from the labs

- ...
- ...

## 11. Interview questions

- ...
- ...

## 12. My mistakes / things I initially misunderstood

- ...