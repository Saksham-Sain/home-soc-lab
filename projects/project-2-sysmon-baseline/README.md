# Project 2 — Windows Logging Baseline with Sysmon

**Environment:** VMware Workstation · Windows 10 x64 · Host `DESKTOP-RA0THKO` · User `Window-Lab`
**Date:** August 20–21, 2026

## Overview

Before any anomaly or attack detection work can mean anything, you need a documented picture of what *normal* looks like on a clean system. This project installs [Sysmon](https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon) with the community-maintained [SwiftOnSecurity config](https://github.com/SwiftOnSecurity/sysmon-config), enables native Windows Security auditing, generates a period of ordinary system activity, and documents the resulting baseline across both logging sources.

This baseline is the reference point every later project in this lab (Wazuh detection, brute-force simulation, log forensics, Sigma rules) will be compared against.

## Setup

- Installed Sysmon 64-bit with the SwiftOnSecurity `sysmonconfig-export.xml` configuration:
  ```
  Sysmon64.exe -i sysmonconfig-export.xml -accepteula
  ```
- Verified service state:
  ```
  sc query Sysmon64   →  STATE: RUNNING
  ```
- Increased max log size on both `Sysmon/Operational` and `Security` logs to prevent early baseline events rotating out.
- Enabled Advanced Audit Policy (via `secpol.msc`) for:
  - **Logon/Logoff → Logon** (Success + Failure) → enables 4624 / 4625
  - **Account Management** (Success + Failure) → enables 4720/4724/4728/4732-type events
  - **Detailed Tracking → Process Creation** (Success) → enables 4688
- Applied policy immediately with `gpupdate /force`.
- Generated a few hours of ordinary activity: browsing, Notepad, File Explorer, Task Manager, Settings, and a logoff/logon cycle.

## Logging Pipeline

![Logging pipeline diagram](images/diagram-logging-pipeline.png)

A single piece of system activity (a process launching, a DNS lookup, a registry write) is captured by Sysmon's driver against the loaded config and lands in `Sysmon/Operational`. The same activity is separately picked up by native Windows auditing into the `Security` log where the audit policy above is enabled — two independent views of the same ground truth, useful for cross-referencing.

## Sysmon Baseline Findings

| Event ID | Count | What It Captured |
|---|---|---|
| **1** – Process Creation | 180 | Example: `msedge.exe` (Microsoft Edge, Microsoft Corporation) launched during normal browsing. |
| **3** – Network Connection | 3 | Example: `OneDrive.exe` initiating an outbound TCP connection from `192.168.214.132`, port 59422 — routine background sync traffic. |
| **22** – DNS Query | 31 | Example: Windows Search resolving `img-s-msn-com.akamaized.net` (Microsoft/MSN CDN) — routine OS telemetry, not user-initiated browsing. |
| **13** – Registry Value Set | 98 | Example: `services.exe` writing to `HKLM\System\CurrentControlSet\Services\OneSyncSvc_cb37c9\Start` — a routine Windows sync service, not user action. Higher volume than a "should be rare" expectation, but explained by background OS services rather than anything user-driven. |

<details>
<summary>Screenshots</summary>

**Event ID 1 — Process Creation**
![Sysmon Event ID 1](images/sysmon-eid1-process-create.png)

**Event ID 3 — Network Connection**
![Sysmon Event ID 3](images/sysmon-eid3-network.png)

**Event ID 22 — DNS Query**
![Sysmon Event ID 22](images/sysmon-eid22-dns.png)

**Event ID 13 — Registry Value Set**
![Sysmon Event ID 13](images/sysmon-eid13-registry.png)

</details>

## Security Log Baseline Findings

| Event ID | Count | What It Captured |
|---|---|---|
| **4624** – Successful Logon | 190 | Mostly background service logons (`LogonType 5`, SYSTEM). The interactive console logon (`LogonType 2`) from the logoff/logon cycle is confirmed present among the 190. |
| **4688** – Process Creation (native auditing) | 478 | Confirms the Step 7 audit policy took effect. Same underlying activity as Sysmon Event ID 1, seen through Windows' native auditing instead. |
| **4625** – Failed Logon | 2 | Two failed logons for `Window-Lab`, `LogonType 2`, `SubStatus 0xc000006a` (**"wrong password"**). A simple mistyped password on the local console — not an unknown account, not a brute-force pattern. Documented here rather than omitted, to keep the baseline honest. |

<details>
<summary>Screenshots</summary>

**Event ID 4624 — Successful Logon**
![Security Event ID 4624](images/security-4624-logon.png)

**Event ID 4688 — Process Creation**
![Security Event ID 4688](images/security-4688-process.png)

**Event ID 4625 — Failed Logon**
![Security Event ID 4625](images/security-4625-failed-logon.png)

</details>

## Why This Baseline Matters

![Baseline vs anomaly diagram](images/diagram-baseline-vs-anomaly.png)

None of the individual events above mean much on their own — a process launching, a registry key being written, a logon succeeding are all things that happen constantly on any working machine. What turns them into something useful is having this record of what counts as *ordinary*, on this machine, under normal conditions, before anything unusual has happened. Every project after this one depends on that contrast: a brute-force attempt only looks abnormal because we now know what zero failed logons looks like; a suspicious parent-child process relationship only stands out because we've seen what `notepad.exe` launching under `explorer.exe` looks like when nothing is wrong. Documenting the baseline first is what makes anomaly detection possible later, rather than guessing at what looks "off" without anything to compare it to.

## Status

- [x] Sysmon installed with SwiftOnSecurity config, service confirmed running
- [x] Log sizes increased, Advanced Audit Policy configured
- [x] Baseline activity generated and reviewed across Sysmon (1, 3, 13, 22) and Security (4624, 4625, 4688)
- [x] Findings documented with evidence
- [ ] Snapshot `Sysmon-Configured-Baseline` taken — pending

**Next project:** Wazuh SIEM setup and detection, built on top of this baseline.
