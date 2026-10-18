# Insecure Deserialization

## What is serialization and deserialization?
- Serialization means converting an object or data structure into a format that can be stored or transmitted. 
- Deserialization means converting that data back into an object or data structure.

## Serialization in PHP
- Serialization in PHP using <pre>serialize()</pre> function.
- <pre>O:5:"Notes":1:{s:7:"content";s:14:"Welcome to THM";}</pre> is an example of serialized data.
- <b>Decoding this data</b>: 0 is the object representation in PHP serialized data, then comes 5 which is the length of the string, which is basically the class name and then comes 1 which is means only one property. String in serialized form are represented as s and then the length<pre>s:\<length>:\<String></pre> So, index 1, there is content which is the field name and its length is 7 and then the value of the field that is the "Welcome to THM", which is of length 14.

## Serialization in Python
- Python uses two functions, <pre>dumps()</pre> for serializing and <pre>loads()</pre> for deserializing the data.
- But the data serialized are in binary form which is not safe to show in the external environment, also binary data can contain bytes which may interfere with the communication protocols so using base64 it is encoded to plain text which is in readable format.

## How to exploit the vuln?
- When access to the source code is available, identifying serialisation vulnerabilities can be more straightforward but requires a keen understanding of what to look for. For example, through code review, we can examine the source code for uses of serialisation functions such as <code>serialize(), unserialize(), pickle.loads()</code>, and others.
