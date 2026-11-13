<div align="center">

# 🛡️ OFFENSIVE SECURITY ASSESSMENT REPORT
### External OSINT Footprinting & Active Subnet Discovery
**`W2-PM-FINAL` | Ethical Hacking Internship | Networkwalks**

![Cohort](https://img.shields.io/badge/Cohort-B084-crimson?style=for-the-badge)
![Certification](https://img.shields.io/badge/Certified-ISC2%20CC-0A66C2?style=for-the-badge)
![Status](https://img.shields.io/badge/Phases%201%20%26%202-Completed-success?style=for-the-badge)
![Environment](https://img.shields.io/badge/Environment-Kali%20Linux%20%7C%20Windows%2010-262626?style=for-the-badge)

</div>

---

## 📑 Table of Contents
- [Engagement Metadata](#-engagement-metadata)
- [1. Authorization & Legal Disclaimer](#1-authorization--legal-disclaimer)
- [2. Executive Summary & Scope](#2-executive-summary--scope)
- [3. Technical Arsenal & Utilities](#3-technical-arsenal--utilities)
- [4. Assessment Execution & Technical Findings](#4-assessment-execution--technical-findings)
  - [4.1 Phase 1: External Domain Footprinting (`W2-PM1`)](#41-phase-1-external-domain-footprinting-w2-pm1)
  - [4.2 Phase 2: Local Interface Audit & Zenmap Network Scanning (`W2-PM5`)](#42-phase-2-local-interface-audit--zenmap-network-scanning-w2-pm5)
- [5. Technical Risk & Exposure Analysis](#5-technical-risk--exposure-analysis)
- [6. Remediation & Hardening Recommendations](#6-remediation--hardening-recommendations)
- [7. Conclusion](#7-conclusion)
- [8. Empirical Evidence & Lab Screenshots (Figures 1–13)](#8-empirical-evidence--lab-screenshots-figures-113)
- [Author & Project Information](#-author--project-information)

---

## 📋 Engagement Metadata

| Assessment Parameter | Engagement Details |
| :--- | :--- |
| **Security Analyst (Penetration Tester)** | **Najeem Omokunmi Atanda** (`ISC2 CC`) |
| **Cohort / Track** | `B084` — Networkwalks Cybersecurity & Ethical Hacking |
| **Assessment Date** | `13 November 2026` |
| **Assigned Lab Modules** | • **Module W2-PM1:** Multi-Tool Kali Linux Domain Footprinting<br>• **Module W2-PM5:** Local Subnet & Remote Target Scanning (Zenmap) |
| **Authorized Scope** | 1. `networkwalks.com` & `scanme.nmap.org` *(Authorized External Targets)*<br>2. Local Host & Wireless LAN Subnets (`10.0.0.0/24` & `192.168.43.0/24`) |
| **Rules of Engagement (RoE)** | ✅ Written Authorization Confirmed *(Non-Destructive Recon & Scanning Only)* |
| **Methodology Phases** | • ✅ **Phase 1:** Passive & Semi-Passive Footprinting *(Completed)*<br>• ✅ **Phase 2:** Active Network Scanning & Topology Mapping *(Completed)*<br>• ⏳ **Phases 3–5:** Vulnerability Validation, Exploitation & Post-Exploitation *(Scheduled)* |

---

## 1. Authorization & Legal Disclaimer

> [!CAUTION]
> **STRICT AUTHORIZATION & LEGAL COMPLIANCE:**
> All reconnaissance queries, DNS enumerations, and active network scans documented in this report were executed strictly against lab environments that I personally own and administer or against external domains (`networkwalks.com` and `scanme.nmap.org`) where explicit written permission was granted for educational testing.
>
> The methodologies and evidence presented herein are intended solely for defensive research and ethical hacking training. Unauthorized scanning or intrusion against third-party systems without prior consent violates computer misuse statutes and carries severe legal and professional penalties.

---

## 2. Executive Summary & Scope

This technical assessment report presents the findings from two core offensive security engagements completed during **Week 2** of the **Networkwalks Ethical Hacking Internship**:
1. **External Surface Footprinting** of `networkwalks.com` (**Module W2-PM1**)
2. **Multi-Subnet Active Network Discovery** using Zenmap (**Module W2-PM5**)

Together, these exercises illustrate the operational transition from open-source intelligence (OSINT) collection to active host enumeration and topology mapping. External footprinting utilities were executed from a **Kali Linux** virtual machine, while local interface verification and graphical Nmap scans were conducted from a **Windows** workstation (`Devarotech` / `DESKTOP-CFKFD73`).

Each activity section below documents the exact command syntax executed, the raw technical telemetry captured, its tactical significance to an adversary, and timestamped screenshot verification.

---

## 3. Technical Arsenal & Utilities

The table below summarizes the operating environments and specialized reconnaissance utilities deployed during this engagement:

| Utility / Command | Operational Role in Assessment |
| :--- | :--- |
| **Kali Linux & Windows 10** | Attacker workstation environments used for CLI footprinting and GUI network scanning. |
| `whois` | Query ICANN/VeriSign registry databases for registrar metadata, domain lifecycle dates, and NS records. |
| `whatweb` | Fingerprint HTTP server banners, CMS core versions, active plugins, and frontend JavaScript frameworks. |
| `nslookup` | Query recursive DNS resolvers (`8.8.8.8#53`) to map the target FQDN to its host IPv4 address. |
| `curl -I` | Send an HTTP `HEAD` request over TLS (`HTTP/2`) to inspect server headers, cookies, and REST API links. |
| `wafw00f` | Transmit crafted HTTP probes to detect and fingerprint Web Application Firewall (WAF) appliances. |
| `dnsrecon` | Perform automated DNS record enumeration (`SOA`, `NS`, `MX`, `A`, `SPF/TXT`, `SRV`) and BIND version checks. |
| **Zenmap (Nmap v7.98 GUI)** | Execute Fast Scans (`-T4 -F`), Intense Scans (`-A -v -Pn`), and interactive Fisheye network topology mapping. |
| `ipconfig` & `ipconfig /all` | Enumerate local Windows NIC profiles, MAC hardware addresses, DHCP leases, and subnet masks. |

---

## 4. Assessment Execution & Technical Findings

### 4.1 Phase 1: External Domain Footprinting (`W2-PM1`)

To construct an external threat profile of `networkwalks.com`, I executed a six-stage reconnaissance workflow on Kali Linux utilizing `whois`, `whatweb`, `nslookup`, `curl`, `wafw00f`, and `dnsrecon`. Each utility extracted specific infrastructure intelligence without triggering intrusive exploit payloads.

#### 1. Domain Registry Intelligence (`whois networkwalks.com`)
```bash
whois networkwalks.com
```
* **Registrar & Registry ID:** Querying the public WHOIS database confirmed that the domain is registered via **GoDaddy.com, LLC** (Registry Domain ID: `2452319255_DOMAIN_COM-VRSN`, Registrar IANA ID: `146`, Registrar WHOIS Server: `whois.godaddy.com`).
* **Domain Lifecycle Dates:** Created on `2019-11-06T22:51:46Z`, updated on `2025-11-12T10:08:43Z`, and expiring on `2027-11-06T22:51:46Z`.
* **Authoritative Name Servers & DNSSEC:** Exposed `NS6135.HOSTGATOR.COM` and `NS6136.HOSTGATOR.COM`, domain lock statuses (`clientDeleteProhibited`, `clientRenewProhibited`, `clientTransferProhibited`, `clientUpdateProhibited`), and indicated that **`DNSSEC` is currently `unsigned`**.

#### 2. Web Stack Fingerprinting (`whatweb networkwalks.com`)
```bash
whatweb networkwalks.com
```
* **HTTP-to-HTTPS Redirection:** WhatWeb followed the initial `301 Moved Permanently` redirect from `http://networkwalks.com` to `https://networkwalks.com/` (`200 OK`) and fingerprinted an **Apache** web server hosted in the United States (`192.232.216.135`).
* **Technology Stack & Metadata:** The `MetaGenerator` header disclosed **WordPress 7.0.4** alongside **WordPress Download Manager 3.3.58**, `Bootstrap 7.0.4`, `JQuery 3.7.1`, `HTML5`, `Open-Graph-Protocol`, `HttpOnly` cookie `__wpdm_client`, page title `Networkwalks Academy`, and contact email `info@networkwalks.com`.

#### 3. Forward DNS Resolution (`nslookup networkwalks.com`)
```bash
nslookup networkwalks.com
```
* **DNS Resolution Telemetry:** Using Google's public recursive DNS resolver (`8.8.8.8#53`), a non-authoritative forward lookup resolved `networkwalks.com` directly to its primary IPv4 host address: **`192.232.216.135`**.

#### 4. HTTP Response Header Inspection (`curl -I https://networkwalks.com`)
```bash
curl -I [https://networkwalks.com](https://networkwalks.com)
```
* **Protocol & Server Headers:** Fetching only the HTTP/2 response headers on `Mon, 09 Nov 2026 17:55:41 GMT` confirmed an `HTTP/2 200` status from `server: Apache` (`content-type: text/html; charset=UTF-8`, `referrer-policy: no-referrer-when-downgrade`).
* **Caching, Cookies & REST API Exposure:** Revealed caching layers (`x-endurance-cache-level: 0` and `x-nginx-cache: WordPress`), a session cookie (`__wpdm_client=09520658c9e2ec881c9ff314aadd9e89; path=/; domain=networkwalks.com; secure; HttpOnly`), and the live WordPress REST API endpoint at `https://networkwalks.com/wp-json/` (`/wp-json/wp/v2/pages/53`).

#### 5. Web Application Firewall Detection (`wafw00f networkwalks.com`)
```bash
wafw00f networkwalks.com
```
* **WAF Fingerprinting Output:** Using the **WAFW00F v2.4.2** fingerprinting toolkit, two HTTP requests were dispatched to `https://networkwalks.com`, confirming that the application is actively protected behind a **ModSecurity (SpiderLabs) WAF**.

#### 6. Comprehensive DNS Enumeration (`dnsrecon -d networkwalks.com`)
```bash
dnsrecon -d networkwalks.com
```
Executed on `2026-11-10T05:52:51-0500`, DNSRecon enumerated **8 core records** and confirmed `ERROR No answer for DNSSEC query for networkwalks.com`:
* **SOA & NS Records:** `ns6135.hostgator.com` (`50.87.144.87`) and `ns6136.hostgator.com` (`192.232.216.131`), plus exposed the underlying DNS software version: **`Bind Version "9.16.23-RH"`** on both name servers.
* **MX & A Records:** `mail.networkwalks.com` and `networkwalks.com` both resolving to **`192.232.216.135`**.
* **TXT & SPF Policies:**
  * SPF Policy: `v=spf1 +a +mx +ip4:50.87.144.87 +include:websitewelcome.com ~all`
  * Verification Token: `google-site-verification=rr04eRmqHoWY3XemnizDNVK4q75X-Ij-mjgEeg-UsYI`
* **SRV Records:** Eight `_autodiscover._tcp.networkwalks.com` records pointing to `cpanelemaildiscovery.cpanel.net` on port `443` across `184.94.203.9`, `184.94.203.11`, `184.94.203.14`, `184.94.203.15`, `184.94.204.9`, `184.94.204.11`, `184.94.204.14`, and `184.94.204.15`.

---

### 4.2 Phase 2: Local Interface Audit & Zenmap Network Scanning (`W2-PM5`)

For the second module, I performed host configuration auditing via Windows Command Prompt (`ipconfig` / `ipconfig /all`) followed by active network scanning and topology generation using **Zenmap (Nmap GUI)** across my local lab environment, my active wireless LAN subnet, and the authorized external Nmap test server.

#### 1. Local Interface & Hardware MAC Identification (`ipconfig /all`)
```cmd
ipconfig
ipconfig /all
```
Prior to launching active scans, I audited two network environments on my Windows system:
* **Lab Ethernet Environment (Host: `DESKTOP-CFKFD73`):**
  * **Network Adapter:** `Intel(R) PRO/1000 MT Desktop Adapter`
  * **Physical MAC Address:** `08-00-27-F6-B5-08`
  * **IPv4 Address & Mask:** `10.0.0.10` (`255.255.255.0` / `/24` subnet)
  * **Default Gateway & DNS:** Gateway `10.0.0.1` | DNS Resolvers `8.8.8.8` and `1.1.1.1` (`DHCP Enabled: No`, `NetBIOS over Tcpip: Enabled`)
* **Wireless LAN Environment (Host: `Devarotech`):**
  * **Network Adapter:** `Intel(R) Wi-Fi 6E AX211 160MHz`
  * **Physical MAC Address:** `A0-59-50-15-05-72` *(plus disconnected Ethernet adapter `Intel(R) Ethernet Connection (16) I219-LM` with MAC `08-92-04-49-58-09`)*
  * **IPv4 Address & Mask:** `192.168.43.27` (`255.255.255.0` / `/24` subnet)
  * **Default Gateway / DHCP / DNS:** `192.168.43.132` (`DHCP Enabled: Yes`, Lease obtained `Thursday, 12 November 2026 21:40:21`, Expires `23:10:31`)

#### 2. Active Subnet Discovery & Port Enumeration in Zenmap
```bash
nmap -T4 -F 10.0.0.10
nmap -T4 -F 192.168.43.27/24
```
* **Local Lab Host Quick Scan (`nmap -T4 -F 10.0.0.10`):** Completed in `76.19 seconds` (`97 closed tcp ports (reset)`), revealing **3 open Windows networking ports**:
  * `135/tcp` (`open`) — `msrpc`
  * `139/tcp` (`open`) — `netbios-ssn`
  * `445/tcp` (`open`) — `microsoft-ds`
* **Wireless LAN Subnet Quick Scan (`nmap -T4 -F 192.168.43.27/24`):** Discovered three live hosts on the `192.168.43.0/24` subnet alongside the virtual lab host:

| Host IPv4 Address | Hostname / Role | Hardware MAC Address | Discovered Port Exposure |
| :--- | :--- | :--- | :--- |
| **`192.168.43.27`** | Local Workstation (`Devarotech`) | `A0-59-50-15-05-72` | 3–6 open ports *(Yellow node)* |
| **`192.168.43.132`** | Wireless Default Gateway / DHCP / DNS Server | Subnet Gateway | Fewer than 3 open ports *(Green node)* |
| **`192.168.43.205`** | Active Peer Host on Wireless LAN Subnet | LAN Peer | Fewer than 3 open ports *(Green node)* |
| **`10.0.0.10`** | Local Virtual Lab Interface (`DESKTOP-CFKFD73`) | `08-00-27-F6-B5-08` | `135/tcp`, `139/tcp`, `445/tcp` open |

#### 3. Interactive Topology Visualization
* After completing the `192.168.43.27/24` subnet scan, I opened the **Topology** tab in Zenmap, configured the **Fisheye** ring view (`Fisheye on ring: 1.00`, `interest factor: 2.00`, `spread factor: 0.50`), displayed the interactive **Topology Legend** window, and exported the network map showing `localhost` connected to `192.168.43.132`, `192.168.43.205`, and `Devarotech (192.168.43.27)`.

#### 4. External Traceroute & Intense Scan Validation (`nmap -T4 -A -v -Pn scanme.nmap.org`)
```bash
nmap -T4 -A -v -Pn scanme.nmap.org
```
* **Scan Metrics:** Executed an *Intense scan, no ping* against `scanme.nmap.org` (`45.33.32.156`) in **`40.29 seconds`**, sending `1,096` raw packets (`50.654 KB`) and receiving `1,133` packets (`50.100 KB`).
* **25-Hop International Traceroute:** Mapped the complete network path from my local gateway (`192.168.43.205` → `10.227.237.3`) through Airtel Africa / NTT London & US backbone routers (`ae-0.airtel-africa.londen14.uk.bb.gin.ntt.net` [`83.231.181.219`]) and Akamai transit nodes (`ae9.r21.iad02.mag.netarch.akamai.com` → `ae22.gw3-scz1.netarch.akamai.com`) to the destination server (`scanme.nmap.org` [`45.33.32.156`] at `316.00 ms`).

> *All local and external scan outputs above reflect genuine empirical telemetry captured directly from my workstation (`Devarotech`) during the lab session.*

---

## 5. Technical Risk & Exposure Analysis

**Risk Level Key:** 🔴 **Critical** | 🟠 **Medium** | 🟡 **Low**

| ID | Technical Finding | Empirical Evidence Observed | Adversarial Impact / Risk | Severity |
| :---: | :--- | :--- | :--- | :---: |
| **1** | **CMS, Plugin & BIND Versions Disclosed** | WhatWeb identified `WordPress 7.0.4` & `WP Download Manager 3.3.58`; DNSRecon exposed `BIND 9.16.23-RH` | Enables attackers to cross-reference exact software versions against public CVE exploit databases. | 🟠 **Medium** |
| **2** | **DNSSEC Not Enabled & cPanel SRV Exposed** | WHOIS and DNSRecon confirmed `DNSSEC` is unsigned; 8 `_autodiscover._tcp` SRV records point to cPanel | Lack of cryptographic DNS signing increases cache-poisoning risk; SRV records reveal hosting control panel architecture. | 🟠 **Medium** |
| **3** | **WordPress REST API & Server Headers Exposed** | `curl -I` exposed `/wp-json/wp/v2/pages/53`, `server: Apache`, and `x-nginx-cache: WordPress` | Unrestricted `/wp-json/` endpoints can facilitate automated content or user enumeration if not rate-limited. | 🟡 **Low** |
| **4** | **Origin Host IPv4 & Mail Server Co-Located** | `nslookup` and DNSRecon mapped both `networkwalks.com` (`A`) and `mail.networkwalks.com` (`MX`) to `192.232.216.135` | Reveals that web and mail services share a single IPv4 host, allowing targeted port scanning against the origin server. | 🟡 **Low** |
| **5** | **WAF Vendor Fingerprinted** | `wafw00f` identified `ModSecurity (SpiderLabs)` WAF in 2 requests | While the WAF actively protects the site, knowing the exact vendor allows attackers to research rule-specific bypasses. | 🟡 **Low** |
| **6** | **Active Hosts & SMB/NetBIOS Ports Open on LAN** | Zenmap discovered 3 live nodes on `192.168.43.0/24` and open ports `135, 139, 445/tcp` on `10.0.0.10` | Exposed MSRPC (`135`), NetBIOS (`139`), and SMB (`445`) services on local interfaces increase lateral movement risk if unpatched. | 🟠 **Medium** |

> [!NOTE]
> **Scope Note:**
> The findings cataloged above represent reconnaissance intelligence and attack-surface exposures rather than exploited vulnerabilities. Because Modules `W2-PM1` and `W2-PM5` were strictly scoped to **Phase 1 (Footprinting)** and **Phase 2 (Scanning)**, no intrusive vulnerability exploitation or payload delivery was conducted. Confirming whether exposed versions (such as `WordPress 7.0.4`, `BIND 9.16.23-RH`, or SMB port `445`) are vulnerable requires dedicated **Phase 3** vulnerability validation under formal authorization.

---

## 6. Remediation & Hardening Recommendations

To reduce the external and internal attack surfaces identified during this assessment, I recommend implementing the following defensive controls:

1. **Suppress Version Banners & Meta Generators**
   Configure Apache (`ServerTokens Prod`), BIND (`version "not disclosed"`), and WordPress to strip version numbers from HTML meta tags and DNS chaos queries.
2. **Maintain Continuous Patch Management**
   Ensure WordPress core (`7.0.4`), WP Download Manager (`3.3.58`), and server packages are continuously updated against vendor security advisories.
3. **Restrict WordPress REST API & Sanitize Headers**
   Restrict unauthenticated access to `/wp-json/wp/v2/` endpoints where public API consumption is unnecessary, and strip internal cache headers (`x-endurance-cache-level`).
4. **Implement DNSSEC & Harden SPF Policies**
   Enable cryptographic DNSSEC signing on the domain zone and consider tightening the SPF record from SoftFail (`~all`) to HardFail (`-all`) once all legitimate mail senders are verified.
5. **Maintain & Tune ModSecurity WAF Rulesets**
   Keep the ModSecurity (SpiderLabs) WAF active with updated OWASP Core Rule Set (CRS) signatures and monitor alert logs for reconnaissance probing.
6. **Restrict Local Windows SMB & NetBIOS Services**
   Configure Windows Defender Firewall on local workstations (`10.0.0.10` / `192.168.43.27`) to block inbound TCP ports `135` (MSRPC), `139` (NetBIOS), and `445` (SMB) on untrusted networks.
7. **Enforce Routine Subnet Discovery & NAC**
   Conduct scheduled Nmap/Zenmap audits across internal subnets (`192.168.43.0/24`) to verify connected MAC addresses and detect unauthorized rogue devices.
8. **Maintain Accurate Network Topology Baselines**
   Archive up-to-date Zenmap topology maps and DHCP lease tables so security teams can immediately spot anomalous routing hops or unfamiliar endpoints.
9. **Enforce Strict Rules of Engagement (RoE)**
   Ensure all future vulnerability scanning and penetration testing activities remain strictly within documented, written client authorization.

---

## 7. Conclusion

Completing **Week 2** of the **Networkwalks Cybersecurity & Ethical Hacking Internship** provided end-to-end practical experience in external OSINT footprinting (`W2-PM1`) and active network discovery (`W2-PM5`).

During **Phase 1**, combining `whois`, `whatweb`, `nslookup`, `curl -I`, `wafw00f`, and `dnsrecon` allowed me to map the target's registrar history, Apache/WordPress software stack, ModSecurity WAF defense, `BIND 9.16.23-RH` DNS infrastructure, and cPanel autodiscover SRV records without sending a single malicious exploit.

During **Phase 2**, combining Windows `ipconfig /all` audits with **Zenmap** enabled me to identify my local hardware MAC addresses, enumerate open MSRPC/NetBIOS/SMB ports on `10.0.0.10`, map three active nodes across my `192.168.43.27/24` wireless subnet using Fisheye topology diagrams, and trace a 25-hop international route to `scanme.nmap.org`.

Ultimately, this engagement demonstrated that disciplined reconnaissance is the cornerstone of offensive and defensive security—providing the empirical visibility required to harden systems before adversaries can exploit them.

---

## 8. Empirical Evidence & Lab Screenshots (Figures 1–13)

### Figure 1: Domain Registry Lookup — `whois networkwalks.com`
* **Command:** `whois networkwalks.com`
* **Key Telemetry Verified:** Registrar `GoDaddy.com, LLC` (IANA ID `146`), Domain ID `2452319255_DOMAIN_COM-VRSN`, Created `2019-11-06`, Expires `2027-11-06`, Name Servers `NS6135.HOSTGATOR.COM` & `NS6136.HOSTGATOR.COM`, `DNSSEC: unsigned`.
* **Screenshot Evidence:** `screenshots/01-whois.png`
<img width="273" height="197" alt="whois-cmd" src="https://github.com/user-attachments/assets/68d6e58d-4f4c-42be-b321-0fb763fd63cb" />

---

### Figure 2: Web Technology Fingerprinting — `whatweb networkwalks.com`
* **Command:** `whatweb networkwalks.com`
* **Key Telemetry Verified:** `301 Moved Permanently` → `200 OK`, `Apache`, `IP[192.232.216.135]`, `Country[UNITED STATES][US]`, `WordPress[7.0.4]`, `WordPress Download Manager 3.3.58`, `Bootstrap[7.0.4]`, `JQuery[3.7.1]`, `Email[info@networkwalks.com]`.
* **Screenshot Evidence:** `screenshots/02-whatweb.png`
<img width="279" height="171" alt="whatweb -cmd" src="https://github.com/user-attachments/assets/8b2a3213-e226-4cb4-9f8c-d4fb1f49cdc6" />

---

### Figure 3: Forward DNS Resolution — `nslookup networkwalks.com`
* **Command:** `nslookup networkwalks.com`
* **Key Telemetry Verified:** Resolver `8.8.8.8#53` returned non-authoritative answer `Name: networkwalks.com` → `Address: 192.232.216.135`.
* **Screenshot Evidence:** `screenshots/03-nslookup.png`
<img width="123" height="75" alt="nslookup-cmd" src="https://github.com/user-attachments/assets/59e6ab6a-5d70-4ebb-99ff-17bcba18e9b5" />

---

### Figure 4: HTTP/2 Response Header Inspection — `curl -I https://networkwalks.com`
* **Command:** `curl -I https://networkwalks.com`
* **Key Telemetry Verified:** `HTTP/2 200`, `server: Apache`, `date: Mon, 09 Nov 2026 17:55:41 GMT`, `x-endurance-cache-level: 0`, `x-nginx-cache: WordPress`, `set-cookie: __wpdm_client=...; secure; HttpOnly`, and REST API link `https://networkwalks.com/wp-json/wp/v2/pages/53`.
* **Screenshot Evidence:** `screenshots/04-curl-headers.png`
<img width="277" height="145" alt="curl -I cmd" src="https://github.com/user-attachments/assets/2fa31739-5d67-4418-9e63-48a03f2957da" />

---

### Figure 5: WAF Fingerprinting — `wafw00f networkwalks.com`
* **Command:** `wafw00f networkwalks.com`
* **Key Telemetry Verified:** WAFW00F `v2.4.2` confirmed `https://networkwalks.com` is behind **ModSecurity (SpiderLabs) WAF** (`Number of requests: 2`).
* **Screenshot Evidence:** `screenshots/05-wafw00f.png`
<img width="277" height="149" alt="wafw00f-cmd" src="https://github.com/user-attachments/assets/911c60a8-9a88-4a7d-b070-cb72d65e95f5" />

---

### Figure 6: Comprehensive DNS Record Enumeration — `dnsrecon -d networkwalks.com`
* **Command:** `dnsrecon -d networkwalks.com`
* **Key Telemetry Verified:** `SOA` & `NS` records on `50.87.144.87` & `192.232.216.131`, `Bind Version "9.16.23-RH"`, `MX` (`mail.networkwalks.com`) & `A` (`192.232.216.135`), SPF `TXT` record (`~all`), Google site verification token, and 8 `_autodiscover._tcp` `SRV` records pointing to `cpanelemaildiscovery.cpanel.net:443`.
* **Screenshot Evidence:** `screenshots/06-dnsrecon.png`
<img width="409" height="255" alt="dnsrecon-cmd" src="https://github.com/user-attachments/assets/88aaef7a-b975-4874-9e8e-4116b7c976f4" />

---

### Figure 7: Local Lab Interface Configuration (`DESKTOP-CFKFD73`) — `ipconfig & ipconfig /all (10.0.0.10/24)`
* **Command:** `ipconfig` & `ipconfig /all`
* **Key Telemetry Verified:** Host Name `DESKTOP-CFKFD73`, `Intel(R) PRO/1000 MT Desktop Adapter`, IPv4 `10.0.0.10`, Subnet Mask `255.255.255.0`, Default Gateway `10.0.0.1`, DNS `8.8.8.8` / `1.1.1.1`.
* **Screenshot Evidence:** `screenshots/07-ipconfig-lab.png`
<img width="447" height="297" alt="ipconfig_all" src="https://github.com/user-attachments/assets/9d9ff62b-3365-4b69-9700-c1dcbb15e48d" />

---

### Figure 8: Extended Local NIC & MAC Verification — `ipconfig /all (08-00-27-F6-B5-08)`
* **Command:** `ipconfig /all`
* **Key Telemetry Verified:** Physical MAC Address `08-00-27-F6-B5-08`, `DHCP Enabled: No`, `Autoconfiguration Enabled: Yes`, `NetBIOS over Tcpip: Enabled`.
* **Screenshot Evidence:** `screenshots/08-ipconfig-mac.png`
<img width="351" height="317" alt="Ipconfig window" src="https://github.com/user-attachments/assets/dc15a442-2b9e-44f7-b009-8a0d70b36ed5" />

---

### Figure 9: Workstation Host & Adapter Verification (`Devarotech`) — `ipconfig /all`
* **Command:** `ipconfig /all`
* **Key Telemetry Verified:** Host Name `Devarotech`, `Intel(R) Ethernet Connection (16) I219-LM` (`08-92-04-49-58-09`, Media disconnected) and `Microsoft Wi-Fi Direct Virtual Adapter` (`A0-59-50-15-05-73`).
* **Screenshot Evidence:** `screenshots/09-ipconfig-host.png`
<img width="762" height="408" alt="Ipcong-Host" src="https://github.com/user-attachments/assets/18ac1de9-9a69-4f05-b591-4363ff53e24a" />

---

### Figure 10: Active Wireless LAN Adapter Configuration — `ipconfig /all (192.168.43.27/24)`
* **Command:** `ipconfig /all`
* **Key Telemetry Verified:** `Intel(R) Wi-Fi 6E AX211 160MHz`, Physical MAC `A0-59-50-15-05-72`, IPv4 `192.168.43.27`, Subnet Mask `255.255.255.0`, Gateway / DHCP / DNS `192.168.43.132`.
* **Screenshot Evidence:** `screenshots/10-ipconfig-wifi.png`
<img width="763" height="404" alt="ipconfig-2" src="https://github.com/user-attachments/assets/446355a6-6d58-42d5-bedb-4d758b8d5dc5" />

---

### Figure 11: Zenmap Local Host Quick Scan & Open Port Discovery — `nmap -T4 -F 10.0.0.10`
* **Command:** `nmap -T4 -F 10.0.0.10`
* **Key Telemetry Verified:** Nmap `7.98` Quick Scan discovered 1 live host (`10.0.0.10`, `0.0010s latency`) with open ports `135/tcp` (`msrpc`), `139/tcp` (`netbios-ssn`), and `445/tcp` (`microsoft-ds`).
* **Screenshot Evidence:** `screenshots/11-zenmap-localhost.png`
<img width="852" height="382" alt="Ip-ZenMAP" src="https://github.com/user-attachments/assets/6ed251b8-0d7d-42f4-927a-d8f118efb4ea" />

---

### Figure 12: Zenmap Wireless Subnet Scan & Fisheye Topology Map — `nmap -T4 -F 192.168.43.27/24`
* **Command:** `nmap -T4 -F 192.168.43.27/24`
* **Key Telemetry Verified:** Fisheye topology view (`interest factor: 2.00`, `spread factor: 0.50`) + Topology Legend mapping `localhost` to `Devarotech (192.168.43.27)` (yellow node, 3–6 open ports), `192.168.43.132` (green node), and `192.168.43.205` (green node).
* **Screenshot Evidence:** `screenshots/12-zenmap-topology.png`
<img width="405" height="331" alt="zenMap-Topology" src="https://github.com/user-attachments/assets/6d8fb6cb-4a05-47a7-a989-213f6854e7b5" />

---

### Figure 13: Zenmap External Intense Scan & 25-Hop Traceroute — `nmap -T4 -A -v -Pn scanme.nmap.org`
* **Command:** `nmap -T4 -A -v -Pn scanme.nmap.org`
* **Key Telemetry Verified:** Full 25-hop traceroute from `192.168.43.205` (`2.00 ms`) across Airtel Africa / NTT London (`ae-0.airtel-africa.londen14.uk.bb.gin.ntt.net`) and Akamai nodes to `scanme.nmap.org` (`45.33.32.156` at `316.00 ms`), completed in `40.29 seconds`.
* **Screenshot Evidence:** `screenshots/13-zenmap-traceroute.png`
<img width="302" height="303" alt="ZenMap-cmd--Win-Ip" src="https://github.com/user-attachments/assets/5d3c836f-b910-4c0d-8701-1d39ad1a125f" />

---

## 👨‍💻 Author & Project Information

* **Author:** **Najeem Omokunmi Atanda** (`ISC2 CC`, `Google Cybersecurity`)
* **Role:** Penetration Tester & SOC Analyst | **Cohort B084** (`Devarotech`)
* **Email:** `devarotech@gmail.com`
* **GitHub:** [github.com/Devarotech](https://github.com/Devarotech)
* **Program:** Cybersecurity program at **Networkwalks** | **Week:** `02` | **Repository:** GitHub
