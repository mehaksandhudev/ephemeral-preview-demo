# Ephemeral Inc. — Terms of Service & Acceptable Use Policy (AUP)

**Last Updated:** October 12, 2026  
**Effective Date:** October 12, 2026  
**Version:** 3.0 (Enterprise Comprehensive Edition)

---

### IMPORTANT NOTICE — PLEASE READ CAREFULLY
THESE TERMS OF SERVICE AND ACCEPTABLE USE POLICY ("TERMS", "AGREEMENT") GOVERN YOUR ACQUISITION, ACCESS, AND USE OF THE SERVICES OPERATED BY EPHEMERAL INC. AND ITS PRINCIPALS ("EPHEMERAL", "WE", "US", OR "OUR"). 

BY ACCESSING, BROWSING, CONNECTING A GITHUB REPOSITORY TO, REGISTERING AN ACCOUNT WITH, EXECUTING CONTAINERS ON, OR MAKING PAYMENTS TOWARDS THE EPHEMERAL PLATFORM (ACCESSIBLE VIA EPHEMERAL.DEV, EPHEMERAL.PREVIEW, ITS APIS, ITS GITHUB APP INTEGRATIONS, OR ASSOCIATED DOMAINS), YOU ("USER", "CUSTOMER", "YOU") UNCONDITIONALLY AGREE TO BE BOUND BY EVERY CLAUSE, LIMITATION, AND COVENANT CONTAINED HEREIN. 

IF YOU DO NOT AGREE UNCONDITIONALLY TO THESE TERMS, YOU HAVE NO LEGAL RIGHT OR PERMISSION TO ACCESS, PROVISION, OR OPERATE PREVIEW ENVIRONMENTS THROUGH THE SERVICE AND MUST IMMEDIATELY DISCONNECT THE EPHEMERAL GITHUB APPLICATION AND DISCONTINUE ALL USAGE.

---

## 1. NATURE OF THE SERVICE & INFRASTRUCTURE DISCLAIMER

### 1.1 Scope of Service
Ephemeral provides an automated cloud orchestration platform that ingests webhook events from Git hosting providers (specifically GitHub) and programmatically provisions isolated, ephemeral container stacks (Docker Compose environments), dynamic host port mapping, reverse proxy ingress, and encrypted secure tunnels (via Cloudflare edge routing) to enable live functional preview testing of software pull requests and commit branches.

### 1.2 Ephemeral & Non-Production Nature
You expressly acknowledge, understand, and agree that:
1. **Zero Production Use:** The Service is engineered strictly for non-critical, transient developer testing, design verification, and QA review. The Service is NEVER designed, warranted, or intended to serve as a production hosting environment, long-term stateful database store, mission-critical infrastructure, or high-availability hosting service.
2. **Transient Lifecycles:** Environments provisioned through Ephemeral are temporary by design. Environments are subject to immediate, unannounced teardown upon pull request closure, merge, explicit user command, automated idle timeout expiration, maximum Time-To-Live (TTL) enforcement (e.g., 4-hour max on Spark, 45-minute idle limit on Vapor), host resource reclamation, or system maintenance.
3. **No Stateful Durability Guarantee:** Ephemeral does NOT provide data durability, persistent volume backup, disaster recovery, or database replication for workloads provisioned within preview containers. You agree that any data written to ephemeral container filesystems or mounted volumes will be irreversibly erased upon container termination (`docker compose down -v --rmi local`), and Ephemeral shall bear zero liability for any lost preview data.

---

## 2. ELIGIBILITY, REGISTRATION & ACCOUNT INTEGRITY

### 2.1 Legal Capacity
You represent and warrant that you are at least eighteen (18) years of age (or the age of legal majority in your jurisdiction) and possess full legal capacity and corporate authority to bind yourself or the legal entity on whose behalf you access the Service.

