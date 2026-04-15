# Architectural Layers — Design Guidance

Read this file during Phase 3 of the designer-architect skill when designing each layer.
It provides detailed questions, decision criteria, and common patterns for each layer.

---

## Layer 1 — System Layer

**Goal**: Define the overall topology — the shape of the system as seen from outside.

**Questions to answer**:
- What are the named boundaries of this system? (What is "inside" vs. "outside"?)
- How many independently deployable units does this system have?
- What are all the external actors (users, services, systems) that interact with it?
- Is there a single entry point (API gateway, load balancer) or multiple?
- What are the synchronous vs. asynchronous flows at the system level?

**Common topologies**:
- Single service with a database (simplest, appropriate for small bounded scope)
- Gateway + N backend services (appropriate when services have distinct scaling or auth needs)
- Event bus topology (appropriate when producers and consumers must be decoupled)
- BFF (Backend for Frontend) pattern (appropriate when multiple client types with different needs)

**Signals from the spec that drive topology**:
- Multiple actors with very different permissions → consider separate entry points
- High write vs. read asymmetry → consider CQRS
- Long-running tasks mentioned in FRs → consider a worker/queue topology

**What to produce**: Component inventory table + ASCII topology diagram + external actor list.

---

## Layer 2 — Application Layer

**Goal**: Define what each service/module does, what it owns, and how it talks to peers.

