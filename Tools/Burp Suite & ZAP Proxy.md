# 🔐 Burp Suite & OWASP ZAP Proxy — Complete Study Notes

> Web Application Security Testing | Penetration Testing | Bug Bounty

---

## 📚 Table of Contents

1. [What is Web Application Security Testing?](#1-what-is-web-application-security-testing)
2. [How Web Applications Work](#2-how-web-applications-work)
3. [What is an Intercepting Proxy?](#3-what-is-an-intercepting-proxy)
4. [Burp Suite — Deep Dive](#4-burp-suite--deep-dive)
   - [Editions](#41-editions)
   - [Core Components](#42-core-components)
   - [Setup & Configuration](#43-setup--configuration)
   - [Intruder Attack Modes](#44-intruder-attack-modes)
5. [OWASP ZAP — Deep Dive](#5-owasp-zap--deep-dive)
   - [Overview](#51-overview)
   - [Core Components](#52-core-components)
   - [Setup & Configuration](#53-setup--configuration)
   - [Key Features Unique to ZAP](#54-key-features-unique-to-zap)
6. [Vulnerability Detection Coverage](#6-vulnerability-detection-coverage)
7. [Burp Suite vs OWASP ZAP — Side-by-Side Comparison](#7-burp-suite-vs-owasp-zap--side-by-side-comparison)
8. [Feature Mapping Table](#8-feature-mapping-table)
9. [Practical Workflows](#9-practical-workflows)
   - [Burp Suite Workflow](#91-burp-suite-workflow)
   - [ZAP Workflow](#92-zap-workflow)
   - [Proxy Chaining (Advanced)](#93-proxy-chaining-advanced)
10. [HTTPS Interception & SSL Certificates](#10-https-interception--ssl-certificates)
11. [Common Vulnerabilities Tested](#11-common-vulnerabilities-tested)
12. [Useful Extensions & Add-ons](#12-useful-extensions--add-ons)
13. [Which Tool Should You Use?](#13-which-tool-should-you-use)
14. [Key Takeaways & Study Summary](#14-key-takeaways--study-summary)

---

## 1. What is Web Application Security Testing?

Web Application Security Testing is the practice of probing web applications for security weaknesses before malicious attackers can exploit them. The goals are:

- **Identify vulnerabilities** such as SQL Injection, XSS, CSRF, Broken Access Control
- **Map the application's attack surface** (endpoints, parameters, inputs)
- **Verify that security controls work** as expected
- **Produce reports** that help developers fix issues

**Two testing approaches:**

| Approach | Description |
|---|---|
| **Manual Testing** | A human tester crafts and sends requests to test logic, edge cases, and business flows |
| **Automated Testing** | Tools scan the application systematically for known vulnerability patterns |

Both Burp Suite and ZAP support **both** approaches.

---

## 2. How Web Applications Work

Understanding the client-server model is essential before using proxy tools.

```
Browser (Client)  ←──HTTP/HTTPS──→  Web Server (Backend)
      ↑                                      ↑
  Frontend                              Backend Logic
  HTML, CSS, JS                     Databases, APIs, Auth
```

### Frontend (Client-Side)
- What the user sees in their browser
- Built with **HTML**, **CSS**, **JavaScript**
- Sends HTTP requests to the server

### Backend (Server-Side)
- Processes requests, handles business logic
- Connects to databases
- Returns HTTP responses (HTML, JSON, etc.)

### HTTP Request Anatomy

```
GET /login HTTP/1.1
Host: example.com
Cookie: session=abc123
User-Agent: Mozilla/5.0
```

### HTTP Response Anatomy

```
HTTP/1.1 200 OK
Content-Type: text/html
Set-Cookie: session=newvalue

<html>...</html>
```

---

## 3. What is an Intercepting Proxy?

An **intercepting proxy** sits between your browser and the web server, acting as a **man-in-the-middle**. This allows you to:

- **See** all HTTP/HTTPS traffic in real time
- **Pause** requests before they reach the server
- **Modify** request parameters, headers, cookies, body
- **Replay** requests with different values
- **Analyse** responses for security issues

```
Browser → [PROXY: Burp/ZAP] → Web Server
                ↑
         You control this!
         - Read traffic
         - Edit requests
         - Replay attacks
```

**Default Proxy Ports:**
- Burp Suite: `127.0.0.1:8080`
- OWASP ZAP: `127.0.0.1:8081` (or 8080 depending on config)

---

## 4. Burp Suite — Deep Dive

> Developed by **PortSwigger**. The industry-standard web application pentesting toolkit.

### 4.1 Editions

| Edition | Cost | Key Features |
|---|---|---|
| **Community** | Free | Proxy, Repeater, Decoder, Sequencer, basic Intruder (throttled) |
| **Professional** | ~$399/year | Full Scanner, full Intruder, Collaborator, advanced reporting |
| **Enterprise** | Custom pricing | Automated scanning at scale, team features, CI/CD integration |

---

### 4.2 Core Components

#### 🔵 Dashboard
- Central hub showing all activities, alerts, and scan progress
- Monitors ongoing tasks and displays vulnerabilities found

#### 🔵 Proxy
- Core of Burp Suite — intercepts HTTP/HTTPS traffic
- **Intercept On/Off** toggle to pause or pass traffic
- View and edit requests/responses before forwarding
- All traffic is logged in **HTTP History**

#### 🔵 Target
- **Site Map**: A tree view of all discovered endpoints and resources
- **Scope**: Define which hosts/URLs to include in testing
- Right-click any item → Spider, Scan, Send to Repeater/Intruder

#### 🔵 Spider (Crawler)
- Automatically crawls and maps the application structure
- Discovers hidden endpoints and parameters
- Adapts to complex application architectures
- ⚠️ *Limited in Community Edition — use ZAP proxy chaining to compensate*

#### 🔵 Scanner *(Professional only)*
- Automatically detects a wide range of vulnerabilities
- Includes: SQL Injection, XSS, CSRF, SSRF, XXE, and more
- Provides risk ratings (High / Medium / Low / Info)
- Regularly updated with new vulnerability signatures

#### 🔵 Intruder
- Automates custom attacks against identified parameters
- Supports **payload sets** (wordlists, numbers, dates, custom)
- ⚠️ *Throttled (slow) in Community Edition*

**4 Attack Modes:**

| Mode | Description | Use Case |
|---|---|---|
| **Sniper** | One payload position, one payload list | Testing a single parameter |
| **Battering Ram** | Same payload in ALL positions simultaneously | Credential testing |
| **Pitchfork** | Multiple payload sets, iterated in parallel | Username + Password pairs |
| **Cluster Bomb** | All combinations of multiple payload sets | Full brute-force |

#### 🔵 Repeater
- Manually craft and resend HTTP requests
- See the response immediately
- Ideal for: manually testing SQLi, XSS, authentication flaws
- Chain requests: send output from one request into another

#### 🔵 Decoder
- Encode/decode data in various formats:
  - Base64, URL encoding, HTML entities, Hex, Gzip, etc.
- Useful for analyzing tokens, cookies, and obfuscated data

#### 🔵 Sequencer
- Analyzes the randomness/entropy of session tokens
- Helps determine if tokens are predictable (security flaw)

#### 🔵 Comparer
- Diffs two requests or responses side by side
- Useful for spotting subtle differences in responses (e.g., SQLi blind)

#### 🔵 Extender (BApp Store)
- Install community-created extensions
- Examples: Active Scan++, JS Beautifier, Logger++, Autorize
- Some BApps require Professional edition

---

### 4.3 Setup & Configuration

#### Step 1 — Configure Browser Proxy

In Firefox:
```
Preferences → Network Settings → Manual Proxy Configuration
HTTP Proxy: 127.0.0.1   Port: 8080
☑ Use this proxy server for all protocols
```

Or use the **FoxyProxy** browser extension for easy toggling.

#### Step 2 — Install Burp CA Certificate (for HTTPS)

1. With proxy on, visit: `http://burpsuite` in browser
2. Click **"CA Certificate"** → download `cacert.der`
3. In Firefox: `Preferences → Privacy & Security → Certificates → Import`
4. Trust the cert for **websites**
5. Restart Firefox

Now HTTPS traffic is intercepted without browser warnings.

#### Step 3 — Set Scope

```
Target → Scope → Add [your target domain]
```

This limits scanning/spidering to only your authorized target.

---

### 4.4 Intruder Attack Modes (Detailed)

```
Target URL: POST /login
Body: username=§admin§&password=§password§
                ↑ Position markers
```

| Mode | Payload Behavior | Example |
|---|---|---|
| Sniper | Position 1: payload1, payload2... | Test one field at a time |
| Battering Ram | All positions get same payload | Same value everywhere |
| Pitchfork | Pos1: list1[0], Pos2: list2[0], then [1],[1]... | User:Pass pairs |
| Cluster Bomb | Every combination of list1 × list2 | Full brute force |

---

## 5. OWASP ZAP — Deep Dive

> **Zed Attack Proxy** — Open-source, free, maintained by OWASP Foundation & community volunteers.

### 5.1 Overview

- Formerly known as **OWASP ZAP**, now just **ZAP**
- GitHub Top 1000 project
- Community-driven, free for all editions
- Beginner-friendly but powerful for advanced users
- Acts as a **"manipulator-in-the-middle"** proxy
- Supports scripting in **JavaScript**, **Python**, and **Zest**

---

### 5.2 Core Components

#### 🟢 Dashboard
- Central view of all alerts, scanning activity, and site info
- **Sites Tab**: Tree of all discovered pages/endpoints
- **Alerts Tab**: Security vulnerabilities found, with severity
- **History Tab**: Full log of all HTTP requests/responses

#### 🟢 Proxy (Intercepting)
- Intercepts all HTTP/HTTPS traffic
- **Breakpoints** = ZAP's equivalent of Burp's Intercept
- Set breakpoints to pause and modify requests on the fly
- No need to configure FoxyProxy — ZAP can launch its own browser

#### 🟢 Spider
- Crawls the application by following all discovered hyperlinks
- Automatically adds URLs to the **Scope**
- Searches responses for new links, recursively

#### 🟢 AJAX Spider
- For **JavaScript-heavy / Single Page Applications (SPAs)**
- Uses a real browser engine to render JS and discover dynamic content
- Superior to standard Spider for modern web apps

```
Tools → AJAX Spider → Enter Target URL → Start
```

#### 🟢 Active Scanner (ATTACK Mode)
- **Free** in ZAP (unlike Burp where it's Pro only)
- Actively sends attack payloads to find vulnerabilities
- Tests for: SQLi, XSS, Path Traversal, Command Injection, etc.
- ⚠️ Only run against **authorized targets** — it is an active attack

#### 🟢 Passive Scanner
- Analyzes all traffic passing through the proxy **without sending extra requests**
- Safe to run on any traffic
- Flags issues like: missing security headers, insecure cookies, info disclosure

#### 🟢 Fuzzer
- ZAP's equivalent of Burp Intruder
- **No throttling** — runs at full speed even in the free version
- Supports multiple payload types and generators
- Right-click any request in History → Fuzz → select parameter → add payloads

#### 🟢 Manual Request Editor
- ZAP's equivalent of Burp Repeater
- Manually craft and send HTTP requests
- View and analyze the response

#### 🟢 HUD (Heads-Up Display)
- **Unique to ZAP** — not available in Burp
- Overlays ZAP features directly in the browser window
- Test without switching between browser and tool
- Enable from: `Tools → Options → HUD`

#### 🟢 Automation Framework
- **Unique to ZAP** — control ZAP via a **YAML config file**
- Automate entire security testing workflows
- Integrates into CI/CD pipelines (Jenkins, GitHub Actions, etc.)

```yaml
# Example ZAP Automation YAML
env:
  contexts:
    - name: "My App"
      urls: ["https://example.com"]
jobs:
  - type: spider
  - type: activeScan
  - type: report
    parameters:
      reportFile: zap-report.html
```

#### 🟢 Marketplace (Add-ons)
- Community-contributed plugins, similar to Burp's BApp Store
- Notable add-ons:
  - **Retire.js** — Detects vulnerable JavaScript libraries
  - **GraphQL Support** — Test GraphQL APIs
  - **OpenAPI Support** — Import and test Swagger/OpenAPI specs
  - **JWT Support** — Analyze and attack JWT tokens
  - **OAST Support** — Out-of-band security testing

---

### 5.3 Setup & Configuration

#### Installation on Kali Linux

```bash
# Update repos first
sudo apt update

# Install ZAP
sudo apt install zaproxy

# Or launch directly
zaproxy
```

Or download from: [https://www.zaproxy.org/download/](https://www.zaproxy.org/download/)

#### Launch ZAP

```bash
# From terminal
zaproxy

# Or from Kali menu:
# Applications → Web Application Analysis → ZAP
```

#### Session Management

On startup, ZAP asks: **"Do you want to keep this ZAP session?"**

| Choice | Effect |
|---|---|
| **Yes** | Saves all requests/history to an HSQL database |
| **No** | Temporary session, discarded on exit |

#### Browser Configuration

ZAP can launch its own preconfigured browser — no manual proxy setup needed.

Or configure manually:
```
Manual Proxy: 127.0.0.1   Port: 8080 (or 8081 if changed)
```

---

### 5.4 Key Features Unique to ZAP

| Feature | Description |
|---|---|
| **HUD** | In-browser overlay for ZAP tools |
| **Automation Framework** | YAML-based full automation — great for DevSecOps |
| **AJAX Spider** | Handles JavaScript-heavy SPAs |
| **Fuzzer (unlimited)** | No throttling on the free version |
| **Active Scan (free)** | Full active scanning at no cost |
| **Open Source** | Fully transparent, community audited |

---

## 6. Vulnerability Detection Coverage

| Vulnerability | Burp Suite | ZAP |
|---|---|---|
| SQL Injection | ~96% | ~94% |
| Cross-Site Scripting (XSS) | ~98% | ~96% |
| CSRF | ~99% | ~97% |
| Business Logic Flaws | Manual only | Manual only |
| Path Traversal | ✅ | ✅ |
| Command Injection | ✅ | ✅ |
| SSRF | ✅ | ✅ |
| XXE | ✅ | ✅ |
| Insecure Cookies | ✅ | ✅ |
| Missing Security Headers | ✅ | ✅ |

---

## 7. Burp Suite vs OWASP ZAP — Side-by-Side Comparison

| Category | Burp Suite Community | Burp Suite Pro | OWASP ZAP |
|---|---|---|---|
| **Cost** | Free | ~$399/year | Free forever |
| **Open Source** | ❌ | ❌ | ✅ |
| **Intercepting Proxy** | ✅ | ✅ | ✅ |
| **Passive Scanning** | ✅ | ✅ | ✅ |
| **Active Scanning** | ❌ | ✅ | ✅ (free) |
| **Spider / Crawler** | Limited | Full | ✅ |
| **AJAX Spider** | ❌ | ❌ | ✅ |
| **Intruder / Fuzzer** | Throttled | Full | Full (free) |
| **Repeater** | ✅ | ✅ | ✅ (Manual Editor) |
| **Decoder** | ✅ | ✅ | ✅ (Encoder) |
| **Sequencer** | ✅ | ✅ | ✅ (Token Analysis) |
| **Extensions** | BApp Store | BApp Store | Marketplace |
| **Scripting** | Java | Java | JS, Python, Zest |
| **HUD (In-Browser UI)** | ❌ | ❌ | ✅ |
| **Automation (YAML/CI)** | ❌ | Limited | ✅ Full framework |
| **API Testing** | ✅ | ✅ | ✅ |
| **Reporting** | Basic | Advanced | Basic |
| **Learning Curve** | Steep | Steep | Beginner-friendly |
| **Best For** | Manual pentesting | Professional pentesting | Automation, DevSecOps, learning |

---

## 8. Feature Mapping Table

| Burp Suite Feature | ZAP Equivalent |
|---|---|
| Proxy | Proxy + Breakpoints |
| Spider | Spider + AJAX Spider |
| Scanner | Active Scanner (ATTACK Mode) |
| Intruder | Fuzzer |
| Repeater | Manual Request Editor / Requestor |
| Decoder | Encoder |
| Sequencer | Token Generation and Analysis |
| Comparer | Diff |
| Collaborator | OAST Support Add-on |
| DOM Invader | Eval Villain Add-on |
| BApp Store | Marketplace |
| Project Files | Session Files |
| Extender | Scripts + Marketplace |

---

## 9. Practical Workflows

### 9.1 Burp Suite Workflow

```
Step 1: Setup
├── Install Burp Suite
├── Configure Firefox proxy → 127.0.0.1:8080
└── Install Burp CA certificate for HTTPS

Step 2: Define Scope
├── Target → Scope → Add target URL
└── Enable "Show only in-scope items"

Step 3: Intercept & Browse
├── Proxy → Intercept → ON
├── Browse target in Firefox
└── Forward / Drop / Edit requests

Step 4: Spider the Target
├── Target → Site Map → Right-click host
└── Spider this host (maps all endpoints)

Step 5: Active Scan (Pro) or Manual
├── Right-click → Active Scan
└── Or: Send to Repeater → manually probe

Step 6: Use Intruder for Attacks
├── Send request to Intruder
├── Mark positions (§value§)
├── Choose attack mode
├── Load payload wordlist
└── Start attack → Analyse results

Step 7: Analyze & Report
├── Check Scanner findings
├── Review HTTP History
└── Export report
```

---

### 9.2 ZAP Workflow

```
Step 1: Quick Start (Automated)
├── Launch ZAP
├── Click "Automated Scan"
├── Enter target URL
└── Click "Attack" → ZAP spiders + scans automatically

Step 2: Review Results
├── Alerts Tab → sorted by severity (High/Medium/Low/Info)
├── Sites Tab → explore discovered structure
└── History Tab → review all captured requests

Step 3: Manual Exploration
├── Use built-in browser (ZAP launches it)
├── Browse the application
└── Passive scan runs automatically on all traffic

Step 4: Active Testing
├── Right-click any request → Active Scan
└── Or: Fuzz a parameter → load payloads

Step 5: Use HUD (optional)
└── Enable HUD → test from within the browser

Step 6: Report
└── Report → Generate HTML/XML/JSON Report
```

---

### 9.3 Proxy Chaining (Advanced)

Chain both tools together to get the best of both worlds:

```
Firefox → ZAP (Port 8081) → Burp Suite (Port 8080) → Target Server
```

**Why?** ZAP handles the automated crawling (free AJAX spider) while Burp Suite provides detailed request analysis and manual testing.

**Configuration:**

1. Start Burp Suite on port `8080`
2. Start ZAP on port `8081`
3. In ZAP: `Tools → Options → Network → Upstream Proxy → 127.0.0.1:8080`
4. Configure Firefox to use ZAP: `127.0.0.1:8081`

All traffic now flows: Firefox → ZAP → Burp → Target

---

## 10. HTTPS Interception & SSL Certificates

Both tools intercept HTTPS by acting as a **Certificate Authority (CA)**, dynamically generating certificates for each domain visited.

### Why Browsers Reject These Certificates
Browsers only trust certificates signed by known, pre-installed CAs. Burp/ZAP generate their own, so browsers show security warnings unless you install their CA.

### Burp Suite CA Setup

```
1. With proxy running, go to http://burpsuite in browser
2. Download "CA Certificate" (cacert.der)
3. Firefox: Preferences → Privacy → Certificates → Import
4. Trust for websites ✅
5. Restart Firefox
```

### ZAP CA Setup

```
ZAP handles this more automatically when using its built-in browser.
For manual: Tools → Options → Dynamic SSL Certificates → Generate + Save
Then import into browser the same way as Burp
```

> ⚠️ **Security Warning**: Only install CA certificates from tools you trust. Installing an unknown CA certificate is a significant security risk in production environments.

---

## 11. Common Vulnerabilities Tested

### SQL Injection (SQLi)
Attacker injects SQL code into input fields to manipulate the database.
```
Input: ' OR 1=1 --
Effect: Bypasses login or dumps database
```
**Test with:** Repeater (manual), Intruder (automated), Scanner

### Cross-Site Scripting (XSS)
Attacker injects malicious JavaScript into pages viewed by other users.
```
Input: <script>alert('XSS')</script>
Effect: Steals cookies, redirects users, defacement
```
**Types:** Reflected, Stored, DOM-based

### Cross-Site Request Forgery (CSRF)
Forces authenticated users to unknowingly submit malicious requests.
```
Victim visits attacker's page → browser auto-sends forged request to bank
```

### Broken Access Control
Users can access resources/functions they shouldn't have permission for.
```
Normal user: /user/profile/123
Bypass:      /admin/profile/1  → returns admin data
```

### Insecure Direct Object Reference (IDOR)
A type of access control flaw — directly accessing another user's object.
```
/api/invoice?id=1001  → change to id=1002 → see another user's invoice
```

### Security Misconfigurations
Missing security headers, verbose error messages, default credentials.

---

## 12. Useful Extensions & Add-ons

### Burp Suite BApp Store

| Extension | Purpose |
|---|---|
| **Active Scan++** | Adds extra active scan checks |
| **Logger++** | Advanced request/response logging |
| **Autorize** | Detects authorization bypass issues |
| **JS Beautifier** | Formats/beautifies JavaScript for analysis |
| **Turbo Intruder** | Extremely fast HTTP requests (no throttle) |
| **JWT Editor** | Inspect, modify, attack JWT tokens |

### ZAP Marketplace

| Add-on | Purpose |
|---|---|
| **Retire.js** | Detects outdated/vulnerable JS libraries |
| **GraphQL Support** | Test GraphQL APIs |
| **OpenAPI Support** | Import Swagger specs for testing |
| **JWT Support** | Analyze and attack JWT tokens |
| **OAST Support** | Out-of-band testing (like Burp Collaborator) |
| **Eval Villain** | DOM XSS testing (like Burp DOM Invader) |

---

## 13. Which Tool Should You Use?

### Use **OWASP ZAP** when:
- 💰 You need a **free**, full-featured tool
- 🎓 You are **learning** web security
- ⚙️ You want to **automate** security in CI/CD pipelines
- 🔍 You are testing **JavaScript-heavy SPAs** (AJAX Spider)
- 🏢 Your team has a **limited budget**

### Use **Burp Suite** when:
- 🏆 You are doing **professional penetration testing**
- 🔬 You need **deep manual testing** with fine-grained control
- 📋 You need **advanced vulnerability reports** (Pro)
- 🔗 You are working with **complex business logic** testing
- 👥 You need **collaborative features** for a security team

### Use **Both** (Recommended):
- Learn ZAP first (free, teaches fundamentals)
- Graduate to Burp Suite Pro for professional work
- Use **proxy chaining** to leverage both simultaneously

> "Tools don't find vulnerabilities — skilled testers using tools find vulnerabilities. Master the fundamentals of web security first, then let these tools amplify your capabilities."

---

## 14. Key Takeaways & Study Summary

| Concept | Summary |
|---|---|
| **Intercepting Proxy** | Sits between browser and server; lets you read, pause, modify traffic |
| **Burp Proxy** | Core tool; all other modules receive data from here |
| **Burp Intruder** | Automates payload injection (throttled in free) |
| **Burp Repeater** | Manual request replay and editing |
| **ZAP Active Scanner** | Free alternative to Burp's paid scanner |
| **ZAP Fuzzer** | Free, full-speed alternative to Burp Intruder |
| **ZAP HUD** | In-browser overlay — unique to ZAP |
| **ZAP Automation** | YAML-based CI/CD pipeline integration |
| **Proxy Chaining** | Firefox → ZAP → Burp → Target (best of both worlds) |
| **SSL/TLS** | Install tool's CA cert to intercept HTTPS without warnings |
| **Scope** | Always define scope to stay within authorized testing boundaries |
| **OWASP** | Organization behind ZAP; also publishes OWASP Top 10 vulnerabilities |

---

## 📖 Resources for Further Learning

- [PortSwigger Web Security Academy](https://portswigger.net/web-security) — Free labs using Burp Suite
- [OWASP ZAP Official Docs](https://www.zaproxy.org/docs/)
- [OWASP Top 10](https://owasp.org/www-project-top-ten/) — The 10 most critical web security risks
- [HackTheBox](https://www.hackthebox.com/) — Practice pentesting labs
- [TryHackMe](https://tryhackme.com/) — Guided cybersecurity learning paths
- [VulnWeb](http://testphp.vulnweb.com/) — Legal practice target for Burp/ZAP

---

## ⚠️ Legal & Ethical Disclaimer

> **Only use these tools against systems you own or have explicit written permission to test.**
> Unauthorized scanning or pentesting is illegal and unethical.
> Always practice on dedicated lab environments such as DVWA, HackTheBox, or TryHackMe.

---

*Study Notes compiled from the Burp Suite & OWASP ZAP Proxy video tutorial.*
*Last Updated: 2025*
