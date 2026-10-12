# Ephemeral PR Preview Orchestrator — Comprehensive API Specification

> **Official SaaS Platform:** [**https://ephemeral.peaklabs.in**](https://ephemeral.peaklabs.in)  
> [← Main Documentation](README.md) • [System Architecture](ARCHITECTURE.md) • [Backend Guide](app/README.md) • [Frontend Web App](app/web/README.md) • [Security Policy](SECURITY.md)

---

## 1. Specification Overview

The Ephemeral API is a high-performance RESTful and WebSocket interface powered by **Fastify 5.x**. It provides programmatic access to preview orchestration, repository configuration, domain ingress routing, multi-gateway billing, team permissions, and real-time container log streaming.

- **Base URL (Local / Dev)**: `http://localhost:3000`
- **Base URL (Production)**: `https://api.preview.ephemeral.dev` (or configured `PORT` / reverse proxy)
- **Protocol**: HTTPS / WSS
- **Content-Type**: `application/json`
- **Authentication**: Bearer JWT token in the `Authorization` header (`Authorization: Bearer <token>`) or GitHub App webhook signatures.

---

## 2. Authentication & Authorization

All private endpoints require a valid JWT issued during user authentication. Public endpoints (such as `/api/health`, `/api/auth/register`, and GitHub webhook receivers) do not require Bearer tokens.

### Common Error Responses

| HTTP Status | Error Code | Description |
| :--- | :--- | :--- |
| `400 Bad Request` | `VALIDATION_ERROR` | Request body or parameters failed schema validation. |
| `401 Unauthorized` | `UNAUTHORIZED` | Missing, expired, or malformed Bearer token. |
| `403 Forbidden` | `TRIAL_EXPIRED` | Spark trial has expired (day 7+). Subscription upgrade required. |
| `403 Forbidden` | `PLAN_LIMIT_EXCEEDED`| Organization has reached its maximum concurrent previews. |
| `404 Not Found` | `NOT_FOUND` | Requested resource (environment, repo, domain) does not exist. |
| `409 Conflict` | `PORT_COLLISION` / `CONFLICT` | Resource already exists or port allocation temporarily contended. |
| `500 Server Error` | `INTERNAL_ERROR` | An unexpected internal error occurred during execution. |

---

## 3. Endpoints Directory

### 3.1 Health & Diagnostic

#### `GET /api/health`
Returns the operational status, version, and aggregated container metrics. Results are cached in-memory for 5 seconds via `apiCache`.

**Headers**: None required.

**Response `200 OK`**:
```json
{
  "status": "healthy",
  "version": "1.2.0",
  "timestamp": "2026-10-12T12:00:00.000Z",
  "uptime_seconds": 86420,
  "environments": {
    "active": 4,
    "running": 3,
    "provisioning": 1,
    "failed": 0,
    "destroyed": 18
  },
  "system": {
    "node_version": "v22.14.0",
    "memory_used_mb": 142.6,
    "memory_total_mb": 512.0
  }
}
```

---

### 3.2 Environments & Previews

#### `GET /api/environments`
Lists preview environments belonging to the caller's organization.

**Query Parameters**:
- `status` *(optional, string)*: Filter by `provisioning`, `running`, `failed`, `expiring`, or `destroyed`.
- `repo` *(optional, string)*: Full repository name (e.g. `octocat/hello-world`).
- `limit` *(optional, integer)*: Maximum records to return (default: `50`, max: `100`).
- `offset` *(optional, integer)*: Pagination offset (default: `0`).

**Response `200 OK`**:
```json
{
  "total": 3,
  "environments": [
    {
      "id": "env_a1b2c3d4",
      "repo_full_name": "acme/frontend",
      "pr_number": 42,
      "branch_name": "feature/dark-mode",
      "commit_sha": "e9b21f3",
      "status": "running",
      "preview_url": "https://pr-42-acme-frontend.preview.ephemeral.dev",
      "assigned_port": 10420,
      "created_at": "2026-10-12T10:00:00.000Z",
      "expires_at": "2026-10-12T14:00:00.000Z"
    }
  ]
}
```

---

#### `GET /api/environments/:id`
Retrieves detailed metadata for a single preview stack.

**Path Parameters**:
- `id` *(required, string)*: The unique environment identifier.

**Response `200 OK`**:
```json
{
  "id": "env_a1b2c3d4",
  "repo_full_name": "acme/frontend",
  "pr_number": 42,
  "branch_name": "feature/dark-mode",
  "commit_sha": "e9b21f3",
  "status": "running",
  "preview_url": "https://pr-42-acme-frontend.preview.ephemeral.dev",
  "assigned_port": 10420,
  "cpu_quota": 1.5,
  "memory_limit_mb": 2048,
  "compose_services": ["web", "cache"],
  "created_at": "2026-10-12T10:00:00.000Z",
  "expires_at": "2026-10-12T14:00:00.000Z",
  "ports": [
    {
      "service": "web",
      "internal_port": 3000,
      "host_port": 10420
    }
  ]
}
```

---

#### `POST /api/environments/trigger`
Manually triggers a new preview deployment for a pull request or branch. Used by the web console's **+ Deploy Preview** modal.

**Request Body**:
```json
{
  "repo_full_name": "acme/frontend",
  "pr_number": 42,
  "branch_name": "feature/dark-mode",
  "commit_sha": "e9b21f3",
  "trigger_mode": "manual",
  "ttl_minutes": 240
}
```

**Response `202 Accepted`**:
```json
{
  "status": "queued",
  "environment_id": "env_a1b2c3d4",
  "message": "Preview build queued successfully.",
  "estimated_time_seconds": 35
}
```

**Error Responses**:
- `403 Forbidden`:
  ```json
  {
    "error": "TRIAL_EXPIRED",
    "message": "Your 7-day Spark trial has ended. Please upgrade to Vapor or Stratum to trigger previews."
  }
  ```

---

#### `POST /api/environments/:id/redeploy`
Triggers an immediate container rebuild and deployment of an existing environment.

**Path Parameters**:
- `id` *(required, string)*: The unique environment identifier.

**Response `202 Accepted`**:
```json
{
  "environment_id": "env_a1b2c3d4",
  "status": "provisioning",
  "message": "Redeployment initiated."
}
```

---

#### `DELETE /api/environments/:id`
Immediately tears down the container stack, frees allocated ports, destroys proxy ingress, and releases memory.

**Path Parameters**:
- `id` *(required, string)*: The unique environment identifier.

**Response `200 OK`**:
```json
{
  "environment_id": "env_a1b2c3d4",
  "status": "destroyed",
  "message": "Preview stack terminated and ports reclaimed."
}
```

---

#### `GET /api/environments/:id/logs/stream` (WebSocket)
Opens a bi-directional WebSocket connection streaming live stdout and stderr from running containers.

**WebSocket URL**: `wss://api.preview.ephemeral.dev/api/environments/:id/logs/stream`

**Stream Frame**:
```json
{
  "timestamp": "2026-10-12T10:01:23.456Z",
  "service": "web",
  "stream": "stdout",
  "message": "Ready on http://0.0.0.0:3000"
}
```

---

### 3.3 Repositories & Trigger Settings

#### `GET /api/repositories`
Lists connected GitHub repositories with active trigger modes.

**Response `200 OK`**:
```json
[
  {
    "id": "repo_101",
    "full_name": "acme/frontend",
    "default_branch": "main",
    "trigger_mode": "auto_pr",
    "preview_ttl_minutes": 240,
    "auto_comment": true,
    "active_previews": 2
  }
]
```

---

#### `PATCH /api/repositories/:id`
Updates trigger configuration for a connected repository.

**Request Body**:
```json
{
  "trigger_mode": "branch_push",
  "preview_ttl_minutes": 180,
  "auto_comment": true
}
```

**Response `200 OK`**:
```json
{
  "id": "repo_101",
  "trigger_mode": "branch_push",
  "preview_ttl_minutes": 180,
  "updated_at": "2026-10-12T12:30:00.000Z"
}
```

---

### 3.4 Custom Vanity Domains & Ingress

#### `GET /api/domains`
Lists registered vanity domains and their verification status.

**Response `200 OK`**:
```json
[
  {
    "id": "dom_987",
    "domain": "preview.acme.corp",
    "status": "verified",
    "cname_target": "edge.preview.ephemeral.dev",
    "ssl_status": "active",
    "created_at": "2026-10-01T08:00:00.000Z"
  }
]
```

---

#### `POST /api/domains`
Registers a new custom domain for preview ingress.

**Request Body**:
```json
{
  "domain": "preview.acme.corp"
}
```

**Response `201 Created`**:
```json
{
  "id": "dom_987",
  "domain": "preview.acme.corp",
  "status": "pending_verification",
  "cname_target": "edge.preview.ephemeral.dev",
  "verification_token": "eph_verify_8f7e6d5c4b"
}
```

---

#### `POST /api/domains/:id/verify`
Triggers DNS CNAME resolution check to activate domain proxying.

**Response `200 OK`**:
```json
{
  "id": "dom_987",
  "domain": "preview.acme.corp",
  "status": "verified",
  "message": "Domain CNAME verified successfully. TLS provisioning initiated."
}
```

---

### 3.5 Billing, Subscriptions & Peer Payments

#### `GET /api/billing/org/:orgId`
Retrieves subscription status, plan tier (`spark`, `vapor`, `stratum`, `apex`), trial countdown, and lock state.

**Response `200 OK`**:
```json
{
  "org_id": "org_main",
  "plan_tier": "spark",
  "subscription_status": "trial",
  "trial_starts_at": "2026-10-12T00:00:00.000Z",
  "trial_ends_at": "2026-10-19T00:00:00.000Z",
  "trial_days_remaining": 7,
  "is_trial_expired": false,
  "account_locked": false,
  "concurrency_limit": 2,
  "current_concurrency": 1,
  "payment_methods": {
    "paypal_me": "https://paypal.me/mhksandhu",
    "upi_vpa": "ephemeral@okaxis"
  }
}
```

---

#### `POST /api/billing/manual-claim`
Submits a peer payment claim (via PayPal.Me or UPI) to unlock an expired trial or upgrade subscription tier.

**Request Body**:
```json
{
  "org_id": "org_main",
  "tier": "vapor",
  "billing_cycle": "monthly",
  "payment_method": "paypal",
  "reference_id": "PP-9876543210-TXN",
  "payer_identifier": "mehak@example.com",
  "amount_paid": 39.00,
  "currency": "USD"
}
```

**Response `200 OK`**:
```json
{
  "claim_id": "clm_12345",
  "status": "submitted",
  "message": "Payment claim submitted. Your account will be upgraded immediately upon verification.",
  "temporary_unlock": true
}
```

---

### 3.6 GitHub Webhooks Ingress

#### `POST /api/webhook/github`
Public ingress endpoint receiving events from GitHub App / Repository Webhooks.

**Headers**:
- `X-GitHub-Event` *(string)*: `pull_request`, `push`, `issue_comment`, `pull_request_review`.
- `X-GitHub-Delivery` *(string)*: Unique delivery UUID.
- `X-Hub-Signature-256` *(string)*: HMAC SHA-256 signature generated with the webhook secret.

**Signature Validation Algorithm**:
```typescript
const hmac = crypto.createHmac('sha256', process.env.GITHUB_WEBHOOK_SECRET);
const digest = 'sha256=' + hmac.update(rawBody).digest('hex');
const isValid = crypto.timingSafeEqual(Buffer.from(signature), Buffer.from(digest));
```

**Response `200 OK`**:
```json
{
  "ok": true,
  "action_taken": "spawn_preview",
  "environment_id": "env_a1b2c3d4"
}
```

---

### 3.7 Team Access & Organization

#### `GET /api/teams/members`
Lists organization members and role permissions.

#### `POST /api/teams/invite`
Sends an email invitation to join an organization with a role (`admin`, `developer`, `viewer`).

#### `DELETE /api/teams/members/:id`
Removes member access from the organization.
