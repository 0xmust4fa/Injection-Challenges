Lab: SQL Injection vulnerability in WHERE clause allowing retrieval of hidden data
This lab contains a SQL injection vulnerability in the product category filter. When a user selects a category, the application performs a SQL query like:

```sql
SELECT * FROM products WHERE category = 'Gifts' AND released = 1
```

The goal is to exploit this vulnerability to display unreleased products (i.e., where released = 0).

---

Steps to Reproduce
1. Intercept the Request
  - Use Burp Suite to intercept the request triggered when selecting a product category.
2. Identify the Vulnerable Parameter
  - Locate the category parameter in the intercepted request.
  Example:
  ``` GET /filter?category=Gifts ```
3. Modify the Parameter
  - Inject the SQLi payload to manipulate the query logic:
  ``` Gifts'+OR+1=1-- ```

4. Forward the Modified Request
  - Send the altered request to the server.
5. Observe the Response
  - The response now includes unreleased products, confirming successful SQL injection.

---

The payload ' OR 1=1-- exploits the SQL query by turning this:
```sql
SELECT * FROM products WHERE category = 'Gifts' AND released = 1
```

Into something like:
```sql
SELECT * FROM products WHERE category = 'Gifts' OR 1=1--' AND released = 1
```
-> This causes the query to return all products, including unreleased ones, because 1=1 is always true.

---
# Payload Used
```sql
'+OR+1=1--
```
-> Note: URL encoding is used (+ instead of spaces) to ensure the payload works in a GET request.

---

Screenshot:
![solve_photo](<Screenshot 2025-12-10 183535-1.png>)

---

References
[PortSwigger Lab: SQL injection vulnerability in WHERE clause allowing retrieval of hidden data](https://portswigger.net/web-security/sql-injection/lab-retrieve-hidden-data)
