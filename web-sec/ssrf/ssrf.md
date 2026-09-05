# Server Side Request Forgery (SSRF)

## What is SSRF?
- SSRF is like when the attacker can make server side application to make unintended request to a location.
- In most of the cases, attacker makes the server make request to the internal location itself.

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