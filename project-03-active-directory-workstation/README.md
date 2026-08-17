# Project 3: Active Directory Workstation Integration & Access Control

## Overview

This project demonstrates the deployment and integration of a Windows 11 workstation into an Active Directory environment using Windows Server. The workstation was joined to the `hectortech.local` domain and configured to authenticate users through the domain controller.

The project also demonstrates DNS troubleshooting, Active Directory security group management, SMB network sharing, and NTFS permissions.

A key focus of this project was troubleshooting real-world configuration issues rather than simply completing a successful configuration.


---

## Lab Environment

| Component            | Configuration    |
| -------------------- | ---------------- |
| Domain Controller    | HT-DC01          |
| Domain Controller IP | 192.168.71.10    |
| Domain               | hectortech.local |
| Workstation          | HT-WS01          |
| Workstation OS       | Windows 11       |
| Workstation IP       | 192.168.71.130   |
| Test User            | barbara.hector   |
| Security Group       | IT-Support       |
| Shared Resource      | `\\HT-DC01\IT`   |

### Network Configuration

* Network: `192.168.71.0/24`
* Default Gateway: `192.168.71.2`
* HT-DC01: `192.168.71.10`
* HT-WS01: `192.168.71.130`
* DNS Server: `192.168.71.10`

---

## Objectives

The primary objectives of this project were to:

* Deploy a Windows 11 virtual workstation.
* Configure the workstation for the existing lab network.
* Configure DNS to use the Active Directory domain controller.
* Join the Windows 11 workstation to the `hectortech.local` domain.
* Authenticate to the workstation using an Active Directory user account.
* Verify Active Directory security group membership.
* Configure and test an SMB network share.
* Apply role-based NTFS permissions using an Active Directory security group.
* Troubleshoot authentication, DNS, group membership, and access-control issues.
* Validate successful remote file access and modification.

---

## Active Directory Structure

The Active Directory environment contains organizational units for different departments and workstation objects.

```text
hectortech.local
│
├── IT
│   ├── barbara.hector
│   └── IT-Support
│
├── HR
│   └── HR-Team
│
├── Sales
│   └── Sales-Team
│
└── Workstations
    └── HT-WS01
```

The `IT-Support` group was used to provide role-based access to resources assigned to the IT department.

---

## Implementation

### 1. Windows 11 Workstation Deployment

A Windows 11 virtual machine was created in VMware and named:

`HT-WS01`

The workstation received the following network configuration:

```text
IPv4 Address:    192.168.71.130
Subnet Mask:     255.255.255.0
Default Gateway: 192.168.71.2
DNS Server:      192.168.71.10
```

The workstation was configured to use the domain controller as its DNS server.

---

### 2. DNS Verification

Connectivity between HT-WS01 and HT-DC01 was verified using:

```text
ping 192.168.71.10
```

Result:

```text
Sent = 4
Received = 4
Lost = 0
```

DNS resolution was then tested using:

```text
nslookup HT-DC01.hectortech.local
```

The domain controller successfully resolved to:

```text
HT-DC01.hectortech.local
192.168.71.10
```

This confirmed that HT-WS01 could communicate with the DNS service provided by the domain controller.

---

### 3. Domain Join

HT-WS01 was joined to:

```text
hectortech.local
```

The workstation successfully displayed the Windows confirmation:

> Welcome to the hectortech.local domain.

After restarting, a domain user successfully authenticated to the workstation.

The authenticated account was verified using:

```text
whoami
```

Result:

```text
hectortech\barbara.hector
```

The domain controller responsible for authentication was verified using:

```text
echo %logonserver%
```

Result:

```text
\\HT-DC01
```

---

### 4. Security Group Verification

The test user `barbara.hector` was a member of the:

```text
IT-Support
```

Active Directory security group.

The group was configured as:

```text
Group Scope: Global
Group Type: Security
```

After establishing a fresh domain logon session, group membership was verified using:

```text
whoami /groups
```

The `HECTORTECH\IT-Support` group was successfully included in the user's security token.

---

### 5. SMB Network Share

An `IT` folder located on HT-DC01 was configured as a network share:

```text
\\HT-DC01\IT
```

The share was accessed remotely from HT-WS01 using the domain user account.

---

### 6. NTFS Permissions

The `IT-Support` security group was assigned:

```text
Modify
```

NTFS permissions on the IT folder.

The permission applied to:

```text
This folder, subfolders and files
```

This configuration provided members of the IT-Support group with the ability to create and modify files within the shared resource.

---

## Troubleshooting

### Issue 1: Domain Join Authentication Failure

The initial domain join attempt failed because the account being used for authentication was configured to require a password change at the next logon.

**Resolution:**

The Active Directory user account configuration was corrected, and the domain join was successfully completed.

---

### Issue 2: DNS Resolution

HT-WS01 was initially able to communicate with HT-DC01 by IP address but required verification of DNS resolution.

**Resolution:**

HT-WS01 was configured to use HT-DC01 (`192.168.71.10`) as its DNS server.

DNS resolution was successfully verified using `nslookup`.

---

### Issue 3: Security Group Membership Not Reflected in User Session

The user account was confirmed as a member of the `IT-Support` security group in Active Directory, but the existing Windows session did not initially reflect the updated group membership.

**Resolution:**

The user signed out and established a new domain session. The updated security token was then verified using:

```text
whoami /groups
```

The `HECTORTECH\IT-Support` group was successfully displayed.

---

### Issue 4: Access Denied When Creating Files

The user could successfully open the `IT` network share but received an **Access Denied** error when attempting to create a new file.

**Root Cause:**

The SMB share permissions allowed Read access while NTFS permissions provided Modify access.

Because share and NTFS permissions work together for network access, the more restrictive share permission limited the user's effective access.

**Resolution:**

The share permission was changed to allow Change access while retaining the NTFS Modify permission assigned to the `IT-Support` security group.

The user was then able to successfully create and modify files remotely.

---

## Validation

The final configuration was successfully validated by:

* Successfully joining HT-WS01 to the Active Directory domain.
* Successfully authenticating using a domain account.
* Confirming domain authentication through HT-DC01.
* Confirming `IT-Support` security group membership.
* Accessing the `IT` SMB network share.
* Creating a file remotely from HT-WS01.
* Modifying the file using the domain user's permissions.

---

## Skills Demonstrated

* Windows 11 administration
* Windows Server administration
* Active Directory Domain Services (AD DS)
* Active Directory Users and Computers
* DNS configuration and troubleshooting
* Domain joining
* Domain authentication
* Security group management
* User and group administration
* SMB network shares
* NTFS permissions
* Role-Based Access Control (RBAC)
* Windows command-line troubleshooting
* `ipconfig`
* `ping`
* `nslookup`
* `whoami`
* Authentication troubleshooting
* Access-control troubleshooting
* Virtual machine administration
* VMware

---

## Key Takeaways

This project reinforced the relationship between several core Windows infrastructure components:

```text
DNS
 ↓
Active Directory
 ↓
Domain Authentication
 ↓
Security Groups
 ↓
User Security Token
 ↓
SMB Share
 ↓
NTFS Permissions
 ↓
Resource Access
```

The project also demonstrated the importance of troubleshooting each layer independently rather than assuming that a successful network connection guarantees successful authentication or resource access.

---

## Project Status

**Completed ✅**

The Windows 11 workstation is successfully integrated into the `hectortech.local` Active Directory environment, and role-based access to the IT network resource has been successfully tested and validated.
