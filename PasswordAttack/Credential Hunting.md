Windows:

-Searching with Search Tools:
```
1. Type keyterm into the search bar and let window search the files
```

-Findstr:
```
findstr /SIM /C:"<keyword>" *.txt *.ini *.cfg *.config *.xml *.git *.ps1 *.yml
```

-Hunting Credential via LaZagne:
```
#Prerequisite: We must transfer LaZagne to our target system
start LaZagne.exe all
```

Ref: https://academy.hackthebox.com/app/module/147/section/1318

Linux: 

-Search for different keyword in files with certain extension:
```
for i in $(find / -name *.<ext> 2>/dev/null | grep -v "doc\|lib");do echo -e "\nFile: " $i; grep "<keyword1>\|<keyword2>\|<keyword3>" $i 2>/dev/null | grep -v "\#";done
```

-Search for crontab
```
cat /etc/crontab
```

-Search for History file
```
tail -n5 /home/*/.bash*
#Basically, view .bash_history, .bashrc or .bash_profile
```

-Using minipenguin (require transfer to victim and admin privilege)
```
sudo python3 minipenguin.py
```

-Using LaZagne (require transfer to victim and admin privilege):
```

```