### 2.2 Account Credentials & Security
1. **Authentication Integrity:** You must register using authentic credentials (email and password, verified Supabase token, Google OAuth, or GitHub App authorization). You are solely and exclusively responsible for safeguarding your login credentials, API access tokens, and GitHub OAuth grants.
2. **Anti-Abuse & Disposable Email Prohibition:** To protect the cluster against resource exhaustion and spam bots, registration using temporary, burner, disposable, or throwaway email domains (including but not limited to Mailinator, 10MinuteMail, Temp-Mail, GuerrillaMail, Yopmail, SharkLasers, and over 80 blacklisted services) is strictly prohibited. Any account detected utilizing disposable addresses or obfuscation techniques will be terminated immediately without notice.
3. **Host Liability for Tenant Actions:** You assume full civil, criminal, and financial responsibility for all activities, code builds, container workloads, network requests, and API queries initiated under your account or by any authorized member of your team organization.

---

## 3. STRICT ACCEPTABLE USE POLICY (AUP) — PROHIBITED WORKLOADS

You covenant, warrant, and agree that you shall NOT, and shall not permit any employee, contractor, collaborator, or third party to, utilize Ephemeral preview environments, virtual networks, compute instances, or tunnel ingress for any of the following prohibited workloads:

### 3.1 Cryptocurrency Mining & Distributed Compute Exploits
- Executing, compiling, running, or facilitating any proof-of-work (PoW), proof-of-space, or cryptographic asset mining software (including but not limited to Monero, Bitcoin, Ethereum miners, XMRig, CGMiner, or browser-based mining scripts).
- Participating in distributed computing projects, hash-cracking farms, or cryptocurrency node validation without explicit prior written authorization from Ephemeral.

### 3.2 Network Attacks, Penetration Testing & Malicious Traffic
- Initiating, conducting, or facilitating denial-of-service (DoS), distributed denial-of-service (DDoS), SYN flooding, UDP amplification, or ping floods against any internal host or external network.
- Executing port scanning, vulnerability fuzzing, banner grabbing, network sniffing, brute-force credential stuffing, or unauthorized penetration testing against any server, cluster, or internet endpoint.
- Operating open proxies, Tor exit nodes, anonymizing relays, VPN gateways, or reverse tunneling proxies intended to conceal originating network traffic.

### 3.3 Malware, Botnets & Cyber Warfare
- Hosting, compiling, transmitting, executing, or distributing computer viruses, worms, Trojan horses, rootkits, ransomware, spyware, adware, keyloggers, remote access trojans (RATs), or any code designed to disrupt, damage, or compromise computer systems.
- Establishing command-and-control (C2) servers, botnet coordination nodes, exploit kit hosting, or web shell infrastructure.

### 3.4 Phishing, Fraud & Deceptive Hosting
- Creating or hosting cloned banking portals, fraudulent payment pages, fake login screens (e.g., Google, Microsoft, Apple, or GitHub login clones), credential harvesting web forms, or social engineering landing pages.
- Hosting pyramid schemes, Ponzi schemes, illegal gambling, counterfeit merchandise stores, or unlicensed pharmaceutical distribution.

### 3.5 Illegal, Harmful & Unlawful Content
- Storing, distributing, or displaying child sexual abuse material (CSAM) or any content exploiting or endangering minors. Ephemeral reports any instances of CSAM immediately to the National Center for Missing & Exploited Children (NCMEC) and relevant international law enforcement agencies.
- Hosting non-consensual sexual imagery, terrorism or violent extremism propaganda, harassment, hate speech, or content inciting violence.

### 3.6 Resource Abuse & Concurrency Flooding
- Attempting to bypass, subvert, or overwhelm configured concurrency caps, CPU limits, memory quotas (cgroups), or PID limits.
- Fork bombing, infinite recursive process execution, high-frequency disk I/O thrashing, or automated scripts designed to evade idle timeout termination.
- Submitting rapid, synthetic webhook bursts or automating automated PR cycles for the sole purpose of monopolizing physical server resources.

### 3.7 Public Fork PR Submissions
- Ephemeral enforces strict fork pull request security isolation. External, untrusted pull requests originating from forks of public repositories are blocked from automatic compute provisioning until an authorized repository collaborator explicitly grants cryptographic execution approval. Attempting to bypass fork security verification constitutes an immediate AUP violation.

---

## 4. CONTAINER RUNTIME CONSTRAINTS & CLUSTER ENFORCEMENT

