# Roten (easy)
## Description
The iMoS is responsible for collecting and analyzing targeting data across various galaxies. The data is collected through their webserver, which is accessible to authorized personnel only. However, the iMoS suspects that their webserver has been compromised, and they are unable to locate the source of the breach. They suspect that some kind of shell has been uploaded, but they are unable to find it. The iMoS have provided you with some network data to analyse, its up to you to save us.

## Initial thoughts
We are given a pcap file and looks like it contains a lot of website traffic. Sounds like the payload used involved a file that was uploaded to the server. Since this type of request would most likely involve some type of POST or maybe PUT request that would be a good start to look at.

## Step 1
Analyzing the pcap file I first filtered by post requests.
`http.request.method == "POST"`
With this I found the upload of a malicious file galacticmap.php. We know this is malicious because we see some obfuscated php code and it was used to perform remote excecution since commands like whoami were being passed. 

## Step 2
This step required analysis of the code. It looks like multiple strings are combined and manipulated then ran through an `eval`.
I figured I could just run this code and get to print out the string rather than excecute it by changing the eval to echo. 
Sure enough I get the soruce code for the web shell along with the flag saved as a variable.