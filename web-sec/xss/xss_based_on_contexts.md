# XSS Based On Contexts

## XSS in HTML Tag attributes
- There are two ways to solve in this context:
1. You can either break out of the attribute and then out of the tag, say using\
 <code>"\>\<script>alert(document.domain)\</script></code>.
2. Or the second thing, if the angle brackets are blocked or encoded, then in that case you cannot break out of the tag, then you have to work inside the html tag, say by payload\
<code>" autofocus onfocus=alert(document.domain)</code>
- Even sometimes the context is only upto the tag attribute that can create a scriptable content itself, say the href attribute which if provided with javascript pseudo-protocol can execute javascript lines, say\
<code>javascript:alert(document.domain)</code>

## XSS in JS context
1. The most simple way is to close the script tag and execute html tags. Say,\
<code>\</script>\<img src=x onerror=alert(document.domain)></code>
if the code is this way, 
\
<code>
\<script>
    var item = 'user data here';
\</script>
</code>
Normally HTML Parser goes first than JS Parser, for that reason the closing script gets interpreted first, and even if it is inside a string, it doesn't matter because the HTML interprets the closing script tag first and hence the javascript part closes here itself, after that img tag is interpreted.
2. Even if the angle brackets are encoded and the context is within the string itself, then it is even possible to break out the javascript string and execute javascript there itself.
Say, using this payload, <code>' -alert(document.domain)-'</code> or <code>'; alert(document.domain)//</code>
Sometimes, it is prevented to break out of the string by escaping the single quotes, So if you give input <code>'; alert(document.domain) //</code>, it gets converted to <code>\';alert(document.domain) //</code>, so if you give <code>\';alert(document.domain)//</code>, it gets converted to <code>\\';alert(document.domain)//</code> and hence the backslashes gets cancelled and hence the single quotes gets used effectively.
3. Sometimes the website uses WAF to prevent XSS Vectors, say if the <code>()</code> are escaped, so you cannot execute alert function, then there is another way to pass the function an argument without paranthesis and also run it. That is by using global error handler, <code>onerror=alert;throw 1</code>.
In this payload, you declared alert as the global handler and passed 1 to it through throw. Now throw generates an exception by itself. So browser due to throw, there is an uncaught exception and calls alert by itself and passes it 1. So basically you run the function with 1 as parameter but without using paranthesis.
- Sometimes, you can make use of HTML Encoding as well if some characters are blocked by the WAF or server. Say, if <code>'</code> are blocked and your context is this <code>\<a href="#" onclick ="var input='...'"></code>. Now say you need to break out of the javascript string, but single quotes is blocked. So, what you will do is you can use HTML encoded form of <code>'</code>  that is <code>\&apos;</code>. So, this becomes <code>\<a href="#" onclick="var input = '\&apos; -alert(document.domain) -\&apos;'"</code>. So now after HTML decoding, Javascript receives this <code>\<a href="#" onclick="var input = ''; -alert(document.domain) -'"></code>
- Another way is when there is use of template literals. This means the use of backticks.
So, if there is any backticks used for declaring a variable, say <code>var input =`Hello ${name}`</code>. So for that, if you give any javascript string or function inside the name, it gets executed as well there and there itself. Say you gave <code>alert(document.domain)</code>. So because of that, the js becomes <code>var input = `Hello ${alert(document.domain)}`</code>. So the alert gets executed and you need not break out of the string as well.
