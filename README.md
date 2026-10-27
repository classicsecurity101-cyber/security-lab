# Cybersecurity Home Lab 

##  Introduction
I am an aspiring cybersecurity professional building hands-on skills in 
offensive security and blue team detection. This repository documents my 
home lab exercises, from initial exploitation to network analysis and 
incident response.

### 🛡️ Recent Lab Updates: SIEM Integration & Threat Hunting

**Endpoint Integration:**
* Successfully deployed and configured the Wazuh agent on the Metasploitable 3 (Ubuntu 14.04) target.
* Resolved network routing conflicts to establish a secure connection to the Wazuh SIEM manager.

**Threat Hunting & Analysis:**
* Utilized the Wazuh dashboard to conduct live threat hunting during an active penetration test.
* Successfully identified and correlated the ProFTPD `modcopy_exec` exploit logs (Rule ID `11201`), confirming the SIEM's ability to detect remote code execution attempts.

### 🚩 Full System Compromise & Persistence (Metasploitable 3)

**Privilege Escalation:**
*   Identified the target kernel version (`3.13.0-24-generic`) and recognized its vulnerability to OverlayFS (CVE-2015-1328).
*   Manually transferred the exploit source code (`37292.c`) to the target using a Meterpreter session.
*   Compiled the exploit on the victim machine using `gcc` and successfully executed it to escalate privileges from `www-data` to `root` (`uid=0`).

**Establishing Persistence (MITRE ATT&CK T1136):**
*   Maintained access to the compromised host by creating a new local user account (`backdoor`).
*   Utilized the escalated `root` privileges to add the new account to the `sudo` group, granting it full administrative rights.
*   Verified persistence by establishing a direct SSH connection from the Kali attacker machine using the new backdoor credentials.



##  Lab Environment
- **Attacker:** Kali Linux
- **Victim:** Metasploitable 2,Metasploitable 3
- **Network:** VirtualBox Host-Only Adapter (192.168.56.0/24)
- **Tools Used:** Nmap, Metasploit, Wireshark

##  Lab Reports
| Date | Lab | Topic | Report |
|------|-----|-------|--------|
| Oct 10, 2026 | Day 2 | Exploiting vsftpd 2.3.4 Backdoor | [View](./Day2_vsftpd_Exploit.md) |
| Oct 11, 2026 | Day 3 | Network Analysis of the Attack | Coming soon |

## Skills Demonstrated
- Network scanning and enumeration (Nmap)
- Exploitation with Metasploit
- Gaining root access via Meterpreter
- Packet capture and analysis (Wireshark)
- MITRE ATT&CK mapping (T1190)

##  Lessons Learned
*(To be updated as I progress)*
