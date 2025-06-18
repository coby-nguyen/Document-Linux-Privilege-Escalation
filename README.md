# Linux Privilege Escalation (via snapd)
## Project Goal: 
Exploiting the vulnerability called "Dirty_Sock" (CVE-2019-7304) in the REST API for Canonical's snapd daemon (a default service in Ubuntu Linux), to gain root access.

Ramil Mustafayev's Guide: https://kryptohaker.medium.com/linux-privilege-escalation-via-snapd-using-dirty-sock-exploit-and-demonstration-of-cve-2019-7304-892716cf99ca

## Set up:
This vulnerability was exploited in snapd versions older than 2.37, due to an incorrectly validated and parsed remote socket address when performing access controls on its UNIX socket. This also affected some Unix and Linux distros at that time (e.g., Ubuntu 16.04). Therefore, to implement this exploitation, we need snapd 2.32 and Ubuntu 16.04

#

I obtained the Debian package of snapd 2.32 from https://launchpad.net/ubuntu/+source/snapd

![image](https://github.com/user-attachments/assets/bd07e9ce-a0a4-406f-be80-47a3cf381f7c)

#

First, I check the Ubuntu Machine if it is vulnerable to exploitation by using the commands _cat /etc/lsb-release_ (Showing the Ubuntu version with its description) and _uname -a_ (Checking the information on the current computer and operating system). Then, we are sure that this machine is exploitable. 

![image](https://github.com/user-attachments/assets/638a0b06-0d8b-4b6f-8536-c87fd113f1ad)

#

The next step is to start the snapd service by using the command _systemctl start snapd_ (In reality, when attackers exploit this flaw, the snapd service needs to be started by the admin, otherwise they won't be able to launch it successfully). Then, we can check the version of snapd to make sure it is vulnerable.

![image](https://github.com/user-attachments/assets/3fd76d65-eed5-43b0-8f92-bddc16f5e44c)

#

Next, I will check the user's privileges by using the commands _id_ (Displaying the user and group information) and _tail /etc/passwd_ (The _/etc/passwd_ file is used to keep track of every registered user). The results below show that the attacker with the "coby" username is a normal user, which has the lowest access to system.

![image](https://github.com/user-attachments/assets/14e8942d-809f-4d42-b651-a2bed2ca0d1a)

#

## Exploitation:

