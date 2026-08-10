Sometimes when we are double hopping and issue command to a second host via use of pssession or winrm, we found ourselve unable to do so due the fact that we only have the TGS Ticket sent, without our TGT Ticket the server unable to verify our identity and thus leading in failure to execute command,  we can use the following methods to include our credential so that our command can work

-Using PSCredential Object to workaround:

We can setup PSCredential Objec