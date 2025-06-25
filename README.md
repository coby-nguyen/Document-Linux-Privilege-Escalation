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

First, I check the Ubuntu Machine to see if it is vulnerable to exploitation by using the commands _cat /etc/lsb-release_ (Which Shows the Ubuntu version with its description) and _uname -a_ (Which Checks the information on the current computer and operating system). Then, we are sure that this machine is exploitable. 

![image](https://github.com/user-attachments/assets/638a0b06-0d8b-4b6f-8536-c87fd113f1ad)

#

The next step is to start the snapd service by using the command _systemctl start snapd_ (In reality, when attackers exploit this flaw, the snapd service needs to be started by the admin, otherwise they won't be able to launch it successfully). Then, we can check the version of snapd to make sure it is vulnerable.

![image](https://github.com/user-attachments/assets/3fd76d65-eed5-43b0-8f92-bddc16f5e44c)

#

Next, I will check the user's privileges by using the commands _id_ (Displaying the user and group information) and _tail /etc/passwd_ (The _/etc/passwd_ file is used to keep track of every registered user). The results below show that the attacker with the "coby" username is a normal user, who has the lowest access to the system.

![image](https://github.com/user-attachments/assets/14e8942d-809f-4d42-b651-a2bed2ca0d1a)

## Exploitation:

To launch this attack, we will utilize the **dirty_sock** script, having two versions, and obtained from https://github.com/initstring/dirty_sock. While Version One requires an outbound Internet connection and running the SSH service, Version Two can be run directly without any requirements.

In the _dirty_sockv2.py_, which will be used in this exploitation, some key components should be focused on. The first key component in this script is the variable _TROJAN_SNAP_, which is a base64-encoded string representing an installable snap package. This package is an empty "devmode" snap that has a bash script in the install hook, which will create a new user ("devmode" (developer mode) is a special installation mode that allows snaps to bypass strict confinement for development and debugging purposes).

![image](https://github.com/user-attachments/assets/dd9fd0b1-bc26-4c98-9e58-c528d6d91ffa)

#

Another considerable component in this script is the _create_sockfile()_ function. This function exploits the insecure parsing in "snapd's ucrednet.go" file to generate a random socket file and slip the dirty sock, _;uid=0;_, allowing us to overwrite the UID variable.

![image](https://github.com/user-attachments/assets/ba8daea4-a6e0-48b7-bda4-9bfde0e66012)

#

To implement this exploitation, I simply execute the _dirty_sockv2.py_ in the terminal. After successfully running the dirty_sock exploit, we can see that a dirty_sock account with the same password was automatically created by the script. 

![image](https://github.com/user-attachments/assets/4ccb9e05-e744-4450-b5a7-eaca30d117e9)

#

Then, I check the _/etc/passwd_ to see the list of registered users, and see a new user called "dirty_sock" was created.

![image](https://github.com/user-attachments/assets/b3913b56-6d06-4957-8680-8050d25b389c)

#

By using the _su_ command, I can log into the "dirty_sock" account using the discovered credentials. After successfully logging in, I can check the privileges of this user and see that it is in the **sudo** group. This means the attacker has already gained full control of the system and can do anything with the highest privileges.

![image](https://github.com/user-attachments/assets/e202d9a9-aae1-43ff-a380-4719cc8ebbab)

## Conclusion:
The CVSS score of this vulnerability is 8.8 (High Severity), meaning it can cause a huge impact on the system if it is exploited. This vulnerability has a high severity, since it doesn't require a high privilege to exploit. When an attacker already has some foothold on the system (e.g., a low-privilege user), they can escalate privileges to root, making this vulnerability a serious local security threat, especially in multi-user or cloud environments.
