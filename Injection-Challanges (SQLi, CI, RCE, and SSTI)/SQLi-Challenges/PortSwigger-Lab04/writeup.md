📘 Lab: SQL injection UNION attack, determining the number of columns returned by the query

This lab contains a SQL injection vulnerability in the product category filter. Since the application returns the results of the query in its response, a UNION attack can be used to exfiltrate data. However, for a UNION to work, the injected query must return the same number of columns as the original query.

The objective is to determine exactly how many columns the original query is fetching by injecting multiple NULL values until the application returns a successful response.

---

# Steps to Reproduce
1. Intercept the Request
- Open Burp Suite and intercept the request when selecting a category (e.g., /filter?category=Gifts).
2. Probe with a Single NULL
- Inject ' UNION SELECT NULL-- into the category parameter.
- Observation: The server returns an Internal Server Error (500), indicating a mismatch in the number of columns.
3. Increment NULL Values
- Update the payload to include an additional NULL:
```sql
'+UNION+SELECT+NULL,NULL--
```
- Observation: If it still errors out, continue adding NULL values.
4. Identify the Success State
- Inject the third payload:
```sql
'+UNION+SELECT+NULL,NULL,NULL--
```
- Observation: The error disappears, the page loads normally, and the response now includes the results of the successful UNION.

---

# Explanation
A ```UNION SELECT``` statement has two strict requirements:

1. Both queries must return the same number of columns.
2. The data types in the corresponding columns must be compatible.

Using NULL is the most effective way to determine column count because NULL is convertible to every common data type (string, integer, etc.). When the number of NULLs matches the number of columns in the original SELECT statement, the database executes the query without throwing an error.

---

# Payloads Used
- Initial Attempt (Failure):
```sql
'+UNION+SELECT+NULL--
```

- Final Successful Payload:
```sql
'+UNION+SELECT+NULL,NULL,NULL--
```
-> (Note: In this specific lab, the query returns 3 columns.)

---

# Screenshot
![Solved](lab4.png)

---

References
PortSwigger Lab: https://portswigger.net/web-security/sql-injection/union-attacks/lab-determining-number-of-columns
SQL Injection UNION Attacks Guide: https://portswigger.net/web-security/sql-injection/union-attacks