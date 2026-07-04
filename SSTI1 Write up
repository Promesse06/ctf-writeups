# No Sanity — Web Exploitation (SSTI)
**Platform:** CyLab Security Academy
**Category:** Web Exploitation  
**Date:** July 2026

---

## Goal
Exploit a vulnerability in a web application to retrieve 
the hidden flag.

---

## Initial Thoughts
When I first opened the challenge I noticed it was a web app 
that took user input. My first instinct was that it might be 
a path traversal vulnerability, so I started trying payloads 
like `../../../` to navigate to the root directory and read 
sensitive files. After numerous attempts with no results I 
ruled that out and started looking at the problem differently.

---

## Identifying the Vulnerability
After stepping back I tested for Server Side Template Injection 
 by entering {{7*7}} into the input field box . Instead of 
displaying the literal text, the page returned 49  confirming 
the app was passing my input directly into a template engine and 
executing it server side.

---

## What is SSTI?
Server Side Template Injection occurs when user input is embedded 
directly into a template engine such as Jinja2 without being 
sanitised first. This allows an attacker to inject template syntax 
that gets executed on the server, potentially leading to remote 
code execution or data exposure.

---

## Method
1. Opened the challenge URL and explored the web application
2. Attempted path traversal payloads (`../../../`) no success
3. Pivoted to testing for SSTI using {{7*7} ] which  returned 49,
   confirming vulnerability
4. Crafted an SSTI payload to read the flag from the server
5. Retrieved and submitted the flag

---

## Tools Used
- Browser

---

## Flag
picoCTF{s4rv3r_s1d3_t3mp14t3_1nj3ct10n5_4r3_c001_f5438664}

---

## What I Learned
My initial assumption about path traversal taught me not to 
tunnel vision on one vulnerability type. Methodically testing 
different attack vectors is more effective than repeatedly 
trying variations of the same approach. SSTI is dangerous 
because it can give an attacker direct access to the server 
— always validate and sanitise user input before passing it 
to a template engine.
