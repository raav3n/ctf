# Restricted (easy)
## Description
You 're still trying to collect information for your research on the alien relic. Scientists contained the memories of ancient egyptian mummies into small chips, where they could store and replay them at will. Many of these mummies were part of the battle against the aliens and you suspect their memories may reveal hints to the location of the relic and the underground vessels. You managed to get your hands on one of these chips but after you connected to it, any attempt to access its internal data proved futile. The software containing all these memories seems to be running on a restricted environment which limits your access. Can you find a way to escape the restricted environment ?

## Initial thoughts
Looks like I have to ssh into this docker instance `ssh restricted@ip -p port` and reading the sshd_config I see it accepts empty passwords so I don't need anything there. Once I'm in I realize I'm using rbash (some custom bash) and pretty much restricts me from doing anything. Reading the docker file I see the main thing that user can do is use ssh, top and uptime. 

However I immediately think of GTFOBins.

## Bypass
Taking a look at GTFOBins (library with a ton of commands abusing built in OS commands) and hacktricks I came across the following.
```
ssh -t localhost bash
```
What this does is ssh to localhost and attempts to select the shell it recieves. I ran that exact command with `-p 1337` appended because in the docker we see that 1337 is the port ssh is listening to locally. 

In doing so we get another ssh shell but with bash this time and we are noo longer retricted by rbash and can run normal commands. All that was left was to run
```
cat /flag
```
fyi the flag had a string appended at the end I just used TAB to autocomplete the rest.