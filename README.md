# 🚀 Ubuntu SSH Remote Access Project (VMware Bridged)

This repository documents the process of setting up a secure and automated **Remote SSH Access** between a Windows 11 host and an Ubuntu Server Virtual Machine.

## 🎯 Goal
To demonstrate a successful SSH handshake within a local Wi-Fi network using a **Bridged Networking** configuration, while automating the server-side setup to prevent common "Permission Denied" and "Account Locked" errors.

---

## 🛠️ The Technical Setup

### 🌐 Networking: VMware Bridged Mode
Instead of using NAT (which hides the VM behind the host's IP), I used **Bridged Networking**. 
* **The Logic:** This allows the Ubuntu VM to connect directly to the Wi-Fi router. 
* **The Result:** The VM receives its own unique IP address (e.g., `192.168.x.x`) on the same subnet as the Windows host, making them "peers" on the network.

### 📜 The Master Script: `manage_ssh.sh`
I authored a Bash script to handle the "dirty work" of server administration. It ensures the environment is perfect for a connection.

**Key Features of the Script:**
* **Auto-Install:** Uses `DEBIAN_FRONTEND=noninteractive` to skip purple dialog boxes during OpenSSH installation.
* **Smart Config:** Uses `sed` regex `^#\?PasswordAuthentication` to force-enable password logins.
* **Lock Clearing:** Automatically runs `faillock --reset` to clear failed login attempts.
* **Silent Execution:** Redirects streams using `2>&1 > /dev/null` for a clean UI.

---

## 📸 Proof of Concept

### 1. Server-Side Execution
The following image shows the `manage_ssh.sh` script running successfully. Note the IP address detection and the "Active" status of the SSH service.

![Ubuntu Terminal Success](https://github.com/Syed-Hermain/SSH-server/blob/41ea97773dc51648ba6387d996f016d95173bb17/view/ubuntuserver.png)
*Caption: Ubuntu terminal showing the script output and `sudo systemctl status ssh` confirming the server is listening.*

### 2. Client-Side Connection
This image confirms the connection from the Windows PowerShell terminal. By bypassing Public Key checks, I successfully reached the Ubuntu bash prompt.

![Windows Terminal Success](https://github.com/Syed-Hermain/SSH-server/blob/41ea97773dc51648ba6387d996f016d95173bb17/view/loggedin.png)
*Caption: Windows terminal showing the successful login to the Ubuntu VM via SSH.*

---

## 🔍 Technical Deep Dive

| Command / Tool | What it actually does |
| :--- | :--- |
| `awk '{print $1}'` | Isolates the primary IP from the `hostname -I` string. |
| `sed -i` | Edit-in-place; surgically changes config files without opening `nano`. |
| `2>&1` | Merges the "Error" stream into the "Output" stream. |
| `systemctl enable --now` | Ensures SSH starts immediately AND stays on after a reboot. |

---

## 🚀 How to Replicate This Setup

1. **On Ubuntu:**
   - Clone this repo: `git clone <your-repo-link>`
   - Grant permissions: `chmod +x manage_ssh.sh`
   - Run the master script: `./manage_ssh.sh`

2. **On Windows:**
   - Open PowerShell.
   - Run: `ssh -o PreferredAuthentications=password -o PubkeyAuthentication=no ubuntu@<VM_IP>`


---
*Created by Syed Hermain*