# 🛡️ Cybersecurity Home Lab

I am an aspiring cybersecurity professional building hands-on skills in offensive security and blue team detection. This repository documents my home lab exercises, from initial exploitation to network analysis and incident response.

## Table of Contents

- [Lab Environment](#lab-environment)
- [Lab Reports](#lab-reports)
- [Recent Updates](#recent-updates)
- [Skills Demonstrated](#skills-demonstrated)
- [Lessons Learned](#lessons-learned)

## Lab Environment

| Role | System | Details |
|------|--------|---------|
| Attacker | Kali Linux | Nmap, Metasploit, Wireshark |
| Victim | Metasploitable 2 | Intentionally vulnerable target |
| Victim | Metasploitable 3 | Intentionally vulnerable target |
| Network | VirtualBox Host-Only Adapter | `192.168.56.0/24` |

## Lab Reports

| Date | Lab | Topic | Report |
|------|-----|-------|--------|
| Oct 10, 2026 | Day 2 | Exploiting vsftpd 2.3.4 Backdoor | [View](./Day2_vsftpd_Exploit.md) |
| — | Day 3 | Network Analysis of the Attack | *In progress* |

> Day 1 covered initial lab setup (VirtualBox networking, Kali/Metasploitable install) and was not written up separately.

## Recent Updates

### SIEM Integration & Threat Hunting

**Endpoint Integration**
- Deployed and configured the Wazuh agent on the Metasploitable 3 (Ubuntu 14.04) target.
- Resolved network routing conflicts to establish a secure connection to the Wazuh SIEM manager.

**Threat Hunting & Analysis**
- Used the Wazuh dashboard to conduct live threat hunting during an active penetration test.
- Identified and correlated the ProFTPD `modcopy_exec` exploit logs (Rule ID `11201`), confirming the SIEM's ability to detect remote code execution attempts.

### Full System Compromise & Persistence (Metasploitable 3)

**Privilege Escalation**
- Identified the target kernel version (`3.13.0-24-generic`) and recognized its vulnerability to OverlayFS (CVE-2015-1328).
- Manually transferred the exploit source code (`37292.c`) to the target using a Meterpreter session.
- Compiled the exploit on the victim machine with `gcc` and executed it to escalate privileges from `www-data` to `root` (`uid=0`).

**Establishing Persistence (MITRE ATT&CK T1136)**
- Created a new local user account (`backdoor`) on the compromised host.
- Used the escalated `root` privileges to add the account to the `sudo` group, granting full administrative rights.
- Verified persistence by establishing a direct SSH connection from the Kali attacker machine using the new backdoor credentials.

## Skills Demonstrated

- Network scanning and enumeration (Nmap)
- Exploitation with Metasploit
- Privilege escalation and gaining root access via Meterpreter
- Packet capture and analysis (Wireshark)
- SIEM deployment and threat hunting (Wazuh)
- MITRE ATT&CK mapping (T1190, T1136)

## Lessons Learned

*To be updated as the lab progresses.*

---

> All activity in this repository was performed in an isolated, personal lab environment for educational purposes only.
