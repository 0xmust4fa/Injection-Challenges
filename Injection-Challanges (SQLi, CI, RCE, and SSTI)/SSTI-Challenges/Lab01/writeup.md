# Lab - 01: Basic server-side template injection

## Vulnerability Description:
The application is vulnerable to SSTI due to the unsafe construction of an ERB (Embedded Ruby) template. User input is concatenated directly into the template string rather than being passed as data, allowing for arbitrary code execution on the server.

## Lab Mission:
Identify the ERB template engine, execute arbitrary code, and delete `morale.txt` from Carlos's home directory.

## Solution Steps:

1. **Identify the Injection Point:**
   - Browsed the product pages and noticed that when a product is out of stock, the application uses a `message` GET parameter to display a notification.
   - Example: `/?message=Unfortunately%20this%20product%20is%20out%20of%20stock`


4. **Verification:**
   - Sent the request via Burp Suite. The lab signaled completion once the command executed successfully.
