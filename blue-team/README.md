# Blue Team: Incident Response (CS456 Authorized Lab)

**Author:** Zach Maestas  
**Incident ID:** ZachM-20251205  
**Report Date:** 2025-12-07

This folder documents a blue team incident-response workflow for an **authorized CS456 lab** environment (Podman services + host firewall logging + packet capture).

## Important note (simulation dataset)

My assigned red team partner did not generate attack traffic against my deployed lab environment, which is why my original incident report documents “no observed attack” for my live environment. :contentReference[oaicite:1]{index=1}  
To still demonstrate a realistic incident-response investigation, I analyzed an **instructor-provided log + packet-capture dataset** that *does* contain attack activity. The findings in this README reflect **that instructor dataset** (simulated incident), not my live environment.

## What’s in this folder

- `podman-compose.yml` / environment artifacts (lab services + layout)
- `capture.pcap` (network capture for validation)
- `ufw.log` (host firewall telemetry)
- `log/` (service and system logs collected from the vulnerable container / host)

## Executive summary (instructor dataset)

**Observed activity:** Recon/scanning + SSH authentication pressure (attack activity present).  
**Confirmed compromise:** Not proven from available evidence in this dataset.

At a high level, the dataset shows:
- Broad **TCP SYN probing** across many internal targets/ports (recon / scanning behavior).
- **SSH authentication failures** recorded (failed login attempts).
- No recorded HTTP or PostgreSQL application activity in the provided `nginx` and `postgresql` logs (they are empty in this dataset).

This is still a useful blue-team exercise: it shows how to triage scanning, validate auth activity, and document what evidence supports (or fails to support) a compromise.

## Detection and analysis

### 1) Recon / port scanning behavior (host firewall evidence)

`ufw.log` shows repeated TCP SYN traffic consistent with scanning/recon:
- Large volume of TCP SYN attempts from a single internal source
- Targets spread across dozens of internal destinations
- Frequent probing of **SSH (TCP/22)** plus additional high ports

**Why it matters:** This is a common precursor to exploitation. Even if no compromise is confirmed, this is “raise a flag and investigate” activity.

### 2) SSH authentication activity (container/system evidence)

The dataset includes failed SSH login activity (btmp/failed-login records), consistent with:
- Repeated login attempts against a single username
- Attempts originating from a small set of internal attacker IPs

**Why it matters:** This indicates credential guessing / brute-force style behavior or targeted access attempts. In a real environment, this would trigger alerting and immediate hardening actions (rate limiting, key-only auth, IP restrictions).

### 3) Web and database decoys (no recorded interaction in this dataset)

The provided logs for:
- `log/nginx/access.log`
- `log/nginx/error.log`
- `log/postgresql/postgresql-12-main.log`

…are empty in this dataset, so there’s no evidence here of web exploitation or database authentication attempts.

**Interpretation:** Attack activity in this dataset is concentrated on recon + SSH auth pressure, not web/DB interaction (or logging may not have captured it).

### 4) Packet capture validation

`capture.pcap` is included for packet-level validation:
- Confirm scanning patterns and targeted services
- Confirm whether any SSH sessions fully establish
- Look for post-auth activity (if any) such as callbacks, downloads, or lateral movement

This README focuses on log-driven conclusions, with the pcap serving as the supporting “ground truth” dataset.

## Timeline (instructor dataset window)

- Firewall log window covers: **2025-11-30 00:00 to ~07:26 (local timestamps)**  
- SSH failed logins appear around **Nov 29–30** (failed-auth evidence)

This shows attack-like activity over multiple hours rather than a single one-off probe.

## Scope and impact assessment (simulated incident)

Based on the evidence available in this dataset:
- **Recon and credential pressure occurred**
- **No confirmed privileged session, data access, or persistence** is proven from the provided artifacts

Impact is assessed as **attempted intrusion / suspicious activity**, with insufficient evidence to declare a successful breach from this dataset alone.

## Containment, eradication, and recovery (recommended actions)

If this were my live environment, the immediate response would be:

**Containment**
- Restrict SSH exposure (allowlist source IPs or require VPN/bastion)
- Temporarily block offending IPs observed in failed-auth + scan telemetry
- Increase logging verbosity for auth + network events

**Eradication / Hardening**
- Disable password auth; enforce key-only SSH
- Disable direct root login (`PermitRootLogin no`)
- Add rate limiting (fail2ban or equivalent)
- Validate UFW rules so only required ports are reachable

**Recovery**
- Review accounts, rotate credentials/keys as needed
- Confirm integrity of configs and containers
- Preserve logs + pcap as evidence

## Lessons learned

- Even when your *live* lab doesn’t get attacked, you can still demonstrate incident-response skill by investigating a realistic dataset and clearly separating **simulation findings** from **real environment findings**. :contentReference[oaicite:2]{index=2}
- Defensive work includes stating what you *can’t* prove from evidence, not just what you can.

## Responsible use statement

All activity and artifacts in this repository are from an **authorized university lab** and are shared for educational/portfolio purposes only.
