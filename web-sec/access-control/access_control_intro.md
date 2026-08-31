# Access Control

## 1. What is it?

Access control is basically controlling what a user can do or what a user can access.
Basically there are two types of access controls:
Horizontal access control is like if two users are on same priviledge, then one user should not be able to access the other user's resources. \
Ex-: https://vuln-web/user?id=1001 is the account of Tanmayy and if the id is changed to 1002, then Tanmayy can view the details of 1002. That's the vulnerability and it is commonly associated with IDOR.
Vertical access control is like the user with lower priviledge should not be able to access the higher priviledged user's resources or should not be able to perform higher priviledged user's action.
Ex-: Say deleteUser function is for admins, then if normal user is able to access that function, that is vertical priviledge escalation.
Context-based access control is like the user's action is dependent on the current context of the user rather than who or what the user is. 
Ex-: You are the owner of the items in the shopping cart of the website and even you can edit it but only before you have placed an order, but after placing an order, you are not allowed to edit your order. If you are allowed, then that is Context-based access control vuln.

## 2. How to exploit it?

1. ### Vertical Priviledge escalation vulns:

- Basically there can be unprotected admin pages can if you can find the URL Endpoint then you can just visit that URL. Normally first check for /admin and /robots.txt. Sometimes in robots.txt, some admin URL are also present.

- In some cases, also the endpoint is protected by giving a complex endpoint which is not guessable normally. Thinking that the URL is not guessable, they sometimes do not protect the URL. In that case you can also check for the HTML code in the script tags, sometimes the URL can also be found there. Also check for the JS File as well. Sometimes you can get the URL.

2. ### Parameter based access control vuln:

- Some applications determine who the user is like user or admin and also access the user rights and store it in a user controllable location.
So this can be dangerous as the user can also manipulate this fields. This can affect because the website determines the access of the user according to the input provided.

- Ex: https://insecure-website.com/login/home.jsp?admin=true \
https://insecure-website.com/login/home.jsp?role=1

- You need to study the request if something is in the cookie or if something is in the queries, then check whether you can modify the cookie value or the query value, if yes you got the vuln.

3. ### Platform misconfiguration access control

- Some application enforce the access check and its control at the platform level. Like checking and restricting access to any specific method or URL. Sometimes proxy is also used for this purpose.

- So, you need to use non standard headers like X-Original-URL and X-Rewrite-URL to exploit this type of vuln.

- First of all what are these headers? So X-Original-URL is like the original URL that is being requested. Say you have headers like this: \
GET / HTTP/1.1\
Host: example.com\
X-Original-URL: /admin \
Now depending on the application architecture, the proxy will see it as the URL on which the request is being requested is / but actually the backend server will see the request as coming to /admin endpoint. So, if by any chance if the proxy is configured for disallowing the /admin endpoint, then by requesting to the / endpoint you bypassed it but if the backend is not configured for disallowing in /admin endpoint, then you can easily view the /admin page.\
Now comes the X-Rewrite-URL. This is also the same to bypass the proxy but instead of giving the original URL and then requesting for another, it tells that the original URL should be treated as this.
For example: \
GET / HTTP/1.1\
Host: example.com\
X-Rewrite-URL: /admin\
Here the thing is that / endpoint should be treated as /admin endpoint. In this way also we can bypass the proxy.

- Often websites allow or deny access in frontend itself. So, say the website has a rule of not allowing POST in /admin/change-role endpoint, but there is a chance that backend accept all types of HTTP methods, so the user can just the user can just change the method and do the work as backend accepts all types of method but the frontend is checking for just one specific method, not all methods.

4. ### URL Matching discrepancies

- Websites can vary in how strictly they match the path of an incoming request to a defined endpoint. For example: requesting on /ADMIN/DELETEUSER and /admin/deleteuser can be considered by access control mechanism different sometimes and hence if one will be protected, you can exploit the other. But note this can work only when the request to both of the URL is considered same by frontend or else it is of no use.

- Similarly in SpringBoot there is a function called useSuffixPatternMatch, if developers have enabled it, then /admin/deleteUser.anything will be considered same as /admin/deleteUser by the backend and hence you can exploit it using any arbitary file extension because the access control mechanism is only checking the original URL that is /admin/deleteUser.

- Similarly sometimes /admin/deleteUser and /admin/deleteUser/ is also not considered same by access control mechanism but the backend treats them same. In this case also it is possible to bypass the access control.

5. ### Horizontal priviledge escalation

- First vulnerability is related to IDOR, that is just changing the user id in the query parameter and accessing the user information if no proper validation is implemented. Note that the id can be changed not only in query parameter. Look for places where you can change the ID and send request.

- Sometimes, unpredictable userID is used instead of just simple userID or username, so in that case, you have look for places where that unpredictable userID is revealed and then try to change the userID and access the page. Places where userID can be revealed is in some posts, or in comments or in likes section for example.

- Sometimes, when verification is implemented like when the userID is changed and when to try to access some other's user page by changing the ID, then login page is shown but keenly check the response and request, sometimes the page is available in the response along with login page response. Check the burpsuite properly.

6. ### Referrer based access control
- Some websites enforce access control mechanism in some specific pages and for their sub-pages they just check access control with referer header where the pages with proper access control mechanism should be mentioned.\
For example: an application robustly enforces access control over the main administrative page at /admin, but for sub-pages such as /admin/deleteUser only inspects the Referer header. If the Referer header contains the main /admin URL, then the request is allowed. In this case attacker can control the referer header and exploit this vuln.
## Things I learnt from the lab

1. Whenever you are going for a website hunting, do not just check the specific vulnerability\
For example say when you are just checking for parameter access control, then your work is to check for query and parameter but while solving lab, I encountered a lab where there was the flaw in change-email functionality and I could not figure it out. Basically, you need to check every button or functionality, what each one is requesting and responding. Study the request and the response of each functionality, there can be functionality where you have never expected it to be.

2. You can add X-Original-URL and if there is a request query parameter, then either you have to add it in the real URL and delete it from the X-Original-URL or else you can just simply do as you are doing (ex-: / as real URL and X-Original-URL is /admin/delete?user=carlos). Both of the methods may or may not work depending on how the query string is being parsed. 

## How to prevent it?
- Unless a resource is intended to be publicly accessible, deny access by default.
- use a single application-wide mechanism for enforcing access controls.
- make it mandatory for developers to declare the access that is allowed for each resource, and deny access by default.
- Thoroughly audit and test access controls to ensure they work as designed.