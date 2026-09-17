# Cache Deception Attack

## What is Cache?
- Cache sits between user and server.
- Whenever a client requests for a static resource, then the request is first forwarded to the cache and it is checked whether the static resource is available with the cache, if yes (cache hit) then it is served to the user from the cache itself or else (cache miss) the request goes to the server from which the resource is served and also that resource is saved in the cache.
- Now whether the cache should consider two request same or not, for that purpose cache creates a cache key which consists of both URL Path and parameters. Depending on cache configuration, it can also consist of HTTP Headers. If two cache keys are same, then both the request should be considered identical. If the cache finds a match of the cache key of the request made, then it directly serves the cached response rather than giving the request to the origin server.

## Cache rules
- Cache rules determine what can be cached and for how long like these rules are set for the static resource not for dynamic resource because the dynamic resource may contain something sensitive information as well and also they can be changed so they have to be served fresh from the server itself. 
- Some common cache rules are to be known:
1. File names with .css and .js are cached because they are static resources.
2. Any URL Path that start with a specific prefix like /assets or /static are also cached because they are also considered as storage of the static resources.
3. Also some filenames like robots.txt and favicon.ico are also cached because they are universally required for web application and they do not change generally.

## Types of URL Mapping
1. Traditional URL Mapping:
- <code>http://example.com/path/in/filesystem/resource.html</code>
- They represent path to the resource to be served.
2. REST - style URLs mapping:
- <code>http://example.com/path/resource/param1/param2</code>
- They don't provide a path to the resource to be served.
- <code>/path/resource</code> represent the endpoint representing a resource and <code>/param1/param2</code> represents the parameters used by the server to process the request.

## Exploiting the path mapping discrepancies
1. Using file extensions.
- <code>http://example.com/user/123/profile/wcd.css</code>
- This one here can exploit the caches rules. Say, the server is mapped according to the RESTful style. So, the server will interpret the request is being made to server the user 123's profile page and will ignore the wcd.css.
- But in the same URL, if the cache is Traditional URL mapped, then it will consider that some wcd.css file is being asked for and will cache the response that will be sent by the server which is the profile page of the user 123.
- Now  this is the concept behind the exploit but how to exploit it?
- To first know how the server maps the URL, first add a arbitary path segment to the URL, say add /foo to <code>/api/orders/123</code> to result in <code>/api/orders/123/foo</code>. If still the same response is there, this means that the server is ignoring the added segment.
- Now to test how the cache is mapping the URL, add a extension like .css, .js, .exe, .ico to the arbitary segment itself, if the response is cached, this means that the cache is considering the full URL with the static extension abd also there is a rule to cache the URL with static file extension.
2. Using path delimeters
- Depending on the server, there are different delimeters.
- For example, <code>?</code> is used for query parameters and in java spring boot <code>;</code> is used for matrix variables. So, spring just ignores the part after <code>;</code> but if the cache doesn't use the java interpreter, then it will consider the request as the request for the static resource and hence will cache the response returned by the server.
- For example <code>/profile;foo.css</code>. Normal java server will return the profile and the cache will store it.
- Same goes for ruby on rails which uses <code>.</code> as a delimeter. So say there are three requests-:\
A. <code>/profile</code> will be interpreted by the HTML formatter.\
B. <code>/profile.css</code> will be interpreted as a CSS extension but as there is no CSS formatter for this profile page. So, an error is returned.\
C. <code>/profile.ico</code> here .ico is not recognized by the server, so it will be treated by the server as if only asking for profile page which will be served by the HTML formatter but the cache interprets it as if it is asking for static resource and it will cache the response.
3. Decoding delimeters discrepancies
- Sometimes, there can be vulnerability in decoding as well.
- Say, the server decodes the url before processing, then it will treat the delimeter as it is and will process but what if the cache does not decode the url, then it will think the URL as a request for a static resource.
- Say, if the cache first applies the cache rules and then decodes the URL and then sends to the server, then too the vulnerability arises.
- For example, <code>/profile%23wcd.css</code>, if the server decodes %23 as #, then it will be treated as a delimeter but if the cache does not decode it, then it is just a static resource request. If the cache first applies the rules, and then decodes the URL and then sends it, then too # will be treated as a delimeter.