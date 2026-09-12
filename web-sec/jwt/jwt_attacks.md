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
- There are some custom headers apart from the alg header which we can exploit in different ways:
1. <code>jwk</code>: It is a JSON representation of the cryptographic key.\
Example: <code>{\
    "kid": "ed2Nf8sb-sD6ng0-scs5390g-fFD8sfxG",\
    "typ": "JWT",\
    "alg": "RS256",\
    "jwk": {\
        "kty": "RSA",\
        "e": "AQAB",\
        "kid": "ed2Nf8sb-sD6ng0-scs5390g-fFD8sfxG",\
        "n":"yy1wpYmffgXBxhAUJzHHocCuJolwDqql75ZWuCQ_cb33K2vh9m"\
    }\
}</code>\
As here in this example, we can see there is a JWK, basically in this example, the algorithm used in RS256 and it has two keys, one private key for signing the payload and one public key for verifying and in good practices, server when they recieve JWT, then verify the signature from their own trusted public key, but in the vulnerable sites, the server takes the publilc key from JWK, which is controlled by attacker to verify the signature.
Now what attacker can do is that he can create a pair of RSA keys and sign the payload according to its own generated private key and also embed its public key in the JWK, so what will happen is when server tries to verify the signature using the provided public key, it is successful.
- Similarly, JKU parameter fetches the set of JWT public keys from a URL provided. Sometimes the sets of JWT keys are exposed in this endpoint which is a standard endpoint <code>/.well-known/jwks.json</code>
- Even sometimes server can use several kinds of keys to sign different kinds of data apart from JWTs. So in that case we pass a kid which is a key ID which tells the server which key to choose. Verification keys are mostly stored as JWK set, so server matches the kid with the token to choose which key to use. Sometimes, they even use kid to point to a file where the id is stored and the content of that file is used as a key. This is dangerous if the signing algorithm is symmetric. So in that case, if the parameter is vulnerable to directory traversal, can just change the path and point to a well known file, though the best way is to point to /dev/null file which has no content, so if the content is signed using empty string, then it will be considered valid. 
This case was if the server stores the validation key is stored in a file. If the server stores the validation keys in a database, then kid can also be used for SQLi if vulnerable.