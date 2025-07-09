
#### [[Accounts and Access]]

In traditional Unix security, if an attacker gains shell access to a certain login account, they can perform any action or access any file to which that account has access.

Therefore, making it more difficult for unauthorized people to gain shell access to accounts, particularly to privileged accounts, is a necessary part of securing a system.





#### [[Auditd]]

The audit service provides substantial capabilities for recording system activities.

By default, the service audits about SELinux AVC denials and certain types of security-relevant events such as system logins, account modifications, and authentication events performed by programs such as sudo.







#### Backups

constant backups should take place 




#### Bootloader and Partitions

Protection for the bootloader can prevent unauthorized users who have physical access to the system, e.g. attaining root privileges through single user mode.
Critical file systems should be separated into different partitions in ways that make your system a better and more secure.




#### Compilers

Compilers can be used by hackers from compiling software like worms or root kits to be used on your server.





#### Devices

lorem ipsem




#### Entropy

Entropy is the measure of the random numbers available from `/dev/urandom`.

It is important for a secure operating system to have sufficient quantities of entropy available for various crypotographic and non-cryptographic purposes, such as:

- generation of cryptographic keys
- TCP port randomisation (NAT, outbound connection)
- TCP sequence number selection
- writing random files for testing network functionality and throughput
- overwriting hard disks prior to reuse or resale or encryption




##### Kernel modules

Although security vulnerabilities in kernel networking code are not frequently discovered, the consequences can be dramatic.




#### Maintaining software

Software mintenance is extremely important to maintaining a secure system. It is vital to patch software as soon as it becomes available in order to prevent attackers from using known holes to infiltrate your system.





#### Network Stack




#### OpenSSH
The SSH protocol is recommended for remote login and remote file transfer. SSH provides confidentiality and integrity for data exchanged between two systems, as well as server authentication, through the use of public key cryptography.



#### PAM Module
Linux-PAM is a library that enables the local system administrator to choose how individual applications authenticate users. It offers multiple low-level authentication schemes into a high-level application programming interface (API).

> Modifications of the PAM can have unexpected consequences. To re-write the PAM configuration files, destroying any manually made changes and replacing them with a series of system defaults please use `authconfig` or `system-config-authentication`.






#### Permissions and Limits

Traditional Unix security relies heavily on file and directory permissions to prevent unauthorized users from reading or modifying files to which they should not have access.





#### SELinux

SELinux is a feature of the Linux kernel which can be used to guard against misconfigured or compromised programs. SELinux enforces the idea that programs should be limited in what files they can access and what actions they can take.