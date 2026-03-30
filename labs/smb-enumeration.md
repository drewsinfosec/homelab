# SMB Enumeration Lab

## 🎯 Objective
Enumerate SMB shares and attempt access using valid credentials.

## 🖥️ Target
- IP: 192.168.0.184
- OS: Windows 11

## ⚙️ Tools Used
- nmap
- smbclient
- mount (CIFS)

## 🔍 Enumeration

```bash
nmap -p 445 192.168.0.184
