# 1.9: NoSQL

## Description

A flag validator that claims to be immune to SQL injection. Prove it isn't secure.

## Solution

Opened the page and found a simple input form. Tried basic SQL injection first:

```
' OR '1'='1
" OR 1=1 --
admin'--
```

All rejected. The backend was filtering for SQL syntax. Fair enough, the task said it was SQL injection-proof.

Looked at the network traffic in Burp while submitting a normal input:

```http
POST /validate HTTP/1.1
Content-Type: application/json

{"flag": "test"}
```

The backend was a Node.js app using the `mysql2` driver based on the response headers. I knew `mysql2` has a quirk: if you pass a nested object where a string is expected, the driver serializes it differently and it can bypass comparison logic.

Tried sending an object instead of a string:

```json
{"flag": {"password": 1}}
```

Got a different error back, which told me the input was reaching the query differently. Then tried the comparison operator:

```json
{"flag": {"$gt": ""}}
```

That returned the flag. The query was probably doing something like `WHERE flag = ?` and the driver turned the object into a condition the database evaluated as always true.

No SQL keywords, no quotes, just a type mismatch the developer never accounted for.

Flag: `28696be6f82e96166a2177d976d32cb9`
