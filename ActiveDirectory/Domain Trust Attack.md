Their are different Cross Domain attack we can perform, below are some Domain Trust attack that utilize child -> parent path and cross forest trust abuse

ExtraSids Attack:

After we compromised the child domain, if, due to misconfiguration (lack of SID filtering in place), what we can do is we add Enterprise Admin group SID into the child domain's account, and since lack of SID Filtering, it would be respected at the parent domain the treat us as Enterprise Admin.

The following is needed to carryon this attack:

- The KRBTGT hash for the child domain
- The SID for the child domain
- The name of a target user in the child domain (does not need to exist!)
- The FQDN of the child domain.
- The SID of the Enterprise Admins group of the root domain.
- With this data collected, the attack can be performed with Mimikatz.