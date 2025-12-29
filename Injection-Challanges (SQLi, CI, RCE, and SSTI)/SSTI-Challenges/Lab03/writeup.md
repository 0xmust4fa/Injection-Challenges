# Lab - 03: Server-side template injection using documentation

## Vulnerability Description:
The application allows administrative users (Content Managers) to modify the templates used for rendering product descriptions. By failing to restrict the template engine's sandbox or sanitize the input, the application is vulnerable to SSTI. This allows an attacker to leverage built-in template directives to instantiate Java classes and execute arbitrary shell commands on the server.

## Lab Mission:
Identify the template engine, consult its documentation to find an exploit vector for remote code execution (RCE), and delete `/home/carlos/morale.txt`.

## Technical Analysis:

### 1. Engine Identification (Fingerprinting)
Upon logging in as the Content Manager, I accessed the "Edit template" feature. To identify the engine, I tested common syntax:
- Input: `${7*7}`
- Result: `49`
- Analysis: This confirmed a template engine using `${}` syntax (common in Java-based engines like FreeMarker or Velocity).

To narrow it down to **Apache FreeMarker**, I tested for FreeMarker-specific directives:
- Input: `<#assign test="success"> ${test}`
- Result: `success`
- Conclusion: The engine is identified as **FreeMarker**.

### 2. Research & Documentation Discovery
Reviewing the Apache FreeMarker documentation and known security research (notably by Albinowax), I identified the `freemarker.template.utility.Execute` class. This class is a legacy utility that implements the `TemplateModel` and can be used to execute arbitrary operating system commands.

### 3. Exploit Construction
By using the `?new()` built-in, I can instantiate the `Execute` class to gain a shell-like execution context.

**Payload:**
```freemarker
<#assign ex="freemarker.template.utility.Execute"?new()> ${ ex("rm /home/carlos/morale.txt") }
```