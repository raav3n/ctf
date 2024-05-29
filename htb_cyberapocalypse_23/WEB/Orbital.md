# Orbital (easy)
## Description
In order to decipher the alien communication that held the key to their location, she needed access to a decoder with advanced capabilities - a decoder that only The Orbital firm possessed. Can you get your hands on the decoder?

## Initial Thoughts
We are prompted with a login screen so first thought is I would need to bypass the authentication. 

## Source Code
A quick glance at source code tells me there is JWT token being used for session token (however it is being verified) and mysql is being used for database.

Taking look at database.py and util.py we see they left a comment for us.
```
# I don't think it's not possible to bypass login because I'm verifying the password later.
    user = query(f'SELECT username, password FROM users WHERE username = "{username}"', one=True)
```
After a little more digging we know this will:
1. query username and password using the inputted username
2. if there is a result that means that that username is valid
3. it will hash the inputted password and compared it with the hash pulled from the database

## Getting admin pass
It does look like we will have to do ome messing with sql so decieded to turn over to sqlmap. Since I was already using Burp Suite, I copied the request, saved it to a file and passed that to sqlmap. 

`sqlmap -r req --dbms=mysql -p username -dump`

It was able to get access to the 'users' table allowing me to view the hash for admin. Then I passed the seclist rockyou wordlist to attempt to crack the hash and we get back 1692b753c031f2905b89e7258dbc49bb (ichliebedich) for admin.

## Getting flag
Looking at the api endpoints eariler we saw /export with the comment.
```
 # Everyone is saying I should escape specific characters in the filename. I don't know why.
        return send_file(f'/communications/{communicationName}', as_attachment=True)
```
Which to me sounds like a hint to Local File Intrusion.

Looking at the Dockerfile we see that flag was copied as /signal_sleuth_firmware"} so if we simply pass
```
{
    "name": "../signal_sleuth_firmware"
}
```
to /api/export we can get our flag.