# 🛡️ Metasploitable 2 – Network Reconnaissance & Enumeration Report

## 📌 1. Objective
This report documents the reconnaissance and enumeration phase performed against Metasploitable 2. The goal is to identify open ports, services, and potential vulnerabilities for exploitation.

---

## 📌 2. Target Information
- IP Address: 192.168.56.101
- Environment: VirtualBox / Internal Network
- OS (Expected): Linux (Metasploitable 2)

---

## 📌 3. Host Discovery

### Command:
```bash
ping 192.168.56.101
```

### Result:
- Host is alive
- TTL ≈ 64 → Linux system

---

## 📌 4. Port Scanning

### Command:
```bash
nmap -F 192.168.56.101
```

### Open Ports:
- 21 FTP
- 22 SSH
- 23 Telnet
- 25 SMTP
- 80 HTTP
- 139 SMB
- 445 SMB
- 2049 NFS
- 3306 MySQL
- 5900 VNC

---

## 📌 5. Service Version Detection

### Command:
```bash
nmap -sV 192.168.56.101
```

### Findings:
- FTP: vsftpd 2.3.4 (backdoor vulnerability)
- Apache: 2.2.8 (outdated)
- Samba: 3.0.20 (null session allowed)
- MySQL: 5.0 (weak configuration)
- UnrealIRCd: RCE risk

---

## 📌 6. OS Detection

### Command:
```bash
nmap -O 192.168.56.101
```

### Result:
- Linux Kernel 2.6.x
- Metasploitable 2 confirmed

---

## 📌 7. NetBIOS Enumeration

### Command:
```bash
nbtscan 192.168.56.101
```

### Result:
- Hostname: METASPLOITABLE
- Workgroup: WORKGROUP

---

## 📌 8. SMB Enumeration

### Command:
```bash
enum4linux -a 192.168.56.101
```

### Users Found:
- root
- msfadmin
- postgres
- nobody
- ftp
- sshd
- mysql

### Shares:
- tmp
- opt
- print$
- IPC$

### Issues:
- Null session enabled
- Anonymous access allowed

---

## 📌 9. SMB OS Discovery

### Command:
```bash
nmap --script smb-os-discovery -p445 192.168.56.101
```

### Result:
- OS: Unix (Samba 3.0.20-Debian)
- Domain: WORKGROUP

---

## 📌 10. NFS Enumeration

### Command:
```bash
showmount -e 192.168.56.101
```

### Result:
```
/ *
```

### Risk:
- Entire filesystem exported (critical misconfiguration)

---

## 📌 11. RPC Enumeration

### Command:
```bash
rpcinfo -p 192.168.56.101
```

### Result:
- mountd active
- nlockmgr active
- NFS exposed

---

## 📌 12. FTP Enumeration

### Command:
```bash
nc 192.168.56.101 21
```

### Result:
- 220 vsFTPd 2.3.4

### Risk:
- Known backdoor vulnerability

---

## 📌 13. SMTP Enumeration

### Command:
```bash
nmap -p25 --script smtp-commands 192.168.56.101
```

### Findings:
- VRFY enabled
- EXPN enabled
- User enumeration possible

---

## 📌 14. SNMP Enumeration

### Command:
```bash
snmpwalk -v2c -c public 192.168.56.101
```

### Result:
- No response (inactive/filtered)

---

## 📌 15. DNS Enumeration (External)

### Commands:
```bash
nslookup google.com
dig google.com ANY
dig google.com MX
```

### Findings:
- A: 172.217.27.14
- AAAA: IPv6 enabled
- MX: smtp.google.com
- NS: Google name servers
- TXT: SPF + verification records
- CAA: pki.goog

---

## 📊 16. Risk Correlation

| Source | Finding | Risk |
|--------|--------|------|
| SMB | User enumeration | High |
| SMB | Writable shares | High |
| NFS | Full filesystem export | Critical |
| FTP | vsFTPd 2.3.4 | Critical |
| SMTP | VRFY enabled | Medium |
| RPC | Service exposure | Medium |

---

## ⚠️ 17. Security Summary

- Multiple outdated services
- Insecure legacy protocols (FTP, Telnet)
- SMB anonymous access enabled
- Critical NFS misconfiguration
- User enumeration possible

---

## 🎯 18. Conclusion

Metasploitable 2 is highly vulnerable due to misconfigurations and outdated services. The system presents multiple attack vectors including remote exploitation, credential enumeration, and full filesystem access.

---

## 🧰 19. Tools Used
- Nmap
- enum4linux
- nbtscan
- rpcinfo
- netcat
- snmpwalk
- dig / nslookup
- showmount

---

## 👤 Author
- Pentester: Your Name
- Date: 2026