### 4.1 Security Hardening
All workloads orchestrated on Ephemeral execute under mandatory kernel-level sandboxing restrictions:
1. **Capability Dropping:** All container processes operate with fully dropped Linux kernel capabilities (`cap_drop: [ALL]`). Containers are denied raw network socket fabrication, kernel module loading, and system administrative overrides.
2. **No Privilege Escalation:** Containers are executed with `no-new-privileges: true` and non-root execution guidelines.
3. **Hardware Boundaries:** Each container is constrained to its plan-specific hardware allocation (e.g., Spark: 1 vCPU / 2GB RAM; Vapor: 2 vCPU / 4GB RAM; Stratum: 4 vCPU / 8GB RAM). Processes exceeding memory allocations are subject to immediate SIGKILL by the Linux Out-Of-Memory (OOM) killer without notice.

### 4.2 Right of Remediation & Immediate Termination
Ephemeral reserves the absolute, unfettered right—in its sole and absolute discretion—to immediately kill, destroy, suspend, inspect, or delete any container, preview environment, GitHub webhook binding, or customer account without prior notice, without judicial process, and without liability or refund, upon:
1. Detecting or suspecting any violation of this Acceptable Use Policy;
2. Receiving a third-party abuse notice or law enforcement inquiry;
3. Observing abnormal host CPU, RAM, or network egress spikes threatening server stability.

---

## 5. SUBSCRIPTION PLANS, 7-DAY SPARK FREE TRIAL & PAYMENTS

### 5.1 Plan Tiers & Resource Specifications
1. **Spark (7-Day Free Trial):** Zero cost ($0), no credit card required. Includes up to 2 concurrent preview environments, 4-hour max TTL per PR, 1 vCPU / 2GB RAM per container, standard logging.
2. **Vapor ($39 USD / month):** Up to 6 concurrent preview environments, 45-minute idle buffer, 2 vCPU / 4GB RAM, custom vanity subdomains, up to 5 team seats, priority email SLA.
3. **Stratum ($129 USD / month):** Up to 20 concurrent preview environments, 3-hour idle buffer, 4 vCPU / 8GB RAM, unlimited custom domains, up to 20 team seats, 5,000 build minutes.
4. **Apex ($349 USD / month):** Dedicated cloud node infrastructure, 999 concurrent environments, zero idle timeout (always-on mode), dedicated private cluster ingress, custom SLA.

### 5.2 7-Day Spark Free Trial & Automatic Account Lock
1. **One Trial Per Customer:** The Spark 7-Day Free Trial is strictly limited to one (1) trial period per user, developer team, and corporate organization. Creation of duplicate accounts, disposable email identities, or synthetic GitHub organizations to evade trial expiration is strictly prohibited and constitutes fraud.
2. **Automatic Lockout at Expiration:** Upon the expiration of seven (7) calendar days (168 hours) from the initial activation of the Spark trial:
   - Your account is automatically transitioned into an **Account Locked** status.
   - All active preview environments will be scheduled for immediate automated teardown.
   - Automatic GitHub webhook deployments, commit syncs, and manual "+ Deploy Preview" triggers will be blocked.
   - You must upgrade to a paid subscription (Vapor, Stratum, or Apex) to unlock your workspace.
3. **14-Day Data Safety Guarantee & Grace Period:** Following trial expiration, your connected repository scope, custom domain records, team memberships, and audit logs are preserved in a dormant state for a grace period of fourteen (14) days. If no upgrade is completed within fourteen (14) days, Ephemeral reserves the right to prune dormant account configurations.

