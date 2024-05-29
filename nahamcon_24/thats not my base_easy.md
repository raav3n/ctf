# That's not my base
**Description:** Everyone knows about Base64, but do you know about this one?

    F#S<YRXdP0Fd=,%J4c$Ph7XV(gF/*]%C4B<qlH+%3xGHo)\

## Solution

1. Based on the description I knew I had to decode this using some 'base algorithm' (is that what they're called?), so I copied this over to our trusted CyberChef

1. CyberChef had a few readily available 'From Base xx' decodings.

    - Base32
    - Base45
    - Base58
    - Base62
    - Base64
    - Base85
    - Base92

    Considering description alludes its not base64, I brute forced the rest of the options.

1. After trying one by one in CyberChef I found that `From Base92` returned a valid value, which was the flag.
    ![flag solution](./screenshots/not%20my%20base.png)

![epic](https://media.giphy.com/media/VuVHki4G9iyRqlP4rd/giphy.gif?cid=790b7611rmtpngzibma8asp81qjdzbum6a09pd82ca865tnn&ep=v1_gifs_search&rid=giphy.gif&ct=g)