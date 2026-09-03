# 📖 Detailed Server Hardening Guide (Ubuntu 26.04.1 LTS)

This guide documents the security hardening process for an Ubuntu 26.04.1 LTS instance on AWS EC2.

---

### 1. Initial System Update

Ensuring the system has the latest security patches.

```bash
sudo apt update && sudo apt upgrade -y
```

---

### 2. Advanced SSH Hardening

Default SSH settings are highly vulnerable. I moved the service to a custom port and enforced key-only access.

#### 🛡️ Hardening Steps:

- **Disabled Root Login:** Prevented direct root access.
- **Key-Pair Only:** Disabled password authentication.
- **Custom Port (2222):** Obscured the service from standard scanners.

#### ⚠️ Troubleshooting: Systemd Socket Activation

On Ubuntu 26.04.1 LTS, SSH is managed via `ssh.socket`. Standard `sshd_config` changes are ignored.

**The Fix:**

```bash
# 1. Edit the socket override
sudo systemctl edit ssh.socket

# 2. Paste these lines in the editor:
[Socket]
ListenStream=
ListenStream=2222

# 3. Reload systemd and restart the socket
sudo systemctl daemon-reload
sudo systemctl restart ssh.socket
```

---

### 3. Network Defense (UFW Firewall)

Implemented a "Deny-by-Default" ingress policy.

```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 2222/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw --force enable
```

---

### 4. Intrusion Prevention (Fail2Ban)

Configured automated response to block IP addresses showing malicious behavior.

- **Log Path:** `/var/log/auth.log`
- **Policy:** 3 failed attempts = 1-hour ban.

```bash
# Verify jail status
sudo fail2ban-client status sshd
```

---

### 5. Automated Security Patching

Enabled `unattended-upgrades` to automate the installation of critical security updates.

---

### 6. AWS CloudWatch Integration

Centralized logs for auditing and persistent monitoring.

1. **Install Agent:**

```bash
wget https://s3.amazonaws.com/amazoncloudwatch-agent/ubuntu/amd64/latest/amazon-cloudwatch-agent.deb
sudo dpkg -i -E ./amazon-cloudwatch-agent.deb
```

2. **Log Group:** Configured to push auth logs to `EC2-SSH-Security-Logs`.

---

### 🔍 Security Audit (Verification)

Verified the system using **Lynis Audit**.

- **Hardening Index:** 66
- **Status:** Hardened

---

### 🛠️ Troubleshooting Documentation

#### **Issue: "Too many authentication failures"**

- **Fix:** Force specific key using `IdentitiesOnly=yes`.

```bash
ssh -o IdentitiesOnly=yes -p 2222 -i key.pem ubuntu@ip
```

#### **Issue: Fail2Ban Socket Error**

- **Fix:** Debugged syntax in `jail.local` using `sudo fail2ban-server -t`.
