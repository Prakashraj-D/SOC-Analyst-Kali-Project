Log Analysis & Threat Detection on Kali Linux project.

SOC Analyst Project: Log Analysis & Threat Detection using Kali Linux

Overview
This project simulates real-world SOC Analyst tasks on a **low-end Kali Linux system** without needing any VMs. It focuses on detecting suspicious activities, monitoring critical files, analyzing logs, and building basic threat detection rules using built-in Linux tools.

What I Learned
- Linux log monitoring (auth, sudo, bash activity)
- File change detection using `auditd`
- Creating custom audit rules
- Extracting and analyzing logs with `grep`, `ausearch`, `awk`, etc.
- Automating and generating reports using `logwatch` and `cron`
- Building a lightweight SOC workflow on Kali

---

Tools Used
| Tool         | Purpose                                  |
|--------------|------------------------------------------|
|  auditd      | Tracks system/file-level activities      |
|  rsyslog     | Collects system logs in  /var/log/       |
|  logwatch    | Summarizes logs into daily reports       |
|  grep , awk ,  cut  | Parses logs and filters alerts    |
|  crontab     | Automates tasks/reports                  |
|  mailutils   | (Optional) Sends reports via email       |

---

Project Breakdown (Phase-wise)

Phase 1: Initial Setup
- Installed tools:  
  bash
  sudo apt update && sudo apt install auditd logwatch mailutils -y
  
- Enabled and started `auditd`:
  bash
  sudo systemctl enable auditd
  sudo systemctl start auditd
  
---

Phase 2: File Monitoring with `auditd`
- Monitored `/etc/passwd`:
  bash
  sudo auditctl -w /etc/passwd -p wa -k passwd_monitor
  
- Simulated an edit:
  bash
  sudo nano /etc/passwd
  
- Extracted logs:
  bash
  sudo ausearch -k passwd_monitor > passwd_audit_report.txt
  
---

Phase 3: Authentication Log Analysis
- Analyzed brute force attempts & sudo logs:
  bash
  sudo journalctl | grep -Ei 'failed|sudo|authentication' > auth_logs_report.txt
  
---

Phase 4: Custom Rule Creation (e.g., Netcat Detection)
- Example: Detect use of `nc` (Netcat):
  bash
  sudo auditctl -a always,exit -F arch=b64 -S execve -F exe=/usr/bin/nc -k netcat_detect
  
---

Phase 5: Daily Report Automation
- Ran Logwatch manually:
  bash
  sudo logwatch --detail high --service all --range today --format text > logwatch_report.txt
  
- Created cron job (optional):
  bash
  crontab -e
  Added line for daily report:
  0 6 * * * /usr/sbin/logwatch --output mail --mailto you@example.com --format html
  
---

Reports Included
- auth_logs_report.txt
- daily_secuirity_report.txt
- detect_netcat_report.txt
- detect_nmap_report.txt
- passwd_audit_report.txt

---

Final Output
- Successfully monitored system activity
- Built real-world alerting and detection capability
- Gained hands-on SOC experience using **only Kali Linux**

---

🌐 Connect with Me
Name: Prakash Raj D  
GitHub: https://github.com/Prakashraj-D  
LinkedIn: https://www.linkedin.com/in/prakashrajd-securityengineer

SOC Analyst Project: Full Command Reference


---

Phase 1: Set Up Logging & Tools

sudo apt update
sudo apt install auditd logwatch mailutils -y
sudo systemctl enable auditd
sudo systemctl start auditd


---

Phase 2: Monitor Critical File /etc/passwd

Audit Rule (Basic):

sudo auditctl -w /etc/passwd -p wa -k passwd_monitor

Audit Rule (Advanced):

sudo auditctl -a always,exit -F arch=b64 -F path=/etc/passwd -F perm=wa -F auid>=1000 -F auid!=4294967295 -k passwd_monitor

Trigger the rule:

sudo nano /etc/passwd

View the log:

sudo ausearch -k passwd_monitor

Save the report:

sudo ausearch -k passwd_monitor > passwd_audit_report.txt

Add header & timestamp:

echo "==== Phase 2: /etc/passwd Audit Report ====" | cat - passwd_audit_report.txt > temp && mv temp passwd_audit_report.txt
echo "Report Generated on: $(date)" | cat - passwd_audit_report.txt > temp && mv temp passwd_audit_report.txt


---

Phase 3: Analyze Authentication Logs

sudo journalctl _COMM=sshd | grep "Failed password"
sudo journalctl _COMM=sshd | grep "Accepted password"

Save auth logs report:

sudo journalctl _COMM=sshd > auth_logs_report.txt

Add header & timestamp:

echo "==== Phase 3: Authentication Log Analysis Report ====" | cat - auth_logs_report.txt > temp && mv temp auth_logs_report.txt
echo "Report Generated on: $(date)" | cat - auth_logs_report.txt > temp && mv temp auth_logs_report.txt


---

Phase 4: Custom Rule Creation + Threat Detection

1. Detect Modification of /etc/shadow

sudo auditctl -a always,exit -F arch=b64 -F path=/etc/shadow -F perm=wa -F auid>=1000 -F auid!=4294967295 -k shadow_monitor

View logs:

sudo ausearch -k shadow_monitor

2. Detect Execution of Netcat (nc)

sudo auditctl -a always,exit -F arch=b64 -F path=/bin/nc -F perm=x -F auid>=1000 -F auid!=4294967295 -k detect_nc

View logs:

sudo ausearch -k detect_nc


---

Phase 5: Automated Reporting with Logwatch

View Sample Logwatch Report:

sudo logwatch --detail High --service All --range today --format text

Save the report:

sudo logwatch --detail High --service All --range today --format text > logwatch_today_report.txt

Add header & timestamp:

echo "==== Phase 5: Daily Logwatch Report ====" | cat - logwatch_today_report.txt > temp && mv temp logwatch_today_report.txt
echo "Report Generated on: $(date)" | cat - logwatch_today_report.txt > temp && mv temp logwatch_today_report.txt
