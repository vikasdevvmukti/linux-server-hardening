# 📖 Detailed Server Hardening & Setup Guide

This document provides a deep dive into the technical implementation of hardening an Ubuntu 24.04 LTS instance on AWS EC2. It covers the "Why" and "How" of each security layer.

---

## 🛠️ Prerequisites
- **AWS Account:** Free Tier instance (t2.micro).
- **Operating System:** Ubuntu 24.04 LTS.
- **IAM Permissions:** Role with `CloudWatchAgentServerPolicy` attached to the EC2 instance.

---

## 1. Initial System Update
Security begins with the latest patches. 
```bash
sudo apt update && sudo apt upgrade -y

2. Advanced SSH Hardening
Default SSH settings are a primary target for automated bots.
🛡️ Hardening Steps:
Disabled Root Login: Prevented direct access to the root account.
Key-Pair Only: Disabled password authentication to stop brute-force attacks.
Custom Port (2222): Moved SSH away from the standard port 22.
⚠️ Troubleshooting: Systemd Socket Activation
On Ubuntu 24.04, standard changes in /etc/ssh/sshd_config for the port are ignored due to Systemd Socket Activation.
The Fix:
code
Bash
# Edit the socket override
sudo systemctl edit ssh.socket

# Add these lines in the editor:
[Socket]
ListenStream=
ListenStream=2222

# Reload and Restart
sudo systemctl daemon-reload
sudo systemctl restart ssh.socket
3. Network Defense (UFW Firewall)
Implemented a "Deny-by-Default" ingress policy to ensure only authorized traffic enters the system.
code
Bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 2222/tcp  # Custom SSH Port
sudo ufw allow 80/tcp    # Web Traffic
sudo ufw allow 443/tcp   # Encrypted Web Traffic
sudo ufw enable
4. Intrusion Prevention (Fail2Ban)
Even on a custom port, bots can attempt connections. Fail2Ban provides an automated response.
Configured: /etc/fail2ban/jail.local
Jail Logic: If an IP fails to authenticate 3 times within 10 minutes, it is banned for 1 hour.
Service Verification:
code
Bash
sudo fail2ban-client status sshd
5. Automated Security Patching
To prevent the server from becoming vulnerable over time, I enabled Unattended-Upgrades. This ensures that critical security updates are installed daily without manual intervention.
6. AWS CloudWatch Integration
For production environments, local logs aren't enough. Centralized logging is critical for auditing.
Installed CloudWatch Agent:
code
Bash
wget https://s3.amazonaws.com/amazoncloudwatch-agent/ubuntu/amd64/latest/amazon-cloudwatch-agent.deb
sudo dpkg -i -E ./amazon-cloudwatch-agent.deb
Log Collection: Targeted /var/log/auth.log to monitor all login attempts directly from the AWS CloudWatch Dashboard.
🔍 Verification & Auditing (Lynis)
Verification was performed using Lynis, the industry-standard security auditing tool for Linux.
Key Findings:
Hardening Index: 66
Status: All high-risk vulnerabilities resolved.
Firewall/IDS Status: Detected and Active.
🛠️ Real-World Troubleshooting Notes
Issue: "Too many authentication failures"
Cause: When connecting locally, the SSH client was trying all available keys in the SSH agent, hitting the server's MaxAuthTries limit.
Fix: Used the following command to force only the specific key:
code
Bash
ssh -o IdentitiesOnly=yes -p 2222 -i your-key.pem ubuntu@your-ip
Issue: Fail2Ban failed to start
Cause: Syntax error in jail.local configuration.
Fix: Debugged using sudo fail2ban-server -t to identify the exact line of error before restarting.
