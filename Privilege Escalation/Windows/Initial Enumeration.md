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
| tasklist /svc                                                            | Show processes currently running                                            | Generic  |
| set                                                                      | Show all path                                                               | Generic  |
| wmic product get name                                                    | Show installed software                                                     | Generic  |
