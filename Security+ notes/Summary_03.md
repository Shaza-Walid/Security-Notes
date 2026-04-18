# CompTIA Security+ (SY0-601) — Study Notes

Organized notes covering Lessons 7–9 of the CompTIA Security+ course.

---

## Table of Contents

1. [Lesson 7 – Implementing Authentication Controls](#lesson-7--implementing-authentication-controls)
2. [Lesson 8 – Implementing Identity & Account Management Controls](#lesson-8--implementing-identity--account-management-controls)
3. [Lesson 9 – Implement Secure Network Designs](#lesson-9--implement-secure-network-designs)

---

## Lesson 7 – Implementing Authentication Controls

### Access Control System (ACS)

The set of technical controls that govern how subjects may interact with objects.

- **Subjects** — users, devices, software processes, or anything that can request and be granted access to a resource.
- **Objects** — the resources (networks, servers, databases, etc.).

```
PC (Subject) ──(request)──→ Server (Object)
             ←──(access)──
```

---

### Identity & Access Management (IAM)

A system described in **4 main processes**:

| # | Process | Description |
|---|---|---|
| 1 | **Identification** (هوية) | Creating an account or ID that uniquely identifies every subject. |
| 2 | **Authentication** (تمييز) | Proving who a subject is, or what they claim to be. |
| 3 | **Authorization** (الصلاحيات) | Determining what rights subjects should have. |
| 4 | **Accounting** | Tracking authorized usage and alerting when unauthorized use is detected or attempted. |

---

### Authentication Technologies (Credential Types)

**1. Something You Know — Knowledge-based Authentication**
> ⚠️ Not secure enough (vulnerable)

- The typical knowledge factor is the **logon** (composed of username & password).
  - Not a secret in itself, but must not be published openly — known only by the user.
- **PIN** — Personal Identification Number.
- **Swipe patterns.**

**2. Something You Have — Ownership-based Authentication**

- The account holder possesses something that no one else does.
- Examples: smart card, fob key, wristband, hard tokens, etc.
- Smartphones can be used to receive a uniquely access code as a **soft token**.
- Unlike passwords, these tokens are valid for only the holder within a **brief time window**.

**3. Something You Are/Do — Biometric Authentication**

- Biometric factor like fingerprint, or behavioural identifiers like the way someone moves (gait).
- These identifiers are scanned and recorded as a **template**.

---

### Authentication Design

About selecting a technology that meets requirements for **Confidentiality, Integrity, and Availability**.

| Type | Description |
|---|---|
| **Single-Factor Authentication** | Uses only one type of knowledge. Can quite easily be compromised — **vulnerable**. |
| **Multi-Factor Authentication (MFA)** | Uses more than one type of knowledge. |
| **Two-Factor Authentication (2FA)** | Combines either an ownership-based smart card or biometric identifier **with** something you know (e.g., password or PIN with fingerprint). |

---

### OS Authentication

- One of the most important features of an OS is its **authentication provider**.
- Knowledge-based authentication relies on **cryptographic hashing**.

#### Windows Authentication

| Type | Description |
|---|---|
| **Local Sign-in** | The Local Security Authority (LSA) compares the submitted credential to a hash stored in the Security Accounts Manager (SAM) database, which is part of the registry. |
| **Network Sign-in** | LSA can pass credentials for authentication to a network service. Based on **Kerberos**. Legacy network apps use **NT LAN Manager (NTLM)** authentication. |
| **Remote Sign-in** | If the user's device is not connected to the local network, authentication can take place over a **VPN** or **web portal**. |

#### Linux Authentication

| Item | Description |
|---|---|
| `/etc/passwd` | Local user account names are stored here. |
| `/etc/shadow` | When a user logs into the local interactive shell, the password is checked against a hash stored here. |
| **SSH** | Used for interactive login over a network. The user can authenticate using **cryptographic keys** instead of passwords. |

---

### Single Sign-On (SSO)

This system allows the user to **authenticate once** to a local device and be authenticated to compatible application servers without having to enter credentials again.

> In Windows → SSO is provided by the **Kerberos Framework**.

#### Kerberos Flow

```
Logon Request ──→ Authentication Service (KDC)
Principal      ←── Granting Ticket ──→ Application Server
```

- **KDC** — Key Distribution Center.
- Kerberos is designed to work over a **trusted local network**.
- KDC runs on **port 88** using TCP or UDP.
- The Authentication Service is responsible for authenticating user logon requests.

---

### Authentication Protocols

#### Password Authentication Protocol (PAP)

- An unsophisticated authentication method developed as part of the **Point-to-Point Protocol (PPP)**.
- Used to transfer TCP/IP data over serial or dial-up connections.
- Also used as the basic authentication mechanism in HTTP.
- Relies on **plaintext exchange** → obsolete for most purposes, except through an encrypted tunnel.

```
Client ──(username/password in cleartext)──→ Server
             [only safe inside encrypted tunnel]
```

#### Challenge Handshake Authentication Protocol (CHAP)

- Developed as part of PPP as a means of authenticating users over a remote link.
- Relies on an encrypted challenge in a system called **three-way handshake**:
  1. **Challenge** — server sends a challenge to the client.
  2. **Response** — client responds.
  3. **Verification** — server verifies the response.

```
Server ──(challenge)──→ Client
       ←──(response)──
       ──(verification)──
```

**MS-CHAPv2** — Microsoft's implementation of CHAP. Recommended for use between Microsoft devices and servers.

---

### Password Attacks

#### 1. Plaintext / Unencrypted Attacks
- Protocols that transmit passwords in cleartext (e.g., PAP, basic HTTP/FTP, Telnet).
- Passwords must **not** be used over these protocols and must **never** be saved to an unmanaged file.

#### 2. Online Attacks
- The threat actor interacts with the authentication service directly (e.g., web login form or VPN gateway).
- Tries passwords using a database of known passwords or a list of cracked ones.

#### 3. Offline Attacks
- The attacker has obtained a database of password hashes.
- Once the DB is obtained, the cracker **does not interact** with the authentication system.

Password database locations:

| OS | Location |
|---|---|
| Windows | `%SystemRoot%\System32\config\SAM` |
| Windows (AD) | `%SystemRoot%\NTDS\NTDS.DIT` |
| Linux | `/etc/shadow` |

> The only indicator of this attack is the **audit log** (file system that records malicious account accessing these files).
> Attacker can also read credentials from **host memory**.

#### 4. Hybrid Attacks
- Uses a **combination of attack methods** when trying to crack a password.

#### Attack Tools

| Tool | Type |
|---|---|
| **Brute-force** | Tries every possible combination by force — time-consuming but thorough. |
| **Dictionary attack** | Tries passwords from a list of known words — useful for non-complex passwords. |
| **Rainbow Table** | A precomputed lookup table for all possible passwords and their hashes. |

#### Salt Technique
- Adding a **random value** to the stored plaintext before hashing.
- Helps to **slow down rainbow table attacks** since the table cannot be created in advance.
- Used by Linux.

#### Password Cracker Tools
**Cain, L0phtcrack, Hashcat**

---

## Lesson 8 – Implementing Identity & Account Management Controls

### IAM & HR Collaboration

- IAM involves both **IT/Security** and **HR** departments — they together do background checks (internally or externally).
- **Background check** determines that the person is not concealing criminal activities or connections that would make them unsuitable or risky.

### Employee Lifecycle

| Process | Description |
|---|---|
| **Onboarding** | The process of welcoming a new employee (HR collaborating with IT). |
| **Non-Disclosure Agreement (NDA)** | A separate document asserting that new employees will not share confidential information with a third party. |
| **Offboarding** | The process of ensuring that an employee leaves gracefully. |

### Privilege Management Principles

- **Privilege Management** — HR & IT collaborate to ensure it.
- **Separation of Duties** — duties and responsibilities should be divided among employees to prevent ethical conflicts or abuse of powers.
- **Least Privilege** — a user is granted sufficient rights to perform their job and no more.
- **Job Rotation** — no one is permitted to remain in the same job for an extended period.
- **Mandatory Vacation** — employees are forced to take their vacation time.

---

### Security Account Types

#### ① Guest Account
- Shared account with no password.
- Allows **anonymous and unauthenticated access** to a resource.

#### Security Group Account
- Simplifies and centralizes the administrative process of assigning rights, rather than assigning rights directly to each user.
- Uses an **Access Control List (ACL)**.

```
ACL
├── Group (Read Only)  →  user1, user2, user3
└── Group (Read/Write) →  user4, users...
```

#### ② Administrative / Privileged Account
- Able to install and remove apps and drivers.
- Change system-level settings.
- Access any object in the file system.

#### ③ Default Account (Superuser)
- Created by the operating system or application when it is installed.
- Has every permission available.
- In Windows → **Administrator**
- In Linux → **root**

#### ④ Service Account
- Used by scheduled processes and application server software (e.g., databases).

---

### Account Attributes & Access Policies

- A user account is defined by a unique **Security Identifier (SID)**, a name, and a credential.
- Each account is associated with a **profile**.
- Each account can be assigned **permissions** — directly or inherited through membership of a group.

**Access Policies** determine things like: install software, right to logon, etc.

> In Windows Active Directory networks, access policies can be configured via **Group Policy Objects (GPOs)** — which configure access rights for user/groups/role accounts.

---

### Account Restrictions

| Restriction | Description |
|---|---|
| **Location-Based Policies** | Have a logical network location identified by IP, Subnet, VLAN, organizational unit, etc. |
| **Geofencing** | Restriction based on physical location. |

---

### Account Audits

Accounting and auditing processes are used to detect whether an account has been **compromised** or is being **misused**.

---

## Lesson 9 – Implement Secure Network Designs

### Network Architecture Weaknesses

Weaknesses in network architecture make it more susceptible to undetected intrusions or catastrophic service failures, such as:

- **Single point of failure** — a single server or app responsible for all services.
- **Lack of documentation & change control** — services might be added without proper change control procedures, leading to a lack of visibility into how the network is constituted.
- **Overdependence on perimeter security** — attacks might come from *inside* the network; not all attacks come from outside (the internet).

---

### Network Devices

#### Switch
- Forwards frames between nodes in a cabled network.
- Works on **Layer 2** of the OSI model.
- Can establish network segments by creating **VLANs** (Virtual LANs).
- Multi-port network device.

#### Wireless Access Point (WAP)
- Works on **Layer 2** of the OSI model.
- Provides a bridge between a cabled network and wireless clients or stations.

#### Router
- Forwards packets around an internetwork.
- Works on **Layer 3** of the OSI model.
- Can apply logical IP subnet addresses to segments within a network.

#### Firewall
- Applies an **Access Control List (ACL)** to filter traffic passing in a network segment.
- Works on **Layer 3** of the OSI model or higher.

#### Load Balancer
- Distributes traffic between network segments or servers to optimize performance.
- Works on **Layer 3** of the OSI model or higher.

---

### Routing & Switching Protocols

The basic function of a network is to **forward traffic from node to node**.

| Protocol/Concept | Description |
|---|---|
| **Layer 2 Forwarding** | Based on MAC address. |
| **Layer 3 Forwarding** | Based on IP address. |
| **ARP (Address Resolution Protocol)** | Resolves IP addresses to MAC addresses. |

- A **topology** is a description of how a computer network is physically or logically organized.
- A **zone** is an area of the network where the security configuration is the same for all hosts within it — it is the **main building block of a security topology**.
- Traffic between zones should be **controlled using a firewall**.

---

### Network Zones

#### The Main Zones

| Zone | Description |
|---|---|
| **External / Guest Zone (Internet)** | A zone permitting anonymous access by untrusted hosts. |
| **Extranet Zone** | A network of semi-trusted hosts. Hosts must authenticate to join the extranet. Example: **DMZ** (acts as proxy). |
| **Internal (Private) Zone** | A network of trusted hosts. |

#### DMZ (Demilitarized Zone)
- A DMZ can be established using a router/firewall appliance with **three network interfaces**:
  - One for public (internet)
  - One for DMZ
  - One for the LAN (internal network)

---

### Network Attacks

#### Man-in-the-Middle / On-Path Attacks (MitM)
- A **Layer 2 attack**.
- The threat actor gains a position **between two hosts** and captures, monitors, and relays all communication between them.
- Can also be used to covertly **modify traffic**, **spoof responses** to DNS queries, or **redirect users to spoofed websites**.

#### MAC Cloning (MAC Address Spoofing)
- A **Layer 2 attack**.
- Changes the hardware address configured on an adapter interface, or asserts the use of an arbitrary MAC address.

#### MAC Flooding Attack
- A **Layer 2 attack**.
- Used to attack a **switch**.
- The attacker's intention is to **exhaust the memory** used to store the MAC address table.
- Overwhelming the table causes the switch to stop applying MAC-based forwarding and flood unicast traffic out of all ports — effectively working as a hub, making sniffing network traffic easier.

> ✅ **Solution to Layer 2 attacks → MAC Filtering & MAC Limiting**

---

### Wireless Networks

- Each **Wireless Access Point (WAP)** is identified by its MAC address, also referred to as its **Basic Service Set Identifier (BSSID)**.
- Each wireless network is identified by its name — the **SSID (Service Set Identifier)**.
- Wireless networks operate in either **2.4 GHz** or **5 GHz** radio band.
- Each WAP must be configured to use a specific **channel** — each band radio is divided into a number of channels.
- A **Wireless Controller** is a device used for centralized management and monitoring of many WAPs, instead of configuring each WAP individually.

#### Rogue Access Point (Evil Twins) Attack
- A WAP that has been installed on the network **without authorization**, whether with malicious intent or not.
- Allows the attacker to perform **MitM attacks**.

#### Jamming Attacks
- A wireless network can be disrupted by interference from other radio sources — it is possible for an attacker to **jam** an access point.
- May be done to disrupt services or to position an evil twin.
- Achieved by setting up a WAP with a stronger signal (**Wi-Fi jamming attack**).

#### DDoS Attacks (Distributed Denial of Service)
- Can be diagnosed by **traffic spikes** that have no legitimate explanation.
- Can be counteracted by providing **high availability services** (e.g., load balancer).
- DDoS aims to **consume network bandwidth**, denying it to legitimate hosts by using an overwhelming number of bots.
- A **stateful firewall** can detect DDoS and block the source.