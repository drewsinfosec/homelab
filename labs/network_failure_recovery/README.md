# Network Failure Recovery Lab (Proxmox + Pi-hole)

## Overview
This lab documents a real-world network failure in a Proxmox homelab environment and the step-by-step troubleshooting process used to restore connectivity.

The issue occurred after a hardware upgrade (RAM installation), which resulted in loss of network access across all virtual machines.

---

## Environment

* Host: Proxmox (Hank)
* Virtual Machines:

  * Kali Linux 
  * Windows 11
  * Pi-hole (Debian)
* Network:

  * Bridge: vmbr0
  * Physical NIC: nic0
  * Pi-hole IP: 192.168.0.193

---

## Initial Problem

After rebooting the server:

* All VMs had no internet access
* Pi-hole showed:

  * `NO-CARRIER`
  * `state DOWN`
  * No valid IP address (127.0.0.1 only)

---

## Root Cause

The outage was caused by multiple issues across different layers:

1. Proxmox bridge not properly connected to the physical NIC
2. Pi-hole VM network interface not correctly attached
3. Interface inside the VM was not activated (`state DOWN`)
4. No DHCP client available for automatic IP assignment
5. Interface not configured to activate on boot

---

## Troubleshooting Process

### 1. Verified Proxmox Bridge

bash~
brctl show
```

Confirmed bridge was connected:

```
vmbr0 → nic0
```

---

### 2. Reloaded Network Configuration

```bash
ifreload -a
```

---

### 3. Verified VM Connection

Confirmed VM interface was attached to bridge:

```
tap102i0
```

---

### 4. Checked Interface Inside VM

```bash
ip a
```

Observed:

* `state DOWN`
* `NO-CARRIER`

---

### 5. Brought Interface Up

```bash
ip link set ens18 up
```

---

### 6. Assigned Static IP Manually

```bash
ip addr add 192.168.0.193/24 dev ens18
ip route add default via 192.168.0.1
```

---

### 7. Verified Connectivity

```bash
ping -c 3 192.168.0.1
```

---

### 8. Fixed Persistent Network Configuration

Edited:

```bash
nano /etc/network/interfaces
```

Final configuration:

```bash
auto lo
iface lo inet loopback

auto ens18
allow-hotplug ens18
iface ens18 inet static
    address 192.168.0.193/24
    gateway 192.168.0.1
```

---

### 9. Rebooted System

```bash
reboot
```

---

## Final Result

* Pi-hole successfully received a static IP
* Network configuration persisted after reboot
* All VMs regained internet connectivity
* Proxmox bridge functioning correctly

---

## Key Takeaways

* Network issues can exist across multiple layers:

  * Host (Proxmox)
  * Virtual machine connection
  * Operating system configuration

* `NO-CARRIER` indicates a Layer 2 connectivity issue

* Minimal Debian installs may:

  * Not include DHCP client
  * Not automatically activate interfaces

* `allow-hotplug` is required in some environments to ensure interfaces activate at boot

---

## Skills Demonstrated

* Virtual networking (Proxmox bridges)
* Linux network troubleshooting
* Layered debugging methodology
* Manual IP configuration
* Root cause analysis

---

## Screenshots

Add screenshots here:

```markdown
![NO CARRIER](screenshots/no_carrier.png)
![Bridge Config](screenshots/bridge_issue.png)
![Fixed IP](screenshots/fixed_ip.png)
```

---<img width="800" height="601" alt="Screenshot_2026-04-03_11-23-42" src="https://github.com/user-attachments/assets/5c5c9d9c-1256-4b43-8ece-43fb1ee9bc67" />
<img width="869" height="368" alt="Screenshot_2026-04-03_11-19-11" src="https://github.com/user-attachments/assets/8b5ac52f-2f0a-44da-b3e0-c58cf7a9f277" />
<img width="923" height="315" alt="Screenshot_2026-04-03_08-50-55" src="https://github.com/user-attachments/assets/a879eb97-c70a-427c-ba1c-62bb3288144b" />
<img width="827" height="613" alt="Screenshot_2026-04-03_08-30-35" src="https://github.com/user-attachments/assets/a42a873f-6ee8-4975-8685-0d9a3c606e00" />


## Reflection

This lab reinforced the importance of troubleshooting in layers and validating each component of the network stack independently.

Rather than reinstalling systems, identifying and fixing root causes leads to deeper understanding and more reliable systems.
