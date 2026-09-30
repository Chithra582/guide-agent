# Agent Explainability & Transparency Report

- **Agent Name:** nginx-infrastructure-agent
- **OpenGAP Specification:** 0.1.0
- **Agent ID:** nginx-infrastructure-agent
- **Domain:** Developer Tools / Web Infrastructure Management & NGINX Observability
- **Passport Validation Tier:** Tier-1 Certified Autonomous Agent

---

## 1. Overview & Architectural Purpose

The **NGINX Infrastructure Agent** (`nginx-infrastructure-agent`) is an autonomous systems management agent designed for managing, configuring, and observing distributed NGINX instances. Operating as a host companion daemon, the agent manages the full lifecycle of NGINX processes, synchronizes configuration trees with centralized management planes (such as F5 NGINX One Console), streams high-frequency telemetry via gRPC, and enforces App Protect security policies.

By embedding automated syntax verification, rollback guards, and directory isolation, the agent guarantees zero downtime and immune infrastructure operations.

---

## 2. How the Agent Decides (Decision-Making Logic)

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
| :--- | :--- | :--- | :--- |
| NGINX Configuration Files (`.conf`) | Management plane API / Local filesystem | Configures reverse proxy, virtual servers, and upstreams | Validated locally, sensitive directives redacted |
| Process & System Metrics | Host `/proc` filesystem & NGINX status API | Observes CPU, memory, connection counts, and error rates | Streamed over TLS-encrypted gRPC channels |
| SSL/TLS Certificates & Keys | Certificate directories (`/etc/ssl/nginx`) | Secures incoming HTTPS connections | Inspected for validity without echoing private keys |
| NGINX Access & Error Logs | Local log files (`/var/log/nginx/`) | Diagnoses upstream timeouts and HTTP errors | Filtered and summarized, PII stripped |

---

## 4. Known Limitations & Failure Modes

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

- **Strict Pre-Reload Verification:** Zero configuration reloads execute without passing automated syntax verification.
- **Atomic Rollback Architecture:** Previous configuration state is archived before write operations, allowing instant revert.
- **Human-in-the-Loop Override:** Local administrators retain root CLI access to kill, restart, or bypass agent actions at any time.
- **Encrypted Control Channels:** All command and metric streams between agent and management plane require mutual TLS (mTLS).
