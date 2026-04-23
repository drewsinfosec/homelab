# Metasploitable2 SSH Troubleshooting Lab

## Overview

This lab documents the process of troubleshooting an SSH connection failure to a Metasploitable2 virtual machine, identifying the root cause, and restoring access. The issue stemmed from both modern SSH client security restrictions and firewall rules on the target system.

__

## Lab Environment

* Attacker Machine: Kali Linux VM ("ginger")
* Target Machine: Metasploitable2 VM
* Network: Internal lab network (192.168.0.0/24)

---

## Objective

* Establish SSH access to Metasploitable2
* Identify why SSH connection fails
* Restore SSH connectivity
* Document the troubleshooting process

---

## Initial Problem

Attempting to connect via SSH:

```
ssh msfadmin@192.168.0.108
```

### Error Received

```
unable to negotiate with 192.168.0.108 port 22: no matching host key type found. Their offer: ssh-rsa,ssh-dss
```

---

## Root Cause Analysis

### Issue 1: Deprecated SSH Algorithms

* Modern Kali Linux disables weak algorithms by default
* Metasploitable2 only supports:

  * ssh-rsa
  * ssh-dss (deprecated)

Result: Connection refused due to cryptographic mismatch

### Issue 2: Firewall (iptables)

* SSH service was not accessible due to firewall rules
* Port 22 was not explicitly allowed

---

## Solution

### Step 1: Allow Legacy SSH Algorithms (Client-Side Fix)

```
ssh -o HostKeyAlgorithms=+ssh-rsa -o PubkeyAcceptedAlgorithms=+ssh-rsa msfadmin@192.168.0.108
```

---

### Step 2: Restore SSH Access via iptables (Server-Side Fix)

```
iptables -A INPUT -p tcp --dport 22 -j ACCEPT
```

---

## Verification

### Confirm Firewall Rule

```
iptables -L -n -v
```

### Successful SSH Login

```
ssh -o HostKeyAlgorithms=+ssh-rsa -o PubkeyAcceptedAlgorithms=+ssh-rsa msfadmin@192.168.0.108
```

### Confirm Access

```
whoami
id
```

---

## Key Takeaways

* Modern systems disable insecure cryptographic algorithms by default
* Legacy systems (like Metasploitable2) rely on outdated protocols
* Troubleshooting requires evaluating both:

  * Client-side configurations
  * Server-side access controls

---

## Security Implications

* Re-enabling deprecated algorithms introduces risk
* Acceptable in controlled lab environments only
* In production environments:

  * Upgrade SSH services
  * Disable legacy systems

---

## Screenshots to Include

* SSH error message
* iptables rules before and after modification
* Successful SSH connection
* Command outputs (whoami, id)

---

## Reflection

This lab reinforced the importance of:

* Understanding protocol compatibility
* Recognizing deprecated security standards
* Systematic troubleshooting
* Documenting each step for reproducibility

---

## Future Work

* Perform SSH brute-force attack using Hydra
* Conduct enumeration after SSH access
* Attempt privilege escalation
* Create repeatable snapshot-based lab workflow

---

## Author

Drew (drewsinfosec)
