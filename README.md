# Active Directory & Windows Server Lab

A hands-on home lab built to practice Windows Server administration, Active Directory, DNS, Group Policy, account administration, PowerShell, file sharing, permissions, and troubleshooting in a virtualized environment.

## Lab Environment

- VirtualBox
- Windows Server 2019
- Windows 10 Pro
- Active Directory Domain Services (AD DS)
- DNS
- Group Policy
- PowerShell

## Lab Architecture

```text
DC01
Windows Server 2019
AD DS + DNS
10.10.10.10
        |
        | adlab.test domain
        |
        v
CLIENT01
Windows 10 Pro
Domain-joined workstation
```

## What I Configured

### Windows Server and Domain Services

- Installed Windows Server 2019 in VirtualBox
- Configured a static IPv4 address for the domain controller
- Installed Active Directory Domain Services
- Installed and configured DNS
- Promoted the server to a domain controller
- Created the `adlab.test` domain
- Created organizational units for departments
- Created domain users and security groups
- Joined a Windows 10 Pro client workstation to the domain

## User and Group Administration

Practiced common Active Directory user-management tasks including:

- Creating user accounts
- Setting and resetting passwords
- Enabling and disabling accounts
- Managing group membership
- Moving users between organizational units
- Testing password history requirements
- Testing minimum password length requirements
- Configuring account lockout policy
- Identifying locked accounts
- Unlocking accounts
- Verifying account status with PowerShell

Example PowerShell commands used:

```powershell
Get-ADUser -Filter *
Get-ADUser -Identity schen -Properties *
Get-ADGroup -Filter *
Get-ADGroupMember "Accounting-Users"
Get-ADPrincipalGroupMembership mlee
New-ADUser
Set-ADAccountPassword
Enable-ADAccount
Disable-ADAccount
Unlock-ADAccount
Add-ADGroupMember
Search-ADAccount -LockedOut
```

## Group Policy

Created and tested organizational-unit-based Group Policy.

Example policy:

- Created an `Accounting User Policy`
- Linked the policy to the Accounting organizational unit
- Configured a user restriction through Group Policy
- Updated policy on the Windows 10 client
- Verified applied Group Policy objects

Commands used:

```cmd
gpupdate /force
gpresult /r
```

Testing included:

- Confirming the policy applied to Accounting users
- Moving users between OUs
- Verifying that OU placement changed which policy applied
- Confirming that security-group membership remained independent of OU placement
- Moving users back and validating policy reapplication

## Domain Authentication

Configured CLIENT01 as a Windows 10 Pro domain workstation and joined it to `adlab.test`.

Verified domain authentication with:

```cmd
whoami
echo %logonserver%
```

Example successful results:

```text
adlab\schen
\\DC01
```

This confirmed that the user was authenticated through the domain controller rather than a local Windows account.

## DNS and Network Troubleshooting

Configured the virtual network so DC01 and CLIENT01 could communicate on the same subnet.

Domain controller configuration:

```text
IPv4: 10.10.10.10
Subnet Mask: 255.255.255.0
Default Gateway: 10.10.10.1
DNS: 10.10.10.10
```

Troubleshooting included:

- Identifying duplicate `10.0.2.15` addresses caused by separate VirtualBox NAT environments
- Moving both virtual machines to a shared VirtualBox NAT Network
- Reconfiguring DC01 with a static IP
- Configuring CLIENT01 to use DC01 for DNS
- Testing connectivity between CLIENT01 and DC01
- Verifying DNS service status
- Testing DNS resolution locally and remotely
- Testing port 53 connectivity

Commands used included:

```cmd
ipconfig /all
ping 10.10.10.10
nslookup adlab.test
nslookup DC01.adlab.test
ipconfig /flushdns
```

```powershell
Get-Service DNS
Resolve-DnsName adlab.test
Resolve-DnsName DC01.adlab.test
Test-NetConnection 10.10.10.10 -Port 53
```

## Account Lockout Troubleshooting

Configured a lab account lockout policy:

- 3 failed attempts
- 10-minute lockout duration
- 10-minute counter reset

Tested the full troubleshooting workflow:

1. Entered an incorrect password repeatedly from CLIENT01
2. Confirmed the account became locked
3. Queried account state from DC01
4. Searched for locked accounts
5. Unlocked the account with PowerShell
6. Verified successful login afterward

Commands used:

```powershell
Get-ADUser tuser -Properties LockedOut,Enabled,PasswordLastSet
Search-ADAccount -LockedOut
Unlock-ADAccount -Identity tuser
```

## SMB Shares and NTFS Permissions

Created an Accounting network share and used Active Directory security groups to control access.

Example structure:

```text
\\DC01\Accounting
        |
        +-- Administrators -> Full Control
        +-- SYSTEM -> Full Control
        +-- Accounting-Users -> Modify
```

Testing included:

- Granting access through the `Accounting-Users` group
- Confirming an Accounting user could open, modify, and create files
- Confirming an IT user was denied access
- Removing a user from the Accounting security group
- Signing out and back in to refresh the user's logon token
- Verifying access was removed
- Adding the user back to the group
- Verifying access returned

Used:

```cmd
whoami /groups
```

to inspect security-group membership in the current user token.

## Key Concepts Practiced

- Active Directory Domain Services
- Domain controller administration
- Organizational units
- User and group administration
- Password policies
- Account lockouts
- Group Policy
- Domain-joined workstations
- DNS
- TCP/IP configuration
- VirtualBox networking
- PowerShell administration
- SMB file sharing
- NTFS permissions
- Security-group-based access control
- Windows authentication tokens
- Troubleshooting and validation

## Troubleshooting Scenarios Completed

- Client could ping the domain controller but DNS queries initially timed out
- Domain controller IP address changed after AD/DNS configuration
- User account disabled and re-enabled
- Password reset and password-policy validation
- User moved between organizational units
- Group membership changed independently from OU placement
- Group Policy removed and reapplied based on OU placement
- Account lockout created and resolved
- Network share access granted and denied based on security-group membership
- User token refreshed through sign-out/sign-in after group changes

## Future Improvements

Planned lab extensions include:

- Event Viewer troubleshooting
- Additional Group Policy scenarios
- More intentional DNS and domain failures
- Windows deployment and imaging
- Remote administration with RSAT
- Additional PowerShell automation
- CSV-based bulk user creation
- Technical runbooks and troubleshooting documentation

## Resume Summary

This lab supports hands-on experience with:

- Active Directory
- Windows Server 2019
- Group Policy
- DNS
- PowerShell
- Windows 10 domain administration
- SMB shares
- NTFS permissions
- Identity and access management fundamentals
- Windows troubleshooting

## Author

Jason Gonzalez Molina  
Computer Science student at California State University, Dominguez Hills