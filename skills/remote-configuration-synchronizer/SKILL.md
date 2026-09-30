---
name: "remote-configuration-synchronizer"
description: "Deploys, validates syntax (nginx -t), and rolls back configuration files across allowed directories."
---

# Remote Configuration Synchronizer Skill

## Overview
Handles reliable configuration updates across distributed NGINX fleets:
- Receives multi-file configuration archives from centralized control planes.
- Stages files into isolated workspaces and performs atomic replacement.
- Archives previous configurations for automated instant rollback upon failure.

## Capabilities
- Syntax pre-validation.
- Atomic file deployment with preservation of file ownership and permissions.
- Multi-file diff generation for administrative audits.
