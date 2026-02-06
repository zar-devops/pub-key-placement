# 🚀 Add SSH Public Key to Remote Server

This GitHub Action automates the process of adding a new SSH public key to a remote server's `authorized_keys` file. It is designed to be **idempotent**—meaning if the key already exists on the server, it will not be added again, preventing duplicate entries.

---

## 🔐 Required GitHub Secrets

To use this workflow, you must navigate to **Settings > Secrets and variables > Actions** in your repository and add the following **Repository Secrets**:

| Secret Name | Description | Format Example |
| :--- | :--- | :--- |
| `SERVER_HOST` | The IP address or hostname of the remote server. | `1.23.45.67` or `myserver.com` |
| `SERVER_USER` | The username used to log in via SSH. | `root`, `ubuntu`, or `admin` |
| `SERVER_SSH_KEY` | The **Private Key** used to access the server. | `-----BEGIN OPENSSH PRIVATE KEY----- ...` |
| `SSH_PUB_KEY` | The **New Public Key** you want to authorize. | `ssh-rsa AAAAB3Nza... user@host` |

> **Note:** Ensure `SERVER_SSH_KEY` includes the header and footer lines and has a trailing newline for best compatibility.

---

## 🛠 How to Run Manually

This workflow is configured with `workflow_dispatch`, which allows you to trigger it manually without pushing new code.

1.  **Go to GitHub**: Open your repository in your browser.
2.  **Actions Tab**: Click the **Actions** tab at the top of the page.
3.  **Select Workflow**: Click on **"Add SSH Public Key"** from the list of workflows on the left.
4.  **Run Workflow**: 
    * Click the **Run workflow** dropdown button on the right.
    * Select the branch (e.g., `main`).
    * Click the green **Run workflow** button.



---

## 🏗 Workflow Logic

The script performs the following steps securely:
1. **Initializes SSH**: Creates the `.ssh` directory on the GitHub runner and loads your private key.
2. **Keyscan**: Automatically adds the server's fingerprint to `known_hosts` to prevent the connection from hanging on a "trust this host" prompt.
3. **Remote Setup**: Ensures the `.ssh` folder exists on the server with correct permissions (`700`).
4. **Smart Append**: Uses `grep` to check if the public key is already present. If missing, it appends the key to `authorized_keys` and sets permissions to `600`.

---

## ⚠️ Troubleshooting

* **Permission Denied**: Ensure the `SERVER_USER` has permission to write to their own `~/.ssh/authorized_keys` file.
* **Invalid Format**: If the job fails instantly, verify that the `SERVER_SSH_KEY` secret was pasted correctly with no accidental leading spaces.
* **Firewall**: Ensure the remote server allows SSH traffic (Port 22) from GitHub's IP ranges or is publicly accessible.
