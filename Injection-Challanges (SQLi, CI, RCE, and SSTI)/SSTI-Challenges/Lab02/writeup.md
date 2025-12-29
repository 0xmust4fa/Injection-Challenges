# Lab - 02: Basic server-side template injection (code context)

## Vulnerability Description:
The application uses the Tornado (Python) template engine. The vulnerability exists in the "Preferred name" functionality, where user input is placed inside a code context within the template. Because the input isn't properly sanitized, an attacker can break out of the intended expression and execute arbitrary Python code.

## Lab Mission:
Break out of the template expression, import the `os` module, and delete `morale.txt` from Carlos's home directory.

## Solution Steps:

1. **Locate the Feature:**
   - Logged in as `wiener:peter`.
   - Navigated to the "My Account" section and found the "Preferred name" settings for blog comments.

2. **Analyze the Context:**
   - The application allows choosing between "First name", "Nickname", and "Name". Looking at the request, the `blog-post-author-display` parameter is used to determine what is displayed.
   - This value is rendered inside a Tornado template expression, likely looking like: `{{user.field}}`.

3. **Break out and Exploit:**
   - To execute code, the payload needs to close the existing expression `}}`, execute a statement to import a module, and then execute the command.
   - Payload: `user.name}}{% import os %}{{os.popen('rm /home/carlos/morale.txt').read()}}`

4. **Execution:**
   - Captured the POST request to `/my-account/change-blog-post-author-display` in Burp Suite.
   - Changed the `blog-post-author-display` parameter to the payload above.
   - Reloaded the account page or a blog post to trigger the template rendering.

5. **Verification:**
   - The command `rm /home/carlos/morale.txt` executed in the background through the `os.popen` call, solving the lab.
