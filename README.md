# Linux Privilege Escalation (via snapd)
## Project Goal: 
Exploiting the vulnerability called "Dirty_Sock" (CVE-2019-7304) in the REST API for Canonical's snapd daemon (a default service in Ubuntu Linux), to gain root access.
## Set up:
This vulnerability was exploited in snapd versions older than 2.37, due to an incorrectly validated and parsed remote socket address when performing access controls on its UNIX socket. This also affected some Unix and Linux distros at that time (e.g., Ubuntu 16.04). Therefore, to implement this exploitation, we need snapd 2.32 and Ubuntu 16.04

---

I obtained the Debian package of snapd 2.32 from https://launchpad.net/ubuntu/+source/snapd
![image](https://github.com/user-attachments/assets/bd07e9ce-a0a4-406f-be80-47a3cf381f7c)

Ubuntu Machine (version 16.04)
![image](https://github.com/user-attachments/assets/9d15aa52-1ab7-4251-a3a1-446b3e48f07c)



## Exploitation:

