# Red Team: Penetration Test (CS456 Authorized Lab)

**Author:** Zach Maestas  
**Assessment Date:** 2025-12-03  
**Target:** 192.168.0.153 (authorized course lab VM)

This folder documents an **authorized course-lab penetration test** against a student VM to identify security weaknesses in exposed services, validate exploitability, and provide remediation guidance.

## What’s in this folder

- **Full report (PDF):** [CS456_Pen_Test_Report_Zach_Maestas.pdf](./report/CS456_Pen_Test_Report_Zach_Maestas.pdf)
- **Scan outputs:** [`./scans/`](./scans/)
- **Screenshots / evidence:** [`./screenshots/`](./screenshots/)

## Scope and rules

- **In scope:** 192.168.0.153  
- **Out of scope:** any other devices on the network  
- **No DoS:** no denial-of-service activity was performed  
- **Limitations:** exploitation beyond the validated compromise path was not pursued

## Methodology (high level)

- Enumerated exposed services (Nmap) and evaluated each for realistic exploitability
- Reviewed SMB and web app exposure for misconfigurations and common weaknesses
- Validated the most impactful finding and documented evidence and remediation steps

## Key result

**Overall risk rating: CRITICAL**

### Finding: Unauthorized Root Access via SSH Misconfiguration (Critical)

**Impact:** Remote privileged access leading to full system compromise.  
**Affected service:** SSH on TCP port 2222.

**Recommended remediation:**
- Disable direct root login (`PermitRootLogin no`)
- Enforce strong authentication (prefer SSH keys over passwords)
- Restrict SSH exposure (limit source IPs, use VPN/bastion, or firewall rules)
- Rotate credentials and audit accounts/logs after remediation

## Notes on responsible use

All activity was performed **only** in an authorized university lab environment and is shared for educational/portfolio purposes.
