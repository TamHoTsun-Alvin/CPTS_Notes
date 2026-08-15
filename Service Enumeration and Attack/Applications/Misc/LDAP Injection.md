If we are able to discover presence of LDAP in a server or suspect that the webserver use LDAP to perform authentication, then we may try using ldap injection technique to bypass security measures:

|Input|Description|
|---|---|
|`*`|An asterisk `*` can `match any number of characters`.|
|`( )`|Parentheses `( )` can `group expressions`.|
|`\|`|A vertical bar `\|` can perform `logical OR`.|
|`&`|An ampersand `&` can perform `logical AND`.|
|`(cn=*)`|Input values that try to bypass authentication or authorisation checks by injecting conditions that `always evaluate to true` can be used. For example, `(cn=*)` or `(objectClass=*)` can be used as input values for a username or password fields.|
For Example, supplying a valid username with * as password may allow us to access the application as any user, while supplying a valid password with * as a username allows us to gain access to the access the application using any password we want