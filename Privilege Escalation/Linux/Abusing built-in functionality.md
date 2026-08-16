Path Abuse:

By abusing how Linux Path works, we can hijack functions by making linux find executables in our current directory first, first we need to add current directory (.) to PATH variable:
```
htb_student@NIX02:~$ PATH=.:${PATH}
htb_student@NIX02:~$ export PATH
htb_student@NIX02:~$ echo $PATH

.:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games
```
After that, we can create a malicious version of whatever exec