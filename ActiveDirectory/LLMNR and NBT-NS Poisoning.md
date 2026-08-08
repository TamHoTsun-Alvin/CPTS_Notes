Essentially, The LLMNR and NBT-NS are MS component that serves as an alternative when DNS fails itself, when LLMNR/NBT-NS is in use, any host can reply to them, thus enabling the LLMNR/NBT-NS Poisoning trick, we can do this as long as we have access to a network and if we are lucky, we can gain some credential pairs (given that we can crack the NTLMv2 hash slipped along with the username)

-Using responder for poisoning:
```
sudo responder -I -wf <interface> 
#Not
```

Next, for all the obtained hash, we could simply have them cracked using hashcat with mode 5600, note that NTLMv2 hash cannot be used for PTH Attacks

Ref: https://academy.hackthebox.com/app/module/143/section/1272