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
smbclient -L //192.168.0.184 -U scout

🔐 Access
sudo mount -t cifs //192.168.0.184/*****Share /mnt \
-o username=*****,password=*******

✅ Results
Successfully listed shares
Mounted SMB share
Verified file access
🧠 What I Learned
SMB enumeration process
Importance of credentials
How misconfigured shares can expose data

---

# 🖼️ Step 4: Add screenshots (THIS is huge)

Take screenshots of:
- nmap scan  
- smbclient output  
- mounted directory  

Put them in `/images` and reference them:

```markdown
![SMB Scan](../images/smb-scan.png)

👉 Screenshots = proof you actually did it

🚀 Step 5: Push it live

In your repo:

git add .
git commit -m "Initial homelab setup with SMB lab"
git push

