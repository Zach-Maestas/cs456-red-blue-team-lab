# Red Team: Penetration Test (CS456 Lab)

**Author:** Zach Maestas  
**Date of Assessment:** 2025-12-03  
**Target (Authorized Lab):** `192.168.0.153` :contentReference[oaicite:0]{index=0}

This repo section documents an **authorized course-lab penetration test** against a student VM to assess weaknesses in exposed/containerized services. The engagement identified a **critical SSH misconfiguration** that enabled **unauthorized root access**, resulting in full system compromise. :contentReference[oaicite:1]{index=1}

## What’s in this folder

- **Full report (PDF):** `./report/CS456_Pen_Test_Report_Zach_Maestas.pdf`
- **Scan outputs:** `./scans/`
- **Screenshots / evidence:** `./screenshots/`

> If your PDF filename differs, update the link above to match.

## Scope + rules

- **In-scope:** `192.168.0.153` :contentReference[oaicite:2]{index=2}  
- **Out-of-scope:** any other devices on the network; **no DoS** activities performed. :contentReference[oaicite:3]{index=3}  
- Additional exploitation beyond the identified compromise path was not pursued. :contentReference[oaicite:4]{index=4}

## Methodology (high level)

- Enumerated exposed services (Nmap) and evaluated each service for realistic exploitability. :contentReference[oaicite:5]{index=5}  
- Investigated SMB, NoMachine NX, and DVWA as potential paths, but they did not produce viable exploitation routes. :contentReference[oaicite:6]{index=6}  
- Identified and validated a critical SSH configuration issue on a non-standard port, leading to privileged access. :contentReference[oaicite:7]{index=7}

## Key result

**Overall risk rating: CRITICAL** :contentReference[oaicite:8]{index=8}

### Finding 1: Unauthorized Root Access via Weak SSH Configuration (Critical)

- **Impact:** Immediate privileged access without brute force or privilege escalation, representing total system compromise. :contentReference[oaicite:9]{index=9}  
- **Affected service:** SSH on TCP **port 2222** (`192.168.0.153`). :contentReference[oaicite:10]{index=10} :contentReference[oaicite:11]{index=11}  

**Recommended remediation:**
- Disable root login in `sshd_config` (`PermitRootLogin no`) :contentReference[oaicite:12]{index=12}  
- Enforce strong passwords :contentReference[oaicite:13]{index=13}  
- Prefer key-based authentication :contentReference[oaicite:14]{index=14}  
- Restrict SSH exposure (authorized IPs) :contentReference[oaicite:15]{index=15}  
- Rotate passwords + audit accounts :contentReference[oaicite:16]{index=16}  

## Non-viable attack paths (attempted)

- **SMB (445):** enumeration did not reveal exploitable access (only default administrative shares; no anonymous/writable shares). :contentReference[oaicite:17]{index=17}  
- **DVWA (8081):** identified, but not in a vulnerable operational state (database not initialized), blocking web exploitation paths. :contentReference[oaicite:18]{index=18}  

## Summary of attack path (high level)

Service enumeration identified SSH on port 2222 as a high-value target. Validation confirmed a critical authentication misconfiguration that allowed privileged remote access, leading to full control of the VM. :contentReference[oaicite:19]{index=19}

## Tools used

- Nmap  
- smbclient  
- SSH client  
- Web browser (DVWA validation) :contentReference[oaicite:20]{index=20}

## Notes on responsible disclosure

This work was performed **only** in an authorized university lab environment and is shared for educational/portfolio purposes.
