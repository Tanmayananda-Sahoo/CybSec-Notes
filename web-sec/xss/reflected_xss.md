# Reflected XSS
 
## What is Reflected XSS?
- Reflected XSS happens when website recieves user input data from the HTTP Request and includes that data in the immediate response.
- For Example:\
if the webiste has a search functionality and you searched gift and the URL goes like 
<code>http://vuln-web.com/search?term=gift</code> and the next page loaded is <code>You searched for gift</code>. Then it is a chance of Reflected XSS.\
So, if you searched for <code>"\<script>*some bad things*\</script>"</code>, then if that gets reflected, means there is a pretty good chance.

## How to test for this vuln?
- Test every entry point for data within the application's HTTP request and see where the data is getting reflected. Entrypoint can be params, inputs
- For each entrypoint submit a random alphanumeric value and see whether it is getting reflected or not. Make sure that the value is passing all the validations.
- For each reflection, determine what is the context of the reflection, like whether it is between HTML Tags or where.
- Test a candidate payload, like check whehter the payload gets executed or not. Best practice is to add a alphanumeric value and then the payload with it so that it gets easy for you search for the payload by searching for the value itself. In this way you can know where the payload is getting reflected.
