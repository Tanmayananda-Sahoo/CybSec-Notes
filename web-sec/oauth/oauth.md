# OAuth 

## What is it?
- OAuth is liking logging in to a website through a different website. Its like sharing a part of your data without actually exposing your login credentials.

## OAuth Grant Types
1. Authorization code grant type
- In this when the client application asks whether user is agreeing to share the asked resources and if user agrees, then the client application gets a authorization code which it exchanges with OAuth service to get the access token which it can use to make API calls and all. All the communication between server-to-server takes place with the help of a secure communication channel that is not visible to the end user. Now this channel is established when client application registered with the OAuth service, so it gets a client-secret which it uses to authenticate itself in the communication channel. This is argaubly the most secure way.

2. Implicit grant type
- This is similar to the above type but the thing is that no authorization code is provided by the OAuth server to the client application to exchange for the access token, instead the access token is provided directly which the client application can use directly to access resources.

## Vulnerabilities in OAuth
1. Improper implementation of the implicit grant type
- This vulnerability exists in the implicit grant type.
- When the access token is provided directly to the client application, then it is provided through the URL fragment. Now normally, the fragment is not sent to the backend server, instead frontend javascript handles it. 
- So, this vulnerability can be exploited depending on the implementation type, like how is the flow constructed? It might accept tokens without correctly validating the OAuth response, use a token issued for another client, or leak tokens through unsafe redirects or browser-side code.

2. Flawed CSRF Token
- In the authorization code grant type, there is a state parameter used as a CSRF token like to authenticate the communication between client application and OAuth server.
- So, if this state parameter is not available, then there is a chance that the communication between OAuth server and the client application are not validated at all. 
- So an attacker can just exploit this. Attacker can login to his own social media account(consider it social media account for now that is  being used to OAuth login say google) and get the OAuth response with a callback url and a authorization code.
- Now somehow if this URL is clicked by the victim in which the victim already has a session in the browser (say login using username and password), then the session will be mapped to the OAuth login of the attacker then.
- This means that whenever attacker tries to login to the website using the social media account, then that is gonna login to the victim's account.
- Make note that this type of vulnerability is not working always, this depends on the construction or the handling of the application, how the application handles such situtations. For example if the victim is logged in using the OAuth login initially, and then attacker tries this thing, then if the application does not allow a user account that is already associated with a OAuth account to again be associated with another OAuth account, then this attack is not possible at all.

3. Leaking authorization code and access token
- The authorization code is used to exchange for access token which is then used to make API calls by client application for resources from OAuth server.
- Now the attacker creates an authorization request
<pre>GET /authorize?
    response_type=code&
    client_id=photoshare123&
    redirect_uri=https%3A%2F%2Fevil.example%2Fcallback
</pre>
The attacker wants AuthProvider to send the victim's authorization code to evil.example instead of PhotoShare.
For this attack to work, the provider must accept the untrusted redirect URI in the relevant victim authorization flow.
- Now the victim clicks on a untrusted link that basically initiates the OAuth flow. Now consider that the victim is logged in to the OAuth provider already, so when the OAuth is initiated by the link, no password is asked just the consent for the resources is asked for, which say is granted by the victim. Now here is the flaw. If the redirect_uri is not properly validated, then the authorization_code is given to the attacker-controlled site.
- Now attacker can submit that code to the legitimate callback url of the OAuth. Now if the request is processed from the attacker's browser, then the attacker basically logs in to his account, but if the request is processed using victim's browser, then the OAuth is completed and the account takeover takes place.

4. Flawed scope validation(Authorization code flow)
- Normally the flow is that the authorization code is generated after user approves for a scope. A scope is like the subset of data that the client application wants to access.
- Say, attacker has an application where he can control the requests and all.
- Now say the fakeapp asks for name as a scope only and victim allows it and authorization code is generated by the OAuth server.
- Now this code needs to be exchanged for access token, during which attacker request for an additional scope as well. Now if the OAuth is not properly validated, or if it trusts the scope values provided rather than checking upon the authorization code, then it will grant that scope and give the access token.
This is called as a scope upgrade vulnerability.

5. Flawed scope validation(Implicit grant type flow)
- Now in implicit grant type flow, the access token is directly provided through the browser.
- Now consider the same scene to be set up, now the OAuth server gives back the access token to the client application but as the application is being controlled by the attacker, it gains the access token.
- After gaining the access token, it request in the /userinfo endpoint requesting for additional scopes in the URL.(This request basically means that attacker is saying to the OAuth server that I have the access token, please add this to my scope as well).
- If the OAuth server has some flaws or vulnerabilities then it will grant the request to the data that the victim has not requested for.
- In this way, attacker can gain access to your unaccepted data.