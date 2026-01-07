# Blue Team: Incident Response (CS456 Authorized Lab)

**Author:** Zach Maestas  
**Incident ID:** ZachM-20251205  
**Report Date:** 2025-12-07

This folder documents a blue team incident-response workflow for an authorized CS456 lab environment (Podman services + host firewall logging + packet capture).

## Important note (simulation dataset)

My assigned red team partner did not generate attack traffic against my deployed lab environment, which is why my original incident report documents “no observed attack” for my live environment.

To still demonstrate a realistic incident-response investigation, I analyzed an **instructor-provided log + packet-capture dataset** that **does** contain attack activity. The findings in this README reflect that **instructor dataset** (simulated incident), not my live environment.

## What’s in this folder

- `podman-compose.yml` (environment artifacts: services + layout)
- `services.pdf` (diagram/descriptions of the Podman services)
- `logs/` (service/system logs from the dataset)
- `report/` (full write-up)

## Executive summary (instructor dataset)

**Observed activity:** Recon/scanning + SSH authentication pressure (attack activity present)  
**Confirmed compromise:** Not proven from the available evidence in this dataset

At a high level, the dataset shows:
- Broad TCP SYN probing across many internal targets/ports (recon/scanning behavior)
- SSH authentication failures recorded (failed login attempts)
- No recorded HTTP or PostgreSQL activity in the provided `nginx` / `postgresql` logs (those files are empty in this dataset)

## Detection and analysis

### 1) Recon / port scanning behavior (UFW firewall evidence)

`ufw.log` shows repeated TCP SYN traffic consistent with scanning/recon:
- High-volume SYN attempts from internal sources
- Targets spread across many internal destinations
- Frequent probing of SSH (TCP/22) plus additional ports

**Why it matters:** This type of activity is a common precursor to exploitation. Even if compromise isn’t confirmed, it’s enough to trigger investigation and hardening actions.

### 2) SSH authentication activity (system logs)

The dataset includes failed SSH authentication activity consistent with:
- Repeated login attempts
- Attempts originating from a small set of internal source IPs

**Why it matters:** This indicates credential guessing or targeted access attempts. In a real environment this would trigger alerting and immediate control changes (key-only auth, rate limiting, access restrictions).

### 3) Web and database decoys (no recorded interaction in this dataset)

The following logs are empty in the dataset:
- `log/nginx/access.log`
- `log/nginx/error.log`
- `log/postgresql/postgresql-12-main.log`

**Interpretation:** The observed attack activity in this dataset is concentrated on recon + SSH authentication pressure (or logging for web/DB was not configured to capture traffic). Either way, there is no evidence in these files of web/DB exploitation.

### 4) Packet capture (`capture.pcap`)

A packet capture is included for packet-level validation:
- Confirm scanning patterns and targeted services
- Confirm whether SSH sessions fully establish
- Look for post-auth activity (downloads, callbacks, lateral movement)

This README focuses on log-driven conclusions, with the pcap serving as supporting evidence for deeper review.

## Timeline (instructor dataset window)

- Firewall log window spans approximately: **2025-11-30 00:00 to ~07:26** (local timestamps)
- Failed SSH authentication evidence appears within the same general time window

## Scope and impact assessment (simulated incident)

Based on the available evidence in the instructor dataset:
- Recon and credential pressure occurred
- No confirmed privileged session, data access, persistence, or service disruption is proven from the provided artifacts

**Impact assessment:** Attempted intrusion / suspicious activity, with insufficient evidence to declare a successful breach from this dataset alone.

## Recommended response actions (what I would do in production)

**Containment**
- Restrict SSH exposure (allowlist source IPs, require VPN/bastion, or close external access entirely)
- Temporarily block offending IPs observed in scan/auth activity
- Increase logging/alerting for auth failures and connection bursts

**Eradication / Hardening**
- Disable password authentication and enforce SSH keys
- Disable direct root login (`PermitRootLogin no`)
- Add rate limiting (fail2ban or equivalent)
- Verify firewall policy: only required ports reachable

**Recovery**
- Rotate keys/credentials as needed
- Review users, sudoers, and configs for tampering
- Preserve logs + pcap for evidence and post-incident review

## Lessons learned

- Incident response includes documenting what happened **and** what you cannot prove from evidence.
- Even when a live environment is not attacked, you can still demonstrate IR competency by analyzing a realistic dataset and clearly labeling simulated fin
