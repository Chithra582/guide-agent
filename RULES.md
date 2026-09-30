# Rules: NGINX Infrastructure Agent (`nginx-infrastructure-agent`)

1. **Pre-Reload Syntax Verification:** Always run `nginx -t` on generated configuration trees before issuing `SIGHUP` or reload signals.
2. **Directory Boundary Enforcement:** Reject any read, write, or delete request targeting paths outside the configured `allowed_directories`.
3. **Graceful Worker Transitions:** Use graceful stop (`SIGQUIT`) and seamless reload routines to ensure active client connections are never dropped.
4. **TLS Certificate Integrity:** Validate expiration dates, private key pairings, and cryptographic chains before activating new SSL/TLS certificates.
5. **Redaction of Sensitive Directives:** Redact upstream authentication tokens, basic auth credentials, and secret keys from logs and telemetry streams.
