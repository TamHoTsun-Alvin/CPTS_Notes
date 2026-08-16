In linux, multiple privileged user groups grants us permission that we can use to archive different attacks, below is a list of privileged groups we can utilize for attacking:

LXC/LXD:

LXC and LXD are groups for using Ubuntu's container manager, upon installation all users are added to LXD group, member of this group can utilize LXD container to perform privilege escalation, the walkthrough is to create an LXD Container, making it privileged and then access the host file system, refer to https://academy.hackthebox.com/app/module/51/section/477 for the whole attack chain

We can often find templates used by the original LXC and LXD users, as often they are given this permission because they needed to use containers. if we really don't have it, we can also transfer an image to it

Docker:

A user with Docker group is essentially root to fs without a password, user of this group can spawn docker containers at the discrete, a payload for docker user is `docker run -v /root:/mnt -it ubuntu`, which create a new docker instance with /root directory on host fs mounted as volume, after container start we can browse mounted directory, retrieve or add SSH keyfor root user, we can also mount other directory such as /etc to retrieve /etc/shadow for password cracking

Refer to https://academy.hackthebox.com/app/module/51/section/2411 for more detail

Disk:

disk group gives full access to all device contained within /dev, we can use debugfs to access the whole fs with root level privileges

ADM:

ADM group can read all logs stored at /var/log, while we cant go to root access with this role but it gave us more access for pillaging or gathering sensitive data