# Stored XSS
 
## What is Stored XSS?
- Stored XSS happens when website recieves user input data and stores it in the db and then when victim loads the website, the data is fetched and displayed but the malicous input is executed.
- For Example:\
if the website has a comment functionality and you passed a comment and it gets stored in the database and when the user loads the website, the datas are fetched and shown. Say if it is a js alert functionality within a script tag and it gets executed, then it is a stored xss.

## How to test for this vuln?
- Same as reflected ones, test every entry points where the input will be displayed to all users like comments, posts or blogs their titles, contents and all.