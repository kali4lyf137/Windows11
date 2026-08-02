# 🖥️ Windows RDP via Tailscale — GitHub Actions Workflow

## 📌 Overview

This GitHub Actions workflow creates a secure, temporary Windows RDP (Remote Desktop) environment accessible via Tailscale's private mesh network. It provisions a Windows runner, configures RDP, creates a user with a strong password, and establishes a Tailscale connection for secure access.

---

## 🎯 What This Workflow Does

**Configures Windows RDP**
- Enables Remote Desktop Protocol (RDP)
- Disables Network Level Authentication (NLA) for compatibility
- Configures Windows Firewall to allow RDP (port 3389)

**Creates a Secure RDP User**
- Automatically generates a complex 16-character password (uppercase, lowercase, numbers, and special characters)
- Adds the user to Administrators and Remote Desktop Users groups

**Installs Tailscale**
- Downloads and installs the latest Tailscale MSI package

**Establishes Tailscale Connection**
- Authenticates using a provided Tailscale auth key
- Assigns a unique hostname based on the GitHub run ID
- Retrieves the Tailscale IP address for remote access

**Verifies RDP Accessibility**
- Tests TCP connectivity to port 3389 via Tailscale IP

**Maintains Connection**
- Keeps the runner alive indefinitely
- Displays RDP credentials and Tailscale IP for user access

---

## 🔑 How This Helps You

| Use Case | Benefit |
|---|---|
| Temporary Windows Environment | Spin up a Windows VM on-demand without provisioning infrastructure |
| Secure Remote Access | Access the Windows runner via Tailscale's encrypted mesh network |
| Testing & Debugging | Connect via RDP to troubleshoot, test software, or perform manual tasks |
| CI/CD Automation | Use as a build agent for Windows-specific workflows |
| Cost-Effective | No need to maintain a dedicated Windows VPS; runs on GitHub's infrastructure |
| Ephemeral & Secure | Credentials are generated per run; environment is fresh each time |

---

## 🛠️ Prerequisites

Before using this workflow, you need:

1. **GitHub Repository** with Actions enabled
2. **Tailscale Account** – Sign up (free tier available)
3. **Tailscale Auth Key** – Generate a reusable auth key from your Tailscale admin console
4. **GitHub Secret** – Add the Tailscale auth key to your repository secrets:

```
Settings → Secrets and variables → Actions → New repository secret

Name: TAILSCALE_AUTH_KEY
Value: your-tailscale-auth-key
```

---

## 📂 Workflow File

Place the YAML file in:

```
.github/workflows/rdp-tailscale.yml
```

---

## 🚀 How to Use

1. **Trigger the Workflow**
   - Go to your repository on GitHub
   - Click on the **Actions** tab
   - Select the workflow from the list
   - Click **"Run workflow"** (manual trigger via `workflow_dispatch`)

2. **Wait for the Runner to Start**
   - The workflow will install Tailscale, configure RDP, and set up the user

3. **Get Access Details**
   In the workflow logs, look for the section:

   ```
   === RDP ACCESS ===
   Address: <tailscale-ip>
   Username: RDP
   Password: <generated-password>
   ==================
   ```

4. **Connect via RDP**
   - Use any RDP client (Windows Remote Desktop, Remmina, etc.)
   - Enter the Tailscale IP as the computer address
   - Use the credentials displayed in the logs

---

## ⚠️ Important Notes

| Consideration | Details |
|---|---|
| Security | The credentials are visible in the GitHub Actions logs. Only run this in private repositories or for testing purposes. |
| Cost | GitHub Actions uses free runner minutes (depending on your plan). Long-running sessions consume significant minutes. |
| Access | Only users with access to your Tailscale network can connect. No public exposure. |
| Persistence | The runner will stay alive until you manually cancel the workflow or it times out. |
| Session Limits | The workflow has a timeout set to 3600000 minutes (~6.8 years) – effectively indefinite. |

---

## 🛡️ Security Best Practices

- ✅ **Use Private Repositories** – Prevent credential exposure
- ✅ **Rotate Auth Keys** – Generate new Tailscale keys periodically
- ✅ **Limit Permissions** – Restrict who can manually trigger workflows
- ✅ **Monitor Tailscale Network** – Check active devices regularly
- ✅ **Use NLA** – Enable Network Level Authentication for better security (if your client supports it)

---

## 🧪 Example Access Commands

**Windows RDP Client**
```
Computer: <tailscale-ip>
Username: RDP
Password: <generated-password>
```

**Linux (Remmina / xfreerdp)**
```bash
xfreerdp /v:<tailscale-ip> /u:RDP /p:<password>
```

**macOS (Microsoft Remote Desktop)**
- Add new connection
- PC name: `<tailscale-ip>`
- Credentials: User `RDP` with generated password

---

## 🧩 Customization Options

| Modification | How to Change |
|---|---|
| RDP User Name | Edit the `New-LocalUser -Name "RDP"` line |
| Password Complexity | Adjust the password generation logic (length, character sets) |
| Tailscale Version | Update the download URL in the Install Tailscale step |
| Hostname Format | Modify the `--hostname` parameter in the `Tailscale up` command |
| Port Forwarding | Change the RDP port (3389) in firewall rules and service settings |

---

## ❌ Limitations

- **Windows-only** – Requires GitHub's `windows-latest` runner
- **Not suitable for production** – Designed for ephemeral testing/access
- **Logs contain sensitive data** – Passwords are printed in plain text (use with caution)
- **Manual trigger only** – No automated triggers (can be modified)

---

## 📖 Troubleshooting

| Issue | Solution |
|---|---|
| Tailscale IP not assigned | Check network connectivity; increase retry count/wait time |
| RDP connection fails | Verify Tailscale IP, ensure firewall rules are applied |
| User creation fails | Check permissions; run as Administrator (already default) |
| Firewall blocks RDP | Confirm the firewall rule is added with `netsh` command |
| Timeout/runner stops | The `while($true)` loop keeps it alive; cancel manually when done |

---

## 📝 License

This workflow is provided "as-is" without any warranty. Use at your own risk.

---

## 🤝 Contributions

Feel free to fork, modify, and improve this workflow for your specific use case.

---

## 📬 Support

For issues related to:
- **Tailscale** – Check Tailscale Docs
- **GitHub Actions** – Review GitHub Docs
- **RDP Configuration** – See Microsoft RDP Documentation

---

Happy RDP-ing! 🖥️ 🔒
