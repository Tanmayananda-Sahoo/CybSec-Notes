# Other Authentication

## 1. What is it?
Here we are gonna study about the vulnerabilities in other authentication mechanisms and how to exploit these vulnerabilities

## 2. Why does it happen?

This happens due to some recklessness in hashing or encryption. Also it can happen due to some flaws in logic.

## 3. Basic example



## 4. How do I identify it?

Look for remember me option. Also create a account and look for your account, how the remember me token in the cookie is constructed like is there any well known pattern of hashing or encrypting which you can recognize or is it just a concatenation of some credentials or timestamps.

## 5. How do I exploit it?

- If there is a remember me option, then remember me token will be generated and send to the browser in the form of cookies. Generally, the value in the cookies are not clearly written, so they will be encrypted but if attacker identifies the encryption algorithm and no salt is used, it is easy for attacker to decrypt your value and generally websites think that even if some known static value is used(like username + password), then due to encryption no one can get it but if the above problem is there, then it is surely vulnerable.

- Sometimes the static value is not encrypted and just it is a combination of username and password or anyone alone, then it is more easy for hackers to identify your credential, even if one credential is known, then other can be brute forced. Now how can hackers know what is the cookie value (like are the value concatenated or not), they can know it by making their own account and then studying the pattern of the cookie.

- Even if attacker is not able to create their own account, they can use XSS to steal the token as well. 

- If any open source framework is used to hash or encrypt the cookie, then their whole process will also be described (like how the cookies are constructed and all).

- Sometimes passwords are also used even if it hashed, list of hashed passwords are also available publicly, so attacker can match the obtained hash with the list.

- Sometimes reset password option is also useful, say you click on the reset password option, then there are two options, one is that a new random password will be generated and given to your mail, now this can become susceptible to the MITM attack if transmitted unsecurely.

- Secondly, if the password is not being given, but sometimes the reset link is given to the user via the email, for example http://vulnerable-website.com/reset-password?user=victim-user then in this case, the user query can be changed to any user and then password of that user can be resetted and then attacker can login with new password.
