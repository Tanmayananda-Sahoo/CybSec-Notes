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
- When source code not available, you have to find the source code from any source, you can use <code>~, .bak, .old</code>, normally the backup files, or old files are stored using this in the end of the file. Sometimes, it can work and return you the source code.

## Ways of exploiting the vuln
#### Modifying object attributes
- There is base64 encoded value in cookies of the website. So, you can take it decode it and then change the parameter or the password or the role and take accounts, or priviledge escalation is also possible.
#### Modifying data types
- There is a comparison method available in PHP for deserialized data <code>5=="5"</code> is considered true, even if the string is <code>5=="5 for something"</code>. The string should just start with the integer and the integer to which it is being compared to should be the starting integer.
- Also, there is one more vuln which was there till version 7.x which considered 0 and any string as true, like <code>0=="for password string"</code> is also true because PHP considered the entrire string as 0.
- # PHP Object Injection via Magic Methods

## What is it?

PHP Object Injection is a vulnerability that occurs when an application deserializes untrusted user input using `unserialize()`. An attacker may craft serialized data to manipulate object properties and trigger dangerous behavior.

## How Does It Work?

1. The application contains a class with a PHP magic method, such as `__wakeup()`, that performs a dangerous operation.
2. The application accepts user-controlled serialized data and passes it to `unserialize()`.
3. The attacker crafts serialized data representing an object of that class with modified property values.
4. PHP reconstructs the object using the class definition available on the server.
5. The `__wakeup()` method executes automatically during deserialization and may use the manipulated properties, potentially leading to **Remote Code Execution (RCE)**.

## Example

```php
class MaliciousUserData {
    public $command = 'default';

    public function __wakeup() {
        exec($this->command);
    }
}

