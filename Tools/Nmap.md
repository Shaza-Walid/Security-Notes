# 🔍 Nmap — Network Mapper — Complete Study Guide

> **Based on the Coder-Web YouTube Series (9 Videos)**
> A full course on Nmap from zero to advanced — covering network scanning, port discovery, OS detection, NSE scripting, firewall evasion, and output formats.

---

## 📚 Table of Contents

1. [What is Nmap?](#1-what-is-nmap)
2. [Installation](#2-installation)
3. [How Nmap Works — Core Concepts](#3-how-nmap-works--core-concepts)
4. [Target Specification](#4-target-specification)
5. [Host Discovery (Ping Scanning)](#5-host-discovery-ping-scanning)
6. [Port Scanning Techniques](#6-port-scanning-techniques)
7. [Port States](#7-port-states)
8. [Service & Version Detection](#8-service--version-detection)
9. [OS Detection](#9-os-detection)
10. [Timing & Performance](#10-timing--performance)
11. [Nmap Scripting Engine (NSE)](#11-nmap-scripting-engine-nse)
12. [Firewall & IDS Evasion](#12-firewall--ids-evasion)
13. [Output Formats](#13-output-formats)
14. [Useful Scan Combinations](#14-useful-scan-combinations)
15. [Nmap Cheat Sheet](#15-nmap-cheat-sheet)
16. [Legal & Ethical Use](#16-legal--ethical-use)

---

## 1. What is Nmap?

**Nmap** (Network Mapper) is a **free, open-source** tool used for:

- **Network Discovery** — find active hosts on a network
- **Port Scanning** — identify open, closed, or filtered ports
- **Service Detection** — determine what software/version is running on open ports
- **OS Fingerprinting** — detect the operating system of a target
- **Vulnerability Scanning** — use scripts to check for known vulnerabilities
- **Security Auditing** — used by penetration testers, sysadmins, and ethical hackers

> Nmap was created by **Gordon Lyon (Fyodor)** and released in 1997. It is the industry-standard tool for the **Reconnaissance** phase of penetration testing.

### Why Learn Nmap?
- It's the **first tool** used in almost every pentest or CTF
- Required knowledge for **CEH, OSCP, CompTIA PenTest+** certifications
- Works on **Linux, Windows, macOS**
- Used in famous movies (The Matrix Reloaded, Elysium, G.I. Joe)

---

## 2. Installation

### Kali Linux (pre-installed)
```bash
nmap --version
```

### Ubuntu / Debian
```bash
sudo apt update
sudo apt install nmap -y
```

### CentOS / RedHat / Fedora
```bash
sudo dnf install nmap -y
# or
sudo yum install nmap -y
```

### Windows
Download the installer from: https://nmap.org/download.html
*(Includes Zenmap GUI and Npcap driver)*

### macOS
```bash
brew install nmap
```

### Verify Installation
```bash
nmap --version
# Expected: Nmap version 7.9x ( https://nmap.org )
```

---

## 3. How Nmap Works — Core Concepts

### The Scanning Process
Nmap works by sending **raw IP packets** to target hosts and analyzing their responses.

```
[Your Machine]  ──→  Send Packets  ──→  [Target]
[Your Machine]  ←──  Analyze Response  ←──  [Target]
```

### TCP Three-Way Handshake (Background Knowledge)
Understanding TCP is essential for understanding Nmap scan types:

```
Client  →  SYN        →  Server   (I want to connect)
Client  ←  SYN/ACK    ←  Server   (OK, I acknowledge)
Client  →  ACK        →  Server   (Connection established)
```

Nmap exploits this handshake to determine port states.

### Port Numbers
| Range | Type | Examples |
|-------|------|---------|
| 0 – 1023 | Well-Known Ports | 22 (SSH), 80 (HTTP), 443 (HTTPS) |
| 1024 – 49151 | Registered Ports | 3306 (MySQL), 8080 (HTTP-Alt) |
| 49152 – 65535 | Dynamic/Private Ports | Ephemeral connections |

**Total ports per protocol: 65,535 (TCP) + 65,535 (UDP) = 131,070**

---

## 4. Target Specification

Nmap can scan single IPs, ranges, subnets, and hostnames.

```bash
# Single IP
nmap 192.168.1.1

# Hostname
nmap scanme.nmap.org

# IP Range
nmap 192.168.1.1-50

# Entire Subnet (CIDR notation)
nmap 192.168.1.0/24

# Multiple targets
nmap 192.168.1.1 192.168.1.5 192.168.1.10

# From a file (one target per line)
nmap -iL targets.txt

# Exclude specific hosts
nmap 192.168.1.0/24 --exclude 192.168.1.1

# Exclude from file
nmap 192.168.1.0/24 --excludefile exclude.txt

# List targets WITHOUT scanning (dry run)
nmap -sL 192.168.1.0/24
```

---

## 5. Host Discovery (Ping Scanning)

Host discovery determines **which hosts are alive** before doing deep scanning. This saves time on large networks.

### Default Behavior
- **On local network**: Nmap uses **ARP** requests
- **On remote network**: Nmap uses **ICMP echo + TCP SYN**

### Host Discovery Methods

```bash
# Ping sweep — find live hosts, no port scan
nmap -sn 192.168.1.0/24

# ARP discovery (most reliable on local LAN — requires root)
sudo nmap -PR -sn 192.168.1.0/24

# ICMP Echo (standard ping)
nmap -PE 192.168.1.0/24

# ICMP Timestamp
nmap -PP 192.168.1.0/24

# ICMP Address Mask
nmap -PM 192.168.1.0/24

# TCP SYN ping on specific port
nmap -PS22 192.168.1.0/24      # SYN to port 22
nmap -PS22,80,443 192.168.1.0/24   # SYN to multiple ports

# TCP ACK ping
nmap -PA80 192.168.1.0/24

# UDP ping
nmap -PU53 192.168.1.0/24      # UDP to DNS port

# Skip host discovery entirely (treat all hosts as up)
nmap -Pn 192.168.1.1
```

> **`-Pn`** is very important — use it when the target **blocks ICMP (ping)** but has open ports. Without it, Nmap may think the host is down and skip it.

---

## 6. Port Scanning Techniques

### SYN Scan (Stealth Scan) — `-sS` ⭐ Most Used

```bash
sudo nmap -sS 192.168.1.1
```

- **How it works**: Sends SYN → waits for SYN/ACK → immediately sends RST (never completes the handshake)
- **Advantage**: Fast, stealthy, doesn't create full TCP connections (harder to log)
- **Requires**: Root / Administrator privileges
- **Also called**: "Half-open scan" or "Stealth scan"

```
Nmap → SYN → Target
Nmap ← SYN/ACK ← Target   (port is OPEN)
Nmap → RST → Target        (abruptly reset, no full connection)
```

### TCP Connect Scan — `-sT`

```bash
nmap -sT 192.168.1.1
```

- **How it works**: Completes the full TCP 3-way handshake
- **Advantage**: Does NOT require root/admin privileges
- **Disadvantage**: Slower, leaves logs on the target system
- **Use when**: You don't have root access

### UDP Scan — `-sU`

```bash
sudo nmap -sU 192.168.1.1
sudo nmap -sU -p 53,67,68,69,123,161 192.168.1.1
```

- **How it works**: Sends UDP packets and waits for responses
- **Advantage**: Scans UDP services (DNS, SNMP, DHCP, TFTP, NTP)
- **Disadvantage**: Very **slow** — UDP doesn't guarantee responses
- **Tip**: Combine with `-sS` → `nmap -sS -sU 192.168.1.1`

### NULL Scan — `-sN`

```bash
sudo nmap -sN 192.168.1.1
```

- Sends a TCP packet with **no flags set**
- Open/filtered ports = no response
- Closed ports = RST response
- Can bypass some firewalls/IDS

### FIN Scan — `-sF`

```bash
sudo nmap -sF 192.168.1.1
```

- Sends a packet with only the **FIN flag** set
- Similar behavior to NULL scan
- May bypass stateless packet filters

### XMAS Scan — `-sX`

```bash
sudo nmap -sX 192.168.1.1
```

- Sends a packet with **FIN, PSH, URG** flags set (all "lit up" like a Christmas tree 🎄)
- Same behavior as NULL and FIN scans
- Works only on RFC-compliant TCP stacks (does NOT work on Windows)

### ACK Scan — `-sA`

```bash
sudo nmap -sA 192.168.1.1
```

- Used **NOT for finding open ports**, but to **map firewall rules**
- Determines if ports are **filtered** (firewall blocks) vs **unfiltered** (firewall allows)

### Window Scan — `-sW`

```bash
sudo nmap -sW 192.168.1.1
```

- Similar to ACK scan
- Exploits a TCP window size quirk in some systems to differentiate open/closed ports

### Idle Scan (Zombie Scan) — `-sI`

```bash
sudo nmap -sI zombie_ip 192.168.1.1
```

- The most **stealthy** scan — your IP is never sent to the target
- Uses a "zombie" (idle host) as an intermediary
- Advanced technique for complete anonymity

### Specifying Ports

```bash
# Scan specific ports
nmap -p 22,80,443 192.168.1.1

# Scan a port range
nmap -p 1-1000 192.168.1.1

# Scan ALL 65535 ports
nmap -p- 192.168.1.1

# Scan top N most common ports
nmap --top-ports 100 192.168.1.1
nmap --top-ports 1000 192.168.1.1

# Fast scan (top 100 ports)
nmap -F 192.168.1.1
```

---

## 7. Port States

Nmap classifies ports into 6 states:

| State | Meaning |
|-------|---------|
| **open** | An application is actively accepting connections on this port |
| **closed** | No application is listening, but the port is accessible (responds with RST) |
| **filtered** | A firewall/filter is blocking the port — Nmap can't determine if open or closed |
| **unfiltered** | Port is accessible but Nmap can't determine open/closed (only in ACK scan) |
| **open\|filtered** | Nmap can't tell if open or filtered (no response received) |
| **closed\|filtered** | Nmap can't tell if closed or filtered |

---

## 8. Service & Version Detection

After finding open ports, Nmap can probe them to find what **software and version** is running.

```bash
# Basic version detection
nmap -sV 192.168.1.1

# Aggressive version detection (more probes, slower)
nmap -sV --version-intensity 9 192.168.1.1

# Light version detection (faster, less accurate)
nmap -sV --version-intensity 0 192.168.1.1

# Version + OS + Scripts + Traceroute (Aggressive All-in-One)
nmap -A 192.168.1.1
```

### Example Output with `-sV`
```
PORT    STATE  SERVICE  VERSION
22/tcp  open   ssh      OpenSSH 8.2p1 Ubuntu
80/tcp  open   http     Apache httpd 2.4.41
443/tcp open   ssl/http nginx 1.18.0
3306/tcp open  mysql    MySQL 8.0.28
```

> Knowing the **version** lets you search for **CVEs (known vulnerabilities)** for that specific version on sites like `cve.mitre.org` or `exploit-db.com`.

---

## 9. OS Detection

Nmap analyzes TCP/IP packet responses (TTL values, window sizes, etc.) to fingerprint the operating system.

```bash
# OS detection (requires root)
sudo nmap -O 192.168.1.1

# OS detection + version detection + scripts + traceroute
sudo nmap -A 192.168.1.1

# Aggressively guess OS even when uncertain
sudo nmap -O --osscan-guess 192.168.1.1
```

### Example Output
```
OS details: Linux 4.15 - 5.6
Network Distance: 1 hop
```

### How OS Detection Works
Nmap sends a series of TCP, UDP, and ICMP probes and compares the responses against a database of **~5,000 OS fingerprints**. Key indicators include:
- **TTL (Time to Live)** values: Linux ≈ 64, Windows ≈ 128, Cisco ≈ 255
- **TCP Window Size**
- **Don't Fragment (DF) bit**
- **TCP options ordering**

---

## 10. Timing & Performance

Nmap has **6 timing templates** (T0–T5) to control scan speed:

| Template | Name | Speed | Use Case |
|----------|------|-------|---------|
| `-T0` | Paranoid | Extremely slow | Bypass IDS, serial scans |
| `-T1` | Sneaky | Very slow | Evade IDS |
| `-T2` | Polite | Slow | Reduce bandwidth/impact |
| `-T3` | Normal | Default | Standard scanning |
| `-T4` | Aggressive | Fast | Fast networks (LAN/CTF) |
| `-T5` | Insane | Very fast | May miss results, sacrifice accuracy |

```bash
# Fast scan (recommended for CTFs and labs)
nmap -T4 192.168.1.1

# Maximum speed (may miss ports)
nmap -T5 192.168.1.1

# Very stealthy (1 probe per 5 minutes)
nmap -T0 192.168.1.1
```

### Additional Performance Options

```bash
# Minimum packets per second
nmap --min-rate 1000 192.168.1.1

# Maximum packets per second
nmap --max-rate 500 192.168.1.1

# Parallel host scanning
nmap --min-hostgroup 50 192.168.1.0/24

# Timeout settings
nmap --host-timeout 5m 192.168.1.0/24
```

---

## 11. Nmap Scripting Engine (NSE)

NSE is one of Nmap's most powerful features. It allows automated, customizable scanning using scripts written in **Lua**.

> Nmap includes **600+ built-in scripts** located at `/usr/share/nmap/scripts/`

### Script Categories

| Category | Description |
|----------|-------------|
| `auth` | Authentication bypass and credential testing |
| `broadcast` | Send broadcast packets to discover hosts |
| `brute` | Brute-force credential attacks |
| `default` | Safe, commonly useful scripts (run with `-sC`) |
| `discovery` | Network and service discovery |
| `dos` | Denial of service testing ⚠️ |
| `exploit` | Exploit known vulnerabilities |
| `external` | Use external resources (DNS, WHOIS) |
| `fuzzer` | Fuzz testing (send unusual input) |
| `intrusive` | Aggressive scripts that may crash services |
| `malware` | Detect malware and backdoors |
| `safe` | Non-intrusive, won't crash services |
| `version` | Advanced version detection |
| `vuln` | Check for known vulnerabilities |

### Running NSE Scripts

```bash
# Run DEFAULT scripts (equivalent to -sC)
nmap -sC 192.168.1.1

# Run a specific script
nmap --script=http-title 192.168.1.1

# Run multiple scripts
nmap --script=http-title,ssh-hostkey 192.168.1.1

# Run all scripts in a category
nmap --script=vuln 192.168.1.1
nmap --script=safe 192.168.1.1
nmap --script=discovery 192.168.1.1

# Run scripts matching a wildcard
nmap --script="http-*" 192.168.1.1

# Pass arguments to scripts
nmap --script=http-brute --script-args userdb=/tmp/users.txt,passdb=/tmp/pass.txt 192.168.1.1
```

### Common & Important NSE Scripts

#### 🌐 HTTP Scripts
```bash
nmap --script=http-title -p 80,443 192.168.1.1        # Get web page titles
nmap --script=http-headers -p 80 192.168.1.1           # HTTP response headers
nmap --script=http-methods -p 80 192.168.1.1           # Check allowed HTTP methods
nmap --script=http-robots.txt -p 80 192.168.1.1        # Read robots.txt
nmap --script=http-enum -p 80 192.168.1.1              # Enumerate web directories
nmap --script=http-sql-injection -p 80 192.168.1.1     # Check for SQLi
nmap --script=http-csrf -p 80 192.168.1.1              # Check for CSRF
nmap --script=http-shellshock -p 80 192.168.1.1        # Shellshock CVE-2014-6271
```

#### 🔐 SSL/TLS Scripts
```bash
nmap --script=ssl-cert -p 443 192.168.1.1              # Read SSL certificate
nmap --script=ssl-enum-ciphers -p 443 192.168.1.1      # List supported ciphers
nmap --script=ssl-heartbleed -p 443 192.168.1.1        # Heartbleed CVE-2014-0160
nmap --script=ssl-poodle -p 443 192.168.1.1            # POODLE vulnerability
```

#### 🖥️ SMB Scripts (Windows Networks)
```bash
nmap --script=smb-os-discovery -p 445 192.168.1.1      # SMB OS info
nmap --script=smb-vuln-ms17-010 -p 445 192.168.1.1    # EternalBlue / WannaCry
nmap --script=smb-vuln-ms08-067 -p 445 192.168.1.1    # Conficker vulnerability
nmap --script=smb-enum-shares -p 445 192.168.1.1       # List SMB shares
nmap --script=smb-enum-users -p 445 192.168.1.1        # Enumerate users
nmap --script=smb-brute -p 445 192.168.1.1             # Brute force SMB
```

#### 🔑 SSH Scripts
```bash
nmap --script=ssh-hostkey -p 22 192.168.1.1            # Get SSH host key
nmap --script=ssh-auth-methods -p 22 192.168.1.1       # Supported auth methods
nmap --script=ssh-brute -p 22 192.168.1.1              # Brute force SSH ⚠️
```

#### 🗄️ Database Scripts
```bash
nmap --script=mysql-info -p 3306 192.168.1.1            # MySQL info
nmap --script=mysql-databases -p 3306 192.168.1.1       # List databases
nmap --script=ms-sql-info -p 1433 192.168.1.1           # MSSQL info
nmap --script=oracle-tns-version -p 1521 192.168.1.1    # Oracle version
```

#### 📡 SNMP Scripts
```bash
nmap --script=snmp-info -sU -p 161 192.168.1.1          # SNMP system info
nmap --script=snmp-interfaces -sU -p 161 192.168.1.1    # Network interfaces
nmap --script=snmp-brute -sU -p 161 192.168.1.1         # SNMP community brute
```

#### 🔍 Discovery Scripts
```bash
nmap --script=dns-brute 192.168.1.1                      # DNS brute force subdomains
nmap --script=whois-domain example.com                   # WHOIS lookup
nmap --script=traceroute-geolocation 192.168.1.1         # Geo-location of hops
```

### Finding Scripts
```bash
# List all scripts
ls /usr/share/nmap/scripts/

# Search for scripts by keyword
ls /usr/share/nmap/scripts/ | grep "http"
ls /usr/share/nmap/scripts/ | grep "vuln"

# Get help on a script
nmap --script-help http-enum
nmap --script-help smb-vuln-ms17-010
```

---

## 12. Firewall & IDS Evasion

These techniques help bypass firewalls and Intrusion Detection Systems (IDS).

> ⚠️ Only use on networks you own or have explicit permission to test.

### Packet Fragmentation

```bash
# Fragment packets into 8-byte pieces
nmap -f 192.168.1.1

# Double fragmentation (16-byte pieces)
nmap -ff 192.168.1.1

# Specify fragment size (must be multiple of 8)
nmap --mtu 16 192.168.1.1
```

### Decoy Scanning

```bash
# Use random decoy IPs (your real IP is also in the list)
nmap -D RND:5 192.168.1.1

# Use specific decoy IPs
nmap -D 192.168.1.100,192.168.1.101,ME 192.168.1.1

# Use RND for fully random decoys
nmap -D RND:10 192.168.1.1
```

The target sees traffic from multiple IPs, making it hard to identify the real scanner.

### IP Spoofing

```bash
# Spoof source IP (scan won't return to you — use with zombie)
nmap -S 192.168.1.200 -e eth0 192.168.1.1

# Spoof source port (bypass firewalls that trust DNS port 53)
nmap --source-port 53 192.168.1.1
nmap -g 53 192.168.1.1
```

### Idle (Zombie) Scan

```bash
# Completely hide your identity using a zombie host
sudo nmap -sI zombie_ip:port target_ip
sudo nmap -sI 192.168.1.50 192.168.1.1
```

### Slow Down Scanning

```bash
# Add random delays (T0/T1) to evade time-based IDS rules
nmap -T1 192.168.1.1
nmap --scan-delay 1s 192.168.1.1
nmap --max-scan-delay 10s 192.168.1.1
```

### Randomize Host Order

```bash
nmap --randomize-hosts 192.168.1.0/24
```

### Append Random Data

```bash
# Append junk data to packets to confuse IDS signatures
nmap --data-length 25 192.168.1.1
```

### Disable DNS Resolution

```bash
# Don't resolve hostnames (faster, less noise)
nmap -n 192.168.1.1

# Always resolve hostnames
nmap -R 192.168.1.1
```

### Spoof MAC Address

```bash
# Random MAC address
nmap --spoof-mac 0 192.168.1.1

# Specific MAC address
nmap --spoof-mac 00:11:22:33:44:55 192.168.1.1

# Use MAC from a specific vendor
nmap --spoof-mac Apple 192.168.1.1
```

---

## 13. Output Formats

Always save your scan results — you'll want to reference them later.

```bash
# Normal output (human-readable)
nmap -oN scan_results.txt 192.168.1.1

# XML output (for tools like Metasploit, Nessus parsers)
nmap -oX scan_results.xml 192.168.1.1

# Grepable output (easy to parse with grep/awk)
nmap -oG scan_results.gnmap 192.168.1.1

# All formats at once (creates .nmap, .xml, .gnmap)
nmap -oA scan_results 192.168.1.1

# Append to existing file
nmap -oN scan_results.txt --append-output 192.168.1.1
```

### Verbosity

```bash
# Verbose (show extra info during scan)
nmap -v 192.168.1.1

# Very verbose
nmap -vv 192.168.1.1

# Debug mode (extremely detailed)
nmap -d 192.168.1.1
nmap -dd 192.168.1.1

# Show reason for port state
nmap --reason 192.168.1.1

# Show all open ports (don't truncate)
nmap --open 192.168.1.1
```

### Parsing Grepable Output

```bash
# Extract live hosts from grepable output
grep "Up" scan_results.gnmap | cut -d " " -f2

# Extract all open ports
grep "/open/" scan_results.gnmap

# Extract hosts with port 80 open
grep "80/open" scan_results.gnmap
```

---

## 14. Useful Scan Combinations

### Quick Network Reconnaissance
```bash
# Step 1: Find live hosts
nmap -sn 192.168.1.0/24 -oG alive_hosts.gnmap

# Step 2: Fast port scan on live hosts
nmap -sS -T4 -F -iL alive.txt -oN fast_scan.txt

# Step 3: Deep scan on interesting hosts
nmap -sS -sV -O -p- -T4 192.168.1.1 -oA full_scan
```

### CTF / Hack The Box One-Liner
```bash
# Full scan: all ports, version, scripts, OS, fast
sudo nmap -sCV -T4 -p- --min-rate 1000 -oA htb_target 10.10.10.1
```

### Vulnerability Scan
```bash
sudo nmap -sV --script=vuln 192.168.1.1 -oN vuln_scan.txt
```

### Web Server Enumeration
```bash
nmap -sV -p 80,443,8080,8443 --script="http-*" 192.168.1.1
```

### Windows Network Scan (SMB Focus)
```bash
sudo nmap -sS -p 135,139,445 --script=smb-os-discovery,smb-vuln-ms17-010 192.168.1.0/24
```

### Stealth Scan with Evasion
```bash
sudo nmap -sS -T1 -f -D RND:5 --data-length 25 -p 80,443 192.168.1.1
```

### Full Aggressive Scan
```bash
sudo nmap -A -T4 -p- 192.168.1.1
# -A = OS detection + version detection + scripts + traceroute
```

---

## 15. Nmap Cheat Sheet

### Essential Flags Quick Reference

| Flag | Description |
|------|-------------|
| `-sS` | SYN/Stealth scan (default with root) |
| `-sT` | TCP connect scan (no root needed) |
| `-sU` | UDP scan |
| `-sN` | NULL scan |
| `-sF` | FIN scan |
| `-sX` | XMAS scan |
| `-sA` | ACK scan (firewall mapping) |
| `-sI` | Idle/Zombie scan |
| `-sn` | Ping sweep (no port scan) |
| `-Pn` | Skip ping, assume host is up |
| `-p-` | Scan all 65535 ports |
| `-F` | Fast scan (top 100 ports) |
| `-sV` | Service/version detection |
| `-O` | OS detection |
| `-A` | Aggressive (OS + version + scripts + traceroute) |
| `-sC` | Run default NSE scripts |
| `--script=X` | Run specific NSE script(s) |
| `-T0` to `-T5` | Timing templates (0=slowest, 5=fastest) |
| `-oN` | Normal text output |
| `-oX` | XML output |
| `-oG` | Grepable output |
| `-oA` | All output formats |
| `-v` / `-vv` | Verbose output |
| `-f` | Fragment packets |
| `-D RND:5` | Use 5 random decoys |
| `--source-port 53` | Spoof source port |
| `-n` | No DNS resolution |
| `--open` | Show only open ports |
| `--reason` | Show reason for port state |
| `--min-rate` | Set minimum packet rate |

---

## 16. Legal & Ethical Use

> **⚠️ IMPORTANT: Only scan networks and systems you own or have EXPLICIT written permission to test.**

### Legal Considerations
- Unauthorized port scanning is **illegal** in many countries
- Even "just looking" can be considered an intrusion attempt
- Always get written authorization before scanning
- In penetration testing, this is covered in the **Rules of Engagement (RoE)**

### Ethical Use Cases ✅
- Scanning your own home network
- Authorized penetration testing engagements
- CTF (Capture The Flag) competitions
- Lab environments (VirtualBox, VMware, HackTheBox, TryHackMe)
- Security auditing your own organization's infrastructure

### Illegal Use Cases ❌
- Scanning company networks without authorization
- Scanning government systems
- Scanning other people's servers or websites
- Using scan data to attack systems

### Safe Practice Environments
| Platform | URL |
|----------|-----|
| HackTheBox | https://hackthebox.com |
| TryHackMe | https://tryhackme.com |
| VulnHub | https://vulnhub.com |
| DVWA | https://dvwa.co.uk |
| Nmap's own test server | `scanme.nmap.org` |

```bash
# Always safe to scan (Nmap's official test server)
nmap -A scanme.nmap.org
```

---

## 🗂️ Additional Resources

| Resource | Link |
|----------|------|
| Official Nmap Website | https://nmap.org |
| Nmap Documentation | https://nmap.org/docs.html |
| NSE Script Database | https://nmap.org/nsedoc/ |
| Nmap Book (free online) | https://nmap.org/book/toc.html |
| Zenmap (GUI for Nmap) | https://nmap.org/zenmap/ |
| CVE Database | https://cve.mitre.org |
| Exploit-DB | https://exploit-db.com |

---

## 📝 Summary — What You Learned

```
Video 1 → What is Nmap + Installation
Video 2 → Target Specification + Host Discovery
Video 3 → Port Scanning Types (SYN, TCP, UDP)
Video 4 → NULL, FIN, XMAS, ACK Scans
Video 5 → Service Version Detection (-sV)
Video 6 → OS Detection (-O, -A)
Video 7 → Timing Templates + Performance
Video 8 → Nmap Scripting Engine (NSE)
Video 9 → Firewall Evasion + Output Formats
```

---

<div align="center">

**Made for studying the Coder-Web Nmap Course**

*For educational and ethical use only*

</div>
