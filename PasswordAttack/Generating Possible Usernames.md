In times, we may obtain some information regarding names of individual that work in our pentesting target, however without knowing our target's naming convention for account, it would be very hard for us to interact with the target's service, in light of this we can use automated name generator to generate possible usernames

-Using Username Anarchy

```
#First, log down possible names in a txt file, one entry per line
./username-anarchy -i <abspath_to_text>
```

In result, we can acquire a list of possible username that we can use in NetExec / Kerbrute or other tools for cracking possible user