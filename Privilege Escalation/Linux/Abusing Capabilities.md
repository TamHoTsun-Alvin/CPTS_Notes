Linux Capabilities are security feature that allows a specific privileges to be granted to processes, allowing them to perform action that would be restricted otherwise, however such can be exploited by us.

Below is a list of capabilities that is dangerous and can be exploited by us:

|**Capability**|**Description**|
|---|---|
|`cap_sys_admin`|Allows to perform actions with administrative privileges, such as modifying system files or changing system settings.|
|`cap_sys_chroot`|Allows to change the root directory for the current process, allowing it to access files and directories that would otherwise be inaccessible.|
|`cap_sys_ptrace`|Allows to attach to and debug other processes, potentially allowing it to gain access to sensitive information or modify the behavior of other processes.|
|`cap_sys_nice`|Allows to raise or lower the priority of processes, potentially allowing it to gain access to resources that would otherwise be restricted.|
|`cap_sys_time`|Allows to modify the system clock, potentially allowing it to manipulate timestamps or cause other processes to behave in unexpected ways.|
|`cap_sys_resource`|Allows to modify system resource limits, such as the maximum number of open file descriptors or the maximum amount of memory that can be allocated.|
|`cap_sys_module`|Allows to load and unload kernel modules, potentially allowing it to modify the operating system's behavior or gain access to sensitive information.|
|`cap_net_bind_service`|Allows to bind to network ports, potentially allowing it to gain access to sensitive information or perform unauthorized actions.|

Below is a set of Linux capabilities that would let us escalate user's to root:

|**Capability**|**Description**|
|---|---|
|`cap_setuid`|Allows a process to set its effective user ID, which can be used to gain the privileges of another user, including the `root` user.|
|`cap_setgid`|Allows to set its effective group ID, which can be used to gain the privileges of another group, including the `root` group.|
|`cap_sys_admin`|This capability provides a broad range of administrative privileges, including the ability to perform many actions reserved for the `root` user, such as modifying system settings and mounting and unmounting file systems.|
|`cap_dac_override`|Allows bypassing of file read, write, and execute permission checks.|

We can Enumerate Capabilities using the following command:
```
find /usr/bin /usr/sbin /usr/local/bin /usr/local/sbin -type f -exec getcap {} \;
```