### 5.3 Peer-to-Peer Payments, PayPal.Me & UPI Disclaimers
1. **Direct Peer Transactions:** Ephemeral offers direct peer payment rails via PayPal (`https://paypal.me/mhksandhu`) and unified payments interface (UPI: `ephemeral@okaxis`). You acknowledge and agree that payments submitted via PayPal.Me or UPI are direct peer transfers for the purchase of transient computing resources.
2. **All Sales Strictly Final & Non-Refundable:** Because cloud computing resources, CPU cycles, RAM allocations, and Cloudflare tunnel bandwidth are consumed in real-time upon provisioning, **ALL PAYMENTS, TRANSFERS, AND SUBSCRIPTION UPGRADES ARE 100% FINAL AND NON-REFUNDABLE.** Ephemeral does NOT provide prorated refunds, partial refunds, or money-back guarantees under any circumstance, including but not limited to account cancellation, voluntary non-use, service interruptions, or termination for AUP violation.
3. **Transaction Verification:** When completing an upgrade via PayPal.Me or UPI, you are required to submit the authentic Transaction Reference ID / UTR number via the dashboard. Ephemeral reserves the right to suspend or revoke access if a transaction ID is invalid, unverified, disputed, or charged back.
4. **No Chargeback Covenant:** You unconditionally agree that you will not initiate, file, or facilitate any chargeback, payment dispute, or reversal claim through PayPal, your credit card issuing bank, or your financial institution for verified software services provided. If a chargeback or dispute is filed in violation of this Agreement, you agree to pay Ephemeral all costs incurred in defending such claim, including administrative fees and full legal expenses.

---

## 6. INTELLECTUAL PROPERTY & CUSTOMER REPOSITORIES

### 6.1 Customer Ownership of Code
You retain 100% full, exclusive ownership of all source code, commit history, configuration files, Dockerfiles, and proprietary assets submitted to Ephemeral via connected GitHub repositories ("Customer IP"). Ephemeral does NOT claim any ownership rights over your intellectual property.

### 6.2 Limited Processing License
You grant Ephemeral an irrevocable, worldwide, non-exclusive, royalty-free, limited license strictly for the duration and purpose of operating the Service to clone your specified repositories, read branch metadata, build Docker container images, mount local configuration files, and route HTTP/WebSocket traffic through edge tunnels to fulfill your preview requests.

### 6.3 Ephemeral Proprietary IP
Ephemeral, its logo, brand marks, orchestration engine, daemon source code, API interfaces, user interfaces, documentation, and benchmark reports are the sole and exclusive proprietary property of Ephemeral Inc. You may not reverse engineer, decompile, disassemble, or extract proprietary algorithms from the Ephemeral platform.

---

## 7. DISCLAIMER OF ALL WARRANTIES ("AS IS" & "AS AVAILABLE")

TO THE MAXIMUM EXTENT PERMITTED UNDER APPLICABLE LAW, THE SERVICE, INFRASTRUCTURE, APIS, DASHBOARD, TUNNELS, CONTAINER RUNTIMES, AND DOCUMENTATION ARE PROVIDED STRICTLY ON AN **"AS IS"** AND **"AS AVAILABLE"** BASIS, WITH ALL FAULTS, DEFECTS, AND ERRORS.

EPHEMERAL INC., ITS FOUNDERS, OFFICERS, EMPLOYEES, CONTRACTORS, AFFILIATES, AND SUPPLIERS EXPRESSLY AND CATEGORICALLY DISCLAIM ANY AND ALL WARRANTIES OF ANY KIND, WHETHER STATUTORY, EXPRESS, IMPLIED, OR ARISING BY COURSE OF DEALING OR USAGE IN TRADE, INCLUDING BUT NOT LIMITED TO:
1. ANY IMPLIED WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE, WORKMANLIKE QUALITY, AND NON-INFRINGEMENT;
2. ANY WARRANTY THAT THE SERVICE WILL MEET YOUR OPERATIONAL REQUIREMENTS, BE COMPATIBLE WITH EVERY DOCKERFILE OR LINUX IMAGE, OR OPERATE UNINTERRUPTED, TIMELY, SECURE, OR ERROR-FREE;
3. ANY WARRANTY CONCERNING THE ACCURACY, RELIABILITY, OR DURABILITY OF PREVIEW URLS, CLOUDFLARE EDGE TUNNELS, GITHUB WEBHOOK DELIVERY, OR WEBSOCKET LOG TRANSMISSIONS;
4. ANY WARRANTY AGAINST THIRD-PARTY OUTAGES, UPSTREAM CLOUD PROVIDER CRASHES (E.G., CLOUDFLARE OUTAGES, GITHUB DOWNTIME, DOCKER HUB RATE LIMITS), SYSTEM HARDWARE FAILURES, OR DATA LOSS RESULTING FROM HARDWARE CRASHES.

