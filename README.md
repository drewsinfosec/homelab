# homelab
# Cybersecurity Homelab

Welcome to my cybersecurity homelab. This environment is built to practice real-world offensive and defensive security techniques. High school CS teacher working toward transitioning into cybersecurity.

# Lab Environment

- **Host:** Proxmox VE (Dell Precision 5820 - "Hank")
- **VMs:**
  - Kali Linux (Attacker)
  - Windows 11 (Target)
  - Debian 12 (Pi-hole DNS)
  - Debian 12 (cowrie-honeypot)

- **Network:**
  - Internal LAN: 192.168.0.0/24
  - Router: TP-Link AC1750
  - Switch: Netgear 24-port

# Goals

- Build hands-on cybersecurity skills
- Prepare for CompTIA Security+
- Simulate real-world attack scenarios
- Document learning for professional growth

# Setup Log

Initialized GitHub repo and connected from command machine.

# SSH Setup
Successfully configured SSH authentication with Github

![SSH Success](screenshots/ssh_auth_success.png)

# Labs

# Defensive / Networking
- [Network Failure Recovery](labs/network-failure-recovery/)
- [Zero Trust Lab](labs/zero-trust/)

# Offensive / Exploitation
- [Metasploitable2 Deployment](labs/metasploitable2-deployment/)
- [Netcat Reverse Shell](labs/netcat-reverse-shell/)
- [SMB Enumeration](labs/smb-enumeration/)
- [Grep Lab](labs/grep-lab/)
