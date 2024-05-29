# All About the Robots
**Description:** Oh wow! Now you can learn all about robots, with our latest web service, All About Robots!!

## Solution
1. Based on the same of this challenge, I wanted to immediately check the robots.txt page.

    This is typically /robots.txt and used to prevent search engine crawlers to display certain pages from the site.

2. I went to /robots.txt on the site and saw the following.

    ```
    User-agent: *
    Disallow: /open_the_pod_bay_doors_hal_and_give_me_the_flag.html
    ```    

    This means that for all user agents, these crawlers should not index `/open_the_pod_bay_doors_hal_and_give_me_the_flag.html`, this isn't really important to know for the means of the challenge BUT worth checking out since devs don't want that to be easily indexed.

3. I navigated to /open_the_pod_bay_doors_hal_and_give_me_the_flag.html and retrieved my flag.

![Epic](https://media.giphy.com/media/v1.Y2lkPTc5MGI3NjExamV1a3Q3ZDIwbDRhd2txd2hwNXhua3A1dW0ybHYxejA3aDU2dzJ4YSZlcD12MV9pbnRlcm5hbF9naWZfYnlfaWQmY3Q9Zw/XRWFgpW88RJ8m4VGoL/giphy.gif)
