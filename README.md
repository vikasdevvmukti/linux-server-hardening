# 🛡️ AWS EC2 Production-Grade Linux Hardening 

**Objective:** Secure a vulnerable default Linux instance into a production-ready environment with a "Zero-Trust" architecture.

---

### 🔴 The Problem (The Risks)
Most default cloud instances are "low-hanging fruit" for hackers. Out-of-the-box settings often lead to:
* **Brute-Force Attacks:** Bots constantly scanning Port 22 for weak passwords.
* **Unauthorized Access:** Unrestricted network ports exposing internal services.
* **Data Loss:** No centralized logs to audit who did what and when.
* **Unpatched Vulnerabilities:** Security holes left open due to lack of automated updates.

---

### 🟢 The Solution (The Fortress)
I implemented a multi-layered security strategy to mitigate these risks. I transformed the instance into a hardened environment where:
1. **Identity is Mandatory:** Password-based logins are disabled; only cryptographic keys are allowed.
2. **Attack Surface is Minimized:** Only essential traffic (HTTPS/Custom SSH) is permitted.
3. **Real-time Defense:** Malicious IPs are automatically banned upon detecting suspicious behavior.
4. **Full Accountability:** Every login and system event is streamed to the cloud for permanent auditing.

---

### 🛠️ Execution Steps & Visual Proof

#### 1. Access Control (SSH Hardening)
* Migrated SSH from the default Port 22 to a **custom port (2222)**.
* Disabled `root` login and enforced **Key-Pair only authentication**.
* **Visual Proof:** Security Group restricted to custom port.
> <img width="1914" height="636" alt="Screenshot from 2026-09-03 11-46-11" src="https://github.com/user-attachments/assets/cbe4746b-8c31-4690-92d9-db07717fd645" />


#### 2. Network Guard (UFW Firewall)
* Implemented a **"Deny-by-Default"** policy.
* Strictly allowed only ports 2222 (SSH).
> <img width="1478" height="339" alt="Screenshot from 2026-09-03 11-41-38" src="https://github.com/user-attachments/assets/905dbf17-6e67-49d5-8cff-6d116e76b1ad" />


#### 3. Intrusion Prevention (Fail2Ban)
* Deployed **Fail2Ban** to monitor authentication logs.
* Configured a 1-hour ban for any IP failing 3 login attempts.
> <img width="1478" height="339" alt="Screenshot from 2026-09-03 11-41-15" src="https://github.com/user-attachments/assets/6a4ca452-2f70-4f52-8ac5-b9f6eaadc19a" />



#### 4. Centralized Monitoring (AWS CloudWatch)
* Integrated the **CloudWatch Agent** to stream `/var/log/auth.log` to AWS Logs.
* *Benefit:* Provides a tamper-proof audit trail even if the server is deleted.
> <img width="1920" height="966" alt="Screenshot from 2026-09-03 11-42-47" src="https://github.com/user-attachments/assets/fb18327b-ecb2-4981-a549-af0032138d63" />



#### 5. Automated Maintenance
* Enabled **Unattended-Upgrades** for daily security patches.
* *Benefit:* The server protects itself from new exploits without manual work.

---

### 📊 Security Audit Results
The system was audited using **Lynis**, an industry-standard security auditing tool.

* **Final Hardening Index: 66 (High)**
* **Status:** Successfully passed 250+ security checks.
> <img width="1920" height="1019" alt="Screenshot from 2026-09-03 11-40-29" src="https://github.com/user-attachments/assets/b0ed37cc-c864-4a2d-a337-0fe4c4ac2ebc" />


---

### ✅ Final Validation
Successfully connected to the hardened server using the custom port and SSH keys.
> <img width="1211" height="734" alt="Screenshot from 2026-09-03 11-45-07" src="https://github.com/user-attachments/assets/17eb220a-3a13-42f6-9279-83bd5ad2a933" />


---

### 🧰 Tools & Technologies
* **Infrastructure:** AWS EC2, IAM, Security Groups.
* **Operating System:** Ubuntu 26.04.1 LTS.
* **Security Suite:** Fail2Ban, UFW, Unattended-Upgrades.
* **Auditing:** Lynis.
* **Monitoring:** AWS CloudWatch Logs.
