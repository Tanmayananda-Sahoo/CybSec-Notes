# XXE (External XML Entity)

## What is XXE?
- XXE is like when attacker is allowed to view files or access server's internal data that only application is allowed to access.
- In some cases, this XXE attack is used to compromise the server and perform SSRF.

## What is XML?
XML stands for extensible markup language. It can store and transport data. Unlike HTML, it does not use predefined tags. Custom names can be provided to indicate what value is stored.
Example: <code>\<name>Tanmay\</name></code>.

## What is XML Entity?
- XML entities are a way of representing an item of data within an XML document, instead of using the data itself.
- Example <code>\&lt;</code> is used to represent <;

## XML Custom Entities
- <code>\<!DOCTYPE foo [ \<!ENTITY myentity "my entity value" > ]></code>
- This means that wherever <code>&myentity;</code> the value "my entity value" will replace.

## XML External Entities
- Type of custom entity whose definition is located outside of the DTD where they are declared.
- <code>\<!DOCTYPE foo [ \<!ENTITY ext SYSTEM "http://normal-website.com" > ]></code>
- Above is the external entity to load the value of the entity from the external website.
- Say if the value is to be loaded from a file, then
<code>\<!DOCTYPE foo [ \<!ENTITY ext SYSTEM "file:///path/to/file" > ]></code>

