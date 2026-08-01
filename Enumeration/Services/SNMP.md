Usual Port: UDP 161, 162

Vulnerable Setting:


| Settings                                        | Effects                                                  |
| ----------------------------------------------- | -------------------------------------------------------- |
| rwuser noauth                                   | Allow access to OID Tree without authentication          |
| rwcommunity <community_string> <ipv4/6_address> | Allowing access to OID Tree regardless origin of request |

Automated Tools:

Using snmpwalk for automated enumeration:
```
snmpwalk -v2c -c <community_string> <ip>
```
Using OneSixtyOne to BruteForce community string:
```
onesixtyone -c <wordlist:ex_seclists/Discovery/SNMP/snmp.txt> <ip>
```
Using Braa to briteforce respective OID:
```
braa <community_string>@<ip>:<bruteforce_format:ex_.1.3.6.*>
```

Ref: https://academy.hackthebox.com/app/module/112/section/1075