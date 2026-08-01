Usual Port: UDP 161, 162

Vulnerable Setting:


| Settings                                        | Effects                                                  |
| ----------------------------------------------- | -------------------------------------------------------- |
| rwuser noauth                                   | Allow access to OID Tree without authentication          |
| rwcommunity <community_string> <ipv4/6_address> | Allowing access to OID Tree regardless origin of request |
