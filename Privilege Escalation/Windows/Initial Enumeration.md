There are multiple commands and stuffs we can enumerate after we gain access to a system:


| Command                                                                  | Info                                                                        | Type     |
| ------------------------------------------------------------------------ | --------------------------------------------------------------------------- | -------- |
| ipconfig /all                                                            | Enumerate Ip of current computer and whether it has access to other network | Network  |
| arp -a                                                                   | Enumerate arp table to discover other device                                | Network  |
| route print                                                              | Discover other device                                                       | Network  |
| Get-MpComputerStatus                                                     | Check Window Defender Status                                                | Security |
| Get-AppLockerPolicy -Effective \| select -ExpandProperty RuleCollections | Check AppLocker Rules                                                       | Security |
| systeminfo                                                               | Show Generic Information                                                    | Security |
| Get-Hotfix \| ft -AutoSize                                               | Show Patch Information                                                      | Security |
| wmic qfe                                                                 | Show Patch Installed                                                        | Security |
| whoami /priv                                                             | Show current user privilege                                                 | Security |
| whoami /groups                                                           | Show current user groups                                                    | Security |
| tasklist /svc                                                            | Show processes currently running                                            | Generic  |
| set                                                                      | Show all path                                                               | Generic  |
| wmic product get name                                                    | Show installed software                                                     | Generic  |
| Get-WmiObject -Class Win32_Product \|  select Name, Version              | Show installed software, but PS                                             | Generic  |
| netstat -ano (PS)                                                        | Show active TCP and UDP connections                                         | Generic  |
| query user                                                               | Show users logged in to system                                              | Generic  |
| net user                                                                 | Show all users                                                              | Generic  |
| net localgroup                                                           | Show all groups                                                             | Generic  |
| net localgroup administrator                                             | Show detail for the group administrator                                     | Generic  |
| net accounts                                                             | Query password policy and account info                                      | Generic  |
