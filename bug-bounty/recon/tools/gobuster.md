# Gobuster
- Gobuster is not just a directory enumeration tool and it has different modes like dir, dns, vhosts, fuzz, s3, gcs and different modes have different functions.
- In dir mode, it bruteforces the directories with the help of a wordlist which you can get from the seclist from github.
- What it does is that it sends request to each one of the content present in the provided wordlist and then checks the response from the server and then returns you the exciting responses out of all.
- This can reveal admin interfaces, development environments, APIs, config files, old forgotten versions of the software and also more things which can be the entry point for the exploit of the application.
- Syntax: <code>gobuster dir -u \<target> -w /usr/share/wordlists/dirb/common.txt</code>, in fact there is one more syntax as well which searches for the file with the specific extension that are provided.
- Syntax: <code>gobuster dir -u http://target -w wordlist.txt -x php,txt,bak,zip,old</code>.
- Syntax: <code>gobuster dir -u https://target -t 50</code>, this is also one more syntax which states the number of concurrent threads. Note that more the value, faster the operation but sometimes more value can cause heavy load in server or also trigger rate limiting as well. In general case, <code>-t 20</code> or <code>-t 50</code> is seen.
- Also there is another flag <code>- r</code>, which follows the redirects. For example: <code>/admin</code> sometimes redirects to <code>/admin/</code>, which is then followed rather than failing.

#### Note:
You will encounter robots.txt file in most of the result, make sure not to be afraid, it is just a configuration file specially for search crawlers, which tells them which endpoint to not show during searching. For example <code>Disallow: /admin/</code> does not means that no one can access <code>/admin</code>, it means that it is telling search crawlers to not index this so that it does not show up in the general search.