YOU EXPRESSLY ACKNOWLEDGE THAT CLOUD SOFTWARE DEPLOYMENTS CARRY INHERENT RISK AND THAT YOUR USE OF THE SERVICE IS CONDUCTED ENTIRELY AT YOUR OWN RISK AND DISCRETION.

---

## 8. STRICT LIMITATION OF LIABILITY

TO THE MAXIMUM EXTENT PERMITTED BY APPLICABLE LAW, IN NO EVENT SHALL EPHEMERAL INC., ITS PRINCIPALS, DIRECTORS, INVESTORS, EMPLOYEES, AGENTS, OR LICENSORS BE LIABLE TO YOU, YOUR ORGANIZATION, YOUR CUSTOMERS, OR ANY THIRD PARTY FOR ANY:
1. **INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, PUNITIVE, OR CONSEQUENTIAL DAMAGES;**
2. **LOSS OF USE, LOSS OF DATA, CORRUPTION OF DATA, OR INABILITY TO RETRIEVE CONTAINER ARTIFACTS;**
3. **LOSS OF PROFITS, LOSS OF REVENUE, LOSS OF GOODWILL, BUSINESS INTERRUPTION, OR PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES;**
4. **UNAUTHORIZED ACCESS, USE, OR ALTERATION OF YOUR REPOSITORIES, TRANSMISSIONS, OR CONTENT;**
5. **ANY DOWNTIME, MISCONFIGURED DOCKER CONTAINERS, FAILED PULL REQUEST BUILDS, OR INABILITY TO ACCESS PREVIEW ENVIRONMENTS;**

REGARDLESS OF THE THEORY OF LIABILITY (WHETHER IN CONTRACT, TORT, STRICT LIABILITY, NEGLIGENCE, INDEMNITY, BREACH OF WARRANTY, OR OTHERWISE), AND EVEN IF EPHEMERAL HAS BEEN ADVISED OF THE POSSIBILITY OF SUCH DAMAGES, AND EVEN IF A LIMITED REMEDY FAILS OF ITS ESSENTIAL PURPOSE.

### 8.1 Aggregate Maximum Liability Cap
UNDER ALL CIRCUMSTANCES, THE MAXIMUM AGGREGATE LIABILITY OF EPHEMERAL INC. AND ITS OPERATORS TO YOU ARISING OUT OF OR RELATING TO THIS AGREEMENT, THE USE OF THE SERVICE, OR ANY ACT OR OMISSION OF EPHEMERAL, SHALL BE STRICTLY LIMITED TO THE LESSER OF:
1. **THE TOTAL FEES ACTUALLY PAID BY YOU TO EPHEMERAL IN THE ONE (1) MONTH IMMEDIATELY PRECEDING THE EVENT GIVING RISE TO LIABILITY; OR**
2. **FIFTY UNITED STATES DOLLARS ($50.00 USD).**

THE EXISTENCE OF MULTIPLE CLAIMS OR CAUSES OF ACTION SHALL NOT INCREASE OR ENLARGE THIS MONETARY LIMIT.

---

## 9. INDEMNIFICATION

