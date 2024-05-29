# Passman (easy)
## Description 
Pandora discovered the presence of a mole within the ministry. To proceed with caution, she must obtain the master control password for the ministry, which is stored in a password manager. Can you hack into the password manager?

## Summary
By abusing the UpdatePassword Graphql object I was able to update the admin password, login as admin and retrieve the flag.

## Technology 
- JWT
- Express
- Graphql
- mysql

## Application 
This acts as a password manager. You can login, register, add passwords to your storages and view them.

## Initial Thoughts
At first glance at the source code I knew what I needed was the password store for user admin. Meaning somehow I needed to get admin passwords either by logging in as admin or pulling all data from mysql table.

Looking at the mysql it looked like input was being sanitized so sql injection was probably not an option. Next I thought JWT but that also looked like it was being verified. WIth that left Graphql.

# Attack
### Graphql 
I've never used Graphql before but taking a look at GraphqlHelper.js it seemed to act as an api in a way and could 
1. getPhraseList
    - Retrieve list of stored passwords for users
2. RegisterUser
3. LoginUser
4. UpdatePassword
5. AddPhrase
    - Add new entry to user store passwords 

### Payload
After playing with the app I didn't notice any UI element to updatepassowrd so that prompted me to take a closer look. Sure enough, unlike the other graphql objects, UpdatePassword accepted the request arguments not the session arguments.

`db.updatePassword(args.username, args.password)`

Meaning I could update the admin password with the query below without it verifying that I am admin.

```
{
    "query":
        "mutation($username: String!, $password: String!)
        { UpdatePassword (username: $username, password: $password)
            { message, token } 
        }",
    "variables":
    {
        "username":"admin","password":"rekt"
    }
}
```

With that I can login as admin and view the flag in the store.
