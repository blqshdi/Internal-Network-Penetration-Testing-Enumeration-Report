🛡️ Internal Network Penetration Testing & Enumeration Report
Target: 192.168.56.101 (Metasploitable 2)
📌 1. Executive Summary

This assessment was conducted against a deliberately vulnerable Linux system (Metasploitable 2) within a controlled virtual lab environment. The objective was to perform active reconnaissance and enumeration to identify exposed services, misconfigurations, and potential attack surfaces.

The target was found to expose multiple insecure services, weak authentication mechanisms, and legacy protocols, making it highly vulnerable to exploitation.

📌 2. Scope
Item	Details
Target IP	192.168.56.101
Environment	Internal Virtual Network (Host-only)
Attacker	Kali Linux
Target	Metasploitable 2
Phase	Reconnaissance & Enumeration
📌 3. Methodology

The following structured approach was used:

Host discovery
Port scanning
Service enumeration
OS fingerprinting
SMB/NetBIOS enumeration
RPC & NFS enumeration
DNS analysis (external)
SMTP/FTP banner grabbing
SNMP probing
📌 4. Network Discovery
🔹 Ping Sweep
Command:
ping 192.168.56.101
Result:
Host is alive
TTL ≈ 64 → Linux system
Interpretation:

TTL value confirms Linux-based operating system, consistent with Metasploitable 2.

📌 5. Port Scanning
🔹 Fast Scan
Command:
nmap -F 192.168.56.101
Result Summary:
Port	Service
21	FTP
22	SSH
23	Telnet
25	SMTP
53	DNS
80	HTTP
139	SMB
445	SMB
2049	NFS
3306	MySQL
5900	VNC
6000	X11
🔹 Service Version Detection
Command:
nmap -sV 192.168.56.101
Key Findings:
Service	Version	Risk
FTP	vsftpd 2.3.4	Backdoored version (known exploit)
Apache	2.2.8	Outdated
Samba	3.0.20	Null session vulnerable
MySQL	5.0	Weak authentication
UnrealIRCd	Backdoored IRC	Remote code execution risk
📌 6. OS Fingerprinting
Command:
nmap -O 192.168.56.101
Result:
Linux Kernel 2.6.x
Metasploitable 2 confirmed
Interpretation:

Old kernel version indicates lack of modern security patches.

📌 7. SMB / NetBIOS Enumeration
🔹 NetBIOS Scan
Command:
nbtscan 192.168.56.101
Result:
Hostname: METASPLOITABLE
Workgroup: WORKGROUP
🔹 SMB Enumeration
Command:
enum4linux -a 192.168.56.101
Key Findings:
👤 Users Extracted:
root
msfadmin
postgres
ftp
sshd
nobody
mysql
📂 Shares:
tmp (writable)
opt
print$
IPC$
⚠️ Security Issues:
Null session allowed
Anonymous enumeration enabled
No password policy enforcement
🔹 SMB OS Discovery
Command:
nmap --script smb-os-discovery -p445 192.168.56.101
Result:
Unix system (Samba 3.0.20-Debian)
Domain: WORKGROUP
📌 8. NFS Enumeration
Command:
showmount -e 192.168.56.101
Result:
/ *
Interpretation:
Entire filesystem exported
Critical misconfiguration
Allows potential full system access if exploited
📌 9. RPC Enumeration
Command:
rpcinfo -p 192.168.56.101
Findings:
mountd active
nlockmgr active
nfs services exposed
Risk:

Exposes internal system service map to attackers.

📌 10. FTP Enumeration
Command:
nc 192.168.56.101 21
Result:
220 (vsFTPd 2.3.4)
Security Note:
Version has known backdoor vulnerability (CVE-2011-2523)
📌 11. SMTP Enumeration
Command:
nmap -p25 --script smtp-commands 192.168.56.101
Findings:
VRFY enabled
EXPN enabled
User enumeration possible
📌 12. SNMP Enumeration
Command:
snmpwalk -v2c -c public 192.168.56.101
Result:
No response
Interpretation:
SNMP service not active or filtered
📌 13. DNS Enumeration (External)
🔹 nslookup
nslookup google.com
IPv4 + IPv6 resolved via DNS server 10.0.2.3
🔹 dig ANY
dig google.com ANY
Records Found:
A / AAAA
MX (smtp.google.com)
NS (Google DNS servers)
TXT (SPF, verification records)
CAA (certificate authority policy)
🔹 MX Record
dig google.com MX
Mail server: smtp.google.com
📌 14. Key Vulnerabilities Identified
🔴 Critical:
vsftpd 2.3.4 backdoor vulnerability
Full NFS filesystem export
Telnet enabled (unencrypted login)
rsh services active (insecure remote shell)
🟠 High:
SMB null session enabled
Anonymous user enumeration
Outdated Apache and Samba
🟡 Medium:
SMTP user enumeration enabled
Weak service hardening
📌 15. Correlation Table
Source	Data	Risk
SMB	Users list	Credential exposure
SMB	Shares (tmp writable)	File manipulation
NFS	/ export	Full system access risk
FTP	vsftpd 2.3.4	Backdoor vulnerability
SMTP	VRFY enabled	User enumeration
RPC	Service map	Internal recon exposure
📌 16. Conclusion

The target system is highly vulnerable due to multiple outdated services and insecure configurations. The combination of SMB exposure, NFS misconfiguration, and legacy services such as Telnet and FTP creates a significant attack surface.

If deployed in a real-world environment, the system would be susceptible to:

Remote code execution
Credential harvesting
Full filesystem access
Service enumeration attacks
📌 17. Tools Used
Nmap
enum4linux
nbtscan
rpcinfo
netcat
snmpwalk
dig / nslookup
showmount
