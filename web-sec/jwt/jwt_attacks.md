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
- 