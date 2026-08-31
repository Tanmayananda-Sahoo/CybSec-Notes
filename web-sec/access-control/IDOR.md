# IDOR (Insecure Direct Object Reference)

## What is it?

- It is one of the vulnerability in OWASP Top 10.
- It is an access control vulnerability where an application lets a user access or modify an object simply by changing an identifier, without properly checking whether that user is authorized to access that object.

## How to exploit it?

1. With direct reference to database object
- https://insecure-website.com/customer_account?customer_number=132355\ Say this is the URL, so the attacker can just change the customer_number param and just access the customer page if proper validation is not implemented.

- Other method is to exploit password leakages and all

2. With direct reference to static files
- IDOR vulnerabilities can occur when sensitive resources are stored as static files and accessed using predictable filenames or identifiers.\
For example: https://insecure-website.com/static/12144.txt \
If user changes the filename from 12144 to 12145 and access it, then that is IDOR vuln.
