# via-internship-2026
Kwarteng Lennox
5230160010

### Evidence Summary
- recon: nmap scan showing open ports 21, 22, 139, 445 on 192.168.1.4
- exploit1: [vsftpd_234_backdoor on port 21 -> root]
- exploit2: [samba/usermap_script on port 139/445 -> root]
- exploit3: [vsftpd backdoor confirmed uid=0(root)]

All exploits executed successfully in isolated VirtualBox lab (Kali 192.168.1.5 -> Metasploitable 192.168.1.4). Mitigation: disable anonymous FTP, patch Samba, update vsftpd.
