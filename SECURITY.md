# Ephemeral Security Policy & Vulnerability Disclosure

## 1. Security Philosophy & Commitment

Security and isolation are the foundational pillars of the **Ephemeral PR Preview Orchestrator**. Because Ephemeral executes container workloads derived from potentially untrusted GitHub pull requests and third-party code branches, we implement defense-in-depth across the entire stack:
- Network partitioning with isolated container bridges.
- AST-level Docker Compose parsing to strip elevated capabilities and block host socket bindings.
- Atomic port allocation preventing collision and port stealing.
- Strict anti-abuse defenses including disposable email blacklisting and concurrency caps.

---

## 2. Supported Versions

Security patches and dependency vulnerability updates are applied to the following active release branches:

| Version Family | Release Status | Security Support |
| :--- | :--- | :--- |
| **v1.2.x** (Latest) | Current Stable | :white_check_mark: Full active security maintenance |
| **v1.1.x** | Previous Minor | :white_check_mark: Critical vulnerabilities only |
| **< v1.1.0** | Deprecated | :x: Unsupported (Upgrade recommended) |

---

## 3. Reporting a Vulnerability

We deeply appreciate responsible disclosure from security researchers, penetration testers, and the open-source community. If you discover a vulnerability, container escape, authorization flaw, or denial-of-service vector, please report it **privately**:

### Preferred Disclosure Channels
1. **GitHub Security Advisories (Private)**: Submit a private advisory directly via the repository's **Security > Advisories > Report a vulnerability** tab on GitHub.
2. **Direct Security Email**: Send encrypted or plain-text reports to:
   - **Email**: `security@ephemeral.preview` or `mehaksandhudev@gmail.com`
   - **Subject Line**: `[SECURITY VULNERABILITY] Ephemeral - <Brief Descriptor>`

### Report Requirements
To help us triage and resolve the issue quickly, please provide:
1. **Vulnerability Type**: (e.g. Container escape, Docker socket bind, Remote Code Execution outside the container, SSRF, IDOR, SQL Injection, Replay attack).
2. **Affected Component**: Specific file, API endpoint, or Docker parser routine.
3. **Reproduction Steps / PoC**: Minimal proof-of-concept repository, malicious `docker-compose.yml`, or HTTP request script.
4. **Impact Assessment**: Real-world exploit scenario and severity assessment.

---

## 4. Response & Remediation SLA

| Action Item | Commitment SLA |
| :--- | :--- |
| **Initial Acknowledgment** | Within **24 hours** of submission. |
| **Triage & Severity Confirmation** | Within **48 hours**. |
| **Patch Development & Testing** | Within **7 calendar days** for Critical / High severity. |
| **Public Advisory & Release** | Coordinated disclosure within **14 calendar days** (or mutually agreed timeline). |

---

## 5. Security Defense Architecture

### 5.1 AST Docker Compose Sanitization
Every pull request `docker-compose.yml` or auto-detected manifest is parsed into an Abstract Syntax Tree (AST) before execution. The parser enforces non-negotiable security boundaries:
- **Forbids Privileged Execution**: Any declaration of `privileged: true` is strictly suppressed and rewritten to unprivileged mode.
- **Blocks Host Socket Mounts**: Mounts referencing `/var/run/docker.sock` or Windows named pipes (`\\.\pipe\docker_engine`) are rejected.
- **Restricts Host Volume Binding**: Direct mounts to root filesystems (`/`, `/etc`, `/sys`, `/proc`, `/var`) are forbidden.
- **Drops Dangerous Linux Capabilities**: Capabilities such as `CAP_SYS_ADMIN`, `CAP_NET_ADMIN`, and `CAP_RAW_IO` are stripped.
- **Private Bridge Isolation**: Every preview stack runs on a distinct Docker network (`ephemeral-net-pr-{envId}`) with no inter-tenant visibility.

### 5.2 Dynamic Atomic Port Allocator
Ports in the `10000–20000` range are allocated through atomic SQLite database transactions (`BEGIN IMMEDIATE`) coupled with active TCP socket verification. This prevents race conditions, port hijackings, and cross-preview port sniffing.

### 5.3 Anti-Abuse & Anti-Mining Controls
- **Disposable Email Filtering**: Accounts attempting registration with burner domains are rejected at the gateway.
- **Concurrency & CPU Throttling**: Strict memory quotas (`2048MB` default) and CPU limits prevent cryptominers from exhausting host infrastructure.
- **7-Day Spark Trial Enforcement**: After 7 days, un-upgraded accounts are automatically locked, preventing dormant or rogue preview instances from persisting.

### 5.4 Zero-Persistence Code Retention
Ephemeral does not persist customer repository source code. Once a preview container image is built, source code archives and temporary clone directories are wiped from disk. When an environment expires, containers, images, and bridge networks are pruned.
