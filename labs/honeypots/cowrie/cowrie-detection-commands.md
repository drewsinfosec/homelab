Clean view of events
	cat cowrie.json | jq
Show ONLY login attempts (clean + readable)
	cat cowrie.json | jq 'select(.eventid | contains("login"))'
Show successful logins only
	cat cowrie.json | jq 'select(.eventid=="cowrie.login.success")'
Extract just usernames + passwords
	cat cowrie.json | jq 'select(.eventid=="cowrie.login.success") | {username, password}'
Show commands attackers ran
	cat cowrie.json | jq 'select(.eventid=="cowrie.command.input") | .input'
Grab a session ID (example: 53c087e09a9f) and run:
	cat cowrie.json | jq 'select(.session=="53c087e09a9f")'
Sorting by time when viewing sessions (example: 53c087e09a9f):
	cat cowrie.json | jq 'select(.session=="53c087e09a9f") | .timestamp, .eventid, .input?'

Attacker Activity

detects Successful Login
	cat cowrie.json | jq 'select(.eventid=="cowrie.login.success") | {timestamp, src_ip, username, password, session}'
detects brute-force activity beginning (failed login attempts).
	cat cowrie.json | jq 'select(.eventid=="cowrie.login.failed") | {timestamp, src_ip, username, password, session}'
detects post login behavior like commands entered after login.
	cat cowrie.json | jq 'select(.eventid=="cowrie.command.input") | {timestamp, src_ip, session, command: .input}'

Detection Rules

detects download commands - possible malware download attempts
	cat cowrie.json | jq 'select(.eventid=="cowrie.command.input" and (.input | test("wget|curl|tftp|ftp"; "i"))) | {timestamp, src_ip, session, command: .input}'
permission changes: an attacker trying to make a file executable or change ownership
	cat cowrie.json | jq 'select(.eventid=="cowrie.command.input" and (.input | test("chmod|chown"; "i"))) | {timestamp, src_ip, session, detection:"Permission Change", command: .input}'

system recon commands: an attacker trying to figure out where they landed
	cat cowrie.json | jq 'select(.eventid=="cowrie.command.input" and (.input | test("uname|whoami|id|pwd|ls|cat /etc/passwd|ip a|ifconfig|netstat|ps"; "i"))) | {timestamp, src_ip, session, command: .input}'
	
reverse shell indicators: possible attempts to call back to the attacker
	cat cowrie.json | jq 'select(.eventid=="cowrie.command.input" and (.input | test("bash -i|/dev/tcp|nc |netcat|python.*socket|perl.*socket"; "i"))) | {timestamp, src_ip, session, command: .input}'
	

	
	
	
	
