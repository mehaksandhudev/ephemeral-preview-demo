# Ephemeral Inc. — Privacy Policy

**Effective Date:** October 12, 2026  
**Last Updated:** October 12, 2026  
**Version:** 3.0 (Global Compliance Edition: GDPR, CCPA/CPRA & DPDP Compliant)

---

### 1. OVERVIEW & COMMITMENT TO PRIVACY
Ephemeral Inc. and its operators ("Ephemeral", "we", "us", or "our") take the privacy and confidentiality of your engineering workflows and personal data with extreme seriousness. This Privacy Policy details the exact mechanisms by which we collect, process, store, disclose, and protect personal and organizational information when you visit our website, connect our GitHub Application, register an account, utilize our APIs, or deploy pull request preview environments through the Ephemeral platform.

By accessing or using Ephemeral in any capacity, you acknowledge and agree to the practices outlined in this Privacy Policy.

---

### 2. DATA CONTROLLER & IDENTITY
For users accessing the platform globally, the Data Controller responsible for your personal data is:
- **Entity:** Ephemeral Inc.
- **Lead Developer & Operator:** Mehak Sandhu (`mehaksandhudev@gmail.com`)
- **Privacy & Security Team:** `privacy@ephemeral.dev`
- **Data Protection Office:** Ephemeral Compliance Unit, New Delhi / Delaware

---

### 3. CATEGORIES OF INFORMATION WE COLLECT

We strictly collect only the minimum telemetry and metadata necessary to orchestrate, sandbox, and network preview environments:

#### 3.1 Account & Identity Information
- **Authentication Data:** Email address, hashed password credentials (managed securely via Supabase Auth), full name, and avatar image.
- **Federated Identity Data:** If you authenticate using Google OAuth or GitHub App authorization, we receive your verified email, GitHub user ID, GitHub username, and organization memberships necessary to verify authorization.
- **Role & Permissions:** Your organizational role (`Admin`, `Developer`, `Viewer`) within team workspaces.

#### 3.2 Repository & Pull Request Metadata
- **GitHub Installation Identifiers:** GitHub App installation ID, repository full names (e.g. `org/repo-name`), default branch references, pull request numbers, branch heads, and commit SHAs.
- **Webhook Payloads:** Ingested GitHub webhook events (`pull_request`, `push`, `issue_comment`, `installation`) used exclusively to trigger preview lifecycles and post sticky preview comments.

#### 3.3 Ephemeral Runtime & Operational Logs
- **Build Logs:** BuildKit container build output, dependency resolution logs, and Docker Compose orchestration stdout/stderr streams.
- **Port Registry & Networking:** Dynamically allocated host port bindings and Cloudflare tunnel edge IDs.
- **Audit Logging:** Timestamps of environment provisioning, teardown, user login sessions, and webhook delivery records.

#### 3.4 Payment & Transaction Metadata
- **Peer Payment Reference Data:** Transaction Reference IDs / UTR numbers provided for verification via PayPal.Me or UPI.
- **Explicit Payment Card Exclusion:** Ephemeral does **NOT** collect, store, transmit, or process credit card numbers, CVVs, expiration dates, or bank account numbers. Peer transactions are settled directly via PayPal or banking rails.

#### 3.5 Device & Technical Telemetry
- **Network Headers:** IP addresses (used strictly for rate limiting, DDoS mitigation, and anti-abuse verification), browser user-agent, operating system details, and Cloudflare Turnstile bot detection tokens.

---

### 4. CATEGORIES OF DATA WE EXPLICITLY DO NOT COLLECT OR STORE

- We do **NOT** sell, monetize, rent, or trade your personal data, repository metadata, or build logs to data brokers or advertising networks.
- We do **NOT** inspect, analyze, or train machine learning/AI models on your private source code.
- We do **NOT** retain customer source code archives on our servers following environment teardown.

---

### 5. SOURCE CODE LIFECYCLE & THE ZERO-PERSISTENCE GUARANTEE

Our architecture is founded upon the principle of **Zero-Persistence Ephemeral Execution**:

1. **Transient Checkout:** When a pull request webhook is verified, source code is shallow-cloned exclusively to an isolated temporary directory on the runner host for the sole purpose of compiling container images.
2. **Immediate Purge upon Teardown:** The moment a pull request is merged, closed, or its Auto-TTL reaper expires:
   - The host workspace directory is recursively deleted from disk.
   - Associated container volumes and virtual bridge networks are destroyed.
   - Built images are wiped using `docker compose down -v --rmi local`.
3. **BuildKit Cache Sanitation:** Build cache layers are pruned automatically on a rolling 24-hour cycle to prevent any cross-tenant residual code fragments.

