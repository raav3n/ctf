# iDoor 
**Description:** It's Apple's latest innovation, the "iDoor!" ... well, it is basically the Ring Doorbell camera, but the iDoor offers a web-based browser to monitor your camera, and super secure using ultimate cryptography with even SHA256 hashing algorithms to protect customers! Don't even think about snooping on other people's cameras!!

## Solution

1. Description hints to SHA256 being used to protect algorithms, I knew this would take a play in the challenge. 

1. Loaded the website up I quickly take note there we can't really do anything and we are accessing it as user 11. I knew I had to find a way to view cameras as another user.

1. After some poking around I realized the url has some string appended to it that appeared as a hash. With the description in mind I had the idea to try hashing the number `11` with SHA256 and surely enough I had a match.

    ```    
    74a49c698dbd3c12e36b0b287447d833f74f3937ff132ebff7054baa18623c35a705bb18b82e2ac0384b5127db97016e63609f712bc90e3506cfbea97599f46f
    ```

    I now knew that hashing the number and appending it to the site would return a different user's view, so much for their 'super secure using ultimate cryptography'

1. Now at this point you can create a sick hacker script using Python, requests, and hashlib to brute force this for you and look for the flag but I decided to manually do so.

1. after trial and error, I found that the sha256 of 0 worked. I entered the hash following the url and the camera feed had my flag.

    ```
    31bca02094eb78126a517b206a88c73cfa9ec6f704c7030d18212cace820f025f00bf0ea68dbf3f3a5436ca63b53bf7bf80ad8d5de7d8359d0b7fed9dbc3ab99
    ```

![woohoo](https://media.giphy.com/media/v1.Y2lkPTc5MGI3NjExa2VvOWprdTVlOTVkNHFrbmx2Yjh1ZzAzc2I3dzhhbDZ5OWlremZ6YiZlcD12MV9naWZzX3NlYXJjaCZjdD1n/3o7buiQeyYFamzRoR2/giphy.gif)