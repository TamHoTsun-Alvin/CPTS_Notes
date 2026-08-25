After gaining local admin / root, MUST DO THE FOLLOWING:

Windows: Dump the following: lsass, security, sam, save, ntds (if applicable)

Note: multiple credential has been missed on AEN, including hporter's plaintest password and mssqladm's plaintext credential (Which is crucial for the suspected attack path, TTIMMONS -> SERVER ADMIN)