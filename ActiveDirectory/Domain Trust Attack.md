Their are different Cross Domain attack we can perform, below are some Domain Trust attack that utilize child -> parent path and cross forest trust abuse

ExtraSids Attack:

After we compromised the child domain, if, due to misconfiguration (lack of SID filtering in place), what we can do is we add Enterprise Admin group SID into the child domain's account, and since lack of SID Filtering, 