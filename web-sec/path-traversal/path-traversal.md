# Path Traversal
Path Traversal attack is basically accessing unwanted files by traversing through directories.

## Ways to exploit path traversal
- Sometimes the path is always considered from the root, so for testing you can just enter <code>/etc/passwd</code>. If the contents of the file are returned in result, then that means it is vulnerable to path traversal and also the path is considered from the root itself.
- If the path is not considered from the root, then in general you have to traverse through directories, like hit and try for 7 to 8 times <code>/../../../../../../etc/passwd</code>. If it appears, then it is vulnerable or else not.
- Sometimes, even the sequence <code>../</code> is removed sometimes recursively or sometimes non recursively, so for that as well, if recursively removed then <code>....//</code> can be used, so that the inner sequence is stripped, then the outer ones becomes the path traversal sequence.
- Even sometimes, the path sequence is not acceptable at all, in that case you can use encoding <code>%2f%2e%2e%2f</code> or even double encoding as well <code>%252f%252e%252e%252f</code>.
- Also sometimes server wants a specific directory to be present as the base directory like <code>/var/www/images</code>, then you can just use <code>/var/www/images/../../../</code> to get out of the directory and then perform the attack.
- Sometimes, server wants the path to end with a particular file extension, then u can use <code>/etc/passwd%00.jpg</code> to just include the extension but the actual file path is considered upto passwd.