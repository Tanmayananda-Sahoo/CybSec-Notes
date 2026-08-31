# Reconnaisance

## What is Recon?
- Systematically collecting information about an authorized target to understand its attack surface and identify where vulnerabilities might exist.
- Recon is like dividing the website or the target to an attack surface which can be tested manually.

### Level 1 - Passive Recon
- Its like gathering information without directly interacting with the target.
- You use public information sources.

- For example:\
1. Search engines
2. Certificate Transparency logs
3. Public DNS datasets
4. Internet archives
5. Public code repositories
6. Public documentation
- You can do subdomain enumeration and google dorking as well. Whenever organizations obtain TLS certificates, certificate information can be publicly logged. These records can reveal hostnames.

### Level 2 - Subdomain enumeration

- One common tool for subdomain enumeration is subfinder by project discovery. Basically subfinder takes info from public sources and gives you the subdomain.
- Make note that finding more subdomain does not mean that you are doing good in recon, you need to know which ones of them are alive, are important, contains API?
- Katana (for crawling, for finding endpoints inside an application), gau and waybackurls (Historical URL Finding)
### Level 3 - Live Asset Discovery

- Now you got so many assets, so many subdomains. You need to find which one of those are live and have applications running in them. 
- This is where HTTP Probing comes in. For that we use tool httpx by project discovery.
- HTTP probing is a reconnaissance technique used to determine the availability and characteristics of web servers by sending HTTP or HTTPS requests to specific URLs or ports.
- ffuf (Content Discovery and hidden directories)

### Level 4 - Technology Fingerprint

- Discover the technologies used in the application.
- dnsx (helps with dns information), naabu (find open ports)

### Level 5 - Content Discovery
- Now say you have the main page of the website, that is good but it is alone not enough. You need to discover the hidden functionality (like /login, /register, /api).
- Now for this you can use crawling, js files, old versions of the application and also public documentation.
- Now crawling is like you follow the links exposed by the website itself in its page.
- In JS Files, now a days modern websites have a lot of functionality are in js files. Some of them expose endpoints like /api/v2.
- Sometimes in old versions of the applications, old endpoints are exposed. You can try exploiting them.
- And sometimes websites have their public documentation where all the API endpoints are exposed.
- Nuclei (vuln checks), Amass (like a more comprehensive asset-discovery toolkit can be used for Subdomain discovery
DNS enumeration
Asset discovery
Relationship mapping), custom scripts.

### Level 6 - Parameter Discovery
- Parameters are where most vulnerabilities are found => IDOR, SQLi, path traversal, Open Redirect and all.

### Level 7 - JS Recon
- Modern bug bounty heavily involves JavaScript.\
Suppose a website loads:  <mark style="background-color: #535353; color: white">main.9382.js </mark>\
That file might reveal:  <mark style="background-color: #535353; color: white">fetch("/api/v2/users")</mark>

### Level 8 - API Recon
- Sometimes Mobile Applications expose APIs which are not obvious in web versions. 
- You can test IDOR in REST APIs, /graphql in GraphQL.

### External tools
1. Burpsuite 
2. Wappalyzer
3. WhatWeb