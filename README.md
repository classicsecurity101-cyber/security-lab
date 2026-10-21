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

**Next Steps:**
* [ ] Authoring custom Wazuh XML detection rules to alert on malicious file uploads (`STOR` commands).
* [ ] Exploring local privilege escalation via the OverlayFS vulnerability (CVE-2015-1328) to obtain `root` access.

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
