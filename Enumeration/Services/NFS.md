Usual Port: 111 and 2049

Dangerous Setting:
no-root-squash: keeping all files created by root with G/UID of 0
nohide: Expose any FS mounted below an exported directory
insecure: Allow the use of ports above 1024
rw: read and write

Common Action:

-Displaying available NFS Shares
```
showmount -e 10.129.14.128
```
-Mounting NFS Share
```
mkdir target-NFS #Making folder for mounting
sudo mount -t nfs <ip>:/ ./target-NFS/ -o nolock
```
