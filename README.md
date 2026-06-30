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

## RDP Step by step Setup
Connecting to a Windows Virtual Machine hosted in Microsoft Azure via RDP is very similar to connecting to a local PC, but you will gather the connection details directly from the Azure Portal.

Here is the straightforward process to get connected.

## Phase 1: Download the RDP File from Azure

1. **Log into Azure:** Open your web browser and sign in to the [Azure Portal](https://www.google.com/search?q=https://portal.azure.com).
2. **Find Your VM:** Use the top search bar to search for **Virtual machines** and select it.
3. **Select the VM:** Click on the specific name of the Virtual Machine you want to connect to.
4. **Verify Status:** Ensure the VM's status says **Running**. If it is stopped, click the **Start** button at the top of the Overview page and wait a few moments.
5. **Initiate Connection:** On the left-hand menu under the VM's settings, click **Connect**, then select **RDP** from the flyout menu or the main screen.
6. **Download:** Leave the default IP address and Port (3389) settings as they are, and click the **Download RDP File** button.

---

## Phase 2: Connect from Your Local Device

1. **Open the File:** Locate the `.rdp` file you just downloaded and double-click it to open it.
2. **Accept the Prompt:** A warning may appear asking if you trust the publisher of this remote connection. Click **Connect**.
3. **Enter Credentials:** When the Windows Security window pops up, click **More choices** and then **Use a different account**.
4. **Log In:** Enter the username and password you configured when you originally created the Azure VM.
5. **Trust the Certificate:** You will see a security warning about the remote computer's certificate. Check the box for **Don't ask me again for connections to this computer** and click **Yes**.

---

## Azure-Specific Troubleshooting

If the connection times out or fails immediately, the issue is almost always related to Azure's network security rules.

* **Check the NSG (Network Security Group):** Azure blocks all incoming traffic by default. You must ensure that your VM's network settings allow inbound traffic on **Port 3389** (the port used for RDP). Go to your VM in the Azure Portal, click **Networking** on the left menu, and verify there is an inbound port rule allowing RDP traffic.
* **Public IP Requirement:** Unless your company is using a VPN or Azure ExpressRoute to connect directly to the Azure network, your VM must have a Public IP address assigned to it to connect over the standard internet.

## Step by step SSH into Linux VM
# How to Connect to a Linux VM via SSH

Secure Shell (SSH) is the standard method for connecting to Linux machines. Unlike RDP, which gives you a visual graphical desktop, SSH connects you directly to the machine's command-line terminal.

> **Important Prerequisite:** Before you begin, you need the **Public IP address** of your Linux VM, your **username**, and either your **password** or the **private SSH key file** (`.pem` or `.pub` file) you generated when creating the VM.

---

## Phase 1: Open Your Terminal

Modern operating systems have SSH clients built directly into their default command-line tools, so you usually do not need to download third-party software like PuTTY anymore.

* **Windows 10/11:** Click the Start button, type **cmd** or **PowerShell** or **GitBash**, and press `Enter`.
* **macOS:** Press `Cmd + Space` to open Spotlight, type **Terminal**, and press `Enter`.
* **Linux:** Open your preferred terminal emulator (usually `Ctrl + Alt + T`).

---

## Phase 2: Run the SSH Command

The way you connect depends entirely on how you secured the VM when you built it.

### Option A: Connecting with a Password

If you configured the Linux VM with a standard username and password:

1. Type the following command in your terminal and press `Enter`:
`ssh your_username@your_vm_ip_address`
*(Example: `ssh azureuser@203.0.113.5`)*
2. **Accept the Fingerprint:** The first time you connect to a new VM, your computer will ask if you trust the host. Type **yes** and press `Enter`.
3. **Enter Password:** Type your password and press `Enter`. **Note:** As a security feature in Linux, your cursor will not move and characters will not appear on the screen as you type. Type the password blindly and press enter.

### Option B: Connecting with an SSH Key (Highly Recommended)

If you downloaded a private key file (usually a `.pem` file) from Azure, AWS, or Google Cloud:

1. **Locate your key:** Note the exact file path where your `.pem` file is saved on your local computer (e.g., `C:\Users\Name\Downloads\mykey.pem`).
2. **Run the connection command:** Use the `-i` flag to point your terminal to that specific key file:
`ssh -i path\to\your\key.pem your_username@your_vm_ip_address`
*(Example: `ssh -i C:\keys\mykey.pem azureuser@203.0.113.5`)*
3. Type **yes** to accept the server fingerprint if prompted. If the key is correct, you will be logged right into the Linux prompt.

---

## Troubleshooting Tips

If your connection fails or hangs, it is almost always due to network rules or key permissions:

* **Connection Timed Out:** Your cloud provider's firewall is blocking you. Check your VM's Network Security Group (Azure) or Security Group (AWS) and ensure there is an inbound rule allowing traffic on **Port 22** (the designated SSH port).
* **Unprotected Private Key Error (Mac/Linux clients):** SSH will reject your key file if it is readable by other users on your local computer. Run `chmod 400 yourkey.pem` in your terminal to restrict the file's permissions, then try connecting again.
* **Permission Denied (publickey):** You are either typing the wrong admin username, pointing to the wrong `.pem` file, or your key was never properly injected into the VM during creation. Verify the exact username in your cloud portal.