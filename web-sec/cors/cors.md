# Cross Origin Request Policy (CORS)

## What is CORS?
- CORS is basically controlling the access of the resources of the origins outside your origin.

## Same origin policy (SOP)
- SOP is the browser's own isolation policy which states that the javascript of a different origin A is not allowed to read the sensitive information of a different origin B.
- But if the website explicitly wants to share sensitive information with the different origin A, then it can and then there is no use of SOP.
- Typically this can be done using headers like <code>Access-Control-Allow-Credentials: true</code> and also <code>Access-Control-Allow-Origin</code>.
## Vulnerabilities in CORS
1. Observe <code>Access-Control-Allow-Credentials: true</code> and also <code>Access-Control-Allow-Origin</code>. If in the second header, if there is a site name, then that means that the site is allowed to read the response and If the first one is true, then that means that the site is can also transfer credentials say in form of cookie or in any other form. Also check with your own origin and also check if the response coming back from the server is including your origin in the response in the header <code>Access-Control-Allow-Origin</code>, then that means that the server is ok to share the response with your origin as well.
2. 