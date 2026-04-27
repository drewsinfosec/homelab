Cowrie Lab – Troubleshooting & Recovery (Full Workflow)

Objective

  Restore Cowrie honeypot functionality after system reboot and verify attacker simulation + logging pipeline.

Environment Overview
  Cowrie VM (target): 192.168.0.104
  Kali attacker (ginger): 192.168.0.103
  SSH Port: 2222 (Cowrie default in config)

Log files:
  /home/cowrie/cowrie/var/log/cowrie.log
  /home/cowrie/cowrie/var/log/cowrie.json

Initial Problem
  SSH attempts returned:
  Permission denied
  Connection refused

Cowrie honeypot not accessible after reboot
  Step 1: Verify Configuration

  Checked Cowrie config file:

  cd ~/cowrie/etc
  cat cowrie.cfg

  Confirmed:

  listen_endpoints = tcp:2222
  auth_class = UserDB

Insight:

  Cowrie listens on port 2222
  Uses userdb.txt for authentication

Step 2: Investigate Authentication

  Checked available files:

  ls ~/cowrie/etc

  Found:

  userdb.example

  But missing:

  userdb.txt
  
  Root cause:

  No active credential database leading to login failures

Step 3: Create Authentication Database

  Copied template:

  cp userdb.example userdb.txt

  Verified contents:

  cat userdb.txt

  Key entries:

  root:x:!root
  root:x:!123456
  root:x:*

Insight:

  !password = blocked
  * = allow any other password
  🔍 Step 4: Diagnose Connection Refusal

Error:

  connection refused

Meaning:

  Nothing listening on port 2222

Step 5: Locate Cowrie Executable

  Explored project structure:

  cd ~/cowrie
  ls -la

Found virtual environment:

  cowrie-env/

Located executable:

  ls cowrie-env/bin

Found:

  cowrie
  twistd

Step 6: Fix Startup Error

Initial error:
FileNotFoundError: twistd

Cause:

Virtual environment not activated

Step 7: Activate Environment
  cd ~/cowrie
  source cowrie-env/bin/activate

Verified:
  which twistd

Output:
  /home/cowrie/cowrie/cowrie-env/bin/twistd

Step 8: Start Cowrie
  cowrie start

  Verified status:

  cowrie status
  
  Confirmed listening:

  ss -tulpn | grep 2222

Step 9: Reconnect from Attacker Machine

From Kali (ginger):

  ssh root@192.168.0.104 -p 2222

  Used password:

  letmein

  Successful login:

  root@svr04:~#

Step 10: Validate Honeypot Behavior

  Confirmed:

  Fake shell environment active
  Commands logged
  Session tracking working

Key Lessons Learned
  1. Services do not persist after reboot/ Cowrie must be manually restarted unless automated
  2. Virtual environments matter. Cowrie depends on Python environment (cowrie-env). Missing activation lead to runtime errors
3. Authentication is file-driven userdb.txt controls access. Missing file = no login possible
4. Port awareness is critical
  Cowrie uses 2222, not 22
  Wrong port = misleading errors
5. Error messages are clues
  connection refused → service not running
  permission denied → auth issue

Operational Workflow (Going Forward)
Start Cowrie
cd ~/cowrie
source cowrie-env/bin/activate
cowrie start
Check Status
cowrie status
Connect from Kali
ssh root@192.168.0.104 -p 2222
Analyze Logs
cd ~/cowrie/var/log/cowrie
cat cowrie.json | jq

Future Improvement

Create alias for faster startup:

nano ~/.bashrc

Add:

alias startcowrie='cd ~/cowrie && source cowrie-env/bin/activate && cowrie start'
