
The most important rule of system hardening that reasonable admins follow is


> **`A production environment is the real instance of the app so make your changes on the dev/test!`**

The second most important rule is

> **`Don’t do anything that will affect the availability of the service or your system.`**

The third rule is

> **`Make backups of the entire virtual machine and important components.`**

And the last rule is

> **`Think about what you actually do with your server.`**







## **Accounts and Access**

In traditional Unix security, if an attacker gains shell access to a certain login account, they can perform any action or access any file to which that account has access.

### Physical console access
There are some steps which, if taken, make it more difficult for an attacker to quickly or undetectably modify a system from its console.

This is the easiest way to gain unauthorised access to a Linux system is to boot the server into single user mode. Attacker can select a kernel to boot from the grub menu item by pressing specific key to edit the boot option.

> Remember to protect GRUB with password because it's the only way to protect single user mode in RedHat/CentOS distributions.

#### Solution

###### Authentication for single user mode

`# Edit /usr/lib/systemd/system/rescue.service:`
`ExecStart=-/bin/sh -c "/usr/sbin/sulogin; /usr/bin/systemctl --fail --no-block default"`

I also recommend change or set these options in `emergency.service`. It is default target when an issue kicks in during the boot process.

[C2S/CIS: CCE-27287-2 (Medium)](https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_require_singleuser_auth)


### Session configuration files

#### Rationale


When a user logs into a Unix account, the system configures the user's session by reading a number of files. Many of these files are located in the user's home directory, and may have weak permissions as a result of user error or misconfiguration.

#### Solution



###### Set sensible umask values


A misconfigured `umask` value could result in files with excessive permissions that can be read or written to by unauthorized users.

```shell
# Edit /etc/profile and /etc/bashrc:
umask 027

```

[C2S/CIS: CCE-80202-5 (unknown)](https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_accounts_umask_etc_bashrc); [C2S/CIS: CCE-80204-1 (unknown)](https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_accounts_umask_etc_profile)


### Login banners

#### Rationale


Login banners provide a definitive warning to any possible intruders that may want to access your system that certain types of activity are illegal, but at the same time, it also advises the authorized and legitimate users of their obligations relating to acceptable use of the computerized or networked environment(s).

Pre-logon warning messages can deter unauthorized use, increase IT security awareness, and provide a legal basis for prosecuting unauthorized access.

#### Solution


```
UNAUTHORIZED ACCESS TO THIS DEVICE IS PROHIBITED
 You must have explicit, authorized permission to access or configure this device.
 Unauthorized attempts and actions to access or use this system may result in civil and/or
criminal penalties.
 All activities performed on this device are logged and monitored.
```

[C2S/CIS: CCE-27303-7 (Medium)](https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_banner_etc_issue)


### Passwords policy

#### Rationale

Conventionally, Unix shell accounts are accessed by providing a username and password to a login program, which tests these values for correctness using the `/etc/passwd` and `/etc/shadow` files. Password-based login is vulnerable to guessing of weak passwords, and to sniffing and man-in-the-middle attacks against passwords entered over a network or at an insecure console.

- setting the password warning age enables users to make the change at a practical time
    
    > The DoD requirement is 7. The C2S/CIS profile requirement is 7.
    
- enforcing a minimum password lifetime helps to prevent repeated password changes to defeat the password reuse
    
    > The DoD requirement is 1. The C2S/CIS profile requirement is 7.
    
- setting the password maximum age ensures users are required to periodically change their passwords
    
    > The DoD requirement is 60. The C2S/CIS profile requirement is 90.
    

Disabling inactive accounts ensures that accounts which may not have been responsibly removed are not available to attackers who may have compromised their credentials.

#### Solution


###### Set password expiration

`#Edit /etc/login.defs and set password warning age:`
`PASS_WARN_AGE 7`

 `Edit /etc/login.defs and set password minimum age:`
`PASS_MIN_DAYS 7`

 `Edit /etc/login.defs and set password maximum age:`
`PASS_MAX_DAYS 90

[C2S/CIS: CCE-26486-1 (unknown)](https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_accounts_password_warn_age_login_defs); [C2S/CIS: CCE-27002-5 (Medium)](https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_accounts_password_warn_age_login_defs); [C2S/CIS: CCE-27051-2 (Medium)](https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_accounts_password_warn_age_login_defs)
###### Set account expiration

`Edit /etc/default/useradd:`
`INACTIVE=30`

[C2S/CIS: CCE-27355-7 (Medium)](https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_account_disable_post_pw_expiration)



### Restrict root logins

#### Rationale

Direct root logins should be allowed only for emergency use. In normal situations, the administrator should access the system via a unique unprivileged account, and then use `su` or `sudo` to execute privileged commands.

#### Solution

###### Verify only root has UID 0

Multiple accounts with a UID of 0 afford more opportunity for potential intruders to guess a password for a privileged account.

```shell
awk -F: '$3 == 0 && $1 != "root" { print $1 }' /etc/passwd | xargs passwd -l

```

[C2S/CIS: CCE-27175-9 (High)](https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_accounts_no_uid_except_zero)

###### Protect direct root logins

Disabling direct root logins ensures proper accountability and multifactor authentication to privileged accounts.

```shell
echo > /etc/securetty
```

[C2S/CIS: CCE-27294-8 (Medium)](https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_no_direct_root_logins)

###### Protect direct root logins

Ensuring shells are not given to system accounts upon login makes it more difficult for attackers to make use of system accounts.

> Do not perform the steps in this section on the root account.

```shell
usermod -s /sbin/nologin SYSACCT
```

[C2S/CIS: CCE-26448-1 (Medium)](https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_no_shelllogin_for_systemaccounts)
#### Comments


A blank `/etc/securetty` file does not prevent the root user from logging in remotely using the OpenSSH suite of tools because the console is not opened until after authentication.


## **Auditd**

The audit service provides substantial capabilities for recording system activities.

By default, the service audits about SELinux AVC denials and certain types of security-relevant events such as system logins, account modifications, and authentication events performed by programs such as sudo.

### Enable auditing for processes which start prior to the audit daemon

#### Rationale


Each process on the system carries an "auditable" flag which indicates whether its activities can be audited. Although auditd takes care of enabling this for all processes which launch after it does, adding the kernel argument ensures it is set for every process during boot.

#### Solution

###### Set the value

`Add to /etc/default/grub:`
`GRUB_CMDLINE_LINUX="... audit=1"`

`Updated grub configuration:`
`grub2-mkconfig -o`






### Enable auditd service

#### Rationale


Without establishing what type of events occurred, it would be difficult to establish, correlate, and investigate the events leading up to an outage or attack. Ensuring the auditd service is active ensures audit records generated by the kernel are appropriately recorded.

#### Solution

###### Set the value


`systemctl enable auditd.service`



### Max log file size

#### Rationale


The total storage for audit log files must be large enough to retain log information over the period required. This is a function of the maximum log file size and the number of logs retained.
#### Solution

###### Set the value

###### Edit /etc/audit/auditd.conf:

`max_log_file = STOREMB`


### Notification on low disk space

#### Rationale

Email sent to the root account is typically aliased to the administrators of the system, who can take appropriate action.

#### Solution

###### Set the value

`#Edit /etc/audit/auditd.conf:`
`action_mail_acct = root`



### Action on low disk space

#### Rationale

Administrators should be made aware of an inability to record audit records. If a separate partition or logical volume of adequate size is used, running low on space for audit records should never occur.

#### Solution

###### Set the value


 `#Edit /etc/audit/auditd.conf:`
`admin_space_left_action = ACTION`

