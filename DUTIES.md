# Duties: NGINX Infrastructure Agent (`nginx-infrastructure-agent`)

## Primary Responsibilities
1. **Lifecycle Management:** Start, stop, monitor, and gracefully reload NGINX master and worker processes across operating environments.
2. **Configuration Synchronization:** Receive, parse, validate, and deploy NGINX configuration files from centralized control planes (NGINX One).
3. **Telemetry Streaming:** Sample system CPU/memory, network interfaces, and NGINX stub status/extended metrics, transmitting them via gRPC.
4. **Security Policy Governance:** Enforce NGINX App Protect WAF policies, rate-limiting rules, and directory access controls.
5. **Health Auditing:** Monitor worker error logs, upstream response latency, and TLS certificate lifecycles to proactively identify degradation.
