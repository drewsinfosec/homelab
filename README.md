# homelab
# 🖥️ Drew's Cybersecurity Homelab

Welcome to my cybersecurity homelab. This environment is built to practice real-world offensive and defensive security techniques.

## 🔧 Lab Environment

- **Host:** Proxmox VE (Dell Precision 5820 - "Hank")
- **VMs:**
  - Kali Linux (Attacker)
  - Windows 11 (Target)
  - Debian (Pi-hole DNS)

- **Network:**
  - Internal LAN: 192.168.0.0/24
  - Router: TP-Link AC1750
  - Switch: Netgear 24-port

## 🧪 Labs Completed

### 1. SMB Enumeration & Access
- Used `nmap` to identify open ports (445)
- Enumerated shares using `smbclient`
- Mounted SMB share to Kali
- Tested write permissions

📄 [View Lab](./labs/smb-enumeration.md)

---

## 🎯 Goals

- Build hands-on cybersecurity skills
- Prepare for CompTIA Security+
- Simulate real-world attack scenarios
- Document learning for professional growth
