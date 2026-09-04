# Rootless Docker vs. Rootful with `userns-remap`: Security Architecture Analysis

## Overview
Container security relies on isolation mechanisms enforced by the Linux kernel and container runtime architecture. Two prominent configurations, Rootless Docker and rootful Docker with user namespace remapping (userns-remap), diverge significantly in how they handle user privilege boundaries, host file exposure, and daemon-level risk mitigation.

## Kernel Zero-Day Exploits
When a kernel zero-day vulnerability permits a container breakout, userns-remap provides superior protection for personal host files. Container root maps to an isolated sub-UID range that owns no external files, restricting a successful escape to an unprivileged sandbox. Rootless Docker maps container root directly to the invoking host user account, meaning a kernel-level escape immediately exposes that user's personal files, home directory, and local credentials.

## Docker Ecosystem Zero-Day Exploits
Rootless Docker completely neutralizes vulnerabilities within the Docker daemon or API layer. Because the background service runs entirely as an unprivileged user, a daemon compromise cannot escalate to host root. Rootful Docker with userns-remap leaves the underlying host vulnerable to complete system root takeover if a zero-day compromises the daemon, as the core service retains absolute administrative privileges.

## Configuration Vulnerabilities
Under proper administrative hygiene, both configurations maintain structural isolation. If critical misconfigurations occur—such as deploying containers in privileged mode or exposing administrative sockets—both models fail to prevent host compromise because underlying kernel boundaries are explicitly bypassed.

## Claims

* **UNVERIFIED**: A kernel zero-day breakout under userns-remap traps the attacker within a mapped unprivileged sub-UID range, preventing access to personal user files.
* **UNVERIFIED**: A kernel zero-day breakout under Rootless Docker exposes the personal files and home directory of the host user running the daemon.
* **UNVERIFIED**: A Docker ecosystem zero-day exploit against Rootless Docker cannot escalate to host root because the daemon lacks administrative privileges.
* **UNVERIFIED**: A Docker ecosystem zero-day exploit against rootful Docker with `userns-remap` configuration results in a complete system root takeover.
