# EXPLAINABILITY — NGINX Infrastructure Agent

> **Admissibility & Transparency Report for OpenGAP / Agent Passport**  
> *Agent Name:* NGINX Infrastructure Agent (`nginx-infrastructure-agent`)  
> *Specification:* OpenGAP v0.1.0  
> *Domain:* Developer Tools / Web Infrastructure Management & NGINX Observability  

---

## 1. Overview & Operational Purpose

The **NGINX Infrastructure Agent** (`nginx-infrastructure-agent`) is an autonomous systems management agent designed for managing, configuring, and observing distributed NGINX instances. Operating as a host companion daemon, the agent manages the full lifecycle of NGINX processes, synchronizes configuration trees with centralized management planes (such as F5 NGINX One Console), streams high-frequency telemetry via gRPC, and enforces App Protect security policies.

The agent's primary operational purpose is to ensure zero downtime during configuration reloads, validate candidate syntax before applying changes, prevent directory boundary traversal, and deliver real-time infrastructure observability across web tiers.

---

## 2. How the Agent Decides (Decision-Making Logic)

NGINX Infrastructure Agent operates across a deterministic, multi-stage infrastructure management decision pipeline:

```
[Remote Config / Command Request] ──> [Directory Boundary Sentry] ──> [Syntax Pre-flight Check (nginx -t)]
                                                                                        │
                                                                                        ▼
[Structured Audit Log & Telemetry] <── [Zero-Downtime Worker Reload] <── [Atomic Staging File Replacement]
```

### 2.1 Remote Configuration Deployment & Syntax Validation
- **Decision:** Determines whether a received configuration bundle can be safely applied to active NGINX instances.
- **Rules:**
  - Stages received configuration files in a temporary sandboxed staging directory.
  - Executes `nginx -t` with the candidate configuration path to test syntax validity and include integrity.
  - Automatically aborts the transaction and reports syntax errors if verification exits with a non-zero code.

### 2.2 Process Control & Zero-Downtime Reload Execution
- **Decision:** Selects appropriate POSIX signals (`SIGHUP`, `SIGQUIT`, `SIGTERM`) for process lifecycle operations.
- **Rules:**
  - Uses `SIGHUP` for configuration updates to enable master process reload without dropping active client connections.
  - Monitors worker process spawn logs post-reload to verify new workers bind sockets successfully.
  - Falls back to previous configuration snapshot if new worker processes fail health checks within 5 seconds.

### 2.3 Real-Time Telemetry Sampling & gRPC Streaming
- **Decision:** Adjusts metrics sampling intervals and payload batch sizes based on network conditions and host load.
- **Rules:**
  - Queries OS performance counters (CPU, RAM, network I/O) and NGINX connection counters at configured intervals.
  - Enforces exponential backoff when upstream management plane connections encounter network latency or disconnection.
  - Buffers metrics locally in bounded ring buffers to prevent memory growth during network outages.

### 2.4 Allowed Directory Boundaries & Security Policies
- **Decision:** Validates file system paths for reading or writing configuration artifacts, certificates, and logs.
- **Rules:**
  - Resolves symlinks to absolute paths before performing any file read or write operation.
  - Checks resolved paths against the white-list of `allowed_directories` (`/etc/nginx`, `/etc/app_protect`, etc.).
  - Rejects path traversal attempts (`../`) and raises security alert events for disallowed targets.

---

## 3. Data Sources & Inputs Used

| Data Input | Source | Purpose | Data Handling & Privacy |
|---|---|---|---|
| **NGINX Configuration Files (`.conf`)** | Management plane API / Local filesystem | Configures reverse proxy, virtual servers, and upstreams | Validated locally, sensitive directives redacted |
| **Process & System Metrics** | Host `/proc` filesystem & NGINX status API | Observes CPU, memory, connection counts, and error rates | Streamed over TLS-encrypted gRPC channels |
| **SSL/TLS Certificates & Keys** | Certificate directories (`/etc/ssl/nginx`) | Secures incoming HTTPS connections | Inspected for validity without echoing private keys |
| **NGINX Access & Error Logs** | Local log files (`/var/log/nginx/`) | Diagnoses upstream timeouts and HTTP errors | Filtered and summarized, PII stripped |

NGINX Infrastructure Agent complies with operational security and privacy standards:
- **Zero Configuration Downtime:** Pre-flight syntax validation prevents broken configurations from reloading the master process.
- **Encrypted Control Channels:** All command and metric streams between agent and management plane require mutual TLS (mTLS).
- **Directory Isolation:** File operations are strictly confined to whitelisted `allowed_directories`.
- **Right to Terminate:** Local root administrators retain the ability to halt, pause, or kill the agent daemon at any time.

---

## 4. Known Limitations & Failure Modes

Reviewers, auditors, and users should note the following operational constraints:

1. **Port Binding Collisions:**
   - *Limitation:* Reloading configurations with new `listen` directives may fail if ports are bound by foreign processes.
   - *Mitigation:* Pre-reload port availability probing and automatic configuration rollback.

2. **Upstream DNS Resolution Timeouts:**
   - *Limitation:* Missing or unresponsive upstream DNS resolvers can cause NGINX reload delays or startup failures.
   - *Mitigation:* Resolver directive validation and pre-flight DNS lookup checks.

3. **Management Plane gRPC Disconnection:**
   - *Limitation:* Loss of connectivity to centralized management servers halts remote configuration updates.
   - *Mitigation:* Autonomous local operation with cached configurations, exponential backoff retries, and bounded queueing.

4. **Resource Constraints on High-Traffic Nodes:**
   - *Limitation:* Telemetry collection at sub-second intervals may consume measurable CPU on extreme-load instances.
   - *Mitigation:* Adaptive rate throttling and configurable sampling frequencies.

---

## 5. Verification, Safety & Human Oversight

- **Real-Time Human Approval Gate:** Remote configuration pushes can be flagged for mandatory administrative review before signal dispatch.
- **Emergency Session Interrupt:** Sending `SIGTERM` or `SIGINT` to the agent daemon releases file locks and halts monitoring without affecting NGINX workers.
- **Step Quota Guardrails:** Rollback snapshots are bounded to the last 10 versions ($N \le 10$) to prevent disk saturation.
- **Structured Audit Logging:** Every configuration change, syntax verification outcome, and process signal is recorded in structured audit logs.
