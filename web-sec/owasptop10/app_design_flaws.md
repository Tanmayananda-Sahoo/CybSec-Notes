## Security Misconfigurations
- Mostly these happens when application design has some flaws. Sometimes, applications are deployed with default credentials and settings which are the first guess for hackers which is absolutely dangerous. Small mistakes give attacker a chance to get into the system.
- Some of the common patterns:
1. Unrestricted API Access.
2. Verbose error message exposing sensitive info.
3. Default credentials or weak passwords.
4. Unnecessary endpoints or services left exposed.
5. Outdated software or packages with known vuln are used.
6. Misconfigured cloud settings or permissions.

## Software Supply chain failures
- Software supply Chain failures happens when application uses components, packages or librares or services that are outdated or have some vulnerabilites within them which attackers can exploit to gain a control of the software.
- This is a serious concern as well because most modern applications are made from third party dependencies which can have some vulnerabilites within them which attackers can exploit.
- Some common patterns are:
1. Over relying on third party apps.
2. Not proper checking of dependencies after deployment.
3. Using unverified libraries or dependencies.
4. Automatic installing updates without verification.

## Cryptographic failures
- Web in general rely on cryptography much more everywhere. It can include protecting data in rest or in transit, verifying identities and safeguarding secrets.
- Now when there is a cryptographic failure, then it leads to heavy credentials breach and even attacker gain data througn MITM attacks.
- Now some failures are:
1. Using weak or vulnerable algorithm like MD5, SHA-1, or ECB mode.
2. Hard coding encryption secrets or even using guessable or secrets which can be brute forced.
3. Improper rotation in keys.
4. No encryption of data in rest or in transit.
5. Invalid TLS Certificate.

### Note:
- While solving the lab of THM, I got to know about one thing which might be important that is decoding the encrypted text in linux for ecb algorithm.\
<code>openssl enc -aes-128-ecb -d -a -K 6d792d7365637265742d6b65792d3136 -in ciphertext.txt -out decrypted.bin</code>.
- This means that the algorithm used in aes-128-ecb and the -K stands for the secret key but the key has to be converted in hex format and also the ciphertext is inside the ciphertext.txt file which has to be decoded and the output will be there in decrpyted.bin file.
