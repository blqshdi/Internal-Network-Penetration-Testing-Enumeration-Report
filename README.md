# 🛡️ Internal Network Penetration Testing & Enumeration Report

## Target: 192.168.56.101 (Metasploitable 2)

---

# 📌 1. Executive Summary

This report documents the reconnaissance and enumeration phase performed against a vulnerable Linux system (Metasploitable 2) in a controlled lab environment.

The objective was to identify exposed services, gather system information, and evaluate potential security weaknesses using standard penetration testing tools.

The target was found to contain **multiple critical vulnerabilities**, including outdated services, insecure protocols, and misconfigured network services.

---

# 📌 2. Scope

| Item | Details |
|------|--------|
| Target IP | 192.168.56.101 |
| Environment | Virtual Internal Network |
| Attacker | Kali Linux |
| Target | Metasploitable 2 |
| Phase | Reconnaissance & Enumeration |

---

# 📌 3. Methodology

The following techniques were used:

- Host discovery (ICMP ping)
- Network scanning (Nmap)
- Service version detection
- OS fingerprinting
- SMB / NetBIOS enumeration
- RPC & NFS enumeration
- SMTP & FTP banner grabbing
- DNS analysis (external)
- SNMP probing

---

# 📌 4. Host Discovery

## Command:
```bash
ping 192.168.56.101
Result:
Host is alive
TTL ≈ 64 (Linux system)
Observation:

The TTL value confirms a Linux-based operating system.

📌 5. Port Scanning
Command:
nmap -F 192.168.56.101
Open Ports:
Port	Service
21	FTP
22	SSH
23	Telnet
25	SMTP
80	HTTP
139	SMB
445	SMB
2049	NFS
3306	MySQL
5900	VNC
📌 6. Service Version Detection
Command:
nmap -sV 192.168.56.101
Key Findings:
Service	Version	Risk
FTP	vsftpd 2.3.4	Backdoor vulnerability
Apache	2.2.8	Outdated
Samba	3.0.20	Null session allowed
MySQL	5.0	Weak configuration
UnrealIRCd	IRC daemon	RCE risk
📌 7. OS Detection
Command:
nmap -O 192.168.56.101
Result:
Linux Kernel 2.6.x
Metasploitable 2 confirmed
📌 8. NetBIOS Enumeration
Command:
nbtscan 192.168.56.101
Result:
Hostname: METASPLOITABLE
Workgroup: WORKGROUP
📌 9. SMB Enumeration
Command:
enum4linux -a 192.168.56.101
Findings:
👤 Users:
root
msfadmin
postgres
nobody
ftp
sshd
mysql
📂 Shares:
tmp
opt
print$
IPC$
⚠️ Security Issues:
Null session enabled
Anonymous enumeration allowed
📌 10. SMB OS Discovery
Command:
nmap --script smb-os-discovery -p445 192.168.56.101
Result:
OS: Unix (Samba 3.0.20-Debian)
Domain: WORKGROUP
📌 11. NFS Enumeration
Command:
showmount -e 192.168.56.101
Result:
/ *
Risk:

Entire filesystem exported to network (critical misconfiguration)

📌 12. RPC Enumeration
Command:
rpcinfo -p 192.168.56.101
Result:
mountd active
nlockmgr active
NFS services exposed
📌 13. FTP Enumeration
Command:
nc 192.168.56.101 21
Result:
220 (vsFTPd 2.3.4)
Risk:

Known vulnerable FTP version with backdoor vulnerability.

📌 14. SMTP Enumeration
Command:
nmap -p25 --script smtp-commands 192.168.56.101
Findings:
VRFY enabled
EXPN enabled
User enumeration possible
📌 15. SNMP Enumeration
Command:
snmpwalk -v2c -c public 192.168.56.101
Result:
No response received
Conclusion:

SNMP service is not active or filtered.

📌 16. DNS Enumeration (External)
Commands:
nslookup google.com
dig google.com ANY
dig google.com MX
Findings:
DNS Records:
A record: 172.217.27.14
AAAA record: IPv6 address available
MX record: smtp.google.com
NS records: Google name servers
TXT records: SPF and verification
CAA record: pki.goog
📊 17. Correlation Table
Source	Finding	Risk
SMB	User list exposed	Credential leakage
SMB	Writable share (tmp)	File manipulation
NFS	Root filesystem exported	Full access risk
FTP	vsftpd 2.3.4	Known backdoor
SMTP	VRFY enabled	User enumeration
RPC	Service mapping exposed	Recon information leak
⚠️ 18. Security Findings Summary
Multiple outdated services detected
Insecure protocols (Telnet, FTP, rsh)
Anonymous SMB access enabled
NFS misconfiguration exposes full filesystem
Weak service hardening across system
📌 19. Conclusion

The target system (Metasploitable 2) contains multiple critical vulnerabilities due to outdated software and insecure configurations.

The attack surface includes:

Remote code execution potential
Credential enumeration risks
Full filesystem exposure via NFS
Legacy service exploitation opportunities
🧰 20. Tools Used
Nmap
enum4linux
nbtscan
rpcinfo
netcat (nc)
snmpwalk
dig / nslookup
showmount