---

### 6. LEGAL BASES FOR PROCESSING (GDPR & INTERNATIONAL COMPLIANCE)

Under the EU General Data Protection Regulation (GDPR Article 6), we process your data under the following lawful bases:
- **Performance of a Contract (Art. 6(1)(b)):** Processing necessary to provide the preview orchestration service you requested.
- **Legitimate Interests (Art. 6(1)(f)):** Protecting cluster security, preventing cryptocurrency mining and denial-of-service abuse, enforcing concurrency limits, and authenticating sessions.
- **Legal Obligation (Art. 6(1)(c)):** Retaining billing receipts, tax documentation, and responding to lawful governmental inquiries.

---

### 7. SUBPROCESSORS & THIRD-PARTY SERVICE PROVIDERS

We partner only with vetted, enterprise-grade cloud infrastructure subprocessors:
| Subprocessor | Purpose | Location | Safeguards |
| :--- | :--- | :--- | :--- |
| **Supabase, Inc.** | User authentication, identity tokens & verification emails | Global / US | SOC2 Type II, TLS 1.3, AES-256 |
| **Cloudflare, Inc.** | Edge DNS, Quick/Named Tunnels, SSL/TLS & DDoS mitigation | Global Edge | ISO 27001, SOC2, Edge Encryption |
| **GitHub, Inc.** | Webhook ingestion, repository metadata & PR comments | US / Global | SOC1/2, GitHub App permissions |
| **PayPal, Inc.** | Peer-to-peer payment processing | Global | PCI-DSS Level 1 |

---

### 8. COOKIES, LOCAL STORAGE & STATE MANAGEMENT

Ephemeral uses minimal client-side storage strictly for core functional operations:
- `ephemeral_auth_user`: Active authenticated session profile.
- `ephemeral_chosen_plan`: User plan state (`spark`, `vapor`, `stratum`, `apex`).
- `ephemeral_trial_start`: Activation timestamp for the 7-day Spark free trial.
- `ephemeral_theme`: User preference (`light` or `dark`).

We do **NOT** employ third-party advertising tracking cookies, Facebook Pixels, or cross-site fingerprinting scripts.

---

### 9. SECURITY SAFEGUARDS & SYSTEM DEFENSE

We enforce defense-in-depth safeguards across every tier:
1. **Container Isolation:** Dropped Linux kernel capabilities (`cap_drop: [ALL]`), no-new-privileges execution, cgroup memory/CPU limits.
2. **Encrypted Ingress:** Full end-to-end TLS 1.3 encryption for all external preview URLs via Cloudflare edge routing.
3. **Webhook Cryptography:** Mandatory HMAC SHA-256 signature verification for all incoming GitHub webhook payloads.
4. **Anti-Abuse Gating:** Automated blacklisting of 80+ disposable email domains and Cloudflare Turnstile bot verification.

---

### 10. YOUR DATA PROTECTION RIGHTS

Under applicable privacy legislation (GDPR, CCPA/CPRA, and India DPDP Act), you possess the following rights:
- **Right to Access:** You may request a copy of the personal data we hold concerning your account.
- **Right to Rectification:** You may correct inaccurate or incomplete profile data directly via the dashboard or by contacting us.
- **Right to Erasure ("Right to Be Forgotten"):** You may request the permanent deletion of your account, audit logs, and associated records.
- **Right to Restrict or Object:** You may object to the processing of your data on legitimate interest grounds.
- **Right to Revoke Integration:** You may disconnect the Ephemeral GitHub App at any moment directly from your GitHub Account Settings &rarr; Applications.

To exercise any of these rights, email: **`privacy@ephemeral.dev`** or **`mehaksandhudev@gmail.com`**. We respond to verified requests within thirty (30) calendar days.

---

### 11. CHILDREN'S PRIVACY
The Service is strictly targeted towards professional developers and businesses. We do not knowingly collect personal data from children under eighteen (18) years of age. If we discover that a minor has provided us with personal data, we will immediately delete such information.

---

### 12. CHANGES TO THIS PRIVACY POLICY
We reserve the right to amend or update this Privacy Policy at our discretion to reflect evolving legal, technical, or operational practices. Significant revisions will be signaled via a notice on the dashboard or via email.

---

### 13. PRIVACY CONTACT & DATA PROTECTION OFFICER
For all privacy inquiries, data subject requests, or security disclosures:
- **Email:** `privacy@ephemeral.dev` / `mehaksandhudev@gmail.com`
- **Security Team:** `security@ephemeral.dev`
- **Postal / Legal Entity:** Ephemeral Inc., Attn: Privacy Office, New Delhi / Delaware
