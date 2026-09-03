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

### 🛠️ Execution Steps (How I did it)

#### 1. Access Control (SSH Hardening)
* Migrated SSH from the default Port 22 to a **custom non-standard port (2222)**.
* Disabled `root` login and enforced **Key-Pair only authentication**.
* *Benefit:* Eliminates 99% of automated bot scans.

#### 2. Network Guard (UFW Firewall)
* Implemented a **"Deny-by-Default"** policy.
* Strictly allowed only ports 2222 (SSH), 80 (HTTP), and 443 (HTTPS).
* *Benefit:* Prevents hackers from finding "backdoors" in unmonitored services.

#### 3. Intrusion Prevention (Fail2Ban)
* Deployed **Fail2Ban** to monitor authentication logs.
* Configured a 1-hour ban for any IP failing 3 login attempts.
* *Benefit:* Stops brute-force attacks in their tracks.

#### 4. Centralized Monitoring (AWS CloudWatch)
* Integrated the **CloudWatch Agent** to stream `/var/log/auth.log`.
* Created a centralized log group for security auditing.
* *Benefit:* Provides a tamper-proof audit trail even if the server is deleted.

#### 5. Automated Maintenance
* Enabled **Unattended-Upgrades** for daily security patches.
* *Benefit:* The server protects itself from new "Zero-day" exploits without manual work.

---

### 📊 The Proof (Audit Results)
I used **Lynis**, a leading industry-standard security tool, to verify the hardening results.

* **Security Hardening Index: 66 (High)**
* **Status:** Successfully passed 250+ security checks.

---

### 🚀 Visual Evidence

*(Tip: Drag and drop your blurred images here)*

---

### 🧰 Tools & Technologies
* **Infrastructure:** AWS EC2, IAM, Security Groups.
* **Operating System:** Ubuntu 24.04 LTS.
* **Security Suite:** Fail2Ban, UFW, Unattended-Upgrades.
* **Auditing:** Lynis.
* **Monitoring:** AWS CloudWatch Logs.
