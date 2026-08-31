# XSS (Cross Site Scripting)

## What is XSS?
- XSS basically allows an attacker to inject malicous javascript browser code that runs in another user's browser.
- It basically also solves the cross origin thing, that is say evil.com cannot access bank.com as it is of different origin but if attacker injects code in the bank.com itself, then the malicous code is treated as if coming from bank.com itself rather from any other origin.
- Also if attacker gains access of a normal user, then that is though not fine but comparatively it is fine than gaining access of a admin account.

## How does it works?
- Cross-site scripting works by manipulating a vulnerable web site so that it returns malicious JavaScript to users. When the malicious code executes inside a victim's browser, the attacker can fully compromise their interaction with the application.

## How to find the vuln?
- In normal, submitting some simple unique input (such as a short alphanumeric string) into every entry point in the application, identifying every location where the submitted input is returned in HTTP responses, and testing each location individually to determine whether suitably crafted input can be used to execute arbitrary JavaScript.
- For URL based params vuln, place some simple unique input in the parameter, using the browser's developer tools search the DOM for this input, and test each location to determine whether it is exploitable.
- For Non URL based param and to find DOM XSS Vuln, there is no other way than to read and review the code itself. Ex- document.cookie or setTimeout and all.

## How does the attacker crafted malicous thing reaches to user's browser?

- In case of the normal XSS, say in comments section, attacker gives a malicous input and it gets stored in db, obviously comments are stored in dbs, now when victim visits the websites, the stored input is now displayed but that malicous thing gets executed.
- In case of reflected XSS, say in search functionality, the user crafts the input and then gives that to the website and then website returns an URL where the input is getting executed, then that malicous URL is somehow is clicked by the victim (probably social engineering) and then the XSS works on.
- Mostly this happens due to using of innerHTML for adding a text or an element because if you enter any html content in a seach box say <code>\<b>Hello\</b></code>, then innerHTML recognizes it as literal HTML and executes it, rather than this use textContent so that it is literally treated as text.

