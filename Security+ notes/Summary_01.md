# CompTIA Security+ (SY0-601) — Study Notes

> Organized notes covering Lessons 1–6 of the CompTIA Security+ course.

---

## Table of Contents

1. [Lesson 1 – Comparing Security Roles and Security Controls](#lesson-1--comparing-security-roles-and-security-controls)
2. [Lesson 2 – Explaining Threat Actors and Threat Intelligence](#lesson-2--explaining-threat-actors-and-threat-intelligence)
3. [Lesson 3 – Performing Security Assessments](#lesson-3--performing-security-assessments)

---

## Lesson 1 – Comparing Security Roles and Security Controls

### Information Security (InfoSec)

The protection of data resources from unauthorized access, attack, theft, or damage.

Secure information has **3 properties** — the **CIA Triad**:

| Property | Description |
|---|---|
| **Confidentiality** | Certain information should only be known to certain people. |
| **Integrity** | Data is stored and transferred as intended; any modification is authorized. |
| **Availability** | Information is accessible only to those authorized to view or modify it. |

> Some security models also include **Non-repudiation**: a subject cannot deny performing an action.

---

### Information Security Competencies (Responsibilities)

- Risk assessments and testing security systems; make recommendations.
- Specify, source, install, and configure secure devices and software.
- Set up and maintain document access controls and user privilege profiles.
- Monitor audit logs.
- Manage security-related incident response and reporting.
- Create and test disaster recovery plans and procedures.
- Participate in security training and education programs.

---

### Security Policy

A formalized statement that defines how security will be implemented within an organization.

Key roles:
- **Director of Security**
- **Chief Security Officer (CSO)**
- **Chief Information Security Officer (CISO)**

#### Information Security Business Units

**1. Security Operations Center (SOC)**
A location where security professionals monitor and protect critical information assets across business functions.

**2. Incident Response**
May be handled by:
- **CIRT** – Cyber Incident Response Team
- **CSIRT** – Computer Security Incident Response Team
- **CERT** – Computer Emergency Response Team

These serve as a single point of contact for the notification of security incidents.

---

### Security Controls

Security controls are designed to give a system or data asset the CIA Triad properties and non-repudiation. They are under the responsibility of the IT and Risk Management departments.

#### Categories (by implementation)

| Category | Description | Examples |
|---|---|---|
| **Technical** | Implemented as a system (most common); also called *logical controls*. | Hardware, software, firewalls, antivirus |
| **Operational** | Implemented by people. | Security guards, training programs |
| **Managerial** | Provides oversight of the information system. | — |

#### Types (by goal)

| Type | Description | Timing |
|---|---|---|
| **Preventive** | Eliminates or reduces the likelihood of a successful attack. | Before attack |
| **Detective** | Identifies and records attempted or successful intrusions. | During attack |
| **Corrective** | Eliminates or reduces the impact of an intrusion event. | After attack |

#### Functional Types

- **Physical** – Alarms, gateways, locks, etc.
- **Deterrent** – Psychologically discourages an attacker from intrusion.
- **Compensating** – Serves as a substitute for a principal control.

---

### ISO & Cloud Frameworks

**ISO** (International Organization for Standardization) produced a cybersecurity framework in conjunction with the **IEC** (International Electrotechnical Commission).

| Standard | Description |
|---|---|
| **ISO 27001** | InfoSec standards (27K series) |
| **ISO 31000** | Enterprise Risk Management (ERM) |
| **CSA** | Cloud Security Alliance |

---

## Lesson 2 – Explaining Threat Actors and Threat Intelligence

### Core Concepts

```
Vulnerability + Threat = Risk (Impact × Likelihood)
                        ↑
               Security Assessment
```

| Term | Definition |
|---|---|
| **Vulnerability** | A weakness that could be triggered accidentally or exploited intentionally to cause a security breach. |
| **Threat** | The potential for something or someone (a *threat actor/agent*) to exploit a vulnerability and breach security. |
| **Risk** | The likelihood and impact of a threat. `Threat + Impact = Risk` |
| **Attack Vector** | The path or tool the malicious threat actor uses. |

> `Vulnerability + Exploit = Threat`

---

### Threat Actors

- **External** (malicious outsiders)
- **Internal** (insider) — increases risk significantly
- **Intent/Motivation** — the goal and motive behind the attack

#### Hacker Types

| Type | Description |
|---|---|
| **Black Hat** | Unauthorized — malicious hackers |
| **White Hat** | Authorized — penetration testers, ethical hackers |
| **Gray Hat** | Semi-authorized — often involved in bug bounty hunting |
| **Script Kiddies** | Low-skill attackers using existing tools |
| **Hacker Teams / Hacktivists** | Organized groups with ideological motivations |
| **APT** | Advanced Persistent Threat — highly sophisticated, state-sponsored groups |

---

### Attack Vectors

- Direct access
- Removable media
- Email & links
- Remote and wireless
- Web & social media
- Cloud

---

### Threat Intelligence Sources

A department that stays aware of new threats and conducts research to remain updated — increasingly common in organizations today.

- These organizations operate **honeynets** to observe how hackers interact with vulnerable systems.
- A primary source of threat intelligence is the **Dark Web**.

| Term | Definition |
|---|---|
| **Dark Net** | A network established as an overlay to internet infrastructure via software such as TOR, Freenet, or I2P. |
| **Dark Web** | Sites, content, and services accessible only over the dark net. |

> **OSINT** (Open Source Intelligence) — publicly available threat research sources.

---

## Lesson 3 – Performing Security Assessments

### Network Discovery (Reconnaissance)

The process of mapping out the attack surface.

#### Topology Discovery (Footprinting)

Scanning for hosts, IP ranges, and routes between networks to map out the structure of the target network.

#### Basic Command-Line Tools

| Tool | Platform | Description |
|---|---|---|
| `ipconfig` | Windows | Show network interface configuration |
| `ifconfig` / `ip a` | Linux | Same as ipconfig (old/new command) |
| `ping` | Both | Probe a host using ICMP |
| `arp -a` | Both | Display ARP cache (MAC addresses of communicated hosts) |
| `ip neighbor` | Linux | Discover machines around the local host |
| `route` | Both | View and configure the local routing table |
| `tracert` | Windows | ICMP-based route tracing with RTT |
| `traceroute` | Linux | UDP-based route discovery by default |
| `pathping` / `mtr` | Win/Linux | Latency and packet loss statistics over longer periods |

> **Difference between `arp -a` and `ip neighbor`:**
> - `arp -a` shows only machines that have *communicated* with the local host.
> - `ip neighbor` *discovers* machines around the local host. (`ip neighbor = netdiscover -i`)

---

### Nmap — Network Security Mapper/Scanner

A powerful IP scanner, port scanner, and network scanner.

#### Common Nmap Options

| Flag | Description |
|---|---|
| (default) | Pings and sends TCP ACK to ports 80 & 443 to detect live hosts |
| `-sS` | TCP SYN scan (fast scan) |
| `-sU` | UDP scan |
| `-p` | Specify port range |
| `-sV` / `-A` | Service fingerprinting: discover protocols, app names, versions, device type, OS |
| `-O` | OS detection |
| `-sn` | Scan for up/down hosts only (no port scan) |
| `--top-ports 20` | Scan top 20 common ports |

**Fingerprinting** — detailed analysis of services on a particular host.

---

### Other Reconnaissance & Discovery Tools

| Tool | Description |
|---|---|
| **theHarvester** | Gathers OSINT — scans public sources for emails, names, subdomains, IPs, URLs, etc. |
| **dnsenum** | Provides more information about DNS resolution |
| **scanless** | Uses nmap and curl for scanning |
| **Nessus** | Vulnerability scanning |
| **netstat** | Shows the state of TCP/UDP ports on the local machine (Windows & Linux) |
| **nslookup** (Windows) / **dig** (Linux) | Query name records for a domain using a DNS resolver |

---

### Packet Capture & Tcpdump

Packet and protocol analysis depends on a **sniffer tool** to capture and decode frames of data. Capture can be taken from a host or a network segment via:
- **SPAN (Switched Port Analyzer) / Mirror Port** — switch configured to copy frames to a destination port.
- **TAP (Test Access Port)** — hardware component inserted in cabling to copy frames.

#### Tools

| Tool | Description |
|---|---|
| **tcpdump** | Command-line packet capture utility for Linux |
| **Wireshark** | Graphical packet capture and analysis utility |

**Key tcpdump syntax:**
```bash
tcpdump -i eth0              # Listen on interface eth0
tcpdump -w output.pcap       # Save frames to .pcap file
tcpdump -r output.pcap       # Read a .pcap file
tcpdump -vv dst 10.1.0.2 and port www -w www.pcap  # Sniff HTTP/HTTPS to a destination
```

---

### Packet Injection

Used by penetration testers or threat actors.

**Tools:** Dsniff, Ettercap, Scapy, hping, tcpreplay

- **Scapy** — open-source spoofing tool; lets a pen tester craft network packets to exploit vulnerable firewalls and IDSs. Also supports host/port detection, firewall testing, traceroute, and DoS.
- **tcpreplay** — allows resending a captured packet after modifying its content.

---

### Exploitation Frameworks

- **RAT (Remote Access Trojan)** — malware that gives an adversary the means to remotely access the network; causes data exfiltration.
- **Metasploit** — exploit framework that allows a threat actor to control a device remotely (open PowerShell, create a user, install software, etc.).
- **Netcat** — effective tool for testing connectivity, port scanning, and fingerprinting; can also establish connections with remote machines.

---

### Software Vulnerabilities & Patch Management

Software exploitation targets vulnerabilities in all types of code: applications, OS kernels, firmware, BIOS/UEFI.

| Term | Definition |
|---|---|
| **Zero Day** | A vulnerability exploited before the developer knows about it or can release a patch. |
| **Legacy Platform** | A platform no longer supported with security patches (e.g., Windows 7) — unpatchable. |

#### Common Vulnerability Sources

- Default settings leaving insecure interfaces enabled.
- Weak network appliance settings allowing lateral movement.
- Unsecured root accounts.
- Open permissions.
- Open ports and services — increases the attack surface.
- Unsecure protocols (e.g., HTTP → HTTPS, FTP → SFTP/FTPS, Telnet → SSH).
- Weak encryption.
- Verbose error messages — can reveal info to threat actors.

---

### Data Breaches & Data Exfiltration

| Term | Definition |
|---|---|
| **Data Breach** | Confidential data is read, transferred, modified, or deleted without authorization. |
| **Data Exfiltration** | The methods and tools an attacker uses to transfer data without authorization. |

A data breach may allow identity theft (spoofing) or selling data to other malicious actors.

---

### Vulnerability Scanning

| Type | Tools |
|---|---|
| Network Vulnerability Scanning | Nessus, OpenVAS |
| Application & Web Scanning | Nikto |

- Known vulnerabilities are described as **vulnerability feeds** (plug-ins in Nessus, NVTs in OpenVAS).
- Scanners use **SCAP** (Secure Content Automation Protocol) to obtain feed/plugin updates.

#### CVE & CVSS

- **CVE (Common Vulnerabilities & Exposures)** — a dictionary of vulnerabilities in published OSes and applications.
  - Format: `CVE-YYYY-XXXXXXXX`
- **CVSS (Common Vulnerability Scoring System)** — generates a score from 0–10:

| Score | Severity |
|---|---|
| 0.1 – 3.9 | Low |
| 4.0 – 6.9 | Medium |
| 7.0 – 8.9 | High |
| 9.0 – 10.0 | Critical |

#### Scan Types

| Type | Description |
|---|---|
| **Non-credentialed scan** | Best for external assessment (unprivileged user); no login info needed. |
| **Credentialed scan** | Shows what an insider attack could achieve; uses login credentials to various hosts. |

---

### Penetration Testing (Ethical Hacking)

Uses authorized hacking techniques to discover exploitable weaknesses.

- **Rules of Engagement** — specify what activity is permitted or not.
- The tester must be informed about how much network info is provided → **Attack Profile**.

#### Attack Profiles

| Profile | Environment | Information Level |
|---|---|---|
| **Black Box** | Unknown environment | No privileged information — simulates external threat |
| **White Box** | Known environment | Full knowledge — simulates privileged insider threat |
| **Gray Box** | Partially known environment | Some information — simulates unprivileged insider threat |

#### Penetration Testing Team

- **Red Team** → Offensive role
- **Blue Team** → Defensive role

#### Bug Bounty

A program operated by a software vendor or website operator where rewards are given for reporting vulnerabilities.