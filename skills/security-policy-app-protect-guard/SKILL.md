---
name: "security-policy-app-protect-guard"
description: "Manages NGINX App Protect WAF rules, TLS certificates, and directory boundary enforcement."
---

# Security Policy App Protect Guard Skill

## Overview
Guards the web server environment against unauthorized access and security misconfigurations:
- Enforces strict filesystem boundaries to `allowed_directories`.
- Inspects TLS certificates for validity, expiration dates, and key pair matches.
- Deploys and verifies NGINX App Protect WAF security policy bundles.

## Safeguards
- Path traversal prevention.
- Sensitive token and credential redaction from logs.
- Automatic alerting on certificate expiration thresholds.
