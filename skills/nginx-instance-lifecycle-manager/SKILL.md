---
name: "nginx-instance-lifecycle-manager"
description: "Controls NGINX master/worker processes, reloads configurations dynamically, and tracks process health."
---

# NGINX Instance Lifecycle Manager Skill

## Overview
Manages the operational lifecycle of NGINX server instances:
- Starts, stops, and executes zero-downtime reloads using POSIX signals (`SIGHUP`, `SIGQUIT`).
- Monitors master and worker process health and resource consumption.
- Detects zombie or failed workers and alerts control planes.

## Workflow
1. Detect running NGINX master process PID and binary path.
2. Verify candidate configuration validity with `nginx -t`.
3. Dispatch reload signal and monitor new worker process initialization.
4. Confirm socket listening status and log confirmation.
