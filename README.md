# Ephemeral Preview Demo Application

> **Official SaaS Platform:** [**https://ephemeral.peaklabs.in**](https://ephemeral.peaklabs.in)  
> [← Main Ephemeral Repository](../README.md) • [System Architecture](../ARCHITECTURE.md) • [API Specification](../API_SPEC.md)

A complete, self-contained reference project demonstrating how Ephemeral automatically builds, previews, and tests multi-tier Docker Compose stacks on GitHub Pull Requests.

---

## Architecture of this Demo

This repository contains a lightweight full-stack application configured via standard `docker-compose.yml`:

```
demo-repo/
├── docker-compose.yml     # Compose definition specifying web frontend and backend services
└── README.md              # Demonstration documentation and testing guide
```

---

## How Ephemeral Interacts With This Repo

1. **Pull Request Trigger**:
   - When a developer opens a PR against `master`, GitHub issues a `pull_request.opened` webhook to Ephemeral.
   - Ephemeral verifies the HMAC signature, fetches this repository, and parses `docker-compose.yml`.

2. **AST Sanitization & Sandboxing**:
   - Ephemeral validates the Compose syntax, strips root/privileged flags, drops dangerous capabilities, and allocates atomic ports from `10000–20000`.

3. **Deployment & URL Publication**:
   - The containers start on an isolated bridge network (`ephemeral-net-pr-{id}`).
   - A secure ingress tunnel maps external traffic to the allocated port.
   - A bot comment is posted directly onto the pull request with the live URL:
     `https://pr-1-ephemeral-demo.preview.ephemeral.dev`

4. **Slash Commands Supported**:
   - Maintainers can comment in the PR:
     - `/preview deploy` — Re-triggers deployment on-demand.
     - `/preview logs` — Displays the last 50 log lines.
     - `/preview destroy` — Immediately tears down running containers.

5. **Automatic Cleanup**:
   - When the PR is merged or closed, Ephemeral immediately destroys the container stack, frees allocated ports, and recycles all storage.
