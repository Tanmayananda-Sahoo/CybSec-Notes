# Server Side Parameter Pollution

## What is that?
- It is like you wanted to do a functionality from your browser and the frontend after recieving the request constructs a request URL for the backend server. This can be a vulnerability when the request URL is not made properly.

## Vulnerabilites
- Say sometimes, frontend just concatenates the values to the request.
- Say frontend constructs this <pre>GET /api/users/search?name=peter&publicProfile=true</pre> Say attacker wants to know how the internal API interprets the query string. So, attacker uses <code>%26</code> for <code>&</code>, so that the request becomes <pre>GET /userSearch?name=peter%26admin=true</pre> now when passed to the backend it becomes something likes this <pre>GET /api/users/search?name=peter&admin=true&publicProfile=true</pre>

- You can do another way that is parameter truncation, say the frontend constructs <pre>GET /api/users/search?name=peter&publicProfile=true</pre> The attacker supplies input containing #, now if the frontend decodes it and adds to the backend URL then it becomes <pre>GET /api/users/search?name=peter#anything&publicProfile=true</pre>. Now the # is treated as fragment by the backend server, so the effective URL is <pre>GET /api/users/search?name=peter</pre>

- Say if the JSON is <pre>{
  "name": "peter"
}</pre> now if the input is <code>peter\\",\\"access_level\\":\\"administrator, then the JSON becomes 
<pre>{
  "name": "peter", "access_level":"administrator"
} (the backslashes are for the double quotes because they are being used inside another quotes or else they will close the existing quote).