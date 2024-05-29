# Didactic Octo Paddles (medium)
## Description
You have been hired by the Intergalactic Ministry of Spies to retrieve a powerful relic that is believed to be hidden within the small paddle shop, by the river. You must hack into the paddle shop's system to obtain information on the relic's location. Your ultimate challenge is to shut down the parasitic alien vessels and save humanity from certain destruction by retrieving the relic hidden within the Didactic Octo Paddles shop.

## Initial Thoughts
Looking at the application there aren't any parameters being passed. The only changes I can make is on the JWT token, making me think that is what I have to attack.

After seeing /admin I knew that was my first step meaning I would need to figure out how to bypass JWT protections to view /admin.

## Source Code
Looking at AdminMiddleware.js I see that it does check for the algorithm and none algorithms (meaning no algorithm is set so doesn't require the server's signature), however, the none check only checks 'none' not 'None'. Which I used to my advantage. I base64 encoded the json below, updated my token and got in /admin.
```
{
  "alg": "None",
  "typ": "JWT"
}
```

The above would be the first portion of the token. Then in the seconds part of the token when I decoded it with base64 I noticed an id of 2 so I changes it to 1 since the id correlated with the username and admin is typically first. The token ended up looking somethin like <JWT properties\>.<user info and assignment\>.<NOTHING\> <- since algorithm is None, no signature needed.

This gives us access to view /admin since to the server it gives no reason to not let us view it.

Another reason why I wanted access to /admin is because this is the only place where jsrender is being used so I could attempt to attack it.

## Getting the flag
jsrender can be vulnerable to Server Side Template Injection, I found a command through hacktricks


`{{:"pwnd".toString.constructor.call({},"return global.process.mainModule.constructor._load('child_process').execSync('cat ../flag.txt').toString()")()}}`

Since the jsrender simply renders all the users in the db all I had to do was register a user with the command above and pull the data from /admin to read the flag.

