# Taking Up Residence
**Description:** My friend pulled this file during a recent incident response investigation.

He said it is probably just useless manifest data from a disk.

However, I think there might be some files that have taken up residence...

## Solution

1. I opened up the file in HxD and idenitied this was a MFT filetype which is used by NTFS filesystems. This contains a collection of files.

    I found this sweet [article](https://unminioncurioso.blogspot.com/2019/05/dfir-one-byte-makes-difference-mft.html) talking about MFT and resident files (wow challenge name sounds like that).

    Basically though

    ```
    When is a resident file? A file is resident when all its information, the complete data of the file, is housed only in the MFT. That is to say, that a resident file does not occupy space in disk
    ```

2. I used MFTECmd.exe tool to extract data about the files and the files itself

    ```
    .\MFTECmd.exe -f ..\Evidence_001 --csv out2 --dr
    ```

3. I opened up the csv it created and found `flag.txt`
    
    when opening the flag, I get this 

    ```
    gAAAAABmS9s32v5Ju181EaJhh2vYMsR6MJ31SK-9mDwgiCz3_MBWopjqqynjoY_-HNOw3tX1T3RthBZHz9ylmyqckZ0gUZ_6T7UUxprMHoCAaTV3m1q0weznBg98RL7dRVhRn0cX6Xta
    ```

    this appears to have been encrypted, according to chatgpt it is using a fernet encryption which is done with Python. Thanks chatgpt

4. Since I know this was encrpyted with Python, I now search the csv for a python file that may be related.

    1. I come across ransom.py

        upon opening it, I see it appears to be base64 encoded data 

        looking back at the csv I also find the following 

        ransom.py and ransom.py:key

    Based on this, I can assume this is the fernet key and can create a simple python script to decrypt our flag.

5. All thats left is to run script below and get our flag.

    ```
    from cryptography.fernet import Fernet

    s='gAAAAABmS9s32v5Ju181EaJhh2vYMsR6MJ31SK-9mDwgiCz3_MBWopjqqynjoY_-HNOw3tX1T3RthBZHz9ylmyqckZ0gUZ_6T7UUxprMHoCAaTV3m1q0weznBg98RL7dRVhRn0cX6Xta'
    code = bytes(s, "utf-8")
    def call_key(): return open("ransKey", "rb").read()
    key = call_key()
    b = Fernet(key)
    decoded_code = b.decrypt(code)
    print(decoded_code)
    ```

P.S. I found another neat tool, also Eric Zimmerman's (https://ericzimmerman.github.io/#!index.md), called MFT Explorer which loads everything up more nicely like a file explorer but took a lot longer to process. If you have patience this is "neater".

![winner](https://media.giphy.com/media/l0HU20BZ6LbSEITza/giphy.gif?cid=790b7611jr8mi8hqq142uw391vqe53w7krx9fhxq3m3f5gp8&ep=v1_gifs_search&rid=giphy.gif&ct=g)