# Server Side Request Forgery (SSRF)

## What is SSRF?
- SSRF is like when the attacker can make server side application to make unintended request to a location.
- In most of the cases, attacker makes the server make request to the internal location itself.

## How to identify it during bug bounty?
- If server is requesting from another website, then the url is completely under attacker's control, then it is a SSRF vulnerability.
- If the target application is making request to wherever the attacker wants, then it is a SSRF chance.
- Look for candidates like url, uri, link, target, dest, redirect, callback, webhook,endpoint, image, imageUrl, avatar, file, feed, source, host, hostname, domain.
- Not just look but also test whether the server is actually requesting to the attacker controlled website or not.
- To test this, use your own OAST domain and see if you recieved any HTTP request from the vulnerable website.
- Besides you can also check DNS Lookup if there is a request demanding IP of the attacker-provided URL. But there is only DNS record but the collaborator shows there is no HTTP Request from the vulnerable website, then probably SSRF can be done but due to some security reason, HTTP Request is not being made by the vulnerable website. So by only checking the DNS, SSRF cannot be confirmed until the HTTP Request.
- If some analytics website backend fetches the Referer website, then maybe Referer can be altered and there is a chance of SSRF.
- If there is an url parameter which can be modified but localhost or 127.0.0.1 is not working that always does not mean that SSRF is not working, you can try the alternatives of 127.0.0.1 or use @, #.
- You have to try bypassing the black listed inputs or try to include the white listed inputs.

## SSRF attacks
1. SSRF against Server
- In this case, attacker can make the application make request to the localhost/server that is hosting the application. As the server is hosting the application, it can have admin panels and all as well and sometimes, it is even not protected because it is generally believed that whoever comes from the localhost itself or to say from the server itself, is authorized and safe. So, it can be easy to access the admin panel easily. 
- One more reason of not protecting the admin panel in the localhost is because of any disaster recovery cases. It means that if admin forgets or loses the credentials then still admin can access the admin portal.

2. SSRF against backend servers
- Sometimes, application can interact with the backend server which are not accessible to user directly because they have non routable IP Address. So, you can make a request through the application to those backend servers.
- Say, requesting through this URL <code>http://192.168.0.68/admin</code>

## Circumventing common CSRF Defenses
1. Blacklisted based input filters
- Applications commonly blacklist 127.0.0.1 or admin to prevent SSRF but there are ways to bypass this:\
Use an alternative IP representation of 127.0.0.1, such as 2130706433, 017700000001, or 127.1.\
Register your own domain name that resolves to 127.0.0.1. You can use spoofed.burpcollaborator.net for this purpose.
Obfuscate blocked strings using URL encoding or case variation.\
Provide a URL that you control, which redirects to the target URL. Try using different redirect codes, as well as different protocols for the target URL. For example, switching from an http: to https: URL during the redirect has been shown to bypass some anti-SSRF filters.

2. Whitelisted based input filters
- Some applications match a particular hostname or a particular pattern instead of blocking anything. So, there are ways to bypass this as well:\
You can embed credentials in a URL before the hostname, using the @ character. For example:
https://expected-host:fakepassword@evil-host\
You can use the # character to indicate a URL fragment. For example:
https://evil-host#expected-host\
You can leverage the DNS naming hierarchy to place required input into a fully-qualified DNS name that you control. For example:
https://expected-host.evil-host\
You can also double encode characters like #, @ incase server are recursively decoding the input they received.

## Blind SSRF
- Blind SSRF is when attacker performs SSRF but no response is shown to the attacker.

## Finding hidden attack surface for SSRF vulnerabilities
1. Partial URLs in request
- Sometimes an application leaves partial URL for user to control like just the hostname. So, the scope of SSRF is less.
- Always search not only for hostname, search for domain, host, hostname, server, endpoint, callback, target params in burp suite because server might be reconstructing the url inside.
2. XXE attacks are possible as well (XML External Entity). XXE (XML External Entity) can lead to SSRF when a vulnerable XML parser allows external entities to reference URLs. An XML document contains an external entity pointing to <code>http://internal-server/</code>, and the server's XML parser automatically fetches that resource.
Attacker can manipulate the URL field to fetch from unintended source. This leads to SSRF.