You agree to defend, indemnify, and hold harmless Ephemeral Inc., its founders, officers, directors, employees, contractors, agents, subsidiaries, affiliates, infrastructure providers, and licensors from and against any and all claims, demands, liabilities, damages, judgments, losses, costs, expenses, and fees (including reasonable attorneys' fees and court costs) arising out of or resulting from:
1. Your access to or use of the Service;
2. Your violation of any provision of this Agreement, including the Acceptable Use Policy;
3. Any content, code, binary, repository, or Dockerfile deployed, compiled, or executed by you or your organization through the Service;
4. Any third-party claim alleging that your code or workloads infringe, misappropriate, or violate any patent, copyright, trademark, trade secret, or other proprietary or privacy right;
5. Any security breach, vulnerability, or illegal network activity originating from or hosted within your deployed preview containers.

Ephemeral reserves the right, at its own expense, to assume the exclusive defense and control of any matter otherwise subject to indemnification by you, in which event you agree to cooperate fully with Ephemeral in asserting any available defenses.

---

## 10. DMCA & INTELLECTUAL PROPERTY TAKEDOWNS

Ephemeral respects the intellectual property rights of third parties and complies with the provisions of the Digital Millennium Copyright Act (17 U.S.C. § 512). 

If you believe that material hosted within an Ephemeral preview container infringes your copyright, please dispatch a formal DMCA Notice of Infringement containing:
1. A physical or electronic signature of the copyright owner or authorized representative;
2. Identification of the copyrighted work claimed to have been infringed;
3. Identification of the preview URL or material to be removed;
4. Your contact details (legal name, address, telephone number, email);
5. A statement of good faith belief that the contested use is unauthorized;
6. A statement made under penalty of perjury that the information is accurate.

Dispatch DMCA notices to: **`dmca@ephemeral.dev`** or **`mehaksandhudev@gmail.com`**. Upon receipt of a compliant notice, Ephemeral will expeditiously disable or terminate the identified preview environment.

---

## 11. BINDING ARBITRATION & CLASS ACTION WAIVER

### 11.1 Mandatory Binding Arbitration
Any dispute, controversy, claim, or difference arising out of or relating to this Agreement, including the formation, interpretation, breach, or termination thereof, shall be settled and resolved by binding, individual arbitration administered under the Commercial Arbitration Rules of the American Arbitration Association (AAA) or equivalent recognized arbitration authority. The seat and venue of arbitration shall be determined by Ephemeral, or conducted virtually. The decision of the arbitrator shall be final, conclusive, and binding, and judgment may be entered thereon in any court of competent jurisdiction.

### 11.2 Waiver of Class and Collective Actions
YOU AND EPHEMERAL EXPRESSLY AGREE THAT EACH PARTY MAY BRING CLAIMS AGAINST THE OTHER ONLY IN AN INDIVIDUAL CAPACITY, AND NOT AS A PLAINTIFF OR CLASS MEMBER IN ANY PURPORTED CLASS, COLLECTIVE, CONSOLIDATED, OR REPRESENTATIVE PROCEEDING. NO ARBITRATOR OR COURT SHALL HAVE THE AUTHORITY TO CONSOLIDATE OR JOIN THE CLAIMS OF OTHER TENANTS OR CONDUCT ANY CLASS ACTION ARBITRATION.

---

## 12. GOVERNING LAW & JURISDICTION

This Agreement, and any dispute arising from or related to it, shall be governed by, construed, and enforced in accordance with the laws of the State of Delaware, United States (or, where applicable to international commercial treaties, the laws of India for transactions originating therein), without regard to conflict of law principles. The United Nations Convention on Contracts for the International Sale of Goods (CISG) is expressly disclaimed and excluded.

---

## 13. SEVERABILITY, FORCE MAJEURE & GENERAL TERMS

### 13.1 Severability
If any provision of this Agreement is adjudicated by an arbitrator or court of competent jurisdiction to be invalid, illegal, or unenforceable, such provision shall be enforced to the maximum extent permissible, and the remaining provisions shall continue in full force and effect.

### 13.2 Force Majeure
Ephemeral shall not be liable or deemed in default for any delay, failure in performance, or interruption of the Service resulting directly or indirectly from acts of God, civil or military authority, war, terrorism, civil unrest, natural disasters, national emergencies, power failure, telecommunications breakdown, internet routing anomalies, cloud provider outages (e.g., Cloudflare, GitHub, AWS, DigitalOcean), cyber attacks, or labor disputes.

### 13.3 Entire Agreement
This Agreement, together with the Ephemeral Privacy Policy and any applicable enterprise order forms, constitutes the entire, final, and complete agreement between you and Ephemeral concerning the Service, superseding all prior proposals, understandings, or agreements, whether oral or written.

### 13.4 Contact Information
For inquiries regarding these Terms of Service or to report legal violations:
- **Email:** `legal@ephemeral.dev` / `mehaksandhudev@gmail.com`
- **Security & Abuse:** `abuse@ephemeral.dev`
- **Postal / Legal Representative:** Ephemeral Inc., Attn: Legal Department, New Delhi / Delaware.
