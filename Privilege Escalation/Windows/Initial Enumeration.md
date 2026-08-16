There are multiple commands and stuffs we can enumerate after we gain access to a system:


| Command                                                                             | Info                                                                        | Type     |
| ----------------------------------------------------------------------------------- | --------------------------------------------------------------------------- | -------- |
| ipconfig /all                                                                       | Enumerate Ip of current computer and whether it has access to other network | Network  |
| arp -a                                                                              | Enumerate arp table to discover other device                                | Network  |
| route print                                                                         | Discover other device                                                       | Network  |
| Get-MpComputerStatus                                                                | Check Window Defender Status                                                | Security |
| <br>Get-AppLockerPolicy -Effective \| select -ExpandProperty RuleCollections<br>``` |                                                                             |          |
