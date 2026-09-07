Below is some tools I discovered during HTB lab thats so good:

BloodyAD (multipurpose AD Tools):

BloodyAD is a tools mainly for privesc and AD Operation in AD Environment, all of its command falls under the 4 category: GET (enumeration), ADD (privesc, adding attribute and others), SET (Alter properties or attributes) and REMOVE (delete / remove stuff), it interacts with DC via LDAP Calls.

Below is some operation attempted:

-AddSPN to specific user
-Add specific user to a group
-Read gMSA password for a ms-managed service account