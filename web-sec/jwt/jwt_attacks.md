# JWT Attacks

## What is JWT?
- JWT is JSON Web Token.
- They are kind of a format for sending cryptographically signed JSON data between systems.\
<b>JWT Format</b>: <code>header.payload.signature</code>
- Both header and payload are just base64-urlencoded JSON objects.
- Header just gives the metadata about the token itself while payload contains the actual data of the user.
Anyone who has the token can easily read the data by decoding.
- The actual thing is that JWT Provides reliability and integrity but not confidentiality.
- JWT Signature is basically the hash of both header an payload together. In some cases, the hash is even encrypted and then that is the JWT Signature.
- So, even if there is a slight change in the data, the server would know about it because whenever server recieves the tokens, it first calculates the expected signature according to the received header and payload and then matches with the actual signature that is received.
- And no one can create a new signature without knowing about the server's secret signing key.

## Exploiting JWT Vulnerabilities
- Sometimes there are separate functions for decoding and verifying the signature and developers sometimes get confused between those two function and forget to use the verify function and just use decode function.
- The main point is that sometimes server fails to verify the token, in that case attacker can modify the payload easily and gain access to anyone's account or any unwanted resources.
- Another way is that changing the algorithm to none so that no verification occurs for the signature, like when the algorithm is changed to none, then you don't need to provide the signature but the trailing dot after the payload should be present. 
- One more way is if the signing key is weak enough to brute force or guess, then hashcat can be used which comes pre installed in kali linux. hashcat needs one valid signed jwt from the server itself and a wordlist to bruteforce. As this process does not require sending any request to the server, it is quick enough even for the huge long wordlist.
