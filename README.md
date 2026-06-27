# Cloud Virtual Machine Setup and Secure Remote Connectivity

## Project Overview
This project demonstrates the provisioning, configuration, and secure remote management of cloud-based Virtual Machines within Microsoft Azure. It highlights cross-platform administration (Windows and Linux) and enforces strict network security principles by managing Network Security Groups (NSGs) to restrict inbound traffic.

## 1. Infrastructure Deployment
Two virtual machines were deployed into a centralized resource group to validate different remote connectivity protocols:
* **Windows Server 2022:** Configured for Remote Desktop Protocol (RDP) access.
* **Ubuntu Linux 22.04:** Configured for Secure Shell (SSH) access utilizing key-based authentication.

## 2. Network Security Configuration (NSG)
To adhere to the principle of least privilege and prevent unauthorized exposure, default NSG rules were modified. Access was restricted exclusively to a trusted local IP address.

| Virtual Machine | Protocol | Port | Source IP | Destination | Action |
|-----------------|----------|------|-----------|-------------|--------|
| `vm-windows-01` | RDP      | 3389 | `20.84.64.171` | Any         | Allow  |
| `vm-linux-01`   | SSH      | 22   | `20.119.73.74` | Any         | Allow  |
| *All* | Any      | Any  | `*`       | Any         | Deny   |


## 3. Remote Connection Validation
Secure sessions were successfully established and verified:
* **Linux:** Authenticated via SSH using RSA key pairs. Verified system architecture using `uname -a`.
* **Windows:** Authenticated via RDP. Verified system resources via PowerShell `systeminfo`.

Proper session lifecycle management was observed by explicitly signing out of the Windows session and executing the `exit` command in Linux, preventing hung sessions from consuming compute resources.

