# 2 Factor Authentication

## 1. What is it?

Many websites rely exclusively on single-factor authentication using a password to authenticate users. However, some require users to prove their identity using multiple authentication factors. Poorly implemented two-factor authentication can be beaten, or even bypassed entirely, just as single-factor authentication can.

## 2. Why does it happen?

Due to logic issues in 2FA or no proper checking of the logic or use cases.

## 3. Basic example

Email-based 2FA is one such example. Although the user has to provide a password and a verification code, accessing the code only relies on them who know the login credentials for their email account. Therefore, the one knowing the credentials can know the code because he/she might have the email account logged in as well.

## 4. How do I identify it?

It can be identified by:
1. Can I brute-force the OTP? (rate limiting)
2. Can I reuse or bypass validity of an old/used OTP? (Use an OTP successfully, then try submitting the same OTP again — does it still work?)
3. Can I manipulate the response to force a "success" state?
4. Can I skip straight to the authenticated area after step 1, without ever submitting a valid OTP?
5. Is the OTP itself leaked anywhere (response body, logs, source)? 
6. Is the OTP predictable? (Legitimate OTPs should come from a cryptographically secure random generator — if you can predict the next one from previous ones, that's a real finding)
7. Does account recovery let me bypass 2FA entirely? (Does the password-reset flow require 2FA too, or does resetting your password let you back in without ever touching the second factor?)
8. Are there other login paths (mobile/API/legacy) where 2FA isn't enforced at all? (2FA is very often implemented on the main web login only, and forgotten on a mobile API endpoint or an older API version still left running - A06 OWASP)
9. Does concurrent submission break the attempt counter? (Fire multiple OTP-verification requests simultaneously (Burp's "Send group in parallel" or Turbo Intruder) using different guesses)

## 5. How do I exploit it?

- There is a logic flaw in some websites that when user provides correct username and password, then the user is in a logged state, so in some website, even after only entering the correct credentials and not entering the verification code, you can access the page which can only be accessed after logging in because you are technically in logged in state.

- Another way is when websites use cookies for verifying token. For example when say carlos entered correct credentials, then he will be assigned a cookie (ex-: Cookie: account=carlos). Now while submitting the code, this cookie is also sent so that server can verify the code against this account. Then if attacker logs in to his account and change the value of the cookie to the victim's username, then the attacker can brute force the code and attacker doesn't need to know the password of the victim.

- Generally, the code is of 4 to 6 digits and it is easy to brute force the code, even this process can be automated using Burp suite's Turbo Intruder extension. So, it is necessary for sites to apply rate limiting on this field.

## 6. Things I learned from the labs

- ...
- ...
