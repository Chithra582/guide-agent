![GitHub go.mod Go version](https://img.shields.io/github/go-mod/go-version/nginx/agent)
![GitHub Release](https://img.shields.io/github/v/release/nginx/agent)
![GitHub License](https://img.shields.io/github/license/nginx/agent)
![Contributions Welcome](https://img.shields.io/badge/contributions-welcome-brightgreen.svg?style=flat)
![coverage](https://raw.githubusercontent.com/nginx/agent/badges/.badges/v3/coverage.svg)

# F5 NGINX Agent 

[![OpenGAP Spec 0.1.0](https://img.shields.io/badge/OpenGAP-0.1.0-blue.svg)](https://opengitagent.org)
[![GitAgent Passport](https://img.shields.io/badge/GitAgent%20Passport-Ready-brightgreen.svg)](https://app.hidevs.xyz/passport/submit)
[![Category](https://img.shields.io/badge/Category-Developer%20Tools-purple.svg)](https://app.hidevs.xyz/passport/submit)
[![Compliance](https://img.shields.io/badge/Compliance-MITRE%20ATLAS%20%7C%20OWASP-orange.svg)](EXPLAINABILITY.md)

F5 NGINX Agent is a companion application designed to efficiently manage NGINX instances. Key features include: 

- Remote management: Easily control and configure NGINX instances remotely.  
- Real-time metrics: Monitor and analyze performance data for NGINX and the underlying operating system.

Discover more advanced features and capabilities by visiting [Try NGINX One: Free Enterprise Trial](https://www.f5.com/trials/nginx-one). 


## Installation

You can install **NGINX Agent** using one of the following methods:

1. **Official documentation**  
   Follow the step-by-step guide to add and configure instances:  
   [How to add an instance](https://docs.nginx.com/nginx-one/how-to/nginx-configs/add-instance/)

2. **GitHub releases**  
   Download the latest binaries or packages directly from the GitHub releases page:  
   [NGINX Agent GitHub releases](https://github.com/nginx/agent/releases)  

3. **Installation and upgrade guide**  
   Access detailed instructions to install or upgrade NGINX Agent from the official NGINX documentation:  
   [NGINX Agent Installation Guide](https://docs.nginx.com/nginx-agent/installation-upgrade/)

## Useful links

* [v2 - Official NGINX Agent documentation](https://docs.nginx.com/nginx-agent/)
* [v3 - Official NGINX Agent documentation](https://docs.nginx.com/nginx-one-console/agent/)
* [F5 Support](https://my.f5.com/manage/s/)

## Community 
If you have any questions please reach out using the [NGINX Community](https://community.nginx.org/)

---

## GitAgent Passport Qualification

This repository is fully compliant with the **OpenGAP Spec 0.1.0** standard and qualified for the **HiDevs GitAgent Passport**:

- **Checkpoint 1 (Validate):** Verified OpenGAP spec 0.1.0 compliance via [`agent.yaml`](agent.yaml), [`SOUL.md`](SOUL.md), [`skills/`](skills/), and [`tools/`](tools/).
- **Checkpoint 2 (Explain):** Comprehensive 5-section transparency report in [`EXPLAINABILITY.md`](EXPLAINABILITY.md) detailing remote configuration syntax verification (`nginx -t`), zero-downtime worker reloads, gRPC telemetry streaming, and directory sandboxing under MITRE ATLAS & OWASP standards.
- **Checkpoint 3 (Export):** Cross-framework export compatibility verified across OpenAI SDK, CrewAI, Claude Code, and Lyzr.
- **Target Category:** **`Developer Tools`** (Web Infrastructure Management & NGINX Observability).

