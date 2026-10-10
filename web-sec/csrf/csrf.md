# CSRF (Cross Site Request Forgery)

## What is CSRF?
- It is a way in which an attacker forces victim's browser to send request to the vulnerable website on your behalf to perform some unintended actions. This happens in the state when the victim is logged in to the site, so that the browser attaches the session cookie of the victim to the request made by the attacker on ur behalf and hence the server also finds the request valid and performs the action.

- You have to construct a HTML page for this type of attack to send the request.

## Ways to bypass the CSRF Token validation
- To prevent the CSRF attack, CSRF token is used which is a long unguessable token made of random characters or alphanumerical characters.
- But there are some ways to bypass the CSRF Token validation:
1. Sometimes the validation depends on the request method like GET, POST and all. Like sometimes the validation is there for the POST request but not for the GET request. So with this the attacker can change the request method to GET from POST to escape the validation.
2. Sometimes the validation occurs when the token is present in the request header, in case if the token is not present in the header, the validation is skipped. So, in that case if the attacker completely removes the header, then the validation can be bypassed.
3. Sometimes, the server does not check if the token is associated with the user's session. It just has a pool of tokens which it assigns to the user and if the token belong to that pool of tokens, then it just performs the sensitive actions or else not. In that case attacker can just create an account and get a valid token and then just make a request on behalf of the victim through the victim's browser.
4. Sometimes the server just checks that whether the CSRF token and cookie matches, then in that case it allows the user to perform action. So, if the session is of the victim and the CSRF cookie and token are of the attackers which he obtained from his account, then both the token and cookie are valid and match, so in that case, the server allows the action to be performed using the victim's session. For this case, attacker has to find a way to set the CSRF cookie in the victim's browser so that when attacker sends request it matches with the token sent by the attacker, and that has to be another vulnerability in the website. Though this vulnerability is harder to exploit, but it is still vulnerable and is possible.
5. More variation of the above vulnerability, the server does not store the token, instead it just matches the token in the cookie and the token sent in the request parameter, if both matches then it validates or else not. In this case, attacker can generate or use any valid token as nothing is stored in the server side and attacker has to find a vulnerability to set the token within the cookie to the required and desired value as the attacker wants, so that when the attacker sends a request to the website, the token sent in the request parameter and the cookie matches.

## Bypassing SameSite cookie restriction
- Now there are three ways of samesite restrictions:
1. <code>SameSite = Strict</code> it does not include the CSRF cookie in the cross-site requests, only in the same-site request.
2. <code>SameStie = Lax</code> it does not include the CSRF cookie by default. Two conditions have to be satisfied for the browser to include the CSRF cookie: 
- First one is that the request has to be GET request.
- Secondly, the request should be a top level navigation, like iframes or references to any images are not allowed at all. Likewise the cookie is not included in the background request.
3. <code>SameSite = None</code> in this the cookie is included in every request, let it be same site or cross site. No matter the request, the cookie is being included.

- To by pass the Lax restriction, attacker can use the GET request and also no iframes or image or any resource should not be there.\
<code>
\<script>\
    document.location = 'https://vulnerable-website.com/account/transfer-payment?recipient=hacker&amount=1000000';\
\</script>
</code>
- Even there is another way to send GET request by using methos overriding using _method.
<pre>
POST /account/transfer-payment HTTP/1.1
Content-Type: application/x-www-form-urlencoded

_method=DELETE
</pre>

This will consider the method as DELETE rather than POST.