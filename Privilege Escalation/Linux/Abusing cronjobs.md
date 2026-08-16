We can attempt to use pspy to enumerate running processes without root privilege, if we are able to enumerate for services / script that looks like a cronjob, we might be able to abuse it:

Sometimes, we can find cronjob running on context of user that we don't have access to, or even root, a common misconfiguration is that a cronjob script run by root or privileged user are globally editable, if that's really the case, we can then locate that file and append a payload that would grant us a reverse shell, if everything goes write the next time the cronjob runs, we can gain a reverse shell.

Check https://academy.hackthebox.com/app/module/51/section/469 for details.