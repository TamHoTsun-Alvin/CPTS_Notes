After gaining local admin / root, MUST DO THE FOLLOWING:

Generic: Search through the system for unattended, config files, files with password or connection strings, search very thorough before moving to the next machine.

Windows: Dump the following: lsass, security, sam, save, ntds (if applicable)

Note: multiple credential has been missed on AEN, including hporter's plaintest password and mssqladm's plaintext credential (Which is crucial for the suspected attack path, TTIMMONS -> SERVER ADMIN)