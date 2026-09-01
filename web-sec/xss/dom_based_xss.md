# DOM Based XSS

## What is DOM Based XSS?
- DOM Based XSS is like performed by client side's javascript.
- Even if the input is not reflected in the response, still it is possible to perform this DOM XSS. 
- It is like taking malicous data from source and giving it to the dangerous sink in the JS Code of the web.
- <code>\<h1 id="result">\</h1>\
\<script>\
const query = new URLSearchParams(location.search).get("q");
document.getElementById("result").innerHTML = query;
\</script></code>
- Here location.search is the source and can be controlled by attacker and innerHTML line is the sink where the actual vuln is.

## How test it?
- <code>HTML Sink:</code> You can test HTML sinks. Basically you need to provide an alphanumeric string in the source and then search for the provided input, where it has appeared. Now wherever you find that input, determine the context of the input. Also check whether the input is within double quotes or not, if yes then try putting double quotes in your input as well. Try if you can break out. Note that if the provided inputs are encoded then there is a slightly little chance of XSS to happen.
- <code>JS Executable Sink:</code> You can also test JS executable sink. But comparatively it is harder as input does not appear in the DOM, so for that reason you can use debugger to see where your provided input is going wither either you can see for yourself.
- Check for <code>attr()</code> function and <code>$()</code> selector in case of jquery.
- There can be other sinks as well.
- Here are the list of sinks for DOM XSS:
1. document.write()
2. document.writeln()
3. document.domain
4. element.innerHTML
5. element.outerHTML
6. element.insertAdjacentHTML
7. element.onevent

- Above were for JS, now for jQuery:
1. add()
2. after()
3. append()
4. animate()
5. insertAfter()
6. insertBefore()
7. before()
8. html()
9. prepend()
10. replaceAll()
11. replaceWith()
12. wrap()
13. wrapInner()
14. wrapAll()
15. has()
16. constructor()
17. init()
18. index()
19. jQuery.parseHTML()
20. $.parseHTML()

## Things I learnt from solving labs.
- While finding XSS vuln, if web uses jquery, then if you want to execute javascript in any attributes like href, src and all, then use <code>javascript:alert('1')</code>.
- <code>window.location.search</code> is like to find query parameters. (honestly i didn't knew this till now.)
