---
name: "realtime-telemetry-metrics-collector"
description: "Collects host, OS, network, and NGINX performance metrics, streaming over gRPC to management planes."
---

# Realtime Telemetry Metrics Collector Skill

## Overview
Samples and streams high-fidelity performance metrics to observability backends:
- Collects HTTP request counts, connection states, response codes, and latency distributions.
- Reads OS metrics including CPU utilization, memory pressure, and disk I/O.
- Packages data into protocol buffers and streams over persistent gRPC channels.

## Key Features
- High-efficiency proto encoding.
- Local ring buffer for offline fault tolerance.
- Configurable sampling intervals.