**Questions to answer per component**:
- What is this component's single responsibility? (If you can't say it in one sentence, split it.)
- What state does it own? (Stateless components are easier to scale and test.)
- What does it expose? (APIs, events, queues — link to API Layer.)
- What does it consume? (Other services' APIs, queues, external services.)
- What are its failure modes? If this component dies, what breaks?

**Patterns to apply**:
- **Bounded Context**: Each service owns its slice of the data model. No shared DB tables
  across service boundaries unless explicitly agreed and documented.
- **Anti-Corruption Layer**: If a service wraps a legacy or external system, add an adapter
  layer so internal domain models don't bleed in.
- **Strangler Fig**: If replacing an existing system, describe which pieces are being
  strangled and in what order.

**Red flags to call out**:
- A component with more than 4–5 distinct responsibilities → probably needs to be split
- Two components that share a database table with write access from both → data ownership conflict
- A component with no clear consumer → orphaned architecture, flag it

---

## Layer 3 — Data Layer

**Goal**: Define where data lives, who owns it, what its shape is, and how it evolves.

**Questions to answer**:
- What storage technologies are needed? (Relational, document, key-value, graph, blob, queue)
- Who owns each data store? (One service = one store; avoid shared write ownership)
- What is the full schema for each primary entity? (Fields, types, indexes, constraints)
- How are schema changes applied? (Migrations: versioned, automated, rollback-safe)
- What is the data retention and archival policy?
- Is there a caching layer? What is cached, for how long, and what invalidates it?

**Technology selection criteria**:
- Use relational (Postgres) as default — deviate with justification
- Use Redis for ephemeral/session/cache data, not as primary store
- Use a document store only if the schema is genuinely variable and unstructured
- Use a message queue when producer and consumer lifetimes are decoupled

**Schema conventions** (apply unless spec/stack constraints override):
- Use UUIDs as primary keys (gen_random_uuid() in Postgres)
- Include `created_at` and `updated_at` on all tables
- Soft-delete with `deleted_at` rather than hard delete for auditable entities
- Index foreign keys, status columns, and any field used in a WHERE clause

**Migration strategy**:
- All schema changes as numbered migration files (e.g., Flyway, Alembic, golang-migrate)
- Migrations run automatically in CI before smoke tests
- Each migration must be forward-compatible before being deployed to avoid downtime
- Breaking changes require a multi-step migration (add column → deploy → backfill → remove old)

---

## Layer 4 — API Layer

**Goal**: Define every interface this system exposes in enough detail to generate stubs and tests.

**Questions to answer per interface**:
- What is the base path and versioning scheme? (`/api/v1/`, `/v2/`, etc.)
- What authentication does this interface require?
- What is the rate limiting policy?
- For each endpoint: method, path, path/query params, request body schema, response schema,
  error codes, and auth requirement.
- What is the standard error envelope format?
- How are pagination, filtering, and sorting handled (if applicable)?

**Versioning strategy**:
- URL versioning (`/v1/`, `/v2/`) for public or cross-team APIs — visible, simple
- Header versioning for internal APIs with tighter coupling tolerance

**Error contract** (default, override if spec specifies):
```json
{
  "error": {
    "code": "MACHINE_READABLE_CODE",
    "message": "Human-readable description",
    "details": {}
  }
}
```

**Standard HTTP status usage**:
- 200: Success with body
- 201: Created (POST that creates a resource)
- 204: Success, no body (DELETE, some PUTs)
- 400: Client input error (validation failure, malformed request)
- 401: Not authenticated
- 403: Authenticated but not authorized
- 404: Resource not found
- 409: Conflict (duplicate, state violation)
- 422: Unprocessable entity (valid JSON but semantically invalid)
- 429: Rate limited
- 500: Internal server error (never expose stack traces)

**For async operations**: Document the job/task ID pattern if any endpoint triggers background work.

---

## Layer 5 — Infrastructure Layer

**Goal**: Define how the system is deployed, configured, and promoted across environments.

**Questions to answer**:
- What are the environments? (local, dev, staging, production — others?)
- What is the container/packaging strategy? (Dockerfile per service, shared base image, etc.)
- What is the orchestration platform? (Docker Compose, Kubernetes, ECS, serverless, etc.)
- What is the CI/CD pipeline? What gates exist at each stage?
- How is environment-specific configuration managed? (Env vars, config maps, secrets manager)
- What is the rollback strategy if a deploy goes wrong?
- What are the resource limits (CPU, memory) per service?

**Default environment strategy** (override with spec constraints):
- `local`: Docker Compose, mocked external dependencies
- `staging`: Production-equivalent stack, real integrations, no production data
- `production`: HA deployment, automated scaling, no manual changes

**CI/CD gate model** (reference: align with team's gate release standards):
```
PR → [lint + type check + unit tests] → merge
merge → [build image + integration tests] → push to registry
registry → [deploy staging + smoke tests] → [manual approval] → deploy prod
prod → [canary observation 15min] → stable
```

**Secret injection pattern**:
- Never in source code or Docker images
- Never in `.env` files committed to git
- Use a secrets manager (Vault, AWS Secrets Manager, Doppler) or CI/CD secret store
- Inject as environment variables at container startup

---

## Layer 6 — Security Layer

**Goal**: Define the complete security posture: authn, authz, secrets, network, and top threats.

**Questions to answer**:
- How are users/clients authenticated? (JWT, session, API key, OAuth2, mTLS)
- How is authorization enforced? (RBAC, ABAC, policy engine, middleware)
- What is the token lifecycle? (Issuance, expiry, refresh, revocation)
- How are secrets stored, injected, and rotated?
- What network policies exist between services? (Can every service call every other?)
- What is the input validation strategy? (Where is validation enforced?)
- What are the top 5 threats for this system and how is each mitigated?

**JWT pattern** (default for stateless APIs):
- Access token: short TTL (15min–1hr), contains user ID + roles + issuer
- Refresh token: long TTL (7–30 days), stored securely (httpOnly cookie or encrypted store)
- Rotation: refresh token rotated on each use (prevents reuse after theft)
- Revocation: maintain a small blocklist for logout/revocation, checked at validation

**RBAC design**:
- Roles defined in the spec (e.g., admin, member, viewer)
- Role membership stored in the auth service's DB
- Roles embedded in JWT at issuance (avoid per-request DB lookups in hot path)
- Role changes take effect on next token refresh, not immediately (document this lag)

**Threat model minimum** (always include these five for web/API systems):
1. Token/credential theft → short TTL, secure storage, revocation list
2. Brute force → rate limiting on auth endpoints, lockout policy
3. Injection (SQL, command) → parameterized queries everywhere, no dynamic query construction
4. Privilege escalation → authorization checks in service layer, not just gateway
5. Data exposure → no PII in logs, response filtering by role

---

## Layer 7 — Observability Layer

**Goal**: Define how the system reports on its own health and behavior.

**Questions to answer**:
- What is the log format and where do logs go?
- What fields are mandatory in every log entry?
- What metrics are tracked per service? What is the alerting threshold for each?
- Is distributed tracing required? (Yes if multiple services in the system topology)
- What dashboards are needed? Who reads them?
- What is the on-call escalation path?

**Minimum log fields** (every log entry must include):
- `timestamp` (ISO 8601, UTC)
- `level` (DEBUG / INFO / WARN / ERROR)
- `service` (service name)
- `trace_id` (propagated from the request)
- `message`

**Never log**:
- Passwords, tokens, API keys
- Full PII (email, name) — log user ID instead
- Full request/response bodies by default (opt-in, gated by debug level)

**Key metrics per service** (minimum):
- `http_requests_total` (by method, path, status code)
- `http_request_duration_seconds` (histogram, p50/p95/p99)
- `errors_total` (by error type)
- Custom business metrics for this service's primary operation

**Tracing** (required if >1 service):
- Propagate `trace_id` via HTTP headers (W3C Trace Context standard)
- Every inter-service call creates a child span
- OpenTelemetry is the preferred instrumentation standard

---

## Layer 8 — Integration Layer

**Goal**: Define every external dependency, its failure behavior, and the adapter pattern used.

**Questions to answer per integration**:
- Who is the provider? (External SaaS, another internal team's service, legacy system)
- What protocol? (REST, gRPC, message queue, webhook, file-based)
- How are credentials managed?
- What is the expected availability of this dependency?
- What does our system do if this dependency is down? (Fail open / fail closed / degrade gracefully)
- What is the retry policy? (Max retries, backoff, jitter)
- Is a circuit breaker needed? (Yes if the dependency has known instability or high latency)

**Adapter pattern**:
- Every external integration should have a dedicated adapter module
- The adapter hides the external protocol from the service's domain logic
- The adapter is mockable for local dev and testing
- The adapter owns all retry, timeout, and circuit breaker logic

**Circuit breaker thresholds** (defaults, tune per dependency):
- Open after 5 consecutive failures or 50% error rate in a 10s window
- Half-open after 30s (send one probe request)
- Close after 2 consecutive successes in half-open state

**Webhook integrations** (if applicable):
- Validate signatures on all inbound webhooks
- Respond 200 immediately, process asynchronously
- Implement idempotency — webhook providers retry on failure
