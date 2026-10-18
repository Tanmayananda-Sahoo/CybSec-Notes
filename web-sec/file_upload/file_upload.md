# File Upload Vulnerabilities — Quick Reference

> **Scope:** Use these techniques only in labs, systems you own, or targets where you have explicit permission. The key question is not just “Can I upload a file?” but “What can the server or another user's browser do with it?”

## 1. Flawed file-type validation
**Idea:** The application trusts an easy-to-spoof signal instead of verifying the file safely.

- **Common weak checks:** filename extension only, client-supplied `Content-Type`, or a superficial signature check.
- **Lab approach:** Upload a harmless test file; compare the filename, request `Content-Type`, and actual bytes. In an authorized lab, use Burp Repeater to change one property at a time (extension, MIME type, contents) and observe the server's response and where the file is stored.
- **Look for:** accepted file types, renamed files, validation errors, accessible upload URLs, and whether the server serves the file as data or executes it.
- **Defence:** allowlist required formats, inspect content with a suitable parser, generate server-side filenames, and store files outside executable web roots.

## 2. Preventing execution in user-accessible directories
**Idea:** An uploaded script is especially dangerous if it is stored somewhere the web server executes scripts.

- **Risk condition:** an attacker can upload server-side code *and* reach it at a URL where the server interprets it as code.
- **Lab approach:** First establish whether the upload is accepted and accessible. Use a harmless marker file in an authorized lab to determine whether the server returns it as a download/text or processes it. Do not assume a `.php` extension alone proves execution.
- **Impact if confirmed:** code execution with the web-server process's privileges; possible file access or further access depending on permissions and network controls.
- **Defence:** store uploads outside the web root or on a separate static host; disable script handlers in upload directories; serve with safe content types and download headers; enforce least privilege.

## 3. Insufficient blacklisting of dangerous file types
**Idea:** A denylist blocks known-dangerous extensions but misses alternatives or parsing quirks.

- **Potential weaknesses:** checking only one extension, inconsistent case handling, multiple extensions, trailing characters, or differences between proxy, application, and server filename parsing.
- **Lab approach:** Inspect the exact validation rule and test harmless filename variations one at a time. Record which layer rejects or accepts each request; confirm the final stored name and server behavior.
- **Remember:** extension tricks are server-dependent and are not universal bypasses. Avoid destructive payloads; prove the issue with a harmless marker.
- **Defence:** prefer a strict allowlist of required formats; normalize names before validation; use server-generated names; configure the server so uploaded files cannot execute.

## 4. Flawed validation of file contents
**Idea:** The server checks only a superficial property (such as a magic-byte signature) rather than safely validating the actual file structure.

- **Potential weaknesses:** trusting file signatures alone, trusting metadata, or validating one representation while a later parser handles another.
- **Lab approach:** Use a valid sample file and change one feature at a time. Check whether the server decodes/re-encodes the file, strips metadata, changes the filename, or rejects malformed content. ExifTool can inspect/edit image metadata; metadata containing text does **not** automatically execute.
- **Polyglot reminder:** a polyglot is crafted to be interpreted in more than one way. It only becomes dangerous if the relevant parser/server configuration interprets the embedded content unsafely.
- **Defence:** decode and re-encode images with a trusted library, validate using the actual parser, strip unnecessary metadata, and isolate file processing.

## 5. Exploiting file-upload race conditions
**Idea:** Two operations happen in an unsafe order or overlap in time—for example, a file is briefly accessible before validation or cleanup finishes.

- **Lab approach:** In a controlled lab, use Burp Repeater/Intruder or a small authorized test to repeat uploads and requests while observing timing, status codes, and whether a temporary file is accessible before rejection/deletion. Keep request rates low and avoid impacting other users.
- **Potential impact:** a short-lived file may be accessed or processed before the security check completes.
- **Defence:** validate before publishing; write to a non-public temporary location; atomically move only approved files into final storage; apply consistent cleanup and access controls.

## 6. Uploading malicious client-side scripts
**Idea:** An uploaded HTML/SVG or similar active file may execute script in a visitor's browser if served in an unsafe context.

- **Risk:** stored XSS or content spoofing, especially if the upload is served from the application's origin and scripts are allowed.
- **Lab approach:** Upload a harmless HTML/SVG test file in an authorized lab and inspect response headers, origin, and browser behavior. Use a benign visible marker rather than stealing cookies or data.
- **Impact:** actions may be performed in the victim's browser under the affected site's security context, subject to browser protections.
- **Defence:** serve user content from a separate origin; force safe download behavior where appropriate; set correct `Content-Type` and `X-Content-Type-Options: nosniff`; sanitize active formats and use a restrictive CSP.

## 7. Exploiting vulnerabilities in parsers of uploaded files
**Idea:** Even a file that is not executed as a script may exploit a bug in software that parses or transforms it (image, document, archive, video, or antivirus tools).

- **Examples:** vulnerable image/document libraries, archive extraction path traversal, or unsafe processing pipelines.
- **Lab approach:** Identify which component processes the upload and its version/configuration; use the lab's supplied proof-of-concept or a harmless malformed sample to understand the behavior. Do not test real targets with weaponized parser exploits.
- **Impact:** depends on the parser bug; can range from denial of service to code execution in the parser's security context.
- **Defence:** patch parsers, sandbox processing, limit file size/resource use, safely handle archives, and run processors with minimal privileges.

## Quick testing checklist

1. **Capture** the upload in Burp Suite and save the original request.
2. **Map controls:** extension, MIME type, signature/content, size, filename, authentication, and storage location.
3. **Change one variable at a time** and compare response codes/messages.
4. **Find the final URL/path** and inspect response headers and actual behavior.
5. **Separate impact from acceptance:** “upload accepted” ≠ “code executed” ≠ “data exposed.”
6. **Document evidence** with request/response pairs, timestamps, and a minimal harmless proof.
7. **Report safely:** state prerequisites, demonstrated impact, affected endpoint, and remediation.

## Core defensive model

**Allowlist → validate/parse → re-encode where appropriate → generate a safe name → store outside executable web roots → serve from an isolated origin → least privilege + logging.**

### Fast distinctions
- **File-type validation:** “Is this the kind of file we allow?”
- **Content validation:** “Is the file genuinely valid and safe for its claimed type?”
- **Execution prevention:** “Even if uploaded, can the server run it?”
- **Client-side risk:** “Can a visitor's browser execute active content?”
- **Parser risk:** “Can software processing the file be exploited?”
- **Race condition:** “Can timing expose the file before controls finish?”
