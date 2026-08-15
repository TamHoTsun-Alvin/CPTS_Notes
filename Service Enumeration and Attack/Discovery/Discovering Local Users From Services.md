-SMB

Enumerating Users and server info via rpcclient
```
rpcclient -U "" <ip>
srvinfo
querydominfo
enumdomusers
```
Bruteforcing UserRIDs (Linux)
```
for i in $(seq 500 1100);do rpcclient -N -U "" <ip> -c "queryuser 0x$(printf '%x\n' $i)" | grep "User Name\|user_rid\|group_rid" && echo "";done
```
Enumerating Users via Impacket-Samrdump
```
samrdump.py <ip>
or
impacket-samrdump <ip>
```

-NFS (Requirement: NFS Mounted to local system)

Listing Content of NFS with user / group names
```
ls -l mnt/nfs/
```
Listing Content of NFS with UID and GUID
```
ls -n mnt/nfs/
```