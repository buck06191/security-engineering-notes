#resources/security 

Continues to sit atop the OWASP Top Ten Application Security Risks after more than 20 years of it having been publicly utilized.

Numerous web applications and websites use SQL databases as their method of data storage. Applications with a higher prevalence of older functional interfaces such as PHP and ASP are relatively more susceptible to SQL Injection flaws than applications based on more recent technologies.

Applications are vulnerable to attacks when user-supplied data is not validated, filtered for escape characters or sanitized by the application.

An attacker can use SQL Injection to manipulate an SQL query via the input data from the client to the application, thus forcing the SQL server to execute an unintended operation constructed using untrusted input.

## Methods

There are well-known exploitation techniques that attackers leverage depending on the vulnerability within the implementation of the code:

- Manipulating an SQL query logic to bypass access controls.
- Retrieving hidden data to return additional results, including data from other tables within the databases, e.g., leveraging the `UNION` keyword.
- Executing arbitrary SQL code in the context of the database whether stacked queries are allowed.
- Accessing files and executing commands in the operating system, depending on the vulnerable code and the database management system.

_Blind SQL Injection_:  when the injection succeeds, but the code doesn't return the result of the manipulated query to the attacker. Blind injections are still exploitable to retrieve the content using timing analysis, content analysis, or other out-of-bound techniques.

### Example
The following is a classic example of subverting application logic to bypass access controls.

Usernames and passwords are ubiquitous as the method for logging into applications. In this benign scenario, a user submits the username `user` and the password `secret`. The application then performs a SQL query to verify the credentials:

```
SELECT * FROM users WHERE username = 'user' AND password = 'secret'
```

The login is successful if the query returns the details of the user. If the query doesn't return the user details, it is rejected.

By leveraging single quotes and SQL comments (`--`), it is possible to log in as any user without a password, as the password check from the WHERE clause is removed from the query.

The following example illustrates this in action. By entering `administrator'--` in the username field and leaving the password field blank, the SQL statement would result as the following:

```
SELECT * FROM users WHERE username = 'administrator'--' AND password = '
```

The database evaluates this statement without the commented out part, executing just the first part:

```
SELECT * FROM users WHERE username = 'administrator'
```

Since the manipulated query always returns the details of the `administrator` user, the attacker can successfully log in without knowing the correct password.

## Prevention

To avoid SQL Injection vulnerabilities, developers need to *use parameterized queries*, specifying placeholders for parameters so that they are not considered as a part of the SQL command; rather, as solely data by the database.

When working with legacy systems, developers need to escape inputs before adding them to the query. Object Relational Mappers (ORMs) make this easier for the developer; however, they are not a panacea, with the underlying mitigations still entirely relevant: **untrusted data needs to be validated, query concatenation should be avoided unless absolutely necessary, and minimizing unnecessary SQL account privileges is crucial**.