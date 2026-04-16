# CompTIA Security+ (SY0-601) — Study Notes

> Organized notes covering Lessons 4–6 of the CompTIA Security+ course.

---

## Table of Contents

1. [Lesson 4 – Identifying Social Engineering and Malware](#lesson-4--identifying-social-engineering-and-malware)
2. [Lesson 5 – Summarizing Basic Cryptographic Concepts](#lesson-5--summarizing-basic-cryptographic-concepts)
3. [Lesson 6 – Implementing Public Key Infrastructure](#lesson-6--implementing-public-key-infrastructure)

---

## Lesson 4 – Identifying Social Engineering and Malware

### Social Engineering — "Hacking the Human"

Refers to means of either eliciting information from someone or getting them to perform some action for the threat actor. It is one of the most common and successful malicious techniques as it exploits basic human trust.

#### Social Engineering Types

| # | Type | Description |
|---|---|---|
| 1 | **Impersonation** | Pretending to be someone else. |
| 2 | **Dumpster Diving** | Combing through garbage to find useful documents. |
| 3 | **Tailgating** | Entering a secure area without authorization by following close behind an authorized person. |
| 4 | **Piggybacking** | An attacker enters a secure area *with* an employee's permission (means of an insider threat actor). |
| 5 | **Identity Fraud** | A specific type of impersonation where the attacker uses specific data to make up an identity. |
| 6 | **Identity Theft** | Stealing someone else's real identity. |
| 7 | **Shoulder Surfing** | Stealing data by watching (monitoring) a user type it. |
| 8 | **Lunchtime Attack** | User leaves a workstation unattended while logged on; attacker gains physical access to the system. |
| 9 | **Phishing** | Combination of social engineering and spoofing. |

#### Phishing Variants

| Variant | Description |
|---|---|
| **Spear Phishing** | Attacker knows some info about the target to make the attack more convincing. |
| **Whaling** | Directed specifically against upper-level management (C-level, e.g., CEO). |
| **Vishing** | Conducted through a voice channel (telephone or VoIP). |
| **Spam** | Emails sent to hundreds of people (no specific targeting); used as a vector for many attacks. |
| **Hoaxes** | An email alert or web pop-up designed to deceive. |

---

### Malware (Malicious Software)

Software that does something harmful from the perspective of the system owner.

#### Malware Categories

**1. Viruses & Worms**
- Both **spread without any authorization** from the user.
- **Worms** — designed to replicate by themselves; operate secretly; can run without user intervention and replicate over network resources.
- **Viruses** — cannot replicate unless the user interacts with them.

**Virus Types:**

| Type | Description |
|---|---|
| **Non-resident / File Infector** | Runs with the host process; only executes the malicious app. |
| **Memory Resident** | When the malicious app is executed, the virus creates a new process for itself in memory and runs permanently. |
| **Boot Virus** | Code written to the disk boot sector or partition table of a fixed disk or USB; executes when the OS starts. |
| **Script / Macro Virus** | Uses programming features available in local scripting engines (OS, PowerShell, browsers, etc.). |

**Worm Effects:**
- Consumes network bandwidth.
- Able to crash an OS or server application (Denial of Service attack).
- Can carry a payload that performs other malicious attacks.

**2. Trojan**
Hidden within an installer package that appears to be real/legitimate.

**3. PUPs (Potentially Unwanted Programs)**
- Grayware rather than true malware.
- Software installed alongside a package selected by the user.
- Not automatically regarded as malicious.

#### Other Malware Types

| Type | Description |
|---|---|
| **Spyware** | Can perform adware-like tracking and monitoring, take screenshots, and activate recording devices. |
| **Keylogger** | A type of spyware that actively attempts to steal confidential information by recording keystrokes. |
| **Adware** | A class of PUP/grayware that performs browser reconfigurations. |
| **Backdoor** | A type of access method that circumvents the usual authentication method and gives the remote user administrative control. |
| **RAT (Remote Access Trojan)** | A backdoor malware that mimics the functionality of legitimate remote control programs. Once installed, allows the threat actor to access the host, upload files, and install software. |
| **Rootkit** | Occurs when the threat actor enters the host as an administrator. Allows installation of packages, credential entry, or accepting UAC prompts. Runs with the highest level of privilege; can use an exploit to escalate privileges after installation. |
| **Ransomware** | (فيروس الفدية) — Crypto-malware (e.g., CryptoLocker) that attempts to encrypt data files and demands payment for decryption. |

#### Botnet Architecture

```
Bot (Zombie device affected by RAT)
        ↓
    [Botnet] ──→ Triggering DDoS (Distributed Denial of Service)
                 Launching spam campaigns
                 Performing cryptomining
```

---

## Lesson 5 – Summarizing Basic Cryptographic Concepts

### Cryptography

The art of making information secure by encoding it.

| Term | Definition |
|---|---|
| **Plaintext (Cleartext)** | An unencrypted message. |
| **Ciphertext** | An encrypted message. |
| **Cipher** | The process (or algorithm) used to encrypt or decrypt. |
| **Cryptanalysis** | The art of cracking cryptographic systems. |

---

### Hashing Algorithms

The simplest type of cryptographic operation.

- Produces a **fixed-length string** from the plaintext.
- The output is called a **checksum**, **message digest**, or **hash**.
- It is **impossible to recover** the plaintext from the hash (one-way function).
- Different inputs are **unlikely** to produce the same output (**collision resistance**).
- Used to **prove integrity**.

#### Common Hash Algorithms

| Algorithm | Output Size | Notes |
|---|---|---|
| **MD5** | 128-bit digest | Shorter; not considered as safe as SHA-256 |
| **SHA-1** | 160-bit | Deprecated for most uses |
| **SHA-256** | 256-bit | Most popular; the strongest commonly used |

> SHA (Secure Hash Algorithm) variants produce different-sized outputs — longer digests offer more security.

---

### Encryption Ciphers and Keys

A type of cryptographic process that encodes data so it can be recovered (decrypted). The key ensures that encryption can only be performed by authorized persons.

#### Symmetric Encryption (Cipher)

- Uses a **single key** (private key = shared secret).
- Very **fast encryption**.
- Used for **confidentiality**.
- Used for **bulk encryption** of large amounts of data.

```
① Sender ──(copy of key)──→ Receiver
② Sender [encrypts with key] ──(send encrypted data)──→ Receiver [decrypts with same key]
```

> ⚠️ If the key is lost or stolen, the security is breached.

#### Asymmetric Encryption (Cipher)

- Uses **two keys**: a **public key** and a **private key**.
- Each key is capable of reversing the operation of its pair.
- The keys are linked such that it is **impossible to derive one from the other**.
- Used for **proving identity** (authentication).

```
Receiver generates key pair → keeps private key secret, publishes public key
Sender encrypts using Receiver's public key → sends ciphertext → Receiver decrypts using private key
```

---

### Public Key Algorithms

**1. RSA Algorithm**
- Provides the mathematical properties for deriving key pairs and performing encryption/decryption.
- Called a **trapdoor function** — easy to perform using the public key, but difficult to reverse without knowing the private key.

**2. Elliptic Curve Cryptography (ECC)**
- Another type of trapdoor function used in public key cryptography.
- A key size of **256-bit ECC** is approximately comparable to **RSA with a 2048-bit key**.

---

## Lesson 6 – Implementing Public Key Infrastructure

### Using Asymmetric Keys — Proving Things

| Goal | Method |
|---|---|
| **Confidentiality** | Send the *receiver's* public key to encrypt the message → only the receiver can decrypt it with their private key. |
| **Authentication** | Create a digital signature by encrypting it using *your* private key → others can decrypt it with your public key to verify your identity. |

---

### Public Key Infrastructure (PKI)

- Aims to **prove that the owners of public keys are who they say they are**.
- Under PKI, anyone issuing public keys must obtain a **digital certificate**.
- The validity of the certificate is guaranteed by a **Certificate Authority (CA)**.

#### Certificate Flow Example (Amazon.com)

```
PC ──(HTTP request)──→ amazon.com
PC ←──(HTTP response + certificate)──← amazon.com
                           ↕ (CSR: Certificate Signing Request)
                          [CA]
```

#### CA Types

| Type | Description | Examples |
|---|---|---|
| **Public CA** | External, trusted by browsers worldwide | IdenTrust, DigiCert, GlobalSign |
| **Private CA** | Internal, used within an organization | Windows Server Certificate Services (Active Directory) |

---

### PKI Trust Models

#### 1. Single CA (Simple Model)

- A single CA issues certificates to users.
- Users trust this CA and no other.
- ⚠️ The CA server is very exposed — if compromised, the **whole PKI collapses** (single point of failure).

#### 2. Hierarchical (Intermediate CA)

```
         Root CA
        /       \
   Intermediate  Intermediate
      CA            CA
    /    \         /   \
  CA     CA      CA     CA
```

- A single root CA issues certificates to several intermediate CAs.
- Different CAs can be set up with different certificate policies.
- Each leaf certificate can be **traced back to the root** along the chain (**certificate chaining / chain of trust**).
- ⚠️ The root is still a single point of failure — if compromised, the whole model collapses.

---

### Online vs. Offline CAs

| Type | Description |
|---|---|
| **Online CA** | Available to accept and process Certificate Signing Requests at all times. High risk of being online constantly → so the root CA is kept **offline**. |
| **Offline CA** | Will be brought online only to add or update intermediate CAs. |

---

### Digital Certificates

- A digital certificate is essentially a **wrapper for a subject's public key**.
- Contains info about the **subject** and **issuer/guarantor** (could be a human user or computer server) to prove it was issued by a particular CA.
- Digital certificates are based on the **X.509 standard** — defines the fields/attributes that must be present.

#### Most Important Certificate Lifecycle Fields

1. Key Generation
2. Certificate Generation
3. Storage
4. Limited Duration (expiry)

> - If a certificate is **revoked** (no longer valid), it **cannot be un-revoked**.
> - A **suspended** certificate can be re-enabled.

#### Implementing PKI

| Platform | Tool |
|---|---|
| **Windows Server** | Install **Active Directory Certificate Services** |
| **Linux** | Install **OpenSSL suite** |